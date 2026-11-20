# Prerequisites and Basic Screens

Before you start using Studio, check **the account, organization, and target App or Project you will use**. Being able to sign in is different from being able to use the screens and features you need.

This article explains what to check before starting and how to distinguish basic screens. Installing Apps, inviting members, and operations for each feature are covered in their respective detailed articles.

## 1. What to check before opening Studio

Confirm the following information with the administrator or person who guided you to use Studio.

| Item to check | What to confirm |
|---|---|
| Access destination | The sign-in destination provided by your organization, or the App or Project link |
| Account to use | The email address authorized for use and the sign-in method specified by the organization |
| Organization | The name of the organization to use for this work |
| Resource to use | The App or Project name, and the first screen, Agent, Workflow, or other feature to use |
| Your work | Whether you only use it or also create, edit, and manage |
| Additional preparation | Whether you need to connect a personal external account or an administrator must complete initial setup |

Open Studio in a Web browser on a PC. **If the screen width is less than 600px, "STUDIO" itself is not shown in the left navigation.** This does not mean that every App cannot be used on a smartphone. Check the individual App guidance for its supported environment.

> If you were instructed to use an existing App or Project, do not create a new organization or Project merely because you cannot find it. First check the access destination and account.

## 2. Sign in and confirm your organization

1. Open the access destination provided by your organization.
2. Sign in using the specified method. Even if it opens without a sign-in screen, check that the active account is correct.
3. If the organization selection screen appears, select the organization for this use.
4. Before opening an App or Project, check that the displayed organization matches the guidance.

When one person uses multiple organizations, the Apps and Projects displayed may differ based on the selected organization. If you cannot find the target organization, ask an administrator to check the account you use and your participation status in that organization.

Follow your organization's procedures for sign-in methods and additional authentication. Even if you use another sign-in method on your own, the same membership and permissions are not necessarily carried over.

## 3. Choose the entry point for your goal

The entry points for Studio are **"STUDIO" > "Workspace"** and **"STUDIO" > "Apps"** in the left navigation.

| Menu | What opens |
|---|---|
| "STUDIO" > "Workspace" | The Studio Workspace Project list, used to create and manage Projects and to use shared Projects |
| "STUDIO" > "Apps" | The "My Apps", "Marketplace", and "Organization Apps" tabs. "Organization Apps" is shown only to organization administrators and others with permission to install Apps |

In organizations with app-only seats, "Workspace" is not shown to members who do not have a chat or speech seat.

| What you want to do | Where to open or what to check |
|---|---|
| Move work forward in an installed App | Find the target App in "My Apps" under "Apps", or open the provided App link |
| Use a shared Project | Open the provided Project link. Even when finding it from the "Workspace" list, check the target name. |
| Create or manage Agents, Workflows, Pages, and more | Open the target Project in "Workspace". To build without writing code, you can also ask [Studio Assistant](../build/build-with-studio-assistant.md) in the Project |
| Consider installing a new App | Check the list on the "Marketplace" tab under "Apps" |

**Using an App and installing from Marketplace are separate tasks.** A user does not necessarily need to install an App again to use an installed App.

Even when using a shared Project, you do not need to understand every screen for builders before you start. Begin with the features your representative directed you to use.

For differences in terminology, see [How Studio Workspace, Project, App, and Marketplace Relate](studio-concepts.md).

## 4. How to read basic screens

### Apps list

Open it from "STUDIO" > "Apps". Use "My Apps" for installed Apps and the "Marketplace" tab to find new Apps. The "Organization Apps" tab, for managing Apps in the organization, is shown only to people with permission to install Apps.

When opening an App, check its name and description. If there are Apps with similar names, check that it matches the provided link before proceeding.

### Studio Workspace Project list

Open it from "STUDIO" > "Workspace". This is the entry point for finding Projects to create or manage. When you open a Project, check its name. If you came to use an existing Project, check your access to that Project rather than creating a new one.

