---
title: "Troubleshooting the Openflow Connector for Kinesis"
url: "https://docs.snowflake.com/en/user-guide/data-integration/openflow/connectors/kinesis/troubleshoot"
---

# Troubleshooting the Openflow Connector for Kinesis

[![Snowflake logo in black (no text)](../../../../../_images/logo-snowflake-black.png)](../../../../../_images/logo-snowflake-black.png) Feature — Generally Available

Snowflake connectors are supported in every region where Snowflake Openflow is available.

[Snowflake Openflow on BYOC deployments](../../about-byoc) are available to all accounts in AWS Commercial Regions only ([Commercial regions](../../../../intro-regions.html#label-na-general-regions)).

[Openflow Snowflake deployments](../../about-spcs) are available to all accounts in AWS and Azure Commercial Regions.

Note

This connector is subject to the [Snowflake Connector Terms](https://www.snowflake.com/legal/snowflake-connector-terms/).

This topic describes how to troubleshoot common issues with the
Openflow Connector for Kinesis.

## Common issues

### Messages are not ingested

**Symptom**

The `ConsumeKinesis` processor in a `Kinesis JSON Source` process group is running, but no data is produced and no error bulletins are emitted.

**Cause**

An error might have occurred in an underlying AWS KCL library, which was not propagated to the Openflow UI.

**Solution**

[Check the KCL logs](#label-kinesis-checking-kcl-logs) to identify the underlying error.

### FlowFile queues are full

**Symptom**

FlowFile queues are filled up and the connector is not processing data fast enough.

**Cause**

The downstream processors cannot keep up with the incoming data rate.
Most likely the slowest processor is `Put Data To Snowflake` of
[PutSnowpipeStreaming](../../processors/putsnowpipestreaming) type
in a `Streaming Destination` process group.

**Solution**

Adjust the number of concurrent tasks for the processor.
Concurrent tasks allow processors to run multiple threads simultaneously, improving throughput for high-volume scenarios.

To adjust concurrent tasks for a processor, perform the following tasks:

1. Right-click the processor in the Openflow canvas.
2. Select Configure from the context menu.
3. Navigate to the Scheduling tab.
4. In the Concurrent tasks field, enter the preferred number of concurrent tasks.
5. Select Apply to save the configuration.

Snowflake recommends the following task count values, although the
correct value might differ for a particular use case:

* 1-2 on small size runtimes
* 2-4 on medium size runtimes
* 6-8 on large size runtimes

After changing the task count, observe the processor to ensure that increasing the tasks count improves the throughput.

## Check KCL logs

The connector uses the [AWS Kinesis Client Library (KCL) v3](https://docs.aws.amazon.com/streams/latest/dev/kcl.html)
under the hood. Errors that occur in KCL are not always propagated to
the Openflow UI, so checking KCL logs might be necessary for troubleshooting.

The KCL logs are stored in a [configured event table](../../monitor).
You can retrieve them with the following query:

CopyExpand

```
SELECT
    timestamp,
    runtime_key,
    resource_attributes,
    log,
    log:formattedMessage,
FROM (
    SELECT
        timestamp,
        resource_attributes,
        resource_attributes:"openflow.dataplane.id" AS deployment_id,
        resource_attributes:"k8s.namespace.name" AS runtime_key,
        resource_attributes:"k8s.pod.name" AS runtime_pod,
        TRY_PARSE_JSON(value) AS log,
    FROM <event_table>
    WHERE TRUE
        AND timestamp > DATEADD(minute, -30, SYSDATE())
        AND record_type = 'LOG'
        AND runtime_key = 'runtime-<runtime_name>'
        AND resource_attributes:"k8s.container.name" ILIKE '%-server'
)
WHERE TRUE
    AND log:loggerName LIKE 'software.amazon.kinesis.%'
    AND log:level IN ('WARN', 'ERROR')
ORDER BY timestamp DESC
;
```

Show lessSee more

Scroll to top

Replace `<event_table>` with a configured event table name and `<runtime_name>` with a runtime name.

## Common KCL errors

This section describes common errors that can appear in the KCL logs and how to resolve them.

### Error: User is not authorized

**Error message**

CopyExpand

```
User: **** is not authorized to perform: kinesis:RegisterStreamConsumer on
resource: arn:aws:kinesis:us-east-2:***:stream/*** because no identity-based
policy allows the kinesis:RegisterStreamConsumer action (Service: Kinesis,
Status Code: 400, Request ID: ***, Extended Request ID: ***)
(SDK Attempt Count: 1)
```

Show lessSee more

Scroll to top

**Cause**

The configured AWS user does not have the necessary permissions to access the Kinesis stream.

**Solution**

Make sure the AWS user is configured with the permissions specified in the
[IAM permissions required for KCL consumer applications](https://docs.aws.amazon.com/streams/latest/dev/kcl-iam-permissions.html).

### Error: UnknownHostException

**Error message**

CopyExpand

```
java.net.UnknownHostException: dynamodb.eu-west-1.amazonaws.com
```

Show lessSee more

Scroll to top

**Cause**

If the runtime is using a Snowflake Deployment, the network rule is most likely misconfigured.

**Solution**

Make sure the required AWS domains are allowlisted in your network rule.
For the list of required domains, see
[Set up Openflow - Snowflake Deployment: Configure allowed domains for Openflow connectors](../../setup-openflow-spcs-sf-allow-list).

### Error: No shards found

**Error message**

CopyExpand

```
java.lang.IllegalStateException: No shards found when attempting to
validate complete hash range.
```

Show lessSee more

Scroll to top

**Cause**

This error can occur if the Kinesis stream does not exist or the AWS region is incorrectly specified.

**Solution**

1. Check the KCL logs for messages like:

   CopyExpand

   ```
   Got ResourceNotFoundException when fetching shard list for stream-name.
   Stream no longer exists.
   ```

   Show lessSee more

   Scroll to top
2. Verify that the stream name is correct and that the stream exists in AWS.
3. Verify that the AWS region is specified correctly in the connector configuration.
