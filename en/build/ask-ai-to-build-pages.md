# Ask AI to Create and Edit a Page

Tell AI the purpose and conditions of a business screen, and ask it to create a Page or edit an existing Page. A Page is a web screen for checking information, entering information, and performing business operations. This article explains the flow: **organize requirements → ask AI → check in preview → request edits → check registration and publication status**.

Even if AI responds "Done," the Page is not necessarily ready for business use. Check not only how the screen looks, but also input, displayed data, performed operations, and the users who can access it. For detailed procedures for registering and publishing Pages, see [Register and Publish a Page from the Screen](register-and-publish-pages.md).

## Related articles

- [Ask Studio Assistant to Build for You](build-with-studio-assistant.md)
- [Register and Publish a Page from the Screen](register-and-publish-pages.md)
- [Manage Page Versions](manage-page-versions.md)
- [Page Source Structure and Runtime](../development/page-source-structure-and-runtime.md)

## Before you begin

- Check the target organization and Project, and the Page to create or edit.
- Confirm that you can start a conversation with AI and have permission to create or update Pages in the target Project. Available capabilities and permissions vary by organization and Project settings.
- For a new Page, narrow the business service you will first provide to one. For an existing Page, decide the target Page name, the screen to fix, and capabilities that must not change.
- Prepare fictitious data or test data that you are authorized to use. Do not enter personal information, customer information, passwords, API keys, or similar data into the conversation.
- For screens that actually send externally or create, update, or delete data, decide the scope of impact during testing and the person responsible for confirmation.

> Asking AI to create or edit a Page and registering or publishing it for users are separate tasks. Plan approval for new creation or an instruction in a conversation that it may execute does not automatically replace confirmation before publishing to users.

## 1. Organize the business screen you want to create

First, describe the business work users should complete on the screen rather than simply asking for "a good-looking screen." If requirements are insufficient, the screen may display but lack necessary data or operations.

| Item to organize | What to provide | Example |
|---|---|---|
| Purpose | The work to complete in the Page and the criteria for success | A sales representative checks for omissions in meeting preparation |
| Users | Who uses it, when, and how often | A sales representative uses it before a meeting |
| Data | Items to display and enter, references, and information not handled | Company name, meeting date, and representative notes. Test data only |
| Screen | Required lists, details, input fields, order, and initial display | Search at the top, deal list in the center, and details on the right |
| Operations | Results of search, filtering, creation, update, AI execution, and so on | Record "Checked" and display a completion message |
| Permissions | Who can view and operate it, and whether external use is included | Sales representatives can view and update; only administrators can change settings |
| Completion criteria | What to test and what result means completion | Filter three test deals and confirm that entered content is reflected correctly |

Instead of making a broad request such as "Create a customer management screen," clarify what is in and out of scope this time. For example, limiting the first phase to search and display and separating creation, updates, and sending to external services into a later phase reduces the scope to confirm.

### Decide before making the request

When editing an existing Page, first open the current screen and identify what to change. If preview display is available on the screen, point to the part you want to edit before making the request. The selected location is a visual clue for AI and does not guarantee the exact target of the change. If similar items exist or what to change is unclear, also state the screen name, current text, and expected text in writing.

Also, for an existing Page that includes input, saving, or external integrations, first check what the change will affect. Even when asking only to fix the appearance, explicitly state "what not to change" so that related display conditions or operations are not changed unintentionally.

## 2. Ask AI to create or edit

In the target Project, open a conversation with AI for building. For how to select AI for building, review a plan and execute it, and generally check execution results, see [Ask Studio Assistant to Build for You](build-with-studio-assistant.md).

In your request, first state whether this is a new Page or an edit to an existing Page, then provide the requirements from the previous section. Rather than requesting implementation immediately, when editing an existing Page, ask it to confirm the target and organize what to create or change and how to test it first.

### Example request for a new Page

> I want to create a Page in this Project for sales representatives to check meeting preparation. Users are sales representatives, who will check the company name, meeting date, and representative notes before meetings.
>
> Include a search field at the top that filters by company name, a central list of test deals, and details for the selected deal. At first, limit the scope to display and search. Do not create or update data, send to external services, or change existing Pages.
>
> First, propose the screen to create, test data to use, change targets, and confirmation steps. After execution, separately report the Page name created, operations confirmed, and unconfirmed items.

### Example request to edit an existing Page

> Edit the deal list in the "Meeting Preparation" Page. Add a field to filter by meeting date below the company-name search field, and display company name, meeting date, and representative name in the list.
>
> Do not change creation and update processing, access settings, or other Pages. Before and after the change, check company-name search and meeting-date filtering with test data, and report the results. If you cannot identify the target, ask a question without making changes.

> Only saying "Create it" or "Edit it" may not match the completion scope AI assumes. Include the source of the data, operations it may execute, and the scope that must not change in the request. Written instructions to AI are not a substitute for technical controls over access permissions or external integrations.

## 3. Check the change scope before execution

