# Web UI・AI支援・SDK・CLI・APIを選ぶ

Studioの開発経路は、目的と実行場所で選びます。画面で設定を確認するならWeb UI、Pageのたたき台や修正案にはAI支援、TypeScriptならStudio SDK、端末やCIで反復する操作は`jai-studio` CLI、別言語・HTTPクライアントから直接呼ぶなら公開REST APIが候補です。

同じProjectでも、経路ごとに操作、認証、確認方法は異なります。Project APIキーは安全なサーバー側で管理し、ブラウザの配布物へ含めません。

## 関連記事

- [AIにPageの作成・修正を依頼する](../build/ask-ai-to-build-pages.md)
- [Project APIキーを管理する](../operations/manage-project-api-keys.md)
- [実行モード・認証・対応範囲](execution-modes-and-auth.md)
- [CLIの設定・プロファイル](../cli/cli-config-and-profiles.md)

## この記事の範囲

この記事は開発者が目的に合う入口を選ぶ比較記事です。REST API全仕様、SDK全メソッド、CLIの導入・プロファイル設定は扱いません。実行前に対象組織・Project・権限を確認してください。

SDKは`@japan-ai-inc/studio-sdk`で提供されます。公開版は`0.11.0`です。Node.js向けSDKとCLIにはNode.js 22以降が必要です。`0.x`系は完全な版を固定し、更新前に検証します。

## まず選ぶ経路

| 経路 | 向く目的 | 主にできること | 認証・前提 |
|---|---|---|---|
| Web UI（Studio画面） | 初回設定、目視確認、少数の変更 | 「エージェント」「ワークフロー」「オブジェクト」「ストレージ」「データセット」「ページ」などの作成・設定・確認 | Studioへサインインし、対象Projectの閲覧・操作権限があること |
| AI支援 | Pageの新規作成、既存Pageの差分修正、要件の整理 | 目的・データ・変更範囲・確認条件を伝え、Pageの作成または修正を依頼する | 構築用AIを利用でき、Pageを作成・更新できること |
| Studio SDK | TypeScriptで独自の開発処理を実装する | Agent、Object、Storage、Workflow、Projectメンバーの操作を型付きで実装する | ローカル開発ではProject APIキー。PRSブラウザではセッションを使用し、キーを置かない |
| `jai-studio` CLI | 端末操作、スクリプト、CIでの反復作業 | Pageの配置、Workflow管理、参照・管理操作をコマンドで実行する | 通常のProject操作はCLIプロファイルまたは`JAI_STUDIO_API_KEY`のProject APIキー。スキル導入だけはキー不要 |
| 公開REST API | TypeScript以外、既存のHTTP連携、SDK未採用の実装 | HTTPでAgent、Object、Storage、Workflow、Pageなどの公開APIを直接利用する | 経路ごとの認証・スコープ・リクエスト形式を確認する。キーは安全なサーバー側に保管する |

画面の項目や操作可否は、組織の利用条件、Projectの状態、権限で変わります。項目がないだけで、機能や権限不足とは断定しないでください。

## 1. Web UIを選ぶ場面

Studio画面は、対象Projectと現在の設定を確認して変更したいときの基本の入口です。少数の「オブジェクト」や「ワークフロー」を試作する、既存の「ページ」を開く、APIキーを発行・失効させる操作に向きます。

保存・公開・実行・外部送信は影響が異なります。対象と影響範囲を確認し、許可されたデータで代表結果を確認してください。

## 2. AI支援を選ぶ場面

目的、利用者、データ、許可する操作、変更しない範囲を会話で整理するならAI支援を選びます。画面構成のたたき台や、差分を絞った修正に向きます。

AIへ依頼した後も、対象Pageを「プレビュー」で開き、表示、入力、保存・送信を確認します。AIの完了報告、作成依頼の承認、Pageの登録・公開は別の確認です。依頼方法と確認手順は[AIにPageの作成・修正を依頼する](../build/ask-ai-to-build-pages.md)を参照してください。

> AIへの指示はアクセス権や外部連携に対する技術的な制御ではありません。個人情報、パスワード、APIキー、トークンを会話へ入力せず、登録・更新・送信を許可する範囲を明示してください。

## 3. SDKを選ぶ場面

型チェックと再利用可能な処理を重視してStudioリソースをコードから扱うならStudio SDKを選びます。`createClient()`から、`agents`、`datasets`、`members`、`objects`、`scheduledTasks`、`storage`、`workflows`を利用できます。`datasets`は読取り専用です。`scheduledTasks`は開発者管理のProject APIキーでProject固有のWorkflowを予定実行するときに使います。SDK 0.11.0では、対応するPRS実行環境で、Pageからの`agents.getTurnCredits()`などの利用量確認、候補比較、データセット読取りを行えます。インストール済みAppのPageでは、`platform.openAppSettings()`で設定画面を開けます。

```ts
import { createClient } from "@japan-ai-inc/studio-sdk";

const studio = createClient({
  baseUrl: "https://api.japan-ai.co.jp",
  apiKey: process.env.JAI_STUDIO_API_KEY,
});

const contacts = studio.objects.define<{ name: string }>("contacts");
const result = await contacts.list({ limit: 20 });
```

ローカル開発や管理する自動化では、開発者管理のProject APIキーを使います。PRS上のStudio Pageブラウザコードは`createClient({ baseUrl: "" })`を使い、PRSセッションで認証されます。ブラウザ用JavaScript、公開環境変数、ログ、ソース管理にキーを置いてはいけません。

SDKの直接開発はローカル開発と管理された自動化が主な対象です。任意の外部本番ホスティング・連携をサポート範囲と判断せず、実行モードは[実行モード・認証・対応範囲](execution-modes-and-auth.md)で確認します。

