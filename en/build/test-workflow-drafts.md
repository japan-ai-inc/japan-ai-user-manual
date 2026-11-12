# Test a Workflow Draft

A Workflow is not complete merely because its nodes are connected and its settings can be saved. This article describes how to **run a draft from the editing screen, check the input, processing path, and output, then revise and retest it**.

For node configuration, see [Configure Workflow nodes](configure-workflow-nodes.md). For variables and branches, see [Configure Workflow variables and branches](configure-workflow-variables-and-branches.md). This article covers draft validation only; it does not publish the Workflow.

## Before you begin

- Check the target organization, Project, and Workflow, and the permissions required to edit and run it.
- Configure the output of nodes, models, instructions, variables, branches, and end nodes.
- If you use external services, check the destination and account to use.
- Prepare sample input without confidential information and the expected result.
- If you change a Workflow in use, check the test scope with relevant people.

This article primarily describes the Workflow editing screen on a computer and tests started manually. Screens and input fields vary by start-node type, permissions, and availability status.

> **Testing a draft is also execution.** Configurations that include email sending, data updates, file creation, external API calls, or similar operations may affect the actual destination. Do not treat not being published as a guarantee of simulated execution, no impact, or no cost. First check with a configuration that does not update external services or with an approved test destination.

## 1. Decide the test target and acceptance criteria

### Start from the editing screen

Open the Workflow editing screen in the target Project. Treat tests of the draft being edited separately from runs from a published Workflow list or usage screen.

Even when the Workflow name is the same, the published content and the content being edited may differ. Decide "which entry point and which change to check" before operating it. For steps to run published content, see [Run a published Workflow](../use/run-published-workflow.md).

### Do not use a success display alone as the acceptance criterion

For example, for a Workflow that "summarizes a request and sends urgent requests to separate processing," check the following.

| Perspective | What to check |
|---|---|
| Input | The entered request is in the intended variable |
| Reference | The correct preceding value is passed to the LLM or conditional branch |
| Path | The intended condition branch or "ELSE" branch is processed |
| Output | The expected result is returned in the fields specified by the end node |
| Content | Information needed for the summary remains, and facts not in the original text are not added |
| External impact | If sending or updates occur, the target and number of operations are as intended |

This table is an example of check perspectives. It is not a Studio feature that automatically judges operational correctness.

## 2. Save the draft and check for configuration issues

1. Check node settings and select "Save draft" in the editing screen.
2. Confirm that saving succeeded. If a save error occurs, do not proceed to compare test results until it is resolved.
3. Open "Checklist" and check findings about unconnected nodes, required fields, variable references, and similar issues.
4. Correct the indicated nodes and select "Save draft" again.

When starting a test from the editing screen, some processing saves the graph before running. However, we recommend that you **explicitly save a draft before running and decide what to validate**.

Even if Checklist does not find an issue, confirmation is not complete for external service authentication, actual input values, response content, or branch results. Also, a disabled Workflow cannot run, so check the displayed guidance and basic settings. Even if enabling is required, check the effect on configurations in use before changing it.

> "Save" in input field settings, "Save draft" for the Workflow, and "Publish" for the Workflow are separate operations. You do not need to select "Publish" first to test a draft.

## 3. Select a start node and prepare test input

1. Select "Run" in the editing screen.
2. In "Select start node to execute," select the start node to test.
3. Check the "Input" tab in the opened "Test Run."
4. Enter sample values in the displayed fields.

When there are multiple start nodes, check not only the name but also that the type, such as manual input, and the path you want to check match. Do not treat results run from different start nodes as comparisons under the same conditions.

### Check manual input

- Enter required fields.
- If default values are present, check whether they are the values you want to use for this test.
- If there is file input, specify the target file and check that uploading is complete and has no errors.
- For a configuration with no input fields, guidance that input is unnecessary is displayed. Do not decide that the absence of an input field alone is a problem.

Follow the input field settings and on-screen guidance for supported formats, number of items, and capacity. This article does not define limit values common to all Workflows.

What you can confirm with manual test input is not the same as behavior that starts from an actual event, such as receiving an email. Check event-based start conditions separately in [Configure Workflow triggers](configure-workflow-triggers.md).

## 4. Select "Start Execution"

After checking the input and execution target, select "Start Execution."

If input is insufficient or similar, correct what is displayed. If node settings are incomplete, guidance that includes the node name or reason may appear. When a run result is created, the screen moves to the "Result" tab.

**Moving to the result screen and succeeding in all processing are different.** Check the run status in "Steps." Even if output is not yet displayed, do not immediately run the same processing again.

Especially for Workflows involving external sending or updates, rerunning during a communication error or while waiting for the display may duplicate processing. Check the run history and destination state before deciding whether to rerun.

## 5. Check the final output in "Result"

In the "Result" tab, check the output fields and values returned by the Workflow. If there is file output, also check what is displayed as a file.

Checking in the following order makes it easier to separate configuration mistakes from content problems.

1. **Fields**: Whether the output fields specified in the end node exist.
2. **Values**: Whether they are blank, results from another node, or a string consisting only of a variable name.
3. **Format**: Whether they are the expected text, structured data, or file.
4. **Content**: Whether it is correct for the request. Whether there are omissions or unsupported additions.

If there is no output, distinguish among processing in progress, missing settings, incorrect references, and run failure. If the cause is unclear from "Result" alone, check "Steps" next. When a run fails, the results area may show a general error.

## 6. Check the flow of values and status in "Steps"

The "Steps" tab displays a run summary and processing content for each node.

