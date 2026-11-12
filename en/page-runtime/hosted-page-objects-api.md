# Hosted Page Objects API

In Hosted Page browser code, you can use the Studio SDK `objects` module to operate Object definitions, fields, and records in the Project to which the Page belongs. This integration uses relative paths in the Page runtime; it differs from calling the public REST API with a Project API key. For Object structures, constraints, and effects of deletion, see [Object Specifications and Limits](../build/object-specs-and-limits.md); for the standard HTTP API, see [Objects API](../api/objects-api.md); and for SDK methods and types, see [SDK Objects](../development/sdk-objects.md).

## Related articles

- [Objects API](../api/objects-api.md)
- [SDK Objects](../development/sdk-objects.md)
- [Execution Modes, Authentication, and Supported Scope](../development/execution-modes-and-auth.md)
- [Object Specifications and Limits](../build/object-specs-and-limits.md)
- [Page Specifications and Limits](../build/page-specs-and-limits.md)

## Before you begin

- This method applies only to **browser code** running in the Page runtime. In current public SDK version 0.11.0, `objects` is included among modules for Page browsers.
- Do not configure, read, or send a Project API key in the Page browser. Initializing the SDK with `baseUrl: ""` makes the browser call same-origin relative paths for the Page.
- Check the target Object ID, field names, types, and record access settings. `T` in `define<T>()` is a TypeScript type and does not replace Object input constraints or permissions.

> Do not include API keys, cookies, or authentication headers in Page JavaScript, public environment variables, logs, or inquiries. For integration from an external server or a direct API using a key, follow the execution modes in [Objects API](../api/objects-api.md) and [Execution Modes, Authentication, and Supported Scope](../development/execution-modes-and-auth.md), not this article.

```ts
import { createClient } from "@japan-ai-inc/studio-sdk";

type Contact = { name: string; email: string; company?: string };

const studio = createClient({ baseUrl: "" });
const contacts = studio.objects.define<Contact>("OBJECT_ID");
```

## Routes used in the Page runtime

The SDK Objects API sends from a Page to relative paths rooted at `/api/v1/objects`. Browser code cannot specify organization ID, Project ID, or user ID in a path, body, or header to switch context. Processing uses the Project and effective principal of the valid session associated with the Page.

| Target | SDK methods | Page-relative paths and methods |
|---|---|---|
| Object definitions | `list()`, `get()`, `create()`, `update()`, `delete()` | `GET` and `POST /api/v1/objects`; `GET`, `PATCH`, and `DELETE /api/v1/objects/{objectId}` |
| Records | `define<T>()`, `list()`, `get()`, `create()`, `update()`, `delete()`, `bulkCreate()`, `bulkDelete()` | `GET` and `POST /api/v1/objects/{objectId}/records`; `GET`, `PATCH`, and `DELETE /api/v1/objects/{objectId}/records/{recordId}`; `POST` and `DELETE /records/bulk` |
| Search | `search()` | In the SDK PRS browser, search conditions are sent as query strings to `GET /api/v1/objects/{objectId}/records`. |
| Fields | `listFields()`, `createField()`, `updateField()`, `deleteField()` | `GET` and `POST /api/v1/objects/{objectId}/fields`; `PATCH` and `DELETE /api/v1/objects/{objectId}/fields/{fieldId}` |

`/api/records` is not registered as an Objects API. Do not conflict or confuse it with another API provided by the Page itself. `/api/custom-objects/...` is a separate compatibility route that validates a format including organization and Project, but it is not the route used by SDK `objects`. To handle Objects in browser implementations for new Pages, use `/api/v1/objects...` or the SDK above.

> Even when calling the same route as the SDK with direct `fetch()`, use only relative paths and do not add an API key or headers identifying a principal. However, normally prioritize the SDK because it handles PRS search conversion, response-key normalization, and error types.

## 1. Read and write records

The following example creates, retrieves, fully updates, and deletes a record. `update()` is not a partial update. Pass `data` containing all regular fields you want to retain. Before implementation, check [Object Specifications and Limits](../build/object-specs-and-limits.md) for the effects of deletion and structure changes.

```ts
const created = await contacts.create({
  name: "Hanako Yamada", email: "hanako@example.example",
});
const current = await contacts.get(created.recordId);

const updated = await contacts.update(current.recordId, {
  ...current.data,
  company: "JAPAN AI",
});

await contacts.delete(updated.recordId);
```

When you need to detect concurrent updates, pass the retrieved `revision` as `expectedRevision`. A mismatch returns 409. An update that omits `expectedRevision` is last-write-wins, and deletion is unconditional. `bulkCreate()` accepts up to 1,000 records per call. `bulkDelete(recordIds)` also accepts up to 1,000 items and fails without deleting any records if even one record cannot be deleted. Check nonexistent IDs in the result's `skippedIds`. If the deletion result is unknown, first check the target state.

