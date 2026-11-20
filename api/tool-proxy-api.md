# Tool Proxy API

Pageからツールを直接実行するための経路です。これは **Page実行環境内からのみ呼び出せる経路** であり、公開REST APIやProject APIキーの代替ではありません。Pageのブラウザコードでは`fetch`で相対URLを呼び出します。Studio SDKには、この経路を包む公開クライアントはありません。

## 関連記事

- [実行モード・認証・対応範囲](../development/execution-modes-and-auth.md)
- [外部ツール・データ連携・MCPを管理する](../operations/manage-external-tools-integrations-mcp.md)
- [API／SDK共通のエラー・タイムアウト・制限](../development/api-sdk-errors-timeouts-limits.md)
- [Pageの仕様・制限](../build/page-specs-and-limits.md)

## この記事の範囲

Tool Proxyは、**Page実行環境**（StudioでホストされたPageを実行する環境）がPageに結び付いたセッションから組織・プロジェクト・利用者のコンテキストを取得し、ツール実行基盤へ渡す経路です。production Public API（`https://api.japan-ai.co.jp`）のProject APIキー用エンドポイントではありません。配布SDK 0.11.0にはこの経路を呼ぶ専用クライアントもありません。そのため、次の用途には使いません。

- `https://api.japan-ai.co.jp`にProject APIキーを指定して外部アプリから呼び出すこと
- サーバー間連携用の公開APIを作ること
- 副作用のあるツールをPageから実行すること

> Project APIキー、`x-api-key`、`Authorization: Bearer`をこの経路のブラウザリクエストに設定する必要はありません。また、利用者の組織・プロジェクト・利用者をリクエスト本文やヘッダーで指定して切り替えることもできません。

## 始める前に

1. Pageを公開・実行できる状態にします。Pageへのアクセスに成功すると、Pageに紐付く認証済みセッションで`/api/*`が処理されます。
2. Pageのアクセス条件を満たす利用者として開きます。認証情報がない、無効、またはPage・組織・プロジェクトの組合せが一致しない要求は拒否されます。匿名アクセスのセッションでは401になり、Tool Proxyは使えません。
3. PageのAPIアクセスがブラウザからの呼出を許可する設定であることを確認します。サーバー専用に設定されたPageでは、ブラウザからのTool Proxy呼出は利用できません。
4. 外部アクセスを用いるPageでは、そのアクセスモードで有効なセッションが必要です。アクセス条件を満たさなくなったセッションは処理されません。

## API仕様

### ツール一覧を確認する

|項目|内容|
|---|---|
|メソッド・パス|`GET /api/tools/_catalog`|
|認証|Page実行環境の有効なセッション|
|リクエスト|本文なし|
|成功|`200`。JSON配列。各要素は`name`、`description`、`params_schema`、`output_type`、`side_effects`、`cache_ttl`|
|対象|プラットフォームの読み取り専用ツールすべて。組織やProjectでは絞り込まれません。利用前にカタログの`side_effects`を確認します。|

一覧は組織・Projectで絞り込まれないため、一覧に表示されるツールでも、そのPageの組織・Projectで実行できるとは限りません。実行結果は必ずHTTPステータスで判定します。

```ts
const response = await fetch('/api/tools/_catalog');
if (!response.ok) throw new Error(`catalog: ${response.status}`);
const tools = await response.json();
```

### ツールを実行する

|項目|内容|
|---|---|
|メソッド・パス|`POST /api/tools/{toolName}`|
|`toolName`|カタログの`name`をURLパスに指定します。空の名前は指定できません。|
|Content-Type|`application/json`|
|本文|`{"params": { ... }}`。`params`を省略した場合は空オブジェクトとして扱われます。|
|本文上限|1 MiB|
|成功|`200`。`success`、`tool`、`data`、必要に応じて`files`、`images`を返します。|
|応答上限|ツール実行基盤から受け取る本文は10 MiBまでです。超過した本文は途中で切られ、JSON解析に失敗する場合があります。|

`params`のキー、型、必須項目はツールごとに異なります。実行前にカタログの`params_schema`を参照し、利用者入力を検証してください。

`files`と`images`は、ツールが成果物を返した場合に含まれる参照です。

