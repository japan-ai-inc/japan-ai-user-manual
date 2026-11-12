# CLI Configuration and Profiles

The `jai-studio` CLI is a command for working with Studio Projects from a development machine or managed development automation. This article explains installation, profiles, the priority order of runtime settings, and how to review and switch them. For key issuance, scopes, expiration dates, and revocation, see [Manage Project API Keys](../operations/manage-project-api-keys.md).

## Related articles

- [Execution Modes, Authentication, and Supported Scope](../development/execution-modes-and-auth.md)
- [Development Environment, Versions, and Compatibility](../development/dev-environment-and-versions.md)
- [Manage Project API Keys](../operations/manage-project-api-keys.md)
- [Preview a Page Locally and Deploy It](../development/local-preview-and-deploy-page.md)

## Before you begin

The package is `@japan-ai-inc/studio-sdk`, and the CLI executable is `jai-studio`. Prepare Node.js 22 or later, and issue a Project API key with the minimum scopes required for the target Project. A CLI profile is configuration read only by the CLI. The SDK's `createClient()` does not read profiles.

> A Project API key is secret information. Do not store it in browser code, public environment variables, source control, logs, or chat. Writing a key in a command argument may leave it in shell history or a process list.

## 1. Install the CLI and check its version

Install it globally.

```bash
npm install --global @japan-ai-inc/studio-sdk@0.11.0
jai-studio --version
command -v jai-studio
```

`jai-studio --version` displays the CLI version. If the expected command cannot be found or the version differs, use `command -v` to check the executable being run and the globally installed version.

## 2. Create a profile safely

A profile stores an environment name or endpoint URL and an API key. The first profile you create automatically becomes active. `production` resolves to `https://api.japan-ai.co.jp`. You can also specify a complete `http` or `https` URL as the endpoint.

### `config set`

```text
jai-studio config set <name> [apiKey] [--key-stdin] [-e|--env <env>]
```

Notation containing `<...>` below is **syntax that requires substitution**. Do not paste it into the shell with the angle brackets; replace it with an actual value.

| Argument or flag | Required | Default | Meaning |
|---|---:|---|---|
| `<name>` | Yes | — | The profile name to create or update. |
| `[apiKey]` | No | — | The Project API key. This input method is retained for compatibility, but avoid using it. |
| `--key-stdin` | No | None | Reads the key from standard input. Use it with CI or secret-management tools. |
| `-e, --env <env>` | No | `production` | Specify `production` or a complete HTTP/HTTPS URL. |

Normally, use interactive input. The key is not displayed in the terminal while you enter it.

```bash
jai-studio config set development
```

When passing output from a secret-management tool to standard input, such as in CI, explicitly specify `--key-stdin`.

```bash
vault read -field=key secret/jai | jai-studio config set ci --key-stdin
```

If `JAI_STUDIO_API_KEY` is set, `config set` uses its value.

```bash
JAI_STUDIO_API_KEY=pak_... jai-studio config set development --env production
```

Do not pass an actual key to the compatibility `[apiKey]` argument.

### Storage location and display precautions

The configuration file is saved to `$XDG_CONFIG_HOME/jai-studio/config.json` when `XDG_CONFIG_HOME` exists; otherwise, it is saved to `~/.config/jai-studio/config.json`. Permissions allowing only the owner to read and write (`0600`) are set, so do not copy it to a shared directory or under source control.

`config show` does not display the actual value; it masks all but the first 8 and last 4 characters. The displayed value cannot be reused. If you lose the actual value, switch to a new key according to [Manage Project API Keys](../operations/manage-project-api-keys.md).

## 3. List, check, switch, and delete profiles

### `config list`

```bash
jai-studio config list
```

| Argument or flag | Required | Default | Meaning |
|---|---:|---|---|
| None | — | — | Outputs a JSON list of profile names and the active name. |

### `config show`

```bash
jai-studio config show [name]
```

| Argument or flag | Required | Default | Meaning |
|---|---:|---|---|
| `[name]` | No | Active profile | Displays the profile name, environment, resolved endpoint URL, and masked key. |

```bash
jai-studio config show development
```

### `config use`

```text
jai-studio config use <name>
```

| Argument or flag | Required | Default | Meaning |
|---|---:|---|---|
| `<name>` | Yes | — | Makes an existing profile active. |

```bash
jai-studio config use project-b
```

### `config delete`

```text
jai-studio config delete <name>
```

| Argument or flag | Required | Default | Meaning |
|---|---:|---|---|
| `<name>` | Yes | — | Deletes the profile from the configuration file. |

When you delete the active profile, the first of the remaining names becomes the next active profile. If none remain, there is no active profile. Deleting a profile does not revoke the Project API key itself. Revoke unused or leaked keys in [Manage Project API Keys](../operations/manage-project-api-keys.md).

