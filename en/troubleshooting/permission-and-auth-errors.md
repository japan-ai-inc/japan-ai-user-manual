# Permission and Authentication Errors

Use this page when an operation that should be possible is refused, the API/SDK returns 401 or 403, an external service connection cannot be used, or a Page asks you to authenticate again after opening. It is for users and administrators. Even when symptoms look similar, **the user's role, the Project API key, external connections, the identity used by automated runs, and the Page session** are different causes. Identify which path failed before replacing credentials or broadening permissions.

## Related articles

- [Understand organization/Project/App permissions](../operations/understand-org-project-app-permissions.md)
- [Respond to connection expiry/change of person in charge](../operations/handle-expired-connections-and-owner-changes.md)
- [Manage Project API keys](../operations/manage-project-api-keys.md)
- [Configure Shared Connections for a Project or App](../operations/configure-shared-connections.md)
- [Manage External Tools, Data Integrations, and MCP](../operations/manage-external-tools-integrations-mcp.md)
- [Change, Stop, and Hand Over Scheduled Runs](../operations/change-stop-handover-scheduled-runs.md)

## Key points: whose permissions and credentials are used

| Path | Permissions/credentials used | What to know |
|---|---|---|
| Screen operations | The operator's organization role, Project role, and IAM | Project roles are Admin, Editor, and Member. There is no Viewer (read-only) role; a custom role is required for read-only access. Members can run Agents and Workflows and create many resources. Editors cannot manage members. |
| Project API key | The key's scopes and the key's creator | A key is valid only while its creator is an active member of the Project. If the creator is removed from the Project, all keys they created return 403. Scopes cannot be changed after creation. Scheduled runs created with the SDK/API run as the key's creator. |
| External connections of Agents in a Project | The runner's personal connection → the Project's shared connection → the organization-wide connection, in that order | The first connection found is used. |
| External connections of Workflows | Project shared connections only | Personal connections are not used. Automated runs require a shared connection. |
| Connections declared as personal in an App | The personal connection of the person running it | Does not fall back to a shared connection when there is no person running it. |
| MCP registered in a Project with OAuth/Bearer authentication | The registrant's credentials (shared in the Project) | The account used at registration becomes the shared credential for everyone in the Project and is used for all members' runs. Register with a dedicated least-privilege account. |
| Workflow trigger | The person who first published that start node | Republishing by someone else does not change the run identity. |
| Task Scheduler task | The task's creator | Editing the task does not change the run identity. |
| Triggers and tasks | — | They are automatically disabled when the owner leaves the organization. |

## Scope of this article

This article helps you distinguish refusals and authentication failures related to roles, API keys, external connections, automated runs, and Page sessions. Issuing, switching, and revoking keys is covered in [Manage Project API keys](../operations/manage-project-api-keys.md); repairing and handing over external connections in [Respond to connection expiration and change of person in charge](../operations/handle-expired-connections-and-owner-changes.md); and Page access mode settings in [Register and publish Pages from the screen](../build/register-and-publish-pages.md). Do not conclude from this article alone that Page build/display failures, network failures, or input errors are authentication or permission problems.

## Check first

- [ ] Identified the failed target (screen operation, external integration, automated run, Page, API/SDK) and the organization/Project/App name.
- [ ] Recorded the user, account, API key purpose name, or connection name separately.
- [ ] Recorded the HTTP status, displayed message, the `requestId` in the response body, date and time, and steps to reproduce.
- [ ] Did not include actual values of API keys, passwords, tokens, cookies, or Secrets in logs, screen sharing, or inquiries.
- [ ] Before deleting, reissuing, or re-authenticating connections/keys, checked the affected automated processes and users.

> To resolve an error, do not accept other people's credentials, put a key in browser-side code, create a full-scope key, or share it with everyone. Narrow down the required target, operation, and owner.

## Isolate by symptom

