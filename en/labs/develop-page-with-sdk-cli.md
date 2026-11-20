# Develop a Page with the SDK and CLI

In this exercise, you will list fictional announcements stored in an Object on a Page. The browser SDK uses a same-origin route as the Page, and you will not put a Project API key in JavaScript. Register a locally built static deliverable as a new Page with the CLI, then check access settings, display, updates, and cleanup.

## Related articles

- [Development environment, versions, and compatibility](../development/dev-environment-and-versions.md)
- [Design an Object structure](../build/design-object-structure.md)
- [SDK Objects](../development/sdk-objects.md)
- [Hosted Page Objects API](../page-runtime/hosted-page-objects-api.md)
- [Preview a Page locally and deploy it](../development/local-preview-and-deploy-page.md)
- [CLI configuration and profiles](../cli/cli-config-and-profiles.md)
- [CLI Pages](../cli/cli-pages.md)

## Before you begin

- Prepare a test Project where you can create and view Pages and Objects. For the CLI, securely manage a Project API key with the minimum permissions needed in that Project. Deploying a Page requires the `page:write` scope. For issuing and revoking keys, see [Manage Project API keys](../operations/manage-project-api-keys.md).
- Use Node.js 22 or later and the released SDK/CLI **0.11.0**. This exercise uses `createClient({ baseUrl: "" })`, `objects.define()`, and `list()`. For installation and version pinning, see [Development environment, versions, and compatibility](../development/dev-environment-and-versions.md).
- Use an empty working directory. Do not reuse the deliverable directory of an existing Page. The CLI saves the Page ID and other metadata in `.jai-studio/page.json` inside the deployed folder, and the next deployment without `--page-id` updates that Page.
- Do not put keys in HTML, JavaScript, public environment variables, logs, or repositories. Browser Pages do not read CLI profiles.

## Sample used in this exercise

The Object display name is `Announcements (SDK practice)` and the Page name is `Announcement list (SDK practice)`. Create `title`, `category`, and `published_on`, and register two fictional records. Compare the Object ID with the value used for this exercise and handle it only with the people who need it.

| title (text) | category (text) | published_on (date) |
|---|---|---|
| Training announcement | Training | 2026-11-09 |
| Facilities inspection announcement | General Affairs | 2026-11-10 |

## Step 1. Prepare the Object and development environment

**Goal:** Prepare what will be displayed and a version-pinned development environment.

1. Create the Object and three fields in the test Project, then register the two records in the preceding table.
2. Check the Node.js version, then install the SDK and CLI pinned to 0.11.0.

   ```bash
   node --version
   npm install @japan-ai-inc/studio-sdk@0.11.0
   npm install --global @japan-ai-inc/studio-sdk@0.11.0
   jai-studio --version
   ```

3. In the CLI interactive input, create a profile for the test Project. Do not write the key in command arguments.

   ```bash
   jai-studio config set lab-page
   jai-studio config show lab-page
   ```

4. Do not reuse masked values from `config show`; check only the target connection and profile name.

For Object creation, see [Design an Object structure](../build/design-object-structure.md); for SDK/CLI installation, see [Development environment, versions, and compatibility](../development/dev-environment-and-versions.md); for profiles, see [CLI configuration and profiles](../cli/cli-config-and-profiles.md). **Checkpoint:** Node.js is 22 or later, and both the SDK and `jai-studio` are 0.11.0.

## Step 2. Create browser list code

**Goal:** Read the Object by using the Page's relative route without a Project API key.

1. In your existing build process, prepare HTML that displays the list and a browser entry file, then set the practice Object ID (a UUID). Passing the Object name results in an error (400). Get the ID from the Object screen or with the SDK's `studio.objects.list()`.
2. In the browser, initialize the SDK and display the list in the following form.

   ```js
   import { createClient } from "@japan-ai-inc/studio-sdk";
   const studio = createClient({ baseUrl: "" });
   const notices = studio.objects.define("<Object UUID>");

   async function load() {
     const result = await notices.list({ limit: 20 });
     // Display result.data title, category, and published_on on the screen
   }
   ```

3. If retrieval fails, display only guidance to check the Object ID, Object view permission, and Page access settings. Do not add API keys, Secrets, or authentication headers.

For SDK Object operations, see [SDK Objects](../development/sdk-objects.md); for initialization and permissions in the browser of a Studio-hosted Page, see [Hosted Page Objects API](../page-runtime/hosted-page-objects-api.md). **Checkpoint:** `baseUrl` is an empty string and the browser code contains no API key.

## Step 3. Create the deliverable and check its structure locally

**Goal:** Output only distributable HTML, JavaScript, and necessary static files to `dist`.

Studio decides how to handle an upload by its root. If the root has `package.json` and a lockfile, Studio installs dependencies and runs `build` before starting it. If there is no `package.json` but there is `index.html`, it serves the upload as a static Page. Uploads with neither, and Next.js build output, are rejected. In this exercise, you upload the locally built `dist` (a static deliverable with `index.html` at the root). To upload the source folder and let Studio build it, see [Page build, startup, and updates](../development/page-build-start-update.md).

1. With your current build settings, output the HTML and bundled JavaScript to `dist`.
2. On a local web server, check the heading, static files, and relative paths.
3. Confirm that `dist` does not contain `node_modules`, `.env`, keys, Secrets, or values that must not be in source maps.
4. Because the local screen has no Page runtime session, do not judge Object retrieval success only here.

