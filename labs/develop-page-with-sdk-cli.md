# SDK／CLIでPageを開発する

この実習では、架空のお知らせを保存したObjectを、Pageで一覧表示します。ブラウザ側のSDKはPageと同じオリジンの経路を使い、Project APIキーをJavaScriptへ入れません。成果物をCLIで新規Pageとして登録し、アクセス設定、表示、整理まで確認します。

## 関連記事

- [開発環境・バージョン・互換性](../development/dev-environment-and-versions.md)
- [Objectの構造を設計する](../build/design-object-structure.md)
- [SDK Objects](../development/sdk-objects.md)
- [Hosted PageのObjects API](../page-runtime/hosted-page-objects-api.md)
- [Pageをローカル確認してデプロイする](../development/local-preview-and-deploy-page.md)
- [CLIの設定・プロファイル](../cli/cli-config-and-profiles.md)
- [CLI Pages](../cli/cli-pages.md)

## 始める前に

- PageとObjectを作成・閲覧できる検証用Projectを用意します。CLI用には、そのProjectで必要最小限の権限を持つProject APIキーを安全に管理します。発行・失効は[Project APIキーを管理する](../operations/manage-project-api-keys.md)を参照してください。
- Node.js 22以上と、公開済みのSDK／CLI **0.11.0**を使います。0.11.0でもこの実習で使う`createClient({ baseUrl: "" })`、`objects.define()`、`list()`の使い方は同じです。導入・版固定は[開発環境・バージョン・互換性](../development/dev-environment-and-versions.md)を確認してください。
- 空の作業ディレクトリを使います。既存Pageの成果物ディレクトリを流用しないでください。特に`.jai-studio/page.json`があると、CLIは既存Pageの更新先として扱う場合があります。
- キーをHTML、JavaScript、公開用環境変数、ログ、リポジトリへ入れません。ブラウザ側のPageはCLIプロファイルを読みません。

## この実習で使うサンプル

Objectの表示名は`お知らせ（SDK実習用）`、Page名は`お知らせ一覧（SDK実習用）`です。`title`、`category`、`published_on`を作り、架空の2件を登録します。ObjectのIDは実習用の値と照合し、必要な担当者だけで扱います。

| title（テキスト） | category（テキスト） | published_on（日付） |
|---|---|---|
| 研修のお知らせ | 研修 | 2026-11-09 |
| 設備点検のお知らせ | 総務 | 2026-11-10 |

## Step 1. Objectと開発環境を準備する

**目標：** 表示対象と、版を固定した開発環境を用意します。

1. 検証用ProjectでObjectと3フィールドを作り、上表の2件を登録します。
2. Node.jsの版を確認し、SDKとCLIを0.11.0に固定して導入します。

   ```bash
   node --version
   npm install @japan-ai-inc/studio-sdk@0.11.0
   npm install --global @japan-ai-inc/studio-sdk@0.11.0
   jai-studio --version
   ```

3. CLIの対話入力で、検証用Projectのプロファイルを作成します。キーをコマンド引数へ書きません。

   ```bash
   jai-studio config set lab-page
   jai-studio config show lab-page
   ```

4. `config show`のマスクされた値を再利用せず、対象の接続先とプロファイル名だけを確認します。

Objectの作成は[Objectの構造を設計する](../build/design-object-structure.md)、SDK／CLIの導入は[開発環境・バージョン・互換性](../development/dev-environment-and-versions.md)、プロファイルは[CLIの設定・プロファイル](../cli/cli-config-and-profiles.md)を参照してください。**確認ポイント：** Node.jsが22以上で、SDKと`jai-studio`の両方が0.11.0です。

## Step 2. ブラウザ用の一覧コードを作る

**目標：** Project APIキーを使わず、Pageの相対経路でObjectを読みます。

1. 使用中のビルド手順で、一覧を表示するHTMLとブラウザ用エントリーファイルを用意し、実習用ObjectのIDを設定します。
2. ブラウザ側では、次の形でSDKを初期化し、一覧を表示します。

   ```js
   import { createClient } from "@japan-ai-inc/studio-sdk";
   const studio = createClient({ baseUrl: "" });
   const notices = studio.objects.define("実習用ObjectのID");

   async function load() {
     const result = await notices.list({ limit: 20 });
     // result.dataのtitle、category、published_onを画面へ表示する
   }
   ```

3. 取得失敗時は、Object ID、Objectの閲覧権限、Pageのアクセス設定を確認する案内だけを表示します。APIキー、Secret、認証ヘッダーを追加しません。

SDKのObject操作は[SDK Objects](../development/sdk-objects.md)、PRSブラウザでの初期化と権限は[Hosted PageのObjects API](../page-runtime/hosted-page-objects-api.md)を参照してください。**確認ポイント：** `baseUrl`は空文字で、ブラウザコードにAPIキーがありません。

## Step 3. 成果物を作り、ローカルで構造を確認する

**目標：** 配布するHTML、JavaScript、必要な静的ファイルだけを`dist`に出力します。

