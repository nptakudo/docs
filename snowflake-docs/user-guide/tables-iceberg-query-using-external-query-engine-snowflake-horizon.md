---
title: "Query Apache Iceberg™ tables with an external engine through Snowflake Horizon Catalog"
url: "https://docs.snowflake.com/en/user-guide/tables-iceberg-query-using-external-query-engine-snowflake-horizon"
---

# Query Apache Iceberg™ tables with an external engine through Snowflake Horizon Catalog

Feature — Generally Available

* Available on Amazon S3, Google Cloud, or Microsoft Azure in all commercial regions.
* Available on FedRAMP (Moderate) deployments on AWS Commercial Gov (US) in the us-east-1 and us-west-2 regions.

Query Snowflake-managed Apache Iceberg™ tables by using an external query engine through
Snowflake Horizon Catalog. To ensure this interoperability with external engines, [Apache Polaris™ (incubating)](https://github.com/apache/polaris)
is integrated into Horizon Catalog. In addition, Horizon Catalog exposes the Apache Iceberg™ REST API (Horizon Iceberg REST Catalog API). This
API lets you read the tables by using external query engines.

To query Snowflake-managed Iceberg tables with an external query engine, you can use this feature instead of
[syncing Snowflake-managed Iceberg tables with Snowflake Open Catalog](tables-iceberg-open-catalog-sync). For more information about Open
Catalog, see [Snowflake Open Catalog overview](opencatalog/overview).

By connecting an external query engine to Iceberg tables through Horizon Catalog, you can perform the following tasks:

* Use any external query engine that supports the open Iceberg REST protocol to query these tables, such as Apache Spark™.
* Query any existing and new Snowflake-managed Iceberg tables in a new or existing Snowflake account by using a single Horizon Catalog endpoint.
* Query the tables by using your existing users, roles, policies, and authentication in Snowflake.
* Use vended credentials.

For more information about Snowflake Horizon Catalog, see [Snowflake Horizon Catalog](snowflake-horizon).

The following diagram shows external query engines reading Snowflake-managed Iceberg tables through Horizon Catalog and Snowflake reading and
writing to these tables:

[![Diagram that shows external query engines reading Snowflake-managed Iceberg tables through Horizon Catalog and Snowflake reading and writing to these tables.](../_images/tables-iceberg-query-using-external-query-engine-snowflake-horizon-diagram.svg)](../_images/tables-iceberg-query-using-external-query-engine-snowflake-horizon-diagram.svg)

## Billing

* The Horizon Iceberg REST Catalog API is available in all Snowflake editions.
* The API requests are billed as 0.5 credit per million calls and charged as Cloud Services.
* For cross-region data access, standard cross-region data egress charges as stated in the [Snowflake Service Consumption Table](https://www.snowflake.com/legal-files/CreditConsumptionTable.pdf) are applicable.

Note

Billing for this feature is scheduled to begin in mid-2026, subject to change.

## Supported external engines and catalogs

The following tables, although not exhaustive, show many external engines and catalogs that integrate with the Horizon Iceberg REST Catalog API.
This integration enables access to Snowflake managed Iceberg tables through external systems.

### Supported external engines

The following external query engines integrate with the Horizon Iceberg REST Catalog API:

| Product | Access Snowflake-managed Iceberg tables through Horizon Catalog |
| --- | --- |
| Apache Doris™ | ✔ |
| Apache Flink™ | ✔ |
| Apache Spark™ | ✔ |
| Dremio | ✔ |
| DuckDB | ✔ |
| PyIceberg | ✔ |
| StarRocks | ✔ |
| Trino | ✔ |

See moreShow less

Expand

### Supported external catalogs

The following external catalogs integrate with the Horizon Iceberg REST Catalog API:

| Product | Access Snowflake-managed Iceberg tables through Horizon Catalog | Comment |
| --- | --- | --- |
| Apache Polaris™ | ✔ |  |
| AWS Glue | ✔ | For instructions on how to configure this integration, see [Access Snowflake Horizon Catalog data using catalog federation in the AWS Glue Data Catalog](https://aws.amazon.com/blogs/big-data/access-snowflake-horizon-catalog-data-using-catalog-federation-in-the-aws-glue-data-catalog/) in the AWS Big Data Blog. |
| Palantir Foundry | ✔ | For instructions on how to configure this integration, see [Iceberg tables (virtual tables only)](https://www.palantir.com/docs/foundry/available-connectors/snowflake#iceberg-tables-virtual-tables-only) in the Palantir documentation. |
| Databricks Unity Catalog | Not announced |  |
| Google BigLake Metastore | In development |  |
| Microsoft Fabric / Synapse | In development |  |

See moreShow less

Expand

## Prerequisites

Retrieve the account identifier for your Snowflake account that contains the Iceberg tables that you want to query. For instructions,
see [Account identifiers](admin-account-identifier). You specify this identifier when you
[connect an external query engine to your Iceberg tables](#label-tables-iceberg-query-using-external-query-engine-snowflake-horizon-connect-external-query-engine).

Tip

To get your account identifier by using SQL, you can run the following command:

CopyExpand

```
SELECT CURRENT_ORGANIZATION_NAME() || '-' || CURRENT_ACCOUNT_NAME();
```

Show lessSee more

Scroll to top

## (Optional) Private connectivity

For secure connectivity, consider configuring [Inbound](private-connectivity-inbound) and
[Outbound](private-connectivity-outbound) private connectivity for your Snowflake account while you access the
Horizon Catalog endpoint.

Note

Private connectivity is only supported for Snowflake-managed Iceberg tables stored on Amazon S3 or Azure Storage (ADLS).

## Workflow for querying Iceberg tables by using an external query engine

To query Iceberg tables by using an external query engine, complete the following steps:

1. [Create Iceberg tables](#label-tables-iceberg-query-using-external-query-engine-snowflake-horizon-create-tables)
2. [Configure access control](#label-tables-iceberg-query-using-external-query-engine-snowflake-horizon-configure-access-control)
3. [Obtain an access token for authentication](#label-tables-iceberg-query-using-external-query-engine-snowflake-horizon-generate-access-token)
4. [Verify access token permissions](#label-tables-iceberg-query-using-external-query-engine-snowflake-horizon-verify-access-token-permissions)
5. [(Optional) Configure data protection policies](#label-tables-iceberg-query-using-external-query-engine-snowflake-horizon-configure-access-policies)
6. [Connect an external query engine to Iceberg tables through Horizon Catalog](#label-tables-iceberg-query-using-external-query-engine-snowflake-horizon-connect-external-query-engine)
7. [Query Iceberg tables](#label-tables-iceberg-query-using-external-query-engine-snowflake-horizon-query-tables)

## Step 1: Create Iceberg tables

Important

If you already have Snowflake-managed Iceberg tables you want to query, you can skip this step.

In this step, you create Snowflake-managed Iceberg tables that use Snowflake as the catalog, so you can query them with an external
query engine. For instructions, see the following topics:

* [Tutorial: Create your first Apache Iceberg™ table](tutorials/create-your-first-iceberg-table): A tutorial that shows how to create a database, create a Snowflake-managed Iceberg table, and load data into the table.
* [Create a Snowflake-managed Iceberg table](tables-iceberg-create.html#label-tables-iceberg-create-snowflake-catalog): Example code for creating a Snowflake-managed Iceberg table.

## Step 2: Configure access control

Important

If you already have roles that are configured with access to the Iceberg tables that you want to query, you can skip this step.

In this step, you configure access control for the Snowflake-managed Iceberg tables that you want to query with an external query engine.
For example, you can set up the following roles in Snowflake:

* data\_engineer role, which has access to all schemas and all Snowflake-managed Iceberg tables in a database.
* data\_analyst role, which has access to one schema in the database and only access to two Snowflake-managed Iceberg tables within that schema.

### Configure access to your Iceberg tables

To query Iceberg tables, the role used to perform the operation must have the USAGE privilege on the external volume that you use to connect
to your external cloud storage.

The following example grants the USAGE privilege for an external volume named `my_ext_vol` to a role named `data_engineer`.

CopyExpand

```
GRANT USAGE ON EXTERNAL VOLUME my_ext_vol TO ROLE data_engineer;
```

Show lessSee more

Scroll to top

For more information about the USAGE privilege for external volumes, see [External volume privileges](security-access-control-privileges.html#label-external-volume-privileges).

Note

To query Iceberg tables, the role used to perform the operation must also have the SELECT privilege on the Iceberg table and the USAGE and
MONITOR privileges on the parent database and schema. For an example of granting these privileges to a role, see
[Example: Set up a service account user](#label-tables-iceberg-query-using-external-query-engine-snowflake-horizon-set-up-service-account-user).

### Example: Set up a service account user

The following example sets up a service account user in Snowflake with read-only access to an Iceberg table, as follows:

* Creates a `data_engineer` role.
* Grants the `data_engineer` role the USAGE privilege on the `my_ext_vol` external volume.
* Grants the `data_engineer` role USAGE and MONITOR privileges on the `iceberg_test_db` database and its `public` schema.
* Grants SELECT privileges on the `test_table` Iceberg table.
* Creates a service user named `horizon_rest_srv_account_user` and assigns the `data_engineer` role to that user.

CopyExpand

```
CREATE OR REPLACE ROLE data_engineer;

GRANT USAGE ON EXTERNAL VOLUME my_ext_vol TO ROLE data_engineer;

GRANT USAGE,MONITOR ON DATABASE iceberg_test_db TO ROLE data_engineer;
GRANT USAGE,MONITOR ON SCHEMA iceberg_test_db.public TO ROLE data_engineer;

GRANT SELECT ON TABLE iceberg_test_db.public.test_table TO ROLE data_engineer;

CREATE OR REPLACE USER horizon_rest_srv_account_user TYPE=SERVICE DEFAULT_ROLE=data_engineer;

GRANT ROLE data_engineer TO USER horizon_rest_srv_account_user;
```

Show lessSee more

Scroll to top

### (Optional) Apply future grants on Iceberg tables

To ensure access to any new Iceberg tables created in a schema, use the
[GRANT … ON FUTURE ICEBERG TABLES](../sql-reference/sql/grant-privilege.html#label-grant-privilege-schema-future-grants) syntax.

The following example grants the `data_engineer` role access to any Iceberg tables created under a schema named `my_schema`.

CopyExpand

```
GRANT SELECT, REFERENCES ON FUTURE ICEBERG TABLES IN SCHEMA my_db.my_schema TO ROLE data_engineer;
```

Show lessSee more

Scroll to top

For more information about access control in Snowflake, see the following topics:

* [Overview of Access Control](security-access-control-overview)
* [Configuring access control](security-access-control-configure)

## Step 3: Obtain an access token for authentication

In this step, you obtain an access token, which you must have to authenticate to the Horizon Catalog endpoint for your Snowflake account. You
need to obtain an access token for each user — service or human — and role that is configured with access to Snowflake-managed Iceberg tables. For example, you need to
obtain one access token for a user with DATA\_ENGINEER role and another user with a DATA\_ANALYST role.

You specify this access token later when you
[connect an external query engine to Iceberg tables through Horizon Catalog](#label-tables-iceberg-query-using-external-query-engine-snowflake-horizon-connect-external-query-engine).

You can obtain an access token by using one of the following authentication options:

* [External OAuth](#label-tables-iceberg-query-using-external-query-engine-snowflake-horizon-external-oauth)
* [Key-pair authentication](#label-tables-iceberg-query-using-external-query-engine-snowflake-horizon-key-pair)
* [Programmatic access token (PAT)](#label-tables-iceberg-query-using-external-query-engine-snowflake-horizon-pat)

### External OAuth

If you’re using External OAuth, generate an access token for your identity provider. For instructions, see [External OAuth overview](oauth-ext-overview).

Note

For External OAuth, alternatively, you can configure your connection to the engine with automatic token refresh instead of specifying
an access token.

### Key-pair authentication

If you use key-pair authentication, to obtain an access token, you sign a JSON web token (JWT) with your
private key.

The following steps cover how to generate an access token for key-pair authentication:

1. [Configure key-pair authentication](#label-tables-iceberg-query-using-external-query-engine-snowflake-horizon-key-pair-configure)
2. [Grant a role to the user](#label-tables-iceberg-query-using-external-query-engine-snowflake-horizon-key-pair-grant-role)
3. [Generate a JSON Web Token (JWT)](#label-tables-iceberg-query-using-external-query-engine-snowflake-horizon-key-pair-generate-jwt)
4. [Generate an access token](#label-tables-iceberg-query-using-external-query-engine-snowflake-horizon-key-pair-generate-access-token)

#### Step 1: Configure key-pair authentication

In this step, you perform the following tasks:

* Generate a private key
* Generate a public key
* Store the private and public keys securely
* Grant the privilege to assign a public key to a Snowflake user
* Assign the public key to a Snowflake user
* Verify the user’s public key fingerprint

For instructions, see [Configuring key-pair authentication](key-pair-auth.html#label-configuring-key-pair-authentication).

#### Step 2: Grant a role to the user

Run the [GRANT ROLE](../sql-reference/sql/grant-role) command to grant the Snowflake role that has privileges to the tables you want to query to the
key-pair authentication user. For example, to grant the ENGINEER role to the `my_service_user` user, run
the following command:

CopyExpand

```
GRANT ROLE ENGINEER to user my_service_user;
```

Show lessSee more

Scroll to top

#### Step 3: Generate a JSON Web Token (JWT)

In this step, you use SnowSQL to generate a JSON Web Token (JWT) for key-pair authentication.

Note

* You must have [SnowSQL](https://www.snowflake.com/developers/downloads/snowsql/) installed on your machine.
* Alternatively, you can use Python, Snowflake CLI, Java, or Node.js to generate a JWT. For an example, see the following sections:

  + [Python example](../developer-guide/sql-api/authenticating.html#label-sql-api-authenticating-key-pair-python)
  + [Snowflake CLI example](../developer-guide/sql-api/authenticating.html#label-sql-api-authenticating-key-pair-snowcli)
  + [Java example](../developer-guide/sql-api/authenticating.html#label-sql-api-authenticating-key-pair-java)
  + [Node.js example](../developer-guide/sql-api/authenticating.html#label-sql-api-authenticating-key-pair-nodejs)

Use SnowSQL to generate a JWT:

CopyExpand

```
snowsql --private-key-path "<private_key_file>" \
  --generate-jwt \
  -h "<account_identifier>.snowflakecomputing.com" \
  -a "<account_locator>" \
  -u "<user_name>"
```

Show lessSee more

Scroll to top

Where:

* `<private_key_file>` is the path to your private key file that corresponds to the public key assigned to your Snowflake user.
  For example: `/Users/jsmith/.ssh/rsa_key.p8`.
* `<account_identifier>` is the account identifier for your Snowflake account, in the format `<organization_name>-<account_name>`.
  To find the account identifier, see [Supported external engines and catalogs](#label-tables-iceberg-query-using-external-query-engine-snowflake-horizon-before-you-begin).
  An example of an account identifier is `myorg-myaccount`.
* `<account_locator>` is the account locator for your Snowflake account.

  To find your account locator, see
  [Locate your Snowflake account information in Snowsight](ui-snowsight-gs.html#label-snowsight-account-details) and view the *Account locator* in the Account Details dialog.
* `<user_name>` is the user name for a Snowflake user with the public key assigned to the user.

#### Step 4: Generate an access token

Important

To generate an access token, you must first [generate a JWT](#label-tables-iceberg-query-using-external-query-engine-snowflake-horizon-key-pair-generate-jwt).
You must first generate a JWT because you use the JWT to
generate the access token.

Use a `curl` command to generate an access token:

CopyExpand

```
curl -i --fail -X POST "https://<account_identifier>.snowflakecomputing.com/polaris/api/catalog/v1/oauth/tokens" \
 --header 'Content-Type: application/x-www-form-urlencoded' \
 --data-urlencode 'grant_type=client_credentials' \
 --data-urlencode 'scope=session:role:<role>' \
 --data-urlencode 'client_secret=<JWT_token>'
```

Show lessSee more

Scroll to top

Where:

* `<account_identifier>` is the account identifier for your Snowflake account, in the format `<organization_name>-<account_name>`.
  To find the account identifier, see [Supported external engines and catalogs](#label-tables-iceberg-query-using-external-query-engine-snowflake-horizon-before-you-begin).
  An example of an account identifier is `myorg-myaccount`.
* `<role>` is the Snowflake role that is granted access to Iceberg tables, such as ENGINEER.
* `<JWT_token>` Is the JWT that you generated in the previous step.

### Programmatic access token (PAT)

If you use PATs, generate a PAT for authentication.

First, you generate a PAT, which you use to [connect an external query engine to Iceberg tables](#label-tables-iceberg-query-using-external-query-engine-snowflake-horizon-connect-external-query-engine).
Then, you generate an access token, which you only use to verify the permissions for your PAT.

#### Step 1: Generate a PAT

For instructions on how to configure and generate a PAT,
see [Using programmatic access tokens for authentication](programmatic-access-tokens).

The following example creates a programmatic access token (PAT) for the service account user that you created in the previous step by
using the [ALTER USER … ADD PROGRAMMATIC ACCESS TOKEN (PAT)](../sql-reference/sql/alter-user-add-programmatic-access-token) command:

CopyExpand

```
ALTER USER IF EXISTS HORIZON_REST_SRV_ACCOUNT_USER
ADD PAT HORIZON_REST_SRV_ACCOUNT_USER_PAT
  DAYS_TO_EXPIRY = 7
  ROLE_RESTRICTION = 'DATA_ENGINEER'
  COMMENT = 'HORIZON REST API PAT FOR SERVICE ACCOUNT';
```

Show lessSee more

Scroll to top

#### Step 2: Generate an access token for your PAT

In this step, you generate an access token for your PAT.

Attention

You only specify the access token that you generate in this step when you
[verify the permissions](#label-tables-iceberg-query-using-external-query-engine-snowflake-horizon-verify-access-token-permissions)
for your PAT. When you
[connect an external query engine to Iceberg tables](#label-tables-iceberg-query-using-external-query-engine-snowflake-horizon-connect-external-query-engine),
you must specify your PAT that you generated in the previous step, not the access token that you generate in this step.

Use a `curl` command to generate an access token for your PAT:

CopyExpand

```
curl -i --fail -X POST "https://<account_identifier>.snowflakecomputing.com/polaris/api/catalog/v1/oauth/tokens" \
 --header 'Content-Type: application/x-www-form-urlencoded' \
 --data-urlencode 'grant_type=client_credentials' \
 --data-urlencode 'scope=session:role:<role>' \
 --data-urlencode 'client_secret=<PAT_token>'
```

Show lessSee more

Scroll to top

Where:

* `<account_identifier>` is the account identifier for your Snowflake account, in the format `<organization_name>-<account_name>`.
  To find the account identifier, see [Supported external engines and catalogs](#label-tables-iceberg-query-using-external-query-engine-snowflake-horizon-before-you-begin).
  An example of an account identifier is `myorg-myaccount`.
* `<role>` is the Snowflake role that is granted to your PAT and has access to the Iceberg tables you want to query, such as ENGINEER.
* `<PAT_token>` is the value for the PAT token that you generated in the previous step.

## Step 4: Verify access token permissions

In this step, you verify the permissions for the access token that you obtained in the previous step.

* [Verify access to the Horizon IRC endpoint](#label-tables-iceberg-query-using-external-query-engine-snowflake-horizon-verify-access)
* [Retrieve the metadata for a table](#label-tables-iceberg-query-using-external-query-engine-snowflake-horizon-load-table)

### Verify access to the Horizon IRC endpoint

Use a `curl` command to verify that you have permission to access your Horizon IRC endpoint:

CopyExpand

```
curl -i --fail -X GET "https://<account_identifier>.snowflakecomputing.com/polaris/api/catalog/v1/config?warehouse=<database_name>" \
-H "Authorization: Bearer <access_token>" \
-H "Content-Type: application/json"
```

Show lessSee more

Scroll to top

Where:

* `<account_identifier>` is the account identifier for your Snowflake account, in the format `<organization_name>-<account_name>`.
  To find the account identifier, see [Supported external engines and catalogs](#label-tables-iceberg-query-using-external-query-engine-snowflake-horizon-before-you-begin).
  An example of an account identifier is `myorg-myaccount`.
* `<access_token>` is your access token that you generated. If you’re using a PAT, this value is the [access token you generated](#label-tables-iceberg-query-using-external-query-engine-snowflake-horizon-pat-generate-access-token), not the
  *personal access token (PAT)* you generated.
* `<database_name>` is the name of the database you want to query.

  Important

  You must specify the database name in *all capital letters*, even if it was created with lowercase letters.

Example return value:

Expand

```
{
  "defaults": {
    "default-base-location": ""
  },
  "overrides": {
    "prefix": "MY-DATABASE"
  }
}
```

Show lessSee more

Scroll to top

### Retrieve the metadata for a table

You can also make a GET request to retrieve the metadata for a table. Snowflake uses the
[loadTable](https://github.com/apache/iceberg/blob/apache-iceberg-1.6.1/open-api/rest-catalog-open-api.yaml#L616)
operation to load table metadata from your REST catalog.

CopyExpand

```
curl -i --fail -X GET "https://<account_identifier>.snowflakecomputing.com/polaris/api/catalog/v1/<database_name>/namespaces/<namespace_name>/tables/<table_name>" \
 -H "Authorization: Bearer <access_token>" \
 -H "Content-Type: application/json"
```

Show lessSee more

Scroll to top

Where:

* `<account_identifier>` is the account identifier for your Snowflake account, in the format `<organization_name>-<account_name>`.
  To find the account identifier, see [Supported external engines and catalogs](#label-tables-iceberg-query-using-external-query-engine-snowflake-horizon-before-you-begin).
  An example of an account identifier is `myorg-myaccount`.
* `<database_name>` is the database of the table whose metadata you want to retrieve.
* `<namespace_name>` is the namespace of the table whose metadata you want to retrieve.
* `<table_name>` is the table whose metadata you want to retrieve.
* `<access_token>` is your access token that you generated. If you’re using a PAT, this value is the
  [access token you generated](#label-tables-iceberg-query-using-external-query-engine-snowflake-horizon-pat-generate-access-token), not the
  *personal access token (PAT)* you generated.

Important

You must specify the database, namespaces, and table names in *all capital letters*, even if the object was created with lowercase
letters.

## (Optional) Step 5: Configure data protection policies

In this step, you configure data protection policies for Iceberg tables. If you don’t have tables that you need to
protect with Snowflake data policies, you can proceed to the next step.

Note

Tables protected by data protection policies can be accessed over the Horizon Iceberg REST API and by using Apache Spark™.

For instructions on how to configure data protection policies, see [Configure data protection policies on Iceberg tables accessed over Horizon Iceberg REST API and using Apache Spark™](tables-iceberg-query-using-external-query-engine-snowflake-horizon-enforce-access-policies.html#label-enforce-access-policies-configure-policies).

## Step 6: Connect an external query engine to Iceberg tables through Horizon Catalog

In this step, you connect an external query engine to Iceberg tables through Horizon Catalog. With this connection, you can query the tables
by using the external query engine.

The external engines use the Apache Iceberg™ REST endpoint exposed by Snowflake. For your Snowflake account, this endpoint is
in the following format:

CopyExpand

```
https://<account_identifier>.snowflakecomputing.com/polaris/api/catalog
```

Show lessSee more

Scroll to top

The example code in this step shows how to set up a connection in Spark, and the example code is in PySpark. For more information,
see the following sections:

* [Connect by using External OAuth or key pair authentication](#label-tables-iceberg-query-using-external-query-engine-snowflake-horizon-connect-external-oauth-key-pair)
* [Connect by using a programmatic access token (PAT)](#label-tables-iceberg-query-using-external-query-engine-snowflake-horizon-connect-pat-token)

### Connect by using External OAuth or key pair authentication

Use one of the following configurations to connect:

* To access Iceberg tables that *don’t* have Snowflake data protection policies configured, [connect an external query engine without enforcing data policies](#label-tables-iceberg-query-using-external-query-engine-snowflake-horizon-connect-external-oauth-key-pair-no-access-policies).
* To access Iceberg tables that have Snowflake row access and masking policies configured, [connect an external query engine with data policies enforced](#label-tables-iceberg-query-using-external-query-engine-snowflake-horizon-connect-external-oauth-key-pair-access-policies).

#### Connect an external query engine without enforcing data policies

* To connect the external query engine to Iceberg tables by using External OAuth or key pair authentication. Use the following example code.

This code doesn’t enforce data protection policies:

CopyExpand

```
# Snowflake Horizon Catalog Configuration, change as per your environment

CATALOG_URI = "https://<account_identifier>.snowflakecomputing.com/polaris/api/catalog"
HORIZON_SESSION_ROLE = f"session:role:<role>"
CATALOG_NAME = "<database_name>" #provide in UPPER CASE

# Cloud Service Provider Region Configuration (where the Iceberg data is stored)
REGION = "eastus2"

# Paste the External Oauth Access token that you generated in Snowflake here
ACCESS_TOKEN = "<your_access_token>"

# Iceberg Version
ICEBERG_VERSION = "1.9.1"

def create_spark_session():
  """Create and configure Spark session for Snowflake Iceberg access."""
  spark = (
      SparkSession.builder
      .appName("SnowflakeIcebergReader")
      .master("local[*]")

# JAR Dependencies for Iceberg and Azure
      .config(
          "spark.jars.packages",
          f"org.apache.iceberg:iceberg-spark-runtime-3.5_2.12:{ICEBERG_VERSION},"
          f"org.apache.iceberg:iceberg-aws-bundle:{ICEBERG_VERSION}"
          # for Azure storage, use the below package and comment above azure bundle
          # f"org.apache.iceberg:iceberg-azure-bundle:{ICEBERG_VERSION}"
      )

      # Iceberg SQL Extensions
      .config("spark.sql.extensions", "org.apache.iceberg.spark.extensions.IcebergSparkSessionExtensions")
      .config("spark.sql.defaultCatalog", CATALOG_NAME)

      # Horizon REST Catalog Configuration
      .config(f"spark.sql.catalog.{CATALOG_NAME}", "org.apache.iceberg.spark.SparkCatalog")
      .config(f"spark.sql.catalog.{CATALOG_NAME}.type", "rest")
      .config(f"spark.sql.catalog.{CATALOG_NAME}.uri", CATALOG_URI)
      .config(f"spark.sql.catalog.{CATALOG_NAME}.warehouse", CATALOG_NAME)
      .config(f"spark.sql.catalog.{CATALOG_NAME}.token", ACCESS_TOKEN)
      .config(f"spark.sql.catalog.{CATALOG_NAME}.scope", HORIZON_SESSION_ROLE)
      .config(f"spark.sql.catalog.{CATALOG_NAME}.client.region", REGION)

      # Required for vended credentials
      .config(f"spark.sql.catalog.{CATALOG_NAME}.header.X-Iceberg-Access-Delegation", "vended-credentials")
      .config("spark.sql.iceberg.vectorization.enabled", "false")
      .getOrCreate()
  )
  spark.sparkContext.setLogLevel("ERROR")
  return spark
```

Show lessSee more

Scroll to top

Where:

* `<account_identifier>` is your Snowflake account identifier for the Snowflake account that contains the Iceberg tables that you
  want to query. To find this identifier, see [Supported external engines and catalogs](#label-tables-iceberg-query-using-external-query-engine-snowflake-horizon-before-you-begin).
* `<your_access_token>` is your access token that you obtained. To obtain it, see [Step 3: Obtain an access token for authentication](#label-tables-iceberg-query-using-external-query-engine-snowflake-horizon-generate-access-token).

  Note

  For External OAuth, alternatively, you can configure your connection to the engine with automatic token refresh instead of specifying
  an access token.
* `<database_name>` is the name of the database in your Snowflake account that contains Snowflake-managed Iceberg tables that you want to query.

  Note

  The `.warehouse` property in Spark expects your Snowflake *database* name, not your Snowflake warehouse name.
* `<role>` is the role in Snowflake that is configured with access to the Iceberg tables that you want to query. For example: DATA\_ENGINEER.

Important

By default, the code example is set up for Apache Iceberg™ tables stored on Amazon S3. If your Iceberg tables are stored on Azure Storage (ADLS),
perform the following steps:

> 1. Comment out the following line: `f"org.apache.iceberg:iceberg-aws-bundle:{ICEBERG_VERSION}"`
> 2. Uncomment the following line: `# f"org.apache.iceberg:iceberg-azure-bundle:{ICEBERG_VERSION}"`

#### Connect an external query engine with data policies enforced

* To connect with data protection policies enforced, see [Connect Spark to Iceberg tables](tables-iceberg-query-using-external-query-engine-snowflake-horizon-enforce-access-policies.html#label-connect-spark-connect-spark-to-iceberg-tables).

### Connect by using a programmatic access token (PAT)

Use one of the following configurations to connect:

* If you *don’t use* data protection policies with the Iceberg tables that you want to query, use the configuration [Connect an external query engine without enforcing data policies](#label-tables-iceberg-query-using-external-query-engine-snowflake-horizon-connect-pat-token-no-access-policies).
* If you *use* data protection policies with the Iceberg tables that you want to query, use the configuration [Connect an external query engine with data policies enforced](#label-tables-iceberg-query-using-external-query-engine-snowflake-horizon-connect-pat-token-access-policies).

#### Connect an external query engine without enforcing data policies

* To connect the external query engine to Iceberg tables by using a programmatic access token (PAT), use the following example code.

This code doesn’t enforce data protection policies:

CopyExpand

```
# Snowflake Horizon Catalog Configuration, change as per your environment

CATALOG_URI = "https://<account_identifier>.snowflakecomputing.com/polaris/api/catalog"
HORIZON_SESSION_ROLE = f"session:role:<role>"
CATALOG_NAME = "<database_name>" #provide in UPPER CASE

# Cloud Service Provider Region Configuration (where the Iceberg data is stored)
REGION = "eastus2"

# Paste the PAT you generated in Snowflake here
PAT_TOKEN = "<your_PAT_token>"

# Iceberg Version
ICEBERG_VERSION = "1.9.1"

def create_spark_session():
  """Create and configure Spark session for Snowflake Iceberg access."""
  spark = (
      SparkSession.builder
      .appName("SnowflakeIcebergReader")
      .master("local[*]")

# JAR Dependencies for Iceberg and Azure
      .config(
          "spark.jars.packages",
          f"org.apache.iceberg:iceberg-spark-runtime-3.5_2.12:{ICEBERG_VERSION},"
          f"org.apache.iceberg:iceberg-aws-bundle:{ICEBERG_VERSION}"
          # for Azure storage, use the below package and comment above azure bundle
          # f"org.apache.iceberg:iceberg-azure-bundle:{ICEBERG_VERSION}"
      )

      # Iceberg SQL Extensions
      .config("spark.sql.extensions", "org.apache.iceberg.spark.extensions.IcebergSparkSessionExtensions")
      .config("spark.sql.defaultCatalog", CATALOG_NAME)

      # Horizon REST Catalog Configuration
      .config(f"spark.sql.catalog.{CATALOG_NAME}", "org.apache.iceberg.spark.SparkCatalog")
      .config(f"spark.sql.catalog.{CATALOG_NAME}.type", "rest")
      .config(f"spark.sql.catalog.{CATALOG_NAME}.uri", CATALOG_URI)
      .config(f"spark.sql.catalog.{CATALOG_NAME}.warehouse", CATALOG_NAME)
      .config(f"spark.sql.catalog.{CATALOG_NAME}.credential", PAT_TOKEN)
      .config(f"spark.sql.catalog.{CATALOG_NAME}.scope", HORIZON_SESSION_ROLE)
      .config(f"spark.sql.catalog.{CATALOG_NAME}.client.region", REGION)

      # Required for vended credentials
      .config(f"spark.sql.catalog.{CATALOG_NAME}.header.X-Iceberg-Access-Delegation", "vended-credentials")
      .config("spark.sql.iceberg.vectorization.enabled", "false")
      .getOrCreate()
  )
  spark.sparkContext.setLogLevel("ERROR")
  return spark
```

Show lessSee more

Scroll to top

Where:

* `<account_identifier>` is your Snowflake account identifier for the Snowflake account that contains the Iceberg tables that you want
  to query. To find this identifier, see [Supported external engines and catalogs](#label-tables-iceberg-query-using-external-query-engine-snowflake-horizon-before-you-begin).
* `<your_PAT_token>` is your PAT that you obtained. To obtain it, see [Step 3: Obtain an access token for authentication](#label-tables-iceberg-query-using-external-query-engine-snowflake-horizon-generate-access-token).
* `<role>` is the role in Snowflake that is configured with access to the Iceberg tables that you want to query. For example:
  DATA\_ENGINEER.
* `<database_name>` is the name of the database in your Snowflake account that contains Snowflake-managed Iceberg tables that you
  want to query.

  Note

  The `.warehouse` property in Spark expects your Snowflake *database* name, not your Snowflake warehouse name.

Important

By default, the code example is set up for Apache Iceberg™ tables stored on Amazon S3. If your Iceberg tables are stored on Azure Storage (ADLS),
perform the following steps:

> 1. Comment out the following line: `f"org.apache.iceberg:iceberg-aws-bundle:{ICEBERG_VERSION}"`
> 2. Uncomment the following line: `# f"org.apache.iceberg:iceberg-azure-bundle:{ICEBERG_VERSION}"`

#### Connect an external query engine with data policies enforced

* To connect with data protection policies enforced, see [Connect Spark to Iceberg tables](tables-iceberg-query-using-external-query-engine-snowflake-horizon-enforce-access-policies.html#label-connect-spark-connect-spark-to-iceberg-tables).

## Step 7: Query Iceberg tables

This step provides the following code examples for using Apache Spark™ to query Iceberg tables:

* Show namespaces
* Use namespaces
* Show tables
* Query a table

### Show namespaces

CopyExpand

```
spark.sql("show namespaces").show()
```

Show lessSee more

Scroll to top

### Use namespace

CopyExpand

```
spark.sql("use namespace <your_schema_name_in_snowflake>")
```

Show lessSee more

Scroll to top

### Show tables

CopyExpand

```
spark.sql("show tables").show()
```

Show lessSee more

Scroll to top

### Query a table

CopyExpand

```
spark.sql("use namespace spark_demo")
spark.sql("select * from <your_table_name_in_snowflake>").show()
```

Show lessSee more

Scroll to top

## Considerations for querying Iceberg tables with an external query engine

Consider the following items when you query Iceberg tables with an external query engine:

* Iceberg

  + For tables in Snowflake:

    - Only Snowflake-managed Iceberg tables are supported.
    - Querying the following tables isn’t supported:

      * Remote tables
      * Snowflake native tables
      * Externally managed Iceberg tables including Delta-based Iceberg tables and
        Snowflake-managed Iceberg tables that you loaded with data from Iceberg-compatible Parquet data files by using the COPY INTO table command
  + You can query but can’t write to Iceberg tables.
  + The external reads are supported only on Iceberg version 2 or earlier.
* Access control:

  + Tables protected by the following fine-grained data policies can be accessed over Apache Spark™ through Snowflake Horizon Catalog:

    - Masking policies
    - Tag-based masking policies
    - Row access policies

    For more information, see [Enforce data protection policies when querying Apache Iceberg™ tables from Apache Spark™](tables-iceberg-query-using-external-query-engine-snowflake-horizon-enforce-access-policies).
* Network and private connectivity:

  + Using network policies that are set at the user level isn’t supported with this feature.
  + For [Snowflake-managed network rules](network-rules.html#label-snowflake-managed-network-rules), egress IP addresses that are static aren’t supported.
  + Explicitly granting the Horizon Catalog endpoint access to your storage accounts isn’t supported. We recommend that you use private connectivity for
    secure connectivity from external engines to Horizon Catalog and from Horizon Catalog to your storage account.
* Listings:

  + Iceberg tables that you share through [auto-fulfillment for listings](../collaboration/provider-listings-auto-fulfillment) aren’t
    accessible through the consumer account’s Horizon Iceberg REST Catalog API.
* Clouds:

  + This feature is only supported for Snowflake-managed Iceberg tables that are stored on Amazon S3, Google Cloud, or Microsoft Azure for
    all commercial cloud regions. S3-compatible non-AWS storage isn’t yet supported.
  + For Iceberg tables stored on Amazon S3:

    - If you want to use SSE-KMS encryption, contact customer support or your account team for assistance with enabling access.
  + For Iceberg tables stored on Azure:

    - Azure Virtual Network (VNet) isn’t supported.
* Authentication:

  + For key-pair authentication, key-pair rotation isn’t supported.
  + Workload identity federation isn’t supported with this feature.
