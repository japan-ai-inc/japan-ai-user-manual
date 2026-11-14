# Configure Run Result Notifications

In a Studio Project's Task Scheduler, you can configure notification destinations for Agent or Workflow run results. Notifications help you review results; they do not guarantee completion of processing that sends to or updates an external service.

For task creation and schedule configuration, see [Configure the Task Scheduler](../build/configure-task-scheduler.md). For changes, stopping, and handover, see [Change, Stop, and Hand Over Scheduled Runs](change-stop-handover-scheduled-runs.md).

## Before you begin

- Check the target Project, task, execution target, and people who receive notifications.
- Check whether output text and shared links may be included in notifications, and the viewing scope of recipients, Slack channels, and Chatwork rooms.
- When using email, Slack, or Chatwork, prepare destination information through a secure channel. Do not copy Webhook URLs or API tokens into chat, tickets, or screen sharing.

## Notification destinations and required settings

Open **Task Scheduler** in the target Project, then create or edit the target in **Task List**. In **Notification Destination**, you can select multiple methods below.

| Notification destination | Required setting | What to check |
|---|---|---|
| Notification Center | None | The scope in which Project members can view results |
| Email | Email address | Recipients, delivery restrictions, and shared-mailbox viewers |
| Slack | Slack Webhook URL | Destination channel and its visibility scope |
| Chatwork | Chatwork API token and room ID | Token validity, posting permission, and room participants |

## 1. Configure notification destinations

1. In the target Project's settings, open **Task Scheduler** and create or edit the target task in **Task List**.
2. Check the target, inputs, run time or start method, and status.
3. In **Notification Destination**, select the required destinations. Notification Center alone requires no additional destination input.
4. Enter an email address for email, a Slack Webhook URL for Slack, and an API token and room ID for Chatwork.
5. When selecting a destination other than Notification Center, configure **Display output in notification** as needed.
6. After creating or updating, check notification destinations and task status.

You can use multiple notification destinations. Because the same output or link can arrive in several places, use the minimum necessary destinations. A task may not save if required values are empty.

## 2. Decide on output display and automatic share links

### Display output in notification

When you select a destination other than Notification Center, you can configure **Display output in notification**. When enabled, notifications include the task output. If output or errors can contain confidential or personal information, consider leaving it disabled and checking results on the run-result screen.

### Auto Share Link

For an Agent task, you can enable **Auto Share Link**. When enabled, a shared link to the run result is generated automatically and can be included in notifications such as email, Slack, and Chatwork. For a Workflow task, use of this setting from the same screen could not be confirmed; check Workflow result sharing according to the permissions and sharing specification of the run screen.

## 3. Check after saving

Check notifications and run results once with low-impact input or a scheduled run.

1. For a scheduled task, use **Execute** if needed to run without waiting for the scheduled time. It is unavailable for disabled tasks and Webhook tasks. Check the effect of external sending or updates before running it.
2. In **History**, check the start time, status, run result, and errors.
3. At every selected destination, check the task name, success or failure, link to the result, and output display where needed.
4. If Auto Share Link is enabled, check its destination and sharing scope.
5. For a task that updates an external service, also check the result in that service.

## If notifications do not arrive

When no notification arrives, check the run itself separately from notification settings.

| Situation | What to check and do |
|---|---|
| No entry in History | Check task status, run time, end date, and the target task |
| The run failed | Check the error in History, connections, permissions, and input |
| It does not appear in Notification Center | Check that Notification Center is selected and check Project members and result-access scope |
| Email does not arrive | Check the email address, spam or delivery rules, and receiving-side settings |
| It does not arrive in Slack | Check the Webhook URL, destination channel, active state of the Webhook, and channel permissions |
| It does not arrive in Chatwork | Check the API token, room ID, token expiration, and posting permission |
| You cannot open the link or content differs from expectations | Separately check the run result, task type, recipient permission, shared link, and output-display setting |

A missing notification alone does not mean that the run failed. When correcting an external destination and checking again, also check the effect of duplicate execution.

## Checklist

- [ ] I checked the target Project, task, execution target, and notification owner
- [ ] I checked the viewing scope of destinations and whether output may be shared
- [ ] I selected the minimum necessary Notification Center, email, Slack, and Chatwork destinations
- [ ] After saving, I checked History, each destination, the link destination, and the external-service result
- [ ] I checked notification delivery failure separately from run failure

## Related articles

- [Configure the Task Scheduler](../build/configure-task-scheduler.md)
- [Change, Stop, and Hand Over Scheduled Runs](change-stop-handover-scheduled-runs.md)
- [Handle Expired Connections and Owner Changes](handle-expired-connections-and-owner-changes.md)
- [A Workflow Does Not Start or Fails](../troubleshooting/workflow-not-starting-or-failing.md)
