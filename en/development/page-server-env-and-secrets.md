# Page Server Processing, Environment Variables, and Secret References

When using an external API token or other secret in a Page, design the Page to read it **only on the Page server side**, not place it in browser JavaScript, HTML, initial data, logs, or source maps. Do not include the actual value of a Project Secret in a ZIP or send it directly to an external service from a user's browser.

Pages hosted in Studio run install and `build` from the uploaded source and run as Node/Bun servers (except static Pages). Server processing can read environment variables and Project Secret references written in the root `.env` from `process.env`.

## Key points

| Item | Direct Page upload (screen or CLI) | App publishing and installation |
|---|---|---|
| `.env` files read | Only the root `.env` and `.env.production` (other `.env.*` files are ignored) | The root `.env` and `.env.<name>` (`.env.local` files and similar are excluded) |
| Resolving `${secret:name}` | Resolved **by name as is** to the target Project's Project Secret. There is no binding screen | Bound in the installing Project with "Select secret" |
| Secret reference in a public-prefix key (such as `NEXT_PUBLIC_`) | Not rejected. Developers must avoid it | Rejected |
| Reserved keys | `PORT`, `NODE_ENV`, `PRS_*`, and similar keys are excluded | Same as left |
| Value limit | A Project Secret can be up to 8,192 characters, but a value passed as an environment variable can be up to 4,096 bytes | Same as left |

The server listens on `process.env.PORT`. Paths such as `/api/v1/*` and `/api/storage` are used by the platform, so do not use them for your own API routes. The first SSR response has a limit of about 60 seconds.

## Related articles

- [Register, Update, and Delete Project Secrets](../operations/manage-project-secrets.md)
- [Page Specifications and Limits](../build/page-specs-and-limits.md)
- [Execution Modes, Authentication, and Supported Scope](execution-modes-and-auth.md)
- [Page Source Structure and Runtime](page-source-structure-and-runtime.md)

## Before you begin

- Register the actual Secret value in **"Project secrets"** for the target Project. Use [Register, Update, and Delete Project Secrets](../operations/manage-project-secrets.md) as the source of truth for creation, updates, deletion, name and value input rules, permissions, and rotation.
- A Project Secret name must begin with a lowercase English letter and thereafter use only lowercase English letters, numbers, and underscores, with a maximum of **64 characters**. An empty string and UUID format cannot be used. For example, `external_api_token` can be used. A value cannot be empty and has a maximum of **8,192 characters**. However, a value passed to a Page as an environment variable can be at most **4,096 bytes**. Longer values can be saved but cause the Page to fail at startup.
- Clarify why server processing is necessary. Place only processing that requires a secret value on the server side, such as external API calls, signature generation, and data retrieval with a token. The browser sends only the minimum necessary input to server processing and receives only results that do not contain the Secret.
- Environment variable names beginning with `NEXT_PUBLIC_`, `VITE_`, `REACT_APP_`, or `PUBLIC_` may be embedded into browser code at build time. Do not use them for Project Secret references.

> A Secret name is an identifier that indicates its purpose, not its value. Names such as `external_api_token` may be referenced in configuration and diagnostics, but do not write token contents, API keys, Cookies, or authentication headers in names, error displays, or inquiries.

## 1. Declare a Secret reference in `.env`

Write the following exact reference format rather than the actual Secret value. The left-hand side is the environment variable name read on the server side.

```dotenv
EXTERNAL_SERVICE_TOKEN=${secret:external_api_token}
```

An environment variable name begins with a letter or underscore and thereafter uses letters, numbers, and underscores. Environment variable expansion such as `$VAR` or `${VAR}` inside values is not supported. Rather than combining a Secret reference with another environment variable, register the required value as a single Secret.

Reserved keys such as `PORT`, `NODE_ENV`, and `PRS_*` are excluded even if written in `.env`.

### When uploading a Page directly

When you upload a Page directly from the screen or CLI, only the root `.env` and `.env.production` are read. Other `.env.*` files (such as `.env.local` and `.env.staging`) are ignored.

`${secret:name}` is resolved by name as is to the target Project's Project Secret. There is no binding screen. Register a Secret with the same name in the target Project.

With direct upload, a Secret reference in a public-prefix key is not rejected. Developers themselves must ensure that they do not write Secret references in keys such as `NEXT_PUBLIC_`.

### When publishing and installing as an App

App publishing and installation read the root `.env` and `.env.<name>`. `.env.local`, `.env.local.<name>`, and files ending in `.local` are excluded. `.env.example`, `.env.sample`, and `.env.template` are also not used. If there are multiple files, `.env` is processed first, and `.env.<name>` later overwrites the same keys.

In the App flow, release fails if a Secret name does not follow the Project Secret naming rules or if a Secret reference is written in an environment variable name with a public prefix. If the installing Project shows required Secrets, bind the target Secret with "Select secret." For details, see [Design the Configuration of a Distributable Page](design-distributable-page-config.md).

> Do not write a token string received from the issuer in `.env` instead of `${secret:external_api_token}`. The actual value would remain in the uploaded source and could be exposed in distributions, history, and logs.

## 2. Read the value only in server processing

In Node.js code running on the server, read the environment variable by the declared left-hand-side name. The following example queries an external service and returns only the processing result to the browser. Place the server-processing entry point according to the framework you use.

```ts
export async function loadItems() {
  const token = process.env["EXTERNAL_SERVICE_TOKEN"];
  if (!token) throw new Error("External service settings are missing");

  const response = await fetch("https://example.invalid/v1/items", {
    headers: { Authorization: `Bearer ${token}` },
  });
  if (!response.ok) throw new Error("Failed to connect to the external service");

  const data = await response.json();
  return data.items;
}
```

