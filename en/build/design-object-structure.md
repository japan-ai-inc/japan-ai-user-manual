# Design an Object Structure

Objects let you manage business data, such as customers, opportunities, applications, and tasks, using defined fields. Each individual data item (1 item) is called a "record," and the items held by a record are called "fields."

This article explains how to organize the structure before creating an Object and then design and configure fields, relationships with other Objects, and access conditions from the screen. To enter or update records, see [View and Update Object Data](../use/view-and-update-object-records.md).

- Object specifications and limits: [Object Specifications and Limits](object-specs-and-limits.md)
- To define the structure with SQL: [Object SQL DDL](../object-sql/object-sql-ddl.md)

## What is an Object structure?

An Object structure does more than simply list fields. It determines what users can enter and search for, how the data is connected to other data, and who can handle it and to what extent. Designing the following 3 elements separately first can reduce later revisions.

| Design target | Decide | Example |
|---|---|---|
| Fields | What to save and the format and conditions for input | Customer name, person in charge, expected order date, opportunity number |
| Relationships | Which record in which Object to reference | Reference a customer from an opportunity |
| Access conditions | Who can view and operate the Object, fields, and records | The person in charge can edit; other departments can only view |

For example, when creating an "opportunity," consider not only the opportunity name, amount, and status, but also its relationship to the customer and who can view the expected amount. Also ask stakeholders whether it may be used from search, Pages, Agents, or Workflows.

> **Changes to the structure also affect existing records.** If you delete a field that already has values, a confirmation appears stating that the data saved in that field will also be deleted. Before adding required or unique constraints later, also check whether existing data meets the conditions.

## Before you begin

- Confirm the target Project and the business purpose and person responsible for managing the Object you will create.
- Check whether the same data is already managed in an existing Object, Page, Workflow, or Agent. Do not create duplicate Objects.
- Prepare several data items that you will actually register, and identify required fields, fields that allow blank values, and identifiers.
- When using related Objects, confirm the referenced Object and the field that uniquely identifies records.
- Confirm that you have permission to operate Objects, fields, and records, as well as permission to change access settings. The items you can view and operate differ depending on permissions and organization settings.
- If existing data or active Pages or Workflows exist, agree with stakeholders on the change date and time, the scope of impact, and how to respond if changes become necessary.

## 1. Create a design table

Before operating the screen, create a design table like the following. Record not only display names but also the purpose of each value, input conditions, and reference relationships.

| Item | Design example | What to check |
|---|---|---|
| Purpose of the Object | Manage sales opportunities | What 1 record represents |
| Object name and display name | Name used internally / name shown on screen | Whether it duplicates or could be confused with another Object |
| Identifier | Opportunity number | Decide whether duplicates are allowed and whether it is entered manually |
| Fields | Opportunity name, status, expected order date, amount | Type, required status, default value, allowed values |
| Relationship | Reference the customer Object | The referenced Object and its identifying field |
| Access conditions | Sales can edit; administration can view | Whether to control access at the Object, field, or record level |

Separate the roles of display names and management identifiers that must not allow duplicates. Before copying the same information into multiple Objects, consider whether you can reference it through a relationship.

## 2. Create an Object

1. Open the Object list in the target Project.
2. Select "Create Object."
3. In "Object Name (alphanumeric)," enter a name to identify it within the system.
4. In "Display Name," enter a name that users can understand.
5. In "Description," enter the purpose, the meaning of 1 record, and notes for data entry.
6. As necessary, configure "Record change history." History is recorded for record changes made after you turn it on; it does not create past states for records that already exist.
7. Select "Save" and confirm that "Object created successfully." appears.

Use Object names and display names for different purposes. An Object name must start with a letter when it is created, cannot contain spaces, and cannot contain symbols other than underscores. Give display names clear business terms because users check them in lists and on screens.

## 3. Design and create fields

Open the Object you created, then select "Create Field" from the "Fields" tab. First, create the minimum fields needed for registration, search, and reference. Confirm them with representative records before adding more.

### Choose a field type

Under "Field Type," you can select the following types. Choose a type based not on how a value looks, but on its meaning when you later enter, search for, or evaluate conditions on it.

| Field type | Main use | Design considerations |
|---|---|---|
| "Text" | Names, descriptions, codes, email addresses, and similar data | Do not mix long text and identifiers in the same field |
| "Number" | Amounts, counts, order, scores, and similar data | Decide the unit, whether to use decimal places, and the permitted range |
| "Boolean" | Whether an item has been handled or is subject to publication | Decide what "On" and "Off" mean in the business context |
| "Date and time" | Deadlines, occurrence dates, planned updates, and similar data | Determine whether only the date is needed or the time as well |
| "Array" | When a field needs to hold multiple values | Check the effect on search, reference, and input methods |
| "JSON" | When handling structured additional information | Split ordinary business items into separate fields whenever possible |

1. Select "Create Field."
2. Enter "Field Name" and "Display Name." A field name must also start with a letter, cannot contain spaces, and cannot contain symbols other than underscores.
3. In "Description," enter the meaning of the value, examples, and notes for input. Agents also use the description to understand the field.
4. Select a "Field Type."
5. As necessary, configure "Default Value," "Auto-fill current time," "Allow empty value," "Unique constraint," and other options.
6. To apply conditions to values, add the required conditions under "Validation rules."
7. Review the content and save it.

