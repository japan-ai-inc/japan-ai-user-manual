# Running a Workshop

This exercise is an example of how an instructor can safely run JAPAN AI Studio learning materials. Learners use test Projects and fictional data so that they can independently create, run, check, and clean up. The instructor does not finish operations first or work around insufficient permissions with broad permissions or someone else's credentials.

## Related articles

- [Your First Project and Agent](first-project-and-agent.md)
- [An Internal Helpdesk Agent](internal-helpdesk-agent.md)
- [Automate Routine Work](automate-routine-work.md)
- [A Task Management App](task-management-app.md)
- [An App That Integrates an External Service](external-service-integration-app.md)
- [Develop a Page with the SDK and CLI](develop-page-with-sdk-cli.md)
- [Deploy an App in Your Organization](deploy-app-in-organization.md)
- [Understand organization, Project, and App permissions](../operations/understand-org-project-app-permissions.md)
- [Index of limits and availability conditions](../operations/limits-and-availability-index.md)

## Before you begin

- Decide which organization and Project the instructor, assistants, and learners will open, and distinguish production from test environments in their names and descriptions.
- Do not put real customer information, personal information, business data, passwords, API keys, or Secret values in materials, projections, chats, or screen shares.
- Check the organization and Project roles of instructors and assistants and learners' participation status. For permission layers, see [Understand organization, Project, and App permissions](../operations/understand-org-project-app-permissions.md); for member operations, see [Manage Project and App members](../operations/manage-project-and-app-members.md).
- Check features available, run-related costs, and conditions for each organization in the screen on the day and in the [Index of limits and availability conditions](../operations/limits-and-availability-index.md). Do not reuse conditions from other materials or organizations.
- For materials that use API keys, external connections, Secrets, automatic executions, or public URLs, determine the owner and person responsible for stopping or revoking them in advance.

## Sample used in this exercise

Distribute only fictional topics such as the following. If learners replace them with business content, instruct them not to use real data.

| Material | What to create | Fictional data used | Goal |
|---|---|---|---|
| [Your First Project and Agent](first-project-and-agent.md) | An Agent that organizes meeting notes | Fictional meeting notes | Check a response that does not add information absent from the original text |
| [An Internal Helpdesk Agent](internal-helpdesk-agent.md) | An Agent that references an FAQ | Fictional expense reimbursement FAQ | Compare questions inside and outside the document |
| [Automate Routine Work](automate-routine-work.md) | A Workflow that organizes daily reports | Fictional daily reports | Check draft and published results |
| [A Task Management App](task-management-app.md) | Task management Object, Page, and Agent | Fictional tasks | Compare updated values and responses with records |
| [An App That Integrates an External Service](external-service-integration-app.md) | A Page that integrates an external service | Fictional daily-report summary | Send one item only to the test destination |
| [Develop a Page with the SDK and CLI](develop-page-with-sdk-cli.md) | An SDK/CLI Page | Fictional announcements | Check the deliverable and Page display |
| [Deploy an App in Your Organization](deploy-app-in-organization.md) | An App for in-organization installation | Practice data from [A Task Management App](task-management-app.md) | Check it as a user in the installation destination |

## Step 1. Decide the main material and goal

**Goal:** Select one material that fits learners' roles, permissions, and data they can handle.

1. For first-time learners, consider [Your First Project and Agent](first-project-and-agent.md); for document references, [An Internal Helpdesk Agent](internal-helpdesk-agent.md); for Workflows, [Automate Routine Work](automate-routine-work.md); and for an Object, Page, and Agent combination, [A Task Management App](task-management-app.md).
2. When covering external services, the SDK/CLI, or App installation, first read the prerequisites and cleanup for [An App That Integrates an External Service](external-service-integration-app.md), [Develop a Page with the SDK and CLI](develop-page-with-sdk-cli.md), and [Deploy an App in Your Organization](deploy-app-in-organization.md).
3. For each learner, record in the instructor notes what they will create, inputs, checkpoints, and the person responsible for cleanup.
4. Even when continuing with multiple materials, check the status of publications, automatic executions, and credentials created in the preceding material before moving on.

For details and checkpoints of each material, see [Your First Project and Agent](first-project-and-agent.md) through [Deploy an App in Your Organization](deploy-app-in-organization.md). **Checkpoint:** Select materials based not on an "interesting feature," but on a goal the learner can check and a safe test scope.

## Step 2. Check the environment for the day

**Goal:** Ensure that learners can operate the correct test environment with the minimum necessary scope.

