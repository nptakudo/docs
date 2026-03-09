---
title: "Configuring S3 Storage for pg_lake"
url: "https://docs.snowflake.com/en/user-guide/snowflake-postgres/postgres-pg_lake"
---

# Configuring S3 Storage for pg\_lake

[![Snowflake logo in black (no text)](../../_images/logo-snowflake-black.png)](../../_images/logo-snowflake-black.png) [Preview Feature](../../release-notes/preview-features) — Open

Available to all accounts.

pg\_lake is a PostgreSQL extension that enables efficient querying of data stored in object storage
formats like Parquet and ORC. When using pg\_lake with Snowflake Postgres, you configure access to
an Amazon S3 bucket where your data is stored by using a Snowflake storage integration.

This topic explains how to configure S3 bucket permissions on AWS and create a storage integration
that allows Snowflake Postgres to access your data.

Note

Currently, this S3 storage isn’t managed by Snowflake Postgres. You provide your own S3 bucket
and configure access through a storage integration that you attach to your Postgres instance.

## Prerequisites

Before configuring S3 storage for pg\_lake, ensure that you have:

* An active AWS account with permissions to create and manage [S3 buckets](https://docs.aws.amazon.com/AmazonS3/latest/userguide/creating-buckets-s3.html) and [IAM roles](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles.html).
* An S3 bucket in the same AWS region as your Snowflake account. To determine your Snowflake account
  region, execute the following query in Snowflake (not on your Postgres instance):

  CopyExpand

  ```
  SELECT CURRENT_REGION();
  ```

  Show lessSee more

  Scroll to top
* Familiarity with [AWS IAM roles and policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies.html).
* A [Snowflake Postgres instance](postgres-create-instance) with pg\_lake support.
* Privileges to create storage integrations in Snowflake (requires ACCOUNTADMIN role or a role with the CREATE INTEGRATION privilege on the account).

## Step 1: Create an S3 bucket

If you don’t already have one, create an S3 bucket in the same AWS region as your Snowflake account.
For example, if your Snowflake account is in `us-west-2`, create the S3 bucket in the
`us-west-2` region.

Refer to the AWS documentation for instructions on [creating an S3 bucket](https://docs.aws.amazon.com/AmazonS3/latest/userguide/creating-buckets-s3.html).

## Step 2: Create an IAM policy for S3 access

Create an IAM policy that grants the necessary permissions for pg\_lake to read from and write to your S3 bucket:

1. Sign in to the AWS Management Console and navigate to the IAM service.
2. From the left-hand navigation pane, select Account settings.
3. Under Security Token Service (STS) in the Endpoints list, find the Snowflake region where
   your account is located. If the STS status is inactive, move the toggle to Active.
   For more information, see [Activating and deactivating AWS STS in an AWS region](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp_enable-regions.html).
4. From the left-hand navigation pane, select Policies, then choose Create policy.
5. For Policy editor, select JSON.
6. Add a policy document that allows Snowflake to access the S3 bucket and folder. Replace
   `bucket_name` and `prefix` with your actual bucket name and folder path prefix:

   CopyExpand

   ```
   {
       "Version": "2012-10-17",
       "Statement": [
           {
               "Effect": "Allow",
               "Action": [
                   "s3:PutObject",
                   "s3:GetObject",
                   "s3:GetObjectVersion",
                   "s3:DeleteObject",
                   "s3:DeleteObjectVersion"
               ],
               "Resource": "arn:aws:s3:::bucket_name/prefix/*"
           },
           {
               "Effect": "Allow",
               "Action": [
                   "s3:ListBucket",
                   "s3:GetBucketLocation"
               ],
               "Resource": "arn:aws:s3:::bucket_name",
               "Condition": {
                   "StringLike": {
                       "s3:prefix": [
                           "prefix/*"
                       ]
                   }
               }
           }
       ]
   }
   ```

   Show lessSee more

   Scroll to top

   This policy provides permissions to:

   * Read, write, and delete objects in the specified S3 path
   * List bucket contents and retrieve bucket location
   * Support pg\_lake’s ability to create and manage Iceberg tables
7. Choose Next.
8. Enter a policy name (for example, `snowflake_pg_lake_access`) and an optional description.
9. Choose Create policy.

## Step 3: Create an IAM role

Create an IAM role that Snowflake will assume to access your S3 bucket.

Important

When you create this role, you must set the Maximum session duration to `12 hours`.
The storage integration won’t work with the default session duration. See the last step in
this section.

1. From the left-hand navigation pane in the Identity and Access Management (IAM) Dashboard, select Roles.
2. Select Create role.
3. Select AWS account as the trusted entity type.
4. Select Another AWS account.
5. In the Account ID field, enter your own AWS account ID temporarily. You will modify the
   trust relationship in a later step to grant access to Snowflake.
6. Select the Require external ID option. Enter a placeholder external ID such as `0000`.
   You will update this with the actual external ID generated by Snowflake in a later step.

   Note

   An external ID is used to grant access to your AWS resources (such as S3 buckets) to a
   third party like Snowflake. For more information, see
   [How to use an external ID when granting access to your AWS resources to a third party](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_create_for-user_externalid.html).
7. Select Next.
8. Search for and select the policy you created in [Step 2: Create an IAM policy for S3 access](#step-2-create-an-iam-policy-for-s3-access).
9. Select Next.
10. Enter a name and description for the role (for example, `snowflake_pg_lake_role`), then select
    Create role.
11. On the role summary page, locate and record the Role ARN value. You will need this when
    creating the storage integration in Snowflake.
12. While on the role summary page, select Edit in the summary section and change the
    Maximum session duration to `12 hours`. Select Save changes. For more
    information, see [Modifying a role maximum session duration (AWS)](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_use.html#id_roles_use_view-role-max-session).

## Step 4: Create a storage integration in Snowflake

Create a storage integration object in Snowflake that references the IAM role you created.
For the full command syntax, see [CREATE STORAGE INTEGRATION](../../sql-reference/sql/create-storage-integration).

CopyExpand

```
CREATE STORAGE INTEGRATION my_pg_lake_integration
  TYPE = POSTGRES_EXTERNAL_STORAGE
  STORAGE_PROVIDER = 'S3'
  ENABLED = TRUE
  STORAGE_AWS_ROLE_ARN = 'arn:aws:iam::123456789012:role/snowflake_pg_lake_role'
  STORAGE_ALLOWED_LOCATIONS = ('s3://my-bucket/my-prefix/');
```

Show lessSee more

Scroll to top

Where:

* `my_pg_lake_integration` is the name you choose for the storage integration.
* `TYPE = POSTGRES_EXTERNAL_STORAGE` specifies that this integration is for use with Snowflake Postgres.
* `STORAGE_AWS_ROLE_ARN` is the Role ARN you recorded in [Step 3: Create an IAM role](#step-3-create-an-iam-role).
* `STORAGE_ALLOWED_LOCATIONS` specifies the S3 bucket and path prefix. Replace `my-bucket` and `my-prefix` with the bucket name and folder path you created in [Step 1: Create an S3 bucket](#step-1-create-an-s3-bucket). Note that only one location is allowed for Postgres storage integrations.

Note

Creating a storage integration requires the ACCOUNTADMIN role or a role with the CREATE INTEGRATION
privilege on the account. For more information, see [Access control privileges](../security-access-control-privileges).

## Step 5: Retrieve the Snowflake IAM user ARN and external ID

After creating the storage integration, use the [DESCRIBE INTEGRATION](../../sql-reference/sql/desc-integration) command
to retrieve the AWS IAM user and external ID that Snowflake generated for this integration:

CopyExpand

```
DESCRIBE STORAGE INTEGRATION my_pg_lake_integration;
```

Show lessSee more

Scroll to top

In the output, locate and record the following values:

* `STORAGE_AWS_IAM_USER_ARN`: The IAM user ARN that Snowflake will use to assume the role
* `STORAGE_AWS_EXTERNAL_ID`: The external ID to use in the trust policy

You will use these values in the next step to configure the IAM role trust policy.

## Step 6: Update the IAM role trust policy

Update the trust policy of the IAM role you created in [Step 3: Create an IAM role](#step-3-create-an-iam-role) to allow Snowflake to assume the role:

1. Sign in to the AWS Management Console and navigate to the IAM service.
2. From the left-hand navigation pane, select Roles.
3. Select the role you created in [Step 3: Create an IAM role](#step-3-create-an-iam-role).
4. Select the Trust relationships tab.
5. Select Edit trust policy.
6. Replace the policy document with the following text:

   CopyExpand

   ```
   {
       "Version": "2012-10-17",
       "Statement": [
           {
               "Effect": "Allow",
               "Principal": {
                   "AWS": "<storage_aws_iam_user_arn>"
               },
               "Action": "sts:AssumeRole",
               "Condition": {
                   "StringEquals": {
                       "sts:ExternalId": "<storage_aws_external_id>"
                   }
               }
           }
       ]
   }
   ```

   Show lessSee more

   Scroll to top

   Replace the placeholder values with the values you recorded in
   [Step 5: Retrieve the Snowflake IAM user ARN and external ID](#step-5-retrieve-the-snowflake-iam-user-arn-and-external-id):

   * Replace `storage_aws_iam_user_arn` with the `STORAGE_AWS_IAM_USER_ARN` value.
     This is a full ARN in the form `arn:aws:iam::<account_id>:user/snowflake-postgres-integration-management`,
     where the username is always the same and only the AWS account ID varies.
   * Replace `storage_aws_external_id` with the `STORAGE_AWS_EXTERNAL_ID` value.
7. Select Update policy to save the changes.

## Step 7: Attach the storage integration to your Postgres instance

Attach the storage integration to your Snowflake Postgres instance. When the storage integration is attached, the S3 credentials
are automatically synchronized to the Postgres control plane and made available to pg\_lake:

CopyExpand

```
ALTER POSTGRES INSTANCE my_postgres_instance
  SET STORAGE_INTEGRATION = my_pg_lake_integration;
```

Show lessSee more

Scroll to top

You can also specify the storage integration when creating a new Postgres instance:

CopyExpand

```
CREATE POSTGRES INSTANCE my_postgres_instance
  ...
  STORAGE_INTEGRATION = my_pg_lake_integration;
```

Show lessSee more

Scroll to top

To remove a storage integration from a Postgres instance:

CopyExpand

```
ALTER POSTGRES INSTANCE my_postgres_instance
  UNSET STORAGE_INTEGRATION;
```

Show lessSee more

Scroll to top

## Step 8: Configure and use pg\_lake

After attaching the storage integration, connect to your Postgres instance and configure pg\_lake.
For a list of available extensions, see [Snowflake Postgres Extensions](postgres-extensions).

1. Create the pg\_lake extension:

   CopyExpand

   ```
   CREATE EXTENSION pg_lake CASCADE;
   ```

   Show lessSee more

   Scroll to top
2. Set the default storage location for Iceberg tables. This should match the location specified in
   your storage integration.

   The SET command only applies to the current session:

   CopyExpand

   ```
   SET pg_lake_iceberg.default_location_prefix = 's3://my-bucket/my-prefix';
   ```

   Show lessSee more

   Scroll to top

   To set the value for all current and future sessions, use the ALTER DATABASE command instead. If you use
   multiple Postgres databases, make sure to set the storage location for each database:

   CopyExpand

   ```
   -- Substitute the name of your database
   ALTER DATABASE my_database SET pg_lake_iceberg.default_location_prefix = 's3://my-bucket/my-prefix';
   ```

   Show lessSee more

   Scroll to top
3. Verify that the storage integration is configured correctly by listing the contents of your
   S3 bucket:

   CopyExpand

   ```
   SELECT * FROM lake_file.list('s3://my-bucket/my-prefix/*');
   ```

   Show lessSee more

   Scroll to top

   Replace `my-bucket` and `my-prefix` with your actual bucket name and path. If the
   configuration is correct, this query returns a list of files at that location. If the bucket
   is empty, the query returns an empty result set without an error.
4. Verify the end-to-end configuration by creating an Iceberg table, inserting data, and
   querying it back. If this succeeds, pg\_lake can read from and write to your S3 bucket:

   CopyExpand

   ```
   CREATE TABLE my_table (
       id INT,
       data TEXT
     ) USING iceberg;

   INSERT INTO my_table VALUES (1, 'hello iceberg');

   SELECT * FROM my_table;
   ```

   Show lessSee more

   Scroll to top

## Security considerations

When configuring S3 access for pg\_lake, keep these security best practices in mind:

* **Use IAM roles**: Snowflake Postgres uses IAM role assumption rather than static credentials,
  providing better security through temporary credentials and automatic credential rotation.
* **Limit IAM permissions**: Grant only the minimum necessary permissions to the S3 bucket paths
  that pg\_lake needs to access. The IAM policy should restrict access to specific bucket prefixes.
* **Monitor external ID**: The external ID in the trust policy ensures that only your Snowflake
  account can assume the IAM role.
* **Review storage integration changes**: Any updates to the storage integration’s
  `STORAGE_AWS_ROLE_ARN` or `STORAGE_ALLOWED_LOCATIONS` are automatically synchronized to the
  Postgres instance.
* **Use bucket policies**: Consider using S3 bucket policies in addition to IAM policies for defense in depth.
* **Enable S3 access logging**: Enable access logging on your S3 bucket to monitor and audit access patterns.
* **Regional alignment**: Ensure your S3 bucket is in the same AWS region as your Snowflake account
  for optimal performance and to meet data residency requirements.

## Troubleshooting

### Storage integration creation errors

If you encounter errors when creating the storage integration:

* Verify that you have the ACCOUNTADMIN role or a role with the CREATE INTEGRATION privilege on the account.
* Ensure the IAM role ARN is correctly formatted and exists in your AWS account.
* Confirm that the S3 bucket location uses the correct format: `s3://bucket-name/prefix/`
* Note that only one storage location is allowed for `POSTGRES_EXTERNAL_STORAGE` integrations.

Tip

Storage integration errors are logged in the Postgres server logs with a `Storage integration:`
prefix. For example:

`Storage integration: IAM role must have Maximum Session Duration set to 12 hours`

For information about accessing Postgres logs, see
[Snowflake Postgres logging](postgres-logging).

### Connection errors

If pg\_lake cannot access S3 after attaching the storage integration:

* Verify that the storage integration is properly attached to your Postgres instance by querying
  the instance properties.
* Check that the IAM role trust policy has been updated with the correct Snowflake IAM user ARN
  and external ID from the DESCRIBE STORAGE INTEGRATION output.
* Ensure that the S3 bucket region matches your Snowflake account region.
* Verify that the STS endpoint for your region is active in AWS IAM Account settings.

### Permission denied errors

If you receive permission denied errors when accessing S3:

* Confirm that the IAM policy attached to the role includes all required permissions:
  `s3:PutObject`, `s3:GetObject`, `s3:GetObjectVersion`, `s3:DeleteObject`,
  `s3:DeleteObjectVersion`, `s3:ListBucket`, and `s3:GetBucketLocation`.
* Verify that the IAM role’s trust policy allows the Snowflake IAM user to assume the role.
* Check that the S3 bucket policy (if any) doesn’t deny access from the IAM role.
* Ensure that the S3 paths you’re accessing match the prefix specified in `STORAGE_ALLOWED_LOCATIONS`.

### Trust policy errors

If you encounter errors related to assuming the IAM role:

* Verify that the external ID in the trust policy exactly matches the `STORAGE_AWS_EXTERNAL_ID`
  from the storage integration.
* Confirm that the principal ARN in the trust policy matches the `STORAGE_AWS_IAM_USER_ARN` from
  the storage integration.
* Check that the maximum session duration for the IAM role is set to 12 hours.

## Related information

* [Option 1: Configure a Snowflake storage integration to access Amazon S3](../data-load-s3-config-storage-integration) — Similar S3 access workflow to the one described in this topic
* [Apache Iceberg™ tables](../tables-iceberg) — Overview of Iceberg table support in Snowflake
* [Create an Apache Iceberg™ table in Snowflake](../tables-iceberg-create) — Creating Iceberg tables from different catalog sources
* [Configure an external volume](../tables-iceberg-configure-external-volume) — Configuring an external volume for Iceberg tables
* [Configure a catalog integration for files in object storage](../tables-iceberg-configure-catalog-integration-object-storage) — Catalog integration setup for files in object storage
* [pg\_lake extension documentation](https://github.com/Snowflake-Labs/pg_lake)
