# Preview a Page Locally and Deploy It

For a Page you developed, first check the browser-facing artifacts locally, then register it in Studio for the first time. You can select a ZIP in the Studio screen or deploy a ZIP or artifact directory with the Studio CLI. Check display on the development server separately from operation at the user's entry point.

This article covers initial registration. For updates to an existing Page, rollback, and version history, see [Manage Page Versions](../build/manage-page-versions.md).

## Related articles

- [Page Source Structure and Runtime](page-source-structure-and-runtime.md)
- [Register and Publish a Page from the Screen](../build/register-and-publish-pages.md)
- [Page Specifications and Limits](../build/page-specs-and-limits.md)
- [CLI Pages](../cli/cli-pages.md)

## Before you begin

- Check the target Project, Page name, users, and scope that may be published through initial registration. If initial registration from the screen finds an existing Page with the same name, confirmation for "Upload new version" appears. Confirm whether you are creating or updating before proceeding.
- Use Node.js 22 or later locally, and pin the CLI to the same full version as the SDK. For SDK/CLI versions and installation, see [Development Environment, Versions, and Compatibility](dev-environment-and-versions.md).
- Check the scripts in `package.json`. Studio does not install dependencies or run `npm run build` during registration. Generate artifacts locally.
- Prepare only artifacts to distribute to browsers, such as HTML, JavaScript, CSS, and images. Do not include API keys, tokens, Secret values, or personal information in artifacts, source maps, or public environment variables.

> `npm run dev` may be for a development server. Even if it displays locally, it may not display after ZIP registration if it still depends on transformations, proxies, or environment variables provided by that server. If possible, check built artifacts.

## 1. Build locally and check display

First install the dependencies defined by the project and run the build. Command names differ by project, so the following is an example when `package.json` has a `build` script.

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
| Initial display | No blank screen, console errors, or loading failures |
| Assets | JavaScript, CSS, images, fonts, and so on load from built relative paths |
| External dependencies | It does not depend only on a development-server proxy, local environment variables, or History API fallback |
| Confidential information | API keys and similar values do not appear in HTML, JavaScript, requests, or logs visible in browser developer tools |
| Business processing | You can check results at the save destination, sending destination, and so on |

> Not every Page can use `dev` or `preview`. If neither is defined, do not assume it must be added; check built static artifacts with the adopted toolchain.

## 2. Create a ZIP for distribution

Place the first HTML file to open and referenced assets at the ZIP root. Do not wrap the output folder itself, such as `dist` or `build`, in the ZIP; put its **contents** at the root. The following is an example where the output destination is `dist`.

```bash
cd dist
zip -r ../page-release.zip .
cd ..
unzip -l page-release.zip
```

In the `unzip -l` list, check that the initial HTML and necessary assets can be referenced from the ZIP root. Do not mix source files, `node_modules`, `.git`, `.jai-studio`, or a parent directory that wraps the output folder. Paths representing the current or parent directory cannot be registered.

Keep the ZIP at 100 MB or less, entry count at 5,000 or fewer, and total expanded size within 200 MB. For detailed excluded items and structural conditions, see [Page Specifications and Limits](../build/page-specs-and-limits.md).

## 3. Register for the first time from the Studio screen

1. Open "Pages" in the target Project and select "Upload ZIP."
2. In "Upload Page," select "Choose ZIP file" or place the ZIP in "Drop your ZIP file here."
3. Check the file name and target Project, then select "Upload." Do not close the screen while "Uploading file" is displayed.
4. If a Page with the same name is found, read the explanation on the confirmation screen. "Upload new version" adds and applies a new version of the existing Page. Do not update an existing Page when you intend initial registration.
5. Check "Page uploaded successfully." and confirm that the registration result appears in the Pages list.
6. Open the target and check the registered screen and representative operations.

If "You don't have permission to upload pages" appears, check with the Project or Page administrator. For capacity or structure errors, do not leave unnecessary files based on guesses to bypass screen limits; extract the ZIP and review its structure. For detailed screen operations, see [Register and Publish a Page from the Screen](../build/register-and-publish-pages.md).

## 4. Deploy for the first time with the CLI

When using the CLI, first securely configure a Project API key. Because a key written in a command argument may remain in shell history or process lists, use interactive input, standard input, or `JAI_STUDIO_API_KEY`. The following example creates a profile through interactive input.

