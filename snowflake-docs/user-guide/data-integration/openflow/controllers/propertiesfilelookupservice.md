---
title: "PropertiesFileLookupService"
url: "https://docs.snowflake.com/en/user-guide/data-integration/openflow/controllers/propertiesfilelookupservice"
---

# PropertiesFileLookupService

Feature — Generally Available

Openflow Snowflake Deployments are available to all accounts in AWS and Azure [Commercial regions](../../../intro-regions.html#label-na-general-regions).

Openflow BYOC deployments are available to all accounts in AWS [Commercial regions](../../../intro-regions.html#label-na-general-regions).

## Description

A reloadable properties file-based lookup service

## Tags

cache, enrich, join, key, lookup, properties, reloadable, value

## Properties

In the list below required Properties are shown with an asterisk (\*).
Other properties are considered optional. The table also indicates any default values, and whether a property supports the NiFi Expression Language.

| Display Name | API Name | Default Value | Allowable Values | Description |
| --- | --- | --- | --- | --- |
| Configuration File \* | configuration-file |  |  | A configuration file |

See moreShow less

Expand

## State management

This component does not store state.

## Restricted

## Restrictions

| Required Permission | Explanation |
| --- | --- |
| read filesystem | Provides operator the ability to read from any file that NiFi has access to. |

See moreShow less

Expand

## System Resource Considerations

This component does not specify system resource considerations.
