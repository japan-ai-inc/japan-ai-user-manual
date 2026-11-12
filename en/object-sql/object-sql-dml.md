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

> Object SQL accepts **only one statement** per execution. You cannot group multiple statements with `BEGIN`, `COMMIT`, or `ROLLBACK`. DML is processed as one server-side execution unit and is committed on success. Do not rerun the same update without checking when it fails or times out.

## Where you can execute it

In a Workflow, select "Custom Object SQL" from the node list and enter the statement in "SQL." Output variables are "Query result array" and "Total count (SELECT queries)" for SELECT, and "Affected rows (DML queries)" for DML. Before execution, check Workflow testing, publishing, and execution conditions in [Workflow and Automation Specifications and Limits](../build/workflow-and-automation-specs.md).

In an Agent, you can execute it from an Agent with the Object SQL tool enabled. The tool is not read-only: it executes DML as well as `SELECT`. When allowing an Agent to update or delete, minimize permissions for target Objects, fields, and records, and check the executor and result.

Within the scope checked, the public REST API, Studio SDK, and CLI do not have a public interface for directly executing Object SQL. To work with records through the HTTP API, SDK, or CLI, start with [Objects API](../api/objects-api.md) and use the respective Object operations.

## SELECT: Query data

The basic form is as follows. A normal SELECT without `LIMIT` defaults to 100 rows; the maximum is 1,000 rows even when specified. `OFFSET` is at most 10,000. For large scans, use keyset pagination with `_record_id`.

```sql
SELECT name, status
FROM orders
WHERE status = 'open' AND amount >= 1000
ORDER BY name ASC
LIMIT 100 OFFSET 0
```

In `WHERE`, you can use `=`, `!=` / `<>`, `<`, `<=`, `>`, `>=`, `IN` / `NOT IN`, `BETWEEN`, `LIKE` / `ILIKE`, `IS NULL` / `IS NOT NULL`, and `AND`. Use `IN` instead of listing equality comparisons for the same field with OR. OR across different fields, or AND inside OR, is not supported. `LIKE` / `ILIKE` cannot be used for fields other than strings, and `NULL` cannot be included in `IN`.

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

`_record_id` is the unique system identifier. It is returned as the record ID in results, but cannot be used in SELECT projection, `INSERT`, `UPDATE SET`, or as a conflict target. In `WHERE`, it can only use `=`, `<`, `<=`, `>`, `>=`, or `IN` with string literals, and in `ORDER BY`, only `ASC` / `DESC`. It could not be confirmed that metadata such as creation time and update time can be queried or sorted as SQL columns.

```sql
SELECT * FROM orders
WHERE _record_id > 'record ID at the end of the previous page'
ORDER BY _record_id ASC
LIMIT 100
```

This order is not creation-time order. It can also include rows added or deleted between pages, so do not treat it as a fixed snapshot.

## INSERT, UPDATE, and DELETE: Change data

Match field types and values. Object types are string, number, date/time, Boolean, JSON, and array; provide a date/time string for date/time, an object for JSON, and an array for an array. Object-side constraints such as required, unique, and numeric range are also validated in SQL.

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

`INSERT` requires column names and `VALUES`, and can add multiple rows. UPSERT with `ON CONFLICT (...) DO UPDATE SET ...` can also be used. Do not use a `RETURNING` clause; check the count or row result returned by the execution path. INSERT/UPSERT is limited to 1,000 rows.

`UPDATE` and `DELETE` require `WHERE` or `LIMIT`. If you specify `LIMIT` and the target exceeds it, processing is rejected. Even if you do not specify it, updates and deletes are limited to 1,000 rows. It cannot be concluded that deletion is identical to "Move to trash" on screen or to restoration procedures. Check effects before deletion and whether restoration is possible in [Object Specifications and Limits](../build/object-specs-and-limits.md).

> If a write fails partway through or times out, do not conclude that it "was not applied at all." Retrieve the target again with SELECT and check the count, values, and run history before deciding what to do. For Workflows that send externally or notify, also check for duplicates caused by rerunning.

## Passing parameters

When the execution path accepts a parameter array, specify placeholders consecutively from 1, such as `$1` and `$2`. Bind values in a separate array; do not create SQL through string concatenation. You can use the same number multiple times. There can be up to 256 values, totaling up to 8 MiB. Object and array values can be passed only as a complete VALUES value in INSERT or complete SET value in UPDATE.

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
| Update or delete is rejected | Check update/delete rather than read permission, field- and record-level access conditions, WHERE or LIMIT, and the 1,000-row maximum. |
| Timeout | Reduce conditions, projected columns, LIMIT, joins, and subqueries. One statement can run for up to 30 seconds, total read processing for 65 seconds, write processing for 35 seconds, and the whole request for 130 seconds. |
| A conflict occurs or rerunning is needed | Retrieve the latest record and check the result. Do not automatically retry a write whose update status is unknown with the same conditions. |

## Checklist

- [ ] I checked the target Project, Object, fields, and records
- [ ] I separately checked read and write/delete permissions
- [ ] I specified the needed WHERE, ORDER BY, and LIMIT for SELECT
- [ ] I specified WHERE or LIMIT for UPDATE/DELETE
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
