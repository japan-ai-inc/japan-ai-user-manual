# Agents, Conversations, and Artifacts API

Use the JAPAN AI Studio public API to work with Agents, conversation rooms, and Canvas artifacts. Use `/chat/v2` for execution. For the SDK, see [SDK Agents: Invocation and Streaming](../development/sdk-agents-invoke-and-stream.md) and [SDK Agents: Conversation History and Canvas](../development/sdk-agents-history-and-canvas.md).

## Related articles

- [SDK Agents: Invocation and Streaming](../development/sdk-agents-invoke-and-stream.md)
- [SDK Agents: Conversation History and Canvas](../development/sdk-agents-history-and-canvas.md)

## Scope of this article

The production API base URL is `https://api.japan-ai.co.jp`. Pass a Project API key in `x-api-key` or `Authorization: Bearer <key>`, and store it only server-side. Reading requires `agent:read`; creating rooms and executing with a Project API key require `agent:execute`.

> A Project API key is treated as its creator. A shared key cannot view private conversations separately by user. In the browser, do not use an API key; use the SDK Page runtime environment authentication route.

```sh
export JAI_STUDIO_API_KEY='pak_...'
export JAI_API='https://api.japan-ai.co.jp'
```

## 1. Search for and retrieve Agents

| Method and path | Required scope | Parameters | Main successful response | Status |
|---|---|---|---|---|
| `GET /api/v1/agents` | `agent:read` | `limit`: optional, 1-100, default 50 / `cursor`: optional / `search`: optional | `agents`, `totalCount`, `nextCursor` | 200, 400, 401, 403 |
| `GET /api/v1/agents/{agentId}` | `agent:read` | `agentId`: path, required | `agent` | 200, 401, 403, 404 |

`search` searches `name`, `label`, and `description`. `agent` includes `id`, `name`, `label`, `description`, `icon`, `enabled`, `createdAt`, and `updatedAt`. Pass the response `nextCursor` unchanged as `cursor`.

```sh
curl -sS "$JAI_API/api/v1/agents?limit=50&search=%E5%96%B6%E6%A5%AD" \
  -H "x-api-key: $JAI_STUDIO_API_KEY"
```

## 2. Create and update Agent definitions

With a Project API key, you can create, retrieve, and update Agent definitions in addition to listing and retrieving details. All require `agent:write`, and the key creator must also have permission to create or update the target Agent.

| Operation | Method and path | Main body and response |
|---|---|---|
| Create | `POST /api/v1/agents` | Required `name`, `prompt`, `model`. Optional `description`, `openingStatement`, `modelMode`, `datasets`, `tools`, `maxOutputTokens`, `reasoningEffort`, and `temperature`. 201. |
| Retrieve definition | `GET /api/v1/agents/{agentId}/definition` | Returns a definition that can be passed to an update. |
| Update | `PATCH /api/v1/agents/{agentId}` | Retains unspecified fields. Completely replaces arrays, and `null` clears the corresponding field. |

`datasets` and `tools` can each have up to 200 items. Before associating a Dataset with an Agent, check the Dataset in the target Project and whether it is available. Because reading a definition also requires `agent:write`, do not use it as a substitute for permission to merely read conversations.

```sh
curl -sS -X POST "$JAI_API/api/v1/agents" \
  -H "x-api-key: $JAI_STUDIO_API_KEY" -H 'Content-Type: application/json' \
  -d '{"name":"Proposal organization (for validation)","prompt":"Organize the input into three points","model":"available model name"}'
```

## 3. Create a room and run synchronously

To create a conversation first, call the following.

| Method and path | Required scope | Parameters | Main successful response | Status |
|---|---|---|---|---|
| `POST /api/v1/agents/{agentId}/rooms` | `agent:execute` | `agentId`: path, required | `roomId`, `agentId`, `createdAt` | 201, 401, 403, 404 |

Pass the returned `roomId` as `sessionId` to continue the conversation. Synchronous execution uses `POST /chat/v2`. You can pass an Agent UUID in `agentName`. `prompt` is required, and specify `stream: false`.

| Method and path | Required scope | Main body parameters | Main successful response | Status |
|---|---|---|---|---|
| `POST /chat/v2` | `agent:execute` | `prompt`: required. `agentName`, `stream`, `sessionId`, `model`, `temperature`, `systemPrompt`, `skipHistory`, `is_ephemeral`, and `enable_handoff_long_run_job` are optional | `chatMessage`, `sessionId`. As applicable, `references`, image information, and file information | 201, 400, 401, 403, 409, 429, 500 |

```sh
curl -sS -X POST "$JAI_API/chat/v2" \
  -H "x-api-key: $JAI_STUDIO_API_KEY" \
  -H 'Content-Type: application/json' \
  -d '{"agentName":"550e8400-e29b-41d4-a716-446655440000","prompt":"Show the proposal in three points","stream":false}'
```

Use `sessionId` after checking the response content and errors. You cannot assume that an `is_ephemeral: true` run supports history reading or continued conversations.

### Attached files

When attaching files, send them in `files` as `multipart/form-data`. Include body fields in the same form. A request can include up to 10 files, and each file can be up to 100 MB. Do not display or execute unverified content as-is.

## 4. Receive output with SSE

Streaming also uses `POST /chat/v2`. Set body `stream` to `true` and specify `Accept: text/event-stream`. On success, the HTTP status is 200 and `Content-Type` is `text/event-stream`. Process SSE `data:` lines in order, and use `data: [DONE]` to determine the end. Do not discard an empty `delta`, because it may contain an image, file, or `finishReason`. If you receive `type: "error"`, do not finalize the body as a completed result. A 409 may be returned when the same `requestId` is being processed, so do not resend without checking.

