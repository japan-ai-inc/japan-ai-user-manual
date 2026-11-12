# Publish a Workflow

To provide a Workflow to users, **publish** a tested draft. This article explains pre-publication checks, how to publish from the Workflow editing screen, and post-publication checks.

- How to test a draft: [Test a Workflow Draft](test-workflow-drafts.md)
- Run a published Workflow: [Run a Published Workflow](../use/run-published-workflow.md)

## What publishing means

Workflow edits have the following three states.

| State | Meaning | Impact on users |
|---|---|---|
| Edits on the screen | Content changed only in the editing screen | No impact |
| Draft (Save draft) | Configuration saved with "Save draft." It can be test-run | No impact on normal use |
| Published version | Configuration finalized with "Publish." It is used for user runs and triggers | Affects the next run |

When you publish, **the draft at that time becomes the published version**. There is one published version, and it is updated to the latest content each time you publish.

> **You cannot revert to a previous published version after publishing.** The confirmation screen also says, "You cannot revert to a previous version." Record the pre-publication configuration or consider duplication as needed in case you need to return to it.

### Publishing and visibility are separate settings

"Publish" determines which configuration is available for use. **Who can see it (visibility)** is a separate setting managed in Workflow basic settings and elsewhere.

- Publishing does not necessarily expand visibility automatically.
- Expanding visibility does not make an unpublished draft available to users.
- When sharing with everyone in the organization, content written in nodes, such as prompts, templates, and code, also becomes viewable. Do not write passwords or tokens in the body; use connected accounts.

## Before you begin

- Confirm that the target organization, Project, and Workflow are correct.
- Confirm that you have permission to edit and publish the Workflow. Permissions vary by organization and Project settings.
- Test representative inputs and branches using [Test a Workflow Draft](test-workflow-drafts.md).
- When updating a Workflow already in use, check the impact on users, triggers, and connection targets, and notify relevant parties.
- If it includes sending or updates to external services, check trigger conditions so it does not run unexpectedly after publication.

## 1. Check before publishing

Before publishing, check the following.

| Item to check | What to check |
|---|---|
| Test results | Checked results for representative inputs, branches, and errors |
| Checklist | No unconnected nodes or unset items remain |
| Start method | Start-node settings, such as manual or trigger, are as intended |
| Connections | Connections and authentication with required external services are valid |
| Output | Content and format returned to users are decided |
| Scope of impact | Decided whom to notify of the update and when to switch |

The checklist helps identify missing settings. Even if it identifies no issue, it does not guarantee business-correct results.

Depending on organization settings, missing node configuration can cause a publication error and show the target node and reason. Correct the displayed content, then publish again.

## 2. Publish a Workflow

1. Open the target Workflow in the editing screen.
2. Confirm that the status at the top of the screen is "Draft" or "Published."
3. If there are changes, save the draft with "Save draft."
4. Select "Publish."
5. Check the content shown in "Publish confirmation." For a Workflow shared across the organization, a notice about sharing scope also appears.
6. Select "Publish."
7. Confirm that "Published" appears.

When publication is finalized, the configuration on the screen is saved as a draft and then published. However, to prevent an unsaved change before confirmation, we recommend saving in step 3.

### If you cannot select "Publish"

| Display or status | Possible reason | Response |
|---|---|---|
| "No changes to publish" | The draft and published version have the same configuration | Change it, save the draft, then publish |
| "You do not have permission to edit this workflow" | You lack the permission required to publish | Check with an administrator or Project administrator |
| Cannot select it without a message | Permissions or publication status is loading | Wait a while, then reload the screen |

### If publishing fails

- If "Failed to publish" appears, the published version may not have been updated. Check the screen status and run results.
- If a node name and reason appear, correct that node, save the draft, and publish again.
- If a message about permissions appears, check required permissions with an administrator.

## 3. Check after publishing

1. Confirm that the status at the top of the screen is "Published."
2. Run it using the actual method of use, such as a user screen or trigger, and check the result. For confirmation methods, see [Run a Published Workflow](../use/run-published-workflow.md).
3. Notify relevant parties of the publication date and time, changes, and inquiry contact.

### For Workflows that use triggers

When using external-service triggers such as email, updating the published version and registration on the external service are done separately.

- If a notice such as "Published, but failed to configure the Outlook trigger." appears, publication itself is complete. Check the connection and publish again.
- If the connection is disconnected or disabled, the trigger may not operate after publication. Confirm that it starts with a test event after publishing.
- [Configure Workflow Triggers](configure-workflow-triggers.md) explains trigger configuration.

### Runs in progress and scheduled processing

Runs that start newly after publication use the updated published version. **Which version runs to completion for a run already started before publication or processing already accepted may vary depending on the configuration and execution method.** When updating a Workflow in use, choose a time with less processing and check run results after publication.

## 4. Update a published Workflow

1. Make changes in the editing screen.
2. Save the draft and test it.
3. Record the changes.
4. Run "Publish" again.

While you edit a published Workflow, users continue using the current published version. **Draft changes are not reflected in user runs until you publish them.**

> This article explains publishing from the Workflow editing screen. When publishing from another screen, such as for a Workflow used to create Tasks, display names and explanations of post-publication impact may differ. Follow the on-screen confirmation message.

## Checklist

- [ ] Saved the draft and tested representative inputs and branches
- [ ] Checked checklist findings
- [ ] Checked the difference between publication and visibility
- [ ] Checked the sharing scope and content written in nodes
- [ ] Checked the impact of updates on users and triggers
- [ ] Confirmed "Published" and the "Published" status
- [ ] Checked run results using the actual method of use
- [ ] Notified relevant parties of changes

## Next article to read

- [Run a Published Workflow](../use/run-published-workflow.md)
- [Configure Workflow Triggers](configure-workflow-triggers.md)
- [Debug with Workflow Run History](debug-workflow-run-history.md)
- [Workflow and Automation Specifications and Limits](workflow-and-automation-specs.md)
