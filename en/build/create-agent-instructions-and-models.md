# Create an Agent and Set Its Instructions and Models

Configure the work the Agent will handle, how it should formulate responses, and the models it will use. This article explains **how to create an Agent in a Project, configure its name, description, instructions, and models, and confirm the saved settings and first response**.

For details about tools, Skills, and Datasets, see [Configure Tools, Skills, and Datasets for an Agent](configure-agent-tools-skills-datasets.md). For validation and improvement, see [Test and Improve an Agent](test-and-improve-agents.md).

## Before you begin

- Confirm the organization and Project in which you will create the Agent.
- Confirm that you are allowed to create and edit Agents. Being able to join a Project or use an existing Agent does not necessarily allow you to create or edit one.
- Confirm your organization's contract, feature settings, and limits on the number you can create. If you cannot use the Create button, check the guidance on the screen.
- Organize the work to be handled, expected output, and operations that are out of scope.
- Prepare test input that does not contain confidential or personal information.

> **Creating and editing an Agent uses automatic saving.** Do not operate on the assumption that nothing is created until you select "Save," or that closing the screen cancels changes. Give it a validation name, and also confirm its usage status and access permissions.

For Project setup, see [Create a Project and Configure Basic Settings](create-project-and-basic-settings.md). For Project-wide policies, see [Prepare the Project Description and Context](prepare-project-context.md).

## 1. Decide the work for the Agent to handle

Start by focusing on one purpose. Rather than "support all sales activities," a form in which the input and output are clear, such as "organize items to confirm for the next meeting from meeting notes," is easier to check.

| What to decide | Example |
|---|---|
| Usage scenario | After a sales meeting, the person in charge organizes items to confirm for the next meeting |
| Input | Sales meeting notes pasted by the user |
| Output | Key points, confirmed items, unconfirmed items, and suggested questions for the next meeting |
| How to handle unknowns | Mark as "unconfirmed" when there is no basis |
| What not to do this time | Send to the customer, update the CRM, or finalize contract terms |

This example is for practice. It does not mean that CRM integration or sending functionality is automatically configured.

### Separate the name, description, and instructions

| Item | Role | Example entry |
|---|---|---|
| Name | Distinguish its purpose in a list | Sales meeting note organization (for validation) |
| Description | Tell users its purpose and input content | Organize confirmed items and suggested questions for the next meeting based on sales meeting notes |
| Instructions (System Prompt) | Tell the Agent its role, procedures, output format, and notes | Organize based only on the provided notes, separating facts from suggestions |
| Available Models | Set the range of model choices for this Agent | Start by narrowing it to one model to validate |
| Default model | Set the model to use as the baseline | Make the model whose operation you confirmed the default |

Do not write only the procedure in the description; enter specific instructions for the Agent in the instructions field. Also confirm that they do not conflict with the Project-wide description and common instructions.

## 2. Create an Agent from the target Project

1. Open the target Project and confirm the organization name and Project name.
2. Open the Agent list in the Project.
3. Select **"Create Agent."**
4. On the creation screen, enter a name. While validating, add a label such as "for validation" so you can distinguish its purpose.
5. Set an icon as needed.
6. In the description field under **"Basic Settings,"** enter the purpose and input content for users.

The configuration screen structure may differ depending on the entry point. On a screen that displays "AI Creation" and "Configuration," open "Configuration" to check the input items. On a screen that displays the "Instructions," "Basic Settings," and "Advanced Settings" tabs, select the applicable tab. This article covers entering settings while checking each item yourself.

If you cannot use the Create button, tell the administrator the guidance shown for permissions, plan, creation limit, or similar matters. Creating it in another Project or organization instead changes the save location and scope of use, so do not switch without authorization as a workaround.

## 3. Enter instructions

In the **"Instructions (System Prompt)"** field, write how the Agent should perform its work. If an "Instructions" tab is available, check within that tab.

### Five elements to include in instructions