## 4. CLIを選ぶ場面

`jai-studio` CLIは、端末やCIで同じ手順を繰り返す場合に選びます。公開パッケージをグローバルに導入します。

```bash
npm install --global @japan-ai-inc/studio-sdk
jai-studio config set my-project
jai-studio pages deploy ./dist --name "営業ダッシュボード" --json
```

CLIには、`config`、`agents`、`members`、`objects`、`storage`、`workflows`、`scheduled-tasks`、`pages deploy`、`skill install`があります。`scheduled-tasks`はProject固有のWorkflowを予定実行として管理します。`objects`では定義・フィールド・レコードを、`workflows`では作成、下書き保存、公開、実行、状態確認を扱います。`pages deploy`はZIPまたはディレクトリを新規Pageまたは既存Pageの版として配置します。

APIキーをコマンド引数へ渡すと、シェル履歴やプロセス一覧に残る可能性があります。対話入力、`--key-stdin`、または`JAI_STUDIO_API_KEY`を使います。CLIプロファイルはCLI専用で、SDKの`createClient()`は読みません。設定は[CLIの設定・プロファイル](../cli/cli-config-and-profiles.md)を参照してください。

> `delete`、Workflowの`publish`、Pageの既存IDを指定する配置は、対象に変更を加えます。CIへ組み込む前に、対象Project、ID、入力ファイル、キーのスコープ、失敗時の扱いを検証してください。

## 5. 公開REST APIを選ぶ場面

既存システムがTypeScriptでない、依存パッケージを追加できない、HTTPで連携を統一したい場合は公開REST APIを選びます。Agent、Object、Storage、Workflow、Page、Project Membersなどの`/api/v1/`経路があります。Object APIは定義・フィールド・レコードを、Page APIはアップロードURLの取得と配置を扱います。

REST APIでは、HTTPメソッド、URL、本文、ページング、エラー、認証ヘッダー、必要なスコープを呼出側が管理します。Project APIキーを使う公開APIには、`x-api-key`または`Authorization: Bearer`を受け取る実装があります。各APIの認証・提供条件は同じとは限りません。SDK対応操作では、SDKまたはCLIをまず検討します。

未確認のREST API経路・パラメータ・レスポンス形式を、ルート名だけから推測して実装しないでください。対象APIの公開仕様、実行モード、キーのスコープを確認してから接続します。

## 認証とキーの確認

Web UIとAI支援では、通常はサインインした利用者のProject権限が前提です。SDK、CLI、REST APIで直接開発する場合は、必要な対象と操作だけに絞ったProject APIキーを使います。キーの発行、スコープ、有効期限、失効は[Project APIキーを管理する](../operations/manage-project-api-keys.md)に従ってください。

- キーをブラウザ、Pageの配布ファイル、公開リポジトリ、チャット、ログへ保存しません。
- 最小スコープ・短い有効期限から開始し、必要な操作だけを検証します。
- 認証・権限エラーでは、対象Project、キーの有効期限・失効、スコープ、実行モード、設定先を順に確認します。

## 用途別の決定表

| やりたいこと | 第一候補 | 補助・代替 | 選ぶ理由と確認 |
|---|---|---|---|
| 既存設定を見ながら少数のリソースを作成・変更する | Web UI | SDK／CLI | 現在のProjectと設定、変更後の結果を確認します。 |
| 要件から業務画面のたたき台を作り、会話で修正する | AI支援 | Web UI | 目的と変更範囲を整理し、「プレビュー」と業務操作を確認します。 |
| Pageをビルド済みの成果物から繰り返し配置する | CLI | Web UI | `pages deploy`でZIPまたはディレクトリを配置し、対象Page IDと版を確認します。 |
| TypeScriptのサーバー処理からAgent、Object、Storage、Workflowを使う | SDK | REST API | 型付きメソッドを使い、キーはサーバー側で管理します。 |
| CIでWorkflowを取得・下書き保存・公開・実行確認する | CLI | SDK | コマンドと入力ファイルをスクリプト化し、副作用を確認します。 |
| TypeScript以外のバックエンドから公開APIを利用する | REST API | SDKを使える言語環境への変更 | 公開仕様、認証、スコープ、エラー処理を管理します。 |
| Studio PageのブラウザからStudioリソースを扱う | SDKのPRSブラウザモード | Web UIで設定を確認 | PRSセッションを使い、Project APIキーをブラウザに渡しません。 |

## よくある誤解・うまくいかない場合

Web UI、SDK、CLIは同じ認証設定を共有しません。経路を変えてキーや権限を回避せず、次を確認します。

- Web UIやAI支援で対象Project・リソース・操作が表示されない場合は、組織の利用条件、Project参加状況、権限を確認します。
- CLIでプロファイルを作ったのにSDKが認証できない場合は、SDKがCLIプロファイルを読まないことを確認し、安全な環境変数から実値を渡します。
- CLIまたはSDKで操作が不足する場合は、公開REST APIの対象仕様を確認します。内部実装の経路を外部連携の契約として利用しません。
- REST APIの認証・スコープ・形式が確定できない場合は、推測したHTTPリクエストを本番へ送らず、対象APIの仕様と[実行モード・認証・対応範囲](execution-modes-and-auth.md)を確認します。
- 削除、公開、更新、実行後に結果が不明な場合は、直ちに再実行せず、対象と結果を確認します。

## 次に読む記事

- [実行モード・認証・対応範囲](execution-modes-and-auth.md)
- [CLIの設定・プロファイル](../cli/cli-config-and-profiles.md)
- [AIにPageの作成・修正を依頼する](../build/ask-ai-to-build-pages.md)
- [Project APIキーを管理する](../operations/manage-project-api-keys.md)
