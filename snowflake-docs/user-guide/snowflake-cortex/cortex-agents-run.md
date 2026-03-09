---
title: "Cortex Agents Run API"
url: "https://docs.snowflake.com/en/user-guide/snowflake-cortex/cortex-agents-run"
---

# Cortex Agents Run API

Note

Requests to the Cortex Agent REST API time out after 15 minutes.

There are two methods to interact with an Agent:

* Build an agent object and reference this agent object in a request to the `agent:run` API.
* Call `agent:run` directly without an agent object. You provide the configuration in the request body of `agent:run`.

`agent:run` supports **streaming responses by default**. To disable streaming and receive a single JSON response, set `stream` to `false`.

## Agent run request with agent object

`POST /api/v2/databases/{database}/schemas/{schema}/agents/{name}:run`

Sends a user query to the agent object and returns its response.

By default, the API streams responses as server-sent events (SSE). To receive a single JSON response, set `stream` to `false` in the request body.

Note

You can’t set, update, or overwrite the `models`, `instructions`, and `orchestration` fields using this request. To update these fields, you must use [Update Cortex Agent](cortex-agents-rest-api.html#label-snowflake-agents-rest-api-update).

### Path parameters

| Parameter | Description |
| --- | --- |
| `database` | (Required) The database containing the agent. You can use the `/api/v2/databases` GET request to get a list of available databases. |
| `schema` | (Required) The schema containing the agent. You can use the `/api/v2/databases/{database}/schemas` GET request to get a list of available schemas for the specified database. |
| `name` | (Required) The name of the agent. |

See moreShow less

Expand

### Request headers

| Header | Description |
| --- | --- |
| `Authorization` | (Required) Authorization token. See [Authentication](cortex-agents.html#label-chat-api-authenticate-example). |
| `Content-Type` | (Required) application/json |
| `Accept` | (Optional) Response content type. Use `text/event-stream` for streaming responses or `application/json` for a single non-streaming response. |

See moreShow less

Expand

### Request body

| Field | Type | Description |
| --- | --- | --- |
| `thread_id` | integer | The thread ID for the conversation. If thread\_id is used, then parent\_message\_id must be passed as well. |
| `parent_message_id` | integer | The ID of the parent message in the thread. If this is the first message, parent\_message\_id should be 0. |
| `messages` | array of [Message](#label-snowflake-agent-run-message) | If thread\_id and parent\_message\_id are passed in the request, messages includes the current user message in the conversation. Else, messages includes the conversation history and the current message. Messages contains both user queries and assistant responses in chronological order. |
| `stream` | boolean | Whether to return a streaming response (`text/event-stream`) or a non-streaming JSON response (`application/json`). If true, the response will be streamed as Server-Sent Events. If false, the response will be returned as JSON. |
| `tool_choice` | [ToolChoice](#label-snowflake-agent-run-toolchoice) | Configures how the agent should select and use tools during the interaction. Controls whether tool use is automatic, required, or whether specific tools should be used. |

See moreShow less

Expand

**Example**

CopyExpand

```
{
  "thread_id": 0,
  "parent_message_id": 0,
  "messages": [
    {
      "role": "user",
      "content": [
        {
          "type": "text",
          "text": "What is the total revenue for 2023?"
        }
      ]
    }
  ],
  "stream": false,
  "tool_choice": {
    "type": "auto",
    "name": [
      "analyst_tool",
      "search_tool"
    ]
  }
}
```

Show lessSee more

Scroll to top

The request body supports an optional `stream` boolean field:

* If `stream` is omitted, it defaults to `true` and the response is streamed as SSE events.
* If `stream` is `false`, the API returns a single JSON object (see [Non-streaming response (stream: false)](#label-snowflake-agents-run-non-streaming-response)).

## Agent run without an agent object

`POST /api/v2/cortex/agent:run`

Sends a user query to the Cortex Agents service provided in the request body and returns its response.
Interacts with the agent without creating an agent object.

Note

Before September 1st, 2025, the request and response schemas for the `agent:run` API were different from the schema listed in this document. Previously, the orchestration was static and the same sequence of tools was used to generate an answer. `agent:run` now has an updated schema for both the request and response. In addition, the API now dynamically orchestrates and iterates to arrive at the final response. We recommend using the schema described in this document for an improved end-user experience.

To use the legacy schema and behavior, use the following schema:

CopyExpand

```
{
  "model": "claude-4-sonnet",
  "messages": [
     {"role":"user", "content": [] }
  ]
}
```

Show lessSee more

Scroll to top

### Request headers

| Header | Description |
| --- | --- |
| `Authorization` | (Required) Authorization token. See [Authentication](cortex-agents.html#label-chat-api-authenticate-example). |
| `Content-Type` | (Required) application/json |
| `Accept` | (Optional) Response content type. Use `text/event-stream` for streaming responses or `application/json` for a single non-streaming response. |

See moreShow less

Expand

### Request body

| Field | Type | Description |
| --- | --- | --- |
| `thread_id` | integer | The thread ID for the conversation. If thread\_id is used, then parent\_message\_id must be passed as well. |
| `parent_message_id` | integer | The ID of the parent message in the thread. If this is the first message, parent\_message\_id should be 0. |
| `messages` | array of [Message](#label-snowflake-agent-run-message) | If thread\_id and parent\_message\_id are passed in the request, messages includes the current user message in the conversation. Else, messages includes the conversation history and the current message. Messages contains both user queries and assistant responses in chronological order. |
| `stream` | boolean | Whether to return a streaming response (`text/event-stream`) or a non-streaming JSON response (`application/json`). If true, the response will be streamed as Server-Sent Events. If false, the response will be returned as JSON. |
| `tool_choice` | [ToolChoice](#label-snowflake-agent-run-toolchoice) | Configures how the agent should select and use tools during the interaction. Controls whether tool use is automatic, required, or whether specific tools should be used. |
| `models` | [ModelConfig](#label-snowflake-agent-run-modelconfig) | Model configuration for the agent. Includes the orchestration model (e.g., claude-4-sonnet). If not provided, a model is automatically selected. Currently only available for the `orchestration` step. |
| `instructions` | [AgentInstructions](#label-snowflake-agent-run-agentinstructions) | Instructions for the agent’s behavior, including response, orchestration, system, and sample questions. |
| `orchestration` | [OrchestrationConfig](#label-snowflake-agent-run-orchestrationconfig) | Orchestration configuration, including budget constraints (e.g., seconds, tokens). |
| `tools` | array of [Tool](#label-snowflake-agent-run-tool) | List of tools available for the agent to use. Each tool includes a tool\_spec with type, name, description, and input schema. Tools may have a corresponding configuration in tool\_resources. |
| `tool_resources` | map of [ToolResource](#label-snowflake-agent-run-toolresource) | Configuration for each tool referenced in the tools array. Keys must match the name of the respective tool. |

See moreShow less

Expand

**Example**

CopyExpand

```
{
  "thread_id": 0,
  "parent_message_id": 0,
  "messages": [
    {
      "role": "user",
      "content": [
        {
          "type": "text",
          "text": "What is the total revenue for 2023?"
        }
      ]
    }
  ],
  "stream": false,
  "tool_choice": {
    "type": "auto",
    "name": [
      "analyst_tool",
      "search_tool"
    ]
  },
  "models": {
    "orchestration": "claude-4-sonnet"
  },
  "instructions": {
    "response": "You will respond in a friendly but concise manner",
    "orchestration": "For any query related to revenue we should use Analyst; For all policy questions we should use Search",
    "system": "You are a friendly agent ..."
  },
  "orchestration": {
    "budget": {
      "seconds": 30,
      "tokens": 16000
    }
  },
  "tools": [
    {
      "tool_spec": {
        "type": "generic",
        "name": "get_revenue",
        "description": "Fetch the delivery revenue for a location.",
        "input_schema": {
          "type": "object",
          "properties": {
            "location": {
              "type": "string",
              "description": "The city and state, e.g. San Francisco, CA"
            }
          }
        },
        "required": [
          "location"
        ]
      }
    }
  ],
  "tool_resources": {
    "get_revenue": {
      "type": "function",
      "execution_environment": {
        "type": "warehouse",
        "warehouse": "MY_WH"
      },
      "identifier": "DB.SCHEMA.UDF"
    }
  }
}
```

Show lessSee more

Scroll to top

The request body supports an optional `stream` boolean field:

* If `stream` is omitted, it defaults to `true` and the response is streamed as SSE events.
* If `stream` is `false`, the API returns a single JSON object (see [Non-streaming response (stream: false)](#label-snowflake-agents-run-non-streaming-response)).

## Streaming responses

The `agent:run` API provides streaming responses. The server streams back events. This allows you to display responses in your application, token-by-token, as they are generated by the Agent.
Each event streamed in the API response has a strictly typed schema. You can find a list of all of the events in the following section and select to which ones you’d like to subscribe.

The last event sent by the API is a `response` event. This event contains the entire agent output. You can use this as
the agent’s final response. For any non-streaming clients, you can subscribe to this event because it is the logical aggregation of all prior events. If you don’t want to use streaming responses, wait for the `response` event and ignore all prior events.

The majority of the other events streamed can be split into two categories: `Delta` and `Content Items`.

`Delta` events represent a single token generated by the Agent. By listening to these events, you can create
a typewriter effect. The main delta events are `response.thinking.delta`, which
represents a reasoning token, and `response.text.delta`, which represent an answer token.

`Content Item` events represent elements from the `content` array in the final agent response.

Note

Make sure your application can handle unknown event types.

**Example Response**

CopyExpand

```
event: response.status
data: {"message":"Planning the next steps","status":"planning"}

event: response.thinking.delta
data: {"content_index":0,"text":"\nThe user is asking for a"}

event: response.thinking.delta
data: {"content_index":0,"text":" chart showing the"}

...
...
...

event: response.status
data: {"message":"Reviewing the results","status":"reasoning_agent_stop"}

event: response.status
data: {"message":"Forming the answer","status":"proceeding_to_answer"}
```

Show lessSee more

Scroll to top

### `response`

Event streamed when the final response is available. This is the last event emitted, it represents the aggregation of all other events previously streamed.

| Field | Type | Description |
| --- | --- | --- |
| `role` | string | The role for the message. Always `assistant` in the API response. |
| `content` | array of [MessageContentItem](#label-snowflake-agent-run-messagecontentitem) | The content generated by the agent. |
| `warnings` | array of [Warning](#label-snowflake-agent-run-warning) | Non-fatal warnings that occurred during processing. Present for non-streaming clients or as a summary. |
| `metadata` | [ResponseMetadata](#label-snowflake-agent-run-responsemetadata) |  |

See moreShow less

Expand

**Example**

CopyExpand

```
{
  "role": "assistant",
  "content": [
    {
      "type": "chart",
      "chart": {
        "tool_use_id": "toolu_123",
        "chart_spec": "{\"$schema\":\"https://vega.github.io/schema/vega-lite/v5.json\",\"data\":{...},\"mark\":\"bar\"}"
      }
    }
  ],
  "warnings": [
    {
      "message": "Unable to fetch tools from MCP server 'foo'. Response quality may be degraded."
    }
  ],
  "metadata": {
    "usage": {
      "tokens_consumed": [
        {
          "model_name": "llama3.1-70b",
          "input_tokens": {
            "total": 175,
            "cache_read": 50,
            "cache_write": 25,
            "uncached": 100
          },
          "output_tokens": {
            "total": 75
          },
          "context_window": 128000
        }
      ]
    },
    "run_id": "123-456"
  }
}
```

Show lessSee more

Scroll to top

### `response.text`

An event streamed when a text content block is done streaming, including all the aggregated deltas for a particular content index.

| Field | Type | Description |
| --- | --- | --- |
| `content_index` | integer | The index in the response content array this event represents |
| `text` | string | A text result from the agent |
| `annotations` | array of [Annotation](#label-snowflake-agent-run-annotation) | Any annotations attached to the text result (e.g. citations) |
| `is_elicitation` | boolean | Whether this text content is the agent asking for more information from the end user. |

See moreShow less

Expand

**Example**

CopyExpand

```
{
  "content_index": 0,
  "text": "Lorem ipsum dolor...",
  "annotations": [
    {
      "type": "cortex_search_citation",
      "index": 0,
      "search_result_id": "cs_61987ff6-6d56-4695-83c0-1e7cfed818c7",
      "doc_id": "4ac085cb-82d0-4eb4-94f3-2672aa0599a2",
      "doc_title": "Earnings Report",
      "text": "The revenue for 2025 was..."
    }
  ],
  "is_elicitation": false
}
```

Show lessSee more

Scroll to top

### `response.text.delta`

Event streamed when a new output text delta is generated.

| Field | Type | Description |
| --- | --- | --- |
| `content_index` | integer | The index in the response content array this event represents |
| `text` | string | The text delta |
| `is_elicitation` | boolean | Whether this text content is the agent asking for more information from the end user. |

See moreShow less

Expand

**Example**

CopyExpand

```
{
  "content_index": 0,
  "text": "Hello",
  "is_elicitation": false
}
```

Show lessSee more

Scroll to top

### `response.text.annotation`

Event streamed when an annotation is added to a text content.

| Field | Type | Description |
| --- | --- | --- |
| `content_index` | integer | The index in the response content array this event represents |
| `annotation_index` | integer | The index in the annotation array this `annotation` belongs to. |
| `annotation` | [Annotation](#label-snowflake-agent-run-annotation) | The annotation object being added. |

See moreShow less

Expand

**Example**

CopyExpand

```
{
  "content_index": 0,
  "annotation_index": 0,
  "annotation": {
    "type": "cortex_search_citation",
    "index": 0,
    "search_result_id": "cs_61987ff6-6d56-4695-83c0-1e7cfed818c7",
    "doc_id": "4ac085cb-82d0-4eb4-94f3-2672aa0599a2",
    "doc_title": "Earnings Report",
    "text": "The revenue for 2025 was..."
  }
}
```

Show lessSee more

Scroll to top

### `response.thinking`

An event streamed when a thinking content block is done streaming, including all the aggregated deltas for a particular content index.

| Field | Type | Description |
| --- | --- | --- |
| `content_index` | integer | The index in the response content array this event represents |
| `text` | string | Thinking tokens from the agent |
| `signature` | string | The signature of the thinking token |

See moreShow less

Expand

**Example**

CopyExpand

```
{
  "content_index": 0,
  "text": "To answer your question I must...",
  "signature": "lorem ipsum"
}
```

Show lessSee more

Scroll to top

### `response.thinking.delta`

Event streamed when a thinking delta is generated.

| Field | Type | Description |
| --- | --- | --- |
| `content_index` | integer | The index in the response content array this event represents |
| `text` | string | The thinking token |
| `signature` | string | The signature of the thinking token |

See moreShow less

Expand

**Example**

CopyExpand

```
{
  "content_index": 0,
  "text": "lorem ipsum",
  "signature": "lorem ipsum"
}
```

Show lessSee more

Scroll to top

### `response.tool_use`

An event streamed when the agent requests a tool use.

| Field | Type | Description |
| --- | --- | --- |
| `content_index` | integer | The index in the response content array this event represents |
| `tool_use_id` | string | Unique identifier for this tool use. Can be used to associated tool results. |
| `type` | string | The type of the tool (e.g. cortex\_search, cortex\_analyst\_text\_to\_sql) |
| `name` | string | The unique identifier for this tool instance |
| `input` | object | The structured input for this tool. The schema of this object should will vary depending on the tool spec. |
| `client_side_execute` | boolean | Whether the tool use is executed on the client side. |

See moreShow less

Expand

**Example**

CopyExpand

```
{
  "content_index": 0,
  "tool_use_id": "toolu_123",
  "type": "cortex_analyst_text_to_sql",
  "name": "my_cortex_analyst_semantic_view",
  "input": {
    "location": "San Francisco, CA"
  },
  "client_side_execute": "true"
}
```

Show lessSee more

Scroll to top

### `response.tool_result`

Event streamed when a tool finishes executing, including the tool result.

| Field | Type | Description |
| --- | --- | --- |
| `content_index` | integer | The index in the response content array this event represents |
| `tool_use_id` | string | Unique identifier for this tool use. Can be used to associated tool results. |
| `type` | string | The type of the tool (e.g. cortex\_search, cortex\_analyst\_text\_to\_sql) |
| `name` | string | The unique identifier for this tool instance |
| `content` | array of [ToolResultContent](#label-snowflake-agent-run-toolresultcontent) | The content on the tool result |
| `status` | string | The status of tool execution |

See moreShow less

Expand

**Example**

CopyExpand

```
{
  "content_index": 0,
  "tool_use_id": "toolu_123",
  "type": "cortex_analyst_text_to_sql",
  "name": "my_cortex_analyst_semantic_view",
  "content": [
    {
      "type": "json",
      "json": {
        "answer": 42
      }
    }
  ],
  "status": "success"
}
```

Show lessSee more

Scroll to top

### `response.tool_result.status`

Status update for a specific tool use.

| Field | Type | Description |
| --- | --- | --- |
| `tool_use_id` | string | Unique identifier for this tool use. |
| `tool_type` | string | The type of the tool (e.g. cortex\_search, cortex\_analyst\_text\_to\_sql) |
| `status` | string | Enum for the current state. |
| `message` | string | A more descriptive message expanding on the current status. |
| `details` | object | Tool-specific status details. |

See moreShow less

Expand

**Example**

CopyExpand

```
{
  "tool_use_id": "toolu_123",
  "tool_type": "cortex_analyst_text_to_sql",
  "status": "Executing SQL",
  "message": "Executing query 'SELECT * FROM my_table'",
  "details": {}
}
```

Show lessSee more

Scroll to top

### `response.tool_result.analyst.delta`

An delta event streamed for the Cortex Analyst tool execution

| Field | Type | Description |
| --- | --- | --- |
| `content_index` | integer | The index in the response content array this event represents |
| `tool_use_id` | string | Unique identifier for this tool use. Can be used to associated tool results. |
| `tool_type` | string | The type of the tool (always cortex\_analyst\_text\_to\_sql for this event) |
| `tool_name` | string | The unique identifier for this tool instance |
| `delta` | [CortexAnalystToolResultDelta](#label-snowflake-agent-run-cortexanalysttoolresultdelta) | The content delta |

See moreShow less

Expand

**Example**

CopyExpand

```
{
  "content_index": 0,
  "tool_use_id": "toolu_123",
  "tool_type": "cortex_analyst_text_to_sql",
  "tool_name": "my_cortex_analyst_semantic_view",
  "delta": {
    "text": "The...",
    "think": "Thinking...",
    "sql": "SELECT...",
    "sql_explanation": "This...",
    "query_id": "707787a0-a684-4ead-adb0-3c3b62b043d9",
    "verified_query_used": false,
    "result_set": {
      "statementHandle": "707787a0-a684-4ead-adb0-3c3b62b043d9",
      "resultSetMetaData": {
        "partition": 0,
        "numRows": 0,
        "format": "jsonv2",
        "rowType": [
          {
            "name": "my_column",
            "type": "VARCHAR",
            "length": 0,
            "precision": 0,
            "scale": 0,
            "nullable": false
          }
        ]
      },
      "data": [
        [
          "row1 col1",
          "row1 col2"
        ],
        [
          "row2 col1",
          "row2 col2"
        ]
      ]
    },
    "suggestions": {
      "index": 0,
      "delta": "What..."
    }
  }
}
```

Show lessSee more

Scroll to top

### `response.table`

An event streamed when a table content block is added.

| Field | Type | Description |
| --- | --- | --- |
| `content_index` | integer | The index in the response content array this event represents |
| `tool_use_id` | string | The ID of the tool use that generated this table |
| `query_id` | string | The query id of the sql query that generated this data |
| `result_set` | [ResultSet](#label-snowflake-agent-run-resultset) | The SQL results to render a table. Matches the schema from Snowflake’s SQL API ResultSet (<https://docs.snowflake.com/en/developer-guide/sql-api/reference#resultset>) |
| `title` | string | The title for this table |

See moreShow less

Expand

**Example**

CopyExpand

```
{
  "content_index": 0,
  "tool_use_id": "toolu_123",
  "query_id": "6ac75378-6337-48a6-80ab-6de48dd680eb",
  "result_set": {
    "statementHandle": "707787a0-a684-4ead-adb0-3c3b62b043d9",
    "resultSetMetaData": {
      "partition": 0,
      "numRows": 0,
      "format": "jsonv2",
      "rowType": [
        {
          "name": "my_column",
          "type": "VARCHAR",
          "length": 0,
          "precision": 0,
          "scale": 0,
          "nullable": false
        }
      ]
    },
    "data": [
      [
        "row1 col1",
        "row1 col2"
      ],
      [
        "row2 col1",
        "row2 col2"
      ]
    ]
  },
  "title": "Revenue by Month"
}
```

Show lessSee more

Scroll to top

### `response.chart`

An event streamed when a chart content block is added.

| Field | Type | Description |
| --- | --- | --- |
| `content_index` | integer | The index in the response content array this event represents |
| `tool_use_id` | string | The ID of the tool use that generated this chart |
| `chart_spec` | string | The vega-lite chart specification serialized as a string |

See moreShow less

Expand

**Example**

CopyExpand

```
{
  "content_index": 0,
  "tool_use_id": "toolu_123",
  "chart_spec": "{\"$schema\":\"https://vega.github.io/schema/vega-lite/v5.json\",\"data\":{...},\"mark\":\"bar\"}"
}
```

Show lessSee more

Scroll to top

### `response.status`

Status update for the agent execution.

| Field | Type | Description |
| --- | --- | --- |
| `status` | string | Enum for the current state. |
| `message` | string | A more descriptive message expanding on the current status. |

See moreShow less

Expand

**Example**

CopyExpand

```
{
  "status": "executing_tool",
  "message": "Executing tool `my_analyst_tool`"
}
```

Show lessSee more

Scroll to top

### `response.warning`

Sent when a non-fatal warning occurs. The stream continues after this event.

| Field | Type | Description |
| --- | --- | --- |
| `message` | string | The warning message to display to the user. |

See moreShow less

Expand

**Example**

CopyExpand

```
{
  "message": "Unable to fetch tools from MCP server 'foo'. Response quality may be degraded."
}
```

Show lessSee more

Scroll to top

### `error`

Sent when a fatal error is encountered.

| Field | Type | Description |
| --- | --- | --- |
| `code` | string | The Snowflake error code |
| `message` | string | The error message |
| `request_id` | string | The unique identifier for this request |

See moreShow less

Expand

**Example**

CopyExpand

```
{
  "code": "399504",
  "message": "Error during execution",
  "request_id": "61987ff6-6d56-4695-83c0-1e7cfed818c7"
}
```

Show lessSee more

Scroll to top

### `metadata`

Metadata about the request. This event is sent when a message is added to the thread. It is useful for getting the `parent_message_id` to use in following requests to the Agents API.

| Field | Type | Description |
| --- | --- | --- |
| `metadata` | [Metadata](#label-snowflake-agent-run-metadata) |  |

See moreShow less

Expand

**Example**

CopyExpand

```
{
  "metadata": {
    "role": "user",
    "message_id": 0,
    "run_id": "123-456"
  }
}
```

Show lessSee more

Scroll to top

## Schemas

### `AgentInstructions`

| Field | Type | Description |
| --- | --- | --- |
| `response` | string | Instructions for response generation. |
| `orchestration` | string | These custom instructions are used when the agent is planning which tools to use. |
| `system` | string | System instructions for the agent. |

See moreShow less

Expand

**Example**

CopyExpand

```
{
  "response": "You will respond in a friendly but concise manner",
  "orchestration": "For any query related to revenue we should use Analyst; For all policy questions we should use Search",
  "system": "You are a friendly agent ..."
}
```

Show lessSee more

Scroll to top

### `Annotation`

> cortex\_search\_citation
>
> | Field | Type | Description |
> | --- | --- | --- |
> | `type` | string | The citation type (always `cortex_search_citation`) |
> | `index` | integer | The index of the citation in the search results. |
> | `search_result_id` | string | The unique identifier for the search result. |
> | `doc_id` | string | The unique identifier for the document. |
> | `doc_title` | string | The title of the document. |
> | `text` | string | The text excerpt from the document used as the citation. |
>
> See moreShow less
>
> Expand
>
> **Example**
>
> CopyExpand
>
> ```
> {
>   "type": "cortex_search_citation",
>   "index": 0,
>   "search_result_id": "cs_61987ff6-6d56-4695-83c0-1e7cfed818c7",
>   "doc_id": "4ac085cb-82d0-4eb4-94f3-2672aa0599a2",
>   "doc_title": "Earnings Report",
>   "text": "The revenue for 2025 was..."
> }
> ```
>
> Show lessSee more
>
> Scroll to top

### `BudgetConfig`

| Field | Type | Description |
| --- | --- | --- |
| `seconds` | integer | Time budget in seconds. |
| `tokens` | integer | Token budget. |

See moreShow less

Expand

**Example**

CopyExpand

```
{
  "seconds": 30,
  "tokens": 16000
}
```

Show lessSee more

Scroll to top

### `ChartContent`

| Field | Type | Description |
| --- | --- | --- |
| `tool_use_id` | string | The ID of the tool use that generated this chart |
| `chart_spec` | string | The vega-lite chart specification serialized as a string |

See moreShow less

Expand

**Example**

CopyExpand

```
{
  "tool_use_id": "toolu_123",
  "chart_spec": "{\"$schema\":\"https://vega.github.io/schema/vega-lite/v5.json\",\"data\":{...},\"mark\":\"bar\"}"
}
```

Show lessSee more

Scroll to top

### `CortexAnalystSuggestionDelta`

| Field | Type | Description |
| --- | --- | --- |
| `index` | integer | The index of the suggestion array this delta represents |
| `delta` | string | The text delta for the suggestion in this index |

See moreShow less

Expand

**Example**

CopyExpand

```
{
  "index": 0,
  "delta": "What..."
}
```

Show lessSee more

Scroll to top

### `CortexAnalystToolResultDelta`

| Field | Type | Description |
| --- | --- | --- |
| `text` | string | A text delta from Cortex Analyst’s final response. |
| `think` | string | A text delta from Cortex Analyst’s reasoning steps. |
| `sql` | string | A delta from Cortex Analyst’s SQL output. Currently, the entire SQL query comes in a single event but we may stream the SQL token-by-token in the future. |
| `sql_explanation` | string | A delta from Cortex Analyst’s explanation of what the SQL query does |
| `query_id` | string | The query id once SQL execution begins |
| `verified_query_used` | boolean | Whether a verified query was used to generate this response |
| `result_set` | [ResultSet](#label-snowflake-agent-run-resultset) | The results from SQL execution. Matches the schema from Snowflake’s SQL API ResultSet (<https://docs.snowflake.com/en/developer-guide/sql-api/reference#resultset>) |
| `suggestions` | [CortexAnalystSuggestionDelta](#label-snowflake-agent-run-cortexanalystsuggestiondelta) | A delta from Cortex Analyst’s suggested questions. This is sent when Analyst cannot answer the question due to missing information or other failures. |

See moreShow less

Expand

**Example**

CopyExpand

```
{
  "text": "The...",
  "think": "Thinking...",
  "sql": "SELECT...",
  "sql_explanation": "This...",
  "query_id": "707787a0-a684-4ead-adb0-3c3b62b043d9",
  "verified_query_used": false,
  "result_set": {
    "statementHandle": "707787a0-a684-4ead-adb0-3c3b62b043d9",
    "resultSetMetaData": {
      "partition": 0,
      "numRows": 0,
      "format": "jsonv2",
      "rowType": [
        {
          "name": "my_column",
          "type": "VARCHAR",
          "length": 0,
          "precision": 0,
          "scale": 0,
          "nullable": false
        }
      ]
    },
    "data": [
      [
        "row1 col1",
        "row1 col2"
      ],
      [
        "row2 col1",
        "row2 col2"
      ]
    ]
  },
  "suggestions": {
    "index": 0,
    "delta": "What..."
  }
}
```

Show lessSee more

Scroll to top

### `ExecutionEnvironment`

Configuration for server-executed tools.

| Field | Type | Description |
| --- | --- | --- |
| `type` | string | The type of execution environment, currently only `warehouse` is supported. |
| `warehouse` | string | The name of the warehouse. Case-sensitive, if it is an unquoted identifier, provide the name in all-caps. |
| `query_timeout` | integer | The query timeout in seconds |

See moreShow less

Expand

**Example**

CopyExpand

```
{
  "type": "warehouse",
  "warehouse": "MY_WAREHOUSE",
  "query_timeout": 60
}
```

Show lessSee more

Scroll to top

### `InputTokens`

Input token breakdown by cache usage.

| Field | Type | Description |
| --- | --- | --- |
| `total` | integer | Total input tokens processed (including cached tokens). |
| `cache_read` | integer | Input tokens read from cache. |
| `cache_write` | integer | Input tokens written to cache. |
| `uncached` | integer | Input tokens that were not cached. |

See moreShow less

Expand

**Example**

CopyExpand

```
{
  "total": 175,
  "cache_read": 50,
  "cache_write": 25,
  "uncached": 100
}
```

Show lessSee more

Scroll to top

### `Message`

Represents a single message in the conversation. Can be either from the user or the assistant.

| Field | Type | Description |
| --- | --- | --- |
| `role` | string | Identifies who sent the message - either the user or the assistant. User messages typically contain queries, while assistant messages contain responses and tool results. |
| `content` | array of [MessageContentItem](#label-snowflake-agent-run-messagecontentitem) | Array of content elements making up the message. Can include text, tool results, or custom content types. |

See moreShow less

Expand

**Example**

CopyExpand

```
{
  "role": "user",
  "content": [
    {
      "type": "text",
      "text": "What is the total revenue for 2023?"
    }
  ]
}
```

Show lessSee more

Scroll to top

### `MessageContentItem`

> charttabletextthinkingtool\_resulttool\_use
>
> | Field | Type | Description |
> | --- | --- | --- |
> | `type` | string | The content type (always `chart`). |
> | `chart` | [ChartContent](#label-snowflake-agent-run-chartcontent) | The chart. |
>
> See moreShow less
>
> Expand
>
> **Example**
>
> CopyExpand
>
> ```
> {
>   "type": "chart",
>   "chart": {
>     "tool_use_id": "toolu_123",
>     "chart_spec": "{\"$schema\":\"https://vega.github.io/schema/vega-lite/v5.json\",\"data\":{...},\"mark\":\"bar\"}"
>   }
> }
> ```
>
> Show lessSee more
>
> Scroll to top
>
> | Field | Type | Description |
> | --- | --- | --- |
> | `type` | string | The content type (always `table`). |
> | `table` | [TableContent](#label-snowflake-agent-run-tablecontent) | The table. |
>
> See moreShow less
>
> Expand
>
> **Example**
>
> CopyExpand
>
> ```
> {
>   "type": "table",
>   "table": {
>     "tool_use_id": "toolu_123",
>     "query_id": "6ac75378-6337-48a6-80ab-6de48dd680eb",
>     "result_set": {
>       "statementHandle": "707787a0-a684-4ead-adb0-3c3b62b043d9",
>       "resultSetMetaData": {
>         "partition": 0,
>         "numRows": 0,
>         "format": "jsonv2",
>         "rowType": [
>           {
>             "name": "my_column",
>             "type": "VARCHAR",
>             "length": 0,
>             "precision": 0,
>             "scale": 0,
>             "nullable": false
>           }
>         ]
>       },
>       "data": [
>         [
>           "row1 col1",
>           "row1 col2"
>         ],
>         [
>           "row2 col1",
>           "row2 col2"
>         ]
>       ]
>     },
>     "title": "Revenue by Month"
>   }
> }
> ```
>
> Show lessSee more
>
> Scroll to top
>
> | Field | Type | Description |
> | --- | --- | --- |
> | `text` | string | A text result from the agent |
> | `annotations` | array of [Annotation](#label-snowflake-agent-run-annotation) | Any annotations attached to the text result (e.g. citations) |
> | `is_elicitation` | boolean | Whether this text content is the agent asking for more information from the end user. |
> | `type` | string | The content type (always `text`). |
>
> See moreShow less
>
> Expand
>
> **Example**
>
> CopyExpand
>
> ```
> {
>   "text": "Lorem ipsum dolor...",
>   "annotations": [
>     {
>       "type": "cortex_search_citation",
>       "index": 0,
>       "search_result_id": "cs_61987ff6-6d56-4695-83c0-1e7cfed818c7",
>       "doc_id": "4ac085cb-82d0-4eb4-94f3-2672aa0599a2",
>       "doc_title": "Earnings Report",
>       "text": "The revenue for 2025 was..."
>     }
>   ],
>   "is_elicitation": false,
>   "type": "text"
> }
> ```
>
> Show lessSee more
>
> Scroll to top
>
> | Field | Type | Description |
> | --- | --- | --- |
> | `type` | string | The content type (always `thinking`). |
> | `thinking` | [ThinkingContent](#label-snowflake-agent-run-thinkingcontent) | The thinking content. |
>
> See moreShow less
>
> Expand
>
> **Example**
>
> CopyExpand
>
> ```
> {
>   "type": "thinking",
>   "thinking": {
>     "text": "To answer your question I must...",
>     "signature": "lorem ipsum"
>   }
> }
> ```
>
> Show lessSee more
>
> Scroll to top
>
> | Field | Type | Description |
> | --- | --- | --- |
> | `type` | string | The content type (always `tool_result`). |
> | `tool_result` | [ToolResult](#label-snowflake-agent-run-toolresult) | The tool result. |
>
> See moreShow less
>
> Expand
>
> **Example**
>
> CopyExpand
>
> ```
> {
>   "type": "tool_result",
>   "tool_result": {
>     "tool_use_id": "toolu_123",
>     "type": "cortex_analyst_text_to_sql",
>     "name": "my_cortex_analyst_semantic_view",
>     "content": [
>       {
>         "type": "json",
>         "json": {
>           "answer": 42
>         }
>       }
>     ],
>     "status": "success"
>   }
> }
> ```
>
> Show lessSee more
>
> Scroll to top
>
> | Field | Type | Description |
> | --- | --- | --- |
> | `type` | string | The content type (always `tool_use`). |
> | `tool_use` | [ToolUse](#label-snowflake-agent-run-tooluse) | The tool use. |
>
> See moreShow less
>
> Expand
>
> **Example**
>
> CopyExpand
>
> ```
> {
>   "type": "tool_use",
>   "tool_use": {
>     "tool_use_id": "toolu_123",
>     "type": "cortex_analyst_text_to_sql",
>     "name": "my_cortex_analyst_semantic_view",
>     "input": {
>       "location": "San Francisco, CA"
>     },
>     "client_side_execute": "true"
>   }
> }
> ```
>
> Show lessSee more
>
> Scroll to top

### `Metadata`

| Field | Type | Description |
| --- | --- | --- |
| `role` | string | Identifies who sent the message - either the user or the assistant. |
| `message_id` | integer | The thread message id. Use this ID (when role is `assistant`) to ask a followup question on the thread. |
| `run_id` | string | The unique identifier for this Agent Run. Can be used to reconnect to the output stream. |

See moreShow less

Expand

**Example**

CopyExpand

```
{
  "role": "user",
  "message_id": 0,
  "run_id": "123-456"
}
```

Show lessSee more

Scroll to top

### `ModelConfig`

| Field | Type | Description |
| --- | --- | --- |
| `orchestration` | string | Model to use for orchestration. If not provided, a model is automatically selected. |

See moreShow less

Expand

**Example**

CopyExpand

```
{
  "orchestration": "claude-4-sonnet"
}
```

Show lessSee more

Scroll to top

### `OrchestrationConfig`

| Field | Type | Description |
| --- | --- | --- |
| `budget` | [BudgetConfig](#label-snowflake-agent-run-budgetconfig) | Budget constraints for the agent. If more than one constraint is specified, whichever is first hit will end the request. |

See moreShow less

Expand

**Example**

CopyExpand

```
{
  "budget": {
    "seconds": 30,
    "tokens": 16000
  }
}
```

Show lessSee more

Scroll to top

### `OutputTokens`

Output token details.

| Field | Type | Description |
| --- | --- | --- |
| `total` | integer | Total output tokens generated. |

See moreShow less

Expand

**Example**

CopyExpand

```
{
  "total": 75
}
```

Show lessSee more

Scroll to top

### `ResponseMetadata`

Metadata about the response, including usage information.

| Field | Type | Description |
| --- | --- | --- |
| `usage` | [UsageMetadata](#label-snowflake-agent-run-usagemetadata) |  |
| `run_id` | string | The unique identifier for this Agent Run. Can be used to reconnect to the output stream. |

See moreShow less

Expand

**Example**

CopyExpand

```
{
  "usage": {
    "tokens_consumed": [
      {
        "model_name": "llama3.1-70b",
        "input_tokens": {
          "total": 175,
          "cache_read": 50,
          "cache_write": 25,
          "uncached": 100
        },
        "output_tokens": {
          "total": 75
        },
        "context_window": 128000
      }
    ]
  },
  "run_id": "123-456"
}
```

Show lessSee more

Scroll to top

### `ResultSet`

| Field | Type | Description |
| --- | --- | --- |
| `statementHandle` | string | The query id. |
| `resultSetMetaData` | [ResultSetMetaData](#label-snowflake-agent-run-resultsetmetadata) | Metadata on the result set. |
| `data` | array of array | 2D array representing the data |

See moreShow less

Expand

**Example**

CopyExpand

```
{
  "statementHandle": "707787a0-a684-4ead-adb0-3c3b62b043d9",
  "resultSetMetaData": {
    "partition": 0,
    "numRows": 0,
    "format": "jsonv2",
    "rowType": [
      {
        "name": "my_column",
        "type": "VARCHAR",
        "length": 0,
        "precision": 0,
        "scale": 0,
        "nullable": false
      }
    ]
  },
  "data": [
    [
      "row1 col1",
      "row1 col2"
    ],
    [
      "row2 col1",
      "row2 col2"
    ]
  ]
}
```

Show lessSee more

Scroll to top

### `ResultSetMetaData`

| Field | Type | Description |
| --- | --- | --- |
| `partition` | integer | The index number of the partition. |
| `numRows` | integer | The total number of rows of results. |
| `format` | string | Format of the data in the result set. |
| `rowType` | array of [RowType](#label-snowflake-agent-run-rowtype) | Description of the columns in the result. |

See moreShow less

Expand

**Example**

CopyExpand

```
{
  "partition": 0,
  "numRows": 0,
  "format": "jsonv2",
  "rowType": [
    {
      "name": "my_column",
      "type": "VARCHAR",
      "length": 0,
      "precision": 0,
      "scale": 0,
      "nullable": false
    }
  ]
}
```

Show lessSee more

Scroll to top

### `RowType`

| Field | Type | Description |
| --- | --- | --- |
| `name` | string | Name of the column. |
| `type` | string | Snowflake data type of the column. (<https://docs.snowflake.com/en/sql-reference/intro-summary-data-types>) |
| `length` | integer | Length of the column. |
| `precision` | integer | Precision of the column. |
| `scale` | integer | Scale of the column. |
| `nullable` | boolean | Specifies whether or not the column is nullable. |

See moreShow less

Expand

**Example**

CopyExpand

```
{
  "name": "my_column",
  "type": "VARCHAR",
  "length": 0,
  "precision": 0,
  "scale": 0,
  "nullable": false
}
```

Show lessSee more

Scroll to top

### `TableContent`

| Field | Type | Description |
| --- | --- | --- |
| `tool_use_id` | string | The ID of the tool use that generated this table |
| `query_id` | string | The query id of the sql query that generated this data |
| `result_set` | [ResultSet](#label-snowflake-agent-run-resultset) | The SQL results to render a table. Matches the schema from Snowflake’s SQL API ResultSet (<https://docs.snowflake.com/en/developer-guide/sql-api/reference#resultset>) |
| `title` | string | The title for this table |

See moreShow less

Expand

**Example**

CopyExpand

```
{
  "tool_use_id": "toolu_123",
  "query_id": "6ac75378-6337-48a6-80ab-6de48dd680eb",
  "result_set": {
    "statementHandle": "707787a0-a684-4ead-adb0-3c3b62b043d9",
    "resultSetMetaData": {
      "partition": 0,
      "numRows": 0,
      "format": "jsonv2",
      "rowType": [
        {
          "name": "my_column",
          "type": "VARCHAR",
          "length": 0,
          "precision": 0,
          "scale": 0,
          "nullable": false
        }
      ]
    },
    "data": [
      [
        "row1 col1",
        "row1 col2"
      ],
      [
        "row2 col1",
        "row2 col2"
      ]
    ]
  },
  "title": "Revenue by Month"
}
```

Show lessSee more

Scroll to top

### `ThinkingContent`

| Field | Type | Description |
| --- | --- | --- |
| `text` | string | Thinking tokens from the agent |
| `signature` | string | The signature of the thinking token |

See moreShow less

Expand

**Example**

CopyExpand

```
{
  "text": "To answer your question I must...",
  "signature": "lorem ipsum"
}
```

Show lessSee more

Scroll to top

### `TokensConsumed`

Token consumption for a specific model.

| Field | Type | Description |
| --- | --- | --- |
| `model_name` | string | Name of the model used. |
| `input_tokens` | [InputTokens](#label-snowflake-agent-run-inputtokens) |  |
| `output_tokens` | [OutputTokens](#label-snowflake-agent-run-outputtokens) |  |
| `context_window` | integer | The model’s context window size (in tokens). |

See moreShow less

Expand

**Example**

CopyExpand

```
{
  "model_name": "llama3.1-70b",
  "input_tokens": {
    "total": 175,
    "cache_read": 50,
    "cache_write": 25,
    "uncached": 100
  },
  "output_tokens": {
    "total": 75
  },
  "context_window": 128000
}
```

Show lessSee more

Scroll to top

### `Tool`

Defines a tool that can be used by the agent. Tools provide specific capabilities like data analysis, search, or generic functions.

| Field | Type | Description |
| --- | --- | --- |
| `tool_spec` | [ToolSpec](#label-snowflake-agent-run-toolspec) | Specification of the tool’s type, configuration, and input requirements. |

See moreShow less

Expand

**Example**

CopyExpand

```
{
  "tool_spec": {
    "type": "generic",
    "name": "get_revenue",
    "description": "Fetch the delivery revenue for a location.",
    "input_schema": {
      "type": "object",
      "properties": {
        "location": {
          "type": "string",
          "description": "The city and state, e.g. San Francisco, CA"
        }
      }
    },
    "required": [
      "location"
    ]
  }
}
```

Show lessSee more

Scroll to top

### `ToolChoice`

| Field | Type | Description |
| --- | --- | --- |
| `type` | string | Determines how tools are selected: - auto - Automatic tool selection (default) - required - Must use at least one tool - tool - Use specific named tools |
| `name` | array of string | List of specific tool names to use when type is ‘tool’. |

See moreShow less

Expand

**Example**

CopyExpand

```
{
  "type": "auto",
  "name": [
    "analyst_tool",
    "search_tool"
  ]
}
```

Show lessSee more

Scroll to top

### `ToolInputSchema`

| Field | Type | Description |
| --- | --- | --- |
| `type` | string | The type of the input schema object. |
| `description` | string | A description of what the input is. |
| `properties` | map of [ToolInputSchema](#label-snowflake-agent-run-toolinputschema) | If type is `object`, definitions of each input parameter. |
| `items` | [ToolInputSchema](#label-snowflake-agent-run-toolinputschema) | If type is `array`, the schema for the elements of the array. |
| `required` | array of string | If type is `object`, list of required input parameter names. |

See moreShow less

Expand

**Example**

CopyExpand

```
{
  "type": "object",
  "description": "Input for my custom tool",
  "properties": {
    "location": {
      "type": "string",
      "description": "The city and state, e.g. San Francisco, CA"
    }
  },
  "items": {},
  "required": [
    "location"
  ]
}
```

Show lessSee more

Scroll to top

### `ToolResource`

> cortex\_analyst\_text\_to\_sqlcortex\_searchgenericweb\_search
>
> Configuration for text-to-SQL analysis tool. Provides parameters for SQL query generation and execution. Exactly one of semantic\_model\_file or semantic\_view must be provided.
>
> | Field | Type | Description |
> | --- | --- | --- |
> | `semantic_model_file` | string | The path to a file stored in a Snowflake Stage holding the semantic model yaml. |
> | `semantic_view` | string | The name of the Snowflake native semantic model object. |
> | `execution_environment` | [ExecutionEnvironment](#label-snowflake-agent-run-executionenvironment) | Configuration for how to execute the generated SQL query. |
>
> See moreShow less
>
> Expand
>
> **Example**
>
> CopyExpand
>
> ```
> {
>   "semantic_model_file": "@db.schema.stage/semantic_model.yaml",
>   "semantic_view": "db.schema.semantic_view",
>   "execution_environment": {
>     "type": "warehouse",
>     "warehouse": "MY_WAREHOUSE",
>     "query_timeout": 60
>   }
> }
> ```
>
> Show lessSee more
>
> Scroll to top
>
> Configuration for search functionality. Defines how document search and retrieval should be performed.
>
> | Field | Type | Description |
> | --- | --- | --- |
> | `search_service` | string | The fully qualified name of the search service. |
> | `title_column` | string | The title column of the document. |
> | `id_column` | string | The ID column of the document. |
> | `filter` | object | Filter query for search results. |
>
> See moreShow less
>
> Expand
>
> **Example**
>
> CopyExpand
>
> ```
> {
>   "search_service": "database.schema.service_name",
>   "title_column": "account_name",
>   "id_column": "account_id",
>   "filter": {
>     "@eq": {
>       "<column>": "<value>"
>     }
>   }
> }
> ```
>
> Show lessSee more
>
> Scroll to top
>
> | Field | Type | Description |
> | --- | --- | --- |
> | `type` | string | If the tool is server-side executed, whether it is a Stored Procedure or a UDF. |
> | `execution_environment` | [ExecutionEnvironment](#label-snowflake-agent-run-executionenvironment) |  |
> | `identifier` | string | Fully qualified name of the Stored Procedure or UDF. |
>
> See moreShow less
>
> Expand
>
> **Example**
>
> CopyExpand
>
> ```
> {
>   "type": "function",
>   "execution_environment": {
>     "type": "warehouse",
>     "warehouse": "MY_WAREHOUSE",
>     "query_timeout": 60
>   },
>   "identifier": "MY_DB.MY_SCHEMA.MY_UDF"
> }
> ```
>
> Show lessSee more
>
> Scroll to top
>
> Configuration for web search functionality.
>
> | Field | Type | Description |
> | --- | --- | --- |
> | `max_results` | integer | Max web search results returned. |
>
> See moreShow less
>
> Expand
>
> **Example**
>
> CopyExpand
>
> ```
> {
>   "max_results": 20
> }
> ```
>
> Show lessSee more
>
> Scroll to top

### `ToolResult`

| Field | Type | Description |
| --- | --- | --- |
| `tool_use_id` | string | Unique identifier for this tool use. Can be used to associated tool results. |
| `type` | string | The type of the tool (e.g. cortex\_search, cortex\_analyst\_text\_to\_sql) |
| `name` | string | The unique identifier for this tool instance |
| `content` | array of [ToolResultContent](#label-snowflake-agent-run-toolresultcontent) | The content on the tool result |
| `status` | string | The status of tool execution |

See moreShow less

Expand

**Example**

CopyExpand

```
{
  "tool_use_id": "toolu_123",
  "type": "cortex_analyst_text_to_sql",
  "name": "my_cortex_analyst_semantic_view",
  "content": [
    {
      "type": "json",
      "json": {
        "answer": 42
      }
    }
  ],
  "status": "success"
}
```

Show lessSee more

Scroll to top

### `ToolResultContent`

> jsontext
>
> | Field | Type | Description |
> | --- | --- | --- |
> | `type` | string | The type of result (always `json`) |
> | `json` | object | Structured output from a tool. The schema varies depending on the tool type. |
>
> See moreShow less
>
> Expand
>
> **Example**
>
> CopyExpand
>
> ```
> {
>   "type": "json",
>   "json": {
>     "answer": 42
>   }
> }
> ```
>
> Show lessSee more
>
> Scroll to top
>
> | Field | Type | Description |
> | --- | --- | --- |
> | `type` | string | The type of result (always `text`) |
> | `text` | string | The result text |
>
> See moreShow less
>
> Expand
>
> **Example**
>
> CopyExpand
>
> ```
> {
>   "type": "text",
>   "text": "The answer is 42"
> }
> ```
>
> Show lessSee more
>
> Scroll to top

### `ToolSpec`

Specification of the tool’s type, configuration, and input requirements.

| Field | Type | Description |
| --- | --- | --- |
| `type` | string | The type of tool capability. Can be specialized types like ‘cortex\_analyst\_text\_to\_sql’ or ‘generic’ for general-purpose tools. |
| `name` | string | Unique identifier for referencing this tool instance. Used to match with configuration in tool\_resources. |
| `description` | string | Description of the tool to be considered for tool use. |
| `input_schema` | [ToolInputSchema](#label-snowflake-agent-run-toolinputschema) | JSON Schema definition of the expected input parameters for this tool. This will be fed to the agent so it knows the structure it should follow for when generating the input for ToolUses. Required for generic tools to specify their input parameters. |

See moreShow less

Expand

**Example**

CopyExpand

```
{
  "type": "generic",
  "name": "get_weather",
  "description": "lorem ipsum",
  "input_schema": {
    "type": "object",
    "properties": {
      "location": {
        "type": "string",
        "description": "The city and state, e.g. San Francisco, CA"
      }
    },
    "required": [
      "location"
    ]
  }
}
```

Show lessSee more

Scroll to top

### `ToolUse`

| Field | Type | Description |
| --- | --- | --- |
| `tool_use_id` | string | Unique identifier for this tool use. Can be used to associated tool results. |
| `type` | string | The type of the tool (e.g. cortex\_search, cortex\_analyst\_text\_to\_sql) |
| `name` | string | The unique identifier for this tool instance |
| `input` | object | The structured input for this tool. The schema of this object should will vary depending on the tool spec. |
| `client_side_execute` | boolean | Whether the tool use is executed on the client side. |

See moreShow less

Expand

**Example**

CopyExpand

```
{
  "tool_use_id": "toolu_123",
  "type": "cortex_analyst_text_to_sql",
  "name": "my_cortex_analyst_semantic_view",
  "input": {
    "location": "San Francisco, CA"
  },
  "client_side_execute": "true"
}
```

Show lessSee more

Scroll to top

### `UsageMetadata`

Token usage information for this request.

| Field | Type | Description |
| --- | --- | --- |
| `tokens_consumed` | array of [TokensConsumed](#label-snowflake-agent-run-tokensconsumed) | Token consumption details per model used in this request. |

See moreShow less

Expand

**Example**

CopyExpand

```
{
  "tokens_consumed": [
    {
      "model_name": "llama3.1-70b",
      "input_tokens": {
        "total": 175,
        "cache_read": 50,
        "cache_write": 25,
        "uncached": 100
      },
      "output_tokens": {
        "total": 75
      },
      "context_window": 128000
    }
  ]
}
```

Show lessSee more

Scroll to top

### `Warning`

| Field | Type | Description |
| --- | --- | --- |
| `message` | string | The warning message to display to the user. |

See moreShow less

Expand

**Example**

CopyExpand

```
{
  "message": "Unable to fetch tools from MCP server 'foo'. Response quality may be degraded."
}
```

Show lessSee more

Scroll to top

## Non-streaming response (stream: false)

To receive a **single non-streaming JSON response**, set `stream` to `false` in the request body and set the request `Accept` header to `application/json`.

The response body is the same object as the `response` event payload in streaming mode (that is, it corresponds to the JSON returned in the SSE `response` event’s `data` field).

**Example response**

CopyExpand

```
{
  "role": "assistant",
  "content": [
    {
      "thinking": {
        "text": "\nThe user is asking about types of products...\n"
      },
      "type": "thinking"
    },
    {
      "tool_use": {
        "client_side_execute": false,
        "input": {
          "has_time_column": false,
          "need_future_forecasting_data": false,
          "original_query": "what are some types of products?",
          "previous_related_tool_result_id": "",
          "query": "What are the different types or categories of products?"
        },
        "name": "semantic_view_a",
        "tool_use_id": "<tool_use_id>",
        "type": "cortex_analyst_text_to_sql"
      },
      "type": "tool_use"
    },
    {
      "tool_result": {
        "content": [
          {
            "json": {
              "query_id": "<query_id>",
              "result_set": {
                "data": [
                  ["Electronics", "3", "3"],
                  ["Furniture", "2", "2"]
                ],
                "resultSetMetaData": {
                  "format": "jsonv2",
                  "numRows": 2,
                  "partition": 0
                },
                "statementHandle": "<statement_handle>"
              },
              "sql": "WITH __table_a AS (...) SELECT ...",
              "text": "The question is clear and I can answer it with the following SQL."
            },
            "type": "json"
          }
        ],
        "name": "semantic_view_a",
        "status": "success",
        "tool_use_id": "<tool_use_id>",
        "type": "cortex_analyst_text_to_sql"
      },
      "type": "tool_result"
    },
    {
      "text": "Based on the data available, there are 2 main types of products...",
      "type": "text"
    }
  ]
}
```

Show lessSee more

Scroll to top
