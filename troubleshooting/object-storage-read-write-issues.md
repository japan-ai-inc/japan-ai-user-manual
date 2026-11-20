# Object・Storageの読み書きができない

ObjectのレコードやStorageのファイルが見つからない、開けない、保存できない、またはPage・APIから操作すると失敗する場合は、権限だけを変更して解消しようとしないでください。対象の組織・Project・入口、データの状態、操作に合う経路を順に確かめます。この記事は利用者・管理者向けの切り分けです。Objectの設計変更、Storageのフォルダ設計、API実装の詳細な手順は扱いません。

## 関連記事

- [Objectのデータを閲覧・更新する](../use/view-and-update-object-records.md)
- [Storageのファイルを利用する](../use/use-storage-files.md)
- [組織・Project・Appの権限を理解する](../operations/understand-org-project-app-permissions.md)
- [Objectの仕様・制限](../build/object-specs-and-limits.md)
- [Storageの仕様・制限](../build/storage-specs-and-limits.md)
- [Hosted PageのObjects API](../page-runtime/hosted-page-objects-api.md)
- [Hosted PageのStorage API](../page-runtime/hosted-page-storage-api.md)

## 要点：誰が読み書きできるか

| 入口 | 決まり方 |
|---|---|
| 標準画面 | 操作した人のProjectロールとIAM。Projectのロールは「管理者」「編集者」「メンバー」で、閲覧者（読み取り専用）のロールはありません。読み取り専用にするにはカスタムロールが必要です。 |
| Agent | Studio ProjectのすべてのAgentに`custom_object_sql`が自動で付与され、外せません。SELECTだけでなくINSERT／UPDATE／DELETE／DDLも実行できるため、制御はObjectのIAM（ロール権限）で行います。 |
| Page（ブラウザ） | Pageセッションとアクセスモード。Project APIキーは使いません。 |
| 公開API・CLI | Project APIキーのスコープとキーの作成者。 |

## まず確認すること

- 正しい組織・Project・App・Pageを開いているか、対象のObject名、レコード、ファイル名またはパスを照合します。
- 操作が「閲覧」「作成」「更新」「削除」のどれかを分けます。閲覧できても書き込めるとは限りません。
- 標準画面、Page、Agent、SQL、外部APIのどの入口で失敗するかを記録します。同じデータでも入口により認証・経路が異なります。
- 直前の処理が完了したかを確認します。不明な更新・アップロードを直ちに繰り返さず、現在のデータとファイル一覧を再取得します。

> 共有リンク、Storageのパス、ファイルID、Object IDを知っていることは、アクセス権の付与ではありません。権限不足を回避するために共有範囲を広げたり、他人の認証情報やAPIキーを追加したりしないでください。

## 症状別の切り分け

### 標準画面でObject・Storageを操作する場合

