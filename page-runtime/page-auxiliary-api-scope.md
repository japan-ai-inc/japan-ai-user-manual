# Page補助APIの対応範囲

Page実行環境には、Agent・Object・Storage以外にも、音声文字起こし、Tool、Ontology、Workflow、Projectメンバー、状態通知に関係する経路があります。経路があることと、Pageのブラウザコードからサポート対象として使えることは別です。開発者は、下の表で経路ごとのサポート状況と条件を確認してから実装します。

認証とPage相対APIの共通条件は[Hosted Pageの認証・API経路](hosted-page-auth-and-api-routes.md)、通信・提供条件の確認先は[制限・提供条件の索引](../operations/limits-and-availability-index.md)を参照してください。Toolの仕様は[Tool Proxy API](../api/tool-proxy-api.md)を正本とし、本記事では繰り返しません。

## 要点

- Pageのブラウザは、Project APIキーを使わず、Pageセッションで相対パスを呼びます。Pageセッション経由の呼び出しでは、Project APIキーのスコープは確認されません。可否は、Pageセッション、組織・Projectの設定、利用者の権限で決まります。
- `/api/v1/*`の経路の上限は固定値です。リクエスト本文は**32MiB**まで、レートは**毎秒25件・バースト200**です。
- `members`（Projectメンバー）と`workflows`は、Page実行環境が中継するため技術的には呼び出せますが、現時点ではベータのサポート範囲外です（予告なく変更される可能性があります）。

## 関連記事

- [Hosted Pageの認証・API経路](hosted-page-auth-and-api-routes.md)
- [Tool Proxy API](../api/tool-proxy-api.md)
- [Workflows API](../api/workflows-api.md)
- [Project Members API](../api/project-members-api.md)
- [実行モード・認証・対応範囲](../development/execution-modes-and-auth.md)
- [制限・提供条件の索引](../operations/limits-and-availability-index.md)

## この記事の範囲

対象は、Page実行環境でPage相対パスとして処理される補助経路です。Page実行環境の運用・管理用の経路（ビルド、再起動、管理者向け操作など）は、利用者のPage機能として扱わず、本記事の対象外です。

表の「Pageブラウザから」の列は次の意味です。

- **利用可**：Pageのブラウザコードから利用者向けに呼べます。
- **条件付き**：Pageセッションに加えて、組織・Projectの設定や利用者の権限が必要です。
- **サポート範囲外**：技術的には呼び出せますが、現時点ではベータのサポート範囲外です（予告なく変更される可能性があります）。
- **対象外**：利用者の業務APIとしては使いません。

Pageの公開範囲を広げたり、APIキーをブラウザへ追加したりして回避してはいけません。

> Studio SDKの現行公開版0.11.0では`createClient({ baseUrl: "" })`により、Pageセッションで相対パスを使います。SDKがPage実行環境のブラウザ向けとして案内するのは`agents`、`datasets`、`objects`、`storage`、`platform`です。どの経路を使う場合も、キー、Cookie、認証ヘッダー、Secret値をJavaScript・HTML・ログへ含めません。

## 対応範囲

| 分類・Page相対パス | 方法 | Pageブラウザから | セッション・アクセスモード条件 | 制限・提供条件 |
|---|---|---|---|---|
| Speech `/api/speech/v1/transcription`、`/api/speech/transcribe` | HTTPメソッドは中継されます。操作ごとの要求・応答形式はこのマニュアルでは扱いません | 条件付き | 有効なPageセッションと、音声機能用の設定が必要です | リクエスト本文は5 MiB以下、経路全体で毎秒25件・バースト200です。未構成時は503、必要な音声用設定がない場合は400です。 |
| Tool `/api/tools/_catalog`、`/api/tools/{toolName}` | `GET`で一覧、`POST`で実行。詳細は[Tool Proxy API](../api/tool-proxy-api.md) | 条件付き | 有効なPageセッションが必要です。匿名のPageセッションでは401になります。ブラウザからのAPI利用を許可するPage設定、副作用なしのToolであることも必要です | Tool実行は毎秒10件・バースト50、本文は1 MiB以下です。レート制限はPageごとではなく、Page実行環境全体で共有されます。エラーは`{success,error}`の形式ではなくプレーンテキストで返ることがあります。詳細は[Tool Proxy API](../api/tool-proxy-api.md)を参照してください。 |
| Ontology `/api/ontology...` | 経路接頭辞配下を中継。`GET`、`POST`、`PUT`、`PATCH`などを経路だけで一律に許可するものではありません | 条件付き | 有効なPageセッションと、対象組織・ProjectでOntology連携が構成されていることが必要です。利用者向けのAPI契約はこのマニュアルでは扱いません | 本文は32 MiB以下、経路全体で毎秒25件・バースト200です。連携が未構成の場合は、利用不可として扱われます。 |
| Dataset読取り `/api/v1/datasets...` | SDKの`datasets.list()`、`datasets.search()`を使います。作成・更新・削除、文書一覧・編集はPageブラウザの対象外です | 条件付き | 有効なPageセッションが必要です。`scope: "project"`はProject連携済みDataset、`scope: "organization"`はサインイン済み利用者が読める組織Datasetだけを対象にします | `/api/v1`の固定上限（32MiB、毎秒25件・バースト200）が適用されます。対応するPage実行環境がなければ404です。匿名利用者で組織Datasetを読む代替にはしません。検索は生成回答ではなく近いチャンクを返します。 |
| Workflow `/api/v1/workflows...` | Page実行環境が中継します。直接APIの操作仕様は[Workflows API](../api/workflows-api.md) | サポート範囲外 | 技術的には有効なPageセッションで呼び出せますが、現時点ではベータのサポート範囲外です（予告なく変更される可能性があります）。SDKはPageブラウザの利用可能モジュールに`workflows`を含めていません | `/api/v1`の固定上限（32MiB、毎秒25件・バースト200）が適用されます。Workflow固有の制限は[Workflows API](../api/workflows-api.md)を参照してください。 |
| Projectメンバー `/api/v1/project-members` | `GET`。SDKには`studio.members.list()`があります | サポート範囲外 | 技術的には有効なPageセッションで呼び出せますが、現時点ではベータのサポート範囲外です（予告なく変更される可能性があります）。組織でMembers APIが利用可能で、呼出主体のメンバー読取が許可される必要があります | `/api/v1`の固定上限（32MiB、毎秒25件・バースト200）が適用されます。検索・ページング・個人情報の扱いは[Project Members API](../api/project-members-api.md)を参照してください。 |
| room状態 `/api/agent-chat/status/{roomId}` | `GET`のみ | 条件付き | 有効なPageセッションが必要です。`roomId`はUUID形式で、対象Projectの会話として利用できる必要があります。通常の会話継続はSDKの`sessionId`とAgent APIを優先し、状態取得だけを新規機能の基盤にしません | 本文は受け付けず、30秒の要求期限があります。 |
| Page状態ストリーム `/api/status/stream` | `GET`のServer-Sent Events | 対象外 | Pageのビルド・起動状態の通知用です。Page識別情報または共有用のコードにより対象Pageを解決しますが、業務データ・任意処理・認可確認の代替ではありません | 接続は最大10分で切断されます。業務UIのリアルタイム通信には使いません。 |

