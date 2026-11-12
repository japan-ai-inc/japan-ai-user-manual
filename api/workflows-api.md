# Workflows API

Workflows APIは、Project内のWorkflowの取得・作成、下書き保存、公開、実行、実行結果照会を行う開発者向けREST APIです。

## 関連記事

- [SDK Workflows](../development/sdk-workflows.md)
- [CLI Workflows](../cli/cli-workflows.md)
- [Workflow・自動実行の仕様・制限](../build/workflow-and-automation-specs.md)

## 始める前に

ベースURLは`https://api.japan-ai.co.jp`、共通パスは`/api/v1/workflows`です。Project APIキーを`x-api-key`ヘッダーへ渡します。各操作に必要なスコープを持つ対象Projectのキーを使います。キーをブラウザ、ソース管理、ログ、共有履歴へ残してはいけません。

```bash
export JAI_STUDIO_API_KEY='安全な保管場所から取得した値'
curl -sS 'https://api.japan-ai.co.jp/api/v1/workflows?status=all' \
  -H "x-api-key: $JAI_STUDIO_API_KEY"
```

正常な応答には`success`と`requestId`が含まれます。キーなし・無効は401、スコープ・権限不足は403です。429と待機情報は[API／SDK共通のエラー・タイムアウト・制限](../development/api-sdk-errors-timeouts-limits.md)を参照してください。

> 書込み・実行は状態を変えます。検証用Projectで確認し、タイムアウト時は再送前に`runId`または現在の定義を確認してください。

## エンドポイント一覧

| 操作 | メソッド・パス | 必要スコープ |
|---|---|---|
| 一覧 | `GET /api/v1/workflows` | `workflow:read` |
| 詳細 | `GET /api/v1/workflows/{workflowId}` | `workflow:read` |
| 作成 | `POST /api/v1/workflows` | `workflow:write` |
| メタデータ更新 | `PATCH /api/v1/workflows/{workflowId}` | `workflow:write` |
| 下書き保存 | `PUT /api/v1/workflows/{workflowId}/draft` | `workflow:write` |
| 公開 | `POST /api/v1/workflows/{workflowId}/publish` | `workflow:write` |
| 削除 | `DELETE /api/v1/workflows/{workflowId}` | `workflow:delete` |
| 実行開始 | `POST /api/v1/workflows/{workflowId}/run` | `workflow:execute` |
| 実行待機 | `POST /api/v1/workflows/{workflowId}/run-and-wait` | `workflow:execute` |
| 実行結果 | `GET /api/v1/workflows/runs/{runId}` | `workflow:read` |

`workflowId`は対象Workflow ID、`runId`はUUID形式の実行IDです。対象がProjectにない場合は404です。読み取り専用リンクへの書込み・削除は403または409になる場合があります。

## 1. 一覧・詳細を取得する

`GET /api/v1/workflows`は有効なWorkflowをカーソルで返します。`status`の既定は`published`、`draft`は公開済みスナップショットを持たない下書き、`all`は両方です。

| パラメーター | 場所 | 必須 | 既定・制限 | 意味 |
|---|---|---:|---|---|
| `limit` | query | いいえ | 50、1〜100 | 1ページの件数 |
| `cursor` | query | いいえ | なし | 前回の`nextCursor` |
| `status` | query | いいえ | `published` | `published`、`draft`、`all` |

応答は`workflows`、`totalCount`、`nextCursor`を含みます。各Workflowは`id`、`name`、`description`、`enabled`、`isPublished`、`createdAt`、`updatedAt`を持ちます。`GET /{workflowId}`は`definition`も返します。定義は`graph`、`features`、`source`、`draftUpdatedAt`を持ち、なければ`null`です。

```bash
curl -sS 'https://api.japan-ai.co.jp/api/v1/workflows?limit=50&status=all' \
  -H "x-api-key: $JAI_STUDIO_API_KEY"
```

## 2. 作成とメタデータ更新を行う

