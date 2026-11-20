# 制限・提供条件の索引

機能を使えない、数が増やせない、料金や消費の判断が必要なときに、**どの記事を正本として確認するか**を案内する索引です。個々の上限値、料金、API仕様、設定手順は、管理・運用で特に問い合わせの多いものを除いて複製しません。該当する正本と、実際の画面表示を確認してください。

## この記事の範囲

組織ごとに異なる提供状態、権限、契約条件と、固定の仕様を分け、テーマごとの正本へ案内します。

ここには、価格、契約内容、APIのリクエスト・レスポンスを掲載しません。機能別の数値は、下の「よく確認される上限」に挙げたものを除き、各正本で確認します。

> 機能の表示・利用可否は、組織によって異なります。契約、提供状態、権限、Project・App設定、外部サービス側の認証・権限が関係します。別の組織・Project・Appで同じ画面や上限が表示されるとは限りません。

## よく確認される上限

| 対象 | 上限・決まり | 正本 |
|---|---|---|
| Storageの容量 | **Project単位**で判定されます。既定は10,240MBです。 | [Storageの仕様・制限](../build/storage-specs-and-limits.md) |
| Project Secretの値 | 最大8,192文字。ただしPageの環境変数として渡すときは**1値あたり4,096バイト**まで（超えるとPageの起動時に失敗します）。 | [Project Secretを登録・更新・削除する](manage-project-secrets.md) |
| Webログイン用シークレット | 隠されるのはパスワードのみ。ログインIDとURLは、シークレットを読めるProjectメンバー全員に見えます。 | [Webログイン用シークレットを管理する](manage-web-login-secrets.md) |
| Project APIキー | 作成者がProjectの有効メンバーである間だけ有効。スコープ・有効期限は作成後に変更できません。`credit:read`で読めるAPIはありません。 | [Project APIキーを管理する](manage-project-api-keys.md) |
| 監査ログ | 画面で閲覧のみ（エクスポートなし）。Projectを削除すると開けません。 | [削除・引き継ぎと監査記録を確認する](deletion-handover-and-audit-logs.md) |
| 有料Appの料金 | 月額 ＝ 単価 × 有効メンバー数。月次更新時に自動精算されます。 | [Appの利用人数・料金・契約上限を確認する](check-app-seats-pricing-and-limits.md) |
| プランによる上限 | 無料プランではタスクを作成できません。タスク数には組織ごとの上限があります。SkillとPageには組織単位の数の上限があり、一覧画面の使用量バーに表示されます。 | [Workflow・自動実行の仕様・制限](../build/workflow-and-automation-specs.md)、[Skillの仕様・制限](../build/skill-specs-and-limits.md)、[Pageの仕様・制限](../build/page-specs-and-limits.md) |

## まず、条件の種類を分ける

制限や「利用できない」という表示は、次のように分けて確認します。

| 条件の種類 | 確認する内容 | 最初に開く記事 |
|---|---|---|
| 機能固有の仕様・上限 | 作成物、入力、アップロード、構造、実行設定などの制約 | 下の機能別記事 |
| 権限・共有範囲 | 組織、Project、App、リソースごとに誰が表示・操作できるか | [組織・Project・Appの権限を理解する](understand-org-project-app-permissions.md) |
| 認証・接続 | 個人接続、共有接続、外部サービス側の認可、Secretの扱い | [個人接続・共有接続・Secretを選ぶ](choose-personal-shared-connections-and-secrets.md) |
| 契約・人数・料金 | Appの利用人数、契約上限、料金表示、終了時の扱い | [Appの利用人数・料金・契約上限を確認する](check-app-seats-pricing-and-limits.md) |
| 実行に伴う消費・Projectの使用状況 | 組織の実行量・クレジット、Projectのリソース数・ストレージ・記録済みアクティビティの確認 | [実行消費と利用状況を確認する](check-execution-consumption-and-usage.md) |
| API・SDKの通信条件 | 認証、エラー、通信失敗、タイムアウト、共通の制限 | [API／SDK共通のエラー・タイムアウト・制限](../development/api-sdk-errors-timeouts-limits.md) |

## 機能別記事

機能の仕様や上限は、次表の正本で確認します。別の機能・API・契約へ値を流用しません。

