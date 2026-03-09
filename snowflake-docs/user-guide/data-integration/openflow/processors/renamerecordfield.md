---
title: "RenameRecordField 2025.10.9.21"
url: "https://docs.snowflake.com/en/user-guide/data-integration/openflow/processors/renamerecordfield"
---

# RenameRecordField 2025.10.9.21

Feature — Generally Available

Openflow Snowflake Deployments are available to all accounts in AWS and Azure [Commercial regions](../../../intro-regions.html#label-na-general-regions).

Openflow BYOC deployments are available to all accounts in AWS [Commercial regions](../../../intro-regions.html#label-na-general-regions).

## Bundle

org.apache.nifi | nifi-standard-nar

## Description

Renames one or more fields in each Record of a FlowFile. This Processor requires that at least one user-defined Property be added. The name of the Property should indicate a RecordPath that determines the field that should be updated. The value of the Property is the new name to assign to the Record Field that matches the RecordPath. The property value may use Expression Language to reference FlowFile attributes as well as the variables `field.name`, `field.value`, `field.type`, and `record.index`

## Tags

avro, csv, field, generic, json, log, logs, record, rename, schema, update

## Input Requirement

REQUIRED

## Supports Sensitive Dynamic Properties

false

## Properties

| Property | Description |
| --- | --- |
| Record Reader | Specifies the Controller Service to use for reading incoming data |
| Record Writer | Specifies the Controller Service to use for writing out the records |

See moreShow less

Expand

## Relationships

| Name | Description |
| --- | --- |
| failure | If a FlowFile cannot be transformed from the configured input format to the configured output format, the unchanged FlowFile will be routed to this relationship |
| success | FlowFiles that are successfully transformed will be routed to this relationship |

See moreShow less

Expand

## Writes attributes

| Name | Description |
| --- | --- |
| record.index | This attribute provides the current row index and is only available inside the literal value expression. |

See moreShow less

Expand

## Use cases

|  |
| --- |
| Rename a field in each Record to a specific, known name. |
| Rename a field in each Record to a name that is derived from a FlowFile attribute. |
| Rename a field in each Record to a new name that is derived from the current field name. |

See moreShow less

Expand

## See also

* [org.apache.nifi.processors.standard.RemoveRecordField](removerecordfield)
* [org.apache.nifi.processors.standard.UpdateRecord](updaterecord)
