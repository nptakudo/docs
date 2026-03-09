---
title: "YAML specification for semantic views"
url: "https://docs.snowflake.com/en/user-guide/views-semantic/semantic-view-yaml-spec"
---

# YAML specification for semantic views

Semantic views are schema-level objects that define business concepts over your data, making it easier for users to query and analyze data using business terminology. You can use the YAML specification to create a semantic view in Cortex Analyst or use the [SYSTEM$CREATE\_SEMANTIC\_VIEW\_FROM\_YAML](../../sql-reference/stored-procedures/system_create_semantic_view_from_yaml) stored procedure to create a semantic view from a YAML specification.

## Overview

**Semantic views are the recommended approach** for defining business semantics in Snowflake. They are schema-level objects
that integrate with Snowflake’s privilege system, sharing mechanisms, and metadata catalog.

Note

Legacy semantic model YAML files (stored on stages) can still be used with Cortex Analyst for backward compatibility,
but we recommend using semantic views for new implementations.

The benefits of semantic views over legacy semantic models are:

* **Native Snowflake integration**: Schema-level objects with full RBAC, sharing, and catalog support
* **Advanced features**: Support for derived metrics and access modifiers (public/private)
* **Better governance**: Integrated with Snowflake’s privilege and sharing systems
* **Simplified management**: No need to manage YAML files on stages

## YAML format

