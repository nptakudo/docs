---
title: "ListenWebSocket 2025.10.9.21"
url: "https://docs.snowflake.com/en/user-guide/data-integration/openflow/processors/listenwebsocket"
---

# ListenWebSocket 2025.10.9.21

Feature — Generally Available

Openflow Snowflake Deployments are available to all accounts in AWS and Azure [Commercial regions](../../../intro-regions.html#label-na-general-regions).

Openflow BYOC deployments are available to all accounts in AWS [Commercial regions](../../../intro-regions.html#label-na-general-regions).

## Bundle

org.apache.nifi | nifi-websocket-processors-nar

## Description

Acts as a WebSocket server endpoint to accept client connections. FlowFiles are transferred to downstream relationships according to received message types as the WebSocket server configured with this processor receives client requests

## Tags

WebSocket, consume, listen, subscribe

## Input Requirement

FORBIDDEN

## Supports Sensitive Dynamic Properties

false

## Properties

| Property | Description |
| --- | --- |
| server-url-path | The WetSocket URL Path on which this processor listens to. Must starts with ‘/’, e.g. ‘/example’. |
| websocket-server-controller-service | A WebSocket SERVER Controller Service which can accept WebSocket requests. |

See moreShow less

Expand

## Relationships

| Name | Description |
| --- | --- |
| binary message | The WebSocket binary message output |
| connected | The WebSocket session is established |
| disconnected | The WebSocket session is disconnected |
| text message | The WebSocket text message output |

See moreShow less

Expand

## Writes attributes

| Name | Description |
| --- | --- |
| websocket.controller.service.id | WebSocket Controller Service id. |
| websocket.session.id | Established WebSocket session id. |
| websocket.endpoint.id | WebSocket endpoint id. |
| websocket.local.address | WebSocket server address. |
| websocket.remote.address | WebSocket client address. |
| websocket.message.type | TEXT or BINARY. |

See moreShow less

Expand
