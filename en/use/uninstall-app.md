# Uninstall an App

When ending use of an App you no longer need, check the impact on users, data, automatic execution, and charges before uninstalling it. This article explains **how to identify the installation method, complete necessary handover, delete the App, and confirm completion.**

**Uninstalling is not an action for only you to leave an App.** It is an action related to ending use of that App. The confirmation screen for an App installed from Marketplace states that the App and data are deleted, members' access permissions are removed, and the action cannot be undone.

If your goal is to resolve a configuration error or update issue, first see [Complete App Setup](complete-app-setup.md) and [Check App Updates, Changes, and Reconfiguration](check-app-updates.md). Do not delete an App assuming that reinstalling it will restore its former data or settings.

## Before you begin

- Check the target organization, App, and provider. Avoid confusing it with an App of the same name or the developer's Project.
- Confirm approval to end use of the App and that you have permission to perform the action.
- Tell users about the planned end date and check whether any in-progress work or handover remains.
- Check the storage location for necessary data, files, and deliverables, and the scope you are permitted to save.
- Check how to handle automatic execution, notifications, calls from other systems, and other items affected by ending use.
- Check App charges separately from costs for separately contracted external services.

> The following instructions focus on actions in a web browser. Displayed items, action permissions, and deletion guidance vary by organization configuration and installation method. Distinguish between opening the confirmation screen and confirming the final "Uninstall" action.

## 1. Check the target to end and its installation method

First, organize what you want to end.

| Purpose or installation method | How to proceed |
|---|---|
| You only want to stop using the App yourself | Check leaving or member changes. Keep this separate from uninstalling the entire App. |
| You want to end an App installed from Marketplace | Proceed to the Marketplace procedure below. |
| You want to end an App installed through in-organization self-install | Check "For in-organization self-install" in this article. Data guidance differs from the Marketplace version. |
| An App installed through another method, such as JAPAN AI distribution or management | Follow the displayed guidance and ask the distribution owner or JAPAN AI. |
| You want to remove a Project or App that is being created from publication | Treat it as an action separate from uninstalling an installed App. |

Being able to open an App, change App settings, and uninstall an organization's App do not require the same permissions. Ending a Marketplace installation checks the permissions needed to manage organization Apps. Do not conclude that you can always delete an App just because you are an App administrator; if you cannot perform the action, ask the person with permission.

## 2. Complete handover before deletion

Before confirming on the confirmation screen, check the following items.

| Item to check | What to do before ending use |
|---|---|
| Users | Check who uses it, and tell them the alternative procedure and contact after it ends. |
| Business data | Identify necessary information and save or migrate it by approved methods. |
| Files and deliverables | Ensure items that must be referenced later can be checked in an approved storage location. |
| Processing in progress | Check incomplete processing and whether it has been reflected in external services. |
| Scheduled runs, notifications, and external calls | Check dependencies on this App, and coordinate necessary stopping or switching with the person in charge. |
| Connections and Secrets | Check whether they are shared with other purposes, and determine who will organize them after ending use. |
| Charges and contracts | Check the guidance when ending the App and the end conditions for separate contracts, such as external services. |

Save data using functions the App provides or methods permitted by the organization. Not every App has a bulk-export or restore feature. If you cannot save necessary data, consult the administrator or provider before confirming deletion.

**Even when data is retained internally, users cannot necessarily retrieve or restore it later.** Conversely, do not treat an uninstall as meaning that all data, including audit records and information in external services, has been immediately and physically erased. If you need to check retention periods or complete deletion, identify the target data in your inquiry.

Do not delete connections or Secrets first and stop other Apps or business tasks. You do not need to paste authentication information itself into handover materials or chat.

## 3. Uninstall an App installed from Marketplace

### Open the confirmation screen for the target App

1. Open the Studio Apps list in the correct organization.
2. If "Uninstall" is displayed in the target App's menu, select it.
3. When acting from the App's settings, select "Uninstall" displayed in the settings screen.
4. Check that the App name on the confirmation screen matches the App you want to end.

Depending on your permission, you may also be able to act from "Organization Apps." Even if there are multiple entry points, always check the target on the confirmation screen. If you cannot find the button, do not substitute deleting another App or the developer's Project; check the installation method and permissions.

### Read the deletion and charge guidance

On the confirmation screen, check that the App and data will be deleted, members' access permissions will be removed, and the action cannot be undone. Use the displayed number of users as material for checking the impact as well.

For an App that charges according to the number of users, the following charge guidance is displayed.

> No future renewal charges will occur. Charges for the current month will not be refunded.

This guidance applies to renewal charges for that App. Do not interpret it as meaning charges for past usage, already finalized costs, other JAPAN AI contracts, or external-service charges will also be canceled. Do not conclude that all related costs will become zero just because charge guidance is not displayed.

For charge and contract-limit details, see [Check App Seats, Pricing, and Contract Limits](../operations/check-app-seats-pricing-and-limits.md).

### Enter the App name and confirm

1. Make a final check that data saving, handover, and guidance to users are complete.
2. Enter the App name requested on the confirmation screen exactly as displayed.
3. Select "Uninstall" to confirm.
4. Check the processing result.

