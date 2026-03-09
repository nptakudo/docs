---
title: "Use catalog-vended credentials for Apache Iceberg™ tables"
url: "https://docs.snowflake.com/en/user-guide/tables-iceberg-configure-catalog-integration-vended-credentials"
---

# Use catalog-vended credentials for Apache Iceberg™ tables

Vended credential support for Iceberg tables lets you give Snowflake access to your table data and
metadata in cloud storage without using an [external volume](tables-iceberg.html#label-tables-iceberg-external-volume-def).

Instead, you configure and delegate access control with your third-party Iceberg REST catalog (such as [Snowflake Open Catalog](https://other-docs.snowflake.com/en/opencatalog/overview)), then create a
catalog integration in Snowflake configured for vended credentials. For any Iceberg table associated
with the catalog integration, Snowflake uses credentials vended by your catalog provider to securely connect to your external cloud storage.

Note

Using catalog-vended credentials is supported for [externally managed Iceberg tables](tables-iceberg.html#label-tables-iceberg-catalog-integration)
that use a [REST catalog integration](../sql-reference/sql/create-catalog-integration-rest).
To use this feature, your external catalog must also support credential vending.

## Considerations

Consider the following when you use catalog-vended credentials for Iceberg tables:

* This feature is supported for tables that store their data and metadata in Amazon S3, Azure Storage, or Google Cloud Storage.
* Table files must be stored in a single bucket; they can’t be spread across multiple buckets.

  However, you can spread your tables across multiple buckets if each table is stored in one bucket.
* The service principal configured with your REST catalog must have permission to read from *all* of the locations that contain your
  table files in your bucket. If you use AWS Lake Formation with AWS Glue, you might need to take extra steps to enable this access. For more information,
  see [(Optional) Configure Lake Formation access control](tables-iceberg-configure-catalog-integration-rest-glue.html#label-tables-iceberg-glue-rest-lf-config).
* Snowflake expects your catalog to provide one of the following tokens, based on your cloud storage provider:

  + AWS: An expiration time for the AWS session token. Snowflake searches for a key-value pair where the key is
    `s3.session-token-expires-at-ms`, and the value is a timestamp that specifies the expiration time in milliseconds.
  + Azure: An expiration time for the SAS token. Snowflake searches for a key-value pair where the key is
    `adls.sas-token-expires-at-ms`, and the value is a timestamp that specifies the expiration time in milliseconds.
  + Google Cloud Storage: An expiration time for the OAuth 2.0 access token. Snowflake searches for a key-value pair where the key is
    `gcs.oauth2.token-expires-at`, and the value is a timestamp that specifies the expiration time in milliseconds.

  If your catalog doesn’t provide a token, Snowflake expects your catalog to provide an expiration time for vended credentials, and searches for a key-value pair
  where the key is `expiration-time`,
  and the value is a timestamp that specifies the expiration time in milliseconds; for example, `1730234407000`.

  If your catalog doesn’t provide an expiration time, Snowflake assumes that the credentials expire 60 minutes after
  receipt.
* Table creation fails if your catalog provides credentials that aren’t valid.
* The CREATE ICEBERG TABLE … AS SELECT command isn’t supported.
* Private connectivity isn’t supported; to use private connectivity, you must [configure an external volume](tables-iceberg-configure-external-volume).

## Create a catalog integration for vended credentials

To create a catalog integration for vended credentials, use the [CREATE CATALOG INTEGRATION (Apache Iceberg™ REST)](../sql-reference/sql/create-catalog-integration-rest)
command with the `ACCESS_DELEGATION_MODE` property set to `VENDED_CREDENTIALS`.

Where:

`ACCESS_DELEGATION_MODE = { VENDED_CREDENTIALS | EXTERNAL_VOLUME_CREDENTIALS }`
:   Specifies the access delegation mode to use for accessing Iceberg table files in your external cloud storage.

    * `VENDED_CREDENTIALS` specifies that Snowflake should use vended credentials.
    * `EXTERNAL_VOLUME_CREDENTIALS` specifies that Snowflake should use an external volume.

    Default: `EXTERNAL_VOLUME_CREDENTIALS`

You can specify the `ACCESS_DELEGATION_MODE` property in the list of `REST_CONFIG` properties in any
[CREATE CATALOG INTEGRATION (Apache Iceberg™ REST)](../sql-reference/sql/create-catalog-integration-rest) statement.

Important

If you use AWS Lake Formation for access control, you must ensure that Snowflake can access your
AWS Glue catalog or Amazon S3 table. For more information, see
[(Optional) Configure Lake Formation access control](tables-iceberg-configure-catalog-integration-rest-glue.html#label-tables-iceberg-glue-rest-lf-config).

### Example: AWS Glue

The following example creates a catalog integration for AWS Glue that uses vended credentials. For more information,
see [Configure a catalog integration for AWS Glue Iceberg REST](tables-iceberg-configure-catalog-integration-rest-glue).

CopyExpand

```
CREATE CATALOG INTEGRATION glue_rest_catalog_int
  CATALOG_SOURCE = ICEBERG_REST
  TABLE_FORMAT = ICEBERG
  CATALOG_NAMESPACE = 'rest_catalog_integration'
  REST_CONFIG = (
    CATALOG_URI = 'https://glue.us-west-2.amazonaws.com/iceberg'
    CATALOG_API_TYPE = AWS_GLUE
    CATALOG_NAME = '123456789012'
    ACCESS_DELEGATION_MODE = VENDED_CREDENTIALS
  )
  REST_AUTHENTICATION = (
    TYPE = SIGV4
    SIGV4_IAM_ROLE = 'arn:aws:iam::123456789012:role/my-role'
    SIGV4_SIGNING_REGION = 'us-west-2'
  )
  ENABLED = TRUE;
```

Show lessSee more

Scroll to top

### Example: Amazon S3 Tables

This example creates a catalog integration for
[Amazon S3 tables](https://docs.aws.amazon.com/AmazonS3/latest/userguide/s3-tables-tables.html) with SigV4 credential vending
enabled using Lake Formation.

CopyExpand

```
CREATE OR REPLACE CATALOG INTEGRATION my_s3_tables_catalog_integration
  CATALOG_SOURCE = ICEBERG_REST
  TABLE_FORMAT = ICEBERG
  CATALOG_NAMESPACE = 'my_namespace'
  REST_CONFIG = (
    CATALOG_URI = 'https://glue.us-west-2.amazonaws.com/iceberg'
    CATALOG_API_TYPE = AWS_GLUE
    CATALOG_NAME = '123456789012:S3tablescatalog/my_table_bucket'
    ACCESS_DELEGATION_MODE = VENDED_CREDENTIALS
  )
  REST_AUTHENTICATION = (
    TYPE = SIGV4
    SIGV4_IAM_ROLE = 'arn:aws:iam::123456789012:role/my_api_permissions_role'
  )
  ENABLED = TRUE;
```

Show lessSee more

Scroll to top

Where:

> `CATALOG_URI = 'https://glue.us-west-2.amazonaws.com/iceberg'`
> :   Specifies the [AWS Glue Iceberg REST endpoint](https://docs.aws.amazon.com/glue/latest/dg/connect-glu-iceberg-rest.html).
>
> `CATALOG_NAME = 'aws_account_id:s3tablescatalog/s3_table_bucket`
> :   Specifies an S3 table bucket in your AWS account.

## Create an Iceberg table that uses vended credentials

After you set up access control with your third-party Iceberg REST catalog and create a catalog integration for vended credentials,
you can create an Iceberg table.

When you create an Iceberg table that uses vended credentials, you specify a catalog integration configured with
`ACCESS_DELEGATION_MODE = VENDED_CREDENTIALS` and exclude the `EXTERNAL_VOLUME` parameter from the
[CREATE ICEBERG TABLE (Iceberg REST catalog)](../sql-reference/sql/create-iceberg-table-rest) statement.

For example:

CopyExpand

```
CREATE ICEBERG TABLE my_iceberg_table
  CATALOG = open_catalog_int_vended_credentials
  CATALOG_TABLE_NAME = 'my_table'
  AUTO_REFRESH = TRUE;
```

Show lessSee more

Scroll to top

Note

If you’ve set a default external volume at the account, database, or schema level, Snowflake ignores the default external volume during
table creation as long as you specify a catalog integration configured to use vended credentials.
