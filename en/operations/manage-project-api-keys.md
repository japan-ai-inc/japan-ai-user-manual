# Manage Project API Keys

Project API keys are credentials for accessing resources in a target Project from an external system or development program. This article explains how administrators and developers issue keys, choose scopes (permissions) and expiration dates appropriate to the purpose, switch safely during updates, and revoke keys that are no longer needed or may have leaked.

For authentication methods by execution mode and how to pass a key to code, see [Execution Modes, Authentication, and Supported Scope](../development/execution-modes-and-auth.md). For conditions for APIs that retrieve member information, see [Project Members API](../api/project-members-api.md). For isolating SDK/API errors, see [Isolate SDK and API Errors](../troubleshooting/isolate-sdk-api-errors.md).

## Key points: which credentials a key runs with

| Item | Rule |
|---|---|
| Acting identity | Operations with a key run **as the key creator**. A key cannot supplement permissions the creator does not have. |
| Validity | A key is valid within its expiration **only while its creator is an active member of the Project**. If the creator is removed from the Project, all keys they created return 403. |
| Scheduled runs created with the SDK | Run as the key creator. |
| Scopes and expiration | Cannot be changed after creation. To change them, issue a new key and switch. |
| Actual value | Displayed only once at creation. |

Before removing someone from a Project because of a transfer or departure, identify the keys they created and switch to keys created by another owner ([Check Deletion, Handover, and Audit Records](deletion-handover-and-audit-logs.md)).

## Before you begin

- Confirm the target organization, Project, and external system. Keys are issued per Project, so do not confuse Projects corresponding to development, testing, and production.
- Confirm that API key management is available for the organization and Project. If the item is not displayed, contact the organization administrator.
- Decide who creates the key, the system that uses it, the person responsible for storage, and the person who decides to stop it. Because a key is valid only while its creator is an active Project member, have it created by someone who is not expected to leave the Project.
- Identify required operations in advance. Select scopes by target, such as reading, writing, deleting, or running. Do not select unplanned scopes or "Select all scopes."

> A Project API key is different from a Project Secret. To store a confidential value received from an external service in a Project, see [Register, Update, and Delete Project Secrets](manage-project-secrets.md). Do not write API keys themselves in browser source, public repositories, chat, tickets, or logs.

## Who can manage keys and what information you can check

The key list displays the name, masked key, scopes, expiration date, last used, creator, and actions. You cannot redisplay the actual value from the list.

| Operation | Typical required condition | Notes |
|---|---|---|
| Check the list | You can view the target Project and have permission to view key information. | The value is masked, not an actual value. |
| Issue a new key | You can edit the target Project and have permission to create keys. | Even a Project Admin or Editor may be unable to perform the action because of additional permission settings. |
| Delete and revoke | You have permission to delete the target key. | An Admin may be able to manage keys created by others. An Editor may be able to delete only keys they created. If individual permission settings exist, those settings take precedence. |

Being able to open the screen does not mean you can create or delete a key without the permission. If an operation is disabled, do not grant yourself more permission than necessary; ask the Project administrator.

## 1. Open "API Key Management"

1. In JAPAN AI, open the correct organization and target Project.
2. From Project settings, open **"API Key Management."**
3. In the list, check existing key names, scopes, **"Expiration,"** **"Last Used,"** and creators. First check whether you can use an existing key or whether a key for the same external system remains.
4. If "API key management is not enabled for this project." is displayed, contact the organization administrator.

"Never" in the list does not indicate that the key is valid. Last used is also supplemental information. Do not rely on it alone to decide to stop a key; also check the using system configuration, owner, and run records.

## 2. Issue a new API key

