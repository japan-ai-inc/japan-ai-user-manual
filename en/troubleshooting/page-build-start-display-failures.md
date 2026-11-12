# A Page Fails to Build, Start, or Display

If a Page ZIP cannot be registered, does not start after registration, displays a blank screen, returns 404 at a deep URL, repeatedly authenticates, or a process using a Secret fails, check the failed stage separately. Registering a ZIP in Studio has not been confirmed as a feature that installs arbitrary dependencies or runs arbitrary build commands. Create completed browser-ready artifacts locally, then separately check registration, access, display, and business actions.

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
- Open the locally built artifact and check the initial display, asset loading, and representative changed action and result. It is not enough that it works on a development server.
- Extract the ZIP and check that the initial HTML and required JavaScript, CSS, and images remain in a structure eligible for registration. A single parent folder is removed, but a build output folder such as `dist` or `build` at the top level of the ZIP is excluded.
- Treat opening the Page, authenticating, and successfully performing data actions or external integrations as separate checks. Do not repeatedly upload, restore, or perform business processing before the cause is identified.

> Do not include API keys, Cookies, tokens, actual Project Secret values, or other secrets in HTML, JavaScript, configuration, source maps, or logs delivered to the browser. Variable names that conceal values and obfuscation are not protection.

## Isolate the issue by symptom

| Symptom or displayed message | Likely cause | What to check or do | Detailed article |
|---|---|---|---|
| "You don't have permission to upload pages" | You do not have permission to update the Page or Project. | Check the correct organization, Project, and target Page, and ask the administrator responsible to confirm the required permissions. Do not change publishing settings merely to make the action appear. | [Register and publish Pages from the screen](../build/register-and-publish-pages.md), [Build, start, and update Pages](../development/page-build-start-update.md) |
| "The ZIP file did not contain any files.", a structure error, or the ZIP is not accepted | There is no artifact, paths contain current or parent directory components, files exist only under excluded items, or a limit is exceeded. | Extract the ZIP and check whether normal files remain and whether HTML and assets can be referenced from the root. Check ZIP structure and the limits shown on screen in [Page specifications and limits](../build/page-specs-and-limits.md). | [Page specifications and limits](../build/page-specs-and-limits.md), [Page source structure and runtime](../development/page-source-structure-and-runtime.md) |
| "Upload failed. Please try again." or "Another upload is already in progress. Please try again shortly." | A communication or registration process failed, processing for the same Page is in progress, or there is an issue with the target or ZIP. | Do not retry immediately; record the target Page, ZIP name, and displayed message. After some time, check "Applied" in "Version History" and the actual Page, then retry only once if necessary. | [Register and publish Pages from the screen](../build/register-and-publish-pages.md), [Manage Page versions](../build/manage-page-versions.md) |
| "Failed to switch version" occurs after upload completes, or the expected version is not used | Application or switching did not complete, the target Page is incorrect, or processing is in progress. | In "Version History," check whether the intended version is "Applied," as well as its time, name, and uploader. While the cause is unknown, do not perform additional uploads or restores. | [Build, start, and update Pages](../development/page-build-start-update.md), [Manage Page versions](../build/manage-page-versions.md) |
| Registration succeeds but the screen is blank, asset loading fails, or an old display remains after an update | Distribution artifact paths, missing required files, or dependence on development-server-specific transformation, proxy, or environment variables. | In the browser network view, check the failed URL and Content-Type. Include all HTML, JavaScript, CSS, and images in the new ZIP, and check whether they resolve with relative paths. Review on the assumption that old files not included in the new artifact do not remain. | [Page source structure and runtime](../development/page-source-structure-and-runtime.md), [Preview a Page locally and deploy it](../development/local-preview-and-deploy-page.md) |
| Direct access to or reload of a deep URL returns 404 or a blank screen | It depends on SPA fallback on the Page host, the build tool base path, or routing configuration. | In addition to navigation from the Page entry point, test opening a deep URL in another tab, reloading it, and returning after sign-in. Whether SPA fallback is available cannot be confirmed uniformly, so do not publish based on assumptions. | [Page navigation and deep links](../development/page-navigation-and-deep-links.md) |
| Opening a Page returns to the authentication screen or repeats 401 | The access mode, Page session, user permissions, or external customer authentication configuration do not match. | Check "Access Settings" and intended users, then reopen through the correct entry point. Do not work around this by adding a Project API key, `Authorization`, or Cookie to a browser Page-relative API, and do not resend 401 requests unconditionally. | [Hosted Page authentication and API routes](../page-runtime/hosted-page-auth-and-api-routes.md), [Page navigation and deep links](../development/page-navigation-and-deep-links.md) |
| Only processing that uses a Secret fails, or a required value is missing | The target Project lacks the Secret; the reference format or environment variable name is invalid; a browser-public variable name is used; or the server execution surface has not been confirmed. An App installation or update may also require Project Secret configuration. | Do not write actual values in the ZIP or `.env`; check that a value for the target Project exists in "Project Secrets." In the root `.env`, reference `${secret:Secret name}`; do not use it on a left-hand side beginning with `NEXT_PUBLIC_`, `VITE_`, `REACT_APP_`, or `PUBLIC_`. For an App, check the setup owner for the installing Project and the required Secret name. | [Page server processing, environment variables, and Secret references](../development/page-server-env-and-secrets.md) |