For local checking and deliverable structure, see [Preview a Page locally and deploy it](../development/local-preview-and-deploy-page.md); for distribution handling, see [Page specifications and limits](../build/page-specs-and-limits.md). **Checkpoint:** `dist` contains only distributable output and no secret values.

## Step 4. Register it as a new Page with the CLI

**Goal:** Register `dist` as a new Page and record the result safely.

1. Check the target profile, deliverable directory, and whether an existing `.jai-studio/page.json` is present.
2. Run the following.

   ```bash
   jai-studio --profile lab-page pages deploy ./dist \
     --name "Announcement list (SDK practice)" --json
   ```

3. Save the successful result's `pageId`, `studioUrl`, `runtimeUrl`, and `version` in a record that only necessary people can view. You will always use `pageId` for later updates.
4. With the default access mode "Sign-in required," `runtimeUrl` is always `null`. Open the target Page through `studioUrl`.

{% hint style="warning" %}
`.jai-studio/page.json` is written inside `./dist`. Common bundlers empty `dist` on rebuild, so this file is deleted too. If you then deploy without `--page-id`, the existing Page is not updated and **a new Page is silently created** (with a new Page ID and URL). Always specify `--page-id` when updating.
{% endhint %}

For deploy arguments, determining initial registration versus updates, and reading results, see [CLI Pages](../cli/cli-pages.md). **Checkpoint:** Do not conclude that the Object was read from CLI success alone; check the new Page target and result URLs.

## Step 5. Check access settings and display

**Goal:** Check access to the Page and Object reading as a signed-in user.

1. Open **"Access settings"** from the Page card, and confirm that **"Access Mode"** is the default **"Sign-in required."**
2. Open the Page from the intended user entry point and check the two records and displayed count.
3. If they do not display, check the Object ID, Object view permission, Page access settings, and user in order. Do not work around this by adding a key to the browser.
4. If you change the heading and rebuild, deploy with the `pageId` recorded in Step 4, then compare the display and version.

   ```bash
   jai-studio --profile lab-page pages deploy ./dist \
     --page-id "<pageId recorded in Step 4>" --json
   ```

For access settings, see [Register and publish Pages from the screen](../build/register-and-publish-pages.md); for update checks, see [Page build, startup, and updates](../development/page-build-start-update.md). **Checkpoint:** Opening a Page and reading an Object are separate. Check required conditions for both the Page and Object.

## Common misunderstandings

- A CLI profile is for the CLI. The SDK in the browser of a Studio-hosted Page uses `baseUrl: ""` and does not configure an API key.
- `.jai-studio/page.json` inside `dist` is deleted on rebuild. Always specify `--page-id` when updating.
- CLI success and successful Object display are separate. Check Page access conditions and Object view permission.
- Even after upgrading to 0.11.0, initialization, Object definition, and list retrieval in this exercise use the same form. Pin SDK and CLI versions. Version 0.11.0 also has CLI commands for Agent definitions, Object access rules, and scheduled tasks, but do not run them in this Page exercise.

## If something does not work

| Situation | First thing to check |
|---|---|
| `jai-studio` cannot be found or the version differs | Check Node.js, PATH, and SDK/CLI versions in [Development environment, versions, and compatibility](../development/dev-environment-and-versions.md). |
| CLI authentication fails | Check the profile, connection destination, and key expiration and permissions in [CLI configuration and profiles](../cli/cli-config-and-profiles.md) and [Manage Project API keys](../operations/manage-project-api-keys.md). |
| You may update an existing Page when you intended an initial deployment | Check `--page-id` and `dist/.jai-studio/page.json`; do not deploy based on assumptions. See [CLI Pages](../cli/cli-pages.md). |
| A new Page was created when you intended an update | Rebuilding deleted `dist/.jai-studio/page.json`. Redeploy to the original Page with the recorded `pageId` in `--page-id`, and clean up the Page created by mistake. |
| CLI deployment fails with a permission error | Check that the Project API key has the `page:write` scope. |
| The Page cannot read records | Check the Object ID, Object view permission, and Page access settings in [Hosted Page Objects API](../page-runtime/hosted-page-objects-api.md). |
| The Page does not display or the upload is rejected | Check that the root has `index.html` (or `package.json` and a lockfile), and check the deliverable's HTML, JavaScript, and relative paths, then see [Page build, startup, or display failures](../troubleshooting/page-build-start-display-failures.md). |

## Cleanup

1. Check the sharing scope of the practice Page. If it is unnecessary, delete or unpublish it only after checking the target and impact.
2. Deleting a CLI profile does not revoke a Project API key. After confirming that no process uses it, revoke or delete the key according to [Manage Project API keys](../operations/manage-project-api-keys.md).
3. Check whether you need to retain the Page URL, deliverable, Object, or records. Clean them up if no other users or exercises reference them.
4. This exercise does not create Secrets, external connections, triggers, or schedules. If you added them during the exercise, first confirm that they are stopped, revoked, or unpublished.

## Read next

- [CLI Pages](../cli/cli-pages.md)
- [SDK Objects](../development/sdk-objects.md)
- [Hosted Page Objects API](../page-runtime/hosted-page-objects-api.md)
- [Page build, startup, and updates](../development/page-build-start-update.md)
- [Manage Project API keys](../operations/manage-project-api-keys.md)
