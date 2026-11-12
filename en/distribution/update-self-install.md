# Update an In-Organization Self-Install

This article is for creators who want to apply source Project changes to an App installed through in-organization self-install. Updating does not directly share the source or install the App again as new. It applies a prepared new version to the **same installation destination Project**.

Before updating, check changed resources, access settings, credential requirements, and the impact on users. For checking changes, reconfiguring connections and Secrets, and verifying business operations performed by App users and administrators, see [Check What Changed in an App Update and What to Reconfigure](../use/check-app-updates.md).

## Related articles

- [Self-Install in Your Organization as the Creator](self-install-in-organization.md)
- [Check What Changed in an App Update and What to Reconfigure](../use/check-app-updates.md)
- [Deploy an App in Your Organization](../labs/deploy-app-in-organization.md)
- [Design the Configuration of a Distributable Page](../development/design-distributable-page-config.md)
- [Plan a Migration Between Projects](../operations/plan-project-migration.md)

## Scope of this article

This article is for a **creator** who created an App in the organization and has already self-installed it into the same organization. It covers preparing an updated version from the App publishing screen and applying it to the existing installation destination Project.

Marketplace publishing review, distribution to other organizations or customers, and initial setup performed only by users are outside this scope. Depending on organization settings or permissions, "App publishing" or "App in this organization" may not appear, or preparation and updating may be unavailable. A Project editor or administrator with the required permissions can prepare, install, and update.

> Merely editing resources in the source does not change the installed App. Prepare the version to update, check its contents, execute "Update installed app," and check the result at the installation destination.

## Before you begin

- Check the target organization, source Project, App name, and installation destination Project. Do not target another App or source Project with a similar name.
- Record the current "Installed version," users, Pages, Agents, Workflows, external integrations, and scheduled runs in use. Do not record or share API keys, passwords, Secret values, or tokens.
- Separate changes into additions, deletions, and modifications. In particular, when changing roles, access policies, or Object record access rules, check in advance who can read and update what after the update.
- Decide the time period that affects users, the person responsible for checking, and a validation method that does not involve external sending or data updates.

## 1. Check source changes

1. In the source Project, check the resources and dependencies to distribute. Do not leave source-specific IDs, URLs, connections, or Secret values fixed. For design checks, see [Design the Configuration of a Distributable Page](../development/design-distributable-page-config.md).
2. Open **"App publishing"** in Project Settings, then select the target App under **"App in this organization."**
3. Compare the destination's "Installed version" with the changes you will apply. If the current version is latest, do not perform an unnecessary update.
4. Prepare changes, business operations that require checking, potential reconfiguration, and an inquiry contact for users. For the user-side process, see [Check What Changed in an App Update and What to Reconfigure](../use/check-app-updates.md).

## 2. Prepare a new version

1. Run **"Check app resources."** This check verifies whether selected resources can be installed in the organization; it is not submission to Marketplace publishing review.
2. In the result, check added, deleted, and changed resources. Check not only counts but also displayed names and the impact on work at the installation destination.
3. Compare credential and connector requirements between the current and new versions. Check whether "Shared by the project" or "Personal to each user" is required, the connectors added, changed, and deleted, and the settings or handover targets.
4. If guidance about changes to access settings appears, check roles, access policies, and record access rules for target resources. Do not apply a change as-is if it makes the App unavailable to users or broadens access to unnecessary users.
5. When "Resource check completed." appears, check the version to prepare, number of included components, and changes. If the check fails, correct unresolved references, dependencies, and settings, and do not proceed with the update until it succeeds.

> "Check app resources" does not guarantee that all business processing at the installation destination, communication with external services, or authentication for all users succeeds. You must check under actual usage conditions after updating.

## 3. Apply it to the installed App

1. Check the prepared version and select **"Update installed app."**
2. In the confirmation screen, compare **"Version to install,"** **"Current installed version,"** the content to check, number of included components, and credential slots.
3. Check added, deleted, and changed resources; credential requirements; and the effects of access settings again.
4. If there are no issues, confirm **"Update installed app"** in the confirmation screen.
5. If "Request accepted. Check whether the new version is active." appears, do not continue with the same operation. While processing, "Installation or update is in progress. This page will refresh its status." appears.
6. After completion, check that **"Installed version"** is the intended version, then open the installation destination Project from **"Open installed app."**

Follow [Check What Changed in an App Update and What to Reconfigure](../use/check-app-updates.md) for post-update user checks, connections and Secrets, business-operation testing, and user guidance. Step 5 of [Deploy an App in Your Organization](../labs/deploy-app-in-organization.md) is an example of checking a small Page change as an update.

