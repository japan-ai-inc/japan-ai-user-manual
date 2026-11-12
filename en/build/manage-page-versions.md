# Manage Page Versions

When updating a Page, distinguish between the content provided to users and the versions retained in the history. This article explains how to check a Page's **Applied** version, apply a new version, switch to a past version with "Restore and apply," and check the result after switching.

Version operations do more than change the reference to the version displayed. Uploading a new version or restoring a past version applies the selected version's files as the current Page content. Treat this as a change that affects users: check beforehand and verify operation after applying it.

## Related articles

- [Register and Publish a Page from the Screen](register-and-publish-pages.md)
- [Page Build, Start, and Update](../development/page-build-start-update.md)
- [Page Specifications and Limits](page-specs-and-limits.md)

## Versions and applied status

A Page can retain multiple versions, one of which is treated as "Applied." The Page card may show the applied version's name, creation date and time, or version number as `Ver.`. In "Version History," the applied version is marked "Applied."

| Item | What you can check | How it is handled during operations |
|---|---|---|
| Applied version | The version used as the current Page content | Cannot be selected for "Restore and apply" |
| Version History | Version name, creation date and time, description, and uploader | You can select a past version and apply it |
| New version | A version created from a new ZIP package | Replaces the current Page content after upload |
| Version information | The version-specific "Name" and "Description" | You can update it. Changing only the name or description does not switch the applied version |

> **The version with the larger number and the "Applied" version are not necessarily the same.** After you restore and apply a past version, versions with larger numbers may remain in the history. Do not judge the content to provide to users by the number alone; check the "Applied" label and the actual Page.

## Before you begin

- Confirm that the organization, Project, and Page are correct.
- Confirm that you have permission to update the Page. Depending on permissions and feature settings, menus or operations may not be displayed or selectable.
- Record the currently "Applied" version, the purpose of the change, and the candidate version to switch to. For a Page in use, also decide the switch time and the person responsible for checking it.
- When applying a new version, prepare the ZIP to upload and representative screens, inputs, and business operations to check in the Page. For source preparation, see [Page Build, Start, and Update](../development/page-build-start-update.md).
- For Pages that send externally or create, update, or delete data, confirm the scope of impact during verification with relevant parties.

> Switching versions does not change access settings. Also check who can open the Page and whether an external URL can be provided, together with registration, publishing, and access settings. For details, see [Register and Publish a Page from the Screen](register-and-publish-pages.md).

## 1. Check the currently applied version

1. Open the list or card that contains the target Page.
2. Confirm that the Page name and Project match the target.
3. If `Ver.` appears on the Page card, record the displayed value. A named version may display its name; an unnamed version may display its creation date or version number.
4. Select "Version History" from the Page menu.
5. Check the version marked "Applied" in the history.
6. Check the version name, description, creation date and time, and uploader, and confirm that it is not being confused with the version for this switch.

A version with no custom name in the history is displayed with its date and time as the heading. To make changes easier to identify, we recommend giving it a name and description that indicate, for example, that it was tested or approved, and recording the purpose of the change and verification result separately.

## 2. Upload and apply a new version

Use this procedure to create a version from a new ZIP and apply its content to the current Page.

1. Select "Upload New Version" from the target Page menu.
2. Select one ZIP file that you created.
3. Check the selected file name and size.
4. Select "Upload."
5. When the completion message appears, refresh the list and check `Ver.` on the Page card and "Applied" in "Version History."
6. Open the Page with "Preview" or the actual user entry point, and check the prepared screens, operations, and results.

While uploading, do not close the dialog or repeat the same update; wait for completion or an error message. Files other than ZIP files, simultaneous selection of multiple files, and files exceeding the limit shown on the screen are not accepted.

> **Do not omit business-use verification just because an upload-complete message appears.** Applying a new version starts a Page rebuild, so check not only that the screen opens, but also representative operations and results before notifying users. [Page Build, Start, and Update](../development/page-build-start-update.md) covers build and start details.

## 3. Restore and apply a past version

If you want to undo the immediately preceding change or return to a known state, select the target version from the history.

1. Select "Version History" from the target Page menu.
2. For a version that is not "Applied," check its name, description, date and time, and uploader.
3. Open the target version's menu and select "Restore and apply."
4. Confirm that "Switched" appears.
5. Reopen the history and confirm that the selected version is marked "Applied."
6. Open the Page and confirm that the display, operations, and business results you intended to restore have been restored.

"Restore and apply" applies a past version's files as the current Page content and switches the applied version. **It does not create a new version number for restoration.** Applying a past version also does not automatically delete versions uploaded later from the history.

> If an error appears while switching or you cannot confirm the completion message, do not immediately repeat the same operation. Check the "Applied" label in the history and the actual Page. If processing may still be in progress, wait and check again.

## 4. Update names and descriptions, and delete versions

For each version in the history, select "Edit version info" from the menu to update its "Name" and "Description." This information identifies the version; editing it does not switch the Page content.

You can delete an unnecessary version with "Delete." However, once deleted, that version cannot be restored. **You cannot delete the "Applied" version.** ("You cannot delete the active version." appears.) Even if another version exists, it is not switched automatically. First switch to another version with "Restore and apply," confirm "Applied" in the history and the operation of the actual Page, and then delete the unnecessary past version.

| Operation | Effect on Page content | Check before performing it |
|---|---|---|
| "Edit version info" | Does not change applied content | The name and description correspond to the target version |
| "Restore and apply" | Switches to the selected past version's content | You checked the impact on users, business operations, and external connections |
| "Delete" | Deletes a version other than the applied version from the history | You confirmed that it will not need to be restored later |

## 5. Check after switching

1. Confirm that `Ver.` on the Page card and "Applied" in "Version History" indicate the intended version.
2. Open the Page from the same entry point and with the same permissions as a user, and confirm that the screen displays.
3. Try representative viewing, input, search, and execution operations related to the change, and check the results.
4. If the Page sends to external services or creates, updates, or deletes data, check the results with test data or an approved method.
5. Share the switched version, date and time, reason for the change, verification results, and unverified items with relevant parties.

> If the Page screen does not display, unexpected content appears, or you cannot confirm business-operation results, do not perform additional updates or restorations until the cause is known. Record the currently "Applied" version, the immediately preceding operation, displayed messages, and reproduction steps, and contact the administrator.

## Checklist

- [ ] Confirmed the target organization, Project, and Page
- [ ] Recorded the pre-switch "Applied" version and purpose of the change
- [ ] Checked the new ZIP or the past version to restore
- [ ] Checked the target version's name, description, date and time, and uploader in "Version History"
- [ ] Confirmed that "Applied" changed to the intended version after upload or restoration
- [ ] Checked `Ver.` on the Page card and the actual Page
- [ ] Checked representative business operations and their results
- [ ] Checked the scope of impact when external connections or data updates are involved
- [ ] Shared the change and verification results with relevant parties

## Next article to read

- [Register and Publish a Page from the Screen](register-and-publish-pages.md)
- [Page Build, Start, and Update](../development/page-build-start-update.md)
- [Page Specifications and Limits](page-specs-and-limits.md)
