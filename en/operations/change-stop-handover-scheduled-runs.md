# Change, Stop, and Hand Over Scheduled Runs

Scheduled runs configured in a Studio Project's Task Scheduler and event starts configured in Workflow start nodes can start processing while an owner does not have the screen open. When a purpose changes, authentication expires, or ownership changes, identify, change, stop, and hand over the target automation.

For new setup, see [Configure the Task Scheduler](../build/configure-task-scheduler.md). For event starts, see [Configure Workflow Triggers](../build/configure-workflow-triggers.md).

## Summary: who each automation runs as

| Automation | Runs as | What does not change | When the owner leaves the organization |
|---|---|---|---|
| Workflow trigger (event start in a start node) | The person who first published that start node | Republishing by someone else does not transfer it | Automatically disabled |
| Task Scheduler task | The task creator | Editing the task does not change who it runs as | Automatically disabled |
| Scheduled run created with the SDK or API | The creator of the Project API key used | The key is valid only while its creator is an active member of the Project | When the creator is no longer an active Project member, the key returns 403 errors |

- Automatic runs **require a shared connection** for external services. Workflows use only shared connections.
- In execution history, runs started by triggers or schedules show the owner's name. "Automatic run" is shown only for runs by a service account.
- **There is no pause feature.** To stop in an emergency, turn off the Workflow's enable switch (see "3. Stop").
- To change the owner, you must **delete and re-add the trigger node** or **recreate the task**. Republishing or editing by the successor does not change who it runs as (see "4. Hand over when the owner changes").

## Scope of this article

| Type | Start trigger | Main place to check |
|---|---|---|
| Project task | Date, time, repetition, or a Webhook whose target is a Workflow | Task List and History in Task Scheduler in Project settings |
| Workflow event start | Webhook, received email, changes to data or files, and similar events | Start node, publishing state, and execution history of the target Workflow |

Editing or deleting a Project task is separate from changing and publishing a Workflow start node. Changing a start node only in a draft does not change a published event start.

> **Task Scheduler Webhook tasks have no authentication.** Anyone who knows the URL can start them. They are a different mechanism from Workflow Webhook triggers (which verify signatures). Treat a Webhook task URL like a password.

## Before you begin

- Check the organization, Project, App, execution target, external send or update destination, and notification destinations.
- Check the Project permissions required to change or stop the target.
- For high-impact processing, decide the stop time, restart conditions, reviewer, and contact.
- When connections or authentication are involved, check the shared connection and its authentication account.

## 1. Inventory running automation

### Check Project tasks

1. In the target Project's settings, open **Task Scheduler** and find the target in **Task List**.
2. Check the task name, execution target, start method, status, creator, and notification destinations. The creator is who the task runs as.
3. For a date-and-time run, record its run time, repetition, and end date.
4. In **History**, check the latest success or failure, date and time, errors, and run result.

### Check Workflow event starts

1. Open the target Workflow and check whether its start node has an event start.
2. Check the event type, target, filter conditions, and the status of the shared connection used.
3. Check that the Workflow is published and that its enable switch is on.
4. In execution history, check whether it starts on the expected events and whether there are duplicate or consecutive failures. The executor column shows the trigger's owner (the person who first published the start node).

### Check scheduled runs created with the SDK or API

Scheduled runs created with a Project API key run as the key's creator. Check the key creator and whether that person is an active member of the Project. For key management, see [Manage Project API Keys](manage-project-api-keys.md).

Webhook URLs and receiving addresses are information that can start processing externally. Do not copy them into inventories or inquiries more than necessary, and limit their sharing scope.

## 2. Change a Project task

Edit the target in the task list and review its name, execution target, input, notification destinations, date, time, repetition, and end date.

1. Before changing it, record the execution target, input, notification destinations, and the latest run time.
2. If a run is scheduled during the change, make sure the next run time does not overlap with your work.
3. Edit the items to change. When changing date, time, or repetition, check the run date, time, repetition, and end date together.
4. After saving, check the status, run time, and notification destinations again.
5. Check the result with low-impact conditions, and check the result in the external service if needed.

