# Manage External Tools, Data Integrations, and MCP

This article explains how **administrators organize availability conditions and configuration targets for external tools, data integrations, and MCP servers**.

For authentication ownership scope, see [Choose Between Personal Connections, Shared Connections, and Secrets](choose-personal-shared-connections-and-secrets.md). For Agent configuration, see [Configure Tools, Skills, and Datasets for an Agent](../build/configure-agent-tools-skills-datasets.md).

## Related articles

- [Choose Between Personal Connections, Shared Connections, and Secrets](choose-personal-shared-connections-and-secrets.md)
- [Configure Tools, Skills, and Datasets for an Agent](../build/configure-agent-tools-skills-datasets.md)
- [Permission and Authentication Errors](../troubleshooting/permission-and-auth-errors.md)

## Scope of this article

This article covers where organization and Project settings are managed, who can change them, and what to check before they can be used by an Agent. It does not cover connection procedures for individual services or Agent instructions.

The items displayed and whether settings can be changed vary by plan, feature availability, permissions, and App configuration. Do not conclude that a service not in this article can be connected, or that a feature not in the list is unavailable.

> Registering an external tool, storing authentication information, adding it to an Agent, and succeeding in business use are separate checks. For features that update or send information, check the connection target, run permissions, and scope of impact.

## First, distinguish what you manage

| Management target | Main role | Typical setting location | Checks required before use by an Agent |
|---|---|---|---|
| External tools and Plugins | Features that let an Agent call external services or processes. | Organization "Plugins" or Project "Tool Settings" | Authentication, sharing scope, and addition to the Agent |
| Data integrations and Datasets | Settings that import information and treat it as a Dataset for reference. | Project "Data Integration" | Target, update status, selection, and application in the Agent |
| MCP servers | Tool settings that extend external tools available to Agents. | Organization or Project "Tool Settings" | Availability conditions, permissions, authentication, and addition to the Agent |

A "Plugin" is an Agent feature, a "Connector" is an external connection, and a "Data Integration" is a Dataset entry point. Do not substitute one for another even if their names are similar.

## Whether to configure it in the organization or Project

### Items managed in the organization

Organization-wide external tools may be available from organization "Plugins." Even if a tool exists in the organization, Project access, sharing scope, Agent configuration, and authentication may still be required separately.

### Items managed in a Project

Manage work-unit external tools, Datasets, and connections used by an App in the target Project. Check items such as "Data Integration," "Tool Settings," and "Connectors" shown in the Project or App settings.

To add an MCP tool to a Project, you may need required IAM permissions in addition to at least being a Project Editor or Admin. Even at the organization level, you may not be able to create it in an organization where IAM operation controls are enabled. If the screen says "You need Editor or Admin permission to create tools," ask a person with permission.

> Even if you can change organization settings, you cannot necessarily change external data for every Project. Confirm the owner and intended use before adding or changing anything.

## Check availability conditions

External tool candidates and setting screens are not necessarily always displayed. Before adding one, check the following.

1. Open the correct organization and target Project or App.
2. Confirm whether the entry point matching your purpose appears among "Plugins," "Tool Settings," "Data Integration," "MCP Servers," and "Connectors."
3. Check disabled states, guidance, and displays about plans or features.
4. In a Project, check Editor or Admin conditions and IAM permissions if needed.
5. In an App, check whether installation is active, its settings screen is not read-only, and required sharing-scope functionality is available.

Plans and feature availability may affect adding Plugins or creating custom tools. Ask the administrator about contract details.

For data integrations too, the App settings tab and importing resources into a Project may be displayed or executable differently according to feature availability and Project update permissions. Do not treat a missing display as meaning no configuration is needed.

## What to check when adding an MCP server

MCP is a setting for extending external tools that AI Agents can use through custom servers. If you can select "MCP Server" from "Add Tool" on the screen, check the following in order.

