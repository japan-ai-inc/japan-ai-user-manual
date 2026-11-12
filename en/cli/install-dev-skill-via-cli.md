# Install the Development Support Skill with the CLI

`jai-studio skill install` copies the `studio_sdk` Skill bundled with `@japan-ai-inc/studio-sdk` into a local Skill directory that Codex or Claude Code can discover. It is intended for developers who want coding agents to reference the relevant SDK and CLI usage when developing with the Studio SDK/CLI.

This command does not operate a Studio Project. A Project API key, CLI profile, `gh`, GitHub account, and network connection are not required. For keeping the SDK, CLI, and Skill on the same verified version, see [Development Environment, Versions, and Compatibility](../development/dev-environment-and-versions.md); for choosing a development path, see [Choose Between the Web UI, AI Assistance, SDK, CLI, and API](../development/choose-dev-path.md).

## Related articles

- [Choose Between the Web UI, AI Assistance, SDK, CLI, and API](../development/choose-dev-path.md)
- [Development Environment, Versions, and Compatibility](../development/dev-environment-and-versions.md)
- [CLI Configuration and Profiles](cli-config-and-profiles.md)

## Before you begin

The target package is `@japan-ai-inc/studio-sdk`, and the executable name is `jai-studio`. Install Node.js 22 or later and the CLI that supports the Skill you want to use. This article covers SDK **0.11.0**.

```bash
npm install --global @japan-ai-inc/studio-sdk@0.11.0
jai-studio --version
```

Installing a Skill is independent of Project operations that require an API key, such as `pages deploy` and `workflows`. Therefore, you do not need to provide `--profile`, `--env`, `--key`, or `JAI_STUDIO_API_KEY`. Configure CLI operations that require authentication according to [CLI Configuration and Profiles](cli-config-and-profiles.md).

> This Skill is supplementary information for coding agents. Do not determine that a Studio configuration change, deployment, publication, or external transmission is correct based only on an agent's proposal or command result. For operations that make changes, check the target Project, ID, input, and result individually.

## What the Skill includes

The installed Skill is named `studio_sdk`. The package includes a snapshot containing `SKILL.md`, which is required for coding agents to reference published SDK/CLI usage, along with reference Markdown and helper scripts. Its scope covers authentication boundaries for the Studio SDK/CLI; Agents, Members, Objects, Storage, Workflows, and Page packaging and deployment; and use of the public SDK within PRS.

Installation copies the bundled Skill in the published package; it does not retrieve the provider again.

In the Skill bundled with SDK 0.11.0, Page deployment assistance does not add `<base>` to HTML. The former `--no-inject-base` and `--allow-root-assets` options were removed, so remove them from existing scripts. `deploy.yaml` is metadata for the hosting environment and is not included in the bundled Skill or installation destination. The Skill also includes guidance about Agent definitions, record access rules, scheduled tasks, and associating static Pages with Workflows.

> Do not add API keys, tokens, real resource IDs, customer data, signed URLs, or internal-only procedures to the Skill. If you edit an existing `studio_sdk` directory yourself, its contents will be treated as different from the bundled Skill during the next update.

## 1. Choose the target agent and installation scope

On an interactive terminal, run the command without arguments to choose the target agent and scope.

```bash
jai-studio skill install
```

For non-interactive execution such as CI, **both** `--agent` and `--scope` are required.

```text
jai-studio skill install --agent <codex|claude-code|all> --scope <project|user> [--force]
```

Notation containing `<...>` below is **syntax that requires replacement**. Do not paste the angle brackets into the shell; replace them with actual values.

| Argument or flag | Required | Default | Meaning |
|---|---:|---|---|
| `--agent <agent>` | Yes, when non-interactive | Interactive selection | Specify `codex`, `claude-code`, or `all` to install for both. |
| `--scope <scope>` | Yes, when non-interactive | Interactive selection | Specify `project` for only the current Project or `user` for the current OS user. |
| `--force` | No | None | Replaces an existing Skill whose contents differ. |

The following is an example for using Codex only in a Project. If you run it from within a Git repository, the nearest Git root, rather than the current subdirectory, is targeted.

```bash
jai-studio skill install --agent codex --scope project
```

The following is an example for installing for both supported agents on a personal computer.

```bash
jai-studio skill install --agent all --scope user
```

## Installation destinations

`project` searches for the nearest Git root. If no Git root is found, it uses the current directory where you run the command as the Project root. `user` is based on the current OS user's home directory.

