# Complete the Initial Setup of an App
After installing the app, prepare the necessary connections, secret information, and users, and check whether it can be used for business purposes. In this article, we will explain the order in which you should proceed with the initial setup and the things to check before deciding whether to start using it.
**“Installed,” “configured,” and “ready for business use” are different states. ** Even after opening the app, settings required for some functions may remain. Additionally, just displaying completion on the settings screen does not mean that all users have been authenticated, access to external services, or business processing has been completed successfully.
Detailed procedures for registering/changing connections, operating Secrets, and adding members are covered in each management article. Not all apps require the same settings. Follow the displayed requirements and app-specific instructions and proceed only with the necessary items.
## Before you start
- Check the target organization/app and confirm that it has been installed.
- Decide who will be in charge of the initial settings and who will be in charge of determining whether business use can begin.
- Prepare the external service you will use, the account you will use to connect, and the necessary information.
- Check who will be using the service and the range of operations they will be allowed to do.
- Check the cost and approval conditions associated with the number of users and connected services.
- Determine data for operation confirmation and operations that may be performed.
If you do not know the installation result, check the status with [Install App from Marketplace](install-marketplace-app.md). It is not always necessary to reinstall from scratch to correct missing settings.
> The following explanation focuses on the standard screen on a PC web browser. The displayed items and the range of operations that can be performed vary depending on the organization's settings, permissions, and app configuration. The order shown here is a confirmation process and is not a one-time configuration wizard that is common to all apps.
## 1. Open the app and check the remaining settings
1. Open the app in the correct organization.
2. If there are any instructions such as ``Settings required'' or ``Confirm settings,'' check the contents.
3. Check the necessary items such as "Connector" and "Secret Management" in the app settings.
4. Organize the status of each item and who will handle it.
If you see a "Complete Setup" entry on the Marketplace details screen, proceed from there. No matter which screen you enter from, make sure you are using the correct app.
| Examples of display/status | Judgment and next action |
|---|---|
| Not set | Check required connections and secret assignments |
| Confirming | Check whether the judgment is complete. Do not treat as set |
| Disabled/Suspended | Check the explanation of the target item and request the person in charge to take action |
| Set | A state in which the setting status of the item can be confirmed. Check the actual usage later |
| Settings cannot be confirmed/loading fails | Do not judge that there are no requirements, redisplay or check with the person in charge |
| You do not have the authority to change the settings | Request a person in charge who has operational authority |
Even if you are prompted to configure settings, it does not mean that the entire app is unusable. Functions that depend on unconfigured items may not be available. However, please do not announce that you can use all the functions without checking that the functions necessary for your business work.
## 2. Separate required settings by type
First, separate the common settings and the user's own settings.
| Types of settings | Things to check | Main responders |
|---|---|---|
| Common connectors for apps | Does the external service used by the app match the shared connection you assign? | Who can manage shared connections |
| Personal connectors | Should each user authenticate with their own account | The user himself |
| Project Secret | Is the secret information required by the app assigned to the specified item? | Person authorized to handle Secret |
| Members and access rights | Who can open the app and what actions they can take | Who can manage members and permissions |
| App-specific preparations | Is it necessary to specify target data, storage location, business conditions, etc.? | Person in charge specified in app-specific instructions |
Even if you use the same external service, personal and app common connections are not necessarily interchangeable. Don't assume that just because your connection is working, other users or apps have a common connection ready.
## 3. Arrange App common connections and secrets
### Check App common connections
Review the App Common Requirements under Connectors and have authorized personnel set up the required connections.
The points to check are:
- Do the services required by the app match the connection service you select?
- Is the connected account, organization, and business data the intended one?
- Are you authorized to share and use the connection with the app?
- In addition to registering the connection, have you also completed the necessary settings for the app?
**"Connector created" and "set to App" are different. ** If you see a failure to configure the app after creating it, check the created connections and current status, and follow the on-screen instructions to take action. Do not keep making the same connection without checking the cause.
Specific setting operations are handled in [Set up a shared connection for Project/App](../operations/configure-shared-connections.md).
### Check Project Secret
You may be required to set a Project Secret in order to reference API keys, tokens, etc. from your app. Under "Secret Management", please distinguish between "Project Secret" and "Web Login Secret".
- Check the Secret items and names required for the app.
- Prepare the required values ​​in an approved manner.
- On the specified settings screen, select an existing Project Secret and assign it to the required item, or create and assign a new Secret.
- Check the results of saving and linking, and check if any settings remain unset or disabled.
Simply saving a secret to a list may not have assigned it to the items required by the app. Avoid choosing another secret with a similar name or a value from a different environment.
There's no need to paste sensitive information values ​​into chat text, instructions, or screenshots of inquiries. The value will not be displayed for confirmation or distributed to other users, but will be handled on the specified settings screen.
Specific operations are handled in [Register/Update/Delete Project Secret](../operations/manage-project-secrets.md). This setting is different from the Secret for web login.
### Double check common settings
After setting, reopen the app settings screen or details screen and check the status. Even if you can configure the connector, check if there are any other types of settings remaining, such as Secrets.
For items whose settings cannot be changed, inform the person in charge of the item name and status. Please do not expand the scope of sharing or authority without permission because you are unable to respond.
## 4. Check user access and personal connections
### Allow users to open your app
Check who will be using it for work, and arrange the necessary member settings and access rights.
- Distinguish between belonging to an organization and being able to use a target app.
- Check whether not only the administrator can open it, but also the intended users.
- Set the permissions necessary for use and avoid granting unnecessary management and editing permissions.
- If you are asked to confirm the cost when adding members, please confirm the number of people, cost, and approval before proceeding.
Member operations are handled in [Manage Project/App members](../operations/manage-project-and-app-members.md), and judgment on the number of users and fees is handled in [Check the number of users, fees, and contract limits for the App](../operations/check-app-seats-pricing-and-limits.md).
### Verify your personal connection yourself
If your app requires a personal connector, each user performs the necessary authentication with their own account. Even if an administrator connects their account, it does not complete the authentication for all other users.
If "Connection Required" is displayed, the user will respond and check the status after setting up. If you see a different status, such as "Using a shared connector," check which connection you expect to use. Do not share administrator accounts or confidential information with users in situations where a personal connection is required.
Connection operations by the user are handled by [Connect your own external account](../operations/connect-your-external-accounts.md).
## 5. Perform app-specific preparations and operation checks
If the app-specific instructions include preparations such as target data, storage location, business conditions, etc., follow the instructions. There is no need to add unnecessary objects, datasets, or connections as settings are common to all apps.
Once the preparations are complete, perform a small check of the permitted range from the perspective of someone who uses it for work.
1. Decide which business operations you want to review and what results you expect.
2. Have data available for testing or review.
3. The user opens the app and operates the target function.
4. Check not only whether there are any errors, but also the referenced data, output content, and save destination.
5. If there are insufficient settings or permissions, identify the target item and return it to the person in charge.
If it involves sending emails, registering/updating external data, overwriting files, etc., even confirmation operations can affect actual operations. Check the execution destination and permission range, and if the app has a verification method, follow the instructions. Not all apps have test mode.
**Please do not complete the operation check just by opening the screen. ** For example, if you have an app that searches for documents, check to see if the intended documents can be viewed and if the results are valid. Even if external service authentication and settings display are normal, access to the target data or individual processing may fail.
## 6. Decide on the scope of use and hand it over to the person in charge
The message "Setup is complete" on the Marketplace details screen is read as a confirmation of settings for connections, secrets, etc. common to the app. **Please do not assume that each user's personal connection or the success of all business operations has been confirmed just by looking at this display. **
Once the necessary settings and operation checks are complete, we will provide the following information to users and operators.
- Organizations/Apps used and their entrances
- Functions you can start using and operations you have confirmed
- Work such as connection performed by the user himself/herself
- Unsupported items and functions to avoid using
- Contact information in case of connection failure or error
If you want to start using the service with some settings left, the functions that depend on the unconfigured items will be clearly indicated, and the person in charge will determine the scope of use. Instead of announcing that the entire setup is complete, please separate the confirmed scope and remaining work.
## When things don't go well
| Situation | Things to check first |
|---|---|
| I can't find a settings screen or item | Target organization/app, operation privileges, and app requirements. Just because it is not displayed does not mean that settings are not necessary |
| I created a connector but it remains unconfigured | Setting results for required items of the app and information on setting failures |
| I saved the Secret but it remains unset | Types of Project Secrets and linking them to required items of the app |
| Can be used by the administrator, but not by others | Member access rights, personal connection, access scope for external services |
| Unable to check the setting status | Information on loading errors and inability to check. Re-obtain the status, and if it is not resolved, check with the person in charge |
| Business operations fail even though settings have been made | Account used, target data, App-specific preparations, errors during execution |
| The connection becomes invalid or stopped. | Inform the connection administrator of the status and when it occurred. Do not repeat cancellation/re-creation |
In your inquiry, please include the organization name, app name, setting item name, display status, operation date and time, and confirmed operation. Please do not send the secret value or authentication information itself.
## Completion check
- [ ] Confirmed the target organization/app and the person in charge of setting it.
- [ ] Confirmed the required common connector and Project Secret settings.
- [ ] Do not confuse reading failure/unconfirmation with setting unnecessary or completion.
- [ ] Confirmed the access of the prospective user and the necessary connection of the person.
- [ ] Confirmed App-specific preparations
- [ ] Confirmed the necessary business operations using the permitted data.
- [ ] Shared the scope of use, remaining work, and contact information.
## Related articles
- [Install App from Marketplace](install-marketplace-app.md)
- [Open the installed app and start using it](open-installed-app.md)
- [Manage Project/App members](../operations/manage-project-and-app-members.md)
- [Connect your external account](../operations/connect-your-external-accounts.md)
- [Set up a shared connection for Project/App](../operations/configure-shared-connections.md)
- [Register/update/delete Project Secret](../operations/manage-project-secrets.md)
- [Check the number of users, fees, and contract limits for the App](../operations/check-app-seats-pricing-and-limits.md)
- [App installation/initial settings/update fails](../troubleshooting/app-install-setup-update-failures.md)
