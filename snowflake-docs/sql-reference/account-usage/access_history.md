---
title: "ACCESS_HISTORY view"
url: "https://docs.snowflake.com/en/sql-reference/account-usage/access_history"
---

Schema:
:   [ACCOUNT\_USAGE](../account-usage.html#label-account-usage-views)

# ACCESS\_HISTORY view

[![Snowflake logo in black (no text)](../../_images/logo-snowflake-black.png)](../../_images/logo-snowflake-black.png) [Enterprise Edition Feature](../../user-guide/intro-editions)

Access History requires Enterprise Edition (or higher). To inquire about upgrading, please contact
[Snowflake Support](https://docs.snowflake.com/user-guide/contacting-support).

This Account Usage view can be used to query the access history of Snowflake objects (e.g. table, view, column) within the last 365 days
(1 year).

## Columns

This section consists of tables that do the following:

* Provide a sample value for each column.
* Provide a description of each column in the view.
* Provide a description for each field in the JSON array for the `base_objects_accessed`, `direct_objects_accessed`, and
  `objects_modified` columns.
* Provide a description for each field in the object for the `object_modified_by_ddl` column.

### Sample column values

The following table provides a sample value for each column in the view.

| Column name | Example |
| --- | --- |
| `query_id` | `a0fda135-d678-4184-942b-c3411ae8d1ce` |
| `query_start_time` | `2022-01-25 16:17:47.388 +0000` |
| `user_name` | `JSMITH` |
| `direct_objects_accessed` | CopyExpand  ``` [   {     "objectDomain": "FUNCTION",     "objectName": "GOVERNANCE.FUNCTIONS.RETURN_SUM",     "objectId": "2",     "argumentSignature": "(NUM1 NUMBER, NUM2 NUMBER)",     "dataType": "NUMBER(38,0)"   },   {     "columns": [       {         "columnId": 68610,         "columnName": "CONTENT"       }     ],     "objectDomain": "Table",     "objectId": 66564,     "objectName": "GOVERNANCE.TABLES.T1"   } ] ```  Show lessSee more  Scroll to top |
| `base_objects_accessed` | CopyExpand  ``` [   {     "objectDomain": "FUNCTION",     "objectName": "GOVERNANCE.FUNCTIONS.RETURN_SUM",     "objectId": "2",     "argumentSignature": "(NUM1 NUMBER, NUM2 NUMBER)",     "dataType": "NUMBER(38,0)"   },   {     "columns": [       {         "columnId": 68610,         "columnName": "CONTENT"       }     ],     "objectDomain": "Table",     "objectId": 66564,     "objectName": "GOVERNANCE.TABLES.T1"   } ] ```  Show lessSee more  Scroll to top |
| `objects_modified` | CopyExpand  ``` [   {     "objectDomain": "STRING",     "objectId":  NUMBER,     "objectName": "STRING",     "columns": [       {         "columnId": "NUMBER",         "columnName": "STRING",         "baseSources": [           {             "columnName": STRING,             "objectDomain": "STRING",             "objectId": NUMBER,             "objectName": "STRING"           }         ],         "directSources": [           {             "columnName": STRING,             "objectDomain": "STRING",             "objectId": NUMBER,             "objectName": "STRING"           }         ]       }     ]   },   ... ] ```  Show lessSee more  Scroll to top |
| `object_modified_by_ddl` | CopyExpand  ``` {   "objectDomain": STRING,   "objectName": STRING,   "objectId": NUMBER,   "operationType": STRING,   "properties": ARRAY } ```  Show lessSee more  Scroll to top |
| `policies_referenced` | CopyExpand  ``` [   {     "columns": [       {         "columnId": 68610,         "columnName": "SSN",         "policies": [           {               "policyName": "governance.policies.ssn_mask",               "policyId": 68811,               "policyKind": "MASKING_POLICY"           }         ]       }     ],     "objectDomain": "VIEW",     "objectId": 66564,     "objectName": "GOVERNANCE.VIEWS.V1",     "policies": [       {         "policyName": "governance.policies.rap1",         "policyId": 68813,         "policyKind": "ROW_ACCESS_POLICY"       }     ]   } ] ```  Show lessSee more  Scroll to top |

See moreShow less

Expand

### Column descriptions

The following table provides a description of each column in the view.

If a column contains `-1` in a number field or `TRUNCATED` in a string field, information in the column might have been truncated. For
more information, see [Usage notes: Truncation](#label-access-history-notes-truncation-acctuse).

| Column Name | Data Type | Description |
| --- | --- | --- |
| `query_id` | VARCHAR | An internal, system-generated identifier for the SQL statement. This value is also mentioned in the [QUERY\_HISTORY view](query_history). |
| `query_start_time` | TIMESTAMP\_LTZ | The statement start time (UTC time zone). |
| `user_name` | VARCHAR | The user who issued the query. |
| `direct_objects_accessed` | ARRAY | A JSON array of data objects such as user-defined functions (i.e. UDFs and UDTFs), stored procedures, tables, views, and columns directly named in the query explicitly or through shortcuts such as using an asterisk (i.e. `*`).  Virtual columns can be returned in this field.  For additional notes about UDFs, see [Usage notes](#usage-notes). |
| `base_objects_accessed` | ARRAY | A JSON array of all base data objects to execute a query, including columns, external functions, UDFs, and stored procedures.  In this example, the fields in the first array specify a UDF. These same fields in the first array also specify a stored procedure, when applicable.  Note the following:   * This field specifies view names or view columns, including virtual columns, if a shared view is accessed in a data sharing consumer   account. * For additional notes about UDFs, see [Usage notes](#usage-notes). |
| `objects_modified` | ARRAY | A JSON array that specifies the objects that were associated with a write operation in the query.  The UDF and stored procedure array is the same as what is shown earlier and appears in the arrays for `baseSources` and `directSources` depending on how the access took place. For brevity, this example omits the UDF and stored procedure array.  For additional notes about UDFs, see [Usage notes](#usage-notes). |
| `object_modified_by_ddl` | OBJECT | Specifies the DDL operation on a database, schema, table, view, and column. These operations also include statements that specify a row access policy on a table or view, a masking policy on a column, and tag updates (e.g. set a tag, change a tag value) on the object or column. |
| `policies_referenced` | ARRAY | Specifies information about the enforced masking policy set on the column and the enforced row access policy set on the table, including policies set on intermediate objects or columns. |
| `parent_query_id` | VARCHAR | The query ID of the parent job or NULL if the job does not have a parent. |
| `root_query_id` | VARCHAR | The query ID of the top most job in the chain or NULL if the job does not have a parent. |
| `event_source` | VARCHAR | Indicates the source of the event that resulted in an access history record. Possible values include the following:   * `snowflake_sql` — Events generated by SQL statements that were executed within Snowflake. * `horizon_irc` — Events generated by calls made to the [Horizon Iceberg REST Catalog API](../../user-guide/tables-iceberg-query-using-external-query-engine-snowflake-horizon). |
| `additional_properties` | VARIANT | Provides operational metadata for the source of the event. |

See moreShow less

Expand

### JSON field descriptions

The following table defines the fields in the JSON array for the `base_objects_accessed`, `direct_objects_accessed`, and
`objects_modified` columns.

| Field | Data Type | Description |
| --- | --- | --- |
| accountName [[1]](#id2) | VARCHAR | The account locator of the consumer account that queried the provider’s data object. If the query wasn’t executed by a consumer, this field is omitted. |
| columnId | NUMBER | A column ID that is unique within the account. This value is identical to the columnID in the [COLUMNS view](columns). |
| columnName | VARCHAR | The name of the accessed column. For policies, specifies the column on which the masking policy is set. |
| objectId | NUMBER | An identifier for the object, which is unique within a given account and domain. This number will match:   * The `TABLE_ID` number for a table, view, or materialized view. You can obtain this value from [TABLES view](tables), [VIEWS view](views), or [MATERIALIZED\_VIEW\_REFRESH\_HISTORY view](materialized_view_refresh_history). * If a stage was accessed, this number will match the:    + `NAME` identifier for a user stage (see [USERS view](users))   + `TABLE_ID` number for a table stage (see [TABLES view](tables))   + `STAGE_ID` number for a name stage (see [STAGES view](stages)) |
| objectName | VARCHAR | The fully qualified name of the object that was accessed.  If a masking policy is set on a column or a row access policy is set on a table or view, the value refers to the fully qualified name of the table or view on which the row access policy is set or the table or view that has a masking policy set on one of its columns.  If a stage was accessed, this value will be the:   * `username` (User stage). * `table_name` (Table stage). * `stage_name` (Named stage). |
| objectDomain | VARCHAR | The type of object. For a list of supported objects, see [Supported Objects](../../user-guide/access-history.html#label-access-history-supported-objects).  Note that `FUNCTION` specifies UDFs, UDTFs, and external functions.  For data access policies, specifies the domain of the object on which the policy is set. |
| location | VARCHAR | The URL of the external location when the data access is an external location (e.g. `s3://mybucket/a.csv`). . If the query does not access a stage, this field is omitted. |
| stageKind | VARCHAR | When writing to a stage, one of the following: `Table | User | Internal Named | External Named` If the query does not access a stage, this field is omitted. |
| baseSources | VARCHAR | The columns that serve as the source columns for the columns specified by `directSources`. These columns facilitate column lineage. |
| directSources | VARCHAR | The columns specifically mentioned in the data write portion of the SQL statement that serves as the source columns in the target table to which data is written. These columns facilitate column lineage. |
| policyName | VARCHAR | The fully-qualified name of the policy. |
| policyId | NUMBER | An identifier for the policy, which is unique within a given account and domain. This value matches the identifier for a masking policy in the [MASKING\_POLICIES view](masking_policies) or the identifier for a row access policy in the [ROW\_ACCESS\_POLICIES view](row_access_policies). |
| policyKind | VARCHAR | Either: MASKING\_POLICY or ROW\_ACCESS\_POLICY |
| argumentSignature | VARCHAR | The name and data type for each argument in the UDF or stored procedure. |
| dataType |  | The data type of the return value for a UDF or stored procedure.  This value helps to differentiate two or more UDFs that have the same name but different return types. |
| joinObjects | VARCHAR | If a query contains a join, returns an array containing the joined objects and type of join. |
| joinObject | VARCHAR | The table or view that was joined with the accessed object. |
| type | VARCHAR | The type of join, as described in [JOIN](../constructs/join), [ASOF JOIN](../constructs/asof-join), and [LATERAL](../constructs/join-lateral). |

See moreShow less

Expand

[[1](#id1)]

This field is found in the ACCESS\_HISTORY view of the ORGANIZATION\_USAGE schema, but not the ACCESS\_HISTORY view of the ACCOUNT\_USAGE schema.

### Object field descriptions for `object_modified_by_ddl`

The following table describes the fields of objects in the `object_modified_by_ddl` column.

| Field | Data type | Description |
| --- | --- | --- |
| objectDomain | VARCHAR | Type of the object defined or modified by the DDL operation. For more information about supported object types, see [Supported Objects](../../user-guide/access-history.html#label-access-history-supported-objects). |
| objectId | NUMBER | The identifier for the object, which is unique within a given account and domain, defined or modified by the DDL operation. |
| objectName | VARCHAR | The fully qualified name of the object defined or modified by the DDL operation. |
| operationType | VARCHAR | The SQL keyword that specifies the operation on the table, view, or column. For ALTER, CREATE, and DROP, this can also apply to listings and shares. For GRANT and REVOKE, this can also apply to shares. The following values are supported: ALTER | CREATE | DESCRIBE | DROP | REPLACE | UNDROP | REFRESH | SHOW | SUSPEND | RESUME | GRANT | REVOKE |
| properties | ARRAY | A JSON array that specifies the object or column properties when you create, modify, drop, or undrop the object or column. There are two types of properties: atomic and compound. |

See moreShow less

Expand

For the `properties` JSON array:

* Atomic: one value per property (e.g. a `comment` has a single string value, the `enabled` property is a boolean and has one value).
* Compound: the property is multi-valued (e.g. `allowed_values` for a tag, masking policy).

Compound properties are recorded in a JSON array. For example, if a table contains a single column named EMAIL, the column is recorded as
follows:

CopyExpand

```
"columns": {
  "email": {
    "objectId": {
      "value": 1
    },
    "subOperationType": "ADD"
  }
}
```

Show lessSee more

Scroll to top

In the previous example,

* `objectId` specifies the identifier for the column or object, except for allowed tag values, which don’t have an identifier.
* `subOperationType` can be one of the following values:

  + `ADD` specifies adding a compound property (for example, adding a column, setting allowed values).
  + `DROP` specifies removing a compound property.
  + `ALTER` specifies modifying a compound property.

#### CREATE or ALTER LISTING properties of OBJECT\_MODIFIED\_BY\_DDL

The following table describes available `properties` arrays when `operationType` is CREATE or ALTER for a *listing*.

| Command | Properties of OBJECT\_MODIFIED\_BY\_DDL |
| --- | --- |
| CopyExpand  ``` CREATE EXTERNAL LISTING my_listing SHARE my_share   AS $$my_manifest$$ ```  Show lessSee more  Scroll to top | CopyExpand  ``` "manifest": {   "value": "my_manifest" }, "share": {   "value": "my_share" } ```  Show lessSee more  Scroll to top |
| CopyExpand  ``` ALTER LISTING my_listing   AS $$my_manifest$$ ```  Show lessSee more  Scroll to top | CopyExpand  ``` "manifest": {   "value": "my_manifest" } ```  Show lessSee more  Scroll to top |
| CopyExpand  ``` ALTER LISTING my_listing   ADD TARGETS $$my_targets_manifest$$; ```  Show lessSee more  Scroll to top | CopyExpand  ``` "addTargets": {   "value": "my_targets_manifest" } ```  Show lessSee more  Scroll to top |
| CopyExpand  ``` ALTER LISTING my_listing   REMOVE TARGETS $$my_targets_manifest$$; ```  Show lessSee more  Scroll to top | CopyExpand  ``` "removeTargets": {   "value": "my_targets_manifest" } ```  Show lessSee more  Scroll to top |
| CopyExpand  ``` ALTER LISTING my_listing   ADD VERSION V3   FROM @listing_db.listing_schema.stage1; ```  Show lessSee more  Scroll to top | CopyExpand  ``` "manifestStageLocation": {   "value": "@listing_db.listing_schema.stage1" }, "versionAlias": {   "value": "V3" } ```  Show lessSee more  Scroll to top |

See moreShow less

Expand

#### CREATE or ALTER SHARE properties of OBJECT\_MODIFIED\_BY\_DDL

The following table describes available `properties` arrays when the `operationType` is CREATE or ALTER for a *share*.

| Command | Properties of OBJECT\_MODIFIED\_BY\_DDL |
| --- | --- |
| CopyExpand  ``` CREATE SHARE my_share   SECURE_OBJECTS_ONLY=FALSE; ```  Show lessSee more  Scroll to top | CopyExpand  ``` "secureObjectsOnly": {   "value": false } ```  Show lessSee more  Scroll to top |
| CopyExpand  ``` ALTER SHARE my_share   SET ACCOUNTS = acc1, acc2; ```  Show lessSee more  Scroll to top | CopyExpand  ``` "accountsToSet": {   "value": [ "acc1", "acc2" ] } ```  Show lessSee more  Scroll to top |
| CopyExpand  ``` ALTER SHARE my_share   ADD ACCOUNTS = acc1, acc2   SHARE_RESTRICTIONS = false; ```  Show lessSee more  Scroll to top | CopyExpand  ``` "accountsToAdd": {  "value": [ "acc1", "acc2" ] }, "shareRestrictions": {   "value": false } ```  Show lessSee more  Scroll to top |
| CopyExpand  ``` ALTER SHARE my_share   REMOVE ACCOUNTS = acc1, acc2; ```  Show lessSee more  Scroll to top | CopyExpand  ``` "accountsToRemove": {   "value": [ "acc1", "acc2" ] } ```  Show lessSee more  Scroll to top |

See moreShow less

Expand

#### GRANT TO SHARE or REVOKE FROM SHARE properties of OBJECT\_MODIFIED\_BY\_DDL

The following table describes available `properties` arrays when the `operationType` is GRANT TO or REVOKE FROM for a *share*.

| Command | Properties of OBJECT\_MODIFIED\_BY\_DDL |
| --- | --- |
| CopyExpand  ``` GRANT USAGE ON DATABASE my_db   TO SHARE my_share; ```  Show lessSee more  Scroll to top | CopyExpand  ``` "grant": {   "value": {     "PRIVILEGES": [       "USAGE"     ],     "SECURABLE_OBJECT_DOMAIN": "Database",     "SECURABLE_OBJECT_ID": 1234,     "SECURABLE_OBJECT_NAME": "MY_DB"   } } ```  Show lessSee more  Scroll to top |
| CopyExpand  ``` GRANT SELECT ON ALL TABLES IN SCHEMA my_db.my_sch   TO SHARE my_share; ```  Show lessSee more  Scroll to top | CopyExpand  ``` "grant": {   "value": {     "PRIVILEGES": [       "SELECT"     ],     "SECURABLE_OBJECT_DOMAIN": "Table",     "SECURABLE_OBJECT_SCOPE": "MY_DB.MY_SCH",     "SECURABLE_OBJECT_SCOPE_DOMAIN": "Schema"   } } ```  Show lessSee more  Scroll to top |
| CopyExpand  ``` GRANT DATABASE ROLE my_db.my_role   TO SHARE my_share; ```  Show lessSee more  Scroll to top | CopyExpand  ``` "grant": {   "value": {     "ROLES": [       "MY_DB.MY_ROLE"     ]   } } ```  Show lessSee more  Scroll to top |
| CopyExpand  ``` REVOKE SELECT ON VIEW my_db.my_sch.my_view   FROM SHARE my_share; ```  Show lessSee more  Scroll to top | CopyExpand  ``` "revoke": {   "value": {     "PRIVILEGES": [       "SELECT"     ],     "SECURABLE_OBJECT_DOMAIN": "View",     "SECURABLE_OBJECT_ID": 6789,     "SECURABLE_OBJECT_NAME": "MY_DB.MY_SCH.MY_VIEW"   } } ```  Show lessSee more  Scroll to top |

See moreShow less

Expand

## Usage notes

Latency and historical data:
:   * The view displays data starting from February 22, 2021.
    * Latency for the view may be up to 180 minutes (3 hours).

Ancestor queries:
:   The `parent_query_id` and `root_query_id` columns begin to record data starting on January 15-16, 2024, depending on when
    your Snowflake account was updated based on the `2023_08` behavior change bundle transitioning to enabled by default. This date is
    necessary to distinguish between the following records in the view:

    * Queries that ran before the bundle was enabled by default.
    * Queries that ran after the feature was enabled by default but do not have a value in the `parent_query_id`.

General notes:
:   * For increased performance, filter queries on the `query_start_time` column and choose narrower time ranges. For sample queries,
      see [Querying the ACCESS\_HISTORY View](../../user-guide/access-history.html#label-access-history-query).
    * Secure Views. The log record contains the underlying base table (i.e. `base_objects_accessed`) to generate the view. Examples
      include queries on other Account Usage and Organization Usage views and queries on base tables for extract, transform, and load
      (i.e. ETL) operations.
    * Records in the QUERY\_HISTORY view do not always get recorded in the
      ACCESS\_HISTORY view. The structure of the SQL statement determines whether Snowflake records an entry in the ACCESS\_HISTORY view.
    * Specifying the `USING` clause while querying this view might cause non-referenced columns to be recorded in
      `direct_objects_accessed` field. As a workaround, replace the `USING` clause with a `JOIN ... ON ...` clause.
      For details, refer to:

      + [JOIN and USING](../constructs/join.html#label-join-syntax) (in the JOIN reference topic)
      + [Tracking Sensitive stage data movement](../../user-guide/access-history.html#label-access-history-example-track-stage-data-movement) (in the Access History query example)

Read query notes:
:   This view supports read queries of the following type:

    * SELECT, including CREATE TABLE … AS SELECT (i.e. CTAS).

      + Snowflake records the SELECT subquery in a CTAS operation.
    * CREATE TABLE … CLONE

      + Snowflake records the source table in a CLONE operation.
    * COPY INTO … TABLE

      + Snowflake logs this query only when the table is specified as the source in a FROM clause.
    * DML operations that read data (e.g. contains a SELECT subquery, specifies certain columns in WHERE or JOIN): INSERT … SELECT,
      UPDATE, DELETE, and MERGE.
    * UDFs and [Tabular SQL UDFs (UDTFs)](../../developer-guide/udf/sql/udf-sql-tabular-functions) if tables are included in queries inside the functions. This is
      logged in the `base_objects_accessed` field.

Write operation notes:
:   This view supports write operations of the following type:

    * GET `<internal_stage>`
    * PUT `<internal_stage>`
    * DELETE
    * TRUNCATE
    * INSERT

      + INSERT INTO … FROM SELECT \*
      + INSERT INTO TABLE … VALUES ()
    * MERGE INTO … FROM SELECT \*
    * UPDATE

      + UPDATE TABLE … FROM SELECT \* FROM …
      + UPDATE TABLE … WHERE …
    * Data loading statements:

      + COPY INTO TABLE FROM internalStage
      + COPY INTO TABLE FROM externalStage
      + COPY INTO TABLE FROM externalLocation
    * Data unloading statements:

      + COPY INTO internalStage FROM TABLE
      + COPY INTO externalStage FROM TABLE
      + COPY INTO externalLocation FROM TABLE
    * CREATE:

      + CREATE DATABASE … CLONE
      + CREATE SCHEMA … CLONE
      + CREATE TABLE … CLONE
      + CREATE TABLE … AS SELECT
    * For write operations that call the [CASE](../functions/case) function to determine the columns to access, such as a CTAS
      statement with the CASE function in the SELECT query, all columns referenced in every CASE branch are recorded in the
      `base_objects_accessed` column, the `direct_objects_accessed` column, or both columns depending on how the CTAS statement
      is written.

Data sharing notes:
:   If a Data Sharing provider account shares objects to Data Sharing consumer accounts through a share:

    * **Provider accounts:** The queries and logs on the shared objects executed in the provider account are not visible to
      Data Sharing consumer accounts.
    * **Consumer accounts:** The queries on the data share executed in the consumer account are logged and only visible to
      the consumer account, not the Data Sharing provider account.

      For example, if the provider shares a table and a view built from the table to the consumer account, and there is a query on the
      shared view, Snowflake records the shared view access in the `base_objects_accessed` column. This record, which includes the
      `columnName` and `objectName` values, allows the consumer to know which object was accessed in their account and also protects
      the provider because the underlying table (via the `objectId` and `columnId`) is not revealed to the consumer.
    * For column lineage:

      If a data sharing provider makes a view available to the data sharing consumer, the source columns for the view are not visible to the
      consumer because the columns originate from the data sharing provider.

      If the data sharing consumer moves data from the shared view to a table, Snowflake does not record the view columns as
      `baseSources` for the newly created table.
    * For shared UDFs and UDTFs:

      + In the consumer account, the local ACCESS\_HISTORY view records the UDF/UDTF that was shared by the provider when the shared UDF/UDTF
        is invoked by the consumer.
      + In the provider account, the local ACCESS\_HISTORY view records provider usage of a shared UDF/UDTF. Users in the consumer account
        cannot view how the provider account uses the shared UDF/UDTF.
    * For tracking policy references:

      The `policies_referenced` column contains policies that are local to the account that queries the data.

      If a provider shares a policy-protected table and a consumer accesses this table, the consumer cannot see the policy the provider set
      on the table or its columns.

      If a consumer creates a view (`v1`) from the shared object, sets a policy to the view (`v1`) or its columns, and a user in the
      consumer account accesses the protected view (`v1`) or another view (`v2`) created from the protected view (`v1`), the
      ACCESS\_HISTORY view in the consumer account contains the policy that protects the view (`v1`) and its columns. The provider cannot
      see the record that corresponds to `v1`.

Hybrid tables:
:   Short-running queries that operate exclusively against hybrid tables will no
    longer generate a record in the QUERY\_HISTORY view, in [QUERY\_HISTORY view](query_history), or
    in the output of the QUERY\_HISTORY table function. To monitor such queries, use the
    [AGGREGATE\_QUERY\_HISTORY](aggregate_query_history).

    To monitor Access History for such queries, use the
    [AGGREGATE\_ACCESS\_HISTORY](aggregate_access_history).
    This view allows you to more easily monitor high-throughput operational
    workloads for Access History.

Snowflake Native App Framework notes:
:   Some queries related to a Snowflake Native App are redacted. For details, see [Information redacted from SQL commands and views](../../developer-guide/native-apps/redacted-content.html#label-native-apps-redacted-info).

Tag-based masking notes:
:   If a user accesses a table or view protected by a [tag-based masking policy](../../user-guide/tag-based-masking-policies), the
    `policies_referenced` column contains the masking policy applied through the tag when Snowflake enforces the masking policy on the
    protected column.

    The ACCESS\_HISTORY view does not record any tag information.

UDFs & Stored Procedure notes:
:   These notes apply to external functions, UDFs and UDTFs for all languages, including when these functions have the `SECURE` property,
    and stored procedures with owner’s rights and caller’s rights:

    Column details:

    * The `direct_objects_accessed` column records explicit mention of these functions and procedures in a query.

      Snowflake does not record nested UDFs (i.e. a UDF mentioned in the definition of another UDF) in this column.
    * The `base_objects_accessed` column records external functions, shared functions, non-SQL UDFs, and stored procedures that are
      called in a query.
    * The `objects_modified` column records:

      + The UDF/UDTF when the result of calling the function copies the result to another column.
      + The UDF, UDTF, and an external function can be recorded in the arrays for `baseSources` and `directSources` depending on how the
        query is written.

Not supported:
:   This view does not log accesses of the following types:

    * Snowflake-provided [table functions](../functions-table), [Account Usage](../account-usage) views, and
      [Organization Usage](../organization-usage) views.
    * [RESULT\_SCAN](../functions/result_scan) to obtain prior results.
    * An Access History record is generated when DDL operations are performed on
      [sequences](../../user-guide/querying-sequences). It is not generated when a sequence is used in any other
      operations, including generating new values.
    * Intermediate views accessed between the base table and direct object.

      For example, consider a query on View\_A with the following object structure: View\_A » View\_B » View\_C » Base\_Table.

      The ACCESS\_HISTORY view records the query on View\_A and the Base\_Table, not View\_B and View\_C.
    * The operations to update streams.
    * Data movement resulting from replication.
    * Failed queries, although logged in the QUERY\_HISTORY view, will *not* be logged in the ACCESS\_HISTORY view.

## Usage Notes: Column Lineage

These additional notes pertain to column lineage:

Supported operations:
:   Column lineage tracks details for the following SQL operations:

    * [CREATE TABLE … AS SELECT](../sql/create-table.html#label-ctas-syntax) (CTAS)
    * [CREATE TABLE … CLONE](../sql/create-table.html#label-create-table-clone-syntax)
    * [INSERT … SELECT …](../sql/insert)
    * [MERGE](../sql/merge)
    * [UPDATE](../sql/update), two possible variations, for example:

      + Self-update:

        CopyExpand

        ```
        UPDATE mydb.s1.t1 SET col_1 = col_1 + 1;
        ```

        Show lessSee more

        Scroll to top
      + Two table update:

        CopyExpand

        ```
        UPDATE mydb.s1.t1 FROM mydb.s2.t2 SET t1.col1 = t2.col1;
        ```

        Show lessSee more

        Scroll to top
    * [ALTER TABLE](../sql/alter-table) … RENAME TO

Query Conditions:
:   * [Query profile/plan](../../user-guide/ui-snowsight-activity)

      The query plan Snowflake writes determines whether the ACCESS\_HISTORY view contains column lineage. If a column needs to be
      evaluated as part of the query plan, Snowflake contains the column in the ACCESS\_HISTORY view, even if the end result of the query plan
      is that the column is not included in the end result.

      For example, consider the following [INSERT](../sql/insert) statement with a `WHERE` clause for a particular column value:

      > CopyExpand
      >
      > ```
      > insert into a(c1)
      > select c2
      > from b
      > where c3 > 1;
      > ```
      >
      > Show lessSee more
      >
      > Scroll to top

      Even if the WHERE clause evaluates to `FALSE`, Snowflake records the `c2` column as a source column for the `c1` column. The
      `c3` column is not listed as a source column for either `baseSources` or `directSources`.
    * Masked columns:

      + The masked column is always listed in the `directSources` field.
      + The record in the `baseSources` field depends on the policy definition. For example:

        - If the masking policy conditions use a [CASE](../functions/case) function, then all of the columns referenced in each of
          the CASE branches are recorded in the `baseSources` field.
        - If the masking policy conditions only specify a constant value (e.g. `*****`), then the `baseSources` field is empty.
    * UDFs:

      + When passing a column as an argument to a UDF and writing the result to another column, the column that is passed as the argument
        is recorded in the `directSources` field. For example:

        > CopyExpand
        >
        > ```
        > insert into A(col1) select f(col2) from B;
        > ```
        >
        > Show lessSee more
        >
        > Scroll to top

        In this example, Snowflake records `col2` in the `directSources` field because the column is an argument for the UDF named
        `f`.
      + The record in the `baseSources` field depends on the UDF definition.

View columns:
:   View columns are not considered to be source columns and are not listed in the `baseSources` field when data from a view column
    is copied to a table column. The view columns in this case are listed in the `directSources` field.

EXISTS Subquery:
:   Columns that are referenced in the [EXISTS](../operators-subquery.html#label-operators-subquery-exists) subquery clause are not considered to be source
    columns.

## Usage Notes: `object_modified_by_ddl` Column

`IF [ NOT ] EXISTS` clauses: The `object_modified_by_ddl` column only records `CREATE` or `REPLACE` when creating
or modifying an object.

The column records these changes based on the following SQL operations. The DROP and UNDROP operations apply to tables and views, not
columns.

CopyExpand

```
CREATE OR REPLACE

ALTER ... { SET | UNSET }

ALTER ... ADD ROW ACCESS POLICY

ALTER ... DROP ROW ACCESS POLICY

ALTER ... DROP ALL ROW ACCESS POLICIES

DROP | UNDROP
```

Show lessSee more

Scroll to top

The following table summarizes the relationship between DDL operations, supported domains, and the properties Snowflake records.

| Operation | Domain | Properties | Notes |
| --- | --- | --- | --- |
| CREATE [ OR REPLACE ] | TABLE | EXTERNAL TABLE | VIEW | MATERIALIZED VIEW | ICEBERG TABLE | Column name, column identifier. | CREATE DATABASE and CREATE SCHEMA operations do not have properties recorded. |
| CREATE | TABLE … { AS SELECT | USING TEMPLATE | LIKE | CLONE } | Column name, column identifier. | Snowflake records the creation source for LIKE and CLONE operations.  Snowflake does not record the creation source when the source object is from a share or with USING TEMPLATE. |
| ALTER … RENAME TO  ALTER TABLE … RENAME COLUMN | TABLE | VIEW | MATERIALIZED VIEW | ICEBERG TABLE | DATABASE | SCHEMA | The new name of the object or column. |  |
| ALTER … SWAP WITH | TABLE | SCHEMA | DATABASE | objectName, objectId, objectDomain | There are two records in the view, one for each swap target. Each record contains the same query identifier value. |
| ALTER … { ADD | DROP } COLUMN | TABLE | Column name, column identifier, and the ADD or DROP subOperationType. |  |
| DROP | TABLE | VIEW | MATERIALIZED VIEW | ICEBERG TABLE | DATABASE | SCHEMA | Snowflake does not record properties for these operations. |  |
| UNDROP | TABLE | ICEBERG TABLE | SCHEMA | DATABASE | Snowflake does not record properties for these operations. |  |

See moreShow less

Expand

## Usage notes: Truncation

When a record exceeds the size limit for the view, Snowflake applies a progressive truncation strategy that preserves the most critical audit information while reducing the record size. Truncation adheres to the following general guidelines:

* Column-level information is truncated before object-level information.
* Lineage information is truncated before data access and data protection policy information.
* Query-level metadata columns (`query_id`, `query_start_time`, and `user_name`) are always preserved.

When truncating information, Snowflake replaces numbers with `-1` and replaces strings with `TRUNCATED`. These sentinel elements indicate that information has been truncated.

The following sections describe the order in which records are truncated. Truncation stops as soon as the record fits within the size constraints.

Phase 1: Truncate column lineage in the `object_modified` column
:   CopyExpand

    ```
      {
      "objectDomain": "Stream",
      "objectId":  1105,
      "objectName": "\"NESTED_ALERT_PIPELINE_ALERT_eK1VYsLDcTcpqPAA\"",
      "columns": [
        {
          "columnId": -1,
          "columnName": "TRUNCATED",
        }
      ]
    }
    ```

    Show lessSee more

    Scroll to top

Phase 2: Truncate column information in the `policies_referenced` column
:   CopyExpand

    ```
    [
      {
        "columns": [
          {
            "columnId": -1,
            "columnName": "TRUNCATED",
          }
        ],
        "objectDomain": "VIEW",
        "objectId": 66564,
        "objectName": "GOVERNANCE.VIEWS.V1",
        "policies": [
          {
            "policyName": "governance.policies.rap1",
            "policyId": 68813,
            "policyKind": "ROW_ACCESS_POLICY"
          }
      ]
      }
    ]
    ```

    Show lessSee more

    Scroll to top

Phase 3: Truncate column access information in the `base_objects_accessed` column
:   CopyExpand

    ```
    [
      {
        "objectDomain": "Function",
        "objectName": "GOVERNANCE.FUNCTIONS.RETURN_SUM",
        "objectId": "2",
        "argumentSignature": "(NUM1 NUMBER, NUM2 NUMBER)",
        "dataType": "NUMBER(38,0)"
      },
      {
        "columns": [
          {
            "columnId": -1,
            "columnName": "TRUNCATED"
          }
        ],
        "objectDomain": "Table",
        "objectId": 66564,
        "objectName": "GOVERNANCE.TABLES.T1"
      }
    ]
    ```

    Show lessSee more

    Scroll to top

Phase 4: Truncate column access information in the `direct_objects_accessed` column
:   CopyExpand

    ```
    [
      {
        "objectDomain": "Function",
        "objectName": "GOVERNANCE.FUNCTIONS.RETURN_SUM",
        "objectId": "2",
        "argumentSignature": "(NUM1 NUMBER, NUM2 NUMBER)",
        "dataType": "NUMBER(38,0)"
      },
      {
        "columns": [
          {
            "columnId": -1,
            "columnName": "TRUNCATED"
          }
        ],
        "objectDomain": "Table",
        "objectId": 66564,
        "objectName": "GOVERNANCE.TABLES.T1"
      }
    ]
    ```

    Show lessSee more

    Scroll to top

Phase 5: Truncate column properties in the `object_modified_by_ddl` column
:   CopyExpand

    ```
    {
      "objectDomain": "Table",
      "objectId": 20196,
      "objectName": "MY_DB.PUBLIC.T2",
      "operationType": "REPLACE",
      "properties": {
        "columns": "TRUNCATED",
      }
    }
    ```

    Show lessSee more

    Scroll to top

Phase 6: Truncate column access information in the `provider_base_objects_accessed` column
:   CopyExpand

    ```
    [
      {
        "objectDomain": "FUNCTION",
        "objectName": "GOVERNANCE.FUNCTIONS.RETURN_SUM",
        "objectId": "2",
        "argumentSignature": "(NUM1 NUMBER, NUM2 NUMBER)",
        "dataType": "NUMBER(38,0)"
      },
      {
        "columns": [
          {
            "columnId": -1,
            "columnName": "TRUNCATED"
          }
        ],
        "objectDomain": "Table",
        "objectId": 66564,
        "objectName": "GOVERNANCE.TABLES.T1"
      }
    ]
    ```

    Show lessSee more

    Scroll to top

Phase 7: Truncate column information in the `provider_policies_referenced` column
:   CopyExpand

    ```
    [
      {
        "columns": [
          {
            "columnId": -1,
            "columnName": "TRUNCATED",
          }
        ],
        "objectDomain": "VIEW",
        "objectId": 66564,
        "objectName": "GOVERNANCE.VIEWS.V1",
        "policies": [
          {
            "policyName": "governance.policies.rap1",
            "policyId": 68813,
            "policyKind": "ROW_ACCESS_POLICY"
          }
        ]
      }
    ]
    ```

    Show lessSee more

    Scroll to top

Phase 8: Replace information in columns with a single sentinel record
:   > As the last phase in the truncation process, Snowflake replaces all the information in a column with a single sentinel object. Snowflake replaces information in the following order:
    >
    > * `policies_referenced` column
    > * `objects_modified` column
    > * `base_objects_accessed` column
    > * `provider_base_objects_accessed` column (ORGANIZATION\_USAGE schema only)
    > * `provider_policies_referenced` column (ORGANIZATION\_USAGE schema only)

    The following is an example of a sentinel object found in a column:

    > CopyExpand
    >
    > ```
    > [
    >   {
    >     "objectDomain": "TRUNCATED",
    >     "objectId": -1,
    >     "objectName": "TRUNCATED",
    >   }
    > ]
    > ```
    >
    > Show lessSee more
    >
    > Scroll to top
