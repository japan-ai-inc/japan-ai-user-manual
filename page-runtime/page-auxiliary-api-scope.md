# Page補助APIの対応範囲

Page実行環境には、Agent・Object・Storage以外にも、音声文字起こし、Tool、Ontology、Workflow、Projectメンバー、営業活動データ、状態通知に関係する経路があります。ただし、経路が実装されていることだけでは、Pageのブラウザコードから安定して利用できる公開機能とはいえません。開発者は、利用者が実際に呼び出す経路、Pageセッション、組織・Projectでの提供条件、SDKの対応範囲を分けて確認します。

認証とPage相対APIの共通条件は[Hosted Pageの認証・API経路](hosted-page-auth-and-api-routes.md)、通信・提供条件の確認先は[制限・提供条件の索引](../operations/limits-and-availability-index.md)を参照してください。Toolの仕様は[Tool Proxy API](../api/tool-proxy-api.md)を正本とし、本記事では繰り返しません。

## 関連記事

- [Hosted Pageの認証・API経路](hosted-page-auth-and-api-routes.md)
- [Tool Proxy API](../api/tool-proxy-api.md)
- [Workflows API](../api/workflows-api.md)
- [Project Members API](../api/project-members-api.md)
- [実行モード・認証・対応範囲](../development/execution-modes-and-auth.md)
- [制限・提供条件の索引](../operations/limits-and-availability-index.md)

## この記事の範囲

対象は、Page実行環境でPage相対パスとして処理される補助経路です。Page実行環境の運用・管理用の経路（ビルド、再起動、管理者向け操作など）は、利用者のPage機能として扱わず、本記事の対象外です。

「利用可」は、Pageのブラウザコードから利用者向けに呼べることを示します。「条件付き」は、Pageセッションに加えて設定・組織提供・認可などを確認できたものです。「確認できない」は、経路の中継実装だけでは、ブラウザ向けの公開契約、リクエスト形式、または提供条件を確定できないものです。Pageの公開範囲を広げたり、APIキーをブラウザへ追加したりして回避してはいけません。

> Pageのブラウザは、Project APIキーを使いません。Studio SDKの現行公開版0.11.0では`createClient({ baseUrl: "" })`により、Pageセッションで相対パスを使います。SDKがPage実行環境のブラウザ向けとして案内するのは`agents`、`datasets`、`objects`、`storage`、`platform`です。下表の補助経路を使う場合も、キー、Cookie、認証ヘッダー、Secret値をJavaScript・HTML・ログへ含めません。

## 対応範囲

| 分類・Page相対パス | 方法 | Pageブラウザから | セッション・アクセスモード条件 | 確認できた制限・提供条件 |
|---|---|---|---|---|
| Speech `/api/speech/v1/transcription`、`/api/speech/transcribe` | HTTPメソッドは中継されます。操作ごとの対応メソッド・要求形式・応答形式は確認できない | 条件付き | 有効なPageセッションが必要です。「サインイン必須」「サインイン任意」「匿名アクセス」「外部顧客認証」の各モードで、対象Pageを開けることに加え、音声機能用の設定が必要です。モード別に文字起こし可否が同じかは確認できない | リクエスト本文は5 MiB以下、経路全体で毎秒25件・バースト200です。未構成時は503、必要な音声用設定がない場合は400です。 |
| Tool `/api/tools/_catalog`、`/api/tools/{toolName}` | `GET`で一覧、`POST`で実行。詳細は[Tool Proxy API](../api/tool-proxy-api.md) | 条件付き | 有効なPageセッション、ブラウザからのAPI利用を許可するPage設定、利用者のアクセス条件、対象Projectで許可された副作用なしのToolが必要です。4つのアクセスモードごとに、実利用者で許可範囲を検証します | Tool実行は毎秒10件・バースト50、本文は1 MiB以下です。一覧取得の制限・キャッシュを含む詳細は[Tool Proxy API](../api/tool-proxy-api.md)を参照してください。 |
| Ontology `/api/ontology...` | 経路接頭辞配下を中継。`GET`、`POST`、`PUT`、`PATCH`などを経路だけで一律に許可するものではありません | 条件付き | 有効なPageセッションと、対象組織・ProjectでOntology連携が構成されていることが必要です。アクセスモードごとの公開可否、利用者向けのAPI契約、必要な認可は確認できない | 本文は32 MiB以下、経路全体で毎秒25件・バースト200です。連携が未構成の場合は、利用不可として扱われます。 |
| Dataset読取り `/api/v1/datasets...` | SDKの`datasets.list()`、`datasets.search()`を使います。作成・更新・削除、文書一覧・編集はPageブラウザの対象外です | 条件付き | 有効なPageセッションと`dataset:read`が必要です。`scope: "project"`はProject連携済みDataset、`scope: "organization"`は署名済み利用者が読める組織Datasetだけを対象にします | 対応するPage実行環境がなければ404です。Project APIキーや匿名利用者で組織Datasetを読む代替にはしません。検索は生成回答ではなく近いチャンクを返します。 |
| Workflow `/api/v1/workflows...` | SDK互換の中継経路は登録されています。直接APIの操作仕様は[Workflows API](../api/workflows-api.md) | 確認できない | 有効なPageセッションは中継の前提です。ただしSDKはPageブラウザの利用可能モジュールに`workflows`を含めません。ブラウザからの利用可否、アクセスモード別の条件、必要な認可は確認できない | `/api/v1`全体の本文・レート上限は個別の運用設定に依存します。Workflow固有の入力・実行・待機時間の制限は[Workflows API](../api/workflows-api.md)を参照し、ブラウザ用の対応条件とは混同しません。 |
| Projectメンバー `/api/v1/project-members` | `GET`。SDKには`studio.members.list()`があります | 条件付き | 有効なPageセッションに加え、対象組織でMembers APIが利用可能であり、呼出主体のメンバー読取が許可される必要があります。SDK文書は、Pageブラウザ利用には許可リストへの登録が必要とします。アクセスモードごとの利用可否は確認できない | `/api/v1`全体の本文・レート上限は個別の運用設定に依存します。メンバー一覧固有の検索・ページング・個人情報の扱いは[Project Members API](../api/project-members-api.md)を参照してください。 |
| room状態 `/api/agent-chat/status/{roomId}` | `GET`のみ | 条件付き | 有効なPageセッションが必要です。`roomId`はUUID形式で、対象Projectの会話として利用できる必要があります。通常の会話継続はSDKの`sessionId`とAgent APIを優先し、状態取得だけを新規機能の基盤にしません | 本文は受け付けず、30秒の要求期限があります。この経路について、独自の経路側レート制限は確認できません。 |
| Page状態ストリーム `/api/status/stream` | `GET`のServer-Sent Events | 利用者業務APIとしては対象外 | Pageのビルド・起動状態の通知用です。Page識別情報または共有用のコードにより対象Pageを解決しますが、業務データ・任意処理・認可確認の代替ではありません | 接続は最大10分で切断されます。業務UIの恒久的なリアルタイム通信、再接続回数、アクセスモード別の利用可否は確認できない |

