---
title: "Snowflake Openflow version history"
url: "https://docs.snowflake.com/en/user-guide/data-integration/openflow/version-history"
---

# Snowflake Openflow version history

This topic provides version history for [Snowflake Openflow](about).

To apply the latest updates to your deployment, runtimes, or connectors, see [Manage Openflow](manage).

## January 23, 2026

### Runtime Server 2026.1.22.19

* Resolved an issue where simultaneous commits to a Git-based Flow Registry Client could cause one user’s changes to overwrite another’s.
* New Runtime UI 0.63.0.

### Runtime Extensions 2026.1.22.19

* Salesforce Bulk API: Fixed an edge case where the initial snapshot might not create the destination table as expected.
* BigQuery: Processor properties now reference FlowFile attributes, making it easier to understand component behavior.
* Snowpipe Streaming v2: PutSnowpipeStreaming2 now tracks request IDs and automatically terminates empty relationships.
* CDC SQL Server: You can now set a maximum FlowFile size in CaptureChangeSQLServer.
* Jira: Components now include a verification feature to confirm that your configuration is correct.
* Confluence: Components now include a verification feature to confirm that your configuration is correct.

## January 21, 2026

### Runtime Server 2026.1.20.19

* You can now configure custom SSL certificates in GitHub and Gitlab Flow Registry Clients.

### Runtime Extensions 2026.1.20.21

* Enhanced PerformSnowflakeCortexOCR with page splitting and filtering features.
* BigQuery: Fixed time travel timestamp handling in TriggerBigQueryCdcOnState processor.
* Jira: Better handling of API rate limiting.
* CDC Oracle: You can now set a maximum FlowFile size in CaptureChangeOracle.
* CDC MySQL: Added logging in CaptureChangeMysql processor to log the retention period for binlog on start.
* Confluence: The connector can now ingest file attachments and embedded images.
* CDC MySQL and PostgreSQL: FetchTableSnapshot now includes partition chunk attributes to enable multi-channel streaming.

### Connectors 2026.1.20.18

* All Connectors: The default Snowflake Authentication Strategy is now SNOWFLAKE\_MANAGED, a token-based method that works in both SPCS and BYOC deployments.
* Salesforce Bulk API: Added new parameter, Initial Load Chunking. This option lets you split large initial data loads into time-based chunks (MONTHLY, QUARTERLY, YEARLY) to avoid timeouts and API limits.

  When set, the initial data load is split into multiple jobs based on the interval. On the first run for an object, the connector queries Salesforce to find the oldest record and uses that as the starting point. Each subsequent job queries the next time chunk until caught up to the current time.

  Once caught up, the processor continues with normal incremental offload behavior.
* Oracle: Initial snapshot loads can now run with multiple concurrent threads for faster performance.
* SharePoint: The connector now logs when it encounters and processes empty files.
* Confluence: A new connector version is available that does not fetch access control lists (ACLs).

## January 16, 2026

### Runtime Server 2026.1.15.20

* New Runtime UI 0.62.0.
* The copy button in the Bulletin tooltip has been moved so it’s always visible.

### Runtime Extensions 2026.1.15.20

* SQL: Added support for Pre-Queries and Post-Queries in PutDatabaseRecord processor.
* CDC PostgreSQL: You can now set a maximum FlowFile size in CaptureChangePostgreSQL.
* CDC PostgreSQL and MySQL: FlowFiles now include start.row.index and last.row.index attributes.
* CDC MySQL: CaptureChangeMySQL now reads the event position from the header instead of from the binlog client.
* CDC Connectors: Splitting FetchTableSnapshot output FlowFiles into chunks of MAX\_OUTPUT\_FLOWFILE\_SIZE size.
* Snowpipe Streaming: PutSnowpipeStreaming2 now has dedicated handling for empty FlowFiles.
* Salesforce Bulk API: Added support for Objects without SystemModStamp field.
* Salesforce Bulk API: You can now configure how the initial snapshot is split into time-based chunks.

### Connectors 2026.1.15.18

* Salesforce Bulk API: Added support for Objects with Tracking History enabled.
* Salesforce Bulk API: Added support for Objects without SystemModStamp field.
* CDC Connectors: Clearer log messages when a table enters a failed replication state.
* Google Ads: New “Login Customer ID” parameter lets you specify which manager account (MCC) to fetch reports for.
* Dataverse: The COPY GRANTS option is now applied to destination tables.

## January 15, 2026

### AWS Data Plane Agent 1.15.0

* Resolved an issue where some IAM policies were not deleted when a Deployment was deleted.

## January 14, 2026

### Runtime Server 2026.1.13.18

* Resolved an issue with how validation was triggered when Flow Registry Clients were configured.

### Runtime Extensions 2026.1.13.19

* Google Ads: The connector now works with manager accounts and their subaccounts.
* Oracle: Added new processors designed to accelerate initial snapshot loads.
* Snowpipe Streaming: PutSnowpipeStreaming2 now includes a counter for each destination.
* SQL: PutDatabaseRecord now uses setBytes binding for BINARY SQL types.

### Connectors 2026.1.13.16

* Slack: Improved handling of file attachments with Slack messages.
* Unstructured Connectors: Resolved Null Pointer Exceptions that occurred when parameters were left empty.
* Google Drive: You can now specify multiple folders by using a comma-separated list in the “Folder Name” parameter.
* Google Drive: New Simple Ingest and Cortex connectors that don’t require domain-wide delegation.
* Streaming Destination Modules: PutSnowpipeStreaming now limits channel concurrency for streaming destinations.

## January 12, 2026

### Control Plane UI 0.68.0

* You no longer need OWNERSHIP privilege on the Snowflake Role when configuring BYOC and SPCS Runtimes.
* You no longer need CREATE USER privilege to create a BYOC Runtime.
* **Behavior change:** Starting with AWS Data Plane Agent 0.37.0, you must specify a Snowflake Role when creating a Runtime.

## January 8, 2026

### Control Plane UI 0.67.0

* The Deployment details dialog now correctly shows Private Link and End User Auth over Private Link settings.
* The SAP connector card now displays an updated icon.
* The Runtime and Deployment details dialogs now display the SQL name when available.
* The Create Runtime dialog now requires a Snowflake role. It no longer requires CREATE USER privilege.

### Data Plane Service 0.98.0

* The system now polls less frequently for new Runtime versions, reducing query costs.
* Runtime Upgrades are now more reliable because all related components are discovered and upgraded together.

### AWS Data Plane Agent 1.13.0

* Resolved an upgrade failure affecting older Deployments that pulled helm charts from AWS OCI Repository.

### SPCS Data Plane Agent 1.11.0

* The deployment creation sequence has been optimized to reduce wait time.

## January 6, 2026

### Runtime Server 2026.1.5.14

* When you clear bulletins on a process group, bulletins for its scoped controller services are also cleared.
* Registry Clients no longer log confusing WARN messages when you commit the first version of a flow.

### Runtime Extensions 2026.1.5.19

