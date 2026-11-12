# Page Specifications and Limits

A Page is a web screen used within a Project. Creators and developers register screen files that run in a browser as a ZIP, then confirm that it can be opened as a Page after registration, meets access conditions, and produces correct business-processing results. Acceptance of the ZIP or a screen displaying once does not necessarily mean that data integration, authentication, and external sending can also be used correctly.

For screen operations to register and publish, see [Register and Publish a Page from the Screen](register-and-publish-pages.md). For procedures to create and apply versions, see [Manage Page Versions](manage-page-versions.md).

## Related articles

- [Register and Publish a Page from the Screen](register-and-publish-pages.md)
- [Manage Page Versions](manage-page-versions.md)
- [Page Source Structure and Runtime](../development/page-source-structure-and-runtime.md)
- [Page Server Processing, Environment Variables, and Secret References](../development/page-server-env-and-secrets.md)
- [Register, Update, and Delete Project Secrets](../operations/manage-project-secrets.md)
- [Index of Limits and Availability Conditions](../operations/limits-and-availability-index.md)
- [A Page Fails to Build, Start, or Display](../troubleshooting/page-build-start-display-failures.md)

## Scope of this article

This article summarizes conditions checked at registration, access modes, versions, and confirmed limits for Pages registered as ZIPs from Studio. It does not cover an individual Page's screen design, data operations, external-service connection settings, or how to create server processing.

Availability conditions vary by organization feature settings, permissions, and Page type. This article does not state that unlisted package management, particular frameworks, build commands, execution time, or traffic limits are available or unavailable.

## Specifications

| Item | Specification | Notes |
|---|---|---|
| Registration format | With "Upload ZIP," select one ZIP file. Files other than ZIP files and multiple selections are not accepted. | The filename extension and ZIP format are checked. Before registration, package only the deliverables to distribute in the ZIP. |
| Files in the ZIP | Ordinary files in the ZIP are handled as Page files. A ZIP whose paths include elements representing the current or parent directory causes a structure error. | If a ZIP has only one parent folder with all files under it, it is handled without that parent folder. Do not add unnecessary parent folders. |
| Content excluded from registration | Paths containing `__MACOSX`, `node_modules`, `.git`, `.DS_Store`, `.jai-studio`, `__staging`, `__upload-lock.json`, `__backup`, or `__versions` are excluded. In addition, `.next`, `dist`, `build`, `out`, `.cache`, `coverage`, `.turbo`, `.vercel`, `tmp`, and `temp` at the top level of the ZIP (or immediately below a single parent folder in a ZIP wrapped by one) are excluded. | Do not structure distribution content to depend on excluded names. After registration, always open the actual Page and confirm that required HTML, JavaScript, CSS, images, and so on can be read. Do not treat temporary files such as settings and caches generated in the Page source area as deliverables to include in the ZIP. |
| Empty or invalid ZIP | A ZIP cannot be registered if it is empty or has no files that can be registered after exclusions. | Check "The ZIP file did not contain any files." or the message about its structure, and recreate the deliverable. |
| Page name | When registering a new Page, its name is based on the ZIP filename without the extension. | If one Page with the same name is found and you have update permission, it is registered as "Upload new version" after confirmation. Changing the display name does not change registered file content. |
| Execution and build | Registering a ZIP with Studio expands the ZIP and places files. The registration screen has no confirmed option to specify arbitrary dependency installation or an arbitrary build command. | Create required deliverables locally before registration. A Page rebuild is requested after applying a new version, but a completion display alone does not guarantee that the screen or business processing is normal. |
| Browser-run portion | Screen files included in the ZIP are handled on the assumption that they reach users' browsers. | Do not include passwords, API keys, tokens, or Project Secret values in HTML, JavaScript, configuration files, or initial display data. This applies regardless of visibility. |
| Project Secret | A Project Secret is a confidential value managed per Project. Separate permissions are required to create, view, update, and delete it, and it can be managed only in organizations where the feature is enabled. Values are stored encrypted, and masked values rather than actual values are returned in the management-screen list and details. | The normal Page registration process for uploading a ZIP has no input for a Secret name or value. **Do not return a Secret value to the browser, display it on the screen, or put it in browser-side variables, logs, or distributed files.** Safe reference methods from a Page, passing to server processing, and required settings are outside this article's scope. Check [Page Server Processing, Environment Variables, and Secret References](../development/page-server-env-and-secrets.md) and [Register, Update, and Delete Project Secrets](../operations/manage-project-secrets.md). |
| Access modes | In "Access Settings," you can select "Sign-in required," "Sign-in optional," or "Anonymous only." "External customer auth" may also appear depending on organization settings and Page status. | "Sign-in required" requires sign-in and Page access permission. "Sign-in optional" and "Anonymous only" allow anyone to open the Page, so check displayed data and operations that can run individually. You can select external customer auth only when using a configured customer ID provider. Configure at least one connection and up to the maximum shown on the screen. |
| Shared URL | A public URL may appear for "Sign-in optional" and "Anonymous only." An access URL may appear for external customer auth. | Before providing it, check users, displayed information, and execution permissions on the assumption that anyone who knows the URL can reach it. Sharing a URL does not make data operations within the Page safe. |
| Versions | The first version is created when registering a new Page. Registering a new ZIP to an existing Page switches the current Page content to the new version. You can apply a past version again with "Restore and apply." | The current Page always refers to one "Applied" version. Editing its name or description does not switch content. For detailed procedures, see [Manage Page Versions](manage-page-versions.md). |

