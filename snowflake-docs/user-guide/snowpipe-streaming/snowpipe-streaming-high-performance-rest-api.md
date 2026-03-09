---
title: "Snowpipe Streaming REST API endpoints"
url: "https://docs.snowflake.com/en/user-guide/snowpipe-streaming/snowpipe-streaming-high-performance-rest-api"
---

# Snowpipe Streaming REST API endpoints

Note

We recommend that you begin with the Snowpipe Streaming SDK over the REST API to benefit from the improved performance and getting-started experience.

The Snowpipe Streaming REST API is designed for lightweight workloads and provides a flexible way to integrate with external applications without using the Snowpipe Streaming SDK.

The following  diagram provides a visual overview of how data flows from the client to the Snowflake server, detailing each of the key API endpoints in the process.

> ![Snowpipe Streaming REST API overview](../../_images/data-load-snowpipe-streaming-rest-api.png)

## Request headers

The following request headers apply to all the endpoints for the Snowpipe Streaming REST API:

| Header | Description |
| --- | --- |
| `Authorization` | Authentication token |
| `X-Snowflake-Authorization-Token-Type` (optional) | JWT/OAuth |
| `Content-Encoding` (optional) | Specifies the compression format of the payload. Supported: `gzip`, `zstd`. |

See moreShow less

Expand

Note

The maximum allowed size for a single request payload is 16 MB. If your data is larger, you must split it into multiple requests.

## Get Hostname

The `Get Hostname` returns the hostname used to interact with the Snowpipe Streaming REST API. Each account has a unique hostname.

Expand

```
GET /v2/streaming/hostname
```

Show lessSee more

Scroll to top

Response:

CopyExpand

```
{
  "hostname": "string"
}
```

Show lessSee more

Scroll to top

Description of response fields:

| Field | Type | Description |
| --- | --- | --- |
| Hostname | String | The hostname of the account. |

See moreShow less

Expand

## Exchange Scoped Token

The `Exchange Scoped Token` returns a security token that can be used to access only the Snowpipe Streaming API-related service. This provides security protection for the customer.

Expand

```
POST /oauth/token
```

Show lessSee more

Scroll to top

Request:

| Attribute | Required | Component | Description |
| --- | --- | --- | --- |
| content\_type | Yes | Header | “application/x-www-form-urlencoded” |
| grant\_type | Yes | Payload | “<urn:ietf:params:oauth:grant-type:jwt-bearer>” |
| scope | Yes | Payload | The hostname of the account. |

See moreShow less

Expand

Response:

CopyExpand

```
{
  "token": "string"
}
```

Show lessSee more

Scroll to top

Description of response fields:

| Field | Type | Description |
| --- | --- | --- |
| Token | String | The scoped token. |

See moreShow less

Expand

## Open Channel

The `Open Channel` operation creates or opens a new channel against a pipe or table. If the channel already exists, Snowflake bumps the client sequencer of the channel and returns the last committed offset token.

Expand

```
PUT /v2/streaming/databases/{databaseName}/schemas/{schemaName}/pipes/{pipeName}/channels/{channelName}
```

Show lessSee more

Scroll to top

Request:

| Attribute | Required | Component | Description |
| --- | --- | --- | --- |
| databaseName | Yes | URI | Database name, case-insensitive. |
| schemaName | Yes | URI | Schema name, case-insensitive. |
| pipeName | Yes | URI | Pipe name, case-insensitive. |
| channelName | Yes | URI | The name of the channel that you create or re-open, case-insensitive. |
| offset\_token | No | Payload | String used to set an offset token when opening a channel. |
| requestId | No | Query parameter | A universally unique identifier (UUID) used to track requests through the system. |

See moreShow less

Expand

Response:

CopyExpand

```
{
  "next_continuation_token": "string",
  "channel_status": {
    "database_name": "string",
    "schema_name": "string",
    "pipe_name": "string",
    "channel_name": "string",
    "channel_status_code": "string",
    "last_committed_offset_token": "string",
    "created_on_ms": "long",
    "rows_inserted": "int",
    "rows_parsed": "int",
    "rows_error_count": "int",
    "last_error_offset_upper_bound": "string",
    "last_error_message": "string",
    "last_error_timestamp": "timestamp_utc",
    "snowflake_avg_processing_latency_ms": "int"
  }
}
```

