---
title: "Tutorial: Get started with Snowpipe Streaming REST API using cURL and a JWT"
url: "https://docs.snowflake.com/en/user-guide/snowpipe-streaming/snowpipe-streaming-high-performance-rest-tutorial"
---

# Tutorial: Get started with Snowpipe Streaming REST API using cURL and a JWT

Note

We recommend that you begin with the Snowpipe Streaming SDK over the REST API to benefit from the improved performance and getting-started experience.

This guide shows you how to stream data into Snowflake using the [Snowpipe Streaming REST API](snowpipe-streaming-high-performance-rest-api) and [a JSON Web Token (JWT) generated with SnowSQL](../../developer-guide/sql-api/authenticating).

## Prerequisites

Before you begin, ensure you have the following items:

**Snowflake User and Objects:**

A Snowflake user that is configured for key-pair authentication. Register your public key by using the following SQL command:

CopyExpand

```
ALTER USER MY_USER SET RSA_PUBLIC_KEY='<your-public-key>';
```

Show lessSee more

Scroll to top

A Snowflake database, schema, and a target table for streaming ingestion. You can create them by using the following SQL commands and replacing placeholders like `MY_DATABASE`, `MY_SCHEMA`, `MY_TABLE` with the names that you want:

CopyExpand

```
-- Create Database and Schema
CREATE OR REPLACE DATABASE MY_DATABASE;
CREATE OR REPLACE SCHEMA MY_SCHEMA;

-- Create Target Table
CREATE OR REPLACE TABLE MY_TABLE (
    id NUMBER,
    c1 NUMBER,
    ts STRING
);
```

Show lessSee more

Scroll to top

**ACCOUNT\_IDENTIFIER:**

We suggest that you use Format 1 for the ACCOUNT\_IDENTIFIER, which uses the account name within your organization; for example, `myorg-account123`. For more information on the format, see [Account identifiers](../admin-account-identifier).

**Installed tools:**

* `curl`: For making HTTP requests.
* `jq`: For parsing JSON responses.
* `SnowSQL`: For running commands, Snowflake’s command-line client.

**Generated JWT:**

Generate your JWT by using SnowSQL:

CopyExpand

```
snowsql --private-key-path rsa_key.p8 --generate-jwt \
  -a <ACCOUNT_IDENTIFIER> \
  -u MY_USER
```

Show lessSee more

Scroll to top

Caution

Store your JWT securely. Avoid exposing it in logs or scripts.

## Step-by-step instructions

Complete the following steps to stream data into Snowflake.

### Step 1: Set environment variables

Set up the necessary environment variables for your Snowflake account and the streaming operation. Note that the `PIPE` variable targets the default streaming pipe associated with your table.

CopyExpand

```
# Paste the JWT token obtained from SnowSQL
export JWT_TOKEN="PASTE_YOUR_JWT_TOKEN_HERE"

# Configure your Snowflake account and resources:
export ACCOUNT="<ACCOUNT_IDENTIFIER>" # For example, ab12345
export USER="MY_USER"
export DB="MY_DATABASE"
export SCHEMA="MY_SCHEMA"
export PIPE="MY_TABLE-STREAMING"
export CHANNEL="MY_CHANNEL"

# Replace ACCOUNT with your Account URL Host to form the control plane host:
export CONTROL_HOST="${ACCOUNT}.snowflakecomputing.com"
```

Show lessSee more

Scroll to top

### Step 2: Discover ingest host

Important

If your Snowflake account name contains underscores (e.g., MY\_ACCOUNT), a known issue can cause an internal error when calling the ingestion service.

You must replace all underscores with dashes in the INGEST\_HOST before generating the scoped token. This converted format (with dashes) must be used for all subsequent REST API calls, including the generation of the scoped token itself.

For example, if the hostname returned is `my_account.region.ingest.snowflakecomputing.com`, you must change it to `my-account.region.ingest.snowflakecomputing.com` for all subsequent REST API calls.

The ingest host is the endpoint for streaming data. Discover the ingest host by using your JWT:

CopyExpand

```
export INGEST_HOST=$(curl -sS -X GET \
  -H "Authorization: Bearer $JWT_TOKEN" \
  -H "X-Snowflake-Authorization-Token-Type: KEYPAIR_JWT" \
  "https://${CONTROL_HOST}/v2/streaming/hostname")

echo "Ingest Host: $INGEST_HOST"
```

Show lessSee more

Scroll to top

Obtain a scoped token to authorize operations on the ingest host:

CopyExpand

```
export SCOPED_TOKEN=$(curl -sS -X POST "https://$CONTROL_HOST/oauth/token" \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  -H "Authorization: Bearer $JWT_TOKEN" \
  -d "grant_type=urn:ietf:params:oauth:grant-type:jwt-bearer&scope=${INGEST_HOST}")

echo "Scoped Token obtained for ingest host"
```

