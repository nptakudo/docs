---
title: "Enabling Snowpipe error notifications for Amazon SNS"
url: "https://docs.snowflake.com/en/user-guide/data-load-snowpipe-errors-sns"
---

# Enabling Snowpipe error notifications for Amazon SNS

This topic provides instructions for pushing Snowpipe error notifications to the [Amazon Simple Notification Service](https://docs.aws.amazon.com/sns/) (SNS) service. SNS is a publish/subscribe messaging service.

This feature can push error notifications for the following types of loads:

* Auto-ingest Snowpipe.
* Calls to the Snowpipe `insertFiles` REST API endpoint.
* Loads from Apache Kafka using the Snowflake Connector for Kafka with the Snowpipe ingestion method only.

## Cloud platform support

Currently, this feature is limited to Snowflake accounts hosted on Amazon Web Services (AWS). Snowpipe can load data from files in any supported cloud storage service; however, push notifications to SNS are only supported in Snowflake accounts hosted on AWS.

## Notes

* This feature is implemented using the notification integration object. A notification integration is a Snowflake object that provides an
  interface between Snowflake and third-party cloud message queuing services. A single notification integration can support multiple pipes.
* Snowflake guarantees at-least-once message delivery of error notifications (i.e. multiple attempts are made to deliver messages to ensure at least one attempt succeeds, which can result in duplicate messages).

## Enabling error notifications

### Creating the notification integration

See [Creating a notification integration to send notifications to an Amazon SNS topic](notifications/creating-notification-integration-amazon-sns).

### Enabling error notifications in pipes

A single notification integration can be shared by multiple pipes. The body of error messages identifies the pipe, external stage and path,
and file where the error originated, among other details.

To enable error notifications for a pipe, specify an ERROR\_INTEGRATION parameter value.

Note

Creating or modifying a pipe that references a notification integration requires a role that has the USAGE privilege on the notification
integration. In addition, the role must have either the CREATE PIPE privilege on the schema or the OWNERSHIP privilege on the pipe,
respectively.

Operating on an object in a schema requires at least one privilege on the parent database and at least one privilege on the parent schema.

For instructions on creating a custom role with a specified set of privileges, see [Creating custom roles](security-access-control-configure.html#label-security-custom-role).

For general information about roles and privilege grants for performing SQL actions on
[securable objects](security-access-control-overview.html#label-access-control-securable-objects), see [Overview of Access Control](security-access-control-overview).

#### New pipe

Create a new pipe using [CREATE PIPE](../sql-reference/sql/create-pipe). Note that the configuring automated data loads (i.e. auto-ingest Snowpipe)
requires additional pipe parameters. For instructions, see [Automate continuous data loading with cloud messaging](data-load-snowpipe-auto).

CopyExpand

```
CREATE PIPE <name>
  [ AUTO_INGEST = TRUE | FALSE  ]
  ERROR_INTEGRATION = <integration_name>
  AS <copy_statement>
```

Show lessSee more

Scroll to top

Where:

`ERROR_INTEGRATION = <integration_name>`
:   Name of the notification integration you created in [Create the notification integration](notifications/creating-notification-integration-amazon-sns.html#label-notification-integration-amazon-creating-integration).

The following example shows a CREATE PIPE statement that supports both error notifications and automated data loads:

CopyExpand

```
CREATE PIPE mypipe
  AUTO_INGEST = TRUE
  ERROR_INTEGRATION = my_notification_int
  AS
  COPY INTO mydb.public.mytable
  FROM @mydb.public.mystage;
```

Show lessSee more

Scroll to top

#### Existing pipe

Modify an existing pipe using [ALTER PIPE](../sql-reference/sql/alter-pipe):

CopyExpand

```
ALTER PIPE <name> SET ERROR_INTEGRATION = <integration_name>;
```

Show lessSee more

Scroll to top

Where `<integration_name>` is the name of the notification integration you created in
[Create the notification integration](notifications/creating-notification-integration-amazon-sns.html#label-notification-integration-amazon-creating-integration).

For example:

CopyExpand

```
ALTER PIPE mypipe SET ERROR_INTEGRATION = my_notification_int;
```

Show lessSee more

Scroll to top

## Error notification message payload

The body of error messages identifies the pipe and the errors encountered during a load.

The following is a sample message payload describing a Snowpipe error. The payload can include one or more error messages.

CopyExpand

```
{\"version\":\"1.0\",\"messageId\":\"a62e34bc-6141-4e95-92d8-f04fe43b43f5\",\"messageType\":\"INGEST_FAILED_FILE\",\"timestamp\":\"2021-10-22T19:15:29.471Z\",\"accountName\":\"MYACCOUNT\",\"pipeName\":\"MYDB.MYSCHEMA.MYPIPE\",\"tableName\":\"MYDB.MYSCHEMA.MYTABLE\",\"stageLocation\":\"s3://mybucket/mypath\",\"messages\":[{\"fileName\":\"/file1.csv_0_0_0.csv.gz\",\"firstError\":\"Numeric value 'abc' is not recognized\"}]}
```

Show lessSee more

Scroll to top

Note that you must parse the string into a JSON object to process values in the payload.