Semantic views can take a [YAML](https://yaml.org/) specification to define their behavior, allowing for readable, plain-text definitions.

The general syntax of a semantic view YAML specification is:

CopyExpand

```
# Name and description of the semantic view.
name: <name>
description: <string>
comments: <string>

# Logical table-level concepts
# A semantic view can contain one or more logical tables.
tables:
  # A logical table on top of a base table.
  - name: <name>
    description: <string>
    # The fully qualified name of the base table.
    base_table:
      database: <database>
      schema: <schema>
      table: <base table name>

    # Dimension columns in the logical table.
    dimensions:
      - name: <name>
        synonyms: <array of strings>
        description: <string>
        expr: <SQL expression>
        data_type: <data type>
        unique: <boolean>
        cortex_search_service:
          service: <string>
          literal_column: <string>
          database: <string>
          schema: <string>
        is_enum: <boolean>
    - ...
    # Time dimension columns in the logical table.
    time_dimensions:
      - name: <name>
        synonyms: <array of strings>
        description: <string>
        expr: <SQL expression>
        data_type: <data type>
        unique: <boolean>

    # Fact columns in the logical table.
    facts:
      - name: <name>
        synonyms: <array of strings>
        description: <string>
        access_modifier: <public_access | private_access>  # Default is public_access.
        expr: <SQL expression>
        data_type: <data type>

    # Regular metrics scoped to the logical table.
    metrics:
      - name: <name>
        synonyms: <array of strings>
        description: <string>
        access_modifier: <public_access | private_access>  # Default is public_access.
        expr: <SQL expression>
        non_additive_dimensions:
        - table: <table name>
          dimension: <dimension name>
          sort_direction: <ascending | descending>
          null_order: <first | last>

    # Commonly used filters over the logical table.
    filters:
      - name: <name>
        synonyms: <array of strings>
        description: <string>
        expr: <SQL expression>

# View-level concepts
# Relationships between logical tables
relationships:
  - name: <string>
    left_table: <table>
    right_table: <table>
    relationship_columns:
      - left_column: <column>
        right_column: <column>
      - left_column: <column>
        right_column: <column>

# Derived metrics scoped to the semantic view.
# Derived metrics combine metrics from multiple tables.
metrics:
  - name: <name>
    synonyms: <array of strings>
    description: <string>
    access_modifier: <public_access | private_access>  # Default is public_access
    expr: <SQL expression>

# Additional context concepts
# Verified queries with example questions and queries that answer them
verified_queries:
  - name: <string>       # A descriptive name of the query.
    question: <string>   # The natural language question that this query answers.
    verified_at: <int>   # Optional: Time (in seconds since the UNIX epoch, January 1, 1970) when the query was verified.
    verified_by: <string> # Optional: Name of the person who verified the query.
    use_as_onboarding_question: <boolean>  # Optional: Marks this question as an onboarding question for the end user.
    sql: <string>        # The SQL query for answering the question
```

Show lessSee more

Scroll to top

Important

**Semantic views do not require** the `join_type` or `relationship_type` fields that were used in legacy semantic
models. The relationship type is automatically inferred from the data.

## Key concepts

### Tables

Logical tables represent business entities (such as customers, orders, or products) and map to physical database tables.
Each logical table can define:

* **Base table**: The fully qualified name of the physical table
* **Primary key**: Columns that uniquely identify rows
* **Synonyms**: Alternative names for the table
* **Description**: Business-friendly explanation of what the table represents

### Dimensions

Dimensions represent categorical attributes that provide context for analysis. They answer “who,” “what,” “where,” and
“when” questions. Dimensions can be:

* **Regular dimensions**: Text, numeric, or other categorical values
* **Time dimensions**: Date or timestamp columns with special time-based handling

#### Properties of dimensions

* `expr`: SQL expression to calculate the dimension value
* `synonyms`: Alternative terms users might use
* `unique`: Whether values are unique across rows
* `is_enum`: Whether the dimension has a fixed set of values
* `cortex_search_service`: Optional Cortex Search service for semantic search

#### Optional properties for physical dimensions

These fields are optional, but recommended for producing higher-quality results from a semantic view search.

`synonyms`
:   A list of other terms/phrases used to refer to this dimension. Must be unique across all synonyms in this semantic model.

`description`
:   A brief description of this dimension. Include information that provides useful context, such as data this dimension represents.

`unique`
:   A boolean value that indicates this dimension has unique values.

`sample_values`
:   Sample values of this column, if any. Add any value that is likely to be referenced in the user questions.

`is_enum`
:   A Boolean value. If `True`, the values in the `sample_values` field are taken to be the full list of possible values,
    and the model only chooses from those values when filtering on that column.

`cortex_search_service`
:   Specifies the Cortex Search Service to use for this dimension. It has the following fields:

    * `service`: The name of the Cortex Search Service.
    * `literal_column`: (optional) The column in the Cortex Search Service that contains the literal values.
    * `database`: (optional) The database where the Cortex Search Service is located. Defaults to `base_table`’s database.
    * `schema`: (optional) The schema where the Cortex Search Service is located. Defaults to `base_table`’s schema.

    `cortex_search_service` replaces the `cortex_search_service_name` field, which could only specify the name. `cortex_search_service_name` has been deprecated.

#### Optional properties for time dimensions

These fields are optional, but recommended for producing higher-quality results from a semantic view search.

`synonyms`
:   A list of other terms/phrases used to refer to this time dimension. Must be unique across all synonyms in this semantic model.

`description`
:   A brief description of this dimension. Include information that provides useful context, such as the time zone that this dimension uses as a reference point.

`unique`:
:   A boolean value that indicates this column has unique values.

`sample_values`:
:   Sample values of this column, if any. Add any values that are likely to be referenced in the user questions.

### Facts

Facts are row-level quantitative attributes that represent specific business events or transactions. Facts capture
“how much” or “how many” at the most granular level, such as individual sales amounts, quantities purchased, or costs.

Facts typically function as “helper” concepts within the semantic view to help construct dimensions and metrics.

The properties of facts are:

* `expr`: SQL expression to calculate the fact value
* `access_modifier`: Set to `private_access` to hide from queries (useful for intermediate calculations)
* `data_type`: The data type of the fact

### Metrics

Metrics are quantifiable measures of business performance calculated by aggregating facts or other columns using functions
like SUM, AVG, and COUNT.

Two types of metrics:

1. **Table-level metrics**: Scoped to a specific logical table, aggregating data within that table
2. **Derived metrics**: View-level metrics that combine metrics from multiple tables

#### Properties of metrics

* `expr`: SQL expression with aggregation function
* `access_modifier`: Set to `private_access` to hide from queries (useful for intermediate calculations)
* `synonyms`: Alternative terms for the metric

#### Optional properties of metrics

If you want to
[specify the dimensions that should be non-additive for the metric](sql.html#label-semantic-views-metrics-semi-additive), use the
following fields:

`non_additive_dimensions`
:   Specifies the dimensions that the metric should not be aggregated across.

    `table`
    :   Name of the logical table containing the dimension.

    `dimension`
    :   Name of the dimension.

    `sort_direction`
    :   [Sort order for the non-additive dimension](sql.html#label-semantic-views-metrics-semi-additive-order). You can specify one of
        the following values:

        * `ascending`: Sort the dimension values in ascending order.
        * `descending`: Sort the dimension values in descending order.

        Default: `ascending`

    `null_order`
    :   Specifies whether NULLs are [sorted before or after non-NULL values](sql.html#label-semantic-views-metrics-semi-additive-order).
        You can specify one of the following values:

        * `first`: NULLs are sorted before non-NULL values.
        * `last`: NULLs are sorted after non-NULL values.

        Default: Depends on the value in the `sort_direction` field (`ascending` or `descending`); see
        [the usage notes in the ORDER BY documentation](../../sql-reference/constructs/order-by.html#label-order-by-nulls).

    Note

    Because the rows are sorted by the non-additive dimensions, the order in which you specify the dimensions is important. This
    is similar to the order in which you specify columns in the [ORDER BY](../../sql-reference/constructs/order-by) clause.

The following example specifies that the `m_account_balance` metric cannot be aggregated by the `year_dim` and `month_dim`
dimensions:

CopyExpand

```
metrics:
  - name: m_account_balance
    ...
    non_additive_dimensions:
      - table: bank_accounts
        dimension: year_dim
        sort_direction: ascending
        null_order: last
      - table: bank_accounts
        dimension: month_dim
        sort_direction: descending
        null_order: first
```

Show lessSee more

Scroll to top

### Derived metrics

Derived metrics are view-level metrics not tied to a specific table. They can combine metrics from multiple tables
or perform calculations across the entire view.

Example of a derived metric:

CopyExpand

```
metrics:
  - name: total_profit_margin
    description: "Overall profit margin across all products"
    expr: (orders.total_revenue - orders.total_cost) / orders.total_revenue
    access_modifier: public_access
```

Show lessSee more

Scroll to top

### Relationships

Relationships define how logical tables join together. Each relationship specifies:

* `left_table`: The table containing the foreign key
* `right_table`: The table being referenced
* `relationship_columns`: Pairs of columns to join on, as `left_column` and `right_column`

The relationship type (one-to-one, many-to-one) is automatically inferred from the data and primary key definitions.

Note

Unlike legacy semantic models, semantic views do not require explicit `join_type` or `relationship_type`
specifications. These are determined automatically.

### Filters

Filters define commonly used filtering conditions that can be referenced by name. This helps ensure consistent
filtering logic across queries.

Example:

CopyExpand

```
filters:
  - name: active_customers
    description: "Customers who have made a purchase in the last 12 months"
    expr: "customer_last_purchase_date >= DATEADD(month, -12, CURRENT_DATE())"
```

Show lessSee more

Scroll to top

### Verified queries

Verified queries are example questions with their corresponding SQL queries. They help Cortex Analyst understand
how to answer similar questions and serve as documentation for users.

Properties:

* `question`: Natural language question
* `sql`: SQL query that answers the question
* `verified_by`: Optional person who verified the query is correct
* `verified_at`: Optional timestamp when verified
* `use_as_onboarding_question`: Optional flag to show this as a suggestion to users

## Access modifiers

Semantic views support access modifiers for facts and metrics, allowing you to control visibility:

* `public_access` (default): Visible and queryable by users
* `private_access`: Hidden from queries, used only for intermediate calculations

Example:

CopyExpand

```
facts:
  - name: internal_cost
    expr: unit_cost * quantity
    data_type: NUMBER
    access_modifier: private_access  # Not visible in queries

metrics:
  - name: total_revenue
    expr: SUM(sale_amount)
    access_modifier: public_access  # Visible in queries
```

Show lessSee more

Scroll to top

## Custom instructions for Cortex Analyst

You can use SQL commands to provide custom instructions in the semantic view definition.
These instructions guide how the queries are generated and how questions are categorized. These instructions are not
part of the YAML specification but are set using the [CREATE SEMANTIC VIEW](../../sql-reference/sql/create-semantic-view) command.

For more information, see [Providing custom instructions for Cortex Analyst](sql.html#label-semantic-views-custom-instructions).

## Example semantic view YAML

Here’s a complete example of a semantic view YAML specification:

CopyExpand

```
name: revenue_analysis
description: "Semantic view for analyzing revenue across products and customers"

tables:
  - name: customers
    description: "Customer information"
    base_table:
      database: sales_db
      schema: public
      table: customers
    dimensions:
      - name: customer_name
        synonyms: ["client name", "customer"]
        description: "Full name of the customer"
        expr: c_name
        data_type: VARCHAR
      - name: customer_segment
        synonyms: ["segment", "market segment"]
        description: "Customer market segment"
        expr: c_mktsegment
        data_type: VARCHAR
        is_enum: true

  - name: orders
    description: "Order information"
    base_table:
      database: sales_db
      schema: public
      table: orders
    dimensions:
      - name: order_date
        description: "Date when order was placed"
        expr: o_orderdate
        data_type: DATE
    time_dimensions:
      - name: order_year
        description: "Year when order was placed"
        expr: YEAR(o_orderdate)
        data_type: NUMBER
    facts:
      - name: order_total
        description: "Total order amount"
        expr: o_totalprice
        data_type: NUMBER
    metrics:
      - name: total_orders
        description: "Total number of orders"
        expr: COUNT(*)
      - name: average_order_value
        description: "Average order value"
        expr: AVG(o_totalprice)

relationships:
  - name: orders_to_customers
    left_table: orders
    right_table: customers
    relationship_columns:
      - left_column: o_custkey
        right_column: c_custkey

metrics:
  - name: revenue_per_customer
    description: "Average revenue per customer"
    expr: orders.total_revenue / customers.customer_count
    access_modifier: public_access

verified_queries:
  - name: top_customers_by_revenue
    question: "Who are the top 10 customers by revenue?"
    sql: |
      SELECT
        customer_name,
        SUM(order_total) as total_revenue
      FROM revenue_analysis
      GROUP BY customer_name
      ORDER BY total_revenue DESC
      LIMIT 10
    use_as_onboarding_question: true
```

Show lessSee more

Scroll to top

## Creating a semantic view from YAML

To create a semantic view from a YAML specification, use the
[SYSTEM$CREATE\_SEMANTIC\_VIEW\_FROM\_YAML](../../sql-reference/stored-procedures/system_create_semantic_view_from_yaml) stored procedure.

For more information, see [Creating a semantic view from a YAML specification](sql.html#label-semantic-views-create-from-yaml).

## Getting YAML from a semantic view

To export a semantic view to YAML format, use the
[SYSTEM$READ\_YAML\_FROM\_SEMANTIC\_VIEW](../../sql-reference/functions/system_read_yaml_from_semantic_view) function.

For more information, see [Getting the YAML specification for a semantic view](sql.html#label-semantic-views-get-yaml).

## Differences from legacy semantic models

If you’re migrating from legacy semantic model YAML files to semantic views, note these key differences:

| Feature | Legacy semantic models | Semantic views |
| --- | --- | --- |
| Storage | YAML files on stages | Schema-level objects in database |
| Privileges | Stage-based access control | Full Snowflake RBAC integration |
| Sharing | Manual file sharing | Native Snowflake sharing |
| Join types | Requires `join_type` and `relationship_type` | Automatically inferred |
| Derived metrics | Not supported | Fully supported |
| Access modifiers | Not supported | `public_access` / `private_access` |
| Custom instructions | In YAML file | Set via SQL commands |

See moreShow less

Expand

When converting from a legacy semantic model to a semantic view:

1. Remove `join_type` and `relationship_type` from relationships
2. Consider using derived metrics for view-level calculations
3. Add `access_modifier` to facts/metrics you want to make private
4. Move custom instructions to SQL CREATE SEMANTIC VIEW command