Show lessSee more

Scroll to top

Description of response fields:

| Field | Type | Description |
| --- | --- | --- |
| next\_continuation\_token | String | An API-managed token that must be used in the subsequent Append Rows request. The token links a series of calls, ensuring a contiguous, in-order stream of data and maintaining the session state for exactly once delivery. |
| channel\_status | Object | A nested object with the following detailed information about the channel:   * database\_name (String): The name of the database where the pipe is located. * schema\_name (String): The name of the schema where the pipe is located. * pipe\_name (String): The name of the specific pipe being used. * channel\_name (String): The name of the streaming channel. * channel\_status\_code (String): A code that indicates the current status of the channel; for example, “ACTIVE”. * last\_committed\_offset\_token (String): The token that represents the last successfully committed offset. * created\_on\_ms (Long): The timestamp, in milliseconds, when the channel was created. * rows\_inserted (Int): The total number of rows successfully inserted. * rows\_parsed (Int): The total number of rows parsed. * rows\_error\_count (Int): The total number of rows that encountered an error. * last\_error\_offset\_upper\_bound (String): A token that indicates the upper bound of the offset where the last error occurred. * last\_error\_message (String): The message of the last error that occurred. * last\_error\_timestamp (Long): The timestamp, in milliseconds, of the last error. * snowflake\_avg\_processing\_latency\_ms (Int): The average processing latency of Snowflake in milliseconds. |

See moreShow less

Expand

## Append Row(s)

The `Append Rows` operation inserts a batch of rows to the given channel.

Expand

```
POST /v2/streaming/data/databases/{databaseName}/schemas/{schemaName}/pipes/{pipeName}/channels/{channelName}/rows
```

Show lessSee more

Scroll to top

Request:

| Attribute | Required | Component | Description |
| --- | --- | --- | --- |
| databaseName | Yes | URI | Database name, case-insensitive. |
| schemaName | Yes | URI | Schema name, case-insensitive. |
| pipeName | Yes | URI | Pipe, case-insensitive. |
| channelName | Yes | URI | Channel name, case-insensitive. |
| continuationToken | Yes | Query parameter | Continuation token from Snowflake, encapsulates both client and row sequencers. |
| offsetToken | No | Query parameter | String used to set an offset token per batch. |
| rows | Yes | Payload | The actual data payload to be ingested in NDJSON format. The maximum allowed size for this attribute is 4 MB. |
| requestId | No | Query parameter | A UUID used to track requests through the system. |

See moreShow less

Expand

Note

The JSON text within the NDJSON payload must strictly conform to the `RFC 8259` standard. Each JSON text must be followed by a newline character `\n` (`0x0A`). You can also insert a carriage return `\r` (`0x0D`) before the newline character.

Response:

CopyExpand

```
{
  "next_continuation_token": "string"
}
```

Show lessSee more

Scroll to top

Description of response fields:

| Field | Type | Description |
| --- | --- | --- |
| next\_continuation\_token | string | The next continuation token from Snowflake, which encapsulates both client and row sequencers. It should be used for inserting the next batch. |

See moreShow less

Expand

## Drop Channel

The `Drop Channel` operation drops a channel at server side along with its metadata.

Expand

```
DELETE /v2/streaming/databases/{databaseName}/schemas/{schemaName}/pipes/{pipeName}/channels/{channelName}
```

Show lessSee more

Scroll to top

Request:

| Attribute | Required | Component | Description |
| --- | --- | --- | --- |
| databaseName | Yes | URI | Database name, case-insensitive |
| schemaName | Yes | URI | Schema name, case-insensitive |
| pipeOrTableName | Yes | URI | Pipe or table name, case-insensitive |
| channelName | Yes | URI | Channel name, case-insensitive |
| requestId | No | Query parameter | A UUID used to track requests through the system |

