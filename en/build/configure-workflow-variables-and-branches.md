# Configure Workflow Variables and Branches

Simply connecting Workflow nodes does not necessarily pass the values you want to each process. This article explains **how to define inputs at the start, reference values from earlier stages in later stages, and set destinations based on conditions**.

For adding and connecting nodes, see [Configure Workflow Nodes](configure-workflow-nodes.md). This article covers configuration through saving a draft. Check run results in [Test a Workflow Draft](test-workflow-drafts.md).

## Before you begin

- Confirm the target organization, Project, and Workflow, as well as your permission to edit them.
- When changing a Workflow in use, confirm the users and the scope of impact.
- Organize the values to enter, the values to use for processing, and the values to return at the end.
- Prepare a practice configuration that does not send externally or make updates, and inputs that do not contain confidential information.
- Name nodes so that you can distinguish their roles and reference sources by looking at their names.

This article focuses on the desktop editing screen and manually started Workflows. Inputs from triggers and the available options vary depending on the configuration and availability status.

> **"Save" for an input field and "Save draft" for the entire Workflow are different.** After editing input items or node settings, confirm the result of "Save draft" for the Workflow. A changed value on the screen alone does not mean the draft has been saved.

## 1. Distinguish variable names, display names, and values

Variables are items for referencing Workflow inputs and node processing results.

| Item | Role | Example |
|---|---|---|
| Variable name | Name that identifies a value in the configuration | `request_text` |
| Label name | Name shown to the person entering input | Request details |
| Description | Content to enter or notes | Enter the request text you want to summarize |
| Actual value | Content entered or generated at run time | I want to organize the items to confirm for next week's briefing session |
| Reference source | Which node's value to use | `request_text` from the Start node |

If the same variable name appears on multiple nodes, do not decide by the name alone. Confirm the **combination of the reference-source node and item**.

### Configure connection lines and variable references separately

- **Connection lines**: Indicate which node processing proceeds to.
- **Variable references**: Specify which value to use in that node.

For example, even if you connect "Start → LLM → End," you must separately confirm that the Start input is specified in the LLM prompt and that the LLM output is specified in the End node.

## 2. Add an input field to the Start node

1. Select the manually started **"Start"** node.
2. Under **"Input Fields,"** select **"Add."**
3. Enter the field type, variable name, label name, and description.
4. As needed, confirm the maximum length, initial value, and required settings.
5. On the input-field screen, select **"Save."**
6. Confirm that the added item appears in the Start node list.

### Practice input example

| Item | Example setting |
|---|---|
| Field type | Paragraph |
| Variable name | `request_text` |
| Label name | Request details |
| Description | Enter the request text you want to summarize. Do not enter confidential information. |
| Maximum length | Set a value on the screen that accommodates the expected request text |
| Initial value | Leave blank for practice |
| Required | Enable when input must always be required |

This is a configuration example. It does not specify initial values or limits as specifications common to all Workflows.

### Notes on names and types

- A variable name, label name, and description are required.
- Do not include spaces in a variable name or begin it with an underscore.
- Within the same Start node, give variables names that are easy to distinguish.
- On this input screen, select "Paragraph" or "File list." Do not assume that dedicated inputs for numbers or Boolean values are always available.
- Do not assume that writing a number in a "Paragraph" lets a later stage handle it as a number. Check the type displayed in the reference options.
- Distinguish a file list from the file itself and its extracted body text. If you want to use the body text, confirm the extraction process and support on the receiving side.

When setting an initial value, confirm that it matches the intended behavior when input is omitted. Verify the behavior resulting from combinations of required, initial value, and blank fields in later tests.

## 3. Pass the Start input to an LLM

1. Confirm that the Start node is connected to the LLM node.
2. Select the LLM node and confirm the model to use.
3. Enter processing instructions in **"Prompt input."**
4. At the position where you want to insert a value, enter a half-width **`/`**.
5. From the variable options, select `request_text` from the Start node.
6. Confirm the reference source and item in the inserted variable display.

For example, build the instruction as follows.

> Organize the following request details into three sections: key points, items to confirm, and next actions. Do not add dates or people not included in the input; mark them as unconfirmed.
>
> Request details: Insert "Start / request_text" here from the options.

