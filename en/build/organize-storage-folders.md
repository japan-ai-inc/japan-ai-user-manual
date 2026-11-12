# Organize Storage Folders

Storage is a place where you can arrange materials, input files, processing results, and other files used in a Project by folder. Deciding where the items you build will store files in advance makes it easier for creators and users to select the same destination, and to check references passed to Agents and Workflows and target folders for Storage triggers.

This article explains how to decide a folder structure before building, create and organize folders on the Storage screen, and check how to use them from items you build. For uploading and retrieving files, see [Use Files in Storage](../use/use-storage-files.md).

- Configure a Storage trigger: [Configure Workflow Triggers](configure-workflow-triggers.md)
- Upload and reference files: [Use Files in Storage](../use/use-storage-files.md)
- Conditions such as file formats and capacity: [Storage Specifications and Limits](storage-specs-and-limits.md)

## Purpose of deciding a folder structure

Folders serve as shared markers for the input and output locations of items you build. First, consider the following three aspects separately.

| Aspect to separate | What to decide | Example |
|---|---|---|
| Purpose | Roles such as input, reference, output, and in progress | `01_input`, `02_reference`, `03_output` |
| Business unit | Department, customer, case, business name, fiscal year, and so on | `sales`, `customer-a`, `2026` |
| Management unit | Who updates it and when it is reviewed | Organize after a case ends; check output monthly |

Start with the minimum structure that lets users choose a storage destination and lets builders explain reference and monitoring locations. If uses increase, add folders after checking the impact on existing references.

## Before you begin

- Check the target Project and files and folders stored in existing Storage.
- Decide what to accept as input, what to save as deliverables, and who will update them.
- Identify the Agents, Workflows, Pages, and people responsible that use Storage.
- If using a Storage trigger, decide the folder to monitor, whether to include subfolders, and the events that should start it.
- Confirm that you have permission to create, rename, move, and delete. Available operations and their display vary by permissions and organization settings.

> **Check references before organizing folders that are already in use.** The Storage screen can copy file and folder paths, and a Workflow Storage trigger can select a target folder. This screen alone does not confirm whether existing references and settings update automatically after renaming or moving. Before changing anything, check the Agents, Workflows, Pages, and procedures that reference it, and test the actual method of use afterward.

## 1. Create a structure plan

First, make a table of folder roles that matches the flow of the items you build and their users. For example, when receiving applications, having an Agent check their contents, and saving results, you can separate folders as follows.

| Example folder | Contents | Main users and items | Operational notes |
|---|---|---|---|
| `01_input` | Applications, CSV files, and attachments users add | Users, Agents that read input, and starting Workflows | Decide how not to mix unprocessed and processed items |
| `02_reference` | Criteria, templates, and reference materials | Agents and creators | Decide the update owner and how versions are handled |
| `03_output` | Generated reports, check results, and exports | Users and subsequent operations | Decide how to find them, such as including a date or target in output names |
| `99_archive` | Materials no longer in use | Administrators | Decide when to move them and how to confirm no references remain |

### Decide naming rules

Name folders so that people who see them on the screen can determine their purpose. Consistent notation across the team makes storage and reference locations easier to communicate.

| Aspect | Example | Approach |
|---|---|---|
| Sort order | `01_input`, `02_reference`, `03_output` | Use sequential numbers only when you want to show folders in purpose order |
| Target identification | `customer-a`, `2026-q1` | Use notation the team understands for customers, cases, and periods |
| Status identification | `working`, `approved`, `archive` | Avoid mixing work in progress with confirmed items |
| Name stability | `input`, `output` | Avoid frequently changing locations used by items you build based only on short-term business names or assignee names |

Because folder names may reveal their content, confirm that they do not contain confidential information.

> Putting dates, versions, and assignees all in the folder hierarchy makes storage locations deep. Keep them shallow enough for daily navigation, such as by including the date in the file name.

## 2. Create a folder

1. Open "Storage" in the target Project.
2. Navigate to where you want to create the new folder. Check the current location in the breadcrumb at the top of the screen.
3. Select "Create Folder."
4. Enter a "Folder Name" in "Create New Folder."
5. Select "Create."
6. Confirm that the created folder appears in the list and that "Folder created" appears.
7. If necessary, open the folder and create subfolders by following the same procedure.

