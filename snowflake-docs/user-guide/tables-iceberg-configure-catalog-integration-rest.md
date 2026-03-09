---
title: "Configure a catalog integration for Apache Iceberg™ REST catalogs"
url: "https://docs.snowflake.com/en/user-guide/tables-iceberg-configure-catalog-integration-rest"
---

# Configure a catalog integration for Apache Iceberg™ REST catalogs

An Apache Iceberg™ REST [catalog integration](tables-iceberg.html#label-tables-iceberg-catalog-integration-def) lets Snowflake access
[Apache Iceberg™ tables](tables-iceberg) managed in a remote catalog that complies with the
open source [Apache Iceberg REST OpenAPI specification](https://github.com/apache/iceberg/blob/main/open-api/rest-catalog-open-api.yaml).

Snowflake supports the following additional features when you use an Iceberg REST catalog integration:

* [Catalog-linked databases and automatic table discovery](tables-iceberg-catalog-linked-database)
* [Write support for externally managed Iceberg tables](tables-iceberg-externally-managed-writes)

## Authentication methods

Snowflake supports the following authentication methods for Iceberg REST catalogs:

* OAuth
* Bearer token or personal access token (PAT)
* Signature Version 4 (SigV4)

Supported authentication methods vary by [catalog source](#label-tables-iceberg-configure-catalog-integration-sources).

### Credential rotation

To rotate the credentials for a catalog integration, you can use the [ALTER CATALOG INTEGRATION](../sql-reference/sql/alter-catalog-integration)
command to update the credentials that Snowflake uses to authenticate with your remote catalog.

For example:

CopyExpand

```
ALTER CATALOG INTEGRATION my_cat_int SET
  REST_AUTHENTICATION (
    OAUTH_CLIENT_SECRET = 'myNewSecret'
  );
```

Show lessSee more

Scroll to top

## Connection options

This section describes the connection options for Iceberg REST catalogs.

### Vended credentials

In addition to [External volumes](tables-iceberg-configure-external-volume),
Snowflake supports the following connection options for Iceberg REST catalogs:

* [Vended credentials](tables-iceberg-configure-catalog-integration-vended-credentials)

Supported connection options vary by [catalog source](#label-tables-iceberg-configure-catalog-integration-sources).

### Private connectivity

Snowflake supports connecting to Iceberg REST catalogs through [private connectivity](tables-iceberg-configure-catalog-integration-rest-private).

However, when you connect to the catalog through private connectivity, you must use an external volume to connect to the catalog data.

Supported connection options vary by [catalog source](#label-tables-iceberg-configure-catalog-integration-sources).

## Catalog sources

Snowflake supports any external catalog server that complies with the Iceberg REST specification.

The following topics provide examples for commonly used REST catalogs:

* [Snowflake Open Catalog](tables-iceberg-configure-catalog-integration-open-catalog). These instructions also apply to
  Apache Polaris™.
* [AWS Glue](tables-iceberg-configure-catalog-integration-rest-glue)
* [Amazon API Gateway](tables-iceberg-configure-catalog-integration-rest-api-gateway)
* [Tabular](tables-iceberg-configure-catalog-integration-rest-tabular)
* [Unity Catalog](tables-iceberg-configure-catalog-integration-rest-unity)
* [OneLake](tables-iceberg-configure-catalog-integration-rest-onelake)

## Browsing a remote catalog

After you create a catalog integration for Iceberg REST, you can use the following
Snowflake system functions to browse namespaces and tables in the catalog:

* [SYSTEM$LIST\_ICEBERG\_TABLES\_FROM\_CATALOG](../sql-reference/functions/system_list_iceberg_tables_from_catalog)
* [SYSTEM$LIST\_NAMESPACES\_FROM\_CATALOG](../sql-reference/functions/system_list_namespaces_from_catalog)

## Migrate a table to a Iceberg REST catalog integration

After you create a catalog integration for Iceberg REST, if needed, you can
replace the catalog integration associated with an externally managed Iceberg table in a standard Snowflake database with the catalog
integration you created. For instructions, see [SYSTEM$SET\_CATALOG\_INTEGRATION](../sql-reference/functions/system_set_catalog_integration).
