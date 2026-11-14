# Debug with Workflow Run History

When a Workflow does not work as expected, use **Run history** to check the input and output of each node and identify the problem area. This article explains how to find history, read details, narrow down the cause, and rerun a failed execution.

- To test a draft: [Test a Workflow Draft](test-workflow-drafts.md)
- To check results as a user: [Run a Published Workflow](../use/run-published-workflow.md)

## Use the history for the execution method

Where you view Workflow history differs depending on the version you want to check.

| Where to view | Target | Main purpose |
|---|---|---|
| "Run history" on the editing screen | Draft tests run from the editing screen | Verify behavior before publishing |
| "History" in "Task Scheduler" in the Project settings | Runs from the Project Task Scheduler | Check the task, time, status, and result |
| "Execution History" in the Project's Workflows screen | Published Workflow runs | Investigate problems after publishing |

> Check test runs on the editing screen separately from the history of the published version or Tasks. Where a run is displayed varies depending on the entry point used to run it, permissions, and Workflow configuration.

## Before you begin

- Confirm the approximate date and time, runner, and start method (manual/trigger) of the run you want to investigate.
- Confirm that you have permission to view run history. The visible scope varies depending on organization and Project settings.
- Run inputs and outputs may include confidential information such as email bodies and customer information. Be careful about the scope of screen sharing and file transfers.

## 1. Find the target run

### When checking from the Project Task Scheduler

1. Open "Task Scheduler" in the target Project's settings.
2. Select the "History" tab.
3. Compare the task name, date and time, and status to identify the run to investigate.
4. If a link to the Workflow result is displayed, open it to check the run details.

### When checking from Workflow run logs

Open "Execution History" in the target Project's Workflows screen. Filter by Workflow name, date and time, runner, and status. Run logs display published-version runs and exclude draft tests from the editing screen.

Runs with "Automated run" as the runner are started by triggers, schedules, or other means without direct human action.

## 2. Read the run details

When you select a target run, "Log Details" opens.

| Display | What to check |
|---|---|
| Summary | Status, elapsed time, credits, and error messages |
| Result | The final output of the Workflow |
| Steps | The order of executed nodes and each node's input and output |

### How to read statuses

- **Success**: Indicates that processing completed to the end. **It does not guarantee that the result is correct for business purposes.** Also check the output content.
- **Failed**: An error occurred at an intermediate node. Check the error message and the failed point in Steps.
- **Partially successful**: Some processing may not have completed. Check Steps to determine through which node behavior was as expected.
- **Stopped**: The run was stopped partway through. Processing completed before it stopped may remain.

## 3. Identify the problematic node

Check Steps in order from the first node and find the **node where the value first differs from what you expected**. The cause is often before the node that displays an error.

1. Confirm that the output of the Start node (input values and event information) is as expected.
2. Confirm that the previous node's output is passed correctly to the next node's input.
3. Confirm that each node's output has the format required for the next processing step.
4. Use the node where the value first differs from what you expected as the target for correction.

### Common causes

| Symptom | Possible cause | Where to check |
|---|---|---|
| Input is empty | Incorrect variable reference, or a variable from another path is referenced | [Configure Workflow Variables and Branches](configure-workflow-variables-and-branches.md) |
| An unexpected branch was taken | Incorrect condition operator or value, partial match for "Contains" | [Configure Workflow Variables and Branches](configure-workflow-variables-and-branches.md) |
| AI output format breaks down | Insufficient instructions or an ambiguous output format specification | Prompt for the relevant node |
| Error in an external service | An expired connection, insufficient permissions, or restrictions at the other service | Connection status and administrator |
| Does not start from a trigger | Not published, an expired connection, or filtering conditions | [Configure Workflow Triggers](configure-workflow-triggers.md) |
| A Project task cannot run | Task or execution-target status, input, connection, or run-condition issues | Task List and History in the Project Task Scheduler |

## 4. Save run data

You may be able to download run data in JSON format from the details screen. Attaching it when you ask an administrator or development representative to investigate allows you to communicate the situation accurately.

> Downloaded files contain inputs and outputs as they are. Be careful about where you save them and with whom you share them.

## 5. Rerun a failed execution

If a run started by an event such as a trigger fails, "Retry" may be displayed on the details screen.

1. Resolve the cause of failure first, such as an expired connection.
2. Select "Retry."
3. Check the content on the confirmation screen, then select "Re-run."
4. When "Re-run started" is displayed, check the result of the new run in the list.

- A retry runs with the **saved input**. You cannot change the input.
- If writes completed before the failure (such as saving to an Object), **rerunning may duplicate data.** Check the target data before running it.
- "Retry" may be displayed only for failed runs started by events. To retry a failed manual run, run it again from "Manual run" in the Project's Workflow list or the App's run screen.

## 6. Correct and verify

1. On the editing screen, correct the identified node.
2. Save the draft and test with values close to the input that caused the problem ([Test a Workflow Draft](test-workflow-drafts.md)).
3. Check the test result in "Run history" on the editing screen.
4. When the problem is resolved, publish it ([Publish a Workflow](publish-workflow.md)).
5. Check post-publishing results in the Project's Workflow Execution History or Task Scheduler History.

> Publishing corrections does not change the results of runs that already failed. Rerun or manually run them as needed.

## Checklist

- [ ] I distinguished whether the run I want to investigate is a published version, a Task, or a draft test.
- [ ] I identified the target in the Project's Workflow Execution History or Task Scheduler History.
- [ ] I checked the Result and Steps.
- [ ] I identified the node where the value first differed from what I expected.
- [ ] I saved and shared JSON carefully to protect confidential information.
- [ ] I checked the impact of duplication from rerunning.
- [ ] I tested after correcting and also checked the run after publishing.

## Next articles

- [Configure the Task Scheduler](configure-task-scheduler.md)
- [A Workflow Does Not Start or Fails](../troubleshooting/workflow-not-starting-or-failing.md)
- [Workflow and Automation Specifications and Limits](workflow-and-automation-specs.md)
