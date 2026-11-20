# Check App Seats, Pricing, and Contract Limits

Before adding or removing App members or ending use, check **the number of users, contract limits, and displayed charge confirmation**. This article guides App administrators in judging pricing effects when changing the number of users of an installed App or ending its use.

For specific actions to invite, change roles for, or remove members, see [Manage Project and App Members](manage-project-and-app-members.md). For ending the entire App and its effects on data and users, see [Uninstall an App](../use/uninstall-app.md). [Index of Limits and Availability Conditions](limits-and-availability-index.md) explains how to find feature-specific limits and availability conditions.

## Key points

| Item | Rule |
|---|---|
| Billing unit (paid Apps) | Since 2026-09-17, paid Apps are charged by the **number of active members**. **Monthly charge = unit price × number of active members.** |
| Monthly renewal | At renewal, the charge is settled automatically based on the active member count at that time. There is no manual seat adjustment. |
| Adding members | "Confirm member fees" appears before adding; members are added when you agree. |
| Removing members | The active member count decreases, so **the next renewal charge goes down**. The current month is not refunded. |
| Uninstalling the App | The confirmation screen states that no further renewal charges will occur and that the current month will not be refunded. |
| The person who installed the App / an organization administrator who joins by themselves | Automatically becomes an App administrator and, in a paid App, counts as one active member. |

## Scope of this article

This article covers displays related to the number of App users and the App charges displayed on screen. Credits consumed by runs, external-service contracts, already finalized invoices, and charges outside the App cannot be judged from this screen. Check credits in [Check Execution Consumption and Usage](check-execution-consumption-and-usage.md).

Not every App is paid. Free Apps do not show a charge confirmation.

> This article does not state unit prices. Check the target App, number of users after the change, current charge, and next-renewal guidance on the actual screen.

## Two user-related displays

| Check target | Example screen display | Meaning |
|---|---|---|
| Active members | "Members" or "Member count" | People eligible to use the App. In a paid App, this number is used to calculate the monthly charge. Removing someone removes their access and excludes them from charges from the next renewal. |
| Contract limit | "App users" | The contracted number of users for this App. If you reach the limit, action is required before adding. |

Check "App users" in the App's **"Settings" > "Members."** For an App with a contract limit, a bar displays usage and the limit, with guidance stating, "The contracted number of users for this app. Contact your account representative to change it." On the invitation screen, the limit and remaining count appear as **"App users: Limit: N people (N remaining)."**

> The organization's member count, Project members, and an App's "App users" are different conditions. If the App invitation screen displays an App-specific limit, check it first.

### When opening a free App to everyone in the organization

For a free App, an App administrator can turn on the setting that makes it available to all organization members. When an active organization member first opens such an App, they are added as a member. Turning the setting off does not automatically remove members who were already added. **This setting cannot be used for paid Apps.** Follow [Manage Project and App Members](manage-project-and-app-members.md) for individual removal and role changes.

## 1. Check limits and intended users before adding

1. Open the target App in the correct organization, then open **"Members"** from **"Settings."**
2. In the list, check current users and whether each person will actually use the App. In a paid App, the monthly charge increases by the number of people added.
3. Check **"App users"** or the limit and remaining count on the invitation screen.
4. Check that the number of people you add does not exceed the remaining count. If you select more people than remain, a warning appears and you cannot proceed with invitations.
5. If the limit has been reached, decide whether removing people who do not use the App resolves it or whether the contracted number of users must change. Ask your account representative to change the contracted number.

While "Checking the member limit…" or a message that the limit could not be retrieved is displayed, do not finalize the addition. If it still does not appear after displaying it again later, contact the responsible administrator with the target App, planned number of additions, and displayed content.

## 2. Agree when charge confirmation is displayed

For a paid App, **"Confirm member fees"** appears before adding members. Check the current charge and next renewal on this screen.

