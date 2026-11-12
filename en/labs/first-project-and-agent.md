# Your First Project and Agent

In this exercise, you will create an Agent that organizes meeting notes in a test Project and check a conversation with fictional input. Do not add external connections, Datasets, Workflows, or Pages. Finally, clean up the conversation and Project.

## Related articles

- [Getting started prerequisites and basic screens](../getting-started/getting-started-and-navigation.md)
- [Create a Project and configure basic settings](../build/create-project-and-basic-settings.md)
- [Create an Agent and configure instructions and models](../build/create-agent-instructions-and-models.md)
- [Chat with an Agent and check results](../use/chat-with-agents.md)
- [Test and improve an Agent](../build/test-and-improve-agents.md)
- [Index of limits and availability conditions](../operations/limits-and-availability-index.md)

## What you will create and the completed result

The finished item is a practice Agent that separates pasted meeting notes into key points, decisions, next actions, and unconfirmed items. Confirm that it does not add dates, assignees, or decisions absent from the input, and does not send externally or create calendar entries. Instructions alone cannot restrict permissions, so use a test Project that does not add tools or connections.

## Before you begin

- Confirm an organization where you can create and edit Projects and Agents. If you cannot find the feature, check organization settings, your contract, and permissions in the [Index of limits and availability conditions](../operations/limits-and-availability-index.md).
- Do not enter real customer names, personal information, meeting materials, or credentials.
- Before starting, check [Create a Project and configure basic settings](../build/create-project-and-basic-settings.md) and [Create an Agent and configure instructions and models](../build/create-agent-instructions-and-models.md) for detailed procedures.

## Sample used in this exercise

| Item | Value used in this exercise |
|---|---|
| Project name | `Practice_meeting-notes-summary` |
| Project description | A practice Project for organizing fictional meeting notes. Does not send externally, create calendar entries, or update business data. |
| Agent name | `Meeting Notes Summary Assistant (practice)` |
| Agent description | Organizes pasted meeting notes. |

Enter the following in the Agent instructions.

> You are an Agent that helps summarize meeting notes. Based only on the notes pasted in this session, organize them in this order: key points, decisions, next actions, and unconfirmed items. Do not infer and add assignees, deadlines, numbers, or decisions that are not stated; identify them as unconfirmed items. Do not send externally, create calendar entries, or update business data. Return only an organized version of the meeting notes.

Use the following fictional notes in the conversation.

```text
Review the introductory materials for new product A. The sales team requested an update to the pricing table.
Ms. Tanaka will prepare the first draft by the next meeting. The date of the next meeting is undecided.
```

## Step 1. Create a practice Project

**Goal:** Prepare a workspace that can be distinguished from production.

1. In the Studio Workspace, confirm the target organization and open **"Create Project"** from **"Create."**
2. Enter the Project name and description from the sample table. Do not add members who are not participating this time.
3. Select **"Setup,"** and if you will build later, open the Project with **"Skip"** on the screen.
4. Confirm that the name and description identify the Project as a test environment.

For details, see [Create a Project and configure basic settings](../build/create-project-and-basic-settings.md). **Checkpoint:** You can open `Practice_meeting-notes-summary` and identify it as a test Project.

## Step 2. Configure the Agent

**Goal:** Save an Agent that only organizes meeting notes.

1. In the Project Agent list, select **"Create Agent"** and enter the sample name and description.
2. Paste the preceding instruction into **"Instructions (System Prompt)."**
3. Check the options displayed in **"Available Models,"** add options to test if needed, and select **"Set as default."**
4. Do not add tools, Skills, Datasets, or external connections. Reopen the Agent and check its name, description, instructions, and model.

For details, see [Create an Agent and configure instructions and models](../build/create-agent-instructions-and-models.md). **Checkpoint:** The Agent configuration is retained and you can confirm the model being tested.

## Step 3. Check the conversation result

**Goal:** Confirm that facts absent from the input and external actions are not treated as completed.

1. Open a conversation with the Agent you created and confirm its name again.
2. Send the sample notes and check whether key points, decisions, next actions, and unconfirmed items are separated.
3. Check that the undecided date is not added as a confirmed date.
4. Next, send a short request to create a calendar entry or send an email, and confirm that it does not say that it performed the action.

For conversation procedures, see [Chat with an Agent and check results](../use/chat-with-agents.md); for testing with more conditions, see [Test and improve an Agent](../build/test-and-improve-agents.md). **Checkpoint:** You can compare the response with the original text and distinguish missing information from out-of-scope requests.

## Common misunderstandings

| Misunderstanding | What to check |
|---|---|
| Writing it in instructions also removes permission for external actions | Instructions, tools, connections, and permissions are separate. This exercise does not add them. |
| Closing the screen removes Agent changes | Agent settings are automatically saved. Reopen it to check the saved state. |
| A natural response means the content is correct | Compare it with the original input, unconfirmed items, and next actions. |

## If something does not work

| Situation | First thing to check |
|---|---|
| You cannot find the Workspace, Project, or creation controls | Check the organization, sign-in destination, permissions, and availability conditions in [Getting started prerequisites and basic screens](../getting-started/getting-started-and-navigation.md) and [Create a Project and configure basic settings](../build/create-project-and-basic-settings.md). |
| You cannot create or edit an Agent | Check the correct Project, creation permission, and on-screen guidance in [Create an Agent and configure instructions and models](../build/create-agent-instructions-and-models.md). |
| You cannot send a conversation or check a response | Check the Agent name, saved and enabled state, access permission, and error message in [Chat with an Agent and check results](../use/chat-with-agents.md). |
| The notes are not organized as instructed | Compare the instructions, selected Agent and model, and original input; test changes one at a time in [Test and improve an Agent](../build/test-and-improve-agents.md). |

## Cleanup

1. To retain a practice conversation, confirm the target in the conversation list and use the displayed **"Add to archive."** This organizes the conversation; it does not delete the Agent or Project.
2. To retain the Project, confirm that its name and description identify it as practice and do not add real data.
3. If it is unnecessary, confirm that the Agent, conversations, members, external connections, and automatic executions you created are not used elsewhere before deleting the Project. For pre-deletion checks and handover, see [Check deletion, handover, and audit logs](../operations/deletion-handover-and-audit-logs.md).

This exercise does not create schedules, triggers, API keys, or public Pages. If you added them during the exercise, stop, revoke, or unpublish each one before cleaning up the Project.

## Read next

- [Create an Agent and configure instructions and models](../build/create-agent-instructions-and-models.md)
- [Configure Agent tools, Skills, and Datasets](../build/configure-agent-tools-skills-datasets.md)
- [Test and improve an Agent](../build/test-and-improve-agents.md)
