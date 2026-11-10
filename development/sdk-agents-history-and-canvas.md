# SDK Agents：会話履歴・Canvas

Studio SDKで会話履歴とCanvasを安全に扱う方法を説明します。

実行は[SDK Agents：実行・ストリーム](sdk-agents-invoke-and-stream.md)、HTTP APIは[Agents・会話・成果物API](../api/agents-conversations-artifacts-api.md)を参照してください。

## 関連記事

- [SDK Agents：実行・ストリーム](sdk-agents-invoke-and-stream.md)
- [実行モード・認証・対応範囲](execution-modes-and-auth.md)
- [Project APIキーを管理する](../operations/manage-project-api-keys.md)
- [Agents・会話・成果物API](../api/agents-conversations-artifacts-api.md)

## 始める前に

- `@japan-ai-inc/studio-sdk` **0.11.0**とNode.js **22以降**を使います。SDKは1.0未満のため、完全なバージョンを固定します。
- ローカルとPRSサーバーではAPIキーをサーバー側だけに保管します。PRSブラウザではキーを渡さず`createClient({ baseUrl: "" })`で初期化します。
- 会話を継続する場合は、成功応答の`sessionId`を安全なアプリケーション状態に保持します。

> Project APIキーを共有するブラウザアプリでは、利用者別の会話閲覧範囲を実現できません。キー、Cookie、認証ヘッダー、会話に含まれる秘密情報を、ログやブラウザ用コードへ残してはいけません。

## 1. `sessionId`で会話を継続する

最初の`invoke()`または`invokeStream()`で得た`sessionId`を、後続の`input.sessionId`へ渡すと会話を継続できます。空の会話を先に作る必要がある場合は、`createRoom(agentId)`の`roomId`を使います。

```ts
const first = await studio.agents.invoke(agentId, {
  prompt: "提案書の論点を3つ挙げてください",
});
if (first.status === "failed") throw new Error(first.errorMessage);

const followUp = await studio.agents.invoke(agentId, {
  sessionId: first.sessionId,
  prompt: "2つ目を具体例付きで詳しくしてください",
});
console.log(followUp.text);
```

`isEphemeral: true`は会話履歴へ残さない実行です。履歴読取り、会話継続、Canvas取得を前提にする処理では指定しません。`enableHandoffLongRunJob: true`には永続的な会話が必要で、`isEphemeral: true`との同時指定はSDKが拒否します。

## 2. メッセージ履歴をページ単位で読む

`getMessages(roomId, query?)`は、`roomId`、`messages`、`totalCount`、`nextCursor`を返します。各メッセージには`id`、`role`、`content`、`createdAt`があります。`limit`は**1〜100**、省略時は**50**です。`nextCursor`が`null`になるまで、返却値を次回の`cursor`へ渡します。

```ts
async function readAllMessages(roomId: string) {
  const history = [];
  let cursor: string | undefined;
  do {
    const page = await studio.agents.getMessages(roomId, { limit: 50, cursor });
    history.push(...page.messages);
    cursor = page.nextCursor ?? undefined;
  } while (cursor);
  return history;
}
```

任意の`cursor`を組み立てず、直前の応答の値だけを渡してください。ページング中に新しいメッセージが追加される可能性があるため、画面では取得済み`id`による重複排除を検討します。会話が見つからない、またはアクセスできない場合は404になり得るため、別のroom IDへ自動的に切り替えません。

## 3. Canvasを一覧から個別に取得する

Canvasを使うAgentは、HTMLスライド、テキスト、コードなどを会話のCanvas文書として保存できます。`listCanvasArtifacts(roomId, query?)`は古い順のメタデータだけを返し、本文は含みません。`limit`と`cursor`は履歴と同じく**1〜100**、省略時は**50**です。

```ts
const { canvasArtifacts } = await studio.agents.listCanvasArtifacts(roomId, {
  limit: 20,
});
const deck = canvasArtifacts.find(item => item.documentType === "hslides");
if (!deck) throw new Error("この会話にスライドCanvasはありません");

const artifact = await studio.agents.getCanvasArtifact(roomId, deck.id);
console.log(artifact.name, artifact.contentHash);
```

`getCanvasArtifact(roomId, canvasArtifactId)`は、保存された`content`と`contentHash`を含む1件を返します。別roomのIDは取得できません。`content`は書込みツールが保存したJSONで、型を固定して扱う前に`documentType`と実際の値を確認します。旧形式では`documentType`が`null`で、`{ text }`だけの場合があります。

HTMLスライドの`content.text`はスライド定義を含むJSON文字列です。スライドHTMLやassets内の`room://`参照は解決されずに返るため、外部URLとして読み込みません。

> Canvasの`content`やHTMLを検証せずに`innerHTML`へ設定してはいけません。Agent出力・添付ファイル由来のデータとして扱い、用途に応じてサニタイズ、隔離、Content Security Policyを実装してください。

## 候補比較の結果を読む

PRSブラウザで候補比較を使った場合、`getTurns(roomId)`で保存済みのターンと候補をページ単位で読みます。候補は`index`で識別します。同じモデルを複数のeffortで実行できるため、モデル名で候補を対応付けません。