1. Before starting, confirm that everyone has opened the correct organization and test Project.
2. The instructor checks required roles but does not grant learners unnecessary administrative permissions.
3. If a feature is not displayed, check the target, participation status, role, and availability conditions. Do not change settings on your own judgment just to display it.
4. Only for materials that use API keys, check purpose, target Project, custodian, and person responsible for revocation. Do not put actual values in screens or handouts.

For permissions, see [Understand organization, Project, and App permissions](../operations/understand-org-project-app-permissions.md); for participation status, see [Manage Project and App members](../operations/manage-project-and-app-members.md); for key handling, see [Manage Project API keys](../operations/manage-project-api-keys.md). **Checkpoint:** Everyone has opened a test Project where they are allowed to operate, not a production environment.

## Step 3. Have learners complete the steps and checkpoints

**Goal:** Learners compare inputs, settings, and results themselves rather than merely copying the instructor's screen.

1. The instructor explains Step 1 of the main material, and learners perform it in their own test Projects.
2. At the end of each step, compare the target name, input values, saved or published state, and result with the material's checkpoints.
3. If results differ from expectations, learners record the target on screen, input, permissions, and displayed messages. The instructor does not have them run the same operation repeatedly.
4. For Agent, Workflow, Page, and other run results, compare fictional input and expected results rather than checking only that the screen reports success.

The BUILD/USE/OPS articles linked by the main material are the source of truth for operating details. For example, see [Chat with an Agent and check results](../use/chat-with-agents.md) for conversations, [Run a published Workflow](../use/run-published-workflow.md) for published Workflow checks, and [Work with Pages](../use/work-with-pages.md) for Page use. **Checkpoint:** The goal is not to match the instructor's result, but for learners to explain their own targets and results.

## Step 4. Check sharing, automatic execution, and external impact

**Goal:** Understand impacts added in the exercise before it ends.

1. If you shared or published a Page, Agent, chat, or App, check the target, users, URL, and publication scope.
2. If you added Workflow triggers or schedules, check the target, next run, notifications, and person responsible for stopping them.
3. If you used external connections, Secrets, or API keys, check which Project and process use them; do not record or share the values themselves.
4. To avoid leaving publications, automatic executions, or connections that are unnecessary for the exercise, decide the cleanup owner and completion criteria.

For stopping and handover, see [Change, stop, or hand over scheduled runs](../operations/change-stop-handover-scheduled-runs.md); for connections, see [Handle expired connections and owner changes](../operations/handle-expired-connections-and-owner-changes.md); for deletion decisions, see [Check deletion, handover, and audit logs](../operations/deletion-handover-and-audit-logs.md). **Checkpoint:** It is decided who stops and checks what, and when.

## Checklist

- [ ] Decided the main material, learners, goal, and cleanup owner
- [ ] Confirmed that the organization, Project, and learner sign-in destinations are for testing
- [ ] Prepared only fictional data and did not share secret values or real data
- [ ] Each learner checked the material's checkpoints on their own screen
- [ ] Checked whether sharing, publication, external connections, API keys, and automatic executions exist and who owns them
- [ ] Recorded incomplete items, targets, and where to check next

## If something does not work

| Situation | Instructor's first response |
|---|---|
| You cannot find the organization, Project, or creation screen | Check the sign-in destination, Project participation, role, and availability conditions. See [Not visible or cannot join](../troubleshooting/not-visible-cannot-join.md). |
| Options or results differ for each learner | Separately check the Project, selected target, sharing scope, role, and organization settings. Before screen sharing, check that there is no confidential information. |
| The Agent does not reference documents | Check Dataset status, selection, question, and instructions in [An Internal Helpdesk Agent](internal-helpdesk-agent.md) and [Agent does not use data](../troubleshooting/agent-not-using-data.md). |
| An API key or external connection fails | Do not share actual values; check the target, expiration, minimum permissions, and displayed message. See [Isolate SDK/API errors](../troubleshooting/isolate-sdk-api-errors.md). |

## Cleanup

1. For each learner, list the Projects, Agents, Datasets, Objects, Pages, Workflows, and Apps they created.
2. Confirm that schedules and triggers are stopped and that no next run or published automatic launch remains.
3. For published or shared targets, check users and scope; unpublish or delete them if unnecessary.
4. Before revoking, deleting, or handing over API keys, Secrets, and connections, confirm that no active process uses them and identify the responsible person.
5. If retaining a practice Project, keep wording in its name and description that identifies it as practice. If it is unnecessary, clean it up after confirming it is not referenced elsewhere.

## Read next

- [Your First Project and Agent](first-project-and-agent.md)
- [Automate Routine Work](automate-routine-work.md)
- [A Task Management App](task-management-app.md)
- [Understand organization, Project, and App permissions](../operations/understand-org-project-app-permissions.md)
- [Index of limits and availability conditions](../operations/limits-and-availability-index.md)
