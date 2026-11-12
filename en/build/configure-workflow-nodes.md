# Configure Workflow Nodes

Break work performed by a Workflow into small processes and connect them. This article explains **how to create a Workflow in a Project, add, connect, and configure nodes, and save the processing framework as a draft**.

For specific operations for passing values between nodes, see [Configure Workflow Variables and Branches](configure-workflow-variables-and-branches.md). For verification by running a Workflow, see [Test a Workflow Draft](test-workflow-drafts.md). Completing this article does not mean the Workflow is ready for business use or published.

## Before you begin

- Check the target organization and Project and confirm permission to create and edit Workflows.
- When changing an existing Workflow, check its users and the impact of changes. For your first practice, use a test Workflow.
- Decide the information to input, the processing order, and the final result to return.
- Check that the models, Agents, Datasets, tools, and other resources to use are available.
- When operating external services, check the account used for connection and the scope of reading and updates.

> **When editing Workflow nodes, distinguish seeing changes on the screen from saving the draft.** Do not assume the behavior is the same as automatic saving in Agent settings. At each stopping point, select "Save draft" and confirm success.

Screen layouts, nodes you can add, and available operations vary with organization settings and permissions. The following focuses on the PC editing screen.

## 1. Separate inputs, processing, and outputs

A node is a unit of processing that makes up a Workflow. It may be displayed as a "Block" on the screen. Lines connecting nodes represent processing connections.

First, design a small flow without branches or iterations.

### Configuration example: organize meeting notes

> Start → LLM → End

| Role | What it does in this example | What to decide when configuring |
|---|---|---|
| Start | Receives meeting notes | Information users enter and how it will be used |
| LLM | Organizes notes into key points, unconfirmed items, and next questions | Model, instructions, and referenced input |
| End | Returns the organized result | Values to return and their names |

This is a design example, not a template completed merely by placing nodes. You need to pass notes received at Start to the LLM and set the LLM result as output at End.

**Connection lines and passing values are separate settings.** Even when a line is connected, the result of the preceding node is not necessarily inserted automatically into the next prompt or input field.

### A guide to choosing nodes

| What you want to do | Candidate node | Notes |
|---|---|---|
| Receive input | Start | At first, organize inputs assuming manual execution |
| Summarize, classify, or generate text | LLM | Configure the model, prompt, and input reference |
| Ask an existing Agent to perform work | Agent | Check the target Agent, inputs, and use permissions. An Agent with a fixed model runs with its default model and cannot be changed. |
| Reference information from a Dataset | Data Integration | Configure the Dataset and information to use for searching. Do not treat it as processing that necessarily covers all materials. |
| Use capabilities such as external services | Tool | Check target operations, inputs, authentication, and external impact. |
| Split routes by conditions | If / Else, Question Classifier | Configure not only branch conditions but also connections and outputs for each route. See [Configure Workflow Variables and Branches](configure-workflow-variables-and-branches.md) for details. |
| Return the final result | End | Configure the output name and reference for the value to return. |

Choices also include code and template processing. You do not need to use everything from the beginning. Check candidates displayed in the selection screen and their required input fields before choosing.

## 2. Create a Workflow

1. Open the target organization and Project, then go to the Workflow list.
2. Select **"Create New."**
3. In **"Create workflow,"** enter a **"Name."**
4. In **"Description,"** state the purpose, inputs, outputs, and that it is for verification.
5. Check displayed settings such as enabling, then confirm creation.
6. After the editing screen opens, check the Workflow name and work target.

Example:

- Name: Organize Meeting Notes (for verification)
- Description: A verification Workflow that organizes entered meeting notes into key points, unconfirmed items, and next questions. It does not send externally or update CRM.

You cannot create a Workflow with an empty name. If you cannot use the creation operation, check the target Project, permissions, and screen guidance.

The "Visibility" choice may not appear depending on where you opened it. **Visibility, enabling, and publishing processing content are separate items to check.** Creating a Workflow alone does not mean publication or external-service authentication is complete.

Do not paste API keys, passwords, or tokens into input fields. Check who can view Workflow content, including prompts and code in nodes.

## 3. Check the initial configuration and editing screen

The initial configuration contains **"Start" → "LLM" → "End"** nodes and connection lines. When you open a saved Workflow, its draft configuration is displayed. You do not need to restore the same configuration as when it was newly created.

When you select each node, that node's settings area opens. Input fields differ by node.

Check the following three places.

