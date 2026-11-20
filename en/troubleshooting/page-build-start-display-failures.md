# A Page Fails to Build, Start, or Display

If a Page ZIP cannot be registered, does not start after registration, displays a blank screen, returns 404 at a deep URL, repeatedly authenticates, or a process using a Secret fails, check the failed stage separately. A Page is uploaded as a ZIP of its **source**. After upload, the runtime for Pages hosted in Studio (the Page runtime) selects Bun, Node.js, or static delivery based on the lockfile, runs install and `build`, and then starts the Page. Check upload, build, access, display, and business actions separately.

## Key points: how a Page is built and started

| Item | Rule |
|---|---|
| What goes in the ZIP | The source, not built output. Place `package.json` and a lockfile at the root, or `index.html` for a static Page. |
| Rejected ZIPs | An uploaded Next.js build output, or a ZIP with neither `package.json` nor `index.html` at the root |
| Build | Bun, Node.js, or static delivery is selected by lockfile; for Node.js/Bun projects, install and the `build` script in `package.json` are run. |
| Time to display | The first display can take several minutes (up to 15 minutes). Meanwhile a build-in-progress screen is shown, and a build-failed screen is shown on failure. |
| Server processing | Listen on `process.env.PORT`. The first SSR response has a limit of about 60 seconds. |

## Related articles

- [Build, start, and update Pages](../development/page-build-start-update.md)
- [Page specifications and limits](../build/page-specs-and-limits.md)
- [Page source structure and runtime](../development/page-source-structure-and-runtime.md)
- [Preview a Page locally and deploy it](../development/local-preview-and-deploy-page.md)
- [Page navigation and deep links](../development/page-navigation-and-deep-links.md)
- [Page server processing, environment variables, and Secret references](../development/page-server-env-and-secrets.md)
- [Register and publish Pages from the screen](../build/register-and-publish-pages.md)
- [Manage Page versions](../build/manage-page-versions.md)

## Before you begin

- Record the target organization, Project, and Page; the ZIP name used; the currently "Applied" version; and the time of occurrence. The highest-numbered version and the Applied version do not necessarily match.
- Check locally that install and `build` succeed, and check the initial display, asset loading, and representative changed action and result on the build result. It is not enough that it works on a development server.
- Extract the ZIP and check that `package.json` and a lockfile (or `index.html` for a static Page) are at the root. A single parent folder is removed, but a build output folder such as `dist` or `build` at the top level of the ZIP is excluded.
- Treat opening the Page, authenticating, and successfully performing data actions or external integrations as separate checks. Do not repeatedly upload, restore, or perform business processing before the cause is identified.

> Do not include API keys, Cookies, tokens, actual Project Secret values, or other secrets in HTML, JavaScript, configuration, source maps, or logs delivered to the browser. Variable names that conceal values and obfuscation are not protection.

## Isolate the issue by symptom

