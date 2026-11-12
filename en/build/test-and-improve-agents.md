# Test and Improve an Agent

Even if Agent settings can be saved, the Agent may not perform the expected work. This article describes how to **verify behavior with small inputs, record results, and improve settings one at a time**.

For Agent creation, instructions, and models, see [Create an Agent and configure instructions and models](create-agent-instructions-and-models.md). For tools, Skills, and Datasets, see [Configure tools, Skills, and Datasets for an Agent](configure-agent-tools-skills-datasets.md).

## Before you begin

- Check the organization, Project, and Agent to test, and the scopes you can use and edit.
- Check the Agent's current instructions, model, plugins, data integrations, and usage status.
- Check the effect on existing users and operations. Instead of experimenting on an Agent in use, prepare a test Agent with the responsible person as needed.
- Prepare test data that does not include confidential information or unnecessary personal information.
- Decide the expected result and operations that must not be performed in advance.

> **Do not treat preview as a simulated run that does not affect external services.** If connected tools may send, update, or delete, check the test target and permissions. Merely instructing it "This is a test" or "Do not send" cannot limit operation permissions. Do not treat it as free or as not consuming credits; check the terms of use.

Agent settings have automatic saving. Do not use closing the editing screen or switching to preview as a way to cancel setting changes.

## 1. Decide the acceptance criteria first

You cannot compare before and after improvements by only asking whether the answer was good. Decide what to check for each input.

| What to check | Example acceptance criteria |
|---|---|
| Content | Correctly organizes the facts entered and does not invent amounts or dates that do not exist |
| Format | Includes the specified headings and fields, and required information can be found |
| Unknowns | Indicates "Not verified" when information is unavailable, or asks the necessary clarification question |
| References | Checks whether specified materials could be referenced and can compare them with the basis for the answer |
| Operations | Handles only permitted targets and does not send or update unnecessarily |
| Users | Can perform the required processing with the permissions and connections of the intended users |

Use labels the team understands, such as "Pass," "Needs revision," and "Not verified." It is important not to mark items whose results could not be investigated as passing.

First, check basic instructions with tasks such as organizing text that do not update external services. Add work requiring tools or Datasets as separate tests.

## 2. Confirm saving and open preview

1. Open the Agent settings from the target Project and check the Agent name.
2. Check the instructions, model, plugins, and data integrations to test.
3. If you made changes, check the result of automatic saving. On screens that display "Save," save manually as needed.
4. Confirm that "Saving" has finished and there are no errors. Reopen the settings as needed and check that the entered content remains.
5. Check the Agent's enabled status and that you have permission to use it.
6. Open the displayed preview area.

The preview location differs by how you open the screen. Some settings screens have a conversation area, while others let you switch from the Canvas toolbar to **"Preview agent."** You do not need to look for a button in the same place on every screen.

When switching from Canvas settings, if a confirmation about saving or unsaved changes appears, read it before proceeding. If you cannot switch, check for save errors or incomplete editing.

**Do not treat values only entered on screen as the test target.** Check the saved settings and the request you are about to send. Because the normal "Save" display may return after a saved display appears temporarily, do not decide that saving failed merely because the display changed.

### If preview does not open

- **"Agent is disabled"**: Before changing and saving the settings, check whether it is an Agent that may be enabled.
- **A message about usage permissions appears**: Check edit permissions and usage permissions separately. Do not publish to the entire organization just for testing; check the necessary permission scope with the responsible person.
- **Errors or loading continue**: Record and check the save status, target Agent, and screen error. Do not repeatedly resend.

Being able to use preview does not mean you have confirmed usage by other users or from Apps or Workflows.

## 3. Run one small test

The following is a test example for an Agent that organizes sales meeting notes. Replace it with input appropriate for your Agent's role.

### Example input

> Organize the following test sales meeting notes in the order "Key points," "Unverified items," and "Proposed questions for the next meeting."
>
> Sample Trading is considering organizing its inquiry responses. Staff members currently check emails individually. The number of inquiries, budget, and desired implementation date have not been obtained.
>
> Do not supplement amounts or dates that are not in the input; treat them as unverified. This time, only create notes; no external sending or operational data updates are required.

