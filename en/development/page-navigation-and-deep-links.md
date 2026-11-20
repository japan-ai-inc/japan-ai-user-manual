# Page Navigation and Deep Links

Within Studio and with "Sign-in required," a Page is displayed from `/app/{organization ID}/{Project ID}/{Page ID}` on the Page host. The Public URL shared for "Sign-in optional" and "Anonymous only" is in `/p/{token}` format. Design navigation within the Page so the application handles the path, query string, and fragment that follow this base URL. For example, to share a URL that opens a detail screen directly, append a screen state such as `/orders/42?tab=history#notes` after the Page base URL.

Static Pages have an SPA fallback, so opening a deep path directly returns the base HTML, and `<base href>` is inserted automatically. For Pages that run as Node/Bun servers, the response to deep paths is determined by your server's routing. In either case, verify direct access, reloads, and return after sign-in on the registered Page.

## Related articles

- [Page Source Structure and Runtime](page-source-structure-and-runtime.md)
- [Page Build, Start, or Display Failures](../troubleshooting/page-build-start-display-failures.md)
- [Register and Publish Pages from the Screen](../build/register-and-publish-pages.md)
- [Page Specifications and Limits](../build/page-specs-and-limits.md)

## Before you begin

- Confirm the target organization, Project, Page, access mode, and the first screen to guide users to. A Page ID is the identifier for each Page, not its display name.
- For each screen that users should open directly, decide whether to place state in the path, query string, or fragment. Do not include sensitive information, such as search criteria, the item being edited, or a return destination, in a URL. URLs may remain in history, shares, access logs, Referer headers, and elsewhere.
- Review Page links and assets such as JavaScript, CSS, and images to ensure they do not use root-fixed local paths beginning with `/`. They may fail to load after registration if the Page base subpath is lost.

> Do not write the organization ID, Project ID, Page ID, or Page host placed after `/app/` as fixed values in source code. They will no longer match when you register the Page in another Project, recreate the Page, or change the distribution destination. Also, links built in `/app/...` format for "Sign-in optional" or "Anonymous only" users do not work. To share, use the "Public URL" (`/p/{token}`) displayed in "Access settings."

## URL structure and path design

The Page base URL within Studio and with "Sign-in required" has the following format. The Page host name is determined by organization and environment settings, so this article does not fix an example host name.

```text
https://{Page host}/app/{organization ID}/{Project ID}/{Page ID}
```

The Public URL for "Sign-in optional" and "Anonymous only" is in `/p/{token}` format. Do not build this URL yourself; use the value displayed in "Access settings."

Use what follows the base URL as application-specific routes within the Page.

| Purpose | URL example | Handling |
|---|---|---|
| Page base URL | `/app/{organization ID}/{Project ID}/{Page ID}` | Displays the initial screen. |
| Detail screen | `…/{Page ID}/orders/42` | Handles `orders/42` as an application-relative route. |
| Search and display conditions | `…/orders?status=open&sort=created` | Reads the query string and validates value formats and permissions. |
| Position within the same screen | `…/orders/42#notes` | Uses the fragment for position or state in the browser. Do not assume it is sent to the server. |

Make asset and navigation references relative to the current Page base URL whenever possible. Avoid absolute paths to the host root such as `/assets/app.js`, because they lose the Page base URL. For static Pages, `<base href>` is inserted automatically, so relative URLs resolve against the Page base URL. If your framework requires a `basePath` setting, verify that setting using the actual URL after registration.

## 1. Design for opening deep screens

1. Design screen names and required identifiers as relative paths based on the base URL. Example: `orders/42`.
2. For internal Page links, choose a method that does not leave the runtime Page base URL. Even when using a router, verify on the registered Page URL rather than on the development server route.
3. Before putting search criteria or similar data into a query string, URL-encode it and validate input values. Do not determine access only by ID; also verify permissions in the API or processing called by the Page.
4. After registering the ZIP, navigate from the Page base URL and open the same deep URL directly in another tab. Then reload the browser.

> If direct access or reload results in a 404, blank screen, or failed asset load, do not publish based on assumptions. Check the framework's `basePath` setting, asset reference paths, and (for Pages with a server) server-side routing.

