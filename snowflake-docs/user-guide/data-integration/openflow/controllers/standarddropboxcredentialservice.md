---
title: "StandardDropboxCredentialService"
url: "https://docs.snowflake.com/en/user-guide/data-integration/openflow/controllers/standarddropboxcredentialservice"
---

# StandardDropboxCredentialService

Feature — Generally Available

Openflow Snowflake Deployments are available to all accounts in AWS and Azure [Commercial regions](../../../intro-regions.html#label-na-general-regions).

Openflow BYOC deployments are available to all accounts in AWS [Commercial regions](../../../intro-regions.html#label-na-general-regions).

## Description

Defines credentials for Dropbox processors.

## Tags

credentials, dropbox, provider

## Properties

In the list below required Properties are shown with an asterisk (\*).
Other properties are considered optional. The table also indicates any default values, and whether a property supports the NiFi Expression Language.

| Display Name | API Name | Default Value | Allowable Values | Description |
| --- | --- | --- | --- | --- |
| Access Token \* | Access Token |  |  | Access Token of the user’s Dropbox app. See Additional Details for more information about Access Token generation. |
| App Key \* | App Key |  |  | App Key of the user’s Dropbox app. See Additional Details for more information. |
| App Secret \* | App Secret |  |  | App Secret of the user’s Dropbox app. See Additional Details for more information. |
| Refresh Token \* | Refresh Token |  |  | Refresh Token of the user’s Dropbox app. See Additional Details for more information about Refresh Token generation. |

See moreShow less

Expand

## State management

This component does not store state.

## Restricted

This component is not restricted.

## System Resource Considerations

This component does not specify system resource considerations.
