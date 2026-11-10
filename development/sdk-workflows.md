# SDK Workflows

Studio SDKの`workflows`モジュールでは、Workflowの一覧・定義を取得し、下書きの更新・公開、実行結果の確認を行えます。この記事は、TypeScriptまたは`jai-studio` CLIで既存定義を安全に変更し、実行の完了を確定する方法を説明します。

画面からの公開は[Workflowを公開する](../build/publish-workflow.md)、自動実行の仕様は[Workflow・自動実行の仕様・制限](../build/workflow-and-automation-specs.md)を参照してください。

## 関連記事

- [実行モード・認証・対応範囲](execution-modes-and-auth.md)
- [Workflowを公開する](../build/publish-workflow.md)
- [Workflow・自動実行の仕様・制限](../build/workflow-and-automation-specs.md)
- [Workflows API](../api/workflows-api.md)

## 始める前に

- `@japan-ai-inc/studio-sdk` **0.11.0**とNode.js **22以降**を使います。Workflowはローカルの直接開発またはPRSサーバーランタイム向けです。
- 対象ProjectとWorkflow IDを確認し、検証用の入力から始めます。
- APIキーはサーバー側の安全な設定だけに置き、ブラウザやログへ含めません。

> `workflows`はPRSブラウザ向けモジュールではありません。ブラウザへAPIキーを渡しません。

## 1. 一覧と現在の定義を取得する

`list()`は既定で公開済みだけを返します。`status: "draft"`は公開スナップショットのない下書き、`"all"`は両方を返します。`get(workflowId)`の`definition`は下書き、または公開済み`v1`であり、なければ`null`です。

```ts
const page = await studio.workflows.list({ status: "all" });
const current = await studio.workflows.get("workflow-id");
if (!current.definition) throw new Error("Workflowの定義を取得できませんでした");

const { graph, features, draftUpdatedAt } = current.definition;
```

定義は`graph.nodes`と`graph.edges`、必要に応じて`features`を持ちます。まず取得した定義のコピーを変更してください。

### CLIで取得・保存する

CLIはJSONを標準出力へ返します。`definition`を後続の操作へ渡せます。

```bash
jai-studio workflows list --status all
jai-studio workflows get '<workflow-id>' | jq .definition > ./definition.json
```

> `definition.json`をリポジトリや問い合わせへ無条件に追加せず、内容と保管場所を確認してください。

## 2. 下書きを保存する

定義は`saveDraft()`で保存し、これだけでは公開されません。`create()`は未公開Workflowを作り、`update()`は名前、説明、`enabled`を変更します。`enabled: false`またはCLIの`--disable`はトリガーを無効化します。

```ts
const workflowId = "更新対象のWorkflow ID";
const detail = await studio.workflows.get(workflowId);
if (!detail.definition) throw new Error("Workflowの定義がありません");
const editedGraph = structuredClone(detail.definition.graph);
// editedGraphへ必要な変更を加えてから保存します。
const saved = await studio.workflows.saveDraft(workflowId, {
  graph: editedGraph,
  features: detail.definition.features,
  expectedDraftUpdatedAt: detail.definition.draftUpdatedAt,
});
```

`draftUpdatedAt`は解析せず`expectedDraftUpdatedAt`へ返す同時更新用トークンです。他者が下書きを変えると書込みは`409`になります。成功後は`saved.draftUpdatedAt`を使います。

CLIの`--file`は`definition`オブジェクトを受け取ります。

```bash
jai-studio workflows create --name "請求書受付"
jai-studio workflows update '<workflow-id>' --description "検証用" --disable
jai-studio workflows save-draft '<workflow-id>' --file ./definition.json
```

## 3. 同じ定義を公開して結果を確認する

`publish()`はグラフをライブの`v1`スナップショットとし、トリガーの登録も試みます。`unregisteredTriggers`があれば内容を確認します。

```ts
const latest = await studio.workflows.get("更新対象のWorkflow ID");
if (!latest.definition) throw new Error("Workflowの定義がありません");
const published = await studio.workflows.publish(latest.id, {
  graph: latest.definition.graph,
  features: latest.definition.features,
  expectedDraftUpdatedAt: latest.definition.draftUpdatedAt,
});
if (published.unregisteredTriggers.length) console.warn(published.unregisteredTriggers);
```

CLIでは、下書き保存後に`draftUpdatedAt`を更新するか、改めて`get`してから公開します。

```bash
jai-studio workflows publish '<workflow-id>' --file ./definition.json
```

> 公開は以前の公開版へ戻す操作ではありません。代表入力・分岐・外部送信への影響を確認し、公開後に実際の利用経路で結果を確認してください。画面操作は[Workflowを公開する](../build/publish-workflow.md)を参照します。

## 4. 実行IDから状態・出力を確認する

`run()`は任意の`inputs`を受け、`runId`と状態を返します。`getRunResult(runId)`で結果を取得します。

