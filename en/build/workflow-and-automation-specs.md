# Workflow and Automation Specifications and Limits

In addition to being started by users from the screen, Workflows can run automatically in response to events or a schedule configured in Project Settings > Task Scheduler. For automatic execution, it is important to use the published version rather than a draft and design post-failure handling in advance.

- Start from an event: [Configure Workflow triggers](configure-workflow-triggers.md)
- Configure a schedule: [Configure Task Scheduler](configure-task-scheduler.md)
- Limits and availability conditions: [Index of limits and availability conditions](../operations/limits-and-availability-index.md)

## Scope of this article

This article describes start conditions, the version run, treatment of the runner and usage limits, and retries for the following execution methods.

- **Manual run**: A user selects "Start Execution" from the screen.
- **Workflow trigger**: Starts in response to an event configured in the Workflow start node.
- **Task Scheduler**: Starts on a schedule configured in Project Settings.

Displayed items and availability vary by organization, Project, permissions, and feature settings.

## Key points: who a run executes as and which credentials it uses

| Start method | Run-as user | External-service authentication | When the run-as user leaves the organization | How to change the owner |
|---|---|---|---|---|
| Manual run | The user who performed the operation | Only the Project's shared connections | — | — |
| Workflow trigger | **The person who first published that Start node.** Not transferred even if someone else republishes | Only the Project's shared connections | Automatically disabled | Delete the trigger node, add it again, and publish (the Webhook URL and Email by JAI address change) |
| Task Scheduler | **The task's creator.** Does not change when edited | Only the Project's shared connections (for Workflows) | Automatically disabled | Recreate the task |

- **Workflows do not use the runner's personal connections.** For automatic execution, the required shared connections for external services must be set up in the Project.
- In the runner column of the run history, trigger and Task Scheduler runs show the owner's name. Only runs by a service account are displayed as "Automated run."
- **There is no pause feature.** To stop urgently, turn off the Workflow's "Enable" setting. When you do, Email by JAI and Storage triggers are treated as deleted. The Webhook URL changes if the trigger is recreated.
- Tasks cannot be created on the free plan. There is a per-organization limit on the number of tasks.

## Specifications by execution method

| Start method | Start condition | Version run | Runner and usage limit treatment | Retry |
|---|---|---|---|---|
| Manual run | Started by a user | A test in the editing screen can target saved editing content. When running a published Workflow from a list or similar location, the published version is used. | The user who performed the operation | Check the input and run it again from the normal execution screen |
| Workflow trigger | An event such as a Webhook, email receipt, data change, or transcription completion | **Published version** | Runs as the person who first published the Start node, not the event sender. External services use the Project's shared connections. Billing and deduction details vary by configuration. | Use "Retry" displayed on the failed run |
| Task Scheduler | Specified date and time or repeat time | **Published version** | Runs as the task's creator. External services use the Project's shared connections. | Use "Retry" displayed on the failed run (displayed for every failed run linked to a task) |

> **Saving a draft alone does not change automatic execution content.** Workflow triggers and Task Scheduler use the published version. After making changes, proceed in the order of testing, checking, and publishing.

### Manual run

Use a run from the editing screen to check inputs, connections, outputs, and error handling. However, for a Workflow that includes external sending or record updates, even a test may affect actual data. Check input fields, destinations, and write operations before and after publishing.

### Workflow triggers

Configure Workflow triggers in a start node. Use displayed events such as "Webhook trigger," "Gmail: New Email," "Outlook: New Email," and "Speech: Transcription completed" as the starting point. Publishing is required for enablement, and a new draft is not used after changes until you republish. For details, see [Configure Workflow triggers](configure-workflow-triggers.md).

For a Webhook trigger, you can define the following values.

| Screen field | Source |
|---|---|
| "Query Parameters" | URL query string |
| "Header Parameters" | HTTP request headers |
| "Body Parameters" | HTTP request body |

A Webhook URL is generated after publishing. Configure "Signature Verification," "Signature Header," and "Webhook Secret" as needed to prevent URL exposure and unintended starts.

{% hint style="warning" %}
**A Webhook trigger and a Webhook task in the Task Scheduler are different mechanisms.** A Webhook trigger supports signature verification, but a Webhook task has no authentication, and anyone who knows the URL can start it.
{% endhint %}

Each time a trigger runs, a notification is sent to the notification center of every active member of the Project. This notification cannot be turned off.

### Task Scheduler

In Task Scheduler in Project Settings, configure a schedule to run a published Workflow at a specified date and time or repeat time. Missing required connections, a disabled Workflow, and the availability status of models in the Workflow also affect whether it can run.

