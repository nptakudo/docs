---
title: "Using custom classifiers to implement custom semantic categories"
url: "https://docs.snowflake.com/en/user-guide/classify-custom-using"
---

# Using custom classifiers to implement custom semantic categories

[![Snowflake logo in black (no text)](../_images/logo-snowflake-black.png)](../_images/logo-snowflake-black.png) [Enterprise Edition Feature](intro-editions)

Sensitive data classification requires Enterprise Edition or higher. To inquire about upgrading, please contact [Snowflake Support](https://docs.snowflake.com/user-guide/contacting-support).

The CUSTOM\_CLASSIFIER [class](../sql-reference-classes) allows data engineers to extend their sensitive data classification capabilities
based on their own knowledge of their data. To classify sensitive data into [custom semantic categories](classify-custom),
create an instance of the CUSTOM\_CLASSIFIER class in a schema and call instance methods to add regular
expressions associated with the instance.

For an end-to-end example of using a CUSTOM\_CLASSIFIER instance to create a custom semantic category, see [Example](#label-custom-classification-example).

## Commands and methods

The following methods and SQL commands are supported:

* Commands:

  + [CREATE CUSTOM\_CLASSIFIER](../sql-reference/classes/custom_classifier/commands/create-custom-classifier)
  + [DROP CUSTOM\_CLASSIFIER](../sql-reference/classes/custom_classifier/commands/drop-custom-classifier)
  + [SHOW CUSTOM\_CLASSIFIER](../sql-reference/classes/custom_classifier/commands/show-custom-classifiers)
* Methods:

  + [custom\_classifier!ADD\_REGEX](../sql-reference/classes/custom_classifier/methods/add_regex)
  + [custom\_classifier!DELETE\_CATEGORY](../sql-reference/classes/custom_classifier/methods/delete_category)
  + [custom\_classifier!LIST](../sql-reference/classes/custom_classifier/methods/list)

## Access control

These sections summarize the roles and grants on various objects that you need to use an instance.

### Roles

You can use the following roles with custom classification:

* SNOWFLAKE.CLASSIFICATION\_ADMIN: database role that enables you to create a custom classifier instance.
* `custom_classifier`!PRIVACY\_USER: [instance role](../sql-reference/snowflake-db-classes.html#label-instance-roles) that enables you to call the following methods on
  the instance:

  + ADD\_REGEX
  + LIST
  + DELETE\_CATEGORY
* The account role with the OWNERSHIP privilege on the instance can run these commands:

  + DROP CUSTOM\_CLASSIFIER
  + SHOW CUSTOM\_CLASSIFIER

### Grants

To create and manage instances, you can choose to either grant the CREATE SNOWFLAKE.DATA\_PRIVACY.CUSTOM\_CLASSIFIER
[privilege](../sql-reference/sql/grant-privilege) to a role or grant the PRIVACY\_USER instance role to a role.

You can grant the instance roles to account roles and database roles to enable other users to work with custom classifier instances:

CopyExpand

```
GRANT SNOWFLAKE.DATA_PRIVACY.CUSTOM_CLASSIFIER ROLE <name>!PRIVACY_USER
  TO ROLE <role_name>

REVOKE SNOWFLAKE.DATA_PRIVACY.CUSTOM_CLASSIFIER ROLE <name>!PRIVACY_USER
  FROM ROLE <role_name>

GRANT SNOWFLAKE.DATA_PRIVACY.CUSTOM_CLASSIFIER ROLE <name>!PRIVACY_USER
  TO DATABASE ROLE <database_role_name>

REVOKE SNOWFLAKE.DATA_PRIVACY.CUSTOM_CLASSIFIER ROLE <name>!PRIVACY_USER
  FROM DATABASE ROLE <database_role_name>
```

Show lessSee more

Scroll to top

Where:

`name`
:   Specifies the name of the custom classifier instance.

`role_name`
:   Specifies the name of an account role.

`database_role_name`
:   Specifies the name of a database role.

You must use a warehouse to call methods on the instance.

To grant the custom role `my_classification_role` the required instance role and privileges to create and use an instance of the
CUSTOM\_CLASSIFIER class, execute the following statements:

CopyExpand

```
USE ROLE ACCOUNTADMIN;
GRANT DATABASE ROLE SNOWFLAKE.CLASSIFICATION_ADMIN
  TO ROLE my_classification_role;
GRANT USAGE ON DATABASE mydb TO ROLE my_classification_role;
GRANT USAGE ON SCHEMA mydb.instances TO ROLE my_classification_role;
GRANT USAGE ON WAREHOUSE wh_classification TO ROLE my_classification_role;
```

Show lessSee more

Scroll to top

If you would like to enable a specific role, such as `data_analyst` to use a specific instance, do the following:

CopyExpand

```
GRANT SNOWFLAKE.DATA_PRIVACY.CUSTOM_CLASSIFIER ROLE
  mydb.sch.my_instance!PRIVACY_USER TO ROLE data_analyst;
```

Show lessSee more

Scroll to top

## Example

The high-level approach to classify data with custom classifiers is as follows:

1. Identify a table to classify.
2. Use SQL to do the following:

   1. Create a custom classifier instance.
   2. Add the custom semantic category and regular expressions to the instance.
   3. Classify the table.

Complete these steps to create a custom classifier to classify a table:

1. Consider a table, `data.tables.patient_diagnosis`, in which one of its columns contains diagnostic codes, such as
   [ICD-10 codes](https://en.wikipedia.org/wiki/ICD-10).

   > CopyExpand
   >
   > ```
   > +-------------+----------------------------------------------------------+
   > | ICD_10_CODE | DESCRIPTION                                              |
   > +-------------+----------------------------------------------------------+
   > | G30.9       | Alzheimer's disease, unspecified                         |
   > | G80.8       | Other cerebral palsy                                     |
   > | S13.4XXA    | Sprain of ligaments of cervical spine, initial encounter |
   > +-------------+----------------------------------------------------------+
   > ```
   >
   > Show lessSee more
   >
   > Scroll to top

   This table might also include columns to identify patients, such as first and last name, unique health insurance identifiers, and date
   of birth, that were treated at a medical facility. The data owner can classify the table to ensure that the columns are tagged
   correctly so the table can be monitored.

   In this example, the data owner already has these privileges granted to their role:

   * OWNERSHIP on the table to classify.
   * OWNERSHIP on the schema that contains the table.
   * USAGE on the database that contains the schema and table.
2. Enable the data owner to classify the table by granting the SNOWFLAKE.CLASSIFICATION\_ADMIN database role to the data owner role:

   > CopyExpand
   >
   > ```
   > USE ROLE ACCOUNTADMIN;
   > GRANT DATABASE ROLE SNOWFLAKE.CLASSIFICATION_ADMIN
   >   TO ROLE data_owner;
   > ```
   >
   > Show lessSee more
   >
   > Scroll to top
3. As the data owner, create a schema to store your custom classifier instances:

   > CopyExpand
   >
   > ```
   > USE ROLE data_owner;
   > CREATE SCHEMA data.classifiers;
   > ```
   >
   > Show lessSee more
   >
   > Scroll to top
4. Use the [CREATE CUSTOM\_CLASSIFIER](../sql-reference/classes/custom_classifier/commands/create-custom-classifier) command to create a custom classifier
   instance in the `data.classifiers` schema:

   CopyExpand

   ```
   CREATE OR REPLACE SNOWFLAKE.DATA_PRIVACY.CUSTOM_CLASSIFIER medical_codes();
   ```

   Show lessSee more

   Scroll to top

   You can optionally update your [search path](../sql-reference/snowflake-db-classes.html#label-update-search-path) as follows:

   * Add `SNOWFLAKE.DATA_PRIVACY` so that you don’t have to specify the fully qualified name of the class when creating a new
     instance of the class.
   * Add `DATA.CLASSIFIERS` so that you don’t have to specify the fully qualified name of the instance when calling a method on the
     instance or using a command with the instance.
5. Use a [SHOW CUSTOM\_CLASSIFIER](../sql-reference/classes/custom_classifier/commands/show-custom-classifiers) command to list each instance that you create.
   For example:

   CopyExpand

   ```
   SHOW SNOWFLAKE.DATA_PRIVACY.CUSTOM_CLASSIFIER;
   ```

   Show lessSee more

   Scroll to top

   Returns:

   Expand

   ```
   +----------------------------------+---------------+---------------+-------------+-----------------+---------+-------------+
   | created_on                       | name          | database_name | schema_name | current_version | comment | owner       |
   +----------------------------------+---------------+---------------+-------------+-----------------+---------+-------------+
   | 2023-09-08 07:00:00.123000+00:00 | MEDICAL_CODES | DATA          | CLASSIFIERS | 1.0             | None    | DATA_OWNER  |
   +----------------------------------+---------------+---------------+-------------+-----------------+---------+-------------+
   ```

   Show lessSee more

   Scroll to top
6. Call the [custom\_classifier!ADD\_REGEX](../sql-reference/classes/custom_classifier/methods/add_regex) method on the instance to specify the system tags and regular
   expression to identify ICD-10 codes in a column. The regular expression in this example matches all possible ICD-10 codes. The regular
   expression to match the column name, `ICD.*`, and the comment are optional:

   CopyExpand

   ```
   CALL medical_codes!ADD_REGEX(
     SEMANTIC_CATEGORY => 'ICD_10_CODES',
     PRIVACY_CATEGORY => 'IDENTIFIER',
     VALUE_REGEX => '[A-TV-Z][0-9][0-9AB]\.?[0-9A-TV-Z]{0,4}',
     COL_NAME_REGEX => 'ICD.*',
     DESCRIPTION => 'Add a regex to identify ICD-10 medical codes in a column',
     THRESHOLD => 0.8
   );
   ```

   Show lessSee more

   Scroll to top

   Returns:

   Expand

   ```
   +---------------+
   |   ADD_REGEX   |
   +---------------+
   | ICD_10_CODES  |
   +---------------+
   ```

   Show lessSee more

   Scroll to top

   Tip

   Test the regular expression before adding a regular expression to the custom classifier instance. For example:

   CopyExpand

   ```
   SELECT icd_10_code
   FROM medical_codes
   WHERE icd_10_code REGEXP('[A-TV-Z][0-9][0-9AB]\.?[0-9A-TV-Z]{0,4}');
   ```

   Show lessSee more

   Scroll to top

   Expand

   ```
   +-------------+
   | ICD-10-CODE |
   +-------------+
   | G30.9       |
   | G80.8       |
   | S13.4XXA    |
   +-------------+
   ```

   Show lessSee more

   Scroll to top

   In this query, only valid values that match the regular expression are returned. The query does not return invalid
   values such as `xyz`.

   For details, see [String functions (regular expressions)](../sql-reference/functions-regexp).
7. Call the [custom\_classifier!LIST](../sql-reference/classes/custom_classifier/methods/list) method on the instance to verify the regular expression that
   you added to the instance:

   CopyExpand

   ```
   SELECT medical_codes!LIST();
   ```

   Show lessSee more

   Scroll to top

   Returns:

   Expand

   ```
   +--------------------------------------------------------------------------------+
   | MEDICAL_CODES!LIST()                                                           |
   +--------------------------------------------------------------------------------+
   | {                                                                              |
   |   "ICD-10-CODES": {                                                            |
   |     "col_name_regex": "ICD.*",                                                 |
   |     "description": "Add a regex to identify ICD-10 medical codes in a column", |
   |     "privacy_category": "IDENTIFIER",                                          |
   |     "threshold": 0.8,                                                          |
   |     "value_regex": "[A-TV-Z][0-9][0-9AB]\.?[0-9A-TV-Z]{0,4}"                   |
   |   }                                                                            |
   | }                                                                              |
   +--------------------------------------------------------------------------------+
   ```

   Show lessSee more

   Scroll to top

   To remove a category, call the [custom\_classifier!DELETE\_CATEGORY](../sql-reference/classes/custom_classifier/methods/delete_category) method on the instance.
8. Call the [SYSTEM$CLASSIFY\_SCHEMA](../sql-reference/stored-procedures/system_classify_schema) stored procedure to classify the table.
9. If the instance is no longer needed, use the [DROP CUSTOM\_CLASSIFIER](../sql-reference/classes/custom_classifier/commands/drop-custom-classifier) command to
   remove a custom classifier instance from the system:

   CopyExpand

   ```
   DROP SNOWFLAKE.DATA_PRIVACY.CUSTOM_CLASSIFIER data.classifiers.medical_codes;
   ```

   Show lessSee more

   Scroll to top

## Auditing custom classifiers

You can use the following queries to audit the creation of custom classifier instances, adding regular expressions to instances, and
dropping the instance.

* To audit the creation of custom classifier instances, use the following query:

  CopyExpand

  ```
  SELECT * FROM SNOWFLAKE.ACCOUNT_USAGE.QUERY_HISTORY
  WHERE query_text ILIKE 'create % snowflake.data_privacy.custom_classifier%';
  ```

  Show lessSee more

  Scroll to top
* To audit adding regular expressions to a specific instance, use the following query and replace `DB.SCH.MY_INSTANCE` with the name
  of the instance that you want to audit:

  CopyExpand

  ```
  SELECT
      QUERY_HISTORY.user_name,
      QUERY_HISTORY.role_name,
      QUERY_HISTORY.query_text,
      QUERY_HISTORY.query_id
    FROM
      SNOWFLAKE.ACCOUNT_USAGE.QUERY_HISTORY query_history,
      SNOWFLAKE.ACCOUNT_USAGE.ACCESS_HISTORY access_history,
        TABLE(FLATTEN(input => access_history.direct_objects_accessed)) flattened_value
  WHERE flattened_value.value:"objectName" = 'DB.SCH.MY_INSTANCE!ADD_REGEX'
  AND QUERY_HISTORY.query_id = ACCESS_HISTORY.query_id;
  ```

  Show lessSee more

  Scroll to top
* To audit dropping a custom classifier instance, use the following query:

  CopyExpand

  ```
  SELECT * FROM SNOWFLAKE.ACCOUNT_USAGE.QUERY_HISTORY
  WHERE query_text ILIKE 'drop % snowflake.data_privacy.custom_classifier%';
  ```

  Show lessSee more

  Scroll to top
