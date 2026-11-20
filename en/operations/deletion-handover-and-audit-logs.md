# Check Deletion, Handover, and Audit Records

When ending or deleting a Project, resource, member, or installed App, check dependent processing, access permissions, needed data, and handover to a successor before confirming the operation. Deletion, leaving a Project, unlinking a resource, and uninstalling an App affect different scopes.

This article explains the **common approach administrators use before and after deletion or an ownership change**, the "Audit Log" within a Project, and information to provide to support. For App uninstallation, see [Uninstall an App](../use/uninstall-app.md). For ownership changes for external-service connections, see [Handle Expired Connections and Owner Changes](handle-expired-connections-and-owner-changes.md).

## Key points: whose credentials things run with

Removing a person from a Project or the organization stops processing tied to them. Check and switch the following before removing them.

| Target | Acting identity and validity | Effect of removing the person | How to hand over |
|---|---|---|---|
| Project API keys | Run as the key creator. Valid only while the creator is an active Project member. | When the creator is removed from the Project, all keys they created return 403. | The successor issues new keys and switches external systems. |
| Scheduled runs created with the SDK | Run as the key creator. | Automatically disabled when the owner (key creator) leaves the organization. | Recreate them with the successor's key. |
| Triggers | Run as the person who first published the start node. Republishing by someone else does not change this. | Automatically disabled when the owner leaves the organization. | Delete and re-add the trigger node (the Webhook URL and Email by JAI address change). |
| Task Scheduler tasks | Run as the creator. Editing does not change this. | Automatically disabled when the owner leaves the organization. | The successor recreates the task. |

For details, see [Change, Stop, and Hand Over Scheduled Runs](change-stop-handover-scheduled-runs.md) and [Manage Project API Keys](manage-project-api-keys.md).

## Before you begin: Do not confuse the target operation

First, determine what you are ending. Even if names are similar, the following operations are not substitutes for one another.

| Operation | Main impact | What to check before deletion |
|---|---|---|
| Delete a Project | **Physical deletion.** Project Secrets, API keys, and Skills are also deleted, and Storage is treated as deleted. The screen also says it cannot be undone. The Audit Log cannot be opened after deletion. | Dependent resources, members, data that must be retained, external integrations, successors, and the Audit Log |
| Leave a Project | Removes the operator's own access. The Project and resources are not deleted, but API keys you created become invalid. | That you are not the last Admin, the successor Admin, and API keys and automatic runs you created |
| Delete a resource in a Project | Affects the target resource or references to it from the Project. | Links from other Projects and use by Agents, Workflows, Pages, and automatic runs |
| Remove a member | Removes access to the target Project or App. | Successor Admin, API keys and scheduled runs the person created, personal connections, and work to hand over |
| Uninstall an App | Affects the App, access permissions, and data according to the installation method. | Data, pricing, and completion status shown on the App confirmation screen |

> A Project linked to a Slack or LINE bot cannot be deleted. Remove the bot link first.

> Always distinguish Projects and Apps with the same name, a provider's Project from an installed App, and unlinking from deleting the original resource. If permissions are insufficient or items are unclear, do not confirm deletion; ask the responsible person.

## 1. Inventory dependencies before deletion

Check not only the list of deletion targets, but also the people, processing, and external services that use the target. Keeping separate records of the person deleting and the person taking over business responsibility reduces omissions.

| Review area | What to check | Information to leave for the successor |
|---|---|---|
| Users and permissions | Under "Members," check users, roles, and the last Admin. | Successor Admin, minimum required roles, and use end date |
| Created resources | Check use of Agents, Workflows, Pages, Skills, Objects, Datasets, Storage, conversations, and artifacts. | Targets to continue using, storage location, and people responsible for ownership and editing |
| Automatic runs | Check Tasks, triggers, notification destinations, recent run history, and the acting identity (owner). | Person responsible for recreating, run conditions, and notification destinations |
| Connections and credentials | Check shared connections, personal connections, Project Secrets, API key creators, and external-service permissions. | Connection name, purpose, administrator of the authenticated account, and contact for updates. Do not retain actual values. |
| External effects | Check webhooks, external APIs, destinations, update targets, external-service contracts and notifications, and Slack/LINE bots. | Stop method, required external changes, and contact |

Some resources cannot be deleted because they are linked from another Project, and you are asked to unlink them first. Conversely, some actions labeled delete in a Project remove only the link from the Project rather than the original resource. Read the displayed confirmation and determine whether it affects source data, references, or processing in use. Unlinking an Agent also removes that Agent's IAM assignments.

> Deleting connections or Secrets first causes failures in Apps, Agents, and Workflows outside the target you are ending. Do not delete the old configuration until successor configuration, stopping, or an alternative procedure is complete and verified within the permitted scope. Follow [Handle Expired Connections and Owner Changes](handle-expired-connections-and-owner-changes.md) when switching connections.

## 2. Check data retention and deletion

Project deletion is physical deletion; users have no way to restore it afterward. Before deletion, identify needed data and move it to organization-approved storage. Items to retain include not only input data, but also artifacts that users reference, files, and configurations.

1. Decide with the responsible person what data to keep, why to keep it, who can view it, and its retention period.
2. **Check the "Audit Log" and record what you need.** After the Project is deleted, the Audit Log cannot be opened, and there is no export feature.
3. Save needed data using the storage or export methods provided by the target feature. If needed content cannot be retained, do not confirm deletion.
4. Confirm that the successor can open the retained data and that its content is sufficient. Do not copy confidential information to unnecessary locations.
5. Read the target name and confirmation text on the deletion screen, then reconfirm the impact scope before confirming.
6. After confirmation, check the list, usage screen, and external service if needed. Record whether it is complete, failed, or has an unknown result.

