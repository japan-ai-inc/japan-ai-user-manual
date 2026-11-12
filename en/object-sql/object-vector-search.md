# Object Vector Search

With Object SQL, you can use vector search to find records semantically close to a search term rather than by exact or partial text match. It is suited to retrieving FAQ entries, descriptions, inquiry histories, and similar items in candidate order when their wording differs from the query. First configure the fields to search, then execute a `SELECT` containing `vector_score()` after search data is generated for the records.

For basic Object SQL reading, updating, and deletion, see [Object SQL DML](object-sql-dml.md). For Object structure and general constraints, see [Object Specifications and Limits](../build/object-specs-and-limits.md). For how to distinguish it from Dataset RAG references, also see [Prepare a Dataset and Make an Agent Reference It](../build/prepare-and-reference-datasets.md).

## Related articles

- [Object SQL DML](object-sql-dml.md)
- [Object Specifications and Limits](../build/object-specs-and-limits.md)
- [Prepare a Dataset and Make an Agent Reference It](../build/prepare-and-reference-datasets.md)
- [Object SQL DDL](object-sql-ddl.md)

## Scope of this article

This article describes vector search in Object SQL, which treats Objects as tables, fields as columns, and records as rows. It covers defining fields to search, `SELECT` syntax, combinations with normal conditions, constraints, and how to account for the wait until results are reflected.

The confirmed entry points are the Workflow "Custom Object SQL" node and an Agent's Object SQL tool. In the Workflow node, enter a statement in "SQL," and SELECT results can be output to "Query result array" and "Total count (SELECT queries)." A public interface for directly executing Object SQL could not be confirmed in the SDK, CLI, or public REST API. Do not assume execution from an external application.

> Vector search is not a feature that works automatically for every Object or every string column. At least one field to search is required, and generation and processing of search data must be available in your environment. If the screen or execution entry point is not shown, ask an administrator about permissions, the Project, and organization availability conditions.

## Before you begin

- Check the target Object, the field containing text to search, and the Workflow or Agent that will use the results.
- Use only string fields as fields to search. You cannot specify number, Boolean, date/time, array, or JSON fields as search targets.
- When validating records immediately after creation or update, design the test to wait for search results to be reflected.
- Prepare the search term as a fixed string literal. You cannot pass a column name, variable, or expression as an argument to `vector_score()`.

## 1. Define fields to search

In Object SQL DDL, append `EMBEDDING` immediately after the type of a string column to define that field as a vector search target. The following is an example for a new Object.

```sql
CREATE TABLE tickets (
  subject TEXT EMBEDDING,
  status TEXT,
  priority INTEGER
);
```

To add a search target column to an existing Object, use the following.

```sql
ALTER TABLE tickets
  ADD COLUMN details TEXT EMBEDDING;
```

`EMBEDDING` can be specified only for string-type columns such as `TEXT`. If you use `vector_score()` on an Object with no target field, the search is rejected during validation. Even if you change field settings to target existing data, existing records may not appear in results until the asynchronous processing described later finishes.

> `EMBEDDING` is not a normal `INDEX`, `UNIQUE`, or `LIKE` search specification. It specifies a search target for obtaining candidates based on "semantic similarity." Design field types, requiredness, unique constraints, and similar settings according to [Object Specifications and Limits](../build/object-specs-and-limits.md).

## 2. Retrieve semantically similar records

The function name is `vector_score()`. Its argument must be **one string literal** that is not only whitespace. Usually, give the function an alias, sort descending by that alias, and specify the number of records with `LIMIT`.

```sql
SELECT
  subject,
  status,
  vector_score('I want to reset my password') AS similarity
FROM tickets
ORDER BY similarity DESC
LIMIT 10;
```

Each row in results containing `vector_score()` returns `similarity_score` in addition to selected columns. Larger values indicate closer candidates. The alias is for sorting in SQL; it does not necessarily replace the name of the returned value.

To set a lower similarity threshold, compare `vector_score()` with the same search term in `WHERE`. The threshold is a numeric literal from 0.0 to 1.0.

```sql
SELECT subject, status, vector_score('About issuing an invoice') AS similarity
FROM tickets
WHERE vector_score('About issuing an invoice') >= 0.70
ORDER BY similarity DESC
LIMIT 10;
```

When you write the search term in multiple places, make every occurrence exactly the same string. A search term is limited to 8,000 characters after trimming leading and trailing whitespace. An empty string, whitespace-only string, column reference, or placeholder cannot be an argument.

## 3. Combine with normal conditions

In `WHERE`, you can combine a vector condition and normal conditions with `AND`. This lets you, for example, rank records close to a question only among records that are still open.

```sql
SELECT subject, priority, vector_score('Cannot log in') AS similarity
FROM tickets
WHERE status = 'open'
  AND priority <= 2
  AND vector_score('Cannot log in') >= 0.50
ORDER BY similarity DESC
LIMIT 20;
```

