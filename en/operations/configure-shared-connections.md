# Configure Shared Connections for a Project or App

When a Project or App uses the same external service, an administrator prepares a **shared connection** and assigns it to the App when needed.

This article explains how to register a shared connection and configure it under an App's "App-wide connectors." Workflows and automatic runs by triggers or Task Scheduler use only shared connections, so a shared connection is required when you use them. For choosing between types, see [Choose Between Personal Connections, Shared Connections, and Secrets](choose-personal-shared-connections-and-secrets.md). For expiration and reauthentication, see [Handle Expired Connections and Owner Changes](handle-expired-connections-and-owner-changes.md).

## Summary: which connection is used

Connections have three layers: personal, Project (shared), and organization-wide.

| What runs | Connection used |
|---|---|
| An Agent in a Project | The first one found in this order: the runner's personal connection → the Project's shared connection → the organization-wide connection |
| A Workflow | Shared connections only (the runner's personal connection is not used) |
| A connection declared as personal in an App | Only the runner's own personal connection. Even when no person is running it, it does not fall back to a shared connection |
| Automatic runs by triggers or Task Scheduler | A shared connection is required |

## Before you begin

- Check the correct organization and target Project or App. Do not confuse environments.
- Check permission to change connection settings. Only App Admins can configure an App's shared connections.
- Decide the connection destination, purpose, data, and execution scope.
- Prepare an account for the external service that is approved for team, department, or business use. Check external service terms and organization operations before using a personal account for shared use.
- Decide the authentication owner, connection administrator, and contact for expiration.

> Processing that uses a shared connection is affected by the permissions of the authentication account at the external service. Use a business account with minimum required permissions, and do not casually share personal permissions.

If an item is not shown, do not create another connection; ask the responsible administrator.

## Separate connection users from the authentication account

For a shared connection, distinguish these three parties.

| Category | Who | What to check |
|---|---|---|
| Person who configures it | Administrator who creates and assigns the connection | Whether they can manage the target Project/App, service, connection type, and configuration result |
| User or process that uses it | Users or automated processes using Agents, Workflows, or business screens | The feature that uses the shared connection, executable operations, and users' access scope |
| Authenticated account | Business account that signs in or authenticates at the external service | The account's data scope, execution permission, and management responsibility on expiration or transfer |

Configuring a shared connection does not grant management permission to users. On the other hand, when users run processing using a shared connection, reading, sending, or updating at the external service may occur with the authenticated account's permissions. Check both user access permissions and external service account permissions.

## 1. Open Project "Connector Settings"

1. Open the target Project, then open **"Connector Settings"** from the settings screen. For an App, open connection settings from **"Settings"** in the target App.
2. Check **"Project connectors."** Connections there are candidates for shared use in the Project.
3. Check existing connections for the same service, their names, authentication accounts, purposes, and connection types.
4. Also check **"Organization-wide connectors."** Organization-wide connections are used by Agents in the Project when no personal or shared connection is found.

Do not duplicate connections for the same service; check existing connections before adding one.

## 2. Add or copy a shared connection

If you cannot use an existing candidate, add a connection to the Project.

1. Select **"New connector."**
2. Select **"Project connector."** On a screen that displays connection types, check that it is a **"Shared connection."**
3. Select the external service from the list. Do not substitute another service for one that is not displayed.
4. Give the connection a name that identifies its purpose and authentication account. Example: `Sales shared_inquiry handling`. Do not include secret values such as passwords, API keys, or tokens in the name.
5. If an external service authentication screen opens, sign in with an approved business account, check requested permissions, and authenticate.
6. Return to the Project and check that the connection appears in "Project connectors" and that its service, name, and connection type are intended.

When using an existing personal connection from a candidate list, **"Copy"** or **"Copy and configure"** may appear. Before doing so, check whether the source external account can be shared and whether it remains manageable after the owner changes.

> Even if a connection name appears to be for shared use, that does not make the external service account permissions safe. Check the account used for authentication, OAuth consent contents, and permissions for target data. Do not paste credentials into chat, procedures, or similar materials.

## 3. Assign it to an App shared requirement

When an App requires a shared connection, creation in a Project alone may not complete the setup. In App settings, select and assign the required connection.

1. Open the target App and check connection requirements from **"Settings."**
2. In **"App-wide connectors,"** check services that are not set and their status. Statuses include **"Not set,"** **"Checking,"** **"Configured,"** **"Disabled,"** and **"Stopped."**
3. Select the configuration action for the target service, then select the shared connection checked in step 2 from **"Connectors available for this App."**
4. Select **"Configure"** and wait for completion to appear. To use an organization-wide candidate, select it from **"Organization-wide connectors,"** then check and execute the displayed **"Copy and configure."**
5. Return to the App connection requirements and check that the target service is **"Configured."**

If "Connector was created, but configuration for the App failed" appears, do not recreate the same connection. Select the created connection and configure it again. If "This connector's sharing scope does not match the App's requirements, so it cannot be connected" appears, check whether you confused a personal and shared connection or the target Project.

## Which connection an Agent or Workflow selects

Connection selection is fixed as shown in the "Summary" table at the top. Administrators check the following for each situation.

| Execution situation | Connection used | What an administrator checks |
|---|---|---|
| Test an Agent in a Project | The tester's personal connection takes priority if it exists; otherwise the shared connection, then the organization-wide connection | A successful test with your own personal connection does not show what users without a personal connection get; they use the shared connection. Check results with the shared connection as well. |
| Test a Workflow in a Project | Shared connections only | Whether a shared connection is configured |
| A user operates an Agent or business screen in an installed App | Connections the App declares as personal use only the user's own personal connection. "App-wide connectors" requirements use the assigned shared connection | Check guidance such as "Connection required." |
| Workflows and automatic runs by triggers or Task Scheduler | Shared connections only | Check whether the account can be used with no executor present and check send/update destinations. |

A shared connection cannot substitute for a connection the App declares as personal. Guide users who need a personal connection to [Connect Your Own External Accounts](connect-your-external-accounts.md).

> Processing that uses a shared connection runs at the external service with the shared authentication account's permissions, not the executor's own permissions. For email sending, record updates, file changes, and similar operations, check the target and result with a small amount of approved data before expanding use.

## 4. Check operation and management responsibility before use

Separately check configuration status and actual processing.

1. Identify the Agent, Workflow, or App business screen you plan to use.
2. Prepare approved test data and a safe send or update destination.
3. Check user operations and, if there is automatic execution, its processing, each in a small scope.
4. In the external service, check that the data read, destination, update destination, and result are intended.
5. Record the connection name, target, purpose, management owner, and contact. Do not record actual values.

Even when it is "Configured," it can fail because of insufficient permission at the external service. Do not treat merely opening the screen as readiness to use it; check a representative operation.

## If something does not work

| Situation | What to check and do |
|---|---|
| "Connector Settings" or "New connector" is not displayed | Check whether it is the correct organization/Project and whether permission and feature availability conditions exist to manage shared connections. Do not substitute a personal connection for a feature that is not displayed. |
| "You do not have permission to change this setting" appears | Check permission to create connections and assign an App-wide connection, then ask an administrator with permission. |
| The App remains "Not set" or "Checking" | Do not stop after creating the connection. Check that you assigned it under the App's "App-wide connectors" and display the status again. |
| It says the sharing scopes do not match | Check that you selected a shared rather than personal connection, that it belongs to the target Project, and that it is the service required by the App. |
| It becomes "Disabled" or "Stopped" | Check the external service account, consent, expiration, and management status. Do not repeatedly disconnect or recreate it; for expiration handling, see [Handle Expired Connections and Owner Changes](handle-expired-connections-and-owner-changes.md). |
| An Agent or Workflow fails | Check the feature that ran and which connection the "Summary" table says is used, external service permission, target data, and the error at execution. Workflows and automatic runs require a shared connection. |

For an inquiry, include the organization name, Project or App name, connection destination service, connection name, operation time, displayed status/message, target Agent/Workflow, and operations already checked. Do not send passwords, API keys, tokens, or confidential information from authentication screens.

## Checklist

- [ ] I checked the correct organization, Project or App, and connection destination service
- [ ] I checked that I have permission to configure and assign a shared connection
- [ ] I selected an approved business account and checked minimum required external service permissions
- [ ] I checked "Project connector" and "Shared connection" and registered or copied it
- [ ] In the App, I assigned the correct connection under "App-wide connectors" and checked "Configured"
- [ ] I checked connection selection for Agents, Workflows, and automatic execution, and users who need personal connections
- [ ] I checked results in the external service within the approved scope
- [ ] I recorded the management owner, purpose, dependent Projects/Apps, and contact for expiration

## Next articles to read

- Choose a connection type: [Choose Between Personal Connections, Shared Connections, and Secrets](choose-personal-shared-connections-and-secrets.md)
- Check initial App setup: [Complete the Initial Setup of an App](../use/complete-app-setup.md)
- Connect your own external account: [Connect Your Own External Accounts](connect-your-external-accounts.md)
- Handle connection expiration and owner changes: [Handle Expired Connections and Owner Changes](handle-expired-connections-and-owner-changes.md)
