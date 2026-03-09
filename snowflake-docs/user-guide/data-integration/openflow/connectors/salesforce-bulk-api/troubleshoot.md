---
title: "Troubleshooting the Openflow Connector for Salesforce Bulk API"
url: "https://docs.snowflake.com/en/user-guide/data-integration/openflow/connectors/salesforce-bulk-api/troubleshoot"
---

# Troubleshooting the Openflow Connector for Salesforce Bulk API

[![Snowflake logo in black (no text)](../../../../../_images/logo-snowflake-black.png)](../../../../../_images/logo-snowflake-black.png) [Preview Feature](https://www.snowflake.com/en/legal/optional-offerings/offering-specific-terms/preview-terms-of-service/)

Snowflake connectors are supported in every region where Snowflake Openflow is available.

[Snowflake Openflow on BYOC deployments](../../about-byoc) are available to all accounts in AWS Commercial Regions only ([Commercial regions](../../../../intro-regions.html#label-na-general-regions)).

[Openflow Snowflake deployments](../../about-spcs) are available to all accounts in AWS and Azure Commercial Regions.

Note

This connector is subject to the [Snowflake Connector Terms](https://www.snowflake.com/legal/snowflake-connector-terms/).

This topic describes how to troubleshoot the Openflow Connector for Salesforce Bulk API.

## Monitoring

To track the amount of data being synced from Salesforce to Snowflake, query the event table.
The following example query retrieves relevant logs from the last 30 minutes:

CopyExpand

```
SELECT
  timestamp,
  Deployment_ID,
  Runtime_Key,
  parsed_log:level as log_level,
  parsed_log:loggerName as logger,
  parsed_log:formattedMessage as message,
  parsed_log
FROM (
  SELECT
    timestamp,
    resource_attributes:"openflow.dataplane.id" as Deployment_ID,
    resource_attributes:"k8s.namespace.name" as Runtime_Key,
    TRY_PARSE_JSON(value) as parsed_log
  FROM OPENFLOW.TELEMETRY.EVENTS
  WHERE true
    AND timestamp > dateadd('minutes', -30, sysdate())
    AND record_type = 'LOG'
    AND resource_attributes:"k8s.namespace.name" like 'runtime-%'
  ORDER BY timestamp DESC
)
WHERE true
  AND logger = 'org.apache.nifi.processors.standard.LogMessage'
  AND message LIKE '%SALESFORCE_BULK_API%';
```

Show lessSee more

Scroll to top

## Troubleshooting

Use the following information to troubleshoot issues with the connector.

### Check the connector state

You can examine the connector state to ensure that data is being replicated as expected. The connector maintains a state of current and past operations to ensure no Salesforce changes are missed and to retry bulk job queries if failures occur.

To view the state:

1. Right-click on the canvas and select Controller services.
2. Locate the controller service named Salesforce Bulk Jobs State.
3. In the Salesforce Bulk Jobs State menu, click View state.

The state is a set of key/value pairs where the key is the Salesforce Object type. For
example, the state for the `Account` object might look like the following example:

CopyExpand

```
{"previousLast":"2025-09-30T09:41:23.484406926Z","currentLast":"2025-09-30T09:41:23.484406926Z","status":"COMPLETED"}
```

Show lessSee more

Scroll to top

The `status` can be one of the following:

* `IN_PROGRESS`
* `COMPLETED`
* `FAILED`
* `ABORTED`

If the status is `IN_PROGRESS`, a FlowFile is still being processed for that object type.

Caution

Do not delete flow files manually. This can cause a job to remain in the `IN_PROGRESS` status indefinitely because the state cannot be manually updated.

If this occurs, you must perform a full reload for that object type.

### Force a full load for a given object type

To force the connector to perform a full refresh for one or more object types:

1. Stop all processors in the flow.
2. Ensure that no in-flight FlowFiles are being processed.
3. Right-click on the canvas and select Disable all controller services.
4. Go to Controller services and open the state of the controller service named
   Salesforce Bulk Jobs State.
5. Perform one of the following actions:

   * Select Clear state to clear the entire state. This forces a full load for
     **all** configured Object types fetched by the connector.
   * Select the trash icon next to a specific Object type to clear the state for a
     specific object type only. This forces a full load of that specific object type
     during the next execution of the connector.
6. In the canvas, right-click, select Enable all controller services, and then start all processors.

### If an object type remains in status IN\_PROGRESS

If the state for a given object type is stuck in `IN_PROGRESS` and there are no in-flight FlowFiles for that object type, a FlowFile may have been manually deleted before it could update the status.

In this case, you must perform a full load for that object type to ensure the connector
captures all events.

If the state is stuck in `IN_PROGRESS` but no FlowFiles were manually deleted, contact
[Snowflake Support](https://docs.snowflake.com/user-guide/contacting-support).
