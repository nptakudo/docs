---
title: "Configure an external volume for Azure"
url: "https://docs.snowflake.com/en/user-guide/tables-iceberg-configure-external-volume-azure"
---

# Configure an external volume for Azure

Grant Snowflake restricted access to your own Microsoft Azure container using an external
volume. Snowflake supports the following Azure cloud storage services for external volumes:

* Blob storage
* Data Lake Storage Gen2
* General-purpose v1
* General-purpose v2
* Microsoft Fabric OneLake

Note

To harden your security posture, you can configure an external volume to use private connectivity rather than the public Internet for
network traffic. For more information, see [Private connectivity to external volumes for Microsoft Azure](tables-iceberg-configure-external-volume-azure-private).

To configure an external volume for Azure,
you can [use SQL](#label-configure-external-volume-azure-create-sql) or [use Snowsight](#label-configure-external-volume-azure-create-snowsight).

## Prerequisites

Before you configure an external volume, you need the following:

* An Azure storage container.

  + To use the external volume for externally managed Iceberg tables, all of your table data and metadata files must
    be located in the container.
  + To support data recovery, [enable versioning for your external cloud storage location](tables-iceberg-storage.html#label-tables-iceberg-enable-storage-versioning).
* Permissions in Azure to create and manage IAM policies and roles. If you aren’t an Azure administrator, ask your Azure administrator to perform these tasks.

If you use an Azure storage firewall to block unauthorized traffic to your storage account, follow the instructions in [Allow the VNet subnet IDs](data-load-azure-allow)
to explicitly grant Snowflake access to your Azure storage account.

## Configure an external volume by using SQL

### Step 1: Create an external volume in Snowflake

Create an external volume using the [CREATE EXTERNAL VOLUME](../sql-reference/sql/create-external-volume) command.

Note

Only account administrators (users with the ACCOUNTADMIN role) can execute this SQL command.

The following example creates an external volume that defines an Azure storage location with encryption:

CopyExpand

```
CREATE EXTERNAL VOLUME exvol
  STORAGE_LOCATIONS =
    (
      (
        NAME = 'my-azure-northeurope'
        STORAGE_PROVIDER = 'AZURE'
        STORAGE_BASE_URL = 'azure://exampleacct.blob.core.windows.net/my_container_northeurope/'
        AZURE_TENANT_ID = 'a123b4c5-1234-123a-a12b-1a23b45678c9'
      )
    );
```

Show lessSee more

Scroll to top

Note

* Use the `azure://` prefix and not `https://` when specifying a value for STORAGE\_BASE\_URL.
* For information about specifying a OneLake location (preview feature), see the [CREATE EXTERNAL VOLUME](../sql-reference/sql/create-external-volume.html#label-create-external-volume-onelake) reference page.
* If you use a regional endpoint for a Microsoft Fabric OneLake storage location,
  use the same region as your Microsoft Fabric capacity. This must also be the same region that hosts your Snowflake account.

### Step 2: Grant Snowflake access to the storage location

1. To retrieve a URL to the Microsoft permissions request page, use the [DESCRIBE EXTERNAL VOLUME](../sql-reference/sql/desc-external-volume) command.
   Specify the name of the external volume that you created previously.

   CopyExpand

   ```
   DESC EXTERNAL VOLUME exvol;
   ```

   Show lessSee more

   Scroll to top

   Record the values for the following properties:

   | Property | Description |
   | --- | --- |
   | `AZURE_CONSENT_URL` | URL to the Microsoft permissions request page. |
   | `AZURE_MULTI_TENANT_APP_NAME` | Name of the Snowflake client application created for your account. In a later step in this section, you grant this application permission to obtain an access token on your allowed storage location. |

   See moreShow less

   Expand

   You use these values in the following steps.
2. In a web browser, navigate to the Microsoft permissions request page (the `AZURE_CONSENT_URL`).
3. Select Accept. This action allows the Azure service principal created for your Snowflake account to obtain an
   access token on a specified resource inside your tenant. Obtaining an access token succeeds only if you grant the service principal the
   appropriate permissions on the storage account level (see the next step).

   The Microsoft permissions request page redirects to the Snowflake corporate site (snowflake.com).
4. Log in to the Microsoft Azure portal.
5. Go to Azure Services » Storage Accounts. Select the name of the storage account that the Snowflake service principal
   needs to access.

   > Note
   >
   > You must set IAM permissions for an external volume at the storage account level, not the container level.
6. Select Access Control (IAM) » Add role assignment.
7. Select the `Storage Blob Data Contributor` role to grant read and write access to the Snowflake service principal.

   > Note
   >
   > The `Storage Blob Data Contributor` role grants write access to the external volume location.
   > To completely configure write access, set the `ALLOW_WRITES` parameter of the external volume
   > to `TRUE` (the default value).

> 1. Select + Select members.

1. Search for the Snowflake service principal. This is the identity in the AZURE\_MULTI\_TENANT\_APP\_NAME property in the
   DESC EXTERNAL VOLUME output (in Step 1). Search for the string before the underscore in the AZURE\_MULTI\_TENANT\_APP\_NAME property.

   Important

   * It can take an hour or longer for Azure to create the Snowflake service principal requested through the Microsoft
     request page in this section. If the service principal is not available immediately, wait an hour
     or two and then search again.
   * If you delete the service principal, the external volume stops working.

   [![Add role assignment in Azure Storage Console](../_images/azure-storage-add-role-assignment.png)](../_images/azure-storage-add-role-assignment.png)
2. Select Review + assign.

   Note

   It can take up to 10 minutes for changes to take effect when you assign a role. For more information, see
   [Symptom - Role assignment changes are not being detected](https://learn.microsoft.com/en-us/azure/role-based-access-control/troubleshooting?tabs=bicep#symptom---role-assignment-changes-are-not-being-detected)
   in the Microsoft Azure documentation.

### Step 3: Verify storage access

To check that Snowflake can successfully authenticate to your storage provider, call the [SYSTEM$VERIFY\_EXTERNAL\_VOLUME](../sql-reference/functions/system_verify_external_volume)
function.

CopyExpand

```
SELECT SYSTEM$VERIFY_EXTERNAL_VOLUME('my_external_volume');
```

Show lessSee more

Scroll to top

Note

If you receive the following error, your account administrator must activate AWS STS in the Snowflake deployment region.
For instructions, see
[Manage AWS STS in an AWS Region](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp_enable-regions.html)
in the AWS documentation.

Expand

```
Error assuming AWS_ROLE:
STS is not activated in this region for account:<external volume id>. Your account administrator can activate STS in this region using the IAM Console.
```

Show lessSee more

Scroll to top

## Configure an external volume in Snowsight

[![Snowflake logo in black (no text)](../_images/logo-snowflake-black.png)](../_images/logo-snowflake-black.png) [Preview Feature](../release-notes/preview-features) — Open

Available to all accounts.

1. Sign in to [Snowsight](ui-snowsight-gs.html#label-snowsight-getting-started-sign-in).
2. In the lower-left corner, select your name » Switch role, and then select ACCOUNTADMIN or a role that has the CREATE EXTERNAL VOLUME privilege.

   For more information, see [Switch your primary role](ui-snowsight-gs.html#label-switching-your-active-role).
3. In the navigation menu, select Catalog » External data.
4. Select the External volumes tab.
5. Select + Create.
6. Select Microsoft Azure & OneLake and then select Next.
7. From the Prerequisites page, for Azure tenant ID, specify your Azure tenant ID.

   To find your Azure tenant ID, see [How to find your Microsoft Entra tenant ID](https://learn.microsoft.com/en-us/entra/fundamentals/how-to-find-tenant)
   in the Microsoft Entra documentation.
8. Select Next.
9. From the Grant storage access page, to grant Snowflake access to the storage location, follow these steps:

   1. To provide consent for Snowflake to connect to your Azure storage or Microsoft OneLake,
      select Provide consent.

      The Microsoft permissions request page opens in a new browser tab.
   2. From the Microsoft permissions request page, select Accept. This action allows the Azure service principal created for your
      Snowflake account to obtain an access token on a specified resource inside your tenant. Obtaining an access token succeeds only if you
      grant the service principal the appropriate permissions on the storage account level (see the next step).

      The Microsoft permissions request page redirects to the Snowflake corporate site (snowflake.com).
   3. In Snowflake, from the Multi-tenant app name field, copy the name of the Snowflake client application created for your account
      into a text editor. In the next step, you grant this application permission to obtain an access token on your allowed storage location.
10. To grant your application permission to obtain an access token on your allowed storage location, follow these steps:

    1. Log in to the Microsoft Azure portal.
    2. Go to Azure Services » Storage Accounts. Select the name of the storage account that the Snowflake service principal
       needs to access.

       > Note
       >
       > You must set IAM permissions for an external volume at the storage account level, not the container level.
    3. Select Access Control (IAM) » Add role assignment.
    4. Select the `Storage Blob Data Contributor` role to grant read and write access to the Snowflake service principal.

       > Note
       >
       > The `Storage Blob Data Contributor` role grants write access to the external volume location.
       > To completely configure write access, set the `ALLOW_WRITES` parameter of the external volume
       > to `TRUE` (the default value).
    5. Select + Select members.
    6. Search for the Snowflake service principal.

       This is the *Multi-tenant app name* that you copied from Snowflake in the previous step.

       Important

       * It can take an hour or longer for Azure to create the Snowflake service principal requested through the Microsoft
         request page in this section. If the service principal is not available immediately, wait an hour
         or two and then search again.
       * If you delete the service principal, the external volume stops working.

       [![Add role assignment in Azure Storage Console](../_images/azure-storage-add-role-assignment.png)](../_images/azure-storage-add-role-assignment.png)
    7. Select Review + assign.

       Note

       It can take up to 10 minutes for changes to take effect when you assign a role. For more information, see
       [Symptom - Role assignment changes are not being detected](https://learn.microsoft.com/en-us/azure/role-based-access-control/troubleshooting?tabs=bicep#symptom---role-assignment-changes-are-not-being-detected)
       in the Microsoft Azure documentation.
11. In Snowflake, select Next.
12. In Snowflake, to configure your external volume, from the Configure external volume page, complete the fields:

    | Field | Description |
    | --- | --- |
    | External volume name | Enter a name for your external volume. |
    | Storage base URL | Specifies the base URL for your cloud storage location. |
    | Access scope | Specifies whether write operations are allowed for the external volume; must be set to Allow writes for the following tables:  * Iceberg tables that use Snowflake as the catalog. * Iceberg tables that use an external catalog and are writable. Externally managed Iceberg tables are writable when you access them   through a catalog-linked database that has the ALLOWED\_WRITE\_OPERATIONS parameter set to TRUE. For Iceberg tables created from Delta table files, setting this parameter to Allow writes enables Snowflake to write Iceberg metadata to your external storage. For more information, see [Delta-based tables](tables-iceberg-metadata.html#label-tables-iceberg-metadata-delta).  The value of this parameter must also match the permissions that you set on the cloud storage account for each specified storage location.  Note  If you plan to use the external volume for reading externally managed Iceberg tables, you can set this field to Off. Snowflake doesn’t write data or Iceberg metadata files to your cloud storage when you read tables in an external Iceberg catalog. |
    | Scope | Choose where this external volume should become the default location for future Iceberg tables. Possible values are:  * Do not set a default: Don’t set the external volume as a default anywhere. * Account: Set the external volume as the default for Iceberg tables that are created under the entire account. * Specific database: Set the external volume as the default for Iceberg tables that are created under the database you   specify. To specify this database, use the Database drop-down that appears when you select Specific database. * Specific schema: Set the external volume as the default for Iceberg tables that are created under the schema you specify.   To specify this schema, use the Database drop-down that appears to first select   the parent database of the schema and then select the schema. |
    | Comment (optional) | Specifies a comment for the external volume. |
    | Connectivity | Specifies whether to use outbound private connectivity to harden your security posture. For information about using outbound private connectivity, see [Private connectivity to external volumes for Microsoft Azure](tables-iceberg-configure-external-volume-azure-private). Possible values are:  * Public (default): Use the public internet. * Private (Azure Private Endpoint): Use outbound private connectivity. |

    See moreShow less

    Expand
13. Select Next.

    On the Verify connection & create volume page, Snowflake verifies your connection to Azure and then displays
    a “Successfully connected” message.

    Note

    If Snowflake is unable to verify your connection, check your permission or external volume configuration and then select
    Verify again.
14. Select Create.

## Next steps

After you configure an external volume, you can create an Iceberg table.

* To create a read-only Iceberg table that uses an external catalog, see
  [Configure a catalog integration](tables-iceberg-configure-catalog-integration).
* To create an Iceberg table with full Snowflake platform support,
  see [Create a Snowflake-managed table](tables-iceberg-create.html#label-tables-iceberg-create-snowflake-catalog).
