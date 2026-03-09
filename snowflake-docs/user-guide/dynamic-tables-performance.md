---
title: "Dynamic table performance and optimization"
url: "https://docs.snowflake.com/en/user-guide/dynamic-tables-performance"
---

# Dynamic table performance and optimization

Learn how to optimize and monitor dynamic tables for speed and cost-efficiency. This section
provides foundational concepts and links to more detailed topics.

Dynamic table *performance* refers to how quickly and efficiently a
[dynamic table refresh](dynamic-tables-refresh) completes. A
well-performing dynamic table refreshes fast enough to meet its [target lag](dynamic-tables-target-lag) without
consuming excessive compute resources.

## Why performance matters

Data freshness
:   Dynamic tables refresh based on a [target lag](dynamic-tables-target-lag) that you specify,
    which is the maximum allowed delay between updates to source tables and the dynamic table’s content.
    When refreshes take too long, your pipeline might not meet your freshness requirements.

    For example, setting a target lag of five minutes when your refresh takes eight minutes means your
    pipeline can’t maintain the required freshness.

Cost efficiency
:   Dynamic tables require virtual warehouses for refreshes, which consume credits. Poorly optimized
    dynamic tables might scan more data than necessary, trigger full refreshes when incremental would
    suffice, or require larger warehouses to complete within target lag windows.

    For more information about costs, see [Understanding costs for dynamic tables](dynamic-tables-cost).

## Performance decisions

Changes that affect dynamic table performance fall into two categories based on *when* you
can make them:

|  | Design changes | Adjustments |
| --- | --- | --- |
| **When** | Before you create a pipeline. | After your pipeline is running. |
| **Impact** | High | Medium |
| **Flexibility** | Hard to change; requires recreating tables. | Easy to change; no need to recreate tables. |
| **Examples** | Query structure, refresh mode, pipeline design. | Warehouse size, clustering keys, target lag. |

See moreShow less

Expand

For detailed guidance on both categories, see
[Optimize dynamic table performance](dynamic-tables-performance-optimize).

## Get started

To get started with dynamic table performance optimization, try the hands-on tutorial:

[Tutorial: Optimize dynamic table performance for SCD Type 1 workloads](tutorials/optimize-dynamic-table-performance)
:   Learn how to identify and resolve performance bottlenecks in a dynamic table pipeline. This tutorial
    shows how different SQL patterns affect incremental refresh and how to use the `QUALIFY` clause
    to efficiently remove duplicate rows.

## Topics in this section

[Monitor dynamic table performance](dynamic-tables-performance-monitor)
:   How to monitor refresh performance, analyze query profiles, and track key metrics.

[Optimize dynamic table performance](dynamic-tables-performance-optimize)
:   Key concepts and optimization techniques: refresh modes, data locality, warehouse sizing,
    target lag, query patterns, and clustering.

[Optimize queries for incremental refresh](dynamic-tables-performance-optimize-query)
:   Performance guide for how SQL operators affect incremental refresh speed.

[Use immutability constraints](dynamic-tables-performance-optimize-immutability)
:   How to use immutability constraints to mark historical data as unchanging and reduce refresh scope.
