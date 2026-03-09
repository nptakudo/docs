---
title: "Supported queries for dynamic tables"
url: "https://docs.snowflake.com/en/user-guide/dynamic-tables-supported-queries"
---

# Supported queries for dynamic tables

Dynamic tables support standard SQL expressions and Snowflake-supported functions, including mathematical operations, string functions, date
functions, etc. This topic describes the expressions, constructs, functions, operators, and clauses that dynamic tables support in
incremental and full refresh modes.

If a query uses expressions, keywords, operators, or clauses that are not supported for incremental refresh, the automated refresh process
uses a full refresh instead, [which might incur an additional cost](dynamic-tables-cost.html#label-dt-costs-refresh-schedule).

For guidance on how different operators affect incremental refresh *performance*, see
[Optimize queries for incremental refresh](dynamic-tables-performance-optimize-query).

## Supported data types

Dynamic tables support all [Snowflake SQL data types](../sql-reference/intro-summary-data-types)
for both incremental and full refresh, except:

* Structured data types.
* Geospatial data types (full refresh only).

## Supported queries in incremental and full refresh modes

| Keyword | Incremental Refresh Mode | Full Refresh Mode |
| --- | --- | --- |
| [DISTINCT](../sql-reference/sql/select) | Supported | Supported |
| [External functions](../sql-reference/external-functions-introduction) | Not supported | Not supported |
| [FROM](../sql-reference/constructs/from) | Source tables, views, Snowflake-managed Apache Iceberg™ tables, and other dynamic tables.  Subqueries outside of FROM clauses (for example, WHERE EXISTS) are not supported. | Supported |
| [GROUP BY](../sql-reference/constructs/group-by) | Supported | Supported |
| [CROSS JOIN](../sql-reference/constructs/join) | Supported. You can specify any number of tables in the join, and updates to all tables in the join are reflected in the results of the query. | Supported |
| [INNER JOIN](../sql-reference/constructs/join) | Supported. You can specify any number of tables in the join, and updates to all tables in the join are reflected in the results of the query. | Supported |
| [LATERAL](../sql-reference/constructs/join-lateral) JOIN | Not supported. However, you can use [LATERAL with FLATTEN()](lateral-join-using.html#label-lateral-flatten-example). For example:  CopyExpand  ``` CREATE TABLE persons  AS   SELECT column1 AS id, parse_json(column2) AS entity   FROM values    (12712555,    '{ name:  { first: "John", last: "Smith"},      contact: [      { business:[        { type: "phone", content:"555-1234" },        { type: "email", content:"j.smith@example.com" } ] } ] }'),    (98127771,     '{ name:  { first: "Jane", last: "Doe"},      contact: [      { business:[        { type: "phone", content:"555-1236" },        { type: "email", content:"j.doe@example.com" } ] } ] }'); ```  Show lessSee more  Scroll to top  CopyExpand  ``` CREATE DYNAMIC TABLE my_dynamic_table  TARGET_LAG = DOWNSTREAM  WAREHOUSE = mywh  AS   SELECT p.id, f.value, f.path   FROM persons p,   LATERAL FLATTEN(input => p.entity) f; ```  Show lessSee more  Scroll to top  Note the following behavior for using lateral flatten with incremental refresh:   * Selecting the flatten SEQ column from a lateral flatten join is not supported. * When using the [AUTO](dynamic-tables-refresh.html#label-dynamic-tables-intro-refresh-modes) parameter, Snowflake typically chooses incremental refresh for queries with lateral flatten joins, unless prevented by other limitations. | Supported. |
| OUTER-EQUI JOIN. | Supported. You can specify any number of tables in the join, and updates to all tables in the join are reflected in the results of the query. | Supported |
| [{LEFT | RIGHT | FULL }] [OUTER JOIN](querying-joins.html#label-querying-join-outer) | The following is not supported:   * Outer joins where both sides are the same table. * Outer joins where both sides are a subquery with GROUP BY clauses. * Outer joins with non-equality predicates.   Otherwise, you can specify any number of tables in an outer join, and updates to all tables in the join are reflected in the results of the query. | Supported |
| [ML or LLM functions](snowflake-cortex/aisql) | Supported in the SELECT clause. | Supported |
| [PIVOT](../sql-reference/constructs/pivot) and [UNPIVOT](../sql-reference/constructs/unpivot) | Not supported | Not supported |
| [SAMPLE / TABLESAMPLE](../sql-reference/constructs/sample) | Not supported | Not supported |
| Scalar Aggregates | Supported | Supported |
| [SELECT](../sql-reference/sql/select) | Expressions including those using deterministic built-in functions and [immutable](../sql-reference/sql/create-function.html#label-create-function-syntax) [user-defined functions](../developer-guide/udf/udf-overview). | Supported |
| [Set operators](../sql-reference/operators-query) (UNION, MINUS, EXCEPT, INTERSECT) | Not supported, except for UNION. In incremental refresh, the UNION set operator works like the combination of the UNION ALL and SELECT DISTINCT operators. | Supported |
| [Sequences](querying-sequences). | Not supported | Not supported |
| All [subquery operators](../sql-reference/operators-subquery). | Not supported | Supported |
| [UNION ALL](../sql-reference/operators-query) | Supported | Supported |
| [User-defined functions](../developer-guide/udf/udf-overview) (UDFs) | Supported, except for the following limitations:   * UDFs written in Python, Java, Scala, or Javascript that specify the [VOLATILE](../sql-reference/sql/create-function.html#label-create-function-syntax) parameter are not supported. * UDFs written in SQL that contain subqueries are not supported (for example, a SELECT statement). * Replacing an [IMMUTABLE](../sql-reference/sql/create-function.html#label-create-function-syntax) UDF while it’s in use by a dynamic table that uses incremental refresh results in failed refreshes. * Importing UDFs from an external stage is not supported. | Supported |
| [User-defined table functions](../developer-guide/udf/udf-overview) (UDTFs) | Supported, except for the following limitations:   * UDTFs written in SQL are not supported. * SELECT blocks that read from UDTFs must explicitly specify columns and can’t use `*`. | Supported |
| [WHERE](../sql-reference/constructs/where) / [HAVING](../sql-reference/constructs/having) / [QUALIFY](../sql-reference/constructs/qualify) | Filters with the same expressions that are valid in SELECT are supported.  Filters with the CURRENT\_TIMESTAMP, CURRENT\_TIME, and CURRENT\_DATE functions and their aliases are supported. | Supported.  Filters with the CURRENT\_TIMESTAMP, CURRENT\_TIME, and CURRENT\_DATE functions and their aliases are supported. |
| [Window functions](../sql-reference/functions-window) | Supported, except for the following limitations:   * Using the window functions PERCENT\_RANK, DENSE\_RANK, RANK with sliding window frames is not supported. * Using ANY\_VALUE is not supported since it’s a non-deterministic function. | Supported |
| [WITH](../sql-reference/constructs/with) | [Common table expressions (CTEs)](queries-cte) that use incremental refresh supported features in the subquery are supported.  WITH RECURSIVE is not supported. | Supported |

See moreShow less

Expand

## Supported non-deterministic functions in incremental and full refresh modes

| Non-deterministic Function | Incremental Refresh Mode | Full Refresh Mode |
| --- | --- | --- |
| [ANY\_VALUE](../sql-reference/functions/any_value) | Not supported | Not supported |
| [CLASSIFY\_TEXT (SNOWFLAKE.CORTEX)](../sql-reference/functions/classify_text-snowflake-cortex) | Supported in the SELECT clause | Supported |
| [COMPLETE (SNOWFLAKE.CORTEX)](../sql-reference/functions/complete-snowflake-cortex) | Supported in the SELECT clause | Supported |
| [CURRENT\_ACCOUNT](../sql-reference/functions/current_account) | Not supported | Supported |
| [CURRENT\_DATE](../sql-reference/functions/current_date) (and aliases) | Supported only as a part of a WHERE/HAVING/QUALIFY clause. | Supported only as a part of a WHERE/HAVING/QUALIFY clause. |
| [CURRENT\_REGION](../sql-reference/functions/current_region) | Not supported | Supported |
| [CURRENT\_ROLE](../sql-reference/functions/current_role) | Not supported | Supported |
| [CURRENT\_TIME](../sql-reference/functions/current_time) (and aliases) | Supported only as a part of a WHERE/HAVING/QUALIFY clause. | Supported only as a part of a WHERE/HAVING/QUALIFY clause. |
| [CURRENT\_TIMESTAMP](../sql-reference/functions/current_timestamp) (and aliases) | Supported only as a part of a WHERE/HAVING/QUALIFY clause. | Supported only as a part of a WHERE/HAVING/QUALIFY clause. |
| Functions that rely on [CURRENT\_USER](../sql-reference/functions/current_user). | Not supported. Dynamic table refreshes act as their owner role with a special SYSTEM user. | Not supported. Dynamic table refreshes act as their owner role with a special SYSTEM user. |
| [CURRENT\_WAREHOUSE](../sql-reference/functions/current_warehouse) | Not supported | Supported |
| [DENSE\_RANK](../sql-reference/functions/dense_rank) | Supported | Supported |
| [EMBED\_TEXT\_768 (SNOWFLAKE.CORTEX)](../sql-reference/functions/embed_text-snowflake-cortex) | Supported in the SELECT clause | Supported |
| [EMBED\_TEXT\_1024 (SNOWFLAKE.CORTEX)](../sql-reference/functions/embed_text_1024-snowflake-cortex) | Supported in the SELECT clause | Supported |
| [EXTRACT\_ANSWER (SNOWFLAKE.CORTEX)](../sql-reference/functions/extract_answer-snowflake-cortex) | Supported in the SELECT clause | Supported |
| [FINETUNE (SNOWFLAKE.CORTEX)](../sql-reference/functions/finetune-snowflake-cortex) | Supported in the SELECT clause | Supported |
| [FIRST\_VALUE](../sql-reference/functions/first_value) | Supported | Supported |
| [LAST\_VALUE](../sql-reference/functions/last_value) | Supported | Supported |
| [NTH\_VALUE](../sql-reference/functions/nth_value) | Supported | Supported |
| [RANK](../sql-reference/functions/rank) | Supported | Supported |
| [ROW\_NUMBER](../sql-reference/functions/row_number) | Supported | Supported |
| [SENTIMENT (SNOWFLAKE.CORTEX)](../sql-reference/functions/sentiment-snowflake-cortex) | Supported in the SELECT clause | Supported |
| [Sequence functions](../sql-reference/functions/seq1) (e.g., `SEQ1`, `SEQ2`) | Not supported | Supported |
| [TRANSLATE (SNOWFLAKE.CORTEX)](../sql-reference/functions/translate-snowflake-cortex) | Supported in the SELECT clause | Supported |
| [VOLATILE](../sql-reference/sql/create-function.html#label-create-function-syntax) user-defined functions | Not supported | Supported |

See moreShow less

Expand

## Supported Snowflake Cortex AI functions

You can use [Snowflake Cortex AI Functions (including LLM functions)](snowflake-cortex/aisql) in the SELECT clause for dynamic tables in incremental refresh mode. The same
availability restrictions as described in [Cortex AI functions](snowflake-cortex/aisql.html#label-cortex-llm-ai-function) apply.

Cortex AI Functions let you add AI-powered insights directly to your dynamic tables, automatically analyzing data as it updates. For example, it can
classify customer reviews, support tickets, or survey responses as positive/negative or assign categories.

In the following example, `review_sentiment` uses AI\_FILTER to evaluate each review with an LLM. Cortex AI Functions combine
the prompt `The reviewer enjoyed the restaurant` with the actual review text. The output column `enjoyed` is the classification
generated using Cortex AI Functions based on the prompt, indicating whether the reviewer enjoyed the restaurant.

CopyExpand

```
CREATE OR REPLACE TABLE reviews AS
  SELECT 'Wow... Loved this place.' AS review
  UNION ALL
  SELECT 'The pizza is not good.' AS review;

CREATE OR REPLACE DYNAMIC TABLE review_sentiment
  TARGET_LAG = DOWNSTREAM
  WAREHOUSE = mywh
  REFRESH_MODE = INCREMENTAL
  AS
    SELECT review, AI_FILTER(CONCAT('The reviewer enjoyed the restaurant', review), {'model': 'llama3.1-70b'}) AS enjoyed FROM reviews;
```

Show lessSee more

Scroll to top
