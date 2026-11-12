# Common API and SDK Errors, Timeouts, and Limits

When integrating an API or the Studio SDK, design not only for successful responses, but also for failures involving authentication, input, communication, timeouts, and usage controls. This article explains common handling that helps developers classify failures and decide whether to prompt users to run again or guide them to check their settings.

For handling specific to Agent invocation and streams, see [SDK Agents: Invocation and Streaming](sdk-agents-invoke-and-stream.md). For the source of truth on availability conditions and limits by feature or organization, see [Index of Limits and Availability Conditions](../operations/limits-and-availability-index.md).

## Related articles

- [SDK Agents: Invocation and Streaming](sdk-agents-invoke-and-stream.md)
- [Index of Limits and Availability Conditions](../operations/limits-and-availability-index.md)
- [Isolate SDK and API Errors](../troubleshooting/isolate-sdk-api-errors.md)
- [Manage Project API Keys](../operations/manage-project-api-keys.md)

## Scope of this article

This article covers applications that call `@japan-ai-inc/studio-sdk` and public APIs. It does not cover individual HTTP endpoints, feature-specific input limits, contracts or pricing, or availability by organization. Check the feature articles and [Index of Limits and Availability Conditions](../operations/limits-and-availability-index.md).

SDK **0.11.0** returns dedicated error types for each module. All inherit from `SdkApiError`. Record the common fields, then branch on module-specific types only where necessary. Types such as `AgentsApiError` and `ScheduledTasksApiError`, which retain the `code` sent by the destination, allow you to use `code` as well as the message for safe classification.

> Do not output Project API keys, the `x-api-key` header, Bearer tokens, cookies, Secrets, or confidential information in user input directly in exception messages, monitoring logs, or inquiries. Check key issuance, revocation, and scope in [Manage Project API Keys](../operations/manage-project-api-keys.md).

## 1. First classify failures into four categories

Whether to resend differs by the cause, even for the same "failure." Record the target feature, operation type, identifier, time, SDK version, and execution mode. Do not retain content or attachments.

| Category | What to check in the SDK | Initial action |
|---|---|---|
| Configuration or input before invocation | Initialization exceptions and input validation errors | Correct `baseUrl`, execution mode, required values, and target ID before retrying. |
| Rejection by an HTTP response | `statusCode` and `responseBody` | Separately check authentication, permission, input, target existence, conflicts, and usage controls. |
| Communication or timeout | Errors without `statusCode`, `cause`, and cancellation state | Consider that server-side processing may already be complete, and avoid immediate automatic resends. |
| Failure within a stream | The stream's `error` event | Do not finalize already received fragments as a completed result; display and record the event content. |

`SdkApiError` contains `message`, `statusCode`, `responseBody`, and `cause`. Do not display or save `responseBody` unmodified. The SDK 0.11.0 CLI displays the `code` intentionally returned by the API and object-type `params` along with the message, but hides other raw response bodies without `--debug`. Use `code` and the minimum necessary `params` for classification, and confirm that their values contain no personal information, Secrets, or authentication information before recording them.

## 2. Receive SDK errors safely

You can handle the minimum diagnostic information consistently using the common type. Display fixed guidance text on the screen.

```ts
import {
  AgentsApiError,
  SdkApiError,
  createClient,
} from "@japan-ai-inc/studio-sdk";

const studio = createClient({
  baseUrl: "https://api.japan-ai.co.jp",
  apiKey: process.env["JAI_STUDIO_API_KEY"],
});

try {
  await studio.agents.invoke("agent-id", { prompt: "Please summarize." });
} catch (error) {
  if (error instanceof AgentsApiError) {
    recordFailure({ statusCode: error.statusCode, message: error.message, code: error.code });
    showRetryGuidance(error.statusCode);
  } else if (error instanceof SdkApiError) {
    recordFailure({ statusCode: error.statusCode, message: error.message });
  } else {
    recordFailure({ message: "An exception occurred outside the SDK." });
  }
}
```

`ObjectsApiError`, `StorageApiError`, `WorkflowsApiError`, `PagesApiError`, and `PlatformApiError` also inherit from the common type. `TurnCandidatesApiError` inherits from `AgentsApiError`. Put common processing first, and determine dedicated types only where necessary.

