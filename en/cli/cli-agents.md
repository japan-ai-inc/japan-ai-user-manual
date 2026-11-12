# CLI Agents

`jai-studio agents` is a CLI for listing and retrieving Agent details, single invocations, creating and reading conversations (rooms), and reading Canvas documents. This article explains available commands.

## Related articles

- [CLI Configuration and Profiles](cli-config-and-profiles.md)
- [SDK Agents: Invocation and Streaming](../development/sdk-agents-invoke-and-stream.md)
- [SDK Agents: Conversation History and Canvas](../development/sdk-agents-history-and-canvas.md)
- [Manage Project API Keys](../operations/manage-project-api-keys.md)

## Before you begin

Install Node.js 22 or later and `@japan-ai-inc/studio-sdk` 0.11.0, then prepare a CLI profile or API key for connection. For profiles, endpoints, and the priority order of keys, see [CLI Configuration and Profiles](cli-config-and-profiles.md).

On success, JSON is output to standard output. Add `--pretty` to format it. On failure, JSON containing `error`, `statusCode`, and `details` is output to standard error, and the command exits with code 1. Use `--debug` only in a safe investigation environment.

> Prompts to an Agent, conversation history, and Canvas content may include confidential information. Review output before sharing it. Avoid writing an API key directly in `--key`.

## Common flags and scope

| Flag | Default | Meaning |
|---|---|---|
| `--profile <name>` | None | Selects the profile to use for this run. |
| `--env <env>` | None | Overrides the endpoint. |
| `--key <key>` | None | Overrides the API key. Do not normally use it because it remains in history. |
| `--pretty` | Disabled | Formats successful JSON. |
| `--debug` | Disabled | Displays detailed error responses. |

There is no global `--json`. Do not pass a flag specific to `pages deploy` to `agents`. The CLI also has no flags for stream output, file attachments, Storage file attachments, or timeouts. If you need these, use the SDK described in [SDK Agents: Invocation and Streaming](../development/sdk-agents-invoke-and-stream.md).

## 1. Find an Agent and check its details

### List: `agents list`

```text
jai-studio [common flags] agents list [--cursor <cursor>] [--limit <n>] [--search <query>]
```

Notation containing `<...>` below is **syntax that requires substitution**. Do not paste it into the shell with the angle brackets; replace it with an actual value.

| Argument or flag | Required | Default | Meaning |
|---|---:|---|---|
| `--cursor <cursor>` | No | None | Pass the preceding `nextCursor` to retrieve the next page. |
| `--limit <n>` | No | Not specified by the CLI | Specify the maximum items per page as a number. |
| `--search <query>` | No | None | Searches by Agent name. |

```bash
jai-studio agents list --search "summary" --limit 20 --pretty
```

The result includes `agents`, `totalCount`, and `nextCursor`. Check each Agent's `id` and other fields, and pass the returned `nextCursor` unchanged only when you need the next page. `--limit` is rejected unless it is in a safe integer format. The available maximum follows the endpoint response.

### Details: `agents get`

```text
jai-studio [common flags] agents get <agentId>
```

| Argument | Required | Meaning |
|---|---:|---|
| `<agentId>` | Yes | Identifier of the Agent to retrieve. |

```bash
jai-studio agents get 550e8400-e29b-41d4-a716-446655440000 --pretty
```

## 2. Create, retrieve, and update Agent definitions

In SDK 0.11.0, you can manage Agent definitions from the CLI with a Project API key that has the `agent:write` scope. The key creator must also have permission for the target operation. Creation is not idempotent, so check `agents list --search` before creating one with the same name.

```text
jai-studio agents create --file ./agent.json
jai-studio agents definition <agentId> > ./agent.json
jai-studio agents update <agentId> --file ./agent.json
```

Creation JSON requires `name`, `prompt`, and `model`. Specify `description`, `openingStatement`, `modelMode`, `datasets`, `tools`, `maxOutputTokens`, `reasoningEffort`, and `temperature` as needed. `definition` outputs only definitions that can be passed to an update as JSON. In an update, nonexistent fields remain unchanged, arrays are replaced, and `null` clears that field. You cannot specify datasets outside the key's Project, or tools, Skills, and models that are not presented to the creator.

## 3. Run an Agent and continue a conversation

### Run: `agents invoke`

```text
jai-studio [common flags] agents invoke <agentId> --prompt <text> [--session-id <id>] [--model <model>] [--temperature <temp>] [--system-prompt <text>]
```

| Argument or flag | Required | Default | Meaning |
|---|---:|---|---|
| `<agentId>` | Yes | — | Identifier of the Agent to run. |
| `--prompt <text>` | Yes | — | Prompt to pass to the Agent. |
| `--session-id <id>` | No | None | Room ID for continuing a conversation. |
| `--model <model>` | No | None | Overrides the model specification. |
| `--temperature <temp>` | No | None | Overrides temperature with a number. |
| `--system-prompt <text>` | No | None | Overrides the system prompt. |

```bash
jai-studio agents invoke 550e8400-e29b-41d4-a716-446655440000 \
  --prompt "Summarize this text in three points." --pretty
```

