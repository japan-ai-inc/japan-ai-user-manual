# Storage Specifications and Limits

Storage is where you save and reference files and folders used in a Project. This article summarizes specifications, limits, and operational constraints to check when handling files in the standard Storage screen. For upload steps, see [Use Storage files](../use/use-storage-files.md). For folder structure design, see [Organize your Storage folder structure](organize-storage-folders.md).

- [Use Storage files](../use/use-storage-files.md)
- [Storage API](../api/storage-api.md)
- [Hosted Page Storage API](../page-runtime/hosted-page-storage-api.md)

## Scope of this article

This article covers creating files and folders, uploading, searching, previewing, downloading, moving, renaming, and deleting on the standard "Storage" screen for a Project.

A Storage trigger uses changes in Storage, but it is a Workflow configuration feature. This article only summarizes changes that can trigger it and filter conditions. For setup, publishing, and test procedures, see [Configure Workflow triggers](configure-workflow-triggers.md).

Files managed by Apps, APIs, operations within Hosted Pages, and organization- or Project-specific permissions and storage capacity are out of scope. Displayed operations and available capacity vary by permissions, feature settings, and contract details.

## Standard feature specifications

| Item | Specification | Notes |
|---|---|---|
| Storage unit | Manage files and folders within a Project. Open a folder, then select "Create Folder" or "Upload File" at the current location. | The list displays items such as "File Name," "Uploaded By," "Modified," and "File Size." |
| Upload | You can select and upload multiple files. Some screens also let you upload files by dropping them. | After selecting files, select "Start Upload." Do not use the dialog closing as confirmation; check the "Pending," "Uploading," "Completed," or "Failed" status or the list. |
| Accepted formats | Default allowed formats include images, videos, audio, text, PDF, Word, Excel, PowerPoint, JSON, XML, and ZIP, RAR, 7z, TAR, and GZIP archives. | Allowed formats can be changed in environment settings. A format not listed here is not necessarily always rejected, and an extension listed here is not necessarily always accepted. Do not bypass this by changing only the extension. |
| Search | Use "Search by file name" to filter the list in the currently open location. | No search results do not necessarily mean the item does not exist anywhere in the Project. Check the current folder and search term. |
| Preview | "Preview" is available for supported image, PDF, and text formats. For unsupported formats, "This file type is not supported for preview" is displayed; download the file to check it. | Use a PDF preview to check the first page and a text preview to check part of the content. Whether a file can be previewed is separate from whether it can be uploaded. |
| Download | You can download individual files. You can use "Download as ZIP" for a folder or multiple selected items. | You cannot create a ZIP for an empty folder. After downloading, check the contents and scope. |
| Using paths | You can select "Copy path (for use with agents)" for a file or folder. | You can use the copied value to indicate the target to an Agent in the same Project. Knowing a path alone does not grant viewing permission. |
| Handling duplicate names | If a file with the same name exists in the same location, "File Already Exists" is displayed. | "Replace" replaces the existing file with the new file. "Keep Both" adds a number to the new file name. If the existing file is "Uploading," wait for it to finish. |
| Renaming | You can use "Rename" for files and folders. For files, change the name portion while retaining the extension. | If the same name exists, you must resolve the conflict. After changing the name or path, check Agents, Workflows, and Pages that reference it. |
| Moving | You can "Move" files and folders. If an item with the same name exists at the destination, select Replace or Keep Both. | You cannot select the same location as the destination. When operating on multiple items, only some may fail. |
| Deleting | You can "Delete" files. Deleting a folder also targets its files and folders. | The standard screen states, "This action cannot be undone," and does not provide a restore operation. Check child items and references before deleting. |
| Items created by others in the organization drive | In the organization drive, when a non-administrator attempts to delete a completed file created by another user, the screen prevents deletion. Server-side permission checks are also performed for each item when deleting folders. | Bulk deletion returns success or failure for each item, and items without permission may show "Permission denied." This article does not make a definitive statement about detailed permission conditions by creator or administrator for folders. |
| App-managed files | App-managed seed files displayed as "Installed app" are read-only. | They cannot be renamed, moved, or deleted through the standard operations. If a change is required, ask the responsible person about updating or uninstalling the App. |

## Limits and limit values

The following are only values confirmed in the implementation at the specified time. Storage capacity assigned to an organization or Project varies by contract and settings, so it is not listed as a fixed value.

