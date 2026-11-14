# Release Notes
We will guide you through changes to Studio and how to confirm them. SDK/CLI, Studio UI, public API, and Page runtime can be provided separately. The SDK release date, production merge date and time, and deployment date and time are not treated as the same. Unless otherwise specified, the date of this article is the npm release date of `CHANGELOG.md` included in the SDK.
## Related articles
- [Notice of deprecated function/migration](deprecations-and-migrations.md)
- [Development environment/version/compatibility](../development/dev-environment-and-versions.md)
- [SDK/API version migration](../development/sdk-api-version-migration.md)
## Scope of this article
The SDK/CLI history to be posted is 0.2.0 to 0.11.0 in `CHANGELOG.md` included in npm package `@japan-ai-inc/studio-sdk`. The SDK and CLI are the same package, and the CLI name is `jai-studio`. Since the SDK is less than 1.0, there may be breaking changes even in the minor version. Please pin the full verified version instead of range specification or `latest`.
- **Studio UI**: Check the articles for Studio Projects and installed Apps for screen operations. This article does not cover changes to organization-wide screens outside Studio.
- **Public API/Page runtime**: Usability is not determined only by updating the SDK. Check the route, authentication mode, authority, and organization settings in the target environment.
## How to read release notes
Divide the target into Studio UI, SDK/CLI, public API, and Page runtime, and check the conditions for providing the function and the authentication mode to be used.
## Format of published entry
Below is a history summarizing the version, release date, changes, and necessary actions. If there are any usage route conditions, they will be listed in each edition.
## Check first
1. Divide the targets into Studio UI, SDK/CLI, public API, and Page runtime.
2. Check all entries from the current version to the update destination and the methods, types, and CLIs in use.
3. In the verification environment, check the connection destination, authentication mode, representative operation, and behavior when failure occurs.
```bash
node --version
npm ls @japan-ai-inc/studio-sdk
jai-studio --version
```

