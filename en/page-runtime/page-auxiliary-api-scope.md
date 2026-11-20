# Supported Scope of the Page Auxiliary APIs

In addition to Agents, Objects, and Storage, the Page runtime includes routes related to speech transcription, Tools, Ontology, Workflows, Project members, and status notifications. The existence of a route is different from being supported for use from Page browser code. Developers check the support status and conditions for each route in the table below before implementing.

For common requirements for authentication and Page-relative APIs, see [Hosted Page Authentication and API Routes](hosted-page-auth-and-api-routes.md). For where to check communication and availability conditions, see [Index of Limits and Availability Conditions](../operations/limits-and-availability-index.md). [Tool Proxy API](../api/tool-proxy-api.md) is the source of truth for Tool specifications and is not repeated in this article.

## Key points

- The Page browser calls relative paths with the Page session, without a Project API key. Calls through the Page session do not check Project API key scopes. Availability is determined by the Page session, organization and Project settings, and user permissions.
- Limits for `/api/v1/*` routes are fixed: request bodies up to **32 MiB**, and a rate of **25 requests per second with a burst of 200**.
- `members` (Project members) and `workflows` can technically be called because the Page runtime proxies them, but they are currently outside the beta support scope (subject to change without notice).

## Related articles

- [Hosted Page Authentication and API Routes](hosted-page-auth-and-api-routes.md)
- [Tool Proxy API](../api/tool-proxy-api.md)
- [Workflows API](../api/workflows-api.md)
- [Project Members API](../api/project-members-api.md)
- [Execution Modes, Authentication, and Supported Scope](../development/execution-modes-and-auth.md)
- [Index of Limits and Availability Conditions](../operations/limits-and-availability-index.md)

## Scope of this article

This article covers auxiliary routes processed as Page-relative paths in the Page runtime. Operational and administrative Page runtime routes, such as builds, restarts, and administrator operations, are not treated as user Page features and are out of scope.

The "From the Page browser" column means the following:

- **Available**: Can be called for users from Page browser code.
- **Conditional**: Requires organization or Project settings or user permissions in addition to a Page session.
- **Outside support scope**: Can technically be called, but is currently outside the beta support scope (subject to change without notice).
- **Not applicable**: Not used as a user business API.

Do not work around restrictions by expanding a Page's publication scope or adding an API key to the browser.

> In the current public version 0.11.0 of the Studio SDK, `createClient({ baseUrl: "" })` uses relative paths with the Page session. The SDK identifies `agents`, `datasets`, `objects`, `storage`, and `platform` for Page runtime browsers. Whichever route you use, do not include keys, cookies, authentication headers, or Secret values in JavaScript, HTML, or logs.

## Supported scope

| Category and Page-relative path | Method | From the Page browser | Session and access-mode conditions | Limits and availability conditions |
|---|---|---|---|---|
| Speech `/api/speech/v1/transcription`, `/api/speech/transcribe` | HTTP methods are proxied. Request and response formats for individual actions are not covered in this manual. | Conditional | Requires a valid Page session and speech-feature configuration. | Request bodies are 5 MiB or less; the whole route allows 25 requests per second and a burst of 200. Returns 503 when not configured and 400 when required speech configuration is missing. |
| Tool `/api/tools/_catalog`, `/api/tools/{toolName}` | `GET` lists; `POST` runs. See [Tool Proxy API](../api/tool-proxy-api.md) for details. | Conditional | Requires a valid Page session. Anonymous Page sessions receive 401. A Page setting that permits browser API use and Tools without side effects are also required. | Tool runs allow 10 requests per second and a burst of 50; bodies are 1 MiB or less. The rate limit is shared across the whole Page runtime, not per Page. Errors may be returned as plain text rather than in `{success,error}` format. See [Tool Proxy API](../api/tool-proxy-api.md) for details. |
| Ontology `/api/ontology...` | Routes under the prefix are proxied. This does not uniformly permit `GET`, `POST`, `PUT`, `PATCH`, or other methods based on the route alone. | Conditional | Requires a valid Page session and an Ontology integration configured in the target organization and Project. A user-facing API contract is not covered in this manual. | Bodies are 32 MiB or less; the whole route allows 25 requests per second and a burst of 200. If the integration is not configured, it is treated as unavailable. |
| Dataset reads `/api/v1/datasets...` | Use SDK `datasets.list()` and `datasets.search()`. Create, update, delete, document listing, and editing are outside the Page browser scope. | Conditional | Requires a valid Page session. `scope: "project"` targets Datasets linked to the Project; `scope: "organization"` targets only organization Datasets readable by signed-in users. | The fixed `/api/v1` limits (32 MiB, 25 requests per second, burst of 200) apply. Returns 404 if there is no supporting Page runtime. Do not use anonymous users to read organization Datasets. Search returns nearby chunks, not generated answers. |
| Workflow `/api/v1/workflows...` | Proxied by the Page runtime. For direct API operation specifications, see [Workflows API](../api/workflows-api.md). | Outside support scope | Can technically be called with a valid Page session, but is currently outside the beta support scope (subject to change without notice). The SDK does not include `workflows` among modules available for Page browsers. | The fixed `/api/v1` limits (32 MiB, 25 requests per second, burst of 200) apply. For Workflow-specific limits, see [Workflows API](../api/workflows-api.md). |
| Project members `/api/v1/project-members` | `GET`. The SDK includes `studio.members.list()`. | Outside support scope | Can technically be called with a valid Page session, but is currently outside the beta support scope (subject to change without notice). Members API must be available in the organization, and the calling principal must be permitted to read members. | The fixed `/api/v1` limits (32 MiB, 25 requests per second, burst of 200) apply. See [Project Members API](../api/project-members-api.md) for search, pagination, and personal-information handling. |
| Room status `/api/agent-chat/status/{roomId}` | `GET` only | Conditional | Requires a valid Page session. `roomId` must be a UUID and usable as a conversation in the target Project. For normal conversation continuation, prioritize the SDK `sessionId` and Agent API; do not use status retrieval alone as the basis for new features. | Does not accept a body and has a 30-second request timeout. |
| Page status stream `/api/status/stream` | `GET` Server-Sent Events | Not applicable | It notifies Page build and startup status. It resolves the Page through Page identification information or a sharing code, but is not an alternative for business data, arbitrary processing, or authorization checks. | Connections are terminated after at most 10 minutes. Do not use it for real-time communication in business UIs. |

