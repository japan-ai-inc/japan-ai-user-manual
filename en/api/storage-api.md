# Storage API

The Storage API is a public API to list, upload, retrieve, and delete files and folders in Project Storage over HTTP. For SDK implementation, see [SDK Storage](../development/sdk-storage.md), and for storage conditions in the UI, see [Storage Specifications and Limits](../build/storage-specs-and-limits.md).

## Related articles

- [SDK Storage](../development/sdk-storage.md)
- [Storage Specifications and Limits](../build/storage-specs-and-limits.md)
- [CLI Storage](../cli/cli-storage.md)
- [Common API and SDK Errors, Timeouts, and Limits](../development/api-sdk-errors-timeouts-limits.md)
- [Manage Project API Keys](../operations/manage-project-api-keys.md)
- [Hosted Page Storage API](../page-runtime/hosted-page-storage-api.md)

## Before you begin

The base URL is `https://api.japan-ai.co.jp`, and the main route is `/api/v1/storage`. The following sections cover this main route. Pass a Project API key in `x-api-key` or `Authorization: Bearer <key>`, and grant only the scopes required for the operation.

```bash
export JAI_STUDIO_API_KEY='value retrieved from secure storage'
curl -H "x-api-key: $JAI_STUDIO_API_KEY" \
  'https://api.japan-ai.co.jp/api/v1/storage/files?path=/reports&limit=50'
```

> Do not leave API keys, Bearer values, or signed URLs in browsers, source control, logs, or tickets. Use from a Hosted Page browser has different authentication and routes. Check [Hosted Page Storage API](../page-runtime/hosted-page-storage-api.md).

## Common response and error information

JSON responses generally include `success` and `requestId`. Obtain IDs from create and list responses. Authentication issues return 401 or 403, invalid input returns 400, and a missing target returns 404. For 429, use the response `retryAfterSeconds` as a guide for waiting. For details, see [Common API and SDK Errors, Timeouts, and Limits](../development/api-sdk-errors-timeouts-limits.md).

The default path is `/` when omitted. Paths containing `..` cannot be used, and consecutive `/` characters are normalized. File and folder names must be 1-255 characters after leading and trailing whitespace is removed; path separators, control characters, `.`, `..`, and similar values are not accepted.

## 1. List files and folders

`GET /api/v1/storage/files` requires `storage:read`. It returns completed files and folders.

| Parameter | Location | Required | Default and range | Meaning |
|---|---|---:|---|---|
| `path` | query | No | `/` | Path to list. |
| `recursive` | query | No | `false` | Includes subfolders when `true`. |
| `limit` | query | No | 50, 1-100 | Maximum items per page. |
| `cursor` | query | No | — | `nextCursor` from the previous response. |
| `orderBy` | query | No | `updatedAt` | `name`, `size`, `createdAt`, or `updatedAt`. |
| `orderDirection` | query | No | `desc` | `asc` or `desc`. |

The response includes `files` (ID, name, path, size, MIME type, and dates), `folders` (ID, name, and path), the `totalCount` for this page, and `nextCursor`. If `nextCursor` is not `null`, pass the value to the next request.

## 2. Upload files

### Send directly with multipart

`POST /api/v1/storage/files` requires `storage:write`. Pass one or more files in the `files` field of `multipart/form-data`. A request can contain up to 20 files; each file and the total size can each be up to 3 GB. Specify `path` in the query. A body `path` is also accepted for compatibility, but use the query. With an omitted `conflictResolution` or `error`, a file with the same name returns an error; `replace` requests replacement.

```bash
curl -X POST -H "x-api-key: $JAI_STUDIO_API_KEY" \
  -F 'files=@./report.pdf' \
  'https://api.japan-ai.co.jp/api/v1/storage/files?path=/reports&conflictResolution=replace'
```

The successful `files` response includes the ID, name, path, size, MIME type, and status.

### Send with a signed URL

Perform preparation, upload to the returned URL, and finalization in that order. Both `POST /api/v1/storage/files/prepare-upload` and `POST /api/v1/storage/files/finalize-upload` require `storage:write`.

| API | Required body | Optional body | Main response or result |
|---|---|---|---|
| `prepare-upload` | `fileName`, positive integer `fileSize` | `mimeType`, `path`, `metadata`, `md5`, `conflictResolution` | `storageId`, `uploadUrl`, `requiredHeaders`, `expiresAt`, `logicalPath`. `replaceIntent` may be returned only when `replace` is specified. |
| `finalize-upload` | `storageId`, `fileName`, positive integer `fileSize` | `mimeType`, `path`, `metadata`, `md5`, `replaceIntent` | `file` (`fileId`, name, path, size, MIME type, and status). |

