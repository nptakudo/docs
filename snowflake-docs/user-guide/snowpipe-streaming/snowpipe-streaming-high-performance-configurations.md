---
title: "Configurations for Snowpipe Streaming with high-performance architecture"
url: "https://docs.snowflake.com/en/user-guide/snowpipe-streaming/snowpipe-streaming-high-performance-configurations"
---

# Configurations for Snowpipe Streaming with high-performance architecture

This guide describes the configuration settings for the high-performance Snowpipe Streaming client that are available in Java and Python SDKs. There are two distinct kinds of configuration:

* Process-wide environment variables: Variables that control logging and metrics for the entire running application and must be set before the client is initialized.
* Client-side properties: Properties that define the secure connection and ingestion target — such as, `url`, `user`, and `private_key` — and are configured for a specific client object, typically through an inline map or a `profile.json` file.

A single application can run multiple client objects. Each object has its own client-side properties, but they all share the same process-wide environment variable settings for logging and metrics.

The high-performance architecture requires the client to be explicitly bound to a specific PIPE object, which manages the schema, transformations, and ingestion into the target table.

## Environment variables

These configuration settings control process-wide behavior like logging and metrics collection and must be configured as environment variables before the client object is initialized. The following table shows the environmental variables that apply to all Snowpipe Streaming client objects within the same process:

| Variable | Description | Default value |
| --- | --- | --- |
| `SS_ENABLE_METRICS` | Set to TRUE to enable the built-in Prometheus metrics server. | FALSE |
| `SS_METRICS_PORT` | The port used for exposing metrics. | 50000 |
| `SS_METRICS_IP` | The IP address where the metrics server is hosted. | 127.0.0.1 |
| `SS_LOG_LEVEL` | The minimum logging level to output. | `info` (Options: `info`, `warn`, `error`) |

See moreShow less

Expand

## Required properties

The high-performance SDK mandates several properties to establish both the secure connection and the specific ingestion target (the PIPE). The following table shows the required connection and user authentication properties:

| Property | Description |
| --- | --- |
| `url` | URL for accessing your Snowflake account, including your account identifier. The protocol (<https://>) and port number are optional. |
| `user` | User sign-in name for the Snowflake account. |
| `account` | Snowflake account identifier; for example, xy12345. |

See moreShow less

Expand

If `authorization_type` is set to `JWT`, which is the default, you must provide either the key content or the key file path, as shown in the following table:

| Property | Description |
| --- | --- |
| `private_key` | Private key content that is used to authenticate the user. Include only the key content; no header, footer, or line breaks. |
| `private_key_file` | File path to the private key; for example, rsa\_key.p8. This is an alternative to providing the key content directly. |

See moreShow less

Expand

## Optional properties

The following table shows the high-performance SDK optional properties:

| Property | Description |
| --- | --- |
| `role` | Access control role to use for the session after connecting to Snowflake. |
| `authorization_type` | Property that configures the authentication method. Options are: JWT (key pair authentication, default). |

See moreShow less

Expand

## Externalizing secrets

Snowflake strongly recommends that you externalize secrets, such as the `private_key` and OAuth credentials, and store them in a key management service; for example, AWS KMS.

## Configuration examples

The following examples show client-side and environment variable configurations.

### Client-side configuration through a profile.json file

The following example shows how to define client-side properties:

CopyExpand

```
// profile.json
{
  "authorization_type": "JWT",
  "url": "https://<account_identifier>.snowflakecomputing.com",
  "user": "MY_SNOWFLAKE_USER",
  "account": "XY12345",
  "private_key_file": "/path/to/rsa_key.p8",
  "role": "MY_INGEST_ROLE"
}
```

Show lessSee more

Scroll to top

### Client-side configuration provided inline

The following examples show how to define client-side properties directly in code:

#### Python example

CopyExpand

```
config = {
    "authorization_type": "JWT",
    "url": "https://<account_identifier>.snowflakecomputing.com",
    "user": "MY_SNOWFLAKE_USER",
    "account": "XY12345",
    "private_key": "-----BEGIN PRIVATE KEY-----\n...\n-----END PRIVATE KEY-----",
}
# ... code to initialize client with 'config'
```

Show lessSee more

Scroll to top

#### Java example

CopyExpand

```
Map<String, Object> config = new HashMap<>();
config.put("authorization_type", "JWT");
config.put("url", "https://<account_identifier>.snowflakecomputing.com");
config.put("user", "MY_SNOWFLAKE_USER");
config.put("account", "XY12345");
config.put("private_key_file", "/path/to/rsa_key.p8");
config.put("role", "MY_INGEST_ROLE");
// ... code to initialize client with 'config'
```

Show lessSee more

Scroll to top

### Environment variable configuration

The following examples show how to define process-wide environment variables in the shell before you run the application:

#### Linux or macOS (Bash or Zsh)

CopyExpand

```
# Set the log level for the entire application process to 'warn'
export SS_LOG_LEVEL=warn

# Change the IP for metrics to a specific loopback address
export SS_METRICS_IP=127.0.0.5

# Now run your application
```

Show lessSee more

Scroll to top

#### Windows (command prompt)

CopyExpand

```
# Set the log level for the entire application process to 'warn'
set SS_LOG_LEVEL=warn

# Change the metrics port
set SS_METRICS_PORT=55000

# Now run your application
```

Show lessSee more

Scroll to top
