# SDK Agents: Invocation and Streaming

This article explains Agent invocation with the Studio SDK and how to handle output, failures, and credits.

For conversation history and Canvas, see [SDK Agents: Conversation History and Canvas](sdk-agents-history-and-canvas.md). For the HTTP API, see [Agents, Conversations, and Artifacts API](../api/agents-conversations-artifacts-api.md).

## Related articles

- [Execution Modes, Authentication, and Supported Scope](execution-modes-and-auth.md)
- [Manage Project API Keys](../operations/manage-project-api-keys.md)
- [Agents, Conversations, and Artifacts API](../api/agents-conversations-artifacts-api.md)
- [SDK Agents: Conversation History and Canvas](sdk-agents-history-and-canvas.md)

## Before you begin

- Use `@japan-ai-inc/studio-sdk` **0.11.0** and Node.js **22 or later**. Pin the SDK version.
- Keep API keys only on the server side for local use and Page runtime server processing. In the browser of a Studio-hosted Page (the "Page browser" below), do not pass a key; initialize with `createClient({ baseUrl: "" })`.
- Do not expose API keys, Cookies, authentication headers, or confidential information in browser code, public environment variables, or logs.

> A shared Project API key cannot isolate the conversation viewing scope of each calling user. Implement browser screens that handle each user's conversations through the Page runtime session route.

{% hint style="warning" %}
In the Page browser, calls that do not specify `isEphemeral` are treated as ephemeral conversations and do not read or write history. To continue a conversation by passing `sessionId`, **specify `isEphemeral: false` in every call**. Otherwise, the second and later calls have no prior context, and `getMessages()` returns nothing. In the Page browser, `systemPrompt` and `skipHistory` are also ignored (no error is returned).
{% endhint %}

## Create and update Agent definitions with a Project API key

In SDK 0.8.0 or later, you can use `agents.create()`, `agents.getDefinition()`, and `agents.update()` with a Project API key. The required key scope is `agent:write`, and the key creator must also have permission to create and edit Agents. These cannot be used from the Page browser. Creation is not idempotent. Check with `list({ search })` before recreating.

```ts
const created = await studio.agents.create({
  name: "Inquiry classification",
  prompt: "Classify the inquiry.",
  model: "gpt-5.5",
  tools: ["web_search"],
  datasets: ["dataset-id"],
});

const current = await studio.agents.getDefinition(created.agentId);
await studio.agents.update(created.agentId, {
  prompt: "Return the classification and responsible department.",
  tools: ["web_search", "web_fetch"],
});
```

The `definition` from `getDefinition()` can be passed to `update()`. Fields not sent in an update do not change, arrays replace the whole list, and `null` clears that field. Specify only tools, Skills, and models presented to the key creator in Studio's Agent settings screen. For `datasets`, specify only dataset IDs in the key's Project.

## 1. Receive a complete response

`invoke(agentId, input, requestOptions?)` returns `Promise<InvokeResponse>`. `input.prompt` is required. Specifying `isEphemeral: false` and passing the response's `sessionId` to the next input continues the conversation (`isEphemeral: false` is required in the Page browser).

```ts
import { AgentsApiError, createClient } from "@japan-ai-inc/studio-sdk";

const apiKey = process.env["JAI_STUDIO_API_KEY"];
if (!apiKey) throw new Error("Project API key is missing");

const studio = createClient({
  baseUrl: "https://api.japan-ai.co.jp",
  apiKey,
});
const agentId = "<Agent UUID>";

try {
  const result = await studio.agents.invoke(agentId, {
    prompt: "Summarize this document",
    isEphemeral: false, // Specify when continuing the conversation
  });
  if (result.status === "failed") {
    throw new Error(result.errorMessage ?? "Agent invocation failed");
  }
  console.log(result.text, result.sessionId);

  // Continue the same conversation. Specify isEphemeral: false in follow-up calls too.
  const next = await studio.agents.invoke(agentId, {
    prompt: "Narrow it down to three key points",
    sessionId: result.sessionId,
    isEphemeral: false,
  });
  console.log(next.text);
} catch (error) {
  if (error instanceof AgentsApiError) {
    console.error(error.statusCode, error.message);
  }
  throw error;
}
```

