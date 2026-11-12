# View and Update Object Data

An Object is a feature for managing business data such as customers, opportunities, and tasks in defined fields. One data item is called a "record," and items such as name, status, and due date are called "fields."

This article explains how to find a target record in an existing Object, check its contents, and update permitted fields. It does not cover creating a new Object, adding or deleting fields, or changing access settings.

**Changing a record value and changing an Object's structure are different actions.** For example, "change a task due date" is a data update, while "add a field named due date" is a structure change.

## Before you begin

- Check the Project or App you will use and the Object's name and purpose.
- Prepare information that identifies the target record. Checking an administration number, customer, or similar detail as well as the name helps prevent confusion.
- Check which actions you may perform: viewing only, updating, or creating a new record.
- For work in which an update triggers subsequent processing such as notifications or Workflows, check the impact with the person in charge.

If you have not opened where you will use it yet, begin with one of these articles.

- [Open an Installed App and Get Started](open-installed-app.md)
- [Get Started in a Shared Project](start-with-shared-project.md)

> The following instructions focus on the standard Object screen in a desktop web browser. Displayed items and actions vary by permissions, organization configuration, and Object configuration. Input and saving methods may differ in an App-specific Page.

## 1. Open the target Object

### Open it from a Project

1. Open the Project you were given.
2. Open an entry point to the Object list, such as "OBJECT" or "Objects."
3. Check the Object name and description in the list, then open the target.
4. Check that the displayed screen is the record list for the Object you need.

Use the screen where business data is listed, not the field-settings screen. If you only view or update existing records, you do not need to change field definitions.

### Use it from an App Page

When a Page such as a customer list or task management screen is provided, use the Page you were given by the person in charge. **App users do not necessarily have direct access to the Object list.**

For actions such as "Save" and "Update" within a Page, follow that Page's guidance. Do not apply the double-clicking or automatic saving described below for the standard screen directly to an App-specific Page.

- [Move Your Work Forward from a Page](work-with-pages.md)

## 2. Find the target record

In the standard record list, use search filters and display fields to narrow the scope you will check.

### Specify search filters

1. Open "Search filters."
2. Select the search-target item in "Field name."
3. Select a comparison method appropriate for your purpose, such as exact match or partial match, in "Operator."
4. Enter the required "Value" and select "Search."
5. Check the displayed record contents.

For example, when you know an administration number, search by that field using an exact-match condition. When searching by a person's or company name, check that records with similar names are not included. Available conditions and input formats vary by field type.

If you cannot find the record, clear the search filters and check the target Object and the value you entered. Also check page switching when the results span multiple pages.

**An empty search result does not necessarily mean the data does not exist.** It may not be visible because of search filters or your viewing scope. Do not create a new record for the same data until you have confirmed it.

### Adjust displayed fields

If "Select display fields" is available, display the items you want to check. For example, displaying an administration number, person in charge, and status in addition to a name makes it easier to verify the target.

Selecting displayed columns and deleting a field are separate actions. Do not use "Delete field" or change Object settings to hide a column.

## 3. Check record details

1. Compare the administration number, name, and other details in the list.
2. If an arrow or similar control for opening details is displayed, open details for the target record.
3. Check the pre-update values and the fields that need to change.
4. If there is a link to a related record, check that the related destination is correct.

Long text in a list may be abbreviated. Use "Show All" or the details screen so that you do not overlook omitted text.

> Even in the standard list, double-clicking a row behaves differently depending on the screen configuration: it may open details or enter edit mode. When you only want to view data, use the action that opens details and check that you have not entered edit mode.

If you cannot open a related record, check the related target's status and your viewing permissions. A link alone does not mean you can view or update all data in the related target.

## 4. Update permitted fields

### Edit one field in the details screen

In the standard details screen, double-clicking the value of an editable field enters input mode.

1. Check the target record and field again.
2. Double-click the value you want to change.
3. Enter the new value in the input field or select it from the candidates.
4. Review the input, then move outside the edit field.
5. Check that the update finishes and "Record updated successfully" is displayed.
6. Check that the displayed value is what you intended.

**In the standard details screen, saving begins when you change a value and move outside the edit field. It does not use a page-wide "Save" button.** Saving may also begin when you click another field to check it.

To discard an unsaved change, use the cancel action while editing. A value that has already been saved is not restored by canceling. If you save an input error, check the current value and business impact before correcting it.

### Edit a row in the list

If "Edit" is displayed in the row menu, you can start editing from there. Depending on the screen, it may open the details screen.

