# 実行モード・認証・対応範囲

Studio SDKを使うコードは、動かす場所によって認証情報、利用できるモジュール、サポート対象が異なります。開発者は、ローカルのNode.js、PRS（Pages Rendering Service）内のブラウザ、PRS内のサーバーランタイムを混在させず、実行場所ごとにSDKクライアントを初期化してください。

Project APIキーの発行・失効・スコープは[Project APIキーを管理する](../operations/manage-project-api-keys.md)を参照してください。APIの呼出仕様は[Agents・会話・成果物API](../api/agents-conversations-artifacts-api.md)、Hosted Pageの認証経路は[Hosted Pageの認証・API経路](../page-runtime/hosted-page-auth-and-api-routes.md)で扱います。

## 関連記事

- [Project APIキーを管理する](../operations/manage-project-api-keys.md)
- [Agents・会話・成果物API](../api/agents-conversations-artifacts-api.md)
- [Hosted Pageの認証・API経路](../page-runtime/hosted-page-auth-and-api-routes.md)

## 始める前に

- これから動かすコードが、開発者のローカルNode.js、Page利用者のブラウザ、またはPRSのサーバー処理のどれかを決めます。同じリポジトリでも、ブラウザ用とサーバー用では設定を分けます。
- Node SDKとCLIで検証されているNode.jsは22以降です。通常の外部ブラウザでSDKを動かす用途は対象ではなく、ブラウザ対応はPRS内のコードに限られます。
- Project APIキーは秘密情報として扱います。値、値を含む環境変数、マスクされていない設定出力をチケット、ログ、ソース管理へ残さないでください。

> APIが有効なProject APIキーを受け付けることと、任意の外部本番ホスティングや外部連携がサポート対象であることは別です。限定ベータでは、ローカル開発または明示的に管理された開発自動化、ならびにPRSでホストされるPageを前提にします。

## 実行モードを選ぶ

| 実行モード | 動作場所 | 認証 | 利用できるモジュール | 用途と対象外 |
|---|---|---|---|---|
| ローカル（直接開発） | 開発環境または管理された開発自動化のNode.js 22以降 | 開発者が管理するProject APIキー | `agents`、`datasets`、`members`、`objects`、`scheduledTasks`、`storage`、`workflows`。Pageの操作はCLI | ローカル開発向けです。任意の外部本番ホスティングはサポート対象ではありません。 |
| PRSブラウザ | PRSでホストされたPageを開いたブラウザ | PRSセッション。APIキーは使用しません。 | `agents`、`datasets`、`objects`、`storage`、`platform` | Pageの利用者向け画面です。`members`、`workflows`、`scheduledTasks`、Page操作はこのモードの対象ではありません。 |
| PRSサーバー | PRS内のNode.js 22以降のサーバー専用ランタイム | プラットフォームがサーバー専用設定へ注入するProject APIキー | `agents`、`datasets`、`members`、`objects`、`storage`、`workflows` | Hosted Pageのサーバー処理です。外部連携用の認証方式ではなく、`platform`、`scheduledTasks`、Pageのデプロイ操作は対象外です。 |

`storage.downloadFolder()`は、PRSブラウザでは利用できません。APIキーを渡して回避せず、設計を見直してください。

## データセットと予定実行の認証境界

`datasets`は読み取り専用です。`datasets.list({ scope: "project" })`はProject APIキーまたはPRSセッションで使えますが、`scope: "organization"`は、Projectにリンクされていない組織データセットを読み取り可能な**署名済みPRSブラウザ利用者**だけが指定できます。Project APIキーで`organization`を指定すると403になります。`search()`は、Projectにリンクされたデータセット、またはその利用者が読める組織データセットだけを検索できます。

`scheduledTasks`は、開発者管理のProject APIキーでのみ使います。Appリリースのマニフェストでは`task:*`スコープを宣言できないため、PRSサーバーのプラットフォーム管理キーに移して回避することはできません。各タスクはキーの作成者として動作し、作成者が実行時に必要な権限を持たなければ失敗します。

## 1. ローカルではProject APIキーをサーバー側だけで使う

ローカルのNode.jsコードは、Project APIキーとAPIのベースURLを指定して初期化します。キーはシェルへ直接書くより、秘密情報管理から環境変数へ渡す方法を優先します。SDKのクライアント設定では`baseUrl`が必須で、`apiKey`は任意です。ただし、ローカルの直接開発でStudioリソースへ認証してアクセスするにはProject APIキーを渡します。

```ts
import { createClient } from "@japan-ai-inc/studio-sdk";

const apiKey = process.env["JAI_STUDIO_API_KEY"];
if (!apiKey) throw new Error("Project APIキーが設定されていません");

const studio = createClient({
  baseUrl: "https://api.japan-ai.co.jp",
  apiKey,
});

const agents = await studio.agents.list();
```

Project APIキーは`pak_`で始まる形式としてCLIとSDK READMEで案内されています。`jai-studio config show`が表示するキーは、先頭8文字と末尾4文字だけのマスク表示です。この表示値をSDKへ渡すと、SDKはマスク済みとしてエラーにします。設定画面の表示をコピーして復元しようとせず、保管済みの実値を安全な設定から渡してください。

> APIキーを、ブラウザバンドル、クライアント側JavaScript、ブラウザ公開用の環境変数、ログ、ソース管理へ入れてはいけません。`NEXT_PUBLIC_*`、`VITE_*`、`REACT_APP_*`、`PUBLIC_*`にAPIキーや元ProjectのリソースIDを設定しないでください。値を難読化しても、ブラウザへ配布すれば秘密にはなりません。

