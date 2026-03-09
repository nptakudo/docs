---
title: "Querying semantic views"
url: "https://docs.snowflake.com/en/user-guide/views-semantic/querying"
---

# Querying semantic views

To query a semantic view, you can use a standard [SELECT statement](../../sql-reference/constructs). Within this statement, you
can use one of the following approaches:

* Specify the SEMANTIC\_VIEW clause in the FROM clause. For example:

  CopyExpand

  ```
  SELECT * FROM SEMANTIC_VIEW(
      tpch_analysis
      DIMENSIONS customer.customer_market_segment
      METRICS orders.order_average_value
    )
    ORDER BY customer_market_segment;
  ```

  Show lessSee more

  Scroll to top

  For information, see [Specifying the SEMANTIC\_VIEW clause in the FROM clause](#label-semantic-views-querying-semantic-view-clause).
* Specify the name of the semantic view in the FROM clause. For example:

  CopyExpand

  ```
  SELECT customer_market_segment, AGG(order_average_value)
    FROM tpch_analysis
    GROUP BY customer_market_segment
    ORDER BY customer_market_segment;
  ```

  Show lessSee more

  Scroll to top

  For information, see [Specifying the name of the semantic view in the FROM clause](#label-semantic-views-querying-standard-sql).

## Privileges required to query a semantic view

If you are using a role that does not own the semantic view, you must be granted the SELECT privilege on that semantic view to
query that semantic view.

Note

To query a semantic view, you don’t need the SELECT privilege on the tables used in the semantic view. You only need the
SELECT privilege on the semantic view itself.

This behavior is consistent with [the privileges required to query standard views](../views-introduction.html#label-views-privileges).

For information about granting privileges on semantic views, see [Granting privileges on semantic views](sql.html#label-semantic-views-privileges).

## Specifying the SEMANTIC\_VIEW clause in the FROM clause

To query a semantic view, you can specify the [SEMANTIC\_VIEW clause](../../sql-reference/constructs/semantic_view) in the FROM
clause.

The following example selects the `customer_market_segment` dimension and the `order_average_value` metric from the
`tpch_analysis` semantic view, [which you defined earlier](example):

CopyExpand

```
SELECT * FROM SEMANTIC_VIEW(
    tpch_analysis
    DIMENSIONS customer.customer_market_segment
    METRICS orders.order_average_value
  )
  ORDER BY customer_market_segment;
```

Show lessSee more

Scroll to top

Expand

```
+-------------------------+---------------------+
| CUSTOMER_MARKET_SEGMENT | ORDER_AVERAGE_VALUE |
+-------------------------+---------------------+
| AUTOMOBILE              |     142570.25947219 |
| FURNITURE               |     142563.63314267 |
| MACHINERY               |     142655.91550608 |
| HOUSEHOLD               |     141659.94753445 |
| BUILDING                |     142425.37987558 |
+-------------------------+---------------------+
```

Show lessSee more

Scroll to top

Note that you can define an alias for a dimension or metric by specifying the alias after the dimension or metric name. You can
also specify the optional keyword AS before the alias. The following example runs the same query but uses the aliases `segment`
and `average` for the dimension and metric returned in the results.

CopyExpand

```
SELECT * FROM SEMANTIC_VIEW(
    tpch_analysis
    DIMENSIONS customer.customer_market_segment AS segment
    METRICS orders.order_average_value average
  )
  ORDER BY segment;
```

Show lessSee more

Scroll to top

Expand

```
+------------+-----------------+
| SEGMENT    |         AVERAGE |
|------------+-----------------|
| AUTOMOBILE | 142570.25947219 |
| BUILDING   | 142425.37987558 |
| FURNITURE  | 142563.63314267 |
| HOUSEHOLD  | 141659.94753445 |
| MACHINERY  | 142655.91550608 |
+------------+-----------------+
```

Show lessSee more

Scroll to top

The following example selects the `customer_name` dimension and the `c_customer_order_count` fact from the
`tpch_analysis` semantic view:

CopyExpand

```
SELECT * FROM SEMANTIC_VIEW(
    tpch_analysis
    DIMENSIONS customer.customer_name
    FACTS customer.c_customer_order_count
  )
  ORDER BY customer_name
  LIMIT 5;
```

Show lessSee more

Scroll to top

Expand

```
+--------------------+------------------------+
| CUSTOMER_NAME      | C_CUSTOMER_ORDER_COUNT |
|--------------------+------------------------|
| Customer#000000001 |                      9 |
| Customer#000000002 |                     11 |
| Customer#000000003 |                      0 |
| Customer#000000004 |                     20 |
| Customer#000000005 |                     10 |
+--------------------+------------------------+
```

Show lessSee more

Scroll to top

### Guidelines for specifying the SEMANTIC\_VIEW clause

When specifying the SEMANTIC\_VIEW clause, follow these guidelines:

* In the SEMANTIC\_VIEW clause, you must specify at least one of the following clauses:

  + METRICS
  + DIMENSIONS
  + FACTS

  You cannot omit all of these clauses from the SEMANTIC\_VIEW clause.
* When specifying a combination of these clauses, note the following:

  + You cannot specify FACTS and METRICS in the same SEMANTIC\_VIEW clause.
  + Although you can specify both FACTS and DIMENSIONS in a query, you should do so only if the dimensions can uniquely determine
    the facts.

    The query groups the results by dimensions. if the facts do not depend on the dimensions, the results can be
    non-deterministic.
  + If you specify both FACTS and DIMENSIONS, all facts and dimensions used in the query (including those specified in the WHERE
    clause) must be defined in the same logical table.
  + If you specify a dimension and a metric, the logical table for the dimension must be related to the logical table for the
    metric.

    In addition, the logical table for the dimension must have an equal or lower level of granularity than the logical table for
    the metric.

    To determine which dimensions meet this criteria, you can run the
    [SHOW SEMANTIC DIMENSIONS FOR METRIC](../../sql-reference/sql/show-semantic-dimensions-for-metric) command.

    For details, see [Choosing the dimensions that you can return for a given metric](#label-semantic-views-query-dimensions-metrics).
* In the DIMENSIONS clause, you can specify an expression that refers to a fact. Similarly, in the FACTS clause, you can specify
  an expression that refers to a dimension. For example:

  CopyExpand

  ```
  -- Dimension expression that refers to a fact
  DIMENSIONS my_table.my_fact

  -- Fact expression that refers to a dimension
  FACTS my_table.my_dimension
  ```

  Show lessSee more

  Scroll to top

  One of the main differences between using DIMENSIONS and FACTS is that the query groups the results by the dimensions and
  expressions specified in the DIMENSIONS clause.
* In the METRICS clause, you can specify an expression that includes:

  + A scalar expression referring to metrics.
  + An aggregation of dimensions or facts.
* Specify the METRICS, DIMENSIONS, and FACTS clauses in the order in which you want them to appear in the results.

  If you want the dimensions to appear first in the results, specify DIMENSIONS before METRICS. Otherwise, specify METRICS first.

  For example, suppose that you specify the METRICS clause first:

  CopyExpand

  ```
  SELECT * FROM SEMANTIC_VIEW(
      tpch_analysis
      METRICS customer.customer_order_count
      DIMENSIONS customer.customer_name
    )
    ORDER BY customer_name
    LIMIT 5;
  ```

  Show lessSee more

  Scroll to top

  In the output, the first column is the metric column (`customer_order_count`) and the second column is the dimension column
  (`customer_name`):

  Expand

  ```
  +----------------------+--------------------+
  | CUSTOMER_ORDER_COUNT | CUSTOMER_NAME      |
  |----------------------+--------------------|
  |                    6 | Customer#000000001 |
  |                    7 | Customer#000000002 |
  |                    0 | Customer#000000003 |
  |                   20 | Customer#000000004 |
  |                    4 | Customer#000000005 |
  +----------------------+--------------------+
  ```

  Show lessSee more

  Scroll to top

  If you instead specify the DIMENSIONS clause first:

  CopyExpand

  ```
  SELECT * FROM SEMANTIC_VIEW(
      tpch_analysis
      DIMENSIONS customer.customer_name
      METRICS customer.customer_order_count
    )
    ORDER BY customer_name
    LIMIT 5;
  ```

  Show lessSee more

  Scroll to top

  In the output, the first column is the dimension column (`customer_name`) and the second column is the metric column
  (`customer_order_count`):

  Expand

  ```
  +--------------------+----------------------+
  | CUSTOMER_NAME      | CUSTOMER_ORDER_COUNT |
  |--------------------+----------------------|
  | Customer#000000001 |                    6 |
  | Customer#000000002 |                    7 |
  | Customer#000000003 |                    0 |
  | Customer#000000004 |                   20 |
  | Customer#000000005 |                    4 |
  +--------------------+----------------------+
  ```

  Show lessSee more

  Scroll to top
* You can use the relation defined by a SEMANTIC\_VIEW clause in other SQL constructs, including
  [JOIN](../../sql-reference/constructs/join), [PIVOT](../../sql-reference/constructs/pivot), [UNPIVOT](../../sql-reference/constructs/unpivot),
  [GROUP BY](../../sql-reference/constructs/group-by), and [common table expressions (CTEs)](../queries-cte).
* The output column headers use the unqualified names of the metrics and dimensions.

  If you have multiple metrics and dimensions with the same names, use a table alias to assign different names to the column
  headers. See [Handling duplicate column names in the output](#label-semantic-views-duplicate-columns).

To return all metrics or dimensions in a given logical table, use an asterisk as a wildcard, qualified by the name of the logical
table. For example, to return all metrics and dimensions defined in the `customer` logical table:

CopyExpand

```
SELECT * FROM SEMANTIC_VIEW(
  tpch_analysis
  DIMENSIONS customer.*
  METRICS customer.*
);
```

Show lessSee more

Scroll to top

Expand

```
+-----------------------+-------------------------+--------------------+----------------------+----------------------+----------------+----------------------+
| CUSTOMER_COUNTRY_CODE | CUSTOMER_MARKET_SEGMENT | CUSTOMER_NAME      | CUSTOMER_NATION_NAME | CUSTOMER_REGION_NAME | CUSTOMER_COUNT | CUSTOMER_ORDER_COUNT |
|-----------------------+-------------------------+--------------------+----------------------+----------------------+----------------+----------------------|
| 18                    | BUILDING                | Customer#000034857 | INDIA                | ASIA                 |              1 |                    0 |
| 14                    | AUTOMOBILE              | Customer#000145116 | EGYPT                | MIDDLE EAST          |              1 |                    0 |
...
```

Show lessSee more

Scroll to top

### Examples of specifying the SEMANTIC\_VIEW clause

The following examples use the `tpch_analysis` view defined in [Example of using SQL to create a semantic view](example):

#### Retrieving a metric

The following statement retrieves the total count of customers by querying a metric:

CopyExpand

```
SELECT * FROM SEMANTIC_VIEW(
    tpch_analysis
    METRICS customer.customer_count
  );
```

Show lessSee more

Scroll to top

Expand

```
+----------------+
| CUSTOMER_COUNT |
+----------------+
|          15000 |
+----------------+
```

Show lessSee more

Scroll to top

#### Grouping metric data by a dimension

The following statement groups metric data (`order_average_value`) by a dimension (`customer_market_segment`):

CopyExpand

```
SELECT * FROM SEMANTIC_VIEW(
    tpch_analysis
    DIMENSIONS customer.customer_market_segment
    METRICS orders.order_average_value
  );
```

Show lessSee more

Scroll to top

Expand

```
+-------------------------+---------------------+
| CUSTOMER_MARKET_SEGMENT | ORDER_AVERAGE_VALUE |
+-------------------------+---------------------+
| AUTOMOBILE              |     142570.25947219 |
| FURNITURE               |     142563.63314267 |
| MACHINERY               |     142655.91550608 |
| HOUSEHOLD               |     141659.94753445 |
| BUILDING                |     142425.37987558 |
+-------------------------+---------------------+
```

Show lessSee more

Scroll to top

#### Using the SEMANTIC\_VIEW subclause with other constructs

The following example demonstrates how you can use dimensions and metrics in the SEMANTIC\_VIEW subclause with other SQL
constructs to filter, sort, and limit results:

CopyExpand

```
SELECT * FROM SEMANTIC_VIEW(
    tpch_analysis
    DIMENSIONS customer.customer_name
    METRICS orders.average_line_items_per_order,
            orders.order_average_value
  )
  WHERE average_line_items_per_order > 4
  ORDER BY average_line_items_per_order DESC
  LIMIT 5;
```

Show lessSee more

Scroll to top

Expand

```
+--------------------+------------------------------+---------------------+
| CUSTOMER_NAME      | AVERAGE_LINE_ITEMS_PER_ORDER | ORDER_AVERAGE_VALUE |
+--------------------+------------------------------+---------------------+
| Customer#000045678 |                         6.87 |           175432.21 |
| Customer#000067890 |                         6.42 |           182376.58 |
| Customer#000012345 |                         5.93 |           169847.42 |
| Customer#000034567 |                         5.76 |           178952.36 |
| Customer#000056789 |                         5.64 |           171248.75 |
+--------------------+------------------------------+---------------------+
```

Show lessSee more

Scroll to top

#### Specifying scalar expressions that use dimensions

The following example uses a scalar expression that refers to a dimension in the DIMENSIONS clause:

CopyExpand

```
SELECT * FROM SEMANTIC_VIEW(
    tpch_analysis
    DIMENSIONS DATE_PART('year', orders.order_date) AS year
  )
  ORDER BY year;
```

Show lessSee more

Scroll to top

Expand

```
+------+
| YEAR |
|------|
| 1992 |
| 1993 |
| 1994 |
| 1995 |
| 1996 |
| 1997 |
| 1998 |
+------+
```

Show lessSee more

Scroll to top

#### Specifying the WHERE clause

The following example specifies a WHERE clause that refers to a dimension in the DIMENSIONS clause:

CopyExpand

```
SELECT * FROM SEMANTIC_VIEW(
    tpch_analysis
    DIMENSIONS orders.order_date
    METRICS orders.average_line_items_per_order,
            orders.order_average_value
    WHERE orders.order_date > '1995-01-01'
  )
  ORDER BY order_date ASC
  LIMIT 5;
```

Show lessSee more

Scroll to top

Expand

```
+------------+------------------------------+---------------------+
| ORDER_DATE | AVERAGE_LINE_ITEMS_PER_ORDER | ORDER_AVERAGE_VALUE |
|------------+------------------------------+---------------------|
| 1995-01-02 |                     3.884547 |     151237.54900533 |
| 1995-01-03 |                     3.894819 |     145751.84384615 |
| 1995-01-04 |                     3.838863 |     145331.39167457 |
| 1995-01-05 |                     4.040689 |     150723.67353678 |
| 1995-01-06 |                     3.990755 |     152786.54109399 |
+------------+------------------------------+---------------------+
```

Show lessSee more

Scroll to top

#### Specifying facts in the WHERE clause

The following example uses the `region.r_name` fact in a condition in the WHERE clause:

CopyExpand

```
SELECT * FROM SEMANTIC_VIEW(
    tpch_analysis
    FACTS customer.c_customer_order_count
    WHERE orders.order_date < '2021-01-01' AND region.r_name = 'AMERICA'
  );
```

Show lessSee more

Scroll to top

## Specifying the name of the semantic view in the FROM clause

You can specify the name of the semantic view in the FROM clause of a SELECT statement, as you would when querying a standard SQL
view:

CopyExpand

```
SELECT [ DISTINCT ]
    {
      [<qualifiers>.]<dimension_or_fact>                          |
      <scalar_expression_over_dimension_or_fact>                  |
      AGG( [<qualifiers>.]<metric> )                              |
      <aggregate_function>( [<qualifiers>.]<dimension_for_fact> )
    }
    [ , ... ]
  FROM <semantic_view> [ AS <alias> ]
  [ WHERE <expr_using_dimensions_or_facts> ]
  [ GROUP BY <expr_using_dimensions_or_facts> [ , ... ] ]
  [ HAVING <expr_using_metrics> ]
  [ ORDER BY ... ]
  [ LIMIT ... ]
```

Show lessSee more

Scroll to top

Internally, this statement is rewritten as a SELECT statement that uses the
[SEMANTIC\_VIEW clause](#label-semantic-views-querying-semantic-view-clause):

* The expressions that you specify in the GROUP BY clause are rewritten into the DIMENSIONS clause in the SEMANTIC\_VIEW clause.

  In the SELECT statement, if you use an expression that is not in the GROUP BY clause (for example, a dimension
  expression in the SELECT list), the rewrite uses that expression in the FACTS clause in the SEMANTIC\_VIEW clause.
* When you refer to a metric that is defined in a semantic view, you must pass the metric to the AGG function.
* You can select ad-hoc metrics by passing a dimension or fact to any
  [aggregate function](../../sql-reference/functions-aggregation).
* Any other calculated values that don’t fall into the first two categories are considered to be fact references.

The next sections explain these requirements in more detail:

### Requirements for dimensions and metrics in a SELECT statement

In the SELECT statement, you can only refer to dimensions and metrics that have distinct names and that are not distinguished by
their logical table name. For example, suppose that a semantic view has two dimensions that have the unqualified name `name`:

CopyExpand

```
DIMENSIONS (
  nation.name AS nation.n_name,
  region.name AS region.r_name
);
```

Show lessSee more

Scroll to top

In the SELECT statement, when you specify the qualified name of a dimension or metric, the qualifier is interpreted as the name
of the semantic view, not the name of a logical table:

CopyExpand

```
SELECT nation.name, region.name
  FROM duplicate_names
  GROUP BY nation.name, region.name;
```

Show lessSee more

Scroll to top

Expand

```
000904 (42000): SQL compilation error: error line 1 at position 7
invalid identifier 'NATION.NAME'
```

Show lessSee more

Scroll to top

### Selecting metrics

If you want to select a metric that is defined in a semantic view, you must pass the metric to the
[AGG](../../sql-reference/functions/agg) function, which is a special aggregate function for metrics in semantic views.

For example:

CopyExpand

```
SELECT AGG(order_average_value) FROM tpch_analysis;
```

Show lessSee more

Scroll to top

Note

The AGG function has no effect on the metric because the function evaluates one value of the metric.

In the SELECT list, you can specify an expression that uses a metric. For example:

CopyExpand

```
SELECT AGG(order_average_value) * 10 FROM tpch_analysis;
```

Show lessSee more

Scroll to top

You can also define and select ad-hoc metrics by passing a dimension or fact to any
[aggregate function](../../sql-reference/functions-aggregation). For example:

CopyExpand

```
SELECT COUNT(customer_market_segment) FROM tpch_analysis;
```

Show lessSee more

Scroll to top

### Selecting dimensions

If the SELECT list includes dimensions, you must specify those dimensions in the GROUP BY clause. For example:

CopyExpand

```
SELECT customer_market_segment, customer_nation_name, AGG(order_average_value)
  FROM tpch_analysis
  GROUP BY customer_market_segment, customer_nation_name;
```

Show lessSee more

Scroll to top

In the SELECT list and in the GROUP BY clause, you can specify a dimension or a scalar expression that uses a dimension or a fact.
For example:

CopyExpand

```
SELECT LOWER(customer_nation_name), AGG(order_average_value)
  FROM tpch_analysis
  GROUP BY customer_nation_name;
```

Show lessSee more

Scroll to top

### Specifying the WHERE clause

In the WHERE clause, you can only use conditional expressions that refer to dimensions or facts. For example:

CopyExpand

```
SELECT customer_market_segment, AGG(order_average_value)
  FROM tpch_analysis
  WHERE customer_market_segment = 'BUILDING'
  GROUP BY customer_market_segment;
```

Show lessSee more

Scroll to top

The dimensions must be reachable by every metric used in the query.

### Specifying the HAVING clause

In the HAVING clause, you can only specify metrics, and you must pass them to one of the aggregate functions listed in
[Selecting metrics](#label-semantic-views-query-standard-metrics). For example:

CopyExpand

```
SELECT customer_market_segment, AGG(order_average_value)
  FROM tpch_analysis
  GROUP BY customer_market_segment
  HAVING AGG(order_average_value) > 142500;
```

Show lessSee more

Scroll to top

### Limitations with specifying the semantic view name in the FROM clause

You cannot specify the following in the SELECT statement:

* Extensions of the FROM clause, including:

  + PIVOT
  + UNPIVOT
  + MATCH\_RECOGNIZE
  + LATERAL
* Joins
* Window function calls
* QUALIFY
* Subqueries

## Choosing the dimensions that you can return for a given metric

When you specify a dimension and a metric to return, the base table for the dimension must be related to the base table for the
metric. In addition, the base table for the dimension must have an equal or lower level of granularity than the base table for
the metric.

For example, suppose that you query the `tpch_analysis` semantic view that you created in [Example of using SQL to create a semantic view](example), and you want to return
the `orders.order_date` dimension and the `customer.customer_order_count` metric:

CopyExpand

```
SELECT * FROM SEMANTIC_VIEW (
  tpch_analysis
  DIMENSIONS orders.order_date
  METRICS customer.customer_order_count
);
```

Show lessSee more

Scroll to top

This query fails because the `orders` table for the `order_date` dimension has a higher level of granularity than the
`customer` table for the `customer_order_count` metric:

Expand

```
010234 (42601): SQL compilation error:
Invalid dimension specified: The dimension entity 'ORDERS' must be related to and
  have an equal or lower level of granularity compared to the base metric or dimension entity 'CUSTOMER'.
```

Show lessSee more

Scroll to top

To list the dimensions that you can return with a specific metric, run the
[SHOW SEMANTIC DIMENSIONS FOR METRIC](../../sql-reference/sql/show-semantic-dimensions-for-metric) command. For example:

CopyExpand

```
SHOW SEMANTIC DIMENSIONS IN tpch_analysis FOR METRIC customer_order_count;
```

Show lessSee more

Scroll to top

Expand

```
+------------+-------------------------+-------------+----------+----------+---------+
| table_name | name                    | data_type   | required | synonyms | comment |
|------------+-------------------------+-------------+----------+----------+---------|
| CUSTOMER   | CUSTOMER_COUNTRY_CODE   | VARCHAR(15) | false    | NULL     | NULL    |
| CUSTOMER   | CUSTOMER_MARKET_SEGMENT | VARCHAR(10) | false    | NULL     | NULL    |
| CUSTOMER   | CUSTOMER_NAME           | VARCHAR(25) | false    | NULL     | NULL    |
| CUSTOMER   | CUSTOMER_NATION_NAME    | VARCHAR(25) | false    | NULL     | NULL    |
| CUSTOMER   | CUSTOMER_REGION_NAME    | VARCHAR(25) | false    | NULL     | NULL    |
| NATION     | NATION_NAME             | VARCHAR(25) | false    | NULL     | NULL    |
+------------+-------------------------+-------------+----------+----------+---------+
```

Show lessSee more

Scroll to top

## Handling duplicate column names in the output

The output columns use the unqualified names of the metrics and dimensions. If you have multiple metrics and dimensions
with the same names, multiple columns will use the same name.

To work around this, use a table alias to assign different names to the columns.

For example, suppose that you define the following semantic view, which defines the dimensions `nation.name` and
`region.name`:

CopyExpand

```
CREATE OR REPLACE SEMANTIC VIEW duplicate_names

  TABLES (
    nation AS SNOWFLAKE_SAMPLE_DATA.TPCH_SF1.NATION PRIMARY KEY (n_nationkey),
    region AS SNOWFLAKE_SAMPLE_DATA.TPCH_SF1.REGION PRIMARY KEY (r_regionkey)
  )

  RELATIONSHIPS (
    nation (n_regionkey) REFERENCES region
  )

  DIMENSIONS (
    nation.name AS nation.n_name,
    region.name AS region.r_name
  );
```

Show lessSee more

Scroll to top

If you query this view and select these two dimensions, the output includes two columns named `name` without any qualifiers:

CopyExpand

```
SELECT * FROM SEMANTIC_VIEW(
    duplicate_names
    DIMENSIONS nation.name, region.name
  );
```

Show lessSee more

Scroll to top

Expand

```
+----------------+-------------+
| NAME           | NAME        |
+----------------+-------------+
| BRAZIL         | AMERICA     |
| MOROCCO        | AFRICA      |
| UNITED KINGDOM | EUROPE      |
| IRAN           | MIDDLE EAST |
| FRANCE         | EUROPE      |
| ...            | ...         |
+----------------+-------------+
```

Show lessSee more

Scroll to top

To disambiguate the columns, use a table alias to assign different column names (for example, `nation_name` and
`region_name`):

CopyExpand

```
SELECT * FROM SEMANTIC_VIEW(
    duplicate_names
    DIMENSIONS nation.name, region.name
  ) AS table_alias(nation_name, region_name);
```

Show lessSee more

Scroll to top

Expand

```
+----------------+-------------+
| NATION_NAME    | REGION_NAME |
+----------------+-------------+
| BRAZIL         | AMERICA     |
| MOROCCO        | AFRICA      |
| UNITED KINGDOM | EUROPE      |
| IRAN           | MIDDLE EAST |
| FRANCE         | EUROPE      |
| ...            | ...         |
+----------------+-------------+
```

Show lessSee more

Scroll to top

## Defining and querying window function metrics

You can define metrics that call [window functions](../../sql-reference/functions-window-syntax) and pass in aggregated values.
These metrics are called *window function metrics*.

The following examples illustrate the difference between a window function metric and a metric that passes a row-level
expression to a window function:

* The following metric is a window function metric:

  CopyExpand

  ```
  METRICS (
    table_1.metric_1 AS SUM(table_1.metric_3) OVER( ... )
  )
  ```

  Show lessSee more

  Scroll to top

  In this example, the SUM window function takes another metric (`table_1.metric_3`) as an argument.

  The following metric is also a window function metric:

  CopyExpand

  ```
  METRICS (
    table_1.metric_2 AS SUM(
      SUM(table_1.column_1)
    ) OVER( ... )
  )
  ```

  Show lessSee more

  Scroll to top

  In this example, the SUM window function takes a valid metric expression (`SUM(table_1.column_1)`) as an argument.
* The following metric is not a window function metric:

  CopyExpand

  ```
  METRICS (
    table_1.metric_1 AS SUM(
      SUM(table_1.column_1) OVER( ... )
    )
  )
  ```

  Show lessSee more

  Scroll to top

  In this example, the SUM window function takes a column (`table_1.column_1`) as an argument, and the result of that window
  function call is passed to a separate SUM aggregate function call.

The following sections explain how to define and query window function metrics:

### Defining window function metrics

When specifying a window function call, use [this syntax](../../sql-reference/sql/create-semantic-view.html#label-create-semantic-view-window-function-syntax), which is
described in [Parameters for window function metrics](../../sql-reference/sql/create-semantic-view.html#label-create-semantic-view-window-function).

The following example creates a semantic view that includes the definitions of several window function metrics. The example uses
tables from the [TPC-DS](../sample-data-tpcds) sample database. For information on accessing this database, see
[Add the TPC-DS data set to your account](../sample-data-tpcds.html#label-sample-data-tpc-ds-adding).

CopyExpand

```
CREATE OR REPLACE SEMANTIC VIEW sv_window_function_example
  TABLES (
    store_sales AS SNOWFLAKE_SAMPLE_DATA.TPCDS_SF10TCL.store_sales,
    date AS SNOWFLAKE_SAMPLE_DATA.TPCDS_SF10TCL.date_dim PRIMARY KEY (d_date_sk)
  )
  RELATIONSHIPS (
    sales_to_date AS store_sales(ss_sold_date_sk) REFERENCES date(d_date_sk)
  )
  DIMENSIONS (
    date.date AS d_date,
    date.d_date_sk AS d_date_sk,
    date.year AS d_year
  )
  METRICS (
    store_sales.total_sales_quantity AS SUM(ss_quantity)
      WITH SYNONYMS = ('Total sales quantity'),

    store_sales.avg_7_days_sales_quantity as AVG(total_sales_quantity)
      OVER (PARTITION BY EXCLUDING date.date, date.year ORDER BY date.date
        RANGE BETWEEN INTERVAL '6 days' PRECEDING AND CURRENT ROW)
      WITH SYNONYMS = ('Running 7-day average of total sales quantity'),

    store_sales.total_sales_quantity_30_days_ago AS LAG(total_sales_quantity, 30)
      OVER (PARTITION BY EXCLUDING date.date, date.year ORDER BY date.date)
      WITH SYNONYMS = ('Sales quantity 30 days ago'),

    store_sales.avg_7_days_sales_quantity_30_days_ago AS AVG(total_sales_quantity)
      OVER (PARTITION BY EXCLUDING date.date, date.year ORDER BY date.date
        RANGE BETWEEN INTERVAL '36 days' PRECEDING AND INTERVAL '30 days' PRECEDING)
      WITH SYNONYMS = ('Running 7-day average of total sales quantity 30 days ago')

  );
```

Show lessSee more

Scroll to top

You can also use other metrics from the same logical table in the metric definition. For example:

CopyExpand

```
METRICS (
  orders.m3 AS SUM(m2) OVER (PARTITION BY m1 ORDER BY m2),
  orders.m4 AS ((SUM(m2) OVER (..)) / m1) + 1
)
```

Show lessSee more

Scroll to top

Note

You can’t use window function metrics in row-level calculations (facts and dimensions) or in the definitions of other metrics.

### Querying window function metrics

When you query a semantic view and the query returns a window function metric, you must also return the dimensions specified in
PARTITION BY `dimension`, PARTITION BY EXCLUDING `dimension`, and ORDER BY `dimension` in the
[CREATE SEMANTIC VIEW](../../sql-reference/sql/create-semantic-view) statement for the semantic view.

For example, suppose that you specify the `date.date` and `date.year` dimensions in the PARTITION BY EXCLUDING and ORDER BY
clauses in the definition of the `store_sales.avg_7_days_sales_quantity` metric:

CopyExpand

```
CREATE OR REPLACE SEMANTIC VIEW sv_window_function_example
  ...
  DIMENSIONS (
    ...
    date.date AS d_date,
    ...
    date.year AS d_year
    ...
  )
  METRICS (
    ...
    store_sales.avg_7_days_sales_quantity as AVG(total_sales_quantity)
      OVER (PARTITION BY EXCLUDING date.date, date.year ORDER BY date.date
        RANGE BETWEEN INTERVAL '6 days' PRECEDING AND CURRENT ROW)
      WITH SYNONYMS = ('Running 7-day average of total sales quantity'),
    ...
  );
```

Show lessSee more

Scroll to top

If you return the `store_sales.avg_7_days_sales_quantity` metric in a query, you must also return the `date.date` and
`date.year` dimensions:

CopyExpand

```
SELECT * FROM SEMANTIC_VIEW (
  sv_window_function_example
  DIMENSIONS date.date, date.year
  METRICS store_sales.avg_7_days_sales_quantity
);
```

Show lessSee more

Scroll to top

If you omit the `date.date` and `date.year` dimensions, an error occurs.

Expand

```
010260 (42601): SQL compilation error:
Invalid semantic view query: Dimension 'DATE.DATE' used in a
   window function metric must be requested in the query.
```

Show lessSee more

Scroll to top

To determine which dimensions you must specify in the query, execute the
[SHOW SEMANTIC DIMENSIONS FOR METRIC](../../sql-reference/sql/show-semantic-dimensions-for-metric) command. For example, to determine the dimensions that you must
specify when retrieving the `store_sales.avg_7_days_sales_quantity` metric, run this command:

CopyExpand

```
SHOW SEMANTIC DIMENSIONS IN sv_window_function_example FOR METRIC avg_7_days_sales_quantity;
```

Show lessSee more

Scroll to top

In the output of the command, the `required` column contains `true` for the dimensions that you must specify in the query.

Expand

```
+------------+-----------+--------------+----------+----------+---------+
| table_name | name      | data_type    | required | synonyms | comment |
|------------+-----------+--------------+----------+----------+---------|
| DATE       | DATE      | DATE         | true     | NULL     | NULL    |
| DATE       | D_DATE_SK | NUMBER(38,0) | false    | NULL     | NULL    |
| DATE       | YEAR      | NUMBER(38,0) | true     | NULL     | NULL    |
+------------+-----------+--------------+----------+----------+---------+
```

Show lessSee more

Scroll to top

The following additional examples query the window function metrics defined in
[Defining window function metrics](#label-semantic-views-querying-window-defining). Note that the DIMENSIONS clause includes the dimensions specified in the
PARTITION BY EXCLUDING and ORDER BY clauses of the metric definitions.

The following example returns the sales quantity 30 days ago:

CopyExpand

```
SELECT * FROM SEMANTIC_VIEW (
  sv_window_function_example
  DIMENSIONS date.date, date.year
  METRICS store_sales.total_sales_quantity_30_days_ago
);
```

Show lessSee more

Scroll to top

The following example returns the running 7-day average of the total sales quantity 30 days ago:

CopyExpand

```
SELECT * FROM SEMANTIC_VIEW (
  sv_window_function_example
  DIMENSIONS date.date, date.year
  METRICS store_sales.avg_7_days_sales_quantity_30_days_ago
);
```

Show lessSee more

Scroll to top
