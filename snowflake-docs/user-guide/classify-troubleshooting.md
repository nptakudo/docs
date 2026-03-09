---
title: "Troubleshooting sensitive data classification"
url: "https://docs.snowflake.com/en/user-guide/classify-troubleshooting"
---

# Troubleshooting sensitive data classification

[![Snowflake logo in black (no text)](../_images/logo-snowflake-black.png)](../_images/logo-snowflake-black.png) [Enterprise Edition Feature](intro-editions)

Sensitive data classification requires Enterprise Edition or higher. To inquire about upgrading, please contact [Snowflake Support](https://docs.snowflake.com/user-guide/contacting-support).

The simplest way to start troubleshooting a table that wasn’t classified by
[sensitive data classification](classify-intro) is to query the table directly (for example, `SELECT * FROM my_table`). If
a table can’t be queried, it can’t be classified.

If an object can’t be classified, Snowflake logs an event to an
[event table](../developer-guide/logging-tracing/event-table-setting-up). By default, the event is logged to the account-level event
table. If you have an event table defined for the failed object’s database, then the event is logged there instead.

In general, there is a delay before Snowflake tries to classify the object again. Every additional failed attempt is logged to the event
table. This delay and retry process continues until the object is fixed or removed from automatic classification.

Note

To help avoid unnecessary costs, Snowflake waits additional time to retry classification for some errors, such as timeouts. For these
timeout errors, Snowflake doesn’t retry classification until all objects are reclassified; the schedule on which objects are reclassified
is controlled by the `maximum_classification_validity_days` key of the classification profile.

If you want prevent classification events from being logged, set the [ENABLE\_AUTOMATIC\_SENSITIVE\_DATA\_CLASSIFICATION\_LOG](../sql-reference/parameters.html#label-enable-automatic-sensitive-data-classification-log) account
parameter to FALSE.

## Listing general errors

The following query returns general errors related to sensitive data classification from the event table:

CopyExpand

```
SELECT
  record_type,
  record:severity_text::string log_level,
  parse_json(value) error_message
  FROM <event_db>.<event_schema>.<event_table>
  WHERE record_type='LOG' and scope:name ='snow.automatic_sensitive_data_classification'
  ORDER BY log_level;
```

Show lessSee more

Scroll to top

For a subset of the possible error messages returned by this query, see [Tag-related error messages](#label-classify-troubleshooting-error-message-examples).

## Listing object-level classification errors

The following query against the event table returns errors related to the classification of a specific object. For example, it returns
errors that occurred when Snowflake tried to classify a specific table.

CopyExpand

```
SELECT
  RECORD_ATTRIBUTES:"object_name"::string AS object_name,
  parse_json(value):"error_message" error_message,
  PARSE_JSON(VALUE):"profile_name" classification_profile_name,
  timestamp,
  FROM <event_db>.<event_schema>.<event_table>
  WHERE record_type='LOG'
    AND scope:name ='snow.automatic_sensitive_data_classification'
    AND RECORD_ATTRIBUTES:"event_type" = 'CLASSIFICATION_ERROR'
  ORDER BY TIMESTAMP DESC;
```

Show lessSee more

Scroll to top

## Tag-related error messages

|  |  |
| --- | --- |
| Error | Expand  ``` "failure_reason":"NO_TAGGING_PRIVILEGE" ```  Show lessSee more  Scroll to top |
| Cause | The role that was used for sensitive data classification does not have the correct privileges to set tags. |
| Solution | Grant the necessary privileges to the role used for sensitive data classification. For more information, see [Tag privileges](object-tagging/work.html#label-object-tags-privileges). |

See moreShow less

Expand

|  |  |
| --- | --- |
| Error | Expand  ``` "failure_reason":"MANUALLY_APPLIED_VALUE_PRESENT" ```  Show lessSee more  Scroll to top |
| Cause | Another tag is manually set on the column. |
| Solution | Determine whether you want to keep the tag that was manually set on the column. If not, unset the tag before classifying the table using automatic classification or the SYSTEM$CLASSIFY stored procedure. |

See moreShow less

Expand

|  |  |
| --- | --- |
| Error | Expand  ``` "failure_reason":"TAG_NOT_ACCESSIBLE_OR_AUTHORIZED" ```  Show lessSee more  Scroll to top |
| Cause | The role that was used for classification cannot access the tag. |
| Solution | * If the tag does not exist, create the tag. * If the tag exists, grant privileges on the tag, or the database and schema that contains the tag, to the role that was used to   classify the database or schema. |

See moreShow less

Expand

For more information about event table messages, see [Viewing log messages](../developer-guide/logging-tracing/logging-accessing-messages).
