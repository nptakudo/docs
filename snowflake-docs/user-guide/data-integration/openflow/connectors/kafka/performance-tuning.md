---
title: "Performance Tuning of the Openflow Connector for Kafka"
url: "https://docs.snowflake.com/en/user-guide/data-integration/openflow/connectors/kafka/performance-tuning"
---

# Performance Tuning of the Openflow Connector for Kafka

[![Snowflake logo in black (no text)](../../../../../_images/logo-snowflake-black.png)](../../../../../_images/logo-snowflake-black.png) Feature — Generally Available

Snowflake connectors are supported in every region where Snowflake Openflow is available.

[Snowflake Openflow on BYOC deployments](../../about-byoc) are available to all accounts in AWS Commercial Regions only ([Commercial regions](../../../../intro-regions.html#label-na-general-regions)).

[Openflow Snowflake deployments](../../about-spcs) are available to all accounts in AWS and Azure Commercial Regions.

Note

This connector is subject to the [Snowflake Connector Terms](https://www.snowflake.com/legal/snowflake-connector-terms/).

This topic provides guidance for optimizing the performance of the [About Openflow Connector for Kafka](about)
to achieve optimal throughput and minimize latency when ingesting data into Snowflake.

## Performance considerations

When configuring the Openflow Connector for Kafka for optimal performance,
consider the following key factors that impact ingestion throughput and latency:

### Message characteristics

Message size
:   Larger messages may provide better throughput but may require more memory and processing time per message.

Message format
:   JSON messages typically require more processing overhead compared to AVRO
    messages due to schema inference and different serialization/deserialization.

Message volume
:   Higher message volumes benefit from parallel processing and larger batch sizes.

### Kafka configuration

Partition count
:   More partitions allow for higher parallelism but require careful coordination with consumer configuration.

Compression
:   Message compression can reduce network bandwidth but increases CPU overhead.

### Flowfile optimization

Flowfile size
:   For optimal performance, flowfiles should be in the range 1-10 MB rather than containing individual small messages.
    Larger flowfiles reduce processing overhead and improve throughput by minimizing the number of individual file operations.
    Default settings should yield flowfiles in an acceptable size range.
    Small flowfiles are expected when throughput is low.

    If you observe small flowfiles with high throughput, contact [Snowflake Support](https://docs.snowflake.com/user-guide/contacting-support) for assistance.

### Network and infrastructure

Network latency
:   Lower latency between Kafka brokers and Openflow improves overall performance.

Bandwidth
:   Sufficient network bandwidth is critical for high-throughput scenarios.

## Node size recommendations

The following table provides configuration recommendations based on expected workload characteristics.

| Node Size | Recommended For | Message Rate Capacity |
| --- | --- | --- |
| Small (S) | Low to moderate throughput scenarios | Up to 10 MB/s per node |
| Medium (M) | Moderate to high throughput scenarios | Up to 40 MB/s per node |
| Large (L) | High throughput scenarios | Exceeding 40 MB/s per node |

See moreShow less

Expand

## Performance optimization best practices

### Adjusting processor concurrent tasks

To optimize processor performance, you can adjust the number of concurrent tasks for both [ConsumeKafka](../../processors/consumekafka)
and [PutSnowpipeStreaming](../../processors/putsnowpipestreaming) processors.
Concurrent tasks allow processors to run multiple threads simultaneously, improving throughput for high-volume scenarios.

To adjust concurrent tasks for a processor, perform the following tasks:

1. Right-click on the processor in the Openflow canvas.
2. Select Configure from the context menu.
3. Navigate to the Scheduling tab.
4. In the Concurrent tasks field, enter the preferred number of concurrent tasks.
5. Select Apply to save the configuration.

#### Recommended concurrent task settings

| Node Size | ConsumeKafka Tasks | PutSnowpipeStreaming Tasks |
| --- | --- | --- |
| Small (S) | 1 | 1-2 |
| Medium (M) | 2 | 2-4 |
| Large (L) | 4-8 | 4-10 |

See moreShow less

Expand

#### Important considerations

Memory usage
:   Each concurrent task consumes additional memory. Monitor JVM heap usage when increasing concurrent tasks.

Kafka partitions
:   For ConsumeKafka, the number of concurrent tasks multiplied by number of nodes should not exceed the number of total Kafka partitions from all topics.

Start conservatively
:   Begin with lower values and gradually increase while monitoring performance metrics.

### Adjusting Max Batch Size in PutSnowpipeStreaming processor

The Max Batch Size parameter in the PutSnowpipeStreaming processor controls how many records are processed in a single batch. Tuning this parameter helps optimize memory usage and throughput.

The Max Batch Size should be tuned based on average record size to keep total batch size (Max Batch Size × average record size) around 4 MB, not exceeding 16 MB for optimal performance.

For example: If average record size is 1KB, Max Batch Size should be set to 4,000.

To adjust Max Batch Size, perform the following

1. Right-click on the PutSnowpipeStreaming processor.
2. Select Configure from the context menu.
3. Navigate to the Properties tab.
4. Locate the Max Batch Size property.
5. Enter the calculated value based on your average record size.
6. Select Apply to save the changes.

#### Important considerations

* Monitor memory usage and throughput when adjusting batch size.
* Start with these recommended values and adjust only if needed while monitoring performance.

## Scaling considerations

The Openflow Platform uses a Horizontal Pod Autoscaler (HPA) based on CPU utilization and does not support custom metrics-based autoscaling.

Proper configuration of concurrent tasks is critical for effective autoscaling.
If concurrent tasks are set too low, the system may not scale up even when Kafka lag is increasing,
because the CPU utilization threshold required to trigger scaling may not be reached.
This can result in processing delays and accumulated backlogs despite the availability of additional resources.

To ensure optimal scaling behavior, configure concurrent tasks according to the
recommendations in [Adjusting processor concurrent tasks](#id1) and monitor both CPU utilization and Kafka lag metrics.

## Troubleshooting performance issues

### Common performance bottlenecks

#### High consumer lag or Snowflake ingestion bottlenecks

If Kafka consumer lag is increasing or Snowflake ingestion is slow, then perform the following tasks:

1. Verify network connectivity and bandwidth between Openflow and Kafka brokers.
2. Observe if the queue in front of the PutSnowpipeStreaming processor increases.

   1. If yes, consider adding more concurrent tasks for PutSnowpipeStreaming processor in the range limitations provided in [Adjusting processor concurrent tasks](#id1).
   2. If not, consider adding more concurrent tasks for the ConsumeKafka processor in the range limitations provided in [Adjusting processor concurrent tasks](#id1).
3. Consider using a bigger node type.
4. Consider increasing the max number of nodes for the runtime.

#### Memory pressure

If experiencing memory-related issues:

1. Reduce the batch sizes to lower memory footprint.
2. Reduce the number of concurrent tasks for the ConsumeKafka processor.
3. Consider upgrading to a bigger node type.

#### Network latency issues

If experiencing high latency:

1. Verify network configuration between Openflow and external systems.
2. Consider deploying Openflow closer to your Kafka cluster.
3. If working with low throughput, consider lowering the **Client Lag** settings
   in the PutSnowpipeStreaming processor and **Max Uncommitted Time** in the ConsumeKafka processor.

## Next steps

* Start with the recommended configuration for your node size.
* Monitor performance metrics and adjust settings based on observed behavior.
* Consider load testing in a non-production environment before deploying to production.
