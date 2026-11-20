# Release Notes
This article describes changes to Studio and how to check them. SDK/CLI, Studio UI, the public API, and the Page runtime environment are delivered separately. The SDK's npm release date is not necessarily the date the change reaches production. Unless otherwise specified, dates in this article are npm release dates from the `CHANGELOG.md` bundled with the SDK.
## Related articles
- [Notice of deprecated function/migration](deprecations-and-migrations.md)
- [Development environment/version/compatibility](../development/dev-environment-and-versions.md)
- [SDK/API version migration](../development/sdk-api-version-migration.md)
## Scope of this article
The SDK/CLI history to be posted is 0.2.0 to 0.11.0 in `CHANGELOG.md` included in npm package `@japan-ai-inc/studio-sdk`. The SDK and CLI are the same package, and the CLI name is `jai-studio`. Since the SDK is less than 1.0, there may be breaking changes even in the minor version. Please pin the full verified version instead of range specification or `latest`.
- **Studio UI**: Check the articles for Studio Projects and installed Apps for screen operations. This article does not cover changes to organization-wide screens outside Studio.
- **Public API/Page runtime environment**: Usability is not determined only by updating the SDK. Check the route, authentication mode, permissions, and organization settings in the target environment.
## How to read release notes
Divide the target into Studio UI, SDK/CLI, public API, and Page runtime environment, and check the conditions for providing the function and the authentication mode to be used.
## Format of published entry
Below is a history summarizing the version, release date, changes, and necessary actions. If there are any usage route conditions, they will be listed in each edition.
## Check first
1. Divide the targets into Studio UI, SDK/CLI, public API, and Page runtime environment.
2. Check all entries from the current version to the update destination and the methods, types, and CLIs in use.
3. In the verification environment, check the connection destination, authentication mode, representative operation, and behavior when failure occurs.
```bash
node --version
npm ls @japan-ai-inc/studio-sdk
jai-studio --version
```

Node.js requirements for SDK 0.11.0 and CLI are 22 or higher. If you use the SDK and CLI together, align them to the same complete version.
## Public API and production changes

### 2026-11-13 (production release date): Bulk delete API for Object records

`DELETE /api/v1/objects/:objectId/records/bulk` was added to the public API, so records can be bulk-deleted with a Project API key. It requires the `object:delete` scope, accepts up to 1,000 records per request, and either succeeds for all records or fails for all records (no partial deletion). In the SDK, use `objects.define(id).bulkDelete(recordIds)` from 0.10.0.