| Agent | `project` installation destination | `user` installation destination |
|---|---|---|
| Codex | `<Git root>/.agents/skills/studio_sdk` | `~/.agents/skills/studio_sdk` |
| Claude Code | `<Git root>/.claude/skills/studio_sdk` | `~/.claude/skills/studio_sdk` |

With `--agent all`, the CLI checks both installation destinations before copying. If either contains a different Skill that requires replacement and `--force` is not specified, it does not install only the other one first. For Project scope, it also stops if the destination contains symbolic links.

## 2. Check the result and start the agent

On success, JSON is printed to standard output. `skill` is `studio_sdk`, `scope` is the specified scope, and `installs` contains the agent, installation `path`, and status. `--pretty` is a CLI-wide flag; place it near the beginning when you want formatted output.

```bash
jai-studio --pretty skill install --agent claude-code --scope project
```

Check statuses as follows.

| Status | Meaning | Action |
|---|---|---|
| `installed` | There was no installation destination, so the Skill was copied as new. | Start a new agent session. |
| `unchanged` | The files and contents at the destination matched the bundled Skill. | No change is needed. |
| `replaced` | You specified `--force`, replacing a different existing Skill with the bundled Skill. | Check whether the custom changes were needed, then start a new session. |

An installed agent that is already running may not rediscover the Skill. After installation or update, start a **new session** of the target Codex or Claude Code. Then request SDK, CLI, or Page implementation and verification as usual. Do not give the agent secrets in conversation or code, and inspect generated commands before running them.

## Updates, overwrites, and uninstallation

The Skill corresponds to the version of the SDK package that provides the CLI. Before updating, check the package's `CHANGELOG.md`, update the SDK and CLI to the same full version, and verify required representative operations in a test environment. In the 0.x series, minor updates can include breaking changes. For update decisions, see [Development Environment, Versions, and Compatibility](../development/dev-environment-and-versions.md).

```text
npm install --global @japan-ai-inc/studio-sdk@<verified version>
jai-studio --version
jai-studio skill install --agent codex --scope project
```

If the contents are the same, rerunning results in `unchanged` and does not modify files. If the contents at the same installation destination differ after a package update, the command errors to protect the existing contents. Compare and back up the contents, and use `--force` only when you decide to replace them with the bundled version.

```bash
jai-studio skill install --agent codex --scope project --force
```

This CLI has no `skill uninstall` subcommand. When it is no longer needed, delete only the `studio_sdk` directory identified in the applicable table using normal OS or repository file operations. Deleting user scope makes the Skill unavailable to that OS user's applicable agent. Before deletion, check the scope, path, and that there are no custom additional files.

## If something does not work

| Situation | What to check and do |
|---|---|
| A non-interactive run says that selection is required | Specify both `--agent` and `--scope`. The only values are `codex`, `claude-code`, `all`, `project`, and `user`. |
| It says that a different Skill exists | Check for custom changes at the destination. Stop if you want to retain them, and use `--force` only when replacement is approved. |
| The Project-scope location is not what you expected | Check the nearest Git root from the current directory. If there is no Git root, the current directory is the base. |
| The agent does not use the Skill after installation | Check the installation result's `path` and `SKILL.md`, then start a new session of the target agent. Do not guess and copy it to a location for an unsupported agent. |
| The CLI itself cannot be found or has the wrong version | Check the executable and version with `command -v jai-studio` and `jai-studio --version`, then reinstall according to [Development Environment, Versions, and Compatibility](../development/dev-environment-and-versions.md). |

When the CLI fails, it outputs a JSON-formatted error to standard error and exits with code 1. `--debug` is for displaying server response details and is normally unnecessary for isolating this local Skill installation. For an inquiry, record the CLI version, command without keys, `--agent`, `--scope`, installation destination, and error details. Do not attach API keys, tokens, or unnecessary details of personal home directories.

## Checklist

- [ ] I checked Node.js 22 or later and the verified `jai-studio` version
- [ ] I decided whether to install for Codex or Claude Code, and for the Project or user
- [ ] I specified both `--agent` and `--scope` for non-interactive execution
- [ ] I checked the output installation destination and the `installed`, `unchanged`, or `replaced` status
- [ ] I did not use `--force` without checking a different existing Skill
- [ ] I started a new coding-agent session after installation or update
- [ ] I did not include API keys, tokens, or real resource IDs in the Skill or agent conversation

## Read next

- [Choose Between the Web UI, AI Assistance, SDK, CLI, and API](../development/choose-dev-path.md)
- [Development Environment, Versions, and Compatibility](../development/dev-environment-and-versions.md)
- [CLI Configuration and Profiles](cli-config-and-profiles.md)
