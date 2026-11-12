# Pages API

The Pages API is a REST API for developers that temporarily uploads a completed ZIP for a Page and registers it as a new Page or a new version of an existing Page.

This API provides only ZIP upload preparation and deployment. **There are no endpoints to retrieve or change access settings** such as the Page list, access mode, public URL, or external customer authentication. To change the sharing scope, open "Access settings" for the target Page in Studio. For when to use the SDK or CLI, see [SDK Pages and Platform](../development/sdk-pages-and-platform.md).

## Related articles

- [CLI Pages](../cli/cli-pages.md)
- [SDK Pages and Platform](../development/sdk-pages-and-platform.md)
- [Page Specifications and Limits](../build/page-specs-and-limits.md)
- [Common API and SDK Errors, Timeouts, and Limits](../development/api-sdk-errors-timeouts-limits.md)

**Out of scope:** For the Page list and access settings, use the Studio UI; for checking an artifact locally, see [Preview a Page Locally and Deploy It](../development/local-preview-and-deploy-page.md); and for checking a new version, see [Page Build, Start, and Update](../development/page-build-start-update.md).

## Before you begin

The production base URL is `https://api.japan-ai.co.jp`, and the common path is `/api/v1/pages`. Every endpoint requires the Project API key for the target Project and the `page:write` scope. You can pass the key in the `x-api-key` header or `Authorization: Bearer <key>`. The following examples use `x-api-key`.

```bash
export JAI_STUDIO_API_KEY='value retrieved from secure storage'
export PAGE_ZIP='./page-release.zip'
```

> Do not include API keys in browser JavaScript, public environment variables, source control, logs, or support inquiries. For issuance, minimum scopes, and revocation, follow [Manage Project API Keys](../operations/manage-project-api-keys.md). Complete the ZIP before sending it.

## Endpoint list

| Operation | Method and path | Required scope |
|---|---|---|
| Prepare ZIP upload for a new Page | `POST /api/v1/pages/upload-url` | `page:write` |
| Deploy as a new Page | `POST /api/v1/pages/deploy` | `page:write` |
| Prepare ZIP upload for an existing Page version | `POST /api/v1/pages/{pageId}/upload-url` | `page:write` |
| Deploy a new version of an existing Page | `POST /api/v1/pages/{pageId}/deploy` | `page:write` |

`pageId` is the Page ID to update. If the target Page is not in the Project, the response is 404. Updates to a release-managed Page through this public API are rejected. For both new and updated Pages, perform **prepare -> send ZIP -> deploy** in that order.

## 1. Prepare a ZIP upload

For a new Page, use `POST /upload-url`; to update an existing Page, use `POST /{pageId}/upload-url`. The body is strictly validated, so do not send fields not listed in the table. Preparation for an update also checks whether you can update the specified Page.

| Name | Location | Required | Default and limits | Meaning |
|---|---|---:|---|---|
| `fileName` | body | Yes | 1-255 characters after leading and trailing whitespace is removed | Filename of the ZIP to upload. |
| `contentType` | body | No | `application/zip` when omitted, 1-255 characters | Content-Type used when sending the ZIP. |
| `pageId` | path | Only when updating | Nonempty string | ID of the Page to update. |

```bash
curl -sS -X POST 'https://api.japan-ai.co.jp/api/v1/pages/upload-url' \
  -H "x-api-key: $JAI_STUDIO_API_KEY" \
  -H 'content-type: application/json' \
  --data '{"fileName":"page-release.zip","contentType":"application/zip"}'
```

On success, the response is 200 and returns `uploadUrl`, `uploadPath`, `expiresAt`, and `requiredHeaders`. `uploadPath` is an opaque value to pass unchanged to the deployment body, and `requiredHeaders` are headers to use when sending the ZIP.

Send the ZIP body to `uploadUrl` with `PUT`. Preserve the contents of `requiredHeaders` when sending it.

```bash
curl -sS -X PUT "$UPLOAD_URL" \
  -H 'Content-Type: application/zip' \
  --upload-file "$PAGE_ZIP"
```

For ZIP size and structure conditions, see [Page Specifications and Limits](../build/page-specs-and-limits.md).

> An `uploadPath` is consumed by one deployment. Do not resend the same value to attempt another deployment. It returns 400 if expired, used with another Project or key, or already consumed. Do not unconditionally repeat the same operation after a communication failure.

## 2. Deploy a new Page or update

After sending the ZIP, call `POST /deploy` to create a new Page or `POST /{pageId}/deploy` to update one. For creation, you may omit `name`. Do not include `name` in an update body.

