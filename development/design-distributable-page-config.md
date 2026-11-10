# 配布可能なPageの設定設計

Appの一部としてPageを配布すると、配布元Projectで有効だったPage ID、Workflow ID、Agent ID、Object ID、Storageの配置、Project Secret、外部サービスへの接続を、導入先Projectでそのまま使えるとは限りません。配布物には**何を使うかを表す論理名**だけを残し、導入先で実体を解決・設定できるように設計します。

この記事では、開発者が配布可能なPageを作るときの、固定ID・Secret・接続・環境差分の分離方法を説明します。Appの公開・導入操作や担当分担は対象外です。Project間の移行は[Project間の移行を計画する](../operations/plan-project-migration.md)も参照してください。

## 関連記事

- [Project間の移行を計画する](../operations/plan-project-migration.md)
- [Pageのソース構成とランタイム](page-source-structure-and-runtime.md)
- [Pageをローカル確認してデプロイする](local-preview-and-deploy-page.md)
- [Pageの画面遷移・ディープリンク](page-navigation-and-deep-links.md)
- [Project Secretを登録・更新・削除する](../operations/manage-project-secrets.md)
- [配布対象・依存・認証設定の制約](../distribution/distribution-scope-dependencies-auth.md)
- [作成者が組織内self-installする](../distribution/self-install-in-organization.md)

## この記事の範囲

ここでいう設定は、Pageが参照するリソースの識別方法と、導入先ごとに埋める値の境界です。UIのラベル、導入可能なリソース種別、接続の共有範囲、利用可否は組織の設定・権限・Appの構成により変わります。

配布機構は、宣言したPageリソースを導入先の実体へ解決します。一方、任意の文字列中やブラウザ向け成果物に埋め込んだID、外部サービスの接続先・データまで移し替えるものではありません。導入先で作り直す設定を先に特定します。

## 始める前に

- Pageが参照するWorkflow、Agent、Object、Storage、Project Secret、外部サービス、APIキーを一覧にします。Pageの画面だけでなく、環境変数、初期データ、URL、ビルド時設定、サーバー側処理も対象です。
- 各値を、論理名、導入先で解決する識別子、導入先で設定するSecretまたは接続、外部サービス固有の固定値に分けます。
- 配布元・検証用・導入先のProjectを取り違えないよう、対象環境とテストデータを決めます。配布元の実データ、APIキー、トークンを成果物・ログ・問い合わせに含めません。

> Project ID、Page ID、組織ID、Page用ホストを画面のJavaScript、URL、`.env`、ビルド時定数に固定しないでください。Pageの起点URLも導入先で変わります。Page内の遷移は相対URLを基本にし、詳細は[Pageの画面遷移・ディープリンク](page-navigation-and-deep-links.md)を参照してください。

## 分離する設定

| 種類 | 配布物に残すもの | 導入先で行うこと | 避けること |
|---|---|---|---|
| Page自身 | 論理名、成果物、相対パス | 導入先の識別子とアクセス設定を確認する | 配布元のPage ID・URL・組織IDを固定する |
| Workflow・Agent・Object | 用途が分かる論理名 | 同じリリースで宣言し、導入先での解決を確認する | UUIDや表示名だけをソースへ埋め込む |
| Storage | ルートの論理名、アプリ内の相対パス | 必要なファイル・フォルダーと権限を用意する | 配布元固有のファイルIDや絶対URLに依存する |
| Project Secret | Secret名とサーバー側の環境変数名 | 導入先で値を作成または選択して紐づける | 値・APIキー・トークンをZIPや公開用環境変数へ入れる |
| 外部接続 | 用途、必要な権限、設定担当者 | 導入先のアカウントで認証し、共有・個人利用を確認する | 配布元の認証状態・Cookie・接続IDが移ると仮定する |

論理名は、導入先でも意味が変わらない名前にします。`daily_report`、`support_agent`、`customer`、`uploads`のように、用途・種別・所有境界が分かる名前を採用します。同じ論理名を別用途へ使い回さないでください。

## 1. リソースは論理名で宣言する

配布するPageでは、ルートの `app.refs.json` にWorkflow、Agent、Custom Object、Storageルートの論理名を列挙できます。配布処理は同じリリースで宣言した対象と照合し、導入時に導入先の実体へ解決します。UUID形式、空文字、重複、未定義の種別は受け付けられません。

```json
{
  "workflows": ["daily_report"],
  "agents": ["support_agent"],
  "custom_objects": ["customer"],
  "storage_prefixes": ["uploads"]
}
```

`app.refs.json` がないPageは参照を宣言しません。依存するリソースはリリース側でも同じ論理名で宣言し、公開前に未解決の参照がないことを確認してください。

> `app.refs.json` は任意の設定を記述するファイルではありません。対象はWorkflow、Agent、Custom Object、Storageルートであり、Dataset、外部サービス、任意のHTTP URL、接続そのものを移行するものではありません。

### 実行時にIDが必要な場合

導入先のIDをソースコードに書かず、Pageの実行コンテキストまたはサーバー側設定から解決された値を渡します。外部のサーバー処理でStudio SDKを使う場合も、導入先の一覧を取得して業務上の識別子を検証してから対象IDを使います。