`POST /api/v1/workflows`は空の下書きを持つ未公開Workflowを作成します。`name`は必須（1〜255文字）、`description`は任意、`enabled`は既定で`true`です。成功時は201で`workflow`を返します。

`PATCH /{workflowId}`は`name`、`description`、`enabled`の1つ以上を渡します。成功時は200で更新済み`workflow`を返します。`enabled: false`にすると、メールとStorageのトリガーは無効化されます。

```bash
curl -sS -X POST 'https://api.japan-ai.co.jp/api/v1/workflows' \
  -H "x-api-key: $JAI_STUDIO_API_KEY" -H 'content-type: application/json' \
  --data '{"name":"請求書受付","enabled":false}'
```

## 3. 下書きを保存して公開する

下書き保存は`PUT /{workflowId}/draft`、公開は`POST /{workflowId}/publish`です。`graph`（`nodes`・`edges`配列）と`features`を送ります。グラフ本文は16MBまでです。保存は公開せず、公開は定義をライブの`v1`スナップショットにしてトリガー登録を試みます。

| パラメーター | 場所 | 必須 | 意味 |
|---|---|---:|---|
| `graph.nodes` | body | はい | ノードの配列 |
| `graph.edges` | body | はい | 接続の配列 |
| `features` | body | はい | Workflowの機能設定 |
| `expectedDraftUpdatedAt` | body | いいえ | `GET`または保存成功時の値をそのまま返す同時更新トークン |

保存成功時は`snapshotId`と次操作用`draftUpdatedAt`を返します。`expectedDraftUpdatedAt`を渡し、取得後に他者が変更していると409です。省略時は最後の書込みが優先されます。保存応答の`draftUpdatedAt`を次の保存・公開に使います。

公開成功時は`snapshot`と`unregisteredTriggers`を返します。`snapshot`は`id`、`version`、`createdAt`、`updatedAt`を持ちます。後者が空でなくても公開は成功し得るため、`type`、`nodeId`、`reason`を確認します。定義競合、リリース管理、読み取り専用リンクでは409となる場合があります。

> 下書き保存だけでは、Workflowトリガーやタスクスケジューラが使う公開版は更新されません。変更、テスト、公開、実際の利用経路の確認の順で行います。自動実行は[Workflow・自動実行の仕様・制限](../build/workflow-and-automation-specs.md)を参照してください。

## 4. 削除する

`DELETE /{workflowId}`は成功時に204を返し、本文は返しません。削除前にIDと影響を確認してください。外部からリンクされたWorkflowはこの経路で削除できず、409になる場合があります。

## 5. 実行を開始し、結果を確認する

`POST /{workflowId}/run`の本文は任意の`inputs`です。省略時は空オブジェクトです。成功時は200で`runId`と`status`を返します。実行できるのは公開済みかつ有効なWorkflowです。未公開・無効は403、入力・定義の検証エラーは400、対象なしは404です。408時も`runId`があれば結果照会に使います。

```bash
curl -sS -X POST 'https://api.japan-ai.co.jp/api/v1/workflows/<workflowId>/run' \
  -H "x-api-key: $JAI_STUDIO_API_KEY" -H 'content-type: application/json' \
  --data '{"inputs":{"query":"確認"}}'

curl -sS 'https://api.japan-ai.co.jp/api/v1/workflows/runs/<runId>' \
  -H "x-api-key: $JAI_STUDIO_API_KEY"
```

`GET /runs/{runId}`は`runId`、`status`、必要に応じて`outputs`と`error`を返します。状態は`RUNNING`、`SUCCEEDED`、`FAILED`、`PARTIAL_SUCCEEDED`、`STOPPED`です。`RUNNING`中は同じIDで照会します。不正なIDは400、実行なしは404、照会タイムアウトは408です。

