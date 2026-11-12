# Prepare the Project Description and Context

Tell Project users and AI the purpose, terminology, and work assumptions. This article explains **how to organize the Project description, register common instructions according to available settings, and check saved content and responses**.

For creating a Project and configuring its name and icon, see [Create a Project and Configure Basic Settings](create-project-and-basic-settings.md). For individual build requests, see [Ask Studio Assistant to Build for You](build-with-studio-assistant.md).

## Before you begin

- Confirm the target organization and Project, and that you have permission to change settings.
- Read existing descriptions and instructions, and confirm with the person responsible what you may change.
- Organize users, target business operations, correct terminology, and how to handle uncertain decisions.
- Prepare input examples without confidential information for verification.

> Screens and context-management methods differ by organization settings. The "Custom Instructions" procedure in this article applies when that field appears in a Project's basic settings. Do not conclude that its absence alone means you lack permission or it has not been configured.

## 1. Separate the roles of descriptions, context, and instructions

"Context" is background and assumptions that help AI understand the meaning of a request. Not every environment has a separate input field called "context."

| Location for information | What to write mainly | Do not confuse with |
|---|---|---|
| Project "Description" | What the Project is for, who uses it, target business operations, and scope | Make it an overview that communicates the purpose, not merely a supplement to the name |
| Settings field when "Custom Instructions" are available | Terminology, output formats, and decision policies to follow in common in the Project | Save it separately from the Project description. Changing the description does not automatically rewrite old instructions left here |
| Individual Agent instructions | Work handled by that Agent, and its specific procedures and output | Do not layer content that conflicts with Project-wide policy |
| Request in a conversation | This target, materials to use, scope of work, and expected result | Do not treat content merely conveyed in conversation as saved in the Project's common settings |
| Business materials and data | Detailed regulations, reference materials, and business information that changes | Do not put all materials into the description field. Storing materials and configuring AI to reference them are separate |

Even if a screen or material says "Project Context," check what feature it manages in the current environment. Do not assume that descriptions, common instructions, materials, and Memory all have the same storage location and sharing scope.

**Saving descriptions or instructions does not guarantee that AI reads everything every time and always follows it.** You must also manage permissions and connection settings for allowed and prohibited operations and data-viewing scope.

## 2. Make the Project description short and specific

Write content in the description that lets a person opening the Project for the first time understand its purpose. Combining the following items into a short statement aligns assumptions for building and use.

- **Purpose**: What it supports.
- **Users**: Who uses it and when.
- **Target information**: What is expected as input or reference material.
- **Expected result**: What should be obtained.
- **Out of scope**: Business operations not yet handled or operations not performed.
- **Operational category**: Whether it is for verification or production business operations.

### Example description

> This is a Project for verification to help sales representatives organize items to check before a meeting. Based on company information and meeting notes provided by users, create a preparation memo that separates facts, unverified items, and question ideas. Sending email to customers, updating the CRM, and confirming contract terms are out of scope. Use only approved test data for verification.

This text is an example. Writing it in the description does not configure connections to external systems or material references.

### Procedure for saving

1. Open the target Project and confirm its name and organization.
2. Open **"Basic Settings"** from **"Project Settings."**
3. In **"Project Information,"** select **"Edit."
4. Modify the **"Description."** Leave items such as the name unchanged if they do not need changing.
5. Select **"Save"** and check the update result and displayed content.

You can leave a description empty after creation, but we recommend retaining an overview that lets users determine the purpose. If you select "Cancel" for unsaved description edits, read the displayed confirmation about discarding changes.

## 3. Organize common instructions

While a description communicates "what this place is for," common instructions make "how you want it to respond" specific.

### Writing guidance

| Item | Example of how to write it |
|---|---|
| Terminology | "Customer" means the company on the other side of a meeting. When referring to the individual in charge, write "customer contact." |
| Handling unknowns | Do not fill in amounts, dates, or contract terms not in the material by guesswork; indicate "Unverified." |
| Output format | Organize in the order: conclusion, grounds, unverified items, next actions |
| Target confirmation | For requests involving updates or sending, check the target, content, and scope of execution |
| References | State the names and reference dates of materials to use, and say so if they cannot be referenced |

### Example custom instructions

> In this Project, help create meeting-preparation memos.
>
> - Separate input facts from proposals and inferences.
> - If amounts, dates, or contract terms are not in the materials, state "Unverified."
> - Order output as "Key points," "Grounds," "Unverified items," and "Question ideas."
> - When you can identify referenced materials, include the material name and reference date. Do not state that you read materials you cannot reference.
> - If the company name or this target is ambiguous, confirm it before working.
> - In this verification, do not send externally or update existing business data.

**Instructions such as "do not update" and "do not send" do not revoke operation permissions.** For operations that must actually be prohibited, also check permissions for the Agents, tools, and connections used with the person responsible.

Do not paste API keys, passwords, Secret values, or confidential information for a specific case into common instructions. It is not a private note field; check the content and sharing scope as a Project common setting.

## 4. Register according to the settings method shown

### When "Custom Instructions" appears

1. Open **"Project Settings" → "Basic Settings"** for the target Project.
2. Check the current content in **"Custom Instructions."**
3. If it is not configured, select **"Add Custom Instructions"**; if it is already registered, select **"Edit"** for that field.
4. Enter the organized instructions in **"Custom Instructions Content."** When editing registered content, confirm that you do not remove policies that must remain.
5. Select **"Save."**
6. Check the successful-save notification and the text returned in the displayed field. If necessary, reopen the settings screen and confirm that the change remains.

