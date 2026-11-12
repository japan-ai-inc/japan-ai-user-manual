# Hosted Page Authentication and API Routes

Hosted Page browser code calls same-origin relative paths in the **Page runtime**, not externally public APIs with a Project API key. A Page-specific session created when a Page opens associates the organization, Project, Page, and user (or visitor) authentication context. Do not place Project API keys in browser JavaScript, public environment variables, initial display data, or logs.

## Related articles

- [Execution Modes, Authentication, and Supported Scope](../development/execution-modes-and-auth.md)
- [Page Navigation and Deep Links](../development/page-navigation-and-deep-links.md)
- [Page Server Processing, Environment Variables, and Secret References](../development/page-server-env-and-secrets.md)
- [Agents, Conversations, and Artifacts API](../api/agents-conversations-artifacts-api.md)
- [Objects API](../api/objects-api.md)
- [Storage API](../api/storage-api.md)
- [Workflows API](../api/workflows-api.md)
- [Project Members API](../api/project-members-api.md)
- [Tool Proxy API](../api/tool-proxy-api.md)
- [Manage Project API Keys](../operations/manage-project-api-keys.md)
- [Hosted Page Objects API](hosted-page-objects-api.md)

## Before you begin

This article covers APIs called from a Hosted Page **browser**. Page server processing is a separate execution surface and does not return keys or Secrets to the browser. See [Page Server Processing, Environment Variables, and Secret References](../development/page-server-env-and-secrets.md) and [Execution Modes, Authentication, and Supported Scope](../development/execution-modes-and-auth.md) for details.

> Knowing a Page's public URL and being able to operate arbitrary Project resources are not the same. Perform acceptance tests that combine display data, permitted actions, and access mode.

## 1. Use relative paths and the Page session in the browser

Initialize the SDK in a Page with an empty `baseUrl`. This makes `fetch` use relative paths on the Page's same origin, processed through the Page session held by the browser.

```ts
import { createClient } from "@japan-ai-inc/studio-sdk";

const studio = createClient({ baseUrl: "" });
const files = await studio.storage.list({ path: "/" });
```

The browser-supported SDK scope in 0.11.0 is `agents`, `datasets`, `objects`, `storage`, and `platform`. When it detects the Page runtime, `agents.invoke()` uses `POST /api/agent-chat`. Objects, Storage, and Dataset reads use, for example, `/api/v1/objects/...`, `/api/v1/storage/...`, and `/api/v1/datasets...`.

`/api/v1/*` is not a generic entry point that proxies arbitrary public APIs. Objects, Storage, and Dataset reads are covered by the browser SDK. `workflows` and `scheduledTasks` are outside its scope. Consider retrieving Project members only when permission and read access are both available in the Page runtime, and see [Project Members API](../api/project-members-api.md). Separate other features into server processing.

| Purpose | Route in a Page browser | How to use it |
|---|---|---|
| Run an Agent | `/api/agent-chat` | Use `agents.invoke()`. Do not assume that browser support includes listing or retrieving details through `/api/v1/agents`. |
| Objects | `/api/v1/objects/...` | Follow the SDK/API specification for `objects`. See [Objects API](../api/objects-api.md) and [Hosted Page Objects API](hosted-page-objects-api.md). |
| Storage | `/api/v1/storage/...` | Use `storage`. `downloadFolder()` is unavailable in the browser. See [SDK Storage](../development/sdk-storage.md) for details. |
| Dataset reads | `/api/v1/datasets...` | Use only `datasets.list()` and `datasets.search()`. It returns 404 without a supporting Page runtime. Organization Datasets are handled only in a signed-in user session. |
| Tool Proxy | `/api/tools/...` | It is for the Page runtime only. Use only routes in [Tool Proxy API](../api/tool-proxy-api.md). |

> Do not add `x-api-key`, `Authorization: Bearer`, or a Project API key in `pak_...` format to a Page browser request. Obfuscating code does not prevent users from obtaining values distributed to their browser.

## 2. Check the session for each access mode

| Mode in "Access settings" | Authentication context when opening the Page | Implementation and test points |
|---|---|---|
| "Sign-in required" | A Page session for a signed-in user with Page access. | Confirm that a user who is not signed in returns to the original Page URL after authentication. |
| "Sign-in optional" | A user session when signed in; an anonymous Page session when not signed in. | Test the same screen and API separately when signed in and when not signed in. |
| "Anonymous only" | An anonymous Page session regardless of whether the visitor is signed in. | Do not assume user-specific information or permissions; check permitted reads and writes as an anonymous visitor. |
| "External customer auth" | A Page session for a visitor authenticated through the configured customer identity provider. | If external authentication is enabled and there is no session, it returns 401; reload the current Page document and authenticate again. |

