# SDK Objects

The Studio SDK `objects` module lets you work with Object definitions, fields, and records from TypeScript. `define<T>()` binds the data shape of records to a type, allowing you to handle inputs and outputs for creation, retrieval, updates, and searches. Types do not replace validation of actual data, permissions, or Object constraints.

Use [Object Specifications and Limits](../build/object-specs-and-limits.md) as the source of truth for constraints. SQL and direct HTTP API calls are outside this article's scope.

## Related articles

- [Object Specifications and Limits](../build/object-specs-and-limits.md)
- [Objects API](../api/objects-api.md)
- [Object SQL DDL](../object-sql/object-sql-ddl.md)
- [Execution Modes, Authentication, and Supported Scope](execution-modes-and-auth.md)
- [Manage Project API Keys](../operations/manage-project-api-keys.md)

## Before you begin

- SDK/CLI use with Node.js requires version 22 or later.
- Pass API keys from secure environment variables; do not place them in browsers, public environment variables, or logs. In a PRS browser, do not configure an API key and initialize with `baseUrl: ""`.
- Check the Object ID, field names, and types, and check affected records and integration destinations before structural changes. Follow [Object Specifications and Limits](../build/object-specs-and-limits.md) for constraints.

```bash
npm install @japan-ai-inc/studio-sdk
```

```ts
import { createClient } from "@japan-ai-inc/studio-sdk";

const apiKey = process.env["JAI_STUDIO_API_KEY"];
if (!apiKey) throw new Error("Project API key is not configured");
const studio = createClient({ baseUrl: "https://api.japan-ai.co.jp", apiKey });
```

> API keys are confidential information. Do not include actual values in samples, exception messages, inquiries, or test records. CLI profile configuration is not read by SDK `createClient()`.

## 1. Check an Object and create a typed handle

For listing, retrieving, creating, updating, and deleting Object definitions, use `list()`, `get()`, `create()`, `update()`, and `delete()` respectively.

For record operations, create `studio.objects.define<T>(objectId)`. `T` applies to `data` in `RecordItem<T>` and the data portion of `create()` and `update()`. `recordId` and `revision` are outside `data`.

```ts
type Contact = { name: string; email: string; company?: string };

const definition = await studio.objects.get("contacts");
const contacts = studio.objects.define<Contact>(definition.objectId);

const added = await studio.objects.create({
  objectName: "contacts",
  displayName: "Customer contact",
});
await studio.objects.update(added.objectId, { description: "Sales contact" });
```

For `list()`, you can specify `limit`, `offset`, `cursor`, `includeDeleted`, and `includeCount`. When you specify `includeCount: false` in record lists or `search()`, it omits aggregation of matching counts and `total` in the response is 0. You can use pagination `hasNextPage` and `cursor` as-is. Continue using the same setting on every page when using a cursor without aggregation.

## 2. Create, retrieve, update, and delete records

`create(data)` creates a record, and `get(recordId)` retrieves one. `update(recordId, data)` is not a partial update; it **replaces the complete `data`**. If you omit ordinary fields, they are deleted on the server side, so pass all fields that you want to keep.

```ts
const created = await contacts.create({
  name: "Hanako Yamada", email: "hanako@example.example",
});
const current = await contacts.get(created.recordId);
const updated = await contacts.update(current.recordId, {
  ...current.data, company: "JAPAN AI",
});
await contacts.delete(updated.recordId);
```

For multiple records, use `bulkCreate(records)`, with up to **1,000 records** per request. This is the same as the CSV import and internal bulk-processing unit in [Object Specifications and Limits](../build/object-specs-and-limits.md). Split large insertions with duplicates in mind.

```ts
await contacts.bulkCreate([
  { name: "Ichiro Sato", email: "ichiro@example.example" },
  { name: "Jiro Suzuki", email: "jiro@example.example" },
]);
```

> `delete()` is not a method that provides a restoration procedure. Before execution, check deletion impacts and screen conditions for Objects, fields, and records in [Object Specifications and Limits](../build/object-specs-and-limits.md).

### Delete multiple records

`bulkDelete(recordIds)` (SDK 0.10.0 or later) deletes up to 1,000 records in one request on a connection that has the corresponding public API. The key requires the `object:delete` scope. If even one record cannot be deleted, it fails without deleting any records. IDs that no longer exist are skipped; check `skippedIds` and `skippedCount` in the result.

SDK 0.11.0 sends `DELETE /api/v1/objects/:objectId/records/bulk` with a body in the form `[{ "recordId": "..." }]`. The SDK does not send an empty array and returns a zero-item result. As of 2026-11-11, the production public REST API is confirmed to provide bulk **creation** (`POST`) at the same path, but this bulk deletion has not been confirmed as provided. It is implemented in a development version, so the timing of availability differs by connection. Before use, confirm that the connection provides this deletion route.

```ts
const result = await contacts.bulkDelete(["record-id-1", "record-id-2"]);
console.log(result.deletedIds, result.deletedCount);
console.log(result.skippedIds, result.skippedCount);
```

If communication fails and the result is unknown, do not immediately send the same deletion again; reconfirm the target records.

