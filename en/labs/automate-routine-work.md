# Automate Routine Work

In this exercise, you will create a Workflow that summarizes and categorizes fictional daily reports, then compare draft and published run results. Do not add external sending, data updates, triggers, or schedules. Confirm that dates, assignees, and decisions not in the input are not added, then clean up the practice Workflow.

## Related articles

- [Configure Workflow nodes](../build/configure-workflow-nodes.md)
- [Configure Workflow variables and branches](../build/configure-workflow-variables-and-branches.md)
- [Test Workflow drafts](../build/test-workflow-drafts.md)
- [Publish a Workflow](../build/publish-workflow.md)
- [Debug with Workflow run history](../build/debug-workflow-run-history.md)
- [Run a published Workflow](../use/run-published-workflow.md)

## Before you begin

- Open a test Project where you can create, edit, run, and publish Workflows. If the feature is not displayed, check permissions and availability conditions in the [Index of limits and availability conditions](../operations/limits-and-availability-index.md).
- Do not edit an existing business Workflow; use only fictional input. Draft testing is also a run, so do not add nodes that send externally or update data.
- The linked articles in each step are the source of truth for detailed node configuration, variables, testing, and publishing procedures.

## Sample used in this exercise

| Item | Value |
|---|---|
| Workflow name | Daily report organization (practice) |
| Description | Summarizes and categorizes daily report text. Does not send externally or update data. |
| Input variable | `daily_report` |
| Output variable | `summary_result` |

Enter the following during testing.

```text
Today's work: Updated materials for the product briefing and conducted an internal review.
Progress: The briefing structure has been agreed on. Check the demo environment tomorrow.
Issue: Sales confirmation is required for pricing shown in the materials.
Tomorrow's plan: Check the demo environment and request confirmation of the pricing.
```

Set the following instruction in the LLM node, and insert the start node input variable from the on-screen suggestions in the daily-report section.

> Based only on the following daily report, organize it in English in this order: summary, progress, issues, plans, and items requiring confirmation. Do not infer facts, deadlines, assignees, or numbers that are not in the input. If there are no items requiring confirmation, write "None."

## Step 1. Create the draft framework

**Goal:** Create a Workflow that separates input, text processing, and output.

1. On the Workflow creation screen, set the sample name and description.
2. Add Start, LLM, and End nodes, and connect them from start to end.
3. Check the connections rather than node positions, and use **"Checklist"** to check for missing items.
4. Select **"Save draft"** and confirm that it is a practice draft.

For adding and connecting nodes, see [Configure Workflow nodes](../build/configure-workflow-nodes.md). **Checkpoint:** A connection reaches from Start to End, and there are no nodes that send or update externally.

## Step 2. Configure input and output transfer

**Goal:** Pass the daily report to the LLM and make the organized result the Workflow result.

1. Add an input field for the daily report to the Start node. Set the variable name to `daily_report` and the display name to "Daily report text."
2. Add the sample instruction to the LLM node and insert `daily_report` from the on-screen suggestions. Typing only the variable name as text does not create a reference.
3. Add `summary_result` to the End node and select the LLM text output.
4. After selecting **"Save draft,"** reopen and check the input reference and End output.

For creating, referencing, and outputting variables, see [Configure Workflow variables and branches](../build/configure-workflow-variables-and-branches.md). **Checkpoint:** `daily_report` is the LLM input reference, and `summary_result` references the LLM output.

## Step 3. Run the draft once

**Goal:** Compare input, each node, and the final result before publishing.

1. From the run controls on the editing screen, select the manual Start node.
2. Enter the sample daily report and select **"Start Execution"** only once.
3. Check `summary_result` in the result and confirm that summary, progress, issues, plans, and items requiring confirmation are distinguished.
4. In the step details, compare the Start input, LLM input and output, and End output in order.

For draft runs and viewing results, see [Test Workflow drafts](../build/test-workflow-drafts.md). **Checkpoint:** "Sales confirmation is required for pricing" remains, and no assignee or date that is absent from the input is added. Closing the result screen does not cancel a run, so check the history before running again.

## Step 4. Create the published version and check it from the user entry point

**Goal:** Publish after checking the draft, then check the user's run result.

1. If the result differs from expectations in Step 3, first correct only the first node that differed, then test again.
2. Once you have confirmed the content, select **"Publish,"** check the target and changes, and confirm publishing.
3. Confirm that the status is published.
4. Open the target from the App or Project user entry point and run it once with the same daily report.
5. Check the result and run history, and compare the content using the same criteria as for the draft.

For publishing, see [Publish a Workflow](../build/publish-workflow.md); for running as a user, see [Run a published Workflow](../use/run-published-workflow.md). **Checkpoint:** Being published and producing the expected result are separate. Check the result from the user entry point.

## Common misunderstandings

| Misunderstanding | What to check |
|---|---|
| Connecting nodes with lines also transfers values | Configure the input reference and End output separately. |
| A successful draft means the published version succeeds | Check again from the user entry point after publishing. |
| A natural response is correct | Compare it with the original input, items requiring confirmation, plans, and numbers. |
| Closing the run screen removes the run | Check status and results in run history. |

## If something does not work

| Situation | First thing to check |
|---|---|
| The daily report is not passed to the LLM | Check not only the connection but also whether the Start node input reference was inserted from the suggestions. See [Configure Workflow variables and branches](../build/configure-workflow-variables-and-branches.md). |
| The result is empty or differs from expectations | Check the End output, LLM input/output, and run details in order. See [Test Workflow drafts](../build/test-workflow-drafts.md). |
| You cannot publish or an item is missing | Check the checklist, target Project, permissions, and availability conditions. See [Publish a Workflow](../build/publish-workflow.md). |
| You cannot check the run result | Check start time, status, input, and each step in run history. See [Debug with Workflow run history](../build/debug-workflow-run-history.md). |

## Cleanup

1. If you retain the practice Workflow, keep wording in its name and description that identifies it as practice, and tell users not to use it for business operations.
2. If it is unnecessary, confirm that it is a published Workflow and that no App, Page, schedule, or trigger references it before cleaning it up. For deletion and handover decisions, see [Check deletion, handover, and audit logs](../operations/deletion-handover-and-audit-logs.md).
3. This exercise does not create triggers or schedules. If you added them during the exercise, confirm that they are stopped or disabled before cleaning up the Workflow.

## Read next

- [Publish a Workflow](../build/publish-workflow.md)
- [Debug with Workflow run history](../build/debug-workflow-run-history.md)
- [Run a published Workflow](../use/run-published-workflow.md)
- [Change, stop, or hand over scheduled runs](../operations/change-stop-handover-scheduled-runs.md)
