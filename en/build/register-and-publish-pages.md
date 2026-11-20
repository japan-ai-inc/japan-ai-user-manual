# Register and Publish a Page from the Screen

Register the source of a created Page as a ZIP file, then check the Page list, screen display, and access settings to make it available to users. This article explains the flow: **prepare the source → register it as a Page → check the display → save access settings as needed → check from the user entry point**.

Put the **Page source**, not finished build output, in the ZIP. After registration, Studio selects Bun, Node.js, or static serving based on the lockfile, installs dependencies, runs the build, and starts the Page.

When you ask AI to edit an existing Page, the edit is applied immediately as a new version and published to users. For AI creation, modification, and preview checks, see [Ask AI to Create and Edit a Page](ask-ai-to-build-pages.md).

## Related articles

- [Ask AI to Create and Edit a Page](ask-ai-to-build-pages.md)
- [Manage Page Versions](manage-page-versions.md)
- [Page Specifications and Limits](page-specs-and-limits.md)
- [Preview a Page Locally and Deploy It](../development/local-preview-and-deploy-page.md)
- [A Page Fails to Build, Start, or Display](../troubleshooting/page-build-start-display-failures.md)

## Before you begin

- Check the target organization and Project, the purpose and users of the Page to register, and when to publish it.
- Prepare the Page source as a ZIP file. Place `package.json` and a lockfile (or `index.html` for a static Page) at the root of the ZIP. A ZIP with neither, or a ZIP of Next.js build output, is rejected. For details such as source structure, runtime conditions, and capacity, see [Page Specifications and Limits](page-specs-and-limits.md).
- Confirm that you have permission to register and edit Pages in the target Project. Depending on permissions and organization feature settings, you may not be able to select "Upload ZIP" or a card menu. The number of Pages has an organization-level limit, which you can check in the usage bar on the Page list screen.
- Decide the test data for display checks, operations to check, and contact point if an issue occurs. For screens that create, update, or send externally, also check the impact of execution.
- Check whether a Page with the same name exists and whether this is an update or new registration. For the same name, a confirmation screen appears to apply it as a new version of the existing Page rather than create a new Page.

> Registering a ZIP alone does not necessarily mean that business processing within the screen, data retrieval, and external connections work correctly. Separately check that the Page opens and that users' operations work.

## 1. Check before registration

Before registration, check the source and impact after publishing.

| Item to check | What to check |
|---|---|
| Target | Correct organization, Project, and Page name; new or existing Page update |
| Source | Is the ZIP to register the intended version? Does it have `package.json` and a lockfile, or `index.html`, at the root? Does it contain only verified content? |
| Display name | Can users distinguish it in the Page list? |
| Operation | Have you checked the operations provided this time, such as display, input, search, creation, update, and sending? |
| Data and connections | Do you understand operations that affect production data or external services, and the required permissions and connections? |
| Provision scope | Have you decided whether sign-in is required, whom to notify, and conditions for sharing a URL? |

Do not include confidential information directly in the source, initial screen values, or settings accessible in a browser. For secret values used on the server side, register them as Project Secrets and write references as `${secret:secret_name}` in the root `.env` (a directly uploaded Page reads only `.env` and `.env.production`). Check not only the scope in which the Page is shared, but also data displayed and operated through the screen and permissions for external connections.

> Registering to a Page with the same name affects the existing Page content. Before selecting "Upload new version" on the confirmation screen, reconcile the target Page name and changes with relevant parties.

## 2. Register the ZIP file

1. Open the target Project and then "Pages." If "Upload ZIP" does not appear, check permissions or availability conditions.
2. Select "Upload ZIP."
3. On the "Upload Page" screen, select or drop the ZIP file you prepared.
4. Check the selected filename and select "Upload."
5. While "Uploading file" appears, wait without closing the screen. Closing it cancels the upload.
6. If a Page with the same name is found, check the displayed explanation. Select "Upload new version" only if you may update the existing Page.
7. Confirm that "Upload completed." appears.

After registration, Studio starts installing and building. When you open the Page, a build-in-progress screen appears, and it can take several minutes (up to about 15 minutes) until the first display. If the build fails, a build-failure screen appears.

Refresh the Page list and confirm that the target Page card appears. The card may display the Page name, last update date, and applied version information. Use the displayed information as a clue to confirm that you are not confusing the update target.

### If you cannot register

| Display or situation | Check and response |
|---|---|
| "Please select a ZIP file." | Select the ZIP file again. |
| "The ZIP file did not contain any files." | The ZIP is empty. Recreate the ZIP with the source in it. |
| A message that the ZIP structure is invalid, or another error about structure or extracted content | Check whether all files are in excluded folders (such as `dist` or `build`), whether the root has neither `package.json` nor `index.html`, or whether you uploaded build output, then prepare the source ZIP again. For detailed conditions, see [Page Specifications and Limits](page-specs-and-limits.md). |
| "Another upload is in progress." | Wait until the operation for the same Page is complete, then try again. |
| "Upload failed. Please try again." | Do not immediately repeat it. Record the target Page, file, and displayed message before retrying. If it repeats, see [A Page Fails to Build, Start, or Display](../troubleshooting/page-build-start-display-failures.md). |
| "You do not have permission to upload pages" | Check required permissions with the Project or Page administrator. |

