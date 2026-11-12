# Project Members API

The Project Members API is a read-only REST API for developers to list active members in the authenticated Project. You can use it to display destination candidates, match assignees, and check the number of users. Display names, email addresses, and avatar URLs may be personal information, so limit the fields, number of retrieved items, and display destination according to the purpose.

For SDK implementation, see [SDK Members](../development/sdk-members.md); for checking with the CLI, see [CLI Members](../cli/cli-members.md); and for how Project participation and permissions work, see [Understand Organization, Project, and App Permissions](../operations/understand-org-project-app-permissions.md).

## Related articles

- [SDK Members](../development/sdk-members.md)
- [CLI Members](../cli/cli-members.md)
- [Understand Organization, Project, and App Permissions](../operations/understand-org-project-app-permissions.md)
- [Manage Project API Keys](../operations/manage-project-api-keys.md)
- [Common API and SDK Errors, Timeouts, and Limits](../development/api-sdk-errors-timeouts-limits.md)

## Before you begin

The production base URL for this API is `https://api.japan-ai.co.jp`. When calling it directly with a Project API key, pass the key for the target Project from secure server-side configuration in the `x-api-key` header. `Authorization: Bearer <key>` is also accepted. The examples below use `x-api-key`.

```bash
export JAI_STUDIO_API_KEY='value retrieved from secure storage'

curl -sS 'https://api.japan-ai.co.jp/api/v1/project-members?limit=50' \
  -H "x-api-key: $JAI_STUDIO_API_KEY"
```

> Do not include API keys in browser JavaScript, public environment variables, source control, command history, logs, or support inquiries. For issuance, minimum scopes, expiration, rotation, and revocation, follow [Manage Project API Keys](../operations/manage-project-api-keys.md).

## Availability conditions and retrieval scope

To run `GET /api/v1/project-members`, all of the following must be met. Being able to create an API key or being a Project member alone does not guarantee that you can retrieve the list.

| Condition | What to check |
|---|---|
| Organization availability | The Members API must be available in the target organization. If it is unavailable, do not substitute other credentials or APIs; check with the organization's administrator. |
| Authentication | Call it with a Project API key or an authentication context inherited from the Page runtime environment. |
| API key scope | When using a Project API key, `member:read` is required. Do not add unnecessary write or delete scopes. |
| IAM authorization | The caller must be permitted to read member information for the target Project. Availability may be lost if the key creator's Project access or permission status changes. |
| Project match | Only the Project associated with authentication is in scope. This is not an API for retrieving members of other Projects or organizations by specifying an ID or search term. |

The response includes users who are active in the target Project and target organization. Users who have left and inactive Project members are not included. Member roles are not included in the response, so do not use this API to retrieve or determine roles.

## Endpoint

| Operation | Method and path | Required scope |
|---|---|---|
| Retrieve the Project member list | `GET /api/v1/project-members` | `member:read` |

### Query parameters

| Name | Location | Required | Default and limits | Meaning |
|---|---|---:|---|---|
| `search` | query | No | Up to 255 characters | Filters by a case-insensitive partial match of display name or email address. Leading and trailing whitespace is removed. |
| `limit` | query | No | `50`, integer from 1 to 100 | Maximum number of items returned per page. |
| `cursor` | query | No | Not specified | Pass the previous response `nextCursor` unchanged. |

If you omit the search term, no search filtering is performed. A whitespace-only `search` is treated as an empty string, so you do not need to specify it explicitly to retrieve all items. Specify `limit` using only digits. An out-of-range value, a non-integer value, or a search term over 255 characters returns 400.

## Response, sort order, and pagination

On success, the response is 200 in the following format. `requestId` is a UUID that identifies the request. Record it when necessary for support inquiries, but do not share it together with the full member list or authentication information.

```json
{
  "success": true,
  "requestId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "members": [
    {
      "id": "project-member-id",
      "principalId": "user-id",
      "principalType": "user",
      "name": "display name or null",
      "email": "mail@example.com or null",
      "avatarUrl": "https://... or null",
      "createdAt": "2026-10-01T00:00:00.000Z"
    }
  ],
  "totalCount": 1,
  "nextCursor": null
}
```

