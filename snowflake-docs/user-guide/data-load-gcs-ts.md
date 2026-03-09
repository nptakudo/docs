---
title: "Troubleshooting loads from Google Cloud Storage"
url: "https://docs.snowflake.com/en/user-guide/data-load-gcs-ts"
---

# Troubleshooting loads from Google Cloud Storage

This topic provides instructions for resolving issues specific to loading data from Google Cloud Storage stages.

For general data loading troubleshooting steps, see [Troubleshooting bulk data loads](data-load-bulk-ts).

## Error: Failure using stage area

When attempting to load data from a Google Cloud Storage (GCS) bucket, you could encounter the following error:

CopyExpand

```
Failure using stage area. Cause: [Request violates VPC Service Controls. (Status Code: 403)]
```

Show lessSee more

Scroll to top

This error indicates a violation of restrictions established for a GCP service perimeter, which is configured using VPC Service Controls to secure sensitive data. Although the GCS service account created for your Snowflake account may have been granted permission to read and write to the bucket, the access rules for the service perimeter are applied at the GCP organization level, potentially affecting multiple projects. To review additional details associated with the error message, access the VPC Service Control error logs. See the GCP documentation for descriptions of the `violationReason` values in the logs.

The simplest option to resolve the error is to load data from a bucket that is excluded from the service perimeter. If that option is not allowed by your established security rules, you could exclude the GCS service account for your Snowflake account from the service perimeter filters by adding the service account in an access level policy. Note that the service account still requires access to approved resources using the standard IAM policy described in the instructions for configuring the integration with GCS.

The access policy for a GCP organization contains access levels. Access levels are created and managed using the Access Context Manager and either the Google Cloud Console, the gcloud command-line tool, or the Cloud API. The following instructions rely on the gcloud command-line tool.

To add the GCS service account for your Snowflake account to an access level policy:

1. Using a Snowflake client, retrieve the ID for the Cloud Storage service account that was created automatically for your Snowflake account (using [DESCRIBE INTEGRATION](../sql-reference/sql/desc-integration)):

   CopyExpand

   ```
   DESC STORAGE INTEGRATION <integration_name>;
   ```

   Show lessSee more

   Scroll to top

   Where `integration_name` is the name of a storage integration in your account. For more information, see [Configure an integration for Google Cloud Storage](data-load-gcs-config).
2. Create a file named `snowflake_policy.yaml` on your local machine. Specify the service account ID in the `members` attribute:

   CopyExpand

   ```
   - members:
      - serviceAccount:<service_account>
   ```

   Show lessSee more

   Scroll to top

   For example:

   CopyExpand

   ```
   - members:
      - serviceAccount:service-account-id@project1-123456.iam.gserviceaccount.com
   ```

   Show lessSee more

   Scroll to top
3. Using the gcloud command-line tool, execute the following command to create an access level.

   Note

   This command requires a GCP role with the necessary permissions to change VCP Service Control.

   CopyExpand

   ```
   gcloud access-context-manager levels create <access_level_name> \
      --title snowflake \
      --basic-level-spec snowflake_policy.yaml \
      --combine-function=OR \
      --policy=<policy_name>
   ```

   Show lessSee more

   Scroll to top

> Where:
>
> * `policy_name` is the access policy name for your GCP organization.
> * `access_level_name` is the name of your choice for the access level name.
