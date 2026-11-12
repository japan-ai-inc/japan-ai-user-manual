# Ask Studio Assistant to Build for You

Tell Studio Assistant your business purpose and conditions, and discuss building within a Project. This article explains the process: **state requirements → check the plan → execute → verify the changes**.

Even if AI responds "Done," separately confirm that it can be used for business. Check the created content, save and publication status, and test results before beginning use.

## Before you begin

- Check the organization and Project where you will work. If creating a new one, first follow [Create a Project and Configure Basic Settings](create-project-and-basic-settings.md).
- Check the permissions required to use Studio Assistant and create or change target resources. Being able to open a Project does not necessarily mean you can build in it.
- Narrow the business work you first want to accomplish to one and prepare test data that you are allowed to use.
- When changing an existing Project, confirm with the responsible person which targets may be changed and which must not be touched.
- If an external connection or Secret is required, decide who will configure it. Do not paste API keys or passwords into the chat body.

> The displayed Agents, menus, and capabilities that can be built vary by organization and Project settings and permissions. The following distinguishes planning immediately after Project creation from normal conversations in an existing Project.

## 1. Open a conversation to request building

### Immediately after creating a Project

When you choose **"Create with a plan"** in [Create a Project and Configure Basic Settings](create-project-and-basic-settings.md), you proceed to the **"Project Plan: <Project name>"** screen. An Agent for building support is selected here, and the description entered during creation is included as a draft in the input field.

The description has not yet been sent at this point. Add the target work, input information, desired completion state, and work scope. If necessary, attach materials such as design documents or mockups, then send it. Unlike normal chat, this screen does not display controls for switching Agents, models, or tools.

### Starting in an existing Project

1. From Studio Workspace, open the Project where you will work.
2. Start a conversation from the Project chat input field or displayed **"New Task."**
3. **Check the selected Agent.** If an Agent selection menu appears and Studio Assistant is among the available choices, select it. If there are many choices and a search field appears, search by name.
4. If you cannot select an Agent for building support, check with the responsible administrator whether it is available in that Project.

The initially selected Agent may be a different Agent depending on settings and usage permissions. **Not every conversation in a Project is a conversation with Studio Assistant.** Do not make requests to a normal business Agent on the assumption that it has building permissions.

If a business Page is displayed when you open a Project, proceed to the building screen through the available return path. If you cannot find the entry point, check with the responsible administrator.

### Continuing work in progress

From recent conversations or the conversation list in the Project, open the work from last time. Check the target Project, Agent, and last executed content, and **specify the work that remains instead of repeating completed work from the beginning.**

When you approve and proceed to execution in planning immediately after creation, you move to a conversation for execution. When resuming, do not confuse a conversation where you only discussed work with one that actually executed building. Permission determines the scope in which you can view or add to conversations.

## 2. State the purpose, inputs, and completion criteria

Rather than only saying "Create an app for sales," explain who will enter what and what result you want to obtain.

| Item to provide | What to write |
|---|---|
| Users and purpose | Whose work and which work you want to support |
| Input and reference information | Items to enter, materials that may be used, and location of test data |
| Desired results | What users receive, such as answers, saved information, or screens |
| Initial scope | The minimum capability to complete this time |
| Change scope | Whether this is new creation or an existing-resource edit, the target name, and items not to change |
| Connection and execution conditions | Required external services, person responsible for configuration, and whether sending and updates are allowed |
| Completion criteria | Which inputs to use and what must be confirmed |

### Example first request

> In this Project, consider a mechanism that helps sales representatives create question ideas before a meeting.
>
> Inputs are company name, meeting purpose, and meeting notes entered by the representative. Outputs are confirmation items and question ideas. Do not infer missing information; indicate it as "Review required."
>
> At first, use a minimum configuration that accepts input in a conversation and returns results. Do not create a dedicated screen or CRM integration this time. Use only fictitious test data.
>
> First, ask about missing information and propose a plan that includes the necessary configuration, targets to create or change, and test method. Do not execute building in this request, and do not send email, update CRM, or delete existing resources.