| Location | What to check |
|---|---|
| Editing area with nodes | Whether needed processing is present and the order in which nodes are connected |
| Settings area for the selected node | Whether inputs, models, instructions, references, outputs, and so on are intended |
| Workflow controls area | Draft saving, Checklist, publication status, and so on |

You can move nodes to arrange them for readability, but **changing their positions alone does not change processing order.** To change the order, check connection lines and input references. If "Auto-Layout" appears, use it to arrange the layout and distinguish it from changing business logic.

## 4. Add needed nodes

If the initial configuration meets the purpose, you may begin configuration without adding nodes.

### Add between two connected nodes

1. Check the connection line at the position where you want to add a node.
2. Select **"+"** on the connection line. If it is hard to find, point at the line.
3. From the displayed candidates, select the node type to add. If a search field appears, search by name.
4. After adding it, check that it is connected as preceding node → new node → following node.
5. Select the new node and enter required settings.

When you insert a node in the middle, even if connections are reconfigured, input references in following nodes are not necessarily replaced with the new processing result. To actually use inserted processing later, review the references too.

### Add the next process

If settings for the selected node display **"Add next block" → "Add block,"** you can select the next node there. The same add button may not appear, such as when the next node is already connected.

Some environments display "Add node" elsewhere in the editing area. Not every screen necessarily has the same add button, so use the entry point that is displayed.

### Check external impact before adding

When incorporating tools or Agents, check whether they only read or may send, create, or update. Do not select "Run" or "Publish" merely to check the configuration. Processing may occur at the connection destination when you run it.

## 5. Connect nodes

If there is no connection line, drag from the connection point on the output side of the preceding node to the connection point on the input side of the next node.

1. Decide which process should proceed to which process.
2. Check and connect the output-side and input-side connection points.
3. Confirm that the connection line remains.
4. Confirm that you can trace the intended route from Start through returning a result.

Connection point positions and counts vary by node type. For a branch node, use the connection point corresponding to each branch. [Configure Workflow Variables and Branches](configure-workflow-variables-and-branches.md) covers detailed branch configuration.

### If you cannot connect them

Check the following.

- Whether you are trying to connect output to output or input to input.
- Whether you are trying to draw a line back to the same node itself.
- Whether you are trying to connect after "End" or before "Start."
- Whether a line returning to preceding processing would create a cycle.
- Whether the connection crosses inside and outside an iteration in a way that is not allowed.
- Whether editing is unavailable because execution or another operation is in progress.

Even when processing needs repetition, do not assume drawing a line from later to earlier can accomplish it. Consider an iteration configuration.

**A successful connection does not guarantee that data types or settings are correct.** Separately verify through configuration and testing whether following nodes can receive required information.

## 6. Configure the contents of each node

Select nodes in order and check what each receives, does, and returns. If you can edit names and descriptions, use wording that makes the role clear, such as "Organize key points" or "Create response draft."

### Start: organize received information

For the meeting-note organization example, define notes that users provide as input. Configure required information in "Input Fields." [Configure Workflow Variables and Branches](configure-workflow-variables-and-branches.md) explains input types and variable specification in detail.

At this stage, you do not need to add a trigger for automatic execution. [Configure Workflow Triggers](configure-workflow-triggers.md) covers event-based and scheduled settings.

### LLM: configure the model and instructions

Select an available model, and provide the processing purpose and output format in instructions. Do not leave the initial display unchanged; check both the model and prompt.

Example instructions:

> Based only on the provided meeting notes, organize the content into "Key points," "Unconfirmed items," and "Next question ideas." Do not infer amounts, deadlines, or representatives that are not stated. Do not send externally or update data; return text.

This is an example instruction. You also need to configure the prompt to reference input from the Start node. Writing "provided meeting notes" alone does not connect an input variable.

An LLM node and an Agent node that calls an existing Agent are different types. Do not assume that an Agent's name, instructions, or tools are automatically carried over to an LLM node.

### Agent: check default model and execution conditions

In an Agent node, in addition to the target Agent and request text, you can select files passed from earlier stages. An Agent with a fixed model runs with that Agent's default model. For an Agent whose model can be changed, if you leave the model unselected, it uses the Agent's default model; specify a model in the node only when necessary.

Models you can select in the node vary depending on the selected Agent's model settings and available models. After switching Agents, do not carry over a model selected for the previous Agent; check the default model and selectable choices. You can set temperature from 0.0 to 2.0 and timeout from 60 to 1,200 seconds.

Agent output may include responses, files, and tool-call information. Check variable choices and configure what to use in following nodes or the End node.