This is a non-streaming run that outputs JSON after completion. Check `status`, `sessionId`, and `text` in the result. If `status` is `failed`, also check `errorMessage`. Also check images, files, and references. Because URLs may expire, do not persist or publish output as-is.

Passing `sessionId` to `--session-id` on the next run continues the conversation.

```bash
jai-studio agents invoke 550e8400-e29b-41d4-a716-446655440000 \
  --session-id "previous sessionId" --prompt "Explain the second point in more detail."
```

`--temperature` is converted to a number. Detailed validation for nonnumeric values is delegated to the endpoint. The CLI does not provide incremental display, cancellation, or attachment specification. Use the SDK if required.

## 4. Create rooms and retrieve message history

### Create a room: `agents rooms`

```text
jai-studio [common flags] agents rooms <agentId>
```

| Argument | Required | Meaning |
|---|---:|---|
| `<agentId>` | Yes | Identifier of the Agent for which to create a room. |

```bash
jai-studio agents rooms 550e8400-e29b-41d4-a716-446655440000
```

Use `roomId` in a successful result for subsequent runs and reads. There is no CLI command to retrieve a list of existing rooms.

### Read messages: `agents messages`

```text
jai-studio [common flags] agents messages <roomId> [--cursor <cursor>] [--limit <n>]
```

| Argument or flag | Required | Default | Meaning |
|---|---:|---|---|
| `<roomId>` | Yes | — | Identifier of the room to read. |
| `--cursor <cursor>` | No | None | Pass the preceding result's `nextCursor`. |
| `--limit <n>` | No | Not specified by the CLI | Specify the maximum items per page as a number. |

The result contains `roomId`, `messages`, `totalCount`, and `nextCursor`. Repeat until `nextCursor` becomes `null`, and prevent duplicate display by using retrieved IDs.

## 5. List and retrieve Canvas documents

### List: `agents canvas list`

```text
jai-studio [common flags] agents canvas list <roomId> [--cursor <cursor>] [--limit <n>]
```

`<roomId>` is required. `--cursor` and `--limit` have the same meaning as for `agents messages`. `canvasArtifacts` in the result contains metadata only and does not include content in `content`. Choose the target using `id`, `name`, `documentType`, `contentSizeBytes`, `createdAt`, and `updatedAt`.

### Content: `agents canvas get`

```text
jai-studio [common flags] agents canvas get <roomId> <canvasArtifactId>
```

| Argument | Required | Meaning |
|---|---:|---|
| `<roomId>` | Yes | Canvas room ID. |
| `<canvasArtifactId>` | Yes | Canvas ID obtained from the list. |

`canvas get` returns one item containing saved `content` and `contentHash`. You cannot retrieve an ID that belongs to a different room. Do not display or execute Canvas HTML or JSON in a browser without validating it. For details, see [SDK Agents: Conversation History and Canvas](../development/sdk-agents-history-and-canvas.md).

## Common misconceptions

- **You can run streaming through the CLI**: You cannot. `agents invoke` outputs the result after completion. Use the SDK's `invokeStream()` for incremental display.
- **You can attach any file through the CLI**: You cannot. No attachment-related flags are registered. Consider SDK `files` or `storageFileIds` in an applicable execution environment.
- **`sessionId` and room ID are different**: You can treat the `sessionId` used for continued runs as the room ID. Use the value obtained from a successful result.
- **The Canvas list contains content**: It does not. Always select an ID with `canvas list` and retrieve each item with `canvas get`.

## Troubleshooting

| Situation | Check or response |
|---|---|
| Connection or authentication fails | Check the profile, endpoint, target Project, key expiration, and scopes according to [CLI Configuration and Profiles](cli-config-and-profiles.md) and [Manage Project API Keys](../operations/manage-project-api-keys.md). Do not output actual values to logs. |
| `--limit` fails | Specify only an integer and remove unnecessary leading or trailing characters. Do not construct a `--cursor` other than the preceding `nextCursor`. |
| A conversation does not continue | Check that you pass the preceding successful result's `sessionId` to `--session-id` for the same Agent. |
| You cannot retrieve history or Canvas | Check the room ID, Canvas ID, target Project, and authentication path. Do not try another room ID to explore content. |
| You want detailed errors | Add `--debug` only in a safe environment that will not be shared. Before recording output, confirm that it contains no secrets. |

## Checklist

- [ ] I checked `status`, `sessionId`, `text`, and, when required, `errorMessage` from `agents invoke`.
- [ ] For continued conversations, I saved `sessionId` from a successful result and passed it to `--session-id`.
- [ ] For pagination, I used the returned `nextCursor`.
- [ ] I did not display or execute Canvas content without validating it.

## Next articles to read

- [CLI Configuration and Profiles](cli-config-and-profiles.md)
- [SDK Agents: Invocation and Streaming](../development/sdk-agents-invoke-and-stream.md)
- [SDK Agents: Conversation History and Canvas](../development/sdk-agents-history-and-canvas.md)
- [Manage Project API Keys](../operations/manage-project-api-keys.md)