See moreShow less

Expand

Response:

This operation returns a payload with no specific successful response other than the HTTP status code.

## Bulk Get Channel Status

The `Bulk Get Channel Status` operation returns the status of a channel for a specific client sequencer.

Expand

```
POST /v2/streaming/databases/{databaseName}/schemas/{schemaName}/pipes/{pipeName}:bulk-channel-status
```

Show lessSee more

Scroll to top

Request:

| Attribute | Required | Component | Description |
| --- | --- | --- | --- |
| databaseName | Yes | URI | Database name, case-insensitive |
| schemaName | Yes | URI | Schema name, case-insensitive |
| pipeName | Yes | URI | Pipe name, case-insensitive |
| channel\_names | Yes | Payload | An array of String channel names that the customer wants to get status for; the names are case-sensitive. For example, `{"channel_names":["channel1", "channel2"]}`. |

See moreShow less

Expand

Response:

CopyExpand

```
{
  "channel_statuses": {
    "channel1": {
      "channel_status_code": "String",
      "last_committed_offset_token": "String",
      "database_name": "String",
      "schema_name": "String",
      "pipe_name": "String",
      "channel_name": "String",
      "rows_inserted": "int",
      "rows_parsed": "int",
      "rows_errors": "int",
      "last_error_offset_upper_bound": "String",
      "last_error_message": "String",
      "last_error_timestamp": "timestamp_utc",
      "snowflake_avg_processing_latency_ms": "int"
    },
    "channel2": {
      "comment": "same structure as channel1"
    }
    "comment": "potentially other channels"
  }
}
```

Show lessSee more

Scroll to top

Note

If no requested channel is found in the service, the response payload doesn’t have an entry for that channel within the `channel_statuses` object.

Description of `channel_statuses` fields for each channel:

| Field | Type | Description |
| --- | --- | --- |
| channel\_status\_code | String | Indicates the status of the channel. |
| last\_committed\_offset\_token | String | Latest committed offset token. |
| database\_name | String | The name of the database that the channel belongs to. |
| schema\_name | String | The name of the schema that the channel belongs to. |
| pipe\_name | String | The name of the pipe that the channel belongs to. |
| channel\_name | String | The name of the channel. |
| rows\_inserted | int | A count of all rows inserted into this channel. |
| rows\_parsed | int | A count of all rows parsed, but not necessarily inserted into this channel. |
| rows\_errors | int | A count of all rows that experienced errors when inserted into this channel and were therefore rejected. |
| last\_error\_offset\_upper\_bound | String | The upper bound for an ingestion error. The error will be located at or before this committed offset token. |
| last\_error\_message | String | A human readable message corresponding to the latest error code for that channel, with sensitive customer data redacted. |
| last\_error\_timestamp | timestamp\_utc | Timestamp at the time when the last error occurred. |
| snowflake\_avg\_processing\_latency\_ms | int | Average end-to-end processing time for this channel. |

See moreShow less

Expand

## Error response structure

The Snowpipe Streaming REST APIs return a JSON payload for error responses. This structure provides actionable information for both automated error handling and human analysis.

The response payload has the following structure:

CopyExpand

```
{
  "code": "...",
  "message": "..."
}
```

Show lessSee more

Scroll to top

### Response fields

| Field | Type | Description |
| --- | --- | --- |
| Code | String | A stable, programmatic error code. This value can be used for automated error handling and logging. For example, an application’s logic can check for a specific code to trigger a predefined action. |
| Message | String | A human-readable message that describes the error. This message is subject to change and shouldn’t be used for automated parsing. |

See moreShow less

Expand

### Example

The following example shows an error response you might receive:

CopyExpand

```
{
  "code": "STALE_CONTINUATION_TOKEN_SEQUENCER",
  "message": "Channel sequencer in the continuation token is stale. Please reopen the channel"
}
```

Show lessSee more

Scroll to top

This example shows the response for an attempt to use a continuation token with a stale channel sequencer. The code provides a clear, machine-readable identifier for the error, and the message offers a helpful, descriptive text for a user.