| Symptom or displayed message | Likely cause | What to check or do | Detailed article |
|---|---|---|---|
| "You don't have permission to upload pages" | You do not have permission to update the Page or Project. | Check the correct organization, Project, and target Page, and ask the administrator responsible to confirm the required permissions. Do not change publishing settings merely to make the action appear. | [Register and publish Pages from the screen](../build/register-and-publish-pages.md), [Build, start, and update Pages](../development/page-build-start-update.md) |
| "The ZIP file did not contain any files." | The ZIP is completely empty. | Recreate the ZIP with the full source. | [Page specifications and limits](../build/page-specs-and-limits.md) |
| A structure error, or the ZIP is not accepted | All files are in excluded folders (`node_modules`, top-level `dist` or `build`, and so on); paths contain current or parent directory components; neither `package.json` nor `index.html` is at the root; a Next.js build output was uploaded; or a limit is exceeded. | Extract the ZIP and check that the source is at the top level (or directly under a single parent folder) and that `package.json` and a lockfile, or `index.html`, are at the root. Check limits in [Page specifications and limits](../build/page-specs-and-limits.md). | [Page specifications and limits](../build/page-specs-and-limits.md), [Page source structure and runtime](../development/page-source-structure-and-runtime.md) |
| "Upload failed. Please try again." or "Another upload is already in progress. Please try again shortly." | A communication or registration process failed, processing for the same Page is in progress, or there is an issue with the target or ZIP. | Do not retry immediately; record the target Page, ZIP name, and displayed message. After some time, check "Applied" in "Version History" and the actual Page, then retry only once if necessary. | [Register and publish Pages from the screen](../build/register-and-publish-pages.md), [Manage Page versions](../build/manage-page-versions.md) |
| "Failed to switch version", or the expected version does not become "Applied" | Switching versions did not complete, the target Page is incorrect, or processing is in progress. This message is not shown immediately after an upload. | In "Version History," check whether the intended version is "Applied," as well as its time, name, and uploader. While the cause is unknown, do not perform additional uploads or restores. | [Build, start, and update Pages](../development/page-build-start-update.md), [Manage Page versions](../build/manage-page-versions.md) |
| Opening the Page shows the build-in-progress screen | Install and `build` are running after upload. The first display can take up to 15 minutes. | Wait about 15 minutes and reopen. Do not repeat the same update meanwhile. If it is still building after 15 minutes, do the checks in the next row. | [Build, start, and update Pages](../development/page-build-start-update.md) |
| The build-failed screen is shown, or the Page is still building after 15 minutes | Install or `build` failed: a missing or inconsistent lockfile, an incorrect `build` script in `package.json`, or dependencies that exist only in your local environment. | Check locally that install and `build` succeed using only the source in the ZIP. Upload the corrected source as a new version. | [Build, start, and update Pages](../development/page-build-start-update.md), [Page source structure and runtime](../development/page-source-structure-and-runtime.md) |
| A Page that runs as a server does not start, or the first display times out | The server does not listen on `process.env.PORT`; the first SSR response exceeds the limit of about 60 seconds; reserved keys (`PORT`, `NODE_ENV`, `PRS_*`, and so on) are used as environment variables; or paths used by the platform (`/api/v1/*`, `/api/storage`, and so on) conflict. | Read the listening port from `process.env.PORT` and make the first response light. Avoid reserved keys and platform paths. | [Page server processing, environment variables, and Secret references](../development/page-server-env-and-secrets.md), [Page specifications and limits](../build/page-specs-and-limits.md) |
| Deploying with the CLI created a new Page instead of updating (the ID and URL changed) | A build output folder such as `./dist` was deployed without `--page-id`, and `.jai-studio/page.json`, which stored the Page ID, was erased by a rebuild. | Always specify `--page-id` in update scripts. Discuss with the administrator responsible what to do with the unneeded new Page. | [Preview a Page locally and deploy it](../development/local-preview-and-deploy-page.md) |
| Registration succeeds but the screen is blank, asset loading fails, or an old display remains after an update | Distribution artifact paths, missing required files, or dependence on development-server-specific transformation, proxy, or environment variables. | In the browser network view, check the failed URL and Content-Type. Include the full source in the new ZIP, and check that asset references resolve as paths relative to the Page base URL. Old files not included in the new ZIP do not remain. | [Page source structure and runtime](../development/page-source-structure-and-runtime.md), [Preview a Page locally and deploy it](../development/local-preview-and-deploy-page.md) |
| Direct access to or reload of a deep URL returns 404 or a blank screen | For Pages that run as Node.js/Bun servers, the response to deep paths is determined by your server's routing (static Pages have an SPA fallback, and `<base href>` is inserted automatically). An incorrect build tool base path, or a public URL assembled as `/app/...`. | In addition to navigation from the Page entry point, test opening a deep URL in another tab, reloading it, and returning after sign-in. For "Sign-in optional" and "Anonymous only" users, use the `/p/{token}` public URL shown in "Access Settings"; do not assemble `/app/...` URLs. | [Page navigation and deep links](../development/page-navigation-and-deep-links.md) |
| Opening a Page returns to the authentication screen or repeats 401 | The access mode, Page session, user permissions, or external customer authentication configuration do not match. | Check "Access Settings" and intended users, then reopen through the correct entry point. Do not work around this by adding a Project API key, `Authorization`, or Cookie to a browser Page-relative API, and do not resend 401 requests unconditionally. | [Hosted Page authentication and API routes](../page-runtime/hosted-page-auth-and-api-routes.md), [Page navigation and deep links](../development/page-navigation-and-deep-links.md) |
| Only processing that uses a Secret fails, or a required value is missing | The target Project lacks the Secret; the reference format or environment variable name is invalid; a browser-public variable name is used; or the value is used from processing that runs only in the browser. An App installation or update may also require Project Secret configuration. | Do not write actual values in the ZIP or `.env`; check that a value for the target Project exists in "Project Secrets." In the root `.env`, reference `${secret:Secret name}`; do not use it on a left-hand side beginning with `NEXT_PUBLIC_`, `VITE_`, `REACT_APP_`, or `PUBLIC_`. For an App, check the setup owner for the installing Project and the required Secret name. | [Page server processing, environment variables, and Secret references](../development/page-server-env-and-secrets.md) |