Descriptions and custom instructions are saved separately. Do not conclude that an in-progress custom instruction was also saved by selecting "Save" for the description field.

You cannot select "Save" if there is an input error, no change, insufficient permission, or similar issue. **If you select "Cancel" while editing custom instructions, unsaved input is not saved. A discard confirmation like the one for description edits does not necessarily appear, so retain any needed draft before operating.**

The custom-instruction input shows a character count. However, a save error may occur even within the displayed count. Limit long text to common key points and check the save result. If you cannot save, preserve the input content and tell the person responsible whether the operation is adding or editing, the character count, and the displayed error. Do not avoid the issue by deleting and recreating existing instructions.

### When "Custom Instructions" does not appear

The display varies by organization context-management method and your permission or role.

- First organize the Project description and check the save result.
- Ask the administrator which feature or storage location manages common context and instructions.
- If another management feature is used, follow its sharing scope and update method.
- Even when temporarily adding needed assumptions to a conversation, do not treat common-setting registration as completed.

Do not use another screen URL or API as a substitute to make the field appear, or indiscriminately register the same instructions in multiple locations. [Manage Memory and Project Shared Information](../operations/manage-memory-and-project-shared-info.md) covers management of Memory and Project shared information.

## 5. Check saved content separately from actual responses

Check in the following two stages.

### Confirm saving

- Did you change settings in the correct Project?
- Do both the description and instructions have no old purpose or out-of-scope processing remaining?
- Was there a successful-save notification, and does the content remain after reopening?
- Is there any contradiction between added and existing text?

### Confirm responses

Send a small request without confidential information to an Agent used in the target Project, and check that its format and judgment meet expectations. To separate it from assumptions in old conversations, check in a new conversation as needed. Using a new conversation itself does not guarantee that changes are reflected.

Example:

> Create a meeting-preparation memo using the following test information.
>
> Customer: Sample Trading Company. Topic: Organizing inquiry handling.
>
> No budget, desired implementation date, or contract terms have been provided.
>
> Do not send externally or update data; create only the memo.

Do not merely check whether the AI answers "Understood."

- Is the output in the specified headings and order?
- Has it invented an unprovided budget or date?
- Are facts and proposals separated?
- Does it avoid claiming to have referenced materials it could not read?
- Has no out-of-scope operation been executed?

If results differ from expectations, check the target Project and Agent, saved content, Agent-specific instructions, conversational request, and access to materials. "Saved" and "reflected in this response" are separate checks. Do not treat one successful result as a guarantee that all Agents, conversations, and Workflows will produce the same result.

## 6. Review old text when assumptions change

When moving from verification to production operation, expanding target business operations, or replacing reference materials, review descriptions and instructions together.

1. Check the reason for the change and affected users and Agents.
2. Decide which old text to replace. Do not merely append to the end; organize conflicting content.
3. Save the description and instructions separately.
4. Check responses and results of any needed processing with representative input.
5. Tell users the changes and remaining constraints.

"Delete" to stop using saved common instructions differs from canceling unsaved input. Deleting custom instructions removes those instructions; it does not delete the entire Project or collectively delete the description and other materials. When deletion is needed, check the impact with the person responsible, then follow the confirmation screen for the target field. [Manage Memory and Project Shared Information](../operations/manage-memory-and-project-shared-info.md) covers detailed deletion and shared-information operations.

## When things do not work

| Situation | Check and response |
|---|---|
| Cannot find custom instructions | Check the organization's management method and your permissions and role. Do not conclude it is unconfigured merely because it is hidden |
| Can edit the description but cannot save instructions | The permissions required to change a description and add or update instructions may differ |
| Cannot select "Save" | Check input errors, whether there is a change, and permissions |
| Long text fails to save | Preserve the draft and organize key points. Do not conclude it can be saved based only on the character-count display |
| Cannot find content that should have been saved | Check the target Project, save notification, and whether you edited the description or instructions |
| AI answers differently from instructions | Separate the save result, Agent-specific instructions, conversation assumptions, and materials used |
| Does not reference materials | Separately check writing material names in the description and access/reference settings for the materials |

For an inquiry, include the organization name, Project name, edited field, whether it was an addition or update, operation date and time, error, and save and response verification results. Do not include confidential values or customer information.

## Completion checklist

- [ ] The Project's purpose, users, and scope can be understood from the description
- [ ] Separated the overview, common instructions, individual requests, and reference materials
- [ ] Checked where the current environment manages common context
- [ ] Saved to the target Project and checked content after reopening
- [ ] Did not include confidential values in common instructions and distinguished them from required permission settings
- [ ] Verified format, unverified items, and reference content with a small input
- [ ] Organized old text and contradictions, and communicated needed changes to users

## Related articles

- [Roles of the Components](../getting-started/studio-components.md)
- [Create a Project and Configure Basic Settings](create-project-and-basic-settings.md)
- [Ask Studio Assistant to Build for You](build-with-studio-assistant.md)
- [Test and Improve an Agent](test-and-improve-agents.md)
- [Understand Organization, Project, and App Permissions](../operations/understand-org-project-app-permissions.md)
- [Manage Memory and Project Shared Information](../operations/manage-memory-and-project-shared-info.md)
