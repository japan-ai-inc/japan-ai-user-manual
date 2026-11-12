# Configure Run Result Notifications

When you incorporate scheduled Tasks or Workflows into business operations, you may want to know that a run finished, failed, or produced a result to check without opening run history every time. This article describes how operations personnel configure a Task's **"Notification destination"** and check the route to results and sharing scope.

For Task creation and scheduling itself, see [Configure the Task Scheduler](../build/configure-task-scheduler.md). For stopping, handover, and history operations, see [Change, Stop, and Hand Over Scheduled Runs](change-stop-handover-scheduled-runs.md).

## Scope of this article

The scope is notifications about completion and failure for Agent Tasks and Workflow Tasks managed in **"Tasks."** In the regular Task settings screen, you can select multiple notification destinations: Notification Center, email, Slack, and Chatwork. Workflows and the legacy Task Scheduler are integrated into "Tasks."

Notifications supplement the indication of run success or failure; they do not guarantee that the business processing itself succeeded. For Tasks that send to or update an external service, do not conclude that work is complete based on notifications alone. Compare **"Run history"** and, as necessary, results in the external service.

> Notification destinations include links to run results, errors, and, depending on settings, output content. For Tasks handling personal, customer, or confidential information, check the viewing permissions of recipients, Slack channels, and Chatwork rooms in advance.

## Before you begin

- Check the target organization, Project, Task name, and execution target (Agent or Workflow). Do not confuse a test Task with a production Task of the same name.
- Decide who receives notifications, who is first responsible for checking them, and whom to contact on failure. For a Task that runs in a Project, Notification Center recipients are not necessarily limited to the Task creator.
- Decide according to business and information-management rules whether output text can appear in notifications and whether a shared link can be created.
- When using email, Slack, or Chatwork, prepare destination information through a secure channel. Do not copy webhook URLs or API tokens into tickets, chat, or screen sharing.
- Check permission to create and update Tasks, and permission to post or receive in the external notification service. For connection or ownership changes, also see [Handle Expired Connections and Owner Changes](handle-expired-connections-and-owner-changes.md).

## Notification destinations and required settings

| Notification destination | Setting on screen | Operational check |
|---|---|---|
| Notification Center | Select Notification Center in **"Notification destination"** | Check the Project member configuration and the scope in which each member can view results. |
| Email | Select email and enter an **"Email address"** | Check recipient errors, delivery restrictions, and viewers of shared mailboxes. You can specify multiple recipients. |
| Slack | Select Slack and enter a **"Slack Webhook URL"** | Check that the webhook URL is for the target channel and that the channel's visibility scope is appropriate. |
| Chatwork | Select Chatwork and enter a **"Chatwork API token"** and **"Chatwork room ID"** | Check the token's validity, posting permission, and the room's participants and viewing scope. |

You can use multiple notification destinations together. For example, you can select Notification Center so the owner notices immediately, email to contact the operations duty, and Slack for team monitoring. However, because the same output or link reaches multiple places, use the minimum necessary destinations.

## 1. Configure a Task notification destination

1. Open **"Tasks"** from navigation, then choose the target Task from **"In use."** If you opened the previous Task Scheduler screen, follow the displayed guidance to move to "Tasks."
2. For a new Task, open the creation screen; for an existing Task, open **"Update Task."** First check the target, run method, schedule, and status.
3. In **"Notification destination,"** select the necessary destinations. No additional destination input is required when using Notification Center only.
4. If you select email, enter the **"Email address."** Even when sending to several owners, check recipients so that it does not become unnecessary organization-wide distribution.
5. If you select Slack, enter the **"Slack Webhook URL."** Check that it is a notification webhook URL, and keep it out of browser history, materials, logs, and similar places.
6. If you select Chatwork, enter the **"Chatwork API token"** and **"Chatwork room ID."** You cannot save if either is missing. Treat a token like a password and limit who can view it.
7. If needed, configure **"Display output in notification"** and **"Automatic share link,"** described later.
8. Select **"Create"** or **"Update,"** then check the notification destination, status, and **"Next run"** again in Task details.

If you select email, Slack, or Chatwork but leave required values empty, the Task cannot be saved. Clearing a notification destination's check removes its corresponding input values from settings. When replacing settings, check after updating that notifications are not still sent to the previous destination.

## 2. Decide on output display and automatic share links