## 1. 先に利用可否を判定する

1. Pageを、想定する「アクセス設定」と利用者状態で開きます。
2. SDKでPageブラウザ向けに案内されたモジュールかを確認します。Datasetは読取り専用です。`workflows`と`members`はサポート範囲外のため、本番の機能をこれらに依存させません。
3. Speech、Ontology、Toolは、対象組織・Projectの設定と権限を確認します。未構成、401、403、503を別の認証情報で回避しません。
4. 条件付きの経路は、各アクセスモードで必要最小限の読取り・実行だけを実機確認します。匿名または外部顧客向けPageでは、個人情報・Project内部情報・副作用のある処理を前提にしません。

> `/api/v1/...`が中継されることと、SDKがPageブラウザでサポートすること、組織で提供されること、利用者が許可されることは別です。

## よくある誤解

### 中継経路があれば、任意のHTTPメソッドを呼べる

いいえ。SpeechとOntologyは接頭辞配下を中継しますが、個別操作の契約は別です。room状態は`GET`のみです。WorkflowとMembersは中継されますが、サポート範囲外です。

### Pageを匿名公開すれば、補助APIも匿名で使える

いいえ。匿名アクセスはPageを開く入口の条件です。たとえばToolは匿名のPageセッションでは401になります。各経路ではPageセッション、対象Project、組織設定、利用者・匿名主体の権限が別に判定されます。匿名画面に内部データの読取りや更新を追加する前に、対象状態で拒否・成功を確認します。

### Project APIキーのスコープで呼べる範囲を絞れる

いいえ。Pageセッション経由の呼び出しでは、キーのスコープは確認されません。ブラウザにキーを置かないことに加え、表示データと実行できる操作を、アクセス設定と利用者の権限で制御します。

### ビルド状態ストリームを業務イベント通知に流用できる

できません。この経路はPageのビルド・起動状態を通知するためのものです。会話の状態や業務データの購読、任意イベントの配信には使いません。

## うまくいかない場合

Speechの操作ごとの本文・応答形式、Ontologyの操作一覧は、このマニュアルでは扱いません。推測して実装せず、対象の組織・Project・Page、アクセスモード、SDKバージョン、相対パス、HTTPメソッド、発生日時、HTTP状態、秘密情報を除いた応答を記録して管理担当者へ確認します。

APIキー、Cookie、認証ヘッダー、Secret、共有コード、音声データ、会話本文、メンバーの個人情報を問い合わせへ含めません。制限・提供条件の確認先は[制限・提供条件の索引](../operations/limits-and-availability-index.md)です。

## チェックリスト

- [ ] PageブラウザにProject APIキー、Cookie、認証ヘッダー、Secretを埋め込んでいない
- [ ] Speech、Tool、Ontologyで、対象の組織・Projectの提供条件と権限を確認した
- [ ] Toolの詳細を[Tool Proxy API](../api/tool-proxy-api.md)に従って実装し、匿名セッションの401とプレーンテキストのエラーを処理した
- [ ] `workflows`と`members`をサポート範囲外と理解し、本番の機能を依存させていない
- [ ] 想定するアクセスモード・利用者で必要最小限の操作を確認した
- [ ] room状態とPage状態ストリームを、業務用の任意API・イベント基盤として流用していない
- [ ] 429、400、401、403、503、通信失敗を区別し、状態変更の可能性がある要求を無条件に再送していない

## 次に読む記事

- [Hosted Pageの認証・API経路](hosted-page-auth-and-api-routes.md)
- [Tool Proxy API](../api/tool-proxy-api.md)
- [Workflows API](../api/workflows-api.md)
- [Project Members API](../api/project-members-api.md)
- [実行モード・認証・対応範囲](../development/execution-modes-and-auth.md)
- [制限・提供条件の索引](../operations/limits-and-availability-index.md)
