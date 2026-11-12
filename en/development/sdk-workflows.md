# SDK Workflows

Studio SDK's `workflows` module lets you retrieve Workflow lists and definitions, update and publish drafts, and check run results. This article explains how to safely change existing definitions with TypeScript or the `jai-studio` CLI and confirm that a run is complete.

For publishing from the screen, see [Publish a Workflow](../build/publish-workflow.md). For automatic execution specifications, see [Workflow and Automation Specifications and Limits](../build/workflow-and-automation-specs.md).

## Related articles

- [Execution Modes, Authentication, and Supported Scope](execution-modes-and-auth.md)
- [Publish a Workflow](../build/publish-workflow.md)
- [Workflow and Automation Specifications and Limits](../build/workflow-and-automation-specs.md)
- [Workflows API](../api/workflows-api.md)

## Before you begin

- Use `@japan-ai-inc/studio-sdk` **0.11.0** and Node.js **22 or later**. Workflows are intended for local direct development or the PRS server runtime.
- Check the target Project and Workflow ID, and start with test inputs.
- Keep API keys only in secure server-side settings. Do not include them in browsers or logs.

> `workflows` is not a module for PRS browsers. Do not pass API keys to the browser.

## 1. Get the list and current definition

By default, `list()` returns only published Workflows. `status: "draft"` returns drafts with no published snapshot, and `"all"` returns both. `definition` from `get(workflowId)` is a draft or published `v1`, or `null` if neither exists.

```ts
const page = await studio.workflows.list({ status: "all" });
const current = await studio.workflows.get("workflow-id");
if (!current.definition) throw new Error("Could not retrieve the Workflow definition");

const { graph, features, draftUpdatedAt } = current.definition;
```

A definition has `graph.nodes` and `graph.edges`, and `features` when necessary. First modify a copy of the definition you retrieved.

### Retrieve and save with the CLI

The CLI returns JSON to standard output. You can pass `definition` to subsequent operations.

```bash
jai-studio workflows list --status all
jai-studio workflows get '<workflow-id>' | jq .definition > ./definition.json
```

> Do not unconditionally add `definition.json` to a repository or inquiry; check its contents and storage location.

## 2. Save a draft

Save a definition with `saveDraft()`; this alone does not publish it. `create()` creates an unpublished Workflow, and `update()` changes the name, description, and `enabled`. `enabled: false` or the CLI's `--disable` disables triggers.

```ts
const workflowId = "Workflow ID to update";
const detail = await studio.workflows.get(workflowId);
if (!detail.definition) throw new Error("The Workflow has no definition");
const editedGraph = structuredClone(detail.definition.graph);
// Make the required changes to editedGraph before saving.
const saved = await studio.workflows.saveDraft(workflowId, {
  graph: editedGraph,
  features: detail.definition.features,
  expectedDraftUpdatedAt: detail.definition.draftUpdatedAt,
});
```

`draftUpdatedAt` is a concurrency token that you return to `expectedDraftUpdatedAt` without parsing it. The write returns `409` if someone else changes the draft. After success, use `saved.draftUpdatedAt`.

The CLI's `--file` accepts a `definition` object.

```bash
jai-studio workflows create --name "Invoice intake"
jai-studio workflows update '<workflow-id>' --description "For validation" --disable
jai-studio workflows save-draft '<workflow-id>' --file ./definition.json
```

## 3. Publish the same definition and check the result

`publish()` makes the graph the live `v1` snapshot and also attempts to register triggers. Check the contents if `unregisteredTriggers` is present.

```ts
const latest = await studio.workflows.get("Workflow ID to update");
if (!latest.definition) throw new Error("The Workflow has no definition");
const published = await studio.workflows.publish(latest.id, {
  graph: latest.definition.graph,
  features: latest.definition.features,
  expectedDraftUpdatedAt: latest.definition.draftUpdatedAt,
});
if (published.unregisteredTriggers.length) console.warn(published.unregisteredTriggers);
```

With the CLI, update `draftUpdatedAt` after saving the draft, or run `get` again before publishing.

```bash
jai-studio workflows publish '<workflow-id>' --file ./definition.json
```

> Publishing is not an operation to revert to a previous published version. Check the effects on representative inputs, branches, and external sends, then check the result through the actual usage path after publishing. For screen operations, see [Publish a Workflow](../build/publish-workflow.md).