In the Project Task Scheduler, configure the run date and time, repetition, and end date. Times are set in Japan Standard Time (JST); there is no time-zone selector. Repeat options include "No Repeat," "Every hour," "Every day," "Weekdays," "Every week," "Every month," and "Custom." In "Custom," choose intervals of 10, 15, 20, or 30 minutes, or 1, 2, 3, 4, 6, 8, 12, or 24 hours. For operation steps, see [Configure Task Scheduler](configure-task-scheduler.md).

Event triggers configured in a Workflow start node are different from schedules configured in Task Scheduler. Change event triggers, including transcription completion, on the Workflow side. Configure schedules in Task Scheduler in Project Settings, and check results in the execution history in the Project's Workflows screen.

## Limits and limit values

The main limits and configurable ranges are as follows. They do not include counts, unit prices, or concurrent runs by plan.

| Target | Maximum or configurable range | Notes |
|---|---:|---|
| Number of Workflow nodes | Up to 1,000 nodes in Studio pre-publish validation and the publishing API. Up to 500 nodes on some save paths. | Limits vary by entry point. Values exceeding the limit cause an error. |
| Number of Workflow connections | Up to 3,000 connections in Studio pre-publish validation and the publishing API. Up to 2,000 connections on some save paths. | Validated separately from the node count. |
| Agent node "Timeout" | 60–1,200 seconds | — |
| Request received by a Webhook trigger | Up to 1 MB | — |
| Sub-Workflow "Timeout (seconds)" | 60–1,800 seconds | The default is 300 seconds. |
| Sub-Workflow "Max Nesting Depth" | 1–10 | The default is 5. |
| Maximum count for node "Retry on failure" | 1–10 times | The default when enabled is 3 times. |
| Workflow run input for a published App: number of files | Up to 10 items per input | If the definition-side setting is fewer than 10 items, that value applies, and a specification exceeding 10 items is not accepted. Check each input setting and screen display to determine whether the same limit applies to Workflows configured in Task Scheduler. |
| Workflow input for Task Scheduler: file size | Follows the definition-side input setting | Limits on file formats and sizes are validated using each input's settings. |
| Custom "Repeat Interval" (minutes) | 10, 15, 20, or 30 minutes | Choices |
| Custom "Repeat Interval" (hours) | 1, 2, 3, 4, 6, 8, 12, or 24 hours | Choices |

Even if you can configure a value up to the limit, it does not necessarily operate reliably due to constraints on external APIs, models, write destinations, or execution time. Start with a small configuration and check elapsed time and consumption in run history.

## Retry and rerun

"Retry on failure" is a setting for supported nodes in a Workflow. "Retry" is an operation that starts a failed Workflow run in the run log again with saved input.

| Operation | Target | Notes |
|---|---|---|
| "Retry on failure" | Nodes in a Workflow | Increasing the number of times increases provider calls and costs. |
| "Retry" in the run log | Entire failed Workflow | Because it runs with saved input, processing may be duplicated if write operations have already occurred. |
| "Execute" | An enabled scheduled task in Task Scheduler in Project Settings | This starts it without waiting for its schedule; it is not automatic retry after a failure. It is unavailable for Webhook tasks. |

Before you "Re-run failed run," check whether target records, external sends, or notifications have already completed. If a write to a custom object or similar item completed before a failure was displayed, retrying may duplicate data.

## Common misconceptions

### Saving a draft reflects it in automatic execution

It does not. Workflow triggers and Task Scheduler use the published version. You must publish after making changes.

### Task Scheduler and a Workflow event trigger are the same

They are different. Task Scheduler starts on a schedule in Project Settings, while a Workflow event trigger starts from an event configured in the start node. Configure events, including transcription completion, on the Workflow side.

### A failure is always automatically retried

Not necessarily. Configure node-level "Retry on failure" explicitly. To redo an entire failed Workflow, use "Retry" in the run log.

### "Retry" safely processes it only once

This is not guaranteed. It reruns the entire Workflow with saved input, so external sends or writes may be duplicated if they have already completed. Design duplicate-prevention keys or processed flags in the external system.

## Items not covered on this page

The following vary by contract, plan, organization settings, and external services, so this page does not cover them.

- Run counts, credit unit prices, concurrent runs, and retention periods by contract or plan
- Final billing amount for each execution method
- Retries, timeouts, and rate limits on external services
- Whether items are displayed based on organization feature settings, permissions, and Project settings

In the execution history in the Project's Workflows screen, check the run ID, start time, error details, input, and runner. Then ask an administrator to check the Workflow's published or disabled status, Task Scheduler in Project Settings, and the monthly credit limit. For contract and availability conditions, see [Index of limits and availability conditions](../operations/limits-and-availability-index.md).

## Next articles to read

- [Configure Workflow triggers](configure-workflow-triggers.md)
- [Configure Task Scheduler](configure-task-scheduler.md)
- [Index of limits and availability conditions](../operations/limits-and-availability-index.md)
