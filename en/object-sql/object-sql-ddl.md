# Object SQL DDL

Object SQL is SQL that treats Objects as tables, fields as columns, and records as rows. With DDL, you can define Objects, fields, indexes, and descriptions. Check [Design an Object Structure](../build/design-object-structure.md) and [Object Specifications and Limits](../build/object-specs-and-limits.md) for the effects of changes.

## Related articles

- [Design an Object Structure](../build/design-object-structure.md)
- [Object Specifications and Limits](../build/object-specs-and-limits.md)
- [Workflow and Automation Specifications and Limits](../build/workflow-and-automation-specs.md)
- [SDK Objects](../development/sdk-objects.md)
- [CLI Objects](../cli/cli-objects.md)
- [Objects API](../api/objects-api.md)

## Before you begin

- Check the target organization and Project, and the Object, fields, and existing records to be changed.
- Creating, deleting, or changing an Object or field requires the corresponding permission for each target. View permission alone does not let you execute DDL.
- In the "Custom Object SQL" Workflow node, specify only one statement in "SQL." "Generate SQL with Agent" assists with creating SQL; review the generated content before execution. You can also execute DDL with an Agent's Object SQL tool.

> DDL accepts only one SQL statement per execution. Multiple statements such as `CREATE TABLE ...; CREATE INDEX ...` are rejected as `Only a single SQL statement is allowed`. Review changes statement by statement and execute them separately if necessary.

Within the scope checked, there is no Object SQL execution API or command in the public REST API, Studio SDK, or Studio CLI. Use Object definitions with the SDK and CLI, and the Objects API with the HTTP API. This article does not describe direct use outside a Workflow or Agent.

## 1. Create Objects and fields with `CREATE TABLE`

Object and field names follow the Object naming rules. Object and field names specified in DDL are lowercased, so use lowercase consistently, such as `customer_code` rather than `CustomerCode`. Set a Japanese display name with `SET DISPLAY_NAME`, described later.

```sql
CREATE TABLE tasks (
  title VARCHAR(100) NOT NULL,
  priority INT DEFAULT 0,
  completed BOOLEAN DEFAULT false,
  due_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
) COMMENT 'Object for managing tasks'
```

The `COMMENT` clause sets the Object **description**. It does not change the display name. Table-level constraints (for example, `CONSTRAINT ...` or `UNIQUE (...)` at the end of a table) cannot be used; write constraints for each column.

### SQL types and Object field types

| SQL type | Created field type | Notes |
|---|---|---|
| `TEXT`, `VARCHAR(n)`, `CHAR(n)` | Text | The length in `VARCHAR(n)` and similar types becomes the field's maximum length. |
| `INT`, `INTEGER`, `BIGINT`, `DECIMAL`, `NUMERIC`, `FLOAT`, `DOUBLE`, `REAL` | Number | Integer types are handled as numbers that accept integers only. |
| `BOOLEAN`, `BOOL` | Boolean |  |
| `DATE`, `TIMESTAMP`, `DATETIME` | Date and time | Only this type can use `CURRENT_TIMESTAMP` / `NOW()` as the default value. |
| `JSON`, `JSONB` | JSON |  |
| Array types such as `TEXT[]` | Array |  |
| `SERIAL`, `BIGSERIAL`, `AUTO_INCREMENT` | Auto-incrementing number | They are handled as auto-incrementing values. |

### Constraints available for columns

| Syntax | Behavior and conditions |
|---|---|
| `NOT NULL` / `NULL` | Sets whether the field is required or optional. |
| `UNIQUE` | Sets a unique constraint and index. It cannot be specified for `JSON` or arrays. |
| `DEFAULT <literal>` | A number, string, Boolean, or `NULL` can be the default value. Expressions cannot be specified. |
| `DEFAULT CURRENT_TIMESTAMP` | Can be specified only when creating a date/time field or adding a column. |
| `CHECK (column_name IN (...))` | Becomes an allowed list of literal values for the same column. `NOT IN`, an empty list, a list containing `NULL`, or conditions using another column or an expression are rejected. Up to 100 values are allowed. |
| `REFERENCES object_name(field_name)` | Sets a relationship. The referenced Object and field must exist, the referenced field must be unique, and both field types must match. |

For example, define the reference target first.

```sql
CREATE TABLE customers (customer_key TEXT NOT NULL UNIQUE);
CREATE TABLE orders (
  title TEXT NOT NULL,
  customer_ref TEXT REFERENCES customers(customer_key)
);
```

`ON UPDATE` and `REFERENCES` across multiple columns are not supported. The statement is rejected if the reference target does not exist, is not unique, or has a different type. A relationship does not grant permissions to its reference target.

## 2. Change only one item with `ALTER TABLE`

Only one operation can be specified in each `ALTER TABLE` statement. You cannot separate additions or changes with commas.