```ts
const toolName = 'カタログで確認した名前';
const response = await fetch(`/api/tools/${encodeURIComponent(toolName)}`, {
  method: 'POST',
  headers: { 'Content-Type': 'application/json' },
  body: JSON.stringify({ params: { /* ツール固有の引数 */ } }),
});
const bodyText = await response.text();
if (!response.ok) {
  // Page実行環境が返すエラーはJSONとは限りません（プレーンテキストの場合もあります）
  throw new Error(`tool ${toolName}: ${response.status} ${bodyText.slice(0, 200)}`);
}
const result = JSON.parse(bodyText);
if (result.success === false) throw new Error(`tool ${toolName} failed`);
```

## 実行できるツールとキャッシュ

実行対象は、名前から解決でき、実行基盤がその引数での副作用を`none`と判定したツールです。カタログの`side_effects`だけでなく、実行時にも引数を使って判定され、副作用ありと判定されると`403`・`TOOL_SIDE_EFFECTS_BLOCKED`になります。送信・更新などの処理には、この経路ではなく、承認と接続を設計したAgentやWorkflowを使います。

ツール実行は秒間10、バースト50の制限を受けます。この制限は**Page実行環境全体で共有**され、Pageごとではありません。上限を超えると`429`です。ツール側がキャッシュ時間を返す場合、同じ組織・Project、ツール、引数で結果が再利用されることがあります。利用者ごとの結果は利用者単位に分離され、安定した利用者識別子が得られない場合は利用者別キャッシュを書き込みません。応答の`X-Cache`は`HIT`または`MISS`です。

> キャッシュ可能な応答であっても、個人情報や最新性が必須の情報を画面に長く保持しないでください。キャッシュ時間はツール実装により異なり、呼出側から指定できません。

## うまくいかない場合

|状態|確認・対応|
|---|---|
|`400`|JSONが不正、または`toolName`が空です。`Content-Type`と`JSON.stringify`の結果を確認します。|
|`401`|Pageセッションがない、有効ではない、または匿名アクセスのセッションです。Pageを開き直し、アクセス条件を確認します。匿名アクセスのPageではTool Proxyを使えません。|
|`403`|Pageとの対応関係、信頼済みOrigin、PageのAPIアクセス条件、副作用判定を確認します。|
|`404`|指定したツールが見つかりません。カタログから`name`を取り直します。|
|`413`|本文が1 MiBを超えています。入力・添付データを縮小し、必要なら別の保存方法を検討します。|
|`429`|制限は実行環境全体で共有されるため、他のPageの利用状況でも発生します。ツール実行の短時間の連続呼出を減らし、UI側で待機・再試行を実装します。共通方針は[API／SDK共通のエラー・タイムアウト・制限](../development/api-sdk-errors-timeouts-limits.md)を参照してください。|
|`502`・`503`・`504`|ツール実行基盤または認証・接続が一時的に利用できない可能性があります。利用者に再試行可能な表示を出します。|
|`500`|ツールの解決または実行に失敗しています。HTTPステータスと応答本文（テキスト）を記録して管理者へ連絡します。|

成功時の応答は`success`、`tool`、`data`、必要に応じて`files`・`images`を含むJSONです。失敗時にPage実行環境が返すエラーは`{success, error: {code, message}}`の形式とは限らず、プレーンテキストの場合もあります。`result.error?.message`に頼らず、まずHTTPステータスで判定し、本文はテキストとして読んでからJSONかどうかを扱ってください。

## チェックリスト

- [ ] Page実行環境内の相対パス`/api/tools/...`を使用している
- [ ] カタログの`name`と`params_schema`に基づいて入力を作っている
- [ ] `POST`の本文をJSONにし、1 MiB以下にしている
- [ ] 副作用なしのツールだけを用途に選んでいる
- [ ] `401`、`403`、`429`、一時的な`5xx`をUIで区別し、エラー本文がJSONでない場合も処理している
- [ ] Project APIキーで外部からこの経路を呼ぼうとしていない

## 次に読む記事

- [実行モード・認証・対応範囲](../development/execution-modes-and-auth.md)
- [外部ツール・データ連携・MCPを管理する](../operations/manage-external-tools-integrations-mcp.md)
- [Pageの仕様・制限](../build/page-specs-and-limits.md)
