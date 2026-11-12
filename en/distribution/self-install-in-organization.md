# Self-Install in Your Organization as the Creator

This article explains how creators bundle resources from a source Project into an App and install it into an **installation destination Project separate from the source Project** in the same organization. In-organization self-install does not require JAPAN AI review or Marketplace listing. After installation, manage users and access rights separately from the source Project.

This operation lets creators check distribution content. To install a selected App from the Marketplace, see [Install an App from the Marketplace](../use/install-marketplace-app.md). [Complete the Initial Setup of an App](../use/complete-app-setup.md) covers required connections, Secrets, and per-user preparation after installation.

## Related articles

- [Constraints on Distribution Scope, Dependencies, and Authentication Settings](distribution-scope-dependencies-auth.md)
- [Complete the Initial Setup of an App](../use/complete-app-setup.md)
- [Install an App from the Marketplace](../use/install-marketplace-app.md)
- [Deploy an App in Your Organization](../labs/deploy-app-in-organization.md)
- [Design the Configuration of a Distributable Page](../development/design-distributable-page-config.md)
- [Manage Project and App Members](../operations/manage-project-and-app-members.md)

## Before you begin

Check the following before you begin:

- You have opened the correct organization and the source Project that provides the distribution.
- You have decided the resources, initial data, and home screen to include in the App. Review resource dependencies, fixed IDs, provider-specific URLs, external connections, and Secrets in [Constraints on Distribution Scope, Dependencies, and Authentication Settings](distribution-scope-dependencies-auth.md) and [Design the Configuration of a Distributable Page](../development/design-distributable-page-config.md) in advance.
- The person performing the operation can edit the target Project and perform an in-organization installation. Being able to view the screen does not necessarily mean they can prepare or install it.
- You have decided who configures connections and Secrets after installation and who adds users.
- You have selected users, separate from the source, to check the installation result. Use approved test data rather than production data or credentials.

> In-organization self-install does not add components to the source Project. A dedicated installation destination Project is created, and the source Project remains separate. Do not conclude that connections, access, and business operations at the installation destination are checked merely because it worked in the source.

## 1. Create an App from the source Project

1. In the source Project, open "Project Settings" and select "App publishing."
2. Under "Publishing destination," select "Within organization." Do not select "Marketplace."
3. Under "App information," enter "App name" and select "Create App." When using an existing App, check that the target App belongs to this source Project.
4. After creation, compare the App name and source Project. If the organization, Project, or App name is incorrect, do not proceed; switch to the correct target and check it.

Choose an App name that users at the installation destination can identify. Creating an App and preparing a version that can actually install resources are separate operations.

## 2. Select and check resources to distribute

1. Under "Resources to publish," select the resources to include in the App. Select "Initial storage data" as needed.
2. When users should open a Page first, check "First screen" and the target Page under "Home page." When using Page navigation, include only Pages users can view.
3. Run "Check app resources." This operation checks whether selected resources can be installed in the organization; it is not Marketplace review submission.
4. When "Resource check completed." appears, check the selected content again. If unresolved references, warnings, or loading failures exist, correct the cause and check again.

> Before choosing "Include all," check that it does not include unnecessary resources, test data, or settings used only by the provider. Do not include Secret values, API keys, tokens, cookies, or connection IDs in the distribution package or initial data. Prepare Apps that need connections or Secrets on the assumption they will be configured at the installation destination.

## 3. Prepare the version to install

After checking resources, select "Install in this organization." The first time, an installation version is prepared from the selected content. If the same content is already installed, "Up to date" may appear.

After preparation, check at least the following on the confirmation screen:

| Check item | What to check |
|---|---|
| Version to install | It is the version prepared from this source Project |
| Number of included components | It does not differ substantially from the expected number of resources |
| Resource changes | There are no unnecessary items among additions, deletions, and changes |
| Access settings | You understand whether roles, access policies, or record access have changed and their impact |
| Credentials and connectors | You checked which are configured as shared and which per user, added, changed, and deleted connectors, and the post-installation owner |

