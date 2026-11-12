# Choose Between the Web UI, AI Assistance, SDK, CLI, and API

Choose a Studio development path based on your goal and execution location. Use the Web UI to check settings on screen, AI assistance for Page drafts or proposed changes, the Studio SDK for TypeScript, the `jai-studio` CLI for repeated terminal or CI operations, or the public REST API when calling directly from another language or HTTP client.

Even for the same Project, operations, authentication, and verification differ by path. Manage Project API keys securely on the server side; do not include them in browser-distributed assets.

## Related articles

- [Ask AI to Create and Edit a Page](../build/ask-ai-to-build-pages.md)
- [Manage Project API Keys](../operations/manage-project-api-keys.md)
- [Execution Modes, Authentication, and Supported Scope](execution-modes-and-auth.md)
- [CLI Configuration and Profiles](../cli/cli-config-and-profiles.md)

## Scope of this article

This is a comparison article for developers choosing an entry point that fits their goals. It does not cover the complete REST API specification, every SDK method, or CLI installation and profile configuration. Before execution, check the target organization, Project, and permissions.

The SDK is provided as `@japan-ai-inc/studio-sdk`. The public version is `0.11.0`. The Node.js SDK and CLI require Node.js 22 or later. Pin the full version in the 0.x series and verify before updating.

## Choose a path first

| Path | Best for | What you can mainly do | Authentication and prerequisites |
|---|---|---|---|
| Web UI (Studio screen) | Initial configuration, visual checks, and a small number of changes | Create, configure, and check "Agents," "Workflows," "Objects," "Storage," "Datasets," "Pages," and more | Sign in to Studio and have permission to view and operate the target Project |
| AI assistance | Creating a new Page, focused changes to an existing Page, and organizing requirements | Provide goals, data, change scope, and verification criteria to request Page creation or modification | Be able to use the build AI and create or update Pages |
| Studio SDK | Implementing custom development processing in TypeScript | Implement typed operations for Agents, Objects, Storage, Workflows, and Project members | Use a Project API key for local development; use a session in the PRS browser and do not place a key there |
| `jai-studio` CLI | Repeated terminal, script, and CI work | Deploy Pages and execute Workflow management, reference, and administrative operations with commands | Normal Project operations require a Project API key in a CLI profile or `JAI_STUDIO_API_KEY`. Skill installation alone needs no key. |
| Public REST API | Non-TypeScript, existing HTTP integration, or implementations that do not use the SDK | Directly use public APIs for Agents, Objects, Storage, Workflows, Pages, and more through HTTP | Check authentication, scopes, and request format for each path. Store keys securely on the server side. |

Screen items and whether operations are available vary by organization availability conditions, Project state, and permissions. Do not conclude that a feature or permission is lacking merely because an item is absent.

## 1. When to choose the Web UI

The Studio screen is the basic entry point when you want to check the target Project and current settings before changing them. It is suitable for prototyping a small number of "Objects" or "Workflows," opening an existing "Page," and issuing or revoking API keys.

Saving, publishing, running, and external transmission have different impacts. Check the target and impact scope, and verify representative results using permitted data.

## 2. When to choose AI assistance

Choose AI assistance when you want to organize the goal, users, data, permitted operations, and unchanged scope through conversation. It is suitable for drafts of screen composition and focused changes.

Even after requesting AI assistance, open the target Page in "Preview" and check display, input, saving, and sending. An AI completion report, approval of a creation request, and Page registration or publication are separate checks. For request and verification procedures, see [Ask AI to Create and Edit a Page](../build/ask-ai-to-build-pages.md).

> Instructions to AI are not technical controls over access rights or external integrations. Do not enter personal information, passwords, API keys, or tokens in the conversation, and explicitly state the scope in which registration, updates, and sending are allowed.

## 3. When to choose the SDK

Choose the Studio SDK when you want to handle Studio resources from code with an emphasis on type checking and reusable processing. From `createClient()`, you can use `agents`, `datasets`, `members`, `objects`, `scheduledTasks`, `storage`, and `workflows`. The SDK's `datasets` module is read-only (`list()` and `search()`); the public API is summarized in [Datasets API](../api/datasets-api.md). Use `scheduledTasks` to schedule Project-specific Workflows with a developer-managed Project API key. In SDK 0.11.0, the supported PRS execution environment allows a Page to check usage, such as with `agents.getTurnCredits()`, compare candidates, and read datasets. In an installed App's Page, you can open the settings screen with `platform.openAppSettings()`.

```ts
import { createClient } from "@japan-ai-inc/studio-sdk";

const studio = createClient({
  baseUrl: "https://api.japan-ai.co.jp",
  apiKey: process.env.JAI_STUDIO_API_KEY,
});

const contacts = studio.objects.define<{ name: string }>("contacts");
const result = await contacts.list({ limit: 20 });
```

For local development and automations you manage, use a developer-managed Project API key. Studio Page browser code on PRS uses `createClient({ baseUrl: "" })` and is authenticated by the PRS session. Do not place keys in browser JavaScript, public environment variables, logs, or source control.

