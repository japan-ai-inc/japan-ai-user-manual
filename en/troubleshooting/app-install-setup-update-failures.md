# App Installation, Initial Setup, or Update Fails

This page provides troubleshooting steps for when you cannot install an App, when "Setup" does not finish after installation, or when it does not work as expected after an update. It is intended for App administrators. First, treat installation from the Marketplace, in-organization self-install by the creator, post-installation configuration, and applying updates as separate stages. Accepting an installation, creating an App-specific Project, configuring connections and Secrets, and verifying user operation do not have the same completion criteria.

## Key points: who can do what

| Item | Rule |
|---|---|
| Install, uninstall, and the "Organization Apps" tab | Organization administrators, or people with the App install permission (`jai.studioapp.install`). |
| The person who installs | Automatically becomes an App administrator (uses one seat for a paid App). |
| Shared connections, Secrets, and members | Only App administrators can configure them. |
| How general members start using an App | An App administrator adds them as members, or they open for the first time an App with "Publish to all organization members" enabled (free Apps), which joins them as members. |
| Pre-installed Apps waiting for installation | Installed the first time someone selects "Open". They can also be deleted. |
| Updates | Apps from the Marketplace and pre-installed Apps are updated automatically after a new official version is published. You cannot choose updates or roll back. Check the current version in the App header ("v… · installation status"). |
| How connections are used | Agents use "the runner's personal connection → the Project's shared connection → the organization-wide connection," in that order. Workflows use shared connections only. A connection declared as personal in an App does not fall back to a shared connection when there is no person running it. |

## Related articles

- [Install an App from the Marketplace](../use/install-marketplace-app.md)
- [Complete the Initial Setup of an App](../use/complete-app-setup.md)
- [Check What Changed in an App Update and What to Reconfigure](../use/check-app-updates.md)
- [Uninstall an App](../use/uninstall-app.md)
- [Self-Install in Your Organization as the Creator](../distribution/self-install-in-organization.md)
- [Deploy an App in Your Organization](../labs/deploy-app-in-organization.md)
- [Collect the Information Needed for an Inquiry](collect-info-for-support.md)

## Before you begin

Record the organization where you are installing the App, the App name, installation method, administrator who performed the action, status shown on the screen, and date and time of occurrence. The provider Project and the Project of the installed App are different. Do not conclude that the App is available in the installing organization merely because it worked for the provider or showed "Installing."

> Do not include connection IDs, Project API keys, Secrets, tokens, cookies, or external service credentials in configuration fields, screen captures, or inquiries. Before trying to resolve the issue by reentering values, check the App-specific guidance to determine which settings are required.

## What to check first

- [ ] I opened the correct organization and target App and confirmed whether the installation is from the Marketplace or an in-organization self-install.
- [ ] I checked the installation status and whether an App-specific Project was created in the installing organization.
- [ ] When the App shows "Installing" or the result is unknown, I have not repeatedly performed the same installation action.
- [ ] I checked separately the connections, Project Secrets, members, and App-specific preparation in the App guidance.
- [ ] For an update, I distinguished between the provider's changes, the version applied to the installing organization, and users' actual operation.
- [ ] I checked the effect that retrying or reinstalling will have on existing settings, data, and users.

## Troubleshoot by symptom