### End: configure the result to return

Specify the final-result name and value to return. For the meeting-note organization example, specify the text created by the LLM as output.

Simply being connected to the End node does not necessarily output all required results. Check the output reference, and after renaming or replacing nodes, check whether you need to select it again.

### Distinguish closing settings from saving

Content entered in node settings is reflected in the configuration being edited. Do not treat closing the settings area as "canceling input." However, reflecting it in the configuration being edited alone does not confirm that the draft was saved to the server.

## 7. Organize unnecessary nodes and connections

You can remove an unnecessary node after checking it by using the node operations menu. Some nodes cannot be deleted depending on their type or status. In particular, do not try to forcibly remove a manual Start node merely because you decide it is unnecessary.

To remove only a connection line, use that line's remove operation. Treat this separately from deleting the entire node.

After deletion, replacement, or duplication, check the following.

- Whether needed nodes or lines were also removed.
- Whether the route from Start to result is broken.
- Whether remaining nodes reference a deleted node or output.
- Whether duplicated nodes retain previous references or connection destinations.

If you notice an incorrect operation, check for "Undo" or similar controls displayed in the editing screen. However, undoing edits and undoing external operations already executed are separate. Do not assume a deleted configuration is always automatically restored.

## 8. Check the Checklist and save a draft

1. Open the Workflow **"Checklist."**
2. Read displayed items such as unconnected nodes, unset models or prompts, and incomplete references.
3. Select a target item and check settings for the relevant node.
4. Correct issues you can resolve at this stage. Record parts you will configure later, such as variables and branches, as incomplete.
5. Select **"Save draft."**
6. Check the save-success notification. If saving fails or a permission error appears, do not treat it as complete.
7. After confirming success, reopen the editing screen as needed and confirm that nodes, lines, and settings remain.

The Checklist helps review settings; it does not guarantee that business results are correct or external-service operations succeed. Even without findings, verification by running is necessary.

### Do not confuse "Save draft" and "Publish"

| Operation or status | Treatment at this stage |
|---|---|
| Creating or changing basic settings | Registers items such as name and description. Check this separately from saving the node configuration. |
| Editing node settings | Changes the configuration being edited. Confirmation that saving succeeds is required. |
| Save draft | Saves nodes and connections as a draft. |
| Publish | A separate operation that publishes processing content as a usable version. Perform it only after verification and impact checks. |
| Visibility and use permissions | Settings for who can view or operate it. Separate from draft and published status. |

**This article stops after saving a draft without selecting "Publish."** Even when editing an existing published Workflow, do not confuse saving a draft with updating the published version. [Publish a Workflow](publish-workflow.md) covers publication details.

## When things do not go well

| Situation | What to check and do |
|---|---|
| You cannot create or edit | Check the organization, Project, permissions, and screen guidance |
| You cannot find a node to add | Check search terms, candidate categories, and organization availability settings |
| You rearranged nodes but the order did not change | Check connection lines and references rather than positions |
| You cannot connect lines | Check input and output direction, cycles, Start and End, iteration scope, and execution state |
| Results are not passed to a following node | Check input variables, prompts, and output references in addition to connections |
| A warning remains after deletion | Check whether a remaining node references a deleted output |
| Saving a draft fails | Check the displayed error and permissions, and preserve changes. Do not reload before saving. |
| You saved but cannot use it for business | Separately check draft saving, publication, use permissions, and authentication. Do not republish without checking. |

For an inquiry, include the Workflow name, Project name, relevant node, operation date and time, connection and configuration status, and error. Do not include authentication information or confidential data.

## Completion checklist

- [ ] Set a purpose-revealing name and description in the correct Project
- [ ] Separated input, processing, and output into nodes
- [ ] Checked routes with connection lines rather than node positions
- [ ] Checked required models, instructions, references, and outputs
- [ ] Recorded unset variables, branches, authentication, and other items as incomplete
- [ ] Checked Checklist findings
- [ ] Confirmed draft-save success and the saved configuration
- [ ] Distinguished configuration saving from completed testing and publication

## Next articles

- [Configure Workflow Variables and Branches](configure-workflow-variables-and-branches.md)
- [Test a Workflow Draft](test-workflow-drafts.md)
- [Publish a Workflow](publish-workflow.md)
- [Configure Workflow Triggers](configure-workflow-triggers.md)
- [Workflow and Automation Specifications and Limits](workflow-and-automation-specs.md)
- [Run a Published Workflow](../use/run-published-workflow.md)
