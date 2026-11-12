# Page Build, Start, and Update

Developers updating a Page build browser-facing artifacts locally, package them as a ZIP, and upload them to the existing Page as a new version. Do not depend on building or dependency installation during registration; prepare completed artifacts. An upload-complete indication alone does not guarantee operation in users' environments.

This article explains updates to published Pages, the Applied version and rollback, the relationship to access settings, operation verification, and what you can check when builds or display fail. For initial registration and publication, see [Register and Publish a Page from the Screen](../build/register-and-publish-pages.md); for ZIP structure and limits, see [Page Specifications and Limits](../build/page-specs-and-limits.md).

## Related articles

- [Manage Page Versions](../build/manage-page-versions.md)
- [Page Specifications and Limits](../build/page-specs-and-limits.md)
- [Register and Publish a Page from the Screen](../build/register-and-publish-pages.md)
- [A Page Fails to Build, Start, or Display](../troubleshooting/page-build-start-display-failures.md)

## Before you begin

- Check the target organization, Project, Page name, and version currently "Applied." The `Ver.` label on a Page card helps with checking, but treat "Applied" in "Version History" as authoritative.
- Create artifacts locally and place only files distributed to browsers, such as HTML, JavaScript, CSS, and images, at the ZIP root. `node_modules` in the ZIP and root-level `dist`, `build`, and similar directories may be excluded from distribution.
- From changed screens, inputs, data retrieval, registration or update, and external transmission, decide representative operations to perform after publishing. Also check affected users and switching time with relevant parties.
- A user who can edit the target Page performs the update. If a menu or operation is not shown or cannot be selected, check the target Project, Page, and permissions.

> Do not include API keys, passwords, tokens, or Project Secret values in a ZIP distributed to browsers. Even when the screen is not published, values that reach users' browsers can be obtained.

## Check build and deployment status

A Page displays items such as "Upload New Version," "Applied," and "Failed to switch version." A screen for tracking Page-specific build and deployment statuses or logs in a list or history could not be confirmed.

Do not determine operation by status names alone immediately after upload; combine the following three checks.

| Target to check | How to check | What you can determine |
|---|---|---|
| Version application | In "Version History," check that the intended version is "Applied." | The currently referenced version is as intended |
| Start of delivery | Open the Page from the same entry point as users. | The process of opening the Page and the screen's initial display |
| Business operation | Run the representative changed operation and check the screen, saved result, and external processing. | Whether processing needed for actual use succeeds |

> An "Uploaded" or update-complete notification is not evidence that representative operations also succeeded. In particular, for Pages that include authentication, data updates, or external transmission, check under the applicable usage conditions before providing them.

## 1. Apply a new ZIP to the same Page

This operation does not create a new Page. Registering a ZIP from "Upload New Version" on the target Page creates a new version with the same Page ID, and its version number increases beyond the existing maximum. To avoid confusing it with a separate new upload that uses the same Page name, always start from the existing Page menu.

1. From the target Page menu, select "Upload New Version."
2. Select one completed ZIP and check its file name and size. The ZIP must be 100 MB or less.
3. Select "Upload." While processing, do not close the dialog or layer the same update; wait for completion or an error indication.
4. After completion, open the `Ver.` label on the Page card and "Version History." Check that a new history entry was added and the intended version is "Applied."
5. Open the Page and check the initial display, representative operations decided in advance, and their results.

During an update, the new artifacts are reflected in the distribution target, and prior files not included in the new artifacts are not retained. To avoid issues that appear to be references to old assets or caching, include every required HTML, JavaScript, CSS, image, and other file in the ZIP every time.

## 2. Check the Applied version and return if necessary

"Version History" displays a version's creation date and time, name, description, and uploader; the Applied version displays "Applied." The version with the highest number does not necessarily match the Applied version. After returning to a previous version, entries with newer version numbers remain.

1. From the target Page menu, open "Version History."
2. Check the "Applied" indication, version date and time, name, description, and uploader.
3. If there is a problem, select "Restore and apply" from the menu of the previous version to which you want to return.
4. When "Switched to ~" appears, reopen the history and check that the selected version is "Applied."
5. Open the Page again and check not only the display you wanted to restore, but also representative operations and results.

"Restore and apply" reapplies previous files as the current Page content and switches the Applied version. It is not a rollback that creates a new version number. Also, an Applied version cannot be deleted.

> If a switching or display problem occurs, do not repeatedly upload and restore while the cause is unconfirmed. Record the currently "Applied" version, immediately preceding operation, occurrence time, displayed message, and reproduction steps.

## 3. What to check when changing access settings

What you change in "Access Settings" is the authentication and publication condition for opening a Page. Changeable options include "Sign-in required," "Sign-in optional," and "Anonymous only"; depending on organization and Page conditions, "External customer auth" is also handled. Saving access settings does not create or switch a Page ID or version.

1. From the Page menu, open "Access Settings."
2. Check the "Access Mode" and target Page, then select the required setting.
3. After saving, check "Access settings saved."
4. In "Version History," check that the "Applied" version did not change unintentionally.
5. Under the changed access conditions, check that intended users can open the Page. Even when a Public URL is displayed, check the displayed data and executable operations before sharing it.

> Loosening access settings does not make data operations inside a Page safe. Before publishing with sign-in optional or anonymous access, separately check the information displayed on the screen and operations users can execute.

## Checklist for verifying operation

- [ ] The `Ver.` label on the Page card and "Applied" in "Version History" indicate the intended version
- [ ] I can open the Page through the same entry point and access settings as users
- [ ] Required HTML, JavaScript, CSS, and images load on initial display
- [ ] I checked representative changed operations, such as viewing, input, search, and execution, and their results
- [ ] If there are data updates or external transmissions, I checked their results using an approved verification method
- [ ] I shared the version, switching time, changes, verification results, and unconfirmed items with relevant parties

## If something does not work

### I want to see build-failure status or logs

A screen that displays detailed Page build/deployment status or build logs users can open could not be confirmed. Record errors displayed during upload or switching, browser network failures, target Page and Project, Applied version, ZIP name, occurrence time, and reproduction steps. Do not include secret values, authentication information, or personal information in ZIP files, screenshots, or inquiries.

If the Page does not open, displays a blank screen, or you cannot check operation after the update, extract the ZIP and check artifact placement and reference paths, then check [Page Specifications and Limits](../build/page-specs-and-limits.md). [A Page Fails to Build, Start, or Display](../troubleshooting/page-build-start-display-failures.md) covers failure isolation.

### Uploading or switching fails

You cannot register non-ZIP files, multiple files, or ZIP files over 100 MB. If a message indicates processing is ongoing, do not retry immediately; wait and check "Applied" in "Version History" and the actual Page. If an operation is not displayed, check the target organization, Project, Page, and edit permission. For entry count, expanded size, and ZIP structure, see [Page Specifications and Limits](../build/page-specs-and-limits.md).

## Read next

- [Manage Page Versions](../build/manage-page-versions.md)
- [Page Specifications and Limits](../build/page-specs-and-limits.md)
- [Register and Publish a Page from the Screen](../build/register-and-publish-pages.md)
- [A Page Fails to Build, Start, or Display](../troubleshooting/page-build-start-display-failures.md)