## 1. 先に利用可否を判定する

1. Pageを、想定する「アクセス設定」と利用者状態で開きます。
2. SDKでPageブラウザ向けに案内されたモジュールかを確認します。Datasetは読取り専用です。`workflows`と営業活動データは、経路だけを根拠に実装しません。
3. Speech、Ontology、Tool、Membersは、対象組織・Projectの設定と認可を確認します。未構成、403、503を別の認証情報で回避しません。
4. 条件付きの経路は、各アクセスモードで必要最小限の読取り・実行だけを実機確認します。匿名または外部顧客向けPageでは、個人情報・Project内部情報・副作用のある処理を前提にしません。

> `/api/v1/...`が存在しても、SDKがPageブラウザでサポートすること、組織で提供されること、利用者が許可されることは別です。特にWorkflowと営業活動データは、Pageのブラウザから使える公開機能として確認できません。

## よくある誤解

### 中継経路があれば、任意のHTTPメソッドを呼べる

いいえ。SpeechとOntologyは接頭辞配下を中継しますが、個別操作の契約は別です。room状態は`GET`のみです。Workflow、Members、営業活動データは`/api/v1`に接続されますが、利用可否は個別のSDK対応、認可、組織の提供条件に依存します。

### Pageを匿名公開すれば、補助APIも匿名で使える

いいえ。匿名アクセスはPageを開く入口の条件です。各経路ではPageセッション、対象Project、組織設定、利用者・匿名主体の認可が別に判定されます。匿名画面に内部データの読取りや更新を追加する前に、対象状態で拒否・成功を確認します。

### ビルド状態ストリームを業務イベント通知に流用できる

できません。この経路はPageのビルド・起動状態を通知するためのものです。会話の状態や業務データの購読、任意イベント配信の公開APIとしては確認できません。

## 確認できない場合

Speechの文字起こし操作ごとの本文・応答形式、Ontologyの公開操作一覧、営業活動データの利用者向けAPI契約、WorkflowのPageブラウザ利用、各経路のアクセスモード別の完全な許可表は確認できません。これらを推測して実装せず、対象の組織・Project・Page、アクセスモード、SDKバージョン、相対パス、HTTPメソッド、発生日時、HTTP状態、秘密情報を除いた応答を記録して管理担当者へ確認します。

APIキー、Cookie、認証ヘッダー、Secret、共有コード、音声データ、会話本文、メンバーの個人情報を問い合わせへ含めません。制限・提供条件の確認先は[制限・提供条件の索引](../operations/limits-and-availability-index.md)です。

## チェックリスト

- [ ] PageブラウザにProject APIキー、Cookie、認証ヘッダー、Secretを埋め込んでいない
- [ ] Speech、Tool、Ontology、Membersで、対象の組織・Projectの提供条件と認可を確認した
- [ ] Toolの詳細を[Tool Proxy API](../api/tool-proxy-api.md)に従って実装した
- [ ] Workflowと営業活動データを、経路があるだけでPageブラウザ対応と判断していない
- [ ] 想定するアクセスモード・利用者で必要最小限の操作を確認した
- [ ] room状態とPage状態ストリームを、業務用の任意API・イベント基盤として流用していない
- [ ] 429、400、403、503、通信失敗を区別し、状態変更の可能性がある要求を無条件に再送していない

## 次に読む記事

- [Hosted Pageの認証・API経路](hosted-page-auth-and-api-routes.md)
- [Tool Proxy API](../api/tool-proxy-api.md)
- [Workflows API](../api/workflows-api.md)
- [Project Members API](../api/project-members-api.md)
- [実行モード・認証・対応範囲](../development/execution-modes-and-auth.md)
- [制限・提供条件の索引](../operations/limits-and-availability-index.md)
