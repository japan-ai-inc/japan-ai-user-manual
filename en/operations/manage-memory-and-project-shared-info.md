# Manage Memory and Project Shared Information

Memory and Project shared information differ in storage purpose and sharing scope. This article explains **how administrators distinguish personal Memory from information shared in a Project, and check the effects of viewing, changing, and deleting it**.

For operations to organize a Project's purpose, description, and common instructions, see [Prepare the Project Description and Context](../build/prepare-project-context.md). For organization, Project, and App permission relationships, see [Understand Organization, Project, and App Permissions](understand-org-project-app-permissions.md).

## Key points

| Item | Rule |
|---|---|
| Memory referenced by Agents in a Project | **Only Memory in that Project's scope.** Your Memory and Organization Memory are not referenced. |
| A list screen for Project Memory | Studio does not have one. "Memory Management" in organization settings handles "My Memory" and "Organization Memory." |
| Moving between scopes | There is no feature to move Memory between personal, organization, and Project scopes. |
| Manual creation | Not available in "Memory Management." Memory is created through interaction with an Agent. |

Put premises that Agents in a Project must always follow in the Project description, Custom Instructions ([Prepare the Project Description and Context](../build/prepare-project-context.md)), or materials, not in Memory.

## Before you begin

- Confirm the correct organization, target Project, and target to change.
- Check whether you will view, change, or delete it and the permissions required.
- Check existing content, target Agents, and affected users.

> The Memory screen and an Agent's "Use Memory" setting are displayed differently according to organization availability conditions and settings.

## Distinguish storage locations and sharing scopes

Even when the purpose is to provide context to AI, confusing storage locations may share information you wanted to keep personal or limit information that should be used throughout a Project to one owner.

| Type | What is mainly stored | Who is expected to find and use it | Management notes |
|---|---|---|---|
| Your Memory | Your continuing preferences and assumptions for your responsibilities | Your own usage context (not referenced by Agents in a Project) | Do not use it for Project-wide business rules. |
| Organization Memory | Assumptions handled commonly across the organization | Permitted users and Agents (not referenced by Agents in a Project) | Do not register Project-specific confidential matters. |
| Project Memory | Memory created through conversations with Agents in the Project | Agents in that Project | Studio has no list screen for it. It cannot be moved from other scopes. |
| Project "Description" | Project purpose, target, users, and exclusions | People opening the Project and those building or operating it | Stored separately from Memory. When "Custom Instructions" is set, the description is not passed to the AI. |
| Common instructions such as "Custom Instructions" | Terms, output format, decision policy, and operations outside scope | Policies to be followed commonly in the target Project | Instructions are not authorization settings or a place to store secret values or personal sensitive information. |
| Datasets, Storage, and Objects | Reference materials, files, and structured business data | Users and features within configured access scope | Do not transcribe all materials into content in place of Memory or instructions. |

Memory is a mechanism by which an Agent retains facts and context obtained through conversations and Tasks and references or updates them as needed. Being stored does not mean it is reflected in every response.

## 1. Check sharing scope and impact before changes

First, organize the target of the change in a register or operation record from the following perspectives. You do not need to copy the content itself into a ticket or chat.

| Check item | What to check |
|---|---|
| Storage location | Whether it is your Memory, Organization Memory, description, common instructions, or materials |
| Usage scope | Which individual, organization, Project, Agent, or work it may affect |
| Content and reason | Whether it is still correct and has no duplicate, contradiction, old date, or old owner name. Whether the action is addition, correction, consolidation, or retirement. |
| Impact and responsibility | Impact on Agents, users, descriptions, and common instructions; reviewer, updater, and trigger for review |

There is no feature to move Memory between scopes. Register premises that Agents in a Project should use again as a description, Custom Instructions, or materials. Do not include health or personnel information or unnecessary customer information.

> Joining a Project, being able to view Memory, being able to edit or delete Memory, and being able to configure an Agent are separate conditions. Do not conclude that you can make changes from a displayed role name alone; check whether the operation is available on the target screen and the permission guidance actually displayed.

## 2. Check the Memory list

If **"Memory Management"** appears in organization settings, first check the current list.

1. Select the correct organization and open **"Memory Management"** from settings.
2. Switch between **"My Memory"** and **"Organization Memory."** If Organization Memory is not visible, check your viewing permission.
3. Check **"Agent Name,"** **"Memory Content,"** **"Created at,"** and **"Updated at."**
4. Check the content, target Agent, and sharing scope, then decide the next action.

Check for incorrect customer information, outdated business conditions, and mixed personal information. Do not transcribe content into another Project's description or instructions without human review.

## 3. Check whether changes and deletion are possible

### Changing Memory

Whether Memory can be changed or deleted differs by target and permission. Organization and personal Memory can be changed only by the owner, administrator, or individually permitted people, depending on organization settings.

1. Check whether you can select **"Edit."** If you cannot, do not recreate it as different Memory.
2. Check the content before changes, target Agent, and update date and time.
3. Revise the content so correct facts, applicable scope, and exceptions are clear. Do not leave assumptions, unsupported numbers, or expired premises.
4. After saving, display the list again and check the content and **"Updated at."**
5. With an affected Agent, use a small request that does not contain confidential information to check whether unexpected references or old premises remain.

