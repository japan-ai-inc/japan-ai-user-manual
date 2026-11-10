# Pageとチャット・コンテキストの連携

Page実行環境で動くブラウザ画面は、利用者のPageセッションを使ってAgentとの会話を開始・継続し、表示中の組織・Project・Page・利用者のコンテキストを読めます。Project APIキーをブラウザへ渡さず、画面の入力と会話を接続できることがポイントです。

Agentの一般的なSDK呼出しとストリームは[SDK Agents：実行・ストリーム](../development/sdk-agents-invoke-and-stream.md)、roomの履歴と成果物は[SDK Agents：会話履歴・Canvas](../development/sdk-agents-history-and-canvas.md)、利用者の会話操作は[Agentと会話して結果を確認する](../use/chat-with-agents.md)を参照してください。

## 関連記事

- [SDK Pages・Platform](../development/sdk-pages-and-platform.md)
- [Agents・会話・成果物API](../api/agents-conversations-artifacts-api.md)
- [Agentと会話して結果を確認する](../use/chat-with-agents.md)

## 始める前に

- 対象は、Page実行環境で表示される**ブラウザ側**コードです。ローカル、外部ホスティング、Pageのサーバー側処理と同じ認証方法だと考えないでください。
- `@japan-ai-inc/studio-sdk`は現行公開版0.11.0を使用し、利用するPage実行環境との対応を確認します。`platform`はベータ機能です。
- Pageのブラウザコードには、Project APIキー、Cookie、認証ヘッダー、Secret値を入れません。SDKは空の`baseUrl`で初期化します。
- 会話に渡す画面データは必要最小限にします。顧客情報・個人情報・画面上の非表示項目を、利便性だけを理由に丸ごと渡さないでください。

## 1. Page内のAgent呼出しを実装する

Page実行環境のブラウザでは、次のように初期化します。`agents.invoke()`はPage実行環境を検出すると、Page相対の`/api/agent-chat`へ送信します。利用者のPageセッションが使われるため、APIキーを追加しません。

```ts
import { createClient } from "@japan-ai-inc/studio-sdk";

const studio = createClient({ baseUrl: "" });

const result = await studio.agents.invoke(agentId, {
  prompt: "選択した案件を3点で要約してください",
});

if (result.status === "failed") {
  throw new Error(result.errorMessage ?? "Agentの実行に失敗しました");
}
showAnswer(result.text);
```

画面には、Agent、対象データ、実行してよい範囲を明示します。利用者向けの確認方法は[Agentと会話して結果を確認する](../use/chat-with-agents.md)に従います。

### ストリームを表示する

生成中の表示には`invokeStream()`を使います。`delta`だけでなく`error`も処理し、`error`を受けたときは、それまでの文字列を完了結果として確定しません。

```ts
let text = "";
for await (const event of studio.agents.invokeStream(agentId, {
  prompt: userInput,
})) {
  if (event.type === "delta") {
    text += event.content;
    renderStreamingText(text);
  } else {
    showError(event.text ?? "応答を完了できませんでした");
  }
}
```

Page相対には、roomの状態を確認する`/api/agent-chat/status/{roomId}`、履歴を取得する`/api/chat-history/{roomId}`もあります。いずれも有効なPageセッションを前提とし、room IDはUUID形式です。Page向けに返る履歴では、ツール呼び出しの引数（`args`）は含まれません。画面表示や処理でツールの引数を使わないでください。ただし通常の会話継続では、これらの経路を直接組み立てず、`sessionId`とSDKを優先してください。`/api/mentions`は、この確認範囲でPage利用者向けのAPIとして確認できませんでした。

SDK 0.11.0では、Page実行環境が対応する場合に限り、実行結果の`requestId`から`getTurnCredits()`でターンのクレジットを、`getRoomCredits()`でroomの合計を取得できます。対応していない実行環境では404になるため、クレジット表示を必須の画面機能にせず、失敗を処理してください。

### 回答候補を比較する場合

比較は、署名済み利用者のPageでのみ利用できる任意機能です。先に`listCompareModels(agentId)`を呼び、返る`min`、`max`、`models`、`toolsAvailable`に従います。`compare()`には2〜4候補だけを指定し、モデル名を推測して渡しません。候補ごとの添付は`files`または最大20件の`storageFileIds`、副作用のないToolは`tools`で指定できます。`getTurns()`の候補には添付、Tool、参照情報が含まれ得るため、表示前にURL・ファイル名・本文を安全に扱います。

候補ごとの`effort`（`low`、`mid`、`high`、`max`）と同一モデルの異なるeffortでの比較は、`listCompareModels()`の`candidateEfforts`が`true`のときだけ指定します。各モデルの`effortSupported`が`false`なら、effortの選択肢を表示しません。未対応のPage実行環境ではこれらの値は`false`になり得るため、モデル名を1回ずつ指定する通常の比較へ戻します。候補の採用は`adoptCandidate()`で行い、roomがアイドルであることと利用者の権限を確認します。

## 2. `sessionId`で同じ会話を続ける

最初の成功応答の`sessionId`はroom IDです。画面の状態に保持し、次の入力へ渡すと、同じ会話を続けられます。会話を残して続ける用途で`isEphemeral: true`を指定しないでください。

```ts
const first = await studio.agents.invoke(agentId, {
  prompt: "この案件の確認事項を挙げてください",
});
if (first.status === "failed") throw new Error(first.errorMessage);

const next = await studio.agents.invoke(agentId, {
  sessionId: first.sessionId,
  prompt: "優先度順に並べ替えてください",
});
```

会話をまたぐ場合は、room ID、対象Agent、Project、利用者を対応付けます。推測したIDで別の会話へ接続しません。履歴とCanvasは[SDK Agents：会話履歴・Canvas](../development/sdk-agents-history-and-canvas.md)を参照してください。