`InvokeResponse` has `status`, `sessionId`, and `text`. Check `references`, `imageAssets`, `files`, and `errorMessage` as needed. Output URLs expire.

## 2. Choose input and conversation handling

You can specify the following optional fields in `input`. Verify combinations in the target environment.

| Field | Purpose |
|---|---|
| `sessionId` | Continues an existing conversation. |
| `model`, `temperature`, `systemPrompt`, `skipHistory` | Specifies the runtime model, temperature, instructions, and history handling. In the Page browser, `systemPrompt` and `skipHistory` are ignored. |
| `isEphemeral` | `true` specifies a run that is not saved to conversation history. In the Page browser, it is treated as `true` when omitted, so specify `false` to continue a conversation. |
| `files` | Attaches a `File` or `Blob`. |
| `storageFileIds` | Attaches Storage `fileId` values in the Page browser. Maximum **20 items**. |
| `enableHandoffLongRunJob` | Permits handoff of a long-running job. |

You cannot specify `isEphemeral: true` and `enableHandoffLongRunJob: true` together. For handoff, retain `sessionId`, set `isEphemeral: false`, and check the history.

## 3. Reflect a stream in the screen

`invokeStream(agentId, input, requestOptions?)` is an `AsyncGenerator<ChatStreamEvent>`. Receive it with `for await...of` and branch on `type`. A `delta` may have images, files, or a finish reason even if `content` is empty.

```ts
let text = "";

for await (const event of studio.agents.invokeStream(agentId, {
  prompt: "Summarize in bullet points",
})) {
  if (event.type === "delta") {
    text += event.content;
    renderText(text);
    if (event.imageAsset) renderImage(event.imageAsset.url);
    if (event.file) renderFile(event.file.name, event.file.url);
    if (event.finishReason) markCompleted(event.finishReason);
  } else {
    showAgentError(event.text ?? event.code ?? "An error occurred during streaming");
  }
}
```

A `delta` has `responseId`, `content`, `finishReason`, and, as needed, `sessionId`, `imageAsset`, and `file`. `imageUrl` is a compatibility alias. For an `error` event, do not finalize preceding text as a successful result. Authentication, HTTP, and input failures before streaming starts become `AgentsApiError`. Network errors, aborts, and disconnections while reading the stream are thrown as exceptions other than `AgentsApiError`, so wrap the loop in `try/catch` and handle both.

## Compare responses in the Page browser

`agents.compare()`, `listCompareModels()`, `getTurns()`, and `adoptCandidate()` are for the Page browser. They cannot be used by the public API or in direct development. They require the response comparison feature to be enabled for the organization and the viewer to be a signed-in JAPAN AI user. They cannot be used in Pages with anonymous access or external customer authentication. First use `listCompareModels(agentId)` to obtain models that the user's organization settings, plan, and Agent settings allow for comparison. Follow the returned `min` to `max` range (currently 2 to 4 candidates) and `models`; do not guess and specify a model not in the list.

```ts
const available = await studio.agents.listCompareModels(agentId);
if (available.models.length < available.min) throw new Error("Not enough comparable models are available");

for await (const event of studio.agents.compare(agentId, {
  prompt: "Compare the proposals",
  // Specify models returned by listCompareModels(). Placeholder values such as "model-a" are rejected.
  models: ["model-a", "model-b"],
  primaryIndex: 0,
  tools: available.toolsAvailable ? ["web_search"] : undefined,
})) {
  if (event.type === "delta") appendCandidate(event.candidateIndex, event.text);
  if (event.type === "candidate_reset") resetCandidate(event.candidateIndex);
  if (event.type === "error") showCompareError(event.code, event.text);
}
```

For `files`, specify images and PDFs passed to all candidates; for `storageFileIds`, specify up to 20 Storage `fileId` values. `tools` can contain up to 10 distinct names, and only when `toolsAvailable` is `true` can you use tools with no candidate side effects. A candidate that does not support images can become `unsupported_input`.

If `candidateEfforts` is `true` and the model's `effortSupported` is `true`, you can pass `{ model, effort }` in `models` and select `low`, `mid`, `high`, or `max` for each candidate. You can duplicate the same model with different effort levels. On older connections, effort is ignored and duplicate models are rejected, so if `candidateEfforts` is `false`, pass each model name only once. Match candidates by `candidateIndex` or `index`, not by model name.