Show lessSee more

Scroll to top

### Step 3: Open the channel

Open a streaming channel to begin data ingestion:

CopyExpand

```
curl -sS -X PUT \
  -H "Authorization: Bearer $SCOPED_TOKEN" \
  -H "Content-Type: application/json" \
  "https://${INGEST_HOST}/v2/streaming/databases/$DB/schemas/$SCHEMA/pipes/$PIPE/channels/$CHANNEL" \
  -d '{}' | tee open_resp.json | jq .
```

Show lessSee more

Scroll to top

### Step 4: Append a row of data

Append a single row of data to the open channel.

#### 4.1 Extract continuation and offset tokens

These tokens are crucial for maintaining the state of your streaming session.

CopyExpand

```
export CONT_TOKEN=$(jq -r '.next_continuation_token' open_resp.json)
export OFFSET_TOKEN=$(jq -r '.channel_status.last_committed_offset_token' open_resp.json)
export NEW_OFFSET=$((OFFSET_TOKEN + 1))
```

Show lessSee more

Scroll to top

#### 4.2 Create sample row

Generate a sample data row in NDJSON format:

CopyExpand

```
export NOW_TS=$(date -u +"%Y-%m-%dT%H:%M:%SZ")

cat <<EOF > rows.ndjson
{
  "id": 1,
  "c1": $RANDOM,
  "ts": "$NOW_TS"
}
EOF
```

Show lessSee more

Scroll to top

#### 4.3 Append row

Send the sample row to the streaming channel:

CopyExpand

```
curl -sS -X POST \
  -H "Authorization: Bearer $SCOPED_TOKEN" \
  -H "Content-Type: application/x-ndjson" \
  -H "Content-Encoding: zstd" \
  "https://${INGEST_HOST}/v2/streaming/data/databases/$DB/schemas/$SCHEMA/pipes/$PIPE/channels/$CHANNEL/rows?continuationToken=$CONT_TOKEN&offsetToken=$NEW_OFFSET" \
  --data-binary @rows.ndjson | jq .
```

Show lessSee more

Scroll to top

Note

This example includes the `Content-Encoding: zstd` header to demonstrate compression support. For this simple example with uncompressed data, you can omit this header. When you send compressed data, specify either `zstd` or `gzip` to match the compression format of your payload.

Important

* After each append operation, you must update the `continuationToken` for the next append call. The response from the append rows call contains a `next_continuation_token` field that you should use to make your updates.
* The success of the append operation confirms only that the data was received by the service, not that it is persisted to the table. Take the next step to verify persistence before querying or moving to the next batch.

#### 4.4 Verify data persistence and committed offset by using `getChannelStatus`

Complete this critical step to ensure application reliability. Data isn’t guaranteed to be persistent until the `committedOffset` has advanced. To confirm that the rows that you just appended are successfully persisted, use `getChannelStatus`.

Check the current status of your streaming channel:

CopyExpand

```
curl -sS -X POST \
  -H "Authorization: Bearer $SCOPED_TOKEN" \
  -H "Content-Type: application/json" \
  "https://${INGEST_HOST}/v2/streaming/databases/$DB/schemas/$SCHEMA/pipes/$PIPE:bulk-channel-status" \
  -d "{\"channel_names\": [\"$CHANNEL\"]}" | jq ".channel_statuses.\"$CHANNEL\""
```

Show lessSee more

Scroll to top

**Verification check**

You must ensure that the `committedOffset` returned in the response is greater than or equal to the offset of the rows you just appended. Only after the `committedOffset` advances can you be certain that the data is safely available in the table.

#### 4.5 Query the table for persisted data

After you confirm that the `committedOffset` has advanced in [the previous step (4.4)](#verify-data-persistence), you can query to confirm that the data is ingested into your Snowflake table.

Run the following SQL query in Snowflake:

CopyExpand

```
SELECT * FROM MY_DATABASE.MY_SCHEMA.MY_TABLE WHERE id = 1;
```

Show lessSee more

Scroll to top

### (Optional) Step 5: Clean up

Remove temporary files and unset environment variables:

CopyExpand

```
rm -f rows.ndjson open_resp.json
unset JWT_TOKEN SCOPED_TOKEN ACCOUNT USER DB SCHEMA PIPE CHANNEL CONTROL_HOST INGEST_HOST CONT_TOKEN OFFSET_TOKEN NEW_OFFSET NOW_TS
```

Show lessSee more

Scroll to top

## Troubleshooting

* **HTTP 401 (Unauthorized):** Verify that your JWT token is valid and not expired. If needed, regenerate it.
* **HTTP 404 (Not Found):** Double-check that the database, schema, pipe, and channel names are spelled correctly and exist in your Snowflake account.
* **No Ingest Host:** Ensure your control plane host URL is correct and accessible.