* Oracle: Archive logs are now properly removed even when database traffic isn’t captured by XStream Out server.
* JIRA: Resolved a resource leak triggered by certain HTTP error codes and improved log messages.
* Azure components: Fixed NoClassDefFoundError: io/netty/handler/codec/quic/Quic.
* Kafka: The verification process is improved and now returns information about the Kafka Connection Controller Service.
* MS SQL Server: Database names with special characters are now properly quoted when available tables are fetched.

### Connectors 2026.1.5.13

* All Database CDC Connectors: The snapshot completion log now shows the correct total number of rows ingested.
* All Unstructured Connectors: The Cortex service name parameter is now correctly applied to documents.
* MySQL & PostgreSQL: You can now configure concurrency settings for Snapshot loads.
* JIRA: Performance is improved by reducing small FlowFiles and batching data sent via Snowpipe Streaming.
* Google Drive: Inserts via Snowpipe Streaming can now run in parallel instead of sequentially.

## December 19, 2025

### Runtime Oracle Extensions 2025.12.19.8

* Fixed an issue validating Oracle licenses that prevented the OracleCapture processor from starting.
* Improved change detection for large schemas

## December 17, 2025

### Runtime Server 2025.12.16.19

* Improved how invalid controller services are handled when you enable or disable them.
* Included Registry Clients in the Runtime documentation.

### Runtime Extensions 2025.12.16.19

* PostgreSQL: Fixed ordering of composite key columns.

### Connectors 2025.12.16.19

* Salesforce Bulk API: Added a new parameter to control case sensitivity for object identifiers created in Snowflake. By default, column names remain case sensitive for backward compatibility. This default may change at public preview or general availability.
* Confluence Data Center: New connector to integrate with Confluence Data Center edition.

## December 16, 2025

### AWS Data Plane Agent 1.12.0

* Fixed an issue where BYOC deployment upgrades failed due to a mismatch between the machine image and Kubernetes cluster versions.
* Fixed an issue where BYOC deployment upgrades failed with the error message “OCI Registry Login Failed”.

## December 11, 2025

### Control Plane Core 0.95.0

* Fixed an issue where the Runtime Run As Role couldn’t be set for roles containing Snowflake-restricted characters, such as hyphens.

### Runtime Server 2025.12.11.21

* Improved behavior when enabling controller services that are invalid and shouldn’t be enabled.
* New Runtime UI 0.59.0.
* Registry clients now support property verification.

### Runtime Extensions 2025.12.11.21

* AWS Secrets Manager: Parameter Provider now considers non-string values as valid parameters.
* RenameRecordField processor now properly handles multiple records per FlowFile.
* Kinesis: Fixed an issue where the ConsumeKinesis processor throttled new records even when buffers were empty.
* Snowflake: Added a default network timeout to the Snowflake Connection Service.
* Confluence: Fixed handling of page deletion.
* Confluence Data Center: Fixed the HTTP response decoder for the client.
* MySQL: Improved logging for table mapping when consuming binlog events.
* CDC Databases Connectors: Observability dashboards now display the failure reason when a table’s replication status changes to failed.

### Connectors 2025.12.11.18

* SQL Server: Exposed new parameters (Re-read Tables in State and Starting Change Tracking Position) for starting position.
* Oracle: Set CASE\_INSENSITIVE as the default for created Snowflake objects.
* Jira: Added support for App Forge authentication method.
* Confluence: Added support for App Forge authentication method.
* Oracle: Fixed missing service name in XStream URL in default parameter values.
* Oracle: Added support for internationalization.
* Unstructured Connectors: Added a parameter to specify the Cortex Search Service name.
* Slack Connectors: Added a parameter to control whether user names are resolved.

## December 9, 2025

### Control Plane Core 0.94.0

* Added support for accessing and using Openflow with an organization or account that has been renamed.

## December 8, 2025

### AWS Data Plane Agent 1.11.0

* Fixed issue upgrading older deployments with non-critical “inconsistent result after apply” error message.

## December 5, 2025

### Runtime Server 2025.12.4.19

* New Runtime UI 0.58.0.
* Added new action to clear bulletins.
* Improved error handling when launching the Status History dialog.

### Runtime Extensions 2025.12.4.19

* Kinesis: Fixed checkpoint committed records in ConsumeKinesis that could previously cause data loss.
* PostgreSQL: Fixed issue where CaptureChangePostgreSQL ignored events when data was loaded via COPY FROM STDIN.

### Connectors 2025.12.4.17

* CDC SQL Server MultiDB: Added and exposed support for case sensitivity for created Snowflake objects.

## December 3, 2025

### AWS Data Plane Agent 1.10.0

* Added support for encrypting EBS volumes across the entire Openflow Deployment.

### SPCS Data Plane Agent 1.9.0

* Snowflake Deployments encountering internal certificate authority mismatch issues are now auto-healed on upgrade.

### Control Plane Core 0.93.0

* Retained visibility and use of resources when an account name or organization is changed.
* Improved resource utilization efficiency for Small size runtimes in Snowflake Deployments, allowing 3 runtime pods per node instead of 2.
* Added Manage endpoints action for SPCS deployments (requires account parameter).
* Improved external access integration (EAI) list to only show those EAIs the user has access to when creating a runtime in a Snowflake Deployment.

### Data Plane Service 0.93.0

* Improved resiliency of automatic diagnostic bundling and cleanup behavior when a runtime fails to create.
* Added management capabilities for Openflow endpoints in a deployment accessible via new API methods.
* Extended wait time for runtime upgrade failures in SPCS deployments to avoid premature timeout and failure.

### Control Plane UI 0.65.0

* Added Manage endpoints action for SPCS deployments (requires account parameter).

### Data Plane UI 0.11.0

* Added Openflow endpoints management view for SPCS deployments (requires account parameter).

### Openflow Ingress Controller 2025.12.2-17

* Added support for routing to Openflow endpoints attached to Openflow runtimes.
* Fixed client IP address forwarding when evaluating Snowflake privileges for Openflow runtimes.
* Fixed request header propagation to support deployments with Private Link enabled.

### Runtime Server 2025.12.3.16

* Added support for discovering listen ports from Openflow runtime processors to provide users as available targets for Openflow endpoints.
* Controller Services: Fixed validation and enabling that could take too long and cause the runtime to not start.

### Runtime Extensions 2025.12.3.16

* Kinesis: Introduced Shared Throughput consumer in ConsumeKinesis and removed concurrency limits in the HTTP client.
* Kafka: Added support for specifying custom SASL Extensions.
* EventHub: Added support for OAuth authentication in EventHub processors.
* AWS RDS: Added support for AWS RDS IAM Authentication in the DBCP Connection Pool to access databases over JDBC.
* Listen\* Processors (Examples: ListenHTTP, HandleHttpRequest, ListenOTLP): Added support for new ListenComponent and ListenPortDefinition NiFi APIs to allow discovery of listen ports for use with Openflow endpoints.
* OpenflowRuntimeSSLContextProvider: Added new control service for use with Listen\* Processors to integrate with Openflow endpoints.
* Oracle: Fixed handling of case sensitivity on column names when using lower casing.
* Oracle: Fixed support for internationalization.
* MySQL: Fixed filtering of Azure-specific system tables.
* BigQuery: Added new components for the Google BigQuery Change Data Capture (CDC) connector.
* SQL Server: Added ability to choose the starting position when reading the stream.
* Confluence Data Center: Improved support for Audit Records ingestion.
* Confluence: Improved performance to retrieve Confluence page IDs.
* Confluence: Added support for Forge App authentication method.
* Google Drive: Improved recursive listing efficiency when listing the content of a drive.
* Slack: Fixed fetching information of users for large workspaces with a large number of users.