The browser does not receive `token`, values from `process.env`, or the external service's authentication header. Limit values returned from server processing to the fields needed by the screen. When returning an exception to the browser, do not return the original API error or request settings as is; use a message that does not contain Secret values, Authorization headers, or connection strings.

An environment variable for a Project API key, such as `JAI_PROJECT_API_KEY`, is not injected automatically. When using Studio APIs in server processing, the developer chooses the variable name in the root `.env`. When the App is published, that value is replaced with a platform-managed key.

Avoid the following implementation.

```ts
// Example of placing a Secret in browser code or configuration. Do not use.
const token = process.env.NEXT_PUBLIC_EXTERNAL_SERVICE_TOKEN;
window.runtimeConfig = { token };
```

Build tools that use browser-public prefixes embed values into the client bundle at build time. **Check artifacts before and after the build, and as the developer, ensure that actual values are not included** in custom build settings, generated JavaScript, HTML, JSON, or source maps.

## 3. Conditions to meet at publication

To publish or enable a Page that contains Secret references, at least the following must be met.

| Condition | What to check |
|---|---|
| Feature availability | The Project Secret feature is available in the organization. In organizations where it is not available, releases containing Secret references cannot proceed. |
| Correct Project | The Secret is registered in the target Project where the Page is enabled. A value with the same name in another Project cannot be substituted. |
| Reference declaration | The `.env` that is read (`.env` or `.env.production` for direct upload) contains the server key and `${secret:SecretName}` in the correct format. |
| Binding (App only) | In the installing Project, the target Secret is bound with "Select secret," and its value is saved. |
| Server-only key | The left-hand side of the Secret reference does not begin with `NEXT_PUBLIC_`, `VITE_`, `REACT_APP_`, or `PUBLIC_` and is not a reserved key (such as `PORT`, `NODE_ENV`, or `PRS_*`). |
| Value length | Each value passed as an environment variable is 4,096 bytes or less. |
| Runtime verification | In the actual Page, representative operations using the Secret succeed, and actual values do not appear on screen, in network responses, or in logs. |

Up to **32** environment variables containing Secret references are handled. Ordinary server environment variables are limited to **128**, keys to **255 characters**, each value to **4,096 bytes**, and the total to **32 KiB**. These are smaller than the registration limit for the Project Secret value itself (8,192 characters), so a long value can be saved but causes the Page to fail at startup.

## When a Secret cannot be found or has no value

If the referenced Secret does not exist, does not match the target Project or organization, is an invalid shared Secret, or has an empty saved value, enabling the Page fails. Runtime resolution also validates the target Project, active revision, and decryptable value, so unresolved references result in an error. Do not assume behavior that continues by passing an empty string.

Respond in the following order.

1. Check the target organization, Project, and Page, and the Secret name in `.env`. For direct upload, also check that it is written in `.env` or `.env.production`.
2. In **"Project secrets,"** confirm that a value with the same name is registered and shown as masked. Do not attempt to restore the actual value from the screen.
3. If installed as an App, bind the target Secret with "Select secret" in the installing Project. A directly uploaded Page has no binding screen and is resolved by matching names.
4. Check that the value does not exceed 4,096 bytes.
5. If you updated a Secret, recheck representative operations through the usage path. Do not assume immediate reflection.
6. If it is not resolved, do not share Secret values; record the target Project, Secret name, environment variable name, occurrence time, error code or displayed message, release version, and reproduction steps, and contact the administrator.

## Common misunderstandings

### It is safe if a Secret name is the value of a public variable

It is not safe. Values of public keys such as `NEXT_PUBLIC_` may be embedded into browser code. The App flow rejects such references, but direct upload does not. Implementations that retrieve a value and copy it into another public variable are also prohibited.

### Putting it in `.env.local` or `.env.staging` makes it a production server Secret

It does not. Direct upload reads only `.env` and `.env.production`. The App flow also excludes `.env.local` files. Register a Secret to use in the target Project according to [Register, Update, and Delete Project Secrets](../operations/manage-project-secrets.md), and write only its reference in the `.env` that is read.

### A process using a Secret may call an external API from the browser

It may not. Users can observe authentication headers and responses from browser calls, which can lead to exposure of values or excessive permissions. Make external calls requiring a Secret on the server side and minimize data returned to the screen.

## Checklist

- [ ] The actual Secret value is registered as a Project Secret in the target Project and is not written in `.env`, source, or logs
- [ ] The Secret name meets the naming rules in [Register, Update, and Delete Project Secrets](../operations/manage-project-secrets.md) and [Page Specifications and Limits](../build/page-specs-and-limits.md) (begins with a lowercase English letter; lowercase English letters, numbers, and underscores; 64 characters or fewer)
- [ ] For direct upload, `${secret:SecretName}` is declared in `.env` or `.env.production`
- [ ] Environment variable names for Secret references do not use a public prefix or reserved key
- [ ] Each value passed as an environment variable is 4,096 bytes or less
- [ ] The server listens on `process.env.PORT` and does not use paths such as `/api/v1/*` or `/api/storage` for its own routes
- [ ] Only server processing reads from `process.env`, and values, authentication headers, and raw errors are not returned to the browser
- [ ] Confirmed that built JavaScript, HTML, JSON, source maps, and initial data contain no actual values
- [ ] Confirmed that, when a Secret is not configured, processing fails safely without displaying actual values

## Next articles

- [Register, Update, and Delete Project Secrets](../operations/manage-project-secrets.md)
- [Page Specifications and Limits](../build/page-specs-and-limits.md)
- [Execution Modes, Authentication, and Supported Scope](execution-modes-and-auth.md)
- [Page Source Structure and Runtime](page-source-structure-and-runtime.md)
