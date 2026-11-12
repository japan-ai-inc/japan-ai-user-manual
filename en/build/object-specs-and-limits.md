# Object Specifications and Limits

Objects are a feature for managing business data with "records" and "fields." Before creating one, checking naming rules, field types, input conditions, relationships, and conditions for CSV data operations can reduce later modifications and import errors.

This article is a reference that summarizes Object structure and data-operation specifications and limits. For design and creation, see [Design an Object Structure](design-object-structure.md); for day-to-day record operations, see [View and Update Object Data](../use/view-and-update-object-records.md).

Related articles:

- [Design an Object Structure](design-object-structure.md)
- [Objects API](../api/objects-api.md)
- [Index of Limits and Availability Conditions](../operations/limits-and-availability-index.md)
- [Object SQL DDL](../object-sql/object-sql-ddl.md)

## Scope of this article

This article covers the following items available on the standard Object screen.

- Input conditions for Object names and field names, display names, and descriptions
- Structural constraints such as "Field Type," "Allow empty value," "Unique constraint," and "Validation rules"
- "Relationship" and conditions for reference targets
- Record display, CSV "Export" and "Import from CSV," and deletion checks
- Limits for items, pages, and rules selectable on the screen

Availability conditions by contract plan, features enabled in a particular environment, and detailed conditions for external API and SQL use are not determined here. Features not displayed on the screen may be unavailable because of permission or organization/Project settings.

## Basic specifications

| Item | Specification | Notes |
|---|---|---|
| Object name | Required. It must start with a letter and can contain alphanumeric characters and underscores. Spaces and symbols other than underscores cannot be used. | This identifies an Object. You cannot use the same name as an existing Object. |
| Display name | The name displayed on the screen. | You can set it for both Objects and fields. Use business terminology users understand. |
| Description | You can describe the purpose of the Object. | In a field's "Description," state the meaning of the value and input precautions. It is also used for AI Agents to understand the field. |
| Field name | Required. It must start with a letter and can contain alphanumeric characters and underscores. Spaces and symbols other than underscores cannot be used. | Within the same Object, you cannot create fields with the same field name or the same display name. |
| Field Type | You can select "Text," "Number," "Boolean," "Date and time," "Array," "JSON," or "Auto-increment." | "Auto-increment" is treated as a number field. Choose the type according to the meaning of the value to save. |
| "Allow empty value" | When on, empty values are allowed; when off, the field is required. | You cannot change it to required if existing records have empty values. |
| "Unique constraint" | Prevents duplicate values among records. | You cannot configure it if existing records have duplicate values. Resolve duplicates before use. |
| Default value | You can set a value that matches the type. | "Auto-fill current time" enters the time when a record is added. |
| "Auto-increment" | Starts at 1 and always increases by 1 when a record is added. It cannot be edited manually. | Deleted numbers are not reused. |
| Maximum length | Can be set only for "Text" fields. | It cannot be set for number fields or similar fields. Use it to limit string length. |
| Minimum and maximum numbers | Can be set only for "Number" fields. The minimum must be less than or equal to the maximum. | You cannot change the range if existing data is outside it. |
| "Validation rules" | You can combine character limits, number ranges, regular expressions, allowed values, array element counts, date and time ranges, and custom rules. | You cannot set rules unsupported by the type. You can set only one rule of each type. |

## Relationship specifications

A "Relationship" is a setting for linking to records in another Object. You must select both a reference Object and reference field.

| Item | Specification | Notes |
|---|---|---|
| Reference field | You can select only fields with a "Unique constraint." | A field whose value is held by multiple records cannot be a reference target. Prepare a stable identifier. |
| Type | Must match the reference target's data type. | You cannot combine different types for the source and reference target. |
| Unavailable types | "Array" and "JSON" cannot be used for relationships. | If you want to make a field with multiple values or an arbitrary structure a source or target, review the structure. |
| Use with "Unique constraint" | You cannot set "Unique constraint" and "Relationship" on the same field at the same time. | Consider whether the referencing field needs uniqueness or should be separated into another field. |

Setting a relationship does not automatically grant permission to view or update the reference target. Check "Access Settings" for both the source and target.

## Limits and limit values

The following numbers show only values that can be confirmed in the implementation. This article does not state a limit for items with no numeric value.