| Symptom/displayed message | Main cause | Check/action | Detailed article |
|---|---|---|---|
| "You do not have permission to perform this operation", "Access denied", "You do not have permission to use this agent. Please contact your administrator." | Missing organization role, Project role, IAM, or sharing/policy on the individual resource | Separate the target organization/Project/resource and the failed operation (view/run/edit). Administrators check the base role, Project membership, and, if needed, IAM and sharing scope. Users ask for changes instead of changing roles themselves. | [Understand organization/Project/App permissions](../operations/understand-org-project-app-permissions.md) |
| The screen opens but you cannot edit, delete, or change settings | Missing role or IAM permission for the change operation | Record which button/target failed. Since no default Project role is read-only, check whether a custom role or IAM restricts the operation. Request only the minimum operation needed. | [Understand organization/Project/App permissions](../operations/understand-org-project-app-permissions.md) |
| You cannot add or change Project members | Editors and Members cannot manage members | Ask a Project Admin. Changing someone to Editor does not solve this. | [Understand organization/Project/App permissions](../operations/understand-org-project-app-permissions.md) |
| The API/SDK returns 401 (Unauthorized) or "Authentication is required" | No credentials were sent at all (only some routes return 401) | Check that the key is attached to the request. In the CLI, the precedence of endpoint/key settings is fixed as flag > environment variable > profile, so also check that an unintended setting is not used. Do not repaste and share the key value. | [Manage Project API keys](../operations/manage-project-api-keys.md) |
| The API/SDK returns 403 with "Invalid API key" | Wrong key value, deleted/revoked key, or the key's creator is no longer an active Project member | Check the target Project, key name, deletion/revocation, and the creator's Project membership. If the creator was removed, all keys they created are invalid. The actual value is shown only at creation, so issue a new key and switch safely. | [Manage Project API keys](../operations/manage-project-api-keys.md) |
| The API/SDK returns 403 with "API key has expired" | The key has expired | Issue a new key with the minimum scopes and a new expiration, switch to it, then revoke the old key. | [Manage Project API keys](../operations/manage-project-api-keys.md) |
| The API/SDK returns another 403 (Forbidden) | Missing scope or no permission on the target | Compare the requested target/operation with the key's scopes. Scopes cannot be changed after creation, so only if a scope is missing, issue a new key with the minimum scopes, switch, and revoke the old key. | [Manage Project API keys](../operations/manage-project-api-keys.md) |
| "Re-authentication required", "Linkage has been disabled. Please re-authenticate with the same account." or "Authentication failed. If the problem persists, please contact the administrator." | The external service authorization expired, was revoked, or the account changed, or permissions changed on the external service. A connector added or changed by an App install/update may not be configured. | Check whether it is a personal or shared connection, the destination, connection name, and authenticated account. The owner re-authenticates a personal connection with the same account. Users do not replace a shared connection with their own personal connection; administrators assess the impact. Workflows use only shared connections, so re-authenticating a personal connection does not fix a Workflow failure. | [Respond to connection expiration and change of person in charge](../operations/handle-expired-connections-and-owner-changes.md) |
| An MCP tool runs with another account's permissions instead of the runner's | When an OAuth/Bearer MCP is registered in a Project, the registrant's credentials become the shared credentials for everyone in the Project | This is the expected behavior. If it was registered with a personal account, register it again with a dedicated least-privilege account. | [Manage External Tools, Data Integrations, and MCP](../operations/manage-external-tools-integrations-mcp.md) |
| Triggers or scheduled runs stop or fail with permission errors after the owner changes role or leaves | Triggers run as the person who first published the start node; tasks run as their creator. They are automatically disabled when the owner leaves the organization. | Republishing or editing by a successor does not change the run identity. Delete and re-add the trigger node (the Webhook URL and Email by JAI address change), or recreate the task. | [Change, Stop, and Hand Over Scheduled Runs](../operations/change-stop-handover-scheduled-runs.md) |
| Authentication is requested while using a Page; it works or does not work after reloading | The sign-in or Page session required by the Page's access mode has expired or become invalid | Check the Page URL, access mode, account, and previous operation. Reload or authenticate as instructed once; if the symptom continues, do not repeat it and contact the Page administrator. For Pages with external customer authentication, use the authentication route provided. | [Register and publish Pages from the screen](../build/register-and-publish-pages.md), [Understand organization/Project/App permissions](../operations/understand-org-project-app-permissions.md) |

