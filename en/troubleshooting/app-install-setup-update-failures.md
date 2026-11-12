# App Installation, Initial Setup, or Update Fails

This page provides troubleshooting steps for when you cannot install an App, when "Setup" does not finish after installation, or when it does not work as expected after an update. It is intended for App administrators. First, treat installation from the Marketplace, in-organization self-install by the creator, post-installation configuration, and applying updates as separate stages. Accepting an installation, creating an App-specific Project, configuring connections and Secrets, and verifying user operation do not have the same completion criteria.

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
| You cannot select "Install," or a message says that you cannot install the App | The organization or installation permission is incorrect, availability or purchase conditions are not met, or the App requires an inquiry | Check the organization, App name, displayed installation method, and administrator permissions. Do not work around the issue by using another account or changing permissions merely to make it appear. | [Install an App from the Marketplace](../use/install-marketplace-app.md) |
| The App remains "Installing," and you do not know the result because of a communication error | Installation is in progress, or you left the screen or communication failed. You cannot determine from the screen alone whether installation completed. | Reopen the latest installation status for the same App, and check the installing Project and status. Do not perform additional actions while the result is unknown. Only if the screen prompts you to select "Retry," check the target and details before doing so. For paid Marketplace Apps, check the number of seats, this billing amount, and the next renewal date; you may need to agree to the purchase again. | [Install an App from the Marketplace](../use/install-marketplace-app.md) / [Self-Install in Your Organization as the Creator](../distribution/self-install-in-organization.md) |
| "App publishing," "App in this organization," or "Install in this organization" is not displayed for an in-organization installation | You are not in the correct provider Project or organization, or you lack the required action permissions or availability conditions | Check the provider Project, organization, target version, and whether resource checks are complete. Do not treat the installing Project as the provider or expand permissions based on your own judgment. | [Self-Install in Your Organization as the Creator](../distribution/self-install-in-organization.md) / [Deploy an App in Your Organization](../labs/deploy-app-in-organization.md) |
| A resource check fails, or required functionality is missing after installation | Unresolved references, missed selections, fixed IDs, provider-specific URLs, or handling of connections and Secrets do not suit the installing organization | Confirm with the creator the dependent resources, settings that are not specific to a distribution destination, and credentials to configure after installation. Do not resolve the issue by including secret values in distribution materials or initial data. | [Self-Install in Your Organization as the Creator](../distribution/self-install-in-organization.md) |
| You cannot complete "Setup," check its status, or change settings | Shared connectors, personal connections, Project Secrets, members, or App-specific preparation are incomplete; a verification process failed; or you lack action permissions | Open the App and identify which settings have not been verified. Also check connector requirements added or changed by an installation or update. If you still cannot verify them after reloading, do not conclude that there are no requirements; ask the person responsible for configuration. | [Complete the Initial Setup of an App](../use/complete-app-setup.md) |
| Processing fails even after setup is complete, or only administrators can use the App | The user's own connection, App access permission, permission to access target data in the external service, or App-specific conditions are insufficient | Compare permitted test actions for the administrator and affected users. Check App-wide connections and personal connections separately, and separately check App members and permissions in the external service. | [Complete the Initial Setup of an App](../use/complete-app-setup.md) |
| There is an update notice, but it is not applied to the installing organization, or the version in the installing organization differs | You are confusing the Marketplace or provider version, installation method, or application status | Check the provider's changes and the installation status of the target organization and App. Do not reinstall before identifying the cause; ask the distribution owner to check the application status. | [Check What Changed in an App Update and What to Reconfigure](../use/check-app-updates.md) |
| Missing settings are indicated after an update, a connection stops, or only some users fail | The update changed connections, Secrets, input formats, access permissions, or work procedures | From the change notice, identify shared connections, personal connections, Project Secrets, members, data, and notifications as items to check. Do not consider only an administrator's success as completion; verify operation with representative users. | [Check What Changed in an App Update and What to Reconfigure](../use/check-app-updates.md) |

## Check by stage

1. **Before and during installation**: Check the organization, App, installation method, installation permissions, pricing, and availability conditions. If the installation result is unknown, first retrieve the latest status.
2. **Immediately after installation**: Check the App-specific Project and App in the installing organization. Do not confuse them with the provider Project, another App with the same name, or a previous installation.
3. **Initial setup**: Based on the App guidance, have the responsible people check shared connectors, users' own connections, Project Secrets, members and access permissions, and App-specific preparation.
4. **Usage verification**: Using representative actions that avoid external updates, test the flow in which intended users, not only administrators, open, run, and check results.
5. **After an update**: Check the changes, the installing organization's status, reconfiguration, and actual work procedures in that order. Even with automatic updates, separately check whether connections and output work as expected.

> Even if "Installed" is displayed and you can select "Open," this does not mean that all connection, Secret, and member settings are complete. Conversely, not finding a settings screen is not evidence that configuration is unnecessary. Compare the guidance for the target App with its current status.

## Common misconceptions

### Reinstalling also fixes configuration problems

Access to an installed App, initial setup, and applying updates are separate issues. Reinstalling first makes it harder to trace the cause and assess the impact on existing data and users. Record the status and displayed messages before proceeding with the necessary steps.

### Users can start using the App because an administrator was able to run it

Personal connections, membership in the App, and permissions in external services may differ by user. Separate App-wide settings from user-specific settings, and check with representative users.

## Before consulting an administrator or support

Excluding confidential information, gather the organization, App name, installation method, identifying information for the installing Project, occurrence date and time and time zone, screens and actions used, displayed status or error, the stage of installation, configuration, or update, affected users, checked connection, Secret, and member status, update notices, and whether you retried. For the inquiry template and information that must not be sent, see [Collect the Information Needed for an Inquiry](collect-info-for-support.md).

## Read next

- [Complete the Initial Setup of an App](../use/complete-app-setup.md)
- [Check What Changed in an App Update and What to Reconfigure](../use/check-app-updates.md)
- [Self-Install in Your Organization as the Creator](../distribution/self-install-in-organization.md)
- [Uninstall an App](../use/uninstall-app.md)
- [Collect the Information Needed for an Inquiry](collect-info-for-support.md)
