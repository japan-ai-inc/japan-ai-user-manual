# Isolate SDK and API Errors

This article provides a procedure for isolating issues when Studio SDK or Public API calls fail, return an unexpected response, the CLI exits with failure, or you receive a 429 or timeout. It is for developers. Before trying additional credentials, separately check the execution mode, Node.js, SDK and CLI versions, endpoint, target Project, and HTTP response or CLI exit code.

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

- [ ] I identified the execution location as local Node.js, a PRS browser, or a PRS server.
- [ ] I checked the actual SDK/CLI 0.11.0, Node.js 22 or later, dependency, and lockfile versions.
- [ ] I checked a valid Project API key for the target Project and only the minimum required scopes.
- [ ] I verified that `baseUrl`, CLI `--env`, `JAI_STUDIO_API_URL`, and the profile endpoint point to the intended environment.
- [ ] I recorded whether an HTTP response exists, `statusCode`, a safely handled error summary, and the CLI exit code.
- [ ] For writes, runs, and uploads, I did not resend after a timeout or failure without checking whether processing completed.

## Isolate the issue by symptom

| Symptom or displayed message | Likely cause | What to check or do | Detailed article |
|---|---|---|---|
| `baseUrl is required` during Node.js initialization, or a relative URL fails | Node.js cannot use an empty `baseUrl`. Initialization intended for a PRS browser is being reused. | For direct Node.js development, configure the HTTPS Studio API endpoint and target Project key only on the server. An empty `baseUrl` is for a PRS browser. | [Execution modes, authentication, and supported scope](../development/execution-modes-and-auth.md) / [Common API/SDK errors, timeouts, and limits](../development/api-sdk-errors-timeouts-limits.md) |
| 401, 403, or 404 in a PRS browser, or an expected SDK module cannot be used | You are confusing the Page-session route with the Project API key Public API route. Access mode, same-origin requirements, or browser-supported scope differs. | Initialize with `baseUrl: ""`; do not put keys or `Authorization` in the browser. Reopen the Page and check access settings and browser-supported modules. | [Execution modes, authentication, and supported scope](../development/execution-modes-and-auth.md) / [Hosted Page authentication and API routes](../page-runtime/hosted-page-auth-and-api-routes.md) |
| 403, an authentication or permission error, or CLI authentication failure | The key's target Project, expiration, revocation, scope, configuration source, or execution mode does not match. | Without displaying or sharing the API key value, check its issuer, target Project, required scopes, and the environment variable or profile. Do not pass a masked value to the SDK. | [Manage Project API keys](../operations/manage-project-api-keys.md) / [CLI configuration and profiles](../cli/cli-config-and-profiles.md) |
| `node --version` is below 22, or SDK or CLI behavior differs by environment | Node.js, SDK, CLI, and lockfile versions are not aligned. | Check `node --version`, `jai-studio --version`, `npm ls`, and the execution directory. Use an environment that satisfies SDK/CLI 0.11.0 (or the fixed version supporting the feature) and Node.js 22 or later. | [Development environment, versions, and compatibility](../development/dev-environment-and-versions.md) |
| Low 400s, 404, or 409 | Input, target ID, target Project, route, or current state does not match. | For the low 400s, check inputs and execution mode; for 404, check ID, Project, sharing scope, and route; for 409, check current state and concurrent actions. Do not automatically resend the same request. Check feature-specific inputs in the relevant API article. | [Common API/SDK errors, timeouts, and limits](../development/api-sdk-errors-timeouts-limits.md) / [Public API articles](../api/agents-conversations-artifacts-api.md) |
| 429 or a response asking you to wait | A usage-control or concurrency-control limit was reached. | Check response guidance such as `retryAfterSeconds`, and reduce parallelism and resend frequency. Do not implement a fixed wait period or consecutive retries. | [Common API/SDK errors, timeouts, and limits](../development/api-sdk-errors-timeouts-limits.md) |
| A 5xx, network error, or no `statusCode` | A service, dependency, communication, response parsing, client cancellation, or SDK timeout may be involved. | Record `cause`, start and end times, and execution environment. Do not treat the absence of `statusCode` as evidence that nothing ran; check the target state before deciding on a limited retry or inquiry. | [Common API/SDK errors, timeouts, and limits](../development/api-sdk-errors-timeouts-limits.md) |
| `Request timed out` or the result is unknown after an action | A client-side deadline was reached. Server-side processing may have started or completed. | Check `timeoutMs`, whether cancellation occurred, and target resources or run results. Do not send the same create, update, or run request until you can confirm it will not duplicate processing; do not use the same retry policy as for reads. | [Common API/SDK errors, timeouts, and limits](../development/api-sdk-errors-timeouts-limits.md) |
| The CLI ends with exit code 1 or CI fails | CLI configuration, endpoint, key, command arguments, or API response failed. | Save a safe summary of standard error and the exit code. Use `config show` to check the profile name, endpoint, and masked key; use `--debug` only in a temporary environment that will not be shared. | [CLI configuration and profiles](../cli/cli-config-and-profiles.md) |

## Fix the execution mode and endpoint first

1. **Local Node.js** uses Node.js 22 or later and a Project API key on the server. Set `baseUrl` to the HTTPS Studio API endpoint and pass the key through a secure route such as an environment variable.
2. **PRS browser** uses the Page session. Set `baseUrl: ""` and do not set an API key. Available modules and routes are not identical to local use.
3. **PRS server** uses the key that PRS injects into server-only configuration. Do not pass the key into responses, screen initial values, or browser-public environment variables.
4. For the CLI, check the specified `--env`, `JAI_STUDIO_API_URL`, and profile display values, and verify the intended endpoint and target-Project key combination. Do not change the endpoint by guessing precedence.

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

Module-specific SDK errors inherit from `SdkApiError`. When an HTTP response exists, classify `statusCode`, the response body, and a cause exception only to the extent they can be handled safely. The usual SDK default timeout is 30 seconds, but it differs by feature. For example, an Agent's `invoke()` and stream have different defaults, so check the feature article.

- **4xx**: Correct inputs, IDs, Project, scopes, execution mode, and current state before trying again.
- **429**: Prioritize the response wait guidance and reduce parallel work and resend attempts.
- **5xx, connection loss, or timeout**: Query whether processing completed. Do not unconditionally resend writes or runs.
- **CLI exit code 1**: Do not determine the cause only from the absence of success JSON; check standard error, safe configuration verification, and API responses together.

## Before contacting an administrator or support

Excluding confidential information, collect the target feature/API; organization, Project, and target ID; SDK/CLI and Node.js versions; execution mode; an identifier for `baseUrl` or CLI environment name only insofar as the value itself is not confidential; occurrence time and time zone; action type; `statusCode`; CLI exit code; a redacted error summary; reproduction steps; and whether a timeout, retry, or state query occurred. For the format, see [Collect the Information Needed for an Inquiry](collect-info-for-support.md).

## Next articles

- [Common API/SDK errors, timeouts, and limits](../development/api-sdk-errors-timeouts-limits.md)
- [Execution modes, authentication, and supported scope](../development/execution-modes-and-auth.md)
- [CLI configuration and profiles](../cli/cli-config-and-profiles.md)
- [Manage Project API keys](../operations/manage-project-api-keys.md)
- [Collect the Information Needed for an Inquiry](collect-info-for-support.md)