## Flow by stage

1. **Local build stage**: After the build defined by the project, check the initial display, navigation, and representative actions on the built artifact, rather than on the development server. Do not assume Studio runs arbitrary frameworks, server runtimes, or build commands.
2. **ZIP registration stage**: Extract the ZIP and check that artifacts remain eligible for registration. A single parent folder is removed, but `node_modules`, `.git`, and top-level `dist`, `build`, and similar items are excluded from registration. Resolve on-screen limits and structure errors first.
3. **Version application stage**: "Page uploaded successfully." alone does not confirm operation. Compare "Applied" in "Version History" with the Page actually opened.
4. **Start and display stage**: Open the Page using the same entry point and access mode as users, and check that HTML, JavaScript, CSS, and images can load. Record failed requests and URLs.
5. **Authentication and business-processing stage**: Test signed-in and signed-out states, and if needed the state after external customer authentication. Do not rerun data updates or sending when it is unknown whether processing has completed.

## Common misunderstandings

- **Registering a ZIP makes Studio build the source**: The registration processing that can be confirmed is ZIP inspection and placement. Do not assume dependency installation or execution of `package.json` scripts; register completed artifacts.
- **If the screen opens once, publishing is complete**: Users' access conditions, deep URLs, authentication return, data retrieval and updates, and external sending must be checked separately.
- **Sharing a public URL resolves authentication or permission errors**: A public URL is an entry point; it does not increase permissions for data actions or APIs in the Page. Check displayed information and actions before loosening access settings.
- **Put a Secret in a client environment variable**: A value that reaches the browser is not a Secret. Handle processes that use confidential values, such as external APIs, only on an available server execution surface.

## Before contacting an administrator or support

See [Collect the Information Needed for an Inquiry](collect-info-for-support.md), and collect the organization, Project, and Page name; Page ID; Applied version; ZIP file list and size; access mode; failure stage; occurrence time; displayed message; failed request URL and HTTP status; reproduction steps; and attempted mitigations. Detailed Page build/deployment state and user-facing build logs are not displayed on the screens checked. Do not include API keys, Cookies, authentication headers, Secret values, tokens, or personal information in the ZIP, logs, screenshots, or inquiry.

## Next articles

- [Build, start, and update Pages](../development/page-build-start-update.md)
- [Page specifications and limits](../build/page-specs-and-limits.md)
- [Page source structure and runtime](../development/page-source-structure-and-runtime.md)
- [Page navigation and deep links](../development/page-navigation-and-deep-links.md)
- [Page server processing, environment variables, and Secret references](../development/page-server-env-and-secrets.md)
