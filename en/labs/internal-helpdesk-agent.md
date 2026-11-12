# An Internal Helpdesk Agent

In this exercise, you will import a fictional expense reimbursement FAQ into a Dataset and create an Agent that references it. Compare a question answered by the document with one it cannot answer, then compare responses with the original text. Do not use real employee names, requests, receipts, or credentials.

A Dataset is for finding relevant information to use in answers. Use a separate approach for tasks such as checking every item in a contract without omissions.

## Related articles

- [Prepare and reference a Dataset](../build/prepare-and-reference-datasets.md)
- [Configure Agent tools, Skills, and Datasets](../build/configure-agent-tools-skills-datasets.md)
- [Create an Agent and configure instructions and models](../build/create-agent-instructions-and-models.md)
- [Chat with an Agent and check results](../use/chat-with-agents.md)
- [Index of limits and availability conditions](../operations/limits-and-availability-index.md)

## Before you begin

- Prepare a test organization or Project where you can create and edit Datasets and Agents. If it is unavailable, check the [Index of limits and availability conditions](../operations/limits-and-availability-index.md).
- Use only test documents and keep sharing to the minimum necessary scope.
- For Dataset creation, Agent configuration, and conversation details, see the related articles above. Writing a document name in instructions alone does not connect a Dataset.

## Sample used in this exercise

Create `expense-reimbursement-faq.md` locally and save the following content.

```md
# Expense reimbursement FAQ

## Q. What should I submit when I travel to a client site by train?
A. Submit it as transportation expenses, including the route, date of use, and purpose of the visit.

## Q. By when should I submit receipts?
A. Submit them by the 5th of the month following the month in which the expense was incurred.

## Q. What should I note when submitting meal expenses?
A. Include attendees, purpose, and restaurant name, and attach the receipt.
```

| Item | Value used in this exercise |
|---|---|
| Dataset name | Expense reimbursement FAQ (test) |
| Agent name | Internal Helpdesk Agent (test) |
| Do not use | Real expense information, external connections, sending or update tools |

Use the following Agent instruction.

> Answer based on the expense reimbursement FAQ in the connected Dataset. Separate the answer from its basis. For questions without a basis in the document, do not speculate; say that confirmation is needed because the document does not contain the information. In this test, do not send externally, create requests, or update business data.

## Step 1. Import the FAQ into a Dataset

**Goal:** Prepare the document that the Agent will reference.

1. In the target organization or Project, open **"Data Integration"** and create a Dataset.
2. Enter `Expense reimbursement FAQ (test)` as the name. If sharing settings are displayed, choose the scope required for testing.
3. Add `expense-reimbursement-faq.md` from **"Upload Data."**
4. In the list, confirm that the target data status is **"Complete."**

For details, see [Prepare and reference a Dataset](../build/prepare-and-reference-datasets.md). **Checkpoint:** The correct file is in the target Dataset, and it does not show as processing or failed.

## Step 2. Create an Agent and select the Dataset

**Goal:** Prepare an Agent that answers based on the FAQ.

1. Use **"Create Agent"** to create `Internal Helpdesk Agent (test)` and enter the preceding instruction.
2. Select an available model and confirm its saved state.
3. Open the Agent's **"Data Integration"** and select only `Expense reimbursement FAQ (test)` in **"Select Dataset."**
4. After selecting **"Apply,"** reopen the Agent and confirm the Dataset name and saved state.

For Agent creation, see [Create an Agent and configure instructions and models](../build/create-agent-instructions-and-models.md); for Dataset selection, see [Configure Agent tools, Skills, and Datasets](../build/configure-agent-tools-skills-datasets.md). **Checkpoint:** Do not rely only on the instructions; the correct Dataset is selected.

## Step 3. Compare a question covered by the document

**Goal:** Confirm that the answer corresponds to the original FAQ.

1. Open a conversation with the Agent and send the following.

   > When I travel to a client site by train, what should I include in the request?

2. Compare the response with the original text to confirm that it mentions the route, date of use, purpose of the visit, and a transportation expense request.
3. If the response provides a basis or reference, confirm that it supports the actual FAQ content.

For conversation details, see [Chat with an Agent and check results](../use/chat-with-agents.md). **Checkpoint:** It does not add conditions that are absent from the FAQ.

## Step 4. Check a question outside the document

**Goal:** Confirm that it does not invent a policy without a basis.

1. Send the same Agent: "What is the maximum lodging expense for overseas business trips?"
2. Confirm that it can answer that confirmation is needed because the document does not contain the information.
3. Confirm that it does not add policies absent from the FAQ, such as amounts, limits, or approvers.

For testing criteria, see [Test and improve an Agent](../build/test-and-improve-agents.md). **Checkpoint:** Compare both in-document and out-of-document questions with the FAQ source text.

## Common misunderstandings

| Misunderstanding | What to check in this exercise |
|---|---|
| Connecting a Dataset reads the full FAQ every time | It is for finding relevant information. Use a different process for full-text review. |
| A display that appears to cite a basis means it is correct | Compare the answer and reference with the original text. |
| Instructions alone control document references | Also check Dataset selection, import status, and sharing scope. |

## If something does not work

| Situation | What to check |
|---|---|
| You cannot create or add a Dataset | Check the target, permissions, and on-screen guidance in [Prepare and reference a Dataset](../build/prepare-and-reference-datasets.md). |
| The Agent does not reference the FAQ | Check data status, the selected Dataset, **"Apply,"** and saved state; see [Agent does not use data](../troubleshooting/agent-not-using-data.md). |
| The response differs from the original text | Do not use the response for business decisions. Review the FAQ, question, instructions, and selected Dataset. |
| You cannot find the Agent or chat with it | Check the Agent name, Project, access permission, and enabled state in [Chat with an Agent and check results](../use/chat-with-agents.md). |

## Cleanup

1. In the Agent's **"Data Integration,"** deselect the Dataset and confirm **"Apply"** and the saved state.
2. Before deleting the Agent, Dataset, or source file, confirm that no other Agents or users use them. Deselecting it and deleting the Dataset or source file are separate actions.
3. This exercise does not create schedules, triggers, API keys, or public Pages. If you added them, confirm that they are stopped, revoked, or unpublished.

## Read next

- [Prepare and reference a Dataset](../build/prepare-and-reference-datasets.md)
- [Configure Agent tools, Skills, and Datasets](../build/configure-agent-tools-skills-datasets.md)
- [Test and improve an Agent](../build/test-and-improve-agents.md)
- [Agent does not use data](../troubleshooting/agent-not-using-data.md)