When AI returns a plan or items to confirm, check the following before asking it to execute.

- Whether the target Project and Page name are correct, and whether it is clear if this is a new Page or an edit to an existing Page.
- Whether users, purpose, displayed data, and input fields meet the requirements.
- Whether the operations the screen will provide and the targets created, updated, or sent by those operations are clear.
- Whether existing Pages, existing data, access settings, and external integrations that must not be touched are excluded.
- Whether the data used for confirmation and the method to confirm normal and information-insufficient cases are decided.
- Whether registration, publication, access settings, and version switching will be done this time or separated into follow-up work.

If the plan includes unnecessary operations, ask it to narrow and rewrite the scope. If you approve a plan in a newly created Project, proceed only after understanding that "Approve" starts execution. In normal conversations, the same approval screen is not necessarily displayed, so state in writing which targets may be executed.

When an AI Page-building request changes Project settings or resources, check the Project name and description, change targets, conditions blocking execution, and progress. Even if "Retry" or "Skip" appears for a failed step, do not continue without checking the reason. If insufficient permissions or rejection from a connection destination appears, review the target, permissions, and allowed execution scope.

## 4. Check the preview and business operations

After AI completes its work, open the target Page and check it. For how to open it from screen cards or lists, see [Move Your Work Forward from a Page](../use/work-with-pages.md). Do not treat merely opening the screen as complete; check each requirement.

1. Compare the target Page name with the creation or change details reported by AI.
2. If preview display is available, confirm that the intended screen appears. Do not treat loading errors or a blank screen as complete.
3. Confirm that lists, headings, input fields, buttons, and descriptive text suit the requested work and users.
4. With test data, try each requested operation one by one, such as search, filtering, display, input, and confirmation.
5. If there are operations that create, update, or send data, check not only the screen display but also the result at the target save destination or destination system.
6. Also check display for unexpected states, such as missing required information, no search results, or insufficient permissions.

| Perspective to check | What to check |
|---|---|
| Purpose and flow | Whether users understand the first operation to perform and the next action to take |
| Data | Whether only target information is displayed and test data is not mixed with production data |
| Input | Whether required fields, formats, and input-error guidance are understandable for the work |
| Operation results | Whether you can confirm execution, saving, updating, and sending results, and do not repeat the same operation |
| Permissions and visibility | Whether users are allowed only necessary operations and the Page is not opened to unintended people |
| Screen display | Whether text, tables, and buttons are readable in PC use, and important information is not hidden |

> "Save," "Send," and "Run" in a Page can affect actual data or external services. Distinguish checking display behavior from executing business processing. If it is unclear whether processing has completed, do not run it again without confirming.

## 5. Request focused edits

If any part differs from expectations, state the differences specifically before asking to rebuild the whole Page. Providing the target, current state, expected state, items not to change, and operation to recheck together helps limit the scope of impact.

> Edit the "Meeting Preparation" Page. Currently, the deal list displays only company names. Display meeting date and representative name to the right of the company name. If the meeting date is not entered, display "Not set" instead of leaving it blank. Do not change search, access settings, or creation and update processing. After the change, check a deal with a meeting date and a deal without one.

After editing, check the updated Page in preview display again rather than relying on the previous result. If you updated an existing Page, record the currently applied content and the purpose of the change in case you need to return to the previous state. The Page may display "Version History" and "Restore and apply," but check the impact on users before switching. [Manage Page Versions](manage-page-versions.md) explains version handling.

## 6. Check registration, publication, and access settings

After confirming creation or edits, check who will use the Page and through which entry point. Being visible in the Page list, being able to open a Page, and being able to provide it to external users are not the same state.

- Confirm that the Page is registered in the target Project and that you can open the required Page.
- If "Upload New Version" or an update was performed, confirm that the intended content is currently applied.
- If "Access Settings" is displayed, check whether sign-in is required and the scope of users. Configurable choices may vary by organization feature settings.
- Before informing external users, confirm the conditions for registration, publication, and URL sharing with the responsible administrator.

Do not treat registration, publication, and access settings as complete based only on a report that screen creation is complete. Provide the Page only after checking business operations through the entry point and permissions actual users will have.

## Checklist

- [ ] Organized the purpose, users, data, screen, operations, and permissions
- [ ] Communicated whether this is new creation or an edit to an existing Page, along with the target and scope that must not change
- [ ] Decided the test data and business operations to check
- [ ] Confirmed that the AI plan does not include unnecessary creation, updates, sending, or publication
- [ ] Checked the screen display in preview
- [ ] Checked requested operations and their results with test data
- [ ] Requested focused edits and rechecked the updated Page
- [ ] Checked registration, publication, access settings, and the actual user entry point

## Next articles

- [Move Your Work Forward from a Page](../use/work-with-pages.md)
- [Register and Publish a Page from the Screen](register-and-publish-pages.md)
- [Manage Page Versions](manage-page-versions.md)
- [Page Source Structure and Runtime](../development/page-source-structure-and-runtime.md)
