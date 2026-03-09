---
title: "GCSFileResourceService"
url: "https://docs.snowflake.com/en/user-guide/data-integration/openflow/controllers/gcsfileresourceservice"
---

# GCSFileResourceService

Feature — Generally Available

Openflow Snowflake Deployments are available to all accounts in AWS and Azure [Commercial regions](../../../intro-regions.html#label-na-general-regions).

Openflow BYOC deployments are available to all accounts in AWS [Commercial regions](../../../intro-regions.html#label-na-general-regions).

## Description

Provides a Google Compute Storage (GCS) file resource for other components.

## Tags

file, gcs, resource

## Properties

In the list below required Properties are shown with an asterisk (\*).
Other properties are considered optional. The table also indicates any default values, and whether a property supports the NiFi Expression Language.

| Display Name | API Name | Default Value | Allowable Values | Description |
| --- | --- | --- | --- | --- |
| Bucket \* | Bucket | ${gcs.bucket} |  | Bucket of the object. |
| Name \* | Name | ${filename} |  | Name of the object. |
| GCP Credentials Provider Service \* | gcp-credentials-provider-service |  |  | The Controller Service used to obtain Google Cloud Platform credentials. |

See moreShow less

Expand

## State management

This component does not store state.

## Restricted

This component is not restricted.

## System Resource Considerations

This component does not specify system resource considerations.
