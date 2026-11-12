# Create and Use Skills

By bundling recurring work procedures, output formats, supporting files, and required tools into a Skill, you can more easily reuse them across multiple Agents. This article explains how to design a Skill, create and edit it in Skill Builder, import an existing package, and add and verify it in an Agent.

- To add a Skill to an Agent: [Configure Tools, Skills, and Datasets for an Agent](configure-agent-tools-skills-datasets.md)
- Skill-specific specifications and limits: [Skill Specifications and Limits](skill-specs-and-limits.md)

## What is a Skill?

A Skill is a **folder that bundles the instructions, code, and resources** required for a particular task. An Agent determines and loads the Skills it needs according to the request. Because you can manage templates, reference files, and processing code together, Skills are useful when you want multiple Agents to reproduce the same procedures and outputs.

| Best suited for | Example | Consider another approach for |
|---|---|---|
| Standardized decisions and work procedures | Extracting key points from meeting minutes in a fixed format | One-time requests or requests that vary substantially with each conversation |
| Common output rules | Creating answers in the order "Conclusion, rationale, items to confirm" | Basic instructions that determine the Agent's overall behavior and tone |
| Reusable materials | Document templates, helper scripts, and reference files | The current business materials themselves. Consider a Dataset or similar option as needed. |

Simply adding a Skill does not mean the same processing will always be performed for every request. Clearly separate when to use it, what to check, and what to include in the output among the Skill description, its body, and the Agent instructions.

## Before you begin

- Check the target organization and Project, and confirm that you have permission to create and edit Skills. If the screen displays "Editor or Admin role is required to create or edit skills.", check with an administrator.
- Search existing Skills to check whether there is one for the same purpose or one that should be updated.
- Organize the target work, expected inputs, completion criteria, users, and required tools.
- When operating an external service, check the connection, permissions, and test target first. Do not write credentials, passwords, API keys, or Secret values in the Skill body or attached files.
- Prepare low-impact test inputs and expected outputs.

> Creating and editing a Skill, and adding it to an Agent to make it available, are separate actions. Do not treat it as having been applied to an Agent immediately after creation; confirm where it was added and how it actually works.

## 1. Design the process to reuse

Do not create a large Skill from the start. Make it the smallest unit that completes one purpose. For example, rather than "create sales materials," limit the input and output to something such as "create a proposal outline from meeting notes."

| Item to organize | What to decide |
|---|---|
| Purpose | What the Skill completes and how to determine that the result is complete |
| Input | What users need to provide and what to ask when it is missing |
| Procedure | The order in which to check, process, and review |
| Output | Headings, tables, writing style, required items, and how to show rationale |
| Exceptions | How to respond without guessing when information is insufficient, there is no rationale, or execution is not possible |
| Tools | The tools that need to be used and their purpose. Do not select unnecessary tools that make updates. |
| Materials | Files reused within the Skill, such as templates and scripts |

### Write descriptions and instructions separately

Because the "description" is also used by the Agent to decide whether to select the Skill, describe the purpose, target, and expected deliverable specifically in language that users can understand. Descriptions such as "handle anything" or "convenient Skill" do not make clear when to use it.

In the body instructions, write the processing order and decision criteria. Use verifiable wording as in the following examples.

| Avoid | Rewrite example |
|---|---|
| Summarize appropriately | Read only the specified meeting minutes and output the content under "Decisions," "Owners and deadlines," and "Open items." |
| Check if necessary | If a date, owner, or deadline is missing, ask for it as a missing item without guessing. |
| Make the content correct | Do not add items unsupported by the input; state "This could not be confirmed from the input." |

Instructions should cover not only the normal flow but also prohibited actions and how to respond in exceptional cases. However, writing "do not send externally" in the Skill body does not revoke the permissions of the tools or connections themselves.

## 2. Create a new Skill

1. In the target organization or Project, open the list of "Skills."
2. Select "New Skill." Depending on the screen, "Skill Builder" opens.
3. Open "Configuration" and enter a "Skill ID." Use lowercase ASCII letters, numbers, and underscores in the Skill ID. Make sure it does not duplicate an existing ID.
4. Enter a name users can understand in "Display Name."
5. In "Description," specifically enter the requests for which the Skill is used and what it returns.
6. Select "Tools" only when necessary. The selected tools become available to this Skill.
7. If necessary, use "Select Icon" to set an image for identification. You can also save without an image.
8. Open "SKILL.md" and describe the procedures, decision criteria, output format, and handling of exceptions organized in the previous section. Check the supporting files in the file list. If you refer to them from the body, make their names and purposes consistent.
9. Save, and confirm that there are no save errors.

SKILL.md requires frontmatter. Include at least `name` and `description`. The on-screen "Skill ID," "Display Name," "Description," and selected "Tools" correspond to the information in SKILL.md. Even when editing SKILL.md directly, confirm before saving that these are as intended.

> If "Failed to save" is displayed, do not treat creation as complete. Check the frontmatter in SKILL.md, the Skill ID format and duplication, required permissions, and the error displayed on the screen.

