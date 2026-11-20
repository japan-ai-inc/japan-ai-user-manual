# Preview a Page Locally and Deploy It

Check install, `build`, and display locally for the Page you developed, then **ZIP the source** and register it in Studio for the first time. You can choose a ZIP on the Studio screen, or deploy a ZIP or directory with the Studio CLI. After upload, the runtime for Pages hosted in Studio (the Page runtime) selects Bun, Node, or static according to the lockfile, runs install and `build`, and then starts the Page.

This article covers initial registration. For updates to an existing Page, rollback, and version history, see [Manage Page Versions](../build/manage-page-versions.md).

## Key points

| Item | Details |
|---|---|
| What to upload | The whole source (`package.json` and a lockfile at the root, or `index.html` for a static Page). Do not include `node_modules` or build output |
| Required for the CLI | A Project API key with the `page:write` scope |
| Updating with the CLI | Always specify `--page-id` in update scripts (without it, a new Page may be created) |
| First display | The build can take several minutes (up to 15 minutes) |
| `runtimeUrl` | Always `null` with the default "Sign-in required". Open the Page from `studioUrl` |

## Related articles

- [Page Source Structure and Runtime](page-source-structure-and-runtime.md)
- [Register and Publish a Page from the Screen](../build/register-and-publish-pages.md)
- [Page Specifications and Limits](../build/page-specs-and-limits.md)
- [CLI Pages](../cli/cli-pages.md)

## Before you begin

- Check the target Project, Page name, users, and the scope that may be published by the initial registration. During initial registration from the screen, if the Page is judged to have the same name as an existing Page, an "Upload new version" confirmation appears. Confirm whether you are creating a new Page or updating before proceeding.
- Use Node.js 22 or later locally, and pin the CLI to the same full version as the SDK. For SDK/CLI versions and installation, see [Development Environment, Versions, and Compatibility](dev-environment-and-versions.md).
- Check that `package.json` and a lockfile are at the root and that the `build` script succeeds locally. Studio runs install and `build` after upload.
- Pages with a server must listen on `process.env.PORT`. For structure details, see [Page Source Structure and Runtime](page-source-structure-and-runtime.md).
- Do not include API keys, tokens, Secret values, or personal information in the source, source maps, or public environment variables.

> `npm run dev` is for the development server. Even if the Page displays locally, it may not display after registration if it depends on development-server-specific proxies or environment variables. Also check behavior after `build` locally.

## 1. Build locally and check display

As Studio does, check that dependency installation and `build` succeed locally. The following is an example when `package.json` has a `build` script.

```bash
npm install
npm run build
```

To check the screen during development, use the following if a `dev` script is defined.

```bash
npm run dev
```

If a script is defined for checking build results in a production-like form, also run that command. For example, a project with `preview` would use the following.

```bash
npm run preview
```

Open the displayed local URL in a browser and check the initial display, screen navigation and asset loading, representative operations, and results of input, registration, and sending. For operations involving registration or external transmission, use test data and a test destination, and do not repeat processing whose result is unknown.

| Item to check | What to check |
|---|---|
| Build | Install and `build` complete without errors |
| Initial display | No blank screen, console errors, or loading failures |
| Delivery path | `basePath` or relative paths are set so assets load under a sub-path |
| Server | If there is a server, it listens on `process.env.PORT` |
| Confidential information | API keys and similar values do not appear in HTML, JavaScript, requests, or logs visible in browser developer tools |
| Business processing | Results at the save destination, sending destination, and so on are as expected |

## 2. Create a ZIP for distribution

Place `package.json`, the lockfile, and the source at the ZIP root. Do not include `node_modules`, local build output (`dist`, `build`, and so on), or `.git`. The following example runs at the project root.

```bash
zip -r ../page-source.zip . -x "node_modules/*" "dist/*" "build/*" ".git/*"
unzip -l ../page-source.zip
```

In the `unzip -l` list, check that `package.json` and the lockfile (or `index.html` for a static Page) are at the root. A ZIP with neither `package.json` nor `index.html` at the root, and a ZIP containing an uploaded Next.js build output, are rejected. Paths representing the current or parent directory cannot be registered.

Keep the ZIP at 100 MB or less, entry count at 5,000 or fewer, and total expanded size within 200 MB. For detailed excluded items and structural conditions, see [Page Specifications and Limits](../build/page-specs-and-limits.md).

## 3. Register for the first time from the Studio screen

1. Open "Pages" in the target Project and select "Upload ZIP."
2. In "Upload Page," select "Choose ZIP file" or place the ZIP in "Drop your ZIP file here."
3. If a Page with the same name is found, a confirmation screen appears first. "Upload new version" adds and applies a new version of the existing Page. Do not update an existing Page when you intend initial registration.
4. Check the file name and target Project, then select "Upload." Do not close the screen while "Uploading file" is displayed.
5. Check "Page uploaded successfully." and confirm that the registration result appears in the Pages list.
6. Open the target. A dedicated screen is shown while building, and the first display can take several minutes (up to 15 minutes). After it displays, check representative operations.

If "You don't have permission to upload pages" appears, check with the Project or Page administrator. For capacity or structure errors, extract the ZIP and review its structure. If the build failure screen appears, check that install and `build` succeed locally. For detailed screen operations, see [Register and Publish a Page from the Screen](../build/register-and-publish-pages.md).

## 4. Deploy for the first time with the CLI

