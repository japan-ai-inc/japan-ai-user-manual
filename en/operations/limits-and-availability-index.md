# Index of Limits and Availability Conditions

This index directs you to the **authoritative article to check** when you cannot use a feature, cannot increase a quantity, or need to determine pricing or consumption. Except for frequently asked administration limits, it does not duplicate individual limits, prices, API specifications, or setup procedures. Check the applicable authoritative source and the actual screen display.

## Scope of this article

This article separates availability, permissions, and contract conditions that vary by organization from fixed specifications, and directs you to the authoritative source for each topic.

It does not list prices, contract details, or API requests and responses. Check feature-specific numbers in each authoritative source, except those listed under "Frequently checked limits" below.

> Feature visibility and availability vary by organization. Contracts, availability status, permissions, Project and App settings, and external-service authentication and permissions are relevant. The same screen or limit is not necessarily displayed in another organization, Project, or App.

## Frequently checked limits

| Target | Limit or rule | Authoritative source |
|---|---|---|
| Storage capacity | Evaluated **per Project**. The default is 10,240 MB. | [Storage Specifications and Limits](../build/storage-specs-and-limits.md) |
| Project Secret value | Up to 8,192 characters, but **4,096 bytes per value** when passed to a Page as an environment variable (larger values make the Page fail at startup). | [Register, Update, and Delete Project Secrets](manage-project-secrets.md) |
| Web login secrets | Only the password is hidden. The login ID and URL are visible to every Project member who can read the secret. | [Manage Web Login Secrets](manage-web-login-secrets.md) |
| Project API keys | Valid only while the creator is an active Project member. Scopes and expiration cannot be changed after creation. No API can be read with `credit:read`. | [Manage Project API Keys](manage-project-api-keys.md) |
| Audit Log | View on screen only (no export). It cannot be opened after the Project is deleted. | [Check Deletion, Handover, and Audit Records](deletion-handover-and-audit-logs.md) |
| Paid App charges | Monthly charge = unit price × number of active members, settled automatically at monthly renewal. | [Check App Seats, Pricing, and Contract Limits](check-app-seats-pricing-and-limits.md) |
| Plan limits | Tasks cannot be created on the free plan. The number of tasks has a per-organization limit. Skills and Pages have per-organization count limits, shown by the usage bar on their list screens. | [Workflow and Automation Specifications and Limits](../build/workflow-and-automation-specs.md), [Skill Specifications and Limits](../build/skill-specs-and-limits.md), [Page Specifications and Limits](../build/page-specs-and-limits.md) |

## First, distinguish the type of condition

Check limits and "unavailable" displays by category as follows.

| Type of condition | What to check | Article to open first |
|---|---|---|
| Feature-specific specifications and limits | Constraints on created resources, input, uploads, structure, and run settings | Feature articles below |
| Permissions and sharing scope | Who can view or operate items by organization, Project, App, or resource | [Understand Organization, Project, and App Permissions](understand-org-project-app-permissions.md) |
| Authentication and connections | Personal connections, shared connections, external-service authorization, and Secret handling | [Choose Between Personal Connections, Shared Connections, and Secrets](choose-personal-shared-connections-and-secrets.md) |
| Contracts, users, and pricing | App users, contract limits, pricing displays, and handling at termination | [Check App Seats, Pricing, and Contract Limits](check-app-seats-pricing-and-limits.md) |
| Run consumption and Project usage | Organization execution and credits; Project resource count, Storage, and recorded activity | [Check Execution Consumption and Usage](check-execution-consumption-and-usage.md) |
| API and SDK communication conditions | Authentication, errors, communication failures, timeouts, and common limits | [Common API and SDK Errors, Timeouts, and Limits](../development/api-sdk-errors-timeouts-limits.md) |

## Feature articles

Check feature specifications and limits in the authoritative sources below. Do not reuse a value for a different feature, API, or contract.