## 3. Open and check the Page after registration

1. On the Page card, check the registered Page's name and last update date.
2. Select the Page card itself to open it. The card menu is separate from opening it.
3. After the build-in-progress screen or loading display, confirm that the screen appears. If a build-failure screen appears, see [A Page Fails to Build, Start, or Display](../troubleshooting/page-build-start-display-failures.md). If an error appears, before using "Retry" on the screen, check whether business processing has already run.
4. Including when "Preview" is available, use test data to check representative operations users perform.
5. For a Page that creates, updates, or sends, check results at the storage or sending destination as well as the completion display on the screen.

Being able to open the screen does not guarantee that all required data, connections, and operation permissions are available. For usage checks, see [Move Your Work Forward from a Page](../use/work-with-pages.md).

> "Retry" for a display error and rerunning a Page operation such as "Create," "Send," or "Run" are separate actions. If you do not know whether processing is complete, do not run the same business processing again.

## 4. Check the Page name and access settings

You can check and change the display name and access conditions from the Page card menu. These are settings separate from source registration.

### Set the Page name and thumbnail

1. Open the target Page card menu and select "Edit."
2. Check "Page Name" and use a name users can identify.
3. As needed, set a thumbnail with the icon or "Upload Image."
4. Check the content, save it, and confirm that "Saved" appears.

Changing the Page name or thumbnail does not replace checking the Page source content or business processing within the screen. Open and check the target Page after changing it as well.

### Configure conditions for users to open it

1. Open the target Page card menu and select "Access Settings."
2. In "Access Mode," select the conditions users need. Options include "Sign-in required," "Sign-in optional," and "Anonymous only."
3. If "External customer auth" appears depending on organization settings and Page status, select the required external connection. You cannot save if no connection can be selected or it is disabled for the organization.
4. Select "Save" and confirm that "Access settings saved." appears.
5. If a "Public URL" or "Access URL" appears, recheck the target Page and access conditions before sharing it, and notify only those who need it. A public URL has the form `/p/{token}`. Links built by hand as `/app/...` do not work correctly, so use the URL shown on the screen as is.

| Access mode | Screen explanation | Check before providing |
|---|---|---|
| Sign-in required | Requires sign-in and Page access permission. | Confirm that target users have the required account and permission. |
| Sign-in optional | Anyone can open the Page, and signed-in users can use it as they are. | Also check display and operations when not signed in. |
| Anonymous only | Lets anyone open the Page without requiring sign-in. | Carefully check users who know the URL, displayed data, and operations that can run. |
| External customer auth | Authenticates visitors with a configured customer ID provider. | Only when this option appears, check display after authentication and the user URL. |

> Selecting "Sign-in optional" or "Anonymous only" lets users who are not signed in to Studio open the Page from its URL. Before publishing, always check information displayed on the screen, operations that can run, and required external connections.

## 5. Check published status from the user entry point

After saving settings, check not only from the creator's screen but also from the actual entry point you will provide.

1. Open the target Page from the Page list or shared URL.
2. Confirm that it opens with the expected access conditions. If you need to check with another user, ask a verification person with the necessary permission.
3. Reconcile the Page name, screen display, representative business operations, and where results are checked.
4. Tell users the Page name, usage purpose, URL or entry point, start date, and inquiry contact.
5. Record the registered content and verification results in case an update switch or restoration is needed later. [Manage Page Versions](manage-page-versions.md) covers detailed version management.

## Checklist

- [ ] Checked the target organization, Project, and Page name, and whether it is a new registration or update
- [ ] Prepared a source ZIP with `package.json` and a lockfile (or `index.html`) at the root, and confirmed that it contains no confidential information
- [ ] Registered the ZIP from "Upload ZIP" and checked the completion display and the display after the build
- [ ] For a same-name Page, checked the target and impact before adding a new version
- [ ] Checked the Page card and the actual open screen
- [ ] Checked representative operations and results of creation, update, and sending
- [ ] Used "Edit" to set the Page name and thumbnail as needed
- [ ] Saved the access mode users need in "Access Settings"
- [ ] When a shared URL appears, checked the sharing scope and information and processing in the Page
- [ ] Opened it from an actual user entry point and prepared guidance and an inquiry contact

## Next article to read

- [Move Your Work Forward from a Page](../use/work-with-pages.md)
- [Manage Page Versions](manage-page-versions.md)
- [Page Specifications and Limits](page-specs-and-limits.md)
- [Preview a Page Locally and Deploy It](../development/local-preview-and-deploy-page.md)
- [A Page Fails to Build, Start, or Display](../troubleshooting/page-build-start-display-failures.md)