### Connectors 2025.12.3.15

* Google Drive: Fixed potential NullPointerException when Google Drive Folder parameter is not set.
* Slack: Added check to verify files have content before uploading to Snowflake.
* CDC PostgreSQL: Increased backpressure settings to better support large number of synced tables.
* Dataverse: Improved the query for the deletes in the Journal Table.

## December 2, 2025

### Control Plane Core 0.92.0

* Fixed a thread contention issue in Snowflake Deployments that could cause some Runtime actions triggered from Control Plane to time out and fail.

### Data Plane Service 0.92.0

* Fixed a thread contention issue in Snowflake Deployments that could cause some Runtime actions triggered from Control Plane to time out and fail.

### Openflow Ingress Controller 2025.11.20-18

* Added support for Programmatic Access Token authentication and authorization.

### Openflow Runtime Gateway 2025.11.19.22

* Added support for Programmatic Access Token authentication and authorization.

## November 21, 2025

### AWS Data Plane Agent 1.8.0

* Restores support for private Openflow BYOC Deployments by removing all dependencies on URLs outside of Snowflake and AWS, addressing an issue introduced in 1.6.0

### Control Plane Core 0.91.0

* Fixed a rare issue that prevented a Runtime from being activated after it had been suspended

### Data Plane Service 0.91.0

* Fixed an issue causing connector installations to fail on new Runtimes when bulletins are present

## November 20, 2025

### Runtime Server 2025.11.20.20

* Improved visibility of Runtime operations with new metrics for Connectors

### Runtime Extensions 2025.11.20.19

* New components to interact with SAP Business Data Cloud and mapping of CSNs into Snowflake Semantic Views
* CDC MySQL - Improved reliability by clearing out the table map prior to CDC reconnects
* CDC Databases - Fixed a potential deadlock issue with MergeSnowflakeJournalTable when “poll query result” is cleared during operation
* CDC MariaDB - Added support for MariaDB in the MySQL components
* CDC PostgreSQL - Added support for primary keys of type `numeric`

### Connectors 2025.11.20.17

* CDC Databases - Adjusted backpressure thresholds on some connections when processing a lot of data
* Salesforce - Gracefully handle scenarios where we insert duplicate rows in the staging table
* CDC Databases - Exposed parameter to enable private connectivity in the PutSnowpipeStreaming processor for data ingest
* CDC PostgreSQL - Adjusted yield duration on CaptureChangePostgreSQL to not overuse replication connections

## November 19, 2025

### Runtime Extensions 2025.11.18.22

* SQL Server - Performance improvement in Snapshot query
* Dataverse - Schemas are no longer filtered when no column filtering value is provided
* Telemetry - “Bytes Received” is now available for many Snowflake processors after fixing the file size for provenance events
* Azure components - Fix ConsumeAzureEventHub by excluding netty-codec-http3 dependency
* Google Cloud - Added support for Workload Identity Federation
* Azure Blob Storage - Added support for uploading file larger than 200GB

### Connectors 2025.11.18.17

* Google Ads - Set the new Authentication Strategy property of the GCP Credentials Controller Service
* Multi Database SQL Server - Fix `source.table.fqn` value handling
* Salesforce - Add logging for successful sync operations to ease monitoring via the events table

### AWS Data Plane Agent 1.6.0

* Improves security by removing unused inbound ports on Load Balancers configured for “Custom Ingress.” You can further limit access with your own Security Group for these Openflow Deployments.
* Improves security and eased configuration of Runtimes with an optional Deployment-level IAM Role to securely access AWS resources like RDS, MSK, Kinesis, and S3. You can now attach IAM Policies to Openflow’s “NodeInstanceRole” that are granted to all Runtimes in that Openflow Deployment.
* Upgrades EKS Cluster from 1.32 to 1.34 for long-term maintenance and security patching
* Resolves an issue where restarting some EC2 nodes frequently caused the Openflow Deployment to freeze
* Security patches and upgrades to third party libraries

### SPCS Data Plane Agent 1.4.0

* A missing event table will no longer cause failure when creating an Openflow Snowflake Deployment.
* Fixed certificate based issues accessing Runtimes and deploying Connectors into Deployments older than 60 days
* Security patches and upgrades to third party libraries

### Control Plane UI 0.64.0

* Adding support for the new Cleaning Up Runtime state
* Adding support for terms accepted trial not started or active trial

### Control Plane Core 0.90.0

* If a Runtime fails to create, it will automatically generate a diagnostics bundle and clean up any partially created resources in the cluster.

### Data Plane Service 0.90.0

* If a Runtime fails to create, it will automatically generate a diagnostics bundle and clean up any partially created resources in the cluster.

### Openflow Ingress Controller 2025.11.12-18

* Security patches and upgrades to third party libraries

## November 15, 2025

### Runtime Extensions 2025.11.16.2

* Improved reliability for high volume deployments by relocating state tracking for replication position and journal versioning in CDC Connectors

## November 14, 2025

### Runtime Server 2025.11.14.17

* Easier debugging with an updated Bulletin Board that can expand stack traces
* Fixed bug when rendering Documentation for extensions that lack tags
* Viewing component state now supports showing 5,000 local entries and 5,000 cluster entries, up from 500 each

### Runtime Extensions 2025.11.14.17

* Reduced costs by removing the validation query in the Snowflake Connection Service

### Connectors 2025.11.14.14

* Google Drive and Google Sheet - Set the new Authentication Strategy property of the GCP Credentials Controller Service

## November 13, 2025

### Runtime Extensions 2025.11.13.19

* Added support for Web Identity authentication to AWS MSK IAM Connection Service in Kafka components
* Added support for Web Identity authentication to AWS Credentials Controller Service for all AWS components
* Added Flow Registry Client support for Bit Bucket Data Center edition
* Fixed Worker ID generation in ConsumeKinesis and added provenance data
* Added support for nested paths in HashiCorpVaultParameterProvider
* Dataverse - Added retry-after mechanism
* Added Snowflake Secrets Parameter Provider
* CDC Database Connectors - Improved reliability and performance on state management
* JIRA - Enriched issues with email addresses
* HubSpot - Fixed handling of 414 error code responses while fetching objects

### Connectors 2025.11.12.21

* Dataverse - Add Column JSON Filtering parameter
* PostgreSQL - Added FIFO FlowFile prioritizer on queues in Postgres Snapshot Load
* MySQL - Expose parameter for the starting position of the replication
* Dataverse - Added updated\_ad and deleted columns
* Salesforce - Switched the CSV Reader to RFC 4180
* Salesforce - Fixed configuration to capture soft deletes

## October 31, 2025

### AWS Data Plane Agent 1.1.0

* Security patches and upgrades to third party libraries