| Name | Location | New | Update | Limits and meaning |
|---|---|---:|---:|---|
| `pageId` | path | — | Required | ID of the Page to update. |
| `uploadPath` | body | Required | Required | At least 1 character after leading and trailing whitespace is removed. Value from the preparation response. |
| `zipFileName` | body | Required | Required | 1-255 characters after leading and trailing whitespace is removed. ZIP name. |
| `name` | body | Optional | — | 1-255 characters after leading and trailing whitespace is removed. Display name of the new Page. |

```bash
# New Page
curl -sS -X POST 'https://api.japan-ai.co.jp/api/v1/pages/deploy' \
  -H "x-api-key: $JAI_STUDIO_API_KEY" -H 'content-type: application/json' \
  --data '{"uploadPath":"<uploadPath>","zipFileName":"page-release.zip","name":"Order dashboard"}'

# New version of an existing Page
curl -sS -X POST "https://api.japan-ai.co.jp/api/v1/pages/$PAGE_ID/deploy" \
  -H "x-api-key: $JAI_STUDIO_API_KEY" -H 'content-type: application/json' \
  --data '{"uploadPath":"<uploadPath>","zipFileName":"page-release.zip"}'
```

Applying the ZIP does not change access settings. Check sharing scope, public URL, and users in "Access settings" in the Studio UI.

## Deployment response and verification

On successful creation or update deployment, the response is 200 and has the same format.

| Field | Details |
|---|---|
| `pageId` | Created or updated Page ID. |
| `studioUrl` | URL to check the target Page in Studio. |
| `runtimeUrl` | Runtime URL. `null` if it does not exist. |
| `version` | Deployed version. |
| `fileCount` | Number of registered files. |
| `createdAt` | String representing when the deployment result was created. |
| `rebuildTriggered` | Boolean indicating whether the Page rebuild request succeeded. |

Do not infer availability even if `runtimeUrl` is `null` or `rebuildTriggered` is `false`. Use `studioUrl` to check the target Page and the applied version, then verify the initial display and representative operations under the configured access conditions. For the CLI flow, see [CLI Pages](../cli/cli-pages.md).

## Errors and usage controls

Invalid input; invalid, expired, or consumed `uploadPath`; and ZIP format, size, or structure problems return 400. A failure to meet the key, `page:write`, Project operation permission, Page availability conditions, or Page count limit returns 403; a missing update target returns 404. The API applies usage controls to upload preparation and deployment and also controls concurrent deployment. Reaching a limit returns 429.

On 429, do not repeatedly retry; reduce the number of concurrent deployments and request frequency. There are two types of 429. Common API usage controls return the `Retry-After` header and body `retryAfterSeconds`, so follow them. Page-specific controls (preparation and deployment frequency, number of concurrent deployments, unused upload capacity, and update conflicts for the same Page) do not return wait seconds and are distinguished by the body `code`. An update conflict also returns 429 rather than 409. In either case, do not repeatedly retry; space out attempts. For general handling, see [Common API and SDK Errors, Timeouts, and Limits](../development/api-sdk-errors-timeouts-limits.md).

| Situation | What to check and do |
|---|---|
| 400 | Check `fileName`, `contentType`, `uploadPath`, ZIP name, and ZIP size and structure. Check whether you need to prepare the upload again. |
| 403 | Check the API key, `page:write`, target Project, Page availability conditions, and operation permission. Do not add scopes based on guesswork. |
| 404 | Check `pageId` and the target Project. Do not use an ID from another Project. |
| 429 | Do not repeatedly send the same ZIP or deployment. Reduce concurrency and frequency before retrying. If `Retry-After` exists, follow its seconds. |
| Timeout or communication failure | Check the current state using the new Page, Page version, and `studioUrl` before deciding the next operation. |

## Checklist

- [ ] Prepared a secure Project API key and `page:write` for the target Project
- [ ] Confirmed that the completed ZIP meets the size, structure, and secret-information conditions in [Page Specifications and Limits](../build/page-specs-and-limits.md)
- [ ] Performed preparation, ZIP `PUT`, and deployment in order, without modifying or reusing `uploadPath`
- [ ] Used the correct paths and `pageId` for new creation and existing Page updates
- [ ] Checked `studioUrl`, `runtimeUrl`, version, and rebuild result
- [ ] Understand that access mode and share URL cannot be changed by the API, and checked them in "Access settings" in the Studio UI
- [ ] Did not repeatedly retry deployment after 429, timeout, or communication failure without checking state

## Next article

- [CLI Pages](../cli/cli-pages.md)
- [SDK Pages and Platform](../development/sdk-pages-and-platform.md)
- [Page Specifications and Limits](../build/page-specs-and-limits.md)
- [Common API and SDK Errors, Timeouts, and Limits](../development/api-sdk-errors-timeouts-limits.md)
