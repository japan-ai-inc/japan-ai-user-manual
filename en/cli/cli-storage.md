# CLI Storage

`jai-studio storage` is a group of CLI commands that performs JSON operations for files and folders in Project Storage: listing, metadata, signed download URLs, deletion, and folder operations. It is for developers who can securely manage Project API keys and use the CLI.

For Storage SDK operations and execution modes, see [SDK Storage](../development/sdk-storage.md); for Storage retention conditions and UI limits, see [Storage Specifications and Limits](../build/storage-specs-and-limits.md). [CLI Configuration and Profiles](cli-config-and-profiles.md) covers profiles, credential resolution order, and common flags.

## Related articles

- [SDK Storage](../development/sdk-storage.md)
- [Storage Specifications and Limits](../build/storage-specs-and-limits.md)
- [Manage Project API Keys](../operations/manage-project-api-keys.md)
- [CLI Configuration and Profiles](cli-config-and-profiles.md)

## Before you begin

- Run the `@japan-ai-inc/studio-sdk` 0.11.0 CLI with Node.js 22 or later.
- Configure the API key and endpoint for the target Project through a profile, environment variable, or global flag. Do not leave keys in command history, source control, logs, or inquiries.
- Use `fileId` for file operations and `folderId` for folder operations. First, check target IDs, paths, and counts in the list JSON.

> `jai-studio storage` does not have `upload` or `download` subcommands. Do not guess and run CLI syntax to transfer file content. For methods using SDK `upload()` and `download()`, see [SDK Storage](../development/sdk-storage.md).

Successful standard output is JSON. Add `--pretty` to format it. On failure, JSON is output to standard error and the exit code is 1. Because `--debug` may display server-response details, do not share output that contains confidential information.

## Command list

| Command | Purpose | Destructive operation |
|---|---|---|
| `storage list` | Lists files and folders | No |
| `storage metadata` | Retrieves file metadata | No |
| `storage download-url` | Retrieves a time-limited download URL | No |
| `storage delete` | Deletes a file | Yes |
| `storage folders list` | Lists the contents of a specified folder | No |
| `storage folders create` | Creates a folder | No |
| `storage folders delete` | Deletes a folder and its contents | Yes |

## 1. List files and folders

```text
jai-studio storage list [--path <path>] [--recursive] [--cursor <cursor>] [--limit <n>] [--order-by <field>] [--order-direction <dir>]
```

Notation containing `<...>` below is **syntax that requires substitution**. Do not paste it into the shell with the angle brackets; replace it with an actual value.

| Flag | Required | Default | Meaning |
|---|---:|---|---|
| `--path <path>` | No | Not specified | Filters by the target path. |
| `--recursive` | No | Not specified | Requests a recursive list. |
| `--cursor <cursor>` | No | Not specified | Cursor for retrieving the next page. |
| `--limit <n>` | No | Not specified | Maximum items per page. Specify an integer. |
| `--order-by <field>` | No | Not specified | Specify `name`, `size`, `createdAt`, or `updatedAt` as the sort field. |
| `--order-direction <dir>` | No | Not specified | Specify `asc` or `desc`. |

```bash
jai-studio storage list --path /reports --recursive --limit 50 --order-by updatedAt --order-direction desc --pretty
```

If `nextCursor` in the response is not null, pass its value to the next `--cursor`.

```text
jai-studio storage list --cursor '<nextCursor>'
```

## 2. Retrieve file metadata and signed URLs

```text
jai-studio storage metadata <fileId>
```

| Positional argument or flag | Required | Default | Meaning |
|---|---:|---|---|
| `<fileId>` | Yes | — | ID of the target file. |

Metadata includes the file ID, name, path, MIME type, and size.

```bash
jai-studio storage metadata file-id
```

```text
jai-studio storage download-url <fileId> [--expires-in-minutes <n>]
```

| Positional argument or flag | Required | Default | Meaning |
|---|---:|---|---|
| `<fileId>` | Yes | — | ID of the target file. |
| `--expires-in-minutes <n>` | No | Not specified | Requests URL validity in an integer number of minutes. |