If you created a folder in the wrong location, review the current breadcrumb and your structure plan. If you cannot create it, check your permissions and the reason shown on the screen.

## 3. Align file destinations with references in items you build

After creating the structure, confirm that the people who place files and the people who configure items you build point to the same folders.

### Separate inputs and outputs

1. Decide the folder where users add files. If necessary, share an explanation or operating rule with users.
2. Decide a folder for processing results that is separate from the input folder.
3. Decide whether to update and use the same file or retain results as separate files.
4. Use a test file to confirm that it can be saved to and retrieved from the intended folder.

If inputs and outputs are in the same location, users may select deliverables as inputs again, or it may become harder to find items to process. Particularly for a design that processes files when they are added, make the input folder clear and consider separating the output and storage destinations.

### Reference files and folders with an Agent

In the Storage list, you can select "Copy path (for use with agents)" for a target file or folder. You can use the copied path to specify a reference in a Project conversation.

- Do not construct paths manually; use the value copied from the screen.
- When asking an Agent, state the target, the processing you want it to perform, and whether it may make changes or send externally.
- When you specify a folder, its files are not necessarily always handled in the same way. Confirm from the answer or run result that the intended file was referenced.

For how Agents reference files and how files stored in Storage are handled, see [Use Files in Storage](../use/use-storage-files.md).

### Monitor folders with a Workflow Storage trigger

When configuring a Storage trigger, you can select "Target Folder" in the Workflow start node. Configure whether to select "Include subfolders" and the event types to target (new folder creation, new file upload, file update, or file deletion) according to the structure plan.

For example, monitoring only `01_input` lets you consider a design that does not treat saving deliverables to `03_output` as input. If you use subfolders as input units, check the "Include subfolders" setting together with the depth to include. For configuration, publishing, and confirmation using actual events, see [Configure Workflow Triggers](configure-workflow-triggers.md).

> The availability and display of Storage triggers may vary according to conditions such as feature settings. After changing the structure, check before and after publishing that the target folder, subfolder handling, and event type are as intended.

## 4. Rename, move, and delete safely

In Storage, you can select items to rename, move, or delete them. You can also move or delete multiple selected items. If the folders being organized are used by items you build, proceed in the following order.

1. Check the target folder and the files under it.
2. Check whether Agents, Workflows, Pages, procedures, and so on use the target name or location.
3. Share the change and its implementation time with affected users and people responsible.
4. Perform "Rename" or move. If an item with the same name exists, review the content before deciding between options such as replacement or retaining both.
5. Update item settings and procedures as necessary.
6. Confirm file references, saving, trigger starts, and so on using the actual method of use.

When deleting a folder, a message indicates that all files and folders under it will also be deleted and cannot be restored. Do not delete it until you can determine it is unnecessary; check files that must be kept, items that reference it, and migration destinations.

In the organization drive, if a non-administrator tries to delete another user's completed file, deletion is blocked on the screen. Whether a folder or other target can be deleted is checked server-side for each target. If you cannot delete it, check with its creator or an administrator. When a same-name item conflicts, review the choices shown on the screen before proceeding.

Files managed by an App may be displayed as read-only and may not allow normal modification. Before updating or deleting an App or changing settings for use, check with the App's person in charge.

## Checklist

- [ ] Checked the target Project and existing Storage structure
- [ ] Separated input, reference, output, and storage roles to the needed extent
- [ ] Decided folder-name notation, sort order, and update owner
- [ ] Created folders at the intended hierarchy with "Create Folder"
- [ ] Aligned the location where users save files with where items you build reference and save them
- [ ] Obtained Agent paths with "Copy path (for use with agents)"
- [ ] When using a Storage trigger, checked the target folder, subfolders, and event type
- [ ] Checked existing references before changes and tested the actual method of use afterward
- [ ] Checked files and folders below the target and items in use before deletion

## Next article to read

- [Use Files in Storage](../use/use-storage-files.md)
- [Configure Workflow Triggers](configure-workflow-triggers.md)
- [Storage Specifications and Limits](storage-specs-and-limits.md)
