# Development Environment, Versions, and Compatibility

When developing with `@japan-ai-inc/studio-sdk`, pin the combination of SDK, Node.js, CLI, and execution destination. Based on the published SDK **0.11.0**, this article explains environment checks, installation, version checks, and update decisions. Installing the SDK alone does not guarantee use with arbitrary external production hosting or all execution environments.

## Related articles

- [SDK and API Version Migration](sdk-api-version-migration.md)
- [Execution Modes, Authentication, and Supported Scope](execution-modes-and-auth.md)

## Before you begin

The target package is `@japan-ai-inc/studio-sdk`. It includes the SDK and CLI. The version to check is **0.11.0**.

The Node SDK and CLI require **Node.js 22 or later**. Node.js 20 or earlier, and configurations that pass API keys directly to browsers, are out of scope. Browser SDK use is limited to Pages that run within PRS (Pages Rendering Service). In local development and explicitly managed development automation, handle Project API keys on the Node.js side.

> Project API keys are confidential information. Do not place them in browser JavaScript, environment variables exposed to the browser, logs, or source control. For key issuance, storage, and revocation, see [Manage Project API Keys](../operations/manage-project-api-keys.md).

Check the Node.js and npm versions in the shell you will use.

```bash
node --version
npm --version
```

Confirm that `node --version` returns `v22` or later. Do not use different Node.js versions on development machines, CI, and containers.

## Target SDK and module format

### Align versions

Confirm SDK 0.11.0 in the package definition and `SDK_VERSION`. Align dependencies, lockfiles, and CI to the same version.

| Item to check | What to check |
|---|---|
| Package name | `@japan-ai-inc/studio-sdk` |
| SDK version | Pin `0.11.0` in dependencies and the lockfile. |
| Node.js | Align development, CI, and server-side environments to `>=22`. |
| Import | It is compiled as CommonJS. Do not treat it as ESM-only. |

The SDK's TypeScript configuration specifies `commonjs`. To explicitly load it as CommonJS on the Node.js side, use the following format.

```js
const { createClient, SDK_VERSION } = require("@japan-ai-inc/studio-sdk");

console.log(SDK_VERSION); // 0.11.0
```

If the existing compilation configuration in a TypeScript project handles import syntax, you can use the following form from the README.

```ts
import { createClient, SDK_VERSION } from "@japan-ai-inc/studio-sdk";

console.log(SDK_VERSION); // 0.11.0
const studio = createClient({
  baseUrl: "https://api.japan-ai.co.jp",
  apiKey: process.env.JAI_STUDIO_API_KEY,
});
```

This example is for Node.js-side configuration. Do not return the value to a screen. For authentication and initialization of a PRS Page running in a browser, see [Execution Modes, Authentication, and Supported Scope](execution-modes-and-auth.md).

## 1. Install the SDK

At the project root, install the target version explicitly.

```bash
npm install @japan-ai-inc/studio-sdk@0.11.0
```

Regardless of the package manager you use, pin the SDK to `0.11.0` and share the lockfile.

After installation, check the installed dependency and the version published by the SDK.

```bash
npm ls @japan-ai-inc/studio-sdk
node -e "console.log(require('@japan-ai-inc/studio-sdk').SDK_VERSION)"
```

If both are not `0.11.0`, check `package.json`, the lockfile, and the execution directory. You may be loading duplicate dependencies or another package.

## 2. Install and check the CLI

The CLI command name is `jai-studio`. To use it globally, install it at the same version as the SDK.

```bash
npm install --global @japan-ai-inc/studio-sdk@0.11.0
jai-studio --version
```

`jai-studio --version` displays the CLI version. When using the SDK and CLI together, align both to 0.11.0. If the executable differs from what you expect, check its location and version.

```bash
command -v jai-studio
jai-studio --version
```

On Windows, use the command appropriate for your shell to check the executable path. Do not run Page operations or automation with an unverified CLI version.

The CLI also has `skill install`, which places a bundled skill locally.

```bash
jai-studio skill install --agent codex --scope project
```

A GitHub account and network connection are not required. Rerunning with the same contents makes no changes; use `--force` only when replacing a different copy. After placement, check it in a new coding-agent session.

## 3. Check and pin compatibility

Whether you can use the SDK is not determined by npm installation alone. Check the execution mode, Node.js, SDK, and destination together.

| Usage location | Required prerequisites |
|---|---|
| Local Node.js | Node.js 22 or later and a developer-managed Project API key. This does not support arbitrary external production hosting. |
| PRS browser | A PRS session. Do not configure or reference a Project API key. |
| PRS server | Node.js 22 or later and PRS-managed server-side settings. Do not return keys to the browser. |

