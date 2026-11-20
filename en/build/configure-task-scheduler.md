# Configure the Task Scheduler

In a Studio Project, use **Task Scheduler** in the Project settings to create and manage execution tasks for Agents or Workflows. This article covers schedule configuration, notifications, and execution-history checks.

- Choosing an automatic execution method: [Choose an Automatic Execution Method](../getting-started/choose-execution-method.md)
- Starting a Workflow from an event: [Configure Workflow Triggers](configure-workflow-triggers.md)

## Scope of the Task Scheduler

Open **Task Scheduler** in the target Project's settings. The page has **Task List** and **History** tabs.

| Target | Where to configure | Use |
|---|---|---|
| Tasks started by date, time, or repetition | Task List in Task Scheduler | Configure the run time, repetition, end date, and notification destinations |
| Workflow event starts | Workflow start node | Configure conditions that start from an event, such as a Webhook |

Task Scheduler is configured per Project. It does not replace event starts configured in a Workflow start node.

### Who a task runs as

| Item | Rule |
|---|---|
| Run-as user | **The task's creator.** It does not change even if someone else edits the task |
| External-service authentication for Workflows | **Only the Project's shared connections** are used (the creator's personal connections are not used) |
| When the creator leaves the organization | The task is automatically disabled |
| How to change the owner | Have the new owner recreate the task, then delete the old task |
| Conditions for creating tasks | Tasks cannot be created on the free plan. There is a per-organization limit on the number of tasks |

## Before you begin

- Check the target Project, Agent or Workflow to run, and the connections and inputs to use.
- For processing that sends to or updates external services, check the effects of duplicate scheduled runs or **Execute**.
- Check the Project permissions required to create, update, delete, and run tasks. Available actions vary by permission.
- A task runs as its creator. If the owner is expected to change, have the person taking over create the task.

## 1. Create a task

1. In the target Project's settings, open **Task Scheduler** and select **Task List**.
2. Select **Create New**.
3. Select an Agent or Workflow to run, then check the name, required inputs, and connections.
4. For a date-and-time run, configure the run time and repetition. When the target is a Workflow, also check the Workflow inputs.
5. Configure notification destinations, review the settings, and create the task.

Available start methods vary by task type in a Studio Project. Date-and-time runs are available for Agents and Workflows. A Webhook can be configured for a task whose target is a Workflow. Transcription-completed starts cannot be selected in the Studio Project task-creation screen.

Missing required inputs can prevent saving, and unavailable connections used by the execution target can cause runs to fail. For a Webhook task targeting a Workflow, use the Webhook URL generated and displayed after saving. This URL is separate from the Webhook URL used for Slack notifications. Do not copy Webhook URLs or connection credentials into chat or tickets.

{% hint style="warning" %}
**A Webhook task URL has no authentication.** Anyone who knows the URL can start the task. If you need signature verification, use a Workflow [Webhook trigger](configure-workflow-triggers.md). Webhook tasks and Webhook triggers are different mechanisms.
{% endhint %}

## 2. Configure the date, time, and repetition

For a date-and-time run, configure the run date, time, and repetition. The repetition options shown are no repetition, every hour, every day, weekdays, every week, every month, and custom.

For a custom schedule, configure the days of the week and interval. Choose 10, 15, 20, or 30 minutes, or 1, 2, 3, 4, 6, 8, 12, or 24 hours. Set an end date when the repetition must end.

Set run times in Japan Standard Time (JST) on this screen. Check the warning when your browser time zone differs from JST, and check the next run after saving. Starting at the specified time or finishing by that time is not guaranteed. Allow sufficient time for work with deadlines.

## 3. Configure notifications

In the creation or edit screen, use **Notification Destination** to select Notification Center, email, Slack, or Chatwork. Email requires an email address; Slack notifications require a Slack Webhook URL; and Chatwork requires an API token and room ID.

When you select a destination other than Notification Center, you can configure **Display output in notification**. Outputs and errors can contain confidential information, so keep recipients and displayed content to the minimum necessary. For detailed operation, see [Configure Run Result Notifications](../operations/configure-run-result-notifications.md).

## 4. Check the task after creation

In **Task List**, check the target task's status, run date and time, repetition, and notification destinations. As needed, use its action menu to edit, delete, or select **Execute**.

**Execute** starts actual processing for a scheduled task without waiting for its scheduled time. It is unavailable for disabled tasks and Webhook tasks. When the task sends or updates data externally, check its input and target before running it.

In **History**, check the date and time, status, result, and error details of a run. A Workflow result may provide a link that opens the Workflow result. When failed processing includes external sending or writing, check the destination before deciding whether to run it again.

## 5. Change or delete a task

Use editing in Task List to review the name, target, inputs, notification destinations, date and time, repetition, and end date. The Agent/Workflow type and the start method, such as Schedule or Webhook, cannot be switched after creation. Before and after a change, check the next scheduled run and effects on external processing.

Before deleting an unneeded task, check whether to retain its history, its Webhook callers, and related Workflow start nodes. Deleting a task does not delete its Agent or Workflow. **There is no feature to pause a task.** To stop a task targeting a Workflow urgently, turn off the Workflow's "Enable" setting (this has side effects, such as Email by JAI and Storage triggers being treated as deleted; see [Configure Workflow Triggers](configure-workflow-triggers.md)). Otherwise, check the impact of deletion and then delete the task.

## Checklist

- [ ] I opened Task List in the target Project's Task Scheduler
- [ ] I checked the target, inputs, and connections used by the execution target
- [ ] For a date-and-time run, I checked the run time, repetition, time zone, and end date
- [ ] I checked notification destinations and the scope of output shown in notifications
- [ ] I checked task status and the first run result after creation
- [ ] Before changing or deleting, I checked scheduled runs and effects on external processing

## Next articles

- [Change, Stop, and Hand Over Scheduled Runs](../operations/change-stop-handover-scheduled-runs.md)
- [Configure Run Result Notifications](../operations/configure-run-result-notifications.md)
- [Configure Workflow Triggers](configure-workflow-triggers.md)
- [A Workflow Does Not Start or Fails](../troubleshooting/workflow-not-starting-or-failing.md)