You cannot confirm the action if the name you entered does not match the displayed name. If guidance says the App name has changed, check the latest name and target again.

To cancel before confirming, select "Cancel." **Closing the screen after a request has been accepted does not cancel an uninstall that has already been accepted.** Avoid repeated actions while processing is in progress. If the result is unclear, check the status in the next procedure.

## 4. For in-organization self-install

In-organization self-install also has an uninstall action for the installed App. However, it follows a management path different from Marketplace installations, so do not describe required permissions or data handling as the same.

1. Open the **installed App** you want to end, not the developer's Project.
2. Select "Uninstall" displayed in the Apps-list menu or App settings.
3. Check the App name on the confirmation screen and guidance with the following meaning:
   - The installed App and its access permissions will be deleted.
   - The developer's Project remains.
   - Existing retention rules apply to customer data.
4. After checking necessary data saving and handover, enter the requested App name and confirm.
5. After returning to the Apps list, check the current status as well.

"The developer's Project remains" does not mean data or settings added by the installing organization return to the developer. If the targets of retention rules, storage locations, or scope available for later reference are unclear, check with the person in charge before confirming.

Even if guidance says the action failed, processing may have completed. Check the current status before retrying. Marketplace-specific displays such as "accepted" and "Retry" are not necessarily shown in the same way for self-install.

## 5. Distinguish between acceptance and completion

**Being unable to open an App or returning to the list alone does not confirm that deletion is complete.** With Marketplace installations, use may first be disabled while subsequent deletion processing remains incomplete. For this Marketplace-specific action, returning from App settings to the list can occur even when the response is incomplete.

| Result or display | Next action |
|---|---|
| Completion guidance such as "App uninstalled" | Check the latest state in the list and record the result of ending use. |
| Uninstalling | Do not repeat the action; check the processing result. |
| Accepted but uninstall incomplete | Do not treat it as complete. Check the guidance and use "Retry" for the same target. |
| Deploying | Check that deployment is complete before retrying. |
| Guidance about an always-on Page | Ask JAPAN AI Support to stop the always-on Page and complete deletion. |
| Data deletion is incomplete | Retry according to the guidance, and contact Support if it is not resolved. |
| The result is unclear because of a communication error or similar issue | Reopen the list or details in the correct organization, check the current status, and then decide the next action. |

When retrying an accepted Marketplace installation, you may not be required to enter the App name again. This is not an action to delete a new installation; it is an action to complete deletion that was already accepted.

Even if it is not visible in the list, the organization, search conditions, or viewing permissions may differ. Do not reinstall while the result is unconfirmed or repeatedly delete it from another screen.

## 6. Check and communicate after ending use

After you confirm completion, record the date and time, target App, result you checked, and remaining tasks, then share them with users.

- Whether necessary saved data and deliverables can be referenced at the handover destination
- Whether settings remain in other systems that call the ended App
- Whether notifications, connections, or contracts in external services require separate organization
- Whether incomplete processing or inquiries remain
- Whether you told users the alternative procedure and contact

External actions that have already run are not necessarily canceled by uninstalling the App. Check with the person responsible for the relevant service as needed about handling sent notifications, registered data, and similar outcomes.

For detailed organization of data, permissions, and audit records, see [Check Deletion, Handover, and Audit Logs](../operations/deletion-handover-and-audit-logs.md).

## When things do not go as expected

- **"Uninstall" is not displayed:** Check the target organization, installation method, and action permissions. Permission to use the App alone may not be enough.
- **You are told to contact JAPAN AI:** Ask the person responsible for managing that installation. Do not substitute another deletion feature.
- **The App cannot be used but is shown as incomplete:** Check disabled use and completed deletion separately.
- **It does not complete even after retrying:** Tell Support the target, displayed guidance, whether it was accepted, and checks already performed.

Include the organization name, App name, installation method, action date and time, screen display, and recheck or retry results in your inquiry. Do not include API keys, Secret values, passwords, or confidential business data.

## Completion checklist

- [ ] I checked the target to end and the installation method, and distinguished it from leaving, deleting a Project, or removing from publication.
- [ ] I completed user guidance, necessary data saving, and business handover.
- [ ] I read the confirmation screen for the applicable method concerning data, access permissions, and charges.
- [ ] I confirmed the uninstall with the correct App name.
- [ ] I did not confuse accepted status, disabled use, or an unclear result with completed deletion.
- [ ] I checked the completion result and assigned a person responsible for any remaining work.
- [ ] I shared the result of ending use and the alternative procedure with users.

## Related articles

- [Complete App Setup](complete-app-setup.md)
- [Check App Updates, Changes, and Reconfiguration](check-app-updates.md)
- [Manage Project and App Members](../operations/manage-project-and-app-members.md)
- [Check App Seats, Pricing, and Contract Limits](../operations/check-app-seats-pricing-and-limits.md)
- [Check Deletion, Handover, and Audit Logs](../operations/deletion-handover-and-audit-logs.md)
- [Creator Performs an In-Organization Self-Install](../distribution/self-install-in-organization.md)
- [App Installation, Setup, or Update Fails](../troubleshooting/app-install-setup-update-failures.md)