1. 使用中のビルド設定で、HTMLとバンドル済みJavaScriptを`dist`へ出力します。
2. ローカルWebサーバーで、見出し、静的ファイル、相対パスを確認します。
3. `dist`に`node_modules`、`.env`、キー、Secret、ソースマップに含めるべきでない値がないことを確認します。
4. ローカルの画面はPRSセッションを持たないため、Object取得の成否をここだけで判定しません。

ローカル確認と成果物の構成は[Pageをローカル確認してデプロイする](../development/local-preview-and-deploy-page.md)、配布時の扱いは[Pageの仕様・制限](../build/page-specs-and-limits.md)を参照してください。**確認ポイント：** `dist`には配布する成果物だけがあり、秘密値は含まれません。

## Step 4. CLIで新規Pageとして登録する

**目標：** `dist`を新しいPageとして登録し、結果を安全に記録します。

1. 対象のプロファイル、成果物ディレクトリ、既存の`.jai-studio/page.json`の有無を確認します。
2. 次を実行します。

   ```bash
   jai-studio --profile lab-page pages deploy ./dist \
     --name "お知らせ一覧（SDK実習用）" --json
   ```

3. 成功結果の`pageId`、`studioUrl`、`runtimeUrl`、`version`を、必要な担当者だけが見られる記録先に保存します。
4. `runtimeUrl`がない場合は、`studioUrl`から対象Pageを確認します。同じ`dist`を次回使う前に、メタデータが更新先を示すことを理解します。

デプロイ引数、初回登録と更新の判定、結果の読み方は[CLI Pages](../cli/cli-pages.md)を参照してください。**確認ポイント：** CLIの成功だけでObjectを読めたとは判断せず、新規Pageの対象と結果のURLを確認します。

## Step 5. アクセス設定と表示を確認する

**目標：** PageへのアクセスとObjectの読取りを、サインイン済みの利用者として確認します。

1. Pageカードから**「アクセス設定」**を開き、**「アクセスモード」**を**「サインイン必須」**にして保存します。
2. 想定する利用者の入口でPageを開き、2件のレコードと表示件数を確認します。
3. 表示されない場合は、Object ID、Objectの閲覧権限、Pageのアクセス設定、利用者を順に確認します。キーをブラウザへ追加して回避しません。
4. 見出しを変更して再度成果物を作る場合は、更新先を確認してからCLIでデプロイし、表示とバージョンを照合します。

アクセス設定は[画面からPageを登録・公開する](../build/register-and-publish-pages.md)、更新確認は[Pageのビルド・起動・更新](../development/page-build-start-update.md)を参照してください。**確認ポイント：** Pageを開けることと、Objectを読めることは別です。PageとObjectの双方で必要な条件を確認します。

## よくある誤解

- CLIプロファイルはCLI用です。PRSブラウザのSDKは`baseUrl: ""`を使い、APIキーを設定しません。
- CLIの成功とObject表示の成功は別です。Pageのアクセス条件とObjectの閲覧権限を確認します。
- 0.11.0へ上げても、この実習の初期化・Object定義・一覧取得は同じ形です。SDKとCLIの版は固定します。0.11.0ではAgent定義、Objectアクセスルール、予定実行タスクのCLIコマンドもありますが、このPage実習では実行しません。

## うまくいかない場合

| 状況 | 最初に確認すること |
|---|---|
| `jai-studio`が見つからない・版が違う | Node.js、PATH、SDK／CLIの版を[開発環境・バージョン・互換性](../development/dev-environment-and-versions.md)で確認します。 |
| CLIの認証で失敗する | プロファイル、接続先、キーの期限・権限を[CLIの設定・プロファイル](../cli/cli-config-and-profiles.md)と[Project APIキーを管理する](../operations/manage-project-api-keys.md)で確認します。 |
| 初回のつもりで既存Pageを更新しそう | `--page-id`と`dist/.jai-studio/page.json`を確認し、推測でデプロイしません。[CLI Pages](../cli/cli-pages.md)を参照します。 |
| Pageでレコードを読めない | Object ID、Objectの閲覧権限、Pageのアクセス設定を[Hosted PageのObjects API](../page-runtime/hosted-page-objects-api.md)で確認します。 |
| Pageが表示できない | 成果物のHTML・JavaScript・相対パスを確認し、[Pageのビルド・起動・表示に失敗する](../troubleshooting/page-build-start-display-failures.md)を参照します。 |

## 後片付け

1. 実習用Pageの共有範囲を確認し、不要ならPageの削除または非公開を、対象・影響を確認してから行います。
2. CLIプロファイルを削除してもProject APIキーは失効しません。利用中の処理がないことを確認したうえで、キーの失効・削除は[Project APIキーを管理する](../operations/manage-project-api-keys.md)に従います。
3. PageのURL、成果物、Object、レコードを残す必要があるか確認します。他の利用者・実習が参照していなければ整理します。
4. この実習ではSecret、外部接続、トリガー、スケジュールを作成しません。途中で追加した場合は、先に停止・失効・非公開を確認します。

## 次に読む記事

- [CLI Pages](../cli/cli-pages.md)
- [SDK Objects](../development/sdk-objects.md)
- [Hosted PageのObjects API](../page-runtime/hosted-page-objects-api.md)
- [Pageのビルド・起動・更新](../development/page-build-start-update.md)
- [Project APIキーを管理する](../operations/manage-project-api-keys.md)