For comparison `error` events or `TurnCandidatesApiError` before streaming, check `model_not_allowed` and `policy_unavailable`. Refer to `models` for rejected models and retrieve the list again.

## 4. Handle `requestId` and credits in the Page browser

`requestId`, a turn identifier in the credit ledger, may be included in a Page browser `invoke()` response and `invokeStream()` `delta`.

```ts
const requestIds = new Set<string>();
for await (const event of studio.agents.invokeStream(agentId, { prompt: "Please review this" })) {
  if (event.type === "delta" && event.requestId) requestIds.add(event.requestId);
}

const ids = [...requestIds];
const { credits } = await studio.agents.getTurnCredits(ids);
const { credits: roomCredits } = await studio.agents.getRoomCredits(roomId);

for await (const total of studio.agents.followTurnCredits(ids)) showTurnCredits(total);
for await (const total of studio.agents.followRoomCredits(roomId)) showRoomCredits(total);
```

`getTurnCredits()` aggregates a signed-in user's own turns from the last 24 hours using **1 to 200** `requestId` values per request. `getRoomCredits()` gets the total for a readable room. `followTurnCredits()` and `followRoomCredits()` reread after 5 seconds, 30 seconds, 2 minutes, and 5 minutes, returning only the increased total.

Use these in a Studio-hosted Page opened by a signed-in user. Outside the Page runtime, they result in `AgentsApiError`.

## 5. Implement timeouts and cancellation

The default timeout is **5 minutes** for `invoke()` and **10 minutes** for `invokeStream()`. You can specify `timeoutMs` and an `AbortController` `signal` per call in `requestOptions`.

- The client-wide `timeoutMs` passed to `createClient()` does not apply to Agent calls.
- In direct mode (Project API key), a stream is no longer subject to the timeout after the response headers are received. To limit the total stream duration, abort it with an `AbortController`.

```ts
const controller = new AbortController();
const timer = setTimeout(() => controller.abort(), 120_000); // Limit for the whole stream
try {
  for await (const event of studio.agents.invokeStream(
    agentId,
    { prompt: "Create a proposal draft" },
    { signal: controller.signal, timeoutMs: 120_000 },
  )) {
    if (event.type === "delta") appendText(event.content);
    else showAgentError(event.text ?? "An error occurred");
  }
} catch (error) {
  if (controller.signal.aborted) showCancelled();
  else throw error;
} finally {
  clearTimeout(timer);
}

// Run this when the user chooses "Cancel".
controller.abort();
```

Cancellation stops receiving on the client side. Do not automatically rerun the same request; check the conversation and business result before resuming.

## If it does not work

| Situation | Check and response |
|---|---|
| An authentication or permission error occurs | Check the target Project, key, execution mode, and required scopes. |
| It does not work in the Page browser | Check that it runs within a Studio-hosted Page and initializes with `baseUrl: ""` without passing a key. |
| The second call does not carry over the previous context | In the Page browser, specify `isEphemeral: false` and the previous `sessionId` in every call. |
| `storageFileIds` fails | Check whether the call is from the Page browser, whether there are 20 or fewer IDs, and whether the user can handle the target files. |
| A response fails or stops midway | Record `status`, `errorMessage`, stream `error`, and exception `statusCode` separately, and do not immediately resend. |
| Cannot retrieve credits | Check that you use SDK 0.7.0 or later, that a signed-in user opened the Page, the number of `requestId` values, and the room ID. |

For an inquiry, include the SDK version, execution mode, Agent ID, time, `statusCode`, and reproduction steps. Do not send keys, Cookies, or authentication information.

## Checklist

- [ ] Chose the execution location and authentication method and stored API keys only on the server side
- [ ] Handled `status`, `sessionId`, and stream `delta` and `error`
- [ ] Considered permissions and expiration for attachments and output URLs
- [ ] When displaying credits, handled `requestId` and unsupported environments
- [ ] Checked screen display after timeout and cancellation

## Next article

- [Agents, Conversations, and Artifacts API](../api/agents-conversations-artifacts-api.md)
- [SDK Agents: Conversation History and Canvas](sdk-agents-history-and-canvas.md)