## 2. Implement search and pagination

You can pass `filters`, `sort`, `limit`, `offset`, `cursor`, `includeDeleted`, and `includeCount` to `search()`. You can also pass `includeCount` to `list()`. For screens that do not need counts, specifying `includeCount: false` omits calculation of the total matching records and makes `total` 0. `hasNextPage` and `cursor` remain available, so for the next page, pass the returned `cursor` with the same conditions and sort order.

```ts
const page = await contacts.search({
  filters: [{ field: "company", operator: "contains", value: "AI" }],
  sort: [{ field: "name", direction: "asc" }],
  limit: 50,
});

const next = page.hasNextPage && page.cursor
  ? await contacts.search({
      filters: [{ field: "company", operator: "contains", value: "AI" }],
      sort: [{ field: "name", direction: "asc" }],
      limit: 50, cursor: page.cursor,
    })
  : undefined;
```

For PRS browser searches, the query string created by the SDK must be **8,000 characters or fewer**. If it exceeds that limit, `ObjectsApiError` (status 400) occurs before the request is sent. This is not a limit on search-result count or Object character count. Shorten it by reducing filters and sort orders or dividing the search into stages.

## Access modes and permissions

The Objects API requires a valid Page session. Being able to open a Page and being able to operate all Object data are separate. Permissions for Objects and records in the Project for the effective principal, Object definitions, and access rules ultimately determine availability. If Page API access from the browser is not allowed by configuration, the browser Objects API cannot be used.

| Mode in "Access settings" | Objects API from the browser |
|---|---|
| "Sign-in required" | Requires a session for a signed-in user who can access the Page. |
| "Sign-in optional" | Signed-in users use their session. For signed-out users, it is called when a Page session for anonymous access is established. |
| "Anonymous only" | It is called when a Page session for anonymous access is established. A public URL alone does not permit arbitrary Object operations. |
| "External customer auth" | Requires a Page session that completed external customer authentication. You cannot substitute an ordinary Page session or spoof authentication information from the browser. |

For Pages displayed with anonymous access, signed-out users in sign-in-optional mode, or external customer authentication, minimize record permissions and test that changing only an Object ID or record ID on screen cannot reach other data. For Page access modes, public URLs, and access settings, see [Page Specifications and Limits](../build/page-specs-and-limits.md).

## If something does not work

| Situation | What to check and do |
|---|---|
| 401 | There is no Page session, it has expired, or an effective principal required for the Object request cannot be established. Reopen the Page and check the access mode and authentication completion state. |
| 403 | Check the Page, organization, and Project relationship; Object or record permissions; access mode; and Origin/Referer for writes. Publishing a Page alone does not increase permissions. |
| 404 | Check `/api/v1/objects...`, Object ID, record ID, and field ID. Do not replace it with `/api/records` or `/api/custom-objects`. |
| 409 | Check `expectedRevision` and the latest record. Do not resend with the same value; check the latest value and the business-correct change. |
| 400 | Check Object and field input conditions, search conditions, or the 8,000-character PRS search limit. |
| 429, 5xx, or communication failure | Avoid rapid consecutive calls and retry only the minimum necessary after reading. For a failed write, do not resend unconditionally; retrieve the current state and check for duplicates and conflicts. |

SDK HTTP failures, communication failures, and JSON parsing failures can be handled as `ObjectsApiError`. You can use `statusCode` and `responseBody` to separate screen messages, but do not display or record them as-is because response bodies may contain personal information, cookies, or authentication information.

## Checklist

- [ ] Used `createClient({ baseUrl: "" })` in the Page browser and did not include an API key
- [ ] Used `/api/v1/objects...` or SDK `objects` for Object operations and do not depend on `/api/records`
- [ ] Passed complete `data` including fields to retain to `update()`
- [ ] Kept search queries to 8,000 characters or fewer, considered `includeCount: false` for screens that do not need counts, and carried cursors forward with the same conditions and sort order
- [ ] For bulk deletion, checked target IDs, the maximum of 1,000 items, that no records are deleted on failure, and state after deletion
- [ ] Confirmed only permitted reads, writes, and deletions for users in each access mode
- [ ] Minimized Object and record permissions and data displayed on screens for anonymous or external customers

## Next articles

- [Objects API](../api/objects-api.md)
- [SDK Objects](../development/sdk-objects.md)
- [Execution Modes, Authentication, and Supported Scope](../development/execution-modes-and-auth.md)
- [Object Specifications and Limits](../build/object-specs-and-limits.md)
- [Page Specifications and Limits](../build/page-specs-and-limits.md)
