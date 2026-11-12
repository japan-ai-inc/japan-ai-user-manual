# Configure Tools, Skills, and Datasets for an Agent

Combine the capabilities and reference information an Agent needs for its work. This article explains **how to configure tools, Skills, and Datasets for an existing Agent, and check saving, authentication, user access, and actual behavior**.

For Agent creation, instructions, and model configuration, see [Create an Agent and Set Its Instructions and Models](create-agent-instructions-and-models.md). This article focuses on selecting existing tools, Skills, and Datasets; other articles explain new creation and detailed connection configuration.

## Before you begin

- Check the organization, Project, and Agent to configure, and confirm permission to edit the Agent.
- Check the existing configuration and the people and work using this Agent. Do not add experimental capabilities to an Agent in use without authorization.
- Decide what may be read, output, and changed.
- Check the contents and sharing scope of Skills you intend to use, as well as Dataset materials and update status.
- When using external services, check the account used for connection and the scope of permitted operations.
- Prepare test inputs without confidential information and expected results.

> **Agent settings are automatically saved.** In particular, adding and removing plugins are saved even before you close the selection screen. Do not use "Close" as a way to cancel changes. This behavior differs from confirming a Dataset selection.

Screen layouts and choices vary depending on organization settings, Projects, access permissions, and availability. This article focuses on the PC screen.

## 1. Separate required capabilities and reference information

| What to configure | Main role | Check before configuring |
|---|---|---|
| Tool | Performs operations such as search, file processing, and access to external services | Whether it reads or updates, whether authentication is required, and whether it can access target data |
| Skill | Lets an Agent use reusable procedures and knowledge | Whether its content suits the purpose, necessary tools or connections are available, and its sharing scope lets users use it |
| Dataset | Configures a source of information for referencing business materials | Whether it contains the correct materials, updates and ingestion are complete, and users are permitted to reference it |

In Agent settings, add tools and Skills from **"Plugins."** Select Datasets from **"Data Integration."** Operate by matching each capability's role with the name displayed on the screen.

### At first, select only what the purpose needs

For example, for an Agent that "creates response drafts based on internal FAQs," first check the Dataset that contains the target FAQs. If you want to standardize response structure or confirmation procedures, you can add a Skill whose content you have checked. If external search or sending email is unnecessary, do not add it without understanding its purpose.

This is an example of how to consider a configuration; it does not mean that a particular FAQ Skill or connection is provided to every organization.

**Writing a capability name in instructions alone does not complete tool configuration or authentication.** Also, do not assume that adding a plugin guarantees it will execute in every conversation. State the required operations and reference information in the instructions too, and check the results.

## 2. Open settings for the target Agent

1. Open the target organization and Project.
2. From the Agent list, open the edit screen for the Agent to configure.
3. Check the Agent name and existing instructions.
4. Open the area where settings such as "Configuration" and "Instructions" appear.
5. Check **"Plugins"** and **"Data Integration."**

Do not confuse temporarily selecting a capability in a conversation with editing the Agent's own settings. This article covers operations in the Agent settings screen.

If you cannot edit it, check use permissions and editing permissions separately. Merely being able to chat with an existing Agent does not necessarily mean you can change its configuration.

## 3. Add a tool

1. In the **"Plugins"** field, select **"Add."**
2. Enter the capability or plugin name you want to use in the search field.
3. As needed, switch displayed categories such as "Official List," "Project," "Created by Organization," and "Created by Me."
4. Check the candidate's name, description, "Tool" or "Skill" display, and availability scope.
5. Select **"Add"** for the candidate that suits the purpose.
6. Close the selection screen and confirm that the added item appears in the Agent's Plugins field.
7. Check the Agent's save status and authentication guidance.

Even when names are similar, the target service or scope of reading and updating may differ. Do not choose based on the name alone; compare the description with the intended use.

### Closing the add screen does not cancel a change

"Add" and "Remove" for plugins change the selection in the Agent form each time. Closing the selection screen does not cancel that change.

If you add something by mistake, identify the target, remove it from the Agent settings, and then check the saved result. Understanding the configuration before adding helps you identify what to restore.

### If you cannot find a candidate

- Shorten your search terms and check category filters.
- Check that you are operating in the correct Project and organization.
- Ask the responsible person about availability, listing in the selection screen, sharing scope, and use permissions.

Do not conclude solely from its absence in search results that it is not contracted, does not exist, or has been deleted.

