---
title: "Optimizing query performance"
url: "https://docs.snowflake.com/en/user-guide/performance-query-options"
---

# Optimizing query performance

You can optimize Snowflake query performance in the following ways:

* Search optimization service
* Query acceleration
* Creating one or more materialized views (clustered or unclustered)
* Clustering a table

Each of these optimization methods has different advantages, as shown in the following table:

| Feature | Supported query types | Notes |
| --- | --- | --- |
| [Search optimization service](search-optimization-service) | * [Equality searches](search-optimization/point-lookup-queries.html#label-search-optimization-service-queries-equality-in). * [Substring and regular expression searches](search-optimization/substring-queries.html#label-search-optimization-service-queries-wildcard-regexp). * [Character data (text) and IP address searches](search-optimization/text-queries). * Searches of [elements in VARIANT](search-optimization/semi-structured-queries.html#label-search-optimization-service-queries-variant). * Searches of [elements in structured types](search-optimization/structured-queries.html#label-search-optimization-service-queries-structured). * Searches of [GEOGRAPHY columns using geospatial functions](search-optimization/geospatial-queries.html#label-search-optimization-service-queries-geo).   The search optimization service can improve the performance of these types of searches for the [supported data types](search-optimization/queries-that-benefit.html#label-search-optimization-service-supported-data-types). |  |
| [Query acceleration service](query-acceleration-service) | Queries with filters or aggregation. If the query includes LIMIT, the query must also include ORDER BY.  The filters must be highly selective, and the ORDER BY clause must have a low cardinality.    Query acceleration works well with ad-hoc analytics, queries with unpredictable data volume,  and queries with large scans and selective filters. | Query acceleration and search optimization are complementary. Both can accelerate the same query. See [Compatibility with query acceleration](#label-sos-with-query-acceleration). |
| [Materialized views](views-materialized) | * Equality searches. * Range searches. * Sort operations. | You can also use materialized views to define different clustering keys on the same source table, or a subset of that table, or to store flattened JSON or VARIANT data so it only needs to be flattened once.  Materialized views improve performance only for the subset of rows and columns included in the materialized view. |
| [Clustering the table](tables-clustering-keys) | * Equality searches. * Range searches. | A table can be clustered only on a single key, which can contain one or more columns or expressions. |

See moreShow less

Expand

The following table shows which of these optimizations have storage or compute costs:

| Optimization | Storage cost | Compute cost |
| --- | --- | --- |
| Search optimization service | ✔ | ✔ |
| Query acceleration service |  | ✔ |
| Materialized view | ✔ | ✔ |
| Clustering the table | ✔ [[1]](#id2) | ✔ |

See moreShow less

Expand

[[1](#id1)]

The process of reclustering can increase the size of [fail-safe](data-failsafe) storage
because of the rewriting of existing partitions into new partitions. Reclustering doesn’t introduce any new rows.
For more information, see [Credit and Storage Impact of Reclustering](tables-clustering-keys.html#label-clustering-keys-reclustering-credit-storage).

## Compatibility with query acceleration

Search optimization and [query acceleration](query-acceleration-service) can work together to
optimize query performance. First, search optimization can prune the [micro-partitions](tables-clustering-micropartitions.html#label-what-are-micropartitions) that aren’t needed for a query. Then, for [eligible queries](query-acceleration-service.html#label-identifying-queries-warehouses-for-qas), query acceleration can offload portions of the rest of the work to
shared compute resources that the service provides.

The performance of queries that are accelerated by both services varies depending on the workload and available resources.
