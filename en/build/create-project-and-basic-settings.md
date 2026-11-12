# Create a Project and Configure Basic Settings

When you begin building a business solution in Studio, prepare a Project as your workspace. This article explains how to **create a Project from Studio Workspace, check its name, description, and members, and make it ready for building**.

Creating a Project is different from installing an App from the Marketplace. If you will only use a shared Project, do not create a new one; see [Get Started in a Shared Project](../use/start-with-shared-project.md).

## Before you begin

- Confirm the destination organization and that you have permission to create Projects.
- Check with the owner or in the list whether a Project for the same purpose already exists.
- Decide who will use it and for what purpose, and how much you will build initially.
- Confirm the initial members and required roles. You can also prepare it by yourself and add other members later.
- For validation, prepare a name that distinguishes it from production work and test data you are allowed to use.

> The following procedure creates a Project from the Workspace list in a web browser. Menus, permissions, and creation limits vary by organization settings and contract. Do not substitute an unavailable feature with another screen or URL; check with the responsible administrator.

## 1. Confirm the destination and open "Create"

1. Sign in to JAPAN AI and confirm the organization where you will create the Project.
2. Open **Studio Workspace** and go to the Project list.
3. Check the list for a Project with the same purpose. When searching by name, also check related business and team names.
4. Select **"Create"** to open the **"Create Project"** screen.

The list shows Projects you can access and Projects that are displayed based on organization settings. Not finding one in search alone does not establish that it does not exist in the organization.

If you cannot select "Create," check the guidance near the button and the usage status. Creation permissions, contractual availability, and the organization's limit on the number of Projects may be involved. Do not independently delete an existing Project to make room under the limit.

If you do not know where to start, see [Prerequisites and Basic Screens](../getting-started/getting-started-and-navigation.md).

## 2. Set the name, description, and icon

Enter the basic information on the creation screen. **Both the project name and description are required when creating it.** You cannot proceed to setup if either is blank or contains only whitespace.

| Item | What to enter | Examples and notes |
|---|---|---|
| Project name | A name that identifies the work or team using it | `sales_department_deal_preparation_validation`, for example. Distinguish production and validation use. |
| Description | The target work, users, information used, and what you want to achieve | Also describe the initial scope to build and operations not yet to perform. |
| Icon | An image to distinguish it in lists and elsewhere. Set it as needed. | Follow the conditions shown on screen. You can create the Project without setting one. |

### Description example

> This is a validation Project for the sales team to organize information before meetings. From the provided company information and meeting notes, it creates items to confirm and proposed questions. At first, it uses test data only and does not send email or update the CRM.

When you select "Create with a plan," the description is entered as a draft in the input field on the planning screen. With standard "Create," no build request is sent, so in either case, specify the target and purpose rather than only "Sales support." However, **writing "do not update" in the description is not a substitute for restrictions based on operation permissions or connection settings.**

Do not enter API keys, passwords, Secret values, or other sensitive values in the name or description. Treat connections to external services and registration of sensitive values as dedicated configuration.

## 3. Check members and roles

Check the member list on the creation screen.

1. Confirm that you are included. On this creation screen, you are initially set as an administrator.
2. If people will work with you from the start, add them from the displayed candidates.
3. **Check the role of each person you add.** Because administrator is initially selected when adding a person on this screen, do not confirm it as-is; change it to a role appropriate for their responsibility.
4. Check names and email addresses, and remove anyone added by mistake.

Selecting candidates here adds displayed organization members to the Project. It does not necessarily mean that you can send an organization invitation from here to any email address for a person who is not a candidate.

At least one administrator is required when creating the Project. Do not confuse an organization administrator with a Project administrator, and do not grant more permissions than necessary. Actions actually available also depend on organization and Project permission settings, not just roles.

For details on adding, changing, and removing members, see [Manage Project and App Members](../operations/manage-project-and-app-members.md).

## 4. Choose how to create it

After checking the entered information and members, choose a creation method. "Create" and "Create with a plan" are displayed.

| What you want to do | Action | What to check |
|---|---|---|
| Prepare only the workspace and build later | Select "Create" | The Project is created and you proceed to it. Planning does not start. |
| Discuss a build plan with AI before proceeding | Select "Create with a plan" | After creating the Project, you proceed to "Project Plan: <Project name>." |
| Review the name or description | Select "Cancel" and review the entries | This closes the entries before creation; it does not delete an already created Project. |

Even in an environment where you can choose templates, do not confuse the choice of creation method with installing or publishing a template.

### When you only complete basic settings

Select **"Create"**, then open the Project after creation and check the basic settings. This does not create or execute a build plan. To ask AI to build later, see [Ask Studio Assistant to Build for You](build-with-studio-assistant.md).

### When you create a plan with AI before building

When you select **"Create with a plan,"** after the Project is created you proceed to **"Project Plan: <Project name>."** There, send the discussion content and check the proposed plan.