Node.js requirements for SDK 0.11.0 and CLI are 22 or higher. If you use the SDK and CLI together, align them to the same complete version.
## SDK/CLI changes that can be confirmed currently
### 0.11.0 (2026-11-06)
`models` of `compare()` accepts `{ model, effort? }` (`low`/`mid`/`high`/`max`) in addition to the model name. `primaryIndex` takes precedence over `primary`, and the same model can be placed side by side with different efforts. Same model/same effort will be rejected with "models must differ in model or effort". `candidateEfforts`, `effortSupported`, and candidate `effort` have been added.
This is an extension of the comparison functionality that has a corresponding PRS/agent-service route. In the old backend, effort is ignored, duplicate models are rejected, and candidate effort is null. We do not treat the comparison functionality as being available by directly calling the SDK of the Project API key.
### 0.10.0 (2026-11-03)
PRS browser `agents.listCompareModels(agentId)`, image/PDF/Storage files for comparison (up to 20 items for `storageFileIds`), read-only tools (up to 10 items), and candidate web reference sources have been added. Unavailable models are rejected with `model_not_allowed` before execution. In older backends without a corresponding comparison path, the model list will fail, the attachment will be empty, and no model usage authorization check will be performed.
`objects.define(id).bulkDelete(recordIds)` has also been added. The maximum number is 1,000, and if there is even one target that cannot be deleted, the process will fail without deleting all of them, and non-existent IDs will be skipped. Requires `object:delete`. As of 2026-11-11, this can be confirmed in the Object path of the PRS browser, but the production public API `DELETE /api/v1/objects/:objectId/records/bulk` has not been confirmed to be available (there is an implementation in the version under development). Please do not tell us that you can use it directly with the Project API key, but rather make sure that it is provided in the environment you will use.
### 0.9.0 (2026-10-23)
`includeCount` has been added to Object's `list()` and `search()`. `false` will omit all counts, and `total` will be 0. `hasNextPage` and cursor remain unchanged.
### 0.8.0 (2026-10-20)
- You can manage regular execution of Workflow in Project using `scheduledTasks` and CLI. The execution entity is the creator of the key, and requires `task:read`, `task:write` + `workflow:execute`, `task:delete` and the creator's privileges. These scopes cannot be declared in the App release manifest.
- `datasets` is read-only. `project` can be used with Project API key, but `organization` is for signed-in users of PRS browser and is 403 with Project API key. Search is only for retrieval.
- Creating/updating Agent definitions requires `agent:write` and creator authority. Change only the sent items, replace the array, and clear `null`.
- Record access rules require `object:read` and `object:policy` to list, `object:write` and `object:policy` to create and delete, and the creator's Object policy privilege.
### 0.7.0 (2026-10-05)
In PRS Page, acquisition of user's own turn or room usage amount and `platform.openAppSettings(tab?)` have been added. If there is no corresponding PRS runtime, getting the usage will result in a 404 error and the call to open the configuration screen will fail. Canvas publishing has a breaking change to `agents.publishCanvasArtifact(roomId, canvasArtifactId, scope)` and requires a scope of `"PUBLIC"` or `"ORGANIZATION"`. The old two-argument route cannot be used.
### 0.6.0 (2026-10-01)
Comparison turn tool event and candidate `tools`, PRS browser `storageFileIds` (up to 20 items), Canvas writing and publishing from Page have been added. Storage attachments are only available for PRS browsers, and PRSs that are not supported will respond without files. Canvas writing and publishing also requires a Page session and corresponding path. The included Page deployment aids `--no-inject-base` and `--allow-root-assets` have been removed.
### 0.5.0 (2026-09-28)
Added PRS browser `compare()`, `adoptCandidate()`, `getTurns()`. Comparison requires the corresponding PRS/agent-service and organization's `EnableTurnCandidates` settings. `jai-studio members list` has been added to the CLI. Fixed SDK to exclude local artifacts from skill staging and reject masked keys in `config show`.
### 0.4.0 (2026-09-15)
Listing and retrieving Canvas documents, creating/updating workflows, saving drafts, publishing, and deleting, reading definitions, and detecting draft conflicts have been added. The corresponding public API and required scope are assumed. With the corresponding route, non-owners cannot retrieve the messages and canvas of the private room, resulting in a 404 error.
### 0.3.0 (2026-09-10)
Optimistic concurrency control using the Object field validation rule type and record update/delete revisions has been added. PRS Page now has `platform.logout()`, which ends only the current Page session. The corresponding PRS runtime is required instead of signing out from the entire JAPAN AI account.
### 0.2.1 (2026-08-10)
Fixed PRS browser Object search to comply with public filtering and sorting agreements. It is not listed as a behavior change for the direct public API.
### 0.2.0 (2026-08-10)
`jai-studio skill install` and included skills have been added. Object updates require a fully typed payload and omitted regular fields are not preserved. Workflow wait input has been changed from `timeout` to `timeoutMs` (milliseconds). Node SDK and CLI are Node.js 22 or higher.
## Common Misconceptions
- **Use new features just by updating the SDK**: Check public API, PRS runtime, authentication mode, permissions, and organization settings separately.
- **npm publication date is production release date**: Varies. Don't confuse publish dates, merges, deployments, and enablement in each environment.
- **Only the CLI needs to be a different version**: Since they are provided in the same package, verify the configuration using the complete SDK and CLI versions.
## If you cannot confirm
Record the SDK/CLI/Node.js version, execution environment, target Project, connection destination, method or command, occurrence date and time, HTTP status, and results before and after updating. API keys, cookies, authentication headers, secret values, and tokens are not included in records or inquiries.
## Read next article
- [Notice of deprecated function/migration](deprecations-and-migrations.md)
- [Development environment/version/compatibility](../development/dev-environment-and-versions.md)
- [SDK/API version migration](../development/sdk-api-version-migration.md)
