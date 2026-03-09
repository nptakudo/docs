---
title: "GetZendesk 2025.10.9.21"
url: "https://docs.snowflake.com/en/user-guide/data-integration/openflow/processors/getzendesk"
---

# GetZendesk 2025.10.9.21

Feature — Generally Available

Openflow Snowflake Deployments are available to all accounts in AWS and Azure [Commercial regions](../../../intro-regions.html#label-na-general-regions).

Openflow BYOC deployments are available to all accounts in AWS [Commercial regions](../../../intro-regions.html#label-na-general-regions).

## Bundle

org.apache.nifi | nifi-zendesk-nar

## Description

Incrementally fetches data from Zendesk API.

## Tags

zendesk

## Input Requirement

FORBIDDEN

## Supports Sensitive Dynamic Properties

false

## Properties

| Property | Description |
| --- | --- |
| web-client-service-provider | Controller service for HTTP client operations. |
| zendesk-authentication-type-name | Type of authentication to Zendesk API. |
| zendesk-authentication-value-name | Password or authentication token for Zendesk login user. |
| zendesk-export-method | Method for incremental export. |
| zendesk-query-start-timestamp | Initial timestamp to query Zendesk API from in Unix timestamp seconds format. |
| zendesk-resource | The particular Zendesk resource which is meant to be exported. |
| zendesk-subdomain | Name of the Zendesk subdomain. |
| zendesk-user | Login user to Zendesk subdomain. |

See moreShow less

Expand

## State management

| Scopes | Description |
| --- | --- |
| CLUSTER | Paging cursor for Zendesk API is stored. Cursor is updated after each successful request. |

See moreShow less

Expand

## Relationships

| Name | Description |
| --- | --- |
| success | For FlowFiles created as a result of a successful HTTP request. |

See moreShow less

Expand

## Writes attributes

| Name | Description |
| --- | --- |
| record.count | The number of records fetched by the processor. |

See moreShow less

Expand
