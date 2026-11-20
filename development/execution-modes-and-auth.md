# 実行モード・認証・対応範囲

Studio SDKを使うコードは、動かす場所によって認証情報、利用できるモジュール、サポート対象が異なります。開発者は、ローカルのNode.js、StudioでホストされたPageのブラウザ、Page実行環境（PRS）内のサーバーランタイムを混在させず、実行場所ごとにSDKクライアントを初期化してください。

Project APIキーの発行・失効・スコープは[Project APIキーを管理する](../operations/manage-project-api-keys.md)を参照してください。APIの呼出仕様は[Agents・会話・成果物API](../api/agents-conversations-artifacts-api.md)、Hosted Pageの認証経路は[Hosted Pageの認証・API経路](../page-runtime/hosted-page-auth-and-api-routes.md)で扱います。

## 関連記事

- [Project APIキーを管理する](../operations/manage-project-api-keys.md)
- [Agents・会話・成果物API](../api/agents-conversations-artifacts-api.md)
- [Hosted Pageの認証・API経路](../page-runtime/hosted-page-auth-and-api-routes.md)

## 始める前に

- これから動かすコードが、開発者のローカルNode.js、Page利用者のブラウザ、またはPage実行環境のサーバー処理のどれかを決めます。同じリポジトリでも、ブラウザ用とサーバー用では設定を分けます。
- Node SDKとCLIにはNode.js 22以降が必要です。通常の外部ブラウザでSDKを動かす用途は対象ではなく、ブラウザ対応はStudioでホストされたPage内のコードに限られます。
- Project APIキーは秘密情報として扱います。値、値を含む環境変数、マスクされていない設定出力をチケット、ログ、ソース管理へ残さないでください。

> APIが有効なProject APIキーを受け付けることと、任意の外部本番ホスティングや外部連携がサポート対象であることは別です。限定ベータでは、ローカル開発または明示的に管理された開発自動化、ならびにStudioでホストされたPageを前提にします。

## 実行モードを選ぶ

| 実行モード | 動作場所 | 認証 | 利用できるモジュール | 用途と対象外 |
|---|---|---|---|---|
| ローカル（直接開発） | 開発環境または管理された開発自動化のNode.js 22以降 | 開発者が管理するProject APIキー | `agents`、`datasets`、`members`、`objects`、`scheduledTasks`、`storage`、`workflows`。Pageの操作はCLI | ローカル開発向けです。任意の外部本番ホスティングはサポート対象ではありません。 |
| Page実行環境（ブラウザ） | StudioでホストされたPageを開いたブラウザ | Page実行環境のセッション。APIキーは使用しません。 | `agents`、`datasets`、`objects`、`storage`、`platform` | Pageの利用者向け画面です。`members`と`workflows`はPage実行環境が中継するため技術的には呼び出せますが、現時点ではベータのサポート範囲外です（予告なく変更される可能性があります）。`scheduledTasks`とPage操作は使えません。 |
| Page実行環境（サーバー） | Page実行環境内のNode.js 22以降のサーバー処理 | 開発者がルートの`.env`で定義した環境変数のProject APIキー（自動では注入されません。Appとして公開するとプラットフォーム管理のキーに置き換わります） | `agents`、`datasets`、`members`、`objects`、`storage`、`workflows` | Hosted Pageのサーバー処理です。外部連携用の認証方式ではなく、`platform`、`scheduledTasks`、Pageのデプロイ操作は対象外です。 |

`storage.downloadFolder()`は、Page実行環境のブラウザでは利用できません。APIキーを渡して回避せず、設計を見直してください。

## データセットと予定実行の認証境界

SDKの`datasets`モジュールは読み取り専用（`list()`と`search()`）です。公開APIは[Datasets API](../api/datasets-api.md)を参照してください。`datasets.list({ scope: "project" })`はProject APIキーまたはPage実行環境のセッションで使えますが、`scope: "organization"`は、Projectにリンクされていない組織データセットを読み取り可能な**サインイン済みのPage利用者（ブラウザ）**だけが指定できます。Project APIキーで`organization`を指定すると403になります。`search()`は、Projectにリンクされたデータセット、またはその利用者が読める組織データセットだけを検索できます。

`scheduledTasks`は、開発者管理のProject APIキーでのみ使います。Appリリースのマニフェストでは`task:*`スコープを宣言できないため、Page実行環境のサーバーで使うプラットフォーム管理キーに移して回避することはできません。各タスクはキーの作成者として動作し、作成者が実行時に必要な権限を持たなければ失敗します。

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

Project APIキーは`pak_`で始まります。`jai-studio config show`が表示するキーは、先頭8文字と末尾4文字だけのマスク表示です。この表示値をSDKへ渡すと、SDKはマスク済みとしてエラーにします。設定画面の表示をコピーして復元しようとせず、保管済みの実値を安全な設定から渡してください。

> APIキーを、ブラウザバンドル、クライアント側JavaScript、ブラウザ公開用の環境変数、ログ、ソース管理へ入れてはいけません。`NEXT_PUBLIC_*`、`VITE_*`、`REACT_APP_*`、`PUBLIC_*`にAPIキーや元ProjectのリソースIDを設定しないでください。値を難読化しても、ブラウザへ配布すれば秘密にはなりません。

## 2. Page実行環境のブラウザではキーを設定しない

StudioでホストされたPageのブラウザコードは、現在のPage実行環境のセッションで認証されます。Project APIキーを設定・読取り・転送せず、空の`baseUrl`で初期化します。

```ts
import { createClient } from "@japan-ai-inc/studio-sdk";

const studio = createClient({ baseUrl: "" });
const result = await studio.agents.invoke("agent-id", { prompt: "こんにちは" });
```

