# Deprecation and Migration Notices

This page lets you check deadlines, impacts, and migration destinations for changes that require updates to existing operations or code. Users should check the impact on daily operations, while developers using the SDK, CLI, or API should review code, automation, and Pages before updating.

This page covers model retirement in the Studio UI and the breaking changes and compatibility notes for versions 0.2.0 through 0.11.0 in the SDK/CLI `CHANGELOG.md`.

## Related articles

- [Release Notes](release-notes.md)
- [SDK and API Version Migration](../development/sdk-api-version-migration.md)
- [Development Environment, Versions, and Compatibility](../development/dev-environment-and-versions.md)

## Scope of this article

Read this page by separating the scope into the following four areas.

| Scope | What to check on this page |
|---|---|
| Studio UI | The model retirement display and the migration users perform (changing the model of Agents and Workflows). |
| SDK/CLI | Changes to the npm package `@japan-ai-inc/studio-sdk`, `jai-studio`, public methods, types, and command arguments. |
| Public API | Compatibility with paths, responses, and write behavior used by the SDK. Check SDK updates and the support status of the connection destination separately. |
| Page runtime | SDK features, sessions, authentication, and supported execution environments available in the browser or server side within Studio Pages. |

“Deprecation” does not simply mean that an older version exists. It means that it has been announced that an old feature, argument, or operation will no longer be usable or requires migration. Because the SDK is below 1.0, minor versions may also contain breaking changes. Do not independently treat items without a stated deadline or end date as “scheduled for discontinuation.”

> Do not include Project API keys, cookies, authentication headers, Secret values, or tokens in migration notes, source code, the browser side of a Page, or inquiries. For issuing and managing authentication information, see [Manage Project API Keys](../operations/manage-project-api-keys.md).

## What to check first

- Check the SDK/CLI version, Node.js, lockfile, and the version of the CLI you are running.
- Separate whether the change applies to the Studio UI, SDK/CLI, public API, or Page runtime.
- Read the entries from the current version through the target version, and search for methods, types, CLI commands, tests and mocks, and Pages that are in use.
- For items without a stated deadline, impact, or migration destination, do not set an arbitrary date. First check compatibility and validate.
- Record behavior before updating, and check changes, failures, and conflicts in a validation environment.

```bash
node --version
npm ls @japan-ai-inc/studio-sdk
jai-studio --version
```

SDK 0.11.0 and the CLI require Node.js **22 or later**. In configurations that use the SDK and CLI, use the same exact version. For how to check versions, see [Development Environment, Versions, and Compatibility](../development/dev-environment-and-versions.md).

## Notice entry format

Deprecation and migration notices are described in the following format.

| Item | What to include |
|---|---|
| Scope/status | Whether it is Studio UI, SDK/CLI, public API, or Page runtime, and its status, such as deprecation, breaking change, or scheduled end of service |
| Deadline | The announced date, version, or end condition. If none is stated, “No deadline stated” |
| Impact | Operations that will no longer be usable, data, types, or displays that will change, and affected users and execution environments |
| Migration destination/action | New arguments, formats, or operations; validation methods; and related procedures |
| Basis/inquiry contact | The source of the announcement, related migration articles, and the responsible contact. Do not include authentication information. |

## Studio UI: Model retirement

| Item | Details |
|---|---|
| Scope/status | Studio UI. A model scheduled for retirement is shown with a deprecated label and its scheduled retirement date (「非推奨」 and 「（日付） に廃止予定」 in the Japanese UI). |
| Deadline | The scheduled retirement date shown on screen. |
| Impact | Scheduled Workflows that use a retired model fail with an error stating that the model has been retired (「モデルが提供終了」 in the Japanese UI). Because automatic runs happen without anyone opening the screen, you may not notice until they fail. |
| Migration destination/action | When you see the label, change the model in the Agents and Workflow nodes that use it to another model, test, and then publish. Check Workflows run by the Task Scheduler or triggers first. |

## SDK/CLI migration items

### Full payloads for Object updates (SDK 0.2.0)

| Item | Details |
|---|---|
| Scope/status | SDK/CLI and public API. Updating a Custom Object record was changed to assume that a complete typed payload is sent. |
| Deadline | Changed in 0.2.0. No separate end deadline is stated. |
| Impact | With the old update that sends only some fields, you cannot assume that omitted regular fields remain. Omitted fields are deleted. |
| Migration destination/action | Check the latest record and all required fields before updating, then build a complete typed payload. Retrieve the values again after updating and validate them. |

