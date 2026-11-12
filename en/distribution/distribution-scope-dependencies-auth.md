# Constraints on Distribution Scope, Dependencies, and Authentication Settings

Publishing as an App does not duplicate the development Project as-is. A release includes the definitions of selected resources, some data explicitly specified for initialization, references between resources, and **requirements** for credentials to configure at the installation destination. It does not carry daily business data, data added by users, authentication state for external services, or the actual values of Secrets and tokens.

This article explains for creators what can be published, what to prepare at the installation destination, and how dependencies are resolved to entities there. For in-organization installation, see [Self-Install in Your Organization as the Creator](self-install-in-organization.md). For post-installation setup by users and administrators, see [Complete the Initial Setup of an App](../use/complete-app-setup.md).

## Related articles

- [Design the Configuration of a Distributable Page](../development/design-distributable-page-config.md)
- [Choose Between Personal Connections, Shared Connections, and Secrets](../operations/choose-personal-shared-connections-and-secrets.md)
- [Configure Shared Connections for a Project or App](../operations/configure-shared-connections.md)
- [Register, Update, and Delete Project Secrets](../operations/manage-project-secrets.md)
- [Complete the Initial Setup of an App](../use/complete-app-setup.md)
- [Self-Install in Your Organization as the Creator](self-install-in-organization.md)

## Scope of this article

Here, "resources to publish" are Project resources selected for inclusion in an App release. In "Resources to publish," you can choose **"Include all,"** **"Select individually,"** or **"Do not include"** for each target type. Being selectable does not mean the installation destination's data, credentials, or permission to use external services are automatically ready.

This article does not cover normal resource limits, contract conditions, user counts, or pricing. See [Index of Limits and Availability Conditions](../operations/limits-and-availability-index.md). It also does not cover submission and review procedures.

> Do not write the provider Project ID, resource ID, Page URL, external connection ID, API key, cookie, access token, or Secret value in Page source, environment variables, initial Storage files, descriptions, or logs. IDs are not the same at the installation destination.

## What to include in a release and what not to include

Content selected as a publishing target is prepared as managed resources for the installation destination. "Initial data" in the following table does not mean moving all business data.

| Target | Content handled in a release | What is not carried and what to check at the installation destination |
|---|---|---|
| Agents and Skills | The specified version and references required within the release | Individual settings and usage history of the provider Project. Check whether the version is available at the installation destination. |
| Workflows | Workflow definitions and references to declared resources | Run history, processing in progress, and authentication state for external services. |
| Pages | Page artifacts, display settings, and declared resource references | Provider Page IDs, URLs, and user sessions. For a design that does not embed fixed IDs, see [Design the Configuration of a Distributable Page](../development/design-distributable-page-config.md). |
| Custom Objects | Schema (Object, field, relationship, and access definitions), plus explicitly specified initial records when needed | All ordinary business records and records users add or update after installation. Individually decide whether to include initial records. |
| Storage | Explicitly selected initial files and the logical destination scope (including Project Storage seeds) | All files in the Project, files users add after installation, and provider file IDs. Even if a destination reference becomes unavailable, it may remain as a saved selection until explicitly removed. |
| Datasets | The destination container and display information required as a Dataset | The assumption that source Dataset body content, search results, and usage history are copied as-is. Prepare and register required knowledge data after installation. |
| Custom roles | Role definitions included in the App | Automatic assignment to members at the installation destination. Check the impact of role changes at installation and update. |
| Automatic execution | Definitions for automatic execution triggered by a schedule or event, and logical references to target resources | Past run results and authentication state for external connections. Check connections, destinations, and update targets before execution. |
| Connections, Secrets, and API keys | Required credential types, usage scope, and requirements to meet at the installation destination | Connection IDs, OAuth tokens, passwords, Secret values, and actual API key values. The owner at the installation destination configures them. |

Even when including initial Storage or Custom Object data, do not mix in personal information, customer information, credentials, or files in operation used for validation. Select only the minimum truly needed to create the initial state.

## How to select connectors and resources

When you can select connectors on the publishing screen, candidates include connectors derived from the provider and connectors that can be added. Check them by search, icon, and personal/shared display. When you identify an external account or connector required by the App, do not move the provider's connection; decide who configures it at the installation destination and how.

For individual resource selection, check Agents, Workflows, Pages, Skills, automatic execution, Datasets, Custom Objects, custom roles, and Project Storage for each target. Individual selections and Storage seeds have limits on the number of items and data volume for each target, so check the publishing screen and validation results. If an item is not shown in search results or a saved Storage reference becomes unavailable, check its name, storage destination, and necessity before explicitly removing the selection.

## 1. Inventory publishing targets and dependencies

1. List the Agents, Skills, Workflows, Pages, Custom Objects, Datasets, Storage, and automatic executions used by the App.
2. For each item, separate the "definition" to distribute, "initial data" needed only for initialization, and "business data and credentials" created at the installation destination.
3. Select the required types in **"Resources to publish."** Do not omit dependencies that are not directly displayed, such as a Workflow called by a Page or an Agent or Dataset used by a Workflow.
4. When including initial files or records, check whether it remains safe if data with the same name conflicts at the installation destination and what content you want to retain during updates.
5. Before publishing, list connections, Secrets, and user-specific connections to reconfigure at the installation destination.

> "Include all" does not specify inclusion of credentials or production data. However, we recommend narrowing distribution to the scope required for App functionality so that unnecessary definitions and initial data are not distributed.