## 2. Verify return with "Sign-in required"

"Sign-in required" in "Access settings" is for users who are signed in and have permission to access the Page. When an unsigned-in user reaches the correct `/app/{organization ID}/{Project ID}/{Page ID}/…` URL on the Page host, authentication treats that URL as the return destination. The return destination is verified to be the same origin as the Page host and a path for the same organization, Project, and Page.

After authentication, the user returns with authentication information added while retaining the query string already present in the return destination. Therefore, include at least the following in acceptance testing for URLs with deep paths, query strings, and fragments.

1. In a signed-out browser, open a deep screen URL directly.
2. Complete normal sign-in. If the flow moves to an organization-specific sign-in screen, also verify that the return destination is not lost.
3. Verify that you return to the original deep screen, query string, and fragment.
4. Verify that users without permission for the target organization, Project, and Page cannot open the screen.

Do not leave authentication values themselves in URLs, screens, logs, or inquiries. Return destinations that include a host other than the Page host, another Page, or control characters are not accepted. For this reason, do not design the Page to navigate to an arbitrary external URL.

## 3. Return on authentication expiry and SDK use

If a Page displayed in Studio requires authentication renewal, Studio obtains new authentication information and reloads the Page. Within the same origin of the same Page, it returns to the path, query string, and fragment passed by the Page. The same renewal method does not necessarily work for externally shared URLs, direct access, or custom authentication methods.

To open a Studio Task from browser code in a Page, you can use the beta `platform` module in the released SDK 0.11.0. Use Node.js 22 or later in the development environment that installs and builds the SDK. This is browser-only within the Page runtime. It works only when embedded in JAPAN AI; when the Page is opened on its own, it is rejected with `UNSUPPORTED`. Check availability with the result of `waitForReady()` before calling it.

```ts
import { createClient } from "@japan-ai-inc/studio-sdk";

const studio = createClient({ baseUrl: "" });
const context = await studio.platform.waitForReady();

if (context) {
  const result = await studio.platform.openTask(
    [{ orderId: "42" }],
    "Please review this order"
  );
  console.log(result.roomId);
}
```

`openTask()` is not an API for navigation to arbitrary URLs. Do not use it for navigation within a Page, moving to an external site, or specifying an authentication return destination. `platform` is beta and depends on compatibility with the Page runtime. Pin an exact version such as `@japan-ai-inc/studio-sdk@0.11.0` and verify on a registered Page.

## Checklist

- [ ] Designed screen routes after the Page base URL `/app/{organization ID}/{Project ID}/{Page ID}`
- [ ] Shared the `/p/{token}` Public URL with anonymous and sign-in-optional users instead of building `/app/...` links
- [ ] Did not fix the organization ID, Project ID, Page ID, or Page host in the output
- [ ] Made assets and internal links load from the subpath after registration
- [ ] Verified direct access and reload for deep URLs on a registered Page
- [ ] Verified that paths, query strings, and fragments return after sign-in with "Sign-in required"
- [ ] Did not leave tokens, personal information, or confidential information in URLs, logs, or inquiries
- [ ] When using SDK `platform`, verified availability within the Page runtime and the pinned version

## If something does not work

"Sign-in optional" and "Anonymous only" are opened from the Public URL (`/p/{token}`), so their entry point differs from "Sign-in required." For these modes and "External customer auth," verify direct access and reloads starting from each Public URL or Access URL.

If direct access, reload, or authentication return fails, record the target Project and Page name, access mode, SDK version, failing path (excluding authentication values), query key names, occurrence time, reproduction steps, and browser network failure. Do not share authentication information or Cookie values. For isolating build, start, and display issues, see [Page Build, Start, or Display Failures](../troubleshooting/page-build-start-display-failures.md).

## Next article

- [Page Source Structure and Runtime](page-source-structure-and-runtime.md)
- [Page Build, Start, or Display Failures](../troubleshooting/page-build-start-display-failures.md)
- [Register and Publish Pages from the Screen](../build/register-and-publish-pages.md)
- [Page Specifications and Limits](../build/page-specs-and-limits.md)