| Topic to check | Authoritative source | Not covered by this index | Also check |
|---|---|---|---|
| Agent | [Agent Specifications and Limits](../build/agent-specs-and-limits.md) | Availability depending on models or organization settings; contracted number that can be created | [Create an Agent and Set Its Instructions and Models](../build/create-agent-instructions-and-models.md), [Configure Tools, Skills, and Datasets for an Agent](../build/configure-agent-tools-skills-datasets.md) |
| Workflow and automatic execution | [Workflow and Automation Specifications and Limits](../build/workflow-and-automation-specs.md) | Consumption per run and external-service communication limits | [Configure the Task Scheduler](../build/configure-task-scheduler.md), [Change, Stop, and Hand Over Scheduled Runs](change-stop-handover-scheduled-runs.md) |
| Dataset | [Dataset Specifications and Limits](../build/dataset-specs-and-limits.md) | Credit balance and source constraints | [Prepare a Dataset and Make an Agent Reference It](../build/prepare-and-reference-datasets.md) |
| Object | [Object Specifications and Limits](../build/object-specs-and-limits.md) | Individual specifications through APIs and SQL | [Design an Object Structure](../build/design-object-structure.md), [View and Update Object Data](../use/view-and-update-object-records.md) |
| Storage | [Storage Specifications and Limits](../build/storage-specs-and-limits.md) | API conditions (capacity is per Project, default 10,240 MB) | [Organize Storage Folders](../build/organize-storage-folders.md), [Use Files in Storage](../use/use-storage-files.md) |
| Page | [Page Specifications and Limits](../build/page-specs-and-limits.md) | Development runtime and communication and execution time | [Register and Publish a Page from the Screen](../build/register-and-publish-pages.md), [Page Source Structure and Runtime](../development/page-source-structure-and-runtime.md) |
| Skill | [Skill Specifications and Limits](../build/skill-specs-and-limits.md) | Organization-specific availability and permissions for tools and connection targets | [Create and Use Skills](../build/create-and-use-skills.md) |

## Articles on administration, contracts, and connections

Check the following table for availability conditions and administration settings.

| Topic to check | Authoritative source | Key points to check |
|---|---|---|
| Project Secret | [Register, Update, and Delete Project Secrets](manage-project-secrets.md) | Only Page server processing and API call nodes can read them; Agents cannot. For reference methods, see [Page Server Processing, Environment Variables, and Secret References](../development/page-server-env-and-secrets.md). |
| Web login secrets | [Manage Web Login Secrets](manage-web-login-secrets.md) | Everything except the password (login ID and URL) is visible to Project members. |
| Project API keys | [Manage Project API Keys](manage-project-api-keys.md) | Check the scope list, validity tied to the creator, and scopes that cannot be declared in a manifest. |
| Memory | [Manage Memory and Project Shared Information](manage-memory-and-project-shared-info.md) | Agents in a Project reference only Project-scope Memory. There is no feature to move Memory between scopes. |
| Deletion, handover, and Audit Log | [Check Deletion, Handover, and Audit Records](deletion-handover-and-audit-logs.md) | Project deletion is physical deletion. The Audit Log cannot be exported and cannot be opened after deletion. |
| Migration between Projects | [Plan a Migration Between Projects](plan-project-migration.md) | Only organization-level and official resources can be linked; there is no copy feature between Projects. |
| App seats, contracts, and pricing | [Check App Seats, Pricing, and Contract Limits](check-app-seats-pricing-and-limits.md) | Paid Apps are charged at unit price × active members, and removing members lowers the next renewal charge. Check contract limits, charge confirmation, and termination guidance. |
| Run consumption and usage | [Check Execution Consumption and Usage](check-execution-consumption-and-usage.md) | Separate organization credit usage from Project usage (resources, Storage, and recorded values), and do not confuse either with App users or pricing. |
| Personal and shared connections | [Choose Between Personal Connections, Shared Connections, and Secrets](choose-personal-shared-connections-and-secrets.md), [Configure Shared Connections for a Project or App](configure-shared-connections.md) | Check ownership scope, authenticated accounts, user preparation, and external-service permissions. |
| External tools, data integrations, and MCP | [Manage External Tools, Data Integrations, and MCP](manage-external-tools-integrations-mcp.md) | Separate registration, addition to an Agent, authentication, sharing scope, and connection-target limits. Do not conclude that all users can use MCP merely because it is registered. |
| Dependencies and authentication when distributing an App | [Constraints on Distribution Scope, Dependencies, and Authentication Settings](../distribution/distribution-scope-dependencies-auth.md) | Check distribution targets, settings that cannot be carried over, and setup required at the installation target. Distinguish these from ordinary feature limits and installation procedures. |