SDKは実行中のブラウザに`window.JapanAI.getContext`があるかを確認します。これによりPage実行環境と判定した場合、`agents.invoke()`およびストリーム呼出しは、通常のAPI経路ではなくPage実行環境の`/api/agent-chat`へ送られます。Page実行環境のセッションCookieとPage情報が認証・経路選択に使われるため、ブラウザ側でキーを追加する必要はありません。

このモードでは、Agent呼出しの`systemPrompt`と`skipHistory`は無視されます（エラーにはなりません）。また`isEphemeral`を指定しない呼出しは一時的な会話として扱われ、履歴を読み書きしません。同じ会話を続けるには`isEphemeral: false`を指定します（[SDK Agents：実行・ストリーム](sdk-agents-invoke-and-stream.md)を参照）。

`platform`はPage実行環境のブラウザ専用のベータモジュールです。`studio.platform.isAvailable()`で利用可否を確認し、必要なら`studio.platform.waitForReady()`を使えます。Page実行環境の外ではコンテキストを取得できません。`platform.openTask()`はPageがJAPAN AIの画面に埋め込まれているときだけ使え、Pageを単独で開いた場合は`UNSUPPORTED`で拒否されます。

## 3. Page実行環境のサーバーではキーをサーバー内に閉じる

Project APIキーは、Page実行環境のサーバー処理へ自動では注入されません。サーバー処理で使う環境変数名は、開発者がルートの`.env`で決めます。Appとして公開すると、その値はプラットフォーム管理のキーに置き換わります。値はサーバーコードだけで読み、通常のNodeクライアントとして初期化します。

```ts
import { createClient } from "@japan-ai-inc/studio-sdk";

// STUDIO_API_KEY は例です。ルートの .env で定義した変数名を使います。
const apiKey = process.env["STUDIO_API_KEY"];
if (!apiKey) throw new Error("サーバー処理用のAPIキーが設定されていません");

const studio = createClient({
  baseUrl: "https://api.japan-ai.co.jp",
  apiKey,
});
```

ランタイム設定を返すエンドポイント、APIレスポンス、画面用の初期データ、ブラウザ用環境変数へキーを含めたり、ブラウザへ転送したりしないでください。`.env`の書き方、外部サービスの認証情報、Secretの取扱いは[Pageのサーバー処理・環境変数・Secret参照](page-server-env-and-secrets.md)で確認します。

## よくある誤解

### ブラウザからもAPIキーを渡せば、ローカルと同じモジュールを使える

使えません。Page実行環境のブラウザはPage実行環境のセッションによる利用者向けのモードであり、対応モジュールが異なります。キーを追加すると秘密情報を露出するだけで、サポート対象外の実行形態を正当化しません。必要な処理はPage実行環境のサーバーへ置くか、対応モジュールへ設計を合わせてください。

### `createClient()`が自動的に全モードを切り替える

実行モードは、コードの配置、`baseUrl`、キーの有無と保管場所で決まります。Page実行環境の判定は、ブラウザの`window.JapanAI.getContext`を使う機能で行われます。

### サーバー処理のキーを画面初期化用の設定に入れてよい

入れてはいけません。サーバー処理で使うキーもProject APIキーです。ブラウザへ届くレスポンス、HTML、JavaScript、ソースマップ、ログは秘密情報の保管場所ではありません。

## うまくいかない場合

| 状況 | 確認・対応 |
|---|---|
| ローカルで認証エラーになる | 対象Project、キーの有効期限・失効、必要なスコープ、実値を渡しているかを確認します。マスク表示を使わず、[Project APIキーを管理する](../operations/manage-project-api-keys.md)に従って安全に発行・切替します。 |
| PageのブラウザでSDKが期待どおり動かない | StudioでホストされたPage内で実行されているか、`baseUrl: ""`で初期化しているか、ブラウザ用コードにキーやサーバー専用設定を含めていないかを確認します。 |
| `members`または`workflows`をブラウザから使いたい | 技術的には呼び出せますが、現時点ではベータのサポート範囲外です。キーをブラウザへ渡すのではなく、Page実行環境のサーバーで処理できるかを検討します。 |
| `platform`をローカルで使おうとしている | `platform`はPage実行環境のブラウザ専用です。`isAvailable()`の結果を確認し、Page実行環境の外では代替の画面処理を実装します。 |
| サーバー処理でキーが見つからない | キーは自動では注入されません。ルートの`.env`で環境変数を定義しているか、読み取っている変数名が一致しているか、実行場所がサーバー処理かを確認します。値を仮のキーで画面側へ埋め込まないでください。 |

問い合わせには、対象Project、実行モード、SDKのバージョン、実行場所、発生日時、エラー全文（キー・トークンを除く）、再現手順を記録します。Project APIキー、Cookie、Secret値、認証ヘッダーを添付してはいけません。

## チェックリスト

- [ ] 実行場所をローカル、Page実行環境のブラウザ、Page実行環境のサーバーのいずれかに区分した
- [ ] モードに対応するモジュールだけを使用している
- [ ] ローカルとサーバー処理のキーをサーバー側の安全な設定に限定した
- [ ] Page実行環境のブラウザを`createClient({ baseUrl: "" })`で初期化し、キーを読んでいない
- [ ] ブラウザバンドル、公開用環境変数、ログ、ソース管理にキーを含めていない
- [ ] APIキーの発行・スコープ・失効を[Project APIキーを管理する](../operations/manage-project-api-keys.md)で確認した

## 次に読む記事

- [開発環境・バージョン・互換性](dev-environment-and-versions.md)
- [Hosted Pageの認証・API経路](../page-runtime/hosted-page-auth-and-api-routes.md)
- [Pageのサーバー処理・環境変数・Secret参照](page-server-env-and-secrets.md)
