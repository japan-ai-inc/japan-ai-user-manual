# Objects API

The Objects API is a public REST API for operating Object definitions, fields, and records in a Project over HTTP. For structure and deletion, see [Object Specifications and Limits](../build/object-specs-and-limits.md); for the SDK, see [SDK Objects](../development/sdk-objects.md).

## Related articles

- [SDK Objects](../development/sdk-objects.md)
- [Object Specifications and Limits](../build/object-specs-and-limits.md)
- [Common API and SDK Errors, Timeouts, and Limits](../development/api-sdk-errors-timeouts-limits.md)
- [Manage Project API Keys](../operations/manage-project-api-keys.md)

## Before you begin

The base URL is `https://api.japan-ai.co.jp`, and the common path is `/api/v1/objects`. Grant `object:read`, `object:write`, or `object:delete` according to the operation.

For authentication, enter the actual value in the `x-api-key` header. `Authorization: Bearer <key>` is also accepted. Do not include keys in browsers, code, logs, or support inquiries.

```bash
export JAI_STUDIO_API_KEY='(Project API key)'
export JAI_OBJECT_ID='(Object ID)'
curl -sS "https://api.japan-ai.co.jp/api/v1/objects" \
  -H "x-api-key: $JAI_STUDIO_API_KEY"
```

> Object names and field names must begin with an alphabetic character and contain only alphanumeric characters and underscores. Check structure and deletion in [Object Specifications and Limits](../build/object-specs-and-limits.md).

## Scope of this article

This article covers definitions, fields, records, search, and bulk creation called with a Project API key. CSV, SQL, permission settings, and structural design are out of scope. Structural changes to Objects provided by an App may return 403.

## Common responses and pagination

The main list and search response fields are `data`, `total`, `limit`, `cursor`, `hasNextPage`, and `queryTimestamp`. For the next page, pass `cursor` using the same conditions and sort order. `limit` and `offset` are numeric strings; `include_deleted` is `true` or `false`.

On success, `GET`, `PATCH`, and search return 200; `POST` returns 201; and deletion returns 204. Invalid input returns 400, insufficient authentication, scope, or permission returns 403, a missing target returns 404, and an `expectedRevision` mismatch returns 409. If a read returns 429, follow `retryAfterSeconds` and `Retry-After`. For retries and errors, see [Common API and SDK Errors, Timeouts, and Limits](../development/api-sdk-errors-timeouts-limits.md).

## 1. Manage Object definitions

| Operation | Method and path | Required scope | Main parameters and response |
|---|---|---|---|
| List | `GET /api/v1/objects` | `object:read` | query: `limit`, `offset`, `cursor`, `include_deleted` (all optional). Paginated response. |
| Create | `POST /api/v1/objects` | `object:write` | body: required `objectName`; optional `displayName`, `description`. Returns the Object definition. |
| Retrieve | `GET /api/v1/objects/{objectId}` | `object:read` | path: required `objectId`. Returns the Object definition. |
| Update | `PATCH /api/v1/objects/{objectId}` | `object:write` | body: optional `objectName`, `displayName`, `description`. The latter two can be cleared with `null`. Returns the Object definition. |
| Delete | `DELETE /api/v1/objects/{objectId}` | `object:delete` | path: required `objectId`. Returns 204 with no body on success. |

The response includes `objectId`, `objectName`, `displayName`, `description`, `createdAt`, and `updatedAt`.

## 2. Manage fields

| Operation | Method and path | Required scope | Main parameters and response |
|---|---|---|---|
| List | `GET /api/v1/objects/{objectId}/fields` | `object:read` | The query is the same as the Object list. Returns field definitions as an array or paginated response. |
| Create | `POST /api/v1/objects/{objectId}/fields` | `object:write` | body: required `fieldName`, `displayName`, `dataType`, `fieldNum`. `fieldNum` is an integer of 1 or greater. |
| Update | `PATCH /api/v1/objects/{objectId}/fields/{fieldId}` | `object:write` | body: fields to change, including display name, flags, reference target, default value, range, and validation rules. |
| Delete | `DELETE /api/v1/objects/{objectId}/fields/{fieldId}` | `object:delete` | Returns 204 with no body on success. |

`dataType` values are `text`, `string`, `number`, `boolean`, `datetime`, `date`, `json`, `array`, `relationship`, `file`, `select`, `multiselect`, `url`, `email`, `phone`, and `auto_increment`. `maxLength` is an integer from 1 to 65,535. The response includes `fieldId`, `fieldName`, `displayName`, `dataType`, `fieldNum`, each `is...` flag, `refObjectId`, `refFieldId`, `defaultValue`, and `validationRules`.

`validationRules` is completely replaced as an array, cleared with an empty array, and retained if omitted.

## 3. Manage records

A record body is `{"data": {"field name": value}}`. Keys in `data` are not converted. The response includes `recordId`, `data`, `revision`, `createdAt`, and `updatedAt`.

| Operation | Method and path | Required scope | Parameters and success result |
|---|---|---|---|
| List | `GET /api/v1/objects/{objectId}/records` | `object:read` | The query is the same as the common list. Paginated response. |
| Create | `POST /api/v1/objects/{objectId}/records` | `object:write` | body: required `data`. Created record. |
| Retrieve | `GET /api/v1/objects/{objectId}/records/{recordId}` | `object:read` | path: required `recordId`. Record. |
| Update | `PATCH /api/v1/objects/{objectId}/records/{recordId}` | `object:write` | body: required `data`, optional `expectedRevision`. Record. |
| Delete | `DELETE /api/v1/objects/{objectId}/records/{recordId}` | `object:delete` | query: optional `expectedRevision`. 204 with no body on success. |

