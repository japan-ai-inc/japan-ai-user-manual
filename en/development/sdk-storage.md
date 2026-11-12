# SDK Storage

Studio SDK's `storage` is a module for operating files and folders in a Project's Storage. Authentication and some operations differ by run location.

For execution modes, see [Execution Modes, Authentication, and Supported Scope](execution-modes-and-auth.md), and for storage conditions, see [Storage Specifications and Limits](../build/storage-specs-and-limits.md). [Storage API](../api/storage-api.md) and [Hosted Page Storage API](../page-runtime/hosted-page-storage-api.md) cover the details.

## Related articles

- [Execution Modes, Authentication, and Supported Scope](execution-modes-and-auth.md)
- [Storage Specifications and Limits](../build/storage-specs-and-limits.md)
- [Storage API](../api/storage-api.md)
- [Hosted Page Storage API](../page-runtime/hosted-page-storage-api.md)

## Before you begin

- Use Node.js 22 or later with the SDK.
- In local environments and PRS servers, pass the API key only from the server side. In a PRS browser, use `createClient({ baseUrl: "" })`.
- Obtain file IDs from a list or upload response.

> Do not leave API keys or signed URLs in browsers, logs, source control, or inquiries.

## Supported scope by execution mode

`downloadFolder()` cannot be used in a PRS browser.

| Operation | Local | PRS browser | PRS server | Notes |
|---|---|---|---|---|
| List and metadata | Available | Available | Available | Use `list()`, `listFolderContents()`, and `getMetadata()`. |
| Upload files | Available | Available | Available | In a PRS browser, `upload()` internally switches to a flow that uses a signed URL. |
| Download files | Available | Available | Available | `download()` returns a `Response`. |
| Signed download URL | Available | Available | Available | Use `getDownloadUrl()` to obtain the URL and expiration information. |
| Delete files and folders | Available | Available | Available | `deleteFile()` and `deleteFolder()` are not substitutes for restoration. |
| Download folder ZIP | Available | Not available | Available | In a PRS browser, `downloadFolder()` returns an error. |

## 1. Get a list

With `list()`, you can specify `path`, `recursive`, `limit`, `cursor`, `orderBy`, and `orderDirection`.

```ts
const result = await studio.storage.list({
  path: "/reports",
  recursive: true,
  limit: 50,
  orderBy: "updatedAt",
  orderDirection: "desc",
});

for (const file of result.files) {
  console.log(file.fileId, file.path, file.sizeBytes);
}

if (result.nextCursor) {
  const next = await studio.storage.list({ cursor: result.nextCursor });
}
```

In a PRS browser, list page size is 1–100, with a default of 50. To inspect only within a folder, use `listFolderContents()`.

## 2. Upload files

Pass a `File`, `Blob`, or array to `upload()`. `path` is the destination, and `conflictResolution: "replace"` replaces a file with the same name.

```ts
const input = document.querySelector<HTMLInputElement>("#file");
const file = input?.files?.[0];
if (!file) throw new Error("Please select a file");

const uploaded = await studio.storage.upload(file, {
  path: "/reports",
  conflictResolution: "replace",
});
console.log(uploaded.files[0].fileId);
```

You can pass up to 20 files to a single `upload()`. Each file can be up to 2 GB. Split multiple-file requests so that their total does not exceed 2 GB.

For large files, run `prepareUpload()`, `PUT` to `uploadUrl`, then `finalizeUpload()`. Pass `requiredHeaders`, `storageId`, and `replaceIntent` from the response unchanged.

```ts
const prepared = await studio.storage.prepareUpload({
  fileName: file.name,
  fileSize: file.size,
  mimeType: file.type,
  path: "/reports",
  conflictResolution: "replace",
});

await fetch(prepared.uploadUrl, {
  method: "PUT",
  headers: prepared.requiredHeaders,
  body: file,
});

const saved = await studio.storage.finalizeUpload({
  storageId: prepared.storageId,
  replaceIntent: prepared.replaceIntent,
  fileName: file.name,
  fileSize: file.size,
  mimeType: file.type,
  path: "/reports",
});
```

