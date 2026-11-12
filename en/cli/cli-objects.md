# CLI Objects

`jai-studio objects` lets you work with Objects, fields, and records from the CLI. Successful standard output is JSON. For limits, see [Object Specifications and Limits](../build/object-specs-and-limits.md); for SDK operations, see [SDK Objects](../development/sdk-objects.md).

## Related articles

- [SDK Objects](../development/sdk-objects.md)
- [Object Specifications and Limits](../build/object-specs-and-limits.md)
- [CLI Configuration and Profiles](cli-config-and-profiles.md)

## Before you begin

This article covers the SDK **0.11.0** CLI. Use Node.js 22 or later. Connection information is resolved in the following order: flags, environment variables, then the selected profile.

```text
jai-studio --profile <name> objects list --limit 50 --pretty
```

Notation containing `<...>` below is **syntax that requires substitution**. Do not paste it into the shell with the angle brackets; replace it with an actual value.

The common flags are `--profile <name>`, `--env <env>`, `--key <key>`, `--pretty`, and `--debug`. For `--env`, you can specify `production` or an HTTP/HTTPS URL. `--pretty` formats JSON, and `--debug` displays detailed responses in error output. There is no `--json` flag; successful output is JSON.

> A Project API key is secret information. Do not leave an actual `--key` value in shell history, CI logs, or shared records.

## 1. Work with Object definitions

| Command | Positional arguments | Flags (required/default) | Description |
|---|---|---|---|
| `list` | None | `--cursor <cursor>`, `--limit <n>` (optional) | Retrieves a page of Objects. |
| `get` | `<objectId>` (required) | None | Retrieves one definition. |
| `create` | None | `--name <name>` (required), `--display-name <name>`, `--description <desc>` | Creates an Object. |
| `update` | `<objectId>` (required) | `--name <name>`, `--display-name <name>`, `--description <desc>` (all optional) | Updates the specified definition fields. |
| `delete` | `<objectId>` (required) | None | Deletes an Object. |

```text
jai-studio objects list [--cursor <cursor>] [--limit <n>]
jai-studio objects get <objectId>
jai-studio objects create --name <name> [--display-name <name>] [--description <desc>]
jai-studio objects update <objectId> [--name <name>] [--display-name <name>] [--description <desc>]
jai-studio objects delete <objectId>
```

```bash
jai-studio objects create --name contacts --display-name 'Customer contacts'
jai-studio objects update object-id --description 'Sales contacts'
```

`--limit` is parsed as a safe integer. Check `cursor` and `hasNextPage` in the result, and pass the returned cursor to retrieve the next page. Before deletion, check the impact in [Object Specifications and Limits](../build/object-specs-and-limits.md).

## 2. Work with field definitions

| Command | Positional arguments | Flags (required/default) | Description |
|---|---|---|---|
| `fields list` | `<objectId>` (required) | None | Retrieves a field list. |
| `fields create` | `<objectId>` (required) | `--data <json>` (required) | Creates a field from JSON. |
| `fields update` | `<objectId> <fieldId>` (required) | `--data <json>` (required) | Updates the specified fields from JSON. |
| `fields delete` | `<objectId> <fieldId>` (required) | None | Deletes a field. |

```text
jai-studio objects fields list <objectId>
jai-studio objects fields create <objectId> --data <json>
jai-studio objects fields update <objectId> <fieldId> --data <json>
jai-studio objects fields delete <objectId> <fieldId>
```

The JSON for `fields create` requires `fieldName`, `displayName`, `dataType`, and `fieldNum`. Quote the outside of the JSON for the shell, and use double quotes for internal keys and strings.

```bash
jai-studio objects fields create object-id --data '{"fieldName":"email","displayName":"Email","dataType":"email","fieldNum":1}'
```

> Check the consistency of field types and constraints with existing values. Before deletion, see [Object Specifications and Limits](../build/object-specs-and-limits.md).

## 3. Create, retrieve, update, and delete records

| Command | Positional arguments | Flags (required/default) | Description |
|---|---|---|---|
| `records list` | `<objectId>` (required) | `--cursor <cursor>`, `--limit <n>` (optional) | Retrieves a page of records. |
| `records get` | `<objectId> <recordId>` (required) | None | Retrieves one record. |
| `records create` | `<objectId>` (required) | `--data <json>` (required) | Creates a JSON record. |
| `records update` | `<objectId> <recordId>` (required) | `--data <json>` (required) | Replaces all record data. |
| `records delete` | `<objectId> <recordId>` (required) | None | Deletes a record. |

