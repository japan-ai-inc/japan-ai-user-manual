# Isolate SDK and API Errors

This article provides a procedure for isolating issues when Studio SDK or Public API calls fail, return an unexpected response, the CLI exits with failure, or you receive a 429 or timeout. It is for developers. Before trying additional credentials, separately check the execution mode, Node.js, SDK and CLI versions, endpoint, target Project, and HTTP response or CLI exit code.

## Key points: credentials by execution location

| Execution location | Credentials | Endpoint |
|---|---|---|
| Local Node.js (direct mode) | Project API key, passed from a server-side environment variable or similar | `baseUrl` set to the HTTPS Studio API endpoint |
| Page browser (Page hosted in Studio) | Page session. Do not set an API key | `baseUrl: ""` |
| Page server processing | A key read from a variable name the developer defines in the root `.env`. When published as an App, it is replaced with a platform-managed key | Endpoint passed through environment variables |
| CLI | The API key and endpoint URL are each resolved in the fixed order "command flag > environment variable > profile" | `--env`, `JAI_STUDIO_API_URL`, profile |

Distinguishing authentication errors: an invalid key returns 403 (`Invalid API key`) and an expired key returns 403 (`API key has expired`). 401 is returned only when no credentials are sent at all. Every Studio API error response includes a `requestId`.

## Related articles

- [Execution modes, authentication, and supported scope](../development/execution-modes-and-auth.md)
- [Development environment, versions, and compatibility](../development/dev-environment-and-versions.md)
- [Common API/SDK errors, timeouts, and limits](../development/api-sdk-errors-timeouts-limits.md)
- [CLI configuration and profiles](../cli/cli-config-and-profiles.md)
- [Manage Project API keys](../operations/manage-project-api-keys.md)
- [Hosted Page authentication and API routes](../page-runtime/hosted-page-auth-and-api-routes.md)
- [Agents, conversations, and artifacts API](../api/agents-conversations-artifacts-api.md)
- [Objects API](../api/objects-api.md)
- [Storage API](../api/storage-api.md)
- [Workflows API](../api/workflows-api.md)
- [Project Members API](../api/project-members-api.md)
- [Pages API](../api/pages-api.md)
- [Tool Proxy API](../api/tool-proxy-api.md)

## Before you begin

Record the failed feature, action type (read, create, update, or run), target identifying information, occurrence time and time zone, SDK/CLI version, Node.js version, execution mode, and `statusCode` or CLI exit code. Align the SDK/CLI version that supports the target feature with the lockfile. With the current npm SDK/CLI 0.11.0, check inputs, permissions, and states required for each action, including creating or updating Agents, Datasets, and automatic executions; record rules; comparisons; and bulk deletion. On success, the CLI writes JSON to standard output; on failure, it writes an error to standard error and exits with code 1.

> Do not expose API keys, `x-api-key`, Bearer tokens, Cookies, Secrets, or personal information in request bodies in consoles, CI logs, tickets, screenshots, or inquiries. Masked values from `jai-studio config show` cannot be used for authentication. Rather than pasting actual values to isolate the problem, check their source and validity.

## Before you begin

- [ ] I identified the execution location as local Node.js, a Page browser, or Page server processing.
- [ ] I checked the actual SDK/CLI 0.11.0, Node.js 22 or later, dependency, and lockfile versions.
- [ ] I checked a valid Project API key for the target Project and only the minimum required scopes.
- [ ] I verified that `baseUrl`, CLI `--env`, `JAI_STUDIO_API_URL`, and the profile endpoint point to the intended environment.
- [ ] I recorded whether an HTTP response exists, `statusCode`, `requestId`, a safely handled error summary, and the CLI exit code.
- [ ] For writes, runs, and uploads, I did not resend after a timeout or failure without checking whether processing completed.

## Isolate the issue by symptom