Manual new creation in this management screen is disabled; Memory is created through interaction with an Agent. You cannot enable manual creation by adding permission. To have Agents in a Project follow common policies, manage them in the Project description or Custom Instructions.

### Deleting Memory

A confirmation says that deletion cannot be undone. Before deletion, check that the target is not different Memory with the same name and does not conflict with currently used Projects, Agents, or common instructions.

1. Check the content, target Agent, and usage scope.
2. Check the reason for deletion and whether correct content exists in another storage location.
3. Select **"Delete"** in the target row and read the deletion target in the confirmation screen.
4. After confirmation, display the list again and confirm the target disappeared.
5. If the impact is large, check related Agents and representative requests, then inform users and the administrator of the change.

> Do not assume you can retrieve the original content from the screen after deletion. If correct premises must remain, decide the storage location and sharing scope first, reflect approved wording in another appropriate location, and then delete it. Deleting or creating a conversation does not mean that Memory or Project shared information is deleted at the same time.

## 4. Review Project shared information

Separate roles for information shared in a Project among the description, common instructions, and materials or data. Do not treat organizing Memory alone as updating the Project description or common instructions.

1. Open the target Project's **"Project Settings" → "Basic Settings."**
2. Check **"Description"** under **"Project Information"** and confirm that the purpose, target users, target work, and exclusions match current operations.
3. Check that **"Custom Instructions"** does not conflict with Memory. When Custom Instructions is set, the "Description" is no longer passed to the AI, so include needed premises in Custom Instructions.
4. Do not transcribe large amounts of materials or business data into Memory content; check their actual storage locations and access settings.
5. Record changed storage locations.

For the procedure to change Project descriptions and common instructions, and differences among saving, canceling, and deleting, check [Prepare the Project Description and Context](../build/prepare-project-context.md) as the authoritative source. Instructions alone cannot revoke permission for external operations.

## Notes on sensitive and shared information

Do not casually store the following in Memory, descriptions, or instructions, whether personal or Project shared.

- Secret values such as passwords, API keys, tokens, and authentication codes
- Information about a specific person's health, personnel matters, evaluation, or private life
- Customer information, contract conditions, or unpublished sales information not approved for sharing
- Information accessible only through an individual's account or that cannot be handed over after transfer
- Unsupported facts, assumptions, or expired pricing, date, or owner information

Use a management feature suitable for the purpose, such as Project Secrets, for secret values. See [Register, Update, and Delete Project Secrets](manage-project-secrets.md) for steps. Keep shared information to the minimum, and check accuracy and update responsibility.

## Troubleshooting

| Situation | Check and response |
|---|---|
| "Memory Management" is not displayed | Check organization availability conditions, the correct organization, and permissions. |
| You want to see a list of Project Memory | Studio has no list screen for it. Manage premises for Agents in a Project with the description, Custom Instructions, and materials. |
| You cannot view Organization Memory | Check viewing permission. Do not work around it by copying personal Memory as shared information. |
| You cannot select "Edit" or "Delete," or saving fails | Check the target storage location, individual operation permission, and displayed error. Do not repeat the same operation. |
| You cannot add Memory manually | Manual creation is disabled in this management screen. Distinguish this from editing existing Memory, and check a suitable storage method, such as interaction with an Agent or the Project description or Custom Instructions. |
| AI uses old information or does not use it | Agents in a Project reference only Project-scope Memory. Do not conclude that Memory alone is the cause. Separate the target Agent configuration, common instructions, conversation request, access to materials, and save result. |
| You cannot decide whether to delete it | Identify the target Agent, Project, and users, and do not delete until correct replacement information is saved and representative checks are complete. |

For an inquiry, include the organization name, Project name, storage location, target Agent, operation date and time, displayed message, and confirmed results. Do not attach confidential information or actual Secret values.

## Management checklist

- [ ] I distinguished the storage locations for personal, organization, and Project information, descriptions, common instructions, and materials or data.
- [ ] I understand that Agents in a Project reference only Project-scope Memory.
- [ ] I checked the correct organization, Project, target Agent, and permissions to view, change, and delete.
- [ ] Before changes, I checked content accuracy, sharing scope, impact, and the responsible person.
- [ ] I checked the scope of additions, changes, and deletion and currently available management methods.
- [ ] Before deletion, I checked needed replacement information and affected users and Agents.
- [ ] I stored and checked descriptions, common instructions, Memory, and materials separately.
- [ ] I did not store secret values, personal information, or unapproved business information in shared information.
- [ ] I checked results after changes using a representative request without confidential information.

## Related and next articles

- [Prepare the Project Description and Context](../build/prepare-project-context.md)
- [Understand Organization, Project, and App Permissions](understand-org-project-app-permissions.md)
- [Manage Project and App Members](manage-project-and-app-members.md)
- [Register, Update, and Delete Project Secrets](manage-project-secrets.md)
