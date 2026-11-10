# SDK Agents：実行・ストリーム

Studio SDKのAgent呼出し、出力・失敗・クレジットの扱いを説明します。

会話履歴とCanvasは[SDK Agents：会話履歴・Canvas](sdk-agents-history-and-canvas.md)、HTTP APIは[Agents・会話・成果物API](../api/agents-conversations-artifacts-api.md)を参照してください。

## 関連記事

- [実行モード・認証・対応範囲](execution-modes-and-auth.md)
- [Project APIキーを管理する](../operations/manage-project-api-keys.md)
- [Agents・会話・成果物API](../api/agents-conversations-artifacts-api.md)
- [SDK Agents：会話履歴・Canvas](sdk-agents-history-and-canvas.md)

## 始める前に

- `@japan-ai-inc/studio-sdk` **0.11.0**とNode.js **22以降**を使います。SDKの版は固定します。
- ローカルとPRSサーバーではAPIキーをサーバー側だけに保管します。PRSブラウザではキーを渡さず、`createClient({ baseUrl: "" })`で初期化します。
- APIキー、Cookie、認証ヘッダー、秘密情報をブラウザ用コード、公開環境変数、ログへ出してはいけません。

> 共有したProject APIキーでは、呼出し元の利用者ごとに会話の閲覧範囲を分離できません。利用者ごとの会話を扱うブラウザ画面は、PRSセッション経路で実装してください。

## Project APIキーでAgent定義を作成・更新する

SDK 0.11.0では、Project APIキーで`agents.create()`、`agents.getDefinition()`、`agents.update()`を使えます。必要なキーのスコープは`agent:write`で、キーの作成者にもAgentを作成・編集する権限が必要です。これらはPRSブラウザからは使えません。作成は冪等ではありません。再作成前に`list({ search })`で確認します。

```ts
const created = await studio.agents.create({
  name: "問い合わせ分類",
  prompt: "問い合わせを分類してください。",
  model: "gpt-5.5",
  tools: ["web_search"],
  datasets: ["dataset-id"],
});

const current = await studio.agents.getDefinition(created.agentId);
await studio.agents.update(created.agentId, {
  prompt: "分類と担当部署を返してください。",
  tools: ["web_search", "web_fetch"],
});
```

`getDefinition()`の`definition`は`update()`へ渡せる形です。更新では送らない項目は変更されず、配列はリスト全体を置換し、`null`はその項目をクリアします。ツール、Skill、モデルは、キー作成者にStudioのAgent設定画面で提示されるものだけを指定します。`datasets`にはキーのProjectにあるデータセットIDだけを指定します。

## 1. 応答をまとめて受け取る

`invoke(agentId, input, requestOptions?)`は`Promise<InvokeResponse>`を返します。`input.prompt`は必須です。`sessionId`を次の入力へ渡すと、会話を継続できます。

```ts
import { AgentsApiError, createClient } from "@japan-ai-inc/studio-sdk";

const apiKey = process.env["JAI_STUDIO_API_KEY"];
if (!apiKey) throw new Error("Project APIキーがありません");

const studio = createClient({
  baseUrl: "https://api.japan-ai.co.jp",
  apiKey,
});
const agentId = "<Agent UUID>";

try {
  const result = await studio.agents.invoke(agentId, {
    prompt: "この文書を要約してください",
  });
  if (result.status === "failed") {
    throw new Error(result.errorMessage ?? "Agentの実行に失敗しました");
  }
  console.log(result.text, result.sessionId);
} catch (error) {
  if (error instanceof AgentsApiError) {
    console.error(error.statusCode, error.message);
  }
  throw error;
}
```

`InvokeResponse`には`status`、`sessionId`、`text`があります。`references`、`imageAssets`、`files`、`errorMessage`も必要に応じて確認します。出力URLには有効期限があります。

## 2. 入力と会話の扱いを選ぶ

`input`には次の任意項目を指定できます。組合せは対象環境で検証します。

