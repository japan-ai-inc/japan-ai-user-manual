# 制限・提供条件の索引

機能を使えない、数が増やせない、料金や消費の判断が必要なときに、**どの記事を正本として確認するか**を案内する索引です。個々の上限値、料金、API仕様、設定手順は複製しません。該当する正本と、実際の画面表示を確認してください。

## この記事の範囲

組織ごとに異なる提供状態、権限、契約条件と、固定の仕様を分け、テーマごとの正本へ案内します。

ここには、機能別の数値、価格、契約内容、APIのリクエスト・レスポンスを掲載しません。画面に項目が見当たらないことだけを、無制限・無料・未契約の根拠にはしません。

> 機能の表示・利用可否は、組織によって異なります。契約、提供状態、権限、Project・App設定、外部サービス側の認証・権限が関係します。別の組織・Project・Appで同じ画面や上限が表示されるとは限りません。

## まず、条件の種類を分ける

制限や「利用できない」という表示は、次のように分けて確認します。

| 条件の種類 | 確認する内容 | 最初に開く記事 |
|---|---|---|
| 機能固有の仕様・上限 | 作成物、入力、アップロード、構造、実行設定などの制約 | 下の機能別記事 |
| 権限・共有範囲 | 組織、Project、App、リソースごとに誰が表示・操作できるか | [組織・Project・Appの権限を理解する](understand-org-project-app-permissions.md) |
| 認証・接続 | 個人接続、共有接続、外部サービス側の認可、Secretの扱い | [個人接続・共有接続・Secretを選ぶ](choose-personal-shared-connections-and-secrets.md) |
| 契約・人数・料金 | Appの利用人数、契約上限、料金表示、終了時の扱い | [Appの利用人数・料金・契約上限を確認する](check-app-seats-pricing-and-limits.md) |
| 実行に伴う消費・Project使用量 | 組織の実行量・クレジット、Projectのリソース数・ストレージ・記録済みアクティビティの確認 | [実行消費と利用状況を確認する](check-execution-consumption-and-usage.md) |
| API・SDKの通信条件 | 認証、エラー、通信失敗、タイムアウト、共通の制限 | [API／SDK共通のエラー・タイムアウト・制限](../development/api-sdk-errors-timeouts-limits.md) |

## 機能別記事

機能の仕様や上限は、次表の正本で確認します。別の機能・API・契約へ値を流用しません。

| 確認したいテーマ | 正本 | この索引で扱わないこと | あわせて確認する記事 |
|---|---|---|---|
| Agent | [Agentの仕様・制限](../build/agent-specs-and-limits.md) | モデル・組織設定に依存する提供可否、契約上の作成数 | [Agentを作成して指示・モデルを設定する](../build/create-agent-instructions-and-models.md)、[Agentにツール・Skill・Datasetを設定する](../build/configure-agent-tools-skills-datasets.md) |
| Workflow・自動実行 | [Workflow・自動実行の仕様・制限](../build/workflow-and-automation-specs.md) | 実行ごとの消費額、外部サービス側の通信制限 | [タスクスケジューラを設定する](../build/configure-task-scheduler.md)、[定期実行を変更・停止・引き継ぐ](change-stop-handover-scheduled-runs.md) |
| Dataset | [Datasetの仕様・制限](../build/dataset-specs-and-limits.md) | 組織ごとの容量、クレジット残高、外部取得元の制約 | [Datasetを準備して参照させる](../build/prepare-and-reference-datasets.md) |
| Object | [Objectの仕様・制限](../build/object-specs-and-limits.md) | API・SQL経由の個別仕様 | [Objectの構造を設計する](../build/design-object-structure.md)、[Objectのデータを閲覧・更新する](../use/view-and-update-object-records.md) |
| Storage | [Storageの仕様・制限](../build/storage-specs-and-limits.md) | 契約・設定に依存する保存容量、API経由の条件 | [Storageのフォルダ構成を整える](../build/organize-storage-folders.md)、[Storageのファイルを利用する](../use/use-storage-files.md) |
| Page | [Pageの仕様・制限](../build/page-specs-and-limits.md) | 開発時の実行環境、通信・実行時間、組織別の提供数 | [画面からPageを登録・公開する](../build/register-and-publish-pages.md)、[Pageのソース構成とランタイム](../development/page-source-structure-and-runtime.md) |
| Skill | [Skillの仕様・制限](../build/skill-specs-and-limits.md) | 組織ごとの利用可否、ツール・接続先の権限 | [Skillを作成して利用する](../build/create-and-use-skills.md) |

