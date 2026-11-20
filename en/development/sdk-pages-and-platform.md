# SDK Pages and Platform

Studio SDK's `pages` and `platform` have different purposes and run locations. `pages` is a client for registering and deploying ZIP files locally or through managed development automation. `platform` is a beta feature for interacting with context, Studio Tasks, and Page sessions **from within the browser** of a Studio-hosted Page (a Page displayed by the Page runtime).

For deployment, access settings, and Page integration, choose the path that fits your purpose.

## Related articles

- [Execution Modes, Authentication, and Supported Scope](execution-modes-and-auth.md)
- [Preview a Page Locally and Deploy It](local-preview-and-deploy-page.md)
- [Page Navigation and Deep Links](page-navigation-and-deep-links.md)
- [Register and Publish a Page from the Screen](../build/register-and-publish-pages.md)
- [Page Specifications and Limits](../build/page-specs-and-limits.md)
- [CLI Pages](../cli/cli-pages.md)
- [Pages API](../api/pages-api.md)
- [Integrating a Page with Chat and Context](../page-runtime/page-chat-and-context.md)

## Before you begin

- Use Node.js 22 or later with SDK **0.11.0**. Use `platform` within a Page hosted by the Page runtime.
- Use `pages` or the CLI to register and update ZIP files, and `platform` for integration.
- Restrict API keys to secure settings. Do not put them in browser JavaScript, public environment variables, or logs.

> `pages` and `platform` are not APIs for changing users or "Access Settings." Check access modes and shared URLs in the target Page's "Access Settings."

## Choose a role

| Purpose | Use | Run location | Authentication and notes |
|---|---|---|---|
| Register a new Page or a version of an existing Page from a ZIP file | `pages` or `jai-studio pages deploy` | Local Node.js or managed development automation | Use the Project API key only on the server side. The SDK's `pages` provides upload preparation, ZIP transfer, and deployment. |
| Get a list of Pages | Check in the Studio screen | Studio | SDK 0.11.0 `pages` has no method to get a list. |
| Configure a Page's access mode or public URL | "Access Settings" | Studio | SDK 0.11.0 `pages` and `platform` have no methods for changing access control. Follow [Register and Publish a Page from the Screen](../build/register-and-publish-pages.md). |
| Identify the user, Project, and Page of the Page being displayed | `platform.getContext()` or `waitForReady()` | Browser in the Page runtime | Uses the Page runtime session. No API key or `baseUrl` is required. |
| Pass data from a Page to the Studio Tasks screen or end a Page session | `platform.openTask()`, `platform.logout()` | Browser in the Page runtime | Beta features exclusive to the Page runtime. They are not available outside a Page or in a server runtime. |

`platform` is not an API for navigating to arbitrary URLs. For deep links, follow [Page Navigation and Deep Links](page-navigation-and-deep-links.md).

## 1. Deploy with `pages` or the CLI

The SDK's `pages` uses three steps: `prepareUpload()` (`prepareVersionUpload(pageId)` for an existing Page), `uploadZip()`, and `deployUploadedZip()`. You can pass a `Blob`, `ArrayBuffer`, `Uint8Array`, or `ReadableStream` as the ZIP itself. The default timeout is 5 minutes for ZIP transfer and 10 minutes for deployment. The client-wide `timeoutMs` passed to `createClient()` does not apply to `pages`.

Put the **Page source** in the ZIP, not built artifacts (at the root, `package.json` and a lockfile, or `index.html` for a static Page). After upload, Studio runs install and `build`. Uploading Next.js build output is rejected. For details, see [Page Specifications and Limits](../build/page-specs-and-limits.md).

```ts
import { createClient } from "@japan-ai-inc/studio-sdk";

const studio = createClient({
  baseUrl: "https://api.japan-ai.co.jp",
  apiKey: process.env["JAI_STUDIO_API_KEY"],
});

const bytes = new Uint8Array(await readPageSourceZip()); // ZIP containing the Page source
const upload = await studio.pages.prepareUpload({
  fileName: "page-release.zip",
  contentType: "application/zip",
});
await studio.pages.uploadZip(upload, bytes);

const deployed = await studio.pages.deployUploadedZip({
  uploadPath: upload.uploadPath,
  zipFileName: "page-release.zip",
  name: "Order dashboard",
});
console.log(deployed.pageId, deployed.studioUrl, deployed.runtimeUrl);
```

To update an existing Page, pass the same `pageId`. The result includes the Page ID, Studio URL, runtime URL, version, file count, date and time, and whether a rebuild is required. You can perform the same flow with the CLI.

```bash
jai-studio --profile my-project pages deploy ./my-page \
  --name "Order dashboard" --json
```

