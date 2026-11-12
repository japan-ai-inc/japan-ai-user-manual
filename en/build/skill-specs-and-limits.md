# Skill Specifications and Limits

A Skill is a folder that groups instructions, code, and resources to reuse for a specific task. Agents use Skills based on requests, but creating and saving a Skill does not automatically add it to existing Agents. This article covers Skill-specific prerequisites, input formats, sharing scope, and limits. For creation and usage steps, see [Create and use a Skill](create-and-use-skills.md).

- Related article: [Create and use a Skill](create-and-use-skills.md)
- Related article: [Index of limits and availability conditions](../operations/limits-and-availability-index.md)

## Scope of this article

This article describes the file structure of Skills handled in the on-screen "Skill Builder," the `SKILL.md` frontmatter, uploads, tools, visibility scope, adding Skills to Agents, and editing permissions. For Agent instructions, authentication, and post-addition checks, see [Configure tools, Skills, and Datasets for an Agent](configure-agent-tools-skills-datasets.md). For design and creation procedures, see [Create and use a Skill](create-and-use-skills.md).

The displayed items, choices, and available operations vary by organization, Project, permissions, and availability status. The numbers in this article apply to input validation confirmed in the implementation; they do not indicate contractual limits or future availability conditions.

## Specifications and prerequisites

### `SKILL.md` and file structure

| Item | Specification | Notes |
|---|---|---|
| Required file | A `SKILL.md` file is required in the Skill root. | Without it, the Skill cannot be saved or imported. In the body, specify the purpose, decision criteria, procedure, output format, and how to handle exceptions. |
| Frontmatter | YAML frontmatter is required at the beginning of `SKILL.md`. | Enclose it in `---`. If the format cannot be read, correct it before saving. |
| `name` | Required. Corresponds to the on-screen "Skill ID" and must be 3–128 characters. | Use only lowercase English letters, numbers, and underscores. Hyphens, spaces, and uppercase letters cannot be used. Also avoid duplicating an existing Skill ID. |
| `description` | Required. | Do not leave it blank. Use a description that makes clear what the Skill does, its target, and its deliverable. The maximum number of characters for the description could not be confirmed from the implementation in scope. |
| Optional fields | You can include `display_name`, `agent_prompt`, and `tools` in the frontmatter. | `display_name` is the Display Name, and `tools` is a list of tool names. The on-screen "Display Name," "Description," and "Tools" and `SKILL.md` are handled in sync, so check the content after changing only one of them. |
| Supporting files and folders | You can place templates, reference materials, scripts, and similar files in the same folder as `SKILL.md`. | Ensure file names and relative locations referenced in the body match the actual structure. Do not include unnecessary credentials or confidential information. |

This is a minimal example. In `description`, write the usage scenario and result specifically.

```md
---
name: meeting_summary
display_name: Meeting summary
description: Organize decisions, owners, and deadlines from meeting notes
---

# Procedure
...
```

### "Upload Skill" and folder handling

| Item | Specification | Notes |
|---|---|---|
| Import method | In "Upload Skill," you can use "Select ZIP file," drag and drop, or "Select folder." | Even when you select or drop a folder, it is converted to a ZIP file on screen before import. A single non-ZIP file cannot be imported. |
| `SKILL.md` in the ZIP | A ZIP containing `SKILL.md` is required. | On-screen validation checks for `SKILL.md` at the top level or directly under one parent folder. If an error occurs, open the ZIP and review its placement. |
| Nested archives | A Skill ZIP cannot contain another archive file. | This prevents ZIP bombs. If source materials are compressed, extract the necessary content before including it in the Skill. |
| Paths | Paths that point outside the parent folder or absolute paths are not accepted. | Before importing, check that the ZIP does not contain unintended files or paths. |
| Viewing and editing | You can review the contents in the file list and edit `SKILL.md`. | You cannot save while viewing a past version. Return to the "Current" version before updating. |

### Tools, visibility scope, and adding to an Agent

