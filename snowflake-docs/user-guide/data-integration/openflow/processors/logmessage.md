---
title: "LogMessage 2025.10.9.21"
url: "https://docs.snowflake.com/en/user-guide/data-integration/openflow/processors/logmessage"
---

# LogMessage 2025.10.9.21

Feature — Generally Available

Openflow Snowflake Deployments are available to all accounts in AWS and Azure [Commercial regions](../../../intro-regions.html#label-na-general-regions).

Openflow BYOC deployments are available to all accounts in AWS [Commercial regions](../../../intro-regions.html#label-na-general-regions).

## Bundle

org.apache.nifi | nifi-standard-nar

## Description

Emits a log message at the specified log level

## Tags

attributes, logging

## Input Requirement

REQUIRED

## Supports Sensitive Dynamic Properties

false

## Properties

| Property | Description |
| --- | --- |
| log-level | The Log Level to use when logging the message: [trace, debug, info, warn, error] |
| log-message | The log message to emit |
| log-prefix | Log prefix appended to the log lines. It helps to distinguish the output of multiple LogMessage processors. |

See moreShow less

Expand

## Relationships

| Name | Description |
| --- | --- |
| success | All FlowFiles are routed to this relationship |

See moreShow less

Expand