> An unsuccessful HTTP response becomes a module-specific error. `statusCode` may be absent for network failures, parsing failures, and timeouts. Its absence is not evidence of success or that no operation occurred.

## 3. Separate decisions by HTTP status

An API can return input deficiencies, insufficient authentication or permission, missing targets, conflicts, usage controls, and server-side failures as HTTP responses. Use the following as a general isolation order.

| Example status | Decision | Action |
|---|---|---|
| Low 400s | Input or request conditions are insufficient | Check parameters, target ID, and execution mode. |
| 401 | The API key is missing or invalid, or the session has expired | Check the key value and expiry, and the PRS sign-in state. |
| 403 | There is an issue with the key or operation permission | Check the target Project, required scopes, and permissions. |
| 404 | The target does not exist or cannot be referenced | Check the ID, Project, environment, and sharing scope. |
| 409 | A state conflict or concurrent operation occurred | Retrieve the current state again. |
| 408 or timeout | The start or completion state is unknown | Check the invocation result or resources that may have been created. |
| 429 | Usage control or concurrent-execution control | Follow the response's wait guidance. |
| 500s | A failure on the service or a dependency | Record identifying information and check it. |

Public API read paths and Page deployment have usage controls and may return 429. Do not embed numbers or scope as fixed values.

## 4. Make timeouts and cancellations explicit

The SDK's normal default timeout is **30 seconds**. You can change it with `timeoutMs` in `createClient()` or on each call, and can also specify `AbortSignal`.

Agents are an exception: `invoke()` defaults to **5 minutes**, and `invokeStream()` defaults to **10 minutes**. These are not common limits for other modules or the HTTP API as a whole.

```ts
const controller = new AbortController();

try {
  await studio.objects.list({ limit: 20 }, {
    timeoutMs: 15_000,
    signal: controller.signal,
  });
} finally {
  // Run this when you stop the call, such as when closing the screen.
  controller.abort();
}
```

On timeout, the SDK aborts communication and throws the applicable module's API error. Cancellation or timeout alone does not determine the server-side result. Do not resend writes unconditionally.

## 5. Design retries and usage controls

The SDK's common communication processing does not automatically retry failures. The caller decides whether retrying is allowed, when to wait, and how to prevent duplicates. Do not apply the same policy to reads and writes.

1. Classify the operation as a read, create, update, invocation, or upload.
2. For 429, prioritize wait guidance and do not resend repeatedly.
3. For disconnection, timeout, or 500s, query the target state or invocation result.
4. Retry only reads that you confirm did not run, with a limited number of attempts.
5. Run writes again after checking the state.

> Do not set a uniform retry count, interval, or rate-limit value; verify them for the target feature.

## If something does not work

| Situation | What to check and do |
|---|---|
| It always returns 403 | Check the API key, target Project, required scopes, and execution mode. |
| 429 continues | Check the response guidance, and reduce parallelism and resend frequency. |
| You do not know whether you can run again after a timeout | Query the target resource or invocation result. |
| `statusCode` is absent | Check the network, client cancellation, and SDK timeout. |
| You cannot identify the SDK type | Process it as `SdkApiError` and record the SDK version and `message`. |

For an inquiry, include the target feature, SDK version, execution mode, operation time, request type, `statusCode`, error, and reproduction steps. Do not include API keys, authentication headers, cookies, Secret values, personal information, or input data.

## Checklist

- [ ] I record non-success `statusCode` values and `message`
- [ ] I process `SdkApiError` commonly and identify module-specific types only where necessary
- [ ] I distinguish the normal 30-second default from the Agent defaults of 5 and 10 minutes
- [ ] I specify `timeoutMs` and `AbortSignal` according to the screen or job termination policy
- [ ] I do not automatically resend writes or invocations after a timeout without checking the state
- [ ] For 429, I check the response's wait guidance and suppress resends
- [ ] I do not include authentication information or confidential data in error records or inquiries

## Read next

- [SDK Agents: Invocation and Streaming](sdk-agents-invoke-and-stream.md)
- [Index of Limits and Availability Conditions](../operations/limits-and-availability-index.md)
- [Isolate SDK and API Errors](../troubleshooting/isolate-sdk-api-errors.md)
- [Manage Project API Keys](../operations/manage-project-api-keys.md)