### SPCS Data Plane Agent 1.1.0

* Security patches and upgrades to third party libraries

### Control Plane Core 0.88.0

* Enable Openflow Oracle Connector in Snowflake Deployments
* PrPr release of Horizon Oracle Metadata Connector

### Data Plane Service 0.88.0

* Security patches and upgrades to third party libraries

### Runtime Operator 0.45.0

* Security patches and upgrades to third party libraries

### Runtime Extensions 2025.10.31.13

* Improved reliability of high volume CDC Connectors

## October 30, 2025

### Runtime Extensions 2025.10.30.21

* CDC Database Connectors - New components for multi-databases support are now included in the runtime image
* JIRA - Added support for Forge App authentication method
* New OAuth2 controller service to get Snowflake issued JWTs for Workload Identity Federation

### Connectors 2025.10.30.20

* PostgreSQL - Added First In First Out (FIFO) connection prioritizer in PostgreSQL Snapshot load
* CDC Database Connectors - Disabled load balancing in the incremental flow to ensure single node processing of the data
* Horizon - Made parameters reachable from the root process group of the connectors using a root parameter context
* Dataverse - Added parameter for the new JSON Column Filtering property

## October 29, 2025

### Runtime Server 2025.10.28.18

* Fixed bug in Summary table formatting Process Group task time

### Runtime Extensions 2025.10.28.20

* Kinesis - Support Output Strategy property in ConsumeKinesis processor
* Kinesis - Added the new Kinesis components leveraging the latest AWS client library
* SQL Server - Added support for multiple databases
* MySQL - Added the possibility to specify the binlog starting position for reading the CDC stream
* PostgreSQL - Added support for negative scale in numeric types
* SQL Server - Improved the ordering of the ORDER BY clause
* Snowpipe Streaming - Improved Input Buffer Handling in PutSnowpipeStreaming2
* PostgreSQL - Improved performances in FetchTableSnapshot on large tables with composite primary key
* MySQL - Fixed incorrectly replicated DATEs pre 1582-10-15 (Julian calendar)

### Connectors 2025.10.28.9

* Horizon - new connector to fetch metadata from Oracle
* Oracle - support for multiple logical databases
* MySQL, PostgreSQL, SQL Server - no longer writing the unused avro.schema FlowFile attribute
* Jira - support for fetching Worklogs

## October 28, 2025

### AWS Data Plane Agent 0.61.0

* Security patches and upgrades to third party libraries

## October 27, 2025

### Control Plane UI 0.63.0

* Security patches and upgrades to third party libraries

## October 24, 2025

### Control Plane Core 0.86.0

* Fixed issue where a user can’t log into Openflow if their most recently selected active role was revoked.
* Disable creating a Snowflake Deployment if the user’s role is not granted CREATE COMPUTE POOL privilege.

### Control Plane UI 0.62.0

* Improved display for Upgrade Failed, Inactive, and Activate Failed states
* Always show the current “Run As” role even if it’s not in the current user’s set of account roles
* Fixed issue with “Run As” role validation in Create Runtime dialog

## October 23, 2025

### Runtime Server 2025.10.23.16

* Bulletin icons now reflect the severity of the message
* Parameters can now be edited by double clicking on the row in the Parameter Context
* Included state of system diagnostics API call in the loading skeleton and spinner in the Cluster Listing
* Improved awareness of errors through the global banner when extension types fail to load
* Updated styling for unset, blank, empty styles throughout the Runtime UI

### Runtime Extensions 2025.10.23.11

* Fixed incorrect handling of Drop Table actions in UpdateSnowflakeTable processor
* Oracle - Improved performance by moving metadata generation in FetchSnapshot processor
* Oracle - Fixed handling of column filters DDL
* Dataverse - Added optional configuration to filter columns being fetched
* Cortex - Improved error message when there is an issue calling Cortex in PromptSnowflakeCortex processor
* MySQL - Fixed the filtering out of the “user” table
* Salesforce Data Cloud - Added support for detecting deletions of Data Shares and linked Objects in the shares
* MySQL - Fixed skipping compressed transaction DDLs and DMLs spanning over the transaction
* JIRA - Enrich Jira Worklogs processor
* Confluence - Support for Confluence Data Center edition
* Added Offset Tracking Resolution to PutSnowpipeStreaming2 processor
* Sharepoint - Fixed pagination handling when listing more than 200 items
* Salesforce - Added optional lookup key in UpsertSFDCObjects processor allowing user to specify a field other than ID for retrieving the record to upsert

### Connectors 2025.10.22.17

* Excel - Added missing SPCS related configuration options
* HubSpot - Added support for new object types: Notes, Orders and Carts
* Salesforce - Added missing configuration for authentication strategy for usage of the connector in Openflow Snowflake Deployments
* PostgreSQL - Migrated the connector to standard identifiers for better management of case sensitivity on object naming
* Oracle - Removed the addition of Snowflake Specific Columns to leverage FetchSnapshot processor instead and improve performances
* Sharepoint - New Simple Ingest connector that does not fetch the ACLs associated to the data
* Salesforce - Added support for specifying the object fields that should be included/excluded when retrieving the data

## October 20, 2025

### Control Plane Core 0.84.1

* Released Control Plane Core version 0.84.1.

## October 17, 2025

### AWS Data Plane Agent 0.60.0

* Fixed certificate issues that blocked access to runtimes and connector deployments in deployments older than 60 days.

### Control Plane Core 0.84.0

* Fixed input validation issues when filtering in role selection menus.
* Fixed an issue where links to runtimes were shown to users without access privileges.
* Fixed an issue where users with only USAGE privilege on a runtime couldn’t create connectors in that runtime.
* Fixed an issue for users accessing Openflow over PrivateLink with a network policy enforcing the VPCE ID.
* Added support for suspending and activating runtimes in Snowflake deployments.
* Snowflake deployments now display their current version immediately after creation is initiated.

### Control Plane UI 0.60.0

* Warns users before navigating to a deployment or runtime where VPN connectivity may be required when using custom ingress.
* Keeps the selection panel open for multi-select components after a selection is made.
* Enforces user permissions for viewing the runtime canvas and hides links if permissions are missing.
* Improves setup experience by considering total counts of runtimes and deployments, not just those in the ACTIVE state.
* Makes the Snowflake role optional when creating a runtime in a BYOC deployment.
* Improves text overflow handling for connector cards.

### Openflow Ingress Controller 2025.10.16-17

* Fixed an issue that prevented access to runtimes over PrivateLink.
* Fixed an issue where a new runtime couldn’t be accessed if its name matched that of a previously deleted runtime.

## October 15, 2025

### Runtime Extensions 2025.10.14.22

* Added Snowflake Managed Authentication Strategy to SnowflakeConnectionService and PutSnowpipeStreaming.

### Runtime Oracle Extensions 2025.10.14.22

* Improved snapshot query performance by correcting ORDER BY column sorting.

### Runtime Server 2025.10.14.12

* Fixed missing Process Group identifier information in Processor and Controller Service log records.

## October 08, 2025

### AWS Data Plane Agent 0.59.0

