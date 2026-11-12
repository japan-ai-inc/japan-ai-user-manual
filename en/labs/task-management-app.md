# A Task Management App

In this exercise, you will combine an Object that stores fictional tasks, a Page for listing and updating them, and an Agent that explains their contents. Do not use real employee names, customer names, opportunities, or credentials. The goal is to compare values changed on the Page with Object records and Agent responses.

## Related articles

- [Design an Object structure](../build/design-object-structure.md)
- [View and update Object data](../use/view-and-update-object-records.md)
- [Ask AI to create or modify Pages](../build/ask-ai-to-build-pages.md)
- [Register and publish Pages from the screen](../build/register-and-publish-pages.md)
- [Configure Agent tools, Skills, and Datasets](../build/configure-agent-tools-skills-datasets.md)
- [Test and improve an Agent](../build/test-and-improve-agents.md)

## Before you begin

- Prepare a test Project where you can create and edit Objects, Pages, and Agents. If the features are unavailable, check permissions and availability conditions in the [Index of limits and availability conditions](../operations/limits-and-availability-index.md).
- Keep the Page's publication scope to the minimum necessary. This exercise does not add external connections, Secrets, API keys, triggers, or schedules.
- Fix the Object structure first. Do not change existing business data while creating the Page or Agent.

## Sample used in this exercise

| Target | Value used in this exercise |
|---|---|
| Object display name | Tasks (test) |
| Object name | `tasks_lab` |
| Page name | Task management (test) |
| Agent name | Task Guide Agent (test) |

Create the following fields in the Object.

| Display name | Field name | Type | Example value |
|---|---|---|---|
| Title | `title` | Text | Review proposal structure |
| Assignee | `assignee` | Text | Alex |
| Due date | `due_date` | Date and time | 2026-10-10 |
| Status | `status` | Text | Not started, In Progress, Complete |
| Priority | `priority` | Text | High, Medium, Low |

Register the following three fictional records.

| Title | Assignee | Due date | Status | Priority |
|---|---|---|---|---|
| Review proposal structure | Alex | 2026-10-10 | In Progress | High |
| Review demo screen | Jordan | 2026-10-14 | Not started | Medium |
| Update meeting notes template | Casey | 2026-10-07 | Complete | Low |

## Step 1. Prepare the Object and records

**Goal:** Let the Page and Agent reference the same test data.

1. In the Object list, select **"Create Object"** and set `tasks_lab` and the display name.
2. Use **"Create Field"** to add the five fields in the preceding table. For status and priority, use only the defined choices.
3. On the records screen, select **"Create New Record"** and register the three records in the preceding table.
4. Filter where `status` is "In Progress" and confirm that only "Review proposal structure" remains.

For Object design, see [Design an Object structure](../build/design-object-structure.md); for record operations, see [View and update Object data](../use/view-and-update-object-records.md). **Checkpoint:** You can compare field names, types, and the three values in both the list and details.

## Step 2. Create a Page that uses the Object and compare updates

**Goal:** Confirm that changes on the Page are reflected in the Object.

1. In the Page-creation conversation, provide the following requirements.

   > Create a new Page named "Task management (test)." List the title, assignee, due date, status, and priority in `tasks_lab`, and allow filtering by status and priority. Allow updates only to the status and priority of a selected record. Do not change the existing Object structure, existing Pages, external sending, or access settings. Before executing, propose the changes and confirmation procedure.

2. Confirm that the proposal includes the target Object, editable fields, and the scope that will not change, then request creation.
3. In the preview, check the three records and columns.
4. Update the status of "Review demo screen" to "In Progress," then confirm the same value in both the Page and Object record.
5. In the Page's **"Access settings,"** check the access mode required for users and the Object's view and edit permissions. Do not broaden the publication scope to test operation.

For Page creation and preview, see [Ask AI to create or modify Pages](../build/ask-ai-to-build-pages.md); for registration and access settings, see [Register and publish Pages from the screen](../build/register-and-publish-pages.md). **Checkpoint:** Do not stop after the Page displays successfully; also compare the updated value in the Object.

## Step 3. Create an Agent that answers about the Object

**Goal:** Check an Agent that answers based on current records.

1. Select **"Create Agent"** and create `Task Guide Agent (test)`.
2. Enter the following in the instructions.

   > Before answering questions about practice tasks, check the current records in `tasks_lab` using the available Object tool. Provide the relevant task, assignee, due date, status, and priority in that order. Do not infer values you cannot confirm; if no matching task exists, say so. This exercise does not create, update, delete, or send externally.

3. In the Agent tool settings, check and add the description and permissions for the option that reads Objects. If no option is available, do not guess at an alternative; ask the responsible person.
4. In a conversation, ask: "Tell me the assignee, due date, status, and priority for Review demo screen."
5. Compare the response with the Object record and Page display. Then ask about a task that does not exist and confirm that it does not invent plausible values.

For tool configuration, see [Configure Agent tools, Skills, and Datasets](../build/configure-agent-tools-skills-datasets.md); for testing, see [Test and improve an Agent](../build/test-and-improve-agents.md). **Checkpoint:** Do not treat instructions alone as configuring tools or permissions. Compare the response with the actual record.

## Common misunderstandings

| Misunderstanding | What to check |
|---|---|
| If it appears on the Page, the Object is also updated | Check the updated Object record directly. |
| Agent instructions alone let it read an Object | Check tool configuration, permissions, and the target Object separately. |
| Requiring Page sign-in also allows Object access | Page access conditions and Object permissions are separate. |
| A natural response is correct | Compare current records and the title you asked about. |

## If something does not work

| Situation | First thing to check |
|---|---|
| You cannot create fields or records | Check the Project, Object permissions, and input requirements in [Design an Object structure](../build/design-object-structure.md). |
| The Page does not display or updates are not reflected | Check the target Object, search criteria, Page settings, and Object-side values. See [Ask AI to create or modify Pages](../build/ask-ai-to-build-pages.md). |
| The Agent does not check the Object | Check the added tool, target, Agent saved state, and access permission in [Configure Agent tools, Skills, and Datasets](../build/configure-agent-tools-skills-datasets.md). |
| The response differs from the record | Do not use the response for business decisions. Check the question, record, and tool run result in [Test and improve an Agent](../build/test-and-improve-agents.md). |

## Cleanup

1. If you retain the Page, check its access settings and users, and keep wording in its name and description that identifies it as practice.
2. If you remove the Object tool from the Agent, confirm removal and saving. Removal does not delete the Object or conversations.
3. Before deleting the Agent, Page, Object, or records, confirm that other exercises and users do not reference them. Deleting an Object affects its records and references.
4. If you added external connections, automatic executions, or API keys during the exercise, first confirm that they are stopped, revoked, or unpublished.

## Read next

- [View and update Object data](../use/view-and-update-object-records.md)
- [Register and publish Pages from the screen](../build/register-and-publish-pages.md)
- [Work with Pages](../use/work-with-pages.md)
- [Test and improve an Agent](../build/test-and-improve-agents.md)