## Limits and limit values

The following table contains only values confirmed in registration processing or screen displays. The number of Pages allowed per contract or organization is evaluated as a feature limit, but because a uniform numeric value could not be confirmed at this time, it is not included in the table.

| Target | Limit or limit value | Conditions |
|---|---:|---|
| ZIP file size | 100 MB or less | Applies to the compressed ZIP for both "Upload ZIP" and "Upload New Version." A ZIP over 100 MB cannot be registered. |
| ZIP entry count | 5,000 or less | Total files and folders. Checked both against the ZIP central-directory count and during extraction. |
| Total extracted size | 200 MB or less | Total files expanded for registration. Checked separately from compressed ZIP size. |
| ZIPs selectable at the same time | 1 | Number of files selectable on the upload screen. You cannot register multiple ZIPs together. |
| Project Secret name | 64 characters or less | It must start with a lowercase English letter; subsequent characters can be lowercase English letters, numbers, or underscores. Empty strings and UUID-formatted names are not accepted. |
| Project Secret value | 8,192 characters or less | Empty values cannot be registered or updated. Values are masked in the screen list and details. |

## Common misconceptions

### Uploading a ZIP makes Studio build it in any development environment

The confirmed registration processing checks ZIP size, structure, and extracted contents, then places files. No feature to choose arbitrary package managers, language runtimes, or build commands could be confirmed. Create the required deliverables before registering and verify them by actually opening the Page.

### Sign-in optional and anonymous access are the same

Both are options that let anyone open a Page, but their screen descriptions differ. With sign-in optional, signed-in users use the Page as they are; anonymous access does not require sign-in. Before sharing a public URL in either case, check information displayed and operations that can run in the Page.

### A Secret may be kept on the client side if it has a disguised variable name

Even if you change a variable name, a value sent to a browser can potentially be obtained by users or developer tools. You must not include Secret values in distributed content, browser configuration, screens, or logs. Masking values in the Project Secret management screen and not exposing values on the Page are separate checks.

### You can notify users immediately after uploading a new version

A rebuild is requested after upload, but successful display, authentication, data retrieval, updates, and external sending have not yet been confirmed. After checking the applied version, verify representative operations and results from the expected permission and user entry point.

## If you cannot confirm something

This article does not cover the complete requirements for Page code that does not pass Secret values to a browser, the complete server runtime, available languages, frameworks, package managers, and build commands, or uniform limits for execution time, traffic, and Pages per organization. Do not treat these as specifications of this article.

Do not guess and recreate the ZIP or loosen publishing settings in the following situations.

- An error appears regarding ZIP structure, capacity, entry count, or extracted size.
- After registration, a blank screen, loading error, or required-file loading failure occurs.
- Access-mode options, public URLs, or external customer auth do not appear.
- You do not know how to configure processing that needs a Secret safely.
- You cannot determine the applied version, Page name, target Project, or execution result.

Record the target organization, Project, Page name, ZIP filename, occurrence date and time, displayed message, reproduction steps, and confirmed operations. Do not attach Secret values, passwords, API keys, tokens, or credentials to an inquiry. For build and display issues, see [A Page Fails to Build, Start, or Display](../troubleshooting/page-build-start-display-failures.md); for Secret configuration, see [Register, Update, and Delete Project Secrets](../operations/manage-project-secrets.md).

## Next article to read

- [Register and Publish a Page from the Screen](register-and-publish-pages.md)
- [Manage Page Versions](manage-page-versions.md)
- [Page Source Structure and Runtime](../development/page-source-structure-and-runtime.md)
- [Page Server Processing, Environment Variables, and Secret References](../development/page-server-env-and-secrets.md)
- [Register, Update, and Delete Project Secrets](../operations/manage-project-secrets.md)
- [A Page Fails to Build, Start, or Display](../troubleshooting/page-build-start-display-failures.md)