## Flow by stage

1. **Local build stage**: Run install and `build` using only the source that goes in the ZIP, and check the initial display, navigation, and representative actions on the build result rather than on the development server. Studio also runs install and `build` with the runtime selected by the lockfile.
2. **ZIP registration stage**: Extract the ZIP and check that `package.json` and a lockfile (or `index.html` for a static Page) are at the root. A single parent folder is removed, but `node_modules`, `.git`, and top-level `dist`, `build`, and similar items are excluded from registration. Resolve on-screen limits and structure errors first.
3. **Build stage**: After upload, the Page runtime runs install and `build`. Opening the Page shows a build-in-progress screen while building and a build-failed screen on failure. The first display can take up to 15 minutes.
4. **Version application stage**: "Page uploaded successfully." alone does not confirm operation. Compare "Applied" in "Version History" with the Page actually opened.
5. **Start and display stage**: Open the Page using the same entry point and access mode as users, and check that HTML, JavaScript, CSS, and images can load. A Page that runs as a server must listen on `process.env.PORT` and return its first response within about 60 seconds. Record failed requests and URLs.
6. **Authentication and business-processing stage**: Test signed-in and signed-out states, and if needed the state after external customer authentication. Do not rerun data updates or sending when it is unknown whether processing has completed.

## Common misunderstandings

- **You should ZIP the built output (such as `dist`)**: It is the opposite. Studio runs install and `build` on the uploaded source. Top-level `dist` and `build` are excluded, and Next.js build output is rejected. ZIP the full source.
- **If the screen opens once, publishing is complete**: Users' access conditions, deep URLs, authentication return, data retrieval and updates, and external sending must be checked separately.
- **Sharing a public URL resolves authentication or permission errors**: A public URL is an entry point; it does not increase permissions for data actions or APIs in the Page. Check displayed information and actions before loosening access settings.
- **Put a Secret in a client environment variable**: A value that reaches the browser is not a Secret. Handle processes that use confidential values, such as external APIs, only in the Page's server processing.

## Before contacting an administrator or support

See [Collect the Information Needed for an Inquiry](collect-info-for-support.md), and collect the organization, Project, and Page name; Page ID (check it in the Page URL); Applied version; ZIP file list and size; access mode; failure stage (including whether the build-in-progress or build-failed screen appeared); occurrence time; displayed message; failed request URL and HTTP status; reproduction steps; and attempted mitigations. Do not include API keys, Cookies, authentication headers, Secret values, tokens, or personal information in the ZIP, logs, screenshots, or inquiry.

## Next articles

- [Build, start, and update Pages](../development/page-build-start-update.md)
- [Page specifications and limits](../build/page-specs-and-limits.md)
- [Page source structure and runtime](../development/page-source-structure-and-runtime.md)
- [Page navigation and deep links](../development/page-navigation-and-deep-links.md)
- [Page server processing, environment variables, and Secret references](../development/page-server-env-and-secrets.md)
