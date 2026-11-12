# Manage Project and App Members

To operate Projects and Apps safely, it is important not only to add users, but also to grant only needed roles and appropriately remove access that is no longer needed. This article explains **how administrators invite members, change roles, and remove members from the "Members" screen of a Project or App**.

You also manage App members as members of the Project corresponding to the App. However, when adding members to an App, confirmation or limits may be displayed according to user contract conditions and availability. For pricing and contract-limit decisions, see [Check App Seats, Pricing, and Contract Limits](check-app-seats-pricing-and-limits.md).

For the overall order of initial setup, see [Complete the Initial Setup of an App](../use/complete-app-setup.md). For organization membership administration and how roles affect available operations, see [Understand Organization, Project, and App Permissions](understand-org-project-app-permissions.md).

## Before you begin

- Confirm the correct organization and target Project or App. If targets have the same name, also match the description and users.
- Confirm that you have permission to manage members. Being able to open a target does not necessarily mean you can invite, change, or remove members.
- Confirm with the responsible person who will be added, changed, or removed, the required role, and the permitted date and time for the action.
- For an App, check whether the person you add will actually use it and the effect on user-count and contract conditions.
- Check whether the person to remove has active work, shared connections, or work that must be handed over.

> The following assumes a standard screen displayed in a desktop web browser. Menus, role options, user-count displays, and available operations vary according to organization settings, permissions, and the App contract and configuration. Do not substitute a feature that is not displayed with another method; ask a responsible person with permission.

## Targets to check in member management

| Target | Typical way to open it | What to check |
|---|---|---|
| Project | Open "Members" from "Project Settings" in the target Project. | People participating in the Project and their roles |
| App | Open the target App, then open "Members" in "Settings." | App users, roles, and user-count displays |
| Organization | Organization member-management screen | Organization membership and invitation status. Check it separately from Project and App member management. |

The App "Members" screen is displayed to people who can manage target App settings. Although Projects and Apps have different entry points, the approach to member lists, invitations, role changes, and removal is the same.

> **Belonging to an organization and being able to use a Project or App are not the same thing.** Adding a person to the wrong destination may prevent the needed person from using it or grant access to an unnecessary person. Check the target name before and after the operation.

## 1. Open the member list and check the current state

1. Open the target Project, then select **"Project Settings"** and **"Members."** For an App, open the target App, then select **"Settings"** and **"Members."**
2. In the list, check names, email addresses, **"Role,"** and the user-count display if needed.
3. Identify the person to add, change, or remove by email address as well as name, not only by a duplicate name.
4. If a user-count bar, guidance, or warning is displayed for an App, read it before adding a member.

Role options may include "Viewer," "Member," "Editor," and "Admin." Available options and actual permissions vary by settings, so do not infer operational scope from a role name alone. See [Understand Organization, Project, and App Permissions](understand-org-project-app-permissions.md) for how to choose a role.

## 2. Add or invite a member

1. Select **"Invite Member"** at the top of the list.
2. From the displayed candidates, select the person to add. If the candidate is not displayed, check organization membership status and search conditions.
3. Under **"Role,"** select the role needed for the person's responsibilities. Do not make people who will not perform administrative work "Admin" without confirmation.
4. For an App, check whether user-count conditions or pricing confirmation are displayed. Review the displayed information and select **"Confirm and add"** or the screen's confirmation action only if you can approve it.
5. After invitation, refresh the list and check the person, role, and, for an App, user-count display.

Available invitation candidates vary by organization and status. Do not conclude that an absent candidate is caused only by an incorrect email address. Also, a display that addition completed does not prove that the person can use required external services or App-specific settings.

> When pricing confirmation appears while adding a person to an App, check the target App, number of people added, displayed pricing, and renewal guidance before confirming. Do not guess pricing here or assume the same conditions as another App.

## 3. Change a role

Review the role of an existing member when they transfer or their responsibilities change. Before changing it, check the administration, editing, and use activities they currently perform and those they will need after the change.

1. In the "Members" list, open the target person's row.
2. Check the current value of **"Role"** and the granted role.
3. Select or remove the required role and use the screen's save operation.
4. After saving, display the list again and confirm that the person's role is as intended.
5. For important changes, notify the person of the change and the work scope that will become available.

