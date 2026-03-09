---
title: "Configure an external volume for S3-compatible storage"
url: "https://docs.snowflake.com/en/user-guide/tables-iceberg-s3-compatible"
---

# Configure an external volume for S3-compatible storage

For [externally managed](tables-iceberg.html#label-tables-iceberg-catalog-integration) or [Snowflake-managed](tables-iceberg.html#label-tables-iceberg-snowflake-as-catalog)
Apache Iceberg™ tables with data and metadata in S3-compatible storage,
you can configure an external volume associated with an [Amazon S3-compatible storage](data-load-s3-compatible-storage) location.

To create an external volume for S3-compatible storage, you can [Use SQL](#label-tables-iceberg-s3-compatible-sql) or
[Use Snowsight](#label-tables-iceberg-s3-compatible-snowsight).

## Prerequisites

To use S3-compatible storage for Iceberg tables, you must have an S3-compatible API endpoint for Snowflake.
For more information, see [Requirements for S3-compatible storage](data-load-s3-compatible-storage.html#label-data-load-s3-compatible-storage-reqs).

## Create an external volume for S3-compatible storage by using SQL

Create an external volume that specifies an S3-compatible storage location.
For information about the S3-compatible parameters in the CREATE EXTERNAL VOLUME command, see the
[command syntax](../sql-reference/sql/create-external-volume.html#label-iceberg-external-volume-s3-compatible-syntax).

CopyExpand

```
CREATE OR REPLACE EXTERNAL VOLUME ext_vol_s3_compat
  STORAGE_LOCATIONS = (
    (
      NAME = 'my_s3_compat_storage_location'
      STORAGE_PROVIDER = 'S3COMPAT'
      STORAGE_BASE_URL = 's3compat://mybucket/unload/mys3compatdata'
      CREDENTIALS = (
        AWS_KEY_ID = '1a2b3c...'
        AWS_SECRET_KEY = '4x5y6z...'
      )
      STORAGE_ENDPOINT = 'mystorage.com'
    )
  );
```

Show lessSee more

Scroll to top

## Create an external volume for S3-compatible storage by using Snowsight

[![Snowflake logo in black (no text)](../_images/logo-snowflake-black.png)](../_images/logo-snowflake-black.png) [Preview Feature](../release-notes/preview-features) — Open

Available to all accounts.

1. Sign in to [Snowsight](ui-snowsight-gs.html#label-snowsight-getting-started-sign-in).
2. In the lower-left corner, select your name » Switch role, and then select ACCOUNTADMIN or a role that has the CREATE EXTERNAL VOLUME privilege.

   For more information, see [Switch your primary role](ui-snowsight-gs.html#label-switching-your-active-role).
3. In the navigation menu, select Catalog » External data.
4. Select the External volumes tab.
5. Select + Create.
6. Select S3 compatible storage.
7. Select Next.
8. In your S3-compatible storage provider, create access key credentials that have the permissions required to access your bucket and get
   objects.

   For more information, see [Requirements for S3-compatible storage](data-load-s3-compatible-storage.html#label-data-load-s3-compatible-storage-reqs).
9. Select Next.
10. To configure your external volume, from the Configure external volume page, complete the fields:

    | Field | Description |
    | --- | --- |
    | External volume name | Enter a name for your external volume. |
    | Storage endpoint | Specifies a fully qualified domain that points to your S3-compatible API endpoint.  Note  The storage endpoint should not include a bucket name; for example, specify `example.com` instead of `my_bucket.example.com`. |
    | AWS key ID | Specifies the AWS key ID for connecting to and accessing your S3-compatible storage location. |
    | AWS secret key | Specifies the AWS secret key for connecting to and accessing your S3-compatible storage location. |
    | Access scope | Specifies whether write operations are allowed for the external volume; must be set to Allow writes for the following tables:  * Iceberg tables that use Snowflake as the catalog. * Iceberg tables that use an external catalog and are writable. Externally managed Iceberg tables are writable when you access them   through a catalog-linked database that has the ALLOWED\_WRITE\_OPERATIONS parameter set to TRUE. For Iceberg tables created from Delta table files, setting this parameter to Allow writes enables Snowflake to write Iceberg metadata to your external storage. For more information, see [Delta-based tables](tables-iceberg-metadata.html#label-tables-iceberg-metadata-delta).  The value of this field must also match the permissions that you set on the cloud storage account for each specified storage location.  Note  If you plan to use the external volume for reading externally managed Iceberg tables, you can set this field to Off. Snowflake doesn’t write data or Iceberg metadata files to your cloud storage when you read tables in an external Iceberg catalog. |
    | Scope | Choose where this external volume should become the default location for future Iceberg tables. Possible values are:  * Do not set a default: Don’t set the external volume as a default anywhere. * Account: Set the external volume as the default for Iceberg tables that are created under the entire account. * Specific database: Set the external volume as the default for Iceberg tables that are created under the database you   specify. To specify this database, use the Database drop-down that appears when you select Specific database. * Specific schema: Set the external volume as the default for Iceberg tables that are created under the schema you specify.   To specify this schema, use the Database drop-down that appears to first select   the parent database of the schema and then select the schema. |
    | Comment | Specifies a comment for the external volume. |
    | Storage base URL | Specifies the base URL for your cloud storage location. |

    See moreShow less

    Expand
11. Select Next.

    On the Verify connection & create volume page, Snowflake verifies your connection to your S3 compatible storage and then displays
    a “Successfully connected” message.

    Note

    If Snowflake is unable to verify your connection, check your permission or external volume configuration and then select
    Verify again.
12. Select Create.

## Update your external volume credentials

To change or update the credentials for the external volume, you can use the
[ALTER EXTERNAL VOLUME … UPDATE](../sql-reference/sql/alter-external-volume) command.
Specify the name of the storage location that you want to change the credentials for.

CopyExpand

```
ALTER EXTERNAL VOLUME ext_vol_s3_compat UPDATE
  STORAGE_LOCATION = 'my_s3_compat_storage_location'
  CREDENTIALS = (
    AWS_KEY_ID = '4d5e6f...'
    AWS_SECRET_KEY = '7g8h9i...'
  );
```

Show lessSee more

Scroll to top
