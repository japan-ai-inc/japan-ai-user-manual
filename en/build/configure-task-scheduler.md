# Configure the Task Scheduler

In Studio, the former Workflow and Task Scheduler have been combined into **"Tasks."** With Tasks, you can run Agent tasks and published Workflows at specified dates and times, on repeats, or from the start triggers of each Workflow, and manage settings and history in one place.

This article explains how to find an execution target in the "Tasks" page, create a task, and check its schedule, notifications, and run results.

In environments where you can open legacy Workflow or Task Scheduler URLs, migration guidance to "Tasks" may appear. Even if you can open the legacy screen during the migration period, use the following "Tasks" page as the entry point for everyday creation, execution, configuration, and history checks.

- Choosing an automatic execution method: [Choose an Automatic Execution Method](../getting-started/choose-execution-method.md)
- Configuring Workflow start triggers: [Configure Workflow Triggers](configure-workflow-triggers.md)

## What is the Task Scheduler?

"Tasks" is the entry point for creation, use, and management that was previously divided between the Workflow list and Task Scheduler. You can open **"Tasks"** from **"Other features"** in navigation. You can pin it to the sidebar if you use it often.

| Tab | Main use |
|---|---|
| **In use** | Check the status, runs, history, and settings of tasks you use |
| **Find** | Find Workflow and Agent task definitions published by colleagues and create tasks for yourself |
| **Build** | Create, edit, and publish Agent tasks or Workflows |

"Tasks" is an entry point for managing runs; it does not replace event triggers configured in a Workflow's start node. Agent tasks run by date, time, and repeats, while start triggers configured in a Workflow, such as email receipt and webhooks, have different targets and configuration methods.

| Execution method | Main trigger | Where to configure and check |
|---|---|---|
| Agent task schedule | Date and time, repeats, and transcription completion when available | The creation and settings screen in "Tasks" |
| Workflow task | Start triggers defined in the Workflow | The Workflow's start node. Check status, connections, and inputs for your task in "Tasks" |

## Before you begin

- Check the Agent or Workflow to run and the target organization and Project.
- When using a Workflow, check that its creator has published it and that you are allowed to use it.
- Check the permissions required for execution, creation, and Workflow creation or publication. Available tabs and operations vary with organization settings and permissions.
- For Workflows that require connections, required inputs, or a Webhook Secret, prepare them. You may be unable to create or enable a task while they are not set.
- For processing that sends to or updates external services, check whether duplicate scheduled or manual runs are acceptable.

## 1. Open "Tasks" and select an execution target

1. Open **"Tasks"** from **"Other features"** in navigation.
2. To check or change an existing task, select **"In use."**
3. To create a task for yourself from a published definition, select **"Find"** and search by name or description.
4. To create a new Agent task or Workflow, select **"Build,"** choose **"New,"** then choose **"New agent task"** or **"New workflow."**

When creating an Agent task, configure its name, description, Agent, prompt, default schedule, visibility, and organization-wide execution. Saving alone does not affect users of a published definition. When you are ready to let users use it, publish it and check its visibility.

When creating a Workflow, configure nodes and its start method on the canvas. For Workflow configuration, testing, and publication, see [Configure Workflow Nodes](configure-workflow-nodes.md) through [Publish a Workflow](publish-workflow.md).

## 2. Create a task from a published definition

Select the target in "Find," then select **"Create task."** You can create multiple tasks from the same definition. In the creation screen, check at least the following.

| Item | What to check |
|---|---|
| Name | Whether it has a name you can distinguish in the list |
| Execution target | Whether the Agent or Workflow to run is intended |
| Connection | Whether required connections are "Connected" and none require re-authentication |
| Input fields | Whether required inputs and initial values suit the work |
| Webhook Secret | For a Webhook that verifies signatures, whether you entered the same secret as the sender securely |
| Notification destination | Who receives success and failure notifications |
| Status | Whether to enable it immediately after creation |

For tasks created from a Workflow, the creator may manage the Workflow's start trigger or schedule. In this case, the creation screen displays **"The author decides when this definition runs; it cannot be changed here."** Do not replace the date, time, or event type at your own discretion; ask the Workflow creator to change and republish it if needed.

You may be unable to create a task unless required connections, inputs, and the Webhook Secret are ready. Resolve displayed missing items before creating it. Do not copy Webhook URLs or secrets, or connection credentials, into chat, tickets, or screen sharing.

## 3. Configure a schedule

For tasks where you can configure date, time, and repeats, check **"Frequency"** and **"Run at."** Available choices vary by task type.