Apply this to update processes, batches, test data, and writes from Pages. Do not overwrite fields whose intent is unclear with empty values.

### Workflow wait input name and unit change (SDK 0.2.0)

| Item | Details |
|---|---|
| Scope/status | SDK/CLI. `RunAndWaitInput.timeout` was renamed to `timeoutMs`. |
| Deadline | Changed in 0.2.0. No separate end deadline is stated. |
| Impact | Code, custom types, configuration files, and tests that reference or specify the old `timeout` are affected. The unit of the new value is milliseconds. |
| Migration destination/action | Replace it with `timeoutMs` and review values as milliseconds. Validate whether waiting succeeds, timeouts, and duplicate processing on reruns. |

The CLI command `workflows run-and-wait --timeout <seconds>` was corrected to apply to server-side execution time. Do not assume that the SDK input and CLI flag use the same unit; check each entry point you use.

### Node.js execution requirement (SDK 0.2.0)

| Item | Details |
|---|---|
| Scope/status | SDK/CLI. The Node SDK and CLI require Node.js 22 or later. |
| Deadline | Stated as a requirement in 0.2.0. No grace period or end date for older Node.js versions is stated. |
| Impact | Development machines, CI, containers, and server-side Page environments running on Node.js are unsupported if they are below 22. |
| Migration destination/action | Standardize on Node.js 22 or later and validate the SDK, CLI, lockfile, and representative operations. Before avoiding this by downgrading only the SDK, check the supported range of the version you use. |

### Access restriction for retrieving private rooms (SDK 0.4.0)

| Item | Details |
|---|---|
| Scope/status | SDK and public API. On supported connection destinations, `agents.getMessages()` and Canvas reads no longer return private rooms to anyone other than the owner. A Project API key is treated as its creator. |
| Deadline | Behavior changed in 0.4.0. No separate end deadline is stated. |
| Impact | A user or automation other than the owner receives a 404 when trying to read a private room. Do not work around insufficient permissions by expanding the sharing scope. |
| Migration destination/action | Check the principal that needs to read, the room sharing scope, the key creator, and the purpose of the process. For an unexpected 404, record the target ID and execution principal, then review the permission design. |

### Additional argument for publishing a Canvas (SDK 0.7.0)

| Item | Details |
|---|---|
| Scope/status | SDK and Page runtime. `agents.publishCanvasArtifact()` was changed to require a third argument specifying the publication scope, in addition to the room ID and Canvas ID. |
| Deadline | Changed in 0.7.0. No separate end deadline is stated. |
| Impact | The two-argument call in 0.6.0 cannot be used. Check publication processes, wrappers, mocks, and bundled skills. |
| Migration destination/action | Specify `"PUBLIC"` or `"ORGANIZATION"` as the third argument. Check publishing and unpublishing on a supported connection destination. |

On unsupported connection destinations, the new publication path may return 404. Do not infer the publication scope and continue processing. For the procedure, see [SDK and API Version Migration](../development/sdk-api-version-migration.md).

### Removed options for bundled skills (SDK 0.6.0)

| Item | Details |
|---|---|
| Scope/status | Preparation for Page deployment by skills bundled with the SDK/CLI. `--no-inject-base` and `--allow-root-assets` were removed. |
| Deadline | Changed in 0.6.0. No separate end deadline is stated. |
| Impact | Scripts and automation that specify them cannot run. |
| Migration destination/action | Remove the deleted options and check the Page. |

### Changes to answer comparison types and error wording (SDK 0.10.0–0.11.0)

| Item | Details |
|---|---|
| Scope/status | SDK and Page runtime. The types for answer comparison (`compare()`) and messages when it is rejected have changed. |
| Deadline | Changed in 0.10.0 and 0.11.0. No separate end deadline is stated. |
| Impact | Turns from `getTurns()` now include `attachments`, and candidates now include `references` (0.10.0) and `effort` (0.11.0) (candidate `tools` was already added in 0.6.0). Code, tests, and mocks that construct these types manually need the new properties. The message rejecting comparisons with the same model changed from “models must be distinct” to “models must differ in model or effort.” This rejection happens inside the SDK (client side) before sending and has no error code. Processes that evaluate the wording are affected. |
| Migration destination/action | Add the new properties to manually written `Turn` and `TurnCandidate`. Do not rely on the wording or an error code for duplicate rejection; before sending, check that `models` contains no duplicate combination of the same model and the same effort. When listing the same model, specify different effort values in `CompareChoice`. |

