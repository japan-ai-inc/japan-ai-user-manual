# Supported Scope of the Page Auxiliary APIs

In addition to Agents, Objects, and Storage, the Page runtime includes routes related to speech transcription, Tools, Ontology, Workflows, Project members, sales activity data, and status notifications. However, implementation of a route alone does not mean it is a stable public feature available to Page browser code. Developers must separately check the route users actually call, the Page session, availability conditions for the organization and Project, and SDK support.

For common requirements for authentication and Page-relative APIs, see [Hosted Page Authentication and API Routes](hosted-page-auth-and-api-routes.md). For where to check communication and availability conditions, see [Index of Limits and Availability Conditions](../operations/limits-and-availability-index.md). [Tool Proxy API](../api/tool-proxy-api.md) is the source of truth for Tool specifications and is not repeated in this article.

## Related articles

- [Hosted Page Authentication and API Routes](hosted-page-auth-and-api-routes.md)
- [Tool Proxy API](../api/tool-proxy-api.md)
- [Workflows API](../api/workflows-api.md)
- [Project Members API](../api/project-members-api.md)
- [Execution Modes, Authentication, and Supported Scope](../development/execution-modes-and-auth.md)
- [Index of Limits and Availability Conditions](../operations/limits-and-availability-index.md)

## Scope of this article

This article covers auxiliary routes processed as Page-relative paths in the Page runtime. Operational and administrative Page runtime routes, such as builds, restarts, and administrator operations, are not treated as user Page features and are out of scope.

"Available" means the route can be called for users from Page browser code. "Conditional" means that settings, organization availability, authorization, or other requirements in addition to a Page session could be confirmed. "Could not be confirmed" means that proxy implementation alone could not establish the public browser contract, request format, or availability conditions. Do not work around this by expanding a Page's publication scope or adding an API key to the browser.

> The Page browser does not use a Project API key. In the current public version 0.11.0 of the Studio SDK, `createClient({ baseUrl: "" })` uses relative paths with the Page session. The SDK identifies `agents`, `datasets`, `objects`, `storage`, and `platform` for Page runtime browsers. Even when using the auxiliary routes in the table below, do not include keys, cookies, authentication headers, or Secret values in JavaScript, HTML, or logs.

## Supported scope

| Category and Page-relative path | Method | From the Page browser | Session and access-mode conditions | Confirmed limits and availability conditions |
|---|---|---|---|---|
| Speech `/api/speech/v1/transcription`, `/api/speech/transcribe` | HTTP methods are proxied. Supported methods and request and response formats for individual actions could not be confirmed. | Conditional | A valid Page session is required. In each of the "Sign-in required," "Sign-in optional," "Anonymous only," and "External customer auth" modes, speech-feature configuration is required in addition to being able to open the target Page. It could not be confirmed whether transcription availability is the same by mode. | Request bodies are 5 MiB or less; the whole route allows 25 requests per second and a burst of 200. It returns 503 when not configured and 400 when required speech settings are absent. |
| Tool `/api/tools/_catalog`, `/api/tools/{toolName}` | `GET` lists; `POST` runs. See [Tool Proxy API](../api/tool-proxy-api.md) for details. | Conditional | Requires a valid Page session, a Page setting that permits browser API use, user access conditions, and non-side-effect Tools permitted in the target Project. Verify the allowed scope with actual users for all four access modes. | Tool runs allow 10 requests per second and a burst of 50; bodies are 1 MiB or less. For details including list limits and caching, see [Tool Proxy API](../api/tool-proxy-api.md). |
| Ontology `/api/ontology...` | Routes under the prefix are proxied. This does not uniformly permit `GET`, `POST`, `PUT`, `PATCH`, or other methods based on the route alone. | Conditional | Requires a valid Page session and an Ontology integration configured in the target organization and Project. Availability by access mode, a user-facing API contract, and required authorization could not be confirmed. | Bodies are 32 MiB or less; the whole route allows 25 requests per second and a burst of 200. If the integration is not configured, treat it as unavailable. |
| Dataset reads `/api/v1/datasets...` | Use SDK `datasets.list()` and `datasets.search()`. Create, update, delete, document listing, and editing are outside the Page browser scope. | Conditional | Requires a valid Page session and `dataset:read`. `scope: "project"` targets Datasets linked to the Project; `scope: "organization"` targets only organization Datasets readable by signed-in users. | It returns 404 without a supporting Page runtime. Do not use Project API keys or anonymous users as alternatives to read organization Datasets. Search returns similar chunks, not generated answers. |
| Workflow `/api/v1/workflows...` | An SDK-compatible proxy route is registered. For direct API operation specifications, see [Workflows API](../api/workflows-api.md). | Could not be confirmed | A valid Page session is required for proxying. However, the SDK does not include `workflows` among modules available for Page browsers. Browser availability, conditions by access mode, and required authorization could not be confirmed. | Body and rate limits for all of `/api/v1` depend on individual operating settings. See [Workflows API](../api/workflows-api.md) for Workflow-specific limits on inputs, runs, and wait times, and do not confuse them with browser support conditions. |
| Project members `/api/v1/project-members` | `GET`. The SDK includes `studio.members.list()`. | Conditional | In addition to a valid Page session, Members API must be available in the organization and the calling principal must be permitted to read members. SDK documentation requires registration on an allowlist for Page browser use. Availability by access mode could not be confirmed. | Body and rate limits for all of `/api/v1` depend on individual operating settings. See [Project Members API](../api/project-members-api.md) for member-list search, pagination, and personal-information handling. |
| Room status `/api/agent-chat/status/{roomId}` | `GET` only | Conditional | Requires a valid Page session. `roomId` must be a UUID and usable as a conversation in the target Project. For normal conversation continuation, prioritize the SDK `sessionId` and Agent API; do not use status retrieval alone as the basis for new features. | Does not accept a body and has a 30-second request timeout. A route-specific rate limit could not be confirmed. |
| Page status stream `/api/status/stream` | `GET` Server-Sent Events | Outside the scope of user business APIs | It notifies Page build and startup status. It resolves the Page through Page identification information or a sharing code, but is not an alternative for business data, arbitrary processing, or authorization checks. | Connections are terminated after at most 10 minutes. Its availability for permanent business-UI real-time communication, reconnection count, and each access mode could not be confirmed. |

