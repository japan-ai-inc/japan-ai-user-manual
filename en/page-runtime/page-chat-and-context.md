# Integrating a Page with Chat and Context

A browser screen running in the Page runtime can use a user's Page session to start and continue conversations with an Agent and read the context of the displayed organization, Project, Page, and user. The key point is that you can connect screen input and conversations without passing a Project API key to the browser.

For general Agent SDK calls and streams, see [SDK Agents: Invoke and Stream](../development/sdk-agents-invoke-and-stream.md); for room history and artifacts, see [SDK Agents: Conversation History and Canvas](../development/sdk-agents-history-and-canvas.md); and for user conversation operations, see [Chat with Agents and Review Results](../use/chat-with-agents.md).

## Related articles

- [SDK Pages and Platform](../development/sdk-pages-and-platform.md)
- [Agents, Conversations, and Artifacts API](../api/agents-conversations-artifacts-api.md)
- [Chat with Agents and Review Results](../use/chat-with-agents.md)

## Before you begin

- This article applies to **browser-side** code displayed in the Page runtime. Do not assume it uses the same authentication method as local development, external hosting, or Page server-side processing.
- Use the current public version 0.11.0 of `@japan-ai-inc/studio-sdk` and check compatibility with the Page runtime you use. `platform` is a beta feature.
- Do not place Project API keys, cookies, authentication headers, or Secret values in Page browser code. Initialize the SDK with an empty `baseUrl`.
- Minimize the screen data passed to conversations. Do not pass customer information, personal information, or hidden screen fields wholesale merely for convenience.

## 1. Implement Agent calls in a Page

In a Page runtime browser, initialize as follows. When `agents.invoke()` detects the Page runtime, it sends to the Page-relative `/api/agent-chat`. Because it uses the user's Page session, do not add an API key.

```ts
import { createClient } from "@japan-ai-inc/studio-sdk";

const studio = createClient({ baseUrl: "" });

const result = await studio.agents.invoke(agentId, {
  prompt: "Summarize the selected opportunity in three points.",
});

if (result.status === "failed") {
  throw new Error(result.errorMessage ?? "Agent execution failed.");
}
showAnswer(result.text);
```

Clearly state the Agent, target data, and scope that may be run on screen. Follow [Chat with Agents and Review Results](../use/chat-with-agents.md) for user confirmation methods.

### Display a stream

Use `invokeStream()` to display output while it is generated. Handle `error` as well as `delta`; when receiving `error`, do not finalize the text accumulated so far as the completed result.

```ts
let text = "";
for await (const event of studio.agents.invokeStream(agentId, {
  prompt: userInput,
})) {
  if (event.type === "delta") {
    text += event.content;
    renderStreamingText(text);
  } else {
    showError(event.text ?? "Could not complete the response.");
  }
}
```

Page-relative routes also include `/api/agent-chat/status/{roomId}` for checking room status and `/api/chat-history/{roomId}` for retrieving history. Both require a valid Page session, and room IDs are UUIDs. History returned for Pages does not include Tool call arguments (`args`). Do not use Tool arguments in display or processing. For normal conversation continuation, however, prioritize `sessionId` and the SDK rather than directly constructing these routes. `/api/mentions` could not be confirmed as an API for Page users within this confirmation scope.

In SDK 0.11.0, only when the Page runtime supports it, you can obtain credits for a turn with `getTurnCredits()` from a result's `requestId` and the room total with `getRoomCredits()`. Unsupported runtimes return 404, so do not make credit display a required screen feature, and handle failures.

### When comparing answer candidates

Comparison is an optional feature available only on Pages for signed-in users. First call `listCompareModels(agentId)` and follow the returned `min`, `max`, `models`, and `toolsAvailable`. Specify only two to four candidates in `compare()`; do not infer and pass model names. You can specify attachments for each candidate with `files` or up to 20 `storageFileIds`, and non-side-effect Tools with `tools`. `getTurns()` candidates can include attachments, Tools, and reference information, so safely handle URLs, file names, and content before display.

Specify `effort` (`low`, `mid`, `high`, or `max`) for each candidate, and compare the same model at different effort levels, only when `candidateEfforts` returned by `listCompareModels()` is `true`. If a model's `effortSupported` is `false`, do not display effort choices. These values may be `false` in unsupported Page runtimes, so fall back to ordinary comparison by specifying each model once. Use `adoptCandidate()` to adopt a candidate, and confirm that the room is idle and the user has permission.

## 2. Continue the same conversation with `sessionId`

The `sessionId` in the first successful response is the room ID. Retain it in screen state and pass it with the next input to continue the same conversation. Do not specify `isEphemeral: true` for a use case that must retain and continue a conversation.

```ts
const first = await studio.agents.invoke(agentId, {
  prompt: "List the items to confirm for this opportunity.",
});
if (first.status === "failed") throw new Error(first.errorMessage);

const next = await studio.agents.invoke(agentId, {
  sessionId: first.sessionId,
  prompt: "Reorder them by priority.",
});
```