1. Check whether the addition target is the organization or target Project so that you do not register it in the wrong scope.
2. Enter "Tool Name," "Tool Description," and "Endpoint."
3. Under "Authentication Method," select the method that matches the server requirements.
4. For OAuth, check the destination service and the account and permissions to authorize. For a Bearer token, enter only an approved token.
5. Check the connection validation result on registration. Confirm not only that it was registered in the list but also that it can retrieve the required tools.
6. Add it in the target Agent settings, then check the result with a permitted, small operation.

MCP registration may validate the URL format, detect duplicates, detect the authentication method, or validate the connection. If "MCP server is unreachable," "Failed to detect MCP server authentication," or "Failed to validate MCP server connection" appears, do not keep trying guessed URLs or authentication methods. Ask the server administrator.

> A "Bearer Token" is authentication information. Do not paste it into Agent instructions, Skill content, chat, inquiries, or screen sharing. When connecting through OAuth, do not treat an administrator's personal account as shared authentication. For authentication ownership scope, see [Choose Between Personal Connections, Shared Connections, and Secrets](choose-personal-shared-connections-and-secrets.md).

## Make it usable by an Agent

Registering an external tool or MCP does not necessarily make existing Agents run it automatically. On the Agent edit screen, choose the intended tool or Skill under "Plugins" and check that it was saved.

Datasets use a different procedure. On the Agent edit screen, select the target under "Data Integration" and choose "Apply."

| Check stage | External tools and MCP | Data integrations and Datasets |
|---|---|---|
| Administration | In organization or Project settings, check candidates, availability conditions, authentication method, and ownership scope. | In the target Project, check the Dataset, integration source, update status, and sharing scope. |
| Agent configuration | Add the intended tool from "Plugins." | Select the target Dataset from "Data Integration" and choose "Apply." |
| User preparation | Check whether it is a personal or shared connection and whether the connection target has sufficient permissions. | Check Agent and Dataset use permissions and whether the information may be referenced. |
| Operation check | Check run results with a permitted low-impact operation, such as reading. | Ask a small question whose answer is in the material, then check the reference content and answer. |

Writing only a tool name or server name in Agent instructions does not complete addition, selection, authentication, or external-service permission configuration.

## Common misunderstandings

### Registering in the organization makes it usable by every Agent

Even if a tool exists in the organization, Project, Agent, and user settings are separate. Check addition to the target Agent's "Plugins," Agent or Skill sharing scope, connection method, and the user's external-service permissions.

### A data integration uses the same settings as a Plugin

A data integration selects a Dataset and has a different entry point and confirmation action than "Plugins," which selects tools. To have an Agent reference material, select it in "Data Integration" and confirm both "Apply" and saving the Agent.

### Registering MCP immediately makes it usable by everyone

MCP server registration, addition to an Agent, authentication, and access for the Project, App, and user are separate conditions.

### Successful connection validation means business processing will succeed safely

Validation checks the connection and authentication stage. Also check the run target, send and update permissions, and external-service limits.

## If you cannot confirm it

If you cannot find "Plugins," "Data Integration," "MCP Servers," or the add operation, or authentication or validation fails, check the following.

1. Confirm that you have not confused the organization, Project, App, or setting target.
2. Check the plan, feature availability, App status, and displayed guidance.
3. Check the Project role, IAM, and sharing scope.
4. Check the authentication method and external-service access.
5. Check Agent configuration and save results.
6. If it remains unresolved, contact the administrator with the target, operation date and time, and displayed message. Do not send tokens or passwords.

## Next articles

- Choose authentication ownership scope: [Choose Between Personal Connections, Shared Connections, and Secrets](choose-personal-shared-connections-and-secrets.md)
- Configure tools, Skills, and Datasets for an Agent: [Configure Tools, Skills, and Datasets for an Agent](../build/configure-agent-tools-skills-datasets.md)
- Isolate permission and authentication errors: [Permission and Authentication Errors](../troubleshooting/permission-and-auth-errors.md)
