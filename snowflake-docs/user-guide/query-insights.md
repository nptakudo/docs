---
title: "Using query insights to improve performance"
url: "https://docs.snowflake.com/en/user-guide/query-insights"
---

# Using query insights to improve performance

If conditions exist that affect query performance, Snowflake provides insights about these conditions. Each insight includes a
message that explains how query performance might be affected and provides a general recommendation for improving performance.

You can access these insights in Snowsight and by querying
[the QUERY\_INSIGHTS view](../sql-reference/account-usage/query_insights).

The next sections provide details about query insights:

* [List of insight types](#label-query-insights-types)
* [Viewing the query insights in Snowsight](#label-query-insights-viewing)
* [Limitations](#label-query-insights-limitations)

## List of insight types

The Query Insights pane and [The QUERY\_INSIGHTS view](../sql-reference/account-usage/query_insights) provide the
insights, which include:

* A message about the condition detected and how it can affect query performance.
* Details about the part of the query that produced the condition.
* A suggested next step to address the condition, if the condition negatively affects performance.

The following table lists the types of insights by type ID.

|  |  |
| --- | --- |
| Type ID | Insight |
| `QUERY_INSIGHT_NO_FILTER_ON_TOP_OF_TABLE_SCAN` | [No filter on table scan](#label-query-insight-no-filter-on-top-of-table-scan) |
| `QUERY_INSIGHT_INAPPLICABLE_FILTER_ON_TABLE_SCAN` | [Filter not applicable](#label-query-insight-inapplicable-filter-on-table-scan) |
| `QUERY_INSIGHT_UNSELECTIVE_FILTER` | [Filter not selective](#label-query-insight-unselective-filter) |
| `QUERY_INSIGHT_LIKE_WITH_LEADING_WILDCARD` | [LIKE filter with leading wildcard](#label-query-insight-like-with-leading-wildcard) |
| `QUERY_INSIGHT_FILTER_WITH_CLUSTERING_KEY` | [Filter uses clustering key](#label-query-insight-filter-with-clustering-key) |
| `QUERY_INSIGHT_SEARCH_OPTIMIZATION_USED` | [Query benefited from search optimization](#label-query-insight-search-optimization-used) |
| `QUERY_INSIGHT_SNOWFLAKE_OPTIMA` | [Query benefited from Snowflake Optima](#label-query-insight-snowflake-optima) |
| `QUERY_INSIGHT_SEARCH_OPTIMIZATION_AND_SNOWFLAKE_OPTIMA` | [Query benefited from search optimization and Snowflake Optima](#label-query-insight-search-optimization-and-snowflake-optima) |
| `QUERY_INSIGHT_JOIN_WITH_NO_JOIN_CONDITION` | [Join with no join condition](#label-query-insight-join-with-no-join-condition) |
| `QUERY_INSIGHT_INEFFICIENT_JOIN_CONDITION` | [Join with inefficient join condition](#label-query-insight-inefficient-join-condition) |
| `QUERY_INSIGHT_NESTED_EXPLODING_JOIN` | [Exploding join (nested join)](#label-query-insight-nested-exploding-join) |
| `QUERY_INSIGHT_EXPLODING_JOIN` | [Exploding join (not nested)](#label-query-insight-exploding-join) |
| `QUERY_INSIGHT_INEFFICIENT_AGGREGATE` | [Unnecessary aggregation](#label-query-insight-inefficient-aggregate) |
| `QUERY_INSIGHT_UNNECESSARY_UNION_DISTINCT` | [Unnecessary UNION [ DISTINCT ] clause](#label-query-insight-unnecessary-union-distinct) |
| `QUERY_INSIGHT_REMOTE_SPILLAGE` | [Remote spillage](#label-query-insight-remote-spillage) |
| `QUERY_INSIGHT_QUEUED_OVERLOAD` | [Query was in the queue for the warehouse for too long](#label-query-insight-queued-overload) |

See moreShow less

Expand

### No filter on table scan

A query or subquery has no WHERE clause, which means that the query scans an entire table and might return more rows than
intended.

To improve performance, add a WHERE clause to reduce the amount of data scanned.

### Filter not applicable

A WHERE clause doesn’t filter out any rows, which means that the query might scan more data than intended.

To improve performance, add a more selective condition to the WHERE clause, or make the existing condition more selective.

### Filter not selective

A WHERE clause doesn’t significantly reduce the number of rows, which means that the query might scan more data than intended.

Unlike the [Filter not applicable](#label-query-insight-inapplicable-filter-on-table-scan) insight, this insight indicates that the WHERE
clause is filtering out some rows but it could have been more selective.

To improve performance, add a more selective condition to the WHERE clause, or make the existing condition more selective.

### LIKE filter with leading wildcard

The query uses a LIKE filter that starts with a wildcard character. Specifying a pattern that starts with a wildcard can result in
scanning a large amount of data.

To reduce the amount of data scanned, specify a pattern that does not start with a wildcard, if possible. If you need to specify a
pattern that starts with a wildcard, consider enabling [search optimization](search-optimization-service) for
more efficient pattern matching.

### Filter uses clustering key

The query benefited from filtering on a [clustering key for the table](tables-clustering-keys).

### Query benefited from search optimization

The query benefited from filtering on a column that is configured for
[search optimization](search-optimization-service).

### Query benefited from Snowflake Optima

The query benefited from [Snowflake Optima](snowflake-optima).

### Query benefited from search optimization and Snowflake Optima

The query benefited from [search optimization](search-optimization-service) and
[Snowflake Optima](snowflake-optima).

### Join with no join condition

The join is missing the join condition. The result is a [cross join](querying-joins.html#label-querying-join-cross), which returns every
possible combination of rows.

To reduce the row count produced by this join, specify one or more join conditions.

### Join with inefficient join condition

The join contains a complex join condition that is evaluated after the data sets are joined. This is less efficient than if the
condition were evaluated before the data sets were joined, which reduces the amount of data that the join must process.

To speed up this query, simplify the join condition.

### Exploding join (nested join)

A join that includes the output of at least one other join is
[returning many more rows than are in the tables being joined](ui-snowsight-activity.html#label-exploding-join). This might indicate a problem with
the join conditions for the child joins.

To prevent the join from producing more rows than the joined tables contain, add or change the join conditions for the child
joins. In addition, adding a WHERE clause to a subquery used in a child join might reduce the number of rows returned.

### Exploding join (not nested)

A join of two data sets (for example, tables, views, or output from table function calls) is
[returning many more rows than the joined tables contain](ui-snowsight-activity.html#label-exploding-join). This might indicate a problem with
the join condition.

To prevent the join from producing more rows than are in the tables being joined, add or change the join condition. In addition,
adding a WHERE clause to a subquery used by this join might reduce the number of rows returned.

### Unnecessary aggregation

The DISTINCT or GROUP BY clause produces the same number of rows as the same statement without the DISTINCT or GROUP BY clause.
Specifying the clause introduces an additional processing step that has no effect on the result.

To improve performance, remove the unnecessary DISTINCT or GROUP BY clause.

### Unnecessary UNION [ DISTINCT ] clause

The UNION [ DISTINCT ] clause isn’t necessary because the input sets are disjoint.

To improve performance, use UNION ALL, rather than UNION [ DISTINCT ].

### Remote spillage

This query scanned more data than the warehouse had capacity to store. As a result, the warehouse
[spilled data](ui-snowsight-activity.html#label-spillage) to storage, which slowed down the processing of the query.

To prevent this problem, use a larger warehouse that has more capacity. If using a larger warehouse is not an option, change the
query to process data in smaller batches.

### Query was in the queue for the warehouse for too long

This query was [waiting in the queue for the warehouse](warehouses-overview.html#label-warehouses-overview-query-processing) for too long.

To avoid this problem, use a larger warehouse that has more capacity, or use a warehouse that has fewer concurrent queries.

## Viewing the query insights in Snowsight

In [Query Profile](ui-snowsight-activity.html#label-snowsight-specific-query-details) tab under Query History, you can view the insights for a
query. The nodes that have corresponding insights are highlighted.

The Query Insights pane on the right displays each type of insight that was detected for this query and lists each instance
of that insight type that was detected for the query. To learn more about the condition that was detected, select View
next to an entry in the Query Insights pane.

The details include the recommended next steps to take to improve the performance of the query. You can select
Learn more to view more information about this insight.

## Limitations

* Insights are produced for SQL queries that are made against databases and are processed by warehouses.
* Snowflake does not produce the [“filter not selective” insight](#label-query-insight-unselective-filter) for queries that
  are accelerated by the [query acceleration service](query-acceleration-service).
* Insights are not produced for:

  + Queries for which the query plan takes multiple steps to finish.
  + Queries involving secure objects.
  + Queries executed against hybrid tables (Unistore).
  + Queries generated by Native Apps.
  + EXPLAIN queries.
  + Queries that [reuse results](querying-persisted-results).
  + Queries executing on [interactive tables](interactive).
