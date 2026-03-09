---
title: "External lineage"
url: "https://docs.snowflake.com/en/user-guide/external-lineage"
---

# External lineage

[![Snowflake logo in black (no text)](../_images/logo-snowflake-black.png)](../_images/logo-snowflake-black.png) [Preview Feature](../release-notes/preview-features) — Open

Available to all accounts that are Enterprise Edition (or higher).

To inquire about upgrading, please contact [Snowflake Support](https://docs.snowflake.com/user-guide/contacting-support).

External lineage extends Snowflake’s [native lineage](ui-snowsight-lineage) to include external data sources and
destinations, providing you with visibility into data flows across your entire data ecosystem. It captures lineage from external ETL tools
and source databases to create a unified view of how data moves through your data pipeline.

[OpenLineage](https://openlineage.io) is an open standard for capturing and sharing data lineage information across diverse data
tools and platforms. Snowflake leverages this framework by accepting OpenLineage-compatible events through a REST endpoint. External tools
like dbt and Apache Airflow can use the endpoint to send lineage metadata to Snowflake, which then incorporates this information into the
native lineage graph displayed in Snowsight.

External lineage REST endpoint
:   CopyExpand

    ```
    /api/v2/lineage/external-lineage
    ```

    Show lessSee more

    Scroll to top

Snowflake base URL for REST endpoints
:   CopyExpand

    ```
    https://<account_identifier>.snowflakecomputing.com
    ```

    Show lessSee more

    Scroll to top

    Where `account-identifier` is the [account identifier](admin-account-identifier) of your Snowflake account. You
    can use either the account name format or the account locator format as your account identifier.

    For example, if your account identifier is `myorg-dev_account`, then the base URL of the external lineage
    endpoint is: `https://myorg-dev_account.snowflakecomputing.com`

## External lineage workflow

Implementing external lineage for a data tool consists of the following tasks:

1. [Grant the necessary privileges](#label-external-lineage-privilege) to the user who is authenticating to the external lineage
   endpoint.
2. [Configure your data tool](#label-external-lineage-configure) to send OpenLineage events to the Snowflake REST endpoint.
3. [Choose an authentication method](#label-external-lineage-auth) that works for Snowflake REST APIs, and then configure your data
   tool to use it to authenticate its requests to the external lineage endpoint.
4. Use your data tool as usual. OpenLineage events are sent to Snowflake automatically and appear in the native lineage graph in
   Snowsight.

If you want to test the external lineage endpoint before you configure a data tool to emit OpenLineage events, see
[Send manual requests to establish lineage](#label-external-lineage-configure-manual).

## View your data lineage

To view data lineage in Snowsight, complete the following steps:

1. Sign in to [Snowsight](ui-snowsight-gs.html#label-snowsight-getting-started-sign-in) with the [necessary privileges](ui-snowsight-lineage.html#label-lineage-privileges).
2. In the navigation menu, select Catalog » Database Explorer, and then select a [supported object](ui-snowsight-lineage.html#label-ui-lineage-supported-objects) such as a table or
   view.
3. Select the Lineage tab.

When a data tool sends lineage information to Snowflake, external objects appear in the Snowsight lineage graph and are
labeled as an external node. For example:

![Snowsight lineage graph with external objects](../_images/universal-lineage-external-source.png)

You can select an external object or the line connecting objects to obtain additional information just like you can with native lineage.

## Grant Snowflake privileges

After a REST request is [authenticated](#label-external-lineage-auth), Snowflake checks whether the user associated with the request
is authorized to use external lineage. The user associated with the request must have a role that is granted the INGEST LINEAGE privilege
on the account.

For example, suppose you want requests sent by the service user `dbt_integration_user` to show up in Snowsight lineage. As an
administrator, run the following commands to create a dedicated role, grant it the necessary privilege, and then grant the role to the user:

CopyExpand

```
CREATE ROLE dbt_lineage_role;
GRANT INGEST LINEAGE ON ACCOUNT TO ROLE dbt_lineage_role;
GRANT ROLE dbt_lineage_role TO USER dbt_integration_user;
```

Show lessSee more

Scroll to top

## Configure your data tool

Note

Any data tool with an OpenLineage integration can be configured to send lineage data to Snowflake. For a full list of tools that have an
integration, see [OpenLineage Integrations](https://github.com/OpenLineage/OpenLineage/tree/main/integration#openlineage-integrations).

The following sections provide basic instructions for using external lineage with dbt and Apache AirFlow.

* [Configure dbt to send lineage data to Snowflake](#label-external-lineage-configure-dbt)
* [Configure Airflow to send lineage data to Snowflake](#label-external-lineage-configure-airflow)

### Configure dbt to send lineage data to Snowflake

Note

Configuring dbt to emit OpenLineage events isn’t unique to Snowflake; the only thing specific to Snowflake is the endpoint and base URL
of external lineage.

The following steps provide the minimum configuration you need to set up your dbt environment. Consult the
[OpenLineage dbt documentation](https://openlineage.io/docs/integrations/dbt) and the [OpenLineage specification](https://openlineage.io/apidocs/openapi/) to configure your OpenLineage-dbt integration.

1. Install the [OpenLineage-dbt integration](https://pypi.org/project/openlineage-dbt/):

   CopyExpand

   ```
   pip3 install openlineage-dbt
   ```

   Show lessSee more

   Scroll to top
2. Set your transport variables to specify the [base URL](#label-external-lineage-base-url),
   [endpoint](#label-external-lineage-endpoint), and [security token](#label-external-lineage-auth) for external lineage.

   For example, if the account identifier of your account is `MYORG-DEV_ACCOUNT`, define the following code in your YAML configuration
   file:

   CopyExpand

   ```
   transport:
      type: http
      url: https://MYORG-DEV_ACCOUNT.snowflakecomputing.com
      endpoint: /api/v2/lineage/external-lineage
      auth:
         type: api_key
         apiKey: eyJ0eXAiOiJKV1QiLsecuritytoken...
      compression: gzip
   ```

   Show lessSee more

   Scroll to top
3. Replace `dbt` commands with `dbt-ol`. For example, change the `dbt run` command to `dbt-ol run`.

   These `dbt-ol` commands are required by the OpenLineage-dbt integration, and aren’t unique to Snowflake.

For more information about OpenLineage-dbt integrations, including other methods of setting variables, see the
[OpenLineage dbt documentation](https://openlineage.io/docs/integrations/dbt).

### Configure Airflow to send lineage data to Snowflake

Note

Configuring Apache Airflow to emit OpenLineage events isn’t unique to Snowflake; the only thing specific to Snowflake is the endpoint
and base URL of external lineage.

The following steps provide the minimum configuration you need to set up your Airflow environment for Airflow version 2.7+, which is the
preferred version for OpenLineage. Consult the [OpenLineage Airflow documentation](https://openlineage.io/docs/integrations/airflow) and the [OpenLineage specification](https://openlineage.io/apidocs/openapi/) to
configure your OpenLineage-Airflow integration.

1. Install the [OpenLineage Airflow integration](https://airflow.apache.org/docs/apache-airflow-providers-openlineage/stable/index.html#apache-airflow-providers-openlineage)
   for version 2.7+:

   > CopyExpand
   >
   > ```
   > pip install apache-airflow-providers-openlineage
   > ```
   >
   > Show lessSee more
   >
   > Scroll to top

   If you use an older version of Airflow, install `openlineage-airflow` instead.
2. Set your transport variables to specify the [base URL](#label-external-lineage-base-url),
   [endpoint](#label-external-lineage-endpoint), and [security token](#label-external-lineage-auth) for external lineage.

   For example, if the account identifier of your account is `MYORG-DEV_ACCOUNT`, define the following code in your YAML configuration
   file:

   CopyExpand

   ```
   transport:
      type: http
      url: https://MYORG-DEV_ACCOUNT.snowflakecomputing.com
      endpoint: /api/v2/lineage/external-lineage
      auth:
         type: api_key
         apiKey: eyJ0eXAiOiJKV1QiLsecuritytoken...
      compression: gzip
   ```

   Show lessSee more

   Scroll to top

For more information about OpenLineage-Airflow integrations, including other methods of setting variables, see the
[OpenLineage Airflow documentation](https://openlineage.io/docs/integrations/airflow).

## Choose an authentication method

Snowflake provides multiple ways to authenticate requests to a Snowflake REST endpoint like the one used by external lineage. For a
complete list of authentication methods, see [Authenticating Snowflake REST APIs with Snowflake](../developer-guide/snowflake-rest-api/authentication).

After you select your preferred authentication method, you must generate a security token for a specific user. The token is used to
associate a user with the REST request so that Snowflake can authenticate the user and verify that the user is
[authorized to use external lineage](#label-external-lineage-privilege).

After successfully associating a user with a security token in Snowflake, you need to configure your data tool to authenticate its requests
with this token. For example, if you use a YAML configuration file to set OpenLineage transport variables, use the following code to
specify the security token that is sent in the header of the request:

CopyExpand

```
transport:
   auth:
      type: api_key
      apiKey: eyJ0eXAiOiJKV1QiLsecuritytoken...
```

Show lessSee more

Scroll to top

For other methods of specifying a security token, see the OpenLineage documentation for your data tool.

## Send manual requests to establish lineage

External lineage works by accepting JSON payloads that conform to the OpenLineage specification for COMPLETE events. When integrated with a
data tool, the tool emits these COMPLETE events. But you can also construct a COMPLETE event, then send it to the endpoint by using any tool
or language that can send POST requests to an endpoint.

A valid request consists of the following method, [base URL](#label-external-lineage-base-url), and [endpoint](#label-external-lineage-endpoint):

CopyExpand

```
POST https://<account_identifier>.snowflakecomputing.com/api/v2/lineage/external-lineage
```

Show lessSee more

Scroll to top

Where `account_identifier` is the [account identifier](admin-account-identifier) of your Snowflake account.

The following example shows how to use curl to send lineage information to external lineage:

CopyExpand

```
curl -i -X POST \
 -H "Content-Type: application/json" \
 -H "Authorization: Bearer eyJ0eXAiOiJKV1QiLsecuritytoken..." \
 -H "Accept: application/json" \
 -H "User-Agent: myApplicationName/1.0" \
 -H "X-Snowflake-Authorization-Token-Type: KEYPAIR_JWT" \
 -d "@request_body.json" \
 "https://MYORG-DEV_ACCOUNT.snowflakecomputing.com/api/v2/lineage/external-lineage"
```

Show lessSee more

Scroll to top

Where `request_body.json` conforms to the OpenLineage specification for COMPLETE events. For more information about this JSON payload, see [Payload requirements](#label-external-lineage-configure-manual-payload).

### Authentication and authorization of a manual request

The authentication and authorization of a manual request sent to the external lineage endpoint are the same as those in a request sent from
a data tool.

* The header of the request must include a security token from one of the
  [forms of authentication](../developer-guide/snowflake-rest-api/authentication) supported by Snowflake REST endpoints.
* The user associated with the security token must have the [proper privileges](#label-external-lineage-privilege).

### Payload requirements

When you send the JSON payload in a manual request to the external lineage endpoint, the payload must meet the following requirements:

* Must conform to the [OpenLineage specification](https://openlineage.io/apidocs/openapi/).
* Must be a COMPLETE event. That is, the `eventType` property must be `COMPLETE`. Other types of events are ignored.
* The `inputs` property and `outputs` property must be a mix of Snowflake and external objects. You cannot use external lineage
  to establish lineage between two external objects or between two Snowflake objects. If both properties specify the same type of object
  (Snowflake or external), then the request returns a 404 HTTP status code.
* Must contain the following properties:

  + `inputs`
  + `outputs`
  + `eventType`
  + `eventTime`
  + `job`

  You can optionally include the `run` property, which is useful in identifying the job. The payload can contain additional
  properties, but Snowflake ignores them.

#### Minimal payload example

The following example shows a minimal payload that you can send to the external lineage endpoint:

CopyExpand

```
{
   "eventType": "COMPLETE",
   "eventTime": "2025-03-12T06:51:12.000Z",
   "job": {"namespace": "exampleNamespace", "name": "exampleJob"},
   "run": {"runId": "123e4567-e89b-12d3-a456-426614174000"},
   "producer": "https://github.com/OpenLineage/OpenLineage/blob/v1-0-0/client",
   "schemaURL": "https://openlineage.io/spec/0-0-1/OpenLineage.json",
   "inputs": [{"namespace": "snowflake://AXORG-AX_TEST_PP8", "name": "OL_TEST.OL_TEST_SCH.TEST_DEMO"}],
   "outputs": [{"namespace": "postgres://localhost:5432", "name": "PDB.SCH.OUTPUT"}]
}
```

Show lessSee more

Scroll to top

#### Specifying object types

Within the `outputs` array of the payload, you can use the `facets` field to specify the type of the object, which can be any
user-defined string. For example, the following snippet of the payload specifies that the object is of type VIEW:

CopyExpand

```
"outputs": [
    {
        "namespace": "postgres://db.company.com:5432",
        "name": "db.schema.view",
        "facets": {"datasetType": {"datasetType": "VIEW"}},
    },
],
```

Show lessSee more

Scroll to top

If you don’t specify a `facets` field, the type of object defaults to `External Node`.

#### Specifying multiple inputs

If a payload includes more than one input, the resulting lineage shows the output as a downstream object of both inputs. For example, if a payload has input A and B along with an output C, then the lineage shows both A-C and B-C.

## Send requests to remove lineage

You can send a DELETE request to the external lineage endpoint to remove lineage that was established between a Snowflake object and an
external object.

* To break lineage between the source object and target object, use URL query parameters to specify details about the two objects.
* To break lineage between an object and all of its downstream objects, specify the source object without specifying a target object.
* To remove a target object from the lineage graph regardless of how many objects are upstream of it, specify the target object without
  specifying a source object.

A valid request to remove lineage consists of the following method, [base URL](#label-external-lineage-base-url), and [endpoint](#label-external-lineage-endpoint):

CopyExpand

```
DELETE https://<account_identifier>.snowflakecomputing.com/api/v2/lineage/external-lineage
```

Show lessSee more

Scroll to top

| Query parameter | Description |
| --- | --- |
| `sourceNamespace={namespace}` | Namespace of the source dataset. |
| `sourceName={FQN}` | Fully qualified name of the source dataset. |
| `sourceDatasetType={dataset type}` | Type of the source dataset (for example, TABLE, VIEW, DATASET). By default, the value should be External node. If you provided a value in the `facets` field of the payload when you sent a request to establish lineage, then specify the value that you sent in the payload, not External node. |
| `targetNamespace={namespace}` | Namespace of the target dataset. |
| `targetName={FQN}` | Fully qualified name of the target dataset. |
| `targetDatasetType={dataset type}` | Type of the target dataset (for example, TABLE, VIEW, DATASET). By default, the value should be External Node (`External%20Node`). If you provided a value in the `facets` field of the payload when you sent a request to establish lineage, then specify the value that you sent in the payload, not External node. |

See moreShow less

Expand

Note

The values of the query parameters are case sensitive.

### Access control for removing lineage

The user sending a request to remove lineage between objects must have the DELETE LINEAGE privilege on the account.

## Limitations and considerations

* A Snowflake object must be either the INPUT or the OUTPUT of a COMPLETE event. That is, external lineage doesn’t ingest lineage events
  when neither the input data nor the output data is a Snowflake object.
* Snowflake doesn’t support OpenLineage version 2.
* The retention period for external lineage events is one year.
* Snowflake only recognizes COMPLETE lineage events. All other events emitted by a data tool are ignored.
* Lineage from external sources doesn’t appear in the output of the GET\_LINEAGE function.
* External lineage doesn’t support the lineage of columns.
* The fully qualified name of a dataset — that is, the input or output — can’t exceed 1000 characters.
* You can’t store more than 10,000 events in the same account. If you reach this limit, you’ll have to delete events before adding new ones.
