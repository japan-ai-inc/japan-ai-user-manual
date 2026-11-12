# Page Server Processing, Environment Variables, and Secret References

When using an external API token or other secret in a Page, design the Page to read it **only on the Page server side**, not place it in browser JavaScript, HTML, initial data, logs, or source maps. Do not include the actual value of a Project Secret in a ZIP or send it directly to an external service from a user's browser.

This article covers the implementation in which the Page/App release process detects Project Secret references from a root `.env` file and resolves them as server environment variables. It cannot confirm that an arbitrary Node.js server, SSR, or API route runs in a Page that simply uploads a ZIP. Confirm that the target Page/App can use a server runtime in the target environment before adopting this approach.

## Related articles

- [Register, Update, and Delete Project Secrets](../operations/manage-project-secrets.md)
- [Page Specifications and Limits](../build/page-specs-and-limits.md)
- [Execution Modes, Authentication, and Supported Scope](execution-modes-and-auth.md)
- [Page Source Structure and Runtime](page-source-structure-and-runtime.md)

## Before you begin

- Register the actual Secret value in **"Project secrets"** for the target Project. Use [Register, Update, and Delete Project Secrets](../operations/manage-project-secrets.md) as the source of truth for creation, updates, deletion, name and value input rules, permissions, and rotation.
- A Project Secret name must begin with a lowercase English letter and thereafter use only lowercase English letters, numbers, and underscores, with a maximum of **64 characters**. An empty string and UUID format cannot be used. For example, `external_api_token` can be used. A value cannot be empty and has a maximum of **8,192 characters**.
- Clarify why server processing is necessary. Place only processing that requires a secret value on the server side, such as external API calls, signature generation, and data retrieval with a token. The browser sends only the minimum necessary input to server processing and receives only results that do not contain the Secret.
- Treat environment variable names beginning with `NEXT_PUBLIC_`, `VITE_`, `REACT_APP_`, or `PUBLIC_` as being inlined for the browser. Do not use them for Project Secret references.

> A Secret name is an identifier that indicates its purpose, not its value. Names such as `external_api_token` may be referenced in configuration and diagnostics, but do not write token contents, API keys, Cookies, or authentication headers in names, error displays, or inquiries.

## 1. Declare a Secret reference in `.env`

The Page/App release process reads `.env` and `.env.<name>` at the root of the bundle. `.env.local`, `.env.local.<name>`, and files ending in `.local` are excluded from release configuration. `.env.example`, `.env.sample`, and `.env.template` are also not treated as release configuration.

Write the following exact reference format rather than the actual Secret value. The left-hand side is the environment variable name read on the server side.

```dotenv
EXTERNAL_SERVICE_TOKEN=${secret:external_api_token}
```

An environment variable name begins with an English letter or underscore and then uses English letters, numbers, and underscores. Environment variable expansion using `$VAR` or `${VAR}` in a value is not supported. Do not combine a Secret reference with another environment variable; register the required value as a single Secret.

When there are multiple `.env` files, `.env` is processed first, and `.env.<name>` later overwrites the same key. Placing a production Secret in local-only `.env.local` does not make it release configuration.

### Reference detection and binding

When the release process detects a value of `${secret:...}`, it treats it as a Secret reference without copying the actual value into the bundle. You cannot release if the Secret name does not meet Project Secret naming rules or if it is written in an environment variable name for browser exposure.

If Secret settings show guidance for missing items, select and bind the target Secret from the displayed items. Do not manually enter identifiers other than the reference name and environment variable name; normally, manage only `${secret:external_api_token}` and a server environment variable name.

> Do not write a token string received from an issuer in `.env` in place of `${secret:external_api_token}`. The actual value may remain in release-target configuration and be exposed in distribution files, history, or logs.

## 2. Read the value only in server processing

In Node.js code running in the server environment, read the environment variable using the declared left-hand-side name. The following example queries an external service and returns only the processing result to the browser. The entry point for server processing and framework-specific syntax are outside the confirmation scope, so follow the target Page/App runtime method.

```ts
export async function loadItems() {
  const token = process.env["EXTERNAL_SERVICE_TOKEN"];
  if (!token) throw new Error("External service configuration is missing");

  const response = await fetch("https://example.invalid/v1/items", {
    headers: { Authorization: `Bearer ${token}` },
  });
  if (!response.ok) throw new Error("Failed to connect to the external service");

  const data = await response.json();
  return data.items;
}
```

The browser does not receive `token`, values from `process.env`, or external-service authentication headers. Limit server processing return values to fields required by the screen. Even when returning an exception to the browser, do not return the original API error or request configuration as-is; use a message that does not include a Secret value, Authorization header, or connection string.

Avoid the following implementation.

