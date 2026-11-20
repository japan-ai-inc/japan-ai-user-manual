# A Workflow Does Not Start or Fails

This page provides steps to isolate when the Workflow cannot be run manually, does not start for an event or on a schedule configured in Project Settings > Task Scheduler, or fails after starting. Target users are creators and operators. First, distinguish between testing drafts, manual execution of published Workflows, triggers, and schedules configured in Task Scheduler. Changes to the draft will not be reflected in regular usage, triggers, or scheduled executions until you publish it.

## Related articles

- [Test Workflow Draft](../build/test-workflow-drafts.md)
- [Publish Workflow](../build/publish-workflow.md)
- [Set Workflow trigger](../build/configure-workflow-triggers.md)
- [Debug with Workflow execution history](../build/debug-workflow-run-history.md)
- [Configure task scheduler](../build/configure-task-scheduler.md)
- [Respond to connection expiry/change of person in charge](../operations/handle-expired-connections-and-owner-changes.md)
- [Change/stop/inherit periodic execution](../operations/change-stop-handover-scheduled-runs.md)
- [Set execution result notification](../operations/configure-run-result-notifications.md)
- [Collect information necessary for inquiries](collect-info-for-support.md)

## Key points: who automated runs run as

| Start method | Run identity | External connections |
|---|---|---|
| Workflow trigger | The person who first published that start node (unchanged if someone else republishes) | Project shared connections only |
| Task Scheduler task | The task's creator (unchanged by editing) | Project shared connections only |
| Scheduled run created with the SDK/API | The creator of the Project API key | Project shared connections only |

- Workflows do not use personal connections. Automated runs require a shared connection.
- Triggers and tasks are automatically disabled when their owner leaves the organization.
- In the run history, trigger and scheduled runs show the owner's name as the runner. Runs are shown as automatic execution only when run by a service account.
- There is no pause feature. To stop urgently, turn off the Workflow's enable switch. When it is off, Email by JAI and storage triggers are treated as deleted, and a recreated Webhook trigger gets a new URL.

> A Webhook task in Task Scheduler has no authentication; anyone who knows the URL can start it. It is a different mechanism from the Webhook trigger, which verifies signatures. Do not share the URL, and stop the task if unexpected runs occur.

## Before you start

- Identify which method to start with. The confirmation location differs for tests on the editing screen, manual execution by users, event triggers, and schedules managed in Project Settings > Task Scheduler.
- Record the target organization/Project/Workflow name, publication status, start node, date and time of occurrence, executor or event.
- Workflows that send emails, create/update objects, and update external services may have already been processed halfway even when they fail. Do not repeatedly "start execution" or re-execute without knowing the cause.
- Tests should be performed with configurations that do not require external updates as much as possible, or with permitted test connection destinations and data.

> Not receiving notifications is not the same as a Workflow not starting. First, check Task Scheduler History or Workflow Execution History in the Project, depending on how the run was started, and then separate the execution, results, and notifications.

## Check first

- [ ] Checked whether the target was a draft or "published" and how to start.
- [ ] "Temporarily save" the modified draft and "publish" it if necessary.
- [ ] Checked the start node, required inputs, variable references, and unconnected/unset nodes.
- [ ] For triggers, check the event conditions, connection, and publication status; for schedules, check the enabled status and next execution in Project Settings > Task Scheduler.
- [ ] Checked the status, result, step, and first node of the target execution in the execution history.
- [ ] Confirmed the impact of external transmission and record duplication due to re-execution

## Isolation by symptoms

