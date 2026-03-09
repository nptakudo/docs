---
title: "ListenOTLP 2025.10.9.21"
url: "https://docs.snowflake.com/en/user-guide/data-integration/openflow/processors/listenotlp"
---

# ListenOTLP 2025.10.9.21

Feature — Generally Available

Openflow Snowflake Deployments are available to all accounts in AWS and Azure [Commercial regions](../../../intro-regions.html#label-na-general-regions).

Openflow BYOC deployments are available to all accounts in AWS [Commercial regions](../../../intro-regions.html#label-na-general-regions).

## Bundle

org.apache.nifi | nifi-opentelemetry-nar

## Description

Collect OpenTelemetry messages over HTTP or gRPC. Supports standard Export Service Request messages for logs, metrics, and traces. Implements OpenTelemetry OTLP Specification 1.0.0 with OTLP/gRPC and OTLP/HTTP. Provides protocol detection using the HTTP Content-Type header.

## Tags

OTLP, OTel, OpenTelemetry, logs, metrics, telemetry, traces

## Input Requirement

FORBIDDEN

## Supports Sensitive Dynamic Properties

false

## Properties

| Property | Description |
| --- | --- |
| Address | Internet Protocol Address on which to listen for OTLP Export Service Requests. The default value enables listening on all addresses. |
| Batch Size | Maximum number of OTLP request resource elements included in each FlowFile produced |
| Client Authentication | Client authentication policy for TLS communication with HTTPS |
| Port | TCP port number on which to listen for OTLP Export Service Requests over HTTP and gRPC |
| Queue Capacity | Maximum number of OTLP request resource elements that can be received and queued |
| SSL Context Service | SSL Context Service enables TLS communication for HTTPS |
| Worker Threads | Number of threads responsible for decoding and queuing incoming OTLP Export Service Requests |

See moreShow less

Expand

## Relationships

| Name | Description |
| --- | --- |
| success | Export Service Requests containing OTLP Telemetry |

See moreShow less

Expand

## Writes attributes

| Name | Description |
| --- | --- |
| mime.type | Content-Type set to application/json |
| resource.type | OpenTelemetry Resource Type: LOGS, METRICS, or TRACES |
| resource.count | Count of resource elements included in messages |

See moreShow less

Expand
