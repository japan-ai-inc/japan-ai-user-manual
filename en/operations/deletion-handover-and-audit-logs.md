# Check Deletion, Handover, and Audit Records

When ending or deleting a Project, resource, member, or installed App, check dependent processing, access permissions, needed data, and handover to a successor before confirming the operation. Deletion, leaving a Project, unlinking a resource, and uninstalling an App affect different scopes.

This article explains the **common approach administrators use before and after deletion or an ownership change**, the "Audit Log" available within a Project, and information to provide to support. For App uninstallation, see [Uninstall an App](../use/uninstall-app.md). For ownership changes for external-service connections, see [Handle Expired Connections and Owner Changes](handle-expired-connections-and-owner-changes.md).

## Before you begin: Do not confuse the target operation

First, determine what you are ending. Even if names are similar, the following operations are not substitutes for one another.

| Operation | Main impact | What to check before deletion |
|---|---|---|
| Delete a Project | May affect the Project and its data. The screen also says it cannot be undone. | Dependent resources, members, data that must be retained, external integrations, and successors |
| Leave a Project | Removes the operator's own access. The Project and resources are not deleted. | That you are not the last Admin and the successor Admin |
| Delete a resource in a Project | Affects the target resource or references to it from the Project. | Links from other Projects and use by Agents, Workflows, Pages, and automatic runs |
| Remove a member | Removes access to the target Project or App. | Successor Admin, scheduled runs tied to the creator, personal connections, and work to hand over |
| Uninstall an App | Affects the App, access permissions, and data according to the installation method. | Data, pricing, and completion status shown on the App confirmation screen |

> Having a screen that allows deletion does not mean the operation can be completed safely. Always distinguish Projects and Apps with the same name, a provider's Project from an installed App, and unlinking from deleting the original resource. If permissions are insufficient or items cannot be confirmed, do not confirm deletion; ask the responsible person.

## 1. Inventory dependencies before deletion

Check not only the list of deletion targets, but also the people, processing, and external services that use the target. Keeping separate records of the person deleting and the person taking over business responsibility reduces omissions.

| Review area | What to check | Information to leave for the successor |
|---|---|---|
| Users and permissions | Under "Members," check users, roles, and the last Admin. | Successor Admin, minimum required roles, and use end date |
| Created resources | Check use of Agents, Workflows, Pages, Skills, Objects, Datasets, Storage, conversations, and artifacts. | Targets to continue using, storage location, and people responsible for ownership and editing |
| Automatic runs | Check Tasks, triggers, notification destinations, recent run history, and next actions. | Person responsible for stopping or recreating, run conditions, and notification destinations |
| Connections and credentials | Check shared connections, personal connections, Project Secrets, and external-service permissions. | Connection name, purpose, administrator of the authenticated account, and contact for updates. Do not retain actual values. |
| External effects | Check webhooks, external APIs, destinations, update targets, and external-service contracts and notifications. | Stop method, required external changes, and contact |

Some resources cannot be deleted because they are linked from another Project, and you may be asked to unlink them first. Conversely, even when a Project says delete, it may remove only access from the Project rather than the original resource. Read the displayed confirmation and determine whether it affects source data, references, or processing in use.

> Deleting connections or Secrets first can cause failures in Apps, Agents, and Workflows outside the target you are ending. Do not delete the old configuration until successor configuration, stopping, or an alternative procedure is complete and confirmed within the permitted scope. Follow [Handle Expired Connections and Owner Changes](handle-expired-connections-and-owner-changes.md) when switching connections.

## 2. Check data retention, deletion, and recoverability

Before deletion, identify needed data and check whether it can be moved to organization-approved storage. Items to retain may include not only input data, but also artifacts that users reference, files, configurations, and results already sent to or updated in external services.

1. Decide with the responsible person what data to keep, why to keep it, who can view it, and its retention period.
2. Check storage or export methods provided by the target feature and access to the storage destination. If needed content cannot be retained, do not confirm deletion.
3. Confirm that the successor can open the retained data and that its content is sufficient. Do not copy confidential information to unnecessary locations.
4. Read the target name and confirmation text on the deletion screen, then reconfirm the impact scope before confirming.
5. After confirmation, check the list, usage screen, and external service if needed. Record whether it is complete, accepted, failed, or has an unknown result.

The Project deletion screen says it cannot be undone, and the deletion-permission description says child data may be deleted. However, the screen display alone cannot determine post-deletion data-retention period, when physical erasure occurs, or whether users can restore every data type. Data handling for App uninstallation also differs by installation method.

> Do not treat disappearance from the screen as proof that all data, including backups, is gone, or as proof that it can be restored. If retention, permanent deletion, or recoverability needs confirmation, ask explicitly, specifying the organization, Project or App, data type, deletion date and time, and the confirmation needed.