## 5. Read room history and Canvas artifacts

| Method and path | Required scope | Parameters | Main successful response | Status |
|---|---|---|---|---|
| `GET /api/v1/agents/rooms/{roomId}/messages` | `agent:read` | `roomId`: path, required / `limit`: optional, 1-100, default 50 / `cursor`: optional | `roomId`, `messages`, `totalCount`, `nextCursor`, `state` | 200, 400, 401, 403, 404 |
| `GET /api/v1/agents/rooms/{roomId}/canvas-artifacts` | `agent:read` | `roomId`: path, required / `limit`: optional, 1-100, default 50 / `cursor`: optional | `roomId`, `canvasArtifacts`, `nextCursor` | 200, 400, 401, 403, 404 |
| `GET /api/v1/agents/rooms/{roomId}/canvas-artifacts/{canvasArtifactId}` | `agent:read` | `roomId`, `canvasArtifactId`: path, required | `canvasArtifact` | 200, 401, 403, 404 |
| `PUT /api/v1/agents/rooms/{roomId}/canvas-artifacts/{canvasArtifactId}` | `agent:execute` | `roomId`, `canvasArtifactId`: path, required / body: `name`, `content` | Created or replaced Canvas | 200, 400, 403, 404, 409, 413 |
| `PUT /api/v1/agents/rooms/{roomId}/canvas-artifacts/{canvasArtifactId}/publication/{scope}` | `agent:execute` | `roomId`, `canvasArtifactId`, `scope`: path, required | Publication result | 200, 403, 404, 409 |
| `DELETE /api/v1/agents/rooms/{roomId}/canvas-artifacts/{canvasArtifactId}/publication` | `agent:execute` | `roomId`, `canvasArtifactId`: path, required | Result of stopping publication | 200, 403, 404 |

Messages include `id`, `role`, `content`, `createdAt`, and `feedback`. New messages can be added during pagination, so remove duplicates by `id`. A room that cannot be read from the target Project can also return 404.

The Canvas list does not return content; it returns `id`, `name`, `documentType`, `contentSizeBytes`, and creation and update dates. Retrieve only the necessary items individually. The `content` in an individual response is JSON at save time; in older formats, `documentType` may be `null` and be `{ "text": ... }`. The `content.text` of HTML slides is a JSON string, and HTML and `room://` references are not resolved to URLs.

> Do not set content originating from Canvas JSON, HTML, or attachments to `innerHTML` without validation. Implement sanitization, isolation, and a Content Security Policy.

### Write to and publish Canvas with SDK 0.11.0

In SDK 0.11.0, a signed-in Page user can write and publish an hslides-format deck to Canvas in a room created through the public API. `putCanvasArtifact(roomId, canvasArtifactId, { name, content })` creates or replaces Canvas with the specified Canvas ID. `content` is not the HTML string itself; it is in the format `{ document_type: "hslides", text: "..." }`. For a write with an unknown result, check the state using the same ID before proceeding. These writes and publications are for signed-in Page users. Authentication that cannot identify a user, such as a Project API key, returns 403, and an unsupported deployment returns 404.

For publication, use `publishCanvasArtifact(roomId, canvasArtifactId, scope)`, where `scope` is `"PUBLIC"` or `"ORGANIZATION"`. `PUBLIC` is rejected unless public sharing is allowed in the organization, and `ORGANIZATION` is rejected in a public room. `unpublishCanvasArtifact()` returns the Canvas to the room sharing scope. Use compatible versions of the SDK and Page runtime environment, and do not include secrets in Canvas content, links, or attachments.

## Troubleshooting

| Situation | What to check and do |
|---|---|
| 400 | Check required fields, the `limit` range, and JSON or multipart format. Do not automatically correct and resend requests based on guesswork. |
| 401 or 403 | Check key storage location and validity, target Project, and read or execute scope. Do not include the actual key value in logs or support inquiries. |
| 404 | Check the Agent ID, room ID, Canvas ID, target Project, and authentication route. Do not try other IDs to explore existence. |
| 429 | If present, follow `retryAfterSeconds` and `Retry-After`, and reduce concurrency and retry frequency. Do not implement a fixed wait time. |
| Stream interruption or timeout | Do not determine completion or non-execution from the screen alone. Check room history and the business result before resuming. |

A 429 may return `retryAfterSeconds`. For retry and usage control, see [Common API and SDK Errors, Timeouts, and Limits](../development/api-sdk-errors-timeouts-limits.md).

## Checklist

- [ ] Stored the API key only server-side and chose the minimum scopes
- [ ] When changing an Agent definition, checked `agent:write`, the key creator's permissions, and the meanings of complete array replacement and clearing with `null`
- [ ] Created a room or executed using the Agent ID
- [ ] Handled synchronous execution, SSE, and HTTP errors separately
- [ ] Carried forward `nextCursor` and kept `limit` from 1 to 100
- [ ] Retrieved Canvas using the pair of room ID and Canvas ID
- [ ] When writing to or publishing Canvas, checked SDK 0.11.0, the compatible Page runtime environment, and publication scope
- [ ] Did not display or execute attachments, Canvas, or Agent output while unverified
- [ ] Did not automatically retry after 429 or timeout

## Next article

- [SDK Agents: Invocation and Streaming](../development/sdk-agents-invoke-and-stream.md)
- [SDK Agents: Conversation History and Canvas](../development/sdk-agents-history-and-canvas.md)
- [Common API and SDK Errors, Timeouts, and Limits](../development/api-sdk-errors-timeouts-limits.md)
- [Manage Project API Keys](../operations/manage-project-api-keys.md)