The last line explains the insertion location. **Do not paste the text "insert here" or the variable-name string as is; select the variable from the on-screen options.**

Distinguish a state where the variable name is written as ordinary text from a state where the variable has been inserted. If you change an earlier connection or item name, confirm whether you need to select it again.

### When a variable does not appear in the options

| What to check | How to review it |
|---|---|
| Whether it is connected from an earlier stage | Confirm the connection line, not just that it is placed to the left on the screen |
| Whether input or output is defined | Open the reference-source node settings |
| Whether the receiving side can handle the type | Distinguish strings, numbers, files, arrays, and so on |
| Whether it is inside or outside a loop | Confirm the values available at that location and their reference scope |
| Whether an old reference remains | After renaming a variable or replacing a node, select it again from the current options |

Reference options are generated according to connection relationships and receiving-side settings. Outputs from every node cannot necessarily be selected from everywhere. Rather than guessing and manually entering a reference name in old documentation, use the current options.

## 4. Specify values to return in the End node

1. Connect the processing node to **"End."**
2. Select the End node and open **"Output Variables."**
3. Create an output item with **"Add."**
4. For the variable name, enter `result`, for example.
5. From **"Select a variable,"** select `text` from the target LLM node.
6. Confirm not only the output name but also that the reference destination is correct.

In this example, configure the LLM's text output to be returned under the name `result`. Do not assume that all information generated by the LLM is automatically included in the final output.

When removing unneeded output, confirm that no Page or other process uses that value. Changing the output name of a published Workflow may affect callers.

## 5. Configure conditional branches with If / Else

To split routes based on fixed values or conditions, use **"If / Else."** Here, you will configure an example that splits processing destinations based on whether request details contain a specific word.

### Example: Split based on whether request details contain "urgent"

1. Add and connect "If / Else" after the Start node.
2. Open the If / Else settings and select **"Add condition."**
3. Select `request_text` from the Start node.
4. Set the comparison operator to **"Contains."**
5. Enter `urgent` as the comparison value.
6. From the **"IF"** connection point on the node, connect to processing for priority review.
7. From the **"ELSE"** connection point, connect to processing for standard review.
8. Confirm the input variables used by each process and the outputs that follow.

Connecting only the "IF" line does not mean the "ELSE" connection is complete. Confirm both routes.

This example is practice using a string as a condition. "It is not urgent" also contains the word "urgent." **A text match is not the same as the meaning of a sentence or its business urgency.** Do not determine an actual priority using this condition alone.

### Available conditions differ for strings and numbers

| Target type | Main operators | Notes |
|---|---|---|
| String | Contains, does not contain, starts with, ends with, is, is not, empty, not empty | Distinguish partial matches from equality checks |
| Number | =, ≠, >, <, ≥, ≤, empty, not empty | Confirm that the option is a numeric type, not text that contains digits |

Current variable selection for If / Else primarily handles strings and numbers. You cannot necessarily select files or Objects directly and use the same operators.

Configure operators that check whether a value exists, such as "empty," separately from ordinary value comparisons. Do not treat an empty string, an input containing only spaces, and no value itself as the same; test the cases you need. After changing an operator, also confirm that no comparison value remains and that no required value is missing.

## 6. Use condition combinations and additional branches appropriately

### Add conditions that proceed to the same branch

Within the same case, select "Add condition," then confirm the **AND/OR (and/or)** switch that appears when there are multiple conditions.

- **AND**: A setting that proceeds to that case when all conditions are met.
- **OR**: A setting that proceeds to that case when any condition is met.

For example, when combining "contains urgent" and "contains estimate," decide first whether both are required or either one is sufficient. If you do not confirm AND/OR after adding conditions, you may include targets other than those intended.

### Add different processing destinations

To add another route, use **"Add ELIF."** For each added case, configure the target variable, operator, comparison value, and destination.

| Action | Purpose |
|---|---|
| "Add condition" within the same case | Add conditions for determining one branch |
| "Add ELIF" | Add a branch destination with a different condition |
| "ELSE" connection | Prepare a processing destination for input that does not meet the conditions |

Also prepare inputs that meet multiple cases and confirm the expected route. Starting with non-overlapping conditions makes checking easier. Do not decide from the screen order alone that business priority has been correctly implemented.

