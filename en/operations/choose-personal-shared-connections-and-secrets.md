# Choose Between Personal Connections, Shared Connections, and Secrets

For a Project or App that uses external services, first decide "whose credentials to use and for whom." A wrong choice can make getting started or handover difficult.

This article explains how to choose between personal connections, shared connections, Project Secrets, and Web Login Secrets based on the ownership scope of credentials. For setup procedures, see the respective management articles.

## Summary: whose credentials are used

Connections have three layers: personal, Project (shared), and organization-wide. Which connection is used is determined as follows.

| What runs | Credentials used |
|---|---|
| An Agent in a Project | The first one found in this order: the runner's personal connection → the Project's shared connection → the organization-wide connection |
| A Workflow | Shared connections only (the runner's personal connection is not used) |
| A connection declared as personal in an App | Only the runner's own personal connection. Even when no person is running it, it does not fall back to a shared connection |
| Automatic runs (triggers and Task Scheduler) | No runner is present at the screen, so a shared connection is required |
| An MCP server with OAuth or Bearer authentication registered in a Project | The registrant's credentials are used as shared credentials for everyone in the Project |
| Project Secrets | Can be referenced only from Page server-side environment variables and Workflow API call nodes. Agents cannot read them |

For "as whom" triggers and tasks run, see [Change, Stop, and Hand Over Scheduled Runs](change-stop-handover-scheduled-runs.md).

## Related articles

- [Complete the Initial Setup of an App](../use/complete-app-setup.md)
- [Page Specifications and Limits](../build/page-specs-and-limits.md)
- [Connect Your Own External Accounts](connect-your-external-accounts.md)
- [Configure Shared Connections for a Project or App](configure-shared-connections.md)
- [Register, Update, and Delete Project Secrets](manage-project-secrets.md)
- [Manage Web Login Secrets](manage-web-login-secrets.md)
- [Execution Modes, Authentication, and Supported Scope](../development/execution-modes-and-auth.md)

## Scope of this article

This article covers how to choose before registration. It does not cover permission settings at external services, actual registration, updating, deletion, or how to reference them from a Page.

> Being able to connect and being allowed to share that connection for business are different. Before completing authentication, check the external service's terms of use, account permissions, target data, cost, and handover method for resignation or transfers.

## What to check first

Check whether it is an individual's or common business account, whether you need a connection or a secret value, and who updates or stops it.

Choose a personal connection when working with an individual's permissions, and a shared connection for a common business scope or automatic runs. Check Project Secrets for API keys and similar values, and Web Login Secrets for website login information.

## Comparison by type

Availability also depends on Project/App access permissions, external service permissions, and permission to operate the settings. Simply placing a connection or Secret does not mean everyone can perform the same operations.

| Type | Credential ownership scope | Mainly usable by | Main settings location and screen display | What to check when the owner transfers or leaves | Typical use |
|---|---|---|---|---|---|
| Personal connection | A connection authenticated with the individual's external service account. | The person who connected it (Agents that person runs) | "Personal connectors," "Personal Connector," and similar labels | Whether the individual continues using it, expiration, and whether the successor needs their own connection | Personal email, calendar, and similar services |
| Project/App shared connection | A connection for common processing in a Project or App. | Agents run by Project members, Workflows, and automatic runs | "Project connectors," "App-wide connectors," and similar labels | Authentication account, update owner, and dependent Apps | A department-shared contact point, shared data, and automatic runs |
| Organization-wide connection | A connection prepared for the entire organization. | Agents in a Project when no personal or shared connection exists | "Organization-wide connectors" | Authentication account and management owner | External services used across the organization |
| Project Secret | A secret value such as an API key or token stored per Project. | Page server-side processing and Workflow API call nodes (Agents cannot read them) | The "Project Secrets" tab in "Secret Management" | Issuer, expiration, update owner, and dependent features | External API keys and App tokens |
| Web Login Secret | Secret information used to log in to a website. | The configured use | "Web Login Secrets" | Account continuity, changes, and successor responsibility | Login to approved websites |

> Even if you can copy personal authentication for shared use, that does not necessarily mean the team is allowed to use it. Check external service permission and the responsible administrator.

## Guidelines for choosing

### The user does their own work with their own permissions

Choose a personal connection. In an App where each person uses their own external account, other users also need their own authentication. If "Connection required" appears, have the individual confirm the authenticated state.

Do not have someone else use an individual's credentials as a substitute.

### A team uses the same business account and process, or runs automatically

Choose a Project/App shared connection. This applies when a shared business account connects to an external service and multiple users or common processes handle the same scope. Workflows and automatic runs by triggers or Task Scheduler cannot use personal connections, so a shared connection is required.

After creating it, check the App configuration result and representative operation.

> Choosing a shared connection does not grant administrator permission to users.

### Use values such as API keys in a Project

Consider a Project Secret. The "Project Secrets" tab in "Secret Management" stores API keys and tokens per Project. They can be referenced only from Page server-side environment variables and Workflow API call nodes. They cannot be used for Agents to read. If the App has a requirement, link the correct Secret after creation.

Values are masked in lists and details. Check the name and purpose, and do not confuse test and production values or values for another Project.

> Do not put a Secret value in browser-side variables, logs, distribution files, or inquiry materials. For references from a Page, see [Page Specifications and Limits](../build/page-specs-and-limits.md) and [Execution Modes, Authentication, and Supported Scope](../development/execution-modes-and-auth.md).

### Handle login information for a website

Check Web Login Secrets. They are for website login and are different from "Project Secrets." Do not register one merely because you want to use an API key in a Project.

> Web Login Secrets hide only the password. The login ID and URL are visible to all Project members who can read the secret, including the default "Member" role.

If MFA, IP restrictions, device restrictions, or similar controls apply, check the requirements of the target feature and the responsible administrator.

## Checks during handover

For transfers, resignations, administrator changes, password changes, or API key updates, check dependent Projects, Apps, and features; the stop/update date and time; the successor; and representative operations after the change.

Record the setting name, target, purpose, management owner, and renewal date. Do not record the actual value.

## Common misunderstandings

### If an administrator connects once, all users can use it

A connection declared as personal in an App requires each user's own authentication, and a shared connection cannot substitute for it. When a shared connection is required, a personal connection alone does not meet the requirement. Check the status such as "Personal Connector" and "App-wide Connector."

### If I have my own personal connection, Workflows and automatic runs will work

Workflows use only shared connections. Automatic runs by triggers or Task Scheduler also require a shared connection.

### Connections and Secrets are the same setting

A connection authenticates and integrates with an external service; a Project Secret stores a secret value. Web Login Secrets are also a separate type. First distinguish whether you need a connection, an API key or similar value, or website login information.

### If the person who created a Secret leaves, it will always become unusable immediately

You cannot judge operation based on the creator changing alone. Check dependencies, validity, and change permission, and if necessary have the new owner update it and verify operation. Deleting it may cause some features to produce runtime errors.

### You can store API keys in Web Login Secrets

Web Login Secrets are for logging in to websites. For API keys or tokens used in a Project, check "Project Secrets." If the purpose is unclear, do not register a value; check the target App/feature guidance and responsible administrator.

## If settings or statuses are not as expected

If "Connectors" or "Secret Management" is not displayed, or "Not set," "Checking," or "Disabled" appears after setup, do not conclude that setup is unnecessary. Check the target, permissions, and account state at the external service.

For an inquiry, include the target, setting type, displayed content, and operation time. Do not send Secret values or passwords.

## Next articles to read

- [Connect Your Own External Accounts](connect-your-external-accounts.md)
- [Configure Shared Connections for a Project or App](configure-shared-connections.md)
- [Register, Update, and Delete Project Secrets](manage-project-secrets.md)
- [Manage Web Login Secrets](manage-web-login-secrets.md)
- [Execution Modes, Authentication, and Supported Scope](../development/execution-modes-and-auth.md)
- [Complete the Initial Setup of an App](../use/complete-app-setup.md)
