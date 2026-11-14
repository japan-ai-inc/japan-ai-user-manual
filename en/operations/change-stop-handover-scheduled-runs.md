# Change, Stop, and Hand Over Scheduled Runs

Scheduled runs configured in a Studio Project's Task Scheduler and event starts configured in Workflow start nodes can start processing while an owner does not have the screen open. When a purpose changes, authentication expires, or ownership changes, identify, change, stop, and hand over the target automation.

For new setup, see [Configure the Task Scheduler](../build/configure-task-scheduler.md). For event starts, see [Configure Workflow Triggers](../build/configure-workflow-triggers.md).

## Scope of this article

| Type | Start trigger | Main place to check |
|---|---|---|
| Project task | Date, time, repetition, or a Webhook whose target is a Workflow | Task List and History in Task Scheduler in Project settings |
| Workflow event start | Webhook, received email, changes to data or files, and similar events | Start node, publishing state, and execution history of the target Workflow |

Editing or deleting a Project task is separate from changing and publishing a Workflow start node. Changing a start node only in a draft does not change a published event start.

## Before you begin

- Check the organization, Project, App, execution target, external send or update destination, and notification destinations.
- Check the Project permissions required to change or stop the target.
- For high-impact processing, decide the stop time, restart conditions, reviewer, and contact.
- When connections or authentication are involved, check the authentication account and shared or personal connections.

## 1. Inventory running automation

### Check Project tasks

1. In the target Project's settings, open **Task Scheduler** and find the target in **Task List**.
2. Check the task name, execution target, start method, status, creator, and notification destinations.
3. For a date-and-time run, record its run time, repetition, and end date.
4. In **History**, check the latest success or failure, date and time, errors, and run result.

### Check Workflow event starts

1. Open the target Workflow and check whether its start node has an event start.
2. Check the event type, target, filtering conditions, and connection state for external accounts.
3. Check whether the Workflow is published.
4. In execution history, check whether it starts on intended events and whether there are duplicates or consecutive failures.

Webhook URLs and receiving addresses can be used to start processing externally. Do not copy them unnecessarily into inventory sheets or inquiries, and limit their sharing scope.

## 2. Change a Project task

Edit the target in Task List and review its name, execution target, inputs, notification destinations, date and time, repetition, and end date.

1. Before changing it, record the execution target, inputs, notification destinations, and next run time.
2. If a run is scheduled during the change, ensure that its next run time does not overlap the change work.
3. Edit the required items. When changing the date and time or repetition, check the run date, time, repetition, and end date together.
4. After saving, check status, run time, and notification destinations again.
5. Check the run result under low-impact conditions and, as necessary, the result in the external service.

The Agent/Workflow type and the start method, such as Schedule or Webhook, cannot be switched after creation. When switching to a different type or start method, check the target's inputs, connections, and notifications, then create a new task and prevent it from running alongside the old task. When changing a Workflow's configuration, publish it after testing.

## 3. What to check before stopping

### Project tasks

A Studio Project UI action to temporarily suspend and restart an existing task could not be confirmed in Task Scheduler. When scheduled runs must stop, before deletion check the scope of impact, alternative procedure, need to retain history, Webhook callers, and notification recipients. Deleting a task does not delete its Agent or Workflow.

### Workflow event starts

To stop a Workflow event start, change or delete the target Workflow's start node and publish it. Changing only a draft does not stop the published event start.

## 4. Hand over when ownership changes

Tasks and Workflow event starts can depend on their creator or publisher, or on that person's connections. Inventory targets and external accounts before the handover.

1. In the Project's Task List, check target tasks and their creators. Also check external accounts used by Workflow event starts.
2. Record the purpose, execution target, inputs, schedule or conditions, notification destinations, connections, and recent history.
3. Prepare the Project, Workflow, and external-service permissions required by the successor. Do not assume that the former owner's personal connection remains usable.
4. Prepare a new task or connection in the successor's configuration. Ensure it does not run at the same time as the old task.
5. Run under low-impact conditions and check History, notifications, and the result in the external service.
6. After confirming that the new configuration runs stably, decide whether to delete the old task or retain necessary settings and history separately as a record.
7. For Workflow event starts, separately check the successor's connection, target conditions, and publishing state. When changing them, publish after testing.

## 5. When failures repeat

1. If there is impact, check the effect of deleting the Project task or change and publish the Workflow event start to stop new starts.
2. In the task's **History** or Workflow execution history, check the failure time, target, displayed message, latest success, and whether it followed a change.
3. Check task status, execution target, inputs, connections, notification destinations, and Workflow publishing state.
4. Check external-service permissions and target data.
5. For a failure in a Workflow, check nodes, inputs, outputs, and errors in execution history.
6. After correction, check with a limited target, compare success, notifications, and external-service results, then restart.

## Checklist

- [ ] I checked the Project's Task List, History, and Workflow start nodes
- [ ] I recorded the effects of changes or stopping on external processing and notification destinations
- [ ] Before deleting a Project task, I checked its impact and the history that must be retained
- [ ] I published after changing or stopping a Workflow event start
- [ ] For ownership changes, I checked Project permissions, connections, and external-service permissions
- [ ] After restarting, I checked History, notifications, and the external-service result

## Related articles

- [Configure the Task Scheduler](../build/configure-task-scheduler.md)
- [Configure Run Result Notifications](configure-run-result-notifications.md)
- [Configure Workflow Triggers](../build/configure-workflow-triggers.md)
- [Handle Expired Connections and Owner Changes](handle-expired-connections-and-owner-changes.md)
- [A Workflow Does Not Start or Fails](../troubleshooting/workflow-not-starting-or-failing.md)