## 管理・契約・接続の記事

利用条件や管理設定は、次表を確認します。

| 確認したいテーマ | 正本 | 確認の要点 |
|---|---|---|
| Project Secret | [Project Secretを登録・更新・削除する](manage-project-secrets.md) | 登録・更新・削除、入力規則、操作権限、参照中の機能への影響を確認します。安全な参照方法は[Pageのサーバー処理・環境変数・Secret参照](../development/page-server-env-and-secrets.md)で確認します。 |
| Appの利用人数・契約・料金 | [Appの利用人数・料金・契約上限を確認する](check-app-seats-pricing-and-limits.md) | 利用者数、購入数、料金確認、更新、終了時の案内を分けて確認します。表示がないことを費用なしの根拠にしません。 |
| 実行消費・利用状況 | [実行消費と利用状況を確認する](check-execution-consumption-and-usage.md) | 組織のクレジット利用状況と、Projectの使用量（リソース・ストレージ・記録値）を分け、Appの人数・料金とも混同しません。 |
| 個人・共有接続 | [個人接続・共有接続・Secretを選ぶ](choose-personal-shared-connections-and-secrets.md)、[Project・Appの共有接続を設定する](configure-shared-connections.md) | 所有範囲、認証アカウント、利用者の準備、外部サービス側の権限を確認します。 |
| 外部ツール・データ連携・MCP | [外部ツール・データ連携・MCPを管理する](manage-external-tools-integrations-mcp.md) | 登録、Agentへの追加、認証、共有範囲、接続先の制限を分けます。MCPの登録だけで全利用者が使えるとは判断しません。 |
| App配布時の依存・認証設定 | [配布対象・依存・認証設定の制約](../distribution/distribution-scope-dependencies-auth.md) | 配布対象、持ち運べない設定、導入先での再設定を確認します。通常の機能上限や導入手順とは区別します。 |

## API・SDKを使う場合

画面操作の上限と、API・SDKで呼び出す条件は同じとは限りません。開発時は、機能別の正本と経路に対応するAPI記事を確認します。

| 確認対象 | 正本 |
|---|---|
| API・SDK共通のエラー、タイムアウト、通信制御、共通制限 | [API／SDK共通のエラー・タイムアウト・制限](../development/api-sdk-errors-timeouts-limits.md) |
| Agent・会話・成果物のAPI | [Agents・会話・成果物API](../api/agents-conversations-artifacts-api.md) |
| ObjectのAPI | [Objects API](../api/objects-api.md) |
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
5. 記事にない契約条件、組織別の提供状態、API固有の条件は、管理担当者またはサポートへ確認します。

## よくある誤解

| 誤解 | 正しい確認 |
|---|---|
| 別機能にも同じ上限値を使える | 機能・入力経路・APIごとの正本を開きます。 |
| 画面に機能がないため、利用できない | 提供状態、権限、対象Project・App、契約条件を確認します。 |
| Appの利用人数と実行消費は同じ料金条件である | [Appの利用人数・料金・契約上限を確認する](check-app-seats-pricing-and-limits.md)と[実行消費と利用状況を確認する](check-execution-consumption-and-usage.md)の確認対象を分けます。 |
| 接続できたため、MCPや外部ツールを全員が使える | Agentへの追加、共有範囲、認証、外部サービス側の権限を確認します。 |
| APIで呼べる機能は画面と同じ制限である | [API／SDK共通のエラー・タイムアウト・制限](../development/api-sdk-errors-timeouts-limits.md)と各API記事で、認証・通信・API固有の条件を確認します。 |

## 確認できない場合

正本の記事で該当項目が見つからない、画面と正本が一致しない、または組織別の提供条件が判断できない場合は、対象の組織・Project・App、機能名、利用経路、操作日時、画面の案内やエラーを記録して、管理担当者またはサポートへ確認してください。Secret、APIキー、トークン、パスワード、個人情報、機密データは問い合わせに含めません。

## 関連記事・次に読む記事

- [Agentの仕様・制限](../build/agent-specs-and-limits.md)
- [Pageの仕様・制限](../build/page-specs-and-limits.md)
- [Project Secretを登録・更新・削除する](manage-project-secrets.md)
- [Appの利用人数・料金・契約上限を確認する](check-app-seats-pricing-and-limits.md)
- [API／SDK共通のエラー・タイムアウト・制限](../development/api-sdk-errors-timeouts-limits.md)
