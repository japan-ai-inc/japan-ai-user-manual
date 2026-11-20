# SDK Agents: Conversation History and Canvas

This article explains how to safely handle conversation history and Canvas with the Studio SDK.

For execution, see [SDK Agents: Invocation and Streaming](sdk-agents-invoke-and-stream.md). For the HTTP API, see [Agents, Conversations, and Artifacts API](../api/agents-conversations-artifacts-api.md).

## Related articles

- [SDK Agents: Invocation and Streaming](sdk-agents-invoke-and-stream.md)
- [Execution Modes, Authentication, and Supported Scope](execution-modes-and-auth.md)
- [Manage Project API Keys](../operations/manage-project-api-keys.md)
- [Agents, Conversations, and Artifacts API](../api/agents-conversations-artifacts-api.md)

## Before you begin

- Use `@japan-ai-inc/studio-sdk` **0.11.0** and Node.js **22 or later**. Because the SDK is pre-1.0, pin the complete version.
- Keep API keys only on the server side for local use and Page runtime server processing. In the browser of a Studio-hosted Page (the "Page browser" below), do not pass a key; initialize with `createClient({ baseUrl: "" })`.
- When continuing a conversation, retain the successful response's `sessionId` in secure application state, and specify `isEphemeral: false` in every call.

> A browser application sharing a Project API key cannot provide conversation viewing scope for each user. Do not leave keys, Cookies, authentication headers, or secret information in conversations in logs or browser code.

{% hint style="warning" %}
In the Page browser, calls that do not specify `isEphemeral` are treated as ephemeral conversations and do not read or write history. **To continue a conversation in a Page or read history with `getMessages()`, you must specify `isEphemeral: false`.** Otherwise, the second and later calls have no prior context, and `getMessages()` returns nothing.
{% endhint %}

## 1. Continue a conversation with `sessionId`

Passing the `sessionId` obtained from the first `invoke()` or `invokeStream()` called with `isEphemeral: false` to a subsequent `input.sessionId` continues the conversation. Specify `isEphemeral: false` in subsequent calls as well. If you need to create an empty conversation first, use the `roomId` from `createRoom(agentId)`.

```ts
const first = await studio.agents.invoke(agentId, {
  prompt: "List three key points for the proposal",
  isEphemeral: false, // Required to continue the conversation in the Page browser
});
if (first.status === "failed") throw new Error(first.errorMessage);

const followUp = await studio.agents.invoke(agentId, {
  sessionId: first.sessionId,
  prompt: "Explain the second point in detail with an example",
  isEphemeral: false,
});
console.log(followUp.text);
```

`isEphemeral: true` runs without preserving conversation history. In the Page browser, an omitted value is also treated as `true`. For processing that relies on history reads, conversation continuation, or Canvas retrieval, explicitly specify `isEphemeral: false`. `enableHandoffLongRunJob: true` requires a persistent conversation, and the SDK rejects its use together with `isEphemeral: true`.

## 2. Read message history by page

`getMessages(roomId, query?)` returns `roomId`, `messages`, `totalCount`, and `nextCursor`. Each message has `id`, `role`, `content`, and `createdAt`. `limit` is **1 to 100** and defaults to **50**. Until `nextCursor` is `null`, pass the returned value as the next `cursor`. A conversation run in the Page browser without `isEphemeral: false` is not saved to history, so `getMessages()` returns nothing.

```ts
async function readAllMessages(roomId: string) {
  const history = [];
  let cursor: string | undefined;
  do {
    const page = await studio.agents.getMessages(roomId, { limit: 50, cursor });
    history.push(...page.messages);
    cursor = page.nextCursor ?? undefined;
  } while (cursor);
  return history;
}
```

Do not construct arbitrary `cursor` values; pass only the value from the preceding response. Since new messages can be added during pagination, consider deduplicating by retrieved `id` on the screen. A missing or inaccessible conversation can result in 404, so do not automatically switch to another room ID.

## 3. Retrieve Canvas individually from the list

An Agent using Canvas can save HTML slides, text, code, and other content as Canvas documents in a conversation. `listCanvasArtifacts(roomId, query?)` returns only metadata in oldest-first order; it does not include the body. `limit` and `cursor` are **1 to 100** and default to **50**, as with history.

```ts
const { canvasArtifacts } = await studio.agents.listCanvasArtifacts(roomId, {
  limit: 20,
});
const deck = canvasArtifacts.find(item => item.documentType === "hslides");
if (!deck) throw new Error("This conversation has no slide Canvas");

const artifact = await studio.agents.getCanvasArtifact(roomId, deck.id);
console.log(artifact.name, artifact.contentHash);
```

`getCanvasArtifact(roomId, canvasArtifactId)` returns one item, including saved `content` and `contentHash`. You cannot retrieve an ID from another room. `content` is JSON saved by a write tool; check `documentType` and its actual value before treating it as a fixed type. In the legacy format, `documentType` can be `null` and only `{ text }` may be present.

For HTML slides, `content.text` is a JSON string containing slide definitions. `room://` references in slide HTML or assets are returned without being resolved, so do not load them as external URLs.

> Do not set Canvas `content` or HTML to `innerHTML` without validation. Treat it as data originating from Agent output or attachments, and implement sanitization, isolation, and Content Security Policy as appropriate.

