---
title: "StandardTableStateService"
url: "https://docs.snowflake.com/en/user-guide/data-integration/openflow/controllers/standardtablestateservice"
---

# StandardTableStateService

Feature — Generally Available

Openflow Snowflake Deployments are available to all accounts in AWS and Azure [Commercial regions](../../../intro-regions.html#label-na-general-regions).

Openflow BYOC deployments are available to all accounts in AWS [Commercial regions](../../../intro-regions.html#label-na-general-regions).

## Description

A controller Service that provides and manages table state. The state is cached and refreshed only when one of set table state method is invoked. This caching method requires that getting or setting state for a given table must be done on the same node. The Tables processing can be partitioned between NiFi nodes, but the get and set state operations for a single table must be associated with a single NiFi node.

## Tags

## State management

This component does not store state.

## Restricted

This component is not restricted.

## System Resource Considerations

This component does not specify system resource considerations.
