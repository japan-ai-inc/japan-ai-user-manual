# Page Source Structure and Runtime

Developers delivering a Page package locally generated browser output into a ZIP. Studio registration inspects the received ZIP and places its files for delivery. It has not been confirmed that dependencies are installed or `package.json` scripts are run during upload.

Therefore, prepare completed HTML, JavaScript, CSS, images, and other files that a user's browser can load, rather than the complete repository source. For registration and publishing screen operations, see [Register and Publish Pages from the Screen](../build/register-and-publish-pages.md). For limits such as capacity and ZIP structure, use [Page Specifications and Limits](../build/page-specs-and-limits.md) as the source of truth.

## Related articles

- [Page Specifications and Limits](../build/page-specs-and-limits.md)
- [Preview and Deploy a Page Locally](local-preview-and-deploy-page.md)
- [Register and Publish Pages from the Screen](../build/register-and-publish-pages.md)

## Scope of this article

This article explains the files to include in a ZIP, content excluded during registration, and the handling of confirmed runtime behavior. Whether specific frameworks such as React, Vue, and Next.js, server runtimes such as Node.js, server-side rendering, API routes, or arbitrary build commands are supported cannot be determined within this scope.

The Page delivery location is accessed as a Page host separate from Studio, and Studio displays it in an iframe. However, the checked source alone cannot determine which URLs the Page host maps to the base file, how it handles SPA deep URLs, or whether it can run server processing. Do not configure routing or a backend based on unconfirmed behavior.

## Before you begin

- Confirm the target Project, Page name, first screen users open, and intended browsers.
- Complete the build locally and collect only the generated output in a separate folder. Do not ZIP the original source, dependencies, and development configuration as-is.
- Confirm that the initially loaded HTML and its referenced JavaScript, CSS, images, fonts, and other files are available at relative paths in the output.
- Do not include passwords, API keys, tokens, Project Secret values, or personal information in files delivered to the browser. For Secret management, see [Register, Update, and Delete Project Secrets](../operations/manage-project-secrets.md).

> You do not need to include `package.json`, lock files, source maps, or environment files for building during registration. Do not mix files that may contain secret values into the output.

## Structure to prepare for delivery

Registration reads ordinary files in the ZIP and places allowed paths as delivery targets. It has not been confirmed that HTML file names, entry point names, the existence of `package.json`, or `scripts` names are validated. Nor has validation requiring `index.html` been confirmed within this scope.

For this reason, use a structure like the following, placing "files to deliver to the browser after the build" at the root of the ZIP. File names are examples and do not specify this structure or framework for Studio.

```text
page-release.zip
├── index.html
├── assets/
│   ├── app-xxxxx.js
│   └── app-xxxxx.css
└── images/
    └── logo.svg
```

Paths referenced by HTML must resolve based on their placement after ZIP extraction. If they depend on development-server-specific transformations, proxies, environment variables, or History API fallback, verify them first with built output.

### Handling `package.json` and build scripts

Even if you include `package.json`, there is no confirmation that `npm run build`, `pnpm build`, `yarn build`, or similar commands run during registration. Processing that restores dependencies, detects an output destination from a framework name, or automatically selects an output folder has also not been confirmed.

Therefore, build with your toolchain during development and put **its output expanded at the ZIP root**. For example, if you make a local `dist` or `build` only the parent folder of a ZIP, that folder name may be excluded during registration and the output may not be delivered.

> Whether a particular framework or server output can run could not be confirmed. Decide based on whether it can ultimately produce browser output.

## Folders and files excluded during registration

Paths containing `__MACOSX`, `node_modules`, `.git`, `.DS_Store`, `.jai-studio`, `__staging`, `__upload-lock.json`, `__backup`, or `__versions`, as well as common build-output folders at the root, are excluded. Use [Page Specifications and Limits](../build/page-specs-and-limits.md) as the source of truth for details.

