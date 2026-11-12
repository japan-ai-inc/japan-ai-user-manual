# Cannot Read or Write Objects or Storage

If you cannot find, open, or save Object records or Storage files, or actions fail from a Page or API, do not try to resolve the issue by changing permissions alone. Check the target organization, Project, and entry point; the data state; and the route appropriate for the action, in that order. This article is for users and administrators. It does not cover detailed procedures for changing Object design, Storage folder design, or API implementation.

## Related articles

- [View and update Object data](../use/view-and-update-object-records.md)
- [Use Storage files](../use/use-storage-files.md)
- [Understand organization, Project, and App permissions](../operations/understand-org-project-app-permissions.md)
- [Object specifications and limits](../build/object-specs-and-limits.md)
- [Storage specifications and limits](../build/storage-specs-and-limits.md)
- [Hosted Page Objects API](../page-runtime/hosted-page-objects-api.md)
- [Hosted Page Storage API](../page-runtime/hosted-page-storage-api.md)

## Before you begin

- Verify that you have opened the correct organization, Project, App, and Page, and compare the target Object name, record, and file name or path.
- Distinguish whether the action is "view," "create," "update," or "delete." Being able to view does not necessarily mean that you can write.
- Record whether the issue occurs in the standard screen, a Page, an Agent, SQL, or an external API. Authentication and routes differ by entry point even for the same data.
- Confirm whether the immediately preceding processing completed. Do not immediately repeat an unknown update or upload; retrieve the current data and file list again.

> Knowing a shared link, Storage path, file ID, or Object ID does not grant access. Do not broaden sharing scope or add someone else's credentials or API key to bypass insufficient permissions.

## Isolate the issue by symptom

### When using Objects or Storage in the standard screen

| Symptom or displayed message | Likely cause | What to check or do | Detailed article |
|---|---|---|---|
| The Object, record, or Storage file cannot be found | You opened another Project or App, search criteria or current folder differs, or it is outside your viewing scope. | Check the Project and the guided entry point; review Object search criteria and page, and the current Storage folder and search terms. Do not create the same record or file just because it is not visible. | [View and update Object data](../use/view-and-update-object-records.md), [Use Storage files](../use/use-storage-files.md) |
| The Object is visible but cannot be edited or created | Update or create permission, field or record conditions, or system-field restrictions. | Check the permissions required for the action type and the guidance on screen. Also check entry conditions for target fields, such as required status, type, numeric range, and uniqueness constraints. | [Object specifications and limits](../build/object-specs-and-limits.md), [Understand organization, Project, and App permissions](../operations/understand-org-project-app-permissions.md) |
| A save error occurs, or an update by another user or process is reported | Invalid input, duplication, or simultaneous updates. | Correct the field indicated by the message. In a conflict, read the latest record again, confirm other users' changes and the value appropriate for the business, then update only what is necessary. Do not resend a write with the same conditions when completion is unknown. | [View and update Object data](../use/view-and-update-object-records.md), [Object SQL DML](../object-sql/object-sql-dml.md) |
| "Upload Files" cannot be selected | You lack save or update permission, it is an App-managed file, or availability conditions are not met. | Check the target Project and save destination, the required create/save and update permissions, and the restriction reason on screen. Do not treat read-only files marked "Installed app" as normal update targets. Do not assume Storage seeds during distribution arbitrarily overwrite same-named user files. | [Use Storage files](../use/use-storage-files.md), [Understand organization, Project, and App permissions](../operations/understand-org-project-app-permissions.md) |
| You closed the dialog but the file is absent, or "Upload failed" is displayed | Transfer or save confirmation has not completed, or there is a capacity, format, storage-capacity, or permission issue. | Check the list for "Pending," "Uploading," "Complete," and "Failed." The default limit for one file is 2GB, but actual availability also depends on storage capacity, permissions, and environment configuration. | [Storage specifications and limits](../build/storage-specs-and-limits.md) |
| "File Already Exists" | A same-named file is in the same destination. | Check the existing file's version, and the Pages, Agents, and Workflows that reference it, as well as the impact of replacement. If the intent is unclear, do not select "Keep Both" or replacement; ask the owner. | [Use Storage files](../use/use-storage-files.md) |
| "This file type is not supported for preview" | The file is outside the preview scope, or exceeds the preview limit. | This does not mean the file cannot be uploaded. Download it to check it if you have retrieval permission. The standard-screen preview limit is 10MB. | [Storage specifications and limits](../build/storage-specs-and-limits.md) |