```ts
const started = await studio.workflows.run("workflow-id", {
  inputs: { documentUrl: "https://example.invalid/input.pdf" },
});

const pollDelay = () => new Promise(resolve => setTimeout(resolve, 1_000));
let result = await studio.workflows.getRunResult(started.runId);
while (result.status === "RUNNING") {
  await pollDelay();
  result = await studio.workflows.getRunResult(started.runId);
}

if (result.status === "SUCCEEDED" || result.status === "PARTIAL_SUCCEEDED") {
  useWorkflowOutput(result.outputs);
} else {
  reportWorkflowFailure(result.error);
}
```

`run()`は常に即時に戻る送信専用の呼出しではありません。タイムアウト時は`responseBody`の`runId`で`getRunResult()`を続け、直ちに再送しません。

完了まで待つだけなら、`runAndWait()`またはCLIの`run-and-wait`を使えます。SDKの`timeoutMs`は既定で**60,000ms**、範囲は**1,000〜270,000ms**です。CLIの`--timeout`は秒単位で**1〜270**です。

```bash
jai-studio workflows run '<workflow-id>' --input '{"documentUrl":"https://example.invalid/input.pdf"}'
jai-studio workflows status '<run-id>'
jai-studio workflows run-and-wait '<workflow-id>' --input '{"query":"確認"}' --timeout 60
```

## 5. ProjectのWorkflowを予定実行する

SDK 0.11.0では`scheduledTasks`で、Projectが作成したWorkflowを予定実行できます。各タスクはProject APIキーの作成者として実行され、組織からリンクされたWorkflowは予定実行できません。`list`には`task:read`、作成・更新には`task:write`と`workflow:execute`、削除には`task:delete`が必要です。`scheduledTasks`は直接開発用のモジュールであり、PRSブラウザ・PRSサーバーのSDKでは利用できません。Appリリースのマニフェストでスコープを宣言しても、この実行モードの制約は変わりません。

```ts
const createdTask = await studio.scheduledTasks.create({
  name: "週次集計",
  workflowId: "workflow-id",
  inputs: { region: "jp" },
  triggerTime: "2026-11-09T09:00:00+09:00",
  schedule: { repeatPattern: "WEEKLY", scheduleTimezone: "Asia/Tokyo" },
});

const tasks = await studio.scheduledTasks.list({ search: "週次", limit: 20 });
await studio.scheduledTasks.update(createdTask.taskId, { disabled: true });
```

`schedule`は`repeatPattern`または`cronSchedule`のどちらかを指定し、省略すると1回実行です。更新は指定した項目だけを変更し、`inputs`はキー単位でマージします。作成は冪等ではないため、作成前と5xx後の再送前に、名前で`list()`して既存タスクを確認します。ページングは短いページでも`nextCursor`が`null`になるまで続けます。

## よくある誤解

### `run()`が返ったので、業務処理まで完了している

`RUNNING`なら`getRunResult()`で確認します。

### 下書きを保存すれば自動実行の内容も更新される

更新されません。下書き保存と公開は別です。Workflowトリガーとタスクスケジューラが使う公開版は[Workflow・自動実行の仕様・制限](../build/workflow-and-automation-specs.md)を参照してください。

## うまくいかない場合

| 状況 | 確認・対応 |
|---|---|
| 一覧に期待するWorkflowがない | `status`、Project、`nextCursor`を確認します。 |
| 保存・公開で`409`になる | 定義を再取得し、最新の`draftUpdatedAt`を使います。 |
| 公開後にトリガーが動かない | `unregisteredTriggers`、公開状態、接続を確認します。 |
| 実行がタイムアウトする | `runId`を記録し、`getRunResult()`で確認します。直ちに再実行しません。 |
| CLIの更新・公開が失敗する | `--file`の`definition`と最新トークンを確認します。 |

問い合わせには、Workflow ID、run ID、SDKまたはCLIの版、実行モード、日時、状態、エラーを記録します。APIキー、Secret、認証ヘッダー、業務データを添付しません。

## チェックリスト

- [ ] Node.js 22以降とSDK 0.11.0を使い、実行モードを選んだ
- [ ] `list()`の公開状態フィルターとページングを確認した
- [ ] `get()`で現在の定義を取得し、取得結果を起点に変更した
- [ ] 下書き保存・公開で最新の`draftUpdatedAt`を渡した
- [ ] 公開後に`unregisteredTriggers`と実際の利用経路を確認した
- [ ] `runId`を記録し、`RUNNING`中は`getRunResult()`で状態を確認した
- [ ] 部分成功・失敗・停止では、重複処理を確認してから再実行を判断した

## 次に読む記事

- [Workflowを公開する](../build/publish-workflow.md)
- [Workflow・自動実行の仕様・制限](../build/workflow-and-automation-specs.md)
- [Workflows API](../api/workflows-api.md)