Response comparison is a feature used from the browser of a Page hosted in Studio. It requires the feature to be enabled for your organization and the viewer to be a signed-in JAPAN AI user; it is not available on Pages with anonymous access or external customer authentication. Direct calls with a Project API key are not supported.

### New APIs that require key scopes (SDK 0.8.0–0.10.0)

| Item | Details |
|---|---|
| Scope/status | SDK/CLI and public API. Scheduled Tasks, creating and updating Agent definitions, Object record access rules, and bulk deletion of Objects were added. |
| Deadline | These are added features, so there is no end deadline. |
| Impact | Scheduled Tasks require `task:read`, `task:write` (plus `workflow:execute`), and `task:delete`; Agent definitions require `agent:write`; record rules require `object:read` and `object:policy` for listing and `object:write` and `object:policy` for creation and deletion. `bulkDelete` requires `object:delete`. Scopes for Scheduled Tasks, Agent definitions, and record rules cannot be declared in an App release manifest. The key creator must also have the corresponding permissions. |
| Migration destination/action | Grant the key only the scopes required for the operations you use. Record rules and bulk delete (`DELETE /api/v1/objects/:objectId/records/bulk`, available in production since 2026-11-13) can be used through the public API with a Project API key. Bulk delete accepts up to 1,000 records and either succeeds for all or fails for all. For issuance and management, see [Manage Project API Keys](../operations/manage-project-api-keys.md). |

### Dependencies of new features on connection destinations and the Page runtime (SDK 0.3.0–0.11.0)

Page session logout in 0.3.0; Canvas reads and Workflow creation/editing in 0.4.0; answer candidate comparison in browsers within Pages from 0.5.0; Storage file attachment and Canvas writing in 0.6.0; usage checking and App settings in 0.7.0; Dataset search in 0.8.0; answer comparison extensions in 0.10.0; and per-candidate effort specification in 0.11.0 are not necessarily available just because the SDK has been installed. Datasets have an authentication difference: use the `project` scope with a Project API key, and use the `organization` scope with a user signed in through the Page browser. Comparison depends on use from the Page browser, organization settings, and user permissions. If a supported public API or Page runtime is not available, you may receive a 404 or rejection, or the feature may not be available.

This is not a feature “deprecation deadline”; it is a compatibility note when adopting only a new SDK ahead of other components. Validate the methods you use, target Pages, and displays on failure in supported connection destinations and execution environments. For details, see [SDK and API Version Migration](../development/sdk-api-version-migration.md).

## When announcing the end of execution support for an App revision

The end of execution support for an old App revision is an operation separate from updating the SDK version. After support ends, you can still view the history of old rooms, but you cannot run new messages with that old revision. Even if you start a new room with the successor current version, do not assume that the conversation content of the old room is carried over automatically.

In a notice to users, clearly state the target App, target business process, old revision being discontinued, that it can no longer run, the migration destination of the successor version or a new room, any required reconfiguration, and the inquiry contact. Manage the announcement period separately through notices to users.

## Common misunderstandings

- **No deadline stated means it is safe to use forever**: It only means that no deadline is written. When updating, check the CHANGELOG through the target version and validate.
- **The SDK was updated, so the connection destination is also supported**: Check the SDK, public API, and Page runtime separately. Unsupported paths may not be usable.
- **A 404 means the target resource was deleted**: For private room retrieval since 0.4.0, a 404 can also result from access restrictions for someone other than the owner. Check the target and execution principal.

## If the issue is not resolved

Collect the target organization, Project, Page, or App; SDK/CLI/Node.js versions; connection destination; results before and after the change; occurrence date and time; commands or methods executed; displayed messages and HTTP status; and what has already been checked. Do not include API keys, cookies, authentication headers, Secret values, tokens, or confidential customer data. For gradual SDK and API updates, see [SDK and API Version Migration](../development/sdk-api-version-migration.md).

## Articles to read next

- [Release Notes](release-notes.md)
- [SDK and API Version Migration](../development/sdk-api-version-migration.md)
- [Development Environment, Versions, and Compatibility](../development/dev-environment-and-versions.md)
