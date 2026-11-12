# Use Files in Storage

Storage is where you save files such as materials and deliverables used in a Project. This article explains how to find and obtain saved files, upload necessary files, and reference them from an Agent or similar feature.

**Saving a file, downloading it to your device, and having an Agent read it are separate actions.** Placing a file in Storage does not necessarily mean every Agent automatically reads its contents or that it becomes searchable in a Dataset.

Designing folder structures, configuring access, and using APIs or SDKs are outside the scope of this article. Use existing storage locations within the permitted scope.

## Before you begin

- Check the Project and destination folder you will use. When using an App, check the file entry point provided by the person in charge.
- Check the name, purpose, target period, and version of the file you will obtain or save.
- Handle only information approved for business use. Also check whether its contents may be shown to the people with whom it is shared.
- Check which of the following actions you may perform: saving a new file, replacing an existing file, or obtaining a file.
- Know whom to ask if you cannot perform an action.

If you have not opened where you will use it yet, begin with one of these articles.

- [Open an Installed App and Get Started](open-installed-app.md)
- [Get Started in a Shared Project](start-with-shared-project.md)

> The following instructions focus on the standard Storage screen in a desktop web browser. Screen labels and the scope of actions vary by configuration, permissions, and App structure. For attach or save buttons within an App-specific Page, follow that Page's guidance.

## 1. Open the storage location and find the target file

1. Open the target Project and proceed to an entry point such as "Storage."
2. Open folders and navigate to the storage location you need.
3. Check your current location using the folder hierarchy displayed at the top of the screen.
4. Enter part of the name or similar text in "Search by file name" to narrow the candidates.
5. In addition to the file name, check displayed information such as modification date, size, and user who uploaded it.
6. If there are many items, switch list pages to check them.

**If you cannot find a file by searching, check not only the search term but also the current folder.** The standard list retrieves files in the current folder, so an empty search result does not mean there are no files in the entire Project. Search by clearing the term, returning to the parent folder, and checking the storage location you were given, in that order.

Even files with the same name may have different storage locations or contents. Especially for materials by customer or opportunity, do not identify the target by the file name alone.

If you cannot find a Storage entry point in an App, use it from the Page or link you were given. Not every App has the same file list. You do not need to change the App's structure or permissions yourself in order to use it.

## 2. Check the contents and download it if needed

### Check it in preview

If a preview action is displayed, check the contents of the target file. How it is displayed varies by file format and other factors.

- If preview generation is in progress, check the completion or error display.
- If preview is not supported, download the file and open it with an appropriate application.
- Even if display fails, do not conclude that the source file has been deleted or corrupted.
- If it is difficult to determine the content from an image-based display or partial display alone, obtain the source file and check it.

Seeing a file on the preview screen and saving the source file to your device are different things.

### Save it to your device

1. Select the download action for the target file.
2. Check the download result in your browser.
3. Open the saved file and check that it is the required content, target period, and version.
4. Manage the local save location and whether it may be taken outside according to organizational rules.

If "Download as ZIP" is displayed when you select a folder or multiple files, you can obtain them together. Follow screen guidance for limits or errors such as the number of items and total size. Even if you obtain a ZIP file, check that the files inside are the intended ones.

If you cannot obtain it, check whether the target is still uploading, whether you have viewing permission, and whether your browser is blocking downloads. A download URL obtained previously may expire. In that case, obtain it again from the Storage screen.

## 3. Upload files

1. **Check the destination Project and folder.** Navigate to the destination before selecting files.
2. Select "Upload File."
3. Select the files to upload.
4. Review the names and sizes in "Selected Files."
5. Select "Start Upload."
6. If you are asked about a file with the same name, check the next section before deciding.
7. Check upload progress and completion or failure for each file.
8. Check the destination list for the file with the intended name.
9. As needed, preview or download it and check the content you saved.

Some screens allow you to drop files, but you must still check the destination first.

**Closing the file-selection screen does not necessarily mean the upload is complete.** On the standard screen, the dialog closes when files are added to the upload queue, then file transfer and save confirmation occur afterward. Check the "Done" display or completion notification.

When sending multiple files, check that only some have not failed. Even when uploading a failed file again, first check the destination so that you do not register a file that has already been saved twice.

Follow screen guidance for supported formats, file size, and available capacity. **Preview limits and upload limits are not necessarily the same.** Do not change only the file extension to avoid an error; ask the person in charge how to proceed if needed.

## 4. Decide what to do when a file with the same name exists

When "File Already Exists" is displayed, check the existing file name, modification date, and other details.

| Option | What it does | What to check before choosing |
|---|---|---|
| Replace | Replaces the existing file with the new file | Whether this is the correct storage location and target, whether you may update the existing material, and whether it affects work that references it |
| Keep Both | Saves the new file separately from the existing file with a number added to its name | Whether you intend to keep it as a separate file and whether users can tell which one to use |
| Cancel | Stops the conflict decision so you can review it | If you are unsure whether to replace it, check with the person in charge |

