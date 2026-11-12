# Change, Stop, and Hand Over Scheduled Runs

Scheduled runs and Workflow triggers start processing even when the owner does not have the screen open. When the purpose changes, authentication expires, or an owner transfers or leaves, identify, change, stop, and hand over automation.

This article explains how operations personnel inventory Tasks and Workflow triggers, then change, stop, respond to failures, and transfer ownership. For new setup, see [Configure the Task Scheduler](../build/configure-task-scheduler.md); for event-trigger configuration, see [Configure Workflow Triggers](../build/configure-workflow-triggers.md).

## Scope of this article

Automation here includes the following two types.

| Type | Start trigger | Main place to check |
|---|---|---|
| Task | Date/time, repetition, webhook, and similar triggers | Tasks in **"In use"** under **"Tasks,"** and "Next run" and "History" in Task details |
| Workflow trigger | Webhook, JAI email, received email, changes to data or files, and similar events | Start node, publishing state, and run history of the target Workflow |

For automation, separately check **triggers created and configured as Tasks** and **triggers created and managed by the Workflow itself when it is published**. Disable or change settings for the former in "Tasks." The latter is updated when you publish the Workflow, so it normally cannot be changed in regular Task settings; change the start node and publish.

Even if a Workflow's own trigger is shown as deletable from "Tasks," it can be deleted only when the conditions for deleting your own publisher trigger are met. If another member uses it, or a Task cannot currently be created from that Workflow version, it cannot be deleted and may be restored at the next publication. Changing only a Workflow draft does not stop a published trigger.

> Automation can include sending email or updating external services. In an incident, unintended run, or ownership change, decide whether to stop the next scheduled or event-based start. Do not repeatedly use "Run now" or rerun while the result is unknown.

## Before you begin

- Check the organization, Project, and App. Do not confuse same-name targets or production and test targets.
- Check the business purpose to change, target Agent or Workflow, external send/update destination, notification destination, creator, and administrator.
- Check permission to change or stop the target.
- For high-impact processing, decide the contact, stop time, restart conditions, and reviewer.
- If connections or authentication are involved, check the authentication account and shared or personal connection. For expired connections or ownership changes, see [Handle Expired Connections and Owner Changes](handle-expired-connections-and-owner-changes.md).

## 1. Inventory running automation

Before changing or processing a departing member's work, list target automation. Check not only names, but also start conditions and recent results.

### Check Tasks

1. Open **"Tasks"** from navigation and find the target Task under **"In use."** Workflows and the legacy Task Scheduler are integrated into this "Tasks" screen. If you open the previous screen, follow the displayed guidance to move to "Tasks."
2. On the Task card or in details, check the Task name, description, execution target, trigger, status, and creator. If necessary, match it against the department or business name.
3. Open each Task and record **"Next run,"** notification destination, run time, repetition, and end date.
4. In **"Run history,"** check the latest success/failure, occurrence date and time, displayed error, and run result. For a Task that runs a Workflow, also check [Debug with Workflow Run History](../build/debug-workflow-run-history.md) if necessary.

### Check Workflow triggers

1. Open Workflows that may automate work and check whether the start node has a trigger.
2. Check the trigger type, target data, email, or folder; filtering conditions; and the connection state for external accounts.
3. Check whether the Workflow is published and whether the start node is in an active state, such as listening.
4. In run history, check whether it starts on intended events and whether there are duplicates or consecutive failures.

> Webhook URLs and receiving addresses are information that can be used to start processing externally. Do not copy them unnecessarily into inventory sheets or inquiries, and limit the sharing scope. Do not copy credentials, email bodies, or personal information directly from run history.

## 2. Change Task settings

You can review a Task's name, description, prompt, notification destination, schedule, end date, and similar settings in the target Task's **"Update Task."** Before changing it, check the immediately following run and effects on external processing.

1. Record the target Task's "Target," "Run method," "Notification destination," and "Next run."
2. Check scheduled runs during the change and disable the Task first if necessary.
3. Open **"Update Task"** and review only the items to change. When changing the schedule, check the run date, time, repetition, and end date together.
4. After **"Update,"** display and check the status, **"Next run,"** and notification destination again.
5. Check the first result under low-impact conditions and, as necessary, also check send/update destinations in the external service.

You cannot change trigger type or a Workflow-derived execution target by updating an existing Task. For an Agent Task, you may be able to change the Agent or model if the screen provides the choice and you have permission. To switch to a different start method or Workflow, create a new Task and stop the old Task after testing and checking notification settings. When changing a Workflow, its published version runs, so you also need to revise, test, and publish the Workflow.

## 3. Use temporary suspension and permanent stop appropriately

### Stop temporarily

During investigation, authentication repair, business suspension, or handover preparation, set the Task to **"Disabled."** While disabled, it does not run even if a run time is configured. Before restarting, check settings, connections, notification destinations, and the next run, then enable it again.

Check runs that have already started separately. If they include external sends or updates, check history and external service results, then decide whether duplicate execution is needed.

