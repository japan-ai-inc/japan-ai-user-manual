# Object SQL DML

Object SQL is a SQL dialect, not full PostgreSQL, for querying, adding, updating, and deleting Project Objects with SQL-like syntax. Specify Objects as tables, fields as columns, and records as rows. See [Object SQL DDL](object-sql-ddl.md) to create or change Objects and fields, and [View and Update Object Data](../use/view-and-update-object-records.md) to work with records on screen.

## Related articles

- [Object SQL DDL](object-sql-ddl.md)
- [Object Specifications and Limits](../build/object-specs-and-limits.md)
- [Workflow and Automation Specifications and Limits](../build/workflow-and-automation-specs.md)
- [Understand Organization, Project, and App Permissions](../operations/understand-org-project-app-permissions.md)
- [Objects API](../api/objects-api.md)

## Before you begin

- Check the target Project, Object name, field names, field types, and target records. Use Object and field names resolvable by Object SQL, not display names.
- Read access and write/delete access are separate. Even if SQL is valid, Object, field, and record access conditions can filter results or reject the operation. Check roles, sharing scope, and access settings in [Understand Organization, Project, and App Permissions](../operations/understand-org-project-app-permissions.md).
- `UPDATE`, `DELETE`, and multi-row `INSERT` change real data. Check with a test Object or narrowed target, and review conditions, record count, and effects on subsequent Workflows before writing.

> Object SQL accepts **only one statement** per execution. If a code example in this article contains multiple statements, pasting them together fails. Execute them one statement at a time. You cannot group multiple statements with `BEGIN`, `COMMIT`, or `ROLLBACK`. DML is processed as one server-side execution unit and is committed on success. Do not rerun the same update without checking when it fails or times out.

## Where you can execute it

In a Workflow, select "Custom Object SQL" from the node list and enter the statement in "SQL." Output variables are "Query result array" and "Total count (SELECT queries)" for SELECT, and "Affected rows (DML queries)" for DML. Before execution, check Workflow testing, publishing, and execution conditions in [Workflow and Automation Specifications and Limits](../build/workflow-and-automation-specs.md).

In an Agent, the Object SQL tool (`custom_object_sql`) is **automatically given to every Agent in a Studio Project** and cannot be removed. The tool is not read-only: it can execute `SELECT`, `INSERT`, `UPDATE`, `DELETE`, and DDL. Instructing the Agent in its prompt to "only read" does not restrict it. To prevent an Agent from updating or deleting, use the Object's role permissions (IAM) so that writes and deletes on the target Objects, fields, and records are not allowed.

The public REST API, Studio SDK, and CLI do not have a public interface for directly executing Object SQL. To work with records through the HTTP API, SDK, or CLI, start with [Objects API](../api/objects-api.md) and use the respective Object operations.

## SELECT: Query data

The basic form is as follows. A normal SELECT without `LIMIT` defaults to 100 rows; the maximum is 1,000 rows even when specified. `OFFSET` is at most 10,000. For large scans, use keyset pagination with `_record_id`.

```sql
SELECT name, status
FROM orders
WHERE status = 'open' AND amount >= 1000
ORDER BY name ASC
LIMIT 100 OFFSET 0
```

In `WHERE`, you can use `=`, `!=` / `<>`, `<`, `<=`, `>`, `>=`, `IN` / `NOT IN`, `BETWEEN`, `LIKE` / `ILIKE`, `IS NULL` / `IS NOT NULL`, `AND`, and `OR`.

| Statement | Handling of `OR` |
|---|---|
| `SELECT` | `OR` across different fields can be used (for example, `WHERE status = 'open' OR priority = 1`). |
| `UPDATE` / `DELETE` | Only `OR` within the same field can be used (for example, `WHERE status = 'a' OR status = 'b'`). `OR` across different fields cannot be used. |

AND inside OR is not supported. `LIKE` / `ILIKE` cannot be used for fields other than strings, and `NULL` cannot be included in `IN`.

### Sort order, aggregation, and joins

`ORDER BY` can specify multiple columns and `ASC` / `DESC`. Aggregations are `COUNT`, `SUM`, `AVG`, `MIN`, and `MAX`, and can be used with `GROUP BY` and `HAVING`. When using an aggregate in `HAVING` or `ORDER BY`, give it an explicit alias.

```sql
SELECT status, COUNT(*) AS total
FROM orders
GROUP BY status
HAVING total > 1
ORDER BY total DESC
```

Only one `INNER JOIN` or `LEFT JOIN ... ON` can be used, and only for a defined relationship field. Self-joins, multiple JOINs, joins between arbitrary columns, and joins using aliases cannot be used. You can use up to two `WHERE ... IN (SELECT ...)` / `NOT IN (SELECT ...)` subqueries only between Objects connected by a relationship. A subquery selects one column and cannot include `ORDER BY`, `LIMIT`, `GROUP BY`, `HAVING`, or nested subqueries. A subquery result is limited to 1,000 rows. Subqueries cannot be used in DML.

Normal SELECT supports field projection, `*`, and `DISTINCT`. In addition to aggregate functions, `COALESCE` may be usable in projections and aggregate arguments. Do not assume arbitrary functions, window functions, `CASE`, casts, `UNION`, or similar features. `WITH` (CTE) and subqueries in the FROM clause cannot be used.

### Pagination with `_record_id`

`_record_id` is the unique system identifier. It is returned as the record ID in results, but cannot be used in SELECT projection, `INSERT`, `UPDATE SET`, or as a conflict target. In `WHERE`, it can only use `=`, `<`, `<=`, `>`, `>=`, or `IN` with string literals, and in `ORDER BY`, only `ASC` / `DESC`. This manual does not cover querying or sorting metadata such as creation time and update time as columns.