| Symptom or displayed message | Likely cause | What to check or do | Detailed article |
|---|---|---|---|
| `baseUrl is required` during Node.js initialization, or a relative URL fails | Node.js cannot use an empty `baseUrl`. Initialization intended for a Page browser is being reused. | For direct Node.js development, configure the HTTPS Studio API endpoint and target Project key only on the server. An empty `baseUrl` is for a Page browser. | [Execution modes, authentication, and supported scope](../development/execution-modes-and-auth.md) / [Common API/SDK errors, timeouts, and limits](../development/api-sdk-errors-timeouts-limits.md) |
| 401, 403, or 404 in a Page browser, or an expected SDK module cannot be used | You are confusing the Page-session route with the Project API key Public API route. Access mode, same-origin requirements, or browser-supported scope differs. | Initialize with `baseUrl: ""`; do not put keys or `Authorization` in the browser. Reopen the Page and check access settings and browser-supported modules. `members` and `workflows` can technically be called because the Page runtime relays them, but they are currently outside the beta support scope (subject to change without notice). | [Execution modes, authentication, and supported scope](../development/execution-modes-and-auth.md) / [Hosted Page authentication and API routes](../page-runtime/hosted-page-auth-and-api-routes.md) |
| 403 with `Invalid API key` or `API key has expired` | The key is invalid (wrong value or revoked) or expired, or a masked value is being passed. | Without displaying or sharing the API key value, check which configuration source supplied the key (flag, environment variable, then profile) and whether the key is revoked or expired. Do not pass a masked value to the SDK. | [Manage Project API keys](../operations/manage-project-api-keys.md) / [CLI configuration and profiles](../cli/cli-config-and-profiles.md) |
| 403 with another message | Insufficient scope or action permission, a different target Project, or the key's creator is no longer an active member of the Project (when the creator is removed, all keys they created return 403). | Check the required scopes, target Project, and the key creator's membership. | [Manage Project API keys](../operations/manage-project-api-keys.md) |
| 401 | No credentials were sent at all (the key environment variable is not read, no profile is configured, and so on). | Check the key's configuration source. The CLI resolves it in the order "command flag > environment variable > profile." | [CLI configuration and profiles](../cli/cli-config-and-profiles.md) |
| `node --version` is below 22, or SDK or CLI behavior differs by environment | Node.js, SDK, CLI, and lockfile versions are not aligned. | Check `node --version`, `jai-studio --version`, `npm ls`, and the execution directory. Use an environment that satisfies SDK/CLI 0.11.0 (or the fixed version supporting the feature) and Node.js 22 or later. | [Development environment, versions, and compatibility](../development/dev-environment-and-versions.md) |
| Low 400s, 404, or 409 | Input, target ID, target Project, route, or current state does not match. | For the low 400s, check inputs and execution mode; for 404, check ID, Project, sharing scope, and route; for 409, check current state and concurrent actions. Do not automatically resend the same request. Check feature-specific inputs in the relevant API article. | [Common API/SDK errors, timeouts, and limits](../development/api-sdk-errors-timeouts-limits.md) / [Public API articles](../api/agents-conversations-artifacts-api.md) |
| 429 or a response asking you to wait | A usage-control or concurrency-control limit was reached. | Check response guidance such as `retryAfterSeconds`, and reduce parallelism and resend frequency. The CLI's normal output removes `retryAfterSeconds`. Do not implement a fixed wait period or consecutive retries. | [Common API/SDK errors, timeouts, and limits](../development/api-sdk-errors-timeouts-limits.md) |
| A 5xx, network error, or no `statusCode` | A service, dependency, communication, response parsing, client cancellation (abort), interrupted stream reading, or SDK timeout may be involved. The error may not be an `SdkApiError`. | Record `cause`, start and end times, and execution environment. Do not treat the absence of `statusCode` as evidence that nothing ran; check the target state before deciding on a limited retry or inquiry. | [Common API/SDK errors, timeouts, and limits](../development/api-sdk-errors-timeouts-limits.md) |
| `Request timed out` or the result is unknown after an action | A client-side deadline was reached. Server-side processing may have started or completed. | Check `timeoutMs`, whether cancellation occurred, and target resources or run results. The client-wide `timeoutMs` does not apply to agents, pages, or `runAndWait`. Do not send the same create, update, or run request until you can confirm it will not duplicate processing; do not use the same retry policy as for reads. | [Common API/SDK errors, timeouts, and limits](../development/api-sdk-errors-timeouts-limits.md) |
| The CLI ends with exit code 1 or CI fails | CLI configuration, endpoint, key, command arguments, or API response failed. | Save a safe summary of standard error and the exit code. Use `config show` to check the profile name, endpoint, and masked key; use `--debug` only in a temporary environment that will not be shared. The normal output removes `requestId` and `runId`; use `--debug` to see them when needed. | [CLI configuration and profiles](../cli/cli-config-and-profiles.md) |
| `workflows run-and-wait` times out and exits with code 1 | Only the CLI's wait expired; the Workflow run continues. The server returns 408 with a `runId`, but the CLI's normal output removes the `runId`. | Rerunning the same command causes a **duplicate run**. Use `--debug` to get the `runId`, then get the state and output with `workflows status`. | [CLI: Workflows](../cli/cli-workflows.md) |
| `pages deploy` created a new Page instead of updating | A build output folder such as `./dist` was deployed without `--page-id`, and `.jai-studio/page.json`, which stored the Page ID, was erased by a rebuild. | Always specify `--page-id` in update scripts. | [Preview a Page locally and deploy it](../development/local-preview-and-deploy-page.md) |

