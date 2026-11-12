# Design the Configuration of a Distributable Page

When distributing a Page as part of an App, Page IDs, Workflow IDs, Agent IDs, Object IDs, Storage placement, Project Secrets, and connections to external services that were valid in the provider Project may not be usable unchanged in the installing Project. Leave only **logical names that express what is used** in the distributable artifact, and design it so that the installing Project can resolve and configure actual entities.

This article explains how developers creating a distributable Page can separate fixed IDs, Secrets, connections, and environment differences. App publishing, installation operations, and division of responsibilities are out of scope. For migration between Projects, also see [Plan a Migration Between Projects](../operations/plan-project-migration.md).

## Related articles

- [Plan a Migration Between Projects](../operations/plan-project-migration.md)
- [Page Source Structure and Runtime](page-source-structure-and-runtime.md)
- [Preview a Page Locally and Deploy It](local-preview-and-deploy-page.md)
- [Page Navigation and Deep Links](page-navigation-and-deep-links.md)
- [Register, Update, and Delete Project Secrets](../operations/manage-project-secrets.md)
- [Constraints on Distribution Scope, Dependencies, and Authentication Settings](../distribution/distribution-scope-dependencies-auth.md)
- [Self-Install in Your Organization as the Creator](../distribution/self-install-in-organization.md)

## Scope of this article

Here, configuration means how a Page identifies referenced resources and the boundary for values supplied for each installing Project. UI labels, installable resource types, connection sharing scope, and availability vary by organization settings, permissions, and App configuration.

The distribution mechanism resolves declared Page resources to entities in the installing Project. It does not, however, transfer IDs embedded in arbitrary strings or browser artifacts, destinations and data of external services, or other values. Identify in advance the configuration that must be recreated in the installing Project.

## Before you begin

- List the Workflows, Agents, Objects, Storage, Project Secrets, external services, and API keys the Page references. Cover not only the Page screen, but also environment variables, initial data, URLs, build-time configuration, and server-side processing.
- Divide each value into a logical name, an identifier resolved in the installing Project, a Secret or connection configured in the installing Project, and a fixed value specific to the external service.
- Determine the target environments and test data so you do not confuse the provider, verification, and installing Projects. Do not include the provider Project's real data, API keys, or tokens in artifacts, logs, or inquiries.

> Do not fix a Project ID, Page ID, organization ID, or Page host in screen JavaScript, URLs, `.env`, or build-time constants. A Page's entry URL also differs in the installing Project. Use relative URLs as the basis for navigation within a Page; for details, see [Page Navigation and Deep Links](page-navigation-and-deep-links.md).

## Separate configuration

| Type | What remains in the distribution | What to do in the installing Project | What to avoid |
|---|---|---|---|
| Page itself | Logical name, artifact, relative paths | Check the installing Project's identifier and access settings | Fixing the provider Page ID, URL, or organization ID |
| Workflow, Agent, and Object | A logical name that indicates the purpose | Declare it in the same release and check resolution in the installing Project | Embedding only a UUID or display name in source code |
| Storage | A logical root name and relative paths within the App | Prepare required files, folders, and permissions | Depending on provider-specific file IDs or absolute URLs |
| Project Secret | The Secret name and server-side environment-variable name | Create or select a value in the installing Project and associate it | Putting values, API keys, or tokens in ZIP files or public environment variables |
| External connection | Purpose, required permissions, and configuration owner | Authenticate with an account in the installing Project, and check shared or personal use | Assuming the provider's authentication state, cookies, or connection ID transfers |

Choose logical names whose meaning does not change in the installing Project. Use names that make the purpose, type, and ownership boundary clear, such as `daily_report`, `support_agent`, `customer`, and `uploads`. Do not reuse the same logical name for another purpose.

## 1. Declare resources by logical name

A distributed Page can list logical names for Workflows, Agents, Custom Objects, and Storage roots in root-level `app.refs.json`. Distribution processing matches them with items declared in the same release and resolves them to entities in the installing Project during installation. UUID formats, empty strings, duplicates, and undefined types are not accepted.

```json
{
  "workflows": ["daily_report"],
  "agents": ["support_agent"],
  "custom_objects": ["customer"],
  "storage_prefixes": ["uploads"]
}
```

A Page without `app.refs.json` declares no references. Declare dependent resources by the same logical names on the release side, and check that there are no unresolved references before publishing.

> `app.refs.json` is not a file for arbitrary configuration. Its targets are Workflows, Agents, Custom Objects, and Storage roots; it does not migrate Datasets, external services, arbitrary HTTP URLs, or connections themselves.

### When an ID is needed at runtime

Do not write the installing Project's IDs in source code. Pass resolved values from the Page execution context or server-side configuration. Even when using the Studio SDK in external server processing, obtain the installing Project's list, validate the business identifier, and then use the target ID.

