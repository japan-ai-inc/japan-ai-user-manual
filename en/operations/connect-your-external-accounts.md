# Connect Your Own External Accounts

An Agent or Workflow that uses an external service, or an App that contains one, may require authentication by the user. This article explains how to **create a personal connection with your own account, check its status, and disconnect or re-authenticate it when needed**.

A personal connection authenticates you to an external service that you use. Its purpose and administrator differ from those of a shared connection used by an entire Project or App and from Secrets such as API keys. Do not assume that other users' settings or App-wide settings are complete merely because your own connection is ready. See [Choose Between Personal Connections, Shared Connections, and Secrets](choose-personal-shared-connections-and-secrets.md) for how to choose.

If you are starting from an installed App, first see [Open an Installed App and Get Started](../use/open-installed-app.md). If you are starting from a shared Project, first see [Get Started in a Shared Project](../use/start-with-shared-project.md).

## Before you begin

| Check | What to check |
|---|---|
| Where you will use it | The organization, App or Project, and Agent, Workflow, or work screen to use |
| Connection target | The external service that requests the connection and your account approved for business use |
| Necessity | Whether the screen says "Connection required" or the feature guidance says your authentication is required |
| Scope of impact | Data, destinations, and update targets that an Agent or Workflow may reference or run after connection |
| Contact | The person who provided the App or Project; the administrator if shared settings are required |

> The displayed connection targets, entry points, and operations available vary by the organization's availability conditions, permissions, and App or Project configuration. If guidance for a personal connection is not shown, a connection may be unnecessary or a shared connection may be used. Do not add settings based on assumptions.

> Do not paste passwords, API keys, or authentication tokens into chat, inquiries, or screenshots. If an external-service authentication screen such as OAuth opens, confirm the service name and account to sign in with before proceeding.

## Where to make a personal connection

You mainly start a personal connection from the following locations. At either entry point, confirm that the target App or Project and connection service match the guidance.

| Entry point | When to use it | How to proceed |
|---|---|---|
| App connection requirements | When you open an App or are asked for personal authentication while using it | From "Settings" in the App or the displayed connection guidance, choose the required "Personal connectors." |
| Project "Connector Settings" | When you need to check or add a connection in a Project | Open "Connector Settings" and check "Personal connectors." If you can add one, select "Personal connector" from "New Connector." |
| Guidance when running an Agent or Workflow | When credentials are requested at runtime | From "Connect credential" or the displayed connection guidance, connect only the specified service. |

If "Personal connectors" is not shown in "Connector Settings," or "New Connector" cannot be used, the configuration may not allow user setup or the feature may not be available. Ask the responsible person whether a shared connection or permissions action is needed.

## 1. Choose the required personal connection

1. Open the App or Project you will use and identify the feature that says a connection is required.
2. Open "Connector Settings" from "Settings" or the indicated entry point. If a connection screen opens in the App, proceed from that screen.
3. Choose "Personal connectors." On the add screen, choose "Personal connector."
4. Select the external service indicated in the guidance. Only available services, such as Google or Microsoft, may be shown.
5. Confirm the service name, account to connect, and purpose, then select "Connect" or the displayed connection action.

If you move to an external-service authentication screen, sign in to that service and review and allow the requested permissions. Required permissions and screens vary by connection target. Do not connect using an account that is not approved for business use, another person's account, or an account whose test status is unclear.

> "Project connector" and "Shared connection" are settings separate from a personal connection. Do not choose them for the purpose of setting up your own authentication as a regular user. If an App-wide connection is "Not set" or "Invalid," ask an administrator to address it.

## 2. Complete authentication and check the connection status

After authentication, you return to the original screen or "Connector Settings." Check the following in order.

1. Confirm that the selected service connection appears under "Personal connectors."
2. If the App has a requirements screen, confirm that the personal status is "Connected."
3. If you can open connection details, check "Account," "Connection type," and "Last Authentication Date." Confirm that the connection type is "Personal connection."
4. Return to the indicated Agent, Workflow, or App work screen, and confirm that it can be used with a permitted, small operation.

The App requirements screen may show "Using shared connector" instead of "Connected." In that case, ask the responsible person whether you should add your own personal connection or are expected to use the shared setting. If it remains "Connection required," authentication may not have completed, you may have selected a different connection target, or the connection status may not have loaded.