## 4. Add a Skill and check its sharing scope

You also select a Skill through "Plugins" → "Add." Check the content and purpose of candidates displayed as "Skill" before adding them.

Check the following.

- Which requests the Skill is used for.
- Which procedures and outputs it is intended to provide.
- Whether it operates external services or needs additional authentication.
- Whether people who use the Agent, not only you, can use it.
- Whether it conflicts with the Agent's existing instructions.

If a path to open details is displayed, use it to check the content. [Create and Use Skills](create-and-use-skills.md) covers creating and revising Skills.

### When a private Skill warning appears

When you configure a private Skill for yourself in an Agent that other people use, a warning about visibility may appear. **Sharing an Agent and sharing a Skill are separate settings.** Just because the creator could use it does not necessarily mean that other users can.

1. Check the Skill causing the warning.
2. Check the content included in the Skill and intended users.
3. Decide with the owner or responsible administrator whether it may be shared.
4. Only if sharing is permitted, use guidance such as "Visibility change required" to check and save the target Skill and scope. In visibility choices, distinguish the scope available within the organization from the scope only for you.
5. Confirm that Skills used in a shared Agent do not remain in a scope only for you. If saving fails, reopen it and check which Skills were changed.
6. If it cannot be shared, replace it with another Skill users can use or review the Agent configuration.

**Changing visibility is not only an adjustment to what is displayed in this Agent; it changes the Skill's own settings.** Do not broaden visibility merely to remove a warning. Even if you share a Skill, do not treat its external-service credentials as having been provided to users.

If "Inaccessible plugins" appears, check with the relevant owner or responsible administrator. Distinguish it from simply still loading, and identify why candidates cannot be retrieved before taking action.

## 5. Check required authentication and connections

Plugins that use external services may display **"Authentication Required."** Adding and authenticating are separate operations.

1. Check which service to connect to and whose account to use.
2. If "Authentication Required" appears and the connection is permitted, open the authentication screen through its guidance.
3. Check the target account and requested permissions, then proceed with authentication.
4. If guidance requests that you contact an administrator, ask the responsible person to configure it.
5. Return to Agent settings and check the authentication display and saved content.
6. Use authorized test data to verify actual behavior, such as reading data.

An on-screen authenticated display alone does not confirm that you can read target files or records or perform required updates. You must verify expiry, permissions at the connection destination, and access to target data through actual operations.

Whether to use a personal connection or shared connection, and whether each user needs individual authentication, depends on the connection method. Do not state that everyone can use the creator's connection.

Do not paste API keys, passwords, or Secret values into Agent instructions or Skill text. [Choose Between Personal Connections, Shared Connections, and Secrets](../operations/choose-personal-shared-connections-and-secrets.md) covers credential management.

## 6. Select a Dataset

To let the Agent reference materials, select a Dataset from **"Data Integration."**

1. Open the target Agent's **"Data Integration"** field.
2. In the **"Select Dataset"** screen, find the Dataset you need.
3. Select the target checkbox. Also check Datasets already selected.
4. Clear the checkbox for Datasets you do not want referenced.
5. Check the selected item count at the bottom of the screen, then select **"Apply."**
6. Return to the Agent's "Data Integration" field and check the selected names and Agent save status.

**Selecting "Apply" in the selection screen and successfully saving Agent settings are separate checks.** If you select "Cancel" or close the screen without confirming the selection, those selection changes are not applied to the Agent. Its behavior is not the same as the plugin-selection screen.

What you select here is reference information configured for the Agent. Treat it separately from naming materials, or attaching files in a conversation.

### What to check before and after selection

- Whether it contains materials for the correct work, organization, and version.
- Whether ingestion or synchronization of updated materials is complete.
- Whether the answer to test questions is included in the materials.
- Whether users may be permitted to reference the materials.
- Whether you have selected old versions or similar materials redundantly.

Selecting a Dataset does not complete ingestion of original materials or newly grant reference permissions. Even if a new-creation button appears, whether you can create one depends on permissions and other conditions. For material preparation and ingestion, see [Prepare a Dataset and Make an Agent Reference It](prepare-and-reference-datasets.md).

### Separate this from use cases that must read all text without omissions

Configuring a Dataset does not mean that the full text of all materials is used in every response. Design separately for searching relevant information to answer and for comprehensive checking, such as "check every clause in a contract without omissions."

For comprehensive processing, you need a separate procedure to specify target files and verify the scope whose content was retrieved and checked. Even with attachments, do not conclude that the full text was checked merely because a file was attached.