## 7. Use Question Classifier appropriately

When you want a model to classify the content of text, **"Question Classifier"** is also an option.

| Node | Configuration approach | Suitable example |
|---|---|---|
| If / Else | Specify a variable, operator, and comparison value | Route based on fixed string or numeric conditions |
| Question Classifier | Configure a model, input variable, classification items, and instructions as needed | Classify request text into categories such as "product question" and "usage question" |

In Question Classifier, select a model, configure the input variable and classification items, and connect each classification connection point to its processing destination. As needed, refine the instructions in Advanced Settings.

Do not assume it has the same "ELSE" endpoint as If / Else. Design which classification handles out-of-scope or difficult-to-judge input, and confirm it with tests. Model-based classification does not guarantee accuracy or the same result every time.

## 8. Confirm available values after branching

In a branched configuration, a value visible in the options may not necessarily be generated on that run route.

- Confirm which Start inputs and earlier outputs nodes in each branch use.
- Do not reference a value generated only in another branch on the assumption that it naturally exists.
- Even if you connect multiple branches to the same node, do not assume that their results have been automatically merged.
- Confirm that the values you want to return in the final output are prepared on each route.
- If you delete or replace a case or node, review both connection lines and variable references.

Rather than creating a complex merge all at once, first organize the input, processing, and output of each branch, and check run results one route at a time.

## 9. Review the checklist and save a draft

1. Review reference sources, reference destinations, types, conditions, and connection destinations.
2. Open **"Checklist"** and review notices such as variables not found, conditions not configured, and nodes not connected.
3. Correct the noted items. Record items to configure later as incomplete.
4. Select **"Save draft"** and confirm a success message or error.
5. After saving succeeds, reopen it and confirm that input items, variable references, conditions, and connection destinations remain.

Even if the checklist has no notices, the result is not necessarily business-correct. Also, saving a draft is not publishing. Do not publish in this article; proceed to the next draft test.

### Input examples to check in the next article

| Case | What to check |
|---|---|
| Normal input | Whether the specified variable is used for processing and the expected output is returned |
| Input that matches a condition | Whether it proceeds to the intended branch |
| Input that does not match | Whether it proceeds to the intended processing, such as "ELSE" |
| Text such as "It is not urgent" | Whether a text-match condition suits the business purpose |
| Input that uses a blank or initial value | Whether required, initial value, and empty checks behave as expected |
| Input that matches multiple cases | Whether the expected route is selected |
| Numeric boundary values | Whether differences such as `>` and `≥` are configured correctly |

This table suggests testing perspectives. It does not mean that an automated test has been registered or that run verification has been completed.

## Troubleshooting

| Symptom | First thing to check |
|---|---|
| Cannot save an input field | Variable name, label name, description, and input errors on the screen |
| A variable is not in the options | Connection to the earlier stage, output definition, type, and scope inside or outside loops |
| The variable name appears as text | Whether you inserted the variable from the options rather than as an ordinary string |
| Does not proceed to the intended branch | Target variable, comparison value, type, AND/OR, and branch-endpoint connections |
| Final output is insufficient | Output name and reference destination in the End node, and the route through which the reference source passes |
| Changes are not saved | Whether not only the input-screen save but also Save draft succeeded |

If the issue is not resolved, tell the person in charge the Workflow name, node name, input example, expected route, and displayed error. Do not include API keys or unnecessary personal information.

## Completion checklist

- [ ] Configured the input variable name, label name, description, and required condition
- [ ] Selected a value from an earlier stage from the options and passed it to a later stage
- [ ] Configured the End node output name and reference destination
- [ ] Distinguished adding a condition from adding a branch
- [ ] Confirmed the operator, comparison value, AND/OR, and ELSE connection
- [ ] Reviewed whether values referenced on each route are prepared
- [ ] Confirmed successful draft saving and the saved content
- [ ] Organized incomplete items and the next test inputs

## Read next

- [Configure Workflow Nodes](configure-workflow-nodes.md)
- [Test a Workflow Draft](test-workflow-drafts.md)
- [Publish a Workflow](publish-workflow.md)
- [Debug with Workflow Run History](debug-workflow-run-history.md)
- [Workflow and Automation Specifications and Limits](workflow-and-automation-specs.md)
