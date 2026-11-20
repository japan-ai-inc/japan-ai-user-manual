# Page Source Structure and Runtime

Developers who deliver a Page put the **project source** into a ZIP and upload it. The runtime for Pages hosted in Studio (the Page runtime) selects Bun, Node, or static according to the lockfile in the uploaded source, runs dependency installation and `build`, and then runs the Page as a Node/Bun server (static Pages are served as files).

For registration and publishing screen operations, see [Register and Publish Pages from the Screen](../build/register-and-publish-pages.md). For limits such as capacity and ZIP structure, use [Page Specifications and Limits](../build/page-specs-and-limits.md) as the source of truth.

## Key points

| Item | Details |
|---|---|
| What to upload | The project source. `package.json` and a lockfile at the root, or `index.html` for a static Page |
| What Studio does | Selects Bun, Node, or static according to the lockfile, runs install and `build`, and starts Node/Bun as a server |
| Rejected ZIPs | ZIPs containing an uploaded Next.js build output; ZIPs with neither `package.json` nor `index.html` at the root |
| Server listening | Listen on the port given by `process.env.PORT` |
| Delivery path | Pages are served under a sub-path, so match the framework's `basePath` setting to the delivery URL |
| Time | The build can take several minutes (up to 15 minutes) before the first display. The first SSR response must be returned within about 60 seconds |

## Related articles

- [Page Specifications and Limits](../build/page-specs-and-limits.md)
- [Preview and Deploy a Page Locally](local-preview-and-deploy-page.md)
- [Register and Publish Pages from the Screen](../build/register-and-publish-pages.md)

## Scope of this article

This article explains the source structure to put in the ZIP, content excluded during registration, and runtime handling. For environment variables and Secrets used in server processing, see [Page Server Processing, Environment Variables, and Secret References](page-server-env-and-secrets.md). For URLs and routing, see [Page Navigation and Deep Links](page-navigation-and-deep-links.md).

Pages are served from a Page host separate from Studio itself and are displayed in an iframe in Studio.

## Before you begin

- Check the target Project, Page name, the first screen users open, and the intended browsers.
- Check that `package.json` and a lockfile are at the root (or `index.html` for a static Page). The type of lockfile determines whether Bun or Node is used, so include the lockfile of the package manager you use locally.
- Check that the `build` script in `package.json` completes without errors locally.
- For Pages with a server, listen on `process.env.PORT`. Do not hard-code the port number.
- Do not include passwords, API keys, tokens, Project Secret values, or personal information in files delivered to the browser. For Secret management, see [Register, Update, and Delete Project Secrets](../operations/manage-project-secrets.md).

> Do not include `node_modules` or local build output (`dist`, `build`, and so on). Dependencies and build output are created by Studio's install and `build`. Uploading a Next.js build output is rejected.

## Source structure to upload

Place `package.json`, the lockfile, and the source at the ZIP root. File names are examples.

```text
page-source.zip
├── package.json
├── package-lock.json   (the lockfile of the package manager you use)
├── src/
│   └── ...
└── public/
    └── ...
```

For a static Page (HTML, JavaScript, and CSS without a build step), place `index.html` at the root.

```text
page-static.zip
├── index.html
├── assets/
│   ├── app.js
│   └── app.css
└── images/
    └── logo.svg
```

A ZIP with neither `package.json` nor `index.html` at the root is rejected.

### Handling `package.json` and build scripts

After upload, the Page runtime selects Bun, Node, or static according to the lockfile, installs dependencies, and then runs the `build` script. Check the following locally before uploading:

- The lockfile matches `package.json`, and installation succeeds
- The `build` script succeeds locally
- If you start a server, it listens on `process.env.PORT`
- The framework's `basePath` (delivery path prefix) setting matches the Page's delivery URL

## Folders and files excluded during registration

Paths containing `__MACOSX`, `node_modules`, `.git`, `.DS_Store`, `.jai-studio`, `__staging`, `__upload-lock.json`, `__backup`, or `__versions`, as well as common build-output folders at the root, are excluded. Use [Page Specifications and Limits](../build/page-specs-and-limits.md) as the source of truth for details.