After sending, check the following in addition to the appearance of the response.

- Whether "Key points," "Unverified items," and "Proposed questions for the next meeting" are separated.
- Whether the number of inquiries, budget, and desired implementation date remain unverified.
- Whether the fact that emails are checked individually is not mixed with improvement proposals.
- Whether unrequested processing, such as sending to or updating an external service, was performed.

This test checks the basic behavior of organizing input text. It does not confirm that Dataset referencing, external integrations, or deliverable file generation succeeded.

Even if the response says "Completed" or "Checked the materials," do not mark it as passing based on that text alone. When needed, check the reference destination, processing result, and deliverable itself. For basic conversation and file operations, see [Chat with an Agent and check the results](../use/chat-with-agents.md).

## 4. Change conditions and check difficult inputs

After normal input succeeds, try different conditions that may occur in operations. The following are test design examples; they are not an automatic testing feature in Studio or a prepared test set.

| Test type | Example input or condition | What to check |
|---|---|---|
| Normal input | A short note with all required information | Whether it can output the specified content and format |
| Missing information | Omit the budget or owner | Whether it can avoid inventing unknowns and request confirmation |
| Ambiguous target | Assume multiple customers have the same name | Whether it avoids assuming the target and requests confirmation |
| Out-of-scope request | Ask a note-creation Agent to make an update for which it has not been granted permission | Whether it can explain its scope rather than pretending it completed the operation |
| Reference materials | Ask about an item whose answer can be verified in prepared materials | Whether the referenced content and response match |
| Question not in materials | Ask about a condition not stated in the materials | Whether it can explain that there is no basis |
| Ongoing conversation | Add conditions to the first response | Whether it can handle the added conditions and prior assumptions |
| Different user | An intended user runs it with their own account | Whether the scopes of Skills, materials, and connections are appropriate |

For tests that check permissions, use permitted test data and accounts. Do not enter unrelated data to test whether actual confidential information could leak.

### Additional checks when using tools or Datasets

- **Tools**: Check the call result or error and the actual target data. For update tests, agree on the target and changes with the responsible person before performing them.
- **Skills**: Check whether the expected procedure and format were used. Check that intended users, not only the creator, can use the Skill.
- **Datasets**: Check the target materials, update status, selection, and reference permissions. Being able to answer one question does not guarantee that all material can be checked without omissions.
- **Deliverables**: Check that they can actually be opened and that the content and number of items are correct. Displaying a file name alone does not mean completion.

Even if processing appears to have failed, the external service may already have been updated. Check its state before rerunning it.

## 5. Separate checks in a new conversation from checks in an ongoing conversation

If you repeatedly request corrections in the same conversation, prior feedback helps the response. To find out whether it can assist first-time users in the same way, you also need to check in a new conversation.

"Reset conversation" in preview creates a new preview conversation. Content being entered and files attached to the previous preview conversation are not carried over to the new conversation, so record the input, response, and errors you want to compare before using it.

1. Record the current test result.
2. If processing is running, check its completion status and the result of external processing.
3. Select "Reset conversation" and confirm that the new conversation is ready.
4. Send the test input you want to compare under the same conditions.
5. Check whether it meets the acceptance criteria without the supplementary information provided in the previous conversation.

**Resetting a conversation does not mean resetting Agent settings, canceling external operations, or deleting Memory or Project-shared information.** Do not use it as a way to stop processing in progress. Common settings and shared information can also affect a new conversation, so do not treat it as a state with no information at all.

In contrast, keep the same conversation for a test that checks how it handles follow-up questions. Recording whether you tested a "new conversation" or "continuation of the same conversation" makes results easier to compare.

## 6. Separate causes and narrow changes to improve

When something does not work, do not change every setting at once. Separate possible causes.

