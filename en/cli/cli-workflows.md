# CLI Workflows

`jai-studio workflows` is a developer CLI for retrieving, saving, publishing, and running Workflows from a terminal or CI. For SDK implementation, see [SDK Workflows](../development/sdk-workflows.md); for authentication and profiles, see [CLI Configuration and Profiles](cli-config-and-profiles.md).

## Related articles

- [SDK Workflows](../development/sdk-workflows.md)
- [Publish a Workflow](../build/publish-workflow.md)
- [Workflow and Automation Specifications and Limits](../build/workflow-and-automation-specs.md)
- [CLI Configuration and Profiles](cli-config-and-profiles.md)

## Before you begin

Use SDK package `@japan-ai-inc/studio-sdk` 0.11.0 and Node.js 22 or later. Credentials have priority in this order: global options, environment variables, and profiles. Because `--key` may remain in history or a process list, normally use a profile or `JAI_STUDIO_API_KEY`.

```text
jai-studio --profile <profile> workflows list
```

Notation containing `<...>` below is **syntax that requires substitution**. Do not paste it into the shell with the angle brackets; replace it with an actual value.

| Global option | Required | Meaning |
|---|---:|---|
| `--profile <name>` | No | Specifies a profile. |
| `--env <env>` | No | `production` or a custom URL. |
| `--key <key>` | No | Temporarily overrides the Project API key. |
| `--pretty` | No | Formats successful JSON. |
| `--debug` | No | Displays server error details. Do not use it in shared logs. |

Successful results are output as JSON to standard output. There is no `--json` option. On failure, JSON errors are output to standard error and the exit code is 1.

> `publish`, `delete`, `run`, and `run-and-wait` may cause external sending or data updates. Before running them, check the Project, ID, definition, and input. Do not leave API keys, Webhook Secrets, or business data in commands, definitions, or CI logs.

## List, retrieve, create, and update

### `list`

```text
jai-studio workflows list [--cursor <cursor>] [--limit <n>] [--status <status>]
```

| Argument or option | Required | Default | Meaning |
|---|---:|---|---|
| `--cursor <cursor>` | No | None | Cursor for retrieving the next page. |
| `--limit <n>` | No | API default | Maximum items per page. |
| `--status <status>` | No | `published` | `published`, `draft`, or `all`. `draft` is unpublished; `all` includes both. |

```bash
jai-studio workflows list --status all --pretty
```

### `get`

```text
jai-studio workflows get <workflowId>
```

| Argument or option | Required | Default | Meaning |
|---|---:|---|---|
| `<workflowId>` | Yes | — | ID of the Workflow to retrieve. |

When retrieving `definition` and writing it to a file, save `definition`, not the entire object.

```text
jai-studio workflows get <workflowId> | jq .definition > definition.json
```

### `create`

```text
jai-studio workflows create --name <name> [--description <desc>] [--disabled]
```

| Argument or option | Required | Default | Meaning |
|---|---:|---|---|
| `--name <name>` | Yes | — | Workflow name. It is created as an empty draft. |
| `--description <desc>` | No | None | Description. |
| `--disabled` | No | Enabled | Creates it disabled. |

```bash
jai-studio workflows create --name "Invoice intake" --disabled
```

### `update`

```text
jai-studio workflows update <workflowId> [--name <name>] [--description <desc>] [--enable | --disable]
```

| Argument or option | Required | Default | Meaning |
|---|---:|---|---|
| `<workflowId>` | Yes | — | ID of the Workflow to update. |
| `--name <name>` | No | Unchanged | Updates the name. |
| `--description <desc>` | No | Unchanged | Updates the description. |
| `--enable` / `--disable` | No | Unchanged | Switches between enabled and disabled. They cannot be specified together. `--disable` disables triggers. |

At least one update option is required.

```text
jai-studio workflows update <workflowId> --description "For validation" --disable
```

## Drafts, publishing, and deletion

### `save-draft` and `publish`

```text
jai-studio workflows save-draft <workflowId> --file <path>
jai-studio workflows publish <workflowId> --file <path>
```

| Argument or option | Required | Default | Meaning |
|---|---:|---|---|
| `<workflowId>` | Yes | — | Target Workflow ID. |
| `--file <path>` | Yes | — | JSON file created from `definition` returned by `get`. |

The file must be a JSON object: `graph` is required and `features` is optional. `draftUpdatedAt` is sent as a concurrency check value. If `expectedDraftUpdatedAt` is explicitly provided, it takes precedence. Other keys (for example, `source`) are not sent. Invalid tokens cause an error.

