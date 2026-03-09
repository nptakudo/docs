---
title: "All controller services (alphabetical)"
url: "https://docs.snowflake.com/en/user-guide/data-integration/openflow/controllers/index"
---

# All controller services (alphabetical)

Feature — Generally Available

Openflow Snowflake Deployments are available to all accounts in AWS and Azure [Commercial regions](../../../intro-regions.html#label-na-general-regions).

Openflow BYOC deployments are available to all accounts in AWS [Commercial regions](../../../intro-regions.html#label-na-general-regions).

This topic provides a list of all openflow controller services in alphabetical order.
The list includes:

> * Type of controller service (Snowflake or not)
> * The name of each controller service
> * A summary of each controller service

## A

|  | Controller | Description |
| --- | --- | --- |
|  | [ADLSCredentialsControllerService](adlscredentialscontrollerservice) | Defines credentials for ADLS processors. |
|  | [ADLSCredentialsControllerServiceLookup](adlscredentialscontrollerservicelookup) | Provides an ADLSCredentialsService that can be used to dynamically select another ADLSCredentialsService. |
|  | [AmazonGlueEncodedSchemaReferenceReader](amazonglueencodedschemareferencereader) | Reads Schema Identifier according to AWS Glue Schema encoding as a header consisting of a two byte markers and a 16 byte UUID |
|  | [AmazonGlueSchemaRegistry](amazonglueschemaregistry) | Provides a Schema Registry that interacts with the AWS Glue Schema Registry so that those Schemas that are stored in the Glue Schema Registry can be used in NiFi. |
|  | [AmazonMSKConnectionService](amazonmskconnectionservice) | Provides and manages connections to AWS MSK Kafka Brokers for producer or consumer operations. |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [AmazonMSKConnectionService](amazonmskconnectionservice) | Provides and manages connections to AWS MSK Kafka Brokers for producer or consumer operations. |
|  | [ApicurioSchemaRegistry](apicurioschemaregistry) | Provides a Schema Registry that interacts with the Apicurio Schema Registry so that those Schemas that are stored in the Apicurio Schema Registry can be used in NiFi. |
|  | [AvroReader](avroreader) | Parses Avro data and returns each Avro record as an separate Record object. |
|  | [AvroRecordSetWriter](avrorecordsetwriter) | Writes the contents of a RecordSet in Binary Avro format. |
|  | [AvroSchemaRegistry](avroschemaregistry) | Provides a service for registering and accessing schemas. |
|  | [AWSCredentialsProviderControllerService](awscredentialsprovidercontrollerservice) | Defines credentials for Amazon Web Services processors. |
|  | [AzureBlobStorageFileResourceService](azureblobstoragefileresourceservice) | Provides an Azure Blob Storage file resource for other components. |
|  | [AzureCosmosDBClientService](azurecosmosdbclientservice) | Provides a controller service that configures a connection to Cosmos DB (Core SQL API) and provides access to that connection to other Cosmos DB-related components. |
|  | [AzureDataLakeStorageFileResourceService](azuredatalakestoragefileresourceservice) | Provides an Azure Data Lake Storage (ADLS) file resource for other components. |
|  | [AzureEventHubRecordSink](azureeventhubrecordsink) | Format and send Records to Azure Event Hubs |
|  | [AzureStorageCredentialsControllerService\_v12](azurestoragecredentialscontrollerservice_v12) | Provides credentials for Azure Storage processors using Azure Storage client library v12. |
|  | [AzureStorageCredentialsControllerServiceLookup\_v12](azurestoragecredentialscontrollerservicelookup_v12) | Provides an AzureStorageCredentialsService\_v12 that can be used to dynamically select another AzureStorageCredentialsService\_v12. |

See moreShow less

Expand

## C

|  | Controller | Description |
| --- | --- | --- |
|  | [CEFReader](cefreader) | Parses CEF (Common Event Format) events, returning each row as a record. |
|  | [ConfluentEncodedSchemaReferenceReader](confluentencodedschemareferencereader) | Reads Schema Identifier according to Confluent encoding as a header consisting of a byte marker and an integer represented as four bytes |
|  | [ConfluentEncodedSchemaReferenceWriter](confluentencodedschemareferencewriter) | Writes Schema Identifier according to Confluent encoding as a header consisting of a byte marker and an integer represented as four bytes |
|  | [ConfluentProtobufMessageNameResolver](confluentprotobufmessagenameresolver) | Resolves Protobuf message names from Confluent Schema Registry wire format by decoding message indexes and looking up the fully qualified name in the schema definition For Confluent wire format reference see: <https://docs>. |
|  | [ConfluentSchemaRegistry](confluentschemaregistry) | Provides a Schema Registry that interacts with the Confluent Schema Registry so that those Schemas that are stored in the Confluent Schema Registry can be used in NiFi. |
|  | [CSVReader](csvreader) | Parses CSV-formatted data, returning each row in the CSV file as a separate record. |
|  | [CSVRecordLookupService](csvrecordlookupservice) | A reloadable CSV file-based lookup service. |
|  | [CSVRecordSetWriter](csvrecordsetwriter) | Writes the contents of a RecordSet as CSV data. |

See moreShow less

Expand

## D

|  | Controller | Description |
| --- | --- | --- |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [DatabaseLookup](databaselookup) | A Lookup Service that allows for enrichment with a database using a user-specified SQL statement. |
|  | [DatabaseRecordLookupService](databaserecordlookupservice) | A relational-database-based lookup service. |
|  | [DatabaseRecordSink](databaserecordsink) | Provides a service to write records using a configured database connection. |
|  | [DBCPConnectionPool](dbcpconnectionpool) | Provides Database Connection Pooling Service. |
|  | [DBCPConnectionPoolLookup](dbcpconnectionpoollookup) | Provides a DBCPService that can be used to dynamically select another DBCPService. |
|  | [DeveloperBoxClientService](developerboxclientservice) | Provides Box client objects through which Box API calls can be used. |
|  | [DistributedMapCacheLookupService](distributedmapcachelookupservice) | Allows to choose a distributed map cache client to retrieve the value associated to a key. |

See moreShow less

Expand

## E

|  | Controller | Description |
| --- | --- | --- |
|  | [ElasticSearchClientServiceImpl](elasticsearchclientserviceimpl) | A controller service for accessing an Elasticsearch client, using the Elasticsearch (low-level) REST Client. |
|  | [ElasticSearchLookupService](elasticsearchlookupservice) | Lookup a record from Elasticsearch Server associated with the specified document ID. |
|  | [ElasticSearchStringLookupService](elasticsearchstringlookupservice) | Lookup a string value from Elasticsearch Server associated with the specified document ID. |
|  | [EmailRecordSink](emailrecordsink) | Provides a RecordSinkService that can be used to send records in email using the specified writer for formatting. |
|  | [EmbeddedHazelcastCacheManager](embeddedhazelcastcachemanager) | A service that runs embedded Hazelcast and provides cache instances backed by that. |
|  | [ExcelReader](excelreader) | Parses a Microsoft Excel document returning each row in each sheet as a separate record. |
|  | [ExternalHazelcastCacheManager](externalhazelcastcachemanager) | A service that provides cache instances backed by Hazelcast running outside of NiFi. |

See moreShow less

Expand

## F

|  | Controller | Description |
| --- | --- | --- |
|  | [FreeFormTextRecordSetWriter](freeformtextrecordsetwriter) | Writes the contents of a RecordSet as free-form text. |

See moreShow less

Expand

## G

|  | Controller | Description |
| --- | --- | --- |
|  | [GCPCredentialsControllerService](gcpcredentialscontrollerservice) | Defines credentials for Google Cloud Platform processors. |
|  | [GCSFileResourceService](gcsfileresourceservice) | Provides a Google Compute Storage (GCS) file resource for other components. |
|  | [GrokReader](grokreader) | Provides a mechanism for reading unstructured text data, such as log files, and structuring the data so that it can be processed. |

See moreShow less

Expand

## H

|  | Controller | Description |
| --- | --- | --- |
|  | [HazelcastMapCacheClient](hazelcastmapcacheclient) | An implementation of DistributedMapCacheClient that uses Hazelcast as the backing cache. |
|  | [HikariCPConnectionPool](hikaricpconnectionpool) | Provides Database Connection Pooling Service based on HikariCP. |
|  | [HttpRecordSink](httprecordsink) | Format and send Records to a configured uri using HTTP post. |

See moreShow less

Expand

## I

|  | Controller | Description |
| --- | --- | --- |
|  | [IPLookupService](iplookupservice) | A lookup service that provides several types of enrichment information for IP addresses. |

See moreShow less

Expand

## J

|  | Controller | Description |
| --- | --- | --- |
|  | [JettyWebSocketClient](jettywebsocketclient) | Implementation of WebSocketClientService. |
|  | [JettyWebSocketServer](jettywebsocketserver) | Implementation of WebSocketServerService. |
|  | [JMSConnectionFactoryProvider](jmsconnectionfactoryprovider) | Provides a generic service to create vendor specific javax. |
|  | [JndiJmsConnectionFactoryProvider](jndijmsconnectionfactoryprovider) | Provides a service to lookup an existing JMS ConnectionFactory using the Java Naming and Directory Interface (JNDI). |
|  | [JsonConfigBasedBoxClientService](jsonconfigbasedboxclientservice) | Provides Box client objects through which Box API calls can be used. |
|  | [JsonPathReader](jsonpathreader) | Parses JSON records and evaluates user-defined JSON Path ‘s against each JSON object. |
|  | [JsonRecordSetWriter](jsonrecordsetwriter) | Writes the results of a RecordSet as either a JSON Array or one JSON object per line. |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [JsonTableColumnFilter](jsontablecolumnfilter) | Provides a table column filter based on a JSON configuration. |
|  | [JsonTreeReader](jsontreereader) | Parses JSON into individual Record objects. |
|  | [JWTBearerOAuth2AccessTokenProvider](jwtbeareroauth2accesstokenprovider) | Provides OAuth 2. |

See moreShow less

Expand

## K

|  | Controller | Description |
| --- | --- | --- |
|  | [Kafka3ConnectionService](kafka3connectionservice) | Provides and manages connections to Kafka Brokers for producer or consumer operations. |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [Kafka3ConnectionService](kafka3connectionservice) | Provides and manages connections to Kafka Brokers for producer or consumer operations. |

See moreShow less

Expand

## L

|  | Controller | Description |
| --- | --- | --- |
|  | [LoggingRecordSink](loggingrecordsink) | Provides a RecordSinkService that can be used to log records to the application log (nifi-app. |

See moreShow less

Expand

## M

|  | Controller | Description |
| --- | --- | --- |
|  | [MapCacheClientService](mapcacheclientservice) | Provides the ability to communicate with a MapCacheServer. |
|  | [MapCacheServer](mapcacheserver) | Provides a map (key/value) cache that can be accessed over a socket. |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [MicrosoftClientCertificateOAuth2TokenProvider](microsoftclientcertificateoauth2tokenprovider) | Provides OAuth2 access tokens for the Microsoft Graph API using client\_credentials with a client certificate. |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [MicrosoftGraphAuthenticationProvider](microsoftgraphauthenticationprovider) | Provides authentication for the Microsoft Graph API, which can be used for interacting with Microsoft 365 services. |
|  | [MongoDBControllerService](mongodbcontrollerservice) | Provides a controller service that configures a connection to MongoDB and provides access to that connection to other Mongo-related components. |
|  | [MongoDBLookupService](mongodblookupservice) | Provides a lookup service based around MongoDB. |

See moreShow less

Expand

## P

|  | Controller | Description |
| --- | --- | --- |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [ParquetIcebergWriter](parqueticebergwriter) | Provides record serialization for Apache Iceberg using Apache Parquet formatting |
|  | [PEMEncodedSSLContextProvider](pemencodedsslcontextprovider) | SSLContext Provider configurable using PEM Private Key and Certificate files. |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [PolarisIcebergCatalog](polarisicebergcatalog) | Provides Apache Iceberg integration with Apache Polaris Catalog access over REST HTTP |
|  | [PropertiesFileLookupService](propertiesfilelookupservice) | A reloadable properties file-based lookup service |
|  | [ProtobufReader](protobufreader) | Parses a Protocol Buffers message from binary format. |

See moreShow less

Expand

## R

|  | Controller | Description |
| --- | --- | --- |
|  | [ReaderLookup](readerlookup) | Provides a RecordReaderFactory that can be used to dynamically select another RecordReaderFactory. |
|  | [RecordSetWriterLookup](recordsetwriterlookup) | Provides a RecordSetWriterFactory that can be used to dynamically select another RecordSetWriterFactory. |
|  | [RecordSinkServiceLookup](recordsinkservicelookup) | Provides a RecordSinkService that can be used to dynamically select another RecordSinkService. |
|  | [RedisConnectionPoolService](redisconnectionpoolservice) | A service that provides connections to Redis. |
|  | [RedisDistributedMapCacheClientService](redisdistributedmapcacheclientservice) | An implementation of DistributedMapCacheClient that uses Redis as the backing cache. |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [RemoveFieldRecordReader](removefieldrecordreader) | A wrapper for a RecordReaderFactory that supports filtering out specified fields from NiFi Records. |
|  | [RestLookupService](restlookupservice) | Use a REST service to look up values. |

See moreShow less

Expand

## S

|  | Controller | Description |
| --- | --- | --- |
|  | [S3FileResourceService](s3fileresourceservice) | Provides an Amazon Web Services (AWS) S3 file resource for other components. |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [SalesforceDataCloudOAuthTokenProvider](salesforcedatacloudoauthtokenprovider) | Retrieves an OAuth2 access token from Salesforce using the configured OAuth2 Access Token Provider and exchanges the token for a Data Cloud API token. |
|  | [ScriptedLookupService](scriptedlookupservice) | Allows the user to provide a scripted LookupService instance in order to enrich records from an incoming flow file. |
|  | [ScriptedReader](scriptedreader) | Allows the user to provide a scripted RecordReaderFactory instance in order to read/parse/generate records from an incoming flow file. |
|  | [ScriptedRecordSetWriter](scriptedrecordsetwriter) | Allows the user to provide a scripted RecordSetWriterFactory instance in order to write records to an outgoing flow file. |
|  | [ScriptedRecordSink](scriptedrecordsink) | Allows the user to provide a scripted RecordSinkService instance in order to transmit records to the desired target. |
|  | [SetCacheClientService](setcacheclientservice) | Provides the ability to communicate with a SetCacheServer. |
|  | [SetCacheServer](setcacheserver) | Provides a set (collection of unique values) cache that can be accessed over a socket. |
|  | [SimpleCsvFileLookupService](simplecsvfilelookupservice) | A reloadable CSV file-based lookup service. |
|  | [SimpleDatabaseLookupService](simpledatabaselookupservice) | A relational-database-based lookup service. |
|  | [SimpleKeyValueLookupService](simplekeyvaluelookupservice) | Allows users to add key/value pairs as User-defined Properties. |
|  | [SimpleRedisDistributedMapCacheClientService](simpleredisdistributedmapcacheclientservice) | An implementation of DistributedMapCacheClient that uses Redis as the backing cache. |
|  | [SimpleScriptedLookupService](simplescriptedlookupservice) | Allows the user to provide a scripted LookupService instance in order to enrich records from an incoming flow file. |
|  | [SlackRecordSink](slackrecordsink) | Format and send Records to a configured Channel using the Slack Post Message API. |
|  | [SmbjClientProviderService](smbjclientproviderservice) | Provides access to SMB Sessions with shared authentication credentials. |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [SnowflakeConnectionService](snowflakeconnectionservice) | Provides pooled database connections to Snowflake services |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [SnowflakeDatabaseDialectService](snowflakedatabasedialectservice) | Database Dialect Service supporting Snowflake. |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [SnowflakeSignJWTService](snowflakesignjwtservice) | Provides OAuth2 access token using a JWT signed with a secret stored in Snowflake. |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [SnowflakeTableSchemaRegistry](snowflaketableschemaregistry) | Uses Snowflake tables as the source of schema — utilises Snowpipe Streaming REST API. |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [StandardAnthropicLLMService](standardanthropicllmservice) | A Controller Service that provides integration with Anthropic’s Claude AI models through their Messages API. |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [StandardAtlassianRequestRateManager](standardatlassianrequestratemanager) | Provides rate limiting coordination for Atlassian API calls across processors to prevent cascading rate limit issues. |
|  | [StandardAzureCredentialsControllerService](standardazurecredentialscontrollerservice) | Provide credentials to use with an Azure client. |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [StandardConfluenceClientService](standardconfluenceclientservice) | Provides connection service to Confluence APIs |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [StandardDatabricksWorkspaceClientService](standarddatabricksworkspaceclientservice) | Databricks client. |
|  | [StandardDropboxCredentialService](standarddropboxcredentialservice) | Defines credentials for Dropbox processors. |
|  | [StandardFileResourceService](standardfileresourceservice) | Provides a file resource for other components. |
|  | [StandardHashiCorpVaultClientService](standardhashicorpvaultclientservice) | A controller service for interacting with HashiCorp Vault. |
|  | [StandardHttpContextMap](standardhttpcontextmap) | Provides the ability to store and retrieve HTTP requests and responses external to a Processor, so that multiple Processors can interact with the same HTTP request. |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [StandardHubSpotClientService](standardhubspotclientservice) | HubSpot Controller Service to integrate with HubSpot HTTP api. |
|  | [StandardJsonSchemaRegistry](standardjsonschemaregistry) | Provides a service for registering and accessing JSON schemas. |
|  | [StandardKustoIngestService](standardkustoingestservice) | Sends batches of flowfile content or stream flowfile content to an Azure ADX cluster. |
|  | [StandardKustoQueryService](standardkustoqueryservice) | Standard implementation of Kusto Query Service for Azure Data Explorer |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [StandardMilvusConnectionService](standardmilvusconnectionservice) | Provides connection service to a Milvus instance |
|  | [StandardOauth2AccessTokenProvider](standardoauth2accesstokenprovider) | Provides OAuth 2. |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [StandardOCRService](standardocrservice) | Provides integration to Openflow OCR Service |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [StandardOpenAILLMService](standardopenaillmservice) | A Controller Service that provides integration with OpenAI’s Chat Completion API. |
|  | [StandardPGPPrivateKeyService](standardpgpprivatekeyservice) | PGP Private Key Service provides Private Keys loaded from files or properties |
|  | [StandardPGPPublicKeyService](standardpgppublickeyservice) | PGP Public Key Service providing Public Keys loaded from files |
|  | [StandardPrivateKeyService](standardprivatekeyservice) | Private Key Service provides access to a Private Key loaded from configured sources |
|  | [StandardProtobufReader](standardprotobufreader) | Parses Protocol Buffers messages from binary format into NiFi Records. |
|  | [StandardProxyConfigurationService](standardproxyconfigurationservice) | Provides a set of configurations for different NiFi components to use a proxy server. |
|  | [StandardRestrictedSSLContextService](standardrestrictedsslcontextservice) | Restricted implementation of the SSLContextService. |
|  | [StandardS3EncryptionService](standards3encryptionservice) | Adds configurable encryption to S3 Put and S3 Fetch operations. |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [StandardSalesforceBulkJobsStateService](standardsalesforcebulkjobsstateservice) | Stores Salesforce Bulk Jobs state per object type at cluster scope |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [StandardSalesforceClientService](standardsalesforceclientservice) | Provides connection service to Salesforce APIs |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [StandardSalesforceDataCloudClientService](standardsalesforcedatacloudclientservice) | Provides connection service to Salesforce Data Cloud APIs |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [StandardSlackRateLimiterService](standardslackratelimiterservice) | Provides rate limiting coordination for Slack API calls across processors to prevent cascading rate limit issues |
|  | [StandardSSLContextService](standardsslcontextservice) | Standard implementation of the SSLContextService. |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [StandardTableStateService](standardtablestateservice) | A controller Service that provides and manages table state. |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [StandardVectaraClientService](standardvectaraclientservice) | Vectara Controller Service to integrate with Vectara HTTP Api. |
|  | [StandardWebClientServiceProvider](standardwebclientserviceprovider) | Web Client Service Provider with support for configuring standard HTTP connection properties |
| [Snowflake logo in blue (no text)](../../../../_images/logo-snowflake-sans-text.png) | [StateManagedCdcSchemaRegistry](statemanagedcdcschemaregistry) | Uses the in-built NiFi State Management to store the hashes of table schemas. |
|  | [Syslog5424Reader](syslog5424reader) | Provides a mechanism for reading RFC 5424 compliant Syslog data, such as log files, and structuring the data so that it can be processed. |
|  | [SyslogReader](syslogreader) | Attempts to parses the contents of a Syslog message in accordance to RFC5424 and RFC3164. |

See moreShow less

Expand

## U

|  | Controller | Description |
| --- | --- | --- |
|  | [UDPEventRecordSink](udpeventrecordsink) | Format and send Records as UDP Datagram Packets to a configurable destination |

See moreShow less

Expand

## V

|  | Controller | Description |
| --- | --- | --- |
|  | [VolatileSchemaCache](volatileschemacache) | Provides a Schema Cache that evicts elements based on a Least-Recently-Used algorithm. |

See moreShow less

Expand

## W

|  | Controller | Description |
| --- | --- | --- |
|  | [WindowsEventLogReader](windowseventlogreader) | Reads Windows Event Log data as XML content having been generated by ConsumeWindowsEventLog, ParseEvtx, etc. |

See moreShow less

Expand

## X

|  | Controller | Description |
| --- | --- | --- |
|  | [XMLFileLookupService](xmlfilelookupservice) | A reloadable XML file-based lookup service. |
|  | [XMLReader](xmlreader) | Reads XML content and creates Record objects. |
|  | [XMLRecordSetWriter](xmlrecordsetwriter) | Writes a RecordSet to XML. |

See moreShow less

Expand

## Y

|  | Controller | Description |
| --- | --- | --- |
|  | [YamlTreeReader](yamltreereader) | Parses YAML into individual Record objects. |

See moreShow less

Expand
