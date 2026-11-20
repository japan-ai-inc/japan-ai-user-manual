# CLI Pages

`jai-studio pages deploy` uploads your Page project source (a ZIP file or directory) and deploys it as a Studio Page. You can use the same command for initial Page registration and for registering a new version of an existing Page. This article is a reference for arguments, metadata, and output when using it from a development machine or managed development automation.

The CLI itself does not build, but **Studio builds the Page**. After upload, Studio selects Bun, Node, or static based on the lockfile, installs dependencies, runs the `build` script in `package.json`, and runs Node/Bun Pages as servers. Therefore, what you upload is **your project source**, not prebuilt artifacts.

| What you upload | Required at the root |
|---|---|
| Node/Bun Page | `package.json` and a lockfile |
| Static Page | `index.html` |

Uploads with neither `package.json` nor `index.html` at the root, and uploads of Next.js build output, are rejected. For preparing and verifying an initial deployment at the user entry point, see [Preview a Page Locally and Deploy It](../development/local-preview-and-deploy-page.md); for verification and rollback after an update, see [Page Build, Start, and Update](../development/page-build-start-update.md).

## Related articles

- [CLI Configuration and Profiles](cli-config-and-profiles.md)
- [Preview a Page Locally and Deploy It](../development/local-preview-and-deploy-page.md)
- [Page Build, Start, and Update](../development/page-build-start-update.md)
- [SDK Pages and Platform](../development/sdk-pages-and-platform.md)
- [Page Specifications and Limits](../build/page-specs-and-limits.md)

## Before you begin

- Prepare Node.js 22 or later, `@japan-ai-inc/studio-sdk` 0.11.0, and a Project API key with the `page:write` scope for the target Project. Follow [CLI Configuration and Profiles](cli-config-and-profiles.md) for installation, profiles, and the priority order of endpoint and key.
- Do not write a key in `--key`. It may remain in shell history or a process list. Use a profile, `JAI_STUDIO_API_KEY`, or a secure CI secret setting.
- For directory input, the **contents** of that directory become the root of the ZIP. Specify the project root that contains `package.json` (or `index.html` for a static Page). `node_modules` is excluded automatically. Do not include unnecessary secrets.
- ZIP capacity, structure, number of entries, and uncompressed size are validated by the registration destination separately from CLI packaging. Check the 100MB, 5,000-item, and 200MB limits in [Page Specifications and Limits](../build/page-specs-and-limits.md).

> Even if Page deployment succeeds, it does not mean users can open it or that authentication, data processing, and external sending have succeeded. After success, check the initial display and representative operations through `studioUrl` or the user entry point.

## `pages deploy`

```text
jai-studio [--profile <name>] [--env <env>] pages deploy <zip-or-dir> [options]
```

Notation containing `<...>` below is **syntax that requires substitution**. Do not paste it into the shell with the angle brackets; replace it with an actual value.

| Argument or flag | Required | Default | Meaning |
|---|---:|---|---|
| `<zip-or-dir>` | Yes | — | ZIP file to deploy, or the project root directory. |
| `--name <name>` | No | — | Display name for a **new Page**. It is not passed in the deployment request when an update destination is determined. |
| `--page-id <pageId>` | No | — | ID of the existing Page to update. When specified, deploys as a new version of that Page. |
| `--timeout-ms <milliseconds>` | No | SDK default | Timeout passed to upload preparation, ZIP sending, and the deployment request. Specify only a positive finite number. |
| `--save-page-metadata <path>` | No | — | Path to write Page metadata when the input is a ZIP file. |
| `--json` | No | None | Output specification accepted by `pages deploy`. Successful results are output as JSON to standard output whether or not it is specified. |

`--pretty` is a global flag. To format successful JSON, specify it before the subcommand, not after it.

```bash
jai-studio --profile my-project --pretty pages deploy ./my-page \
  --name "Order dashboard" --json
```

This example explicitly updates an existing Page. Pass the target Page ID to `PAGE_ID` from a secure environment variable or similar source, and check the target before running.

```bash
jai-studio --profile my-project pages deploy ./page-release.zip \
  --page-id "$PAGE_ID" --timeout-ms 600000 --json
```

### How the initial deployment and update destination are determined

The CLI first checks `--page-id`. If there is no `--page-id` for directory input, it then reads `.jai-studio/page.json` in that directory; if it has a nonempty string `pageId`, that becomes the update destination. If neither ID exists, it is an initial deployment.

| Condition | Deployment performed | `--name` |
|---|---|---|
| `--page-id` is specified | New version of the specified existing Page | Not used. |
| Metadata in the directory has `pageId` | New version of that existing Page | Not used. |
| Otherwise | New Page | Passed as the new Page display name when specified. |

If the metadata file is missing, cannot be read, is invalid JSON, or does not have a valid `pageId`, it is handled as having no metadata, and **a new Page is created without warning** (with a new Page ID and URL).

{% hint style="warning" %}
Metadata is written **inside the input directory** (`<input directory>/.jai-studio/page.json`). If you specify a build output folder such as `./dist`, common bundlers empty that folder on rebuild, so the next deployment without `--page-id` silently creates a new Page.