## 4. Check the runtime priority order for endpoint and key

For CLI commands that require authentication, the endpoint URL and API key are resolved **separately**. Both use the following priority order: command flag, environment variable, then profile.

| Setting | 1. Highest priority | 2. Next | 3. Last | Notes |
|---|---|---|---|---|
| Endpoint URL | `--env <env>` | `JAI_STUDIO_API_URL` | Endpoint of the profile selected with `--profile <name>`, or the active profile | For `--env`, specify a known environment name or complete URL. |
| API key | `--key <key>` | `JAI_STUDIO_API_KEY` | Key in the selected profile | `--key` may remain in history or a process list, so do not normally use it. |
| Project | There is no global flag, environment variable, or profile item for specifying a Project | — | The Project to which the API key applies | To change Projects, use a profile with a key for that Project or a secure environment variable. |

`--profile <name>` selects the named profile only for that run; it does not modify the active setting. For example, the following selects the `project-b` profile, but if `JAI_STUDIO_API_KEY` exists, only the key is still taken from the environment variable.

```bash
jai-studio --profile project-b workflows list
```

Even if you override only `--env` or `JAI_STUDIO_API_URL`, the key is resolved separately. To avoid combining a different endpoint and key, check the endpoint with `config show` before running, and explicitly specify the required variables in CI.

## Global flags and output

| Flag | Meaning |
|---|---|
| `--profile <name>` | Selects the profile for this run. |
| `--env <env>` | Overrides the endpoint. |
| `--key <key>` | Overrides the API key. Do not normally use it. |
| `--pretty` | Formats successful JSON output. |
| `--debug` | Displays detailed server error responses. Do not use it in shared logs because it may contain confidential information. |
| `--version` | Displays the CLI version. |

Successful CLI results are output as JSON to standard output and can be formatted with `--pretty`. There is no global `--json`. `--json` is a flag specific to `pages deploy`; do not apply it generally to other commands.

## Handling error output

In SDK 0.11.0, the CLI outputs JSON containing `error`, `statusCode`, and `details` to standard error on failure. When the API returns a `message`-format error body, deliberately returned `code` and object-format `params` are retained in `details`, so they can help isolate issues such as an incomplete rule ID. Other raw response bodies are normally hidden. Because `--debug` outputs details, use it only in an isolated investigation environment where Secrets, credentials, and personal information cannot be output.

## Troubleshooting

| Situation | Check or response |
|---|---|
| You get an endpoint-not-configured error | Set `--env`, `JAI_STUDIO_API_URL`, or the endpoint in the selected profile. Check the endpoint with `config show`. |
| You get a key-not-configured or authentication error | Do not work around it by adding `--key`; check `JAI_STUDIO_API_KEY` or the actual value in the target profile, the target Project, expiration date, and scopes. Do not use the masked display. |
| A profile cannot be found | Check its name with `jai-studio config list`, and create it with `config set` if necessary. |
| CI waits for interactive input | Use `--key-stdin` or `JAI_STUDIO_API_KEY`. Configure it so the actual value is not output to standard output or logs. |
| You need error details | Normal errors are displayed as JSON on standard error. Because `--debug` outputs response details, use it only in a safe temporary environment and do not share its results. |

The exit code is 1 for errors or cancelled interactive input. Normal error output is JSON containing `error`, `statusCode`, and `details`; response details are output only with `--debug`. In automation, separate standard output from standard error, and do not treat a nonzero exit code as success.

## Checklist

- [ ] I checked Node.js 22 or later and the verified CLI version.
- [ ] I prepared a key with the minimum scopes required for the target Project.
- [ ] I did not write the key in an argument and used interactive input, `--key-stdin`, or an environment variable.
- [ ] I checked the target profile and endpoint with `config show`.
- [ ] In CI, I checked the priority order of profile, endpoint, and key.
- [ ] I have not shared a masked key, detailed debug output, or an actual key value.

## Next articles to read

- [Execution Modes, Authentication, and Supported Scope](../development/execution-modes-and-auth.md)
- [Development Environment, Versions, and Compatibility](../development/dev-environment-and-versions.md)
- [Manage Project API Keys](../operations/manage-project-api-keys.md)
- [Preview a Page Locally and Deploy It](../development/local-preview-and-deploy-page.md)
- [CLI Agents](cli-agents.md)
- [CLI Objects](cli-objects.md)
- [CLI Storage](cli-storage.md)
- [CLI Workflows](cli-workflows.md)
- [CLI Pages](cli-pages.md)
- [CLI Members](cli-members.md)
- [Install the Development Support Skill with the CLI](install-dev-skill-via-cli.md)
