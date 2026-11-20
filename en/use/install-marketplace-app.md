# Install an App from the Marketplace
This article explains the steps to install selected Studio Apps from Marketplace to your organization and check the installation results. The target audience is those responsible for app implementation.
**Installation is more than just adding a shortcut to your screen. ** This action makes the app available to your organization and may require you to agree to a fee. Please check the target organization and approval details before proceeding.
Comparing and selecting apps is handled by [Selecting an app in the Marketplace](choose-marketplace-app.md), and preparing for connections and users after installation is handled by [Completing initial app settings](complete-app-setup.md). This article does not cover the steps to introduce an app you created into your organization.

## Who can do what

| Operation | Who can do it |
|---|---|
| Install / uninstall, view the "Organization Apps" tab | Organization administrators (ADMIN), or users with the `jai.studioapp.install` permission |
| Manage the App after installation | The installer automatically becomes an App administrator (for paid Apps, the installer also uses 1 seat) |
| Configure shared connections, Secrets, and members | App administrators only |
| Join an installed App | Only organization administrators can join by themselves (always as an administrator; for paid Apps this uses a seat). Regular members are added by an App administrator, or join as a member the first time they open an App with "Publish to all organization members" enabled |

The monthly fee for a paid App is "unit price × number of active members", settled automatically at each monthly renewal based on the member count at that time.

## Before you start
- The app to be introduced and the organization to which it will be introduced have been decided.
- Verify the purpose, required authority, external connections, and costs, and obtain the necessary internal approval.
- The operating account is an organization administrator (ADMIN) or has the `jai.studioapp.install` permission.
- A person has been determined who will be in charge of initial settings and user management after installation.
- You have checked that the same App is not already installed or being installed by another person.
> The following explanation focuses on the standard screen on a PC web browser. The entrance, buttons, price display, and selectable operations vary depending on your organization's settings, permissions, and app availability. Not everyone can do the same thing.
## 1. Check the status of the target organization and app
1. Check the currently selected organization.
2. In the left navigation, open "STUDIO" > "Apps" and select the "Marketplace" tab.
3. Find the selected app and open the details screen using operations such as "View details".
4. Check the app name and provider.
5. Check the deployment status and operations displayed.
| Example of display/status | What to do next |
|---|---|
| Installation | Reconfirm the details and proceed to the confirmation screen |
| Contact JAPAN AI | Request introduction to contact person. Distinguish from apps that can be installed directly using this procedure |
| During installation | Check the current processing results without additional execution |
| Installed/Open | Instead of installing a new app, proceed to use and initial settings of the existing app |
| Join/Request to administrator | The App is already installed. "Join" is shown only to organization administrators, who join as an administrator (using a seat for paid Apps). Regular members ask an App administrator to add them. Do not try to solve the problem by reinstalling |
| Installation failure/retry | Check the latest status and errors, then decide on the retry procedure described below |
| Information regarding currently unavailable/installation privileges | Check with administrator or support |
**Being able to view the list and being able to install are different things.** Only organization administrators (ADMIN) or users with the `jai.studioapp.install` permission can install. If you have the permission but the button does not appear, check whether the app requires an inquiry or has restrictions on purchase or provision conditions.