| Symptoms/Displayed Messages | Main Causes | Checks/Treatments | Detailed Articles |
|---|---|---|---|
| "This workflow cannot be executed because it is disabled" | The Workflow's enable switch is off, or the target is confused | Check the target Workflow's enable switch; it cannot run while off. Through the API/SDK, Workflows that are not enabled do not appear in the list and return 403 when run. Simply reorganizing the draft will not change the published version. After testing the necessary modifications, "Publish" and check "Published" and the actual execution results. | [Publish Workflow](../build/publish-workflow.md) |
| Does not start with a trigger/"Please publish the workflow to enable the trigger" | The start node/event condition is inappropriate as it is in the draft, and the linkage has expired. For triggers such as Gmail/Outlook, it is also necessary that the Workflow has been published. | Publish the target Workflow and check the start node type, target, filtering conditions, and connection with external services. Executing a test alone will not confirm activation in a real event. | [Set Workflow trigger](../build/configure-workflow-triggers.md) |
| "Input required" or "Unable to execute the workflow. Please correct the following:" | Required input is empty, node setting/variable reference/connection is incorrect | Check the input on the execution screen, the start node, the displayed node name and reason. Correct any indications such as "input variable is required" or "data set is required" at the relevant node and save it temporarily. | [Test Workflow draft](../build/test-workflow-drafts.md) |
| "Execution failed" | Failed in node execution, input value, variable, external service, or connection | Open "Results" and "Steps" for the target execution and follow the inputs and outputs in order from the start node. Targets correction not only for nodes in error, but also for nodes whose values ​​were initially different from expected. | [Debug with Workflow execution history](../build/debug-workflow-run-history.md) |
| Execution is slow or "timed out" | External services, processing volume, node settings/wait, connection destination response | Check the elapsed time and failed steps in the execution history, and do not start the same execution continuously. Check the node input, number of targets, and restrictions on the external service side, and divide the process or review the design if necessary. | [Debug with Workflow execution history](../build/debug-workflow-run-history.md) / [Workflow/Automatic execution specifications/limitations](../build/workflow-and-automation-specs.md) |
| Error when receiving Gmail/Outlook triggered email | Expired connection, different account, failed linkage settings after publication | Check the connection status, authentication account, permissions on the external service side, and publication status in order. Re-authentication is performed after checking the existing connection account. | [Respond to connection expiration and change of person in charge](../operations/handle-expired-connections-and-owner-changes.md) |
| The schedule does not run | The Task Scheduler schedule is disabled, or the next execution/end date is inappropriate | In Project Settings > Task Scheduler, check that the target schedule's status, "Next Run," and end date are as intended. Times on this screen are set in Japan Standard Time (JST). | [Set the task scheduler](../build/configure-task-scheduler.md) / [Change, stop, or take over periodic execution](../operations/change-stop-handover-scheduled-runs.md) |
| Triggers or schedules do not run after a change of person in charge | Triggers run as the first publisher and tasks as their creator. They are automatically disabled when the owner leaves the organization. | Republishing or editing by a successor does not change the run identity. Delete and re-add the trigger node (the Webhook URL and Email by JAI address change), or recreate the task. Then check the shared connection and the first run. | [Change/stop/take over periodic execution](../operations/change-stop-handover-scheduled-runs.md) |
| A node for an external service fails even though the personal connection is authenticated | Workflows use only shared connections | Configure or re-authenticate the Project's shared connection. Re-authenticating a personal connection does not fix it. | [Respond to connection expiration and change of person in charge](../operations/handle-expired-connections-and-owner-changes.md) |
| A scheduled Workflow fails because the model has been retired | The model specified in a node is no longer provided | Change the node's model to one that is available, test, and publish. | [Debug with Workflow execution history](../build/debug-workflow-run-history.md) |
| A task cannot be created | Tasks cannot be created on the free plan, and the number of tasks per organization is limited | Ask an administrator about the plan and the number of tasks in the organization, and remove unneeded tasks. | [Configure the Task Scheduler](../build/configure-task-scheduler.md) |
| The runner shown in the run history is unexpected | Trigger and scheduled runs show the owner's name; automatic execution is shown only for service-account runs | Check the run identity in the key-points table above. | [Change, Stop, and Hand Over Scheduled Runs](../operations/change-stop-handover-scheduled-runs.md) |
| Execution is successful but notification is not received | Notification destination settings, recipient authority, webhook/token expiration | First check the execution results of the Workflow, then check the notification destination in Project Settings > Task Scheduler, the destination, and the notification destination service side. Notification center notifications are sent to all active Project members on every trigger run and cannot be turned off. Do not paste the destination URL or token into the inquiry. | [Set execution result notification](../operations/configure-run-result-notifications.md) |