## What is retained and updated

On the update confirmation screen, you can check the current and updated versions, resource additions, deletions, and content updates; connector changes; access-setting changes; and Project Secret requirements. Whether existing users, credentials, and customer-owned data are retained or can be changed is not guaranteed by this screen alone. Managed resources and access rights included in the updated version migrate according to differences checked on the preparation screen. Handle them separately as follows.

| Target | Handling during update | What the creator checks |
|---|---|---|
| Installation destination Project | Apply to the same Project; do not create it again. | Do not confuse the source Project with the installation destination Project. |
| Users, credentials, and customer-owned data | Whether they are retained is not shown on the update confirmation screen. They follow migration specifications for managed resources and the current state at the installation destination. | For updates affecting important data, check in a validation environment before performing them. Also check required shared credentials, each user's personal connection, and business data after updating. |
| Managed resources | Additions, deletions, and changes in the new version are applied. | Check changes to Pages, Agents, Workflows, Objects, Storage, and so on. Do not assume that data outside management at the installation destination will be overwritten. |
| Access rights | Changes to roles, access policies, and record access rules included in the version have an effect. | With the minimum set of actual users, check read, run, and update availability before and after the update. |
| Initial data and files | Initial content managed by the App may be added, changed, or deleted according to version migration rules. | Design it so that it does not use the same location or name as data or files entered or created by users. Stop the update if a conflict occurs. |

When a version changes credential requirements, repairing existing shared credentials or each user connecting their personal account may be required. Do not distribute credential values to users or include them in screens, logs, or inquiries. Separate the roles of configuration owners and users, then check by following [Check What Changed in an App Update and What to Reconfigure](../use/check-app-updates.md).

## If something does not work

| Situation | Check and action |
|---|---|
| The update button is unavailable, or "Please prepare a new version." appears | Check the target App, source Project, prepared version, and permissions. First run "Check app resources" and check the prepared version. |
| Resource check fails | Check unresolved references, dependency resources not included in distribution, and fixed environment-specific settings. Do not change installation-destination permissions or Secrets based on assumptions. |
| You cannot determine conflicts or differences | Check resources, credentials, and access settings in the current and prepared versions. In particular, when changing the same target as user data or initial data, organize the impact before preparing again. |
| The result is unknown or communication is interrupted | Do not run it again immediately. Use **"Check installation status again"** to check the current version and status. The screen advises you to check the current version and status before retrying because completion may be possible. |
| "The previous deployment failed or additional action is required." appears | Check the current version and displayed error. If necessary, use **"Manage credentials"** to repair credentials, then consider **"Retry deployment"** or preparing a corrected new version. |
| Users cannot use it after updating | Separately check the App version, access rights, shared credentials, the user's personal connection, Project Secret, and run result. Follow [Check What Changed in an App Update and What to Reconfigure](../use/check-app-updates.md) and check with the user's original permissions. |

### About rollback

If an update fails, the application process restores managed resources to their original state up to the point at which the process failed. However, this scope could not confirm an operation for a creator to select and restore any past version from the screen. Also, do not conclude from a display alone that rollback itself completed or that user data, external-service operations, and authentication state were restored.

Therefore, when a failure occurs, do not reinstall or repeatedly update first. Use **"Check installation status again"** to check the current version and status. Record the error, target organization, App name, source/installation destination Project, operation date and time, changed resources, and differences in credential requirements, then consult the distribution owner or support. Do not send Secret values, API keys, tokens, passwords, or customer data.

## Completion checklist

- [ ] I checked the target organization, source Project, App, installation destination Project, and current version.
- [ ] In "Check app resources," I checked additions, deletions, changes, and dependencies.
- [ ] I checked credential requirements and changes to roles, access policies, and record access rules.
- [ ] In the "Update installed app" confirmation screen, I compared the current version with the prepared version.
- [ ] After updating, I checked the installed version and representative operations in the installation destination Project.
- [ ] I told users and configuration owners the changes, required reconfiguration, and checked scope.
- [ ] If the result was unknown or failed, I used "Check installation status again" before rerunning.

## Next article

- [Check What Changed in an App Update and What to Reconfigure](../use/check-app-updates.md)
- [Deploy an App in Your Organization](../labs/deploy-app-in-organization.md)
- [Design the Configuration of a Distributable Page](../development/design-distributable-page-config.md)
- [Plan a Migration Between Projects](../operations/plan-project-migration.md)
- [Self-Install in Your Organization as the Creator](self-install-in-organization.md)