| 確認したいテーマ | 正本 | この索引で扱わないこと | あわせて確認する記事 |
|---|---|---|---|
| Agent | [Agentの仕様・制限](../build/agent-specs-and-limits.md) | モデル・組織設定に依存する提供可否、契約上の作成数 | [Agentを作成して指示・モデルを設定する](../build/create-agent-instructions-and-models.md)、[Agentにツール・Skill・Datasetを設定する](../build/configure-agent-tools-skills-datasets.md) |
| Workflow・自動実行 | [Workflow・自動実行の仕様・制限](../build/workflow-and-automation-specs.md) | 実行ごとの消費額、外部サービス側の通信制限 | [タスクスケジューラを設定する](../build/configure-task-scheduler.md)、[定期実行を変更・停止・引き継ぐ](change-stop-handover-scheduled-runs.md) |
| Dataset | [Datasetの仕様・制限](../build/dataset-specs-and-limits.md) | クレジット残高、外部取得元の制約 | [Datasetを準備して参照させる](../build/prepare-and-reference-datasets.md) |
| Object | [Objectの仕様・制限](../build/object-specs-and-limits.md) | API・SQL経由の個別仕様 | [Objectの構造を設計する](../build/design-object-structure.md)、[Objectのデータを閲覧・更新する](../use/view-and-update-object-records.md) |
| Storage | [Storageの仕様・制限](../build/storage-specs-and-limits.md) | API経由の条件（容量はProject単位、既定10,240MB） | [Storageのフォルダ構成を整える](../build/organize-storage-folders.md)、[Storageのファイルを利用する](../use/use-storage-files.md) |
| Page | [Pageの仕様・制限](../build/page-specs-and-limits.md) | 開発時の実行環境、通信・実行時間 | [画面からPageを登録・公開する](../build/register-and-publish-pages.md)、[Pageのソース構成とランタイム](../development/page-source-structure-and-runtime.md) |
| Skill | [Skillの仕様・制限](../build/skill-specs-and-limits.md) | 組織ごとの利用可否、ツール・接続先の権限 | [Skillを作成して利用する](../build/create-and-use-skills.md) |

## 管理・契約・接続の記事

利用条件や管理設定は、次表を確認します。

| 確認したいテーマ | 正本 | 確認の要点 |
|---|---|---|
| Project Secret | [Project Secretを登録・更新・削除する](manage-project-secrets.md) | 読めるのはPageのサーバー処理とAPI呼び出しノードのみで、Agentからは読めません。参照方法は[Pageのサーバー処理・環境変数・Secret参照](../development/page-server-env-and-secrets.md)で確認します。 |
| Webログイン用シークレット | [Webログイン用シークレットを管理する](manage-web-login-secrets.md) | パスワード以外（ログインID・URL）はProjectメンバーに見えます。 |
| Project APIキー | [Project APIキーを管理する](manage-project-api-keys.md) | スコープの一覧、作成者に結び付く有効期間、マニフェストで宣言できないスコープを確認します。 |
| Memory | [MemoryとProject共有情報を管理する](manage-memory-and-project-shared-info.md) | Project内のAgentが参照するのはProject範囲のMemoryのみです。範囲間で移す機能はありません。 |
| 削除・引き継ぎ・監査ログ | [削除・引き継ぎと監査記録を確認する](deletion-handover-and-audit-logs.md) | Projectの削除は物理削除です。監査ログはエクスポートできず、削除後は開けません。 |
| Project間の移行 | [Project間の移行を計画する](plan-project-migration.md) | リンクできるのは組織レベル・公式のリソースのみで、Project間のコピー機能はありません。 |
| Appの利用人数・契約・料金 | [Appの利用人数・料金・契約上限を確認する](check-app-seats-pricing-and-limits.md) | 有料Appは単価 × 有効メンバー数で課金され、メンバーを削除すると次回の更新料金が下がります。契約上限、料金確認、終了時の案内を確認します。 |
| 実行消費・利用状況 | [実行消費と利用状況を確認する](check-execution-consumption-and-usage.md) | 組織のクレジット利用状況と、Projectの使用状況（リソース・ストレージ・記録値）を分け、Appの人数・料金とも混同しません。 |
| 個人・共有接続 | [個人接続・共有接続・Secretを選ぶ](choose-personal-shared-connections-and-secrets.md)、[Project・Appの共有接続を設定する](configure-shared-connections.md) | 所有範囲、認証アカウント、利用者の準備、外部サービス側の権限を確認します。 |
| 外部ツール・データ連携・MCP | [外部ツール・データ連携・MCPを管理する](manage-external-tools-integrations-mcp.md) | 登録、Agentへの追加、認証、共有範囲、接続先の制限を分けます。MCPの登録だけで全利用者が使えるとは判断しません。 |
| App配布時の依存・認証設定 | [配布対象・依存・認証設定の制約](../distribution/distribution-scope-dependencies-auth.md) | 配布対象、持ち運べない設定、導入先での再設定を確認します。通常の機能上限や導入手順とは区別します。 |