* Added support for workarounds when using self-managed certificates in AWS.
* Fixed issues that caused BYOC deployment upgrades to get stuck with invalid image references and job cleanup.
* Restored support for adding customer-managed IAM policies to Openflow’s IAM roles.

## October 03, 2025

### Connectors 2025.9.30.17

* Updated the Dataverse connector to set empty collation for the Dataverse journal table.

### Runtime Extensions 2025.10.2.19

* Added better support for case sensitivity on Snowflake objects in `MergeSnowflakeJournalTable`.
* Improved HubSpot pagination handling when retrieving more than 10,000 records.
* Unstructured Processing - `PerformSnowflakeCortexOCR` now uses the `AI_PARSE_DOCUMENT` function instead of `PARSE_DOCUMENT`.
* Added better support for case sensitivity on Snowflake objects in PutSnowpipeStreaming.
* PostgreSQL - Fixed unsigned handling of type OIDs in the CaptureChangePostgreSQL processor.

### Runtime Server 2025.9.30.19

* New Runtime UI 0.53.0.
* Fixed a regression that prevented tabbed dialogs from remembering the previously active tab.
* Fixed balto icon regressions and selected radio button display issues.
* Fixed an issue where Parameter Context update requests weren’t deleted when users canceled the request.
* Fixed an issue that caused double scroll bars to appear in the asset upload dialog.
* Fixed an issue where the selected asset count could get out of sync.

## September 26, 2025

### AWS Data Plane Agent 0.52.0

* Improved efficiency of private IP addresses used by EKS cluster nodes, reducing the total number required for scaling out to many Runtime nodes.
* Fixed issue with Runtime logs that incorrectly redacted some component IDs.

### Connectors 2025.9.25.17

* Confluence connector - Better failure handling and retries when facing API rate limits.

### Control Plane Core 0.80.0

* Support for deploying Oracle Runtime Extensions to Runtimes in BYOC Deployments for PrPr customers who have accepted the Terms of Service.
* Fixed an issue where Snowflake deployment moved into an active state prematurely during an upgrade.
* Fixed a rare issue where Snowflake deployment deletions could get stuck and need manual intervention.

### Control Plane UI 0.57.0

* Introduced new deployment upgrade dialog that shows the version mapping.

### Data Plane Service 0.80.0

* Support for deploying Oracle Runtime Extensions to Runtimes in BYOC Deployments for PrPr customers who have accepted the Terms of Service.

### Runtime Extensions 2025.9.25.19

* CDC database connectors: Removed Record Reader from MergeSnowflakeJournalTable processor.
* All connectors log the Query ID whenever a connector executes a query in Snowflake.

### Runtime Oracle Extensions 2025.9.23.19

* PrPr release of Oracle Extension for Openflow Runtimes.

### Runtime Server 2025.9.25.19

* Improved the Openflow Connectors upgrade user experience.

## September 23, 2025

### Connectors 2025.9.23.17

* PostgreSQL connector now includes a new parameter so you can set the replication slot name.
* The PostgreSQL, MySQL, and SQL Server connectors now support column names that include special characters.

### Runtime Extensions 2025.9.23.19

* Added compression to rows added using the Insert Rows method through PutSnowpipeStreaming2.
* MySQL: Added support for compressed bin log events.
* Added new processors, UpdateSnowflakeSchema and UpdateSnowflakeStream, to better manage object lifecycles and support case sensitivity.
* HubSpot: Added support for new “Notes,” “Orders,” and “Carts” object types.
* Slack: Fixed Null Pointer Exception when trying to verify the configuration of ConsumeSlackConservations processor.

### Runtime Server 2025.9.23.19

* Using latest Apache NiFi 2.6.0 release.
* Improved the flow upgrade user experience by improving Flow Differences Filters to handle renameProperty, removeProperty, and createControllerService.
* New Runtime UI 0.52.0.
* Fixed bug allowing default values for dynamic properties.
* Improved the performance of the searchable select used in the Property combo editor.

## September 19, 2025

### AWS Data Plane Agent 0.50.0

* Openflow now supports VPCs with DHCP Option Sets, making it easier to connect to private data sources.
* You can now secure Openflow deployments with PrivateLink, while still allowing browser-based authentication to runtimes without PrivateLink.
* Fixed an issue during upgrades where IAM inline policies failed by exceeding maximum character limits.

### Control Plane Core 0.78.0

* Improved error messages for Snowflake deployment failures to show the root causes.
* Fixed a case where BYOC deployment ends up in Not Healthy state but can’t be deleted from Openflow Control Plane.

### Control Plane UI 0.55.0

* Removed unnecessary title on Runtime and Deployment state columns.

### Openflow Runtime Gateway 2025.9.18.22

* Improved cookie session handling to allow users to remain logged in, even when Runtime is open in an inactive browser tab.

## September 18, 2025

### Connectors 2025.9.17.18

* Addition of the 2 new Oracle CDC connectors.
* Confluence connector - The introduction of a new controller service to handle API rate limits will show the connector as a process group with local changes.
  This can be ignored and will be resolved when upgrading the connector to the next version, when available.

### Runtime Extensions 2025.9.18.18

* Introduced the `UpdateSnowflakeTable` processor, which is like `UpdateSnowflakeDatabase`, but designed for tables and improved case sensitivity.

## September 16, 2025

### Connectors 2025.9.16.18

* SQL Server connector: Exposed the new SQL Server query interval property as a parameter.
* The new controller service for API rate limits in the Jira connector causes the connector to appear as a process group with local changes. You can safely ignore this; it will be fixed in a future connector upgrade.

### Control Plane UI 0.54.0

* Allow users to optionally configure whether end users authenticate over PrivateLink.
* The Estimated time to completion shown when creating Snowflake Deployments and Runtimes is now more accurate.

### Openflow Ingress Controller 2025.9.15-14

* Initial release offering privilege isolation for Openflow runtime authentication and authorization to Snowflake deployments.

### Runtime Extensions 2025.9.16.20

* Added support for DATETIME columns with PutBigQuery processor.
* You can now specify the HTTP protocol version in StandardWebClientServiceProvider.
* Better logging and increased timeouts for FetchSharepointFile processor.
* Added the option to set the replication slot name in CaptureChangePostgreSQL processor.
* You can now use `-infinity` and `+infinity` with Postgres TIMESTAMPTZ values.
* New controller service StandardAtlassianRequestRateManager to deal with API rate limits for the Jira connector.
* Fixed exceptions thrown from ListMicrosoftDataverseTables when table schema isn’t returned by API.

### Runtime Operator 0.40.0

* Support deploying the new Openflow ingress controller for PuPr release of Snowflake deployments.

### Runtime Server 2025.9.16.19

* New Runtime UI 0.51.0.
* You can now delete individual entries in the component state if the component allows it.
* Improved tooltips for Property and Parameter values, especially when values are long or reference external resources.

## September 15, 2025

### AWS Data Plane Agent 0.41.1

* Fixed an issue from AWS Data Plane Agent 0.39.0 that blocked the first install of an Openflow deployment into a new AWS region.

## September 11, 2025

### Control Plane Core 0.73.0

* Fixed issue preventing runtime deletion in Snowflake deployments when a network policy is present.