## 4. Check status and output from a run ID

`run()` accepts arbitrary `inputs` and returns `runId` and status. Use `getRunResult(runId)` to retrieve the result.

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

`run()` is not always a send-only call that returns immediately. On timeout, continue with `getRunResult()` using the `runId` in `responseBody`; do not immediately resend it.

If you only need to wait for completion, use `runAndWait()` or the CLI's `run-and-wait`. The SDK's `timeoutMs` defaults to **60,000ms**, with a range of **1,000–270,000ms**. The CLI's `--timeout` is in seconds, with a range of **1–270**. This is shorter than the limit for direct REST API calls (300 seconds) to allow time for communication.

```bash
jai-studio workflows run '<workflow-id>' --input '{"documentUrl":"https://example.invalid/input.pdf"}'
jai-studio workflows status '<run-id>'
jai-studio workflows run-and-wait '<workflow-id>' --input '{"query":"Check"}' --timeout 60
```

## 5. Schedule a Project Workflow

In SDK 0.11.0, you can use `scheduledTasks` to schedule Workflows created by the Project. Each Task runs as the creator of the Project API key, and Workflows linked from the organization cannot be scheduled. `list` requires `task:read`; creation and updates require `task:write` and `workflow:execute`; deletion requires `task:delete`. `scheduledTasks` is a module for direct development and cannot be used in the PRS browser or PRS server SDK. Declaring scopes in an App release manifest does not change this execution mode restriction.

```ts
const createdTask = await studio.scheduledTasks.create({
  name: "Weekly summary",
  workflowId: "workflow-id",
  inputs: { region: "jp" },
  triggerTime: "2026-11-09T09:00:00+09:00",
  schedule: { repeatPattern: "WEEKLY", scheduleTimezone: "Asia/Tokyo" },
});

const tasks = await studio.scheduledTasks.list({ search: "Weekly", limit: 20 });
await studio.scheduledTasks.update(createdTask.taskId, { disabled: true });
```

Specify either `repeatPattern` or `cronSchedule` in `schedule`; if omitted, the Task runs once. Updates change only the specified fields, and `inputs` are merged by key. Creation is not idempotent, so use `list()` by name to check for an existing Task before creation and before resending after a 5xx error. Continue pagination until `nextCursor` is `null`, even for short pages.

## Common misconceptions

### Because `run()` returned, business processing is complete

If it is `RUNNING`, check with `getRunResult()`.

### Saving a draft also updates automatic execution content

It does not. Saving a draft and publishing are separate. For the published version used by Workflow triggers and the Task Scheduler, see [Workflow and Automation Specifications and Limits](../build/workflow-and-automation-specs.md).

## If something does not work

| Situation | Check and action |
|---|---|
| The expected Workflow is not in the list | Check `status`, the Project, and `nextCursor`. |
| You get `409` when saving or publishing | Retrieve the definition again and use the latest `draftUpdatedAt`. |
| A trigger does not run after publishing | Check `unregisteredTriggers`, publishing status, and connections. |
| A run times out | Record `runId` and check with `getRunResult()`. Do not run it again immediately. |
| A CLI update or publish fails | Check the `definition` in `--file` and the latest token. |

For an inquiry, record the Workflow ID, run ID, SDK or CLI version, execution mode, date and time, status, and error. Do not attach API keys, Secrets, authentication headers, or business data.

## Checklist

- [ ] I use Node.js 22 or later and SDK 0.11.0, and selected an execution mode.
- [ ] I checked the published-status filter and pagination of `list()`.
- [ ] I retrieved the current definition with `get()` and based changes on the retrieved result.
- [ ] I passed the latest `draftUpdatedAt` when saving a draft and publishing.
- [ ] After publishing, I checked `unregisteredTriggers` and the actual usage path.
- [ ] I recorded `runId` and check status with `getRunResult()` while it is `RUNNING`.
- [ ] For partial success, failure, or a stop, I check for duplicate processing before deciding whether to run again.

## Next article

- [Publish a Workflow](../build/publish-workflow.md)
- [Workflow and Automation Specifications and Limits](../build/workflow-and-automation-specs.md)
- [Workflows API](../api/workflows-api.md)