| Item | Specification | Notes |
|---|---|---|
| "Tools" | Tools selected in "Tools" become available for use in that Skill. | Select only what is needed. Merely listing a tool in a Skill does not configure authentication or operation permissions for the connected service. |
| "Organization" visibility scope | This is the scope to set for a Skill used within the organization. | Include only instructions, supporting files, and selected tools that can be shown within the organization. |
| "Private" visibility scope | This is the scope to set for a Skill used only by you. | Other users cannot use a private Skill. You also cannot add another user's private Skill to your Agent. |
| Adding to an Agent | From "Plugins" in the Agent editing screen, select "Add" and add a choice under "Skills." | Creating or updating a Skill and adding it to an Agent are separate operations. After adding it, check that it appears in the Agent settings. |
| External Skill linked to a Project | Link an external Skill used within a Project from the choices displayed on screen. | A Skill linked from outside the Project is read-only within the Project. If you need to change its content, update it at its source. |
| Agent published to the organization | If an Agent published to the organization has a private Skill configured, a warning prompts you to change the visibility scope. | Do not change it to "Organization" only to remove the warning. Confirm that the Skill content may be shared. Sharing an Agent and the visibility scope of a Skill are separate. |
| Create and edit permissions | Creating and editing a Skill requires Editor or administrator permissions. | Being able to view or use a Skill is different from being able to create or edit it. If an operation is not displayed or you cannot save, check with an administrator. |

## Limits and limit values

The following values are limits confirmed for Skill input, ZIP files, and icons. "Less than or equal" includes the maximum value, while values that exceed it are rejected. When you select a folder, it is also validated after conversion to ZIP.

| Item | Limit or limit value | Conditions and notes |
|---|---|---|
| Compressed ZIP file size | 20MB or less | Applies to a ZIP selected with "Select ZIP file," uploaded by drag and drop, or created from a folder. |
| Number of files in a ZIP | 1,000 or fewer | This is the number of files excluding directories themselves. |
| Total size after extraction | 100MB or less | Applies to the total after extracting files in the ZIP. |
| Individual file size after extraction | 50MB or less | Applies to on-screen ZIP content validation. |
| Compression ratio | 100 times or less | This is the total size after extraction divided by the compressed ZIP size. If the compressed size is 0, the ratio is not validated. |
| Skill icon | 1MB or less; PNG, JPEG/JPG, or WebP; each dimension 10,000px or less | This limit applies only when you set an icon. Confirm that both the input image and converted image are 1MB or less. You can save without an image. |

## Common misconceptions

- **Creating a Skill does not make it available in every Agent.** Add it to "Plugins" for the Agent that will use it, and confirm that its visibility scope is accessible to users.
- **Setting it to "Organization" does not give everyone permission to operate external services.** Check the Skill visibility scope, Agent sharing, tool authentication, and permissions for data at the connected service separately.
- **Placing only `SKILL.md` does not mean the content can be arbitrary.** The frontmatter requires `name` and `description`, and the Skill ID has format and duplication restrictions.
- **Selecting a folder does not bypass size limits.** The folder is converted to ZIP and undergoes the same ZIP validation.
- **Selecting a tool for a Skill does not mean it will always run.** Check the Agent instructions, request content, authentication and permissions, and runtime conditions, then validate results with low-impact input.
- **You cannot directly update a past version while it is open.** Past versions are read-only.

## If you cannot confirm it

If the screen display or save result differs from this article, do not treat the work as complete. Check the following.

1. Record the target organization, Project, Skill name, Agent name, and visibility scope: "Organization" or "Private."
2. Check the `SKILL.md` frontmatter, the location of `SKILL.md` in the ZIP, the size and number of files, and the selected "Tools."
3. Record the date and time of the operation, steps performed, displayed errors, and results after reopening following the save. Do not share credentials, Secrets, or the contents of confidential files.
4. Check permissions, availability status, and visibility scope with an administrator. For limits, contract conditions, or availability not guaranteed as specifications, see [Index of limits and availability conditions](../operations/limits-and-availability-index.md) or contact the responsible person.

## Next articles to read

- [Create and use a Skill](create-and-use-skills.md)
- [Configure tools, Skills, and Datasets for an Agent](configure-agent-tools-skills-datasets.md)
- [Test and improve an Agent](test-and-improve-agents.md)
- [Index of limits and availability conditions](../operations/limits-and-availability-index.md)