```ts
// サーバー側だけで実行する例。apiKeyはブラウザへ渡しません。
const { data: objects } = await studio.objects.list();
const customer = objects.find(item => item.objectName === "customer");
if (!customer) throw new Error("導入先に customer Object がありません");

const handle = studio.objects.define(customer.objectId);
```

SDKの `objects.list()` と `objects.define()` は公開APIです。ただし、この例だけでObject名の一意性、一覧取得の権限、PageからSDKを直接呼べる実行環境までは保証しません。選定規則、権限、ページング、未検出時の表示はアプリごとに確認してください。Project APIキーはブラウザ、公開用環境変数、ソース管理、ログに置きません。

## 2. 環境変数は「導入先で解決する値」として扱う

成果物ルートの `.env` または `.env.<英数字・アンダースコア・ハイフンで構成する名前>` は、配布時にサーバー側の環境変数テンプレートとして読み取り、配信ファイルから除かれます。`.env.local` と `.env.*.local` はローカル開発用でテンプレートには含まれません。変数展開（例：`${HOST}/api`）は使わず、1変数ごとに具体的な値または参照を記述します。

```dotenv
# 配布物に値を書かず、導入先で選ばれるSecretを参照します。
CRM_TOKEN=${secret:crm_token}

# 外部サービスに共通の公開情報だけは、必要性を確認して具体値にします。
CRM_REGION=jp
```

`${secret:名前}` は、Project Secretの実値ではなく、導入先で満たす必要があるSecret名の宣言です。Secret名は英小文字で始め、英小文字・数字・アンダースコアを使います。公開用の環境変数名にはSecret参照、導入先のリソースID、Project APIキーを置けません。ブラウザに展開される可能性があるためです。

導入後は、Appの「アプリに必要なシークレット」で必要項目を確認し、対象Projectの「プロジェクトシークレット」を作成または選択して「シークレットを選択」から紐づけます。Secretの値は配布元からコピーしません。作成・更新・削除の運用は[Project Secretを登録・更新・削除する](../operations/manage-project-secrets.md)を参照してください。

## 3. 接続は導入先で再認証する

Appが共有の外部接続を必要とする場合、導入先では接続とSecretの未設定状態が別々に案内される場合があります。接続を必要とする機能と、Secretだけを必要とするPage機能を分けて台帳化してください。導入前に必要な認証プロバイダーが表示されても、認証情報やSecret値がマーケットプレイスの掲載情報に含まれるものではありません。

1. 外部サービスごとに、接続先、必要な操作、最小権限、認証担当者、共有か個人利用かを決めます。
2. 導入先Projectで、導入先のアカウントを使って接続またはSecretを設定します。配布元のアカウントでテストして完了と判断しません。
3. Appの「セットアップ」で未完了の接続・Secretを確認します。表示されない項目を推測して追加せず、管理者またはAppの案内を確認します。
4. 導入先の最小権限のテストアカウント・テストデータで、読み取り、更新、外部送信を分けて検証します。

接続ID、OAuthトークン、Cookie、パスワード、Project APIキーは、`app.refs.json`、`.env`、JavaScript、URL、ログへ入れません。接続の再認証後に有効化や再公開が必要か、実行中の処理へ即時反映されるかは構成によって異なり、この確認範囲では断定できません。

## 配布前チェックリスト

- [ ] 配布元のProject ID、Page ID、組織ID、URL、リソースUUIDを成果物へ固定していない
- [ ] Workflow、Agent、Custom Object、Storageの依存を論理名で整理し、必要なものを `app.refs.json` に宣言した
- [ ] 宣言した論理名が、同じリリースで提供する対象と一致している
- [ ] Secretは名前だけを宣言し、値・APIキー・トークンをZIP、公開用環境変数、ログへ含めていない
- [ ] 接続ごとに導入先の認証担当者、最小権限、共有範囲、代表操作を決めた
- [ ] 導入先で初期表示、リソース取得、更新、外部送信、権限不足時の動作を確認する計画がある

## 確認できない場合

次の状態では、IDを固定して回避したり、Secretをブラウザ側へ移したり、アクセス設定を緩めたりしないでください。

- 配布時に参照先が未解決、またはUUID・環境変数に関するエラーになる。
- 導入先で「アプリに必要なシークレット」または接続の設定状況を確認できない。
- Pageは開くが、データ取得・更新・外部送信だけが失敗する。
- 接続が共有か個人利用か、必要な外部サービス権限、再認証の責任者を確定できない。

対象のApp・Page・導入先Project、論理名、Secret名（値を除く）、接続先、発生日時、表示メッセージ、実施済みの代表操作を記録して、管理者または提供元へ確認します。Secret値、APIキー、トークン、Cookie、実データを問い合わせに添付しません。

## 次に読む記事

- [Project間の移行を計画する](../operations/plan-project-migration.md)
- [Project Secretを登録・更新・削除する](../operations/manage-project-secrets.md)
- [配布対象・依存・認証設定の制約](../distribution/distribution-scope-dependencies-auth.md)
- [作成者が組織内self-installする](../distribution/self-install-in-organization.md)