## 1. Determine availability first

1. Open the Page with the expected "Access settings" and user state.
2. Check whether it is a module identified for Page browsers by the SDK. Datasets are read-only. Do not implement `workflows` and sales activity data based only on the route.
3. For Speech, Ontology, Tools, and Members, check settings and authorization for the target organization and Project. Do not work around unconfigured states, 403, or 503 with different authentication information.
4. For conditional routes, verify only the smallest necessary read or run on actual systems in every access mode. For anonymous or external-customer Pages, do not assume personal information, internal Project information, or side-effecting processing.

> Even if `/api/v1/...` exists, SDK support in a Page browser, organization availability, and user permission are separate. In particular, Workflows and sales activity data could not be confirmed as public features usable from a Page browser.

## Common misunderstandings

### If there is a proxy route, any HTTP method can be called

No. Speech and Ontology proxy paths under their prefixes, but contracts for individual actions are separate. Room status supports only `GET`. Workflows, Members, and sales activity data connect to `/api/v1`, but availability depends on individual SDK support, authorization, and organization availability conditions.

### If a Page is anonymous, auxiliary APIs can also be used anonymously

No. Anonymous access is a condition for the entry point that opens a Page. Each route separately evaluates the Page session, target Project, organization settings, and authorization of the user or anonymous principal. Before adding internal data reads or updates to an anonymous screen, confirm rejection and success in the target state.

### The build status stream can be repurposed for business event notifications

No. This route notifies Page build and startup status. It could not be confirmed as a public API for conversation-status or business-data subscriptions, or arbitrary event delivery.

## When you cannot confirm

The request and response formats for individual speech transcription actions, the list of public Ontology actions, the user-facing API contract for sales activity data, Workflow use in the Page browser, and a complete authorization table by access mode for each route could not be confirmed. Do not implement them by inference. Record the target organization, Project, and Page; access mode; SDK version; relative path; HTTP method; date and time; HTTP status; and a response excluding secrets, then check with the administrator.

Do not include API keys, cookies, authentication headers, Secrets, sharing codes, audio data, conversation content, or members' personal information in inquiries. For where to check limits and availability conditions, see [Index of Limits and Availability Conditions](../operations/limits-and-availability-index.md).

## Checklist

- [ ] Did not embed Project API keys, cookies, authentication headers, or Secrets in the Page browser
- [ ] Checked availability conditions and authorization for the target organization and Project for Speech, Tools, Ontology, and Members
- [ ] Implemented Tool details according to [Tool Proxy API](../api/tool-proxy-api.md)
- [ ] Did not determine that Workflows and sales activity data support Page browsers merely because routes exist
- [ ] Confirmed the smallest necessary operations with the intended access modes and users
- [ ] Did not repurpose room status or the Page status stream as arbitrary business APIs or event infrastructure
- [ ] Distinguished 429, 400, 403, 503, and communication failures, and did not unconditionally resend requests that may change state

## Next articles

- [Hosted Page Authentication and API Routes](hosted-page-auth-and-api-routes.md)
- [Tool Proxy API](../api/tool-proxy-api.md)
- [Workflows API](../api/workflows-api.md)
- [Project Members API](../api/project-members-api.md)
- [Execution Modes, Authentication, and Supported Scope](../development/execution-modes-and-auth.md)
- [Index of Limits and Availability Conditions](../operations/limits-and-availability-index.md)