| Target | Limit or limit value | Conditions and notes |
|---|---:|---|
| Object name, Object display name, field name, and field display name | 255 bytes | This is a UTF-8 byte count. For multibyte characters such as Japanese, it does not match the character count. |
| Object description | 1,000 bytes | This is a UTF-8 byte count. |
| "Maximum length" for text fields | 1–65,535 | Specify an integer. Applies only to "Text" fields. |
| Number of records displayed in the record list | 30, 50, or 100 items/page | These are options on the standard Object screen. An unspecified or invalid value becomes 30. This is separate from the CSV export limit. |
| CSV "Import from CSV" | Up to 1,000 records per import | This excludes the header row. The CSV header must match the current field structure and order. |
| Internal processing unit for CSV import and export | 1,000 records | CSV export retrieves 1,000 records at a time while a next cursor exists. This does not limit the entire export to 1,000 records. |
| Record access rules | Up to 100 atomic rules per Object | One rule group on the screen may expand into multiple atomic rules. Creation is rejected if the total exceeds 100. |
| Record change-history list | 1–200 items/retrieval | The number of items retrieved at one time for an Object with history available. This does not indicate the retention period on the screen. |
| Priority of record access rules | Integer of 1 or greater | Smaller values take priority. A uniform input limit of 100 has not been confirmed. |
| Record access rule conditions | 1–20 items/rule | Applies to creation and updates through the UI. |
| Record access rule assignment targets | 1–200 items/rule | Select from "Role," "Member," and "Agent." |

When editing an existing "Record Access Rules" rule, load the current field definitions before checking conditions. If saved conditions cannot be expressed on the screen, the condition portion may be displayed as read-only. In that case, do not guess at the conditions and recreate them. Check the target field and rule, and if necessary reopen the list before editing.

## Data-operation specifications

### CSV "Export" and "Import from CSV"

"Export" is available for Objects you have permission to view. The exported CSV contains "Record ID" as the first column, followed by field display names (or field names when no display name exists). Values containing commas, double quotation marks, or line breaks are escaped for CSV.

"Import from CSV" requires permission to update the Object. You can select only CSV files, and empty rows are excluded when read. The header must **exactly match in column count, order, and names** the current field structure. Using the "Record ID" column included in the template identifies and updates existing records. Even without an ID, a row becomes an update target if the value of a unique field can uniquely identify an existing record. Rows with conflicting identification results are not imported.

During import, requirements, field types, unique constraints, and so on are checked for each row. Check the counts and applicable rows for created, skipped, and failed results. Correct input values or field structure before importing again, and avoid registering the same data more than once.

### Deletion and restoration

Deleting Objects, fields, and records each requires the corresponding deletion permission. Deleting a field also deletes record data saved in that field. Before deletion, check the impact on CSV export, referencing Pages, Agents, Workflows, relationships, and business-required history.

The screen may show "Move to trash" and "Permanently delete." "Move to trash" indicates that the item will be permanently deleted after 30 days and can no longer be restored. Individual fields cannot be restored.

## Common misconceptions

### If Japanese can be used in a display name, it can also be used in a field name

A display name is a name for users. In contrast, field names and Object names must start with a letter and contain only alphanumeric characters and underscores. Set an easy-to-understand screen name as the "Display name."

### Setting a relationship lets anyone view the reference target's data

A relationship links data. It does not inherit access permissions. Whether someone can view or update the target Object, field, or record is determined separately by "Access Settings."

### "Auto-increment" closes gaps from deleted numbers

"Auto-increment" does not reuse deleted numbers. Gaps in numbering are not abnormal. If you have a different operational requirement, such as consecutive document numbers, consider the management method beforehand.

### CSV export covers only the page currently displayed on the screen

"Export" outputs records by traversing pages separately from screen display counts of 30, 50, or 100. In contrast, "Import from CSV" has a per-import record limit.

### A deleted field can always be restored

Individual fields cannot be restored. Deleting a field also affects saved values. If you only want to hide an unnecessary column, use display-field settings rather than deleting the field.

## If you cannot confirm something

If buttons or settings do not appear, or you cannot import, export, delete, or restore, check the following in order.

1. Confirm that you have not confused the target Project, Object, or record.
2. Check with the Project administrator or organization administrator that you have the required view, create, update, delete, and manage permissions.
3. If the Object is provided by an App, check whether its structure can be changed.
4. For CSV, export the latest template and review the header, column order, required fields, types, and number of records per import.
5. If you cannot change a constraint, resolve the displayed validation results, such as empty values, duplicates, character counts, and number ranges in existing records.
6. If action is required after deletion, consult an administrator with the deletion target, deletion date and time, necessary records, and error display. Before deletion, check the impact on business operations.

For an inquiry, share the Project and Object names, operation date and time, information identifying the target, error message, and, for CSV, the header. Do not share values or files containing personal or confidential information more than necessary.

## Next article to read

- [Object Vector Search](../object-sql/object-vector-search.md)
- [Design an Object Structure](design-object-structure.md)
- [View and Update Object Data](../use/view-and-update-object-records.md)
- [Objects API](../api/objects-api.md)
- [Object SQL DDL](../object-sql/object-sql-ddl.md)
- [Index of Limits and Availability Conditions](../operations/limits-and-availability-index.md)
