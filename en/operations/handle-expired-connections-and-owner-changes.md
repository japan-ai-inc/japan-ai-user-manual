# Handle Expired Connections and Owner Changes

Connections to external services may become unusable because authentication expires, the external service revokes access, or an account or owner changes. Personal and shared connections have different people responsible for repair and handover. This article explains the order in which users and administrators identify the scope of impact from what is displayed and safely repair or hand over connections.

- Create, disconnect, and re-authenticate a personal connection: [Connect Your Own External Accounts](connect-your-external-accounts.md)
- Configure a shared connection: [Configure Shared Connections for a Project or App](configure-shared-connections.md)
- Isolate permission and sign-in issues: [Permission and Authentication Errors](../troubleshooting/permission-and-auth-errors.md)
- Change, stop, or hand over scheduled runs: [Change, Stop, and Hand Over Scheduled Runs](change-stop-handover-scheduled-runs.md)

## Summary: what is affected when the owner changes

| Target | Whose permissions and credentials it runs with | When the former owner leaves the organization or Project | How to hand over |
|---|---|---|---|
| Personal connection | The person who connected it (used only by Agents that person runs) | The successor cannot use it | The successor creates their own personal connection |
| Shared connection | The authenticated business account (Workflows, automatic runs, and Agents of users without a personal connection) | Check the state of the authenticated account at the external service and who can re-authenticate | Re-authenticate with an approved business account, or switch to a new shared connection |
| Workflow trigger | The person who first published that start node | Automatically disabled. Republishing by someone else does not change who it runs as | The successor deletes and re-adds the trigger node, then publishes |
| Task (Task Scheduler) | The Task creator. Editing does not change it | Automatically disabled | The successor recreates the Task |
| Project API key | The key creator. Scheduled runs created with the SDK or API also run as the key creator | When the creator is no longer an active Project member, all of that person's keys return 403 errors | The successor creates a new key and switches the callers |

## Before you begin

- Confirm the correct organization, Project or App, and connection service. If Projects have the same name or multiple connections exist, also match their purpose.
- Identify the Agents, Workflows, App features, data integrations, triggers, and scheduled runs that may be affected. Record the connection name, connection type, authenticated account, last authentication date and time, and occurrence date and time.
- On the external service, confirm the account and permissions the successor will use and the approved handover time. Do not record or send actual passwords, API keys, or tokens.
- If changing a shared connection, notify the responsible person about affected users and work. If you do not have permission to make the change, do not repeat the operation; ask an administrator.

> A displayed connection and successful business processing are different things. After repair, use a permitted, small operation to check the reference target, destination, and update target.

## Separate causes and responses by symptom

Check the wording on the screen and the target, and first determine whether it is a personal or shared connection. "Needs re-authentication" means that external-service authentication needs updating; it is not a cue to replace it with another connection by guessing the service or account.

| Symptom or display | How to view the likely cause | First response |
|---|---|---|
| "Needs re-authentication" or "This connection is no longer valid. Please re-authenticate with the same account." | Authentication may be invalid or need updating. | Check the connection name, account, and connection type, then select "Re-authenticate" using the same account. |
| "This connection is linked to a different account. Please re-authenticate with the same account." | You may have selected a different account on the authentication screen. | Do not switch accounts; check the account of the existing connection. For an ownership transfer, handle it as a connection for the successor. |
| "Connection required" | The App or feature requires the user's own connection. | The user checks "Personal connectors" and follows [Connect Your Own External Accounts](connect-your-external-accounts.md). |
| A shared requirement is "Invalid" or "Not set," or "This app needs a shared credential configured by an administrator." | An App-wide connection may be unavailable or unconfigured. | The user contacts an administrator, who checks the target shared connection and its App configuration. |
| "Email error" for a Gmail or Outlook trigger | The connection status or publication status needs checking again. | Check "Refresh connection status," the connection status, and publication status in order. |
| Scheduled-run history says "The task could not run because its creator is no longer a member of the organization." | The Task creator is no longer an organization member. | The successor checks the run content, notifications, and schedule, then follows [Change, Stop, and Hand Over Scheduled Runs](change-stop-handover-scheduled-runs.md). |

> Even when a connection is "Connected" or "Authenticated," a run can fail if external-service data permissions, target folders, destinations, or other settings change. Do not conclude that the connection alone is the cause.

## Repair and hand over a personal connection

A personal connection authenticates the user's own external account and is used only by Agents that person runs. Workflows and automatic runs do not use personal connections. When a user transfers or leaves, a successor cannot take over and use the former user's personal connection. After the successor's business account and permissions are ready, the successor creates the necessary connections.

### When the user can re-authenticate

1. In the relevant App or Project, open "Connector Settings" and check "Personal connectors" or the guidance requesting a connection.
2. Confirm that the connection service, "Account," and "Connection type" are "Personal connection." On the App requirements screen, also check "Personal connectors" and its status.
3. If "Re-authenticate" is displayed, select it and complete external-service authentication with **the same account as the current connection**. If there is no "Re-authenticate" entry point, ask the responsible person before disconnecting or adding a connection.
4. Confirm that "Connected" or "Authenticated" is displayed.
5. Within the permitted scope, run the feature that had failed once and check the result.

