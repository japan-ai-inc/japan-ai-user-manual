# Tool Proxy API

This route runs tools directly from a Page. It is a **route that can be called only from within the Page runtime environment**; it is not a replacement for the public REST API or Project API keys. Browser code in a Page calls the relative URL using `fetch`. The Studio SDK has no public client that wraps this route.

## Related articles

- [Execution Modes, Authentication, and Supported Scope](../development/execution-modes-and-auth.md)
- [Manage External Tools, Data Integrations, and MCP](../operations/manage-external-tools-integrations-mcp.md)
- [Common API and SDK Errors, Timeouts, and Limits](../development/api-sdk-errors-timeouts-limits.md)
- [Page Specifications and Limits](../build/page-specs-and-limits.md)

## Scope of this article

Tool Proxy is a route through which the **Page runtime environment (PRS)** obtains organization, Project, and user context from the session associated with the Page and passes it to the tool execution platform. It is not a Project API key endpoint of the production Public API (`https://api.japan-ai.co.jp`). The distributed SDK 0.11.0 also has no dedicated client for this route. Therefore, do not use it for the following purposes:

- Calling `https://api.japan-ai.co.jp` from an external app with a Project API key
- Creating a public API for server-to-server integration
- Running tools with side effects from a Page

> You do not need to set a Project API key, `x-api-key`, or `Authorization: Bearer` in browser requests to this route. You also cannot switch the user's organization, Project, or user by specifying them in a request body or header.

## Before you begin

1. Make the Page ready to publish and run. After Page access succeeds, `/api/*` is handled by the authenticated session associated with the Page.
2. Open it as a user who meets the Page access conditions. Requests with no authentication, invalid authentication, or a nonmatching Page, organization, and Project combination are rejected.
3. Confirm that the Page API access setting permits calls from the browser. Tool Proxy calls from a browser are unavailable for a Page configured for server use only.
4. A Page that uses external access requires a session valid for that access mode. A session that no longer meets the access conditions is not processed.

## API specification

### Check the tool catalog

| Item | Details |
|---|---|
| Method and path | `GET /api/tools/_catalog` |
| Authentication | A valid Page runtime environment session |
| Request | No body |
| Success | `200`. A JSON array. Each item contains `name`, `description`, `params_schema`, `output_type`, `side_effects`, and `cache_ttl` |
| Scope | Tools that the tool execution platform can resolve for the organization and Project of that Page session. Check the catalog's `side_effects` before use. |

The catalog may be cached briefly for the organization and Project associated with the Page. Therefore, retrieving the catalog does not guarantee that subsequent execution will always succeed.

```ts
const response = await fetch('/api/tools/_catalog');
if (!response.ok) throw new Error(`catalog: ${response.status}`);
const tools = await response.json();
```

### Run a tool

| Item | Details |
|---|---|
| Method and path | `POST /api/tools/{toolName}` |
| `toolName` | Specify the catalog `name` in the URL path. An empty name is not allowed. |
| Content-Type | `application/json` |
| Body | `{"params": { ... }}`. If `params` is omitted, it is treated as an empty object. |
| Body limit | 1 MiB |
| Success | `200`. Returns `success`, `tool`, `data`, and, as applicable, `files` and `images`. |
| Response limit | The body received from the tool execution platform is limited to 10 MiB. An oversized body may be truncated and JSON parsing may fail. |

The keys, types, and required fields of `params` differ by tool. Before running a tool, refer to its catalog `params_schema` and validate user input.

`files` and `images` are references included when the tool returns artifacts.

```ts
const toolName = 'name confirmed in the catalog';
const response = await fetch(`/api/tools/${encodeURIComponent(toolName)}`, {
  method: 'POST',
  headers: { 'Content-Type': 'application/json' },
  body: JSON.stringify({ params: { /* tool-specific arguments */ } }),
});
const result = await response.json();
if (!response.ok || !result.success) throw new Error(result.error?.message);
```

## Available tools and caching

A tool can run when it can be resolved by name and the execution platform determines that it has `none` side effects for those arguments. This is determined from the arguments at execution time as well as the catalog `side_effects`; if it is determined to have side effects, the response is `403` and `TOOL_SIDE_EFFECTS_BLOCKED`. For processing such as sending or updating, use an Agent or Workflow designed with approval and connections rather than this route.

Tool execution is limited to 10 per second with a burst of 50. Exceeding the limit returns `429`. If the tool returns a cache duration, results for the same organization, Project, tool, and arguments may be reused. Per-user results are separated by user; if a stable user identifier cannot be obtained, no per-user cache is written. The response `X-Cache` is `HIT` or `MISS`.

> Even for cacheable responses, do not keep personal information or information that must be current on the screen for a long time. The cache duration depends on the tool implementation and cannot be specified by the caller.

## Troubleshooting

| Status | What to check and do |
|---|---|
| `400` | The JSON is invalid or `toolName` is empty. Check `Content-Type` and the result of `JSON.stringify`. |
| `401` | There is no Page session or it is not valid. Reopen the Page and check the access conditions. |
| `403` | Check the relationship to the Page, trusted Origin, Page API access conditions, and side-effect determination. |
| `404` | The specified tool was not found. Retrieve `name` again from the catalog. |
| `413` | The body exceeds 1 MiB. Reduce input or attached data, and consider another storage method if necessary. |
| `429` | Reduce rapid repeated tool calls and implement waiting and retries in the UI. For the common policy, see [Common API and SDK Errors, Timeouts, and Limits](../development/api-sdk-errors-timeouts-limits.md). |
| `502`, `503`, or `504` | The tool execution platform or authentication or connection may be temporarily unavailable. Show the user that a retry is possible. |
| `500` | Tool resolution or execution failed. Record `error.code` and `error.message`, and contact an administrator. |

The execution platform response returns `success`, `tool`, `data`, and, as applicable, `files` and `images` on success. On failure, it returns `success: false`, `tool`, and `error: { code, message }`. Evaluate it together with the HTTP status.

## Checklist

- [ ] Use the relative path `/api/tools/...` within the Page runtime environment
- [ ] Build input based on the catalog `name` and `params_schema`
- [ ] Use JSON for the `POST` body and keep it to 1 MiB or less
- [ ] Select only tools without side effects for the purpose
- [ ] Distinguish `401`, `403`, `429`, and temporary `5xx` in the UI
- [ ] Do not try to call this route externally with a Project API key

## Next article

- [Execution Modes, Authentication, and Supported Scope](../development/execution-modes-and-auth.md)
- [Manage External Tools, Data Integrations, and MCP](../operations/manage-external-tools-integrations-mcp.md)
- [Page Specifications and Limits](../build/page-specs-and-limits.md)
