# Index of Tips by Goal

This exercise is an index for selecting an article based on what you want to accomplish. Briefly organize your goal, target organization and Project, data you can handle, users, and expected result, then select one appropriate article. This is not an article that restates feature specifications or procedures. Check the linked procedures, permissions, and limits as the source of truth.

## Related articles

- [Choose features by goal](../getting-started/choose-features-by-goal.md)
- [Choose an automatic execution method](../getting-started/choose-execution-method.md)
- [How to read by role](../getting-started/reading-by-role.md)
- [Index of limits and availability conditions](../operations/limits-and-availability-index.md)
- [Collect information needed for support](../troubleshooting/collect-info-for-support.md)

## Before you begin

- Do not write real customer information, personal information, Secrets, or API keys in planning notes or support inquiries.
- Check conditions for the target organization and Project, and initially focus on one goal, user, and confirmed result.

## Sample used in this exercise

Use the fictional request, "I want answers to questions about HR policies to be based on test documents and to direct the user to check with the responsible person if there is no basis." Begin with "I want answers based on internal documents," then check document preparation, Agent configuration, and response testing in order. Do not add organization-wide publication, external connections, or automatic executions.

## Step 1. Express the goal in one sentence and select the first article

**Goal:** Begin with what the user wants to achieve, not with a method.

1. State in one sentence who will input what and which result they want to confirm.
2. Select one close goal in the following table and read the first link.
3. If the prerequisites do not fit after reading the article, return to a different goal. Do not infer permissions or features and proceed.

| Goal | Articles to read |
|---|---|
| Refine response format or judgment | [Create an Agent and configure instructions and models](../build/create-agent-instructions-and-models.md) → [Test and improve an Agent](../build/test-and-improve-agents.md) |
| Answer based on internal documents | [Prepare and reference a Dataset](../build/prepare-and-reference-datasets.md) → [Configure Agent tools, Skills, and Datasets](../build/configure-agent-tools-skills-datasets.md) |
| Perform routine work | [Configure Workflow nodes](../build/configure-workflow-nodes.md) → [Test Workflow drafts](../build/test-workflow-drafts.md) |
| Start based on a date, time, or event | [Choose an automatic execution method](../getting-started/choose-execution-method.md) → [Configure Workflow triggers](../build/configure-workflow-triggers.md) or [Configure Task Scheduler](../build/configure-task-scheduler.md) |
| Create a Page | [Ask AI to create or modify Pages](../build/ask-ai-to-build-pages.md) → [Register and publish Pages from the screen](../build/register-and-publish-pages.md) |
| Store or update Objects | [Design an Object structure](../build/design-object-structure.md) → [View and update Object data](../use/view-and-update-object-records.md) |
| Integrate code or external systems | [Choose Web UI, AI assistance, SDK, CLI, or API](../development/choose-dev-path.md) → [Execution modes, authentication, and support scope](../development/execution-modes-and-auth.md) |
| Use or distribute an App | [Choose an App in Marketplace](../use/choose-marketplace-app.md) → [Complete App setup](../use/complete-app-setup.md) |

For selecting goals and features, see [Choose features by goal](../getting-started/choose-features-by-goal.md). **Checkpoint:** The first article you selected matches the one-sentence description of the user, input, and result.

## Step 2. Check the required conditions separately

**Goal:** Check features, data, access, and operations without mixing them.

1. Identify the target organization and Project and the creator, user, and administrator.
2. Separately write down data used for input, data referenced, and output or update destinations.
3. Determine separately whether sharing, external connections, Secrets, API keys, public URLs, or automatic executions are needed.
4. If something is unavailable or not displayed, check permissions, availability conditions, and configuration status. Do not work around this with another person's credentials or broad permissions.

For reading order by role, see [How to read by role](../getting-started/reading-by-role.md); for permissions, see [Understand organization, Project, and App permissions](../operations/understand-org-project-app-permissions.md); for where to check conditions and limits, see [Index of limits and availability conditions](../operations/limits-and-availability-index.md). **Checkpoint:** You can separately explain what you want to create and the required permissions, sharing, authentication, and automatic execution.