The `fileSize` for `prepare-upload` is up to 2 GB. Send the request to `uploadUrl` with `requiredHeaders` unchanged, then pass the same file information and `storageId` to the finalization API. When replacing, also pass `replaceIntent` unchanged. Do not treat a file as completed before finalization. On finalization, a size or MD5 mismatch returns 400, and a missing prepared upload or storage destination returns 404.

## 3. Retrieve content, metadata, and an expiring URL

All require `storage:read`, and `fileId` is required.

| Method and path | Purpose | Parameters | Main successful response | Status |
|---|---|---|---|---|
| `GET /api/v1/storage/files/:fileId` | Download content | `fileId` | File body. Returns `Content-Type`, attached filename, and length. | 200, 404 |
| `GET /api/v1/storage/files/:fileId/metadata` | Check without sending content | `fileId` | `file.fileId`, `fileName`, `path`, `mimeType`, `sizeBytes` | 200, 404 |
| `GET /api/v1/storage/files/:fileId/download-url` | Issue an expiring URL | Optional `expiresInMinutes` | `downloadUrl`, `expiresAt`, `expiresInMinutes` | 200, 400, 404 |

When omitted, `expiresInMinutes` is 60 minutes. Specify a positive integer; it is capped at 1440 minutes. Because `downloadUrl` is not permanent, check `expiresAt` and retrieve it again when needed.

## 4. Manage folders and retrieve a ZIP

| Method and path | Scope | Input | Main response and constraints |
|---|---|---|---|
| `POST /api/v1/storage/folders` | `storage:write` | Required body `folderName`; optional `path` and `metadata` | ID, name, and path of `folder`. Created if the parent path does not exist. |
| `GET /api/v1/storage/folders/:folderId/entries` | `storage:read` | `folderId`; optional `recursive`, `limit`, and `cursor` | File and folder list, `nextCursor`. `limit` is 50, 1-100. |
| `GET /api/v1/storage/folders/:folderId` | `storage:read` | `folderId`, optional `maxSizeMb` | ZIP body. Default 100 MB, maximum 500 MB. Empty or oversized folders return 400. |
| `DELETE /api/v1/storage/folders/:folderId` | `storage:delete` | `folderId` | `folderId`, `deletedCount`. Deletes including its contents. |

The ZIP `maxSizeMb` is an integer of 1 or greater. A value over the limit is treated as 500 MB, and empty or oversized folders cannot be retrieved. To delete a file, use `DELETE /api/v1/storage/files/:fileId`, which also requires `storage:delete`. On success, it returns `fileId` and a message; a missing target returns 404.

> Check the scope of impact before deleting. Deleting a folder also affects its contents. A restore operation in the standard UI could not be confirmed.

## Common misconceptions

### You can save `downloadUrl` and use it as a permanent link

You cannot. The URL expires. Retrieve it immediately before use and check the returned expiration.

### An upload is complete if only preparation succeeds

It is not. A file is treated as completed by the API only after upload to the returned URL and successful `finalize-upload`.

### `storage:write` also permits deletion

It does not. Deleting files or folders requires `storage:delete`. Use `storage:read` for reading.

## Troubleshooting

For 400, check the input; for 401/403, check the Project, key, and scopes; and for 404, check the ID. For 429, check `retryAfterSeconds` and avoid repeated retries. For writes after a communication interruption, check the list, metadata, and finalization result before proceeding.

When contacting support, record the target Project, API route, `requestId`, target ID, time of execution, and HTTP status. Do not attach API keys, Authorization headers, signed URLs, or file contents.

## Checklist

- [ ] Passed the Project API key from secure server-side configuration
- [ ] Chose the minimum `storage:read`, `storage:write`, or `storage:delete` scope for each operation
- [ ] Obtained `fileId` or `folderId` from a list or create response
- [ ] Sent no more than 20 files in multipart, with each file and the total at 3 GB or less
- [ ] Completed the signed upload through finalization
- [ ] Checked the expiring URL `expiresAt`
- [ ] Checked the target and contents before deleting

## Next article

- [SDK Storage](../development/sdk-storage.md)
- [Storage Specifications and Limits](../build/storage-specs-and-limits.md)
- [CLI Storage](../cli/cli-storage.md)
- [Common API and SDK Errors, Timeouts, and Limits](../development/api-sdk-errors-timeouts-limits.md)
- [Hosted Page Storage API](../page-runtime/hosted-page-storage-api.md)
