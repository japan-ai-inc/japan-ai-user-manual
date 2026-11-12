# Configure Workflow Triggers

When you configure a **trigger**, you can automatically start a published Workflow based on an event, such as receiving email or adding or updating data. This article explains adding triggers, main settings by type, testing, and post-publication checks.

- Choosing an automatic execution method: [Choose an Automatic Execution Method](../getting-started/choose-execution-method.md)
- Publication procedure: [Publish a Workflow](publish-workflow.md)

## What is a trigger?

A Workflow has the following three main start methods.

| Start method | Trigger | Article to configure it |
|---|---|---|
| Manual | A user runs it from the screen | [Run a Published Workflow](../use/run-published-workflow.md) |
| Trigger | An event such as email receipt or data change | This article |
| Schedule | A specified date, time, or interval | [Configure the Task Scheduler](configure-task-scheduler.md) |

Add triggers as **Start nodes** in a Workflow. You may be able to use both a manual Start node and a trigger Start node in one Workflow.

> **Triggers become active when you publish.** They do not start when events occur in draft status. The settings screen displays "Publish the workflow to enable the trigger."

## Before you begin

- Confirm that you have permission to edit and publish the target Workflow.
- Decide specifically which event will start it: which email, which data, and what change.
- When using external services such as Gmail or Outlook, decide the account to use for the connection.
- When post-start processing includes external sending or updates, decide filtering conditions in advance to prevent unintended large-scale execution.

## 1. Add a trigger Start node

1. Open the target Workflow in the editing screen.
2. Select "Add node" (the + icon) in the operation panel at the bottom of the screen.
3. From the "Trigger" tab in the displayed menu, select the type to use.
4. A Start node is added to the canvas. Then connect processing nodes after it. For node connection methods, see [Configure Workflow Nodes](configure-workflow-nodes.md).

> **If "Add node" is not displayed:** Adding triggers may be unavailable depending on organization feature settings. Check with the organization administrator.

> **You cannot change the trigger type after creation.** To use another type, add a new Start node and delete the unnecessary Start node.

### Trigger types you can select

Displayed types vary with organization feature settings and the Workflow location.

| Name on the screen | Start event | Notes |
|---|---|---|
| Manual trigger | A user runs it from the screen | You can configure input fields |
| Webhook trigger | An HTTP request from an external system | Displayed depending on feature settings |
| Custom Object trigger | Object record creation, update, or deletion | Only for Workflows in a Project. Displayed depending on feature settings |
| Storage trigger | A change to a Storage file or folder | Only for Workflows in a Project. Displayed depending on feature settings |
| Dataset trigger | Dataset item addition, update, or deletion | Only for Workflows in a Project. Displayed depending on feature settings |
| Gmail: New Email | Receipt of email in Gmail | Requires a Google account connection |
| Outlook: New Email | Receipt of email in Outlook | Requires a Microsoft account connection |
| Email by JAI: New Email | Email forwarding to the assigned receiving address | You must configure forwarding in the email client |
| Speech: Transcription completed | Completion of meeting transcription | Displayed depending on feature settings |

## 2. Configure settings for each type

When you select a Start node, its settings area appears on the right. Main settings for each type are as follows.

| Type | Main settings | Determined after publication |
|---|---|---|
| Manual | Input Fields | — |
| Webhook | HTTP method, content type, parameters to receive (query, header, and body), Signature Verification, Custom Response, and Challenge API Config as needed | Webhook URL |
| Custom Object | Target Object, trigger type (creation, update, deletion, and conditional types for each), condition builder, and Monitored Fields | — |
| Storage | Target Folder, whether to include subfolders, event type, and filters (file name, extension, and file size) | — |
| Dataset | Target Dataset, trigger action (item addition, update, or deletion), and data source type. If selecting Speech, transcription text or summary | — |
| Gmail / Outlook | Account connection and filters (subject, sender, recipient, presence of attachments, and so on) | Start of email receipt |
| Email by JAI | Forwarding source email address | Receiving address |
| Speech | Meeting filters (title, meeting type, tags, and participants) | Account subject to start |

### Configure Webhook signature verification and responses

For a Webhook, select the HTTP method and content type, then define parameters to extract from query, headers, and body. Body settings are not displayed for GET. Check values used by following nodes in the Start node's "Output Variables."

When you need to verify the sender, select GitHub, Slack, or "Custom" in "Signature Verification," and configure the same Webhook Secret as the sender. When you select GitHub or Slack, the signature header is set automatically. Do not copy secrets or signature headers into node descriptions, prompts, or shared run-history materials.

