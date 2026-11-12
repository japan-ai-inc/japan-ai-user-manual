# Understand Organization, Project, and App Permissions

In JAPAN AI Studio, permissions to manage organizations and Projects are separate from the conditions for using and configuring Apps. Before making changes, organize the scope of actions you will delegate.

This article explains how administrators can assess permission relationships among organizations, Projects, and Apps. For invitations, role changes, and removals, see [Manage Project and App Members](manage-project-and-app-members.md); for initial configuration, see [Complete App Setup](../use/complete-app-setup.md).

## Scope of this article

This article distinguishes the following three layers.

1. **Organization roles**: Basic roles as an organization member. They form the basis for actions involving organization settings, members, and organization-wide resources.
2. **Project roles**: Roles assigned to people who join an individual Project. They determine the scope for viewing, using, creating, and managing resources in the Project.
3. **App-specific conditions**: Conditions for opening, using, and configuring an installed App. In addition to Project access, user registration, user count, connections, Secrets, and other factors may apply.

It does not cover procedures for configuring individual resources. For detailed error isolation, see [Permission and Authentication Errors](../troubleshooting/permission-and-auth-errors.md).

> **Belonging to an organization, participating in a Project, and being able to use an App for work are not the same state.** Even if one condition is met, the target action may be unavailable if another condition is missing.

## First, understand the overall permission model

| Layer | Main management targets | Typical roles and conditions | Primary managers | What this layer alone does not determine |
|---|---|---|---|---|
| Organization | Organization settings, organization members, and organization-wide resources | "Admin," "Editor," "Member," and IAM roles as needed | Organization administrators | Participation in individual Projects and availability for each App |
| Project | Project members, Agents, Workflows, Pages, and more | "Admin," "Editor," "Member," and "Viewer" | Project administrators | User count, external-service permissions, and App-specific settings |
| App | Users, settings, and required connections and Secrets for installed Apps | Project access, users and user count, and App-specific requirements | Project administrators who manage the App and others | Organization-wide administrative permissions and the user's own external-service permissions |

IAM supplements actions that basic roles do not cover. In organizations and Projects where "IAM Role Settings" is displayed, you can check system roles and "Custom roles."

> Do not treat someone who has only been assigned an IAM role as an App user if they have not joined the Project. Also check the person's Project participation status.

## What organization roles determine

Organization roles define the basic scope of actions in the organization. The basic roles are "Admin," "Editor," and "Member."

| Organization role | What it primarily allows | Administrative approach |
|---|---|---|
| Admin | Broadly manages the organization and organization-wide resources. | Limit this role to people who handle organization settings and member management. |
| Editor | Has scope to manage organization-wide resources other than IAM management. | Does not necessarily always have the same actions as an Admin. |
| Member | Serves as the basis for viewing and running permitted resources. | Does not automatically join every Project or App. |

Whether an App can be installed depends not only on organization roles but also on IAM and availability conditions.

## What Project roles determine

Project roles define roles within the target Project. The roles are "Admin," "Editor," "Member," and "Viewer," though available choices on screen may vary by configuration.

| Project role | Main scope within the Project | Member and Project management | Suitable owner |
|---|---|---|---|
| Admin | Generally has full access to Project resources. | Handles settings and member management. | Project owner |
| Editor | Creates, updates, and deletes Project resources. | Cannot perform some management actions. | Builder |
| Member | Generally views and runs resources. May also create and update items depending on the target. | Is not an administrator of the entire Project. | Business user |
| Viewer | Read-only. | Is not expected to create, update, or delete. | Content reviewer |

A Project "Admin" is separate from an organization "Admin." It does not delegate organization settings or organization-wide installation.

Before removing, demoting, or causing the last administrator to leave, assign a successor.

## Check App-specific conditions in addition

To determine whether an App can be opened, first check access to the target Project.

To determine whether work can actually be performed in an App, check in the following order.

1. **Organization**: Confirm that the user belongs to the correct organization.
2. **Project**: Confirm that the user has joined the Project for the target App and has a Project role appropriate to the purpose.
3. **App usage conditions**: Check user registration, user count, and contractual conditions.
4. **Sharing scope**: Check whether Agents, Pages, Workflows, Datasets, and other items in the App are published or shared with the user.
5. **Settings and authentication**: Check whether shared connections, Project Secrets, the user's own connection, and external-service access are required.

> **Joining a Project does not automatically enable every action in an App.** Also check user count, configuration, publication scope, and permissions in connected services.

## Do not confuse sharing scope with roles

Roles define "the scope of actions a person can change or run," while sharing scope defines "which people can find and use the target resource." Both must be satisfied for the expected action to be available.

| Situation | What may occur | What to check |
|---|---|---|
| Has joined the Project but cannot find the target Agent or Workflow | The resource's sharing or publication scope may not include the person. | Publication status, sharing destinations, and usage permission for the target resource |
| The resource is displayed but cannot be edited | The person may lack Project role or IAM change permission. | Project role, Custom roles, and the target resource's policy |
| The App opens but a feature run fails | Connection, Secret, external-service permission, user count condition, or similar setup may be incomplete. | Displayed guidance, App configuration status, and the user's authentication |
| Received a shared link but cannot open it | Link sharing and granting access may be separate. | Correct organization, Project participation status, and sharing scope |

When you share with the entire organization or Project, more people can view the resource. Do not expand sharing scope to avoid a permission shortage; first decide the required users, actions, and data.

## Common misunderstandings

- Being an **organization administrator does not necessarily mean every App can be used immediately**. Check Project participation, user count, and connections.
- Being a **Project administrator does not mean organization members or contracts can be managed**.
- Being an **Editor does not necessarily mean members can be managed**. Check the role, IAM, and whether the screen action is available.
- Being able to **view does not necessarily mean you can run**.
- Sending a **shared link does not mean permission configuration is unnecessary**.

## When you cannot confirm

If "Members," "IAM Role Settings," or "Settings" is not displayed, or an expected action is unavailable, check the following.

1. Confirm that you opened the correct organization, Project, and App.
2. Confirm whether the target person has joined the organization and Project.
3. Separate which of organization role, Project role, and IAM is insufficient.
4. In the App, check guidance for user registration, user count, settings, and connections.
5. If it remains unresolved, consult an administrator with the screen and displayed message. Do not share Secret values.

## Pre-administration checklist

- [ ] Separated the people who need organization administration from those who need Project administration
- [ ] Checked organization roles and Project roles separately
- [ ] Separated App usage conditions into participation, user count, configuration, and sharing scope
- [ ] Used IAM only for actions not covered by basic roles
- [ ] Confirmed that the last Project administrator will not be removed

## Next articles

- [Manage Project and App Members](manage-project-and-app-members.md)
- [Complete App Setup](../use/complete-app-setup.md)
- [Permission and Authentication Errors](../troubleshooting/permission-and-auth-errors.md)