1. **Role**: What work it supports.
2. **Information in scope**: What it uses as a basis.
3. **Procedure**: How it organizes or judges information.
4. **Output format**: Headings, order, and level of detail.
5. **Constraints and handling of unknowns**: What it does not do and what to do when it does not know.

### Example instructions

The following text is an example entry for the "sales meeting note organization" decided in the preceding steps. Adjust and enter it for your organization's work.

> You are an Agent that supports organizing sales meeting notes.
>
> Based on the notes that the user has provided this time, organize the items to confirm for the next sales meeting.
>
> Procedure:
> 1. Separate facts written in the notes from the user's assumptions and requests.
> 2. Organize issues, requests, constraints, and items to confirm by the next meeting.
> 3. If the amount, implementation date, decision-maker, or contract terms are not written, mark them as "unconfirmed."
> 4. Create suggested questions for the next meeting. Distinguish suggested questions from confirmed facts.
>
> Use the following order for output: "Key points," "Confirmed items," "Unconfirmed items," and "Suggested questions for the next meeting."
>
> If you cannot identify the relevant sales meeting, ask the user first.
>
> Do not supplement information without a basis, and do not state that you referenced materials you have not read.
>
> In this validation, do not send externally, update business data such as a CRM, or finalize contract terms; return only the organized notes.

### What cannot be configured with instructions alone

- Writing "research internal materials" does not complete the configuration for referencing those materials.
- Writing "use an external service" does not configure a connection or authentication information.
- Writing "do not send" does not configure the removal of actual sending permissions.
- Saving instructions does not guarantee the same response or judgment every time.

Confirm reference sources, executable operations, and access permissions separately. Do not paste API keys, passwords, or Secret values into instructions. For procedures to add tools or material references, see [Configure Tools, Skills, and Datasets for an Agent](configure-agent-tools-skills-datasets.md).

## 4. Configure the models to use

Open **"Available Models"** and confirm the default and selection range. The displayed options depend on factors such as your organization's model settings. The same models do not necessarily appear as on another organization's screen or in older documentation.

### When checking with one model first

1. Confirm the status of **"Allow all models."**
2. To narrow the models to use, clear this checkbox.
3. Confirm the configured models and the **"Default"** indicator.
4. To use another model, select an option from **"Add a model..."**
5. Select **"Set as default"** for the model you added.
6. Remove unneeded models from the list and confirm that only the one being validated remains.

If you want to remove the current default, first change the default to another model. Removing a model from the list changes this Agent's settings; it does not remove the model from the entire organization.

### When retaining multiple models as options

Add the needed options from "Add a model..." and select a default from them. **Registering multiple models does not configure comparative runs or simultaneous runs with these screen settings alone.** Also confirm whether they can be selected in conversation and their actual usage status.

### When allowing all models

Select "Allow all models" and configure a default model. You still need to select a default in this case.

Here, "all" does not remove organizational limits to make even unavailable models usable. Disabled models or models whose runs are stopped may not appear in the options.

### Points for choosing a model

Rather than uniformly recommending a particular model, confirm using the options that are actually available.

- Whether it can respond in the required format.
- Whether it can handle Japanese and business terminology appropriately.
- Whether it works with the tools and input you plan to use.
- Whether response speed and usage cost suit your operation.

When comparing, use the same instructions and test input and change only the model, which makes differences easier to check. For detailed model conditions and limits, see [Agent Specifications and Limits](agent-specs-and-limits.md).

## 5. Confirm the saved settings and usage status

After configuring the name, description, instructions, and models, confirm the save status.

1. Confirm that the indication showing that saving is in progress disappears.
2. If **"Save"** is displayed, select it and confirm the save result.
3. Confirm the **"Saved"** indication or success notification. If there is an **"Error"** or input error, check and correct its content.
4. After saving completes, return to the list and reopen the target Agent.
5. Confirm that the name, description, instructions, default model, and model selection range remain.