## Distinguish between 401 and 403

| Status | Meaning | Check first |
|---|---|---|
| 401 (Unauthorized) | No credentials were sent at all. Only some routes return 401. | Check that the key is attached and where it is set (flag, environment variable, profile). |
| 403 "Invalid API key" | The key is invalid (wrong value, deleted/revoked, or the creator is not an active Project member) | Check the key state and the creator's Project membership. |
| 403 "API key has expired" | The key has expired | Issue a new key and switch to it. |
| Other 403 | Missing scope, role, or IAM permission | Check the scopes/permissions required for the operation. |

Invalid and expired keys return 403, not 401. **Do not mechanically decide "401 means recreate the key, 403 means maximize permissions."** Use the message in the response body. Studio API error responses include a `requestId`. The CLI removes it from its default output, so take it from the API/SDK response body for inquiries.

## Steps to isolate the cause

1. **Check whether it is a user role.** If only certain edit/management operations fail in the browser, check the organization role, Project role, IAM, and sharing scope. Project membership (the Project appears under "Joined Projects") and permission on individual resources are different.
2. **Check whether it is the API key.** If the API/SDK fails, check the response message, target Project, key purpose name, expiration, deletion/revocation, the creator's Project membership, required scopes, and where the key is set. Scopes cannot be changed after creation; if a change is needed, issue a new key, switch, and revoke the old one.
3. **Check whether it is an external connection.** For "Re-authentication required" or "Not linked", use the key-points table to see which connection was used, then check the connection owner and the account/permissions on the external service. When responsibility changes, the successor prepares their own connection or a shared connection instead of inheriting the predecessor's personal connection.
4. **Check the run identity of automated runs.** If only triggers or tasks fail, check whether the run identity (first publisher, task creator, or key creator) is still in the organization/Project.
5. **Check whether it is the Page session.** If it occurs only while viewing or using a Page, check the account or external authentication that matches the access mode and record the state after reloading/re-authenticating. Do not work around it by making the Page public.
6. **Check the scope of reproduction.** Distinguish whether it affects only one user, everyone in the Project, or only one external service, API key, or Page. Avoid duplicate reruns and issuing multiple keys.

## Common misconceptions

- **Assigning a Viewer role makes a user read-only**: Projects have no Viewer role. Even the default Member can run Agents and Workflows and create many resources. Read-only access requires a custom role.
- **An API key can call every API**: The target Project, scopes, expiration, and the creator's Project membership all matter.
- **401 means a key problem and 403 means a permission problem**: Invalid and expired keys also return 403. Use the response message.
- **Re-authenticating a personal connection also fixes Workflows**: Workflows use only shared connections.
- **If a successor republishes, automated runs use the successor's permissions**: Triggers keep running as the first publisher, and tasks as their creator.
- **"Connected" means external processing will succeed**: Also check data permissions, the authenticated account, and the destination's state on the external service.
- **If a Page asks you to re-authenticate, you should raise permissions**: Distinguish an access mode/Page session problem from a permission problem with the function being run.

## Before consulting an administrator or support

Record the following. For details, see [Collect information required for inquiries](collect-info-for-support.md).

- Organization, Project, App/Page/API target name, and user or key purpose name (no actual values)
- Failed operation/request destination, HTTP status, displayed message, `requestId`, date and time, and steps to reproduce
- Results of checking the user's role, the required operation, personal/shared connection, and whether re-authentication was done
- For API keys: expiration, scopes, deletion/revocation, creator's membership, and switching status; for automated runs: the run identity; for Pages: URL, access mode, and state after re-authentication

## Read next

- [Understand organization/Project/App permissions](../operations/understand-org-project-app-permissions.md)
- [Respond to connection expiry/change of person in charge](../operations/handle-expired-connections-and-owner-changes.md)
- [Manage Project API keys](../operations/manage-project-api-keys.md)
- [Not displayed/Cannot participate](not-visible-cannot-join.md)