Editing a task does not change who it runs as (the creator). You cannot switch between Agent and Workflow, or between a schedule and a Webhook start method, after creation. To switch to a different type or start method, check the target's input, connections, and notifications, create a new task, and prevent duplicate runs with the old task. If you change a Workflow's structure, test it before publishing.

## 3. Stop

There is no pause or resume feature. Stop each target as follows.

| Target | How to stop | Notes |
|---|---|---|
| Workflow (including triggers) | Turn off the Workflow's enable switch. Use this for emergency stops too | Email by JAI and storage triggers are treated as deleted. If you recreate a Webhook trigger, its Webhook URL changes. When restarting, check the triggers and caller settings |
| Only a Workflow event start | Change or delete the start node and publish | Changing only a draft does not stop the published event start |
| Project task | Delete the task | Before deleting, check the scope of impact, alternative procedure, history that must be kept, Webhook callers, and notification recipients. Deleting a task does not delete the target Agent or Workflow itself |

## 4. Hand over when the owner changes

Who an automation runs as does not change when a successor republishes or edits it. When the former owner leaves the organization, that person's triggers and tasks are automatically disabled. Before the change, switch to the successor using the following steps.

1. In the Project task list, check the target tasks and their creators. Also list Workflow triggers and the Project API keys used to create scheduled runs with the SDK or API.
2. Record the purpose, execution target, input, date, time or conditions, notification destinations, connections, and recent history.
3. Prepare the Project, Workflow, and external-service permissions the successor needs. Check that the shared connection used for automatic runs is authenticated with a business account that remains usable after the former owner leaves.
4. Have the successor take over.
   - **Task**: The successor creates a new task. To prevent it from running at the same time as the old task, delete the old task after creating the new one.
   - **Workflow trigger**: The successor deletes and re-adds the trigger node, then publishes. The Webhook URL and Email by JAI receiving address change, so update the callers and senders.
   - **Scheduled run created with the SDK or API**: The successor creates a Project API key and recreates the scheduled run with that key.
5. Run it under low-impact conditions, and check that the executor in history is the successor, along with notifications and results in the external service.
6. Keep needed settings and history as records, then proceed with removing the former owner from the Project or other steps.

## 5. When failures repeat

1. If there is impact, stop new starts by turning off the Workflow's enable switch or deleting the task.
2. In the task's **History** or the Workflow's execution history, check the failure date and time, target, displayed message, latest success, and whether it occurred right after a change.
3. Check the task status, execution target, input, shared connection, notification destinations, and the Workflow's publishing state and enable switch.
4. Check whether the owner (the task creator, the person who first published the trigger, or the API key creator) is still in the organization and Project.
5. Check permissions and target data in the external service.
6. For failures inside a Workflow, check the node, input, output, and error in execution history.
7. After fixing it, check a limited target and match success, notifications, and external-service results before resuming.

## Checklist

- [ ] I checked the Project task list, history, Workflow start nodes, and scheduled runs created with the SDK or API.
- [ ] I recorded who each automation runs as (creator, first publisher, or API key creator).
- [ ] I recorded the impact of changing or stopping it on external processing and notification destinations.
- [ ] I have not shared Webhook task URLs more than necessary.
- [ ] I used the enable switch for emergency stops and checked trigger settings when restarting.
- [ ] For an owner change, I re-added the trigger node or recreated the task.
- [ ] After resuming, I checked history, notifications, and external-service results.

## Related articles

- [Configure the Task Scheduler](../build/configure-task-scheduler.md)
- [Configure Run Result Notifications](configure-run-result-notifications.md)
- [Configure Workflow Triggers](../build/configure-workflow-triggers.md)
- [Handle Expired Connections and Owner Changes](handle-expired-connections-and-owner-changes.md)
- [Manage Project API Keys](manage-project-api-keys.md)
- [A Workflow Does Not Start or Fails](../troubleshooting/workflow-not-starting-or-failing.md)