If the row enters input mode in place, change the target field and check the contents. In standard row editing, saving begins when you move outside the edited row. Check that processing is complete and that the updated value is displayed.

If a confirmation screen is displayed when you cancel changes, check the content you will discard before selecting an option. Do not repeat other actions while saving or close the screen before you confirm completion.

### What to check when entering data

| Field type or condition | What to check |
|---|---|
| Required fields | Whether they have been left blank |
| Numbers | Whether digits, units, and allowed range follow business rules |
| Date and time | Whether the target date or time and the format displayed on screen are correct |
| Options and Boolean values | Whether you selected the intended option or on/off state |
| Related fields | Whether you selected a different record with the same name |
| Fields that do not allow duplicates | Whether an administration number or similar value duplicates an existing record |

If there is an input error, correct the field indicated in the message. Some fields, such as system fields, are not normally editable. Do not change a field definition or access settings to modify a field you cannot edit.

## 5. Create a new record only when necessary

Create a new record only when it is necessary for business and you are permitted to do so.

1. Check that the same data has not already been registered.
2. Select "Create New Record" for the target Object.
3. Enter required information in the displayed fields.
4. Check that fields containing default values also fit this registration.
5. Select the registration button and check the completion display.
6. Check the record you registered in the list or details.

Values may be configured automatically depending on the field. You do not need to force an entry for a field with no input control or for an automatically generated number.

With search filters applied, a new record may not appear in the list. Even if you cannot find it immediately after creation, check the search filters and registration result before selecting the registration button again.

## 6. Check the state after updating

In addition to the update-completion display, check the following.

- You changed the intended record.
- The changed fields have the intended values.
- You did not accidentally change unrelated fields.
- When necessary, reopening the list or details still displays the updated values.

If the updated status or person in charge no longer matches the search filters, the record may disappear from the list. Clear the filters and look for it before concluding it was deleted.

If "Change History" is available, it is useful for checking changes. However, history viewing and restoration are not necessarily available in every environment. Restoring changes current data, so distinguish it from simply viewing history.

For business work that also requires checking external integration or automatic processing results, use the confirmation destination provided by the person in charge. A successful Object update and successful subsequent processing are not the same.

## When you cannot perform the action as expected

| Situation | What to check first |
|---|---|
| You cannot find the Object | Project or App, target name, entry point you were given, participation status, and access scope |
| Records are not displayed | Search filters, page, and viewing scope. If you can switch to a trash view, also check the display destination. |
| A field is not visible | Display-field settings, Object configuration, and viewing restrictions |
| You cannot edit or create a new record | Action permissions, editing restrictions such as system fields, and guidance near the button |
| Required, type, date, or similar error | The field and input format specified in the message |
| Error that a value is duplicated | Whether existing data has the same administration number or similar value. Do not resolve it by removing the constraint. |
| You are notified that another user or process updated it | Reload the latest data, check the changed content, then make only the necessary correction. |
| Saving fails or you do not know whether it completed | Check the current saved state. Note necessary input before reloading, and avoid duplicate registration or overwriting a newer value. |

When you see a concurrent-update notification, do not resend the content from an older screen unchanged. Not only other users, but processes such as Agents or Workflows may be updating it.

When consulting the person in charge, collect the following information.

- The Project or App and Object names
- A number that identifies the target record, or a URL within the scope you may share
- The action you wanted to perform and target field
- The date and time and error message
- The state you could confirm before and after editing

Do not share values or screens containing confidential information more broadly than necessary.

## Precautions when operating

- **Being able to view data and being able to update, register, or delete it are different.** Do not determine your action scope based on a role name alone; check actual permissions and business rules.
- Do not select deletion of an Object or field when you intend to change a value.
- CSV import, bulk deletion, restoration, and similar actions affect multiple data items or the current state. Do not use them instead of an ordinary one-record update; follow separately established operational procedures.
- Do not use closing the screen as a way to cancel saving or stop processing.

## Completion criteria

- [ ] I identified the correct Object and record.
- [ ] I changed only permitted fields.
- [ ] I checked completion of the update or registration and the saved values.
- [ ] I did not repeat an action while an unclear error or conflict remained.

## Related articles

- [Get Started in a Shared Project](start-with-shared-project.md)
- [Move Your Work Forward from a Page](work-with-pages.md)
- [Understand Organization, Project, and App Permissions](../operations/understand-org-project-app-permissions.md)
- [Object Specifications and Limits](../build/object-specs-and-limits.md)
- [Unable to Read or Write Objects or Storage](../troubleshooting/object-storage-read-write-issues.md)