When using the CLI, first securely configure a Project API key with the **`page:write` scope**. Because a key written in a command argument may remain in shell history or process lists, use interactive input, standard input, or `JAI_STUDIO_API_KEY`. The following example creates a profile through interactive input and deploys the ZIP created in the previous step.

```bash
npm install --global @japan-ai-inc/studio-sdk@0.11.0
jai-studio config set my-project
jai-studio --profile my-project pages deploy ./page-source.zip \
  --name "Order Dashboard" \
  --save-page-metadata ./page-metadata.json \
  --json
```

You can pass a ZIP file or directory to `pages deploy`. When you pass a directory, the CLI creates a ZIP. For an initial deployment, you can specify the display name with `--name`. On success, the JSON returns the Page ID, `studioUrl`, `runtimeUrl`, version, file count, time, and whether a rebuild is required.

With the default access mode "Sign-in required," `runtimeUrl` is always `null`. Open the target Page from `studioUrl` and check it. Record the Page ID in a safe place; do not record API keys.

{% hint style="warning" %}
When you deploy a directory, the CLI stores the Page ID and similar information in `.jai-studio/page.json` inside that directory, and the next time you specify the same directory without `--page-id`, it updates using that ID. However, if you specify a build-output folder such as `./dist`, common bundlers empty that folder on rebuild, so this file is also deleted. If you then deploy without `--page-id`, **a new Page is silently created** (with a new Page ID and URL).

Always specify `--page-id` in update scripts. For updates to existing Pages, see [Page Build, Start, and Update](page-build-start-update.md) and [Manage Page Versions](../build/manage-page-versions.md).
{% endhint %}

To save the Page ID and similar information for ZIP input, use `--save-page-metadata` as in the example above and specify a file outside the build output.

## 5. Check the access URL and user entry point

Success from the CLI or "Page uploaded successfully." does not complete access verification for users. Open the target Page in Studio and check "Access Settings" from the Page menu.

1. Select the access mode required by users and check "Access settings saved."
2. For "Sign-in optional" or "Anonymous only," check the displayed "Public URL" (in `/p/{token}` format). Links built yourself in `/app/...` format do not work. Sharing this URL allows access without signing in to Studio, so check displayed data and executable processing before providing it.
3. For "External customer auth," select a configured external authentication connection. After saving, check that the "Access URL" is valid. This URL becomes valid after the settings are saved and a published version exists.
4. Open it from the intended user entry point (within Studio, Public URL, or Access URL), and check display, permissions, representative operations, and the result at the save or sending destination.

Being able to open a Page does not guarantee that data retrieval, authentication, external integration, or updates succeed. For items to check during actual use, see [Move Your Work Forward from a Page](../use/work-with-pages.md).

## Checklist

- [ ] Install and `build` succeeded locally, and I checked representative operations and results
- [ ] I placed `package.json` and a lockfile (or `index.html` for a static Page) at the ZIP root and did not include `node_modules`, build output, or confidential information
- [ ] I checked ZIP capacity, entry count, expanded size, and structure
- [ ] I decided whether to use the UI or CLI for initial registration and matched the target Project and Page name
- [ ] In the CLI, I used a key with the `page:write` scope and did not write it in an argument
- [ ] My update scripts always specify `--page-id`
- [ ] After deployment, I waited for the build to finish and opened the Page from `studioUrl` or its Page card
- [ ] I checked "Access Settings" and the sharing scope of the Public URL or Access URL
- [ ] I checked screen and business-processing results from the intended user entry point

## If something does not work

| Situation | What to check and do |
|---|---|
| The ZIP is rejected | Check whether `package.json` or `index.html` is at the root, whether you uploaded a Next.js build output, and the limits of 100 MB, 5,000 entries, and 200 MB expanded size. |
| The build failure screen appears | Check that the lockfile and `package.json` are consistent and that `build` succeeds locally. |
| A blank screen or no response after registration | Check that it listens on `process.env.PORT`, that `basePath` matches the delivery URL, and that it does not depend on proxies or environment variables available only on the development server. |
| The CLI cannot authenticate or returns a permission error | Check the target profile and destination with `jai-studio config show`. Check that the key has the `page:write` scope. Do not reuse the displayed masked key; configure the stored actual value securely. |
| A new Page was created when you meant to update | `.jai-studio/page.json` may have been deleted by the build. Specify `--page-id` from now on. Consult the administrator about the unneeded new Page. |
| `runtimeUrl` is `null` | It is always `null` with the default "Sign-in required." Open the Page from `studioUrl`. |
| It cannot be opened from a URL, or unexpected people can open it | Check "Access Settings," access mode, the published version, and sharing destination. For anonymous access, share the "Public URL" (`/p/{token}`). Consult the administrator before expanding URL sharing. |

For an inquiry, record the target Project and Page name, SDK/CLI version, executed command excluding the key, ZIP file list, Page ID, occurrence time, displayed message, and reproduction steps. Do not attach API keys, tokens, cookies, Secret values, or URLs before publication.

## Read next

- [Register and Publish a Page from the Screen](../build/register-and-publish-pages.md)
- [Manage Page Versions](../build/manage-page-versions.md)
- [Page Specifications and Limits](../build/page-specs-and-limits.md)
- [CLI Pages](../cli/cli-pages.md)
- [Page Build, Start, and Update](page-build-start-update.md)