```sql
ALTER TABLE tasks ADD COLUMN assignee TEXT;
ALTER TABLE tasks ADD COLUMN IF NOT EXISTS estimate_hours INT DEFAULT 0;
ALTER TABLE tasks ALTER COLUMN title SET NOT NULL;
ALTER TABLE tasks ALTER COLUMN priority SET DEFAULT 0;
ALTER TABLE tasks ALTER COLUMN priority DROP DEFAULT;
ALTER TABLE tasks ALTER COLUMN title SET DATA TYPE VARCHAR(200);
ALTER TABLE tasks ALTER COLUMN title SET DISPLAY_NAME 'Subject';
ALTER TABLE tasks SET DISPLAY_NAME 'Tasks';
```

Supported operations are adding a column, `DROP COLUMN ... CASCADE`, `SET/DROP NOT NULL`, setting or clearing a literal default value, changing a data type within the same field type, setting or clearing a display name, and setting or clearing a relationship. You can also use `ADD RELATIONSHIP field REFERENCES target(key)`.

You cannot change the field type itself or reduce the maximum length of text. `SET DATA TYPE ... USING`, `GENERATED AS IDENTITY`, and `ALTER COLUMN SET DEFAULT CURRENT_TIMESTAMP` are rejected. `DROP COLUMN` requires `CASCADE`. Adding an existing column with `IF NOT EXISTS`, or deleting a nonexistent column with `IF EXISTS`, makes no change and produces a warning.

`ALTER TABLE ... SET/DROP DISPLAY_NAME` handles display names. `DROP DISPLAY_NAME` restores the display name to the name. `COMMENT ON COLUMN` cannot be used.

## 3. Handle indexes and deletion

```sql
CREATE INDEX idx_tasks_title ON tasks(title);
DROP INDEX idx_tasks_title ON tasks(title);
DROP TABLE IF EXISTS obsolete_tasks CASCADE;
```

`CREATE INDEX` can specify only a simple column name for one column. Specify `UNIQUE` with the column's `UNIQUE` constraint; `CREATE UNIQUE INDEX` cannot be used. `CONCURRENTLY`, `USING`, `INCLUDE`, `NULLS DISTINCT`, `WITH`, `WHERE`, multiple columns, and sort order are rejected. If you create an index on an already indexed column with `IF NOT EXISTS`, no change is made and a warning is issued.

`DROP INDEX` requires `ON object(field)` and cannot use `CASCADE`. An index created by a unique constraint cannot be deleted.

`DROP TABLE` targets only one Object and includes `CASCADE`. With `IF EXISTS`, a nonexistent Object is unchanged and a warning is issued. After deletion, SQL that references the Object fails. Because no SQL restore syntax could be confirmed, refer to [Object Specifications and Limits](../build/object-specs-and-limits.md) for deletion and restoration operations and effects.

`COMMENT ON TABLE tasks IS 'Description';` sets or updates the Object description. `COMMENT` for anything other than a table, such as `COMMENT ON COLUMN`, is rejected.

## Common misunderstandings

### You can set a display name with `COMMENT`

You cannot. `COMMENT` is the Object description. Set an Object with `ALTER TABLE tasks SET DISPLAY_NAME 'Tasks'` and a field with `ALTER TABLE tasks ALTER COLUMN title SET DISPLAY_NAME 'Subject'`.

### `CREATE INDEX` can speed up any search condition

Only simple index creation for one column is supported. You cannot create partial or multi-column indexes. Check [Object Specifications and Limits](../build/object-specs-and-limits.md) for search design and Object limits and constraints.

## If something does not work

| Situation | What to check and do |
|---|---|
| DDL is rejected with 400 | Use one SQL statement, and check Object/field names, supported types and syntax, `CASCADE`, and whether the target exists. If it says `not supported`, the syntax is outside Object SQL's scope. |
| You get a permission error | Check the permission for the target that corresponds to the DDL you executed: creating, deleting, or changing an Object or field. |
| `REFERENCES` fails | Check that the referenced Object and field exist, the reference target's unique constraint, both field types, and existing record values. |
| DDL fails in a Workflow | Check "SQL" in the "Custom Object SQL" node, the target Project, the permissions required by the Workflow execution principal, and the error in run history. DDL is rejected in execution contexts that do not allow structural changes. |

Before and after changes, check the field structure, existing records, relationships, and representative Page, Agent, and Workflow operations. Within the scope checked, an entry point for users that only validates DDL could not be confirmed.

## Next articles to read

- [Design an Object Structure](../build/design-object-structure.md)
- [Object Specifications and Limits](../build/object-specs-and-limits.md)
- [Workflow and Automation Specifications and Limits](../build/workflow-and-automation-specs.md)
- [SDK Objects](../development/sdk-objects.md)
- [Objects API](../api/objects-api.md)
