# Check App Seats, Pricing, and Contract Limits

Before adding or removing App members or ending use, separately check **the number of users, contract limits, and displayed charge confirmation**. This article guides App administrators in judging pricing effects when changing the number of users of an installed App or ending its use.

For specific actions to invite, change roles for, or remove members, see [Manage Project and App Members](manage-project-and-app-members.md). For ending the entire App and its effects on data and users, see [Uninstall an App](../use/uninstall-app.md). [Index of Limits and Availability Conditions](limits-and-availability-index.md) explains how to find feature-specific limits and availability conditions.

## Scope of this article

This article covers displays related to the number of App users and the seat charges displayed on screen. Do not determine credits consumed by runs, external-service contracts, already finalized invoices, or charges outside the App from this screen's guidance alone.

Also, not every App has per-user charges, seat purchases or adjustments, or the same charge confirmation. The absence of pricing or contract-condition displays cannot be evidence that an App is free or that no cost occurs.

> This article does not state amounts. If pricing is displayed, check the target App, number of users after the change, current charge, and next-renewal guidance on the actual screen. Do not apply displayed charges to another App or organization.

## First distinguish these three numbers

Do not confuse at least the following three displays related to App users.

| Check target | Example screen display | Meaning and decision |
|---|---|---|
| Current users | "Members" or "Member count" | People eligible to use the App. Removing them removes access. |
| Contract limit | "App users" | The contracted number of users for this App. If you reach the limit, action is required before adding. |
| Purchased, assigned, and available seats | "Purchased," "In use," and "Available" | These may be displayed for per-seat Apps. Even if removing a user frees a seat, the purchase count and renewal charge do not necessarily change automatically. |

Check "App users" in the App's **"Settings" > "Members."** For an App with a contract limit, a bar displays usage and the limit, with guidance stating, "The contracted number of users for this app. Contact your account representative to change it." On the invitation screen, the limit and remaining count may appear as **"App users: Limit: N people (N remaining)."**

> The organization's member count, a Project's general member limit, and an App's "App users" are different conditions. If the App invitation screen displays an App-specific limit, check it first.

### When opening a free App to everyone in the organization

For a free App, an administrator with required App management and invitation permissions may be able to open it to all organization members. When an active organization member first opens an available App, they are added as a user. Stopping availability does not automatically remove members who were already added. **This availability setting cannot be used for Apps charged by seat.** Follow [Manage Project and App Members](manage-project-and-app-members.md) for individual removal and role changes.

## 1. Check limits and intended users before adding

1. Open the target App in the correct organization, then open **"Members"** from **"Settings."**
2. In the list, check current users and whether each person will actually use the App.
3. Check **"App users"** or the limit and remaining count on the invitation screen.
4. Check that the number of people you add does not exceed the remaining count. If you select more people than remain, a warning about the number that can be added may appear and prevent proceeding with invitations.
5. If guidance says the limit has been reached, check whether removal alone resolves it or whether the contracted number of users must change. Follow screen guidance and ask your account representative or an organization administrator with purchase permission.

While "Checking the member limit…" or a message that the limit could not be retrieved is displayed, do not decide that the number requirement is met. If you still cannot confirm it after displaying it again later, do not finalize the addition. Ask the responsible administrator, including the target App, planned number of additions, and displayed content.

## 2. Agree when charge confirmation is displayed

For an App charged by users, **"Confirm member fees"** may appear before adding members. This screen confirms the current charge and next renewal when the addition may cause a charge.

1. In **"Invite Member,"** select the people to add and their **"Role."** For role and invitation details, see [Manage Project and App Members](manage-project-and-app-members.md).
2. If charge confirmation appears, check the target App, people added, current charge, and the date and monthly charge for **"Next renewal."** Dates are displayed in Japan Standard Time.
3. Check the authority to approve charges and the budget. If you cannot agree with or judge the displayed content, select **"Cancel"** and do not add members.
4. Only if you can agree, select **"Confirm and add."** Canceling confirmation does not proceed with that addition.
5. After adding, refresh the list and check the people, roles, and displayed number of users.

Even if charge confirmation does not appear, addition is not necessarily free. Conversely, even if it appears, it does not determine costs other than the current charge and next renewal displayed on screen. If a charge or estimate change is displayed, do not use an old confirmation; check the latest charge and agree again.

> Even if permission setup fails during adding, a member may already be added and, for a paid App, guidance may indicate that they are billable. Before adding the same person repeatedly, check the member list and displayed message. Guidance may say that retrying does not incur an additional charge; even then, check its target and state before acting.