### Data Plane Service 0.73.0

* Fixed an issue that prevented runtime deletion in Snowflake deployments when a network policy was present.
* Fixed an issue that prevented new versions of runtime extensions from being used when runtimes were created or upgraded.

### Runtime Extensions 2025.9.11.18

* CaptureChangeSQLServer: A new setting, `Table Changes Query Interval`, is introduced to reduce the resource pressure on the source database. Now, the processor queries the source database every 10 seconds (`10 sec`) by default. To restore the original behavior, change the setting to `0 sec`.

## September 10, 2025

### AWS Data Plane Agent 0.40.0

* Resolved an issue where deployments were left partially upgraded after AWS Data Plane Agent 0.39.0 was used.

### Connectors 2025.9.9.18

* Unstructured connectors: Improved reporting on `ChunkText` failures.

### Runtime Extensions 2025.9.10.7

* Microsoft Dataverse: Fixed handling of schemas that include the `Edm.Date` type.
* Fixed attribute prefix handling in the XML Reader.
* Fixed MongoDB controller service for certain authentication methods when information is provided through the URI.
* Added Azure DevOps Flow Registry Client for Git integration with Azure DevOps to version flows.

### Runtime Server 2025.9.9.20

* Added the ability to change the version of a ghosted component if a bundle with the same coordinates and a different version exists.

## September 8, 2025

### AWS Data Plane Agent 0.37.0

* Added support for AWS Data Plane Agent deployments that have DHCP Option Sets configured on the account.
* Upgraded all EKS nodes from Amazon Linux 2 to Amazon Linux 2023.

### AWS Data Plane Agent 0.38.0

* Added support for AWS accounts that require encrypted EBS volumes by default, even if an unencrypted EBS volume is requested. Customers can enable this by adding IAM Policies to the `*-eks-role IAM Role` that grant access to their KMS keys.

### Control Plane Core 0.72.0

* Error messages are now clearer and more informative when runtime-related failures occur.
* Fixed a rare case where an older deployment version disallowed creating a runtime with the same name as a previously deleted runtime.

### Control Plane UI 0.52.0

* Deployment listing and details now include the deployment version number.
* Control Plane logout page now offers a link back to Snowsight
* Searchable select control (used in **Create Runtime** and **Manage Access**) now offers improved behavior when text overflows available space.
* Fixed a bug that temporarily showed duplicate roles when revoking privileges through the **Manage Access** dialog.

### Data Plane Service 0.70.0

* Added support for AWS Data Plane Agent deployments that have DHCP Option Sets configured on the account.
* Allowed customers to delete a runtime and create a new one with the same name shortly thereafter.

## September 5, 2025

### Connectors 2025.9.4.19

* Confluence Connector: Refresh frequency is now set to 1 minute and is no longer exposed as a parameter.

### Runtime Extensions 2025.9.4.20

* Resolved an incompatibility between the Github Registry Client and the latest Jackson release.
* Fixed attribute prefix handling in XML Reader
* Added `StandardProtobufReader` controller service for Protobuf record processing
* `ListTableName` won’t fail the entire FlowFile if partial input is incorrect.

### Runtime Server 2025.9.4.20

* Introduced Runtime UI 0.50.0
* Added a new logout page that provides users options for logging back in or navigating to the Control Plane.
* Enhanced the searchable select control to display options more clearly when text exceeds available space.
* Fixed casing and icon issues when inputting attributes during extension verification.
* Fixed header styling applied to additionalDetails markdown files.

## September 2, 2025

### AWS Data Plane Agent 0.35.0

* Support for AWS Tags with dots in the Tag key.

### Connectors 2025.9.2.16

* MySQL CDC: Always create a new table (and fail if the table already exists) when replication mode is set to `full`.

### Runtime Extensions 2025.9.2.17

* The GitLab Flow Registry Client now supports versioning flows larger than 2MB.
* Fixed issue in the MongoDB Controller Service preventing users to authenticate using X509.
* Fixed irrelevant error logs about schema hash in `UpdateSnowflakeDatabase` processor.
* Confluence: Fixed a bug that prevented users from being added to authorized users even though they had permissions to the space from the group level.
* Fixed `NoSuchElementException` thrown in ChunkText processor and better failure handling with dedicated relationship.
* HubSpot: Fixed bug preventing the List processors to properly go through all the pages.

### Runtime Server 2025.9.2.20

* New Runtime UI 0.48.0.
* Upgraded to latest version of Codemirror and updated usage throughout the application.

## August 28, 2025

### Connectors 2025.8.28.17

* MS SQL CDC Connector: Added support for incremental only mode.
* HubSpot connector: Fixed table creation on invalid object type.

### Runtime Extensions 2025.8.28.19

* Added StandardProtobufReader Controller Service for Protobuf record processing

## August 27, 2025

### AWS Data Plane Agent 0.33.0

* Fixes health checks for Load Balancer Target Groups, so everything shows green in the AWS Console.

### Control Plane Core v0.68.0

* Supports a finer-grained privilege model for deployments and runtimes including MONITOR and OPERATE privileges.

### Control Plane UI v0.51.0

* Supports a finer-grained privilege model for deployments and runtimes including MONITOR and OPERATE privileges.

### Runtime Operator 0.39.0

* Supports a finer-grained privilege model for deployments and runtimes including MONITOR and OPERATE privileges.

## August 26, 2025

### Runtime Extensions 2025.8.26.18

* MS SQL Server: Fixes handling of datetime when used as a primary key.

## August 21, 2025

### Connectors 2025.8.21.16

* PostgreSQL connector: Supports TOASTed values.

### Runtime Extensions 2025.8.21.17

* Uses Google Ads API v21 (Note, v18 is no longer supported).

### Runtime Server 2025.8.21.17

* New Runtime UI 0.47.0.

## August 20, 2025

### Connectors 2025.8.19.17

* Slack connectors: Fixes handling of attachments by appending the File ID to the filename for the files stored in the stage.

### Runtime Extensions 2025.8.20.10

* Adds Google Cloud support to PutSnowpipeStreaming2.
* Adds support for Incremental Only mode in PostgreSQL CDC connector.
* Fixes error when trying to verify configuration in List Azure processors.

### Runtime Server 2025.8.19.18

* Supports unquoted parameter references with spaces in their names within an expression language.

## August 15, 2025

### Control Plane Core 0.64.0

* Resolves an issue that sometimes caused runtime deletion to fail in Snowflake deployments.

### Runtime Operator 0.38.0

* Resolves an issue facilitating runtime autoscaling in Snowflake deployments.

### Runtime Server 2025.8.14.18

* Improves readability in Provenance Event dialog.

## August 13, 2025

### Control Plane Core 0.62.0

* New AWS BYO-VPC deployments now adds the “Private Security Group” to the EKS cluster, making it easier to configure connections to data sources.
* Resolves an issue for new Deployments with a private security group configuration that
  couldn’t pull images from Snowflake over PrivateLink.

### Control Plane UI 0.49.0

* Runtime and Deployment action menus now have separators to help group actions.
* Account roles show in a searchable selection with virtual scrolling.

### Data Plane Service 0.62.0

