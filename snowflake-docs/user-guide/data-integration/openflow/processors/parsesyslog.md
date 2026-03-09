---
title: "ParseSyslog 2025.10.9.21"
url: "https://docs.snowflake.com/en/user-guide/data-integration/openflow/processors/parsesyslog"
---

# ParseSyslog 2025.10.9.21

Feature — Generally Available

Openflow Snowflake Deployments are available to all accounts in AWS and Azure [Commercial regions](../../../intro-regions.html#label-na-general-regions).

Openflow BYOC deployments are available to all accounts in AWS [Commercial regions](../../../intro-regions.html#label-na-general-regions).

## Bundle

org.apache.nifi | nifi-standard-nar

## Description

Attempts to parses the contents of a Syslog message in accordance to RFC5424 and RFC3164 formats and adds attributes to the FlowFile for each of the parts of the Syslog message. Note: Be mindfull that RFC3164 is informational and a wide range of different implementations are present in the wild. If messages fail parsing, considering using RFC5424 or using a generic parsing processors such as ExtractGrok.

## Tags

attributes, event, logs, message, syslog, system

## Input Requirement

REQUIRED

## Supports Sensitive Dynamic Properties

false

## Properties

| Property | Description |
| --- | --- |
| Character Set | Specifies which character set of the Syslog messages |

See moreShow less

Expand

## Relationships

| Name | Description |
| --- | --- |
| failure | Any FlowFile that could not be parsed as a Syslog message will be transferred to this Relationship without any attributes being added |
| success | Any FlowFile that is successfully parsed as a Syslog message will be to this Relationship. |

See moreShow less

Expand

## Writes attributes

| Name | Description |
| --- | --- |
| syslog.priority | The priority of the Syslog message. |
| syslog.severity | The severity of the Syslog message derived from the priority. |
| syslog.facility | The facility of the Syslog message derived from the priority. |
| syslog.version | The optional version from the Syslog message. |
| syslog.timestamp | The timestamp of the Syslog message. |
| syslog.hostname | The hostname or IP address of the Syslog message. |
| syslog.sender | The hostname of the Syslog server that sent the message. |
| syslog.body | The body of the Syslog message, everything after the hostname. |

See moreShow less

Expand

## See also

* [org.apache.nifi.processors.standard.ListenSyslog](listensyslog)
* [org.apache.nifi.processors.standard.PutSyslog](putsyslog)