```bash
npm install --global @japan-ai-inc/studio-sdk@0.11.0
jai-studio config set my-project
jai-studio --profile my-project pages deploy ./dist --name "Order Dashboard" --json
```

You can pass a ZIP file or directory to `pages deploy`. When you pass a directory, the CLI creates a ZIP, so specify the completed `./dist`. For an initial deployment, you can specify the display name with `--name`. On success, the JSON returns the Page ID, `studioUrl`, `runtimeUrl`, version, file count, time, and whether a rebuild is required.

If `runtimeUrl` is `null`, open the target Page from `studioUrl` and check it. Record URLs and Page IDs in a safe place; do not record API keys.

> When you first deploy a directory, the CLI stores the Page ID and similar information in `.jai-studio/page.json` inside that directory. The next time you specify the same directory without `--page-id`, it updates using that ID. Do not reuse initial-deployment artifacts carelessly. For updates to existing Pages, see [Page Build, Start, and Update](page-build-start-update.md) and [Manage Page Versions](../build/manage-page-versions.md).

If you already created the ZIP yourself, you can specify it with the same command. Use `--save-page-metadata` only when saving metadata for ZIP input.

```bash
jai-studio --profile my-project pages deploy ./page-release.zip \
  --name "Order Dashboard" \
  --save-page-metadata ./page-metadata.json \
  --json
```

## 5. Check the access URL and user entry point

Success from the CLI or "Page uploaded successfully." does not complete access verification for users. Open the target Page in Studio and check "Access Settings" from the Page menu.

1. Select the access mode required by users and check "Access settings saved."
2. For "Sign-in optional" or "Anonymous only," check the displayed "Public URL." Sharing this URL allows access without signing in to Studio, so check displayed data and executable processing before providing it.
3. For "External customer auth," select a configured external authentication connection. After saving, check that the "Access URL" is valid. This URL becomes valid after the settings are saved and a published version exists.
4. Open it from the intended user entry point (within Studio, Public URL, or Access URL), and check display, permissions, representative operations, and the result at the save or sending destination.

Being able to open a Page does not guarantee that data retrieval, authentication, external integration, or updates succeed. For items to check during actual use, see [Move Your Work Forward from a Page](../use/work-with-pages.md).

## Checklist

- [ ] I displayed built artifacts locally and checked representative operations and results
- [ ] I placed HTML and necessary assets at the ZIP root and did not include confidential information
- [ ] I checked ZIP capacity, entry count, expanded size, and structure
- [ ] I decided whether to use the UI or CLI for initial registration and matched the target Project and Page name
- [ ] In the CLI, I did not write the API key in an argument and used the correct profile or secure environment variable
- [ ] After deployment, I opened the Page from `studioUrl` or its Page card
- [ ] I checked "Access Settings" and the sharing scope of the Public URL or Access URL
- [ ] I checked screen and business-processing results from the intended user entry point

## If something does not work

| Situation | What to check and do |
|---|---|
| It works locally but shows a blank screen after registration | Check built HTML and asset relative paths, and dependencies on proxies or environment variables available only on the development server. Extract the ZIP and check its root structure. |
| The ZIP is not accepted | Check 100 MB, 5,000 entries, 200 MB expanded size, paths in the ZIP, and excluded items. Consult with the screen error and ZIP list. |
| The CLI cannot authenticate | Check the target profile and destination with `jai-studio config show`. Do not reuse the displayed masked key; configure the stored actual value securely. |
| The CLI appears likely to update an existing Page unintentionally | Check `.jai-studio/page.json` in the directory, `--page-id`, and the target Page ID. Use a new artifact directory for the initial deployment, or stop and check the update procedure. |
| It cannot be opened from a URL, or unexpected people can open it | Check "Access Settings," access mode, the published version, and sharing destination. Consult the administrator before expanding URL sharing. |

For an inquiry, record the target Project and Page name, SDK/CLI version, executed command excluding the key, ZIP file list, Page ID, occurrence time, displayed message, and reproduction steps. Do not attach API keys, tokens, cookies, Secret values, or URLs before publication.

## Read next

- [Register and Publish a Page from the Screen](../build/register-and-publish-pages.md)
- [Manage Page Versions](../build/manage-page-versions.md)
- [Page Specifications and Limits](../build/page-specs-and-limits.md)
- [CLI Pages](../cli/cli-pages.md)
- [Page Build, Start, and Update](page-build-start-update.md)
