# Open an Installed App and Get Started

This article explains how to open an App introduced by an administrator or other person in charge, then proceed to the screen or process you need. **Whether the App is installed, whether you have access, and whether required connections are configured are separate things to check.**

This article is for people using an App that has already been installed. It does not cover installing a new App or changing sharing settings. You do not need to create a Project or edit Agents or Workflows.

## What you can do with this procedure

- Open the App you use from the list or from the link you were given.
- Decide whether to proceed yourself or check with the person in charge when you see guidance about joining or setup.
- Navigate to the feature you need, such as a Page, Agent, or published Workflow in the App.

## Before you begin

Prepare the following information.

| Item to check | What to check |
|---|---|
| Where you will use it | The organization name and App name. If you received a link, that link |
| Account | A login account authorized for use by the organization |
| First task | The Page to open, the Agent to use, or the process to run |
| Setup contact | The App administrator who configures App members and shared connections |

If you have not yet logged in or selected an organization, first see [Getting started requirements and basic screens](../getting-started/getting-started-and-navigation.md).

> Screen labels and displayed items vary by the organization configuration, permissions, and screen layout. The following steps describe what to check in a desktop web browser.

## 1. Find the installed App

### Find it from the Apps list

1. Open the organization you will use this time.
2. In the left navigation, open "STUDIO" > "Apps".
3. Select the "My Apps" tab (the Apps tabs are "My Apps", "Marketplace", and "Organization Apps"; "Organization Apps" is shown only to organization administrators (ADMIN) or users with the `jai.studioapp.install` permission).
4. Enter the App name you were given in "Search by app name."
5. Check the name and description, then select the App you need.

Depending on your administrative permissions, you may see an administration list as well as cards. If "Open" is displayed, you can proceed from there.

**"My Apps" does not necessarily list every App installed in the organization.** If you cannot find the App by searching, clear the search term and check the list. If you still cannot find it, check the link you were given or your participation status.

### Apps waiting for pre-installation

Apps that JAPAN AI has prepared as pre-installed appear in the list before they are installed. They are installed the first time someone clicks "Open". If you do not need one, you can also "Delete" it.

"Marketplace" is where you find new Apps and consider installing them. Do not reinstall an App just because you cannot find an installed App.

### Open it from a link you were given

1. Open the link for the App provided by the administrator or person in charge.
2. If you are prompted to sign in, use the specified account.
3. Check that the opened App name and organization match the guidance you received.

Even when the App is not shown in the list, opening the link you were given shows whether you can open the App. However, **having a link does not itself grant access permissions.**

## 2. Check the details only when you are prompted to join

If you are already a member of the App, its screen opens directly. How you become a member depends on your role.

| Role | How to become an App member |
|---|---|
| Organization administrator (ADMIN) | Can join by themselves with "Join". Always joins as an administrator; for paid Apps this uses one seat |
| Regular member | Is added as a member by an App administrator, or joins automatically as a member the first time they open an App (free App) with "Publish to all organization members" enabled |

Regular members cannot "Join" by themselves.

> **When an organization administrator selects "Join", they join the App as an administrator.** For paid Apps this uses a seat and affects the monthly fee (unit price × number of active members). If you will not manage the App, do not join; consult an App administrator instead.

You are asked to join a Project on the App screen because the resources and members that make up the App are managed by Project. This does not create a new Project.

If you cannot open the App or an access error occurs, ask an App administrator to add you. Provide the App name, account you are using, and the guidance that was displayed.

## 3. Choose the feature to use from the opened screen

The screen displayed immediately after you open an App mainly differs as follows.

| First screen displayed | How to proceed |
|---|---|
| An App-specific home Page | Follow the instructions on the screen and use the form, list, or business menu you were given. |
| A dashboard with Pages, Agents, and other items | Select the item that fits your purpose. You do not need to operate every item in order. |

When a home Page opens, it is not necessarily a problem if you cannot see the same menu as on the dashboard. If "Back to dashboard" is displayed, you can return from there.

In the dashboard or App navigation, look for the following entry points. What is displayed varies by the features and permissions available in the App.

| What you want to do | Main entry point | Next article |
|---|---|---|
| Enter information and review it on a business screen | "Pages," or the home Page that opened first | [Move Your Work Forward from a Page](work-with-pages.md) |
| Ask AI to do something or ask a question | "Agents" | [Chat with an Agent and Review the Results](chat-with-agents.md) |
| Review or continue a previous conversation | "Chat Rooms" | [Chat with an Agent and Review the Results](chat-with-agents.md) |
| Run a prepared Workflow | The relevant Workflow in "Automations" | [Run a Published Workflow](run-published-workflow.md) |