The CLI supports `--page-id`, `--timeout-ms`, and `--save-page-metadata`. For directory input, the CLI creates a ZIP and uses the Page ID as the update target when one is provided. The CLI writes Page metadata inside the specified directory, so if the directory is emptied (for example, by a rebuild), the next deployment without `--page-id` creates a new Page (with a new ID and URL). Scripts that update an existing Page should always specify `--page-id`. For details, see [Preview a Page Locally and Deploy It](local-preview-and-deploy-page.md).

> With the default access setting (sign-in required), `runtimeUrl` is always `null`. Check the target Page from `studioUrl`. For ZIP structure and capacity, see [Page Specifications and Limits](../build/page-specs-and-limits.md).

## 2. Initialize `platform` in the Page runtime

In the Page browser, create it with an empty `baseUrl`. `isAvailable()` checks availability, and `getContext()` returns `null` when unavailable. `waitForReady()` waits 5,000 milliseconds by default and also returns `null` on timeout.

```ts
import { createClient } from "@japan-ai-inc/studio-sdk";

const studio = createClient({ baseUrl: "" });
const context = await studio.platform.waitForReady();

if (!context) {
  showUnavailableMessage();
} else {
  showCurrentUser(context.userName);
  const result = await studio.platform.openTask(
    [{ orderId: "42" }],
    "Please review this order"
  );
  // On failure, an object with code is returned instead of an exception.
  if ("code" in result) showTaskError(result.code);
  else console.log(result.roomId);
}
```

The context includes identifiers for the user, organization, Project, Page, and Custom Object. `openTask()` passes data and optional instructions to a Studio Task and returns `roomId`. `openTask()` works only when the Page is embedded in the JAPAN AI screen; when the Page is opened on its own, it is rejected with `UNSUPPORTED`. `logout()` ends the current Page session target and reloads the Page.

For Agent invocation, prioritize `studio.agents.invoke()`.

### Open App settings

In SDK 0.7.0 or later, you can use `platform.openAppSettings(tab?)` to open the settings of an App installed on this Page. Available tabs are `"basic"`, `"members"`, `"iamRoles"`, `"connectors"`, `"secrets"`, and `"dataIntegration"`. Unavailable tabs are hidden, so a different tab may open.

```ts
await studio.platform.openAppSettings("connectors");
```

The call fails outside the Page of an installed App. Even if `isAvailable()` is `true`, check that you use SDK 0.7.0 or later and that the Page belongs to an installed App. Failures of `platform` methods are thrown as ordinary `Error` objects, not `SdkApiError`.

## Common misconceptions

### You can change a Page's visibility with `platform`

You cannot. `platform` is a browser module for connecting a Studio-hosted Page with Studio features. Check access modes and shared URLs in "Access Settings."

### You can deploy with `pages` in the Page browser

Do not do this. `pages` is for development and control operations, not a module available in the Page browser. Do not design Page code to update itself or another Page; deploy from local or managed development automation.

### If `platform` is unavailable, you can add an API key to the browser

Do not do this. Branch with `isAvailable()` or `waitForReady()`, and do not pass keys to the browser.

## If something does not work

| Situation | Check and action |
|---|---|
| Deployment with `pages` fails | Check the ZIP, target Project, Page ID, and authentication settings. |
| You are looking for SDK methods for a Page list or access settings | `pages` has no corresponding methods. Use the Studio Page list and "Access Settings." |
| `waitForReady()` returns `null` | Check whether it is running in a browser within a Studio-hosted Page and the timing of execution. |
| `openTask()` returns `UNSUPPORTED` | Check that the Page is opened embedded in the JAPAN AI screen. It cannot be used in a Page opened on its own. |
| `logout()` fails | Check that it runs in the browser of a Studio-hosted Page. |
| `openAppSettings()` fails | Check that it runs with SDK 0.7.0 or later in the Page of an installed App. |

For an inquiry, record the SDK version, execution mode, target Page, date and time of occurrence, error, and steps to reproduce. Do not attach API keys, cookies, or Secret values.

## Checklist

- [ ] I separated deployment operations from in-Page integration.
- [ ] I registered and updated ZIP files locally or through managed development automation.
- [ ] I checked the Page list and access mode in Studio.
- [ ] In the Page browser, I use `createClient({ baseUrl: "" })` and do not read an API key.
- [ ] I checked availability or readiness before calling `platform`.
- [ ] I do not use `openTask()` to navigate to arbitrary URLs.
- [ ] When using `openAppSettings()`, I checked SDK 0.7.0 or later and that the Page belongs to an installed App.

## Next article

- [Preview a Page Locally and Deploy It](local-preview-and-deploy-page.md)
- [Page Navigation and Deep Links](page-navigation-and-deep-links.md)
- [Register and Publish a Page from the Screen](../build/register-and-publish-pages.md)
- [CLI Pages](../cli/cli-pages.md)
- [Pages API](../api/pages-api.md)
- [Integrating a Page with Chat and Context](../page-runtime/page-chat-and-context.md)