```ts
const page = await studio.agents.getTurns(roomId, { limit: 20 });
for (const turn of page.turns) {
  for (const candidate of turn.candidates) {
    console.log(candidate.index, candidate.model, candidate.effort, candidate.references);
  }
}
```

`attachments`にはターンへ添付した画像・ファイルのメタデータ、候補の`references`には回答中の`[n]`に対応するWeb参照が含まれます。テキストだけの回答や古い接続先では空配列になり得ます。ストリーム終了時に`adoptedIndex`がない場合は「未採用」と断定せず、`getTurns()`で再確認してから、最後のターンに限り`adoptCandidate()`を提示します。

## 4. PRS PageからHTMLスライドを書込み・公開する

SDK **0.6.0以降**では、署名済み利用者が開いたPRS Pageから、APIで作成したroom内のHTMLスライド（`hslides`）を書込みできます。APIキーによる呼出しは使えません。書込みの成否が不明な場合に重複作成しないよう、Canvas IDは呼出し側で作成・保持します。

```ts
const canvasId = crypto.randomUUID();
await studio.agents.putCanvasArtifact(roomId, canvasId, {
  name: "提案書",
  content: {
    document_type: "hslides",
    text: JSON.stringify(manifest),
  },
});

await studio.agents.publishCanvasArtifact(roomId, canvasId, "ORGANIZATION");
// 組織メンバーがサインイン後にリンクを開けます。
```

同じIDへの書込みは、名前と本文を置き換えます。`content.text`はUTF-8で最大**8MiB**、`name`は最大**255文字**です。`room://`参照を含むデッキは書込み・公開できません。対象roomに書込み権限があること、Pageセッションで署名済みであること、接続先がCanvas書込み経路を提供することが必要です。未対応の接続先では404になり得ます。

SDK **0.11.0**では`publishCanvasArtifact(roomId, canvasArtifactId, scope)`に公開範囲を明示します。`"PUBLIC"`はリンクを知る利用者向け、`"ORGANIZATION"`はサインイン済みの組織メンバー向けです。リンク公開が組織で許可されない場合は`"PUBLIC"`を使えません。公開を解除するには`unpublishCanvasArtifact()`を使いますが、room自体がセッションなしで読める状態では解除できません。

> 公開範囲はデッキの閲覧リンクに関するものです。Canvas本文に秘密情報、利用者の個人情報、認証情報を含めないでください。0.6.0の2引数の公開呼出しは0.11.0で使えません。更新時は必ず第3引数の公開範囲を指定します。

## よくある誤解

### `sessionId`を渡せば、必ず履歴へ保存される

`isEphemeral: true`の実行では履歴を前提にできません。会話継続、長時間実行の結果確認、Canvas取得が必要なら、永続的な会話として実行します。

### Canvas一覧に本文も含まれる

含まれません。一覧は候補選択のためのメタデータです。本文は選んだIDで`getCanvasArtifact()`を呼び出して取得します。

### APIキーでCanvasを書込み・公開できる

できません。SDK 0.6.0以降の書込み・公開は、署名済み利用者が開いたPRS Pageの経路向けです。キーをブラウザへ渡して回避してはいけません。

## うまくいかない場合

| 状況 | 確認・対応 |
|---|---|
| 後続の応答が前の会話を踏まえない | 前回の成功応答の`sessionId`を同じAgentへの次回入力へ渡したか、`isEphemeral: true`でないかを確認します。 |
| 履歴やCanvasが404になる | room ID、Project、認証経路、会話の所有者、接続先の対応状況を確認します。別IDを試して探索しません。 |
| Canvas本文が期待した形式でない | `documentType`、`content`、`text`の型を確認します。形式を推測して処理を継続しません。 |
| Canvasの書込み・公開に失敗する | SDK 0.11.0、署名済みPRS Page、roomへの書込み権限、デッキ形式、Canvas ID、接続先の対応状況を確認します。 |
| `PUBLIC`で公開できない | 組織でリンク公開が許可されているか確認し、要件に合えば`ORGANIZATION`を選びます。 |

問い合わせには、対象Project、SDK版、実行モード、Agent識別子、room ID、Canvas ID、日時、HTTP状態、再現手順を記録します。APIキー、Cookie、認証ヘッダー、Canvas本文、秘密情報は送付しないでください。

## チェックリスト

- [ ] 成功応答の`sessionId`を保持し、後続の会話に渡した
- [ ] 永続化が必要な会話で`isEphemeral: true`を指定していない
- [ ] `nextCursor`を使い、履歴とCanvasを必要なページまで取得した
- [ ] Canvasをroom IDとCanvas IDの組で個別取得した
- [ ] CanvasのJSON・HTML・`room://`参照を未検証で表示・実行していない
- [ ] Canvas書込みでは固定したCanvas IDを使い、公開範囲を明示した
- [ ] APIキー、Cookie、認証ヘッダー、秘密情報をログへ含めていない

## 次に読む記事

- [SDK Agents：実行・ストリーム](sdk-agents-invoke-and-stream.md)
- [Agents・会話・成果物API](../api/agents-conversations-artifacts-api.md)
