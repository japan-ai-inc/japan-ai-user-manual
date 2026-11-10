# SDK Pages・Platform

Studio SDKの`pages`と`platform`は、目的と実行場所が異なります。`pages`はローカルまたは管理された開発自動化でZIPを登録・デプロイするクライアントです。`platform`は、PRS（Pages Rendering Service）で表示中のPageの**ブラウザ内**からコンテキスト、Studioタスク、Pageセッションと連携するベータ機能です。

デプロイ・アクセス設定・Page内連携は、目的に合う経路を選んでください。

## 関連記事

- [実行モード・認証・対応範囲](execution-modes-and-auth.md)
- [Pageをローカル確認してデプロイする](local-preview-and-deploy-page.md)
- [Pageの画面遷移・ディープリンク](page-navigation-and-deep-links.md)
- [画面からPageを登録・公開する](../build/register-and-publish-pages.md)
- [Pageの仕様・制限](../build/page-specs-and-limits.md)
- [CLI Pages](../cli/cli-pages.md)
- [Pages API](../api/pages-api.md)
- [Pageとチャット・コンテキストの連携](../page-runtime/page-chat-and-context.md)

## 始める前に

- SDK **0.11.0**ではNode.js 22以上を使います。`platform`はPRSでホストされたPage内で使います。
- ZIPの登録・更新は`pages`またはCLI、連携は`platform`です。
- APIキーは安全な設定に限定し、ブラウザ用JavaScript、公開環境変数、ログへ入れません。

> `pages`と`platform`は利用者や「アクセス設定」を変更するAPIではありません。アクセスモードや共有URLは対象Pageの「アクセス設定」で確認します。

## 役割を選ぶ

| 目的 | 使うもの | 実行場所 | 認証・注意点 |
|---|---|---|---|
| 新しいPageまたは既存Pageの版をZIPから登録する | `pages`または`jai-studio pages deploy` | ローカルNode.jsまたは管理された開発自動化 | Project APIキーをサーバー側だけで使います。SDKの`pages`は、アップロード準備、ZIP送信、デプロイを提供します。 |
| Pageの一覧を取得する | Studio画面で確認する | Studio | SDK 0.11.0の`pages`に一覧取得メソッドはありません。 |
| Pageのアクセスモード・公開URLを設定する | 「アクセス設定」 | Studio | SDK 0.11.0の`pages`と`platform`にアクセス制御の変更メソッドはありません。[画面からPageを登録・公開する](../build/register-and-publish-pages.md)に従います。 |
| 表示中のPageの利用者・Project・Pageを知る | `platform.getContext()`または`waitForReady()` | PRS内のブラウザ | PRSセッションを使います。APIキーや`baseUrl`は不要です。 |
| PageからStudioのタスク画面へデータを渡す、Pageセッションを終了する | `platform.openTask()`、`platform.logout()` | PRS内のブラウザ | PRS専用のベータ機能です。Page外やサーバーランタイムの機能ではありません。 |

`platform`は任意URLへの移動APIではありません。ディープリンクは[Pageの画面遷移・ディープリンク](page-navigation-and-deep-links.md)に従います。

## 1. `pages`またはCLIでデプロイする

SDKの`pages`は、`prepareUpload()`（既存Pageは`prepareVersionUpload(pageId)`）、`uploadZip()`、`deployUploadedZip()`の3段階です。ZIP本体には`Blob`、`ArrayBuffer`、`Uint8Array`、`ReadableStream`を渡せます。既定タイムアウトはZIP送信が5分、デプロイが10分です。

```ts
import { createClient } from "@japan-ai-inc/studio-sdk";

const studio = createClient({
  baseUrl: "https://api.japan-ai.co.jp",
  apiKey: process.env["JAI_STUDIO_API_KEY"],
});

const bytes = new Uint8Array(await readBuiltPageZip());
const upload = await studio.pages.prepareUpload({
  fileName: "page-release.zip",
  contentType: "application/zip",
});
await studio.pages.uploadZip(upload, bytes);

const deployed = await studio.pages.deployUploadedZip({
  uploadPath: upload.uploadPath,
  zipFileName: "page-release.zip",
  name: "受注ダッシュボード",
});
console.log(deployed.pageId, deployed.studioUrl, deployed.runtimeUrl);
```

既存Pageの更新では同じ`pageId`を渡します。結果にはPage ID、StudioのURL、実行用URL、版、ファイル数、日時、再ビルド要求の有無が返ります。同じ流れはCLIでも実行できます。

```bash
jai-studio --profile my-project pages deploy ./dist \
  --name "受注ダッシュボード" --json
```

