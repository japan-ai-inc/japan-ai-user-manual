# Check What Changed in an App Update and What to Reconfigure
App updates may change not only screens and functions, but also required connections, secrets, and business operation procedures. In this article, we will explain how to proceed by checking the changes in the installed app and completing the necessary reconfiguration and operation verification.
**``A new version has been published,'' ``It has been reflected in my organization,'' and ``I have confirmed that it can be used for work.'' are different states. ** Please do not assume that the update is ready for use just by looking at the Marketplace information or version display.
This article is intended for app administrators in deployments. The author's internal self-install update operations, release review/distribution, and compatibility management are covered in articles for distribution staff.
## Before you start
- Check the target organization, app, and provider.
- If there is an update notice, check the changes, target version, and how they are reflected in the installation site.
- Organize the functions you use in your work and the users who will be affected.
- Confirm the person in charge of setting the common connection/Secret and the person in charge of determining continued use.
- Decide on the data to be used for operation confirmation and the operations that can be performed.
> Screen display and operation range vary depending on organization settings, permissions, and app configuration. Below is the order of confirmation. We do not assume that there is a common "update" button for all apps or a screen where users must approve updates.
If you have not completed the initial setup yet, please check [Complete the initial app setup](complete-app-setup.md) first.
## 1. Check the update information and the installation status separately.
### Check for updates
If the Marketplace details screen shows "new features" or changes, read them along with the target version. If you do not understand the changes based on the posted content, please check with the provider or distributor.
In particular, check whether the following changes are relevant to your business:
| Type of change | Things to check |
|---|---|
| Screen/operation procedure | Will the entrance, input items, execution button, and method of checking results change? |
| Agent/Workflow | Will the input conditions, output, processing to be executed, and operations on external services change? |
| Connection/Authentication | Will the destination service, user account, required permissions, and personal/common usage change? |
| Project Secret | Do new items or values/assignments need to be reviewed? |
| Data files | Are there any changes to the input/output format, storage location, or handling of existing data? |
| Operation | Are there any changes to automatic execution, notifications, and user guidance? |
This is from the perspective of confirming the impact. Not all updates change these.
### Check the status of installed apps
1. Open the target deployed app in the correct organization.
2. If the version or installation status is displayed in the app description field, etc., check the contents.
3. Check the target of the update guide and the version displayed in the installation destination.
4. If it is not displayed, the version is different, or you cannot determine whether it has been reflected, check with the person in charge of distribution to confirm the update status in the target organization.
**Do not confuse the version listed in the Marketplace with the version enabled in your deployment. ** Even if an app's status is "enabled", it does not indicate that it has been updated to the latest version as expected.
### Don't skip confirmation even with automatic updates
For apps that have been installed other than internal self-install, updates may be automatically distributed to installation sites that meet the distribution conditions when the provider switches to a stable version. However, it is not always the case that the release of a new version will be completed at the same time as the new version is released.
Self-install within an organization is not subject to this automatic distribution. Update operations on the creator side are handled by [Update self-install within the organization](../distribution/update-self-install.md).
If you can't find an update, instead of reinstalling from scratch or adding another app, check your current deployment status and update targets.
## 2. Organize the impact on business and the person in charge of confirmation
After reading the change notice, replace it with items that you would check in your own work. Items whose changes are unknown will be left as "unconfirmed" instead of "no impact."
| Items to check | Contents to record |
|---|---|
| Business or function affected | Which Pages, Agents, Workflows, and other features are used |
| Expected impact | Changes to procedures, added connections, changes to input formats, and so on |
| Verification owner | The App administrator, connection administrator, the user, and so on |
| Verification method | What to run with which data, and what constitutes success |
| Use decision | Verified, awaiting verification, or use partially suspended |
If you know the status before the update, recording the version in use, the connection name and type, and normal processing results makes comparison easier. If the update has already been applied, proceed with verification using the information available.
What is recorded is the setting items and status, not the API key, password, or secret value. Also, keeping a record for confirmation does not guarantee that you can return to the original version.
## 3. Check the connection/Secret after updating
Reopen the app and check the instructions such as ``Requires settings'' and ``Check settings'' as well as ``Connector'' and ``Secret management'' in the app settings.
Instead of redoing all authentication for every update, address only what is necessary based on the current requirements and state. Conversely, do not decide that no settings review is necessary after an update solely because the App worked before.
### App-wide connections
- Check whether any connections required by the App are unconfigured or invalid.
- Confirm that the destination service, account, and organization are the intended ones.
- If new permissions are requested, confirm that their scope and purpose are approved.
- Confirm that the connection was not only created or changed, but also assigned to the App.
If you do not have administrative permission, ask the person responsible for managing connections. Setup procedures are covered in [Configure shared connections for a Project or App](../operations/configure-shared-connections.md), and responses to expiration or a change of owner are covered in [Handle expired connections and owner changes](../operations/handle-expired-connections-and-owner-changes.md).
### User connection
Apps that require a personal connection also check who you are. Even if the administrator's connection is working, it does not mean that other users are authenticated.
If personal connection or additional permission is required, we will notify the target user. Do not substitute administrator accounts or confidential information for distribution. Specific operations are handled in [Connect your own external account](../operations/connect-your-external-accounts.md).
### Project Secret
- Check the Secret items required by the app.
- If there are new or unconfigured items, the person in charge will confirm the required values ​​and usage.
- Check the destination Secret and its association with the item referenced by the app.
- After setting, re-obtain the status and check if there are any unset or invalid settings remaining.
Project secret and web login secret are separate settings. Don't use values ​​of different types or environments, even if they appear to have the same name. Registration/update operations are handled in [Register/Update/Delete Project Secret](../operations/manage-project-secrets.md).
### How to read the configuration status
| Display/Status | Correspondence |
|---|---|
| Not set | Check the required connection/Secret and assignment destination |
| Confirming | Reconfirm after judgment is complete |
| Invalid/Suspended | Check the target items and instructions and request the person in charge to take action |
| Setup/Connected | After checking the setting status, try the necessary business operations |
| Settings cannot be confirmed/loading fails | Do not judge that settings are not necessary, re-display or check with the person in charge |
The display of completion of sharing settings does not guarantee the personal authentication of all users or the success of processing by external services.
## 4. Check your daily work to the extent permitted.
In addition to the changed features, we'll also review important operations you'll use on a daily basis. Depending on the app configuration, choose what you need from the following perspectives.
| Target | Confirmation example |
|---|---|
| Page | Do you want to open the screen? Are input, result display, and screen transitions as per business expectations? |
| Agent | Can the required request be carried out? Are the connections, answers, and deliverables appropriate? |
| Workflow | Can it be executed with the expected input? Can I check the processing results and history? |
| Object/Storage | Is it possible to access the necessary data/files? Is the storage location appropriate? |
| External linkage | Is it processed using the intended account and target data |
| Automatic execution/notification | If you are using it, can you check the execution results and notification destination after updating? |
**Please do not send emails, update or delete external data without permission, simply as a confirmation. ** Use test data and approved destinations to decide in advance how far you can operate.
We will ask not only the administrator but also actual users, if necessary, to verify their own accounts. Don't try to temporarily expand your privileges to succeed, but check your original terms and conditions.
If you do not know the execution result, check the history and save location before deciding whether to rerun. Reloading the screen and rerunning the business process are different. Please do not repeat operations that lead to double submission/double registration.
## 5. Communicate changes and remaining work to users
Once verified, please share at least the following:
- Target App and confirmed version or update status
- Changes related to users
- Tasks requested from the user, such as reconnecting the user
- Functions whose operation has been confirmed and functions whose operation has not been confirmed yet
- If there is a range of usage that has to be suspended, what is that scope?
- Contact information
Instead of giving a blanket message saying, "It's been updated, so there's no problem," we'll specifically tell you what we've confirmed. If there is any remaining work, please clarify the person in charge and precautions for use.
## When things don't go well
| Situation | Things to check first |
|---|---|
| Marketplace version and installation destination version are different | Target organization/app, installation method, and reflection status confirmed by the distribution person |
| There is an update guide, but I don't know what the changes are. | Provider's guide. Don't judge lack of information as "no change" |
| Settings required is displayed after updating | Current connector/Project Secret requirements and assignments |
| Can be used by administrators, but not by users | Personal connection, App access rights, and external service permissions |
| Processing fails even though settings have been made | User account, target data, App-specific conditions, runtime error |
| Automatic execution and notifications do not work | Operational changes to connection status, execution history, notification destination, and update guide |
| If the update failed or you don't know the update status | Request the person in charge of distribution to check the status. Do not try reinstalling first |
Please include the organization name, app name, displayed version/status, date and time of occurrence, operation details, error, and affected users/functions in your inquiry. If the information cannot be confirmed, please simply say that it is not confirmed.
Do not remove connections, secrets, or uninstall apps without investigating the cause. Even if it is necessary to return to the original version, it is not always possible for the user to do so, so consult with the person in charge of distribution.
## Completion check
- [ ] Confirmed the target organization/app and the implementation status at the implementation site.
- [ ] Confirmed the update information and the impact on business, and left unclear points as unconfirmed.
- [ ] Confirmed the required common connection, personal connection, and project secret.
- [ ] Do not mistake failure to confirm settings or failure to read as completion of settings.
- [ ] Confirmed critical business operations with actual usage conditions and permitted data.
- [ ] Informed users of the changes, required work, scope of use, and contact information.
## Related articles
- [Complete initial app setup](complete-app-setup.md)
- [Uninstall App](uninstall-app.md)
- [Open the installed app and start using it](open-installed-app.md)
- [Connect your external account](../operations/connect-your-external-accounts.md)
- [Set up a shared connection for Project/App](../operations/configure-shared-connections.md)
- [Respond to connection expiry/change of person in charge](../operations/handle-expired-connections-and-owner-changes.md)
- [Register/Update/Delete Project Secret](../operations/manage-project-secrets.md)
- [Update internal self-install (for creators)](../distribution/update-self-install.md)
- [App installation/initial settings/update fails](../troubleshooting/app-install-setup-update-failures.md)