| 症状・表示されるメッセージ | 主な原因 | 確認・対処 | 詳しい記事 |
|---|---|---|---|
| Object、レコード、またはStorageファイルが見つからない | 別Project・別Appを開いている、検索条件・現在のフォルダが違う、閲覧範囲外 | Projectと案内された入口を確認し、Objectは検索条件・ページ、Storageは現在のフォルダ・検索語を見直します。見えないからと同じレコードやファイルを新規作成しません。 | [Objectのデータを閲覧・更新する](../use/view-and-update-object-records.md)、[Storageのファイルを利用する](../use/use-storage-files.md) |
| Objectは見えるが編集・新規作成できない | 更新・作成権限、フィールドまたはレコードの条件、システム項目の制約 | 操作種別に必要な権限と画面の案内を確認します。必須、型、数値範囲、ユニーク制約など、対象フィールドの入力条件も確認します。 | [Objectの仕様・制限](../build/object-specs-and-limits.md)、[組織・Project・Appの権限を理解する](../operations/understand-org-project-app-permissions.md) |
| 保存エラー、または他のユーザー・処理による更新が通知される | 入力不正、重複、または同時更新 | メッセージの項目を直します。競合時は最新のレコードを読み直し、他者の変更と業務上正しい値を確認してから必要な箇所だけ更新します。完了不明の書込みを同じ条件で再送しません。 | [Objectのデータを閲覧・更新する](../use/view-and-update-object-records.md)、[Object SQLのDML](../object-sql/object-sql-dml.md) |
| 「ファイルアップロード」を選べない | 保存・更新権限がない、App管理ファイルである、利用条件が満たされない | 対象Projectと保存先、必要な新規保存・更新権限、画面の制限理由を確認します。「インストール済みアプリ」と表示される読み取り専用ファイルは通常の更新対象にしません。配布時のStorageシードは、同名の利用者ファイルを任意に上書きする仕組みと決めつけません。 | [Storageのファイルを利用する](../use/use-storage-files.md)、[組織・Project・Appの権限を理解する](../operations/understand-org-project-app-permissions.md) |
| ダイアログを閉じたがファイルがない／「アップロードに失敗しました」 | 転送・保存確定前、容量・形式・保存容量・権限の問題 | 「待機中」「アップロード中」「完了」「失敗」と一覧を確認します。既定の1ファイル上限は2GBです。保存容量はProject単位で判定され、既定は10,240MBです。 | [Storageの仕様・制限](../build/storage-specs-and-limits.md) |
| 特定の名前のファイル・フォルダーを作成・アップロードできない | 名前・パスの規則に合わない | フォルダー階層は10段まで、パスは1,000文字までです。拡張子のブロックリストは名前のドット区切りごとに判定されるため、`app.config.json`のように途中にブロック対象の語を含む名前も拒否されます。Windowsの予約名も使えません。名前を変えて保存します。 | [Storageの仕様・制限](../build/storage-specs-and-limits.md) |
| 「ファイルが既に存在します」 | 同じ保存先に同名ファイルがある | 既存ファイルの版、参照しているPage・Agent・Workflow、置換の影響を確認します。意図が不明なら「両方保持」や置換を選ばず、担当者へ確認します。 | [Storageのファイルを利用する](../use/use-storage-files.md) |
| レコードを削除できない | 他のレコードから参照されている | 参照しているレコードを確認し、参照を外すか参照元を先に処理してから削除します。 | [Objectの仕様・制限](../build/object-specs-and-limits.md) |
| CSVの取り込みで文字化けする、または失敗する | CSVテンプレートは日本語WindowsではShift-JISで出力されるが、取り込みはUTF-8のみ | CSVをUTF-8で保存し直してから取り込みます。 | [Objectのデータを閲覧・更新する](../use/view-and-update-object-records.md) |
| 「このファイル形式はプレビューに対応していません」 | プレビュー対象外、またはプレビューの上限超過 | これはアップロード不可を意味しません。取得権限があればダウンロードして確認します。通常画面のプレビュー上限は10MBです。 | [Storageの仕様・制限](../build/storage-specs-and-limits.md) |

### Page・APIから操作する場合