```bash
jai-studio storage download-url file-id --expires-in-minutes 30
```

The returned `downloadUrl` is not permanent. Check `expiresAt` and `expiresInMinutes`, and retrieve it when needed. Because the CLI does not validate the minimum or maximum allowed duration, do not assume unverified values.

> Handle signed URLs like credentials. Do not paste them into tickets, public documents, or source code, and do not reuse expired URLs.

## 3. Work with folders

```text
jai-studio storage folders list <folderId> [--recursive] [--cursor <cursor>] [--limit <n>]
```

| Positional argument or flag | Required | Default | Meaning |
|---|---:|---|---|
| `<folderId>` | Yes | — | ID of the target folder. |
| `--recursive` | No | Not specified | Requests a recursive list. |
| `--cursor <cursor>` | No | Not specified | Cursor for pagination. |
| `--limit <n>` | No | Not specified | Maximum items per page. Specify an integer. |

```bash
jai-studio storage folders list folder-id --recursive --limit 100
```

```text
jai-studio storage folders create --name <name> [--path <path>]
```

| Flag | Required | Default | Meaning |
|---|---:|---|---|
| `--name <name>` | Yes | — | Name of the folder to create. |
| `--path <path>` | No | Not specified | Parent path. |

```bash
jai-studio storage folders create --name archive --path /reports
```

Folder names follow common Storage limits. Leading and trailing whitespace is removed; empty strings, `.`, `..`, and names containing `/` or `\` cannot be used. Control characters are removed, and names longer than 255 characters cannot be used.

## 4. Delete a file or folder

```text
jai-studio storage delete <fileId>
```

| Positional argument | Required | Default | Meaning |
|---|---:|---|---|
| `<fileId>` | Yes | — | ID of the file to delete. |

```bash
jai-studio storage delete file-id
```

```text
jai-studio storage folders delete <folderId>
```

| Positional argument | Required | Default | Meaning |
|---|---:|---|---|
| `<folderId>` | Yes | — | ID of the folder to delete. |

```bash
jai-studio storage folders delete folder-id
```

Folder deletion also affects items below the folder. Before deletion, check IDs, paths, and references using the list and metadata. `deletedCount` in the deletion result is the number of items deleted.

## Common misconceptions

### `storage download-url` saves the file locally

No. This command only returns a time-limited URL in JSON. `storage download` for retrieving content directly is not implemented in the CLI. If you need retrieval processing, choose SDK `download()` or a method appropriate to your environment.

### You can specify any string or decimal in `--limit`

You cannot. `--limit` and `--expires-in-minutes` are parsed as safe integers. However, the CLI does not independently limit page size or URL validity time. Follow server-side conditions, and check the value and target when an error occurs.

## Troubleshooting

| Situation | Check or response |
|---|---|
| You get an authentication or connection error | Check the target Project, profile, endpoint, API key expiration, scopes, and revocation. |
| The list is empty or the target cannot be found | Check `--path`, `--recursive`, the cursor, and target Project. Do not conclude that it does not exist solely from an empty result. |
| A signed URL cannot be used for retrieval | Check `fileId`, URL expiration, and user permissions, then retrieve a new URL. Do not share the URL itself. |
| You may have deleted the wrong item | Do not write back to the same ID or path by guesswork. Record the target, operation time, and output, then contact an administrator. |

## Checklist

- [ ] I checked the target Project and execution profile.
- [ ] I checked the target `fileId` or `folderId` from list JSON.
- [ ] I checked the signed URL's expiration and did not share it unnecessarily.
- [ ] I confirmed that the CLI has no `upload` or `download` and did not confuse it with SDK operations.
- [ ] Before deletion, I checked the target path, contents below it, and references.

## Next articles to read

- [SDK Storage](../development/sdk-storage.md)
- [Storage Specifications and Limits](../build/storage-specs-and-limits.md)
- [Manage Project API Keys](../operations/manage-project-api-keys.md)
- [CLI Configuration and Profiles](cli-config-and-profiles.md)