## 7. Refine instructions and verify with small inputs

Write in the instructions when and how to use configured capabilities and materials. Rather than adding plugins without explanation, clarify their roles and verify behavior.

### Example instructions

> Reference the target FAQ materials and create a response draft for the user's question.
> - Do not fill in content without evidence in the materials by guessing; state that it is unconfirmed.
> - Separate output into "Response draft," "Confirmed evidence," and "Additional items to confirm."
> - Do not claim to have read materials you cannot reference.
> - In this test, do not send externally or update business data.

This is an example of instructions. It does not replace Dataset selection or tool authentication. Also, an instruction to "not update" is not a setting that revokes update permissions at the connection destination.

### Verify in stages

| Verification stage | What to check |
|---|---|
| Configuration | Required plugins and Datasets are selected, and there are no unnecessary items |
| Save | There is no save error, and content remains after reopening the settings screen |
| Access | Intended users can use the Agent, Skills, and materials, and required connections are available |
| Behavior | With small inputs, expected materials or capabilities are used and results are correct |

First, try a question whose answer is in the materials and one whose answer is not. When external-service connections are included, begin with low-impact operations, such as reading authorized test targets.

Do not treat an AI response such as "I referenced it" or "I executed it" as completion. Check the evidence in the response, displayed execution results, and target data. Obtain approval of the target and content before testing that sends or updates. Treat tool use during verification as an actual operation as well.

[Test and Improve an Agent](test-and-improve-agents.md) covers detailed test design.

## 8. Remove unnecessary configuration

### Remove a tool or Skill

Use the removal icon on the target card in the Plugins field, or "Remove" for the target in the add screen. After saving, confirm that the target was removed.

This operation **removes configuration from the Agent**. It does not delete the Skill itself, disconnect an external service, or undo already executed processing.

### Remove a Dataset

Open "Data Integration," clear the target checkbox, and select "Apply." Also check the Agent's saved result. Clearing a Dataset selection and deleting the original materials or Dataset itself are separate operations.

Do not assume that removing configuration deletes information in past conversations or outputs or prohibits use through other paths. If use must be prohibited, also ask the responsible person to check sharing, permissions, and connection management.

## When things do not go well

| Situation | What to check and do |
|---|---|
| You cannot find a tool or Skill | Check search and categories, organization and Project, listing status, and sharing scope |
| You cannot add it | Check availability, on-screen warnings, and required permissions. Separately check being displayed and selectable as a candidate and being usable in other environments. |
| "Authentication Required" remains | Check the authenticated service and account, and the connection result |
| You cannot process it even though it is authenticated | Check access to target data, permissions at the connection destination, expiry, and execution errors |
| Only the creator can use a Skill | Check sharing scope for the Agent and Skill separately, and each user's connection |
| A Dataset is not reflected in settings | Separately check that you selected "Apply" and that the Agent was saved |
| The answer differs from materials | Check Dataset version and ingestion status, instructions, the question, and actual referenced content |
| A save error appears | Check the error content and review required inputs and permissions. Do not treat it as complete before saving succeeds. |

For an inquiry, include the organization name, Project name, Agent name, target plugin or Dataset name, operation date and time, error, and the stage you confirmed. Do not include authentication information or confidential material content.

## Completion checklist

- [ ] Separated capabilities required for the purpose from reference information
- [ ] Configured tools, Skills, and Datasets for the target Agent
- [ ] Distinguished immediate plugin changes from Dataset "Apply"
- [ ] Checked Skill content and sharing scope, and user access
- [ ] Checked required authentication and permissions at the connection destination
- [ ] Checked the saved result and confirmed that configuration remains after reopening
- [ ] Checked evidence and actual behavior with small inputs
- [ ] Removed unnecessary configuration and distinguished removing settings from deleting original data

## Related articles

- [Roles of the Components](../getting-started/studio-components.md)
- [Create an Agent and Set Its Instructions and Models](create-agent-instructions-and-models.md)
- [Chat with an Agent and Check the Results](../use/chat-with-agents.md)
- [Test and Improve an Agent](test-and-improve-agents.md)
- [Prepare a Dataset and Make an Agent Reference It](prepare-and-reference-datasets.md)
- [Create and Use Skills](create-and-use-skills.md)
- [Choose Between Personal Connections, Shared Connections, and Secrets](../operations/choose-personal-shared-connections-and-secrets.md)