## 3. Pageコンテキストを読む

`platform`はPage実行環境のブラウザ専用です。`isAvailable()`で利用可否を確認でき、同期読取の`getContext()`は利用できない場合に`null`を返します。初期表示で注入が完了していないことがあるため、通常は`waitForReady()`を使います。既定の待機時間は5,000ミリ秒で、利用不可または待機切れでは`null`になります。

```ts
const context = await studio.platform.waitForReady();
if (!context) {
  showUnavailableMessage();
} else {
  showCurrentProject(context.orgName, context.projectId);
}
```

| 区分 | 値 |
|---|---|
| 利用者 | `userId`、`email`、`userName`、`memberRole` |
| 組織・Project・Page | `orgId`、`orgName`、`projectId`、`pageId` |
| Object | `customObjects`（名前からObject IDへの対応） |

## 4. 画面の選択内容をStudioタスクへ渡す

`openTask()`は、画面で選んだオブジェクトまたは配列と任意の指示をStudioタスクへ渡し、`roomId`を返します。少なくともデータまたは指示のどちらかが必要です。画面内のURL遷移や任意サイトへの移動には使いません。

```ts
const selected = { orderId: "42", status: "確認待ち" };
const task = await studio.platform.openTask(
  selected,
  "この注文で確認すべき事項を整理してください"
);

console.log(task.roomId);
```

返された`roomId`を後続の画面処理で使う場合も、会話内容の閲覧・継続を無条件に許可する値として扱わないでください。利用者がStudio側で会話を確認する導線、共有範囲、入力・追加送信の可否は[Agentと会話して結果を確認する](../use/chat-with-agents.md)を確認します。

## アクセス設定ごとの確認

「アクセス設定」の選択は、画面を開けることと、会話・コンテキストでどの主体が扱われるかに影響します。機能を使えることだけで、データを公開してよいことにはなりません。

| アクセス設定 | 実装・受入確認 |
|---|---|
| 「サインイン必須」 | Pageへ権限を持つ利用者で、コンテキスト、会話開始、継続、エラー表示を確認します。比較回答の候補を読む・採用する`getTurns()`／`adoptCandidate()`は、サインイン済み利用者のPageでのみ確認済みです。 |
| 「サインイン任意」 | 未サインイン時とサインイン時の両方を確認します。未サインイン利用者のIDや役割がある前提で画面・プロンプトを組み立てません。比較回答の候補を読む・採用する機能は、未サインイン状態では使えないため、通常の会話導線へ分けます。 |
| 「匿名アクセス」 | 利用者固有の情報、利用者ごとのroom管理、比較回答の候補を読む・採用する機能を前提にしません。公開URLで実際に見える画面、Agentへ渡るデータ、実行できる操作を確認します。 |
| 「外部顧客認証」 | 外部顧客としてサインインした状態で、画面、会話、コンテキストを確認します。組織メンバーであることを前提にした表示・権限設計にせず、必要なデータだけを渡します。 |

> Pageのアクセス設定を`platform`、SDK、ブラウザコードで変更することはできません。「アクセス設定」で決めた公開範囲を狭めず、アクセスモードごとに実機で確認してください。

## うまくいかない場合

| 状況 | 最初に確認すること |
|---|---|
| `waitForReady()`が`null`になる | Page実行環境のブラウザか、SDKとPage実行環境の対応、初期表示のタイミングを確認します。APIキーを追加して回避しません。 |
| Agent呼出しが失敗する | 対象Page、アクセス設定、Agent、`status`と`errorMessage`、ストリームの`error`を確認します。処理済みか不明な依頼は再送しません。 |
| 比較の候補・effortを取得できない | `listCompareModels()`の結果、署名済み利用者、組織・Agentのモデル設定、Page実行環境を確認します。`candidateEfforts`が`false`ならeffortや同一モデルの重複を送信しません。 |
| 前の会話を踏まえない | 成功応答の`sessionId`を次の呼出しへ渡しているか、永続化が必要なのに`isEphemeral: true`にしていないかを確認します。 |
| roomの状態・履歴を取得できない | Pageセッション、対象Project、room IDの形式を確認します。別のroom IDを試して内容を探索しません。 |
| `openTask()`が失敗する | データまたは指示を渡しているか、値がオブジェクト・配列・`null`のいずれかか、Page実行環境で使っているかを確認します。 |

問い合わせには、SDKバージョン、アクセス設定、対象Page、日時、秘密情報を除くエラー、再現手順を記録します。APIキー、Cookie、Secret値、不要な会話本文は送付しません。

## チェックリスト

- [ ] Pageのブラウザコードを`createClient({ baseUrl: "" })`で初期化し、APIキーを置いていない
- [ ] `invoke()`または`invokeStream()`の成功・失敗を分けて画面へ反映している
- [ ] 継続する会話では成功応答の`sessionId`を保持している
- [ ] 比較UIでは`listCompareModels()`の候補・上限・Tool可否・effort可否に従っている
- [ ] `getContext()`の値をクライアント側の認可判定に使っていない
- [ ] `waitForReady()`が`null`の場合の画面を用意している
- [ ] `openTask()`へ必要最小限のデータだけを渡している
- [ ] 「サインイン必須」「サインイン任意」「匿名アクセス」「外部顧客認証」で必要な利用者状態を確認した
- [ ] APIキー、Cookie、Secret、不要な個人情報をコード・ログ・プロンプトに含めていない

## 次に読む記事

- [SDK Pages・Platform](../development/sdk-pages-and-platform.md)
- [Agents・会話・成果物API](../api/agents-conversations-artifacts-api.md)
- [Agentと会話して結果を確認する](../use/chat-with-agents.md)