* Runtime flows no longer disappear after suspend and reactivate due to a conflicting auto scaling operation.

### Runtime Extensions 2025.8.12.20

* Adds FlowFile attributes support for Database and Schema properties in PutSnowflakeInternalStageFile.
* New GetConfluenceSpaces processor.
* PostgreSQL CDC now properly handles DATE, TIME, TIMESTAMP primary keys.

### Runtime Server 2025.8.12.20

* New Runtime UI 0.45.0: Minor improvements to the Component State dialog to improve
  readability of state entries.

## August 12, 2025

### AWS Data Plane Agent 0.32.0

* Fixes issue destroying BYOC deployments that was introduced with 0.29.0.
* Fixes issue from 0.29.0 release where BYOC deployments in AWS Regions with longer names may fail due to IAM Policy length limitations.

## August 7, 2025

### AWS Data Plane Agent 0.30.0

* Upgrades the AMI of EKS nodes when the deployment is upgraded.
* Removes unnecessary IPv6 Security Group rules for ingress and egress.

### Runtime Extensions 2025.8.7.20

* Improves ConsumeKafka by introducing an Inject Offset Output strategy to add a field kafkaOffset to the records.
* Adds the preview tag for Salesforce, Confluence and HubSpot components.
* Better configuration validation in UpdateSnowflakeDatabase to avoid using empty parameters.
* Adds GetConfluencePageContent and GetConfluencePageIds processors for Confluence.
* Fixes UpdateSnowflakeDatabase to properly redirect to the failure relationship when schema is not specified or does not exist.
* Improves error handling of non-authorized calls in HubSpot processors.

### Runtime Server 2025.8.7.20

* New Runtime UI 0.44.0: Improves ConsumeKinesisStream by introducing a schema difference handling strategy to specify how records using the same schema should be grouped.
* Fixes issue in rendering the canvas that surfaced on initial page load.

## August 6, 2025

### Runtime Extensions 2025.8.5.19

* Adds Pipe Info Counter and Channel Error Message to PutSnowpipeStreaming2.
* MySQL connector: Supports enabling the connector in Incremental mode only.
* HubSpot connector: Improves handling of non-supported object types and fixed processing ordering of the events.

### Runtime Server 2025.8.5.19

* New Runtime UI 0.43.0: The Runtime UI now supports labeling extensions in Preview.
  The badge is shown in the create dialog, on the canvas, in the operate palette,
  in the edit dialog, and in listings for extensions not on the canvas.

## August 5, 2025

### AWS Data Plane Agent 0.29.0

* Private deployments: All images and binaries are provided by Snowflake instead of various internet sources.
* Custom Ingress for “Bring Your Own VPC” deployments: Supports enterprise customers
  who use VPNs to access their cloud infrastructure and self-managed TLS certificates.
* Adds end-to-end support for PrivateLink. Previously, data and management communications
  were available over PrivateLink. Now, the deployment can install over PrivateLink, too.

### Control Plane Core 0.60.0

* Adds improvements necessary to support BYOC private deployments.
* Improves handling of outbound grants when transferring ownership of a runtime or deployment.
* Trial accounts are now permitted to use Openflow with relevant parameter enabled.
* Fixes an issue that disrupted use of Control Plane for customers with a large number of Snowflake roles.

### Control Plane UI 0.48.0

* In runtime and deployment listings, more actions in the menus are disabled rather than hidden.
* Removes a link to accept terms. This change prevents problems when the user doesn’t have an active Snowsight session.
* When a new version is detected, prompts the user to reload the CP UI.
* Disallows changing ownership of runtimes in Snowflake deployments.
* Fixes bug that required a Snowflake role, even when the field was hidden.

### Data Plane Service 0.60.0

* Includes improvements necessary to support BYOC private deployments.
* Fixes an issue that disrupted Connector deployment for customers with a large number of Snowflake roles.

### Openflow Runtime Gateway 2025.8.1.14

* Fixes an issue with certificate refresh upon renewal which prevented users from logging into older runtimes.

## July 31, 2025

### Connectors 2025.7.31.17

* Jira: Improved readability of the flow. The scheduling is now exposed via a parameter.

### Runtime Extensions 2025.7.31.18

* Adds File Fragment Size and Count to PutSnowpipeStreaming2.
* Introduces new Confluence processors for the upcoming connector GetConfluenceGroupUsers,
  GetConfluencePagePermissions, GetConfluenceSpacePermissions, ListConfluenceGroups.
* Adds support for TOASTed value in PostgreSQL CDC.
* Fixes initial rendering of canvas when fonts may load slowly.
* Fixes parameter removal in Parameter Contexts owned by a Parameter Provider.

### Runtime Server 2025.7.31.18

* New Runtime UI 0.42.0: Improves formatting in Status History dialog when values are lengthy.

## July 29, 2025

### Runtime Server 2025.7.29.9

* Fixes an issue with scaling that left some nodes in a disconnected state.

## July 24, 2025

### Connectors 2025.7.24.17

* Kafka Connectors: Fixes referenced readers when writing to Iceberg formatted tables.

### Runtime Extensions 2025.7.24.18

* Fixes S3 Location Type in PutSnowpipeStreaming2.

### Runtime Server 2025.7.24.18

* Adds support for users to reset all Counters in a single action.
* Fixes an issue that caused upgrade failure for runtimes with more than 1 node present.

## July 23, 2025

### Control Plane Core 0.58.0

* Adds support for selecting an active role to use in the application, rather than relying on a default role and secondary role inheritance.
* Adds support for considering Snowflake role hierarchy during authorization controls.

### Control Plane UI 0.47.0

* Adds support for selecting an active role to use in the application, rather than relying on a default role and secondary role inheritance.

### Data Plane Service 0.58.0

* Adds support for considering Snowflake role hierarchy during authorization controls.

### Openflow Runtime Gateway 2025.7.22.20

* Adds support for considering Snowflake role hierarchy during authorization controls.

## July 22, 2025

### Runtime Extensions 2025.7.22.19

* A new controller service better supports Slack API rate limits.
* Fixes SnowflakeSignJWT controller service.

## July 16, 2025

### AWS Data Plane Agent 0.25.1

* Fixes upgrades to pull and use the latest host scripts.
  This change enables Openflow to more easily make changes to the agent itself during an upgrade.

## July 15, 2025

### Connectors 2025.7.15.14

* Confluence JIRA connector: Improves type mapping for the JIRA issues.
  Uses the new processor for managing lifecycle of views.
* Slack connectors: Changes defaults for run schedule properties to avoid rate limiting errors.

### Control Plane Core 0.53.0

* Adds support for generating and downloading runtime diagnostic bundles.

### Control Plane UI 0.46.0

* Adds support for generating and downloading runtime diagnostic bundles.

### Data Plane Service 0.53.0

* Adds support for generating and downloading runtime diagnostic bundles.

### Runtime Extensions 2025.7.15.16

* Adds the PutSnowpipeStreaming2 processor using SSv2.

### Runtime Server 2025.7.15.16

* New Runtime UI 0.40.0: Fixes a bug that prevented tooltips from closing on the canvas.

## July 10, 2025