- In scripts or CI that update an existing Page, **always specify `--page-id`**.
- For directory input, specify your project source folder, not a build output folder.
{% endhint %}

> `--page-id` takes priority over `.jai-studio/page.json` in the directory. If you do not know the update destination, do not run it by guesswork; check the target Page and ID.

### Directory ZIP creation and excluded items

When you specify a directory, the CLI creates and sends a ZIP. Regular files are added recursively; empty directories and symbolic links are not added. At every level, files or directories with the following names are excluded.

`__MACOSX`, `node_modules`, `.git`, `.DS_Store`, `.jai-studio`, `__staging`, `__upload-lock.json`, `__backup`, `__versions`

Because `.jai-studio` is excluded, metadata used for directory updates does not enter the uploaded ZIP. Dependencies (`node_modules`) are installed by Studio. Do not put required source files under excluded names. When you specify a ZIP file, the CLI does not repackage its content; it uploads the file name and bytes as-is.

## `.jai-studio/page.json` and `--save-page-metadata`

On success, the CLI saves the following information as JSON. `updatedAt` is the ISO 8601 date and time when it is saved.

```json
{
  "pageId": "…",
  "studioUrl": "…",
  "runtimeUrl": null,
  "version": "…",
  "updatedAt": "…"
}
```

For directory input, the save path is always `<input directory>/.jai-studio/page.json`. Required directories are created. For ZIP input, there is no default save location; specify `--save-page-metadata <path>` only when saving. Conversely, even if you specify `--save-page-metadata` for directory input, the save location does not change.

```bash
jai-studio --profile my-project pages deploy ./page-release.zip \
  --name "Order dashboard" \
  --save-page-metadata ./page-metadata.json --json
```

This file contains a Page ID and URL. Do not commit it to a repository; keep it in a location restricted to staff who need access. Do not add Project API keys, Cookies, or Secret values to metadata.

## Output and verification

On success, one JSON object is output to standard output. Check at least the following fields.

| Field | Meaning |
|---|---|
| `pageId` | ID of the Page created or updated. You can use it as an explicit update destination next time. |
| `studioUrl` | URL for checking the target Page in Studio. |
| `runtimeUrl` | URL for running the Page. It is `null` when it does not exist, and it is always `null` for Pages with the default "sign-in required" access setting. Do not treat `null` as a URL; check from `studioUrl`. |
| `version` | Version resulting from the deployment. |

The result also includes `fileCount`, `createdAt`, and `rebuildTriggered`. In automation, parse JSON from standard output, and handle standard error and the exit code separately.

## Troubleshooting

| Situation | Check or response |
|---|---|
| It may update an existing Page when you intended an initial registration | Check `--page-id`, `.jai-studio/page.json` in the input directory, and the target Project and Page ID. |
| A new Page was created when you intended an update | `.jai-studio/page.json` in the input directory may have been removed (for example, by regenerating a build output folder). From then on, always specify `--page-id`. |
| The upload is rejected | Check that the root has `package.json` and a lockfile, or `index.html`. Specify the source, not Next.js build output. |
| Specifying `--timeout-ms` fails | Check that the unit is milliseconds and that you did not specify zero or less, infinity, or a nonnumeric value. Do not set an excessively large value by guesswork. |
| A required file is missing after deployment | Check the project root and names of excluded items. `node_modules` and `.jai-studio` are not uploaded. |
| The URL cannot be used or `runtimeUrl` is `null` | For "sign-in required" Pages, `runtimeUrl` is always `null`. Check the Page and access settings from `studioUrl`, and verify it through the same entry point as users. |
| The CLI exits with an error | Check the target profile, endpoint, key, ZIP, and Page ID. On API failure, JSON containing `error`, `statusCode`, and `details` is output to standard error (plain text for usage errors), and the exit code is 1. Because `--debug` details may contain confidential information, do not use it in shared logs. |

For inquiries, record the CLI version, command without the key, input type, Page ID, occurrence time, standard error, and reproduction steps. Do not attach API keys, Cookies, Secret values, or URLs before publication.

## Checklist

- [ ] I specified the project source (with `package.json` and a lockfile, or `index.html`, at the root) as a ZIP or directory.
- [ ] I decided whether it is an initial registration or update, and explicitly specified `--page-id` for updates.
- [ ] I confirmed that `--name` is used only for a new Page.
- [ ] I specified `--timeout-ms` as a positive finite number of milliseconds, or used the default.
- [ ] I did not place required files under excluded names for directory input.
- [ ] I checked `pageId`, `studioUrl`, `runtimeUrl`, and `version` in a safe record location.
- [ ] I checked the Page and representative operations through the same entry point as users.

## Next articles to read

- [CLI Configuration and Profiles](cli-config-and-profiles.md)
- [Preview a Page Locally and Deploy It](../development/local-preview-and-deploy-page.md)
- [Page Build, Start, and Update](../development/page-build-start-update.md)
- [SDK Pages and Platform](../development/sdk-pages-and-platform.md)
- [Page Specifications and Limits](../build/page-specs-and-limits.md)