**Automatic saving also applies to changes on the creation screen.** Using Back or closing the screen does not delete an Agent that has already been created or roll back saved settings. Even if you stop midway, confirm whether the Agent exists and its settings.

### Confirm "Enabled" separately from access permissions

The settings screen has an operation to switch the Agent's **"Enabled"** status. Do not assume that it is necessarily disabled or private immediately after creation; confirm its current status.

- **Save status**: Whether settings have been saved.
- **Enabled/disabled**: Whether the Agent is ready to be used.
- **Access permissions**: Who can use or edit it.

These are separate checks. Do not treat enabling as "making it available to everyone," or saving as "saving a draft visible only to yourself." Before use, confirm that the scope is intended through the displayed access permissions or guidance from the administrator.

If a save error occurs, confirm whether the target Agent already exists before repeatedly creating another Agent. Preserve the required input content, and confirm the error display and save location.

## 6. Check the first response with a small input

After saving the settings and confirming that the target Agent is enabled and that you have permission to use it, try it through an available conversation entry point such as preview.

Example test input:

> Organize the following sales meeting notes.
>
> At Sample Trading Company, staff manually route inquiries to the responsible person.
>
> They asked for help organizing inquiry categories.
>
> The budget, implementation date, and decision-maker have not been confirmed.
>
> Do not send externally or update data; return only the notes.

Check the following.

- Whether output uses the four specified headings.
- Whether facts in the notes are distinguished from suggested questions.
- Whether it has added a budget, implementation date, or decision-maker on its own.
- Whether it claims without basis that it read external materials or made updates.
- Whether you have confused the target Agent or model in use.

If preview is unavailable, confirm the save status, enabled/disabled status, permission to use it, and screen errors. Do not broaden the sharing scope just to make it display.

A successful first attempt is not approval for production use or a quality guarantee for all inputs. Here, confirm the basic settings and initial operation; proceed with validation and improvement using multiple inputs in [Test and Improve an Agent](test-and-improve-agents.md).

## Troubleshooting

| Situation | What to check or do |
|---|---|
| Cannot use the Create button | Confirm the target Project, creation permission, contract and feature settings, creation limit, and screen guidance |
| The desired model is not in the options | Confirm availability, enablement, and whether it can run in the organization. Do not substitute by writing the model name in the instructions. |
| Cannot remove the default model | Add another model and make it the default, then remove the original model |
| Changes are not saved | Confirm input errors, save status, permissions, and network status. Preserve the draft and avoid duplicate creation. |
| Preview is unavailable | Confirm the Agent's saved and enabled status, and your permission to use it |
| The response does not follow the instructions | Confirm conflicting instructions, saved settings, the selected Agent and model, and conversation assumptions |
| Does not use materials or external services | In addition to instructions, confirm reference settings, tools, connections, and permissions |

For an inquiry, include the organization name, Project name, Agent name, date and time of the operation, displayed error, and expected result. Do not include authentication information or confidential data.

## Completion checklist

- [ ] Created the Agent in the correct Project
- [ ] The name and description make the purpose and input content clear
- [ ] Included the role, information in scope, procedure, output format, and constraints in the instructions
- [ ] Confirmed the default model and range of Available Models
- [ ] Confirmed the save result and that content remains after reopening
- [ ] Confirmed enabled/disabled status and access permissions separately
- [ ] Confirmed the first response with a small input that does not contain confidential information

## Related articles

- [Create a Project and Configure Basic Settings](create-project-and-basic-settings.md)
- [Prepare the Project Description and Context](prepare-project-context.md)
- [Chat with an Agent and Check the Results](../use/chat-with-agents.md)
- [Configure Tools, Skills, and Datasets for an Agent](configure-agent-tools-skills-datasets.md)
- [Test and Improve an Agent](test-and-improve-agents.md)
- [Agent Specifications and Limits](agent-specs-and-limits.md)