| Repeat | How it runs |
|---|---|
| No repeat | Runs once at the specified date and time |
| Hourly | Runs every hour |
| Daily | Runs daily at the specified time |
| Weekdays | Runs at the specified time on weekdays |
| Weekly / Monthly | Repeats based on the specified day of week or date |
| Custom | Specify days of the week and repeat interval |

For a custom repeat, choose an interval of 10, 15, 20, or 30 minutes, or 1, 2, 3, 4, 6, 8, 12, or 24 hours. If the repeat needs to end, set an **"End Date."**

Always check the time zone saved on the task and the **"Next run"** display. Starting exactly at the specified time or finishing by that time is not guaranteed. Set sufficient time for work with deadlines.

> Workflow tasks run by a trigger do not have an editable schedule. Check event-based start conditions in the Workflow's start node.

## 4. Configure notifications

In the creation screen or **"Settings"** for an existing task, configure **"Notifications."** Usually, you can select multiple notification methods below.

| Notification destination | Additional required settings |
|---|---|
| Notification Center | None |
| Email | Email address |
| Slack | Slack Webhook URL |
| Chatwork | Chatwork API token and Chatwork room ID |

When you select an external notification destination, you can configure **"Display output in notification."** Output and errors may contain confidential information, so check who can view the destination and keep it to the minimum necessary. For detailed notification configuration and operation, see [Configure Run Result Notifications](../operations/configure-run-result-notifications.md).

## 5. Check it in "In use" after creation

After creation, check the target row in **"In use."** The list displays the name, source, status, run, next run, and latest result.

1. Confirm that the status is **"Enabled."** If execution is stopped organization-wide, it will not run even if you enable it here.
2. For a task with a schedule, check that **"Next run"** is as intended.
3. If connections, inputs, or a Webhook Secret are missing, check **"Connection required,"** **"Input required,"** or **"Webhook secret required"** displayed in the status, and resolve them in **"Settings."**
4. As needed, select **"Run"** from the row menu, select the trigger to start, and run it once. Manual runs start actual processing, so check the impact of external sending and updates before running.
5. Check results for the target task in **"History."** To check all of your tasks together, use **"Run history (all tasks)."**

## If it did not run or failed

First, check the status, settings, and history in "In use." Typical displays and responses are below.

| Display or status | What to do |
|---|---|
| **"Turn the task on to run it"** | Before enabling the task, check required inputs, connections, and notification destinations. |
| **"Stopped org-wide — cannot run"** | Execution is stopped on the organization side. Check with an administrator. |
| **"This task cannot run yet. Complete the following settings: …"** | Configure the displayed connections, inputs, Webhook Secret, or other requirements. |
| **"The task could not be completed."** | Check history, the target Agent or Workflow, connections, inputs, and organization usage conditions in order. |
| **"This workflow is stopped org-wide, so you can't create a task from it."** | Check the organization execution status with the Workflow creator or administrator. |

For detailed investigation of Workflow nodes, inputs, and outputs, see [Debug with Workflow Run History](debug-workflow-run-history.md). If failed processing includes external sending or writing, do not run it repeatedly until you check the history and destination system.

## Changes, stopping, and deletion after creation

When you open **"Settings"** for the target row in "In use," you can change screen-editable items such as the task name, notifications, status, inputs, connections, and schedule. For a start trigger owned by the Workflow itself, the following appears: **"This is a trigger for the workflow itself. It updates when the workflow is published, so make changes in the workflow."** In this case, edit, test, and publish the Workflow to make changes.

To stop it temporarily, change the status to **"Disabled."** When it is no longer needed, use **"Delete this task."** Deleting a task normally does not affect the source Workflow or other tasks. However, Workflow start triggers or external-system Webhook settings may remain. To stop all automatic execution, check both.

For procedures to change, stop, and hand over tasks, see [Change, Stop, and Hand Over Scheduled Runs](../operations/change-stop-handover-scheduled-runs.md).

## Checklist

- [ ] Opened "Tasks" from "Other features" and selected "In use," "Find," or "Build" for the purpose
- [ ] Checked the execution target Agent or published Workflow
- [ ] Configured required connections, inputs, and Webhook Secret
- [ ] If a schedule can be configured, checked frequency, run date and time, time zone, end date, and next run
- [ ] Decided the notification destination and whether to include output in notifications
- [ ] Checked enabled status and the first run result
- [ ] Compared run history with results in the external service
- [ ] When stopping or deletion is required, also checked related Workflow start triggers

## Next articles

- [Change, Stop, and Hand Over Scheduled Runs](../operations/change-stop-handover-scheduled-runs.md)
- [Configure Run Result Notifications](../operations/configure-run-result-notifications.md)
- [Check Execution Consumption and Usage](../operations/check-execution-consumption-and-usage.md)
- [Workflow and Automation Specifications and Limits](workflow-and-automation-specs.md)
