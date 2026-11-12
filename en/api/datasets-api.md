# Datasets API

The Datasets API is a public API that uses HTTP to list Datasets and search them (only retrieves matching document chunks). In the SDK, `datasets.list()` and `datasets.search()` call the same routes. For preparation steps in the UI, see [Prepare a Dataset and Make an Agent Reference It](../build/prepare-and-reference-datasets.md), and for specifications, see [Dataset Specifications and Limits](../build/dataset-specs-and-limits.md).

## Related articles

- [Prepare a Dataset and Make an Agent Reference It](../build/prepare-and-reference-datasets.md)
- [Dataset Specifications and Limits](../build/dataset-specs-and-limits.md)
- [Execution Modes, Authentication, and Supported Scope](../development/execution-modes-and-auth.md)
- [Common API and SDK Errors, Timeouts, and Limits](../development/api-sdk-errors-timeouts-limits.md)
- [Manage Project API Keys](../operations/manage-project-api-keys.md)

## Scope of this article

This article covers only the two read routes. The same `/api/v1/datasets` path also has routes for creating, updating, and deleting Datasets and for adding and deleting documents, but it could not be confirmed whether these may be publicly used, so this article does not cover them. The SDK `datasets` module is also read-only (`list()` and `search()`).

## Before you begin

The base URL is `https://api.japan-ai.co.jp`, and the route is `/api/v1/datasets`. Pass a Project API key in `x-api-key` or `Authorization: Bearer <key>`. The required scope is `dataset:read`.

```bash
export JAI_STUDIO_API_KEY='value retrieved from secure storage'
curl -H "x-api-key: $JAI_STUDIO_API_KEY" \
  'https://api.japan-ai.co.jp/api/v1/datasets?limit=50'
```

> Do not leave API keys in browsers, source control, logs, or tickets.

## 1. List Datasets

| Method and path | Required scope | Parameters | Main successful response |
|---|---|---|---|
| `GET /api/v1/datasets` | `dataset:read` | `limit`: optional / `cursor`: optional / `search`: optional, case-insensitive partial match of Dataset names / `scope`: optional, `project` (default) or `organization` | `datasets`, `totalCount`, `nextCursor` |

- `scope=project` returns Datasets linked to the Project.
- `scope=organization` returns organization Datasets not linked to the Project that the signed-in user can read. It requires a user session and cannot be used with a Project API key.
- Each Dataset includes `id`, `name`, `description`, `status`, `version`, `updateInterval`, `itemCount`, `createdAt`, `updatedAt`, and `lastUpdateAt`.
- In a user session, Datasets that the user is not permitted to read are excluded, so a page can contain fewer items than `limit`. This permission filtering is not reflected in `totalCount`. Continue fetching with `nextCursor` while a next page exists.

## 2. Search a Dataset

| Method and path | Required scope | Parameters | Main successful response |
|---|---|---|---|
| `POST /api/v1/datasets/{datasetId}/search` | `dataset:read` | `datasetId`: path, required / body: `query` (required), `topN` (optional, 1-50, default 10) | `datasetId`, `results` |

This route searches only; it does not generate an answer. You can search Datasets linked to the Project and organization Datasets that the signed-in user can read.

Each result includes `rank`, `chunkId`, `score`, and `text`, and may include `filename`, `title`, `pageNumber`, `sheetName`, and `sourceUrl`.

```bash
curl -X POST \
  -H "x-api-key: $JAI_STUDIO_API_KEY" \
  -H 'Content-Type: application/json' \
  -d '{"query":"Return conditions","topN":5}' \
  'https://api.japan-ai.co.jp/api/v1/datasets/<datasetId>/search'
```

Replace `<datasetId>` with the `id` in the list response.

## Common misconceptions

### Search results are answers as-is

The response contains document chunks close to the question. The caller generates answers and assesses the validity of sources.

### `scope=organization` can also be used with a Project API key

It cannot. It requires a signed-in user session.

### You can check every document for omissions

Search returns nearby chunks, so it is not suitable for a full-text review such as listing every issue in a contract without omissions.

## Troubleshooting

| Status | What to check |
|---|---|
| 401 | Check the key value and expiration date. |
| 403 | Check the target Project, `dataset:read`, and whether `scope=organization` is called with a user session. |
| 404 | Check `datasetId` and the target Project. Do not search for IDs in other Projects. |
| 429 | Follow the response `Retry-After` and `retryAfterSeconds`, and avoid repeated retries. |

When contacting support, record the target Project, API route, `requestId`, `datasetId`, time of execution, and HTTP status. Do not include API keys or Authorization headers.

## Checklist

- [ ] Passed the Project API key from secure server-side configuration
- [ ] Added the `dataset:read` scope
- [ ] Called with a user session when reading organization Datasets
- [ ] Continued fetching while `nextCursor` exists and did not use only `totalCount` to determine the count
- [ ] Treated search results as document chunks, not answers

## Next article

- [Prepare a Dataset and Make an Agent Reference It](../build/prepare-and-reference-datasets.md)
- [Execution Modes, Authentication, and Supported Scope](../development/execution-modes-and-auth.md)
- [Common API and SDK Errors, Timeouts, and Limits](../development/api-sdk-errors-timeouts-limits.md)