## 3. Hand over members and responsibilities safely

When removing members or changing owners, prepare the successor before removing the former person's access. Because you cannot remove or change the last Project Admin to a non-Admin, first give the successor the necessary permissions and confirm that the list reflects them.

1. Under "Members," check the email address, role, and target Project or App for the former and successor.
2. Add the successor and grant the minimum required role. Separately check that the successor can open the target and has necessary permissions on external services.
3. Check the creator of automatic runs, notification destination, authenticated account of shared connections, personal connections, and person responsible for Secret management. Do not assume the successor can use the former person's personal connections or credentials as they are.
4. Have the successor check representative usage and the status of necessary automatic runs. For external sends or updates, pay attention to targets and duplicate runs.
5. Share completed handover, use end date, remaining work, and contact with relevant people before removing the former person or changing their role.

Removing a member does not delete a Project or resource. However, scheduled runs may stop if their creator is no longer an organization member. For detailed steps to change or remove permissions, see [Manage Project and App Members](manage-project-and-app-members.md).

## 4. Use "Audit Log," activity, and run history for different purposes

If "Audit Log" appears in Project settings, you can check operation history related to the Project. Its presence varies by organization settings, permissions, and availability conditions. If the item is absent, do not substitute another screen or conclude that records do not exist.

1. From target Project settings, open **"Audit Log."**
2. Set **"Start Date"** and **"End Date"** to a period that includes before and after deletion, role change, or error.
3. As needed, filter by **"Action,"** **"Actor Type,"** **"Resource Type,"** and **"Result."** You can check deletion, member additions and removals, role changes, and similar actions.
4. Check **"Timestamp,"** actor, operation, target resource, result, and error in the list. For needed rows, select **"View Details"** to check changed content, operations related to the same run, and run-user information when an Agent performed the operation. Even operations without a change diff can be opened to check their context.
5. To check resource additions or removal from a Project, use **"Operation information"** in the details to check whether it was added or unlinked, the specified resource, and, if needed, dependent resources and addition results. Old records may not allow you to check before-and-after changes or dependency information.
6. Record the check date and time, search conditions, facts found, and unconfirmed items. Do not modify or delete Audit Log content, or share confidential information broadly.

Activity and run history are entry points for checking execution status within a Project. Use them to investigate run starts, results, and history. Prioritize **"Audit Log"** to investigate records of deletion, permission changes, or resource additions and removals. Neither guarantees display scope, retention period, or records of every operation. If needed records are missing, ask with the search conditions and target period.

## If you cannot confirm it or information to include in an inquiry

If deletion fails, completion status is unclear, you cannot open the Audit Log, or needed records cannot be found, do not repeat another deletion operation. Separately check the target, permissions, dependencies, and current state.

Include the following in an inquiry:

- Organization name; target Project, App, or resource name; and, if possible, identification information confirmed on the screen
- Operation type (deletion, uninstall, leave, unlink, member removal, role change, and so on), actor, operation date and time, and time zone
- Affected users, Agents, Workflows, Pages, automatic runs, and external services, and business impact
- Displayed result or error, before-and-after deletion state, checks already performed, and whether you retried
- If you checked Audit Log or run history, the target period, filters, and applicable date and time, action, and result
- Needed confirmation, such as deletion completion, retention or permanent deletion, recoverability, access removal, or run stopping

Do not send passwords, API keys, tokens, Secret values, MFA codes, cookies, actual business-data values, or unnecessary personal information. For how to collect detailed information, see [Collect the Information Needed for an Inquiry](../troubleshooting/collect-info-for-support.md).

## Deletion and handover checklist

- [ ] I distinguished the target operation from leaving, unlinking, member removal, and App uninstallation.
- [ ] I inventoried users, administrators, dependent resources, automatic runs, notifications, and external integrations.
- [ ] I made necessary data and artifacts available to the successor in approved storage.
- [ ] I have not confused unconfirmed retention, permanent deletion, or recoverability with deletion completion.
- [ ] I checked successor permissions, external-service permissions, connections, notifications, and automatic runs.
- [ ] I did not include former users' personal connections or actual Secret values in handover materials.
- [ ] I confirmed needed facts in "Audit Log" or activity/run history and recorded search conditions.
- [ ] I shared post-deletion status, remaining work, guidance for users, and contacts.

## Related and next articles

- [Uninstall an App](../use/uninstall-app.md)
- [Manage Project and App Members](manage-project-and-app-members.md)
- [Handle Expired Connections and Owner Changes](handle-expired-connections-and-owner-changes.md)
- [Collect the Information Needed for an Inquiry](../troubleshooting/collect-info-for-support.md)