Turning off "Allow empty value" makes the field required. "Unique constraint" prevents the same value from being duplicated across records. Use it for a value that identifies only 1 record, such as an employee ID or opportunity number. If you add or change a required or unique constraint or validation condition for an Object that has existing data, the existing records are validated, and you may be unable to make the change if there are blank values, duplicates, or similar issues.

> **Do not use only values that may have variations in notation or duplicate names as identifiers.** Names of people or companies may not be suitable for unique constraints. If there is a number that is uniquely managed in the business, create a dedicated field for it.

## 4. Design relationships between Objects

To link to a record in another Object, use the field's "Relationship." For example, linking the "Customer" field of an opportunity Object to a customer Object lets you reference the customer for each opportunity.

1. In the Object that will hold the relationship, select "Create Field."
2. Enable "Relationship."
3. Under "Reference object," select the Object to link.
4. Under "Reference field," select the identifying field in the referenced Object.
5. Review the content and save it.

For a relationship, select the referenced Object and field. The field type is determined by the type of the referenced field. Array and JSON types cannot be used for relationships. Check the conditions displayed on screen for fields that can be selected as reference targets and combinations with unique constraints.

Before creating a relationship, confirm the following:

- The reference target has a non-duplicated identifying field.
- You have decided whether every record in the referencing Object must have a reference target.
- You have selected a field that provides stable identification rather than a display value that may change, such as a customer name.
- You have checked the business impact if the reference target is deleted or hidden.

> **A relationship does not inherit access permissions.** Even when there is a related link, users cannot necessarily view or update all records in the reference target. Design access conditions for the referencing and referenced Objects separately.

## 5. Design and configure access conditions

Consider permissions to operate an Object separately from access conditions for data in that Object. Whether the access settings menu appears depends on enabled features and permissions. If it does not appear or you cannot open it, check with the Project administrator or organization administrator.

When you open "Access settings" from the Object's action menu, you can review the following targets.

| Setting | Scope managed | Design example |
|---|---|---|
| "Object" | Viewing, creating, editing, and deleting the Object itself | Allow only the creator to edit the structure |
| "All data" | Default access permissions for data across the Project | Check the Project's standard data access |
| "Field" | View and edit permissions for each field | Allow only managers to edit expected amounts |
| "Record Access Rules" | Condition-based, record-level access control | Show only records for the responsible department |

In the "Object" access settings, you can select "No access," "Read," "Edit," or "Manage" for each target. Select targets to add from "Search roles, departments, members, or agents" on the screen. When selecting a department as a target, a notice appears that the department may include members without Object viewing permission, so also check their actual permissions.

You can configure field-level settings after creating at least 1 field. You can configure record-level rules after creating at least 1 record. For the initial design, it is safer to first decide Object and field access, check the visible scope with test records, and then add record conditions.

Under "Record Access Rules," specify fields, operators, and values as conditions, and assign permissions to "Assignment targets," such as roles, members, and agents. Combine multiple conditions with "All conditions match" or "Any condition matches." If similar rules overlap, organize their priorities and targets beforehand.

When editing an existing rule, load the current field definitions before checking its conditions. If a saved condition cannot be represented on the screen, its condition section may be read-only. Do not recreate conditions by guessing. Check the target fields and rules, and reopen the list as necessary before editing.

> **After changing access settings, do not verify them only on the configurator's own screen.** Check how the Object list, records, and target fields appear for the intended combinations of roles, departments, and users. Being able to view data is different from being able to create, edit, or delete records or change the structure.

## 6. Test and begin using it

After configuring the structure and access conditions, check representative cases before registering a large volume of actual data.

1. Create test records. Check required fields, default values, and input conditions.
2. For fields where you want to prevent entering the same value, confirm that duplicate records cannot be created.
3. If you configured a relationship, confirm that you can select the correct reference target and check the related target.
4. Check each screen under the intended access conditions, such as view-only or editable.
5. When using it from a Page, Agent, or Workflow, confirm that each can handle the required fields and records.
6. Record the design table, change date and time, check results, and contact point.

> To enable a unique constraint, you must first enable the index and wait for rebuilding to complete before configuring it. You also cannot enable it if existing records have duplicate values.

If testing reveals issues such as too many fields to enter easily, the same information in multiple places, or permissions that cannot be explained, review the structure before full-scale operation. For details on limit values and applicable conditions, see [Object Specifications and Limits](object-specs-and-limits.md).

## Checklist

- [ ] Decided the business unit represented by 1 record and the person responsible for managing the Object
- [ ] Checked for duplication with existing Objects and the impact on Pages, Agents, and Workflows
- [ ] Chose the Object name, display name, and description according to their purposes
- [ ] Decided each field's type, required status, default value, unique constraint, and input conditions
- [ ] Designed an identifier that does not allow duplicates separately from the display name
- [ ] Confirmed the relationship reference target and reference fields that can be selected on screen
- [ ] Designed access conditions for the Object, all data, fields, and records separately
- [ ] Checked test records under the access conditions for intended users
- [ ] Checked the impact of changes on existing data and Pages and Workflows currently in use

## Next articles to read

- [View and Update Object Data](../use/view-and-update-object-records.md)
- [Object Specifications and Limits](object-specs-and-limits.md)
- [Object SQL DDL](../object-sql/object-sql-ddl.md)
