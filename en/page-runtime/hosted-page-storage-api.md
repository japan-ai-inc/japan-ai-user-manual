# Hosted Page Storage API

In a Hosted Page browser, you work with Project Storage through the Page runtime session. Do not place a Project API key in code.

## Related articles

- [SDK Storage](../development/sdk-storage.md)
- [Storage API](../api/storage-api.md)
- [Storage Specifications and Limits](../build/storage-specs-and-limits.md)
- [CLI Storage](../cli/cli-storage.md)

## Scope of this article

This article covers the Page-relative Storage API and Studio SDK `storage` used **inside a Hosted Page browser**. It does not cover ordinary screens, local Node.js, Page server processing, or browser use on arbitrary external sites.

Browser code uses `createClient({ baseUrl: "" })` only in the Page runtime. An empty `baseUrl` calls same-origin relative paths and authenticates with the Page session. Do not add Project API keys or authentication headers.

```ts
import { createClient } from "@japan-ai-inc/studio-sdk";

const studio = createClient({ baseUrl: "" });
const { files, nextCursor } = await studio.storage.list({
  path: "/reports",
  limit: 50,
  orderBy: "updatedAt",
  orderDirection: "desc",
});
```

> This initialization is for Hosted Page browsers only. For local development and Page server processing, see [SDK Storage](../development/sdk-storage.md).

## Choose Page-relative routes

The SDK browser entry point is `/api/v1/storage`. `storage` converts Page runtime responses into SDK public forms such as `fileId`, `folderId`, and `path`. Applications use SDK methods.

| Purpose | SDK method | Page-relative route | Notes |
|---|---|---|---|
| List files and folders | `list()` | `GET /api/v1/storage/files` | Specify `path`, `recursive`, `limit`, `cursor`, and sort order. In browsers, the range is 1–100 and the default is 50. |
| Register files | `upload()`, `prepareUpload()`, `finalizeUpload()` | `POST /api/v1/storage/files/prepare-upload`, `PUT` to the returned URL, and `POST /api/v1/storage/files/finalize-upload` | `upload()` also prepares, sends, then finalizes. |
| Retrieve content | `download()` | `GET /api/v1/storage/files/{fileId}/content-stream` | The return value is `Response`. |
| Metadata | `getMetadata()` | `GET /api/v1/storage/files/{fileId}` | Checks without retrieving file content. |
| Expiring URL | `getDownloadUrl()` | `GET /api/v1/storage/files/{fileId}/download-url` | Check `expiresAt`; do not save or share it as a permanent URL. |
| Folders | `createFolder()`, `listFolderContents()`, `deleteFolder()` | `POST /api/v1/storage/folders`, `GET /api/v1/storage/folders/{folderId}/entries`, and `DELETE /api/v1/storage/folders/{folderId}` | Use `deleteFile()` to delete files. |

`/api/storage/...` is also a Page-relative session route, but it is not the contract of the SDK public API. Standardize new implementations on the SDK using `/api/v1/storage`; do not specify `/api/v1/studio-storage/...`.

## 1. Complete upload through finalization

Pass a `File`, `Blob`, or their array to `upload()`. A single call supports up to 20 items. Specify `conflictResolution: "replace"` only when replacing a file with the same name. Conflicts are errors if it is omitted.

```ts
const input = document.querySelector<HTMLInputElement>("#report");
const file = input?.files?.[0];
if (!file) throw new Error("Select a file.");

const result = await studio.storage.upload(file, {
  path: "/reports",
  conflictResolution: "replace",
});
console.log(result.files[0].fileId);
```

For large files or implementations that control progress, call `prepareUpload()`, `PUT` to the returned `uploadUrl`, and `finalizeUpload()` in order. Use returned `requiredHeaders`, `storageId`, and `replaceIntent` as-is. The upload is not complete before finalization. Check before retrying after failure.

The default size limit for one file is 2GB. Bodies on the `/api/storage` route are limited to 32MiB. SDK `upload()` in the Page runtime uses a signed-URL prepare-and-finalize flow. Whether storage is available also depends on storage capacity, permissions, and environment configuration.

## 2. Separate download from in-screen display

Use `download()` when processing content and obtain `blob()`, `text()`, and other values from `Response`. Use `getDownloadUrl()` for an expiring retrieval destination and retrieve it immediately before use. When `expiresInMinutes` is omitted, the return value is 60 minutes.