| 項目 | 用途 |
|---|---|
| `sessionId` | 既存の会話を継続します。 |
| `model`、`temperature`、`systemPrompt`、`skipHistory` | 実行時のモデル、温度、指示、履歴の扱いを指定します。 |
| `isEphemeral` | 会話履歴へ残さない実行を指定します。 |
| `files` | `File`または`Blob`を添付します。 |
| `storageFileIds` | PRSブラウザでStorageの`fileId`を添付します。最大**20件**です。 |
| `enableHandoffLongRunJob` | 長時間実行の引継ぎを許可します。 |

`isEphemeral: true`と`enableHandoffLongRunJob: true`は同時に指定できません。引継ぎでは`sessionId`を維持し、`isEphemeral: false`として履歴を確認してください。

## 3. ストリームを画面へ反映する

`invokeStream(agentId, input, requestOptions?)`は`AsyncGenerator<ChatStreamEvent>`です。`for await...of`で受け、`type`で分岐します。`content`が空でも画像・ファイルや終了理由を持つ`delta`があります。

```ts
let text = "";

for await (const event of studio.agents.invokeStream(agentId, {
  prompt: "箇条書きで要約してください",
})) {
  if (event.type === "delta") {
    text += event.content;
    renderText(text);
    if (event.imageAsset) renderImage(event.imageAsset.url);
    if (event.file) renderFile(event.file.name, event.file.url);
    if (event.finishReason) markCompleted(event.finishReason);
  } else {
    showAgentError(event.text ?? event.code ?? "ストリーム中にエラーが発生しました");
  }
}
```

`delta`には`responseId`、`content`、`finishReason`、必要に応じて`sessionId`、`imageAsset`、`file`があります。`imageUrl`は互換用の別名です。`error`イベントでは、それまでの本文を成功結果として確定しません。開始前の認証・HTTP・入力・タイムアウト失敗は`AgentsApiError`になる場合があり、ループを`try/catch`で囲みます。

## PRSブラウザで候補比較を行う

`agents.compare()`、`listCompareModels()`、`getTurns()`、`adoptCandidate()`は、署名済み利用者のPRSブラウザ向けです。公開APIや直接開発では使えません。まず`listCompareModels(agentId)`で、その利用者・組織の設定・プラン・Agent設定で比較可能なモデルを取得します。返る`min`〜`max`の範囲（現在は2〜4候補）と`models`に従い、一覧にないモデルを推測して指定しません。

```ts
const available = await studio.agents.listCompareModels(agentId);
if (available.models.length < available.min) throw new Error("比較可能なモデルが不足しています");

for await (const event of studio.agents.compare(agentId, {
  prompt: "提案を比較してください",
  models: ["model-a", "model-b"],
  primaryIndex: 0,
  tools: available.toolsAvailable ? ["web_search"] : undefined,
})) {
  if (event.type === "delta") appendCandidate(event.candidateIndex, event.text);
  if (event.type === "candidate_reset") resetCandidate(event.candidateIndex);
  if (event.type === "error") showCompareError(event.code, event.text);
}
```

`files`には全候補へ渡す画像・PDFを、`storageFileIds`にはStorageの`fileId`を最大20件指定できます。`tools`は最大10個の重複しない名前で、`toolsAvailable`が`true`の場合だけ、候補の副作用を伴わないツールを使えます。画像に対応しない候補は`unsupported_input`になり得ます。

`candidateEfforts`が`true`で、かつモデルの`effortSupported`が`true`なら、`models`に`{ model, effort }`を渡し、候補ごとに`low`、`mid`、`high`、`max`を選べます。同じモデルを異なるeffortで重複させることもできます。旧い接続先ではeffortが無視され同一モデルの重複が拒否されるため、`candidateEfforts`が`false`ならモデル名を各1回だけ渡します。候補の照合はモデル名ではなく`candidateIndex`または`index`で行います。