## SDK/CLI changes
### 0.11.0 (2026-11-06)
`models` of `compare()` accepts `{ model, effort? }` (`low`/`mid`/`high`/`max`) in addition to the model name. `primaryIndex` takes precedence over `primary`, and the same model can be placed side by side with different efforts. Same model/same effort will be rejected with "models must differ in model or effort". `candidateEfforts`, `effortSupported`, and candidate `effort` have been added.
Response comparison is a feature used from the browser of a Page hosted in Studio. Direct calls with a Project API key are not supported. A duplicate of the same model with the same effort is rejected inside the SDK before sending, and this error has no error code.
### 0.10.0 (2026-11-03)
`agents.listCompareModels(agentId)` in the Page browser, image/PDF/Storage files for comparison (up to 20 items for `storageFileIds`), read-only tools (up to 10 items), and candidate web reference sources have been added. Unavailable models are rejected with `model_not_allowed` before execution.
`objects.define(id).bulkDelete(recordIds)` has also been added. Up to 1,000 records; it either succeeds for all or fails for all (if even one target cannot be deleted, nothing is deleted). Non-existent IDs are skipped. Requires `object:delete`. It works both from the Page browser and through the public API with a Project API key (`DELETE /api/v1/objects/:objectId/records/bulk`, available in production since 2026-11-13).
### 0.9.0 (2026-10-23)
`includeCount` has been added to Object's `list()` and `search()`. `false` will omit all counts, and `total` will be 0. `hasNextPage` and cursor remain unchanged.
### 0.8.0 (2026-10-20)
- You can manage regular execution of Workflow in Project using `scheduledTasks` and CLI. The execution entity is the creator of the key, and requires `task:read`, `task:write` + `workflow:execute`, `task:delete` and the creator's privileges. These scopes cannot be declared in the App release manifest.
- `datasets` is read-only. `project` can be used with a Project API key, but `organization` is for users signed in through the Page browser and returns 403 with a Project API key. Search is only for retrieval.
- Creating/updating Agent definitions requires `agent:write` and creator authority. Change only the sent items, replace the array, and clear `null`.
- Record access rules require `object:read` and `object:policy` to list, `object:write` and `object:policy` to create and delete, and the creator's Object policy privilege.
### 0.7.0 (2026-10-05)
In Pages hosted in Studio, retrieval of the user's own turn or room usage and `platform.openAppSettings(tab?)` have been added. If the Page runtime environment does not support them, getting the usage returns 404 and the call to open the settings screen fails. Canvas publishing has a breaking change to `agents.publishCanvasArtifact(roomId, canvasArtifactId, scope)` and requires a scope of `"PUBLIC"` or `"ORGANIZATION"`. The old two-argument route cannot be used.
### 0.6.0 (2026-10-01)
Comparison turn tool events and candidate `tools`, `storageFileIds` in the Page browser (up to 20 items), and Canvas writing and publishing from Pages have been added. Storage attachments are available only in the Page browser; a Page runtime environment that does not support them responds without files. Canvas writing and publishing also require a Page session. The bundled Page deployment aids `--no-inject-base` and `--allow-root-assets` have been removed.
### 0.5.0 (2026-09-28)
Added `compare()`, `adoptCandidate()`, and `getTurns()` in the Page browser. Response comparison requires the feature to be enabled for your organization. `jai-studio members list` has been added to the CLI. Fixed SDK to exclude local artifacts from skill staging and reject masked keys in `config show`.
### 0.4.0 (2026-09-15)
Listing and retrieving Canvas documents, creating/updating workflows, saving drafts, publishing, and deleting, reading definitions, and detecting draft conflicts have been added. The corresponding public API and required scope are assumed. With the corresponding route, non-owners cannot retrieve the messages and canvas of the private room, resulting in a 404 error.
### 0.3.0 (2026-09-10)
Optimistic concurrency control using the Object field validation rule type and record update/delete revisions has been added. Pages hosted in Studio now have `platform.logout()`, which ends only the current Page session; it does not sign out of the entire JAPAN AI account.
### 0.2.1 (2026-08-10)
Fixed Object search from the Page browser to follow the public API filtering and sorting specification.
### 0.2.0 (2026-08-10)
`jai-studio skill install` and included skills have been added. Object updates require a fully typed payload and omitted regular fields are not preserved. Workflow wait input has been changed from `timeout` to `timeoutMs` (milliseconds). Node SDK and CLI are Node.js 22 or higher.
## Common Misconceptions
- **Use new features just by updating the SDK**: Check the public API, Page runtime environment, authentication mode, permissions, and organization settings separately.
- **npm publication date is production release date**: Varies. Don't confuse publish dates, merges, deployments, and enablement in each environment.
- **Only the CLI needs to be a different version**: Since they are provided in the same package, verify the configuration using the complete SDK and CLI versions.
## If the issue is not resolved
Record the SDK/CLI/Node.js version, execution environment, target Project, connection destination, method or command, occurrence date and time, HTTP status, and results before and after updating. API keys, cookies, authentication headers, secret values, and tokens are not included in records or inquiries.
## Read next article
- [Notice of deprecated function/migration](deprecations-and-migrations.md)
- [Development environment/version/compatibility](../development/dev-environment-and-versions.md)
- [SDK/API version migration](../development/sdk-api-version-migration.md)
