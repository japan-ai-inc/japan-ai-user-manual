# SDK Members

The Studio SDK `members` module lists active members of the authenticated Project. Use it for read-only purposes, such as displaying destination candidates, matching assignees, and checking the number of users in a Project. Member information includes personal information, so minimize the retrieval scope, display destinations, and log output.

For the public API HTTP specification, see [Project Members API](../api/project-members-api.md). For issuing, revoking, and managing scopes for Project API keys, see [Manage Project API Keys](../operations/manage-project-api-keys.md). [Common API/SDK Errors, Timeouts, and Limits](api-sdk-errors-timeouts-limits.md) covers common timeouts and error handling.

## Related articles

- [Execution Modes, Authentication, and Supported Scope](execution-modes-and-auth.md)
- [Project Members API](../api/project-members-api.md)
- [Manage Project API Keys](../operations/manage-project-api-keys.md)
- [Common API/SDK Errors, Timeouts, and Limits](api-sdk-errors-timeouts-limits.md)

## Before you begin

- Use `@japan-ai-inc/studio-sdk` **0.11.0** and Node.js **22 or later**.
- This feature is for local direct development or a PRS server runtime. Do not pass an API key to the browser.
- Confirm the target organization, Project, and execution mode.
- The Project API key requires the `member:read` scope for member reads.

> Do not include API keys in browsers, public environment variables, source control, logs, or inquiries. Pass actual values from secure server-side configuration.

## Availability conditions and returned scope

In addition to authentication, listing requires that all of the following conditions are met.

| Condition | What to verify |
|---|---|
| Organization availability | Members API may not be available for an organization. Confirm with an administrator. |
| API key scope | `member:read` is required when using a Project API key for direct development. |
| Caller permissions | Permission to read member information for the target Project is required. |
| Project match | Do not confuse the authenticated Project. |

The results are active Project members. `name`, `email`, and `avatarUrl` may be `null`.

| Field | Description |
|---|---|
| `id` | Project member ID |
| `principalId` | ID representing the user |
| `principalType` | `"user"` |
| `name` | Display name. May be `null`. |
| `email` | Email address. May be `null`. |
| `avatarUrl` | Avatar image URL. May be `null`. |
| `createdAt` | ISO 8601 string representing the creation time |

> Do not unconditionally store member lists, email addresses, or avatar URLs in logs or client-side persistent storage.

## 1. Get a list from the SDK

Call `studio.members.list()` on the client created by `createClient()`. Arguments may be omitted, or you can specify `search`, `limit`, and `cursor`. For direct development, pass the API key from a server-side environment variable.

```ts
import { createClient } from "@japan-ai-inc/studio-sdk";

const studio = createClient({
  baseUrl: "https://api.japan-ai.co.jp",
  apiKey: process.env["JAI_STUDIO_API_KEY"],
});

const page = await studio.members.list({
  search: "ada",
  limit: 50,
});

for (const member of page.members) {
  console.log(member.id, member.name);
}
console.log(page.totalCount, page.nextCursor);
```

`search` filters by name or email address. Omit `search` when all results are required. `limit` is **1 to 100** and defaults to **50**. A search term has a maximum of **255** characters.

## 2. Handle pagination and counts

`members` is the current page, `totalCount` is the total number for the matching Project, and `nextCursor` is for the next page. If `nextCursor` is `null`, there is no next page.

```ts
let cursor: string | undefined;
const members = [];

do {
  const page = await studio.members.list({ limit: 100, cursor });
  members.push(...page.members);
  cursor = page.nextCursor ?? undefined;
} while (cursor);
```

When changing search conditions or the target Project, do not reuse a previous `cursor`. Pass a cursor returned by the SDK unchanged only to the next call with the same search conditions.

## 3. Check with the CLI

You can also retrieve the list with `jai-studio members list`. CLI profiles are not read by SDK `createClient()`.

```bash
jai-studio members list
jai-studio members list --search ada --limit 50
jai-studio members list --cursor '<next-cursor>'
```

`--search` filters by name or email address. Omit the option to retrieve all results. `--limit` and `--cursor` correspond to SDK `limit` and `cursor`.

## If it does not work

| Situation | Check and response |
|---|---|
| A `403` indicates an insufficient scope | Confirm that the target Project key has `member:read`. |
| A `403` indicates that it is unavailable or permissions are insufficient | Check organization availability, the target Project, and caller permissions. |
| The API key is shown as invalid | Check whether the key was revoked or deleted and check the target Project. |
| Retrieval fails with `400` | Check `limit`, the search term, and `cursor`. |
| A timeout or communication error occurs | Record `statusCode`, `message`, and execution mode from `MembersApiError`. |

The SDK returns non-success HTTP responses as `MembersApiError`. This type extends `SdkApiError` and has `statusCode` and `responseBody`. Do not output `responseBody` unprocessed. The default timeout is **30 seconds**, and you can specify `timeoutMs` and `AbortSignal`. For details, see [Common API/SDK Errors, Timeouts, and Limits](api-sdk-errors-timeouts-limits.md).

For an inquiry, record the SDK version, execution mode, operation time, HTTP status, error, and reproduction steps. Do not attach API keys, authentication headers, or personal information.

## Checklist

- [ ] Confirmed SDK 0.11.0, Node.js 22 or later, the target Project, and execution mode
- [ ] Confirmed that it is available in the organization and that the caller has read permission
- [ ] When using an API key, granted only the minimum required `member:read` scope
- [ ] Did not include API keys in browsers, public environment variables, logs, or source control
- [ ] Handled the possibility that `name`, `email`, and `avatarUrl` are `null`
- [ ] Used `totalCount` and `nextCursor` as appropriate and did not modify cursors
- [ ] Did not leave retrieved personal information in logs, fixtures, or inquiries

## Next article

- [Project Members API](../api/project-members-api.md)
- [Manage Project API Keys](../operations/manage-project-api-keys.md)
- [Execution Modes, Authentication, and Supported Scope](execution-modes-and-auth.md)
- [Common API/SDK Errors, Timeouts, and Limits](api-sdk-errors-timeouts-limits.md)