```text
jai-studio objects records list <objectId> [--cursor <cursor>] [--limit <n>]
jai-studio objects records get <objectId> <recordId>
jai-studio objects records create <objectId> --data <json>
jai-studio objects records update <objectId> <recordId> --data <json>
jai-studio objects records delete <objectId> <recordId>
```

```bash
jai-studio objects records create object-id --data '{"name":"Hanako Yamada","email":"hanako@example.example"}'
jai-studio objects records update object-id record-id --data '{"name":"Hanako Yamada","email":"hanako@example.example","company":"JAPAN AI"}'
```

`update` is not a partial update; it is a full replacement. Pass complete JSON that includes regular fields you want to retain. `--data` is interpreted as JSON, and the service determines whether it is consistent with the definition.

## 4. Specify search, sort order, and pagination

| Command | Positional arguments | Flags (required/default) | Description |
|---|---|---|---|
| `records search` | `<objectId>` (required) | `--filter <filter>`, `--sort <sort>`, `--cursor <cursor>`, `--limit <n>` (all optional) | Searches with conditions, sort order, and a cursor. |

```text
jai-studio objects records search <objectId> [--filter <field:operator:value>] [--sort <field:direction>] [--cursor <cursor>] [--limit <n>]
```

`--filter` is not JSON; it uses the `field:operator:value` format and can be repeated. Even when a value includes colons, the second and later colons are part of the value. Only `isnull`, `isnotnull`, and `exists` can omit a value. Operators are `eq`, `ne`, `gt`, `gte`, `lt`, `lte`, `contains`, `startswith`, `endswith`, `in`, `notin`, `isnull`, `isnotnull`, `exists`, `regex`, `between`, and `jsoncontains`. `--sort` uses the `field:direction` format and can be repeated; directions are `asc`, `desc`, `ascending`, and `descending`. You can omit the direction.

```bash
jai-studio objects records search object-id --filter 'company:contains:AI' --filter 'email:isnotnull' --sort 'name:asc' --limit 50
```

> When retrieving the next page of a list or search, keep the same conditions and sort order, and pass the preceding response's `cursor`. The CLI does not define `--offset` or `--include-deleted`.

## 5. Work with record access rules

In SDK 0.11.0, you can use `objects rules`, which corresponds to Studio access rules. You need the `object:policy` scope and Object policy permission for the key creator. Creating and deleting also require write permission for the Object.

```text
jai-studio objects rules list <objectId>
jai-studio objects rules create <objectId> --file ./rule.json
jai-studio objects rules delete <objectId> <ruleGroupId>
```

Pass rule JSON to `--file` for `create`. List existing rules first, and check priority and the access impact of deletion. Record the `code` and `params` displayed on failure. If a partial change may have occurred, do not rerun by guesswork; retrieve the list again. The CLI has no flags for `bulkDelete`, `includeCount`, or comparison updates. For bulk deletion, omitting count aggregation, or concurrency protection using `revision`, use the SDK in [SDK Objects](../development/sdk-objects.md).

## Handling revisions and conflicts

SDK record responses may include `revision`, and the SDK lets you specify `expectedRevision` for comparison updates and deletes. Because this CLI's `records update` and `records delete` have no corresponding flags, comparison updates and automatic retries on conflicts are unavailable. For processing where concurrent updates are a concern, retrieve the latest value again or use the SDK procedure in [SDK Objects](../development/sdk-objects.md) to handle `revision` and `expectedRevision`.

## Troubleshooting

| Situation | Response |
|---|---|
| It cannot be read as JSON | Check shell quotes, double quotes, and commas. |
| Required key error when creating a field | Check the four required keys and `fieldNum`. |
| Invalid search condition | Check `field:operator:value`, the operator, and sort order. |
| Values disappeared after an update | Check whether you passed the complete record, then retrieve the latest value. |
| You need error details | Use `--debug` only in a test environment, and do not record keys. |

## Checklist

- [ ] I checked the endpoint and target Project.
- [ ] I have not left the API key in history, logs, or source control.
- [ ] I quoted `--data` as valid JSON.
- [ ] I passed a complete record for updates.
- [ ] I used the same conditions, sort order, and cursor for the next search page.
- [ ] If concurrency protection is required, I considered the SDK approach in [SDK Objects](../development/sdk-objects.md).
- [ ] Before deletion, I checked the impact in [Object Specifications and Limits](../build/object-specs-and-limits.md).

## Next articles to read

- [SDK Objects](../development/sdk-objects.md)
- [Object Specifications and Limits](../build/object-specs-and-limits.md)
- [CLI Configuration and Profiles](cli-config-and-profiles.md)
- [Manage Project API Keys](../operations/manage-project-api-keys.md)