**A displayed connection status and successful business processing are different things.** For example, if an Agent references external data or a Workflow sends email or updates data, check the target, destination, update target, and result. Do not immediately repeat a process whose completion is unclear.

## How Agents and Workflows use it

A personal connection is used by features that require the connected external service. Not every Agent or Workflow automatically uses your connection.

| What you use | Relationship to a personal connection | What to check before use |
|---|---|---|
| Agent | Used when an external tool or connector configured on the Agent requires your authentication. | Agent name, services it references or runs, and data scope |
| Workflow | Used when processing that uses an external service in the Workflow requires your authentication. | Run target, whether it sends, updates, or saves, and the result |
| App work screen | You may be asked for a personal connection when the screen uses an external service through an Agent or Workflow in the background. | Screen guidance and whether the required connection is personal or shared |

> External services may be accessed with the permissions of the connected account. Connecting alone does not necessarily run an operation, but check target data and external effects before running an Agent or Workflow.

## Disconnect and reconnect

If you change accounts, finish using a service, or authentication becomes invalid, check and address the relevant personal connection.

### Disconnect

1. Under "Personal connectors" in "Connector Settings," open the connection to disconnect.
2. Confirm that the service name, account, and connection type are "Personal connection."
3. Select "Disconnect," check the target again on the confirmation screen, and complete "Disconnect."
4. Confirm that the status changed in the list or App requirements screen.

Disconnecting may make Agent, Workflow, or App features that depend on the connection unavailable. Disconnect only connections you no longer need. Do not change shared connections or other users' connections at your own discretion.

### Reconnect or re-authenticate

If you cannot use the connection because authentication has expired, the external service changed, or you connected the wrong account, first check the relevant connection and displayed error. If the screen shows "Re-authenticate" or reconnect guidance, follow it and authenticate again with the correct account. If there is no re-authentication entry point, ask the responsible person before adding or disconnecting a connection.

For decisions and handover when a connection expires or an owner changes, see [Handle Expired Connections and Owner Changes](handle-expired-connections-and-owner-changes.md).

## If it does not work

| Situation | First things to check |
|---|---|
| "Connection required" is displayed | The target App or Project, required service, whether a personal connection is required, and whether you opened the correct organization |
| The service is not in the list | App requirements, availability conditions, and permissions. Do not connect a service that is not shown by another method at your own discretion. |
| The authentication screen fails or does not return | The account you signed in with, external-service authentication conditions, and the displayed error. Do not share credentials. |
| It does not become "Connected" | Reopen the original screen after authentication. Check whether you selected a different service, account, or a non-personal connection. |
| It is connected but the Agent or Workflow fails | Run permissions, external-service access, target data, need for a shared connection, and runtime errors |
| You cannot change or disconnect a connection | Connection type, permission to operate it, and App configuration. If it is shared, ask an administrator. |

If it remains unresolved, provide the responsible person with the organization name, App or Project name, relevant Agent, Workflow, or screen, connection service, actions taken, occurrence date and time, and displayed message. Do not send passwords, API keys, tokens, or unnecessary information from authentication screens.

## Checklist

- [ ] I confirmed the App, Project, Agent, Workflow, and connection service that require a connection.
- [ ] I created a personal connection using my account approved for business use.
- [ ] I checked the status in "Personal connectors" or the App requirements screen.
- [ ] I confirmed in the connection details that the account and connection type are the intended "Personal connection."
- [ ] I checked the result of a feature that uses the connection within the permitted scope.
- [ ] I have not replaced a shared connection or Secret with my own connection.

## Next articles

- Start using an App: [Open an Installed App and Get Started](../use/open-installed-app.md)
- Start using a shared Project: [Get Started in a Shared Project](../use/start-with-shared-project.md)
- Choose personal connections, shared connections, and Secrets: [Choose Between Personal Connections, Shared Connections, and Secrets](choose-personal-shared-connections-and-secrets.md)
- Handle expired connections and owner changes: [Handle Expired Connections and Owner Changes](handle-expired-connections-and-owner-changes.md)
- [Move Your Work Forward from a Page](../use/work-with-pages.md)
- [Chat with an Agent and Check the Results](../use/chat-with-agents.md)