```ts
// Example of placing a Secret in code or configuration delivered to the browser. Do not use.
const token = process.env.NEXT_PUBLIC_EXTERNAL_SERVICE_TOKEN;
window.runtimeConfig = { token };
```

In build tools that use a browser-exposure prefix, values may be embedded in the client bundle at build time. The Page release process also identifies the four prefixes above as browser-exposed keys and rejects Project Secret references. However, it cannot be confirmed that it automatically removes actual values you write yourself in custom build configuration, generated JavaScript, HTML, JSON, or source maps. **Developers must inspect output before and after the build and ensure it contains no actual values.**

## 3. Conditions to meet at release

To release and enable a Page/App that contains a Secret reference, you must meet at least the following conditions.

| Condition | What to verify |
|---|---|
| Feature availability conditions | The Project Secret feature is available in the organization. In an organization where it is unavailable, a release containing a Secret reference cannot proceed. |
| Correct Project | The Secret is registered in the target Project where the Page/App is enabled. A value with the same name in another Project cannot substitute for it. |
| Reference declaration | The target root `.env` contains the server key and `${secret:Secret name}` in the correct format. |
| Binding | The target Secret is bound as an active Project-shared Secret and its value is saved. |
| Server-only key | The left side of the Secret reference does not begin with `NEXT_PUBLIC_`, `VITE_`, `REACT_APP_`, or `PUBLIC_`. |
| Runtime verification | In the actual Page/App, a representative operation that uses the Secret succeeds and no actual value appears in the screen, network response, or logs. |

The release process handles up to **32** environment variables containing Secret references. Ordinary server environment variables are limited to **128**, with keys up to **255 characters**, one value up to **4,096 bytes**, and all values up to **32KiB**. These limits are separate from the registration limit for the Project Secret value itself.

## When a Secret cannot be found or has no value

Page/App enablement fails if the target Secret does not exist, is not bound, does not match the target Project or organization, is an invalid shared Secret, or has an empty saved value. Runtime resolution also validates the target Project, active revision, binding, and a decryptable value, so an unresolved reference becomes an error. Do not assume that it proceeds by passing an empty string.

Respond in the following order.

1. Check the target organization, Project, and Page/App, and the Secret name in `.env`.
2. In **"Project secrets,"** confirm that a value with the same name is registered and shown as masked. Do not attempt to restore the actual value from the screen.
3. If Secret settings indicate an item is missing or unset, select and bind the target Secret. Do not manually enter the `page_secret_` format from the previous section to bind it; use the displayed setting item.
4. If you update a Secret, reconfirm a representative operation through its use path. It cannot be uniformly confirmed when an update is reflected in running processing or an existing version, so do not assume immediate reflection.
5. If it remains unresolved, do not share the Secret value. Record the target Project, Secret name, environment variable name, occurrence time, error code or displayed message, release version, and reproduction steps, then contact the administrator.

## Common misunderstandings

### It is safe if a Secret name is the value of a public variable

It is not safe. If `${secret:...}` is set for a public key such as `NEXT_PUBLIC_`, it is rejected as a reference. An implementation that obtains the value and copies it into another public variable is also prohibited.

### Putting it in `.env.local` makes it a production server Secret

It does not. `.env.local` files are excluded from release targets as local-development overrides. Register a Secret to use in the target Project according to [Register, Update, and Delete Project Secrets](../operations/manage-project-secrets.md), and write only its reference in the release-target `.env`.

### A process using a Secret may call an external API from the browser

It should not. For a browser call, users can observe authentication headers and responses, which can expose values or grant excessive permissions. Make external calls requiring a Secret on the server side and minimize the data returned to the screen.

## Checklist

- [ ] Registered the actual Secret value as a Project Secret in the target Project and did not write it in `.env`, source, or logs
- [ ] The Secret name meets the naming rules in [Register, Update, and Delete Project Secrets](../operations/manage-project-secrets.md) and [Page Specifications and Limits](../build/page-specs-and-limits.md) (begins with a lowercase English letter; lowercase English letters, numbers, and underscores; 64 characters or fewer)
- [ ] Declared `${secret:Secret name}` in a root `.env` and do not depend on `.env.local`
- [ ] Did not use a browser-exposure prefix for the environment variable name of a Secret reference
- [ ] Only server processing reads from `process.env` and does not return values, authentication headers, or raw errors to the browser
- [ ] Verified that built JavaScript, HTML, JSON, source maps, and initial data contain no actual value
- [ ] Verified that it fails safely without displaying the actual value when a Secret is unset

## Next article

- [Register, Update, and Delete Project Secrets](../operations/manage-project-secrets.md)
- [Page Specifications and Limits](../build/page-specs-and-limits.md)
- [Execution Modes, Authentication, and Supported Scope](execution-modes-and-auth.md)
- [Page Source Structure and Runtime](page-source-structure-and-runtime.md)