完了まで待つには`POST /{workflowId}/run-and-wait`を使います。`inputs`は任意、`timeout`はミリ秒で既定60,000、1,000〜300,000です。送信からサーバー側照会までを含む期限です。SDKとCLIは通信の猶予時間を確保するため、上限を270秒にしています。REST APIを直接呼ぶ場合の上限は300秒です。408時は`runId`があれば結果照会へ切り替えます。

## 6. 予定実行タスクを扱う場合

Workflowの作成・公開・手動実行と、予定実行タスクの管理は別のAPIです。SDK 0.11.0では`scheduledTasks`と`jai-studio scheduled-tasks`で、Projectが作成したWorkflowの予定実行タスクを一覧・作成・更新・削除できます。タスクはProject APIキーの作成者として実行されます。

| 操作 | メソッド・パス | 必要スコープ |
|---|---|---|
| 一覧 | `GET /api/v1/scheduled-tasks` | `task:read` |
| 作成 | `POST /api/v1/scheduled-tasks` | `task:write`、`workflow:execute` |
| 更新 | `PATCH /api/v1/scheduled-tasks/{taskId}` | `task:write`、`workflow:execute` |
| 削除 | `DELETE /api/v1/scheduled-tasks/{taskId}` | `task:delete` |

作成には`name`、`workflowId`、オフセット付きISO 8601形式の`triggerTime`が必要です。繰返しには`repeatPattern`または`cronSchedule`を指定し、必要に応じて`scheduleTimezone`を指定します。作成・更新は状態不明のまま再送せず、名前で一覧を取得して確認します。`task:*`スコープはAppリリースのマニフェストには宣言できません。詳しい入力形式、停止・引継ぎは[SDK Workflows](../development/sdk-workflows.md)および[定期実行を変更・停止・引き継ぐ](../operations/change-stop-handover-scheduled-runs.md)を参照してください。

## よくある誤解

### `run`が成功したら業務処理も完了している

`run`は`RUNNING`を返す場合があります。`GET /runs/{runId}`で終端状態と、必要なら出力・外部送信結果を確認してください。部分成功、失敗、停止時の安易な再実行は、書込みや通知を重複させる可能性があります。

### `draftUpdatedAt`を日時として加工してよい

これは同時更新の確認値です。解析・生成・丸めをせず、取得した文字列または保存応答の文字列を`expectedDraftUpdatedAt`へそのまま渡してください。409なら定義を再取得し、差分を確認してから再度保存・公開します。

## うまくいかない場合

| 状況 | 確認・対応 |
|---|---|
| 401または403 | キー、Project、スコープ、キー作成者のアクセスを確認します。 |
| 409で保存・公開できない | `GET /{workflowId}`で最新定義と`draftUpdatedAt`を取り直します。 |
| 公開後に起動しない | `unregisteredTriggers`、公開状態、`enabled`、接続を確認します。 |
| 408または通信失敗 | `runId`と実行結果を先に照会し、確認前に再送しません。 |
| 429 | 応答の待機情報に従い、並列数と再送頻度を下げます。 |

問い合わせにはProject、Workflow ID、run ID、時刻、HTTP状態、`requestId`、安全なエラー内容を記録します。キー、認証ヘッダー、Webhook Secret、業務入力は送らないでください。

## チェックリスト

- [ ] 対象Projectに対する最小スコープのAPIキーを安全に設定した
- [ ] 一覧の`status`と`nextCursor`、詳細の`definition`を確認した
- [ ] 下書き・公開で最新の`draftUpdatedAt`をそのまま渡した
- [ ] 公開後に`unregisteredTriggers`と実際の起動経路を確認した
- [ ] `runId`を記録し、結果を終端状態まで照会した
- [ ] タイムアウト・失敗時に状態確認なしで実行を再送していない
- [ ] 予定実行を使う場合は、`task:*`スコープ、キー作成者、次回実行、停止方法を確認した

## 次に読む記事

- [SDK Workflows](../development/sdk-workflows.md)
- [CLI Workflows](../cli/cli-workflows.md)
- [Workflow・自動実行の仕様・制限](../build/workflow-and-automation-specs.md)
