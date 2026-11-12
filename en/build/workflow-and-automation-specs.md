# Workflow and Automation Specifications and Limits

In addition to being started by users from the screen, Workflows can run automatically in response to events or "Tasks." For automatic execution, it is important to use the published version rather than a draft, recognize that the Task creator is the basis for run and usage limits, and design post-failure handling in advance.

- Start from an event: [Configure Workflow triggers](configure-workflow-triggers.md)
- Create a Task: [Configure Task Scheduler](configure-task-scheduler.md)
- Limits and availability conditions: [Index of limits and availability conditions](../operations/limits-and-availability-index.md)

## Scope of this article

This article describes start conditions, the version run, treatment of the runner and usage limits, and retries for the following execution methods.

- **Manual run**: A user selects "Start Execution" from the screen.
- **Workflow trigger**: Starts in response to an event configured in the Workflow start node.
- **Task**: Starts a Task created from a published definition through triggers such as a schedule, transcription completion, or Webhook.

Displayed items and availability vary by organization, Project, permissions, and feature settings.

## Specifications by execution method

| Start method | Start condition | Version run | Runner and usage limit treatment | Retry |
|---|---|---|---|---|
| Manual run | Started by a user | A test in the editing screen can target saved editing content. When running a published Workflow from a list or similar location, the published version is used. | Context of the user who performed the operation | A failed run can be retried from the run log using "Retry" |
| Workflow trigger | An event such as a Webhook, email receipt, data change, or transcription completion | **Published version** | Depends on permissions and connection settings at the time the Workflow was published or configured, rather than the event sender. Billing and deduction details vary by configuration. | A run recorded as failed can be retried using "Retry" |
| Task: schedule | Specified date and time or repeat time | **Published version** | Whether it can run is determined by the Task creator, organization, and connection settings. | Automatic rerunning of the same processing could not be confirmed. "Run" and checking history are separate operations. |
| Task: transcription completed | Completion of the target transcription | **Published version** | Whether it can run is determined by the Task creator, organization, and connection settings. | Same as above |
| Task: Webhook | An HTTP request to the issued URL | **Published version** | Runs according to the Task-side settings, not the person who called the URL. | Same as above |

> **Saving a draft alone does not change automatic execution content.** Workflow triggers and "Tasks" use the published version. After making changes, proceed in the order of testing, checking, and publishing.

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

### Tasks

In "Tasks," create a Task from a published Workflow and configure triggers such as a schedule, transcription completion, or Webhook. A Task with missing settings such as connections, inputs, or a Webhook secret does not run. Disabling the Task or Workflow, missing required connections, and the availability status of models in the Workflow also affect whether it can run.

For schedules, configure "Date and time," "Repeat," "Timezone," and "End Date." Repeat options include "No Repeat," "Hourly," "Daily," "Weekdays," "Weekly," "Monthly," and "Custom." In "Custom," choose intervals of 10, 15, 20, or 30 minutes, or 1, 2, 3, 4, 6, 8, 12, or 24 hours. For operation steps, see [Configure Task Scheduler](configure-task-scheduler.md).

Triggers created and managed by the Workflow itself when it is published are not the same as triggers added and configured as Tasks. The former are updated when you republish the Workflow, so make changes on the Workflow side. The latter are configured, run, and managed in "In use" in "Tasks."

For a Task Webhook, entering a secret may be required depending on the settings. Treat URLs and Secrets as credentials and minimize their sharing scope.

## Limits and limit values

The following lists only values that could be numerically verified in the code under review. It does not include counts, unit prices, or concurrent runs by plan.

| Target | Maximum or configurable range | Notes |
|---|---:|---|
| Number of Workflow nodes | Up to 500 nodes when saving through the Workflow management tool. Up to 1,000 nodes in Studio pre-publish validation and the publishing API. | Limits vary by entry point. Values exceeding the limit cause an error, so check what is displayed for the entry point you use. |
| Number of Workflow connections | Up to 2,000 connections when saving through the Workflow management tool. Up to 3,000 connections in Studio pre-publish validation and the publishing API. | Validated separately from the node count. |
| Sub-Workflow "Timeout (seconds)" | 60–1,800 seconds | The default is 300 seconds. |
| Sub-Workflow "Max Nesting Depth" | 1–10 | The default is 5. |
| Maximum count for node "Retry on failure" | 1–10 times | The default when enabled is 3 times. |
| Workflow run input for a published App: number of files | Up to 10 items per input | If the definition-side setting is fewer than 10 items, that value applies, and a specification exceeding 10 items is not accepted. Check each input setting and screen display to determine whether the same limit applies to Workflow Tasks created in the standard "Tasks" screen. |
| Workflow input for a Task: file size | Follows the definition-side input setting | Limits on file formats and sizes are validated using each input's settings. |
| Custom "Repeat Interval" (minutes) | 10, 15, 20, or 30 minutes | Choices |
| Custom "Repeat Interval" (hours) | 1, 2, 3, 4, 6, 8, 12, or 24 hours | Choices |

Even if you can configure a value up to the limit, it does not necessarily operate reliably due to constraints on external APIs, models, write destinations, or execution time. Start with a small configuration and check elapsed time and consumption in run history.

## Retry and rerun

"Retry on failure" is a setting for supported nodes in a Workflow. "Retry" is an operation that starts a failed Workflow run in the run log again with saved input.

| Operation | Target | Notes |
|---|---|---|
| "Retry on failure" | Nodes in a Workflow | Increasing the number of times increases provider calls and costs. |
| "Retry" in the run log | Entire failed Workflow | Because it runs with saved input, processing may be duplicated if write operations have already occurred. |
| "Run" | A Task in "In use" in "Tasks" | This starts it without waiting for its schedule; it is not automatic retry after a failure. |

Before you "Re-run failed run," check whether target records, external sends, or notifications have already completed. If a write to a custom object or similar item completed before a failure was displayed, retrying may duplicate data.

## Common misconceptions

### Saving a draft reflects it in automatic execution

It does not. Workflow triggers and Tasks use the published version. You must publish after making changes.

### A Task Webhook and a Workflow Webhook trigger are the same

They are different. The former starts with a Webhook setting in "Tasks," while the latter starts a Workflow start node from an event. Their configuration locations and scope of ownership and updates differ.

### A failure is always automatically retried

Not necessarily. Configure node-level "Retry on failure" explicitly. Automatic retrying of a failed Task with the same processing is not included in the scope confirmed here.

### "Retry" safely processes it only once

This is not guaranteed. It reruns the entire Workflow with saved input, so external sends or writes may be duplicated if they have already completed. Design duplicate-prevention keys or processed flags in the external system.

## If you cannot confirm it

The following cannot be uniformly determined from the screen or code under review alone.

- Run counts, credit unit prices, concurrent runs, and retention periods by contract or plan
- Final billing amount for each execution method
- Retries, timeouts, and rate limits on external services
- Whether items are displayed based on organization feature settings, permissions, and Project settings

In "History" or the Workflow run log, check the run ID, start time, error details, input, and runner. Then ask an administrator to check the Workflow's published or disabled status, the Task creator's organization membership, and the monthly credit limit. For contract and availability conditions, see [Index of limits and availability conditions](../operations/limits-and-availability-index.md).

## Next articles to read

- [Configure Workflow triggers](configure-workflow-triggers.md)
- [Configure Task Scheduler](configure-task-scheduler.md)
- [Index of limits and availability conditions](../operations/limits-and-availability-index.md)