You can also use `LIKE` or `ILIKE` as a normal condition.

```sql
SELECT subject, vector_score('Authentication fails') AS similarity
FROM tickets
WHERE subject ILIKE '%authentication%'
ORDER BY similarity DESC
LIMIT 10;
```

However, `LIKE` is string pattern matching, while `vector_score()` ranks by semantic similarity. The former does not find paraphrases by itself, while the latter does not necessarily contain a specific term. State, assignee, date, and string conditions required for business must be explicit in `WHERE`; use semantic similarity for candidate ranking. You cannot branch vector conditions with `OR`.

## Constraints and handling results

| Item | Conditions and constraints |
|---|---|
| Number of records | `LIMIT` is required and must be 1-50. `OFFSET` cannot be used. It is not suited to sequentially traversing search pages. |
| Sort order | Specify only similarity as `DESC`. `ASC`, a second sort column, and sorting by another column cannot be used. You can also write `ORDER BY vector_score('…') DESC` directly. |
| SQL form | It cannot be combined with `JOIN`, subqueries, `DISTINCT`, aggregate functions, or `GROUP BY`. If aggregation is needed, retrieve candidates first, then perform it as a separate process. |
| Threshold | If omitted, the top `LIMIT` candidates are returned. When specified, compare it with a numeric literal from 0.0 to 1.0. |
| Total count | Vector search differs from normal paginated search and does not return whether there is a next page. Design for handling the candidates within `LIMIT` in one pass. |

## Notes on reflection timing and search quality

For an Object with search target fields, after a record is created or a search target value changes, a request to generate search data is issued after the transaction is committed, and the data is generated and stored in another process. Therefore, successful saving or updating does not mean that content can immediately be found by vector search. If only non-search-target fields change, the change is evaluated so that the same search data can be reused.

Search also indicates semantic closeness of candidates; it does not guarantee correctness, completeness, or recency. Do not judge based on short terms alone. In subsequent processing, check the returned record text, status, update time, and accessible scope. If generation of search data or the search execution fails or is delayed, the latest update may not be reflected, or the search itself may fail.

## When to use Dataset RAG references

| Goal | Feature to select | Why |
|---|---|---|
| Search structured records in an Object semantically after narrowing by status, assignee, number, or similar conditions | Object SQL vector search | It handles business conditions with `WHERE` and similarity order in the same read. |
| Import materials such as PDFs, the web, and external data for an Agent or Workflow to find reference information for responses | Dataset RAG references | This feature is for importing and syncing materials and RAG references. |
| Find records that strictly contain a specific notation | `WHERE ... LIKE` / `ILIKE` | It can use matching a string pattern as a condition and does not make a semantic similarity determination. |

For Dataset RAG references, import materials, confirm processing is complete, then configure the Agent or Workflow. In contrast, Object SQL reads Object records directly with SQL. Do not use search results alone to complete tasks that require checking all content of long materials without omissions.

## If something does not work

| Situation | What to check and do |
|---|---|
| An error indicates there is no field to search | Check that the target Object has at least one string search target field, such as `TEXT EMBEDDING`. |
| A record saved just now does not appear | Asynchronous search data generation may not be complete. Wait briefly and check again with the same fixed search term; also check that the target field value was saved. |
| SQL has a validation error | Use `LIMIT 1`-`LIMIT 50`, one string literal, `DESC`, and one similarity order. Remove `OFFSET`, `JOIN`, subqueries, `OR`, aggregation, and `DISTINCT`. |
| Candidates without a required term are returned | Do not rely only on semantic search. Add normal conditions such as `AND subject ILIKE '%term%'`, as well as state, assignee, and date conditions. |
| The entry point is not shown or cannot run | Check the target Project, Workflow or Agent settings, permissions, and organization availability conditions. Ask an administrator before substituting equivalent SQL execution through a public API, SDK, or CLI. |

## Checklist

- [ ] I defined a string field to search with `EMBEDDING`
- [ ] I designed for waiting until search data for new or updated records is reflected
- [ ] I passed exactly one nonempty fixed string to `vector_score()`
- [ ] I specified `LIMIT` from 1 to 50 and sorted only similarity with `DESC`
- [ ] I narrowed mandatory business conditions with normal `AND` conditions
- [ ] I treated `similarity_score` as candidate ranking and checked record text and business conditions
- [ ] I separated use cases needing RAG references for materials from those needing Object record search

## Next articles to read

- [Object SQL DML](object-sql-dml.md)
- [Object Specifications and Limits](../build/object-specs-and-limits.md)
- [Prepare a Dataset and Make an Agent Reference It](../build/prepare-and-reference-datasets.md)
- [Object SQL DDL](object-sql-ddl.md)