1. Select **"Create API Key."**
2. Under **"Name,"** enter a name that identifies the purpose, connection target, and owner. Example: `sales_order_integration_test`. Do not include the actual key, passwords, or personal information in the name.
3. Under **"Scopes (Permissions),"** select only needed targets and operations. At least one scope is required. Scopes cannot be changed after creation.
4. Under **"Expiration,"** select an expiration period that matches the use period. The options are **"7 days,"** **"30 days,"** **"90 days,"** and **"365 days."** Select a short expiration for short-term testing and schedule a switch date before expiration.
5. Check the target Project, name, scopes, expiration, and storage location, then select **"Create."**
6. When "API Key Created" appears, copy the actual value and immediately store it in an organization-approved secure location. After confirming that it was stored, select **"Done."**

> The actual value is displayed **only once when created**. After you close it, only a masked display can be checked. If you lose the actual value, do not restore or display the existing key; check the impact, issue a new key, and switch the using system.

### Choose scopes

The screen displays scopes for Ontology, Storage, Dataset, Object, Page, Agent, Workflow, **Task Scheduler**, members, **credit usage**, and LLMs (displayed targets vary by organization availability conditions). The scopes required by the APIs, SDK, and CLI covered in this manual are as follows.

| Scope | Use |
|---|---|
| `storage:read` / `storage:write` / `storage:delete` | Read / upload and update / delete Storage files |
| `dataset:read` | Read Datasets |
| `object:read` / `object:write` / `object:delete` | Read / create and update / delete Object records (including bulk delete) |
| `object:policy` | Manage record rules (with `object:read` for listing, `object:write` for creation and deletion) |
| `page:write` | Deploy Pages |
| `agent:read` / `agent:execute` | Read / run Agents |
| `agent:write` | Create and update Agent definitions |
| `workflow:read` / `workflow:write` / `workflow:delete` / `workflow:execute` | Read / create and update / delete / run Workflows |
| `task:read` / `task:write` / `task:delete` | List / create and update (also requires `workflow:execute`) / delete scheduled Tasks |
| `member:read` | Read Project members |
| `credit:read` | There is currently no API this scope can read. Do not select it. |

`task:*`, `agent:write`, `object:policy`, and `credit:read` cannot be declared in an App release manifest.

`credit:read` can be selected on screen when the organization's credit-usage viewing feature is enabled and the creator is an organization or Project Admin, but no API can be read with it. Check credit usage on the organization's "Credit Usage" screen ([Check Execution Consumption and Usage](check-execution-consumption-and-usage.md)).

| Purpose | How to choose scopes |
|---|---|
| Reference files | Select only `storage:read`. Do not add upload or delete if they are unnecessary. |
| Update data or Objects | Select write for the target. Add delete only when operationally needed. |
| Use an Agent or Workflow | Separately determine whether read is needed or execution alone is enough. Limit Workflow changes and deletion to operations owners. |
| Manage scheduled Tasks from the SDK or CLI | Select `task:read` for listing; `task:write` and `workflow:execute` for creation and updates; and `task:delete` for deletion. |
| Use member information | Select `member:read`. For conditions by execution mode, see [Execution Modes, Authentication, and Supported Scope](../development/execution-modes-and-auth.md) and [Project Members API](../api/project-members-api.md). |

> "Write" does not necessarily mean "Delete" is needed. First test the connection with the minimum scope. Only after you find insufficient permissions should you issue a new key with the required scopes. Scopes and expiration of an existing key cannot be changed.

### When managing scheduled Tasks with the SDK or CLI

Use Project API keys for Studio SDK/CLI scheduled Task functionality. Tasks **run as the key creator**, and a key cannot supplement permissions the creator does not have at the time of operation. Only Workflows created in that Project can be targeted. Workflows imported from the organization cannot be scheduled Tasks.

You can change only submitted items after creation. Because you cannot change the Workflow or owner, when changing an owner or run target, create a new key and Task, confirm operation, then stop or delete the old Task in that order. `task:*`, `agent:write`, `object:policy`, and `credit:read` cannot be declared in an App release manifest. Do not assume scheduled Task functionality is available to a Page distributed as an App. For specific SDK/CLI calls, check the reference for the SDK version in use.

## 3. Check before starting use