1. In **"Invite Member,"** select the people to add and their **"Role."** For role and invitation details, see [Manage Project and App Members](manage-project-and-app-members.md).
2. Check the target App, people added, current charge, and the date and monthly charge for **"Next renewal."** Dates are displayed in Japan Standard Time.
3. Check the authority to approve charges and the budget. If you cannot agree, select **"Cancel"** and do not add members.
4. Only if you agree, select **"Confirm and add."**
5. After adding, refresh the list and check the people, roles, and displayed number of users.

If a message says the charge has changed, do not use an old confirmation; check the latest charge and agree again.

> Even if permission setup fails during adding, the member may already be added and, for a paid App, a message may indicate that they are billable. Before adding the same person repeatedly, check the member list and displayed message.

## 3. Charges after removing members

The monthly charge for a paid App is **unit price × number of active members**, settled automatically at monthly renewal based on the active member count at that time.

- Removing someone who no longer uses the App removes their access and **lowers the next renewal charge**. No separate action to reduce a purchased count is needed (there is no seat adjustment screen).
- The current month is not refunded.
- Removal does not cancel past invoices or change external-service costs.

Steps:

1. Follow [Manage Project and App Members](manage-project-and-app-members.md) to check effects on the removed person's work, handover, and access before removing them.
2. Check the active member count in the list. The next monthly charge is calculated from the active member count at renewal.

## 4. When ending an App

Uninstallation differs from removing one member. Before ending it, check effects on users, data, external services, scheduled runs, and similar items, and follow [Uninstall an App](../use/uninstall-app.md).

When you uninstall a paid App, the confirmation screen states that no further renewal charges will occur and that the current month will not be refunded, and it is processed that way. It does not affect other Apps, external-service contracts, or invoices that are already finalized.

## Common misunderstandings

| Misunderstanding | Actual behavior |
|---|---|
| Removing a member does not change the charge | Paid Apps are charged by active members, so removal lowers the next renewal charge. The current month is not refunded. |
| You need "Adjust seats" to reduce the purchased count | There is no seat adjustment. The charge is settled automatically by active member count at monthly renewal. |
| When the limit is reached, remove an organization member | App users, organization membership, and Project members are different. Remove people who do not use the App from the App's members. |
| Uninstalling refunds the current month | No further renewal charges occur, but the current month is not refunded. |

## Troubleshooting

| Situation | What to check and do |
|---|---|
| You cannot find "App users" or the user bar | Check that you opened "Settings" > "Members" in the correct App and that you are an App administrator. |
| You cannot add because the limit is reached | Remove people who do not use the App, or ask your account representative to change the contracted number of users. |
| You cannot approve the charge-confirmation content | Select "Cancel" and do not finalize the addition. Ask the approver, including the target App, number of people, current charge, and next-renewal guidance. |
| The current month's invoice does not change after removal | Removal takes effect from the next renewal; the current month is not refunded. Check the monthly charge for the next renewal. |
| Charges after ending use are unclear | Check the App uninstallation confirmation screen and [Uninstall an App](../use/uninstall-app.md). For other contracts or finalized costs, contact your account representative or JAPAN AI Support. |

For an inquiry, include organization name, target App, current number of users and displayed limit, planned addition/removal/end, displayed current charge and next-renewal guidance, and operation time. Do not send credit card information, credentials, or personal information unnecessarily.

## Checklist

- [ ] I confirmed that the target is the correct organization and App
- [ ] I checked active members and "App users" under the App's "Members"
- [ ] I checked whether additions fit within the remaining count and contract limit
- [ ] I checked the current charge and next renewal in the charge confirmation and obtained approval
- [ ] I understand that removing people lowers the monthly charge from the next renewal (the current month is not refunded)
- [ ] When ending a paid App, I followed [Uninstall an App](../use/uninstall-app.md) to check effects

## Related articles and next articles to read

- [Manage Project and App Members](manage-project-and-app-members.md)
- [Uninstall an App](../use/uninstall-app.md)
- [Check Execution Consumption and Usage](check-execution-consumption-and-usage.md)
- [Index of Limits and Availability Conditions](limits-and-availability-index.md)
