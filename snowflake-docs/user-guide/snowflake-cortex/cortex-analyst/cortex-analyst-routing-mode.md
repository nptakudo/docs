---
title: "Routing Mode for Cortex Analyst"
url: "https://docs.snowflake.com/en/user-guide/snowflake-cortex/cortex-analyst/cortex-analyst-routing-mode"
---

# Routing Mode for Cortex Analyst

[![Snowflake logo in black (no text)](../../../_images/logo-snowflake-black.png)](../../../_images/logo-snowflake-black.png) [Preview Feature](../../../release-notes/preview-features) — Open

Available to all accounts.

Routing Mode is a query-generation strategy that prioritizes semantic SQL and falls back to standard SQL only when needed. It acts as a simpler version of SQL, with guardrails coming from your semantic views. Routing mode uses your semantic views to drive higher accuracy and consistency. As a result, metrics, joins, and filters follow governed definitions from the semantic view.

Cortex Analyst automatically uses Routing Mode when generating based on a semantic view. There is no change to your workflow, except higher text-to-sql quality.

Note

Routing Mode does not change permissions. Semantic views are Snowflake objects with standard privileges; access is enforced the same way as tables or views.

## Benefits of Routing Mode

Routing Mode offers the following benefits:

* **Consistent metrics:** Queries use definitions from semantic views, not SQL.
* **Safer defaults:** Dimensions, metrics, and joins come from governed metadata.
* **LLM-friendly:** Shorter SQL is easier for an LLM to produce correctly.

Routing Mode could be beneficial in the following situations:

* You have one or more semantic views that define core business entities and metrics.
* You want consistent answers for common questions, with flexibility for edge cases.

For example, consider the following scenarios and how Routing Mode handles them:

* **Ask for a governed metric by a business dimension**

  + **User intent:** “Average order value by customer segment.”
  + **Routing behavior:** Tries semantic SQL first, so joins and metric calculations come from the view.

    > CopyExpand
    >
    > ```
    > SELECT *
    > FROM SEMANTIC_VIEW(
    >   tpch_analysis
    >   DIMENSIONS customer.customer_market_segment
    >   METRICS orders.order_average_value
    > )
    > ORDER BY customer_market_segment;
    > ```
    >
    > Show lessSee more
    >
    > Scroll to top
  + **Benefit:** No manual joins or metric formulas. Results align with your BI definitions.
* **Multiple governed metrics with one dimension**

  + **User intent:** “Show total revenue and order count by year.”

    > CopyExpand
    >
    > ```
    > SELECT *
    > FROM SEMANTIC_VIEW(
    >   tpch_analysis
    >   DIMENSIONS orders.order_year
    >   METRICS orders.total_revenue, orders.order_count
    > )
    > ORDER BY order_year;
    > ```
    >
    > Show lessSee more
    >
    > Scroll to top
  + **Benefit:** Both metrics use the same definitions and filters as in the semantic view.
* **Fallback for uncovered asks**

  + **User intent:** “Show a raw column or transformation not modeled in the view.”
  + **Routing behavior:** If the semantic view cannot satisfy the request, Cortex Analyst automatically routes to standard SQL on base tables.
  + **Benefit:** Flexibility without blocking the user.

## How it works

The following procedure outlines the steps that Cortex Analyst takes when using Routing Mode.

1. Cortex Analyst uses Routing mode in the playground, API, and all product surfaces.
2. Cortex Analyst tries to produce semantic SQL.

   > CopyExpand
   >
   > ```
   > SELECT … FROM SEMANTIC_VIEW(...).
   > ```
   >
   > Show lessSee more
   >
   > Scroll to top
3. If Cortex Analyst is unable to produce a valid semantic SQL query that answers the question within the timeout, Cortex Analyst routes to standard SQL on physical tables.

Note

Routing Mode only results in semantic SQL for about 10% of queries, in aggregate. This number varies depending on the level of coverage the metrics defined in the semantic view have.

## Considerations

* If the semantic view cannot satisfy a question, Cortex Analyst falls back to Standard SQL. You should expand the semantic view to reduce fallbacks over time.