The `data` in `PATCH` is not a partial update; it is a **complete replacement**. Include the fields to retain. `expectedRevision` is an integer from 1 to 2,147,483,647. Pass the retrieved `revision` in the update body or delete query; a mismatch returns 409. When omitted, updates are last-write-wins and deletion is unconditional. On conflict, retrieve the latest record.

> SDK 0.11.0 has `objects.define(objectId).bulkDelete(recordIds)`, but as of 2026-11-11, availability of the corresponding bulk delete route (`DELETE /api/v1/objects/{objectId}/records/bulk`) in the production public REST API could not be confirmed. It is implemented in a development version; after it is available in production, `object:delete` can delete 1-1,000 items, and failure to delete even one causes all items to fail. In the public REST API, use `DELETE /api/v1/objects/{objectId}/records/{recordId}` for each target. Distinguish it from Objects routes in PRS.

```bash
curl -sS -X PATCH \
  "https://api.japan-ai.co.jp/api/v1/objects/$JAI_OBJECT_ID/records/RECORD_ID" \
  -H "x-api-key: $JAI_STUDIO_API_KEY" -H 'Content-Type: application/json' \
  -d '{"data":{"name":"Hanako Yamada","email":"hanako@example.example"},"expectedRevision":7}'
```

## 4. Search and bulk creation

| Operation | Method and path | Required scope | Body and defaults |
|---|---|---|---|
| Search | `POST /api/v1/objects/{objectId}/records/search` | `object:read` | `filters`, `sort`, `limit`, `offset`, `cursor`, and `includeDeleted` are optional. `limit` defaults to 50 (1-1,000), `offset` defaults to 0, and `includeDeleted` defaults to `false`. Paginated response. |
| Bulk create | `POST /api/v1/objects/{objectId}/records/bulk` | `object:write` | body: `[{"data": {...}}, ...]`. At least 1 and **at most 1,000** items. Returns an array of created records or its `data` array. |

A filter has `field`, `operator`, and optional `value`. `operator` values are `eq`, `ne`, `gt`, `gte`, `lt`, `lte`, `contains`, `startswith`, `endswith`, `in`, `notin`, `isnull`, `isnotnull`, `exists`, `regex`, `between`, and `jsoncontains`. `sort` has `field` and optional `direction`, which defaults to `asc`. `cursor` takes precedence over `offset`.

## 5. Manage record access rules

Record access rules are an API for managing view and edit scope for each Object. In the SDK, use `objects.recordRules.list|create|delete`.

| Operation | Method and path | Required scope |
|---|---|---|
| List | `GET /api/v1/objects/{objectId}/record-rules` | `object:read`, `object:policy` |
| Create | `POST /api/v1/objects/{objectId}/record-rules` | `object:write`, `object:policy` |
| Delete | `DELETE /api/v1/objects/{objectId}/record-rules/{ruleGroupId}` | `object:write`, `object:policy` |

The key creator must also have permission to read and configure policies for the target Object. At creation, specify `name`, `status` (`hidden`, `read`, `edit`), `conditions`, and `principals`; specify `conditionLogic` (`AND` or `OR`) and `priority` when needed. `principals` can specify `role`, `user`, and `agent`. Check existing rules and priorities and the impact on users when deleting before making changes.

## Troubleshooting

| Situation | What to check and do |
|---|---|
| 400 | Check JSON, required fields, names, `fieldNum`, search conditions, and bulk creation of 1,000 items or fewer. |
| 403 | Check key format and validity, target Project, required `object:*` scopes, and permissions. |
| 404 or 409 | Check the ID or `expectedRevision` and the latest record before operating. |
| 422 | A list or search result may be too large. Reduce `limit` or narrow the conditions. If only one item is too large, ask a Project administrator to review its content. |
| Record deletion or access-rule change fails | Check the target ID, `object:delete` or `object:policy`, the key creator's Object permissions, and the impact of existing rules, relationships, and the target. Do not retry a write with unknown state as-is. |
| 429 | Follow `retryAfterSeconds` and `Retry-After`; retry fewer times after waiting. |

> If a write times out, do not retry unconditionally; check the current state.

## Checklist

- [ ] Stored the API key in secure server-side configuration and granted the minimum `object:*` scopes
- [ ] Checked Object name, field name, type, and relationship conditions in [Object Specifications and Limits](../build/object-specs-and-limits.md)
- [ ] Passed `data` including all fields to retain when updating a record
- [ ] Handled `revision` and `expectedRevision` in processes with concurrent updates
- [ ] Split bulk creation into batches of 1,000 items or fewer. When deleting records through the public REST API, used the delete route for each target
- [ ] Checked existing rules, priorities, and the impact on users when changing record access rules
- [ ] Carried forward the cursor with the same conditions and sort order in a search

## Next article

- [SDK Objects](../development/sdk-objects.md)
- [Object Specifications and Limits](../build/object-specs-and-limits.md)
- [Common API and SDK Errors, Timeouts, and Limits](../development/api-sdk-errors-timeouts-limits.md)
- [Manage Project API Keys](../operations/manage-project-api-keys.md)