> Updating the SDK does not automatically align functionality at the destination. Check prerequisites in the CHANGELOG and verify representative operations.

An SDK in the 0.x series can include breaking changes in a minor update (for example, from `0.6.0` to `0.11.0`). Pin a full version instead of a range.

```json
{
  "dependencies": {
    "@japan-ai-inc/studio-sdk": "0.11.0"
  }
}
```

`^0.11.0`, `~0.11.0`, and `latest` can resolve to a different version during a later installation. Record the verified full version number and lockfile.

## Make update decisions with the CHANGELOG

The SDK version published to npm and functionality available in your environment (public API and PRS) are not necessarily available at the same time. A method in a newer version may not yet be available in the environment. Before updating, check whether the feature you use is available.

Before updating, check the `CHANGELOG.md` included in the package. The CHANGELOG is primary information for checking changes and compatibility considerations. In particular, check the following.

1. Read release items from the current version through the update target.
2. Check `Changed`, `Fixed`, and execution-environment and destination prerequisites for added features.
3. Align the SDK, CLI, and lockfile to the update candidate, then verify representative operations and failure handling.

From 0.6.0 to 0.11.0, the public Canvas `publishCanvasArtifact()` requires a third visibility argument. Credit checking and `platform.openAppSettings()` in 0.11.0 are used in the supported PRS execution environment. [SDK and API Version Migration](sdk-api-version-migration.md) covers migration details.

## Common misunderstandings

### `npm install` succeeded, so the Node.js requirement is also met

Successfully retrieving dependencies does not guarantee that the runtime uses Node.js 22 or later. Separately check every environment where Node.js code runs.

### The SDK and CLI can be upgraded separately to the latest version

The CLI is provided by the same package. When pinning the SDK to 0.11.0, also check the CLI with `jai-studio --version`. If using different versions, record the reason and verification results.

### With CommonJS configuration, the SDK can be used directly in any browser

Whether CommonJS or ESM is primarily a Node.js-side module-loading issue. Browser SDK support is separate: you must check whether the Page is in PRS, its authentication method, and the module you use.

### A minor update in the 0.x series is always safe

This SDK can include breaking changes in pre-1.0 minor updates. Pin the version, read the CHANGELOG, and verify the update candidate in a separate environment.

## If something does not work

| Situation | What to check and do |
|---|---|
| `node --version` is lower than 22 | Switch to an environment that uses Node.js 22 or later. Do not decide to lower only the SDK for an older Node.js version until checking supported scope and the CHANGELOG. |
| `jai-studio` cannot be found or displays an unexpected version | Check `command -v jai-studio` and `jai-studio --version`, then review the executable on PATH and the globally installed version. |
| `npm ls` and `SDK_VERSION` do not match | Check the execution directory, lockfile, and workspace dependencies. If multiple versions are resolved, pin the version used to one. |
| Authentication or API-key issues occur in a browser | Do not add an API key to the browser. Check whether the execution location is within PRS or on the Node.js side. See [Execution Modes, Authentication, and Supported Scope](execution-modes-and-auth.md) and [Manage Project API Keys](../operations/manage-project-api-keys.md). |
| Some calls fail after an SDK update | Check changes and added prerequisites through the update target in the CHANGELOG, and isolate the issue by recording the SDK/CLI/Node.js/destination combination. If necessary, see the migration procedure in [SDK and API Version Migration](sdk-api-version-migration.md). |

## Checklist

- [ ] The SDK is pinned to `@japan-ai-inc/studio-sdk` 0.11.0
- [ ] Development machines and CI use Node.js 22 or later
- [ ] I can reproduce the installation result, including the lockfile
- [ ] I checked the installed version with `npm ls` and `SDK_VERSION`
- [ ] When using the CLI, `jai-studio --version` is the same 0.11.0 as the SDK
- [ ] I did not include API keys in browsers, public environment variables, logs, or source control
- [ ] I confirmed that the execution mode and module used are within supported scope
- [ ] Before updating, I read the CHANGELOG and checked representative operations in a verification environment

## Read next

- [Develop a Page with the SDK and CLI](../labs/develop-page-with-sdk-cli.md)
- [SDK and API Version Migration](sdk-api-version-migration.md)
- [Execution Modes, Authentication, and Supported Scope](execution-modes-and-auth.md)
- [Install the Development Support Skill with the CLI](../cli/install-dev-skill-via-cli.md)
