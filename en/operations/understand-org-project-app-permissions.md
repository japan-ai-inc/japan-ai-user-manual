# Understand Organization, Project, and App Permissions

In JAPAN AI Studio, permissions to manage organizations and Projects are separate from the conditions for using and configuring Apps. Before making changes, organize the scope of actions you will delegate.

This article explains how administrators can assess permission relationships among organizations, Projects, and Apps. For invitations, role changes, and removals, see [Manage Project and App Members](manage-project-and-app-members.md); for initial configuration, see [Complete App Setup](../use/complete-app-setup.md).

## Summary: who can do what

| Action | Who can do it |
|---|---|
| Invite Project members, change their roles, and remove them | Project Admins |
| Create, update, and delete Project resources (Agents, Workflows, Pages, and more) | Project Admins and Editors |
| Run Agents and Workflows, and create many kinds of resources | Project Members and above |
| Use Studio Assistant | People with Editor or higher in the Project, when the feature is enabled for the organization |
| Install and uninstall Apps, and open the organization's Apps tab | Organization Admins, or people with the `jai.studioapp.install` permission |
| Configure an App's shared connections, Secrets, and members | That App's Admins (the person who installs an App automatically becomes an App Admin) |
| Join an App by yourself | Organization Admins only (they always join as Admin) |

- Studio Projects have three roles: "Admin," "Editor," and "Member." **There is no read-only (Viewer) role.** To give someone read-only access, create an IAM custom role and assign it.
- Editors cannot manage Project members.
- In a paid App, each member, including App Admins, uses one seat.

## Scope of this article

This article distinguishes the following three layers.

1. **Organization roles**: Basic roles as an organization member. They form the basis for actions involving organization settings, members, and organization-wide resources.
2. **Project roles**: Roles assigned to people who join an individual Project. They determine the scope for using, creating, and managing resources in the Project.
3. **App-specific conditions**: Conditions for opening, using, and configuring an installed App. In addition to Project access, App membership, seats, connections, and Secrets apply.

It does not cover procedures for configuring individual resources. For detailed error isolation, see [Permission and Authentication Errors](../troubleshooting/permission-and-auth-errors.md).

> **Belonging to an organization, participating in a Project, and being able to use an App for work are not the same state.** Even if one condition is met, the target action is unavailable if another condition is missing.

## First, understand the overall permission model

| Layer | Main management targets | Typical roles and conditions | Primary managers | What this layer alone does not determine |
|---|---|---|---|---|
| Organization | Organization settings, organization members, organization-wide resources, and App installation | "Admin," "Editor," "Member," and IAM roles as needed | Organization administrators | Participation in individual Projects and availability for each App |
| Project | Project members, Agents, Workflows, Pages, and more | "Admin," "Editor," "Member," and custom roles as needed | Project administrators | Seats, external-service permissions, and App-specific settings |
| App | Members, settings, and required connections and Secrets for installed Apps | App membership, seats, and App-specific requirements | App administrators | Organization-wide administrative permissions and the user's own external-service permissions |

IAM supplements actions that basic roles do not cover. In organizations and Projects where "IAM Role Settings" is displayed, system roles and "Custom roles" are shown.

> Do not treat someone who has only been assigned an IAM role as an App user if they have not joined the Project. Also check the person's Project participation status.

## What organization roles determine

Organization roles define the basic scope of actions in the organization. The basic roles are "Admin," "Editor," and "Member."

| Organization role | What it primarily allows | Administrative approach |
|---|---|---|
| Admin | Broadly manages the organization and organization-wide resources, including installing and uninstalling Apps. | Limit this role to people who handle organization settings and member management. |
| Editor | Has scope to manage organization-wide resources other than IAM management. | Does not always have the same actions as an Admin. |
| Member | Serves as the basis for viewing and running permitted resources. | Does not automatically join every Project or App. |

Apps can be installed by organization Admins and by people granted the `jai.studioapp.install` permission through IAM.

## What Project roles determine

Project roles define roles within the target Project. Studio Projects have three roles—"Admin," "Editor," and "Member"—and no Viewer (read-only) role.