## 2. Make dependencies resolvable by logical name

The distribution process does not copy provider internal IDs to the installation destination. Instead, it maps resources at the installation destination using logical names declared in the release. For example, a Workflow, Agent, Custom Object, or Storage root referenced by a Page must be resolvable as a target delivered in the same release.

Declare Page references as logical names in a distribution reference file. For the syntax and Page design that avoids fixed IDs, see [Design the Configuration of a Distributable Page](../development/design-distributable-page-config.md). IDs in arbitrary text, built JavaScript, URLs, or external service settings are not automatically replaced.

| Dependency status | What the creator does |
|---|---|
| A Page uses a Workflow, Agent, Object, or Storage | Match the Page's logical reference with the logical name on the publishing-target side. |
| A Workflow or automatic execution uses a Dataset or other resource | Include the required target in resources to publish, and separately document the work to prepare data at the installation destination. |
| A Skill or Agent depends on a specific version | Check a specific available version rather than an arbitrary "latest" version. |
| It uses an external service, arbitrary URL, or fixed ID | Do not assume it is automatically resolved during distribution; design and document it as a value to configure at the installation destination. |

New resource identifiers are created at the installation destination, so implementations with fixed IDs may lose their references after distribution. Before publishing, check the design that resolves installation-destination entities from logical names and that no dependency declaration is missing.

## 3. Distribute only credential requirements

A release can show the installation destination the requirements for credentials, but it does not duplicate the provider's credentials themselves. Design shared credentials and credentials set per user separately.

1. For each external service, decide whether a shared connection, personal connection, or Project Secret is required. For guidance, see [Choose Between Personal Connections, Shared Connections, and Secrets](../operations/choose-personal-shared-connections-and-secrets.md).
2. When a shared connection is required, decide the operational account at the installation destination, minimum permissions, and re-authentication owner. Follow [Configure Shared Connections for a Project or App](../operations/configure-shared-connections.md) for creation and assignment to the App.
3. When values such as API keys or tokens are needed, create or select a Project Secret at the installation destination and map it to the item required by the App. For value management, see [Register, Update, and Delete Project Secrets](../operations/manage-project-secrets.md).
4. For features that require each user's own connection, guide each user to configure it with their own account. Do not substitute an administrator's personal connection.
5. After installation, check the status of **"Connectors"** and **"Secret Management"** in the App's **"Settings,"** then perform representative operations with approved test data. For the overall checking order, see [Complete the Initial Setup of an App](../use/complete-app-setup.md).

> Even if a connection or Secret is shown as "Configured," external-service permissions, target data, destinations, and each user's own connection are not necessarily verified. For Apps that include automatic execution, also check shared connections used when no user is executing it.

## Pre-publish checks and displayed errors

On the publishing screen, use **"Pre-publish check"** to check selections and dependencies. You may be unable to complete the check or create the release when there are unresolved Page references, references containing provider IDs, undeclared Dataset references, versions of dependencies that are not fixed, inconsistencies in initial-data references, or similar issues. The screen may display **"The pre-publish check could not be completed. Review the content and run it again."** or **"The release could not be created. Review the content and try again."**

In that case, correct the issue in the following order:

1. Check the target resource name and selected resources to publish. Check whether a target referenced by a Page was omitted and whether logical names match.
2. Replace provider UUIDs, IDs, URLs, and unspecified dependency versions with logical references or installation-destination settings.
3. For Dataset, Storage, and Custom Object data, separate definitions and initial data that can be distributed from data to prepare after installation.
4. Do not work around the issue by adding credential values to artifacts. Instead, review required connections and Secrets and the installation procedure.
5. After correction, run **"Run pre-publish check again."** If a resource was deleted or changed after selection, update the selection before checking.

## Common misconceptions

| Misconception | What to check |
|---|---|
| Distributing an App also makes provider customer data available | Business data is not a distribution target. Explicitly specify only required initial data and separately prepare data at the installation destination. |
| Selecting a connection as a publishing target means no authentication is needed at the installation destination | What is distributed is the requirement. Configure connections, Secrets, and each user's own connection at the installation destination. |
| Writing a resource ID into a Page ensures it can be referenced | IDs differ at the installation destination. Use logical names and follow the design in [Design the Configuration of a Distributable Page](../development/design-distributable-page-config.md). |
| Selecting Storage moves all files in the Project | Only explicitly specified initial files are handled. Business files and user files after installation are separate. |

## When you cannot confirm something

If you cannot load **"Resources to publish,"** a target disappears from the selection, a pre-publish check fails, or you cannot check connection or Secret status after installation, do not work around the issue by embedding IDs or credentials directly. Record the target organization, Project, and App; selected resources; dependent logical names; displayed messages; operation date and time; and settings already checked, then ask the responsible owner. Do not include Secret values, API keys, passwords, tokens, or cookies in inquiries.

## Next article

- [Design the Configuration of a Distributable Page](../development/design-distributable-page-config.md)
- [Choose Between Personal Connections, Shared Connections, and Secrets](../operations/choose-personal-shared-connections-and-secrets.md)
- [Configure Shared Connections for a Project or App](../operations/configure-shared-connections.md)
- [Register, Update, and Delete Project Secrets](../operations/manage-project-secrets.md)
- [Complete the Initial Setup of an App](../use/complete-app-setup.md)
- [Self-Install in Your Organization as the Creator](self-install-in-organization.md)