| Field | Details |
|---|---|
| `success` | `true` on success. |
| `requestId` | UUID that identifies the request. |
| `members` | Member array for the current page. |
| `members[].id` | Project member ID. |
| `members[].principalId` | ID representing the user. |
| `members[].principalType` | Currently `"user"`. |
| `members[].name` / `email` / `avatarUrl` | Display name, email address, and avatar URL. Each may be `null`. |
| `members[].createdAt` | ISO 8601 string representing the time the member information was created. |
| `totalCount` | Number of matching members across the target Project. |
| `nextCursor` | A string if there is a next page; otherwise `null`. |

The list is in descending order of member information creation time, then ascending user ID for the same time. When you need only `totalCount`, do not retrieve every page. For the next page, pass the returned `nextCursor` without modifying it and keep the same `search` and `limit`.

```bash
curl -sS 'https://api.japan-ai.co.jp/api/v1/project-members?search=ada&limit=50' \
  -H "x-api-key: $JAI_STUDIO_API_KEY"

curl -sS 'https://api.japan-ai.co.jp/api/v1/project-members?search=ada&limit=50&cursor=<nextCursor>' \
  -H "x-api-key: $JAI_STUDIO_API_KEY"
```

A cursor is an opaque value. Do not parse, generate, or edit it. An unrecognized cursor is treated as the first page, so processes that avoid duplicates must use only the value from the immediately preceding response. Members may be added or disabled during retrieval, so a process that treats results across multiple pages as an unchanging roster must design for the retrieval time and a reconfirmation method.

> Do not unconditionally store `email`, `avatarUrl`, or the whole list in logs, test fixtures, persistent screen storage, or tickets. Implement handling for `name`, `email`, and `avatarUrl` being `null`.

## Troubleshooting

| Status | What to check and do |
|---|---|
| 400 | Check that `limit` is an integer from 1 to 100, `search` is 255 characters or fewer, and `cursor` was passed unchanged from the response. |
| 401 | Authentication information may be missing or the authentication context may not be verifiable. Check server-side configuration, the target Project, and the execution mode. |
| 403 | Check organization availability, `member:read`, key expiration or revocation, the key creator's Project access, and IAM authorization in that order. Do not broaden scopes based on guesswork; if necessary, securely switch to a new key with the minimum scope. |
| 503 | IAM authorization may not be verifiable. Do not repeatedly retry in a short time. Record the target Project, operation time, HTTP status, and `requestId`, then check with an administrator. |
| Communication failure or timeout | Even for a read, do not determine the result based only on why no response arrived. Retrieve the list again when needed. For common handling, see [Common API and SDK Errors, Timeouts, and Limits](../development/api-sdk-errors-timeouts-limits.md). |

Whether 429 occurs and its wait time may differ by environment and route. For common waiting and retry policy, see [Common API and SDK Errors, Timeouts, and Limits](../development/api-sdk-errors-timeouts-limits.md), and do not hard-code a fixed retry count or wait time in your implementation.

## Checklist

- [ ] Confirmed that the Members API is available in the target organization
- [ ] Prepared authentication information for the target Project and the minimum required `member:read`
- [ ] Confirmed IAM read permission and the key creator's access status
- [ ] Correctly handled `limit`, `search`, and unmodified `nextCursor`
- [ ] Handled cases where `name`, `email`, or `avatarUrl` is `null`
- [ ] Did not leave member information or API keys in logs, shared materials, or the client side

## Next article

- [SDK Members](../development/sdk-members.md)
- [CLI Members](../cli/cli-members.md)
- [Understand Organization, Project, and App Permissions](../operations/understand-org-project-app-permissions.md)
- [Manage Project API Keys](../operations/manage-project-api-keys.md)
- [Common API and SDK Errors, Timeouts, and Limits](../development/api-sdk-errors-timeouts-limits.md)