```ts
const response = await studio.storage.download("file-id");
const blob = await response.blob();

const { downloadUrl, expiresAt } = await studio.storage.getDownloadUrl("file-id", {
  expiresInMinutes: 30,
});
console.log(expiresAt);
```

The dedicated route to display safe formats on the same origin is `GET /api/v1/storage/files/{fileId}/view`. It supports PDF, images, video, audio, and `text/plain`; SVG is unsupported. Files larger than 50MiB cannot be displayed. Video and audio support seeking through Range requests. URL-encode `fileId` and handle format, size, and permission errors.

```ts
const viewUrl = `/api/v1/storage/files/${encodeURIComponent(fileId)}/view`;
preview.src = viewUrl;
```

Folder ZIP retrieval through `downloadFolder()` is unsupported in browsers. Do not work around it by passing a key; consider a UI for individual files or server processing. For limits, see [Storage API](../api/storage-api.md); for screen operations, see [Storage Specifications and Limits](../build/storage-specs-and-limits.md).

## Check each access mode

Storage routes accept only requests with a Page session. "Sign-in required," "Sign-in optional," "Anonymous only," and "External customer auth" have different authentication and session routes. Because the route itself does not branch by access-mode name, you cannot conclude that every Storage operation always succeeds in a particular mode.

| "Access settings" | Page-relative Storage request | Implementation check |
|---|---|---|
| "Sign-in required" | Requests with a signed-in Page session. | Confirm only the required scope of listing, registering, retrieving, and deleting for target users. |
| "Sign-in optional" | Requests using a Page session. | Verify display and whether updates are allowed in both signed-in and signed-out states. |
| "Anonymous only" | Requests using an anonymous Page session. | Limit it to paths and operations that may be shown, and check rejection and success. |
| "External customer auth" | Requests using a Page session after authentication. | Confirm display scope, writes, and deletion with the intended role. |

> Knowing a URL or `fileId` does not grant permission. For 403 and 404, do not add authentication information; check access settings, the target Project, user, and target file.

## Common misunderstandings

### `/api/v1/studio-storage` can be called in the browser

No. This is not a public Page-relative route to specify from a browser. The SDK calls `/api/v1/storage`, and the Page runtime connects in the context of the current organization, Project, and session. Do not implement access to other Projects by manually entering identifiers.

### `uploadUrl` or `downloadUrl` can be saved as a permanent URL

No. They are expiring URLs. Check `expiresAt` and retrieve them again as needed. Do not include them in source control, public URLs, long-term stored data, or inquiries.

### Adding an API key makes folder ZIP work in the browser

No. `downloadFolder()` is unsupported in Page runtime browsers. Adding a key only exposes secret information.

## If something does not work

| Situation | What to check and do |
|---|---|
| Returns 401, 403, or 404 | Check the access mode, target Project, Page session, target ID, and user. Do not add a key. |
| Upload fails | Check 20 items, 2GB, 32MiB for `/api/storage`, storage capacity, conflict specification, and finalization result. |
| Cannot display inline | Check whether it is a MIME type supported by `/view`, not SVG, 50MiB or less, and the target ID. |
| Folder ZIP retrieval fails | It is unsupported in browsers. Consider a UI for individual files or server processing. |
| Storage API returns 503 | Storage integration may not be configured. Do not change the URL or authentication; ask the administrator with the target Page's settings and the occurrence time. |

For inquiries, record the target Project, Page name, access mode, SDK version, method, target ID, date and time, HTTP status, and reproduction steps. Do not share API keys, cookies, authentication headers, signed URLs, or file contents.

## Checklist

- [ ] Used `createClient({ baseUrl: "" })` in a Hosted Page browser and did not include an API key
- [ ] Standardized new implementations on `studio.storage` and did not directly construct `/api/storage` or `/api/v1/studio-storage`
- [ ] Kept uploads to 20 items or fewer and confirmed completion of prepare, send, and finalization
- [ ] Distinguished storage capacity, the default 2GB per-file limit, and the normal 32MiB proxy limit
- [ ] Checked `expiresAt` for expiring URLs and did not share them unnecessarily
- [ ] Checked `/view` MIME types and the 50MiB limit
- [ ] Did not use `downloadFolder()` in a Page runtime browser
- [ ] Tested the minimum required Storage operations with the intended access modes and users

## Next articles

- [SDK Storage](../development/sdk-storage.md)
- [Storage API](../api/storage-api.md)
- [Storage Specifications and Limits](../build/storage-specs-and-limits.md)
- [CLI Storage](../cli/cli-storage.md)