### When using a Page or API

| Symptom or displayed message | Likely cause | What to check or do | Detailed article |
|---|---|---|---|
| 401, 403, or 404 | A mismatch in Page session, access mode, target Project or ID, Object/record/Storage permission, or route. | Reopen the Page and check the user and access mode. In a browser Hosted Page, use same-origin Page-relative routes and the Page session; do not add a Project API key or authentication headers. | [Hosted Page authentication and API routes](../page-runtime/hosted-page-auth-and-api-routes.md), [Hosted Page Objects API](../page-runtime/hosted-page-objects-api.md), [Hosted Page Storage API](../page-runtime/hosted-page-storage-api.md) |
| An Object update returns 409 | `expectedRevision` does not match the latest record. | Get the latest record, review the changes, and then update again. Do not resend with the same revision. An update that omits `expectedRevision` uses a different conflict policy, so select it intentionally. | [Hosted Page Objects API](../page-runtime/hosted-page-objects-api.md) |
| The Object API returns 400 or a search fails | Field entry conditions, an ID or search criteria error, or a browser search query that is too long. | Check Object and field types, required status, and constraints. For Hosted Page SDK search, ensure the query string is 8,000 characters or fewer; reduce or split the criteria. | [Object specifications and limits](../build/object-specs-and-limits.md), [Hosted Page Objects API](../page-runtime/hosted-page-objects-api.md) |
| A Storage upload from a Page fails | Item count, size, conflict option, storage capacity, or incomplete preparation, transfer, or confirmation. | SDK upload accepts up to 20 items at a time. Distinguish the default 2GB per file from the 32MiB body limit for the normal proxy route. In a prepare, transfer, and finalize flow using signed URLs, do not treat processing as complete before finalization. | [Hosted Page Storage API](../page-runtime/hosted-page-storage-api.md) |
| Data can be read in a Page but not through the API, or vice versa | Authentication methods and routes for the standard screen, Page, and Public API are being confused. | Distinguish Page-relative browser APIs from Public APIs for servers and external integrations. Do not assume a Page can call any Public API route; see [Objects API](../api/objects-api.md) and [Storage API](../api/storage-api.md). | [Hosted Page authentication and API routes](../page-runtime/hosted-page-auth-and-api-routes.md), [Hosted Page Objects API](../page-runtime/hosted-page-objects-api.md), [Hosted Page Storage API](../page-runtime/hosted-page-storage-api.md) |

## Order for administrators to check

1. Check whether the user is an active member of the target Project and fits the sharing scope of resources including Objects and Storage. Organization membership, Project membership, and App availability conditions are separate.
2. Check whether the user needs only viewing or needs create, update, or delete access, then review the minimum role, IAM, and resource conditions.
3. For Objects, check field and record access conditions separately, as well as permissions for related targets. Setting a relation does not automatically grant read or update permission on the referenced target.
4. For Storage, check environment-dependent conditions such as the save destination, remaining capacity, and allowed formats, and verify that the file is not App-managed and read-only.
5. For a Page, test the combination of access settings, the intended user's Page session, the Page, Project, and target data. Sharing a public URL does not increase data-action permissions.

## Before contacting an administrator or support

Using the perspective in [Collect the Information Needed for an Inquiry](collect-info-for-support.md), collect the target organization, Project, App/Page, Object name or file name/path, operation time, user role, entry point used, HTTP status or displayed message, reproduction steps, and items already checked. Do not include API keys, Cookies, authentication headers, signed URLs, Secret values, or file contents in an inquiry.

## Next articles

- [View and update Object data](../use/view-and-update-object-records.md)
- [Use Storage files](../use/use-storage-files.md)
- [Understand organization, Project, and App permissions](../operations/understand-org-project-app-permissions.md)
- [Hosted Page Objects API](../page-runtime/hosted-page-objects-api.md)
- [Hosted Page Storage API](../page-runtime/hosted-page-storage-api.md)
