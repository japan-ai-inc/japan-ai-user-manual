# Workflows API

The Workflows API is a REST API for developers to retrieve and create Workflows in a Project, save drafts, publish, run, and query run results.

## Related articles

- [SDK Workflows](../development/sdk-workflows.md)
- [CLI Workflows](../cli/cli-workflows.md)
- [Workflow and Automation Specifications and Limits](../build/workflow-and-automation-specs.md)

## Before you begin

The base URL is `https://api.japan-ai.co.jp`, and the common path is `/api/v1/workflows`. Pass the Project API key in the `x-api-key` header. Use a key for the target Project that has the scope required for each operation. Do not leave keys in browsers, source control, logs, or shared history.

```bash
export JAI_STUDIO_API_KEY='value retrieved from secure storage'
curl -sS 'https://api.japan-ai.co.jp/api/v1/workflows?status=all' \
  -H "x-api-key: $JAI_STUDIO_API_KEY"
```

Successful responses include `success` and `requestId`. A missing or invalid key returns 401, and insufficient scope or permissions returns 403. For 429 and wait information, see [Common API and SDK Errors, Timeouts, and Limits](../development/api-sdk-errors-timeouts-limits.md).

> Writes and runs change state. Verify in a test Project, and on timeout, check `runId` or the current definition before retrying.

## Endpoint list

| Operation | Method and path | Required scope |
|---|---|---|
| List | `GET /api/v1/workflows` | `workflow:read` |
| Details | `GET /api/v1/workflows/{workflowId}` | `workflow:read` |
| Create | `POST /api/v1/workflows` | `workflow:write` |
| Update metadata | `PATCH /api/v1/workflows/{workflowId}` | `workflow:write` |
| Save draft | `PUT /api/v1/workflows/{workflowId}/draft` | `workflow:write` |
| Publish | `POST /api/v1/workflows/{workflowId}/publish` | `workflow:write` |
| Delete | `DELETE /api/v1/workflows/{workflowId}` | `workflow:delete` |
| Start a run | `POST /api/v1/workflows/{workflowId}/run` | `workflow:execute` |
| Wait for a run | `POST /api/v1/workflows/{workflowId}/run-and-wait` | `workflow:execute` |
| Run result | `GET /api/v1/workflows/runs/{runId}` | `workflow:read` |

`workflowId` is the target Workflow ID, and `runId` is a UUID-format run ID. A target not in the Project returns 404. Writing to or deleting through a read-only link may return 403 or 409.

## 1. Retrieve a list or details

`GET /api/v1/workflows` returns active Workflows using a cursor. The default `status` is `published`; `draft` is a draft with no published snapshot, and `all` includes both.

| Parameter | Location | Required | Default and limits | Meaning |
|---|---|---:|---|---|
| `limit` | query | No | 50, 1-100 | Items per page |
| `cursor` | query | No | None | The previous `nextCursor` |
| `status` | query | No | `published` | `published`, `draft`, or `all` |

The response includes `workflows`, `totalCount`, and `nextCursor`. Each Workflow has `id`, `name`, `description`, `enabled`, `isPublished`, `createdAt`, and `updatedAt`. `GET /{workflowId}` also returns `definition`. A definition has `graph`, `features`, `source`, and `draftUpdatedAt`, or is `null` if it does not exist.

```bash
curl -sS 'https://api.japan-ai.co.jp/api/v1/workflows?limit=50&status=all' \
  -H "x-api-key: $JAI_STUDIO_API_KEY"
```

## 2. Create and update metadata

`POST /api/v1/workflows` creates an unpublished Workflow with an empty draft. `name` is required (1-255 characters), `description` is optional, and `enabled` defaults to `true`. On success, it returns `workflow` with 201.

`PATCH /{workflowId}` accepts one or more of `name`, `description`, and `enabled`. On success, it returns the updated `workflow` with 200. When `enabled: false`, email and Storage triggers are disabled.

```bash
curl -sS -X POST 'https://api.japan-ai.co.jp/api/v1/workflows' \
  -H "x-api-key: $JAI_STUDIO_API_KEY" -H 'content-type: application/json' \
  --data '{"name":"Invoice intake","enabled":false}'
```

## 3. Save and publish a draft

Use `PUT /{workflowId}/draft` to save a draft and `POST /{workflowId}/publish` to publish it. Send `graph` (arrays of `nodes` and `edges`) and `features`. The graph body can be up to 16 MB. Saving does not publish. Publishing makes the definition a live `v1` snapshot and attempts to register triggers.

| Parameter | Location | Required | Meaning |
|---|---|---:|---|
| `graph.nodes` | body | Yes | Array of nodes |
| `graph.edges` | body | Yes | Array of connections |
| `features` | body | Yes | Workflow feature configuration |
| `expectedDraftUpdatedAt` | body | No | Concurrent-update token: return the value from `GET` or a successful save unchanged |

A successful save returns `snapshotId` and `draftUpdatedAt` for the next operation. Pass `expectedDraftUpdatedAt`; if another person changes it after retrieval, the response is 409. If omitted, the last write takes precedence. Use the `draftUpdatedAt` from the save response for the next save or publish.

A successful publish returns `snapshot` and `unregisteredTriggers`. `snapshot` has `id`, `version`, `createdAt`, and `updatedAt`. Publishing can succeed even if the latter is not empty, so check `type`, `nodeId`, and `reason`. Definition conflicts, release management, and read-only links may return 409.