| Symptom or displayed message | Main cause | What to check and do | Detailed article |
|---|---|---|---|
| You cannot select "Install," or a message says that you cannot install the App | The organization is incorrect, you lack install permission (organization administrator or `jai.studioapp.install`), availability or purchase conditions are not met, or the App requires an inquiry | Check the organization, App name, displayed installation method, and your install permission. Do not work around the issue by using another account or changing permissions merely to make it appear. | [Install an App from the Marketplace](../use/install-marketplace-app.md) |
| The App remains "Installing," and you do not know the result because of a communication error | Installation is in progress, or you left the screen or communication failed. You cannot determine from the screen alone whether installation completed. | Reopen the latest installation status for the same App, and check the installing Project and status. Do not perform additional actions while the result is unknown. Only if the screen prompts you to select "Retry," check the target and details before doing so. For paid Marketplace Apps, check the billing amount and next renewal date shown on screen, and if you are asked to agree to the purchase, review the details before proceeding. The monthly fee for a paid App is the unit price × the number of active members. | [Install an App from the Marketplace](../use/install-marketplace-app.md) / [Self-Install in Your Organization as the Creator](../distribution/self-install-in-organization.md) |
| "App publishing," "App in this organization," or "Install in this organization" is not displayed for an in-organization installation | You are not in the correct provider Project or organization, or you lack the required action permissions or availability conditions | Check the provider Project, organization, target version, and whether resource checks are complete. Do not treat the installing Project as the provider or expand permissions based on your own judgment. | [Self-Install in Your Organization as the Creator](../distribution/self-install-in-organization.md) / [Deploy an App in Your Organization](../labs/deploy-app-in-organization.md) |
| A resource check fails, or required functionality is missing after installation | Unresolved references, missed selections, fixed IDs, provider-specific URLs, or handling of connections and Secrets do not suit the installing organization | Confirm with the creator the dependent resources, settings that are not specific to a distribution destination, and credentials to configure after installation. If a value that looks like a fixed ID stops the check and you have confirmed it is fine in the installing organization, you can pass it with "Recheck as reviewed." Do not resolve the issue by including secret values in distribution materials or initial data. | [Self-Install in Your Organization as the Creator](../distribution/self-install-in-organization.md) |
| You cannot complete "Setup," check its status, or change settings | Shared connectors, personal connections, Project Secrets, members, or App-specific preparation are incomplete; a verification process failed; or you are not an App administrator (only App administrators can configure shared connections, Secrets, and members) | Open the App and identify which settings have not been verified. Also check connector requirements added or changed by an installation or update. If you still cannot verify them after reloading, do not conclude that there are no requirements; ask the person responsible for configuration. | [Complete the Initial Setup of an App](../use/complete-app-setup.md) |
| Processing fails even after setup is complete, or only administrators can use the App | The user is not an App member, lacks their own connection, lacks permission to target data in the external service, or App-specific conditions are not met. Workflows and automated runs use shared connections only, and connections declared as personal do not fall back to shared connections. | Compare permitted test actions for the administrator and affected users. Check App-wide connections and personal connections separately, and separately check App members and permissions in the external service. Configure connections used by automated runs as shared connections. | [Complete the Initial Setup of an App](../use/complete-app-setup.md) |
| Opening the App shows a message that it is not available to everyone, not available on your plan, the member limit has been reached, or the App has been uninstalled | Respectively: the App is not available to all organization members and the user is not a member; the organization's plan does not include it; the App's member limit has been reached; or the App has been uninstalled | The App administrator checks member addition or "Publish to all organization members" (free Apps), the plan, the member count, and the installation status. If a message says the first launch takes about 30 seconds, wait until the first preparation finishes. | [Open an installed App and start using it](../use/open-installed-app.md) |
| There is an update notice, but it is not applied to the installing organization, or the version in the installing organization differs | A new official version has not been published yet, or you are confusing the provider's version, the installed version, or the installation method | Apps from the Marketplace and pre-installed Apps are updated automatically after a new official version is published; you cannot choose updates or roll back. Check the current version in the App header ("v… · installation status"); the Marketplace details screen shows that the App is always the latest version (auto-update). Do not reinstall before identifying the cause; ask the App provider about the update status. | [Check What Changed in an App Update and What to Reconfigure](../use/check-app-updates.md) |
| Missing settings are indicated after an update, a connection stops, or only some users fail | The update changed connections, Secrets, input formats, access permissions, or work procedures | From the change notice, identify shared connections, personal connections, Project Secrets, members, data, and notifications as items to check. Do not consider only an administrator's success as completion; verify operation with representative users. | [Check What Changed in an App Update and What to Reconfigure](../use/check-app-updates.md) |

## Check by stage

1. **Before and during installation**: Check the organization, App, installation method, installation permissions, pricing, and availability conditions. If the installation result is unknown, first retrieve the latest status.
2. **Immediately after installation**: Check the App-specific Project and App in the installing organization. Do not confuse them with the provider Project, another App with the same name, or a previous installation.
3. **Initial setup**: Based on the App guidance, have the responsible people check shared connectors, users' own connections, Project Secrets, members and access permissions, and App-specific preparation.
4. **Usage verification**: Using representative actions that avoid external updates, test the flow in which intended users, not only administrators, open, run, and check results.
5. **After an update**: Check the changes, the installing organization's status, reconfiguration, and actual work procedures in that order. Apps from the Marketplace and pre-installed Apps are updated automatically, so check after updates that connections and output work as expected.

> Even if "Installed" is displayed and you can select "Open," this does not mean that all connection, Secret, and member settings are complete. Conversely, not finding a settings screen is not evidence that configuration is unnecessary. Compare the guidance for the target App with its current status.

## Common misconceptions

### Reinstalling also fixes configuration problems

Access to an installed App, initial setup, and applying updates are separate issues. Reinstalling first makes it harder to trace the cause and assess the impact on existing data and users. Record the status and displayed messages before proceeding with the necessary steps.

### Users can start using the App because an administrator was able to run it

Personal connections, membership in the App, and permissions in external services differ by user. General members join when an App administrator adds them or through "Publish to all organization members." Separate App-wide settings from user-specific settings, and check with representative users.

## Before consulting an administrator or support

Excluding confidential information, gather the organization, App name, installation method, identifying information for the installing Project, occurrence date and time and time zone, screens and actions used, displayed status or error, the stage of installation, configuration, or update, affected users, checked connection, Secret, and member status, update notices, and whether you retried. For the inquiry template and information that must not be sent, see [Collect the Information Needed for an Inquiry](collect-info-for-support.md).

## Read next

- [Complete the Initial Setup of an App](../use/complete-app-setup.md)
- [Check What Changed in an App Update and What to Reconfigure](../use/check-app-updates.md)
- [Self-Install in Your Organization as the Creator](../distribution/self-install-in-organization.md)
- [Uninstall an App](../use/uninstall-app.md)
- [Collect the Information Needed for an Inquiry](collect-info-for-support.md)