| 症状・表示されるメッセージ | 主な原因 | 確認・対処 | 詳しい記事 |
|---|---|---|---|
| 401、403、404 | Pageセッション、アクセスモード、対象Project・ID、Object／レコード／Storageの権限、または経路の不一致 | Pageを開き直し、利用者とアクセスモードを確認します。ブラウザのHosted Pageでは同一オリジンのPage相対経路とPageセッションを使い、Project APIキーや認証ヘッダーを追加しません。 | [Hosted Pageの認証・API経路](../page-runtime/hosted-page-auth-and-api-routes.md)、[Hosted PageのObjects API](../page-runtime/hosted-page-objects-api.md)、[Hosted PageのStorage API](../page-runtime/hosted-page-storage-api.md) |
| Object更新が409 | `expectedRevision`と最新のレコードの不一致 | 最新レコードを取得し、変更内容を見直してから改めて更新します。同じrevisionで再送しません。`expectedRevision`を省略した更新は別の競合方針になるため、意図して選びます。 | [Hosted PageのObjects API](../page-runtime/hosted-page-objects-api.md) |
| Object APIが400、または検索が失敗する | フィールドの入力条件、ID・検索条件の誤り、ブラウザ検索クエリの過長 | Object・フィールドの型・必須・制約を確認します。公開APIの`:objectId`にはObject名ではなくUUIDを渡します（名前を渡すと400）。IDは`objects.list()`などの一覧で取得します。Hosted PageのSDK検索はクエリ文字列が8,000文字以下か確認し、条件を減らすか検索を分けます。 | [Objectの仕様・制限](../build/object-specs-and-limits.md)、[Hosted PageのObjects API](../page-runtime/hosted-page-objects-api.md) |
| PageからStorageアップロードが失敗する | 件数・サイズ・競合指定、保存容量、準備・送信・確定の未完了 | SDKのアップロードは1回20件までです。既定の1ファイル2GB、通常プロキシ経路の本文32MiBを区別します。署名付きURLを使う準備・送信・確定フローでは、確定前を完了と扱いません。 | [Hosted PageのStorage API](../page-runtime/hosted-page-storage-api.md) |
| Object SQLのUPDATE／DELETEで一部の行しか変わらない | LIMITのないUPDATE／DELETEは、条件に合う行のうち任意の1,000行だけを処理し、エラーも出さない | 影響行数が0になるまで同じ文を繰り返します。UPDATE／DELETEのORは同じフィールド内だけで使えます。JSONと配列のフィールドにはSQLのリテラル表記がないため、構造化パラメーターで渡します。WorkflowノードではWorkflow変数（`{{#node.var#}}`）を使い、`$1`を直接書きません。 | [Object SQLのDML](../object-sql/object-sql-dml.md) |
| CLIの`--filter`で数値・真偽値の比較や`in`／`notin`／`between`／`jsoncontains`が失敗する | CLIは値をすべて文字列として送り、型変換しない。値を省略した`exists`も拒否される | 文字列の比較だけをCLIで行い、型つきの絞り込みはSDKを使います。 | [CLI Objects](../cli/cli-objects.md) |
| Pageでは読めるのにAPIでは読めない、またはその逆 | 標準画面・Page・Public APIの認証方式と経路を混同 | ブラウザのPage相対APIと、サーバー・外部連携向けPublic APIを分けます。Page内で任意のPublic API経路を呼ぶ前提にせず、[Objects API](../api/objects-api.md)、[Storage API](../api/storage-api.md)を確認します。 | [Hosted Pageの認証・API経路](../page-runtime/hosted-page-auth-and-api-routes.md)、[Hosted PageのObjects API](../page-runtime/hosted-page-objects-api.md)、[Hosted PageのStorage API](../page-runtime/hosted-page-storage-api.md) |

## 管理者が確認する順序

1. 利用者が対象Projectの有効なメンバーか、Object・Storageを含むリソースの共有範囲に合うかを確認します。組織所属、Project参加、App利用条件は別です。
2. 利用者に必要なのが閲覧だけか、作成・更新・削除かを確認し、最小限のロール・IAM・リソース条件を見直します。読み取り専用にするにはカスタムロールが必要です。
3. Objectではフィールドとレコードのアクセス条件、リレーション先の権限を別々に確認します。リレーションを設定しても参照先の閲覧・更新権限は自動付与されません。Agentによる予期しない変更を防ぐ場合も、ObjectのIAMで制御します。
4. Storageでは保存先、Projectの残り容量（既定10,240MB）、名前・パスの規則と、ファイルがApp管理の読み取り専用ではないことを確認します。
5. Pageの場合はアクセス設定、想定利用者のPageセッション、Page・Project・対象データの組合せを試験します。公開URLを共有してもデータ操作権限は増えません。

## 管理者・サポートに相談する前に

[問い合わせに必要な情報を集める](collect-info-for-support.md)の観点で、対象の組織・Project・App／Page、Object名またはファイル名・パス、操作日時、利用者の役割、実行した入口、HTTP状態または表示メッセージ、API／SDKの応答本文にある`requestId`、再現手順、確認済み事項をまとめます。問い合わせにAPIキー、Cookie、認証ヘッダー、署名付きURL、Secret値、ファイル内容を含めません。

## 次に読む記事

- [Objectのデータを閲覧・更新する](../use/view-and-update-object-records.md)
- [Storageのファイルを利用する](../use/use-storage-files.md)
- [組織・Project・Appの権限を理解する](../operations/understand-org-project-app-permissions.md)
- [Hosted PageのObjects API](../page-runtime/hosted-page-objects-api.md)
- [Hosted PageのStorage API](../page-runtime/hosted-page-storage-api.md)