In a ZIP where a single parent folder wraps all files, that parent folder is removed. If a path in the ZIP contains an element representing the current or parent directory, a structure error occurs. A ZIP with no files left after exclusions also cannot be registered.

## Runtime display

After upload, the Page runtime runs install and `build` before serving. While building and when a build fails, dedicated screens are displayed. The first display can take several minutes (up to 15 minutes). When you update a Page, it is built again from the new source. For version operations, see [Manage Page Versions](../build/manage-page-versions.md).

- Pages that run as Node/Bun servers listen on `process.env.PORT`. Reserved keys such as `PORT`, `NODE_ENV`, and `PRS_*` are excluded even if a developer sets them in `.env`.
- Paths such as `/api/v1/*` and `/api/storage` are used by the platform. Do not use these paths for your own server's API routes.
- The first SSR response has a limit of about 60 seconds. Do not put time-consuming processing before the first response.
- Static Pages have an SPA fallback, and `<base href>` is inserted automatically.

When you open a Page within Studio, Studio creates the Page host URL and, for Pages that require authentication, sets a URL with short-lived authentication information as the iframe target. In any access setting, do not embed secret values in files delivered to the browser. Handle secret values in server processing. For details, see [Page Server Processing, Environment Variables, and Secret References](page-server-env-and-secrets.md).

## Common misunderstandings

### You can ZIP the locally built `dist`

No. Studio runs install and `build` from the source. Common build-output folders at the root are excluded, and Next.js build output is rejected. Upload the source including `package.json` and the lockfile.

### The port can be fixed

No. The server listens on `process.env.PORT`. `PORT` is a reserved key, so setting it in `.env` has no effect.

### A Secret is acceptable in client-side configuration

Values delivered to the browser can be obtained by users or through browser developer tools. Obfuscation, renamed variables, and hidden screen elements do not provide protection. Do not put secret values in delivered files, source maps, initial data, or logs.

## If it does not work

| Situation | Check and response |
|---|---|
| The ZIP is rejected | Check whether `package.json` or `index.html` is at the root and whether you uploaded a Next.js build output. |
| The build failure screen appears | Check that the lockfile and `package.json` are consistent and that the `build` script succeeds locally. |
| The building screen continues | The first display can take several minutes (up to 15 minutes). Wait and open the Page again. |
| It does not start or respond | Check that it listens on `process.env.PORT` and returns the first SSR response within about 60 seconds. |
| Assets or APIs return 404 | Check the `basePath` setting and whether your paths conflict with platform paths such as `/api/v1/*` and `/api/storage`. |
| A ZIP structure error occurs | Check whether paths contain elements representing the current or parent directory and whether files remain after exclusions. For limits and structural requirements, see [Page Specifications and Limits](../build/page-specs-and-limits.md). |

When making an inquiry, share the target Project and Page name, the file list in the ZIP, the date and time of occurrence, displayed messages, failed browser requests, and reproduction steps. Do not include Secrets, API keys, tokens, or personal information in ZIPs, logs, or screenshots.

## Checklist

- [ ] `package.json` and a lockfile (or `index.html` for a static Page) are at the root
- [ ] Install and `build` succeed locally
- [ ] The server listens on `process.env.PORT`, and `basePath` matches the delivery URL
- [ ] `node_modules`, local build output, and Next.js build output are not included
- [ ] Extracted the ZIP and confirmed there are no unnecessary parent folders or paths to parent directories
- [ ] Files delivered to the browser do not contain secret values or personal information
- [ ] Ready to wait for the build to finish after registration, open the actual Page, and check representative display and operations

## Next articles

- [Preview and Deploy a Page Locally](local-preview-and-deploy-page.md)
- [Page Specifications and Limits](../build/page-specs-and-limits.md)
- [Page Server Processing, Environment Variables, and Secret References](page-server-env-and-secrets.md)