## Fix the execution mode and endpoint first

1. **Local Node.js** uses Node.js 22 or later and a Project API key on the server. Set `baseUrl` to the HTTPS Studio API endpoint and pass the key through a secure route such as an environment variable.
2. **Page browser** uses the Page session. Set `baseUrl: ""` and do not set an API key. Available modules and routes are not identical to local use.
3. **Page server processing** reads the key from a variable name the developer defines in the root `.env` (no key is injected automatically). When published as an App, it is replaced with a platform-managed key. Do not pass the key into responses, screen initial values, or browser-public environment variables.
4. The CLI resolves the endpoint URL and the API key each in the order "command flag > environment variable > profile." This order is fixed. Check `--env`, `JAI_STUDIO_API_URL`, and profile values, and verify the intended endpoint and target-Project key combination.

This is a minimal example for direct development. Retrieve values from secure environment variables and do not output them to logs.

```ts
import { createClient } from "@japan-ai-inc/studio-sdk";

const studio = createClient({
  baseUrl: "https://api.japan-ai.co.jp",
  apiKey: process.env["JAI_STUDIO_API_KEY"],
  timeoutMs: 30_000,
});
```

> If `invokeUrl` is used with a key, it must have the same origin as `baseUrl`. Do not work around this by guessing an endpoint and replacing it with another domain, or by moving the key into the browser.

## Separate HTTP responses, timeouts, and retries

Module-specific SDK errors inherit from `SdkApiError`. When an HTTP response exists, classify `statusCode`, the response body (including `requestId`), and a cause exception only to the extent they can be handled safely. The following arrive in forms other than `SdkApiError`, so handle them separately:

- Network errors
- Cancellation (abort) by `AbortSignal`; detect it with `signal.aborted`
- Interrupted stream reading
- Plain `Error` thrown by `platform.*`
- The `{code}` object returned by `openTask` (a return value, not an exception)

The usual SDK default timeout is 30 seconds. The client-wide `timeoutMs` does not apply to agents, pages, or `runAndWait` (specify the `runAndWait` timeout in the second argument). Direct-mode streams are not subject to the timeout after response headers are received, so limit the overall time with `AbortSignal`.

- **4xx**: Correct inputs, IDs, Project, scopes, execution mode, and current state before trying again.
- **429**: Prioritize the response wait guidance and reduce parallel work and resend attempts.
- **5xx, connection loss, or timeout**: Query whether processing completed. Do not unconditionally resend writes or runs.
- **CLI exit code 1**: Do not determine the cause only from the absence of success JSON; check standard error, safe configuration verification, and API responses together. After a `run-and-wait` timeout the run continues, so do not rerun it.

## Before contacting an administrator or support

Excluding confidential information, collect the target feature/API; organization, Project, and target ID; SDK/CLI and Node.js versions; execution mode; an identifier for `baseUrl` or CLI environment name only insofar as the value itself is not confidential; occurrence time and time zone; action type; `statusCode`; `requestId` (use `--debug` for the CLI); CLI exit code; a redacted error summary; reproduction steps; and whether a timeout, retry, or state query occurred. For the format, see [Collect the Information Needed for an Inquiry](collect-info-for-support.md).

## Next articles

- [Common API/SDK errors, timeouts, and limits](../development/api-sdk-errors-timeouts-limits.md)
- [Execution modes, authentication, and supported scope](../development/execution-modes-and-auth.md)
- [CLI configuration and profiles](../cli/cli-config-and-profiles.md)
- [Manage Project API keys](../operations/manage-project-api-keys.md)
- [Collect the Information Needed for an Inquiry](collect-info-for-support.md)