This is an example of how to write a request. It does not mean that a standard App or the same configuration is available in every environment.

**A request that says "do not execute yet" is not a substitute for controls provided by permissions or connection settings.** Especially in normal conversations, do not assume a dedicated approval screen appears for every change. Use a test Project and data and the minimum necessary permissions.

## 3. Read the plan and decide the execution scope

Check the following points in the proposed plan.

- Whether the purpose and users match the request.
- Whether it is clear what Agents, Workflows, Objects, Pages, and other resources will be created or changed.
- Whether it is clear whether existing resources will be reused or new ones created.
- Whether required information, connections, and permissions are available, and whether items to be configured by people are separated.
- Whether email sending, data updates, deletion, publication, automatic execution, or similar operations are included unintentionally.
- Whether the post-completion confirmation method and the scope not performed this time are clear.

If anything is unclear, request revisions before execution.

> Limit this to creating an Agent and testing it in a conversation. Separate Pages, scheduled runs, and external-service integrations into a later phase. Rewrite the change targets and test items within this scope.

If you change the plan, check the **last agreed content**. Being able to press an approval button does not by itself mean that the plan's content and safety have been checked.

## 4. Proceed to execution

### Execute from the planning screen immediately after creation

1. Read the latest plan and confirm it is within the scope that may be executed.
2. Select **"Approve."**
3. Read the confirmation in **"Approve and execute?"** and confirm only if you will execute.
4. After moving to the conversation for execution, check progress, questions, and errors.

When you confirm, execution of the plan by the Agent begins. **Approval does not only save the plan.**

Materials attached during planning are carried over so that they can also be referenced on the execution side when you approve and proceed to execution. However, do not assume attachment or carryover alone means that content has been read or reflected in settings; check usage status after execution.

You may not be able to select approval while a response is in progress or while a plan is being saved or synchronized. Even if a response contains a plan, the save necessary for execution may not be complete. If it remains unavailable, check the conversation guidance and save status, and request that the plan be saved if needed.

> **Distinguish "Skip" from stopping when you want to build later.** Skip proceeds to the Project without executing the plan. By contrast, the confirmation for stopping setup states that the Project and data will be deleted. Do not use stopping as a substitute for temporary saving when you want to retain the work location. See [Create a Project and Configure Basic Settings](create-project-and-basic-settings.md) for details.

### Execute in a normal conversation in an existing Project

Normal conversations do not necessarily have the same "Approve" button or plan-saving mechanism as immediately after creation. State the reviewed plan and clearly specify in writing what may be executed.

> From the agreed plan, execute only creation of the Agent. The target is this test Project. Do not delete existing resources, send externally, update CRM, or register automatic execution. After completion, list the created targets and tests not performed.

If missing connections or permissions are indicated, prepare them through the official configuration procedure. Do not paste confidential values into the conversation or grant unintentionally broad permissions to avoid errors.

## 5. Verify changes, not only responses

After execution, check in the following order.

1. In the conversation, check completed, failed, and unperformed work.
2. Check the names and locations of created or changed resources.
3. Open the target from the relevant resource list in the Project or the displayed resource panel.
4. Confirm that settings and content match the agreed plan.
5. Perform tests and check the results.

How resources are displayed varies by screen settings. Do not conclude that registration, saving, or publication in the Project is complete merely because descriptions, code, or files appeared in chat.

| Example target | What to check |
|---|---|
| Agent | Whether the name, instructions, reference information, and tools are intended, and whether it can respond to test requests |
| Workflow | Whether processing order, inputs and outputs, branches, and external operations follow the plan; distinguish draft testing from publication status |
| Object and Storage | Whether destinations, fields and data, and files are correct, and whether test data is not mixed into production use |
| Page | Whether not only source creation but also registration, startup, display, and required business operations can be checked; also check publication status and viewing scope |
| External integrations and automatic execution | Whether the connection destination, execution actor, sending and update targets, and start conditions are within the agreed scope |