| Item | Specification | Notes |
|---|---|---|
| Maximum size per file | The default is **2GB**. | It can be changed in environment settings. Whether you can actually upload also depends on this value, the remaining storage capacity configured for the organization, and permissions. |
| Name length | File and folder names must be **1–255 characters**, excluding leading and trailing spaces. | Spaces only, `/`, control characters, `.`, `..`, and reserved names cannot be used. Names are handled with leading and trailing spaces excluded. |
| Maximum size for preview | Up to **10MB**. | This is a preview generation limit, not an upload limit. Download files larger than 10MB to check them. |
| Text preview | Up to **100 lines** and **10,000 characters**. | This is the displayed range in previews of supported text formats. Download the file to check all content. |
| ZIP download for folders or multiple selections | The default total is up to **100MB**. | This applies to the total size of files under the folder or in the selection. It can be changed in environment settings. Empty folders cannot be downloaded. |
| Bulk deletion | Up to **100 items** per operation. | This is the combined number of selected files and folders. The result may be partial success and partial failure. |

File storage capacity and data storage capacity are determined by the organization-specific setting and current usage. Because a uniform capacity value could not be confirmed, check the displayed limit and remaining capacity.

MIME types supported for preview are JPEG, PNG, WebP, GIF, SVG, PDF, TXT, JSON, Markdown, CSV, and XML. Images and PDFs are checked as display images, and text is checked as text. Office documents, videos, audio, archives, and similar files are not preview targets even if they are normally uploadable.

## Changes that can be targeted by Storage triggers

For a Storage trigger, specify "Target Folder" and "Include subfolders," then select one of the following "Event Type" options.

| Item | Specification | Notes |
|---|---|---|
| Folder: newly created | Triggers when a new folder is created in the target folder. | Because there are no file attributes, you cannot use file name, extension, or file size filters. |
| File: new upload | Triggers when a new file is added to the target folder. | If you use file additions as input, consider excluding the output folder from the target. |
| File: updated | Triggers when an existing file is updated. | If your operation selects "Replace" for duplicate names, test on the assumption that it becomes an update event. |
| File: deleted | Triggers when a file is deleted. | Do not design it to read the original file content after deletion. |
| Filters | You can filter file names with "equals" and "contains," extensions with "ends with," and file sizes with "greater than or equal" and "less than or equal." | Multiple filters are combined as OR within a group and AND between groups. File size values are in bytes. |

Publishing the Workflow is required to enable the trigger. After setting the target folder, subfolder handling, event type, and filters, check the trigger result with an actual file operation.

## Common misconceptions

### Saving to Storage lets every Agent and Dataset read it automatically

Saving to Storage, attaching to a conversation, and importing into a Dataset are separate operations. To use a file with an Agent, specify the target with "Copy path (for use with agents)" and confirm that necessary permissions and available features are in place. If you want to make it searchable by a Dataset, configure import and synchronization in the Dataset.

### A format that cannot be previewed also cannot be uploaded

Preview targets are limited. Even if a file cannot be previewed, you can upload or download it if it is in a normally allowed format and meets conditions such as storage capacity and permissions. Conversely, a displayed preview alone does not mean you have checked the file's content or every page.

### "Keep Both" always retains a separate version with the same name

With "Keep Both," a number is added to the new file or item. Agents or Workflows that reference the existing path do not necessarily automatically reference the new numbered item. Check the actual saved name and references.

### References automatically follow renames and moves

The Storage screen does not confirm that all references are automatically updated. Check the referencing Agents, Workflows, Pages, and usage procedures, then test both user operations and automatic execution after the change.

### You can quickly restore deleted items from the trash

The standard screen has no restore operation. The deletion confirmation also states that the action cannot be undone. Until you are certain that an item is unnecessary, consider moving or storing it rather than deleting it.

## If you cannot confirm it

If you cannot upload, an operation is not displayed, storage capacity is insufficient, or a preview or ZIP download fails, check the following in order.

1. Check the target Project, current folder, file name, and file size.
2. Check the list statuses "Pending," "Uploading," "Completed," and "Failed," along with the displayed error.
3. If you cannot determine whether to choose "Replace" or "Keep Both," check the existing file's modified date, size, and purpose, then consult the person responsible for updates.
4. Before and after renaming, moving, or deleting, check that the referencing Agent, Workflow, and Page can actually use it.
5. If the issue is not resolved, contact an administrator or responsible person with the Project name, folder, file name, operation date and time, operation performed, and screen display or error. Do not share confidential information or temporary download URLs beyond the necessary scope.

For storage capacity, permissions, and allowed formats that depend on contracts or settings, treat information displayed on screen and guidance from administrators as authoritative. Do not infer fixed values.

## Next articles to read

- [Use Storage files](../use/use-storage-files.md)
- [Organize your Storage folder structure](organize-storage-folders.md)
- [Configure Workflow triggers](configure-workflow-triggers.md)
- [Storage API](../api/storage-api.md)
- [Hosted Page Storage API](../page-runtime/hosted-page-storage-api.md)