## 2. PRSブラウザではキーを設定しない

PRSで実行されるPageのブラウザコードは、現在のPRSセッションで認証されます。Project APIキーを設定・読取り・転送せず、空の`baseUrl`で初期化します。

```ts
import { createClient } from "@japan-ai-inc/studio-sdk";

const studio = createClient({ baseUrl: "" });
const result = await studio.agents.invoke("agent-id", { prompt: "こんにちは" });
```

SDKは実行中のブラウザに`window.JapanAI.getContext`があるかを確認します。これをPRSとして検出した`agents.invoke()`およびストリーム呼出しは、通常のAPI経路ではなくPRSの`/api/agent-chat`へ送られます。PRSのセッションCookieとPage情報が認証・経路選択に使われるため、ブラウザ側でキーを追加する必要はありません。

`platform`はPRSブラウザ専用のベータモジュールです。`studio.platform.isAvailable()`で利用可否を確認し、必要なら`studio.platform.waitForReady()`を使えます。PRS外ではコンテキストを取得できません。

## 3. PRSサーバーではプラットフォーム管理のキーをサーバー内に閉じる

PRSはHosted Pageのサーバー専用ランタイム設定へ、プラットフォーム管理のProject APIキーを注入する場合があります。サーバーコードだけで値を読み、通常のNodeクライアントとして初期化します。

```ts
import { createClient } from "@japan-ai-inc/studio-sdk";

const apiKey = process.env["JAI_PROJECT_API_KEY"];
if (!apiKey) throw new Error("PRSサーバーランタイムのAPIキーがありません");

const studio = createClient({
  baseUrl: "https://api.japan-ai.co.jp",
  apiKey,
});
```

このキーのライフサイクルはPRSホスティングが管理します。ランタイム設定を返すエンドポイント、APIレスポンス、画面用の初期データ、ブラウザ用環境変数へキーを含めたり、ブラウザへ転送したりしないでください。外部サービスの認証情報を扱う場合も、ブラウザへ返さない設計とSecretの取扱いを[Pageのサーバー処理・環境変数・Secret参照](page-server-env-and-secrets.md)で確認します。

## よくある誤解

### ブラウザからもAPIキーを渡せば、ローカルと同じモジュールを使える

使えません。PRSブラウザはPRSセッションによる利用者向けのモードであり、対応モジュールが異なります。キーを追加すると秘密情報を露出するだけで、サポート対象外の実行形態を正当化しません。必要な処理はPRSサーバーへ置くか、対応モジュールへ設計を合わせてください。

### `createClient()`が自動的に全モードを切り替える

実行モードは、コードの配置、`baseUrl`、キーの有無と保管場所で決まります。PRSの検出は、ブラウザの`window.JapanAI.getContext`を使う機能で行われます。

### PRSサーバーのキーを画面初期化用の設定に入れてよい

入れてはいけません。サーバー専用ランタイムに注入されたキーもProject APIキーです。ブラウザへ届くレスポンス、HTML、JavaScript、ソースマップ、ログは秘密情報の保管場所ではありません。

## うまくいかない場合

| 状況 | 確認・対応 |
|---|---|
| ローカルで認証エラーになる | 対象Project、キーの有効期限・失効、必要なスコープ、実値を渡しているかを確認します。マスク表示を使わず、[Project APIキーを管理する](../operations/manage-project-api-keys.md)に従って安全に発行・切替します。 |
| PageのブラウザでSDKが期待どおり動かない | PRS内で実行されているか、`baseUrl: ""`で初期化しているか、ブラウザ用コードにキーやサーバー専用設定を含めていないかを確認します。 |
| `members`または`workflows`をブラウザから使いたい | PRSブラウザで利用できるモジュールかを表で確認します。キーをブラウザへ渡すのではなく、PRSサーバーで処理できるかを検討します。 |
| `platform`をローカルで使おうとしている | `platform`はPRSブラウザ専用です。`isAvailable()`の結果を確認し、PRS外では代替の画面処理を実装します。 |
| PRSサーバーでキーが見つからない | 実行場所がサーバー専用ランタイムか、必要なホスティング設定があるかを確認します。値を仮のキーで画面側へ埋め込まず、管理担当者へ確認します。 |

問い合わせには、対象Project、実行モード、SDKのバージョン、実行場所、発生日時、エラー全文（キー・トークンを除く）、再現手順を記録します。Project APIキー、Cookie、Secret値、認証ヘッダーを添付してはいけません。

## チェックリスト

- [ ] 実行場所をローカル、PRSブラウザ、PRSサーバーのいずれかに区分した
- [ ] モードに対応するモジュールだけを使用している
- [ ] ローカルとPRSサーバーのキーをサーバー側の安全な設定に限定した
- [ ] PRSブラウザを`createClient({ baseUrl: "" })`で初期化し、キーを読んでいない
- [ ] ブラウザバンドル、公開用環境変数、ログ、ソース管理にキーを含めていない
- [ ] APIキーの発行・スコープ・失効を[Project APIキーを管理する](../operations/manage-project-api-keys.md)で確認した

## 次に読む記事

- [開発環境・バージョン・互換性](dev-environment-and-versions.md)
- [Hosted Pageの認証・API経路](../page-runtime/hosted-page-auth-and-api-routes.md)
- [Pageのサーバー処理・環境変数・Secret参照](page-server-env-and-secrets.md)