### Control Plane UI 0.45.2

* Adds support for PrivateLink redirects for the Launch Openflow button.
* Fixes an issue where logout doesn’t log the user out if the user revisits soon after.

## July 9, 2025

### Connectors 2025.7.8.14

* PostgresSQL, SQL Server and MySQL Connectors: Change to Journal creation process
  group to remove the false positive error bulletin for PutSnowpipeStreaming
  when it was asked to create channels on non yet existing tables/streams.

### Control Plane Core 0.52.0

* Users must have proper privileges before they can list or view a runtime.

### Control Plane UI 0.45.1

* Fixes a bug that caused runtime and deployment listings not to show and prevented creation of new resources.

### Runtime Extensions 2025.7.9.14

* Git Registry clients have the option to ignore parameter changes when versioning a new version of a flow.
* New HubSpot processor to retrieve the schema of HubSpot objects.
* New processor UpdateSnowflakeView to manage lifecycle of Snowflake views.
* New controller service RemoveFieldRecordReader to drop fields on read.
* Supports PostgreSQL Aurora.
* CaptureChangeSQLServer generates a valid query when the primary key consists of multiple columns.
* UpdateSnowflakeDatabase now checks only column types when required.

### Runtime Server 2025.7.9.14

* New Runtime UI 0.39.0
* Improves colors in canvas for Process Group version control status.
* Improves styling for better alignment with Balto colors.
* Assets are no longer prevented from being re-uploaded in the Manage Assets dialog.
* When using form control to increment a numeric value, output from a dirty Edit Processor form is no longer prevented.

## July 3, 2025

### AWS Data Plane Agent 0.22.2

* Upgrades no longer get stuck when upgrading due to a missing Data Plane UI 0.7.0 image.

## July 1, 2025

### AWS Data Plane Agent 0.22.1

* New deployments no longer fail to install due to mid-handling failure code when
  checking for the presence of AWS ECR repositories.

### Runtime Extensions 2025.7.1.18

* Google Ads: Limits the numbers of calls to Google Ads API when validating the
  components to avoid rate limit errors.

## June 28, 2025

### Runtime Extensions 2025.6.27.21

* Fixes NullPointerException in PutSnowpipeStreaming when empty flow files are being processed
  and Delivery Guarantee is set to `Exactly once`.

## June 27, 2025

### Control Plane Core 0.51.0

* New terms of service flow: Customers can use Control Plane to create
  Snowflake-managed deployments without accepting BYOC and Connector terms.

### Control Plane UI 0.43.0

* New terms of service flow: Customers can use Control Plane to create
  Snowflake-managed deployments without accepting BYOC and Connector terms.

## June 26, 2025

### Connectors 2025.6.26.15

* Kafka Connectors: Ignore column type mismatch in UpdateSnowflakeDatabase for Kafka
  connectors is more resilient in case of issue with schema inference.
* Google Drive & SharePoint Connectors: Improves the flow to avoid a race condition
  where group synchronization kicks off but PERMS\_GROUPS has not been created yet
* Kafka Connectors: Warehouse is no longer needed. The corresponding parameter is removed.

### Runtime Extensions 2025.6.26.16

* Tables without primary keys are retried instead of failed.
* New Alter Strategy in UpdateSnowflakeDatabase processor has the option to ignore column type changes.
* Fixes fetching of HubSpot archived records.

## June 24, 2025

### Control Plane Core 0.50.0

* New deployments send status updates to Openflow Control Plane indicating when upgrades are present.
* The PrPr tag is included on some new connectors.

### Control Plane UI 0.42.0

* New deployments now surface when an upgrade is available, with link to documentation.
  earlier deployments can also use this functionality after a migration to a newer version.

### Data Plane Service 0.50.0

* Fixes Create runtime failures where the minimum node count is greater than one.

### Data Plane UI 0.7.0

* Active role now displays in the current user menu.

## June 20, 2025

### AWS Data Plane Agent 0.21.0

* Deployments created with AWS Data Plane Agent 0.20.0 are no longer prevented from
  adopting future updates to EC2 Agent Host scripts.

## June 18, 2025

### AWS Data Plane Agent 0.19.0

* Supports tagging all AWS resources created and managed by Openflow.
  Enables deployments governed by security controls like AWS SCP and cost controls like AWS MAP.

### AWS Data Plane Agent 0.20.0

* New Openflow BYOC deployments and upgrades of existing deployments are no longer blocked by an “Unsupported block type” error.

### Connectors 2025.6.17.15

* JIRA: Multi-projects support flattened views in Snowflake destination.

### Runtime Server 2025.6.17.16

* Process Group metrics are now visible when using the Stateless engine.
* The toolbar renders properly when font size is scaled in the browser settings.
* The UnpackContent shows the TAR option again.

## June 12, 2025

### Connectors 2025.6.12.19

* Google Sheets: Improves failure handling by retrying when ingesting data into Snowflake.
* Workday: Uses TRUNCATE instead of REPLACE when possible on the destination table.
* Sharepoint / Google Drive: Improves failure handling with proper retry / logging in case of failures.
* SQL Server: Prevents stream staleness.
* Box: Properly reflects permissions when groups are removed from files permissions in Box.
* Google Drive (Simple Ingest) - Fixes handling of files being deleted.
* Workday: Fixes clustering configuration to have the first processor run on the primary node only.

### Control Plane UI 0.41.0

* Skeleton loaders are now shown in the deployment and runtime listings when permissions are evaluated.
* Skeleton loaders are now shown in **Create Runtime** and **Add Connector to Runtime** dialogs
  while options are loaded and permissions are evaluated.

### Runtime Extensions 2025.6.12.21

* Adds the possibility to specify multiple projects to fetch JIRA issues when using ‘Simple Search’.
* Improves handling of all fields in the JIRA connector.
  Improves mapping into destination table by using an individual column per field.
* Adds support for the PuPr of Snowflake Structured Maps/Arrays/Objects.
* Google Sheets connector now supports Boolean and numbers to be used in the same column.
* MySQL: Properly handles a changes in the column filtering parameter during replication.
* MySQL: Fixes potential connection leakage when being disconnected from the binlog.
* SQL Server: Fixes column ordering handling in the Journal Log table.

### Runtime Server 2025.6.12.21

* Error reporting now shows in banners instead of toast notifications.
* Adds support for different ranges in the Status History dialog by selecting different start timestamps.
* Introduces a Process Group column to the Parameter Context table to more efficiently see bound Process Groups.

## June 8, 2025

### Runtime Extensions 2025.6.6.16

* Upgrades Snowflake JDBC Driver to 3.24.2
* Resolves an issue that prevented newer runtimes from installing the latest Microsoft Dataverse Connector.
* Removes Microsoft SQL Server replication of logical databases.

### Runtime Gateway 2025.6.8.2

* Adds support for logging in to Openflow runtimes using role names with dashes.

### Runtime Server 2025.6.6.19

* Adds pre-configured version control support for custom flows.
* Gracefully shuts down processors and controller services for stateless process groups.

## May 31, 2025

### Runtime Extensions 2025.5.31.15

* Add kafka.max.offset attribute to Records produced by ConsumeKafka