### Run summary

Check displayed information such as status, elapsed time, credits, and errors. If credits display "Calculating...," do not interpret them as zero.

| Status display | What to check next |
|---|---|
| Running | Wait for completion status and observable processing results. Do not run duplicates. |
| Succeeded | Whether output content, format, and path meet the acceptance criteria |
| Failed | Check the error and node inputs and outputs, then isolate the problem location |
| Partially succeeded | Check what completed and what did not. Do not treat it as overall success. |
| Stopped | Check the executed scope and external impact. Do not treat it as unexecuted or rolled back. |

### Checks by node

Expand the target node and check the displayed fields.

- **Input**: Values passed from the preceding stage or specified this time.
- **Process data**: Information about processing. Expand it as needed.
- **Output**: Values returned by that node.

Not every node necessarily shows the same information. Based on the displayed run record, find the first point where the result differed from expectations.

For example, if a summary differs from the request, first check whether the input passed to the LLM is correct instead of immediately rewriting the instructions. If a conditional branch differs from the intended result, check the value being evaluated, comparison method, and destination connected to the condition branch.

> **"Close" is not a stop operation.** Even if you close the Test Run panel, do not treat the run as canceled or external processing as rolled back. This article does not provide a procedure that assumes this panel has a stop button. If you suspect unintended external processing, avoid additional runs, check the run history and destination, and contact an administrator.

## 7. Change conditions and test each path

Check each configured path, not only one successful result. The following is an example of a configuration that branches based on whether a request includes "urgent."

| Example test input | What to check |
|---|---|
| Urgent, please confirm the estimate | Whether it proceeds to the "Contains" condition branch |
| Please organize the content of next week's meeting | Whether it proceeds to the "ELSE" branch |
| It is not urgent | Whether the configuration fits the operational purpose, considering that even a negative sentence contains the string "urgent" |
| Leave a required field blank | Whether guidance appears before running because input is insufficient |
| Leave an optional field blank | Whether default values and later processing work as intended |

When there is numerical comparison, prepare values immediately before, equal to, and immediately after the boundary. When there are additional or multiple conditions, also check each path, AND/OR combinations, and inputs where conditions overlap.

In a configuration that merges branched paths, also check **whether later processing requires output from a branch that did not run**. Displaying a variable among the choices is different from obtaining its value in that run.

This table is a test design example. It is not an automatically run test set.

## 8. Revise and retest with the same input

1. Separate the gap from expectations into input, references, conditions, connections, instructions, authentication, and similar factors.
2. Correct settings related to the cause.
3. Confirm that "Save draft" succeeded.
4. From "Run" in the editing screen, retest with the same start node and input.
5. Check what improved and whether another path has broken.

To make causes easier to track, do not change many settings at once, and record changes. Because AI text may not exactly match even with the same input, evaluate against criteria such as required information, output format, and prohibited content.

Before retesting, check every time whether the input is retained. Do not treat retained input or displayed history on another screen as a guarantee of the test conditions this time.

## 9. Keep run history and validation notes

From "Execution history" in the editing screen, select the target run and review its results and steps. Compare the Workflow name, time of execution, Test Run number, and similar information so that you do not mistake an old run result for the result after revision.

If "Download Results" is displayed on screen, you can save run information as a JSON file. This downloads run information; it is not a backup of the Workflow definition or a publishing operation. Follow organizational rules when exporting or sharing information that includes inputs and outputs.

The following template is an example for recording in separate notes or a management table. It does not indicate that Studio has a dedicated recording form.

| Record item | Content to enter |
|---|---|
| Target | Project, Workflow, and start node |
| Changes | Settings changed this time |
| Test input | Input excluding confidential information and identification information for used files |
| Expected result | Path, required information, format, and external impact |
| Actual result | Status, output, and node with an issue |
| Run identification information | Date/time, Test Run number, and other information that can be checked |
| Judgment and remaining issues | Pass or needs revision, and the reason |

For detailed investigation using run history, see [Debug with Workflow run history](debug-workflow-run-history.md).

## When you need help

| Situation | What to check |
|---|---|
| Cannot run | Workflow enabled status, permissions, start node, insufficient input, and guidance about configuration issues |
| Input fields differ from expectations | The selected start node, saved input definition, and differences between manual and event-based starts |
| Changes do not appear to be reflected | Whether you ran it from the editing screen, whether Save draft succeeded, and whether the displayed run is new |
| Result is blank or differs from expectations | Run status, end-node output settings, preceding inputs and outputs, and variable references |
| Branch destination differs | Actual comparison value, string or number, comparison method, AND/OR, and connected destination |
| External integration fails | Connection account, permissions, target data, and destination error. Before rerunning, check whether duplicate processing occurred. |

## Completion checklist

- [ ] Made the draft in the editing screen the test target
- [ ] Checked Save draft and Checklist
- [ ] Aligned the start node, input, and expected result
- [ ] Checked the run status and final output
- [ ] Checked node inputs and outputs and the path for each branch
- [ ] When there was external sending or updating, also checked the impact on the destination
- [ ] After revisions, retested under the same conditions and on related alternative paths
- [ ] Recorded the result and remaining issues
- [ ] Distinguished test completion from publishing completion

## Next articles to read

- [Publish a Workflow](publish-workflow.md)
- [Debug with Workflow run history](debug-workflow-run-history.md)
- [Configure Workflow variables and branches](configure-workflow-variables-and-branches.md)
- [Run a published Workflow](../use/run-published-workflow.md)