> Saving a draft alone does not update the published version used by Workflow triggers or Task Scheduler. Make changes, test, publish, and then verify the actual use route in that order. For automatic execution, see [Workflow and Automation Specifications and Limits](../build/workflow-and-automation-specs.md).

## 4. Delete

`DELETE /{workflowId}` returns 204 on success and does not return a body. Check the ID and impact before deleting. A Workflow linked externally cannot be deleted through this route and may return 409.

## 5. Start a run and check the result

The body for `POST /{workflowId}/run` is optional `inputs`. When omitted, it is an empty object. On success, it returns `runId` and `status` with 200. Only published and enabled Workflows can run. Unpublished or disabled Workflows return 403, input or definition validation errors return 400, and a missing target returns 404. On 408, use `runId` to query the result if it is available.

```bash
curl -sS -X POST 'https://api.japan-ai.co.jp/api/v1/workflows/<workflowId>/run' \
  -H "x-api-key: $JAI_STUDIO_API_KEY" -H 'content-type: application/json' \
  --data '{"inputs":{"query":"Check"}}'

curl -sS 'https://api.japan-ai.co.jp/api/v1/workflows/runs/<runId>' \
  -H "x-api-key: $JAI_STUDIO_API_KEY"
```

`GET /runs/{runId}` returns `runId`, `status`, and, as applicable, `outputs` and `error`. Statuses are `RUNNING`, `SUCCEEDED`, `FAILED`, `PARTIAL_SUCCEEDED`, and `STOPPED`. Query the same ID while it is `RUNNING`. An invalid ID returns 400, a missing run returns 404, and a query timeout returns 408.

To wait for completion, use `POST /{workflowId}/run-and-wait`. `inputs` is optional. `timeout` is in milliseconds, defaults to 60,000, and is 1,000-300,000. This is the timeout including the request through the server-side query. The SDK and CLI cap it at 270 seconds to allow time for communication. The maximum when calling the REST API directly is 300 seconds. On 408, switch to querying the result if `runId` is available.

## 6. When managing scheduled Tasks

Creating, publishing, and manually running Workflows are separate from managing scheduled Tasks. In SDK 0.11.0, `scheduledTasks` and `jai-studio scheduled-tasks` can list, create, update, and delete scheduled Tasks for Workflows created by the Project. Tasks run as the creator of the Project API key.

| Operation | Method and path | Required scope |
|---|---|---|
| List | `GET /api/v1/scheduled-tasks` | `task:read` |
| Create | `POST /api/v1/scheduled-tasks` | `task:write`, `workflow:execute` |
| Update | `PATCH /api/v1/scheduled-tasks/{taskId}` | `task:write`, `workflow:execute` |
| Delete | `DELETE /api/v1/scheduled-tasks/{taskId}` | `task:delete` |

Creation requires `name`, `workflowId`, and an ISO 8601 `triggerTime` with an offset. For repetition, specify `repeatPattern` or `cronSchedule`, and specify `scheduleTimezone` when necessary. Do not retry a create or update whose state is unknown; retrieve the list by name to confirm it. You cannot declare `task:*` scopes in an App release manifest. For detailed input formats, stopping, and handover, see [SDK Workflows](../development/sdk-workflows.md) and [Change, Stop, and Hand Over Scheduled Runs](../operations/change-stop-handover-scheduled-runs.md).

## Common misconceptions

### A successful `run` means the business process is complete

`run` can return `RUNNING`. Use `GET /runs/{runId}` to check the terminal status and, if necessary, outputs and external delivery results. Carelessly running again after partial success, failure, or a stop can duplicate writes or notifications.

### You can process `draftUpdatedAt` as a date and time

This is a concurrent-update check value. Do not parse, generate, or round it. Pass the retrieved string or the string from the save response unchanged to `expectedDraftUpdatedAt`. On 409, retrieve the definition again, review the differences, and then save or publish again.

## Troubleshooting

| Situation | What to check and do |
|---|---|
| 401 or 403 | Check the key, Project, scope, and the key creator's access. |
| Cannot save or publish with 409 | Retrieve the latest definition and `draftUpdatedAt` with `GET /{workflowId}`. |
| Does not start after publishing | Check `unregisteredTriggers`, publication status, `enabled`, and connections. |
| 408 or communication failure | Query `runId` and the run result first; do not retry before confirming. |
| 429 | Follow the response wait information, and reduce concurrency and retry frequency. |

When contacting support, record the Project, Workflow ID, run ID, time, HTTP status, `requestId`, and safe error details. Do not send keys, authentication headers, Webhook Secrets, or business inputs.

## Checklist

- [ ] Securely configured a minimum-scope API key for the target Project
- [ ] Checked the list `status` and `nextCursor`, and the detail `definition`
- [ ] Passed the latest `draftUpdatedAt` unchanged when saving or publishing a draft
- [ ] Checked `unregisteredTriggers` and the actual start route after publishing
- [ ] Recorded `runId` and queried the result through a terminal status
- [ ] Did not retry a run after a timeout or failure without checking its state
- [ ] When using scheduled runs, checked `task:*` scopes, the key creator, the next run, and the stopping method

## Next article

- [SDK Workflows](../development/sdk-workflows.md)
- [CLI Workflows](../cli/cli-workflows.md)
- [Workflow and Automation Specifications and Limits](../build/workflow-and-automation-specs.md)