## 3. Import an existing Skill package

When using a prepared Skill, import it from "Upload Skill."

1. Open the Skill you are creating or editing in "Skill Builder."
2. Check "Upload Skill" in "Configuration."
3. Select "Select ZIP file," or drag and drop a file onto the screen. On screens where you can select a folder, you can also use "Select folder."
4. Check the ZIP contents. Confirm that it includes SKILL.md and the supporting files required for processing.
5. After importing, check the "Skill ID" and "Description" in SKILL.md, the tools, and the file list.
6. Review the content for your organization's purpose, connections, and sharing scope, then save it.

When using a ZIP file, SKILL.md is required. You cannot import it if its format or structure is invalid, or if the frontmatter in SKILL.md does not contain `name` or `description`. If the same Skill ID already exists, a confirmation about overwriting may be displayed. To avoid updating an unintended existing Skill, check the name and content before continuing.

> Do not import a Skill from an unknown download source, or code or files whose content you cannot check, as-is. Check external transmission, file operations, and how credentials are handled, and validate it in an approved test environment.

## 4. Check the sharing scope and editing content

The sharing scope displayed on the screen is either "Organization" or "Private." Select it according to the users and Agents that will use it. Available choices and whether changes are allowed may differ depending on where you perform the action, your permissions, and availability status.

| What to check | Check details |
|---|---|
| When testing privately | Confirm whether the Skill is for validation by you alone |
| When using it in the organization | Confirm whether the Skill body, supporting files, and selected tools may be shown to users in the organization |
| When adding it to a shared Agent | Confirm that Agent sharing and Skill sharing are separate settings |
| When updating | Check affected Agents, users, existing procedures, and outputs before saving |

If you add a private Skill to an Agent shared with the organization, guidance stating "Other users cannot use private skills. Please change the skill's visibility settings." may be displayed. Do not broaden the sharing scope solely to remove the warning; check the Skill content and intended users.

If you link an external Skill to a Project, that Skill is read-only within the Project. If you want to change its content, update it at its source. Check the candidates displayed on screen for Skills that can be linked and the permissions required.

## 5. Enable and test it in an Agent

1. Open the edit screen for the Agent that will use the Skill you created.
2. Select "Add" under "Plugins."
3. Search for and select the Skill you created from the candidates displayed as "Skills."
4. Check the name, description, sharing scope, and required tools and authentication, then select "Add."
5. Confirm that the Skill appears in the Agent's "Plugins" section.
6. Supplement the Agent instructions with the purpose of the work that uses the Skill, conditions to prioritize, and how to check the output.
7. Run it with a small input that does not contain confidential information, and check the procedure, rationale, output format, and tool-use results.

If "Would you like to activate this skill for this agent?" is displayed after creation, check the target Agent and impact before selecting "Activate." If you configure it later, you can add it from the Agent by following the preceding steps. For detailed addition, authentication, and removal of settings, see [Configure Tools, Skills, and Datasets for an Agent](configure-agent-tools-skills-datasets.md).

### Check after updating as well

When you update a Skill, test at least a representative normal input, an input with insufficient information, and a condition that must not be executed. If you changed the tools or sharing scope, also check with accounts of the intended users. If "Version History" is displayed, do not confuse the current version with previous versions, and follow the guidance that prevents saving while viewing a previous version.

## When things do not work

| Situation | What to check or do |
|---|---|
| "New Skill" or editing actions are not displayed | Check the organization, Project, permissions, and feature availability status |
| Cannot save | Check the frontmatter in SKILL.md, Skill ID, description, duplication, and on-screen errors |
| Cannot import a ZIP | Check the ZIP format; the presence and location of SKILL.md; the content and structure; and upload permissions |
| Users cannot use a Skill | Check the sharing scopes of both the Agent and the Skill, the user's access, and required authentication |
| The procedure or format is not as intended | Review the purpose in the description, the procedure and exception conditions in SKILL.md, the Agent instructions, and the test input |
| Cannot confirm updates | Check the save result, the version currently displayed, and the Skill configured in the Agent |

## Checklist

- [ ] I decided the reusable purpose, inputs, completion criteria, and handling of exceptions.
- [ ] I set a display name users can understand and a specific description of the purpose.
- [ ] I documented the required frontmatter and verifiable procedures and output conditions in SKILL.md.
- [ ] I selected only necessary tools and did not include credentials in the body or files.
- [ ] I checked the content and safety of imported files and code.
- [ ] I checked the sharing scope and impact on users of shared Agents.
- [ ] I added it to an Agent and confirmed that the setting remains.
- [ ] I checked the output, rationale, and operation of required tools using a small input.

## Next articles

- [Configure Tools, Skills, and Datasets for an Agent](configure-agent-tools-skills-datasets.md)
- [Test and Improve an Agent](test-and-improve-agents.md)
- [Skill Specifications and Limits](skill-specs-and-limits.md)
