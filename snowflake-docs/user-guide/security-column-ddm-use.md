---
title: "Using Dynamic Data Masking"
url: "https://docs.snowflake.com/en/user-guide/security-column-ddm-use"
---

# Using Dynamic Data Masking

[![Snowflake logo in black (no text)](../_images/logo-snowflake-black.png)](../_images/logo-snowflake-black.png) [Enterprise Edition Feature](intro-editions)

This feature requires Enterprise Edition (or higher). To inquire about upgrading, please contact [Snowflake Support](https://docs.snowflake.com/user-guide/contacting-support).

This topic provides instructions on how to configure and use Dynamic Data Masking in Snowflake.

To learn more about using a masking policy with a tag, see [Tag-based masking policies](tag-based-masking-policies).

## Using Dynamic Data Masking

The following lists the high-level steps to configure and use Dynamic Data Masking in Snowflake:

1. Grant masking policy management privileges to a custom role for a security or privacy officer.
2. Grant the custom role to the appropriate users.
3. The security or privacy officer creates and defines masking policies and applies them to columns with sensitive data.
4. Execute queries in Snowflake. Note the following:

   * Snowflake dynamically rewrites the query applying the masking policy SQL expression to the column.
   * The column rewrite occurs at every place where the column specified in the masking policy appears in the query (e.g. projections, join predicate, where clause predicate, order by, and group by).
   * Users see masked data based on the execution context conditions defined in the masking policies. For more information on the execution context in Dynamic Data Masking policies, see [Advanced Column-level Security topics](security-column-advanced).

### Enforce dynamic data masking policies on Apache Iceberg tables queried from Apache Spark™

Snowflake supports enforcing dynamic data masking policies on Apache Iceberg tables that you query from Apache Spark™ through Snowflake Horizon
Catalog. For more information,
see [Enforce data protection policies when querying Apache Iceberg™ tables from Apache Spark™](tables-iceberg-query-using-external-query-engine-snowflake-horizon-enforce-access-policies).

### Step 1: Grant masking policy privileges to the custom role

A [security or privacy officer](security-column-intro.html#label-security-column-mgmt-approach) should serve as the masking policy administrator (i.e. custom role: `MASKING_ADMIN`) and have the privileges to define, manage, and apply masking policies to columns.

Snowflake provides the following privileges to grant to a security or privacy officer for Column-level Security masking policies:

| Privilege | Object | Description |
| --- | --- | --- |
| CREATE MASKING POLICY | Schema | This privilege controls who can create masking policies. |
| APPLY MASKING POLICY | Account | This privilege controls who can [un]set masking policies on columns and is granted to the ACCOUNTADMIN role by default. . This privilege only allows applying a masking policy to a column and does not provide any additional table privileges described in [Access control privileges](security-access-control-privileges). |
| APPLY | Masking policy | Optional. This policy-level privilege can be used by a policy owner to decentralize the [un]set operations of a given masking policy on columns to the object owners (i.e. the role that has the OWNERSHIP privilege on the object). . Snowflake supports [discretionary access control](security-access-control-overview) where object owners are also considered data stewards. . If the policy administrator trusts the object owners to be data stewards for protected columns, then the policy administrator can use this privilege to decentralize applying the policy [un]set operations. |

See moreShow less

Expand

The following example creates the `MASKING_ADMIN` role and grants masking policy privileges to that role.

Create a masking policy administrator custom role:

> CopyExpand
>
> ```
> use role useradmin;
> CREATE ROLE masking_admin;
> ```
>
> Show lessSee more
>
> Scroll to top

Grant privileges to `masking_admin` role:

> CopyExpand
>
> ```
> use role securityadmin;
> GRANT CREATE MASKING POLICY on SCHEMA <db_name.schema_name> to ROLE masking_admin;
> GRANT APPLY MASKING POLICY on ACCOUNT to ROLE masking_admin;
> ```
>
> Show lessSee more
>
> Scroll to top

Allow `table_owner` role to set or unset the `ssn_mask` masking policy (optional):

> CopyExpand
>
> ```
> GRANT APPLY ON MASKING POLICY ssn_mask to ROLE table_owner;
> ```
>
> Show lessSee more
>
> Scroll to top

Where:

* `db_name.schema_name`
  :   Specifies the identifier for the schema for which the privilege should be granted.

For more information, see:

* [GRANT <privileges> … TO ROLE](../sql-reference/sql/grant-privilege)
* [Configuring access control](security-access-control-configure)
* [Access control privileges](security-access-control-privileges)

### Step 2: Grant the custom role to a user

Grant the `MASKING_ADMIN` custom role to a user serving as the security or privacy officer.

CopyExpand

```
GRANT ROLE masking_admin TO USER jsmith;
```

Show lessSee more

Scroll to top

### Step 3: Create a masking policy

Using the MASKING\_ADMIN role, create a masking policy and apply it to a column.

In this representative example, users with the ANALYST role see the unmasked value. Users without the ANALYST role see a full mask.

CopyExpand

```
CREATE OR REPLACE MASKING POLICY email_mask AS (val string) RETURNS string ->
  CASE
    WHEN CURRENT_ROLE() IN ('ANALYST') THEN val
    ELSE '*********'
  END;
```

Show lessSee more

Scroll to top

Tip

If you want to update an existing masking policy and need to see the current definition of the policy, call the [GET\_DDL](../sql-reference/functions/get_ddl) function or run the [DESCRIBE MASKING POLICY](../sql-reference/sql/desc-masking-policy) command.

### Step 4: Apply the masking policy to a table or view column

These examples assume that a masking policy is not applied to the table column when the table is created and the view column when the view
is created. You can optionally apply a masking policy to a table column when you create the table with a
[CREATE TABLE](../sql-reference/sql/create-table) statement or a view column with a [CREATE VIEW](../sql-reference/sql/create-view) statement.

Execute the following statements to apply the policy to a table column or a view column.

CopyExpand

```
-- apply masking policy to a table column

ALTER TABLE IF EXISTS user_info MODIFY COLUMN email SET MASKING POLICY email_mask;

-- apply the masking policy to a view column

ALTER VIEW user_info_v MODIFY COLUMN email SET MASKING POLICY email_mask;
```

Show lessSee more

Scroll to top

### Step 5: Query data in Snowflake

Execute two different queries in Snowflake, one query with the ANALYST role and another query with a different role, to verify that users without the ANALYST role see a full mask.

CopyExpand

```
-- using the ANALYST role

USE ROLE analyst;
SELECT email FROM user_info; -- should see plain text value

-- using the PUBLIC role

USE ROLE PUBLIC;
SELECT email FROM user_info; -- should see full data mask
```

Show lessSee more

Scroll to top

## Masking policy with a memoizable function

This example uses a [memoizable function](../developer-guide/udf/sql/udf-sql-scalar-functions.html#label-udf-sql-scalar-memoizable) to cache the result of a query on the mapping table that
determines whether a role is authorized to view PII data. A data engineer uses a masking policy to protect the columns in the table.

The following procedure references these objects:

* A table that contains PII data, `employee_data`:

  Expand

  ```
  +----------+-------------+---------------+
  | USERNAME |     ID      | PHONE_NUMBER  |
  +----------+-------------+---------------+
  | JSMITH   | 12-3456-89  | 1555-523-8790 |
  | AJONES   | 12-0124-32  | 1555-125-1548 |
  +----------+-------------+---------------+
  ```

  Show lessSee more

  Scroll to top
* A mapping table that determines whether a particular role is authorized to view data, `auth_role_t`:

  Expand

  ```
  +---------------+---------------+
  | ROLE          | IS_AUTHORIZED |
  +---------------+---------------+
  | DATA_ENGINEER | TRUE          |
  | DATA_STEWARD  | TRUE          |
  | IT_ADMIN      | TRUE          |
  | PUBLIC        | FALSE         |
  +---------------+---------------+
  ```

  Show lessSee more

  Scroll to top

Complete these steps to create a masking policy that calls a memoizable function with arguments:

1. Create a memoizable function that queries the mapping table. The function returns an array of roles based on the value of the
   `is_authorized` column:

   CopyExpand

   ```
   CREATE FUNCTION is_role_authorized(arg1 VARCHAR)
   RETURNS BOOLEAN
   MEMOIZABLE
   AS
   $$
     SELECT ARRAY_CONTAINS(
       arg1::VARIANT,
       (SELECT ARRAY_AGG(role) FROM auth_role WHERE is_authorized = TRUE)
     )
   $$;
   ```

   Show lessSee more

   Scroll to top
2. Call the memoizable function to cache the query results. In this example, pass the value `TRUE` as the argument value because the
   resultant array serves as the source of allowed roles to access the data protected by the masking policy:

   CopyExpand

   ```
   SELECT is_role_authorized(IT_ADMIN);
   ```

   Show lessSee more

   Scroll to top

   Expand

   ```
   +---------------------------------------------+
   |         is_role_authorized(IT_ADMIN)        |
   +---------------------------------------------+
   |                    TRUE                     |
   +---------------------------------------------+
   ```

   Show lessSee more

   Scroll to top
3. Create a masking policy to protect the `id` column. The policy calls the memoizable function to determine whether the
   role used to query the table is authorized to see the data in the protected column:

   CopyExpand

   ```
   CREATE OR REPLACE MASKING POLICY empl_id_mem_mask
   AS (val VARCHAR) RETURNS VARCHAR ->
   CASE
     WHEN is_role_authorized(CURRENT_ROLE()) THEN val
     ELSE NULL
   END;
   ```

   Show lessSee more

   Scroll to top
4. Set the masking policy on the table with an [ALTER TABLE … ALTER COLUMN](../sql-reference/sql/alter-table-column) command:

   CopyExpand

   ```
   ALTER TABLE employee_data MODIFY COLUMN id
     SET MASKING POLICY empl_id_mem_mask;
   ```

   Show lessSee more

   Scroll to top
5. Query the table to test the policy:

   CopyExpand

   ```
   USE ROLE data_engineer;
   SELECT * FROM employee_data;
   ```

   Show lessSee more

   Scroll to top

   This query returns unmasked data.

   However, if you switch roles to the PUBLIC role and repeat the query in this step, the values in the `id` are replaced
   with `NULL`.

## Additional masking policy examples

The following are additional, representative examples that can be used in the body of the Dynamic Data Masking policy.

Allow a production [account](admin-account-identifier) to see unmasked values and all other accounts
(e.g. development, test) to see masked values.

> CopyExpand
>
> ```
> case
>   when current_account() in ('<prod_account_identifier>') then val
>   else '*********'
> end;
> ```
>
> Show lessSee more
>
> Scroll to top

Return NULL for unauthorized users:

> CopyExpand
>
> ```
> case
>   when current_role() IN ('ANALYST') then val
>   else NULL
> end;
> ```
>
> Show lessSee more
>
> Scroll to top

Return a static masked value for unauthorized users:

> CopyExpand
>
> ```
> CASE
>   WHEN current_role() IN ('ANALYST') THEN val
>   ELSE '********'
> END;
> ```
>
> Show lessSee more
>
> Scroll to top

Return a hash value using [SHA2 , SHA2\_HEX](../sql-reference/functions/sha2) for unauthorized users. Using a hashing function in a masking policy may result in collisions; therefore, exercise caution with this approach. For more information, see [Advanced Column-level Security topics](security-column-advanced).

> CopyExpand
>
> ```
> CASE
>   WHEN current_role() IN ('ANALYST') THEN val
>   ELSE sha2(val) -- return hash of the column value
> END;
> ```
>
> Show lessSee more
>
> Scroll to top

Apply a partial mask or full mask:

> CopyExpand
>
> ```
> CASE
>   WHEN current_role() IN ('ANALYST') THEN val
>   WHEN current_role() IN ('SUPPORT') THEN regexp_replace(val,'.+\@','*****@') -- leave email domain unmasked
>   ELSE '********'
> END;
> ```
>
> Show lessSee more
>
> Scroll to top

Using timestamps.

> CopyExpand
>
> ```
> case
>   WHEN current_role() in ('SUPPORT') THEN val
>   else date_from_parts(0001, 01, 01)::timestamp_ntz -- returns 0001-01-01 00:00:00.000
> end;
> ```
>
> Show lessSee more
>
> Scroll to top
>
> Important
>
> Currently, Snowflake does not support different input and output data types in a masking policy, such as defining the masking policy to target a timestamp and return a string (e.g. `***MASKED***`); the input and output data types must match.
>
> A workaround is to cast the actual timestamp value with a fabricated timestamp value. For more information, see [DATE\_FROM\_PARTS](../sql-reference/functions/date_from_parts) and [CAST , ::](../sql-reference/functions/cast).

Using a UDF:

> CopyExpand
>
> ```
> CASE
>   WHEN current_role() IN ('ANALYST') THEN val
>   ELSE mask_udf(val) -- custom masking function
> END;
> ```
>
> Show lessSee more
>
> Scroll to top

On variant data:

> CopyExpand
>
> ```
> CASE
>    WHEN current_role() IN ('ANALYST') THEN val
>    ELSE OBJECT_INSERT(val, 'USER_IPADDRESS', '****', true)
> END;
> ```
>
> Show lessSee more
>
> Scroll to top

Using a custom entitlement table. Note the use of [EXISTS](../sql-reference/operators-subquery) in the WHEN clause. Always use EXISTS when including a subquery in the masking policy body. For more information on subqueries that Snowflake supports, see [Working with Subqueries](querying-subqueries).

> CopyExpand
>
> ```
> CASE
>   WHEN EXISTS
>     (SELECT role FROM <db>.<schema>.entitlement WHERE mask_method='unmask' AND role = current_role()) THEN val
>   ELSE '********'
> END;
> ```
>
> Show lessSee more
>
> Scroll to top

Using [DECRYPT](../sql-reference/functions/decrypt) on previously encrypted data with either [ENCRYPT](../sql-reference/functions/encrypt) or [ENCRYPT\_RAW](../sql-reference/functions/encrypt_raw), with a passphrase on the encrypted data:

> CopyExpand
>
> ```
> case
>   when current_role() in ('ANALYST') then DECRYPT(val, $passphrase)
>   else val -- shows encrypted value
> end;
> ```
>
> Show lessSee more
>
> Scroll to top

Using a [<JavaScript UDF](../developer-guide/udf/javascript/udf-javascript-introduction) on JSON (VARIANT):

> In this example, a JavaScript UDF masks location data in a JSON string. It is important to set the data type as VARIANT in the UDF and
> the masking policy. If the data type in the table column, UDF, and masking policy signature do not match, Snowflake returns an error
> message because it cannot resolve the SQL.
>
> CopyExpand
>
> ```
> -- Flatten the JSON data
>
> create or replace table <table_name> (v variant) as
> select value::variant
> from @<table_name>,
>   table(flatten(input => parse_json($1):stationLocation));
>
> -- JavaScript UDF to mask latitude, longitude, and location data
>
> CREATE OR REPLACE FUNCTION full_location_masking(v variant)
>   RETURNS variant
>   LANGUAGE JAVASCRIPT
>   AS
>   $$
>     if ("latitude" in V) {
>       V["latitude"] = "**latitudeMask**";
>     }
>     if ("longitude" in V) {
>       V["longitude"] = "**longitudeMask**";
>     }
>     if ("location" in V) {
>       V["location"] = "**locationMask**";
>     }
>
>     return V;
>   $$;
>
>   -- Grant UDF usage to ACCOUNTADMIN
>
>   grant ownership on function FULL_LOCATION_MASKING(variant) to role accountadmin;
>
>   -- Create a masking policy using JavaScript UDF
>
>   create or replace masking policy json_location_mask as (val variant) returns variant ->
>     CASE
>       WHEN current_role() IN ('ANALYST') THEN val
>       else full_location_masking(val)
>       -- else object_insert(val, 'latitude', '**locationMask**', true) -- limited to one value at a time
>     END;
> ```
>
> Show lessSee more
>
> Scroll to top

Using the [GEOGRAPHY](../sql-reference/data-types-geospatial) data type:

> In this example, a masking policy uses the [TO\_GEOGRAPHY](../sql-reference/functions/to_geography) function to convert all GEOGRAPHY data in a
> column to a fixed point, the longitude and latitude for Snowflake in San Mateo, California, for users whose CURRENT\_ROLE is not
> `ANALYST`.
>
> > CopyExpand
> >
> > ```
> > create masking policy mask_geo_point as (val geography) returns geography ->
> >   case
> >     when current_role() IN ('ANALYST') then val
> >     else to_geography('POINT(-122.35 37.55)')
> >   end;
> > ```
> >
> > Show lessSee more
> >
> > Scroll to top
>
> Set the masking policy on a column with the GEOGRAPHY data type and set the [GEOGRAPHY\_OUTPUT\_FORMAT](../sql-reference/parameters.html#label-geography-output-format) value for the session to
> `GeoJSON`:
>
> > CopyExpand
> >
> > ```
> > alter table mydb.myschema.geography modify column b set masking policy mask_geo_point;
> > alter session set geography_output_format = 'GeoJSON';
> > use role public;
> > select * from mydb.myschema.geography;
> > ```
> >
> > Show lessSee more
> >
> > Scroll to top
>
> Snowflake returns the following:
>
> > CopyExpand
> >
> > ```
> > ---+--------------------+
> >  A |         B          |
> > ---+--------------------+
> >  1 | {                  |
> >    |   "coordinates": [ |
> >    |     -122.35,       |
> >    |     37.55          |
> >    |   ],               |
> >    |   "type": "Point"  |
> >    | }                  |
> >  2 | {                  |
> >    |   "coordinates": [ |
> >    |     -122.35,       |
> >    |     37.55          |
> >    |   ],               |
> >    |   "type": "Point"  |
> >    | }                  |
> > ---+--------------------+
> > ```
> >
> > Show lessSee more
> >
> > Scroll to top
>
> The query result values in column B depend on the GEOGRAPHY\_OUTPUT\_FORMAT parameter value for the session. For example, if the parameter
> value is set to `WKT`, Snowflake returns the following:
>
> > CopyExpand
> >
> > ```
> > alter session set geography_output_format = 'WKT';
> > select * from mydb.myschema.geography;
> >
> > ---+----------------------+
> >  A |         B            |
> > ---+----------------------+
> >  1 | POINT(-122.35 37.55) |
> >  2 | POINT(-122.35 37.55) |
> > ---+----------------------+
> > ```
> >
> > Show lessSee more
> >
> > Scroll to top

For examples using other context functions and role hierarchy, see [Advanced Column-level Security topics](security-column-advanced).

**Next Topics:**

* [Advanced Column-level Security topics](security-column-advanced)