## When using APIs and SDKs

Screen-operation limits and conditions for API or SDK calls can differ. During development, check the feature-specific authoritative source and the API article for the route.

| What to check | Authoritative source |
|---|---|
| Common API and SDK errors, timeouts, communication control, and common limits | [Common API and SDK Errors, Timeouts, and Limits](../development/api-sdk-errors-timeouts-limits.md) |
| Agents, conversations, and artifacts API | [Agents, Conversations, and Artifacts API](../api/agents-conversations-artifacts-api.md) |
| Object API | [Objects API](../api/objects-api.md) |
| Dataset API | [Datasets API](../api/datasets-api.md) (write APIs exist in production but are currently outside the support scope of this manual and may change without notice; see the public OpenAPI at https://api.japan-ai.co.jp/reference) |
| Project members API | [Project Members API](../api/project-members-api.md) (requires `member:read`) |
| Storage API | [Storage API](../api/storage-api.md) |
| Workflow API | [Workflows API](../api/workflows-api.md) |
| Page API | [Pages API](../api/pages-api.md) |
| External tool invocation API | [Tool Proxy API](../api/tool-proxy-api.md) |

> Being able to issue an API key does not necessarily mean that all API or SDK conditions are met. For key management, see [Manage Project API Keys](manage-project-api-keys.md); for execution modes, authentication, and supported scope, see [Execution Modes, Authentication, and Supported Scope](../development/execution-modes-and-auth.md).

## How to select an article and check conditions

1. Categorize the target as feature-specific, contracts and users, run consumption, API and SDK, or connections and MCP.
2. Open the applicable article and confirm the target organization, Project, App, and usage route.
3. For numbers, check the applicable conditions and units in each article; do not substitute a value from another feature.
4. If the screen shows availability conditions, limits, warnings, pricing, or balances, prioritize that display.
5. Contact the administrator or support about contract conditions or organization-specific availability not covered by the article.

## Common misunderstandings

| Misunderstanding | Correct check |
|---|---|
| The same limit can be used for another feature | Open the authoritative source for the feature, input route, and API. |
| The feature is unavailable because it is not on the screen | Check availability status, permissions, target Project or App, and contract conditions. |
| App users and run consumption use the same pricing conditions | Separate what [Check App Seats, Pricing, and Contract Limits](check-app-seats-pricing-and-limits.md) and [Check Execution Consumption and Usage](check-execution-consumption-and-usage.md) cover. |
| Everyone can use MCP or external tools because a connection was made | Check addition to an Agent, sharing scope, authentication, and external-service permissions. |
| A feature callable through an API has the same limits as the screen | Check authentication, communication, and API-specific conditions in [Common API and SDK Errors, Timeouts, and Limits](../development/api-sdk-errors-timeouts-limits.md) and each API article. |

## Troubleshooting

If you cannot find the applicable item in the authoritative source, the screen and authoritative source do not match, or organization-specific availability conditions are unclear, record the organization, Project, App, feature name, usage route, operation date and time, and screen guidance or error, then ask the administrator or support. Do not include Secrets, API keys, tokens, passwords, personal information, or confidential data in an inquiry.

## Next articles

- [Agent Specifications and Limits](../build/agent-specs-and-limits.md)
- [Page Specifications and Limits](../build/page-specs-and-limits.md)
- [Register, Update, and Delete Project Secrets](manage-project-secrets.md)
- [Check App Seats, Pricing, and Contract Limits](check-app-seats-pricing-and-limits.md)
- [Common API and SDK Errors, Timeouts, and Limits](../development/api-sdk-errors-timeouts-limits.md)
