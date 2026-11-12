# Deploy an App in Your Organization

In this exercise, you will install the practice Object, Page, and Agent from the [Task Management App](task-management-app.md) into another Project in the same organization as an App. Without directly sharing the source Project, you will check the display and response as a user after installation, then uninstall it. You will not list it in Marketplace or submit it for review.

## Related articles

- [A Task Management App](task-management-app.md)
- [Creator self-installs within the organization](../distribution/self-install-in-organization.md)
- [Design configuration for distributable Pages](../development/design-distributable-page-config.md)
- [Complete App setup](../use/complete-app-setup.md)
- [Check App update changes and reconfiguration](../use/check-app-updates.md)
- [Uninstall an App](../use/uninstall-app.md)

## Before you begin

- Choose a person who can open the source Project for [A Task Management App](task-management-app.md), create and install Apps, and choose practice members for confirmation. If a feature is not displayed, check permissions and availability conditions in the [Index of limits and availability conditions](../operations/limits-and-availability-index.md).
- Confirm that the Page does not fix a source-specific URL, ID, Secret value, or connection ID. For design checks, see [Design configuration for distributable Pages](../development/design-distributable-page-config.md).
- Do not use real business data, connections, Secrets, schedules, triggers, API keys, or public links. Check only the fictional tasks from [A Task Management App](task-management-app.md) in the installing organization.
- The source and installation destination are separate Projects. Working in the source Project alone does not confirm access or configuration in the installation destination.

## Sample used in this exercise

| Item | Value |
|---|---|
| App name | Task Management Practice App |
| Include | Practice Object, Page, and Agent from [A Task Management App](task-management-app.md) |
| Page to check | Task management (test) |
| Agent to check | Task Guide Agent (test) |
| User question | What is the status of Review demo screen? |
| Change for the update | Add "For in-organization installation confirmation" to the Page heading |

## Step 1. Check resources to include in the App

**Goal:** Prepare only what is needed for the exercise as an App.

1. In the source Project, open the practice Object, `Task management (test)` Page, and `Task Guide Agent (test)`.
2. Confirm that the Page and Agent do not use connections, Datasets, fixed URLs, or Secrets outside this exercise.
3. Open **"App publishing"** for the Project, select **"Within organization"** as the publishing destination, and create the App with the name `Task Management Practice App`.
4. Select only practice resources as publishing resources, then run **"Check app resources."**
5. Confirm that there are no unresolved references or warnings. If there are any, do not proceed with installation; review dependencies and design.

For resource selection and checks, see [Creator self-installs within the organization](../distribution/self-install-in-organization.md). **Checkpoint:** The App name and selected resources are only for practice and do not include source-specific values.

## Step 2. Install into another Project

**Goal:** Create an installation destination Project that is separate from the source.

1. Confirm `Task Management Practice App` under **"App in this organization."**
2. Check the prepared version, included resources, access settings, and credential requirements.
3. Select **"Install in this organization,"** check the target and changes, and confirm.
4. While processing, do not repeat the same action. Confirm that **"Installed version"** and **"Open installed app"** are displayed.
5. Use **"Open installed app"** to open the installation destination Project, which is different from the source.

For installation details and status checks, see [Creator self-installs within the organization](../distribution/self-install-in-organization.md). **Checkpoint:** You have opened the installation destination Project rather than returning to the source Project. Even if the result is unclear, do not run it again; check the status.

## Step 3. Check initial setup and users

**Goal:** Separately check installation status, settings, and user access.

1. If **"Complete setup"** is displayed, check the remaining settings. Do not add connections or Secrets in this exercise.
2. Do not mistake unconfigured, under review, or loading failure for no configuration needed or already configured. If a required setting is displayed, do not guess a value; check with the responsible person.
3. Configure the minimum access needed for practice members to open the target App.
4. Confirm that practice members, not only administrators, can open the App entry point using their own accounts.

