---
title: "All processors (alphabetical)"
url: "https://docs.snowflake.com/en/user-guide/data-integration/openflow/processors/index"
---

# All processors (alphabetical)

Feature — Generally Available

Openflow Snowflake Deployments are available to all accounts in AWS and Azure [Commercial regions](../../../intro-regions.html#label-na-general-regions).

Openflow BYOC deployments are available to all accounts in AWS [Commercial regions](../../../intro-regions.html#label-na-general-regions).

This topic provides a list of all Snowflake openflow processors in alphabetical order.
The list includes:

> * The name of each processor
> * A summary of each processor

## A

|  | Processor | Description |
| --- | --- | --- |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [AbortQueryJob](abortqueryjob) | Aborts a Query Job in Salesforce using the Bulk API 2. |
|  | [AttributesToCSV](attributestocsv) | Generates a CSV representation of the input FlowFile Attributes. |
|  | [AttributesToJSON](attributestojson) | Generates a JSON representation of the input FlowFile Attributes. |

See moreShow less

Expand

## C

|  | Processor | Description |
| --- | --- | --- |
|  | [CalculateRecordStats](calculaterecordstats) | Counts the number of Records in a record set, optionally counting the number of elements per category, where the categories are defined by user-defined properties. |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [CaptureChangeMySQL](capturechangemysql) | Reads CDC events from a MySQL database. |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [CaptureChangePostgreSQL](capturechangepostgresql) | Reads CDC events from a PostgreSQL database. |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [CaptureChangeSqlServer](capturechangesqlserver) | Reads CDC events from a SQL Server database. |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [CaptureGoogleDriveChanges](capturegoogledrivechanges) | Captures changes to a Shared Google Drive and emits a FlowFile for each change that occurs. |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [CaptureMicrosoft365GroupsChanges](capturemicrosoft365groupschanges) | Captures Microsoft365 groups changes and emits a FlowFile for each change that occurs. |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [CaptureSharepointChanges](capturesharepointchanges) | Captures changes from a Sharepoint Document Library and emits a FlowFile for each change that occurs. |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [CheckMetaAdsReportReadiness](checkmetaadsreportreadiness) | Processor checking if the Meta Ads report is ready for download. |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [ChunkRecordText](chunkrecordtext) | Chunks text with options for recursively splitting by delimiters and max character length. |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [ChunkText](chunktext) | Chunks text with options for recursively splitting by delimiters and max character length. |
|  | [CompressContent](compresscontent) | Compresses or decompresses the contents of FlowFiles using a user-specified compression algorithm and updates the mime. |
|  | [ConnectWebSocket](connectwebsocket) | Acts as a WebSocket client endpoint to interact with a remote WebSocket server. |
|  | [ConsumeAMQP](consumeamqp) | Consumes AMQP Messages from an AMQP Broker using the AMQP 0. |
|  | [ConsumeAzureEventHub](consumeazureeventhub) | Receives messages from Microsoft Azure Event Hubs with checkpointing to ensure consistent event processing. |
|  | [ConsumeBoxEnterpriseEvents](consumeboxenterpriseevents) | Consumes Enterprise Events from Box admin\_logs\_streaming Stream Type. |
|  | [ConsumeBoxEvents](consumeboxevents) | Consumes all events from Box. |
|  | [ConsumeElasticsearch](consumeelasticsearch) | A processor that repeatedly runs a paginated query against a field using a Range query to consume new Documents from an Elasticsearch index/query. |
|  | [ConsumeGCPubSub](consumegcpubsub) | Consumes messages from the configured Google Cloud PubSub subscription. |
|  | [ConsumeIMAP](consumeimap) | Consumes messages from Email Server using IMAP protocol. |
|  | [ConsumeJMS](consumejms) | Consumes JMS Message of type BytesMessage, TextMessage, ObjectMessage, MapMessage or StreamMessage transforming its content to a FlowFile and transitioning it to ‘success’ relationship. |
|  | [ConsumeKafka](consumekafka) | Consumes messages from Apache Kafka Consumer API. |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [ConsumeKafka](consumekafka) | Consumes messages from Apache Kafka Consumer API. |
|  | [ConsumeKinesisStream](consumekinesisstream) | Reads data from the specified AWS Kinesis stream and outputs a FlowFile for every processed Record (raw) or a FlowFile for a batch of processed records if a Record Reader and Record Writer are configured. |
|  | [ConsumeMQTT](consumemqtt) | Subscribes to a topic and receives messages from an MQTT broker |
|  | [ConsumePOP3](consumepop3) | Consumes messages from Email Server using POP3 protocol. |
|  | [ConsumeSlack](consumeslack) | Retrieves messages from one or more configured Slack channels. |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [ConsumeSlackConversation](consumeslackconversation) | Retrieves messages from Slack conversations available to the App. |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [ConsumeSlackHistory](consumeslackhistory) | Fetches historical messages from all Slack channels available to the App. |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [ConsumeSnowflakeStream](consumesnowflakestream) | Fetches data from a Snowflake stream and writes it to a FlowFile. |
|  | [ConsumeTwitter](consumetwitter) | Streams tweets from Twitter’s streaming API v2. |
|  | [ControlRate](controlrate) | Controls the rate at which data is transferred to follow-on processors. |
|  | [ConvertCharacterSet](convertcharacterset) | Converts a FlowFile’s content from one character set to another |
|  | [ConvertRecord](convertrecord) | Converts records from one data format to another using configured Record Reader and Record Write Controller Services. |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [ConvertToJournalSchema](converttojournalschema) | Converts the incoming database schema into the appropriate schema for a Snowflake CDC Journal table. |
|  | [CopyAzureBlobStorage\_v12](copyazureblobstorage_v12) | Copies a blob in Azure Blob Storage from one account/container to another. |
|  | [CopyS3Object](copys3object) | Copies a file from one bucket and key to another in AWS S3 |
|  | [CountText](counttext) | Counts various metrics on incoming text. |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [CreateAmazonAdsReport](createamazonadsreport) | Processor which creates report configuration for Amazon Ads connector. |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [CreateAzureOpenAiEmbeddings](createazureopenaiembeddings) | Uses Azure OpenAI to create embeddings for text. |
|  | [CreateBoxFileMetadataInstance](createboxfilemetadatainstance) | Creates a metadata instance for a Box file using a specified template with values from the flowFile content. |
|  | [CreateBoxMetadataTemplate](createboxmetadatatemplate) | Creates a Box metadata template using field specifications from the flowFile content. |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [CreateCohereEmbeddings](createcohereembeddings) | Uses Cohere to create embeddings for text. |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [CreateMetaAdsReport](createmetaadsreport) | Processor which creates report configuration for Meta Ads connector. |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [CreateOpenAiEmbeddings](createopenaiembeddings) | Uses OpenAI to create embeddings for text. |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [CreateSnowflakeEmbeddings](createsnowflakeembeddings) | Create vector embeddings using Snowflake Cortex Large Language Model functions |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [CreateVertexAIEmbeddings](createvertexaiembeddings) | Uses VertexAI to create embeddings for text. |
|  | [CryptographicHashContent](cryptographichashcontent) | Calculates a cryptographic hash value for the flowfile content using the given algorithm and writes it to an output attribute. |

See moreShow less

Expand

## D

|  | Processor | Description |
| --- | --- | --- |
|  | [DebugFlow](debugflow) | The DebugFlow processor aids testing and debugging the FlowFile framework by allowing various responses to be explicitly triggered in response to the receipt of a FlowFile or a timer event without a FlowFile if using timer or cron based scheduling. |
|  | [DecryptContentAge](decryptcontentage) | Decrypt content using the age-encryption. |
|  | [DecryptContentPGP](decryptcontentpgp) | Decrypt contents of OpenPGP messages. |
|  | [DeduplicateRecord](deduplicaterecord) | This processor de-duplicates individual records within a record set. |
|  | [DeleteAzureBlobStorage\_v12](deleteazureblobstorage_v12) | Deletes the specified blob from Azure Blob Storage. |
|  | [DeleteAzureDataLakeStorage](deleteazuredatalakestorage) | Deletes the provided file from Azure Data Lake Storage |
|  | [DeleteBoxFileMetadataInstance](deleteboxfilemetadatainstance) | Deletes a metadata instance from a Box file using the specified template key |
|  | [DeleteByQueryElasticsearch](deletebyqueryelasticsearch) | Delete from an Elasticsearch index using a query. |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [DeleteDBFSResource](deletedbfsresource) | Delete a DBFS files and directories. |
|  | [DeleteDynamoDB](deletedynamodb) | Deletes a document from DynamoDB based on hash and range key. |
|  | [DeleteFile](deletefile) | Deletes a file from the filesystem. |
|  | [DeleteGCSObject](deletegcsobject) | Deletes objects from a Google Cloud Bucket. |
|  | [DeleteGridFS](deletegridfs) | Deletes a file from GridFS using a file name or a query. |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [DeleteMilvus](deletemilvus) | Deletes vectors from Milvus database from a collection by ID. |
|  | [DeleteMongo](deletemongo) | Executes a delete query against a MongoDB collection. |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [DeletePinecone](deletepinecone) | Deletes vectors from a Pinecone index. |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [DeleteQueryJob](deletequeryjob) | Deletes a Query Job in Salesforce using the Bulk API 2. |
|  | [DeleteS3Object](deletes3object) | Deletes a file from an Amazon S3 Bucket. |
|  | [DeleteSFTP](deletesftp) | Deletes a file residing on an SFTP server. |
|  | [DeleteSQS](deletesqs) | Deletes a message from an Amazon Simple Queuing Service Queue |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [DeleteUnityCatalogResource](deleteunitycatalogresource) | Delete a Unity Catalog file or directory. |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [DescribeDataShare](describedatashare) | Describe the specified data share metadata in Salesforce Data Cloud. |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [DescribeSFDCObject](describesfdcobject) | Describe the specified object metadata in Salesforce. |
|  | [DetectDuplicate](detectduplicate) | Caches a value, computed from FlowFile attributes, for each incoming FlowFile and determines if the cached value has already been seen. |
|  | [DistributeLoad](distributeload) | Distributes FlowFiles to downstream processors based on a Distribution Strategy. |
|  | [DuplicateFlowFile](duplicateflowfile) | Intended for load testing, this processor will create the configured number of copies of each incoming FlowFile. |

See moreShow less

Expand

## E

|  | Processor | Description |
| --- | --- | --- |
|  | [EncodeContent](encodecontent) | Encode or decode the contents of a FlowFile using Base64, Base32, or hex encoding schemes |
|  | [EncryptContentAge](encryptcontentage) | Encrypt content using the age-encryption. |
|  | [EncryptContentPGP](encryptcontentpgp) | Encrypt contents using OpenPGP. |
|  | [EnforceOrder](enforceorder) | Enforces expected ordering of FlowFiles that belong to the same data group within a single node. |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [EnrichAttributes](enrichattributes) | Looks up a value using the configured Lookup Service and adds the results to the FlowFile as one or more attributes. |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [EnrichCdcStream](enrichcdcstream) | Enriches incoming FlowFiles that come from CaptureChangePostgreSQL, etc. |
|  | [EvaluateJsonPath](evaluatejsonpath) | Evaluates one or more JsonPath expressions against the content of a FlowFile. |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [EvaluateRagAnswerCorrectness](evaluateraganswercorrectness) | Evaluates the correctness of generated answers in a Retrieval-Augmented Generation (RAG) context by computing metrics such as F1 score, cosine similarity, and answer correctness. |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [EvaluateRagFaithfulness](evaluateragfaithfulness) | Evaluates the faithfulness of generated answers in a Retrieval-Augmented Generation (RAG) system by analyzing responses using an LLM (e. |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [EvaluateRagRetrieval](evaluateragretrieval) | Calculates retrieval metrics (Precision@N, Recall@N, FScore@N, MAP@N, MRR) for a RAG system using an LLM as a judge. |
|  | [EvaluateXPath](evaluatexpath) | Evaluates one or more XPaths against the content of a FlowFile. |
|  | [EvaluateXQuery](evaluatexquery) | Evaluates one or more XQueries against the content of a FlowFile. |
|  | [ExecuteGroovyScript](executegroovyscript) | Experimental Extended Groovy script processor. |
|  | [ExecuteProcess](executeprocess) | Runs an operating system command specified by the user and writes the output of that command to a FlowFile. |
|  | [ExecuteScript](executescript) | Experimental - Executes a script given the flow file and a process session. |
|  | [ExecuteSQL](executesql) | Executes provided SQL select query. |
|  | [ExecuteSQLRecord](executesqlrecord) | Executes provided SQL select query. |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [ExecuteSQLStatement](executesqlstatement) | Executes a SQL DDL or DML Statement against a database. |
|  | [ExecuteStreamCommand](executestreamcommand) | The ExecuteStreamCommand processor provides a flexible way to integrate external commands and scripts into NiFi data flows. |
|  | [ExtractAvroMetadata](extractavrometadata) | Extracts metadata from the header of an Avro datafile. |
|  | [ExtractEmailAttachments](extractemailattachments) | Extract attachments from a mime formatted email file, splitting them into individual flowfiles. |
|  | [ExtractEmailHeaders](extractemailheaders) | Using the flowfile content as source of data, extract header from an RFC compliant email file adding the relevant attributes to the flowfile. |
|  | [ExtractGrok](extractgrok) | Evaluates one or more Grok Expressions against the content of a FlowFile, adding the results as attributes or replacing the content of the FlowFile with a JSON notation of the matched content |
|  | [ExtractRecordSchema](extractrecordschema) | Extracts the record schema from the FlowFile using the supplied Record Reader and writes it to the ‘avro. |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [ExtractSchemaColumns](extractschemacolumns) | Extracts the record schema columns from the FlowFile using the supplied Record Reader and writes it to the ‘schema. |
|  | [ExtractStructuredBoxFileMetadata](extractstructuredboxfilemetadata) | Extracts metadata from a Box file using Box AI. |
|  | [ExtractText](extracttext) | Evaluates one or more Regular Expressions against the content of a FlowFile. |

See moreShow less

Expand

## F

|  | Processor | Description |
| --- | --- | --- |
|  | [FetchAzureBlobStorage\_v12](fetchazureblobstorage_v12) | Retrieves the specified blob from Azure Blob Storage and writes its content to the content of the FlowFile. |
|  | [FetchAzureDataLakeStorage](fetchazuredatalakestorage) | Fetch the specified file from Azure Data Lake Storage |
|  | [FetchBoxFile](fetchboxfile) | Fetches files from a Box Folder. |
|  | [FetchBoxFileInfo](fetchboxfileinfo) | Fetches metadata for files from Box and adds it to the FlowFile’s attributes. |
|  | [FetchBoxFileMetadataInstance](fetchboxfilemetadatainstance) | Retrieves specific metadata instance associated with a Box file using template key and scope. |
|  | [FetchBoxFileRepresentation](fetchboxfilerepresentation) | Fetches a Box file representation using a representation hint and writes it to the FlowFile content. |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [FetchDatabaseMetadata](fetchdatabasemetadata) | Fetches complete database metadata for all tables and outputs them to a FlowFile. |
|  | [FetchDistributedMapCache](fetchdistributedmapcache) | Computes cache key(s) from FlowFile attributes, for each incoming FlowFile, and fetches the value(s) from the Distributed Map Cache associated with each key. |
|  | [FetchDropbox](fetchdropbox) | Fetches files from Dropbox. |
|  | [FetchFile](fetchfile) | Reads the contents of a file from disk and streams it into the contents of an incoming FlowFile. |
|  | [FetchFTP](fetchftp) | Fetches the content of a file from a remote FTP server and overwrites the contents of an incoming FlowFile with the content of the remote file. |
|  | [FetchGCSObject](fetchgcsobject) | Fetches a file from a Google Cloud Bucket. |
|  | [FetchGoogleDrive](fetchgoogledrive) | Fetches files from a Google Drive Folder. |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [FetchGoogleDriveFileComments](fetchgoogledrivefilecomments) | Fetches comments and their replies for a Google Drive file. |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [FetchGoogleDriveMetadata](fetchgoogledrivemetadata) | Fetches Google Drive file metadata. |
|  | [FetchGridFS](fetchgridfs) | Retrieves one or more files from a GridFS bucket by file name or by a user-defined query. |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [FetchJiraFields](fetchjirafields) | Retrieves comprehensive metadata for all fields available in the Jira Cloud instance using the REST API v3 /field endpoint. |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [FetchJiraIssues](fetchjiraissues) | Fetches issues from Jira Cloud using REST API v3 with configurable search options. |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [FetchMicrosoftDataverseTable](fetchmicrosoftdataversetable) | Fetch records from Microsoft Dataverse Tables |
|  | [FetchS3Object](fetchs3object) | Retrieves the contents of an S3 Object and writes it to the content of a FlowFile |
|  | [FetchSFTP](fetchsftp) | Fetches the content of a file from a remote SFTP server and overwrites the contents of an incoming FlowFile with the content of the remote file. |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [FetchSharepointFile](fetchsharepointfile) | Fetches the contents of a file from a Sharepoint Drive, optionally downloading a PDF or HTML version of the file when applicable. |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [FetchSharepointMetadata](fetchsharepointmetadata) | For each drive item retrieves its metadata and permissions and writes them as FlowFile attributes. |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [FetchSlackConversationInfo](fetchslackconversationinfo) | Fetches Slack conversation info and member emails |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [FetchSlackFile](fetchslackfile) | Downloads a file shared on Slack. |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [FetchSlackMessage](fetchslackmessage) | Fetches data about a single Slack message |
|  | [FetchSmb](fetchsmb) | Fetches files from a SMB Share. |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [FetchSnowflakeTableProperties](fetchsnowflaketableproperties) | Reads properties from a table and stores them as flow file attributes. |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [FetchSourceTableSchema](fetchsourcetableschema) | Fetches the table schema (i. |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [FetchTableSnapshot](fetchtablesnapshot) | Fetches a snapshot of a table from a database. |
|  | [FilterAttribute](filterattribute) | Filters the attributes of a FlowFile by retaining specified attributes and removing the rest or by removing specified attributes and retaining the rest. |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [FindConfluencePages](findconfluencepages) | Processor for finding Confluence pages using space name and page name. |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [FindSharepointDriveItem](findsharepointdriveitem) | Finds a Sharepoint Drive Item by its Drive ID and Item path. |
|  | [FlattenJson](flattenjson) | Provides the user with the ability to take a nested JSON document and flatten it into a simple key/value pair document. |
|  | [ForkEnrichment](forkenrichment) | Used in conjunction with the JoinEnrichment processor, this processor is responsible for adding the attributes that are necessary for the JoinEnrichment processor to perform its function. |
|  | [ForkRecord](forkrecord) | This processor allows the user to fork a record into multiple records. |

See moreShow less

Expand

## G

|  | Processor | Description |
| --- | --- | --- |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [GenerateAnswersFromContext](generateanswersfromcontext) | Generates synthetic answers for each question present in the incoming records using a Large Language Model (LLM). |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [GenerateAnswersFromGroundTruth](generateanswersfromgroundtruth) | Generates synthetic answers for each question in the incoming records using an LLM. |
|  | [GenerateFlowFile](generateflowfile) | This processor creates FlowFiles with random data or custom content. |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [GenerateJSON](generatejson) | Produces a batch of JSON Objects with random field values based on a configurable JSON Schema. |
|  | [GenerateRecord](generaterecord) | This processor creates FlowFiles with records having random value for the specified fields. |
|  | [GenerateTableFetch](generatetablefetch) | Generates SQL select queries that fetch “pages” of rows from a table. |
|  | [GeoEnrichIP](geoenrichip) | Looks up geolocation information for an IP address and adds the geo information to FlowFile attributes. |
|  | [GeoEnrichIPRecord](geoenrichiprecord) | Looks up geolocation information for an IP address and adds the geo information to FlowFile attributes. |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [GetAmazonAdsReport](getamazonadsreport) | Processor downloading report from Amazon Ads if ready. |
|  | [GetAwsPollyJobStatus](getawspollyjobstatus) | Retrieves the current status of an AWS Polly job. |
|  | [GetAwsTextractJobStatus](getawstextractjobstatus) | Retrieves the current status of an AWS Textract job. |
|  | [GetAwsTranscribeJobStatus](getawstranscribejobstatus) | Retrieves the current status of an AWS Transcribe job. |
|  | [GetAwsTranslateJobStatus](getawstranslatejobstatus) | Retrieves the current status of an AWS Translate job. |
|  | [GetAzureEventHub](getazureeventhub) | Receives messages from Microsoft Azure Event Hubs without reliable checkpoint tracking. |
|  | [GetAzureQueueStorage\_v12](getazurequeuestorage_v12) | Retrieves the messages from an Azure Queue Storage. |
|  | [GetBoxFileCollaborators](getboxfilecollaborators) | Retrieves all collaborators on a Box file and adds the collaboration information to the FlowFile’s attributes. |
|  | [GetBoxGroupMembers](getboxgroupmembers) | Retrieves members for a Box Group and writes their details in FlowFile attributes. |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [GetConfluenceAuditRecords](getconfluenceauditrecords) | Processor listing Confluence audit records. |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [GetConfluenceGroupUsers](getconfluencegroupusers) | Processor that downloads information about users belonging to a given Confluence group |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [GetConfluencePageContent](getconfluencepagecontent) | Processor downloading Confluence pages. |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [GetConfluencePageIds](getconfluencepageids) | Downloads changed Confluence pages since the last sync and emits each as a FlowFile with metadata. |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [GetConfluencePagePermissions](getconfluencepagepermissions) | Processor downloading Confluence page permissions. |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [GetConfluenceSpaceIds](getconfluencespaceids) | Processor for retrieving Confluence space ids. |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [GetConfluenceSpacePermissions](getconfluencespacepermissions) | Processor downloading Confluence space permissions. |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [GetDataShareCredentials](getdatasharecredentials) | Describe the specified data share metadata in Salesforce Data Cloud. |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [GetDataShareTables](getdatasharetables) | Describe the specified data share metadata in Salesforce Data Cloud. |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [GetDBFSFile](getdbfsfile) | Read a DBFS file. |
|  | [GetDynamoDB](getdynamodb) | Retrieves a document from DynamoDB based on hash and range key. |
|  | [GetElasticsearch](getelasticsearch) | Elasticsearch get processor that uses the official Elastic REST client libraries to fetch a single document from Elasticsearch by \_id. |
|  | [GetFile](getfile) | Creates FlowFiles from files in a directory. |
|  | [GetFileResource](getfileresource) | This processor creates FlowFiles with the content of the configured File Resource. |
|  | [GetFTP](getftp) | Fetches files from an FTP Server and creates FlowFiles from them |
|  | [GetGcpVisionAnnotateFilesOperationStatus](getgcpvisionannotatefilesoperationstatus) | Retrieves the current status of an Google Vision operation. |
|  | [GetGcpVisionAnnotateImagesOperationStatus](getgcpvisionannotateimagesoperationstatus) | Retrieves the current status of an Google Vision operation. |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [GetGoogleAdsReport](getgoogleadsreport) | A processor which can interact with Google Ads Reporting API. |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [GetGoogleGroupMembers](getgooglegroupmembers) | Retrieves the members of one or more Google Groups, specified as a comma-separated list of group IDs that is given as a FlowFile attribute. |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [GetGoogleSheets](getgooglesheets) | Processor responsible for fetching data from Google Sheets. |
|  | [GetHubSpot](gethubspot) | Retrieves JSON data from a private HubSpot application. |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [GetHubSpotObject](gethubspotobject) | Get a HubSpot object and its associations by ID or unique value. |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [GetHubSpotSchema](gethubspotschema) | Retrieves schema information for HubSpot object types including field names, types, and labels. |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [GetLinkedInAdsReport](getlinkedinadsreport) | Processor downloading metrics from the LinkedIn Reporting APIs. |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [GetMicrosoft365GroupMembers](getmicrosoft365groupmembers) | Retrieves Microsoft365 group members and emits a FlowFile for each change that occurs. |
|  | [GetMongo](getmongo) | Creates FlowFiles from documents in MongoDB loaded by a user-specified query. |
|  | [GetMongoRecord](getmongorecord) | A record-based version of GetMongo that uses the Record writers to write the MongoDB result set. |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [GetQueryJobResult](getqueryjobresult) | Gets the results of a Query Job in Salesforce using the Bulk API 2. |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [GetQueryJobStatus](getqueryjobstatus) | Gets the status of a Query Job in Salesforce using the Bulk API 2. |
|  | [GetS3ObjectMetadata](gets3objectmetadata) | Check for the existence of an Object in S3 and fetch its Metadata without attempting to download it. |
|  | [GetS3ObjectTags](gets3objecttags) | Check for the existence of an Object in S3 and fetch its Tags without attempting to download it. |
|  | [GetSFTP](getsftp) | Fetches files from an SFTP Server and creates FlowFiles from them |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [GetSharepointSiteGroupMembers](getsharepointsitegroupmembers) | Retrieves all members of a SharePoint site group. |
|  | [GetShopify](getshopify) | Retrieves objects from a custom Shopify store. |
|  | [GetSmbFile](getsmbfile) | Reads file from a samba network location to FlowFiles. |
|  | [GetSplunk](getsplunk) | Retrieves data from Splunk Enterprise. |
|  | [GetSQS](getsqs) | Fetches messages from an Amazon Simple Queuing Service Queue |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [GetUnityCatalogFile](getunitycatalogfile) | Read a Unity Catalog file up to 5 GiB. |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [GetUnityCatalogFileMetadata](getunitycatalogfilemetadata) | Checks for Unity Catalog file metadata. |
|  | [GetWorkdayReport](getworkdayreport) | A processor which can interact with a configurable Workday Report. |
|  | [GetZendesk](getzendesk) | Incrementally fetches data from Zendesk API. |

See moreShow less

Expand

## H

|  | Processor | Description |
| --- | --- | --- |
|  | [HandleHttpRequest](handlehttprequest) | Starts an HTTP Server and listens for HTTP Requests. |
|  | [HandleHttpResponse](handlehttpresponse) | Sends an HTTP Response to the Requestor that generated a FlowFile. |

See moreShow less

Expand

## I

|  | Processor | Description |
| --- | --- | --- |
|  | [IdentifyMimeType](identifymimetype) | Attempts to identify the MIME Type used for a FlowFile. |
|  | [InvokeHTTP](invokehttp) | An HTTP client processor which can interact with a configurable HTTP Endpoint. |
|  | [InvokeScriptedProcessor](invokescriptedprocessor) | Experimental - Invokes a script engine for a Processor defined in the given script. |
|  | [ISPEnrichIP](ispenrichip) | Looks up ISP information for an IP address and adds the information to FlowFile attributes. |

See moreShow less

Expand

## J

|  | Processor | Description |
| --- | --- | --- |
|  | [JoinEnrichment](joinenrichment) | Joins together Records from two different FlowFiles where one FlowFile, the ‘original’ contains arbitrary records and the second FlowFile, the ‘enrichment’ contains additional data that should be used to enrich the first. |
|  | [JoltTransformJSON](jolttransformjson) | Applies a list of Jolt specifications to either the FlowFile JSON content or a specified FlowFile JSON attribute. |
|  | [JoltTransformRecord](jolttransformrecord) | Applies a JOLT specification to each record in the FlowFile payload. |
|  | [JSLTTransformJSON](jslttransformjson) | Applies a JSLT transformation to the FlowFile JSON payload. |
|  | [JsonQueryElasticsearch](jsonqueryelasticsearch) | A processor that allows the user to run a query (with aggregations) written with the Elasticsearch JSON DSL. |

See moreShow less

Expand

## L

|  | Processor | Description |
| --- | --- | --- |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [ListArchivedHubSpotData](listarchivedhubspotdata) | Lists archived data from HubSpot for the chosen object type and generates one FlowFile per listed object with the corresponding metadata as FlowFile attributes. |
|  | [ListAzureBlobStorage\_v12](listazureblobstorage_v12) | Lists blobs in an Azure Blob Storage container. |
|  | [ListAzureDataLakeStorage](listazuredatalakestorage) | Lists directory in an Azure Data Lake Storage Gen 2 filesystem |
|  | [ListBoxFile](listboxfile) | Lists files in a Box folder. |
|  | [ListBoxFileInfo](listboxfileinfo) | Fetches file metadata for each file in a Box Folder. |
|  | [ListBoxFileMetadataInstances](listboxfilemetadatainstances) | Retrieves all metadata instances associated with a Box file. |
|  | [ListBoxFileMetadataTemplates](listboxfilemetadatatemplates) | Retrieves all metadata templates associated with a Box file. |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [ListConfluenceGroups](listconfluencegroups) | Processor listing Confluence groups. |
|  | [ListDatabaseTables](listdatabasetables) | Generates a set of flow files, each containing attributes corresponding to metadata about a table from a database connection. |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [ListDBFSDirectory](listdbfsdirectory) | List file names in a DBFS directory and output a new FlowFile with the filename. |
|  | [ListDropbox](listdropbox) | Retrieves a listing of files from Dropbox (shortcuts are ignored). |
|  | [ListenFTP](listenftp) | Starts an FTP server that listens on the specified port and transforms incoming files into FlowFiles. |
|  | [ListenHTTP](listenhttp) | Starts an HTTP Server and listens on a given base path to transform incoming requests into FlowFiles. |
|  | [ListenOTLP](listenotlp) | Collect OpenTelemetry messages over HTTP or gRPC. |
|  | [ListenSlack](listenslack) | Retrieves real-time messages or Slack commands from one or more Slack conversations. |
|  | [ListenSyslog](listensyslog) | Listens for Syslog messages being sent to a given port over TCP or UDP. |
|  | [ListenTCP](listentcp) | Listens for incoming TCP connections and reads data from each connection using a line separator as the message demarcator. |
|  | [ListenUDP](listenudp) | Listens for Datagram Packets on a given port. |
|  | [ListenUDPRecord](listenudprecord) | Listens for Datagram Packets on a given port and reads the content of each datagram using the configured Record Reader. |
|  | [ListenWebSocket](listenwebsocket) | Acts as a WebSocket server endpoint to accept client connections. |
|  | [ListFile](listfile) | Retrieves a listing of files from the input directory. |
|  | [ListFTP](listftp) | Performs a listing of the files residing on an FTP server. |
|  | [ListGCSBucket](listgcsbucket) | Retrieves a listing of objects from a GCS bucket. |
|  | [ListGoogleDrive](listgoogledrive) | Performs a listing of concrete files (shortcuts are ignored) in a Google Drive folder. |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [ListGoogleDriveFileInfo](listgoogledrivefileinfo) | Lists all files and folders in a specified Google Drive. |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [ListGoogleGroups](listgooglegroups) | Lists all of the groups for a given domain in Google Workspace. |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [ListHubSpotObjects](listhubspotobjects) | Fetches data from HubSpot for specified object types, and generates one FlowFile per listed object with the corresponding metadata as FlowFile attributes. |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [ListMicrosoftDataverseTables](listmicrosoftdataversetables) | List Tables from Microsoft Dataverse environments |
|  | [ListS3](lists3) | Retrieves a listing of objects from an S3 bucket. |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [ListSFDCDataShares](listsfdcdatashares) | List the available data shares in the organization that are available to the identified user. |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [ListSFDCObjects](listsfdcobjects) | List the available objects in the organization that are available to the identified user. |
|  | [ListSFTP](listsftp) | Performs a listing of the files residing on an SFTP server. |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [ListSharepointDrives](listsharepointdrives) | Emits a FlowFile for each Drive present in the specified Sharepoint Site. |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [ListSharepointSiteGroups](listsharepointsitegroups) | Lists all SharePoint site groups available on a specified SharePoint site. |
|  | [ListSmb](listsmb) | Lists concrete files shared via SMB protocol. |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [ListTableNames](listtablenames) | Fetches all source table names and matches them with one of the possible configurations: - regexp expression e. |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [ListUnityCatalogDirectory](listunitycatalogdirectory) | List file names in a Unity Catalog directory and output a new FlowFile with the filename. |
|  | [LogAttribute](logattribute) | Emits attributes of the FlowFile at the specified log level |
|  | [LogMessage](logmessage) | Emits a log message at the specified log level |
|  | [LookupAttribute](lookupattribute) | Lookup attributes from a lookup service |
|  | [LookupRecord](lookuprecord) | Extracts one or more fields from a Record and looks up a value for those fields in a LookupService. |

See moreShow less

Expand

## M

|  | Processor | Description |
| --- | --- | --- |
|  | [MergeContent](mergecontent) | Merges a Group of FlowFiles together based on a user-defined strategy and packages them into a single FlowFile. |
|  | [MergeRecord](mergerecord) | This Processor merges together multiple record-oriented FlowFiles into a single FlowFile that contains all of the Records of the input FlowFiles. |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [MergeSnowflakeJournalTable](mergesnowflakejournaltable) | Triggers a merge operation on changes from journal table to a destination table in Snowflake. |
|  | [ModifyBytes](modifybytes) | Discard byte range at the start and end or all content of a binary file. |
|  | [ModifyCompression](modifycompression) | Changes the compression algorithm used to compress the contents of a FlowFile by decompressing the contents of FlowFiles using a user-specified compression algorithm and recompressing the contents using the specified compression format properties. |
|  | [MonitorActivity](monitoractivity) | Monitors the flow for activity and sends out an indicator when the flow has not had any data for some specified amount of time and again when the flow’s activity is restored |
|  | [MoveAzureDataLakeStorage](moveazuredatalakestorage) | Moves content within an Azure Data Lake Storage Gen 2. |

See moreShow less

Expand

## N

|  | Processor | Description |
| --- | --- | --- |
|  | [Notify](notify) | Caches a release signal identifier in the distributed cache, optionally along with the FlowFile’s attributes. |

See moreShow less

Expand

## O

|  | Processor | Description |
| --- | --- | --- |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [OpenAiTranscribeAudio](openaitranscribeaudio) | Transcribes audio into English text. |

See moreShow less

Expand

## P

|  | Processor | Description |
| --- | --- | --- |
|  | [PackageFlowFile](packageflowfile) | This processor will package FlowFile attributes and content into an output FlowFile that can be exported from NiFi and imported back into NiFi, preserving the original attributes and content. |
|  | [PaginatedJsonQueryElasticsearch](paginatedjsonqueryelasticsearch) | A processor that allows the user to run a paginated query (with aggregations) written with the Elasticsearch JSON DSL. |
|  | [ParseEvtx](parseevtx) | Parses the contents of a Windows Event Log file (evtx) and writes the resulting XML to the FlowFile |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [ParseExcelCellReference](parseexcelcellreference) | Processor responsible for parsing Excel cell reference formula. |
|  | [ParseSyslog](parsesyslog) | Attempts to parses the contents of a Syslog message in accordance to RFC5424 and RFC3164 formats and adds attributes to the FlowFile for each of the parts of the Syslog message. |
|  | [ParseSyslog5424](parsesyslog5424) | Attempts to parse the contents of a well formed Syslog message in accordance to RFC5424 format and adds attributes to the FlowFile for each of the parts of the Syslog message, including Structured Data. |
|  | [PartitionRecord](partitionrecord) | Splits, or partitions, record-oriented data based on the configured fields in the data. |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [PerformSnowflakeCortexOCR](performsnowflakecortexocr) | Performs Optical Character Recognition (OCR) on PDF documents using Snowflake Cortex ML functions. |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [PickTablesForReplication](picktablesforreplication) | Accepts a list of fully qualified table names and determines if a table: - is new (is not replicated, but was added in the source) - is existing (is replicated and exists in the source) - is stale (is replicated but no longer exists in the source) Configuration is passed as a FlowFile attribute. |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [PromptAnthropicAI](promptanthropicai) | Sends a prompt to Anthropic, writing the response either as a FlowFile attribute or to the contents of the incoming FlowFile. |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [PromptAzureOpenAI](promptazureopenai) | Sends a prompt to Azure’s OpenAI service, writing the response either as a FlowFile attribute or to the contents of the incoming FlowFile. |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [PromptLLM](promptllm) | This processor sends a user defined prompt to a Large Language Model (LLM) to respond. |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [PromptOpenAI](promptopenai) | Sends a prompt to OpenAI, writing the response either as a FlowFile attribute or to the contents of the incoming FlowFile. |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [PromptSnowflakeCortex](promptsnowflakecortex) | Sends a prompt to Snowflake Cortex, writing the response either as a FlowFile attribute or to the contents of the incoming FlowFile. |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [PromptVertexAI](promptvertexai) | Sends a prompt to VertexAI, writing the response either as a FlowFile attribute or to the contents of the incoming FlowFile. |
|  | [PublishAMQP](publishamqp) | Creates an AMQP Message from the contents of a FlowFile and sends the message to an AMQP Exchange. |
|  | [PublishGCPubSub](publishgcpubsub) | Publishes the content of the incoming flowfile to the configured Google Cloud PubSub topic. |
|  | [PublishJMS](publishjms) | Creates a JMS Message from the contents of a FlowFile and sends it to a JMS Destination (queue or topic) as JMS BytesMessage or TextMessage. |
|  | [PublishKafka](publishkafka) | Sends the contents of a FlowFile as either a message or as individual records to Apache Kafka using the Kafka Producer API. |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [PublishKafka](publishkafka) | Sends the contents of a FlowFile as either a message or as individual records to Apache Kafka using the Kafka Producer API. |
|  | [PublishMQTT](publishmqtt) | Publishes a message to an MQTT topic |
|  | [PublishSlack](publishslack) | Posts a message to the specified Slack channel. |
|  | [PutAzureBlobStorage\_v12](putazureblobstorage_v12) | Puts content into a blob on Azure Blob Storage. |
|  | [PutAzureCosmosDBRecord](putazurecosmosdbrecord) | This processor is a record-aware processor for inserting data into Cosmos DB with Core SQL API. |
|  | [PutAzureDataExplorer](putazuredataexplorer) | Acts as an Azure Data Explorer sink which sends FlowFiles to the provided endpoint. |
|  | [PutAzureDataLakeStorage](putazuredatalakestorage) | Writes the contents of a FlowFile as a file on Azure Data Lake Storage Gen 2 |
|  | [PutAzureEventHub](putazureeventhub) | Send FlowFile contents to Azure Event Hubs |
|  | [PutAzureQueueStorage\_v12](putazurequeuestorage_v12) | Writes the content of the incoming FlowFiles to the configured Azure Queue Storage. |
|  | [PutBigQuery](putbigquery) | Writes the contents of a FlowFile to a Google BigQuery table. |
|  | [PutBoxFile](putboxfile) | Puts content to a Box folder. |
|  | [PutCloudWatchMetric](putcloudwatchmetric) | Publishes metrics to Amazon CloudWatch. |
|  | [PutDatabaseRecord](putdatabaserecord) | The PutDatabaseRecord processor uses a specified RecordReader to input (possibly multiple) records from an incoming flow file. |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [PutDatabricksSQL](putdatabrickssql) | Submit a SQL Execution using Databricks REST API then write the JSON response to FlowFile Content. |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [PutDBFSFile](putdbfsfile) | Write FlowFile content to DBFS. |
|  | [PutDistributedMapCache](putdistributedmapcache) | Gets the content of a FlowFile and puts it to a distributed map cache, using a cache key computed from FlowFile attributes. |
|  | [PutDropbox](putdropbox) | Puts content to a Dropbox folder. |
|  | [PutDynamoDB](putdynamodb) | Puts a document from DynamoDB based on hash and range key. |
|  | [PutDynamoDBRecord](putdynamodbrecord) | Inserts items into DynamoDB based on record-oriented data. |
|  | [PutElasticsearchJson](putelasticsearchjson) | An Elasticsearch put processor that uses the official Elastic REST client libraries. |
|  | [PutElasticsearchRecord](putelasticsearchrecord) | A record-aware Elasticsearch put processor that uses the official Elastic REST client libraries. |
|  | [PutEmail](putemail) | Sends an e-mail to configured recipients for each incoming FlowFile |
|  | [PutFile](putfile) | Writes the contents of a FlowFile to the local file system |
|  | [PutFTP](putftp) | Sends FlowFiles to an FTP Server |
|  | [PutGCSObject](putgcsobject) | Writes the contents of a FlowFile as an object in a Google Cloud Storage. |
|  | [PutGoogleDrive](putgoogledrive) | Writes the contents of a FlowFile as a file in Google Drive. |
|  | [PutGridFS](putgridfs) | Writes a file to a GridFS bucket. |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [PutHubSpot](puthubspot) | Upsert a HubSpot object. |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [PutIcebergTable](puticebergtable) | Store records in Iceberg using configurable Catalog for managing namespaces and tables. |
|  | [PutKinesisFirehose](putkinesisfirehose) | Sends the contents to a specified Amazon Kinesis Firehose. |
|  | [PutKinesisStream](putkinesisstream) | Sends the contents to a specified Amazon Kinesis. |
|  | [PutLambda](putlambda) | Sends the contents to a specified Amazon Lambda Function. |
|  | [PutMongo](putmongo) | Writes the contents of a FlowFile to MongoDB |
|  | [PutMongoBulkOperations](putmongobulkoperations) | Writes the contents of a FlowFile to MongoDB as bulk-update |
|  | [PutMongoRecord](putmongorecord) | This processor is a record-aware processor for inserting/upserting data into MongoDB. |
|  | [PutRecord](putrecord) | The PutRecord processor uses a specified RecordReader to input (possibly multiple) records from an incoming flow file, and sends them to a destination specified by a Record Destination Service (i. |
|  | [PutRedisHashRecord](putredishashrecord) | Puts record field data into Redis using a specified hash value, which is determined by a RecordPath to a field in each record containing the hash value. |
|  | [PutS3Object](puts3object) | Writes the contents of a FlowFile as an S3 Object to an Amazon S3 Bucket. |
|  | [PutSalesforceObject](putsalesforceobject) | Creates new records for the specified Salesforce sObject. |
|  | [PutSFTP](putsftp) | Sends FlowFiles to an SFTP Server |
|  | [PutSmbFile](putsmbfile) | Writes the contents of a FlowFile to a samba network location. |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [PutSnowflakeInternalStageFile](putsnowflakeinternalstagefile) | Puts files into a Snowflake internal stage. |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [PutSnowpipeStreaming](putsnowpipestreaming) | Streams records into a Snowflake table. |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [PutSnowpipeStreaming2](putsnowpipestreaming2) | Send Records formatted as Newline Delimited JSON to Snowflake Database Pipes using Snowpipe Streaming Version 2. |
|  | [PutSNS](putsns) | Sends the content of a FlowFile as a notification to the Amazon Simple Notification Service |
|  | [PutSplunk](putsplunk) | Sends logs to Splunk Enterprise over TCP, TCP + TLS/SSL, or UDP. |
|  | [PutSplunkHTTP](putsplunkhttp) | Sends flow file content to the specified Splunk server over HTTP or HTTPS. |
|  | [PutSQL](putsql) | Executes a SQL UPDATE or INSERT command. |
|  | [PutSQS](putsqs) | Publishes a message to an Amazon Simple Queuing Service Queue |
|  | [PutSyslog](putsyslog) | Sends Syslog messages to a given host and port over TCP or UDP. |
|  | [PutTCP](puttcp) | Sends serialized FlowFiles or Records over TCP to a configurable destination with optional support for TLS |
|  | [PutUDP](putudp) | The PutUDP processor receives a FlowFile and packages the FlowFile content into a single UDP datagram packet which is then transmitted to the configured UDP server. |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [PutUnityCatalogFile](putunitycatalogfile) | Write FlowFile content with max size of 5 GiB to Unity Catalog. |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [PutVectaraDocument](putvectaradocument) | Generate and upload a JSON document to Vectara’s upload endpoint. |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [PutVectaraFile](putvectarafile) | Upload a FlowFile content to Vectara’s index endpoint. |
|  | [PutWebSocket](putwebsocket) | Sends messages to a WebSocket remote endpoint using a WebSocket session that is established by either ListenWebSocket or ConnectWebSocket. |
|  | [PutZendeskTicket](putzendeskticket) | Create Zendesk tickets using the Zendesk API. |

See moreShow less

Expand

## Q

|  | Processor | Description |
| --- | --- | --- |
|  | [QueryAzureDataExplorer](queryazuredataexplorer) | Query Azure Data Explorer and stream JSON results to output FlowFiles |
|  | [QueryDatabaseTable](querydatabasetable) | Generates a SQL select query, or uses a provided statement, and executes it to fetch all rows whose values in the specified Maximum Value column(s) are larger than the previously-seen maxima. |
|  | [QueryDatabaseTableRecord](querydatabasetablerecord) | Generates a SQL select query, or uses a provided statement, and executes it to fetch all rows whose values in the specified Maximum Value column(s) are larger than the previously-seen maxima. |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [QueryMilvus](querymilvus) | Queries a given collection in a Milvus database using vectors. |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [QueryPinecone](querypinecone) | Queries Pinecone for vectors that are similar to the input vector, or retrieves a vector by ID. |
|  | [QueryRecord](queryrecord) | Evaluates one or more SQL queries against the contents of a FlowFile. |
|  | [QuerySalesforceObject](querysalesforceobject) | Retrieves records from a Salesforce sObject. |
|  | [QuerySplunkIndexingStatus](querysplunkindexingstatus) | Queries Splunk server in order to acquire the status of indexing acknowledgement. |

See moreShow less

Expand

## R

|  | Processor | Description |
| --- | --- | --- |
|  | [RemoveRecordField](removerecordfield) | Modifies the contents of a FlowFile that contains Record-oriented data (i. |
|  | [RenameRecordField](renamerecordfield) | Renames one or more fields in each Record of a FlowFile. |
|  | [ReplaceText](replacetext) | Updates the content of a FlowFile by searching for some textual value in the FlowFile content (via Regular Expression/regex, or literal value) and replacing the section of the content that matches with some alternate value. |
|  | [ReplaceTextWithMapping](replacetextwithmapping) | Updates the content of a FlowFile by evaluating a Regular Expression against it and replacing the section of the content that matches the Regular Expression with some alternate value provided in a mapping file. |
|  | [RetryFlowFile](retryflowfile) | FlowFiles passed to this Processor have a ‘Retry Attribute’ value checked against a configured ‘Maximum Retries’ value. |
|  | [RouteOnAttribute](routeonattribute) | Routes FlowFiles based on their Attributes using the Attribute Expression Language |
|  | [RouteOnContent](routeoncontent) | Applies Regular Expressions to the content of a FlowFile and routes a copy of the FlowFile to each destination whose Regular Expression matches. |
|  | [RouteText](routetext) | Routes textual data based on a set of user-defined rules. |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [RunDatabricksJob](rundatabricksjob) | Triggers a pre-defined Databricks job to run with custom parameters. |
|  | [RunMongoAggregation](runmongoaggregation) | A processor that runs an aggregation query whenever a flowfile is received. |

See moreShow less

Expand

## S

|  | Processor | Description |
| --- | --- | --- |
|  | [SampleRecord](samplerecord) | Samples the records of a FlowFile based on a specified sampling strategy (such as Reservoir Sampling). |
|  | [ScanAttribute](scanattribute) | Scans the specified attributes of FlowFiles, checking to see if any of their values are present within the specified dictionary of terms |
|  | [ScanContent](scancontent) | Scans the content of FlowFiles for terms that are found in a user-supplied dictionary. |
|  | [ScriptedFilterRecord](scriptedfilterrecord) | This processor provides the ability to filter records out from FlowFiles using the user-provided script. |
|  | [ScriptedPartitionRecord](scriptedpartitionrecord) | Receives Record-oriented data (i. |
|  | [ScriptedTransformRecord](scriptedtransformrecord) | Provides the ability to evaluate a simple script against each record in an incoming FlowFile. |
|  | [ScriptedValidateRecord](scriptedvalidaterecord) | This processor provides the ability to validate records in FlowFiles using the user-provided script. |
|  | [SearchElasticsearch](searchelasticsearch) | A processor that allows the user to repeatedly run a paginated query (with aggregations) written with the Elasticsearch JSON DSL. |
|  | [SegmentContent](segmentcontent) | Segments a FlowFile into multiple smaller segments on byte boundaries. |
|  | [SignContentPGP](signcontentpgp) | Sign content using OpenPGP Private Keys |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [SnowflakeDetectDuplicate](snowflakedetectduplicate) | Checks if a FlowFile ‘s hash (provided as a FlowFile attribute) is already in a Snowflake table, and routes the FlowFile to’ duplicate ‘if found,’distinct ‘if not found, or’ failure’ on errors. |
|  | [SplitAvro](splitavro) | Splits a binary encoded Avro datafile into smaller files based on the configured Output Size. |
|  | [SplitContent](splitcontent) | Splits incoming FlowFiles by a specified byte sequence |
|  | [SplitExcel](splitexcel) | This processor splits a multi sheet Microsoft Excel spreadsheet into multiple Microsoft Excel spreadsheets where each sheet from the original file is converted to an individual spreadsheet in its own flow file. |
|  | [SplitJson](splitjson) | Splits a JSON File into multiple, separate FlowFiles for an array element specified by a JsonPath expression. |
|  | [SplitRecord](splitrecord) | Splits up an input FlowFile that is in a record-oriented data format into multiple smaller FlowFiles |
|  | [SplitText](splittext) | Splits a text file into multiple smaller text files on line boundaries limited by maximum number of lines or total size of fragment. |
|  | [SplitXml](splitxml) | Splits an XML File into multiple separate FlowFiles, each comprising a child or descendant of the original root element |
|  | [StartAwsPollyJob](startawspollyjob) | Trigger a AWS Polly job. |
|  | [StartAwsTextractJob](startawstextractjob) | Trigger a AWS Textract job. |
|  | [StartAwsTranscribeJob](startawstranscribejob) | Trigger a AWS Transcribe job. |
|  | [StartAwsTranslateJob](startawstranslatejob) | Trigger a AWS Translate job. |
|  | [StartGcpVisionAnnotateFilesOperation](startgcpvisionannotatefilesoperation) | Trigger a Vision operation on file input. |
|  | [StartGcpVisionAnnotateImagesOperation](startgcpvisionannotateimagesoperation) | Trigger a Vision operation on image input. |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [SubmitQueryJob](submitqueryjob) | Submits a Query Job to Salesforce using the Bulk API 2. |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [SummarizeText](summarizetext) | This processor uses a Large Language Model (LLM) to summarize the content of a FlowFile. |

See moreShow less

Expand

## T

|  | Processor | Description |
| --- | --- | --- |
|  | [TagS3Object](tags3object) | Adds or updates a tag on an Amazon S3 Object. |
|  | [TailFile](tailfile) | “Tails” a file, or a list of files, ingesting data from the file as it is written to the file. |
|  | [TransformXml](transformxml) | Applies the provided XSLT file to the FlowFile XML payload. |

See moreShow less

Expand

## U

|  | Processor | Description |
| --- | --- | --- |
|  | [UnpackContent](unpackcontent) | Unpacks the content of FlowFiles that have been packaged with one of several different Packaging Formats, emitting one to many FlowFiles for each input FlowFile. |
|  | [UpdateAttribute](updateattribute) | Updates the Attributes for a FlowFile by using the Attribute Expression Language and/or deletes the attributes based on a regular expression |
|  | [UpdateBoxFileMetadataInstance](updateboxfilemetadatainstance) | Updates metadata template values for a Box file using the record in the given flowFile. |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [UpdateBulkJobState](updatebulkjobstate) | Updates the status of a Salesforce Bulk Job in the shared state service for a specific object type |
|  | [UpdateByQueryElasticsearch](updatebyqueryelasticsearch) | Update documents in an Elasticsearch index using a query. |
|  | [UpdateCounter](updatecounter) | This processor allows users to set specific counters and key points in their flow. |
|  | [UpdateDatabaseTable](updatedatabasetable) | This processor uses a JDBC connection and incoming records to generate any database table changes needed to support the incoming records. |
|  | [UpdateRecord](updaterecord) | Updates the contents of a FlowFile that contains Record-oriented data (i. |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [UpdateSnowflakeDatabase](updatesnowflakedatabase) | Updates the definition of a Snowflake table based on the schema provided in the incoming FlowFile. |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [UpdateSnowflakeIcebergDatabase](updatesnowflakeicebergdatabase) | Updates the definition of a Snowflake Iceberg table. |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [UpdateSnowflakeSchema](updatesnowflakeschema) | Creates Snowflake database schema if it does not exist. |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [UpdateSnowflakeStream](updatesnowflakestream) | Manages Snowflake streams by creating, dropping, or replacing them based on the configured operation. |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [UpdateSnowflakeTable](updatesnowflaketable) | Updates the definition of a Snowflake table based on the schema provided in the incoming FlowFile. |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [UpdateSnowflakeView](updatesnowflakeview) | Creates or replaces Snowflake views based on column mappings provided in the incoming FlowFile. |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [UpdateTableState](updatetablestate) | Updates the state of a table in the Table State Service |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [UpsertMilvus](upsertmilvus) | Upserts vectors into Milvus database for a given collection |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [UpsertPinecone](upsertpinecone) | Publishes vectors, including metadata, and optionally text, to a Pinecone index. |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [UpsertSFDCObjects](upsertsfdcobjects) | Upserts the records from the incoming FlowFile into Salesforce |

See moreShow less

Expand

## V

|  | Processor | Description |
| --- | --- | --- |
|  | [ValidateCsv](validatecsv) | Validates the contents of FlowFiles or a FlowFile attribute value against a user-specified CSV schema. |
|  | [ValidateJson](validatejson) | Validates the contents of FlowFiles against a configurable JSON Schema. |
|  | [ValidateRecord](validaterecord) | Validates the Records of an incoming FlowFile against a given schema. |
|  | [ValidateXml](validatexml) | Validates XML contained in a FlowFile. |
|  | [VerifyContentMAC](verifycontentmac) | Calculates a Message Authentication Code using the provided Secret Key and compares it with the provided MAC property |
|  | [VerifyContentPGP](verifycontentpgp) | Verify signatures using OpenPGP Public Keys |

See moreShow less

Expand

## W

|  | Processor | Description |
| --- | --- | --- |
|  | [Wait](wait) | Routes incoming FlowFiles to the ‘wait’ relationship until a matching release signal is stored in the distributed cache from a corresponding Notify processor. |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [WaitForTableState](waitfortablestate) | Blocks incoming FlowFiles until the corresponding table state is not equal to accepted state. |

See moreShow less

Expand