Apps that JAPAN AI has prepared as pre-installed are installed the first time someone clicks "Open". If you do not need one, you can also "Delete" it. See [Open installed app and start using it](open-installed-app.md).
If you have already installed it, refer to [Open installed app and start using it](open-installed-app.md).
## 2. Open the installation confirmation screen
Select "Install" on the details screen. Just opening the confirmation screen does not confirm the installation.
On the confirmation screen, review the following:
| Items to check | Contents to check |
|---|---|
| App name | Does it match the selected and approved app? |
| Destination | Is the currently selected organization sufficient? If you are unsure, go back and check before confirming |
| Where will it be used after deployment | Did you understand that the target app will be used in this organization after deployment |
| Connection settings | Is there a notice that connection settings for external services are required after installation? |
| What to agree to | Can I agree to the introduction to the organization and the billing terms, if any? |
If there is a difference between the required authority/external connection confirmed on the details screen and the content approved within the organization, please check with the person in charge without finalizing it.
This operation is not the procedure for selecting an existing Project and adding App parts. Also, simply installing it does not necessarily mean that connections to external services or preparations for all users are complete.
### If prices are displayed
When you see the price on the confirmation screen, check at least the following:
- Unit of price calculation, such as number of people and number of seats
- Current charge amount
- Next renewal date and monthly amount
- Explanation regarding the user's usage limit to be introduced
- Agreement details including monthly billing
Prices are shown in units of "seats" or "credits". For paid Apps, the installer automatically becomes an App administrator and uses 1 seat. Actual amounts follow the confirmation screen and contract details.
**Be sure to check not only the current payment but also any recurring charges. ** The monthly fee for a paid App is "unit price × number of active members". If you add members after installation, the next renewal fee increases accordingly. Adding users is covered in the initial setup and member management articles.
Check the app fee and the cost of execution consumption and external services such as AI processing separately. Don't assume that the overall cost of the service is zero just because it says "free."
If the price, number of people, or renewal conditions differ from what is approved, please return by clicking "Cancel" before confirming and confirm with the person in charge.
## 3. Agree to the terms and confirm installation
1. Final check the installation location, app name, connection prerequisites, fees, and update conditions.
2. Check the consent box on the confirmation screen.
3. Select "Install" or "Confirm and install".
4. Check the processing display and wait for the response.
If you have not consented or the process is in progress, you cannot confirm. Even if it seems unresponsive, don't repeat the same action or perform it from different tabs at the same time.
There is a difference between "cancelling" before confirmation and canceling the process after confirmation. **Even if you close the screen after confirmation, it will not be considered that the installation or billing has been cancelled. ** If it has been confirmed in the wrong organization, please check the current installation status and contact your administrator or support. Please do not try to solve the problem by repeatedly deleting and reintroducing it yourself.
### If the information changes during confirmation
If the price or App content changes while you are reviewing, messages such as "The price for your seat has changed" or "The app was updated while you were checking" are shown.
1. Follow the instructions such as "Reload" to get the latest information.
2. Re-read the content, requirements, current fees, and renewal conditions of the app.
3. Check if it is within the approved range.
4. If there are no problems, agree and confirm again.
Please do not extend your consent to the previous content to the changed content. If you cannot obtain the latest information, check the status without repeating confirmation.
## 4. Check the installation result
Once confirmed, check not only the notification but also the latest status of the details screen and list of the target app.
### If the installation is complete
Confirm that you receive a notification saying "The app has been installed" or that the target app has been installed. If an operation such as "Open" is displayed, confirm whether you want to proceed to the target app.
If the display remains outdated, check the target organization and app, and then re-open the details screen to obtain the latest status.
Even if the App is installed, you cannot open it unless you are a member of that App. "Join" is shown only to organization administrators and joins them as an administrator (using a seat for paid Apps). Regular members are added by an App administrator, or join as a member by opening an App with "Publish to all organization members" enabled. Handle these separately from new installations.
### During installation
"Installing" is not complete. If you are prompted to leave the screen and return later, return to the details screen for the same organization/app to check its status.
On the standard screen, the status during installation is checked every 2 seconds for a maximum of about 2 minutes after the installation starts. However, do not assume that it will be completed within a certain time. If the display does not change or after checking is complete, reopen the screen and check the latest status.
### If you don't know the result
If communication is interrupted, the screen is closed, or a message saying "Unable to confirm whether installation is complete" is displayed, check the following steps:
1. Reopen the details for the same app in your organization's Marketplace.
2. If it has been installed, proceed to the initial settings without installing it again.
3. If the installation is in progress, continue checking the results.
4. If a failure or unavailability is displayed, follow the instructions.
5. If the status is still unclear, contact your administrator or support.
**Failure to receive a response does not mean that the product has not been installed or has not been charged. ** Please check the current state before retrying.
## 5. Transfer to initial settings
If "Setup completed" or the number of settings remaining is displayed after installation, initial settings are required. Find out who will be responsible for setting it up and what work remains.
- Target organization and app
- Date and time when installation results were confirmed
- Who will open the app?
- Unconfigured items and errors displayed on the screen
- Next steps to check, such as external connection, secret information, user addition, etc.
If the setup status cannot be read, it will not be determined that "setting is not required" or "configured". Refresh the screen and check again.
**Please distinguish between installation completed, on-screen setup completed, and ready for business use. ** The setup field alone does not mean that individual connections, necessary user participation, and operation checks using business data have been completed.
The rest is handled in [Complete initial app settings](complete-app-setup.md). Please do not inform other users that the facility is ``ready for business use'' until the necessary preparations are completed.
## When things don't go well
| Situation | Response |
|---|---|
| Cannot select installation | Check organization, installation permission (organization ADMIN or `jai.studioapp.install`), inquiry type, purchase/provide conditions |
| Insufficient credits | Check with the administrator and review the purchase details after taking necessary action |
| A message appears stating that the seat cannot be purchased | Contact the administrator or JAPAN AI. Do not try changing permissions or using a different account |
| Prices and app information have changed | Re-obtain the latest information, confirm the scope of approval, and agree again |
| Version not available | Check the latest App information and installation status. If the unavailability continues, please contact us |
| Installation failed | Check the latest status and errors. If you are prompted to "try again," check the content and execute |
| Failed even after retrying/Needs support | Contact administrator or support without retrying |
| Not sure if completed | Check the latest deployment status of the same app before trying again |
| Can't open even though it's installed | Ask an App administrator to add you as a member (organization administrators can "Join"). Handle separately from reinstallation |
Please include the organization name, app name, date and time of operation, error indication, current installation status, and any reconfirmations you have performed in your inquiry. Please do not include passwords, API keys, or external service credentials.
## Completion check
- [ ] Operated with the correct organization and app
- [ ] Confirmed necessary approvals for implementation and costs.
- [ ] Read the contents of the confirmation screen, give the necessary consent, and confirm.
- [ ] Check the installed status and do not treat in progress/unknown results as completed.
- [ ] Confirmed the presence or absence of initial settings and the next person in charge/work.
- [ ] Just because the installation is completed does not mean that it is ready for business use.
## Related articles
- [Select App in Marketplace](choose-marketplace-app.md)
- [Open the installed app and start using it](open-installed-app.md)
- [Complete initial app setup](complete-app-setup.md)
- [Check the number of users, fees, and contract limits for the app](../operations/check-app-seats-pricing-and-limits.md)
- [Creator self-installs within organization](../distribution/self-install-in-organization.md)
- [App installation/initial settings/update fails](../troubleshooting/app-install-setup-update-failures.md)
