# CLI Workflows

`jai-studio workflows`は、端末やCIでWorkflowを取得・保存・公開・実行する開発者向けCLIです。SDKでの実装は[SDK Workflows](../development/sdk-workflows.md)、認証・プロファイルは[CLIの設定・プロファイル](cli-config-and-profiles.md)を参照してください。

## 関連記事

- [SDK Workflows](../development/sdk-workflows.md)
- [Workflowを公開する](../build/publish-workflow.md)
- [Workflow・自動実行の仕様・制限](../build/workflow-and-automation-specs.md)
- [CLIの設定・プロファイル](cli-config-and-profiles.md)

## 始める前に

SDKパッケージ`@japan-ai-inc/studio-sdk` 0.11.0とNode.js 22以降を使います。認証情報は、グローバルオプション、環境変数、プロファイルの順で優先されます。`--key`は履歴やプロセス一覧に残り得るため、通常はプロファイルまたは`JAI_STUDIO_API_KEY`を使ってください。

```text
jai-studio --profile <profile> workflows list
```

以下の`<...>`を含む表記は**置換が必要な構文表記**です。角括弧を含めてそのままシェルへ貼り付けず、実際の値に置き換えてください。

| グローバルオプション | 必須 | 意味 |
|---|---:|---|
| `--profile <name>` | いいえ | プロファイルを指定します。 |
| `--env <env>` | いいえ | `production`、またはカスタムURLです。 |
| `--key <key>` | いいえ | Project APIキーを一時的に上書きします。 |
| `--pretty` | いいえ | 成功時JSONを整形します。 |
| `--debug` | いいえ | サーバーエラーの詳細を表示します。共有ログでは使いません。 |

成功結果はJSONを標準出力へ出します。`--json`オプションはありません。失敗時はJSONエラーを標準エラーへ出し、終了コードは1です。

> `publish`、`delete`、`run`、`run-and-wait`は外部送信やデータ更新を起こし得ます。実行前にProject、ID、定義、入力を確認し、APIキー、Webhook Secret、業務データをコマンド、定義、CIログへ残さないでください。

## 一覧・取得・作成・更新

### `list`

```text
jai-studio workflows list [--cursor <cursor>] [--limit <n>] [--status <status>]
```

| 引数・オプション | 必須 | 既定 | 意味 |
|---|---:|---|---|
| `--cursor <cursor>` | いいえ | なし | 次ページを取得するカーソルです。 |
| `--limit <n>` | いいえ | APIの既定 | 1ページの最大件数です。 |
| `--status <status>` | いいえ | `published` | `published`、`draft`、`all`です。`draft`は未公開、`all`は両方を含みます。 |

```bash
jai-studio workflows list --status all --pretty
```

### `get`

```text
jai-studio workflows get <workflowId>
```

| 引数・オプション | 必須 | 既定 | 意味 |
|---|---:|---|---|
| `<workflowId>` | はい | — | 取得するWorkflow IDです。 |

`definition`を取得してファイル化する場合は、オブジェクト全体ではなく`definition`を保存します。

```text
jai-studio workflows get <workflowId> | jq .definition > definition.json
```

### `create`

```text
jai-studio workflows create --name <name> [--description <desc>] [--disabled]
```

| 引数・オプション | 必須 | 既定 | 意味 |
|---|---:|---|---|
| `--name <name>` | はい | — | Workflow名です。空の下書きとして作成されます。 |
| `--description <desc>` | いいえ | なし | 説明です。 |
| `--disabled` | いいえ | 有効 | 無効状態で作成します。 |

```bash
jai-studio workflows create --name "請求書受付" --disabled
```

### `update`

```text
jai-studio workflows update <workflowId> [--name <name>] [--description <desc>] [--enable | --disable]
```

| 引数・オプション | 必須 | 既定 | 意味 |
|---|---:|---|---|
| `<workflowId>` | はい | — | 更新するWorkflow IDです。 |
| `--name <name>` | いいえ | 変更なし | 名前を更新します。 |
| `--description <desc>` | いいえ | 変更なし | 説明を更新します。 |
| `--enable` / `--disable` | いいえ | 変更なし | 有効・無効を切り替えます。同時指定できません。`--disable`はトリガーを無効化します。 |

少なくとも1つの更新オプションが必要です。

```text
jai-studio workflows update <workflowId> --description "検証用" --disable
```

## 下書き・公開・削除

### `save-draft` と `publish`

```text
jai-studio workflows save-draft <workflowId> --file <path>
jai-studio workflows publish <workflowId> --file <path>
```

| 引数・オプション | 必須 | 既定 | 意味 |
|---|---:|---|---|
| `<workflowId>` | はい | — | 対象Workflow IDです。 |
| `--file <path>` | はい | — | `get`の`definition`から作るJSONファイルです。 |

ファイルはJSONオブジェクトで、`graph`が必須、`features`は任意です。`draftUpdatedAt`は同時更新の確認値として送られ、`expectedDraftUpdatedAt`を明示した場合はそちらが優先されます。ほかのキー（例：`source`）は送信されません。不正なトークンはエラーです。

