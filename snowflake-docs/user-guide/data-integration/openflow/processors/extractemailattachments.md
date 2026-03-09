---
title: "ExtractEmailAttachments 2025.10.9.21"
url: "https://docs.snowflake.com/en/user-guide/data-integration/openflow/processors/extractemailattachments"
---

# ExtractEmailAttachments 2025.10.9.21

Feature — Generally Available

Openflow Snowflake Deployments are available to all accounts in AWS and Azure [Commercial regions](../../../intro-regions.html#label-na-general-regions).

Openflow BYOC deployments are available to all accounts in AWS [Commercial regions](../../../intro-regions.html#label-na-general-regions).

## Bundle

org.apache.nifi | nifi-email-nar

## Description

Extract attachments from a mime formatted email file, splitting them into individual flowfiles.

## Tags

email, split

## Input Requirement

REQUIRED

## Supports Sensitive Dynamic Properties

false

## Relationships

| Name | Description |
| --- | --- |
| attachments | Each individual attachment will be routed to the attachments relationship |
| failure | FlowFiles that could not be parsed |
| original | The original file |

See moreShow less

Expand

## Writes attributes

| Name | Description |
| --- | --- |
| filename | The filename of the attachment |
| email.attachment.parent.filename | The filename of the parent FlowFile |
| email.attachment.parent.uuid | The UUID of the original FlowFile. |
| mime.type | The mime type of the attachment. |

See moreShow less

Expand
