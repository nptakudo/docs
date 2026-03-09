---
title: "Threads API"
url: "https://docs.snowflake.com/en/user-guide/snowflake-cortex/cortex-agents-threads-rest-api"
---

# Threads API

Use this API to create threads that are used to interact with Cortex Agents.

## Create thread

`POST /api/v2/cortex/threads`

Creates a new thread and returns the thread UUID.

### Request

#### Request headers

| Header | Description |
| --- | --- |
| `Authorization` | (Required) Authorization token. For more information, see [Authentication](cortex-agents.html#label-chat-api-authenticate-example). |
| `Content-Type` | (Required) application/json |

See moreShow less

Expand

#### Request body

The request body can include the following field:

| Field | Type | Description |
| --- | --- | --- |
| `origin_application` | string | (Optional) Name of the application that created the thread. Allows grouping threads by application. Limited to 16 bytes. |

See moreShow less

Expand

Example:

CopyExpand

```
{
  "origin_application": "my_app"
}
```

Show lessSee more

Scroll to top

### Response

Returns the thread UUID as a string.

CopyExpand

```
"1234567890"
```

Show lessSee more

Scroll to top

## Describe thread

`GET /api/v2/cortex/threads/{id}`

Describes a thread and returns a batch of messages in that thread, based on the page\_size and the last\_message\_id, in descending order of creation. This request is only successful if the thread ID belongs to the user.

### Request

#### Path parameters

| Parameter | Type | Description |
| --- | --- | --- |
| `id` | integer | (Required) UUID for the thread. |

See moreShow less

Expand

#### Query parameters

| Parameter | Type | Description |
| --- | --- | --- |
| `page_size` | integer | (Optional) Number of messages to return (default: 20, max: 100). |
| `last_message_id` | integer | (Optional) The ID of the last message received. Used to set the offset for next batch. Can be empty for the first batch of messages. |

See moreShow less

Expand

#### Request headers

| Header | Description |
| --- | --- |
| `Authorization` | (Required) Authorization token. |
| `Content-Type` | (Required) application/json |

See moreShow less

Expand

### Response

Returns a thread metadata object and an array of messages.

| Field | Type | Description |
| --- | --- | --- |
| [metadata](#label-snowflake-threads-rest-api) | object | Metadata for the thread, including the name, application that created the thread, and the time that it was created. |
| `messages` | array | Array of message objects. |

See moreShow less

Expand

#### metadata

| Field | Type | Description |
| --- | --- | --- |
| `thread_id` | integer | UUID for the thread. |
| `thread_name` | string | Name of the thread. |
| `origin_application` | string | The name of the application that created the thread. |
| `created_on` | integer | Time when the thread was created (milliseconds since UNIX epoch). |
| `updated_on` | integer | Time when the thread was last updated (milliseconds since UNIX epoch). An update includes adding any new messages to the thread. |

See moreShow less

Expand

#### Messages

| Field | Type | Description |
| --- | --- | --- |
| `message_id` | integer | UUID for the message. |
| `parent_id` | integer | UUID for the parent message. |
| `created_on` | integer | Time when the message was created (milliseconds since UNIX epoch). |
| `role` | string | The role that generated this message. |
| `message_payload` | string | Message payload. |
| `request_id` | string | Request ID for the original message. |

See moreShow less

Expand

Example:

CopyExpand

```
{
  "metadata": {
    "thread_id": 1234567890,
    "thread_name": "Support Chat",
    "origin_application": "my_app",
    "created_on": 1717000000000,
    "updated_on": 1717000100000
  },
  "messages": [
    {
      "message_id": 1,
      "parent_id": null,
      "created_on": 1717000000000,
      "role": "user",
      "message_payload": "Hello, I need help.",
      "request_id": "req_001"
    },
    {
      "message_id": 2,
      "parent_id": 1,
      "created_on": 1717000001000,
      "role": "assistant",
      "message_payload": "How can I assist you?",
      "request_id": "req_002"
    }
  ]
}
```

Show lessSee more

Scroll to top

## Update thread

`POST /api/v2/cortex/threads/{id}`

Updates a thread.

### Request

#### Path parameters

| Parameter | Type | Description |
| --- | --- | --- |
| `id` | integer | (Required) UUID for the thread. |

See moreShow less

Expand

#### Request headers

| Header | Description |
| --- | --- |
| `Authorization` | (Required) Authorization token. |
| `Content-Type` | (Required) application/json |

See moreShow less

Expand

#### Request body

| Field | Type | Description |
| --- | --- | --- |
| `thread_name` | string | (Optional) Name of the thread. |

See moreShow less

Expand

Example:

CopyExpand

```
{
  "thread_name": "New Thread Name"
}
```

Show lessSee more

Scroll to top

### Response

Returns the status of the thread update.

CopyExpand

```
{"status": "Thread xxxx successfully updated."}
```

Show lessSee more

Scroll to top

## List threads

`GET /api/v2/cortex/threads`

Lists all threads belonging to the user.

### Request

#### Query parameters

| Parameter | Type | Description |
| --- | --- | --- |
| `origin_application` | string | (Optional) Filter the list of threads by this origin application. Without specifying this field, all threads are returned. |

See moreShow less

Expand

#### Request headers

| Header | Description |
| --- | --- |
| `Authorization` | (Required) Authorization token. |
| `Content-Type` | (Required) application/json |

See moreShow less

Expand

### Response

Returns an array of thread metadata objects.

#### Thread metadata

| Field | Type | Description |
| --- | --- | --- |
| `thread_id` | integer | UUID for the thread. |
| `thread_name` | string | Name of the thread. |
| `origin_application` | string | The name of the application that created the thread. |
| `created_on` | integer | Time when the thread was created (milliseconds since UNIX epoch). |
| `updated_on` | integer | Time when the thread was last updated (milliseconds since UNIX epoch). An update includes adding any new messages to the thread. |

See moreShow less

Expand

Example:

CopyExpand

```
[
  {
    "thread_id": 1234567890,
    "thread_name": "Support Chat",
    "origin_application": "my_app",
    "created_on": 1717000000000,
    "updated_on": 1717000100000
  }
]
```

Show lessSee more

Scroll to top

## Delete thread

`DELETE /api/v2/cortex/threads/{id}`

Deletes a thread and all the messages in that thread.

### Request

#### Path parameters

| Parameter | Type | Description |
| --- | --- | --- |
| `id` | integer | (Required) UUID for the thread. |

See moreShow less

Expand

#### Request headers

| Header | Description |
| --- | --- |
| `Authorization` | (Required) Authorization token. |
| `Content-Type` | (Required) application/json |

See moreShow less

Expand

### Response

Returns a success response if the thread is deleted.

CopyExpand

```
{
  "success": true
}
```

Show lessSee more

Scroll to top