"Automations" in an App shows Workflows and tasks in the Task Scheduler. Read the process name and description, and select the item you were given by the person in charge. You do not need to change automatic execution settings for first-time use.

When you use Object data or files in Storage, follow the Page or operation flow prepared in that App. Not every App has a menu for directly operating Objects or Storage.

## 4. Split responsibilities when you are asked for connections or setup

Even if you can open the App, some features may be unavailable when external-service connections or shared settings are missing.

| What is requested | Who should handle it and how |
|---|---|
| Connect or reauthenticate your own external account | Configure it from the connection screen shown by the App. Check the service and account you will use. |
| Configure a shared Connector or Project Secret | Ask an App administrator (only App administrators can configure these). Do not replace it at your own discretion because it affects other users. |
| You do not know which it is | Tell the person in charge the message that was displayed and check whether it is a setting you should make. |

In some Apps that require a personal connection, you can check the connection from the App's "Settings." For specific authentication steps, see [Connect Your External Accounts](../operations/connect-your-external-accounts.md).

Even if you see guidance such as "Some items are not configured" or "Review setup," it does not necessarily mean the entire App cannot be used. Conversely, **do not conclude from the absence of guidance alone that all required settings have been completed.** Setup guidance varies by permission.

Do not paste passwords, API keys, or authentication tokens into chat or an inquiry message. Handle them in the designated settings screen.

## 5. Perform your first action and check the result

Once you know which feature to use, proceed according to the guidance in that App and the relevant operation article.

1. Check the target of the process and the information you will enter.
2. For actions that send information externally or update data, check the destination, update target, and scope of the run.
3. Perform the action with approved input.
4. Check the result or status on the screen. Depending on the process, also check the run history and whether the change was reflected in the external service.

**Being able to open the App or press a button is different from the process being complete.** Review AI responses before using them, and check that sending or updating was reflected in the intended target.

Even when a result is not displayed, the process may still be running. For actions that send or update information, check the history or reflected status before running them again.

## When you cannot open it or start using it

| Situation | What to check first |
|---|---|
| You cannot find Apps | Check the organization and screen width. When the screen is narrower than 600px, "STUDIO" itself is hidden. If you still cannot find it on a PC, ask an administrator about the conditions for use. |
| The target is not in My Apps | Clear the search term, then check the App name, account you are using, and participation status. Do not immediately conclude that it is not installed in the organization. |
| "Join" is displayed | It is shown only to organization administrators and joins them as an administrator. If you will not manage the App, do not join; ask an App administrator to add you as a user. |
| You cannot select a card or use "Open" | Ask an App administrator to add you as a member and check the App status. Reinstalling does not solve the problem. |
| Opening a link returns you to the list or results in an error | Ask the person in charge whether the link is still valid and whether the target App is available. |
| The App opens but a specific process fails | Check permissions for that action, the personal connection, shared settings, and input conditions. |

If the issue is not resolved, provide the person in charge with the App name, date and time it occurred, action you performed, and displayed message. Ensure screenshots do not contain Secret information or unnecessary personal information.

For detailed troubleshooting, see [Not Visible or Unable to Join](../troubleshooting/not-visible-cannot-join.md).

## Getting-started checklist

- [ ] I opened the intended App with the specified organization and account.
- [ ] If I was not an App member, I asked an App administrator to add me (organization administrators joined with "Join" only if they manage the App).
- [ ] I know the first Page, Agent, Workflow, or other feature I will use.
- [ ] I can distinguish between personal connections and shared settings managed by an administrator.
- [ ] Before my first action, I checked the target, impact, and how to verify the result.

## Next articles to read

- Work in a Page: [Move Your Work Forward from a Page](work-with-pages.md)
- Request something from AI: [Chat with an Agent and Review the Results](chat-with-agents.md)
- Run a prepared process: [Run a Published Workflow](run-published-workflow.md)
- View and update business data: [View and Update Object Data](view-and-update-object-records.md)
- Use files: [Use Files in Storage](use-storage-files.md)
- You were shared a Project rather than an App: [Get Started in a Shared Project](start-with-shared-project.md)
- Set up your authentication: [Connect Your External Accounts](../operations/connect-your-external-accounts.md)
- For the person responsible for shared setup: [Complete App Setup](complete-app-setup.md)