To stop a Workflow trigger, change or delete the start node in the target Workflow and **publish** it. Removing a start node in a draft alone does not stop the published trigger. After stopping, check that new run-history entries are not accumulating from unexpected events.

### Delete a Task that is no longer needed

Only when you have confirmed that the business has ended, it will not be restarted, and there is no need to retain history and settings, use **"Delete Task"** and select **"Delete."** This cannot be undone. Before deletion, check replacement Tasks, webhook callers, notification recipients, and business procedures.

> "Delete Task" does not delete the target Agent or Workflow itself. Conversely, an external system may continue calling a webhook URL, or another Workflow trigger may remain. To stop all automation, check both related Tasks and start nodes.

## 4. Hand over when an owner transfers or leaves

Tasks and Workflow triggers can depend on the member who created or published them, or on that member's credentials. When a member leaves the organization, Tasks created by that member and triggers using their credentials are disabled. Inventory them in advance rather than noticing failure only after departure processing.

It could not be confirmed that a creator can be changed by normal Task updates. For handover, the basic approach is for the successor to create and switch to a new Task.

1. For a member who will transfer or leave, check **"Creator"** under "In use" in "Tasks," and compare it with external accounts used by Workflow triggers.
2. Record the purpose, execution target, input, schedule/conditions, notification destination, connections, and recent history.
3. Prepare needed organization, Project, and Workflow permissions for the successor, as well as permissions at the external service. Do not assume the successor can use the former owner's personal connection.
4. Have the successor create a new Task. To prevent it from running alongside the existing Task, disable the old Task, then check target, input, schedule, and notification destination.
5. Run under safe conditions using the successor's permissions and connection, and check history, notifications, and results at the external service.
6. After confirming that the new Task runs stably, decide whether to delete the old Task or retain it disabled as a record.
7. For Workflow triggers, separately check the successor's connection, target conditions, and publishing state. If changing the publisher or authentication account is required, do not reuse the existing start node by assumption; check and publish according to [Configure Workflow Triggers](../build/configure-workflow-triggers.md) and [Handle Expired Connections and Owner Changes](handle-expired-connections-and-owner-changes.md).

After departure processing, check that Tasks and triggers associated with the person are disabled, scheduled runs are no longer continuing, and only the successor's automation is running.

## 5. When failures repeat

Repeated failures can lead to missed notifications or duplicate processing. Do not repeatedly change settings based only on errors; isolate the issue in this order.

1. If there is impact, disable the target Task or stop and publish the Workflow trigger to stop new starts.
2. In **"Run history,"** check the failure date/time, target, displayed message, last successful time, and whether it was immediately after a change.
3. Check whether the Task is disabled, whether the creator is an organization member, whether the target Agent/Workflow is available, and whether the Workflow is published.
4. Check connections/authentication, external-service permissions, target data, and notification destinations. For authentication issues, see [Handle Expired Connections and Owner Changes](handle-expired-connections-and-owner-changes.md).
5. For failures within a Workflow, check nodes, input, output, and errors in run history. [A Workflow Does Not Start or Fails](../troubleshooting/workflow-not-starting-or-failing.md) covers detailed isolation of start, input, and node failures.
6. After correction, check with a limited target, compare success, notifications, and external-service results, then restart.

| Situation | What to check and do |
|---|---|
| A Task does not run | Check whether its status is "Enabled," whether "Next run" is intended, and whether the end date has passed. |
| "The task could not run because its creator is no longer a member of the organization." | The creator's departure caused it. Do not rely on the disabled old Task; have the successor create and check a new Task. |
| A Workflow does not start | Check publishing state, start node, event conditions, and connection state. Draft-only changes do not stop or change it. |
| Connection or authentication failure | Separately check whether it is a personal or shared connection, the authentication account, and permissions at the external service. |
| The run result is successful but the business result is different | Check the prompt, input, published Workflow, and send/update destination; do not decide completion from history alone. |

## Checklist

- [ ] I checked the "In use" list in "Tasks," next runs, run history, and Workflow start nodes
- [ ] I recorded automation to change or stop and effects on external processing and notification destinations
- [ ] I used "Disabled" for temporary suspension and "Delete" only for unnecessary Tasks
- [ ] I published after changing or stopping a Workflow trigger
- [ ] For ownership change, I checked Tasks created by the former owner and triggers dependent on their authentication
- [ ] I checked the new Task or trigger with the successor's permissions and connection
- [ ] I did not repeatedly run automation with continuing failures before confirming the cause
- [ ] After restarting, I checked history, notifications, and results in the external service

## Related articles and next articles to read

- [Configure Workflow Triggers](../build/configure-workflow-triggers.md)
- [Configure the Task Scheduler](../build/configure-task-scheduler.md)
- [Handle Expired Connections and Owner Changes](handle-expired-connections-and-owner-changes.md)
- [A Workflow Does Not Start or Fails](../troubleshooting/workflow-not-starting-or-failing.md)