Project Secrets, API keys, and Skills are deleted with the Project, and Storage is treated as deleted. Data already sent to or updated in external services remains in those services after the Project is deleted. Data handling for App uninstallation differs by installation method.

## 3. Hand over members and responsibilities safely

When removing members or changing owners, prepare the successor before removing the former person's access. Because you cannot remove or change the last Project Admin to a non-Admin, first give the successor the necessary permissions and confirm that the list reflects them.

1. Under "Members," check the email address, role, and target Project or App for the former and successor.
2. Add the successor and grant the minimum required role. Separately check that the successor can open the target and has necessary permissions on external services.
3. In "API Key Management," check keys created by the former person. Removing that person makes all of those keys return 403, so have the successor issue new keys and switch the using systems.
4. Check triggers, tasks, and SDK-created scheduled runs owned by the former person, and recreate them under the successor following the key-points table above. Also check notification destinations, the authenticated account of shared connections, personal connections, and the person responsible for Secret management. Do not assume the successor can use the former person's personal connections or credentials as they are.
5. Have the successor check representative usage and the status of necessary automatic runs. For external sends or updates, pay attention to targets and duplicate runs.
6. Share completed handover, use end date, remaining work, and contact with relevant people before removing the former person or changing their role.

Removing a member does not delete a Project or resource. However, as described above, their API keys become invalid, and scheduled runs are automatically disabled when their owner leaves the organization. For detailed steps to change or remove permissions, see [Manage Project and App Members](manage-project-and-app-members.md).

## 4. Use "Audit Log," activity, and run history for different purposes

Check operation history related to the Project in "Audit Log" in Project settings. The Audit Log is viewed on screen; there is no export feature. It cannot be opened after the Project is deleted, so check it before deletion.

1. From target Project settings, open **"Audit Log."**
2. Set **"Start Date"** and **"End Date"** to a period that includes before and after deletion, role change, or error.
3. As needed, filter by **"Action,"** **"Actor Type,"** **"Resource Type,"** and **"Result."** You can check deletion, member additions and removals, role changes, and similar actions.
4. Check **"Timestamp,"** actor, operation, target resource, result, and error in the list. For needed rows, select **"View Details"** to check changed content, operations related to the same run, and run-user information when an Agent performed the operation. Even operations without a change diff can be opened to check their context.
5. To check resource additions or removal from a Project, use **"Operation information"** in the details to check whether it was added or unlinked, the specified resource, and, if needed, dependent resources and addition results. Old records may not include before-and-after changes or dependency information.
6. Record the check date and time, search conditions, facts found, and open items. Do not share confidential information broadly.

Activity and run history are entry points for checking execution status within a Project. Use them to investigate run starts, results, and history. Prioritize **"Audit Log"** to investigate records of deletion, permission changes, or resource additions and removals.

## Troubleshooting and information to include in an inquiry

If deletion fails, completion status is unclear, you cannot open the Audit Log, or needed records cannot be found, do not repeat another deletion operation. Separately check the target, permissions, dependencies, and current state. If a Project cannot be deleted, check whether a Slack or LINE bot is linked to it.

Include the following in an inquiry:

- Organization name; target Project, App, or resource name; and, if possible, identification information shown on the screen
- Operation type (deletion, uninstall, leave, unlink, member removal, role change, and so on), actor, operation date and time, and time zone
- Affected users, Agents, Workflows, Pages, automatic runs, and external services, and business impact
- Displayed result or error, before-and-after deletion state, checks already performed, and whether you retried
- If you checked Audit Log or run history, the target period, filters, and applicable date and time, action, and result
- Needed confirmation, such as deletion completion, access removal, or run stopping

Do not send passwords, API keys, tokens, Secret values, MFA codes, cookies, actual business-data values, or unnecessary personal information. For how to collect detailed information, see [Collect the Information Needed for an Inquiry](../troubleshooting/collect-info-for-support.md).

## Deletion and handover checklist

- [ ] I distinguished the target operation from leaving, unlinking, member removal, and App uninstallation.
- [ ] I inventoried users, administrators, dependent resources, automatic runs, notifications, external integrations, and Slack/LINE bots.
- [ ] I made necessary data and artifacts available to the successor in approved storage.
- [ ] Before deleting the Project, I checked the "Audit Log" and recorded what I need (it cannot be opened after deletion).
- [ ] I switched API keys created by the former person and triggers, tasks, and scheduled runs they own to the successor.
- [ ] I checked successor permissions, external-service permissions, connections, and notifications.
- [ ] I did not include former users' personal connections or actual Secret values in handover materials.
- [ ] I shared post-deletion status, remaining work, guidance for users, and contacts.

## Related and next articles

- [Uninstall an App](../use/uninstall-app.md)
- [Manage Project and App Members](manage-project-and-app-members.md)
- [Manage Project API Keys](manage-project-api-keys.md)
- [Change, Stop, and Hand Over Scheduled Runs](change-stop-handover-scheduled-runs.md)
- [Handle Expired Connections and Owner Changes](handle-expired-connections-and-owner-changes.md)
- [Collect the Information Needed for an Inquiry](../troubleshooting/collect-info-for-support.md)