### Inside an App or Project

After opening it, menus vary according to the screen and processing content. Rather than memorizing an identical appearance, look for the following roles.

| Screen element | What to check |
|---|---|
| Target name and heading | Which App, Project, or feature you opened |
| Navigation | Entry points to available Pages, Agents, processing, data, and more |
| Main work area | The place for work in that feature, such as chat, input forms, lists, and results |
| Entry point to settings | The place to manage members, connections, and configuration. Not every user changes these. |
| Status display and guidance | Information needed for your next action, such as incomplete setup, loading, or permission guidance |

An App displays prepared Pages, Agents, automation, and more. A Project has a configuration for navigating resources, conversations, and more. A user-facing home Page may open, and you may not see the building menus.

**Seeing a feature on screen is also different from being permitted to edit or run it.** Available operations vary by resource and permission.

## 5. After opening it, check whether it is ready to use

### When you were asked to join a Project

When you open a Project, a screen guiding you to join may appear. Confirm that it is the Project you were directed to use and follow the guidance displayed.

How to join varies by Project and organization settings. Knowing a link does not necessarily mean that you can join. If you do not see guidance to join, return to a list, or cannot access it, ask an administrator to check your participation status.

### When a connection to an external service is required

If an App or Project uses an external service, the following preparation may be required:

- The user connects their own external account.
- An administrator prepares a shared connection or required settings.

Do not confuse a user's own connection with a shared connection configured by an administrator. When prompted to configure something, check whether it is an item you are responsible for. Avoid pasting passwords or API keys into chat to ask for confirmation.

[Choose Between Personal Connections, Shared Connections, and Secrets](../operations/choose-personal-shared-connections-and-secrets.md) explains connections, and [Connect Your Own External Accounts](../operations/connect-your-external-accounts.md) explains personal setup.

## 6. When the display differs from the guidance

First check the following in order. This section only helps isolate issues when starting to use Studio; do not change permissions or settings based on your own judgment.

| Situation | First thing to check |
|---|---|
| The target organization does not appear | The signed-in account and your participation status in the organization |
| "STUDIO" is not in the left navigation | Screen width (it is hidden below 600px) and the organization in use |
| "Workspace" is not shown | Whether your organization uses app-only seats and you have no chat or speech seat |
| The "Organization Apps" tab is missing | Whether you are an organization administrator or otherwise have permission to install Apps |
| An App or Project is not in the list | The target name, provided link, participation status, and the scope you can view |
| You see participation or access guidance after opening a Project | Whether you are included as an intended user and whether joining procedures are required |
| The screen opens but you cannot run or edit | Required permissions for the operation, whether the feature is enabled, and initial setup or external connections |
| A restriction related to the connection source appears | The network and access conditions specified by the organization |

If it is not resolved, tell the administrator the target name, when it occurred, the operation you tried, and the message displayed. If you include a screen image, hide secret information and unnecessary personal information.

## Getting started checklist

- [ ] I am signed in with the specified account.
- [ ] I confirmed the organization for this use.
- [ ] I can open the target App or Project.
- [ ] I confirmed the screen, Agent, and processing that I will use.
- [ ] I know whom to ask about participation, permissions, initial setup, and external connections.

Once you have checked these items, proceed to an operation article that fits the target. Even if you only intend to try it, check the content before running processing that sends information or updates data.

## Next articles to read

| What you want to do next | Article |
|---|---|
| Use an installed App | [Open an Installed App and Get Started](../use/open-installed-app.md) |
| Use a shared Project | [Get Started in a Shared Project](../use/start-with-shared-project.md) |
| Choose the articles you need | [How to Read This Manual by Role](reading-by-role.md) |
| Investigate display or access problems | [Something Is Not Visible or You Cannot Join](../troubleshooting/not-visible-cannot-join.md) |
| Check differences in permissions | [Understand Organization, Project, and App Permissions](../operations/understand-org-project-app-permissions.md) |