## API・SDKを使う場合

画面操作の上限と、API・SDKで呼び出す条件は異なることがあります。開発時は、機能別の正本と経路に対応するAPI記事を確認します。

| 確認対象 | 正本 |
|---|---|
| API・SDK共通のエラー、タイムアウト、通信制御、共通制限 | [API／SDK共通のエラー・タイムアウト・制限](../development/api-sdk-errors-timeouts-limits.md) |
| Agent・会話・成果物のAPI | [Agents・会話・成果物API](../api/agents-conversations-artifacts-api.md) |
| ObjectのAPI | [Objects API](../api/objects-api.md) |
| DatasetのAPI | [Datasets API](../api/datasets-api.md)（書込み系APIは本番に存在しますが、現時点ではこのマニュアルのサポート対象外です。予告なく変更される可能性があります。公開OpenAPI（https://api.japan-ai.co.jp/reference）を参照してください） |
| ProjectメンバーのAPI | [Project Members API](../api/project-members-api.md)（`member:read`が必要） |
| StorageのAPI | [Storage API](../api/storage-api.md) |
| WorkflowのAPI | [Workflows API](../api/workflows-api.md) |
| PageのAPI | [Pages API](../api/pages-api.md) |
| 外部ツール呼出のAPI | [Tool Proxy API](../api/tool-proxy-api.md) |

> APIキーを発行できても、すべてのAPIやSDKの利用条件を満たすわけではありません。キーの管理は[Project APIキーを管理する](manage-project-api-keys.md)、実行モード・認証・対応範囲は[実行モード・認証・対応範囲](../development/execution-modes-and-auth.md)を確認してください。

## 記事の選び方と確認順

1. 対象を機能固有、契約・人数、実行消費、API・SDK、接続・MCPに分けます。
2. 該当する記事を開き、対象の組織、Project、App、利用経路を確認します。
3. 数値は各記事で適用条件と単位を確認し、別機能の値を代用しません。
4. 画面に提供条件、上限、警告、料金、残高が表示される場合は、その表示を優先します。
5. 記事にない契約条件、組織別の提供状態は、管理担当者またはサポートへ問い合わせます。

## よくある誤解

| 誤解 | 正しい確認 |
|---|---|
| 別機能にも同じ上限値を使える | 機能・入力経路・APIごとの正本を開きます。 |
| 画面に機能がないため、利用できない | 提供状態、権限、対象Project・App、契約条件を確認します。 |
| Appの利用人数と実行消費は同じ料金条件である | [Appの利用人数・料金・契約上限を確認する](check-app-seats-pricing-and-limits.md)と[実行消費と利用状況を確認する](check-execution-consumption-and-usage.md)の確認対象を分けます。 |
| 接続できたため、MCPや外部ツールを全員が使える | Agentへの追加、共有範囲、認証、外部サービス側の権限を確認します。 |
| APIで呼べる機能は画面と同じ制限である | [API／SDK共通のエラー・タイムアウト・制限](../development/api-sdk-errors-timeouts-limits.md)と各API記事で、認証・通信・API固有の条件を確認します。 |

## うまくいかない場合

正本の記事で該当項目が見つからない、画面と正本が一致しない、または組織別の提供条件が分からない場合は、対象の組織・Project・App、機能名、利用経路、操作日時、画面の案内やエラーを記録して、管理担当者またはサポートへ確認してください。Secret、APIキー、トークン、パスワード、個人情報、機密データは問い合わせに含めません。

## 関連記事・次に読む記事

- [Agentの仕様・制限](../build/agent-specs-and-limits.md)
- [Pageの仕様・制限](../build/page-specs-and-limits.md)
- [Project Secretを登録・更新・削除する](manage-project-secrets.md)
- [Appの利用人数・料金・契約上限を確認する](check-app-seats-pricing-and-limits.md)
- [API／SDK共通のエラー・タイムアウト・制限](../development/api-sdk-errors-timeouts-limits.md)
