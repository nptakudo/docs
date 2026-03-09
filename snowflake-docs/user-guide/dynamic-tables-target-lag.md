---
title: "Understanding dynamic table target lag"
url: "https://docs.snowflake.com/en/user-guide/dynamic-tables-target-lag"
---

# Understanding dynamic table target lag

Dynamic table refresh is triggered by the data’s target lag, which determines how outdated it can be. You can set a fixed target lag
or set the dynamic table to DOWNSTREAM, making its refresh timing depend on the dynamic tables that depend on it.

The target lag for a dynamic table is measured relative to the dynamic tables at the root of the graph, not the dynamic tables directly
upstream. To see the graph of tables connected to your dynamic table, see [View the graph of tables connected to your dynamic tables](dynamic-tables-monitor.html#label-dts-monitor-graph-view).

Snowflake schedules refreshes to keep the actual lag of your dynamic tables below their target lag. The duration of each refresh depends on
the query, data pattern, and warehouse size. When choosing a target lag, consider the time needed to refresh each
[dynamic table in a chain](dynamic-tables-create.html#label-dynamic-tables-intro-dependencies) to the root. If you don’t, some refreshes might be skipped, leading
to a higher actual lag.

## Types of target lag

You specify target lag in one of the following ways. Target lag is inversely proportional to the dynamic table’s refresh frequency: frequent
refreshes imply a lower lag.

1. **Measure of freshness**: Defines the maximum amount of time that the dynamic table’s content should lag behind updates to the base tables.

   > The following example sets `my_dynamic_table` to refresh and maintain freshness within every hour:
   >
   > CopyExpand
   >
   > ```
   > ALTER DYNAMIC TABLE my_dynamic_table SET TARGET_LAG = '1 hour';
   > ```
   >
   > Show lessSee more
   >
   > Scroll to top
2. **Downstream**: Specifies that the dynamic table should refresh on demand when downstream tables (tables that depend on this table)
   refresh. This refresh can be triggered by [initialization at creation](dynamic-tables-refresh.html#label-dynamic-tables-initialization),
   [manual refresh](dynamic-tables-manual-refresh), or [scheduled refresh](dynamic-tables-refresh.html#label-dynamic-tables-scheduled-refresh)
   of a downstream table.

   When `refresh_mode` is set to `downstream`, the refresh schedule of a dynamic table is driven by the most demanding (shortest) lag of its
   downstream dependents. For example, if one downstream dependent table requires data that is no older than 10 minutes and another
   downstream dependent table requires data that is no older than 1 hour, the refresh schedule of this dynamic table will be every 10
   minutes because that is the shortest lag of its downstream dependents.

   In the following example, `my_dynamic_table` is set to refresh based on the target lag of its downstream dynamic tables. If
   `my_dynamic_table` doesn’t have any dynamic tables that depend on it, then it won’t refresh.

   CopyExpand

   ```
   ALTER DYNAMIC TABLE my_dynamic_table SET TARGET_LAG = DOWNSTREAM;
   ```

   Show lessSee more

   Scroll to top

   For more examples of downstream target lag, see [Example: Target lag for dynamic table chains](#label-dynamic-tables-target-lag-examples).

## How Snowflake schedules refreshes

Snowflake schedules refreshes slightly earlier than the target lag to allow time for the refresh to complete. For example, if you set the
target lag to 5 minutes, the table might refresh more frequently than every five minutes. Actual refresh intervals are often shorter than
the specified lag.

Note

Target lag is a target, not a guarantee. Snowflake attempts to keep data within the target lag, but actual lag may exceed the target
because of factors such as warehouse size, data volume, and query complexity.

For guidance on adjusting target lag for your workload, see [Alter the warehouse or target lag for dynamic tables](dynamic-tables-alter.html#label-dynamic-tables-manage-lag).
For information about optimizing your target lag, see [Identify the right target lag](dynamic-tables-performance-optimize.html#label-dt-optimize-target-lag).

## How upstream and downstream relationships affect target lag

The following diagram illustrates suspend, resume, and manual refresh operations in the context of upstream and downstream relationships to
other dynamic tables.

![Relationship between dynamic tables. Used to help explain suspend, resume, and manual refresh.](../_images/dynamic-tables-relationships.png)

The diagram depicts a simple declarative data pipeline built with dynamic tables:

* `DT2` is described as *downstream* of `DT1` because it depends on that dynamic table, and as *upstream* of `DT3`, which depends on it.
* `DT3` is downstream of both `DT2` and `DT1` because it depends on `DT2` directly and on `DT1` indirectly.
* `DT1` is directly or indirectly upstream of the other dynamic tables.

## Example: Target lag for dynamic table chains

Consider the following example where a dynamic table (`DT2`) reads from another dynamic table (`DT1`) to materialize its contents. In
this scenario, a report consumes `DT2`’s data via a query.

![Simple example of two dynamic tables: DT2, which is defined based on DT1.](../_images/dynamic-tables-example.png)

The following results are possible, depending on how each dynamic table specifies its lag:

| `DT1` | `DT2` | Refresh results |
| --- | --- | --- |
| `TARGET_LAG = DOWNSTREAM` | `TARGET_LAG = 10minutes` | `DT2` is updated at least every 10 minutes. `DT1` infers its lag from `DT2` and is updated every time `DT2` requires updates. |
| `TARGET_LAG = 10minutes` | `TARGET_LAG = DOWNSTREAM` | This scenario should be avoided. The report query will not receive any data. DT1 is frequently refreshed and `DT2` is not refreshed because there’s no dynamic table that’s based on `DT2`. |
| `TARGET_LAG = 5minutes` | `TARGET_LAG = 10minutes` | `DT2` is updated approximately every 10 minutes with data from `DT1` that’s at most 5 minutes old. |
| `TARGET_LAG = DOWNSTREAM` | `TARGET_LAG = DOWNSTREAM` | Neither `DT1` nor `DT2` is refreshed periodically because both of them have a downstream lag, and neither has a downstream consumer with a defined lag. |

See moreShow less

Expand