## 1. Determine availability first

1. Open the Page with the intended "Access Settings" and user state.
2. Check whether the module is one the SDK identifies for Page browsers. Datasets are read-only. `workflows` and `members` are outside the support scope, so do not make production features depend on them.
3. For Speech, Ontology, and Tools, check the target organization and Project settings and permissions. Do not work around unconfigured states, 401, 403, or 503 with other credentials.
4. For conditional routes, verify only the minimum necessary reads and runs on actual devices in each access mode. For anonymous or external-customer Pages, do not assume personal information, internal Project information, or processing with side effects.

> Being proxied under `/api/v1/...` is different from SDK support in Page browsers, availability in the organization, and user permission.

## Common misunderstandings

### If there is a proxy route, any HTTP method can be called

No. Speech and Ontology proxy routes under their prefixes, but contracts for individual actions are separate. Room status is `GET` only. Workflows and Members are proxied but are outside the support scope.

### If a Page is anonymous, auxiliary APIs can also be used anonymously

No. Anonymous access is a condition for the entry point that opens the Page. For example, Tools return 401 for anonymous Page sessions. Each route separately evaluates the Page session, target Project, organization settings, and the permissions of the user or anonymous principal. Before adding reads or updates of internal data to an anonymous screen, verify rejection and success in the target state.

### Project API key scopes can restrict what can be called

No. Calls through the Page session do not check key scopes. In addition to not placing keys in the browser, control displayed data and executable operations through access settings and user permissions.

### The build status stream can be repurposed for business event notifications

It cannot. This route notifies Page build and startup status. Do not use it for subscribing to conversation status or business data, or for delivering arbitrary events.

## If it does not work

Speech request bodies and response formats by action, and the list of Ontology actions, are not covered in this manual. Do not implement them based on guesses; record the target organization, Project, Page, access mode, SDK version, relative path, HTTP method, occurrence time, HTTP status, and response excluding secrets, and check with the administrator.

Do not include API keys, cookies, authentication headers, Secrets, sharing codes, audio data, conversation content, or members' personal information in inquiries. For where to check limits and availability conditions, see [Index of Limits and Availability Conditions](../operations/limits-and-availability-index.md).

## Checklist

- [ ] Did not embed a Project API key, cookies, authentication headers, or Secrets in the Page browser
- [ ] Checked organization and Project availability conditions and permissions for Speech, Tools, and Ontology
- [ ] Implemented Tool details according to [Tool Proxy API](../api/tool-proxy-api.md), handling 401 for anonymous sessions and plain-text errors
- [ ] Understood that `workflows` and `members` are outside the support scope and did not make production features depend on them
- [ ] Checked minimum necessary operations for intended access modes and users
- [ ] Did not repurpose room status or the Page status stream as an arbitrary business API or event platform
- [ ] Distinguished 429, 400, 401, 403, 503, and communication failures, and did not unconditionally resend requests that may change state

## Next articles

- [Hosted Page Authentication and API Routes](hosted-page-auth-and-api-routes.md)
- [Tool Proxy API](../api/tool-proxy-api.md)
- [Workflows API](../api/workflows-api.md)
- [Project Members API](../api/project-members-api.md)
- [Execution Modes, Authentication, and Supported Scope](../development/execution-modes-and-auth.md)
- [Index of Limits and Availability Conditions](../operations/limits-and-availability-index.md)