If you select a different account during re-authentication, you cannot repair the existing connection. If an ownership transfer requires a different account, do not re-authenticate the old connection; instead, have the successor prepare their own connection.

### When the user or owner changes

1. Check features that depend on the former user's connection and the planned deactivation of the external-service account.
2. Prepare the successor as a user of the target Project or App, and grant needed external-service permissions. Studio use permissions do not substitute for access on the external service.
3. The successor creates and checks a personal connection with their own account. For detailed steps, see [Connect Your Own External Accounts](connect-your-external-accounts.md).
4. From the successor's position, check required Agents, Workflows, and App features using small data.
5. After confirming that the old connection is no longer needed and no dependent processing remains, the former user or an administrator selects "Disconnect."

> "Disconnect" deletes authentication information. Disconnecting a connection with Dataset update settings prevents updates. Do not disconnect the old connection before confirming operation with the successor's connection.

## Repair and hand over a shared connection

Because a shared connection affects common Project or App processing, users should not replace it with their own personal connections. Administrators confirm that the authenticated account remains a business account approved for shared use and matches the target App requirements and sharing scope.

1. In "Connector Settings," open "Project connectors" or the App's "App shared connectors." If "Manage connector" appears in the App, open the target configuration.
2. Check the connection service, authenticated account, connection type, and current status. If it is "Invalid," "Not set," or "Stopped," record the displayed guidance and target App requirements.
3. To repair an existing connection, a person with administration permissions selects "Re-authenticate" and uses an external-service shared account that is approved. If guidance asks you to request re-authentication from "the user or administrator who first connected," follow that guidance.
4. If you need to switch to a successor's business account, prepare a new shared connection. Check the setting result until "Settings" for the target App or "Connector configured for this app" is displayed. For specific registration and assignment, see [Configure Shared Connections for a Project or App](configure-shared-connections.md).
5. Check dependent Apps, Agents, and Workflows with permitted data. If there is no problem, consider disconnecting the old connection that is no longer needed.

> Changing a shared connection may affect multiple users and automated processes in the same Project or App. A connection cannot be made if its sharing scope does not match App requirements. Do not broaden the sharing scope indiscriminately to avoid insufficient permissions.

## Check the impact on scheduled runs and triggers

Workflow triggers and Task Scheduler "Tasks" run as their owner and require a shared connection for external services. They stop or fail when authentication expires or the owner leaves. Do not conclude that all automatic runs have resumed merely because you repaired the connection; check each target.

- For Gmail or Outlook triggers, check for "Needs re-authentication" or "Email error," and select "Refresh connection status" after authentication. The screen guidance says that email reception starts when you "Publish" after connection. Check the Workflow publication status as needed.
- A Task runs as its creator, and editing it does not change that. When the creator leaves the organization, the Task is automatically disabled. For a successor to take over, recreate the Task.
- A trigger runs as the person who first published that start node. Republishing by someone else does not transfer it, and it is automatically disabled when the owner leaves the organization. For a successor to take over, delete and re-add the trigger node, then publish. The Webhook URL and Email by JAI receiving address change when you do this.
- Scheduled runs created with the SDK or API run as the creator of the Project API key used. The key is valid only while its creator is an active member of the Project.
- Check connections, triggers, Tasks, and API keys separately.
- Check the schedule, notification destination, run target, published Workflow, next run, and history. Follow [Change, Stop, and Hand Over Scheduled Runs](change-stop-handover-scheduled-runs.md) when changing, recreating, or stopping them.

> Checking automatic runs actually sends email, updates data, or registers information externally. Check the run target and the impact of duplicate runs, and do not repeat the same process while the result is unknown.

## Checklist

- [ ] I confirmed the target organization, Project or App, service, and affected features.
- [ ] I checked the displayed message, connection type, authenticated account, and last authentication date and time.
- [ ] I distinguished personal and shared connections and decided who will address them.
- [ ] For a personal connection, the user re-authenticated with the same account, or the successor prepared their own connection.
- [ ] For a shared connection, I checked the approved business account and the App configuration result.
- [ ] I checked the successor's connection and dependent features before disconnecting the old connection.
- [ ] I checked trigger publishers, Task creators, Project API key creators, schedules, notification destinations, and history.
- [ ] I checked run results within the permitted scope and notified relevant people of the changes.

## If it does not work

If you cannot re-authenticate, lack permission to change a connection, it remains "Needs re-authentication" even when using the same account, or automatic runs do not resume, do not keep disconnecting and recreating the target. Separately check permissions, the external-service account status, App configuration, and trigger or Task creators.

For an inquiry, include the organization name, Project or App name, connection service, connection type (personal/shared), connection name, target feature, operation date and time, displayed message, run-history result, and whether an ownership transfer occurred. Do not send passwords, API keys, tokens, or authentication-screen content.

## Next articles

- [Connect Your Own External Accounts](connect-your-external-accounts.md)
- [Configure Shared Connections for a Project or App](configure-shared-connections.md)
- [Permission and Authentication Errors](../troubleshooting/permission-and-auth-errors.md)
- [Change, Stop, and Hand Over Scheduled Runs](change-stop-handover-scheduled-runs.md)