> Do not treat uploaded data as a Storage file until `finalizeUpload()` succeeds. If communication fails, do not infer and reuse the URL or `replaceIntent`.

## 3. Choose between downloads and signed URLs

Use `download()` to process content, then obtain `blob()`, `text()`, and so on from the `Response`. For a temporary retrieval destination, use `getDownloadUrl()` to obtain a signed URL and `expiresAt`. If `expiresInMinutes` is omitted in a PRS browser, the default is 60 minutes.

```ts
const response = await studio.storage.download("file-id");
const blob = await response.blob();

const { downloadUrl, expiresAt } = await studio.storage.getDownloadUrl(
  "file-id",
  { expiresInMinutes: 30 }
);
console.log(downloadUrl, expiresAt);
```

`downloadFolder()` is available only locally and on a PRS server. Check [Storage Specifications and Limits](../build/storage-specs-and-limits.md) for folder ZIP constraints.

## 4. Delete and operate folders

Use `createFolder()` and `deleteFolder()` for folders, and `deleteFile()` for files. Check the target ID and path before deletion.

```ts
const folder = await studio.storage.createFolder({
  folderName: "archive",
  path: "/reports",
});

await studio.storage.deleteFile("file-id");
const deleted = await studio.storage.deleteFolder(folder.folderId);
console.log(deleted.deletedCount);
```

> Check the target and references before deletion.

## Common misconceptions

### You can add an API key in a PRS browser to retrieve a folder ZIP

You cannot. `downloadFolder()` is unsupported in a PRS browser. Put the required processing on a PRS server or change it to operate on individual files.

### A signed URL is a permanent file URL

It is not. Upload and download URLs expire. Obtain them again when needed.

### If `upload()` succeeds, you do not need to check file count, size, or storage capacity

This is not correct. `upload()` supports up to 20 files, with a maximum of 2 GB per file. Storage capacity and permissions also affect the result.

## If something does not work

| Situation | Check and action |
|---|---|
| An authentication error occurs | Check the target Project, API key, and run location. |
| A Storage operation in a PRS browser fails | Check that it runs in PRS and is initialized with `baseUrl: ""`. |
| Upload fails | Check the file count, size, storage capacity, permissions, `path`, and conflict setting. |
| `finalizeUpload()` fails | Check `storageId`, file information, `replaceIntent`, and the PUT result. |
| `downloadFolder()` fails in a PRS browser | It is unsupported. Change to individual downloads or PRS server processing. |
| You may have deleted the wrong target | Check the target path and execution record, then contact the administrator. |

For an inquiry, record the execution mode, SDK version, target ID, operation date and time, error, and steps to reproduce. Do not attach API keys, cookies, signed URLs, or file contents.

## Checklist

- [ ] I decided whether it runs locally, in a PRS browser, or on a PRS server.
- [ ] I initialized the PRS browser with `createClient({ baseUrl: "" })` and did not include an API key.
- [ ] I checked the target `fileId` or `folderId` from a list response.
- [ ] I keep uploads to 20 or fewer files and each file and total request size at 2 GB or less.
- [ ] I checked signed URL expiration and completion of `finalizeUpload()`.
- [ ] I do not use `downloadFolder()` in a PRS browser.
- [ ] Before deletion, I checked the target path, references, and impact scope.

## Next article

- [Execution Modes, Authentication, and Supported Scope](execution-modes-and-auth.md)
- [Storage Specifications and Limits](../build/storage-specs-and-limits.md)
- [Storage API](../api/storage-api.md)
- [Hosted Page Storage API](../page-runtime/hosted-page-storage-api.md)
- [Manage Project API Keys](../operations/manage-project-api-keys.md)
