---
title: "Designing multi-step flows"
url: "https://docs.snowflake.com/en/user-guide/cleanrooms/multistep-flows"
---

# Designing multi-step flows

Feature — Generally Available

Currently available in [these regions](web-app-introduction.html#label-web-app-hosting).

Not available in government and VPS deployments.

## Overview

Most clean room usage involves running a single SQL query against one or more tables in a clean room and displaying the results in
the response. However, there are many use cases where you might want to break up your flow into several steps, which can be run
sequentially or individually, and can involve calling Python code to process (or pre-process) data. Examples include a machine learning
flow where the model is trained once against a data set and then run multiple times against varying input data, either singly or in batches.

Clean rooms have several mechanisms to enable these advanced scenarios:

* **Template chains:** A [template chain](developer-template-chains) runs a set of templates in a specific
  order, using the output of each template as the input of the next template. Input for the first template in the chain is provided by the
  user; output from the last template in the chain is returned to the user.
* **Internal tables:** Your template or custom internal functions can create persistent tables within a clean room. These tables behave like linked
  tables in that they are accessible to templates or custom uploaded code. Internal tables are useful for maintaining state or data; in the
  machine learning example, the training data is saved in an internal table that is used by internal functions. Just as with linked tables,
  these tables can be accessed only by templates or uploaded code inside the clean room. Storing intermediary data in internal tables is
  more efficient than passing large blocks of information into and out of the clean room using templates.
* **Custom internal functions:** You can define custom functions within a clean room that can be called by templates in that clean room.
  Functions can be defined in a clean room either by uploading
  [Python UDFs or UDTFs](demo-flows/custom-code) into the clean room, or by
  [creating a container service in your clean room](demo-flows/snowpark.html#label-dcr-snowpark-spcs) that exposes endpoints that implement
  functions. These functions can be called only by templates within the clean room.

Note

A unifying principle of all techniques is that tables and functions are accessed or run using a template. You cannot access a clean room
internal table, run a custom clean room function, or access an internal clean room endpoint directly, only by using a template.

## Internal clean room tables

You can create tables inside a clean room using SQL or Python to store intermediary results, or for persistent storage for the user or
your internal functions (for example, to save training data that is used for multiple runs). These tables behave the same as linked tables, with the following notes:

* Internal tables are created using a clean room template or a UDF/UDTF, and have no linkage to outside tables.
* Internal tables are created in the `cleanroom` namespace.
* You can set row and column policies on internal tables after you create them.
* If the table name is dynamic, and the table is accessed by other templates or code, return the name of the table to
  the user so the user can pass the dynamic table name to any other templates that need to access that table.

Here are some examples of creating an internal table:

TemplateUDFContainer service

A JinjaSQL template can create an internal table, which is done in some types of <activation>.

This example returns the table name so that it can be passed in as a parameter to other templates.

CopyExpand

```
CALL samooha_by_snowflake_local_db.provider.add_custom_sql_template(
  $cleanroom_name,
  $template_name,
  $$
  BEGIN
    CREATE OR REPLACE TABLE cleanroom.analysis_results AS
      SELECT count(*) AS ITEM_COUNT, c.status, c.age_band
      FROM IDENTIFIER({{ my_table[0] }}) AS c
      JOIN IDENTIFIER({{ source_table[0] }}) AS p
      ON {{ c_join_col | sqlsafe | activation_policy }} = {{ p_join_col | sqlsafe | activation_policy }}
      GROUP BY c.status, c.age_band
      ORDER BY c.age_band;
      RETURN 'analysis_results';
  END;
  $$);
```

Show lessSee more

Scroll to top

A UDF can create an internal table. This is typically done by executing SQL in Python.

CopyExpand

```
# Snippet of Python UDF to save results to an internal table.
table_name = f'cleanroom.results'

session.sql(f"""
CREATE OR REPLACE TABLE {table_name} AS (
  WITH joint_data AS (
    SELECT
        date,
        p.hashed_email AS hem,
        impression_id
    FROM {source_table} p
  )
  SELECT
    date,
    COUNT(DISTINCT hem) AS reach,
    COUNT(DISTINCT impression_id) AS num_impressions
  FROM joint_data
  GROUP BY date
  ORDER BY date
);
""").collect()
```

Show lessSee more

Scroll to top

CopyExpand

```
# Snippet of container services Python code to create an internal results table.
# 'cleanroom' table name prefix is added using the schema parameter when the table is created.
@app.post("/score")
def score():

... omitted content ...

df = pd.DataFrame({
    "ID": ids,
    "SCORE": scores
})
table = "LOOKALIKE_RESULTS"
session.write_pandas(df, table, schema="CLEANROOM", auto_create_table=True, overwrite=True)

end_time = time.perf_counter()
execution_time = end_time - start_time
response = make_json_response([[0, {"results_table": table, "size": len(ids), "execution_time": round(execution_time, 2)}]])
return response
```

Show lessSee more

Scroll to top

When you generate an internal table that must be accessed by template or code, you can either use a constant table name, or name the table
dynamically and return the name of the table to the user, who then passes in the table name to the results function.

Here is an example of a dynamically named table used to store results. The user makes two calls: one to generate the data and get the table
name, and a second to see the results.

1. The provider template calls the `reach_impression_regression` UDF to process data (the `cleanroom` prefix indicates that this is a UDF).
   The UDF returns the internal table prefix name to the template, which returns it to the caller.

   CopyExpand

   ```
   -- This template calls a UDF uploaded by a collaborator.
   -- The UDF takes two input tables as parameters.
   CALL samooha_by_snowflake_local_db.provider.add_custom_sql_template(
     $cleanroom_name,
     'prod_calculate_regression',
     $$
     CALL cleanroom.reach_impression_regression({{ source_table[0] }}, {{ my_table[0] | default('NONE') }});
     $$
   );
   ```

   Show lessSee more

   Scroll to top
2. The Python UDF generates the internal table and returns the generated table name to the template caller.

   CopyExpand

   ```
   def main(session, source_table, my_table):
     ...
     table = f'results_{suffix}'.upper()
     retval_df = session.write_pandas(regression_output, table,  schema = 'CLEANROOM', auto_create_table = True)

     return f'Done, results have been written to the following table: {table}'
   ```

   Show lessSee more

   Scroll to top
3. The provider template accepts a table name passed in and displays the contents of that table. Note how the table is always accessed
   from the `cleanroom` namespace.

   CopyExpand

   ```
   CALL samooha_by_snowflake_local_db.provider.add_custom_sql_template(
           $cleanroom_name,
           'prod_get_results',
           $$
             SELECT * FROM cleanroom.{{ results_table | sqlsafe }};
           $$
   );
   ```

   Show lessSee more

   Scroll to top
4. The consumer calls the template, passing in the table name.

   CopyExpand

   ```
   CALL samooha_by_snowflake_local_db.consumer.run_analysis(
     $cleanroom_name,
     'prod_get_results',
     [],
     [],
     object_construct(
         'results_table', $table_name
     )
   );
   ```

   Show lessSee more

   Scroll to top

## Triggering custom functions

Custom functions can be called either by templates or by code (UDFs, UDTFs, or container service endpoints) in the clean room.
Functions uploaded by any collaborator can be accessed by templates or code from any other collaborator.

Clean room functions should always be called scoped to the appropriate namespace:

* `cleanroom.function_name` when calling a custom UDF/UDTF function
* `service_functions.function_name` when calling a function exposed as an embedded Snowpark Container Service function.

Here are examples of calling a custom UDF and a custom container service endpoint from a template:

UDFContainer services

Templates use the `cleanroom` scope to access UDF or UDTFs.

CopyExpand

```
-- Template to generate results. Calls the UDF 'my_function', which
-- generates a results table inside the clean room called 'results'.
CALL samooha_by_snowflake_local_db.provider.add_custom_sql_template(
  $cleanroom_name,
  'generate_results_template',
  $$
  CALL cleanroom.my_function({{ source_table[0] }}, {{ my_table[0] | default('NONE') }});
  $$
);
```

Show lessSee more

Scroll to top

Templates use the `service_functions` scope to access container service functions.

CopyExpand

```
-- Template to trigger training data generation.
CALL samooha_by_snowflake_local_db.provider.add_custom_sql_template(
  $cleanroom_name,
  'lal_train',
  $$
  SELECT service_functions.my_func(
            {{ source_table[0] }},
            {{ provider_join_col }},
            {{ my_table[0] }},
            {{ consumer_join_col }},
            {{ dimensions | sqlsafe }},
            {{ filter_clause }}
          ) AS train_result;
$$
```

Show lessSee more

Scroll to top

## Common multi-step flow patterns

The [Snowpark API example](demo-flows/snowpark.html#label-dcr-snowpark-udf) processes data, generates intermediary tables, then a results table with one
template call, and then exposing results directly through a second template call.

The [Snowpark Container Services example](demo-flows/snowpark.html#label-dcr-snowpark-spcs) creates training data with one template call and stores the
training data to an internal table. A second template analyzes user input against the stored training data.