## Read results of response comparison

When response comparison is used in the Page browser (this requires the feature to be enabled for the organization and the viewer to be a signed-in JAPAN AI user), use `getTurns(roomId)` to read saved turns and candidates by page. Candidates are identified by `index`. Because the same model can run at multiple effort levels, do not match candidates by model name.

```ts
const page = await studio.agents.getTurns(roomId, { limit: 20 });
for (const turn of page.turns) {
  for (const candidate of turn.candidates) {
    console.log(candidate.index, candidate.model, candidate.effort, candidate.references);
  }
}
```

`attachments` contains metadata for images and files attached to a turn, and candidate `references` contains Web references corresponding to `[n]` in a response. These may be empty arrays for text-only responses or older connections. If `adoptedIndex` is absent when streaming ends, do not conclude that it is "not adopted"; check again with `getTurns()`, then offer `adoptCandidate()` only for the final turn.

## 4. Write and publish HTML slides from a Studio-hosted Page

From SDK **0.6.0**, a signed-in user can write HTML slides (`hslides`) in an API-created room from a Studio-hosted Page. API key calls cannot be used. Create and retain Canvas IDs at the caller so you do not create duplicates when success of a write is unknown.

```ts
const canvasId = crypto.randomUUID();
await studio.agents.putCanvasArtifact(roomId, canvasId, {
  name: "Proposal",
  content: {
    document_type: "hslides",
    text: JSON.stringify(manifest),
  },
});

await studio.agents.publishCanvasArtifact(roomId, canvasId, "ORGANIZATION");
// Organization members can open the link after signing in.
```

Writing with the same ID replaces the name and body. `content.text` is UTF-8 with a maximum of **8MiB**, and `name` has a maximum of **255 characters**. A deck containing a `room://` reference cannot be written or published. The target room must have write permission, the Page session must be signed in, and the connection must provide a Canvas write route. An unsupported connection can result in 404.

From SDK **0.7.0**, specify a publication scope in `publishCanvasArtifact(roomId, canvasArtifactId, scope)`. `"PUBLIC"` is for users who know the link, and `"ORGANIZATION"` is for signed-in organization members. You cannot use `"PUBLIC"` if link publication is not allowed by the organization. To unpublish, use `unpublishCanvasArtifact()`, but you cannot unpublish when the room itself can be read without a session.

> The publication scope concerns the deck viewing link. Do not include confidential information, users' personal information, or authentication information in Canvas bodies. The two-argument publishing call from 0.6.0 cannot be used from 0.7.0 onward (a breaking change). When updating, always specify the third publication-scope argument.

## Common misunderstandings

### Passing `sessionId` always saves to history

An `isEphemeral: true` run cannot be assumed to have history. In the Page browser, omitting `isEphemeral` is treated the same as `true`. If conversation continuation, long-running result confirmation, or Canvas retrieval is needed, specify `isEphemeral: false` to run as a persistent conversation.

### The Canvas list includes the body

It does not. The list is metadata for choosing a candidate. Retrieve the body by calling `getCanvasArtifact()` with the selected ID.

### An API key can write and publish Canvas

It cannot. From SDK 0.6.0, writing and publishing are for the route of a signed-in user who opened a Studio-hosted Page. Do not bypass this by passing a key to the browser.

## If it does not work

| Situation | Check and response |
|---|---|
| A subsequent response does not use the previous conversation | Check whether the prior successful response's `sessionId` was passed to the next input for the same Agent and whether `isEphemeral: false` was specified (in the Page browser, omitting it also makes the conversation ephemeral). |
| History or Canvas returns 404 | Check the room ID, Project, authentication route, conversation owner, and connection support. Do not search by trying other IDs. |
| A Canvas body is not in the expected format | Check the types of `documentType`, `content`, and `text`. Do not continue processing based on a guessed format. |
| Canvas writing or publishing fails | Check that you use SDK 0.7.0 or later, that a signed-in user opened the Page, write permission for the room, deck format, Canvas ID, and connection support. |
| Cannot publish as `PUBLIC` | Check whether link publication is allowed by the organization and select `ORGANIZATION` if it meets the requirement. |

For an inquiry, record the target Project, SDK version, execution mode, Agent identifier, room ID, Canvas ID, time, HTTP status, and reproduction steps. Do not send API keys, Cookies, authentication headers, Canvas bodies, or confidential information.

## Checklist

- [ ] Retained the successful response's `sessionId` and passed it to subsequent conversations
- [ ] Specified `isEphemeral: false` in every call for a conversation requiring persistence
- [ ] Used `nextCursor` to retrieve required pages of history and Canvas
- [ ] Retrieved Canvas individually using the pair of room ID and Canvas ID
- [ ] Did not display or execute Canvas JSON, HTML, or `room://` references without validation
- [ ] Used a fixed Canvas ID for Canvas writes and specified the publication scope
- [ ] Did not include API keys, Cookies, authentication headers, or confidential information in logs

## Next article

- [SDK Agents: Invocation and Streaming](sdk-agents-invoke-and-stream.md)
- [Agents, Conversations, and Artifacts API](../api/agents-conversations-artifacts-api.md)