When a ZIP encloses all files in only one parent folder, that parent folder is removed. Since a build-output folder directly under that parent folder is also excluded, a ZIP like the following is unsuitable.

```text
page-release.zip
└── my-app/
    └── dist/
        └── index.html
```

Extract the output and rebuild the ZIP with HTML and assets at the root. If a path element in the ZIP represents the current or parent directory, it is a structural error. A ZIP with no delivery-target files remaining after exclusions also cannot be registered.

## Runtime display

Registration extracts the ZIP and places files. When you update a Page, files from the previous output that are not included in the new output do not remain as delivery targets. For version operations, see [Manage Page Versions](../build/manage-page-versions.md).

When you open a Page in Studio, Studio creates a URL on the Page host and, for a Page that requires authentication, sets an iframe destination URL with short-lived authentication information. A Page using external customer authentication has a route that does not attach authentication information in the same way. Under every access setting, do not embed secret values into client code or configuration on the assumption that delivered files reach the browser.

The runtime has not been confirmed to compile registered source again. JavaScript runs in the user's browser. Server-side processing, secure references to secret values, and available environment variables cannot be determined from this structure alone, so review [Page Server Processing, Environment Variables, and Secret References](page-server-env-and-secrets.md) before implementing them.

## Common misunderstandings

### Put the entire development project in the ZIP

Registration does not install or build a project. `node_modules` is excluded, as are common root output folders. Include only completed deliverables containing HTML and assets.

### If `index.html` exists, it will always display

Registration validation for the existence of `index.html` has not been confirmed. The file to which the Page host resolves an arbitrary URL also cannot be determined within this scope. Prepare HTML, referenced assets, and relative paths, then open the registered Page to verify it.

### A Secret is acceptable in client-side configuration

Values delivered to the browser may be obtained by users or browser developer tools. Obfuscation, renaming variables, and hidden screen elements are not protection. Do not put secret values in distributed files, source maps, initial data, or logs.

## If it does not work

| Situation | Check and response |
|---|---|
| A required file cannot be found after registration | Extract the ZIP and check whether HTML and assets are at the ZIP root and not under an excluded folder name. |
| It displays locally but not as a Page | Check whether it depends on development-server-specific proxies, rewrites, environment variables, or History API fallback. In the browser network view, check the failed file URL and Content-Type. |
| An old screen or asset displays after an update | Check whether the new ZIP includes all required HTML, JavaScript, and CSS, then check the active version and the actual Page display. |
| A ZIP structure error occurs | Check whether paths contain elements representing the current or parent directory and whether ordinary delivery-target files remain after exclusions. For limits and structural requirements, see [Page Specifications and Limits](../build/page-specs-and-limits.md). |
| You want to use server code or a Secret | Do not place an alternative implementation on the client. Check the supported runtime and secure reference method in [Page Server Processing, Environment Variables, and Secret References](page-server-env-and-secrets.md). |

When making an inquiry, share the target Project and Page name, file list in the ZIP, occurrence time, displayed message, failed browser request, and reproduction steps. Do not include Secrets, API keys, tokens, or personal information in ZIPs, logs, or screenshots.

## Checklist

- [ ] Completed the build locally and extracted only browser output
- [ ] Confirmed the initially displayed HTML and paths to referenced assets
- [ ] Do not assume `package.json` scripts run or dependencies are installed
- [ ] Did not place delivery-required files under excluded folder names
- [ ] Extracted the ZIP and confirmed there is no unnecessary parent folder or path to a parent directory
- [ ] Files delivered to the browser do not contain secret values or personal information
- [ ] Are ready to open the actual Page after registration and verify typical display and operations

## Next article

- [Preview and Deploy a Page Locally](local-preview-and-deploy-page.md)
- [Page Specifications and Limits](../build/page-specs-and-limits.md)
- [Page Server Processing, Environment Variables, and Secret References](page-server-env-and-secrets.md)