| Project role | Main scope within the Project | Member and Project management | Suitable owner |
|---|---|---|---|
| Admin | Full access to Project resources. | Handles settings and member management. | Project owner |
| Editor | Creates, updates, and deletes Project resources. Can use Studio Assistant. | Cannot manage members. | Builder |
| Member | Can run Agents and Workflows, and can create many kinds of resources. | Cannot manage members or the Project. | Business user |

"Member" is not a view-only role. If you want someone to only review content, create a custom role that allows only the required actions and assign it.

A Project "Admin" is separate from an organization "Admin." It does not delegate organization settings or organization-wide installation.

Before removing, demoting, or causing the last administrator to leave, assign a successor.

## Check App-specific conditions in addition

People become App members in the following ways.

- **Added by an App Admin**: An App Admin adds them from the "Members" screen.
- **Opening an App shared with all organization members**: When someone first opens an App (a free App) for which sharing with all organization members is enabled, they join as a Member.
- **An organization Admin joins by themselves**: Only organization Admins can join by themselves, and they always join as Admin. In a paid App, this uses a seat.

To determine whether work can actually be performed in an App, check in the following order.

1. **Organization**: Confirm that the user belongs to the correct organization.
2. **App membership**: Confirm that the user has been added as a member of the target App with a role appropriate to the purpose.
3. **Seats**: In a paid App, check that the number of members has not reached the seat limit.
4. **Sharing scope**: Check whether Agents, Pages, Workflows, Datasets, and other items in the App are published or shared with the user.
5. **Settings and authentication**: Check whether shared connections, Project Secrets, the user's own connection, and external-service access are required. Only App Admins can configure shared connections and Secrets.

> **Becoming an App member does not automatically enable every action in an App.** Also check seats, configuration, publication scope, and permissions in connected services.

## Do not confuse sharing scope with roles

Roles define "the scope of actions a person can change or run," while sharing scope defines "which people can find and use the target resource." Both must be satisfied for the expected action to be available.

| Situation | Main cause | What to check |
|---|---|---|
| Has joined the Project but cannot find the target Agent or Workflow | The resource's sharing or publication scope does not include the person. | Publication status, sharing destinations, and usage permission for the target resource |
| The resource is displayed but cannot be edited | The person lacks Project role or IAM change permission. | Project role, Custom roles, and the target resource's policy |
| The App opens but a feature run fails | Connection, Secret, external-service permission, seats, or similar setup is incomplete. | Displayed guidance, App configuration status, and the user's authentication |
| Received a shared link but cannot open it | Receiving a link does not grant access. | Correct organization, Project and App participation status, and sharing scope |

When you share with the entire organization or Project, more people can view the resource. Do not expand sharing scope to avoid a permission shortage; first decide the required users, actions, and data.

## Common misunderstandings

- Being an **organization administrator does not mean every App can be used immediately**. Check App participation (which uses a seat in a paid App) and connections.
- Being a **Project administrator does not mean organization members or contracts can be managed**.
- Being an **Editor does not mean members can be managed**. Member management is a Project Admin action.
- **"Member" is not a safe, view-only role.** Members can run Agents and Workflows and create many kinds of resources.
- Sending a **shared link does not mean permission configuration is unnecessary**.

## If an expected action is unavailable

If "Members," "IAM Role Settings," or "Settings" is not displayed, or an expected action is unavailable, check the following.

1. Confirm that you opened the correct organization, Project, and App.
2. Confirm whether the target person has joined the organization, Project, and App.
3. Separate which of organization role, Project role, and IAM is insufficient.
4. In the App, check guidance for membership, seats, settings, and connections.
5. If it remains unresolved, consult an administrator with the screen and displayed message. Do not share Secret values.

## Pre-administration checklist

- [ ] Separated the people who need organization administration from those who need Project administration
- [ ] Checked organization roles and Project roles separately
- [ ] Prepared a custom role for people who should have read-only access
- [ ] Separated App usage conditions into membership, seats, configuration, and sharing scope
- [ ] Used IAM only for actions not covered by basic roles
- [ ] Confirmed that the last Project administrator will not be removed

## Next articles

- [Manage Project and App Members](manage-project-and-app-members.md)
- [Complete App Setup](../use/complete-app-setup.md)
- [Permission and Authentication Errors](../troubleshooting/permission-and-auth-errors.md)