### Display output in notification

When you select a notification destination other than "Notification Center," you can configure **"Display output in notification."** When enabled, the notification message includes the Task output. This is useful when you want to check a short summary immediately. However, if output may contain confidential or personal information, generally leave it disabled and check it on a run result screen where permissions can be verified.

On failure, error information may be included in the notification instead of output. Error text can also contain input values, target names, or external service information, so inspect the notification destination's sharing scope in the same way.

### Automatic share link

For an Agent Task, you can enable **"Automatic share link."** When enabled, a shared link to the run result is generated automatically and can be included in notifications such as email, Slack, and Chatwork. When disabled, the shared link is not added to notifications. For a Workflow Task, availability of this setting from the same screen could not be confirmed; separately check Workflow result sharing according to the permissions and sharing specification of the run screen.

> An automatic share link is a URL for sharing, not simply an internal-screen route. Before enabling it, check who will receive the link, whether forwarding or reposting is acceptable, and whether the sharing scope of Task output complies with business rules. Check its content once in a test Task before applying it to production.

## 3. Test after saving

Configuration alone does not determine whether a notification actually arrives at an external destination or whether its content is appropriate. Check once with low-impact input and timing.

1. Check that the imminent run does not affect business operations, and use a temporary test Task if needed.
2. After **"Run now"** or the next scheduled run, check the start time, status, and run result in **"Run history."**
3. At every selected notification destination, check the Task name, success or failure, link to the run result, and output display if needed.
4. If you enabled an automatic share link, check its destination and sharing scope. Check not only whether recipients can see necessary results, but also whether they can see information they should not see.
5. For a Task that sends or updates externally, also compare the result in the external service. Do not decide on rerunning or subsequent processing merely because a notification arrived.
6. Delete or disable destinations, webhook URLs, tokens, and test Tasks added for validation once they are no longer needed.

## If notifications do not arrive

When there is no notification, distinguish the run itself from notification settings before repeatedly rerunning.

| Situation | What to check and do |
|---|---|
| No entry in run history | Check whether the Task is "Enabled," whether **"Next run"** and the end date are correct, and whether you are viewing the target Task. |
| The run failed | Check the error in **"Run history"** and the target connection, permissions, and input. Do not conclude it is only a delivery failure. |
| It does not appear in Notification Center | Check whether Notification Center is selected in the Task's "Notification destination," and check the Project's active member configuration and scope of result access. |
| Email does not arrive | Check the email address, spam/delivery rules, and capacity and shared-mailbox settings on the receiving side. |
| It does not arrive in Slack | Check the webhook URL, destination channel, active state of the webhook, and channel permissions. If you issue a new URL, update the Task as well. |
| It does not arrive in Chatwork | Check the API token, room ID, token expiration, and posting permission for the target room. Do not paste the token into chat to check it. |
| You cannot open the link or content differs from expectations | Separately check the run result, Task type, permissions of the notification recipient, automatic share-link enablement, and output-display setting. |

Notification sending failures are handled after the run result is recorded, so do not conclude that the run failed merely because a notification did not arrive. Even if history records success, separately correct external destination settings and check the effect of duplicate runs before testing again.

## Checklist

- [ ] I checked the target organization, Project, Task, and execution target
- [ ] I decided the first notification owner, failure responder, and viewing scope of each destination
- [ ] I selected the minimum necessary Notification Center, email, Slack, and Chatwork options in "Notification destination"
- [ ] I securely checked email addresses, Slack Webhook URLs, Chatwork API tokens, and Chatwork room IDs
- [ ] I checked information-disclosure risks for "Display output in notification" and "Automatic share link"
- [ ] After saving, I compared run history, every notification destination, the link destination, and results in the external service
- [ ] If notifications do not arrive, I separately checked run failure and notification delivery failure

## Related articles and next articles to read

- [Configure the Task Scheduler](../build/configure-task-scheduler.md)
- [Handle Expired Connections and Owner Changes](handle-expired-connections-and-owner-changes.md)
- [Change, Stop, and Hand Over Scheduled Runs](change-stop-handover-scheduled-runs.md)
- [A Workflow Does Not Start or Fails](../troubleshooting/workflow-not-starting-or-failing.md)
- [Collect the Information Needed for an Inquiry](../troubleshooting/collect-info-for-support.md)