```sql
SELECT * FROM orders
WHERE _record_id > 'record ID at the end of the previous page'
ORDER BY _record_id ASC
LIMIT 100
```

This order is not creation-time order. It can also include rows added or deleted between pages, so do not treat it as a fixed snapshot.

## INSERT, UPDATE, and DELETE: Change data

Match field types and values. Object types are string, number, date/time, Boolean, JSON, and array; provide a date/time string for date/time. **JSON and array fields have no SQL literal notation.** Their values can be passed only through structured parameters, described below. Object-side constraints such as required, unique, and numeric range are also validated in SQL.

```sql
INSERT INTO orders (name, amount, active)
VALUES ('Quote A', 1200, true), ('Quote B', 800, false)

UPDATE orders
SET status = 'closed'
WHERE _record_id = 'target record ID'
LIMIT 1

DELETE FROM orders
WHERE status = 'obsolete'
LIMIT 100
```

`INSERT` requires column names and `VALUES`, and can add multiple rows. Do not use a `RETURNING` clause; check the count or row result returned by the execution path. INSERT/UPSERT is limited to 1,000 rows.

{% hint style="danger" %}
**Be careful with how `ON CONFLICT` behaves.** When you specify `ON CONFLICT (...)`, existing records are matched on the conflict columns, but only the `VALUES` rows and the conflict columns are actually used.

- The expressions in `SET` of `DO UPDATE SET ...` are not applied. On conflict, **the existing record is overwritten with all columns in `VALUES`**.
- Even with `DO NOTHING`, on conflict **the existing record is overwritten with all columns in `VALUES`**. Do not use it if you want to keep existing records.

To update only some columns, use `UPDATE`.
{% endhint %}

`UPDATE` and `DELETE` require `WHERE` or `LIMIT`. If you specify `LIMIT` and the target exceeds it, processing is rejected.

> **`UPDATE` / `DELETE` without `LIMIT` processes only an arbitrary 1,000 of the matching rows, without an error.** If more than 1,000 rows may match, repeat the same statement until the affected row count ("Affected rows (DML queries)" in the Workflow node) becomes 0.

Check effects before deletion and whether restoration is possible in [Object Specifications and Limits](../build/object-specs-and-limits.md).

> If a write fails partway through or times out, do not conclude that it "was not applied at all." Retrieve the target again with SELECT and check the count, values, and run history before deciding what to do. For Workflows that send externally or notify, also check for duplicates caused by rerunning.

## Passing parameters

When the execution path accepts a parameter array, specify placeholders consecutively from 1, such as `$1` and `$2`. Bind values in a separate array; do not create SQL through string concatenation. You can use the same number multiple times. There can be up to 256 values, totaling up to 8 MiB. Object and array values (values for JSON and array fields) can be passed only as a complete VALUES value in INSERT or complete SET value in UPDATE.

### Using variables in a Workflow node

In the Workflow "Custom Object SQL" node, write values from previous nodes in SQL in the form `{{#node_id.variable_name#}}`. These are automatically converted to `$1`, `$2`, and so on at run time. For this reason, writing `$1` directly in the Workflow node's "SQL" fails.

```text
SQL:        UPDATE orders SET memo = $1 WHERE _record_id = $2 LIMIT $3
parameters: ["Confirmed", "target record ID", 1]
```

`$0`, skipped numbers, extra values, non-numeric LIMIT/OFFSET, and placing structured values in search conditions or functions are rejected. The SQL body is also limited to 65,536 characters.

## If something does not work

| Situation | What to check and do |
|---|---|
| Syntax or semantic error | Check Object/field names, types, supported syntax, and whether there is only one statement. Do not share full SQL together with confidential information. |
| Zero or partial results | Check WHERE, Object/record view conditions, deleted state, and LIMIT/OFFSET. |
| Update or delete is rejected | Check update/delete rather than read permission, field- and record-level access conditions, WHERE or LIMIT, the 1,000-row maximum, and whether you are using `OR` across different fields. |
| Only some rows are updated or deleted | `UPDATE` / `DELETE` without `LIMIT` processes up to 1,000 rows per run. Repeat until the affected row count is 0. |
| A parameter error occurs in a Workflow node | Do not write `$1` directly; reference variables with `{{#node_id.variable_name#}}`. |
| Timeout | Reduce conditions, projected columns, LIMIT, joins, and subqueries. One statement can run for up to 30 seconds, total read processing for 65 seconds, write processing for 35 seconds, and the whole request for 130 seconds. |
| A conflict occurs or rerunning is needed | Retrieve the latest record and check the result. Do not automatically retry a write whose update status is unknown with the same conditions. |

## Checklist

- [ ] I checked the target Project, Object, fields, and records
- [ ] I separately checked read and write/delete permissions
- [ ] I specified the needed WHERE, ORDER BY, and LIMIT for SELECT
- [ ] I specified WHERE or LIMIT for UPDATE/DELETE, and when there was no LIMIT, repeated until the affected row count was 0
- [ ] I used `ON CONFLICT` understanding that it overwrites with all columns in `VALUES`
- [ ] I prepared SQL or Workflow output to check counts and values before and after changes
- [ ] I bound parameters in `$1` form without string concatenation
- [ ] I checked the effects of deletion, rerunning, and subsequent Workflow processing

## Next articles to read

- [Object Vector Search](object-vector-search.md)
- [Object SQL DDL](object-sql-ddl.md)
- [Object Specifications and Limits](../build/object-specs-and-limits.md)
- [Workflow and Automation Specifications and Limits](../build/workflow-and-automation-specs.md)
- [Understand Organization, Project, and App Permissions](../operations/understand-org-project-app-permissions.md)
- [Objects API](../api/objects-api.md)
