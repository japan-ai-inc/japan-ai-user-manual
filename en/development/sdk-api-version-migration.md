# SDK and API Version Migration

In applications that use `@japan-ai-inc/studio-sdk` and the public API, updating the SDK alone is not enough. The SDK's public types, CLI, called APIs, and Page execution environment work in combination. In particular, the SDK is pre-1.0, and even minor updates can include breaking changes. This article explains impact assessment, phased migration, and post-deployment verification for safely updating an implementation in use.

## Related articles

- [Development Environment, Versions, and Compatibility](dev-environment-and-versions.md)
- [Deprecations and Migration Notices](../troubleshooting/deprecations-and-migrations.md)
- [SDK Agents: Invocation and Streaming](sdk-agents-invoke-and-stream.md)
- [Manage Project API Keys](../operations/manage-project-api-keys.md)

## Scope of this article

This article is for developers using the SDK and public API from Node.js applications, CI, the `jai-studio` CLI, or Studio Pages. The SDK version checked is **0.11.0**.

Here, an SDK version means the npm package version of `@japan-ai-inc/studio-sdk`, and a public API version means the API generation included in a URL. For example, SDK clients for Object, Storage, Page, Agent, Workflow, and Member call public APIs under `/api/v1/`. In contrast, `v1` attached to a published Workflow definition represents a published Workflow snapshot, not a public API generation. Do not confuse update procedures even when the names are the same.

This article does not guarantee support for arbitrary external production hosting environments. For execution location and authentication selection, review [Development Environment, Versions, and Compatibility](dev-environment-and-versions.md) and [Execution Modes, Authentication, and Supported Scope](execution-modes-and-auth.md).

> A Project API key is not a configuration value for migration; it is confidential information. Do not include it in source code, JavaScript delivered to browsers, public environment variables, or logs. Follow [Manage Project API Keys](../operations/manage-project-api-keys.md) for issuing, storing, and revoking it.

## First distinguish three versions

| Target | What it represents | What to check during migration |
|---|---|---|
| SDK/CLI | Features, types, and commands in `@japan-ai-inc/studio-sdk` and `jai-studio` | Align dependencies, lock files, and the CLI executable to the same verified version. |
| Public API | API generation identified by a URL such as `/api/v1/` | Check the endpoints used by the SDK and the features and response fields provided by the connection. |
| Execution environment | Local Node.js, browser in a Page, server side in a Page | Check that available features and authentication differ by execution location even for the same SDK method. |

SDK 0.11.0 requires Node.js **22 or later**. Because the SDK and CLI are provided by the same package, align them to the same verified version in CI.

```bash
node --version
npm ls @japan-ai-inc/studio-sdk
jai-studio --version
```

Record these results along with `package.json` and the lock file. For a globally installed CLI, also check `command -v jai-studio`.

## Changes to read before updating

Read `CHANGELOG.md` and extract changes related to modules, types, CLI, and execution environments in use. Because the SDK is pre-1.0, a minor update can include breaking changes. Pin the complete version.

```json
{
  "dependencies": {
    "@japan-ai-inc/studio-sdk": "0.11.0"
  }
}
```

### Typical migration targets

| Change type | Affected implementation | Check during migration |
|---|---|---|
| Object update in 0.2.0 | Processing that updates records | Review on the premise that it sends a complete typed payload. |
| Workflow wait setting in 0.2.0 | Processing that waits for run completion | Replace `timeout` with `timeoutMs` and check the unit as milliseconds. |
| Concurrent Object update in 0.3.0 | Processing that updates the same record | Check updates and deletes using `revision` and conflict handling. |
| Canvas/Workflow features in 0.4.0 | Processing that retrieves Canvas or creates and publishes Workflows | Check that the connection's public API provides required routes and responses. |
| PRS Agent features in 0.5.0 to 0.7.0 | Candidate comparison, Storage attachments, Canvas publication, credits, and App settings | Check PRS-browser-only features and connection support, and specify Canvas publication scope as the third argument. |
| Datasets and scheduled runs in 0.8.0 | Page RAG references and Workflow scheduled runs | Check the `datasets` scope and PRS route, plus `task:*` scope and executor for `scheduledTasks`. |
| Agent definitions and Object rules in 0.8.0 | Agent building and Object access settings | Check `agent:write`, `object:policy`, key-creator permissions, and update semantics for array replacement and `null` clearing. |
| Object search in 0.9.0 | Lists and searches that display counts | With `includeCount: false`, `total` is 0, so handle pagination and count display separately. |
| Bulk deletion and candidate comparison in 0.10.0 | Large-volume deletions and comparison UI in PRS | Check that `bulkDelete()` succeeds or fails for all records, the availability of the deletion route, and support for comparison models, attachments, tools, and references. |
| Candidate-specific effort in 0.11.0 | Comparison UI and candidate selection | Check `candidateEfforts` and `effortSupported`, and present effort only for candidates meeting both conditions. When the connection response is `candidateEfforts: false`, do not offer duplicate models or effort as options. |