## 3. Judge pricing effects after removing members

Removing someone who no longer uses the App removes their access. In an App charged by seat, removal may free a seat that can be assigned to another member.

However, **removing a member alone does not change the purchased count or renewal charge.** To reduce the purchased count, after checking available seats, open **"Adjust seats"** and specify the purchased seat count after the change. You cannot set it below the number of seats in use.

1. Follow [Manage Project and App Members](manage-project-and-app-members.md) to check effects on the removed person's work, handover, and access before removing them.
2. If "Purchased," "In use," and "Available" are displayed, check available seats after removal.
3. To reduce purchased seats, select **"Adjust seats"** and check the changed count and displayed charge.
4. Read **"I agree to the displayed charge and monthly renewal for this quantity."** and select **"Confirm seat adjustment"** only if you agree.
5. After completion, check purchased, in-use, and available counts and next-renewal guidance again.

Reducing the purchased count lowers the next renewal charge, but does not refund the current month. Guidance indicates that there is no additional charge when returning to the number of seats already paid for during the current month. Do not treat this as an immediate refund after removal, cancellation of past invoices, or reduction of external-service costs.

## 4. When ending an App

Uninstallation differs from removing one member. Before ending it, check effects on users, data, external services, scheduled runs, and similar items, and follow [Uninstall an App](../use/uninstall-app.md).

When uninstalling an App charged by seat, separately check the uninstallation confirmation screen and contract/pricing guidance. Do not interpret uninstallation alone as canceling renewal, current-month refunds, unpaid amounts, already finalized costs, other Apps, or external-service contracts. If seat or renewal treatment cannot be confirmed on screen, ask an administrator or JAPAN AI Support.

If the screen alone cannot confirm reuse after uninstallation or handling of remaining contract limits and charges, ask your account representative or JAPAN AI Support before deciding that it is complete.

## Common misunderstandings

| Misunderstanding | Correct check |
|---|---|
| Removing a member lowers the charge immediately | Removal only frees a seat. Separately check the action to reduce the purchased count and next-renewal guidance. |
| When the limit is reached, remove an organization member | Distinguish App users, organization membership, and Project members. Do not remove anyone without checking effects. |
| If charge confirmation does not appear, there is no cost | You cannot decide from the absence of a display alone. Ask the responsible party about contract conditions and other costs. |
| Uninstallation stops both renewal and the current month | Separately check uninstallation and contract/pricing treatment. Check renewal/refund guidance shown on screen; if unclear, ask an administrator or JAPAN AI Support. |

## If you cannot confirm it

| Situation | What to check and do |
|---|---|
| You cannot find "App users" or the user bar | Check that you opened "Settings" > "Members" in the correct App and have permission to check settings. Do not take absence of the display as evidence of unlimited or free use. |
| You cannot add because the limit is reached | Check people to add, remaining count, people planned for removal, and whether the contracted user count must change. Ask your account representative or an organization administrator with purchase permission. |
| You cannot approve the charge-confirmation content | Select "Cancel" and do not finalize addition or seat-count changes. Ask the approver, including the target App, number of people, current charge, and next-renewal guidance. |
| The charge display does not change after removal | Purchased count and number of users are separate. Check available seats, purchased count, and renewal charge, and if necessary check an estimate through "Adjust seats." |
| Charges after ending use are unclear | Check guidance on the App uninstallation confirmation screen and [Uninstall an App](../use/uninstall-app.md). Ask the responsible party or Support about excluded contracts or finalized costs. |

For an inquiry, include organization name, target App, current number of users and displayed limit, planned addition/removal/end, displayed current charge and next-renewal guidance, and operation time. Do not send credit card information, credentials, or personal information unnecessarily.

## Checklist

- [ ] I confirmed that the target is the correct organization and App
- [ ] I checked users and "App users" under the App's "Members"
- [ ] I checked whether additions fit within the remaining count and contract limit
- [ ] When charge confirmation appeared, I checked the current charge and next renewal and obtained approval
- [ ] I checked member removal and changes to purchased count as separate actions
- [ ] When reducing purchased seats, I checked the number in use and displayed charge and agreed to it
- [ ] When ending an App charged by seat, I followed [Uninstall an App](../use/uninstall-app.md) to check effects and separately checked uninstallation and contract/pricing treatment
- [ ] I did not guess unknown contracts or costs and asked the responsible party or Support

## Related articles and next articles to read

- [Manage Project and App Members](manage-project-and-app-members.md)
- [Uninstall an App](../use/uninstall-app.md)
- [Index of Limits and Availability Conditions](limits-and-availability-index.md)