## 1. Check the boot paths separately

1. If you tried from the editing screen, check the "execution history" of the draft. This is for testing before publication.
2. If a user/trigger starts a Workflow after publishing, check the target Workflow's execution history in the Project's Workflows screen. Test runs on the edit screen are not displayed here.
3. For event triggers, check that the Workflow is 'published' and that the event conditions and connections on the start node are correct. We will confirm once at the actual event after publication.
4. For regular execution, open the target schedule in Project Settings > Task Scheduler and check its status, "Next Run," and end date. Check Task Scheduler History first and then the Workflow result when available. Even if you edit a Workflow, only the published version will be executed.
5. For triggers and scheduled runs, check that the run identity (first publisher or task creator) is still in the organization and that the Project's shared connection is configured.

## 2. Check input and settings before publishing

If this fails, record the node name and reason for the message that appears, and check the following steps:

1. Check the required items for manual input and the input format such as URL, JSON, and numerical values.
2. Verify that the start node matches your expected start method.
3. Check the "Checklist" for unconnected nodes, incorrect settings, and required prompts, models, variables, Datasets, and tools.
4. Check that the referenced variable does not point to an output that has been deleted or renamed, and that the type matches the value for each branch.
5. After making corrections, "temporarily save" and test typical inputs and inputs that result in errors. To check a single node, use the single node test. To reflect it in normal use or automatic startup, "publish" it after testing.

## 3. Check the execution history for failed nodes

Check the status, error, and elapsed time under "Results" in the execution details, and view the inputs and outputs of each node in order from the start node under "Steps." Empty input, unexpected branches, mismatched output formats, and connection/authority/destination errors are identified. Success is not a guarantee that the business results are correct, so please also check the destination, update destination, and final output.
"Rerun" is shown for failed runs linked to a task. A rerun uses the saved inputs. If writing to Object, etc. was completed before the failure, there is a possibility that it will be duplicated when re-executed. Check the cause, existing records, and external sending status before executing. If manual execution fails, check the input from the normal execution screen and try again.

> Even if the connection appears to be "authenticated", it will fail if the permissions to the target folder, destination, record, or restrictions on the external service side change. Don't assume that the connection is the only cause, first check the failed nodes in the execution history and the input/output.

## Before consulting with administrator/support

Except for confidential information, email body, tokens, webhook URLs, and personal information, we record the following: Workflow name and organization/Project, start method, publication status, date and time of occurrence, execution ID (`workflowRunId`, shown in the URL when you open the run details) or schedule name, the `requestId` in the response body if the API/SDK failed, status, display message, failure node, shareable summary of inputs and outputs, connection status, external processing that has been executed, whether or not it will be re-executed, and confirmed actions. When saving execution data in JSON, it includes input and output, so limit where it can be saved and who it can be shared with. For more information, please refer to [Collect information required for inquiries](collect-info-for-support.md).

## Read next article

- [Debug with Workflow execution history](../build/debug-workflow-run-history.md)
- [Set Workflow trigger](../build/configure-workflow-triggers.md)
- [Configure task scheduler](../build/configure-task-scheduler.md)
- [Respond to connection expiry/change of person in charge](../operations/handle-expired-connections-and-owner-changes.md)
- [Change/stop/inherit periodic execution](../operations/change-stop-handover-scheduled-runs.md)
- [Collect information necessary for inquiries](collect-info-for-support.md)