CLIには`--page-id`、`--timeout-ms`、`--save-page-metadata`があります。ディレクトリ入力ではCLIがZIPを作り、Page IDがあれば更新先として利用します。詳細は[Pageをローカル確認してデプロイする](local-preview-and-deploy-page.md)を確認してください。

> `runtimeUrl`が`null`でも、`studioUrl`から対象Pageを確認します。ZIPの構造・容量は[Pageの仕様・制限](../build/page-specs-and-limits.md)を参照してください。

## 2. PRS内で`platform`を初期化する

PRSブラウザでは、空の`baseUrl`で作ります。`isAvailable()`は利用可否を確認し、`getContext()`は利用不可なら`null`を返します。`waitForReady()`は既定で5,000ミリ秒待ち、タイムアウト時も`null`です。

```ts
import { createClient } from "@japan-ai-inc/studio-sdk";

const studio = createClient({ baseUrl: "" });
const context = await studio.platform.waitForReady();

if (!context) {
  showUnavailableMessage();
} else {
  showCurrentUser(context.userName);
  const result = await studio.platform.openTask(
    [{ orderId: "42" }],
    "この注文を確認してください"
  );
  console.log(result.roomId);
}
```

コンテキストには利用者、組織、Project、Page、Custom Objectの識別情報が含まれます。`openTask()`はデータと任意の指示をStudioタスクへ渡し、`roomId`を返します。`logout()`は現在のPageセッション対象を終了してPageを再読込みします。

Agent呼出しは`studio.agents.invoke()`を優先します。

### App設定を開く

SDK 0.11.0では、`platform.openAppSettings(tab?)`で、このPage上にインストール済みAppの設定を開けます。指定できるタブは`"basic"`、`"members"`、`"iamRoles"`、`"connectors"`、`"secrets"`、`"dataIntegration"`です。利用できないタブは非表示となるため、別のタブが開く場合があります。

```ts
await studio.platform.openAppSettings("connectors");
```

PRSがこの機能を提供しない場合は呼出しが失敗します。`isAvailable()`が`true`であっても、対応するPRSとSDK 0.11.0を確認してください。

## よくある誤解

### Pageの公開範囲を`platform`で変更できる

できません。`platform`はPRS PageとStudio機能を連携するブラウザ用モジュールです。アクセスモードや共有URLは「アクセス設定」で確認します。

### PRSのブラウザなら`pages`でデプロイしてよい

しません。`pages`は開発・制御用の操作であり、PRSブラウザで使えるモジュールではありません。Pageのコードから自分自身や別Pageを更新する設計にせず、ローカルまたは管理された開発自動化でデプロイします。

### `platform`が使えないとき、APIキーをブラウザへ追加すればよい

いけません。`isAvailable()`または`waitForReady()`で分岐し、キーをブラウザへ渡しません。

## うまくいかない場合

| 状況 | 確認・対応 |
|---|---|
| `pages`でデプロイに失敗する | ZIP、対象Project、Page ID、認証設定を確認します。 |
| Page一覧やアクセス設定のSDKメソッドを探している | `pages`には該当メソッドがありません。StudioのPage一覧と「アクセス設定」を使います。 |
| `waitForReady()`が`null`になる | PRS内のブラウザで実行しているか、実行タイミングを確認します。 |
| `openTask()`または`logout()`が失敗する | PRSで該当機能が利用可能か確認します。 |
| `openAppSettings()`が失敗する | SDK 0.11.0と対応するPRSで実行しているか確認します。 |

問い合わせには、SDKバージョン、実行モード、対象Page、発生日時、エラー、再現手順を記録します。APIキー、Cookie、Secret値は添付しません。

## チェックリスト

- [ ] デプロイ操作とPage内連携を分けた
- [ ] ZIPの登録・更新をローカルまたは管理された開発自動化で行った
- [ ] Pageの一覧・アクセスモードはStudioで確認した
- [ ] PRSブラウザでは`createClient({ baseUrl: "" })`を使い、APIキーを読んでいない
- [ ] `platform`の呼出し前に利用可否または準備完了を確認した
- [ ] `openTask()`を任意URLへの遷移に使っていない
- [ ] `openAppSettings()`を使う場合はSDK 0.11.0と対応するPRSを確認した

## 次に読む記事

- [Pageをローカル確認してデプロイする](local-preview-and-deploy-page.md)
- [Pageの画面遷移・ディープリンク](page-navigation-and-deep-links.md)
- [画面からPageを登録・公開する](../build/register-and-publish-pages.md)
- [CLI Pages](../cli/cli-pages.md)
- [Pages API](../api/pages-api.md)
- [Pageとチャット・コンテキストの連携](../page-runtime/page-chat-and-context.md)