| What to do on the planning screen | Action | What to check |
|---|---|---|
| Proceed to the Project without running the plan | Select "Skip" and proceed on the confirmation screen | You move to the Project without running the plan. |
| Ask AI to build | Check and discuss the plan, then proceed to execution from the "Approve" confirmation screen | This is not simply saving; execution of the plan begins. |
| Review Project settings | Select "Back" to return to the settings screen, then select "Save & Continue" after revisions | Check the content even after returning to planning. Check member changes in the management screen after creation. |
| Cancel this creation itself | Read the cancellation confirmation when closing the setup screen | This deletes the created Project; it is not a temporary save for continuing later. |

Even if you change members on the screen after selecting "Back," "Save & Continue" does not apply those changes. Add or remove members and change roles in the Project member management screen after finishing setup.

### When skipping planning

1. Select **"Skip."**
2. Read the **"Skip planning?"** confirmation.
3. When you proceed on the confirmation screen, you move to the Project without running the plan.

### When approving and running a plan

Discuss the purpose, input information, expected results, and permitted scope of execution, then check the proposed plan. **When you confirm the execution confirmation after "Approve," the Agent starts execution.** If it includes external transmission or changes to existing data, confirm that the actions may be executed before proceeding.

Starting execution does not mean building is complete. [Ask Studio Assistant to Build for You](build-with-studio-assistant.md) covers checking processing results and created content.

> **"Skip" and "Stopped" are different.** The cancellation confirmation displays "The Project and all data will be deleted." This is the action for canceling setup after "Create with a plan." If you want to retain the Project and build later, do not confirm cancellation; use "Skip."

## 5. Check and revise basic settings after creation

After you can open the Project, check its name, then open **"Basic Settings"** from **"Project Settings"** in the Project.

### Change the name and description

1. Select **"Edit"** under **"Project Information."**
2. Revise the project name and description.
3. Select **"Save"** and check the update result.
4. Confirm that the displayed name and description are what you intended.

Both the name and description are required during creation, but you can clear the description when editing after creation. Operationally, it is recommended that you retain a description that lets users determine the purpose. You cannot clear the project name.

If you select "Cancel" while there are unsaved changes, a screen is displayed to confirm discarding the changes. This is an action to **cancel unsaved edits**, and differs from deleting a Project by canceling setup during creation.

### Change the icon

Use the image upload action under "Project Icon" and select an image as guided on screen. Changing the icon is processed separately from saving the name and description, so check the upload result and display.

If you cannot select "Edit" or an image action, check your permission to change Project settings. Being able to open a Project is not the same as being able to change its settings.

## 6. Check before starting to build

Check completion of basic settings separately from completion of a solution that can be used for work.

- The Project was created in the correct organization and can be opened.
- The name and description make the purpose and the distinction between validation and production clear.
- The intended members and roles are set. If you returned to the settings screen during setup, also check the state after saving.
- It is clear whether you deferred building through standard creation, skipped planning, or approved and started execution through planning.
- If you ran AI building, check the created resources and unfinished items.

Creating a Project alone does not mean that all required Agents, Workflows, Pages, and other resources are complete. Set up external connections, Secrets, a home Page, publishing, and distribution separately as needed.

## When things do not work

| Situation | What to check or do |
|---|---|
| Cannot find Workspace or "Create" | Check the organization, availability conditions, and permission to create. If you only use a shared Project, new creation is unnecessary. |
| Cannot select "Create" | Check the guidance near the button, contractual availability, the organization's creation limit, and permissions. |
| Cannot select "Create" or "Create with a plan" | Check that the name and description are entered and do not consist only of whitespace. Correct any input errors. |
| The person to add is not a candidate | Check the organization and displayed candidates, then check their organization membership and invitation status with the responsible administrator. |
| Cannot start planning | Check that this is the screen after selecting "Create with a plan," and check the error content. To retain only the workspace, use the displayed "Skip." Do not confuse it with deletion by cancellation. |
| Do not know the result of creation or saving | Do not confirm the same action repeatedly; check the list and settings again. Also check whether the Project may already have been created. |
| Cannot select "Edit" | Check the permission to change settings for the target Project. Viewing permission alone may not allow changes. |

For inquiries, include the organization name, Project name, screen where you took the action, date and time, displayed error, and results of rechecking the list or settings. Do not include sensitive values such as API keys or passwords.

## Completion checklist

- [ ] I checked the destination organization and whether an existing Project can be reused.
- [ ] I created the Project and checked its name, description, and any required icon.
- [ ] Rather than relying on the initial selection, I checked members and roles.
- [ ] I checked the difference between "Create" and "Create with a plan," and when using planning, proceeded with an understanding of the differences between skipping, approving and running, and canceling.
- [ ] I opened the Project and checked the result of saving its basic settings.
- [ ] I have decided the next tasks, such as building, connecting, and testing.

## Related articles

- [How Studio Workspace, Project, App, and Marketplace Relate](../getting-started/studio-concepts.md)
- [Prerequisites and Basic Screens](../getting-started/getting-started-and-navigation.md)
- [Get Started in a Shared Project](../use/start-with-shared-project.md)
- [Ask Studio Assistant to Build for You](build-with-studio-assistant.md)
- [Prepare the Project Description and Context](prepare-project-context.md)
- [Set a Project Home Page](set-project-home-page.md)
- [Understand Organization, Project, and App Permissions](../operations/understand-org-project-app-permissions.md)
- [Manage Project and App Members](../operations/manage-project-and-app-members.md)