New APIs and types do not necessarily appear in an existing environment merely by updating the SDK. With an older connection, unavailable routes can return 404, additional input can be ignored, and response fields can be missing. Verify new features with a supporting connection.

### Migrate Canvas publication from 0.6.0 to 0.7.0

The Canvas publishing call from 0.6.0 cannot be used in 0.7.0 or later. Search usages and specify publication scope as the third argument. `"PUBLIC"` is for users who know the link; `"ORGANIZATION"` is for signed-in organization members. After updating, verify both publishing and unpublishing with a supporting connection.

```ts
// 0.6.0
await studio.agents.publishCanvasArtifact(roomId, canvasArtifactId);

// 0.7.0 and later
await studio.agents.publishCanvasArtifact(
  roomId,
  canvasArtifactId,
  "ORGANIZATION"
);
```

## Migration procedure

### 1. Fix the current state and identify impact points

Save the current SDK, Node.js, CLI, and lock file. Read the CHANGELOG from oldest to newest through the target version, and map SDK imports, `createClient()`, module calls, CLI, and custom types and mocks.

In particular, check Workflow wait time, Object updates, Page-only APIs, and added response fields.

### 2. Install the candidate update at a complete version

In a verification environment, align the SDK and CLI to the same complete version. An example when adopting 0.11.0 follows.

```bash
npm install @japan-ai-inc/studio-sdk@0.11.0
npm install --global @japan-ai-inc/studio-sdk@0.11.0
npm ls @japan-ai-inc/studio-sdk
jai-studio --version
```

In a configuration without a globally installed CLI, use the CLI pinned by the project. Do not resolve the SDK and CLI separately to "latest."

### 3. Check the connection and execution mode

Many SDK modules use paths under `/api/v1/`. Check custom HTTP requests in the public API reference.

Do not copy Node.js Project API key configuration into a browser in a Page. Check the execution location and supported modules.

### 4. Test representative operations and failure routes

After updating, check reading, writing, insufficient permissions, and unsupported connections for each feature in use. For Workflows, cover list and retrieval, draft saving, publishing, and run results. For Agent streams, also check error events and interruptions.

Test changes to responses, pagination, and error handling, and do not make unsupported cases appear successful.

### 5. Roll out gradually and record results

After reviewing verification results, update dependencies, the lock file, and CLI-use environments together. Run representative operations after rollout and record the SDK version, CLI version, Node.js version, and verification time. Be able to return to the immediately previous verified version if an issue occurs.

## Common misunderstandings

### If the SDK is 0.11.0, all new features are available

The SDK version alone does not determine availability. Also check the supporting public API and Page execution environment.

### The v1 in `/api/v1/` and the v1 in Workflow are the same version

The former is the generation in the public API URL, and the latter is a published Workflow snapshot. Publishing a Workflow definition does not change the public API generation.

### Minor updates are compatible even before 1.0

Minor updates can include breaking changes. Pin a complete version and review the CHANGELOG before rollout.

## If it does not work

| Situation | Check and response |
|---|---|
| Type errors occur after updating | Check the CHANGELOG, changed arguments, required fields, and mocks. |
| A new SDK method returns 404 | Check whether the connection's public API and execution environment provide the feature. |
| A call fails only within a Page | Do not add an API key to the browser; check execution mode and module. |
| CLI and application results differ | Check `jai-studio --version`, `npm ls`, execution path, and lock file. |
| Writing changes after updating | Check the update payload, concurrent updates, wait time, and response. |

## Checklist

- [ ] Recorded the current SDK, CLI, Node.js, and lock file
- [ ] Reviewed the CHANGELOG from the source version through the target version
- [ ] Identified usages from SDK 0.7.0 through 0.11.0
- [ ] Pinned the SDK and CLI to the same complete version
- [ ] Checked the connection's public API and execution environment
- [ ] Verified reads, writes, insufficient permissions, and unsupported cases
- [ ] Did not include API keys in browsers, public environment variables, or logs
- [ ] Recorded versions and representative-operation results after rollout

## Next article

- [Development Environment, Versions, and Compatibility](dev-environment-and-versions.md)
- [SDK Agents: Invocation and Streaming](sdk-agents-invoke-and-stream.md)
- [Deprecations and Migration Notices](../troubleshooting/deprecations-and-migrations.md)
- [Manage Project API Keys](../operations/manage-project-api-keys.md)
