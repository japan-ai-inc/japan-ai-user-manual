# CLI Members

`jai-studio members list` is a read-only command that retrieves a JSON list of active members in the authenticated Project. You can use it to check destination candidates, match people in charge, and determine the number of users. Returned display names, email addresses, and avatar URLs may be personal information, so limit the retrieval scope and output destination to what the purpose requires. To implement the same capability with the SDK, see [SDK Members](../development/sdk-members.md); for CLI installation, profiles, and common flags, see [CLI Configuration and Profiles](cli-config-and-profiles.md).

## Related articles

- [SDK Members](../development/sdk-members.md)
- [CLI Configuration and Profiles](cli-config-and-profiles.md)
- [Manage Project API Keys](../operations/manage-project-api-keys.md)
- [Execution Modes, Authentication, and Supported Scope](../development/execution-modes-and-auth.md)

## Before you begin

Use `@japan-ai-inc/studio-sdk` **0.11.0** and the `jai-studio` CLI with Node.js **22 or later**. Prepare a CLI profile connected to the target Project, or a securely configured endpoint and Project API key. For the priority order of endpoint and key, creating profiles, and common flags, see [CLI Configuration and Profiles](cli-config-and-profiles.md).

Use this command in direct development or explicitly managed development automation. Listing with a Project API key requires the `member:read` scope. Follow [Manage Project API Keys](../operations/manage-project-api-keys.md) for key issuance, choosing minimum scopes, expiration dates, revocation, and rotation.

> Do not leave a Project API key in a `--key` argument, shell history, CI log, source control, or chat. Normally use a secure profile or `JAI_STUDIO_API_KEY`. The masked value displayed by `config show` cannot be used as credentials.

## Availability conditions and returned scope

Listing requires not only credentials but also all of the following conditions.

| Condition | What to check |
|---|---|
| Availability in the organization | The Members API must be available in the organization. If it is unavailable, do not substitute other credentials or APIs; check with the organization administrator. |
| API key scope | When using a Project API key, it must have `member:read`. Do not add unnecessary write or delete scopes. |
| Caller permissions | The key or caller must be allowed to read member information in the target Project. |
| Project match | The key in the profile or environment variable must apply to the Project you want to check. This is not a command for retrieving members by specifying another Project or organization. |

The response includes active Project members. Each item's `name`, `email`, and `avatarUrl` can be `null`. Do not treat them as required values in screens or follow-on processing.

| Field | Description |
|---|---|
| `id` | ID of the Project member |
| `principalId` | ID that represents the user |
| `principalType` | Currently `"user"` |
| `name` | Display name. May be `null`. |
| `email` | Email address. May be `null`. |
| `avatarUrl` | Avatar image URL. May be `null`. |
| `createdAt` | ISO 8601 string representing the creation date and time |

> Successful standard output includes member information as-is. Do not unconditionally save email addresses, avatar URLs, or the entire list to shared logs, test fixtures, generated examples, tickets, or persistent client-side storage. Making it readable with `--pretty` does not change how personal information must be handled.

## 1. List members

```text
jai-studio members list [--search <query>] [--cursor <cursor>] [--limit <n>]
```

Notation containing `<...>` below is **syntax that requires substitution**. Do not paste it into the shell with the angle brackets; replace it with an actual value.

There are no positional arguments. On success, JSON containing `members`, `totalCount`, and `nextCursor` is output to standard output. `totalCount` is the number of matching members across the entire Project; if you only need the count, you do not need to retrieve every page.

| Argument or flag | Required | Default | Meaning |
|---|---:|---|---|
| Positional argument | — | — | None. |
| `--search <query>` | No | Not specified | Filters by name or email address. Leading and trailing whitespace is removed. |
| `--cursor <cursor>` | No | Not specified | Cursor for retrieving the next page. Specify the preceding response's `nextCursor` unchanged. |
| `--limit <n>` | No | `50` | Maximum items per page. Specify an integer from **1 to 100**. |

First, retrieve the first page of the current Project without a search term.

```bash
jai-studio members list --limit 50
```

This example filters by name or email address.

```bash
jai-studio members list --search ada --limit 50
```

If you explicitly pass an empty string or whitespace-only value to `--search`, it produces an error instead of retrieving all items. To target all members, omit `--search` itself. The CLI has no `--all` flag. Use a cursor as described in the next section only when you need every page.

## 2. Use pagination

If the response's `nextCursor` is a string, pass that value to the next command with the same search conditions. If `nextCursor` is `null`, there is no next page. Do not parse, create, or edit a cursor; use it unchanged.

```text
jai-studio members list --search ada --cursor <next-cursor> --limit 50
```

Keep `--search` and `--limit` the same on the next page. Do not reuse a previous `cursor` when changing the search term, target Project, or retrieval process. Because member information can change during retrieval, design a retrieval timestamp and reconfirmation method for processing that stores or uses multiple pages as an immutable roster.

## Common misconceptions

### You can retrieve all items with `--search ""`

You cannot. An explicitly empty search term causes a CLI error. To list members without searching, do not specify `--search`.

### You can retrieve every page at once with `--all`

You cannot. `members list` does not define `--all`. Only when necessary, retrieve page by page using the response's `nextCursor`.

### You can list members as long as you can create an API key

Not necessarily. Check availability in the organization, the `member:read` scope, caller permissions, and target Project match. Do not broaden key scopes by guesswork; manage keys with minimum scopes according to [Manage Project API Keys](../operations/manage-project-api-keys.md).

## Troubleshooting

| Situation | Check or response |
|---|---|
| `403` indicates insufficient scope | Check that the key for the target Project has `member:read`. Do not broaden an existing key's permissions by guesswork; if necessary, issue and switch to a new key with the minimum scope. |
| `403` indicates unavailable access or insufficient permissions | Check organizational availability, the target Project, caller permissions, and execution mode. It may not be available in the organization. |
| Retrieval fails with `400` | Check that `--limit` is an integer from 1 to 100, the search term is not empty, and the cursor is from the preceding response. |
| You get an authentication error | Check key revocation, deletion, expiration, target Project, endpoint, and that an actual value is configured. Do not use the masked display. |
| You cannot share the output | Since it contains personal information, share only the necessary count or redacted reproduction steps. Do not attach complete JSON, even in a safe test environment. |

Successful results are output as JSON to standard output; errors are output as JSON to standard error. Because `--debug` displays detailed server error responses and may include sensitive information, do not use it in shared logs. For inquiries, record the target Project, CLI version, execution mode, operation time, redacted reproduction steps with the search conditions, and HTTP status; do not include API keys, authentication headers, email addresses, or complete output.

## Checklist

- [ ] I checked Node.js 22 or later, the CLI version, endpoint, and target Project.
- [ ] I confirmed that it is available in the organization and that the caller has read permission.
- [ ] I granted only the necessary `member:read` scope to the Project API key.
- [ ] I have not left the API key in command arguments, history, logs, or source control.
- [ ] I handled the possibility that `name`, `email`, and `avatarUrl` are `null`.
- [ ] On the next page, I use the same search conditions and the returned, unchanged cursor.
- [ ] I have not left personal information in logs, fixtures, inquiries, or shared materials.

## Next articles to read

- [SDK Members](../development/sdk-members.md)
- [CLI Configuration and Profiles](cli-config-and-profiles.md)
- [Manage Project API Keys](../operations/manage-project-api-keys.md)
- [Execution Modes, Authentication, and Supported Scope](../development/execution-modes-and-auth.md)