For initial setup, see [Complete App setup](../use/complete-app-setup.md); for members, see [Manage Project and App members](../operations/manage-project-and-app-members.md). **Checkpoint:** Do not treat a "Configured" display alone as confirmation that users have access.

## Step 4. Check the Page and Agent as a user

**Goal:** Confirm that a practice member in the installation destination can use practice data.

1. Using a practice member account, open `Task Management Practice App`.
2. On the `Task management (test)` Page, confirm that fictional tasks are displayed.
3. Send the sample question to `Task Guide Agent (test)`.
4. Compare the response with Page or Object records and confirm that it is based on practice data rather than inference.

For using and checking Apps, see [Complete App setup](../use/complete-app-setup.md); to return and check the Page and Agent construction, see [A Task Management App](task-management-app.md). **Checkpoint:** A practice member, rather than the developer's permissions, confirmed the display and response in the installation destination.

## Step 5. Apply a small change as an update

**Goal:** Separately check a source change and the state applied to the installation destination.

1. In the source Project, add "For in-organization installation confirmation" to the practice Page heading.
2. On the App publishing screen, recheck the resources and prepare a new version.
3. Check changes, access settings, and credential requirements, then update the installed App.
4. After checking the installed version in the installation destination, have a practice member check the revised heading and representative question.

For post-update settings and use checks, see [Check App update changes and reconfiguration](../use/check-app-updates.md). **Checkpoint:** Do not treat editing the source alone as completion; a user in the installation destination checks the changed state.

## Common misunderstandings

| Misunderstanding | What to check |
|---|---|
| In-organization installation adds components to the source Project | Use a separate installation destination Project. |
| If the App is installed, every user can use it | Check members, access, and individual settings. |
| Updating the source automatically updates the installation destination | Preparing and applying a new version, then checking it in the installation destination, are required. |
| A completed-setup display also confirms business use | Separately check the user entry point, connections, and representative operations. |

## If something does not work

| Situation | First thing to check |
|---|---|
| **"App publishing"** or **"App in this organization"** is missing | Check the target organization, source Project, permissions, and availability conditions. See [Index of limits and availability conditions](../operations/limits-and-availability-index.md). |
| Resource checking shows unresolved references | Check fixed IDs, source-specific URLs, dependent resources, and handling of connections and Secrets. See [Design configuration for distributable Pages](../development/design-distributable-page-config.md). |
| The installation result is unclear | Do not run the same action repeatedly; check version and status in [Creator self-installs within the organization](../distribution/self-install-in-organization.md). |
| A user cannot open it or the response does not match | Check the installation destination Project, members, App settings, and target resources. Do not work around this by expanding source Project permissions. |
| The display does not change after updating | Separately check the source change, prepared version, installation destination version, and user result. See [Check App update changes and reconfiguration](../use/check-app-updates.md). |

## Cleanup

1. Tell practice members that the exercise is complete and confirm that there are no practice deliverables that need to be retained or handed over.
2. For the App in the installation destination, select **"Uninstall app,"** check the App name and guidance, and confirm. Do not delete the source Project.
3. After completion, confirm that the App is unavailable. Even if the result is unclear, do not repeatedly uninstall or reinstall it.
4. Decide whether to retain or delete the source Object, Page, and Agent, and clean them up after confirming they are not used by other exercises.

For uninstall details, see [Uninstall an App](../use/uninstall-app.md). If you created connections, Secrets, automatic executions, or API keys during this exercise even though they were not required, check their impact and assign responsibility for stopping or deleting them.

## Read next

- [Complete App setup](../use/complete-app-setup.md)
- [Check App update changes and reconfiguration](../use/check-app-updates.md)
- [Uninstall an App](../use/uninstall-app.md)
- [Design configuration for distributable Pages](../development/design-distributable-page-config.md)
- [Creator self-installs within the organization](../distribution/self-install-in-organization.md)