Direct SDK development is mainly for local development and managed automation. Do not regard arbitrary external production hosting or integrations as supported scope; check the execution mode in [Execution Modes, Authentication, and Supported Scope](execution-modes-and-auth.md).

## 4. When to choose the CLI

Choose the `jai-studio` CLI when repeating the same procedures in a terminal or CI. Install the public package globally.

```bash
npm install --global @japan-ai-inc/studio-sdk
jai-studio config set my-project
jai-studio pages deploy ./dist --name "Sales Dashboard" --json
```

The CLI includes `config`, `agents`, `members`, `objects`, `storage`, `workflows`, `scheduled-tasks`, `pages deploy`, and `skill install`. `scheduled-tasks` manages Project-specific Workflows as scheduled runs. `objects` handles definitions, fields, and records; `workflows` handles creation, saving drafts, publishing, running, and checking status. `pages deploy` deploys a ZIP or directory as a new Page or a version of an existing Page.

An API key passed as a command argument may remain in shell history or process lists. Use interactive input, `--key-stdin`, or `JAI_STUDIO_API_KEY`. CLI profiles are CLI-only and are not read by the SDK's `createClient()`. For configuration, see [CLI Configuration and Profiles](../cli/cli-config-and-profiles.md).

> `delete`, Workflow `publish`, and deployments that specify an existing Page ID change the target. Before incorporating them into CI, verify the target Project, ID, input files, key scope, and failure handling.

## 5. When to choose the public REST API

Choose the public REST API when an existing system is not TypeScript, you cannot add a dependency package, or you want to standardize integrations on HTTP. There are `/api/v1/` paths for Agents, Objects, Storage, Workflows, Pages, Project Members, and more. The Object API handles definitions, fields, and records; the Page API handles obtaining upload URLs and deployment.

With the REST API, the caller manages HTTP methods, URLs, bodies, pagination, errors, authentication headers, and required scopes. Public APIs that use Project API keys have implementations that accept `x-api-key` or `Authorization: Bearer`. Authentication, scope, and availability conditions are not necessarily the same for every API. For operations supported by the SDK, consider the SDK or CLI first.

Do not implement unconfirmed REST API paths, parameters, or response formats by inferring them from route names alone. Check the target API's public specification, execution mode, and key scope before connecting.

## Check authentication and keys

The Web UI and AI assistance usually rely on the signed-in user's Project permissions. When directly developing with the SDK, CLI, or REST API, use a Project API key limited to only the necessary targets and operations. Follow [Manage Project API Keys](../operations/manage-project-api-keys.md) for key issuance, scope, expiry, and revocation.

- Do not store keys in browsers, Page-distributed files, public repositories, chat, or logs.
- Start with minimum scopes and a short expiry, and verify only required operations.
- For authentication or authorization errors, check the target Project, key expiry or revocation, scopes, execution mode, and configuration destination in order.

## Decision table by use case

| What you want to do | First choice | Support or alternative | Reason and verification |
|---|---|---|---|
| Create or modify a small number of resources while viewing existing settings | Web UI | SDK/CLI | Check the current Project and settings, and the result after changes. |
| Create a business-screen draft from requirements and modify it through conversation | AI assistance | Web UI | Organize the goal and change scope, then check "Preview" and business operations. |
| Repeatedly deploy a Page from built artifacts | CLI | Web UI | Deploy a ZIP or directory with `pages deploy`, and check the target Page ID and version. |
| Use Agents, Objects, Storage, and Workflows from TypeScript server processing | SDK | REST API | Use typed methods and manage keys on the server side. |
| Retrieve, save a draft of, publish, and check a Workflow run in CI | CLI | SDK | Script commands and input files, and check side effects. |
| Use a public API from a non-TypeScript backend | REST API | Change to an environment that can use the SDK | Manage the public specification, authentication, scopes, and error handling. |
| Handle Studio resources from a Studio Page browser | SDK PRS browser mode | Check settings in the Web UI | Use the PRS session and do not pass a Project API key to the browser. |

## Common misunderstandings and problems

The Web UI, SDK, and CLI do not share the same authentication settings. Do not bypass keys or permissions by changing paths; check the following.

- If a target Project, resource, or operation is not shown in the Web UI or AI assistance, check organization availability conditions, Project membership, and permissions.
- If the SDK cannot authenticate even though you created a CLI profile, confirm that the SDK does not read CLI profiles and pass the actual value from a secure environment variable.
- If a CLI or SDK operation is unavailable, check the applicable public REST API specification. Do not use internal implementation paths as a contract for external integration.
- If you cannot determine REST API authentication, scopes, or format, do not send an inferred HTTP request to production. Check the target API specification and [Execution Modes, Authentication, and Supported Scope](execution-modes-and-auth.md).
- If the result is unknown after deletion, publication, update, or execution, do not immediately run it again; check the target and result.

## Read next

- [Execution Modes, Authentication, and Supported Scope](execution-modes-and-auth.md)
- [CLI Configuration and Profiles](../cli/cli-config-and-profiles.md)
- [Ask AI to Create and Edit a Page](../build/ask-ai-to-build-pages.md)
- [Manage Project API Keys](../operations/manage-project-api-keys.md)