`save-draft`は公開しません。成功後は新しい`draftUpdatedAt`を反映するか、再度`get`してから`publish`します。`publish`は指定した定義を公開し、トリガー登録を試みます。

```text
jai-studio workflows save-draft <workflowId> --file ./definition.json
jai-studio workflows publish <workflowId> --file ./definition.json
```

### `delete`

```text
jai-studio workflows delete <workflowId>
```

| 引数・オプション | 必須 | 既定 | 意味 |
|---|---:|---|---|
| `<workflowId>` | はい | — | 削除するWorkflow IDです。 |

削除後、CLIは削除メッセージをJSONで返します。実行前にIDと影響範囲を確認してください。

```text
jai-studio workflows delete <workflowId>
```

## 実行と実行結果

### `run`

```text
jai-studio workflows run <workflowId> [--input <json>]
```

| 引数・オプション | 必須 | 既定 | 意味 |
|---|---:|---|---|
| `<workflowId>` | はい | — | 実行するWorkflow IDです。 |
| `--input <json>` | いいえ | 空の入力 | Workflow入力のJSON文字列です。 |

```text
jai-studio workflows run <workflowId> --input '{"documentUrl":"https://example.invalid/input.pdf"}'
```

`runId`があれば、`status`で状態・出力を取得します。再送前に取得済みの`runId`を確認してください。

### `run-and-wait`

```text
jai-studio workflows run-and-wait <workflowId> [--input <json>] [--timeout <seconds>]
```

| 引数・オプション | 必須 | 既定 | 意味 |
|---|---:|---|---|
| `<workflowId>` | はい | — | 実行するWorkflow IDです。 |
| `--input <json>` | いいえ | 空の入力 | Workflow入力として渡すJSON文字列です。 |
| `--timeout <seconds>` | いいえ | 60 | 完了待機の上限です。整数で1〜270秒を指定します。 |

待機中と完了状態のメッセージは標準エラー、結果JSONは標準出力です。

```text
jai-studio workflows run-and-wait <workflowId> --input '{"query":"確認"}' --timeout 60
```

### `status`（実行結果）

```text
jai-studio workflows status <runId>
```

| 引数・オプション | 必須 | 既定 | 意味 |
|---|---:|---|---|
| `<runId>` | はい | — | `run`などが返した実行IDです。 |

```text
jai-studio workflows status <runId> --pretty
```

状態が`RUNNING`であれば、完了まで同じ`runId`で取得を続けます。`SUCCEEDED`だけでなく、`PARTIAL_SUCCEEDED`、`FAILED`、`STOPPED`を区別し、出力・エラーと外部処理の実行済み状況を確認してから再実行を判断してください。

## Workflowの予定実行を管理する

SDK 0.11.0の`scheduled-tasks`は、Projectが作成したWorkflowを予定実行として管理します。各タスクはProject APIキーの作成者として実行されます。`list`には`task:read`、`create`・`update`には`task:write`と`workflow:execute`、`delete`には`task:delete`が必要です。リンクされた組織Workflowは対象外です。

```text
jai-studio scheduled-tasks list --search "週次"
jai-studio scheduled-tasks create --file ./task.json
jai-studio scheduled-tasks update <taskId> --file ./patch.json
jai-studio scheduled-tasks delete <taskId>
```

作成JSONでは`name`、`workflowId`、`triggerTime`が必須です。繰返しには`repeatPattern`または`cronSchedule`を`schedule`へ指定し、省略すると1回実行になります。作成は冪等ではないため、作成前と5xx後の再送前に名前で一覧を確認します。更新は指定項目だけを変え、`inputs`はキー単位でマージします。

## うまくいかない場合

| 状況 | 確認・対応 |
|---|---|
| 一覧に対象がない | `--status all`、Project、`--cursor`を確認します。 |
| `--file`で失敗する | `graph`、保存した`.definition`、最新の`draftUpdatedAt`を確認します。 |
| 保存・公開が競合する | `get`で定義を取り直し、最新の`draftUpdatedAt`を使います。 |
| 入力JSONまたはタイムアウトが不正 | 引用符、JSON、`--timeout`が1〜270の整数であることを確認します。 |
| エラー詳細が不足する | 対象ID、CLI版、時刻、エラーJSONを記録します。必要な場合だけ、安全な環境で`--debug`を使います。 |

## チェックリスト

- [ ] CLI、Node.js、対象Projectを確認した
- [ ] プロファイルまたは安全な環境変数で認証し、キーをコマンド引数・ログへ残していない
- [ ] `get`の`definition`を起点に下書き・公開用ファイルを作った
- [ ] 保存後は最新の`draftUpdatedAt`を使った
- [ ] 公開後にトリガーと実際の利用経路を確認した
- [ ] 実行IDを記録し、重複実行を避けて結果を確認した

## 次に読む記事

- [SDK Workflows](../development/sdk-workflows.md)
- [Workflowを公開する](../build/publish-workflow.md)
- [Workflow・自動実行の仕様・制限](../build/workflow-and-automation-specs.md)
- [CLIの設定・プロファイル](cli-config-and-profiles.md)