When conversations span screens, associate the room ID with the target Agent, Project, and user. Do not connect to another conversation with an inferred ID. For history and Canvas, see [SDK Agents: Conversation History and Canvas](../development/sdk-agents-history-and-canvas.md).

## 3. Read Page context

`platform` is for Page runtime browsers only. Use `isAvailable()` to check availability; synchronous `getContext()` returns `null` when unavailable. Injection may not be complete during initial rendering, so normally use `waitForReady()`. The default wait time is 5,000 milliseconds, and it returns `null` when unavailable or when the wait times out.

```ts
const context = await studio.platform.waitForReady();
if (!context) {
  showUnavailableMessage();
} else {
  showCurrentProject(context.orgName, context.projectId);
}
```

| Category | Values |
|---|---|
| User | `userId`, `email`, `userName`, `memberRole` |
| Organization, Project, and Page | `orgId`, `orgName`, `projectId`, `pageId` |
| Object | `customObjects` (mapping from names to Object IDs) |

## 4. Pass screen selections to Studio Tasks

`openTask()` passes an object or array selected on screen and optional instructions to a Studio Task, and returns `roomId`. At least data or instructions are required. Do not use it for URL navigation within a screen or navigation to arbitrary sites.

```ts
const selected = { orderId: "42", status: "Pending confirmation" };
const task = await studio.platform.openTask(
  selected,
  "Organize the items that should be confirmed for this order."
);

console.log(task.roomId);
```

Even when using the returned `roomId` in subsequent screen processing, do not treat it as a value that unconditionally permits viewing or continuing the conversation. For the route for users to review conversations in Studio, sharing scope, and whether input and additional sends are allowed, see [Chat with Agents and Review Results](../use/chat-with-agents.md).

## Check by access settings

The selected "Access settings" affects both whether a screen can be opened and which principal handles conversations and context. Being able to use a feature does not mean the data may be made public.

| Access settings | Implementation and acceptance checks |
|---|---|
| "Sign-in required" | For a user with Page access, confirm context, conversation start and continuation, and error display. Reading and adopting comparison-answer candidates with `getTurns()` and `adoptCandidate()` have been confirmed only on Pages for signed-in users. |
| "Sign-in optional" | Check both signed-out and signed-in states. Do not construct screens or prompts on the assumption that signed-out users have IDs or roles. The feature to read and adopt comparison-answer candidates cannot be used while signed out, so separate it into the normal conversation route. |
| "Anonymous only" | Do not assume user-specific information, room management for individual users, or reading and adopting comparison-answer candidates. Confirm the screen actually visible from the public URL, data passed to Agents, and actions that can run. |
| "External customer auth" | When signed in as an external customer, confirm the screen, conversation, and context. Do not design display or permissions on the assumption that the user is an organization member; pass only the data required. |

> You cannot change Page access settings with `platform`, the SDK, or browser code. Do not reduce the publication scope set in "Access settings"; verify on actual systems for each access mode.

## If something does not work

| Situation | First thing to check |
|---|---|
| `waitForReady()` returns `null` | Check whether this is a Page runtime browser, compatibility between the SDK and Page runtime, and timing during initial display. Do not work around it by adding an API key. |
| Agent call fails | Check the target Page, access settings, Agent, `status` and `errorMessage`, and stream `error`. Do not resend a request when it is unknown whether it was processed. |
| Cannot retrieve comparison candidates or effort | Check the results of `listCompareModels()`, signed-in user, organization and Agent model settings, and Page runtime. If `candidateEfforts` is `false`, do not send effort or duplicate use of the same model. |
| Does not use the previous conversation | Check that the successful response's `sessionId` is passed to the next call and that `isEphemeral: true` is not set when persistence is needed. |
| Cannot retrieve room status or history | Check the Page session, target Project, and room ID format. Do not try other room IDs to explore content. |
| `openTask()` fails | Check that data or instructions are passed, that the value is an object, array, or `null`, and that it is used in the Page runtime. |

For inquiries, record the SDK version, access settings, target Page, date and time, error excluding secrets, and reproduction steps. Do not send API keys, cookies, Secret values, or unnecessary conversation content.

## Checklist

- [ ] Initialized Page browser code with `createClient({ baseUrl: "" })` and did not include an API key
- [ ] Reflected success and failure from `invoke()` or `invokeStream()` separately in the screen
- [ ] Retained the successful response's `sessionId` for continuing conversations
- [ ] Followed `listCompareModels()` candidates, limits, Tool availability, and effort availability in comparison UI
- [ ] Did not use values from `getContext()` for client-side authorization decisions
- [ ] Prepared a screen for when `waitForReady()` is `null`
- [ ] Passed only the minimum required data to `openTask()`
- [ ] Checked the required user states for "Sign-in required," "Sign-in optional," "Anonymous only," and "External customer auth"
- [ ] Did not include API keys, cookies, Secrets, or unnecessary personal information in code, logs, or prompts

## Next articles

- [SDK Pages and Platform](../development/sdk-pages-and-platform.md)
- [Agents, Conversations, and Artifacts API](../api/agents-conversations-artifacts-api.md)
- [Chat with Agents and Review Results](../use/chat-with-agents.md)