### Detect concurrent updates

When `revision` is available, you can pass its value as `expectedRevision` to updates and deletions. If the record was updated, the operation fails with HTTP 409 Revision conflict. If there is no `revision`, do not request a compare-and-update.

```ts
const record = await contacts.get("record-id");
if (record.revision === undefined) throw new Error("Cannot retrieve revision");

await contacts.update(
  record.recordId,
  { ...record.data, company: "Updated company name" },
  undefined,
  { expectedRevision: record.revision },
);
```

An update without `expectedRevision` is last-write-wins, and a deletion is unconditional. In a conflict, check the latest record and the business-correct value.

## 3. Search with conditions, ordering, and pagination

You can pass `filters`, `sort`, `limit`, `offset`, `cursor`, and `includeDeleted` to `search()`. A filter has `field`, `operator`, and, as needed, `value`.

```ts
const page = await contacts.search({
  filters: [
    { field: "company", operator: "contains", value: "AI" },
    { field: "email", operator: "isnotnull" },
  ],
  sort: [{ field: "name", direction: "asc" }],
  limit: 50,
});

if (page.hasNextPage && page.cursor) {
  await contacts.search({
    filters: [{ field: "company", operator: "contains", value: "AI" }],
    sort: [{ field: "name", direction: "asc" }],
    limit: 50,
    cursor: page.cursor,
  });
}
```

You can specify `asc`, `desc`, `ascending`, or `descending` for `sort.direction`.

## Handle record access rules

`objects.recordRules.list|create|delete` handles the same record access rules as "Access rules" in Studio. The key requires `object:policy`, and the key creator must have permission to read and configure policies for the target Object. Creation and deletion also require Object write permission. Check rule priority and the impact of deletion, including existing screen rules.

```ts
const { roles, items } = await studio.objects.recordRules.list("contacts");
const staff = roles.find(role => role.name === "Staff");
if (!staff) throw new Error("The target role does not exist");

const created = await studio.objects.recordRules.create("contacts", {
  name: "Records created by the user",
  status: "edit",
  conditions: [{ field: "created_by", operator: "eq", value: "$principal.id", valueType: "variable" }],
  principals: [{ principalType: "role", principalId: staff.id }],
});
await studio.objects.recordRules.delete("contacts", created.ruleGroupId);
```

Rules are evaluated in priority order. For partial write or deletion errors, record `code` and `params`; do not rerun based on assumptions, and check current rules with `list()`.

## 4. Handle field definitions

Use `listFields()`, `createField()`, `updateField()`, and `deleteField()` for fields. `createField()` requires `fieldName`, `displayName`, `dataType`, and a positive integer `fieldNum` starting at 1.

```ts
const fields = await contacts.listFields();
const phone = await contacts.createField({
  fieldName: "phone",
  displayName: "Phone number",
  dataType: "phone",
  fieldNum: fields.length + 1,
});
await contacts.updateField(phone.fieldId, { displayName: "Contact phone number" });
```

Changes to types or constraints affect existing records. Check permitted values, limits, and change conditions in [Object Specifications and Limits](../build/object-specs-and-limits.md) and [Objects API](../api/objects-api.md). Deleting a field also affects saved values.

## Common misunderstandings

### `define<T>()` validates runtime input

`T` types SDK input and output. It does not guarantee required fields, uniqueness, access permissions, or field constraints. Handle creation and update results and errors.

### `update()` changes only specified fields

`update()` replaces complete `data`. Omitting ordinary fields that you want to retain deletes them.

## If it does not work

| Situation | Check and response |
|---|---|
| Cannot retrieve an Object or record | Check the Object ID, target Project, execution mode, authentication, and read permission. |
| An error occurs during creation or update | Check field names, types, requirements, uniqueness, relations, and consistency with existing data. |
| A value disappears after update | Check whether the `data` passed to `update()` is complete. |
| A Revision conflict occurs | Retrieve the latest record and check the conflicting changes before operating. |
| Search fails or has too few results | Check field names, operators, values, ordering, pagination, the setting to include deleted records, and permissions. |
| Bulk creation fails | Check that each request has 1,000 records or fewer and that each value meets the definition. |

For an inquiry, record the target Object, SDK version, execution mode, method, and error. Do not send API keys or personal information.

## Checklist

- [ ] Field names match property names in `T`
- [ ] Stored API keys in secure server-side configuration and did not expose them to browsers or logs
- [ ] Passed complete `data`, including fields to retain, when updating
- [ ] Checked `revision` and `expectedRevision` for processing where concurrent updates matter
- [ ] Handled filters, ordering, and cursors consistently in searches
- [ ] Split bulk creation into requests of 1,000 records or fewer
- [ ] Checked constraints and impacts in [Object Specifications and Limits](../build/object-specs-and-limits.md) before structural changes or deletion

## Next article

- [Objects API](../api/objects-api.md)
- [Object SQL DDL](../object-sql/object-sql-ddl.md)
- [Object Specifications and Limits](../build/object-specs-and-limits.md)
- [Manage Project API Keys](../operations/manage-project-api-keys.md)