Both "Sign-in optional" and "Anonymous only" can be opened while signed out, but the former can use an existing user session while the latter always uses an anonymous context. Check "External customer auth" on the assumption that a configured connection exists. Both screen access and API operations verify that the organization, Project, and Page match the session.

## 3. Pass Origin validation for writes

For `POST`, `PUT`, `PATCH`, `DELETE`, and WebSocket connection requests, Origin validation occurs in addition to the Page session. If `Origin` is absent, the origin in `Referer` is checked; writes with an untrusted value or with neither value return 403. Ordinary same-origin `fetch` and SDK usage do not require custom headers for this.

Do not design writes from another site using cookies, or state changes without an Origin. Run them as client operations within the Page, or separate them into Page server processing and implement authorization.

## 4. Do not retry every 401

The Page runtime treats 401 caused by a missing or invalid session as an authentication-refresh target. For ordinary Page sessions, it notifies the parent screen through `prs:retry-requested`, and the parent reloads the Page with new authentication information. For external customer authentication, the Page reloads and starts authentication again. Reloading is also suppressed.

On the other hand, a 401 returned by the application itself or a destination is not necessarily a signal to refresh authentication. Delegate refresh only when the response is marked as a Page-runtime session authentication error, and display required guidance to users for business 401 and 403 responses. Do not unconditionally resend 401 responses in custom code or display cookies and tokens on screen. For verifying return to the screen, also see [Page Navigation and Deep Links](../development/page-navigation-and-deep-links.md).

## Differences between the Page session route and Public API

| Perspective | Page session route | Public API (Project API key) |
|---|---|---|
| Caller | Browser inside a Hosted Page | Local development, managed automation, or server-side processing |
| URL | `/api/...` on the same origin as the Page | API routes at `https://api.japan-ai.co.jp` |
| Authentication information | Session resulting from opening the Page | Pass a Project API key in `x-api-key` or Bearer |
| Key in the browser | Not required and prohibited | Do not use it. Store keys on the server side only. |
| Scope of use | Limited to routes provided by the Page runtime and browser-supported SDK | Follows key scopes and individual Public API specifications |
| Operations | Use the Page reauthentication route when the session expires | Manage issuance, minimum scope, expiration, rotation, and revocation |

For Public API details, see [Agents, Conversations, and Artifacts API](../api/agents-conversations-artifacts-api.md) through [Project Members API](../api/project-members-api.md); for key management, see [Manage Project API Keys](../operations/manage-project-api-keys.md).

## Checklist

- [ ] Initialized the browser SDK with `createClient({ baseUrl: "" })` and do not read a Project API key
- [ ] Used Page-relative `/api/...` and did not hard-code a public API host in browser code
- [ ] Tested screen display and API operations in both unauthenticated and authenticated states for each access mode
- [ ] Implemented writes as same-origin client operations and do not unconditionally retry 401
- [ ] Distinguished 401, 403, 429, and temporary 5xx in the UI, and did not leave cookies, tokens, or API keys in logs or inquiries

## If something does not work

| Situation | What to check and do |
|---|---|
| `/api/v1/...` returns 404 | Check whether the leading path is provided by the Page runtime and whether the SDK module supports browsers. It does not proxy arbitrary routes for Public APIs. |
| Returns 401 | Reopen the Page, and check the access mode, reaching the target Page, and the display route that performs authentication refresh. Do not work around it by adding a key. |
| Returns 403 | Check that Origin/Referer is not spoofed or fixed, that it is a same-origin Page action, and that the Page and Project combination is correct. |
| Storage or Objects operations behave unexpectedly | Separately check browser SDK support scope and the Public API specifications for each API. Separate browser-unsupported operations into server processing. |

For inquiries, record the Page name, access mode, SDK version, relative path, HTTP status, date and time, and reproduction steps. Do not attach Project API keys, cookies, authentication headers, or external-authentication tokens.

## Next articles

- [Execution Modes, Authentication, and Supported Scope](../development/execution-modes-and-auth.md)
- [Page Navigation and Deep Links](../development/page-navigation-and-deep-links.md)
- [Page Server Processing, Environment Variables, and Secret References](../development/page-server-env-and-secrets.md)
- [Agents, Conversations, and Artifacts API](../api/agents-conversations-artifacts-api.md) through [Project Members API](../api/project-members-api.md)
- [Tool Proxy API](../api/tool-proxy-api.md)
- [Manage Project API Keys](../operations/manage-project-api-keys.md)
- [Hosted Page Objects API](hosted-page-objects-api.md)
- [Hosted Page Storage API](hosted-page-storage-api.md)
- [Page Chat and Context Integration](page-chat-and-context.md)
- [Supported Scope of the Page Auxiliary APIs](page-auxiliary-api-scope.md)