| Result issue | First thing to check | Example improvement |
|---|---|---|
| Format varies | Whether the format specification in the instructions conflicts with the request in the conversation | Specify headings and required fields |
| Fills in unknown values | Whether it states how to handle unknowns | Separate assumptions from facts and specify how to handle unverified items |
| Does not use materials | Dataset selection, import or update status, and access | Check the correct materials and reference settings |
| External operation fails | Connected account, target, operation permissions, and error | Check with the person responsible for connections or permissions |
| Does not follow the Skill | Skill content and visibility scope, and conflicts with Agent instructions | Organize the conditions for use and expected procedure |
| You can use it but others cannot | Usage scopes for the Agent, Skill, materials, and connections | Adjust only the scope required by intended users |
| Results change when the model changes | Whether the input, instructions, materials, and conversation conditions are the same | Compare under matching conditions and select a candidate appropriate for the use case |

Improve in the following order.

1. Record the failed input and result.
2. Choose one hypothesis for the cause.
3. Record the settings before the change and change only necessary items.
4. Check the save result.
5. Recheck with the same test input.
6. Decide whether it improved and also check that it did not worsen with another input.

For example, if it supplements an unprovided budget, first add an instruction such as "If the budget is not in the input, state that it is unverified" and compare. If you also change the model and Dataset at the same time, it becomes difficult to tell which change was effective.

Do not judge based on one success alone; check important inputs multiple times under matching conditions. Because responses can vary even with the same input, evaluate against acceptance criteria for content, format, and scope of operations rather than exact text matches.

## 7. Record results and changes

Do not use preview conversations as the sole record location. Save test results in a storage location approved by the team. The following is a recording template; it does not indicate a dedicated screen in Studio.

| Item | Content to record |
|---|---|
| Test ID and date/time | A name identifying the test and the date and time performed |
| Target | Organization, Project, and Agent; whether it was preview or the regular usage screen |
| Conditions | User role, confirmed model, material version and update date, and connection type |
| Conversation conditions | Whether it was a new or ongoing conversation; required assumptions for an ongoing conversation |
| Input | Test input usable for rechecking; exclude confidential information |
| Expected result | Acceptance criteria and operations that must not be performed |
| Actual result | Response, references, deliverable, confirmation result of external processing, and errors |
| Judgment | Pass, needs revision, or not verified, and the reason |
| Changes | Instructions or settings before and after the change, and the reason for the change |
| Retest | Whether it improved, effect on other input, and remaining issues |

Do not include API keys, passwords, or Secret values in records. Identify connections by classifications needed for verification, such as personal or shared.

## 8. Perform final checks with intended users and at the entry point

After the creator's preview passes, have intended users also check through the Project or App entry point they will actually use. Do not borrow users' accounts; ask them to perform operations for which they are permitted.

- Whether they can select the required Agent and chat with it.
- Whether they can use Skills, materials, and external connections within the intended scope.
- Whether the response includes information that they are not permitted to access.
- Whether responses and deliverables are usable for operations.
- Whether users can check or make an inquiry when an error occurs.

For uses called from a Workflow or API, validate separately at that entry point as well. Do not treat preview alone as confirmation of other execution methods.

Record the conditions that passed and remaining constraints, and communicate them to users. Completing testing does not mean publishing or distributing an App or rolling it out to all users.

## Completion checklist

- [ ] Checked the target Agent, permissions, connections, and test data
- [ ] Separately checked saved settings and successful responses and operations
- [ ] Tried missing information and out-of-scope requests in addition to normal input
- [ ] Distinguished new-conversation and ongoing-conversation conditions
- [ ] Checked references, deliverables, and results of external operations, not only response text
- [ ] Narrowed changes, retested, and also checked inputs that had passed previously
- [ ] Checked with intended users and at the actual usage entry point
- [ ] Recorded acceptance criteria, results, changes, and unverified items

## Related articles

- [Create an Agent and configure instructions and models](create-agent-instructions-and-models.md)
- [Configure tools, Skills, and Datasets for an Agent](configure-agent-tools-skills-datasets.md)
- [Chat with an Agent and check the results](../use/chat-with-agents.md)
- [Prepare and reference Datasets](prepare-and-reference-datasets.md)
- [An Agent does not reference data](../troubleshooting/agent-not-using-data.md)
- [Collect information needed for support](../troubleshooting/collect-info-for-support.md)