You do not have to build every target at once. Check only what you created this time, then add the next capabilities as needed.

### Start testing with small inputs

Use fictitious data or authorized test data. In addition to normal inputs, check missing required information and unexpected inputs. Even when testing for test purposes, external services can send or update data, so check the targets and impact before executing.

When asking AI to report test results, ask it to separate "tests performed," "tests not performed," and "items that could not be confirmed." Check actual screens, saved results, and outputs in addition to the report.

## 6. Request edits and hand over remaining work

Request edits by narrowing the target and the difference you expect.

> For the created meeting-preparation Agent, edit it so that when meeting notes are empty, it asks for notes instead of inferring question ideas. Do not change other settings. Report the difference before and after the change and the retest results.

If something fails, check content saved partway through and the results of external operations before rebuilding everything from the beginning. Re-execution may add resources with the same name or duplicate the same operation.

Do not treat a stop operation or closing the browser as **undoing changes that have already been executed**. If you need to revert, check the change history, version management, and recovery method for each target.

Finally, the following information makes it easier to start follow-up work.

- Created and changed targets and where to open them.
- Tests performed and their results, and tests not performed.
- Unconfigured connections or permissions and items that remain manual.
- Whether publication, distribution, and business use may begin, and who decides.

Being able to build within a Project is separate from being ready for business use or Marketplace distribution.

## When things do not go well

| Situation | What to check and do |
|---|---|
| You cannot select Studio Assistant | Check the Project and usage permissions. If another Agent is selected initially, ask the responsible administrator whether you can use an Agent for building support. |
| Setup initialization fails | Check the displayed error and use "Retry" if it appears on the screen. Do not confuse it with deletion caused by stopping. The planning screen does not open for normal "Create." |
| You cannot select "Approve" | Check whether a response or save operation is in progress and whether the plan is saved. Do not try to proceed through repeated operations. |
| You approved but do not proceed to the execution screen | Check whether a conversation for execution was created. Do not approve repeatedly while the result is unknown. If it cannot be resolved, contact support. |
| You were told "Done" but cannot find the target | Check the target Project, resource name, whether saving or registration succeeded, and viewing permissions. Do not treat only a response or file generation as completed registration. |
| Some processing failed | Separate saved content from incomplete parts, resolve the cause, and rerun only the necessary scope. |

For an inquiry, include the organization and Project, conversation location, operation date and time, requested scope, displayed error, and saved results you confirmed. Do not include secret information or unnecessary business data.

## Completion checklist

- [ ] Made the request in the correct Project and with an Agent for building support
- [ ] Communicated the purpose, inputs, completion criteria, and scope that must not change
- [ ] Checked the latest plan and decided the scope to execute
- [ ] Distinguished plan approval, execution start, and building completion
- [ ] Actually opened and checked created and changed targets
- [ ] Separated test results from unperformed and unconfirmed items
- [ ] Clarified remaining work and the person responsible for deciding publication and business use

## Related articles

- [Create a Project and Configure Basic Settings](create-project-and-basic-settings.md)
- [Chat with an Agent and Check the Results](../use/chat-with-agents.md)
- [Prepare the Project Description and Context](prepare-project-context.md)
- [Test and Improve an Agent](test-and-improve-agents.md)
- [Test a Workflow Draft](test-workflow-drafts.md)
- [Register and Publish a Page from the Screen](register-and-publish-pages.md)
- [Understand Organization, Project, and App Permissions](../operations/understand-org-project-app-permissions.md)
- [Choose Between Personal Connections, Shared Connections, and Secrets](../operations/choose-personal-shared-connections-and-secrets.md)
- [Your First Project and Agent](../labs/first-project-and-agent.md)
