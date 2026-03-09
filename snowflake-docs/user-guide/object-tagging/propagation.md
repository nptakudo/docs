---
title: "Automatic tag propagation with user-defined tags"
url: "https://docs.snowflake.com/en/user-guide/object-tagging/propagation"
---

# Automatic tag propagation with user-defined tags

[![Snowflake logo in black (no text)](../../_images/logo-snowflake-black.png)](../../_images/logo-snowflake-black.png) [Enterprise Edition Feature](../intro-editions)

This feature requires Enterprise Edition or higher. To inquire about upgrading,
please contact [Snowflake Support](https://docs.snowflake.com/user-guide/contacting-support).

Tag propagation automatically assigns an [object tag](introduction) to target objects if it is applied to the
source object. For example, you can define tags on a source object, such as a table and its columns, and these tags are
automatically propagated to a target object, such as a view or another table created from the source object.

The advantages of automatic tag propagation include the following:

* Streamlining tag management across objects, particularly when tags are applied to source objects or columns for ease of discovery and data
  protection.
* Ensuring that any policies associated with the tags are automatically applied to the target objects.

Only the tag owner with the account-level APPLY TAG privilege can implement automatic tag propagation.

## Types of propagation

You can choose to propagate a tag when there is an [object dependency](#label-tag-propagation-dependency),
[data movement](#label-tag-propagation-lineage), or both.

### Tag propagation for object dependencies

When tag propagation is configured for object dependencies, a tag is propagated from a source object to all of the target objects that are
based on it. For example, if you set up propagation for a tag `data_sensitivity` on a table `t1`, and then create two views based
on `t1`, the `data_sensitivity` tag is propagated to both views.

Creating a view, secure view, materialized view, or dynamic table from a source object is considered an object dependency.

#### Continuous propagation for object dependencies

When a tag is configured for object dependencies, Snowflake continuously updates the target objects when any of the following occurs:

* The tag is added to a source object or column.
* The value of a tag is updated.
* A tag is removed from a source object or column. In this case, Snowflake removes the tag from the target object or column.

For example, suppose the tag `data_sensitivity` was propagated from table `t1` to view `v2` after executing a CREATE VIEW statement.
When you change the value of `data_sensitivity` on `t1`, the value of the tag on `v2` is also updated.

Automatic tag propagation relies on the existence of the source object. If the source object with tags is dropped, the tags won’t be
propagated to the target object. Because a view depends on its sources, like a base table or other views, tags are propagated only if
the source object exists.

### Tag propagation for data movement

When tag propagation is configured for data movement, a tag is propagated when you move data from a source object to another
object by doing any of the following:

* Executing a [CREATE TABLE … AS SELECT (CTAS)](../../sql-reference/sql/create-table.html#label-ctas-syntax) statement to create a table.
* Executing a CREATE DYNAMIC TABLE statement.
* Executing a Data Manipulation Language (DML) command. Tag propagation occurs for the following DML commands:

  + INSERT
  + MERGE
  + UPDATE
  + COPY INTO

[CREATE TABLE … CLONE](../../sql-reference/sql/create-table.html#label-create-table-clone-syntax) and [CREATE TABLE … LIKE](../../sql-reference/sql/create-table.html#label-create-table-like) do not rely
on the PROPAGATE tag property for tag propagation. When you execute these statements, tags from the source are always assigned to the
target object.

Note

Unlike tag propagation for object dependencies, tags applied to target objects when there is data movement are *not* continuously updated
as tags change on the source object.

## Setting up tag propagation

To enable automatic tag propagation, use the CREATE TAG or ALTER TAG command to set the PROPAGATE property. You can configure the
property so tags are propagated for object dependencies, data movement, or both.

For instructions on setting up tag propagation, see [Define a tag that will automatically propagate](work.html#label-object-tagging-auto-propagate).

## Tag propagation conflicts

Conflicts can occur when a tag is propagated from different source objects to the same target object. If the tag has a different value in
each of the source objects, there is a conflict when that tag is propagated from the source objects to the target object.

Note

If the target object has a tag that was manually applied, the existing tag value takes precedence over a propagated value so there is no
conflict.

If the target object inherits a value from an object higher in the Snowflake hierarchy of objects, the propagated value takes precedence
and there is no conflict.

The ON\_CONFLICT property of a tag determines what happens when there is a conflict. You have three options for handling tag propagation
conflicts:

* **Option 1:** Replace the value of the tag with the string `CONFLICT`. This is the default if you don’t set the ON\_CONFLICT parameter
  of the tag.
* **Option 2:** Replace the value of the tag with a user-defined string. You set the ON\_CONFLICT parameter to this string.

  For example, if you want the value of a tag to be `HIGHLY CONFIDENTIAL` when there is a conflict in values, use the following SQL to
  create the tag:

  CopyExpand

  ```
  CREATE TAG data_sensitivity
    PROPAGATE = ON_DEPENDENCY_AND_DATA_MOVEMENT
    ON_CONFLICT = 'HIGHLY CONFIDENTIAL';
  ```

  Show lessSee more

  Scroll to top
* **Option 3:** Use the order of the values in the tag’s ALLOWED\_VALUES parameter to determine which value to use. Set
  `ON_CONFLICT = ALLOWED_VALUES_SEQUENCE` to implement this strategy.

  For example, suppose you created the tag with the following SQL statement:

  CopyExpand

  ```
  CREATE TAG data_sensitivity
    ALLOWED_VALUES 'confidential', 'internal', 'public'
    PROPAGATE = ON_DEPENDENCY
    ON_CONFLICT = ALLOWED_VALUES_SEQUENCE;
  ```

  Show lessSee more

  Scroll to top

  If there is a conflict for this tag between values `internal` and `public`, the value of the `data_sensitivity` tag will be
  `internal` because it comes before `public` in the list of allowed values.

  Be aware that if you choose to use `ON_CONFLICT = ALLOWED_VALUES_SEQUENCE`, changing the ALLOWED\_VALUES parameter affects how conflicts
  are resolved. For example, if you change the order of the values in the allowed list, then future conflicts could result in a different
  value being assigned to the tag.

To track conflicts associated with tag propagation, see [Using an event table to monitor tag propagation](#label-tag-propagation-event-table).

## Using an event table to monitor tag propagation

You can use an [event table](../../developer-guide/logging-tracing/event-table-setting-up) to collect telemetry data related to tag
propagation. After Snowflake starts collecting the data in the event table, you can query the table, create a stream to track changes, or
set alerts to send notifications when certain events occur.

If you want to collect telemetry data for tag propagation, you must enable the [ENABLE\_TAG\_PROPAGATION\_EVENT\_LOGGING](../../sql-reference/parameters.html#label-enable-tag-propagation-event-logging) account
parameter. To start collecting data, run the following command:

CopyExpand

```
ALTER ACCOUNT SET ENABLE_TAG_PROPAGATION_EVENT_LOGGING = TRUE;
```

Show lessSee more

Scroll to top

If you have an event table set for the tag’s database, then events are logged to that table. Otherwise, events are logged to the default
event table.

### Understanding the events

The following table describes the values in the event table that correspond to tag propagation so you can focus on the appropriate
events. For detailed information about the structure of an event table, see [Event table columns](../../developer-guide/logging-tracing/event-table-columns).

| Event table column | Column field | Field value | Description |
| --- | --- | --- | --- |
| `scope` | `name` | `snow.automatic_tag_propagation` | Indicates that the record relates to automatic tag propagation. |
| `record_attributes` | `tag_name` | `tag_name` | Name of the tag that had an event during propagation. |
| `record_attributes` | `event_type` | `CONFLICT` | Indicates that a conflict occurred when propagating a tag. |
| `record_attributes` | `event_type` | `TAG_PROPAGATION_LIMIT_EXCEEDED` | Indicates that Snowflake didn’t propagate a tag because there were more than 10,000 target objects. |
| `value` | `conflict_values` | [`tag_value`, `tag_value`] | Array of the tag values that were conflicting. |
| `value` | `resolution_type` | `DEFAULT`, `STRING_OVERRIDE`, or `ALLOWED_VALUES_OVERRIDE` | Indicates the action that Snowflake took when a conflict occurred. To understand why the conflict was resolved in a particular way, see [Tag propagation conflicts](#label-tag-propagation-conflicts). |
| `value` | `resolved_values` | `tag_value` | Final value of the tag after Snowflake resolved a conflict. |

See moreShow less

Expand

Use the following examples to better understand how to identify tag propagation events in an event table.

Query: Find all events related to the propagation of tag `TAG1`
:   CopyExpand

    ```
    SELECT
      TIMESTAMP as time,
      RECORD_ATTRIBUTES['event_type'] as event_type,
      VALUE as event_details
    FROM tagging_db.tagging_schema.my_event_table
    WHERE
      SCOPE['name'] = 'snow.automatic_tag_propagation'
      AND RECORD_ATTRIBUTES['tag_name'] = 'TAGGING_DB.TAGGING_SCHEMA.TAG1';
    ```

    Show lessSee more

    Scroll to top

Query: Find all tags that had a conflict when propagated
:   CopyExpand

    ```
    SELECT
      DISTINCT RECORD_ATTRIBUTES['tag_name'] as tags,
      VALUE['conflict_values'] as conflicting_tag_values,
      VALUE['resolution_type'] as resolution_type,
      VALUE['resolved_value'] as resolved_value,
    FROM tagging_db.tagging_schema.my_event_table
    WHERE
      SCOPE['name'] = 'snow.automatic_tag_propagation'
      AND RECORD_ATTRIBUTES['event_type'] = 'CONFLICT';
    ```

    Show lessSee more

    Scroll to top

Query: Find entities that had conflicts when the tag `TAG1` was propagated
:   CopyExpand

    ```
    SELECT
      TIMESTAMP as time,
      RECORD_ATTRIBUTES['entity_name'] as entity_name,
      RECORD_ATTRIBUTES['entity_domain'] as entity_domain,
    FROM tagging_db.tagging_schema.my_event_table
    WHERE
      SCOPE['name'] = 'snow.automatic_tag_propagation'
      AND RECORD_ATTRIBUTES['tag_name'] = 'TAGGING_DB.TAGGING_SCHEMA.TAG1'
      AND RECORD_ATTRIBUTES['event_type'] = 'CONFLICT';
    ```

    Show lessSee more

    Scroll to top

### Severity of events

> Tag propagation events are logged only if the [LOG\_LEVEL parameter](../../sql-reference/parameters.html#label-log-level) governing the table is configured to show events
> of that severity level. Use the following table to determine the severity level of tag propagation events.

| Event type | Resolution type | Severity |
| --- | --- | --- |
| `CONFLICT` | `default` | WARN |
|  | `string_override` | INFO |
|  | `allowed_values_override` | INFO |
| `TAG_PROPAGATION_LIMIT_EXCEEDED` | n/a | ERROR |

See moreShow less

Expand

## Supported objects

Tag propagation from source to target is supported for the following object types:

* Columns
* The following types of tables:

  + Tables
  + Dynamic tables - Creating a dynamic table is considered both an object dependency and data movement for the purposes of tag propagation.
  + External tables
  + Iceberg tables
  + Temp/transient tables
* The following types of views:

  + Views
  + Secure views
  + Materialized views

## Limitations and considerations

* System tags are not propagated.
* [Inherited tags](inheritance) are not propagated.
* Tags are not propagated from a share to local objects.
* The number of tags on an object cannot exceed the [standard limit](introduction.html#label-object-tagging-quota).
* A tag cannot be propagated to more than 10,000 target objects. You can
  [use the event table](#label-tag-propagation-event-table) to find out if propagation failed for this reason.
* With tag propagation for object dependencies, a tag can be applied to both the source table and target views. If the tag is associated with
  a masking policy, there could be consequences associated with duplicate execution of the policy.
