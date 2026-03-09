---
title: "Manage private connectivity endpoints: Google Cloud"
url: "https://docs.snowflake.com/en/user-guide/private-manage-endpoints-gcp"
---

# Manage private connectivity endpoints: Google Cloud

[![Snowflake logo in black (no text)](../_images/logo-snowflake-black.png)](../_images/logo-snowflake-black.png) [Business Critical Feature](intro-editions)

This feature requires Business Critical (or higher).

To inquire about upgrading, please contact [Snowflake Support](https://docs.snowflake.com/user-guide/contacting-support).

This topic provides information about how to manage private connectivity endpoints for use with private connectivity to an external service.
The examples are specific to Google Cloud.

## Provision private connectivity endpoints

You can create a private connectivity endpoint by calling the [SYSTEM$PROVISION\_PRIVATELINK\_ENDPOINT](../sql-reference/functions/system_provision_privatelink_endpoint) system
function. For example, for your Snowflake account on Google Cloud:

Connect to a published service:

CopyExpand

```
SELECT SYSTEM$PROVISION_PRIVATELINK_ENDPOINT(
  'projects/my-project/regions/us-west2/serviceAttachments/my-http-server',
  'my-http-server.com'
);
```

Show lessSee more

Scroll to top

After creating the endpoint, the connection must be accepted on Google Cloud by the resource provider.

Provision a private endpoint to allow Snowflake on Google Cloud to connect to a service attachment in your Google Cloud VPC Network:

CopyExpand

```
SELECT SYSTEM$PROVISION_PRIVATELINK_ENDPOINT(
  'projects/my-project/regions/us-east4/serviceAttachments/my-service-attachment',
  'my-service.com'
  );
```

Show lessSee more

Scroll to top

Expand

```
Private endpoint with ID "abcd0000000000000001" to resource "projects/my-project/regions/us-east4/serviceAttachments/my-service-attachment"
was provisioned successfully. Please note the Private Endpoint ID and approve the corresponding connection request in the cloud provider console.
```

Show lessSee more

Scroll to top

Provision a private endpoint to allow Snowflake on Google Cloud to connect to the regional Cloud Key Management Service (Cloud KMS) endpoint:

CopyExpand

```
SELECT SYSTEM$PROVISION_PRIVATELINK_ENDPOINT(
  'cloudkms.us-east4.rep.googleapis.com',
  'cloudkms.us-east4.rep.googleapis.com'
  );
```

Show lessSee more

Scroll to top

Expand

```
Private endpoint with ID "abcd0000000000000001" to resource "cloudkms.us-east4.rep.googleapis.com" was provisioned successfully.
Please note the Private Endpoint ID and approve the corresponding connection request in the cloud provider console.
```

Show lessSee more

Scroll to top

Provision a private endpoint to allow Snowflake to connect to an external stage for Google Cloud:

CopyExpand

```
SELECT SYSTEM$PROVISION_PRIVATELINK_ENDPOINT(
  'storage.us-east4.rep.googleapis.com',
  'storage.us-east4.rep.googleapis.com'
);
```

Show lessSee more

Scroll to top

Expand

```
Private endpoint with ID "abcd0000000000000001" to resource "storage.us-east4.rep.googleapis.com" was provisioned successfully.
Please note the Private Endpoint ID and approve the corresponding connection request in the cloud provider console.
```

Show lessSee more

Scroll to top

Snowflake calls the APIs for the cloud platform that hosts your Snowflake account to create the endpoint. Snowflake also updates the related
networking configurations.

You can provision private connectivity endpoints to Google API [regional service endpoints](https://cloud.google.com/vpc/docs/regional-service-endpoints).
Connections to these Google-managed endpoints are automatically approved.

## Change the host name of a private connectivity endpoint

You can change only the host name of a previously provisioned, private connectivity endpoint without changing its network resource.
Changing the host name for an endpoint tells Snowflake that this endpoint now connects to the same service by using a different host name. To
change the host name, call the [SYSTEM$SET\_PRIVATELINK\_ENDPOINT\_HOSTNAME](../sql-reference/functions/system_set_privatelink_endpoint_hostname) system function.

## List private connectivity endpoints

You can list the private connectivity endpoints that you create by calling the
[SYSTEM$GET\_PRIVATELINK\_ENDPOINTS\_INFO](../sql-reference/functions/system_get_privatelink_endpoints_info) system function. For example, for your Snowflake account on Google Cloud:

SQLReturned value

CopyExpand

```
SELECT SYSTEM$GET_PRIVATELINK_ENDPOINTS_INFO();
```

Show lessSee more

Scroll to top

CopyExpand

```
  [
     {
        "provider_resource_id": "projects/my-project/regions/us-east4/serviceAttachments/...",
        "snowflake_resource_id": "abcd0000000000000001",
        "host": "my-service.com",
        "endpoint_state": "CREATED",
        "status": "ACCEPTED",
     }
  ]
```

Show lessSee more

Scroll to top

Note

You can also query the [OUTBOUND\_PRIVATELINK\_ENDPOINTS](../sql-reference/account-usage/outbound_privatelink_endpoints) view in the
ACCOUNT\_USAGE schema to list the private endpoints in your account.

## Deprovision private connectivity endpoints

You can delete an existing private connectivity endpoint by calling the
[SYSTEM$DEPROVISION\_PRIVATELINK\_ENDPOINT](../sql-reference/functions/system_deprovision_privatelink_endpoint) system function. For example, for your Snowflake account on Google Cloud:

Deprovision a private endpoint to prevent Snowflake on Google Cloud from connecting to the service attachment in your Google Cloud VPC
Network:

CopyExpand

```
SELECT SYSTEM$DEPROVISION_PRIVATELINK_ENDPOINT(
  'projects/my-project/regions/us-east4/serviceAttachments/my-service-attachment'
  );
```

Show lessSee more

Scroll to top

Expand

```
Private endpoint with id "abcd0000000000000001" successfully marked for deletion. Before it is fully deleted in 7-8 days, it can be restored.
```

Show lessSee more

Scroll to top

Deprovision a private endpoint to prevent Snowflake on Google Cloud from connecting to a regional Google service endpoint (CloudKMS):

CopyExpand

```
SELECT SYSTEM$DEPROVISION_PRIVATELINK_ENDPOINT(
 'cloudkms.us-east4.rep.googleapis.com'
 );
```

Show lessSee more

Scroll to top

Expand

```
Private endpoint with id "abcd0000000000000001" successfully marked for deletion. Before it is fully deleted in 7-8 days, it can be restored.
```

Show lessSee more

Scroll to top

Deprovision a private endpoint to prevent Snowflake from connecting to an external stage for Google Cloud:

CopyExpand

```
SELECT SYSTEM$DEPROVISION_PRIVATELINK_ENDPOINT(
 'storage.us-east4.rep.googleapis.com'
 );
```

Show lessSee more

Scroll to top

Expand

```
Private endpoint with id "abcd0000000000000001" successfully marked for deletion. Before it is fully deleted in 7-8 days, it can be restored.
```

Show lessSee more

Scroll to top

## Restore a deprovisioned private connectivity endpoint

You can restore a private connectivity endpoint that you deprovisioned within 7 days of deprovisioning it by calling the
[SYSTEM$RESTORE\_PRIVATELINK\_ENDPOINT](../sql-reference/functions/system_restore_privatelink_endpoint) system function. After 7 days, the endpoint can’t be restored and you
need to provision a new endpoint.

Restore a private endpoint to allow Snowflake on Google Cloud to connect to the Google API Management service in your Google Cloud VPC Network:

CopyExpand

```
SELECT SYSTEM$RESTORE_PRIVATELINK_ENDPOINT(
  'projects/my-project/regions/us-east4/serviceAttachments/my-service-attachment'
);
```

Show lessSee more

Scroll to top

Expand

```
Private endpoint with id ''abcd0000000000000001'' restored successfully.
```

Show lessSee more

Scroll to top

## Usage notes

A Snowflake account that is used to provision private endpoints can only connect with services in the same region. For example, a Snowflake account
in `us-central1` can only provision private endpoints to service attachments and Google regional endpoints also located in `us-central1`.

## Limitations

Cross-regional connections aren’t supported.