Available actions vary by permissions. Even if you can open "Upload," you may not be able to both create new files and replace files.

After selecting "Keep Both," check the **actual saved name** in the list. An Agent or business process that references the original name does not necessarily use the newly saved file with a different name automatically.

Before replacing a file, check operational procedures in advance, such as retaining required older versions according to organizational rules. This action alone does not guarantee that you can return to past content.

## 5. Reference a saved file from an Agent or similar feature

### Copy the path and make a request

The standard screen has an action called "Copy path (for use with agents)." Copy the target file's path, then make a request with its purpose in a conversation in the Project that can access that file.

For example, make the following request.

> Read the following Storage file and summarize the contract-renewal procedure under "Required documents," "Deadlines," and "Items to check." Do not infer information that is not in the material; identify it as requiring confirmation. Do not modify the source file or send anything externally.
>
> Target: [Paste the path copied from Storage]

In Project Storage, a reference beginning with `project://` is used. Rather than constructing the path manually, use the value copied from the screen as is to reduce confusion between names and folder levels.

**A path identifies a file; it does not grant viewing permission.** If the Project context, functions available to the Agent, file-access conditions, or similar requirements do not match, the file may not be readable. Pasting it into another Project or an unrelated conversation does not necessarily allow you to reference the same file.

After asking for it to be read, check whether the target file was referenced and whether the response matches its contents. Do not conclude that the text was read merely because the file name appears in the response.

For how to proceed in a conversation, see [Chat with an Agent and Review the Results](chat-with-agents.md).

### Distinguish similar actions

| Action | What to check |
|---|---|
| Upload to Storage | Whether the file was saved in the intended Project storage location |
| Attach a file to a conversation | Whether it was attached in that conversation and is the target of the request |
| Search materials in a Dataset | Whether Dataset-side preparation such as importing, synchronizing, and reference configuration is complete |
| Use a file in a Page or Workflow | Whether you follow the input or reference method that the screen or process requires |

Even when an Agent creates a deliverable, the appearance of a file in the conversation alone does not mean it was saved to the Project's Storage. If you need to save it in Storage, specify the destination and check the save result in the list.

## 6. Check the impact before renaming, moving, or deleting

Organizing files is different from obtaining or saving files. Changing a name or location can make a file unavailable to conversations, Pages, Workflows, and other items that use its prior path.

- For files used in business tasks, check their references and the person in charge before organizing them.
- When operating a folder, also check its effect on files within it.
- Some files, such as files managed by an App, have restricted change actions. Do not treat them the same as ordinary uploaded files.
- Do not change an App's update, deletion, or access settings to bypass a read-only restriction.

This article does not include renaming, moving, or deleting as required steps to begin using Storage. If you only need to obtain or reference a file, you do not need to change the source file.

## When things do not go as expected

| Situation | What to check first |
|---|---|
| You cannot find the file | Project, current folder, search term, list page, and viewing permissions |
| You cannot select Upload File | Permissions to save a new file or update one, and the reason for the displayed restriction |
| The dialog closed but the file was not saved | Queued, uploading, or failed status. Do not treat closing as completion. |
| You are asked about a file with the same name | Destination and version of the existing file, and whether you need replacement or saving with a different name |
| Preview is not displayed | Supported format and generation status. Download and check it if you have permission to obtain it. |
| You cannot download | Upload completion, viewing permission, and browser behavior. Obtain it again from the screen rather than using an old URL. |
| The Agent cannot read the material | Copied path, Project of the conversation, and the Agent's functions and permissions. Check that you are not confusing Storage saving with Dataset reference. |

If the issue is not resolved, provide the person in charge with the Project name, folder, file name, date and time you performed the action, displayed error, and action you wanted to perform. Do not share confidential materials or temporary download URLs with an unnecessarily broad audience.

## Completion checklist

- [ ] I checked the target Project, folder, and file.
- [ ] I checked the contents of the file I obtained or saved.
- [ ] I did not confuse a queued or failed upload with completion.
- [ ] I handled files with the same name as intended and checked the saved name afterward.
- [ ] When using an Agent or similar feature, I checked the reference target and reading result.
- [ ] I did not change unrelated files or permission settings.

## Related articles

- [Move Your Work Forward from a Page](work-with-pages.md)
- [Chat with an Agent and Review the Results](chat-with-agents.md)
- [Run a Published Workflow](run-published-workflow.md)
- [Storage Specifications and Limits](../build/storage-specs-and-limits.md)
- [Unable to Read or Write Objects or Storage](../troubleshooting/object-storage-read-write-issues.md)