The confirmation screen may separately display resource additions, updates, and deletions; connector changes; effects on credentials; and access settings. Do not decide based only on counts; expand them and check target names. When credential requirements are displayed, do not display or enter the values themselves on the confirmation screen. Configure required connectors and Secrets after installation. If any content is unclear, return with "Cancel" and review resource selection and distribution design.

## 4. Install in the organization

1. On the confirmation screen, check the App name, version, changes, access settings, and credential requirements once more.
2. If there are no issues, confirm "Install in this organization."
3. While "Installation or update is in progress. This page will refresh its status." is displayed, do not repeat the same operation.
4. Check that "Installed version" and "Open installed app" are displayed.
5. Select "Open installed app" and check that you can open the installation destination Project separate from the source.

> Even if the result is unknown because of a network error, leaving the screen, or no response, do not run it again immediately. Use "Check installation status again" to check the current version and status. An indication that processing was accepted does not mean installation or setup is complete.

## 5. Check initial setup, members, and usage

After opening the installation destination Project, separately proceed with installation checks and preparation for business use.

1. If "Complete setup" appears, select it and check remaining connections, Secrets, and App-specific settings. Follow [Complete the Initial Setup of an App](../use/complete-app-setup.md) for details.
2. Unless connections or Secrets are shown as unnecessary, do not assume that the source's authentication state can be carried over. Configure approved connections and values for the installation destination.
3. From "Settings" in the installation destination Project, open "Members," add users, and set the minimum necessary "Roles." For details, see [Manage Project and App Members](../operations/manage-project-and-app-members.md).
4. Open the App using intended user accounts as well as administrator accounts. Check the home screen, main resources, required connections, and results of representative business operations.
5. Share the checked version, configured items, features that may be used, outstanding tasks, and inquiry contact with users and operations owners.

Belonging to the organization and being able to use the installation destination Project are different. Also, do not treat the setup-complete display alone as success for each user's personal connection or business operations.

## Completion checklist

- [ ] I created the App from the correct organization and source Project.
- [ ] I selected and checked only the required resources, initial data, and home screen.
- [ ] Before installation, I checked the version, changes, access settings, and credential requirements.
- [ ] I opened a separate installation destination Project from "Open installed app."
- [ ] I checked required initial setup and did not share Secret values or credentials.
- [ ] I added intended users and set the minimum necessary roles.
- [ ] I checked representative operations and results as a user.

## If something does not work

| Situation | Check and action |
|---|---|
| "App publishing" or "App in this organization" is not displayed | Check the correct organization, source Project, operating permissions, and the organization's availability conditions. Do not change permissions or settings based on your own judgment to make it appear. |
| You cannot select "Install in this organization" | Check whether the resource check is complete, whether you can edit the target Project, and whether the App and version are available. |
| Resource check fails | Check unresolved references, omitted selections, fixed IDs, provider-specific URLs, and credential handling. For design, see [Design the Configuration of a Distributable Page](../development/design-distributable-page-config.md). |
| The installation result is unclear | Use "Check installation status again" to check the version and status before acting. Do not run the same operation repeatedly. |
| You cannot open it after installation, or only users cannot open it | Check that you opened the installation destination Project, along with members, roles, and each user's own connection. Do not work around this by expanding permissions in the source Project. |
| You do not know how to configure a connection or Secret | Do not infer requirements and enter values. Check [Complete the Initial Setup of an App](../use/complete-app-setup.md) and App-specific guidance. |

When making an inquiry, provide the organization name, App name, source/installation destination Project, displayed version and status, operation date and time, and what you have checked. Do not send Secret values, API keys, tokens, cookies, or production data.

## Next article

- [Complete the Initial Setup of an App](../use/complete-app-setup.md)
- [Manage Project and App Members](../operations/manage-project-and-app-members.md)
- [Deploy an App in Your Organization](../labs/deploy-app-in-organization.md)
- [Update an In-Organization Self-Install](update-self-install.md)
- [App Installation, Initial Setup, or Update Fails](../troubleshooting/app-install-setup-update-failures.md)