1. Register the key from approved secure storage in the safe configuration of the using system. Confirm that actual values are not displayed in screens or logs.
2. Use approved test Projects, data, and accounts, and perform only required representative operations.
3. Confirm that only intended operations succeed and unnecessary reads, writes, and deletion cannot be performed.
4. Return to the "API Key Management" list and record the name, scopes, expiration, and creator. Do not record the actual value.
5. Record the using system, storage owner, planned renewal date, and contact for revocation in the register.

Specific SDK/API headers, configuration files, and ways to pass values to execution environments are outside this article's scope. Do not embed keys in client-side code; configure them according to the authentication method in [Execution Modes, Authentication, and Supported Scope](../development/execution-modes-and-auth.md).

## 4. Update by rotation

A key's value, scopes, and expiration cannot be changed after creation. If expiration approaches, ownership or purpose changes, or a leak is suspected, switch to a new key in the following order.

1. Check affected external systems, running processes (including scheduled runs created with the SDK), current key name, owner, and switch date and time.
2. Issue a new key with the new purpose, minimum scopes, and new expiration according to step 2.
3. Register the new key in safe configuration and test representative operations. Decide in advance on the contact if it fails and when to stop the old key.
4. Confirm that the using system works only with the new key.
5. Revoke the old key. Keep parallel use to the minimum needed period and do not leave it after switching is complete.

## 5. Revoke unused or leaked keys

Deletion cannot be undone. Deleting a key prevents systems using it from accessing the Project. For unused keys, keys no longer under the owner's responsibility, or keys suspected of leaking, check usage and delete them. If a leak is suspected, issue and switch to a new key first, and prioritize revocation over investigation.

1. In the list, check the target name, scopes, expiration, last used, and creator.
2. Select **"Delete"** in the target row.
3. Check the confirmation and target name in **"Delete API Key."**
4. Confirm that the using system has been switched or stopped, then confirm **"Delete."**
5. Confirm that "API key deleted successfully" appears and the target disappears from the list.

## If it does not work

| Situation | Check and response |
|---|---|
| "API Key Management" is not displayed or is not enabled | Check the target organization, Project, availability conditions, and permissions, then ask the organization administrator. |
| You cannot select "Create API Key" | Check whether you can edit the Project and have permission to create keys. |
| You cannot select deletion | Check permission to delete the target key, the creator, and your role in the Project. Ask an administrator if needed. |
| You forgot to save the actual value after creation | Do not try to restore it from the masked list display. Issue a new key and switch the using system. Delete the old key after confirming the switch. |
| Authentication or permission errors occur in the API or SDK | In order, check the target Project, key expiration, whether it was deleted or revoked, **whether the key creator is still an active Project member** (keys whose creator left the Project return 403), required scopes, execution mode, and the key configuration location. Do not send the actual value in an inquiry; see [Isolate SDK and API Errors](../troubleshooting/isolate-sdk-api-errors.md). |

## Checklist

- [ ] I confirmed the target organization, Project, and using system.
- [ ] I decided the manager, storage owner, and contact for revocation.
- [ ] I chose the minimum required scopes and expiration.
- [ ] I saved the actual value to a secure location at creation and did not leave it in code or logs.
- [ ] I checked representative operations and that unnecessary operations cannot be performed.
- [ ] For renewal, I plan to delete the old key after testing the new key.
- [ ] I promptly revoked keys that are unused, suspected of leaking, or no longer under the owner's responsibility.
- [ ] Before removing a creator from the Project, I switched the keys and scheduled runs they created.

## Next articles

- [Execution Modes, Authentication, and Supported Scope](../development/execution-modes-and-auth.md)
- [Project Members API](../api/project-members-api.md)
- [Isolate SDK and API Errors](../troubleshooting/isolate-sdk-api-errors.md)
- [Register, Update, and Delete Project Secrets](manage-project-secrets.md)
- [Check Deletion, Handover, and Audit Records](deletion-handover-and-audit-logs.md)
