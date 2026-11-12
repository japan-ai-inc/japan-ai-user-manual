# Index of Limits and Availability Conditions

This index directs you to the **authoritative article to check** when you cannot use a feature, cannot increase a quantity, or need to determine pricing or consumption. It does not duplicate individual limits, prices, API specifications, or setup procedures. Check the applicable authoritative source and the actual screen display.

## Scope of this article

This article separates availability, permissions, and contract conditions that vary by organization from fixed specifications, and directs you to the authoritative source for each topic.

It does not list feature-specific numbers, prices, contract details, or API requests and responses. Do not treat the absence of an item on the screen alone as evidence that it is unlimited, free, or not contracted.

> Feature visibility and availability vary by organization. Contracts, availability status, permissions, Project and App settings, and external-service authentication and permissions are relevant. The same screen or limit is not necessarily displayed in another organization, Project, or App.

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
| Dataset | [Dataset Specifications and Limits](../build/dataset-specs-and-limits.md) | Organization-specific capacity, credit balance, and source constraints | [Prepare a Dataset and Make an Agent Reference It](../build/prepare-and-reference-datasets.md) |
| Object | [Object Specifications and Limits](../build/object-specs-and-limits.md) | Individual specifications through APIs and SQL | [Design an Object Structure](../build/design-object-structure.md), [View and Update Object Data](../use/view-and-update-object-records.md) |
| Storage | [Storage Specifications and Limits](../build/storage-specs-and-limits.md) | Storage capacity depending on contracts or settings and API conditions | [Organize Storage Folders](../build/organize-storage-folders.md), [Use Files in Storage](../use/use-storage-files.md) |
| Page | [Page Specifications and Limits](../build/page-specs-and-limits.md) | Development runtime, communication and execution time, and organization-specific quantity available | [Register and Publish a Page from the Screen](../build/register-and-publish-pages.md), [Page Source Structure and Runtime](../development/page-source-structure-and-runtime.md) |
| Skill | [Skill Specifications and Limits](../build/skill-specs-and-limits.md) | Organization-specific availability and permissions for tools and connection targets | [Create and Use Skills](../build/create-and-use-skills.md) |

## Articles on administration, contracts, and connections

Check the following table for availability conditions and administration settings.

| Topic to check | Authoritative source | Key points to check |
|---|---|---|
| Project Secret | [Register, Update, and Delete Project Secrets](manage-project-secrets.md) | Check registration, updates, deletion, input rules, permissions, and impact on referencing features. For safe reference methods, see [Page Server Processing, Environment Variables, and Secret References](../development/page-server-env-and-secrets.md). |
| App seats, contracts, and pricing | [Check App Seats, Pricing, and Contract Limits](check-app-seats-pricing-and-limits.md) | Separately check user count, purchased count, pricing, renewal, and termination guidance. Do not treat no display as evidence of no cost. |
| Run consumption and usage | [Check Execution Consumption and Usage](check-execution-consumption-and-usage.md) | Separate organization credit usage from Project usage (resources, Storage, and recorded values), and do not confuse either with App users or pricing. |
| Personal and shared connections | [Choose Between Personal Connections, Shared Connections, and Secrets](choose-personal-shared-connections-and-secrets.md), [Configure Shared Connections for a Project or App](configure-shared-connections.md) | Check ownership scope, authenticated accounts, user preparation, and external-service permissions. |
| External tools, data integrations, and MCP | [Manage External Tools, Data Integrations, and MCP](manage-external-tools-integrations-mcp.md) | Separate registration, addition to an Agent, authentication, sharing scope, and connection-target limits. Do not conclude that all users can use MCP merely because it is registered. |
| Dependencies and authentication when distributing an App | [Constraints on Distribution Scope, Dependencies, and Authentication Settings](../distribution/distribution-scope-dependencies-auth.md) | Check distribution targets, settings that cannot be carried over, and setup required at the installation target. Distinguish these from ordinary feature limits and installation procedures. |

## When using APIs and SDKs

Screen-operation limits and conditions for API or SDK calls do not necessarily match. During development, check the feature-specific authoritative source and the API article for the route.

| What to check | Authoritative source |
|---|---|
| Common API and SDK errors, timeouts, communication control, and common limits | [Common API and SDK Errors, Timeouts, and Limits](../development/api-sdk-errors-timeouts-limits.md) |
| Agents, conversations, and artifacts API | [Agents, Conversations, and Artifacts API](../api/agents-conversations-artifacts-api.md) |
| Object API | [Objects API](../api/objects-api.md) |
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
5. Ask the administrator or support about contract conditions, organization-specific availability, or API-specific conditions not covered by the article.

## Common misunderstandings

| Misunderstanding | Correct check |
|---|---|
| The same limit can be used for another feature | Open the authoritative source for the feature, input route, and API. |
| The feature is unavailable because it is not on the screen | Check availability status, permissions, target Project or App, and contract conditions. |
| App users and run consumption use the same pricing conditions | Separate what [Check App Seats, Pricing, and Contract Limits](check-app-seats-pricing-and-limits.md) and [Check Execution Consumption and Usage](check-execution-consumption-and-usage.md) cover. |
| Everyone can use MCP or external tools because a connection was made | Check addition to an Agent, sharing scope, authentication, and external-service permissions. |
| A feature callable through an API has the same limits as the screen | Check authentication, communication, and API-specific conditions in [Common API and SDK Errors, Timeouts, and Limits](../development/api-sdk-errors-timeouts-limits.md) and each API article. |

## If you cannot confirm it

If you cannot find the applicable item in the authoritative source, the screen and authoritative source do not match, or you cannot determine organization-specific availability conditions, record the organization, Project, App, feature name, usage route, operation date and time, and screen guidance or error, then ask the administrator or support. Do not include Secrets, API keys, tokens, passwords, personal information, or confidential data in an inquiry.

## Next articles

- [Agent Specifications and Limits](../build/agent-specs-and-limits.md)
- [Page Specifications and Limits](../build/page-specs-and-limits.md)
- [Register, Update, and Delete Project Secrets](manage-project-secrets.md)
- [Check App Seats, Pricing, and Contract Limits](check-app-seats-pricing-and-limits.md)
- [Common API and SDK Errors, Timeouts, and Limits](../development/api-sdk-errors-timeouts-limits.md)