```ts
// Example that runs only on the server side. Do not pass apiKey to the browser.
const { data: objects } = await studio.objects.list();
const customer = objects.find(item => item.objectName === "customer");
if (!customer) throw new Error("The installing Project does not have a customer Object.");

const handle = studio.objects.define(customer.objectId);
```

The SDK's `objects.list()` and `objects.define()` are public APIs. However, this example alone does not guarantee uniqueness of an Object name, permission to retrieve the list, or an execution environment where a Page can call the SDK directly. Check selection rules, permissions, pagination, and what to display when not found for each App. Do not place Project API keys in browsers, public environment variables, source control, or logs.

## 2. Treat environment variables as values resolved in the installing Project

A `.env` or `.env.<name consisting of alphanumeric characters, underscores, and hyphens>` at the artifact root is read as a server-side environment-variable template during distribution and excluded from delivered files. `.env.local` and `.env.*.local` are for local development and are not included in templates. Do not use variable expansion (for example, `${HOST}/api`); specify a concrete value or reference for each variable.

```dotenv
# Do not write a value in the distribution; reference the Secret selected in the installing Project.
CRM_TOKEN=${secret:crm_token}

# Make only public information common to the external service a concrete value after confirming it is needed.
CRM_REGION=jp
```

`${secret:name}` is not the actual value of a Project Secret; it declares a Secret name that must be satisfied in the installing Project. A Secret name must start with a lowercase English letter and use lowercase English letters, numbers, and underscores. A public environment-variable name cannot contain a Secret reference, an installing Project resource ID, or a Project API key, because it may be expanded into the browser.

After installation, check required items in the App's "Required App Secrets," create or select the target Project's "Project Secrets," and associate them using "Select secret." Do not copy Secret values from the provider. For creating, updating, and deleting Secrets, see [Register, Update, and Delete Project Secrets](../operations/manage-project-secrets.md).

## 3. Reauthenticate connections in the installing Project

If an App requires a shared external connection, the installing Project may show separate guidance for unconfigured connections and Secrets. Track features that require connections separately from Page features that require only Secrets. Even if a required authentication provider is displayed before installation, this does not mean authentication information or Secret values are included in Marketplace listing information.

1. For each external service, determine the destination, required operations, minimum permissions, authentication owner, and whether use is shared or personal.
2. In the installing Project, configure the connection or Secret using an account in the installing Project. Do not judge that testing with the provider's account is complete.
3. Check incomplete connections and Secrets in the App's "Setup." Do not infer and add items that are not displayed; check with an administrator or the App guidance.
4. Separately verify reading, updating, and external transmission using a test account with minimum permissions and test data in the installing Project.

Do not put connection IDs, OAuth tokens, cookies, passwords, or Project API keys in `app.refs.json`, `.env`, JavaScript, URLs, or logs. Whether activation or republishing is required after reauthenticating a connection, and whether it is immediately reflected in running processing, varies by configuration and cannot be determined within this verification scope.

## Checklist before distribution

- [ ] I did not fix the provider Project ID, Page ID, organization ID, URL, or resource UUID in the artifact
- [ ] I organized Workflow, Agent, Custom Object, and Storage dependencies by logical name and declared required items in `app.refs.json`
- [ ] The declared logical names match items provided in the same release
- [ ] I declared only Secret names and did not include values, API keys, or tokens in ZIP files, public environment variables, or logs
- [ ] I determined the authentication owner, minimum permissions, sharing scope, and representative operation for each connection in the installing Project
- [ ] I have a plan to check initial display, resource retrieval, updates, external transmission, and behavior when permissions are insufficient in the installing Project

## When you cannot confirm

Do not work around the following states by fixing IDs, moving Secrets to the browser, or loosening access settings.

- A reference is unresolved during distribution, or an error related to UUIDs or environment variables occurs.
- You cannot check "Required App Secrets" or connection configuration status in the installing Project.
- The Page opens, but only data retrieval, updates, or external transmission fail.
- You cannot determine whether the connection is shared or personal, required external-service permissions, or who is responsible for reauthentication.

Record the target App, Page, installing Project, logical name, Secret name excluding its value, connection destination, occurrence time, displayed message, and representative operations already performed; then check with the administrator or provider. Do not attach Secret values, API keys, tokens, cookies, or real data to an inquiry.

## Read next

- [Plan a Migration Between Projects](../operations/plan-project-migration.md)
- [Register, Update, and Delete Project Secrets](../operations/manage-project-secrets.md)
- [Constraints on Distribution Scope, Dependencies, and Authentication Settings](../distribution/distribution-scope-dependencies-auth.md)
- [Self-Install in Your Organization as the Creator](../distribution/self-install-in-organization.md)