You cannot remove Admin permission from the last Project Admin. First grant Admin permission to another member and confirm that it is reflected before changing it. If you cannot change your own role from this list, ask another administrator.

> If an error about applying the change appears after saving a role, do not conclude that it was not saved. Reload the list and check the current role. Before repeating the change, record the display and error and consult the administrator.

## 4. Remove a member

Removal removes access to the target Project or App. It differs from removing a person from the organization itself, uninstalling an App, or deleting a Project or resource.

1. Confirm that the target person is correct and has no work that needs handover or stopping.
2. In the "Members" list, open the target person's action and select **"Delete."**
3. In the confirmation dialog, check the person to remove and target Project or App again.
4. Select **"Delete"** and confirm the operation.
5. Refresh the list, then confirm that the person is no longer shown as a member and access was removed as expected.

The removed member's access to the Project or App is revoked, but the operation does not delete the Project or resources themselves. You cannot remove the last Project Admin. If an administrator leaves or must exit, first decide on a successor and grant that person permission.

> You cannot remove yourself from the list. If you need to leave an App, use the displayed **"Leave this app"** and confirm that you are not the last Admin. Conditions for leaving may differ between Projects and Apps, so do not substitute the removal operation.

## App-specific: Check the impact on users and seats

For an App, adding or removing members may affect user count or seat status. If **"App users,"** a usage limit, pricing confirmation, or guidance about available seats is displayed, check the following.

| Operation | What to check | Response |
|---|---|---|
| Add | Whether availability or limit and pricing confirmation are displayed | Confirm contract and approval conditions before confirming. |
| Limit reached | Whether addition is disabled or guidance appears on the screen | Ask an organization administrator or account contact with purchase permission. |
| Remove | Whether the person has finished using the App | Check the user-count display and access status after removal. |
| Contract or pricing change | Impact on purchased count, renewal pricing, refunds, and similar matters | Do not decide from this screen alone; see [Check App Seats, Pricing, and Contract Limits](check-app-seats-pricing-and-limits.md). |

The user-count display on the screen is a clue for checking target App user count and contract status. Not every App displays the same seat system or pricing confirmation. Do not treat no display as evidence that there are no user-count conditions or costs.

## If it does not work

| Situation | Check and response |
|---|---|
| "Members" or "Settings" is not displayed | Check that this is the correct Project or App, that you have permission to manage settings, and that the feature is available in organization settings. |
| You cannot select "Invite Member" | Check invitation permission, target App status, and guidance about user limits or contract restrictions. |
| The person to add is not a candidate | Check organization membership, candidate search results, and the target Project or App. If an organization invitation is needed, ask an organization administrator. |
| You cannot change a role | Check change permission, selectable roles, and whether you are trying to change the last Admin. |
| You cannot remove a member | Check whether it is yourself or the last Admin and whether you have removal permission. If necessary, grant Admin permission to a successor, then check again. |
| Pricing or user-count confirmation appears when adding to an App | Check the target App, person to add, displayed information, and approval authority. For pricing and contract details, see [Check App Seats, Pricing, and Contract Limits](check-app-seats-pricing-and-limits.md). |
| You do not know the state after saving | Reload the list and check the person, role, and user-count display. Do not confirm the same operation repeatedly. |

For an inquiry, provide the organization name, target Project or App, target person's email address, operation date and time, screen used, displayed message, and current list display. Do not include confidential information such as passwords, API keys, or Secret values.

## Checklist

- [ ] I selected the correct organization and target Project or App.
- [ ] I checked the people to add, change, or remove by email address.
- [ ] I selected the minimum required role and retained a final Admin.
- [ ] I checked the results of invitations, role changes, and removal again in the list.
- [ ] For an App, I checked displays about users, seats, and pricing confirmation.
- [ ] I checked handover and active work before removal.
- [ ] I notified needed responsible people and users of the changes.

## Next articles

- [Complete the Initial Setup of an App](../use/complete-app-setup.md)
- [Understand Organization, Project, and App Permissions](understand-org-project-app-permissions.md)
- [Check App Seats, Pricing, and Contract Limits](check-app-seats-pricing-and-limits.md)
- [Check What Changed in an App Update and What to Reconfigure](../use/check-app-updates.md)
