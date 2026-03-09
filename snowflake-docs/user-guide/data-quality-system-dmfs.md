---
title: "System data metric functions"
url: "https://docs.snowflake.com/en/user-guide/data-quality-system-dmfs"
---

# System data metric functions

[![Snowflake logo in black (no text)](../_images/logo-snowflake-black.png)](../_images/logo-snowflake-black.png) [Enterprise Edition Feature](intro-editions)

Data Quality Monitoring requires Enterprise Edition. To inquire about upgrading, please contact
[Snowflake Support](https://docs.snowflake.com/user-guide/contacting-support).

This topic is a reference for the system data metric functions (DMFs) that Snowflake provides to all accounts. DMFs are the building block
of [data quality checks](data-quality-intro).

## About system DMFs

Snowflake provides system DMFs in the CORE schema of the shared [SNOWFLAKE database](../sql-reference/snowflake-db). System DMFs are
maintained by Snowflake; you cannot change the name or functionality of any system DMF.

Each system DMF enables you to measure a different data quality attribute. You can assign more than one system DMF to a table or view to
allow for a more comprehensive data quality measurement to address your governance and compliance needs.

## System DMFs

Currently, Snowflake supports these system DMFs to measure common metrics without having to define them:

| Category | System DMF | Description |
| --- | --- | --- |
| Accuracy | [BLANK\_COUNT](../sql-reference/functions/dmf_blank_count) | Determine how many blank values are in a column. |
|  | [BLANK\_PERCENT](../sql-reference/functions/dmf_blank_percent) | Determine what percentage of a column’s values are blank. |
|  | [NULL\_COUNT](../sql-reference/functions/dmf_null_count) | Determine how many NULL values are in a column. |
|  | [NULL\_PERCENT](../sql-reference/functions/dmf_null_percent) | Determine what percentage of a column’s values are NULL. |
| Freshness | [FRESHNESS](../sql-reference/functions/dmf_freshness) | Determine the freshness of a table’s data based on a timestamp column or the most recent [DML operation](../sql-reference/sql-dml). |
|  | [DATA\_METRIC\_SCHEDULE\_TIME](../sql-reference/functions/dmf_data_metric_schedule_time) | Define custom freshness metrics. |
| Statistics | [AVG](../sql-reference/functions/dmf_avg) | Determine the average value of a column. |
|  | [MAX](../sql-reference/functions/dmf_max) | Determine the maximum value of a column. |
|  | [MIN](../sql-reference/functions/dmf_min) | Determine the minimum value of a column. |
|  | [STDDEV](../sql-reference/functions/dmf_stddev) | Determine the standard deviation value for a column. |
| Uniqueness | [ACCEPTED\_VALUES](../sql-reference/functions/dmf_accepted_values) | Determine whether values in a column match a Boolean expression. |
|  | [DUPLICATE\_COUNT](../sql-reference/functions/dmf_duplicate_count) | Determine the number of duplicate values in a column, including NULL values. |
|  | [UNIQUE\_COUNT](../sql-reference/functions/dmf_unique_count) | Determine the number of unique, non-NULL values in a column. |
| Volume | [ROW\_COUNT](../sql-reference/functions/dmf_row_count) | Determine how many records are in the table or view. |

See moreShow less

Expand
