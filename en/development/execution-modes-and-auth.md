# Execution Modes, Authentication, and Supported Scope

Code that uses the Studio SDK has different authentication information, available modules, and supported scope depending on where it runs. Developers must not mix local Node.js, a browser within PRS (Pages Rendering Service), and a server runtime within PRS; initialize the SDK client for each execution location.

For Project API key issuance, revocation, and scope, see [Manage Project API Keys](../operations/manage-project-api-keys.md). [Agents, Conversations, and Artifacts API](../api/agents-conversations-artifacts-api.md) covers API invocation specifications, and [Hosted Page Authentication and API Routes](../page-runtime/hosted-page-auth-and-api-routes.md) covers Hosted Page authentication paths.

## Related articles

- [Manage Project API Keys](../operations/manage-project-api-keys.md)
- [Agents, Conversations, and Artifacts API](../api/agents-conversations-artifacts-api.md)
- [Hosted Page Authentication and API Routes](../page-runtime/hosted-page-auth-and-api-routes.md)

## Before you begin

- Decide whether the code you will run is in a developer's local Node.js, a Page user's browser, or PRS server processing. Even in the same repository, separate browser and server settings.
- Node.js 22 or later is verified for the Node SDK and CLI. Running the SDK in a normal external browser is out of scope; browser support is limited to code within PRS.
- Treat Project API keys as confidential information. Do not leave values, environment variables containing values, or unmasked configuration output in tickets, logs, or source control.

> An API accepting a valid Project API key is separate from arbitrary external production hosting or external integration being supported. During the limited beta, local development or explicitly managed development automation, and Pages hosted on PRS, are assumed.

## Choose an execution mode

| Execution mode | Where it runs | Authentication | Available modules | Uses and out-of-scope items |
|---|---|---|---|---|
| Local (direct development) | Node.js 22 or later in a development environment or managed development automation | A developer-managed Project API key | `agents`, `datasets`, `members`, `objects`, `scheduledTasks`, `storage`, `workflows`. Use the CLI for Page operations. | For local development. Arbitrary external production hosting is not supported. |
| PRS browser | The browser that opens a Page hosted on PRS | PRS session. Do not use an API key. | `agents`, `datasets`, `objects`, `storage`, `platform` | Screens for Page users. `members`, `workflows`, `scheduledTasks`, and Page operations are out of scope in this mode. |
| PRS server | A server-only Node.js 22 or later runtime within PRS | A Project API key that the platform injects into server-only settings | `agents`, `datasets`, `members`, `objects`, `storage`, `workflows` | Server processing for Hosted Pages. It is not an authentication method for external integration, and `platform`, `scheduledTasks`, and Page deployment operations are out of scope. |

`storage.downloadFolder()` is unavailable in the PRS browser. Do not work around this by passing an API key; reconsider the design.

## Authentication boundaries for datasets and scheduled tasks

The SDK's `datasets` module is read-only (`list()` and `search()`). For the public API, see [Datasets API](../api/datasets-api.md). You can use `datasets.list({ scope: "project" })` with a Project API key or PRS session, but only a **signed-in PRS browser user** who can read organization datasets not linked to the Project can specify `scope: "organization"`. Specifying `organization` with a Project API key returns 403. `search()` searches only datasets linked to the Project or organization datasets the user can read.

Use `scheduledTasks` only with a developer-managed Project API key. Because an App release manifest cannot declare the `task:*` scope, you cannot work around this by moving it to a platform-managed key on the PRS server. Each task runs as the key's creator and fails if the creator lacks the permissions required at execution time.

## 1. Use the Project API key only on the server side locally

Initialize local Node.js code with a Project API key and the API base URL. Rather than writing the key directly in the shell, prefer passing it from secret management to an environment variable. `baseUrl` is required in the SDK client configuration and `apiKey` is optional. However, pass a Project API key to authenticate access to Studio resources in direct local development.

```ts
import { createClient } from "@japan-ai-inc/studio-sdk";

const apiKey = process.env["JAI_STUDIO_API_KEY"];
if (!apiKey) throw new Error("Project API key is not configured.");

const studio = createClient({
  baseUrl: "https://api.japan-ai.co.jp",
  apiKey,
});

const agents = await studio.agents.list();
```

The CLI and SDK README describe Project API keys as beginning with `pak_`. A key shown by `jai-studio config show` is masked, displaying only the first 8 and last 4 characters. If you pass this displayed value to the SDK, the SDK treats it as masked and errors. Do not try to restore it by copying the value from the settings screen; pass the stored actual value from secure configuration.