比較の`error`イベントや開始前の`TurnCandidatesApiError`では、`model_not_allowed`・`policy_unavailable`を確認します。拒否されたモデルは`models`を参照し、一覧を取り直します。

## 4. PRSブラウザで`requestId`とクレジットを扱う

SDK 0.11.0では、PRSブラウザの`invoke()`応答と`invokeStream()`の`delta`に、クレジット台帳上のターン識別子`requestId`が含まれる場合があります。

```ts
const requestIds = new Set<string>();
for await (const event of studio.agents.invokeStream(agentId, { prompt: "確認してください" })) {
  if (event.type === "delta" && event.requestId) requestIds.add(event.requestId);
}

const ids = [...requestIds];
const { credits } = await studio.agents.getTurnCredits(ids);
const { credits: roomCredits } = await studio.agents.getRoomCredits(roomId);

for await (const total of studio.agents.followTurnCredits(ids)) showTurnCredits(total);
for await (const total of studio.agents.followRoomCredits(roomId)) showRoomCredits(total);
```

`getTurnCredits()`は、署名済み利用者自身の直近24時間のターンを、1回につき**1〜200件**の`requestId`で集計します。`getRoomCredits()`は読取り可能なroomの累計を取得します。`followTurnCredits()`と`followRoomCredits()`は5秒、30秒、2分、5分後に再読し、増えた合計だけを返します。

これらは署名済み利用者が開いたPRS Pageと対応するPRS経路が必要です。未対応の実行環境では404になることがあり、PRS外では`AgentsApiError`になります。

## 5. タイムアウトとキャンセルを実装する

既定タイムアウトは、`invoke()`が**5分**、`invokeStream()`が**10分**です。`timeoutMs`と`AbortController`の`signal`を指定できます。

```ts
const controller = new AbortController();
try {
  for await (const event of studio.agents.invokeStream(
    agentId,
    { prompt: "提案書の下書きを作成してください" },
    { signal: controller.signal, timeoutMs: 120_000 },
  )) {
    if (event.type === "delta") appendText(event.content);
    else showAgentError(event.text ?? "エラーが発生しました");
  }
} catch (error) {
  if (controller.signal.aborted) showCancelled();
  else throw error;
}

// 利用者が「キャンセル」を選んだときに実行します。
controller.abort();
```

キャンセル後の処理、履歴、出力への反映範囲は確定できません。同じ依頼を自動再実行せず、会話と業務結果を確認してから再開してください。

## うまくいかない場合

| 状況 | 確認・対応 |
|---|---|
| 認証・権限エラーになる | 対象Project、キー、実行モード、必要なスコープを確認します。 |
| PRSブラウザで動かない | PRS内で実行し、キーを渡さず`baseUrl: ""`で初期化しているか確認します。 |
| `storageFileIds`で失敗する | PRSブラウザの呼出しか、IDが20件以下か、利用者が対象ファイルを扱えるか確認します。 |
| 応答が失敗・途中停止する | `status`、`errorMessage`、ストリームの`error`、例外の`statusCode`を分けて記録し、直ちに再送しません。 |
| クレジットを取得できない | 0.11.0、署名済みPRS Page、`requestId`数とroom IDを確認します。 |

問い合わせにはSDK版、実行モード、Agent ID、日時、`statusCode`、再現手順を添えます。キー・Cookie・認証情報は送らないでください。

## チェックリスト

- [ ] 実行場所と認証方法を決め、APIキーをサーバー側だけに保管した
- [ ] `status`、`sessionId`、ストリームの`delta`・`error`を処理した
- [ ] 添付と出力URLの権限・有効期限を考慮した
- [ ] クレジットを表示する場合、`requestId`と未対応時を処理した
- [ ] タイムアウトとキャンセル後の画面表示を確認した

## 次に読む記事

- [Agents・会話・成果物API](../api/agents-conversations-artifacts-api.md)
- [SDK Agents：会話履歴・Canvas](sdk-agents-history-and-canvas.md)