`save-draft` does not publish. After success, apply the new `draftUpdatedAt` or run `get` again before `publish`. `publish` publishes the specified definition and attempts to register triggers.

```text
jai-studio workflows save-draft <workflowId> --file ./definition.json
jai-studio workflows publish <workflowId> --file ./definition.json
```

### `delete`

```text
jai-studio workflows delete <workflowId>
```

| Argument or option | Required | Default | Meaning |
|---|---:|---|---|
| `<workflowId>` | Yes | — | ID of the Workflow to delete. |

After deletion, the CLI returns a deletion message as JSON. Check the ID and impact before running it.

```text
jai-studio workflows delete <workflowId>
```

## Runs and run results

### `run`

```text
jai-studio workflows run <workflowId> [--input <json>]
```

| Argument or option | Required | Default | Meaning |
|---|---:|---|---|
| `<workflowId>` | Yes | — | ID of the Workflow to run. |
| `--input <json>` | No | Empty input | JSON string for Workflow input. |

```text
jai-studio workflows run <workflowId> --input '{"documentUrl":"https://example.invalid/input.pdf"}'
```

If you have a `runId`, use `status` to retrieve its state and output. Check an already retrieved `runId` before resending.

### `run-and-wait`

```text
jai-studio workflows run-and-wait <workflowId> [--input <json>] [--timeout <seconds>]
```

| Argument or option | Required | Default | Meaning |
|---|---:|---|---|
| `<workflowId>` | Yes | — | ID of the Workflow to run. |
| `--input <json>` | No | Empty input | JSON string to pass as Workflow input. |
| `--timeout <seconds>` | No | 60 | Upper limit for waiting for completion. Specify an integer from 1 to 270 seconds. |

Messages while waiting and on completion go to standard error; result JSON goes to standard output.

```text
jai-studio workflows run-and-wait <workflowId> --input '{"query":"Check"}' --timeout 60
```

### `status` (run result)

```text
jai-studio workflows status <runId>
```

| Argument or option | Required | Default | Meaning |
|---|---:|---|---|
| `<runId>` | Yes | — | Run ID returned by `run` or another command. |

```text
jai-studio workflows status <runId> --pretty
```

When the status is `RUNNING`, continue retrieving with the same `runId` until it completes. Distinguish `PARTIAL_SUCCEEDED`, `FAILED`, and `STOPPED` as well as `SUCCEEDED`; check the output, errors, and whether external processing has already run before deciding to run it again.

## Manage scheduled Workflow runs

In SDK 0.11.0, `scheduled-tasks` manages Project-created Workflows as scheduled runs. Each task runs as the creator of the Project API key. `list` requires `task:read`; `create` and `update` require `task:write` and `workflow:execute`; `delete` requires `task:delete`. Linked organization Workflows are outside the scope.

```text
jai-studio scheduled-tasks list --search "Weekly"
jai-studio scheduled-tasks create --file ./task.json
jai-studio scheduled-tasks update <taskId> --file ./patch.json
jai-studio scheduled-tasks delete <taskId>
```

The create JSON requires `name`, `workflowId`, and `triggerTime`. For repetition, specify `repeatPattern` or `cronSchedule` in `schedule`; omitting it creates a one-time run. Creation is not idempotent, so check the list by name before creating and before retrying after a 5xx response. Updates change only specified fields, and `inputs` merge by key.

## Troubleshooting

| Situation | Check or response |
|---|---|
| The target is not in the list | Check `--status all`, the Project, and `--cursor`. |
| `--file` fails | Check `graph`, the saved `.definition`, and the latest `draftUpdatedAt`. |
| Saving or publishing conflicts | Retrieve the definition again with `get` and use the latest `draftUpdatedAt`. |
| Input JSON or timeout is invalid | Check quotes, JSON, and that `--timeout` is an integer from 1 to 270. |
| Error details are insufficient | Record the target ID, CLI version, time, and error JSON. Only when necessary, use `--debug` in a safe environment. |

## Checklist

- [ ] I checked the CLI, Node.js, and target Project.
- [ ] I authenticated with a profile or secure environment variable and did not leave the key in command arguments or logs.
- [ ] I created the draft and publishing file from `definition` returned by `get`.
- [ ] I used the latest `draftUpdatedAt` after saving.
- [ ] I checked triggers and the actual user path after publishing.
- [ ] I recorded the run ID, avoided duplicate runs, and checked results.

## Next articles to read

- [SDK Workflows](../development/sdk-workflows.md)
- [Publish a Workflow](../build/publish-workflow.md)
- [Workflow and Automation Specifications and Limits](../build/workflow-and-automation-specs.md)
- [CLI Configuration and Profiles](cli-config-and-profiles.md)