In "Custom Response," you can configure the response type, status code, and response body. Because the response is returned before Workflow processing, you can insert only variables received by the Webhook into the body. As needed, also configure "Challenge API Config," which responds to a test request without running the Workflow. GitHub and Slack verification handshakes are processed automatically.

### Filter by conditions

You can narrow start conditions for many types.

- If you do not configure conditions, the Workflow may start for every target event. For example, if you do not select "Monitored Fields" in a Custom Object trigger, it starts for changes to every field.
- How conditions are combined (And/or and group handling) varies by type. Check the explanation on the screen.
- A "Contains" condition may match unexpected values. Check both representative examples and examples you want to exclude.

### Connect an external account (Gmail and Outlook)

1. In "Google Connection" or "Microsoft connection" in the settings area, select "Connect" and authenticate the account.
2. Confirm that the status is "Enabled." If it is "Needs re-authentication," select "Re-authenticate."
3. When you publish the Workflow, email receipt begins. Confirm that the status displays "Email listening."

> Email content appears in run history. When highly confidential email is targeted, check filters and the scope of people who can view run history.

### Use the Email by JAI receiving address

- You can add only one Email by JAI Start node to a Workflow.
- The receiving address is assigned after publication. Configure the assigned address as the forwarding destination in the email client.
- Configure sorting, meaning which email to forward, in the email client.
- If you select "Rotate address," the current address becomes invalid. You must reconfigure forwarding rules.

### Account subject to Speech triggers

It starts when transcription of a meeting of the user who published it is complete. If the person responsible for publishing changes, the meetings targeted may also change.

## 3. Use output variables in following nodes

A trigger Start node passes event information to following nodes as **Output Variables** (for example, email subject and body, record data, and file information).

- You can check available output variables in "Output Variables" in the Start node's settings area.
- For how to reference variables in following nodes, see [Configure Workflow Variables and Branches](configure-workflow-variables-and-branches.md).
- When one Workflow has multiple Start nodes, available variables vary depending on which Start node initiated it. Check each route with branches and tests.

## 4. Test and publish

1. Save the draft with "Save draft."
2. In test execution on the editing screen, select the trigger Start node and run it.
   - For some types, a field to enter test values appears (for example, Gmail and Outlook use email data, and Webhook uses configured parameters).
   - For types such as Storage and Email by JAI, no input field appears and "No input parameters required" is displayed. In that case, run it with "Start Execution."
3. Check results and steps. For test methods, see [Test a Workflow Draft](test-workflow-drafts.md).
4. Publish it by following [Publish a Workflow](publish-workflow.md).

> **A test run does not confirm that it starts from an actual event.** In a test, processing runs on entered values, or empty values. Check whether external-service connections and filtering conditions work correctly after publication.

## 5. Check with an actual event after publication

1. Confirm that the Start node display is in an active state, such as published or listening.
2. Generate a low-impact test event, such as sending email with a test subject or creating a test record.
3. In the run log, confirm that it started and check processing results. For how to check, see [Debug with Workflow Run History](debug-workflow-run-history.md).
4. If it does not start, check the following:
   - Whether the Workflow is published
   - Whether the external-account connection is "Enabled"
   - Whether filtering conditions are too strict
   - Whether the Webhook URL or receiving address is correctly configured at the integration source

> The time from an event occurring to a Workflow starting, and whether it starts exactly once for every event, are not guaranteed. Design following processing so that duplicate starts do not cause problems.

## Stop or change triggers

- Even when you change trigger settings, they are not reflected in the running trigger **until you publish**.
- Merely deleting a Start node in a draft does not stop the trigger in the published version. [Change, Stop, and Hand Over Scheduled Runs](../operations/change-stop-handover-scheduled-runs.md) covers the official procedures and impact of stopping and changing. If you need to stop urgently, check with the Workflow administrator.
- If an external-account connection expires, a trigger may stop working. Check the status display regularly.

## Checklist

- [ ] Decided the start event and filtering conditions
- [ ] Selected a trigger type and added a Start node
- [ ] Completed settings for the type and external-account connection
- [ ] Referenced Output Variables in following nodes
- [ ] Checked the processing flow in a test run
- [ ] Published the Workflow
- [ ] Confirmed its start and results with an actual event after publication
- [ ] Checked the impact of duplicate starts and large-scale execution

## Next articles

- [Debug with Workflow Run History](debug-workflow-run-history.md)
- [Configure the Task Scheduler](configure-task-scheduler.md)
- [Change, Stop, and Hand Over Scheduled Runs](../operations/change-stop-handover-scheduled-runs.md)
- [Workflow and Automation Specifications and Limits](workflow-and-automation-specs.md)