> Do not put API keys in browser bundles, client-side JavaScript, environment variables exposed to browsers, logs, or source control. Do not set API keys or resource IDs from the source Project in `NEXT_PUBLIC_*`, `VITE_*`, `REACT_APP_*`, or `PUBLIC_*`. Obfuscating a value does not make it secret when it is distributed to browsers.

## 2. Do not configure a key in the PRS browser

Browser code for a Page running on PRS is authenticated by the current PRS session. Do not configure, read, or transfer a Project API key; initialize it with an empty `baseUrl`.

```ts
import { createClient } from "@japan-ai-inc/studio-sdk";

const studio = createClient({ baseUrl: "" });
const result = await studio.agents.invoke("agent-id", { prompt: "Hello" });
```

The SDK checks whether `window.JapanAI.getContext` exists in the running browser. When this detects PRS, `agents.invoke()` and stream calls use the PRS `/api/agent-chat` route rather than the normal API route. The PRS session cookie and Page information are used for authentication and route selection, so you do not need to add a key in the browser.

`platform` is a beta module for the PRS browser only. You can check availability with `studio.platform.isAvailable()` and use `studio.platform.waitForReady()` if necessary. Context cannot be obtained outside PRS.

## 3. Keep the platform-managed key inside the PRS server

PRS may inject a platform-managed Project API key into a Hosted Page's server-only runtime settings. Read the value only in server code and initialize it as a normal Node client.

```ts
import { createClient } from "@japan-ai-inc/studio-sdk";

const apiKey = process.env["JAI_PROJECT_API_KEY"];
if (!apiKey) throw new Error("The PRS server runtime has no API key.");

const studio = createClient({
  baseUrl: "https://api.japan-ai.co.jp",
  apiKey,
});
```

PRS hosting manages this key's lifecycle. Do not include or transfer the key to runtime-setting endpoints, API responses, screen initialization data, browser environment variables, or the browser. When handling external-service credentials as well, check a design that does not return them to browsers and Secret handling in [Page Server Processing, Environment Variables, and Secret References](page-server-env-and-secrets.md).

## Common misunderstandings

### If I pass an API key from the browser, I can use the same modules as locally

You cannot. The PRS browser is a user-facing mode based on the PRS session, and the supported modules differ. Adding a key only exposes confidential information; it does not make an out-of-scope execution form supported. Place required processing on the PRS server or align the design with supported modules.

### `createClient()` automatically switches every mode

The execution mode is determined by code placement, `baseUrl`, and the presence and storage location of a key. PRS detection is performed by features that use the browser's `window.JapanAI.getContext`.

### I can put a PRS server key in settings for screen initialization

You must not. A key injected into a server-only runtime is also a Project API key. Responses that reach the browser, HTML, JavaScript, source maps, and logs are not places to store confidential information.

## If something does not work

| Situation | What to check and do |
|---|---|
| Authentication fails locally | Check the target Project, key expiry or revocation, required scopes, and that you are passing the actual value. Do not use the masked display; issue or switch safely according to [Manage Project API Keys](../operations/manage-project-api-keys.md). |
| The SDK does not work as expected in a Page browser | Check that it is running within PRS, is initialized with `baseUrl: ""`, and browser code does not include a key or server-only settings. |
| You want to use `members` or `workflows` from the browser | Check the table for whether the module is available in the PRS browser. Rather than passing a key to the browser, consider whether it can be processed on the PRS server. |
| You are trying to use `platform` locally | `platform` is for the PRS browser only. Check `isAvailable()` and implement alternative screen processing outside PRS. |
| A key cannot be found on the PRS server | Check that the execution location is a server-only runtime and that required hosting settings exist. Do not embed a placeholder key on the screen; check with the administrator. |

For an inquiry, record the target Project, execution mode, SDK version, execution location, occurrence time, full error excluding keys and tokens, and reproduction steps. Do not attach Project API keys, cookies, Secret values, or authentication headers.

## Checklist

- [ ] I classified the execution location as local, PRS browser, or PRS server
- [ ] I use only modules supported by the mode
- [ ] I limited local and PRS-server keys to secure server-side settings
- [ ] I initialized the PRS browser with `createClient({ baseUrl: "" })` and do not read a key
- [ ] I did not include keys in browser bundles, public environment variables, logs, or source control
- [ ] I checked API key issuance, scopes, and revocation in [Manage Project API Keys](../operations/manage-project-api-keys.md)

## Read next

- [Development Environment, Versions, and Compatibility](dev-environment-and-versions.md)
- [Hosted Page Authentication and API Routes](../page-runtime/hosted-page-auth-and-api-routes.md)
- [Page Server Processing, Environment Variables, and Secret References](page-server-env-and-secrets.md)