## Step 3. Test on a small scale and select the next article from the result

**Goal:** Confirm the first representative operation before expanding scope.

1. In a test Project and with fictional data, perform one minimum step from the article that corresponds to your goal.
2. Record the expected result, actual result, and unconfirmed items. Do not use a success display alone to judge business correctness or whether other users can use it.
3. Use the following table to select a detailed article based on the result.

| What you want to check | Article to read |
|---|---|
| The Agent does not reference documents | [Agent does not use data](../troubleshooting/agent-not-using-data.md) |
| Workflow input or point of failure | [Debug with Workflow run history](../build/debug-workflow-run-history.md) |
| Page display or operations | [Work with Pages](../use/work-with-pages.md) |
| Connection or owner change | [Handle expired connections and owner changes](../operations/handle-expired-connections-and-owner-changes.md) |
| API or SDK error | [Isolate SDK/API errors](../troubleshooting/isolate-sdk-api-errors.md) |
| Setup after App installation | [Complete App setup](../use/complete-app-setup.md) |

For designing small tests, see [Choose features by goal](../getting-started/choose-features-by-goal.md); for records before contacting support, see [Collect information needed for support](../troubleshooting/collect-info-for-support.md). **Checkpoint:** Select the next article based on the actual result and unconfirmed items, not the goal alone.

## Checklist

- [ ] Put the user, input, and expected result into one sentence
- [ ] Selected one first article appropriate to the goal
- [ ] Checked the target organization and Project, data, sharing scope, and required roles
- [ ] Separately checked whether external connections, Secrets, API keys, publication, and automatic execution are needed
- [ ] Checked a small operation and its result in a test environment
- [ ] Recorded unconfirmed items and the next article to read

## Common misunderstandings

| Misunderstanding | What to check |
|---|---|
| Search or reference results alone can check every document | Document retrieval and full-text review are different goals. Select the needed method in [Choose features by goal](../getting-started/choose-features-by-goal.md). |
| Reading an article means you can use the feature | Availability varies by organization, Project, role, availability conditions, and settings. |
| It is faster to configure publication or automatic execution first | First test a manual representative operation, then add it after checking impact scope and how to stop it. |
| If an administrator succeeds, every user can use it | Separately check user access, personal settings, and permissions for target data. |

## If something does not work

| Situation | First thing to check |
|---|---|
| You cannot decide which article to begin with | Return to the one sentence describing the user, input, and result, and select from [Choose features by goal](../getting-started/choose-features-by-goal.md). |
| A feature or creation screen is not displayed | Check the target organization and Project, participation status, role, and availability conditions in [Not visible or cannot join](../troubleshooting/not-visible-cannot-join.md). |
| You see a permission or authentication error | Do not use someone else's credentials. Check the target, required operation, and displayed content in [Permission and authentication errors](../troubleshooting/permission-and-auth-errors.md). |
| You need support | Organize the target, date and time, reproduction steps, displayed content, and operations already checked according to [Collect information needed for support](../troubleshooting/collect-info-for-support.md). Do not send Secret values. |

## Cleanup

1. Check fictional data, URLs, IDs, and records for screen sharing included in planning notes, and stop unnecessary sharing.
2. If you actually created an Agent, Dataset, Object, Page, Workflow, or App in Step 3, follow cleanup in each detailed article.
3. If you added connections, Secrets, API keys, triggers, schedules, or public URLs, check active processes and responsible people before stopping, revoking, or unpublishing them.
4. If you retain an exercise target, record that it is for practice, its owner, and where to check next.

For deletion and handover decisions, see [Check deletion, handover, and audit logs](../operations/deletion-handover-and-audit-logs.md).

## Read next

- [Choose features by goal](../getting-started/choose-features-by-goal.md)
- [Choose an automatic execution method](../getting-started/choose-execution-method.md)
- [How to read by role](../getting-started/reading-by-role.md)
- [Index of limits and availability conditions](../operations/limits-and-availability-index.md)
- [Collect information needed for support](../troubleshooting/collect-info-for-support.md)
