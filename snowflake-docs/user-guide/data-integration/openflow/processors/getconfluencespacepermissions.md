---
title: "GetConfluenceSpacePermissions 2025.10.9.21"
url: "https://docs.snowflake.com/en/user-guide/data-integration/openflow/processors/getconfluencespacepermissions"
---

# GetConfluenceSpacePermissions 2025.10.9.21

Feature — Generally Available

Openflow Snowflake Deployments are available to all accounts in AWS and Azure [Commercial regions](../../../intro-regions.html#label-na-general-regions).

Openflow BYOC deployments are available to all accounts in AWS [Commercial regions](../../../intro-regions.html#label-na-general-regions).

## Bundle

com.snowflake.openflow.runtime | runtime-atlassian-processors-nar

## Description

Processor downloading Confluence space permissions.

## Tags

Preview, atlassian, confluence, permissions, space

## Input Requirement

REQUIRED

## Supports Sensitive Dynamic Properties

false

## Properties

| Property | Description |
| --- | --- |
| Confluence Client Service | Controller service for managing connections to Confluence |
| Confluence Space ID | Identifier of the Confluence Space. |

See moreShow less

Expand

## Relationships

| Name | Description |
| --- | --- |
| failure | Failed to fetch and parse Confluence space permissions. |
| retry | Retryable failure occurred, e.g. rate limiting |
| space not found | Confluence space not found |
| success | Successfully fetched Confluence space permissions. |

See moreShow less

Expand

## Writes attributes

| Name | Description |
| --- | --- |
| confluence.permissions.users | IDs of users with permissions to the Confluence space |
| confluence.permissions.emails | Emails of users with permissions to the Confluence space |
| confluence.permissions.groups | Groups with permissions to the Confluence space |

See moreShow less

Expand
