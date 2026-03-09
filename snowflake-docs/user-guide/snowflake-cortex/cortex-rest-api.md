---
title: "Cortex REST API"
url: "https://docs.snowflake.com/en/user-guide/snowflake-cortex/cortex-rest-api"
---

# Cortex REST API

The Cortex REST API gives you access to leading frontier models from Anthropic, OpenAI, Meta, Mistral, and more
through your preferred endpoint or SDK. All inference runs within the Snowflake perimeter, so your data remains
secure and within your governance boundary. See below on how to get started.

## Choose your API

Cortex REST API supports two industry-standard API specifications. Pick the one that best fits your stack:

|  | **Chat Completions API** | **Messages API** |
| --- | --- | --- |
| Compatibility | [OpenAI Chat Completions API](https://platform.openai.com/docs/api-reference/chat/create) | [Anthropic Messages API](https://docs.anthropic.com/en/api/messages) |
| Endpoint | `/api/v2/cortex/v1/chat/completions` | `/api/v2/cortex/v1/messages` |
| Supported models | All models (OpenAI, Claude, Llama, Mistral, DeepSeek, Snowflake) | Claude models only |
| SDK support | OpenAI Python and JavaScript SDKs | Anthropic Python SDK |
| Best for | Most use cases; multi-model flexibility | Existing Anthropic integrations; Anthropic API parity |

See moreShow less

Expand

Both APIs share the same authentication, model catalog, and rate limits. The only difference is the
request/response format and which models each endpoint supports. For pricing, see the
[Snowflake Service Consumption Table](https://www.snowflake.com/legal-files/CreditConsumptionTable.pdf).

## Quickstart

### Prerequisites

Before you begin, you need:

1. Your **Snowflake account URL** (e.g., `https://<account-identifier>.snowflakecomputing.com`).
2. A **Snowflake Programmatic Access Token (PAT)** for authentication. See [Generating a programmatic access token](../programmatic-access-tokens.html#label-pat-generate).
3. A **model name** to use in requests. See [Model availability](#label-cortex-complete-llm-model-availability) for available models.

### Chat Completions quickstart

The Chat Completions API follows the OpenAI specification. You can use the OpenAI SDK directly.

PythonJavaScript/TypeScriptcurl

CopyExpand

```
from openai import OpenAI

client = OpenAI(
  api_key="<SNOWFLAKE_PAT>",
  base_url="https://<account-identifier>.snowflakecomputing.com/api/v2/cortex/v1"
)

response = client.chat.completions.create(
  model="claude-sonnet-4-5",
  messages=[
    {"role": "system", "content": "You are a helpful assistant."},
    {"role": "user", "content": "How does a snowflake get its unique pattern?"}
  ]
)

print(response.choices[0].message.content)
```

Show lessSee more

Scroll to top

CopyExpand

```
import OpenAI from "openai";

const client = new OpenAI({
  apiKey: "<SNOWFLAKE_PAT>",
  baseURL: "https://<account-identifier>.snowflakecomputing.com/api/v2/cortex/v1"
});

const response = await client.chat.completions.create({
  model: "claude-sonnet-4-5",
  messages: [
    { role: "system", content: "You are a helpful assistant." },
    { role: "user", content: "How does a snowflake get its unique pattern?" }
  ],
});

console.log(response.choices[0].message.content);
```

Show lessSee more

Scroll to top

CopyExpand

```
curl "https://<account-identifier>.snowflakecomputing.com/api/v2/cortex/v1/chat/completions" \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer <SNOWFLAKE_PAT>" \
  -d '{
    "model": "claude-sonnet-4-5",
    "messages": [
      {"role": "user", "content": "How does a snowflake get its unique pattern?"}
    ]
  }'
```

Show lessSee more

Scroll to top

In the preceding examples, replace the following:

* `<account-identifier>`: Your Snowflake account identifier.
* `<SNOWFLAKE_PAT>`: Your Snowflake Programmatic Access Token (PAT).
* `model`: The model name. See [Model availability](#label-cortex-complete-llm-model-availability) for supported models.

### Messages API quickstart

The Messages API follows the Anthropic specification and supports Claude models only.

PythonJavaScript/TypeScriptcurl

The Anthropic SDK sends credentials via `x-api-key` by default, but Snowflake expects a `Bearer` token.
Use an `httpx` client to set the correct authorization header.

CopyExpand

```
import httpx
import anthropic

PAT = "<SNOWFLAKE_PAT>"

http_client = httpx.Client(
  headers={"Authorization": f"Bearer {PAT}"},
)

client = anthropic.Anthropic(
  api_key="not-used",
  base_url="https://<account-identifier>.snowflakecomputing.com/api/v2/cortex",
  http_client=http_client,
  default_headers={"Authorization": f"Bearer {PAT}"},
)

response = client.messages.create(
  model="claude-sonnet-4-5",
  max_tokens=1024,
  messages=[
    {"role": "user", "content": "How does a snowflake get its unique pattern?"}
  ],
)

print(response.content[0].text)
```

Show lessSee more

Scroll to top

Like Python, override the default auth header with a `Bearer` token via `defaultHeaders`.

CopyExpand

```
import Anthropic from "@anthropic-ai/sdk";

const PAT = "<SNOWFLAKE_PAT>";

const client = new Anthropic({
  apiKey: "not-used",
  baseURL: "https://<account-identifier>.snowflakecomputing.com/api/v2/cortex",
  defaultHeaders: {
    "Authorization": `Bearer ${PAT}`,
  },
});

const response = await client.messages.create({
  model: "claude-sonnet-4-5",
  max_tokens: 1024,
  messages: [
    { role: "user", content: "How does a snowflake get its unique pattern?" }
  ],
});

console.log(response.content[0].text);
```

Show lessSee more

Scroll to top

CopyExpand

```
curl "https://<account-identifier>.snowflakecomputing.com/api/v2/cortex/v1/messages" \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer <SNOWFLAKE_PAT>" \
  -H "anthropic-version: 2023-06-01" \
  -d '{
    "model": "claude-sonnet-4-5",
    "max_tokens": 1024,
    "messages": [
      {"role": "user", "content": "How does a snowflake get its unique pattern?"}
    ]
  }'
```

Show lessSee more

Scroll to top

In the preceding examples, replace the following:

* `<account-identifier>`: Your Snowflake account identifier.
* `<SNOWFLAKE_PAT>`: Your Snowflake Programmatic Access Token (PAT).
* `model`: The Claude model name. See [Model availability](#label-cortex-complete-llm-model-availability) for supported models.

## Setting up authentication

To authenticate to the Cortex REST API, you can use the methods described in
[Authenticating Snowflake REST APIs with Snowflake](../../developer-guide/snowflake-rest-api/authentication).

Set the `Authorization` header to include your token (for example, a JSON web token (JWT), OAuth token, or
[programmatic access token](../programmatic-access-tokens)).

Tip

Consider creating a dedicated user for Cortex REST API requests.

## Setting up authorization

To send a REST API request, your default role must be granted the SNOWFLAKE.CORTEX\_USER database role.
In most cases, users already have this privilege because SNOWFLAKE.CORTEX\_USER is granted to the PUBLIC
role automatically, and all roles inherit PUBLIC.

If your Snowflake administrator has revoked this grant, they must re-grant it:

CopyExpand

```
GRANT DATABASE ROLE SNOWFLAKE.CORTEX_USER TO ROLE my_role;
GRANT ROLE my_role TO USER my_user;
```

Show lessSee more

Scroll to top

Important

REST API requests use the user’s default role, so that role must have the necessary privileges. You can change
a user’s default role with [ALTER USER … SET DEFAULT\_ROLE](../../sql-reference/sql/alter-user).

CopyExpand

```
ALTER USER my_user SET DEFAULT_ROLE=my_role
```

Show lessSee more

Scroll to top

## Model availability

The following tables show the models available in the Cortex REST API for each region:

Cross-region and Cross-cloudNorth AmericaEuropeAsia-Pacific

| Model | Cross Cloud  (Any Region) | AWS Global  (Cross-Region) | AWS US  (Cross-Region) | AWS EU  (Cross-Region) | AWS APJ  (Cross-Region) | Azure Global  (Cross-Region) | Azure US  (Cross-Region) | Azure EU  (Cross-Region) |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| `claude-sonnet-4-6` | ✔ | ✔ | ✔ | ✔ |  |  |  |  |
| `claude-opus-4-6` | ✔ | ✔ |  |  |  |  |  |  |
| `claude-sonnet-4-5` | ✔ | ✔ | ✔ | ✔ |  |  |  |  |
| `claude-opus-4-5` | ✔ | ✔ |  |  |  |  |  |  |
| `claude-haiku-4-5` | ✔ | ✔ | ✔ |  |  |  |  |  |
| `claude-4-sonnet` | ✔ | ✔ | ✔ | ✔ |  |  |  |  |
| `claude-4-opus` | ✔ | ✔ |  |  |  |  |  |  |
| `claude-3-7-sonnet` | ✔ | ✔ |  |  |  |  |  |  |
| `claude-3-5-sonnet` | ✔ | ✔ |  |  |  |  |  |  |
| `openai-gpt-4.1` | ✔ |  |  |  | ✔ |  |  |  |
| `openai-gpt-5` | \* |  |  |  | \* | \* | \* |  |
| `openai-gpt-5-mini` | \* |  |  |  |  | \* |  |  |
| `openai-gpt-5-nano` | \* |  |  |  |  | \* |  |  |
| `openai-gpt-5-chat` | ✔ |  |  |  |  |  |  |  |
| `openai-gpt-oss-120b` | \* |  |  |  |  |  |  |  |
| `llama4-maverick` | ✔ | ✔ |  |  |  |  |  |  |
| `llama3.1-8b` | ✔ | ✔ |  |  |  |  |  |  |
| `llama3.1-70b` | ✔ | ✔ |  |  |  |  |  |  |
| `llama3.1-405b` | ✔ | ✔ |  |  |  |  |  |  |
| `deepseek-r1` | ✔ | ✔ |  |  |  |  |  |  |
| `mistral-7b` | ✔ | ✔ |  |  |  |  |  |  |
| `mistral-large` | ✔ | ✔ |  |  |  |  |  |  |
| `mistral-large2` | ✔ | ✔ |  |  |  |  |  |  |
| `snowflake-llama-3.3-70b` | ✔ | ✔ |  |  |  |  |  |  |

See moreShow less

Expand

| Model | AWS US West 2  (Oregon) | AWS US East 1  (N. Virginia) | Azure East US 2  (Virginia) |
| --- | --- | --- | --- |
| `claude-3-5-sonnet` | ✔ | ✔ |  |
| `llama4-maverick` | ✔ |  |  |
| `llama3.1-8b` | ✔ | ✔ | ✔ |
| `llama3.1-70b` | ✔ | ✔ | ✔ |
| `llama3.1-405b` | ✔ | ✔ | ✔ |
| `deepseek-r1` | ✔ |  |  |
| `mistral-7b` | ✔ | ✔ | ✔ |
| `mistral-large` | ✔ | ✔ | ✔ |
| `mistral-large2` | ✔ | ✔ | ✔ |
| `snowflake-llama-3.3-70b` | ✔ |  |  |

See moreShow less

Expand

| Model | AWS Europe Central 1  (Frankfurt) | AWS Europe West 1  (Ireland) | Azure West Europe  (Netherlands) |
| --- | --- | --- | --- |
| `llama3.1-8b` | ✔ |  | ✔ |
| `llama3.1-70b` | ✔ | ✔ | ✔ |
| `mistral-7b` | ✔ |  | ✔ |
| `mistral-large` | ✔ |  | ✔ |
| `mistral-large2` | ✔ | ✔ | ✔ |

See moreShow less

Expand

| Model | AWS AP Southeast 2  (Sydney) | AWS AP Northeast 1  (Tokyo) |
| --- | --- | --- |
| `llama3.1-8b` | ✔ | ✔ |
| `llama3.1-70b` | ✔ | ✔ |
| `mistral-7b` |  | ✔ |
| `mistral-large` |  | ✔ |
| `mistral-large2` | ✔ | ✔ |

See moreShow less

Expand

**\*** Indicates a preview function or model. Preview features are not suitable for production workloads.

You can also use any [fine-tuned](cortex-finetuning) model in any supported region.

## Features

### Streaming

Both APIs support streaming responses using [server-sent events](https://developer.mozilla.org/en-US/docs/Web/API/Server-sent_events/Using_server-sent_events).

#### Chat Completions streaming

PythonJavaScript/TypeScriptcurl

CopyExpand

```
from openai import OpenAI

client = OpenAI(
  api_key="<SNOWFLAKE_PAT>",
  base_url="https://<account-identifier>.snowflakecomputing.com/api/v2/cortex/v1"
)

response = client.chat.completions.create(
  model="claude-sonnet-4-5",
  messages=[
    {"role": "system", "content": "You are a helpful assistant."},
    {"role": "user", "content": "How does a snowflake get its unique pattern?"}
  ],
  stream=True
)

for chunk in response:
    print(chunk.choices[0].delta.content, end="", flush=True)
```

Show lessSee more

Scroll to top

CopyExpand

```
import OpenAI from "openai";

const client = new OpenAI({
  apiKey: "<SNOWFLAKE_PAT>",
  baseURL: "https://<account-identifier>.snowflakecomputing.com/api/v2/cortex/v1"
});

const stream = await client.chat.completions.create({
  model: "claude-sonnet-4-5",
  messages: [
    { role: "system", content: "You are a helpful assistant." },
    { role: "user", content: "How does a snowflake get its unique pattern?" }
  ],
  stream: true,
});

for await (const event of stream) {
  process.stdout.write(event.choices[0]?.delta?.content || "");
}
```

Show lessSee more

Scroll to top

CopyExpand

```
curl "https://<account-identifier>.snowflakecomputing.com/api/v2/cortex/v1/chat/completions" \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer <SNOWFLAKE_PAT>" \
  -d '{
    "model": "claude-sonnet-4-5",
    "messages": [
      {"role": "user", "content": "How does a snowflake get its unique pattern?"}
    ],
    "stream": true,
    "stream_options": {
      "include_usage": true
    }
  }'
```

Show lessSee more

Scroll to top

#### Messages API streaming

PythonJavaScript/TypeScriptcurl

CopyExpand

```
import httpx
import anthropic

PAT = "<SNOWFLAKE_PAT>"

http_client = httpx.Client(
  headers={"Authorization": f"Bearer {PAT}"},
)

client = anthropic.Anthropic(
  api_key="not-used",
  base_url="https://<account-identifier>.snowflakecomputing.com/api/v2/cortex",
  http_client=http_client,
  default_headers={"Authorization": f"Bearer {PAT}"},
)

with client.messages.stream(
  model="claude-sonnet-4-5",
  max_tokens=1024,
  messages=[
    {"role": "user", "content": "How does a snowflake get its unique pattern?"}
  ],
) as stream:
    for text in stream.text_stream:
        print(text, end="", flush=True)
```

Show lessSee more

Scroll to top

CopyExpand

```
import Anthropic from "@anthropic-ai/sdk";

const PAT = "<SNOWFLAKE_PAT>";

const client = new Anthropic({
  apiKey: "not-used",
  baseURL: "https://<account-identifier>.snowflakecomputing.com/api/v2/cortex",
  defaultHeaders: {
    "Authorization": `Bearer ${PAT}`,
  },
});

const stream = client.messages.stream({
  model: "claude-sonnet-4-5",
  max_tokens: 1024,
  messages: [
    { role: "user", content: "How does a snowflake get its unique pattern?" }
  ],
});

for await (const event of stream) {
  if (event.type === "content_block_delta" && event.delta.type === "text_delta") {
    process.stdout.write(event.delta.text);
  }
}
```

Show lessSee more

Scroll to top

CopyExpand

```
curl "https://<account-identifier>.snowflakecomputing.com/api/v2/cortex/v1/messages" \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer <SNOWFLAKE_PAT>" \
  -H "anthropic-version: 2023-06-01" \
  -d '{
    "model": "claude-sonnet-4-5",
    "max_tokens": 1024,
    "stream": true,
    "messages": [
      {"role": "user", "content": "How does a snowflake get its unique pattern?"}
    ]
  }'
```

Show lessSee more

Scroll to top

### Tool calling

Tool calling lets the model invoke external functions during a conversation. The flow works in steps:

1. You send a request with a list of available tools.
2. The model decides to call one or more tools and returns the tool name and arguments.
3. You execute the tool on your end.
4. You send the tool result back, and the model generates a final response.

Tool calling is supported for OpenAI and Claude models.

#### Chat Completions tool calling

PythonJavaScript/TypeScriptcurl

CopyExpand

```
import json
from openai import OpenAI

client = OpenAI(
  api_key="<SNOWFLAKE_PAT>",
  base_url="https://<account-identifier>.snowflakecomputing.com/api/v2/cortex/v1"
)

tools = [
  {
    "type": "function",
    "function": {
      "name": "get_weather",
      "description": "Get the current weather for a location",
      "parameters": {
        "type": "object",
        "properties": {
          "location": {
            "type": "string",
            "description": "The city and state, e.g. San Francisco, CA"
          }
        },
        "required": ["location"]
      }
    }
  }
]

messages = [
  {"role": "user", "content": "What is the weather like in San Francisco?"}
]

# Step 1: Send the request with tools
response = client.chat.completions.create(
  model="claude-sonnet-4-5",
  messages=messages,
  tools=tools,
)

# Step 2: The model responds with tool_calls
message = response.choices[0].message

if message.tool_calls:
    tool_call = message.tool_calls[0]

    # Step 3: Execute the tool (your implementation)
    result = json.dumps({"temperature": "69°F", "condition": "sunny"})

    # Step 4: Send the tool result back
    messages.append(message)
    messages.append({
      "role": "tool",
      "tool_call_id": tool_call.id,
      "content": result,
    })

    final_response = client.chat.completions.create(
      model="claude-sonnet-4-5",
      messages=messages,
      tools=tools,
    )

    print(final_response.choices[0].message.content)
```

Show lessSee more

Scroll to top

CopyExpand

```
import OpenAI from "openai";

const client = new OpenAI({
  apiKey: "<SNOWFLAKE_PAT>",
  baseURL: "https://<account-identifier>.snowflakecomputing.com/api/v2/cortex/v1"
});

const tools = [
  {
    type: "function",
    function: {
      name: "get_weather",
      description: "Get the current weather for a location",
      parameters: {
        type: "object",
        properties: {
          location: {
            type: "string",
            description: "The city and state, e.g. San Francisco, CA"
          }
        },
        required: ["location"]
      }
    }
  }
];

const messages = [
  { role: "user", content: "What is the weather like in San Francisco?" }
];

// Step 1: Send the request with tools
const response = await client.chat.completions.create({
  model: "claude-sonnet-4-5",
  messages,
  tools,
});

// Step 2: The model responds with tool_calls
const message = response.choices[0].message;

if (message.tool_calls) {
  const toolCall = message.tool_calls[0];

  // Step 3: Execute the tool (your implementation)
  const result = JSON.stringify({ temperature: "69°F", condition: "sunny" });

  // Step 4: Send the tool result back
  messages.push(message);
  messages.push({
    role: "tool",
    tool_call_id: toolCall.id,
    content: result,
  });

  const finalResponse = await client.chat.completions.create({
    model: "claude-sonnet-4-5",
    messages,
    tools,
  });

  console.log(finalResponse.choices[0].message.content);
}
```

Show lessSee more

Scroll to top

**Step 1 — Send the request with tools:**

CopyExpand

```
curl "https://<account-identifier>.snowflakecomputing.com/api/v2/cortex/v1/chat/completions" \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer <SNOWFLAKE_PAT>" \
  -d '{
    "model": "claude-sonnet-4-5",
    "messages": [
      {"role": "user", "content": "What is the weather like in San Francisco?"}
    ],
    "tools": [
      {
        "type": "function",
        "function": {
          "name": "get_weather",
          "description": "Get the current weather for a location",
          "parameters": {
            "type": "object",
            "properties": {
              "location": {
                "type": "string",
                "description": "The city and state, e.g. San Francisco, CA"
              }
            },
            "required": ["location"]
          }
        }
      }
    ]
  }'
```

Show lessSee more

Scroll to top

The model responds with a `tool_calls` array:

CopyExpand

```
{
  "choices": [
    {
      "message": {
        "role": "assistant",
        "tool_calls": [
          {
            "id": "call_abc123",
            "type": "function",
            "function": {
              "name": "get_weather",
              "arguments": "{\"location\": \"San Francisco, CA\"}"
            }
          }
        ]
      },
      "finish_reason": "tool_calls"
    }
  ]
}
```

Show lessSee more

Scroll to top

**Step 2 — Execute the tool and send the result back:**

CopyExpand

```
curl "https://<account-identifier>.snowflakecomputing.com/api/v2/cortex/v1/chat/completions" \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer <SNOWFLAKE_PAT>" \
  -d '{
    "model": "claude-sonnet-4-5",
    "messages": [
      {"role": "user", "content": "What is the weather like in San Francisco?"},
      {
        "role": "assistant",
        "tool_calls": [
          {
            "id": "call_abc123",
            "type": "function",
            "function": {
              "name": "get_weather",
              "arguments": "{\"location\": \"San Francisco, CA\"}"
            }
          }
        ]
      },
      {
        "role": "tool",
        "tool_call_id": "call_abc123",
        "content": "{\"temperature\": \"69°F\", \"condition\": \"sunny\"}"
      }
    ],
    "tools": [
      {
        "type": "function",
        "function": {
          "name": "get_weather",
          "description": "Get the current weather for a location",
          "parameters": {
            "type": "object",
            "properties": {
              "location": {
                "type": "string",
                "description": "The city and state, e.g. San Francisco, CA"
              }
            },
            "required": ["location"]
          }
        }
      }
    ]
  }'
```

Show lessSee more

Scroll to top

#### Messages API tool calling

PythonJavaScript/TypeScriptcurl

CopyExpand

```
import json
import httpx
import anthropic

PAT = "<SNOWFLAKE_PAT>"

http_client = httpx.Client(
  headers={"Authorization": f"Bearer {PAT}"},
)

client = anthropic.Anthropic(
  api_key="not-used",
  base_url="https://<account-identifier>.snowflakecomputing.com/api/v2/cortex",
  http_client=http_client,
  default_headers={"Authorization": f"Bearer {PAT}"},
)

tools = [
  {
    "name": "get_weather",
    "description": "Get the current weather for a location",
    "input_schema": {
      "type": "object",
      "properties": {
        "location": {
          "type": "string",
          "description": "The city and state, e.g. San Francisco, CA"
        }
      },
      "required": ["location"]
    }
  }
]

messages = [
  {"role": "user", "content": "What is the weather like in San Francisco?"}
]

# Step 1: Send the request with tools
response = client.messages.create(
  model="claude-sonnet-4-5",
  max_tokens=1024,
  messages=messages,
  tools=tools,
)

# Step 2: The model responds with a tool_use block
if response.stop_reason == "tool_use":
    tool_use = next(b for b in response.content if b.type == "tool_use")

    # Step 3: Execute the tool (your implementation)
    result = json.dumps({"temperature": "69°F", "condition": "sunny"})

    # Step 4: Send the tool result back
    messages.append({"role": "assistant", "content": response.content})
    messages.append({
      "role": "user",
      "content": [
        {
          "type": "tool_result",
          "tool_use_id": tool_use.id,
          "content": result,
        }
      ],
    })

    final_response = client.messages.create(
      model="claude-sonnet-4-5",
      max_tokens=1024,
      messages=messages,
      tools=tools,
    )

    print(final_response.content[0].text)
```

Show lessSee more

Scroll to top

CopyExpand

```
import Anthropic from "@anthropic-ai/sdk";

const PAT = "<SNOWFLAKE_PAT>";

const client = new Anthropic({
  apiKey: "not-used",
  baseURL: "https://<account-identifier>.snowflakecomputing.com/api/v2/cortex",
  defaultHeaders: {
    "Authorization": `Bearer ${PAT}`,
  },
});

const tools = [
  {
    name: "get_weather",
    description: "Get the current weather for a location",
    input_schema: {
      type: "object",
      properties: {
        location: {
          type: "string",
          description: "The city and state, e.g. San Francisco, CA"
        }
      },
      required: ["location"]
    }
  }
];

const messages = [
  { role: "user", content: "What is the weather like in San Francisco?" }
];

// Step 1: Send the request with tools
const response = await client.messages.create({
  model: "claude-sonnet-4-5",
  max_tokens: 1024,
  messages,
  tools,
});

// Step 2: The model responds with a tool_use block
if (response.stop_reason === "tool_use") {
  const toolUse = response.content.find(b => b.type === "tool_use");

  // Step 3: Execute the tool (your implementation)
  const result = JSON.stringify({ temperature: "69°F", condition: "sunny" });

  // Step 4: Send the tool result back
  messages.push({ role: "assistant", content: response.content });
  messages.push({
    role: "user",
    content: [
      {
        type: "tool_result",
        tool_use_id: toolUse.id,
        content: result,
      }
    ],
  });

  const finalResponse = await client.messages.create({
    model: "claude-sonnet-4-5",
    max_tokens: 1024,
    messages,
    tools,
  });

  console.log(finalResponse.content[0].text);
}
```

Show lessSee more

Scroll to top

**Step 1 — Send the request with tools:**

CopyExpand

```
curl "https://<account-identifier>.snowflakecomputing.com/api/v2/cortex/v1/messages" \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer <SNOWFLAKE_PAT>" \
  -H "anthropic-version: 2023-06-01" \
  -d '{
    "model": "claude-sonnet-4-5",
    "max_tokens": 1024,
    "messages": [
      {"role": "user", "content": "What is the weather like in San Francisco?"}
    ],
    "tools": [
      {
        "name": "get_weather",
        "description": "Get the current weather for a location",
        "input_schema": {
          "type": "object",
          "properties": {
            "location": {
              "type": "string",
              "description": "The city and state, e.g. San Francisco, CA"
            }
          },
          "required": ["location"]
        }
      }
    ]
  }'
```

Show lessSee more

Scroll to top

The model responds with a `tool_use` content block:

CopyExpand

```
{
  "role": "assistant",
  "content": [
    {
      "type": "text",
      "text": "I'll check the weather for you."
    },
    {
      "type": "tool_use",
      "id": "toolu_abc123",
      "name": "get_weather",
      "input": {"location": "San Francisco, CA"}
    }
  ],
  "stop_reason": "tool_use"
}
```

Show lessSee more

Scroll to top

**Step 2 — Execute the tool and send the result back:**

CopyExpand

```
curl "https://<account-identifier>.snowflakecomputing.com/api/v2/cortex/v1/messages" \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer <SNOWFLAKE_PAT>" \
  -H "anthropic-version: 2023-06-01" \
  -d '{
    "model": "claude-sonnet-4-5",
    "max_tokens": 1024,
    "messages": [
      {"role": "user", "content": "What is the weather like in San Francisco?"},
      {
        "role": "assistant",
        "content": [
          {"type": "text", "text": "I'\''ll check the weather for you."},
          {
            "type": "tool_use",
            "id": "toolu_abc123",
            "name": "get_weather",
            "input": {"location": "San Francisco, CA"}
          }
        ]
      },
      {
        "role": "user",
        "content": [
          {
            "type": "tool_result",
            "tool_use_id": "toolu_abc123",
            "content": "{\"temperature\": \"69°F\", \"condition\": \"sunny\"}"
          }
        ]
      }
    ],
    "tools": [
      {
        "name": "get_weather",
        "description": "Get the current weather for a location",
        "input_schema": {
          "type": "object",
          "properties": {
            "location": {
              "type": "string",
              "description": "The city and state, e.g. San Francisco, CA"
            }
          },
          "required": ["location"]
        }
      }
    ]
  }'
```

Show lessSee more

Scroll to top

### Structured output

You can request structured JSON output that conforms to a specific schema. This is supported for OpenAI and Claude
models through the Chat Completions API. For the Messages API, use the `tool_use` pattern to enforce structured output.

#### Chat Completions structured output

Use the `response_format` field with a JSON schema to constrain the model’s output.

PythonJavaScript/TypeScriptcurl

CopyExpand

```
import json
from openai import OpenAI

client = OpenAI(
  api_key="<SNOWFLAKE_PAT>",
  base_url="https://<account-identifier>.snowflakecomputing.com/api/v2/cortex/v1"
)

response = client.chat.completions.create(
  model="claude-sonnet-4-5",
  messages=[
    {"role": "user", "content": "Create a dataset of 3 people with their names and ages."}
  ],
  response_format={
    "type": "json_schema",
    "json_schema": {
      "name": "people_data",
      "schema": {
        "type": "object",
        "properties": {
          "people": {
            "type": "array",
            "items": {
              "type": "object",
              "properties": {
                "name": {"type": "string"},
                "age": {"type": "number"}
              },
              "required": ["name", "age"]
            }
          }
        },
        "required": ["people"]
      }
    }
  }
)

data = json.loads(response.choices[0].message.content)
print(data)
```

Show lessSee more

Scroll to top

CopyExpand

```
import OpenAI from "openai";

const client = new OpenAI({
  apiKey: "<SNOWFLAKE_PAT>",
  baseURL: "https://<account-identifier>.snowflakecomputing.com/api/v2/cortex/v1"
});

const response = await client.chat.completions.create({
  model: "claude-sonnet-4-5",
  messages: [
    { role: "user", content: "Create a dataset of 3 people with their names and ages." }
  ],
  response_format: {
    type: "json_schema",
    json_schema: {
      name: "people_data",
      schema: {
        type: "object",
        properties: {
          people: {
            type: "array",
            items: {
              type: "object",
              properties: {
                name: { type: "string" },
                age: { type: "number" }
              },
              required: ["name", "age"]
            }
          }
        },
        required: ["people"]
      }
    }
  }
});

const data = JSON.parse(response.choices[0].message.content);
console.log(data);
```

Show lessSee more

Scroll to top

CopyExpand

```
curl "https://<account-identifier>.snowflakecomputing.com/api/v2/cortex/v1/chat/completions" \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer <SNOWFLAKE_PAT>" \
  -d '{
    "model": "claude-sonnet-4-5",
    "messages": [
      {"role": "user", "content": "Create a dataset of 3 people with their names and ages."}
    ],
    "response_format": {
      "type": "json_schema",
      "json_schema": {
        "name": "people_data",
        "schema": {
          "type": "object",
          "properties": {
            "people": {
              "type": "array",
              "items": {
                "type": "object",
                "properties": {
                  "name": {"type": "string"},
                  "age": {"type": "number"}
                },
                "required": ["name", "age"]
              }
            }
          },
          "required": ["people"]
        }
      }
    }
  }'
```

Show lessSee more

Scroll to top

Note

Claude models support only `json_schema` as the response format type. OpenAI models support additional
response format types as documented in the [OpenAI API reference](https://platform.openai.com/docs/api-reference/chat/create).

#### Messages API structured output

The Messages API does not have a `response_format` field. Instead, define a tool with your desired output schema
and instruct the model to use it. The model’s `tool_use` response will contain structured JSON matching your schema.

PythonJavaScript/TypeScriptcurl

CopyExpand

```
import json
import httpx
import anthropic

PAT = "<SNOWFLAKE_PAT>"

http_client = httpx.Client(
  headers={"Authorization": f"Bearer {PAT}"},
)

client = anthropic.Anthropic(
  api_key="not-used",
  base_url="https://<account-identifier>.snowflakecomputing.com/api/v2/cortex",
  http_client=http_client,
  default_headers={"Authorization": f"Bearer {PAT}"},
)

response = client.messages.create(
  model="claude-sonnet-4-5",
  max_tokens=1024,
  messages=[
    {"role": "user", "content": "Create a dataset of 3 people with their names and ages."}
  ],
  tools=[
    {
      "name": "people_data",
      "description": "Output a list of people with names and ages.",
      "input_schema": {
        "type": "object",
        "properties": {
          "people": {
            "type": "array",
            "items": {
              "type": "object",
              "properties": {
                "name": {"type": "string"},
                "age": {"type": "number"}
              },
              "required": ["name", "age"]
            }
          }
        },
        "required": ["people"]
      }
    }
  ],
  tool_choice={"type": "tool", "name": "people_data"},
)

# Extract the structured data from the tool_use block
tool_use = next(b for b in response.content if b.type == "tool_use")
print(tool_use.input)
```

Show lessSee more

Scroll to top

CopyExpand

```
import Anthropic from "@anthropic-ai/sdk";

const PAT = "<SNOWFLAKE_PAT>";

const client = new Anthropic({
  apiKey: "not-used",
  baseURL: "https://<account-identifier>.snowflakecomputing.com/api/v2/cortex",
  defaultHeaders: {
    "Authorization": `Bearer ${PAT}`,
  },
});

const response = await client.messages.create({
  model: "claude-sonnet-4-5",
  max_tokens: 1024,
  messages: [
    { role: "user", content: "Create a dataset of 3 people with their names and ages." }
  ],
  tools: [
    {
      name: "people_data",
      description: "Output a list of people with names and ages.",
      input_schema: {
        type: "object",
        properties: {
          people: {
            type: "array",
            items: {
              type: "object",
              properties: {
                name: { type: "string" },
                age: { type: "number" }
              },
              required: ["name", "age"]
            }
          }
        },
        required: ["people"]
      }
    }
  ],
  tool_choice: { type: "tool", name: "people_data" },
});

// Extract the structured data from the tool_use block
const toolUse = response.content.find(b => b.type === "tool_use");
console.log(toolUse.input);
```

Show lessSee more

Scroll to top

CopyExpand

```
curl "https://<account-identifier>.snowflakecomputing.com/api/v2/cortex/v1/messages" \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer <SNOWFLAKE_PAT>" \
  -H "anthropic-version: 2023-06-01" \
  -d '{
    "model": "claude-sonnet-4-5",
    "max_tokens": 1024,
    "messages": [
      {"role": "user", "content": "Create a dataset of 3 people with their names and ages."}
    ],
    "tools": [
      {
        "name": "people_data",
        "description": "Output a list of people with names and ages.",
        "input_schema": {
          "type": "object",
          "properties": {
            "people": {
              "type": "array",
              "items": {
                "type": "object",
                "properties": {
                  "name": {"type": "string"},
                  "age": {"type": "number"}
                },
                "required": ["name", "age"]
              }
            }
          },
          "required": ["people"]
        }
      }
    ],
    "tool_choice": {"type": "tool", "name": "people_data"}
  }'
```

Show lessSee more

Scroll to top

### Image input

You can include images in your requests for models that support vision. Images must be provided as base64-encoded strings.
Images are limited to 20 per conversation with a 20 MiB max request size.

Image input is supported for:

* Claude models (`claude-3-7-sonnet` and newer)
* OpenAI models (`openai-gpt-4.1`, `openai-gpt-5`, `openai-gpt-5-chat`, `openai-gpt-5-mini`, `openai-gpt-5-nano`)

#### Chat Completions image input

PythonJavaScript/TypeScriptcurl

CopyExpand

```
import base64
from openai import OpenAI

client = OpenAI(
  api_key="<SNOWFLAKE_PAT>",
  base_url="https://<account-identifier>.snowflakecomputing.com/api/v2/cortex/v1"
)

# Read and encode an image file
with open("image.png", "rb") as f:
    image_data = base64.b64encode(f.read()).decode("utf-8")

response = client.chat.completions.create(
  model="claude-sonnet-4-5",
  messages=[
    {
      "role": "user",
      "content": [
        {
          "type": "image_url",
          "image_url": {
            "url": f"data:image/png;base64,{image_data}"
          }
        },
        {
          "type": "text",
          "text": "What is in this image?"
        }
      ]
    }
  ]
)

print(response.choices[0].message.content)
```

Show lessSee more

Scroll to top

CopyExpand

```
import OpenAI from "openai";
import fs from "fs";

const client = new OpenAI({
  apiKey: "<SNOWFLAKE_PAT>",
  baseURL: "https://<account-identifier>.snowflakecomputing.com/api/v2/cortex/v1"
});

// Read and encode an image file
const imageData = fs.readFileSync("image.png").toString("base64");

const response = await client.chat.completions.create({
  model: "claude-sonnet-4-5",
  messages: [
    {
      role: "user",
      content: [
        {
          type: "image_url",
          image_url: {
            url: `data:image/png;base64,${imageData}`
          }
        },
        {
          type: "text",
          text: "What is in this image?"
        }
      ]
    }
  ],
});

console.log(response.choices[0].message.content);
```

Show lessSee more

Scroll to top

CopyExpand

```
curl "https://<account-identifier>.snowflakecomputing.com/api/v2/cortex/v1/chat/completions" \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer <SNOWFLAKE_PAT>" \
  -d '{
    "model": "claude-sonnet-4-5",
    "messages": [
      {
        "role": "user",
        "content": [
          {
            "type": "image_url",
            "image_url": {
              "url": "data:image/png;base64,<BASE64_IMAGE_DATA>"
            }
          },
          {
            "type": "text",
            "text": "What is in this image?"
          }
        ]
      }
    ]
  }'
```

Show lessSee more

Scroll to top

#### Messages API image input

The Messages API uses a different image format — a `source` block with `type`, `media_type`, and `data` fields
instead of a data URL.

PythonJavaScript/TypeScriptcurl

CopyExpand

```
import base64
import httpx
import anthropic

PAT = "<SNOWFLAKE_PAT>"

http_client = httpx.Client(
  headers={"Authorization": f"Bearer {PAT}"},
)

client = anthropic.Anthropic(
  api_key="not-used",
  base_url="https://<account-identifier>.snowflakecomputing.com/api/v2/cortex",
  http_client=http_client,
  default_headers={"Authorization": f"Bearer {PAT}"},
)

# Read and encode an image file
with open("image.png", "rb") as f:
    image_data = base64.b64encode(f.read()).decode("utf-8")

response = client.messages.create(
  model="claude-sonnet-4-5",
  max_tokens=1024,
  messages=[
    {
      "role": "user",
      "content": [
        {
          "type": "image",
          "source": {
            "type": "base64",
            "media_type": "image/png",
            "data": image_data
          }
        },
        {
          "type": "text",
          "text": "What is in this image?"
        }
      ]
    }
  ],
)

print(response.content[0].text)
```

Show lessSee more

Scroll to top

CopyExpand

```
import Anthropic from "@anthropic-ai/sdk";
import fs from "fs";

const PAT = "<SNOWFLAKE_PAT>";

const client = new Anthropic({
  apiKey: "not-used",
  baseURL: "https://<account-identifier>.snowflakecomputing.com/api/v2/cortex",
  defaultHeaders: {
    "Authorization": `Bearer ${PAT}`,
  },
});

// Read and encode an image file
const imageData = fs.readFileSync("image.png").toString("base64");

const response = await client.messages.create({
  model: "claude-sonnet-4-5",
  max_tokens: 1024,
  messages: [
    {
      role: "user",
      content: [
        {
          type: "image",
          source: {
            type: "base64",
            media_type: "image/png",
            data: imageData
          }
        },
        {
          type: "text",
          text: "What is in this image?"
        }
      ]
    }
  ],
});

console.log(response.content[0].text);
```

Show lessSee more

Scroll to top

CopyExpand

```
curl "https://<account-identifier>.snowflakecomputing.com/api/v2/cortex/v1/messages" \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer <SNOWFLAKE_PAT>" \
  -H "anthropic-version: 2023-06-01" \
  -d '{
    "model": "claude-sonnet-4-5",
    "max_tokens": 1024,
    "messages": [
      {
        "role": "user",
        "content": [
          {
            "type": "image",
            "source": {
              "type": "base64",
              "media_type": "image/png",
              "data": "<BASE64_IMAGE_DATA>"
            }
          },
          {
            "type": "text",
            "text": "What is in this image?"
          }
        ]
      }
    ]
  }'
```

Show lessSee more

Scroll to top

### Prompt caching

Prompt caching lets you reuse previously processed context (such as large system prompts, documents, or
conversation history) across requests, reducing latency and cost.

* **OpenAI models**: Caching is **implicit**. Prompts with 1,024+ tokens are automatically cached — no request changes needed.
* **Claude models**: Caching is **explicit**. Add `cache_control` breakpoints to content blocks you want cached. Only the `ephemeral` cache type is supported, with a **5-minute TTL**. A maximum of 4 cache breakpoints per request.

#### Chat Completions prompt caching

For Claude models via Chat Completions, add `cache_control` to content blocks. OpenAI models are cached
automatically and do not require this field.

PythonJavaScript/TypeScriptcurl

CopyExpand

```
from openai import OpenAI

client = OpenAI(
  api_key="<SNOWFLAKE_PAT>",
  base_url="https://<account-identifier>.snowflakecomputing.com/api/v2/cortex/v1"
)

response = client.chat.completions.create(
  model="claude-sonnet-4-5",
  messages=[
    {
      "role": "system",
      "content": [
        {
          "type": "text",
          "text": "<long system prompt to cache>",
          "cache_control": {"type": "ephemeral"}
        }
      ]
    },
    {"role": "user", "content": "Summarize the key points."}
  ]
)

print(response.choices[0].message.content)
```

Show lessSee more

Scroll to top

CopyExpand

```
import OpenAI from "openai";

const client = new OpenAI({
  apiKey: "<SNOWFLAKE_PAT>",
  baseURL: "https://<account-identifier>.snowflakecomputing.com/api/v2/cortex/v1"
});

const response = await client.chat.completions.create({
  model: "claude-sonnet-4-5",
  messages: [
    {
      role: "system",
      content: [
        {
          type: "text",
          text: "<long system prompt to cache>",
          cache_control: { type: "ephemeral" }
        }
      ]
    },
    { role: "user", content: "Summarize the key points." }
  ],
});

console.log(response.choices[0].message.content);
```

Show lessSee more

Scroll to top

CopyExpand

```
curl "https://<account-identifier>.snowflakecomputing.com/api/v2/cortex/v1/chat/completions" \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer <SNOWFLAKE_PAT>" \
  -d '{
    "model": "claude-sonnet-4-5",
    "messages": [
      {
        "role": "system",
        "content": [
          {
            "type": "text",
            "text": "<long system prompt to cache>",
            "cache_control": {"type": "ephemeral"}
          }
        ]
      },
      {"role": "user", "content": "Summarize the key points."}
    ]
  }'
```

Show lessSee more

Scroll to top

#### Messages API prompt caching

Use `cache_control` on system or user content blocks. Only the `ephemeral` cache type is supported,
with a 5-minute TTL. A maximum of 4 cache breakpoints can be set per request.

PythonJavaScript/TypeScriptcurl

CopyExpand

```
import httpx
import anthropic

PAT = "<SNOWFLAKE_PAT>"

http_client = httpx.Client(
  headers={"Authorization": f"Bearer {PAT}"},
)

client = anthropic.Anthropic(
  api_key="not-used",
  base_url="https://<account-identifier>.snowflakecomputing.com/api/v2/cortex",
  http_client=http_client,
  default_headers={"Authorization": f"Bearer {PAT}"},
)

response = client.messages.create(
  model="claude-sonnet-4-5",
  max_tokens=1024,
  system=[
    {
      "type": "text",
      "text": "<long system prompt to cache>",
      "cache_control": {"type": "ephemeral"}
    }
  ],
  messages=[
    {"role": "user", "content": "Summarize the key points."}
  ],
)

print(response.content[0].text)
```

Show lessSee more

Scroll to top

CopyExpand

```
import Anthropic from "@anthropic-ai/sdk";

const PAT = "<SNOWFLAKE_PAT>";

const client = new Anthropic({
  apiKey: "not-used",
  baseURL: "https://<account-identifier>.snowflakecomputing.com/api/v2/cortex",
  defaultHeaders: {
    "Authorization": `Bearer ${PAT}`,
  },
});

const response = await client.messages.create({
  model: "claude-sonnet-4-5",
  max_tokens: 1024,
  system: [
    {
      type: "text",
      text: "<long system prompt to cache>",
      cache_control: { type: "ephemeral" }
    }
  ],
  messages: [
    { role: "user", content: "Summarize the key points." }
  ],
});

console.log(response.content[0].text);
```

Show lessSee more

Scroll to top

CopyExpand

```
curl "https://<account-identifier>.snowflakecomputing.com/api/v2/cortex/v1/messages" \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer <SNOWFLAKE_PAT>" \
  -H "anthropic-version: 2023-06-01" \
  -d '{
    "model": "claude-sonnet-4-5",
    "max_tokens": 1024,
    "system": [
      {
        "type": "text",
        "text": "<long system prompt to cache>",
        "cache_control": {"type": "ephemeral"}
      }
    ],
    "messages": [
      {"role": "user", "content": "Summarize the key points."}
    ]
  }'
```

Show lessSee more

Scroll to top

Note

Anthropic prompt caching has a **5-minute TTL**. Cached content not accessed within 5 minutes is evicted.
OpenAI prompt caching is implicit and managed automatically — no `cache_control` fields needed.

### Thinking and reasoning

#### Chat Completions thinking

For Claude models, use the `reasoning` object. For OpenAI reasoning models, use the `reasoning_effort` field
(values: `minimal`, `low`, `medium`, `high`).

PythonJavaScript/TypeScriptcurl

CopyExpand

```
from openai import OpenAI

client = OpenAI(
  api_key="<SNOWFLAKE_PAT>",
  base_url="https://<account-identifier>.snowflakecomputing.com/api/v2/cortex/v1"
)

# Claude models — use the reasoning object
response = client.chat.completions.create(
  model="claude-sonnet-4-5",
  messages=[
    {"role": "user", "content": "Are there an infinite number of prime numbers such that n mod 4 == 3?"}
  ],
  extra_body={
    "reasoning": {"effort": "high"}
  }
)

print(response.choices[0].message.content)
```

Show lessSee more

Scroll to top

CopyExpand

```
import OpenAI from "openai";

const client = new OpenAI({
  apiKey: "<SNOWFLAKE_PAT>",
  baseURL: "https://<account-identifier>.snowflakecomputing.com/api/v2/cortex/v1"
});

// Claude models — use the reasoning object
const response = await client.chat.completions.create({
  model: "claude-sonnet-4-5",
  messages: [
    { role: "user", content: "Are there an infinite number of prime numbers such that n mod 4 == 3?" }
  ],
  reasoning: { effort: "high" },
});

console.log(response.choices[0].message.content);
```

Show lessSee more

Scroll to top

CopyExpand

```
# Claude models — use the reasoning object
curl "https://<account-identifier>.snowflakecomputing.com/api/v2/cortex/v1/chat/completions" \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer <SNOWFLAKE_PAT>" \
  -d '{
    "model": "claude-sonnet-4-5",
    "messages": [
      {"role": "user", "content": "Are there an infinite number of prime numbers such that n mod 4 == 3?"}
    ],
    "reasoning": {
      "effort": "high"
    }
  }'
```

Show lessSee more

Scroll to top

CopyExpand

```
# OpenAI reasoning models — use reasoning_effort
curl "https://<account-identifier>.snowflakecomputing.com/api/v2/cortex/v1/chat/completions" \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer <SNOWFLAKE_PAT>" \
  -d '{
    "model": "openai-gpt-5",
    "messages": [
      {"role": "user", "content": "Are there an infinite number of prime numbers such that n mod 4 == 3?"}
    ],
    "reasoning_effort": "high"
  }'
```

Show lessSee more

Scroll to top

#### Messages API thinking

Some Claude models support **adaptive thinking**, where the model adjusts how much reasoning it applies based on task
complexity. The following models support adaptive thinking:

* `claude-opus-4-6`

For the Messages API, use the `thinking` parameter with `type: "adaptive"` to enable adaptive thinking. The `output_config.effort` parameter provides some high-level control over the thinking depth, and accepts the following values:

| Effort level | Behavior |
| --- | --- |
| `max` | Always thinks with no constraints on thinking depth. Claude Opus 4.6 only. |
| `high` (default) | Always thinks. Provides deep reasoning on complex tasks. |
| `medium` | Moderate thinking. May skip thinking for very simple queries. |
| `low` | Minimizes thinking. Skips thinking for simple tasks where speed matters most. |

See moreShow less

Expand

The following examples demonstrate how to make a Messages API call with adaptive thinking enabled:

PythonJavaScript/TypeScriptcurl

CopyExpand

```
import httpx
import anthropic

PAT = "<SNOWFLAKE_PAT>"

http_client = httpx.Client(
  headers={"Authorization": f"Bearer {PAT}"},
)

client = anthropic.Anthropic(
  api_key="not-used",
  base_url="https://<account-identifier>.snowflakecomputing.com/api/v2/cortex",
  http_client=http_client,
  default_headers={"Authorization": f"Bearer {PAT}"},
)

response = client.messages.create(
  model="claude-opus-4-6",
  max_tokens=16384,
  thinking={
    "type": "adaptive"
  },
  messages=[
    {"role": "user", "content": "Are there an infinite number of prime numbers such that n mod 4 == 3?"}
  ],
)

# The response includes thinking blocks followed by text
for block in response.content:
    if block.type == "thinking":
        print(f"Thinking: {block.thinking[:100]}...")
    elif block.type == "text":
        print(f"Answer: {block.text}")
```

Show lessSee more

Scroll to top

CopyExpand

```
import Anthropic from "@anthropic-ai/sdk";

const PAT = "<SNOWFLAKE_PAT>";

const client = new Anthropic({
  apiKey: "not-used",
  baseURL: "https://<account-identifier>.snowflakecomputing.com/api/v2/cortex",
  defaultHeaders: {
    "Authorization": `Bearer ${PAT}`,
  },
});

const response = await client.messages.create({
  model: "claude-opus-4-6",
  max_tokens: 16384,
  thinking: {
    type: "adaptive"
  },
  messages: [
    { role: "user", content: "Are there an infinite number of prime numbers such that n mod 4 == 3?" }
  ],
});

// The response includes thinking blocks followed by text
for (const block of response.content) {
  if (block.type === "thinking") {
    console.log(`Thinking: ${block.thinking.slice(0, 100)}...`);
  } else if (block.type === "text") {
    console.log(`Answer: ${block.text}`);
  }
}
```

Show lessSee more

Scroll to top

CopyExpand

```
curl "https://<account-identifier>.snowflakecomputing.com/api/v2/cortex/v1/messages" \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer <SNOWFLAKE_PAT>" \
  -H "anthropic-version: 2023-06-01" \
  -d '{
    "model": "claude-opus-4-6",
    "max_tokens": 16384,
    "thinking": {
      "type": "adaptive"
    },
    "messages": [
      {"role": "user", "content": "Are there an infinite number of prime numbers such that n mod 4 == 3?"}
    ]
  }'
```

Show lessSee more

Scroll to top

The response includes thinking blocks with summarized thinking and thinking signatures.
Pass these blocks back in multi-turn conversations to maintain reasoning context:

CopyExpand

```
{
  "role": "assistant",
  "content": [
    {"type": "thinking", "thinking": "<thinking>", "signature": "<signature>"},
    {"type": "text", "text": "Yes, there are infinitely many primes p where p ≡ 3 (mod 4)..."}
  ]
}
```

Show lessSee more

Scroll to top

For a full description of the Messages API support for Adaptive Thinking, see [Claude API Docs – Adaptive thinking](https://platform.claude.com/docs/en/build-with-claude/adaptive-thinking).

### Beta features (Messages API)

[![Snowflake logo in black (no text)](../../_images/logo-snowflake-black.png)](../../_images/logo-snowflake-black.png) [Preview Feature](../../release-notes/preview-features) — Open

Available to all accounts.

The Messages API supports Anthropic beta features via the `anthropic-beta` header. Pass one or more beta header
values as a comma-separated string.

Supported beta headers

| Beta header value | Feature |
| --- | --- |
| `token-efficient-tools-2025-02-19` | Token-efficient tools |
| `interleaved-thinking-2025-05-14` | Interleaved thinking |
| `output-128k-2025-02-19` | Enables output tokens up to 128K |
| `dev-full-thinking-2025-05-14` | Developer mode for raw thinking on Claude 4+ models |
| `context-1m-2025-08-07` | 1 million token context window |
| `context-management-2025-06-27` | Context management |
| `effort-2025-11-24` | Effort parameter for thinking |
| `tool-search-tool-2025-10-19` | Tool search tool |
| `tool-examples-2025-10-29` | Tool use examples |

See moreShow less

Expand

The following example enables the 1 million token context window with `claude-sonnet-4-6`:

PythonJavaScript/TypeScriptcurl

CopyExpand

```
import httpx
import anthropic

PAT = "<SNOWFLAKE_PAT>"

http_client = httpx.Client(
  headers={"Authorization": f"Bearer {PAT}"},
)

client = anthropic.Anthropic(
  api_key="not-used",
  base_url="https://<account-identifier>.snowflakecomputing.com/api/v2/cortex",
  http_client=http_client,
  default_headers={
    "Authorization": f"Bearer {PAT}",
    "anthropic-beta": "context-1m-2025-08-07",
  },
)

response = client.messages.create(
  model="claude-sonnet-4-6",
  max_tokens=8192,
  messages=[
    {"role": "user", "content": "<very long document text>... Summarize the key themes."}
  ],
)

print(response.content[0].text)
```

Show lessSee more

Scroll to top

CopyExpand

```
import Anthropic from "@anthropic-ai/sdk";

const PAT = "<SNOWFLAKE_PAT>";

const client = new Anthropic({
  apiKey: "not-used",
  baseURL: "https://<account-identifier>.snowflakecomputing.com/api/v2/cortex",
  defaultHeaders: {
    "Authorization": `Bearer ${PAT}`,
    "anthropic-beta": "context-1m-2025-08-07",
  },
});

const response = await client.messages.create({
  model: "claude-sonnet-4-6",
  max_tokens: 8192,
  messages: [
    { role: "user", content: "<very long document text>... Summarize the key themes." }
  ],
});

console.log(response.content[0].text);
```

Show lessSee more

Scroll to top

CopyExpand

```
curl "https://<account-identifier>.snowflakecomputing.com/api/v2/cortex/v1/messages" \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer <SNOWFLAKE_PAT>" \
  -H "anthropic-version: 2023-06-01" \
  -H "anthropic-beta: context-1m-2025-08-07" \
  -d '{
    "model": "claude-sonnet-4-6",
    "max_tokens": 8192,
    "messages": [
      {"role": "user", "content": "<very long document text>... Summarize the key themes."}
    ]
  }'
```

Show lessSee more

Scroll to top

You can combine multiple beta features by passing a comma-separated string:

CopyExpand

```
-H "anthropic-beta: context-1m-2025-08-07,interleaved-thinking-2025-05-14"
```

Show lessSee more

Scroll to top

## Chat Completions API reference

### POST /api/v2/cortex/v1/chat/completions

Generates a chat completion using the specified model. The request and response format follows the
[OpenAI Chat Completions API specification](https://platform.openai.com/docs/api-reference/chat/create).

Expand

```
POST https://<account_identifier>.snowflakecomputing.com/api/v2/cortex/v1/chat/completions
```

Show lessSee more

Scroll to top

#### Required headers

`Authorization: Bearer token`
:   Authorization for the request. `token` is a JSON web token (JWT), OAuth token, or
    [programmatic access token](../programmatic-access-tokens). For details, see
    [Authenticating Snowflake REST APIs with Snowflake](../../developer-guide/snowflake-rest-api/authentication).

`Content-Type: application/json`
:   Specifies that the body of the request is in JSON format.

#### Optional headers

`X-Snowflake-Authorization-Token-Type: type`
:   Defines the type of authorization token.

    If you omit the `X-Snowflake-Authorization-Token-Type` header, Snowflake determines the token type by examining the token.

    Even though this header is optional, you can choose to specify this header. You can set the header to one of the following values:

    * `KEYPAIR_JWT` (for key-pair authentication)
    * `OAUTH` (for OAuth)
    * `PROGRAMMATIC_ACCESS_TOKEN` (for [programmatic access tokens](../programmatic-access-tokens))

`Accept: application/json, text/event-stream`
:   Specifies that the response will either contain JSON (error case) or server-sent events.

#### Required JSON fields

| Field | Type | Description |
| --- | --- | --- |
| `model` | string | The model to use (see [Model availability](#label-cortex-complete-llm-model-availability)). You may also use the fully-qualified name of any [fine-tuned](cortex-finetuning) model in the format `database.schema.model`. |
| `messages` | array | An array of message objects representing the conversation. Each message must have a `role` (`system`, `user`, `assistant`, or `tool`) and `content` (string or array of content parts). |

See moreShow less

Expand

#### Commonly used optional JSON fields

| Field | Type | Default | Description |
| --- | --- | --- | --- |
| `max_completion_tokens` | integer | 4096 | Maximum tokens in the response. Theoretical maximum is 131,072; each model has its own output limit. |
| `temperature` | number | Varies by model | Controls randomness. Values from 0 to 2. |
| `top_p` | number | 1.0 | Controls diversity via nucleus sampling. |
| `stream` | boolean | false | Whether to stream back partial progress as server-sent events. |
| `tools` | array | null | A list of tools the model may call. Each tool must have `type: "function"` and a `function` object with `name`, `description`, and `parameters`. |
| `tool_choice` | string or object | `"auto"` | Controls how the model selects tools. Options: `"auto"`, `"required"`, `"none"`, or an object specifying a particular function. |
| `response_format` | object | null | Constrains the output format. Use `{"type": "json_schema", "json_schema": {...}}` for structured output. |
| `reasoning_effort` | string | null | For OpenAI reasoning models. Values: `"minimal"`, `"low"`, `"medium"`, `"high"`. |
| `reasoning` | object | null | For Claude models. Set `reasoning.effort` or `reasoning.max_tokens` to enable thinking. |

See moreShow less

Expand

See the [detailed compatibility chart](#label-cortex-openai-sdk-compatibility) for the full list of supported
fields per model family.

#### Status codes

200 `OK`
:   Request completed successfully.

400 `invalid options object`
:   The optional arguments have invalid values.

400 `unknown model model_name`
:   The specified model does not exist.

400 `schema validation failed`
:   The response schema structure is incorrect.

400 `max tokens of count exceeded`
:   The request exceeded the maximum number of tokens supported by the model.

400 `all requests were throttled by remote service`
:   The request has been throttled. Try again later.

402 `budget exceeded`
:   The model consumption budget was exceeded.

403 `Not Authorized`
:   Account not enabled for REST API, or the default role for the calling user does not have the
    `snowflake.cortex_user` database role.

429 `too many requests`
:   The usage quota has been exceeded. Try again later.

503 `inference timed out`
:   The request took too long.

#### Limitations

* If unset, `max_completion_tokens` defaults to 4096. Each model has its own output token limit.
* Tool calling is supported for OpenAI and Claude models only.
* Audio is not supported.
* Image understanding is supported for OpenAI and Claude models only. Images are limited to 20 per conversation
  with a 20 MiB max request size.
* Only Claude models support ephemeral cache control points for prompt caching. OpenAI models support implicit caching.
* Only Claude models support returning reasoning details in the response.
* `max_tokens` is deprecated. Use `max_completion_tokens` instead.
* Error messages are generated by Snowflake, not by the model provider.

#### Detailed compatibility chart

The following tables summarize which request and response fields are supported when using the Chat Completions API
with different Snowflake-hosted model families.

Request fields

| Field | OpenAI Models | Claude Models | Other Models |
| --- | --- | --- | --- |
| `model` | ✔ Supported | ✔ Supported | ✔ Supported |
| `messages` | See sub-fields | See sub-fields | See sub-fields |
| `messages[].audio` | ❌ Error | ❌ Ignored | ❌ Ignored |
| `messages[].role` | ✔ Supported | ✔ Only user/assistant/system | ✔ Only user/assistant/system |
| `messages[].content` (string) | ✔ Supported | ✔ Supported | ✔ Supported |
| `messages[].content[]` (array) | See sub-fields | See sub-fields | See sub-fields |
| `messages[].content[].text` | ✔ Supported | ✔ Supported | ✔ Supported |
| `messages[].content[].type` | ✔ Supported | ✔ Supported | ✔ Supported |
| `messages[].content[].image_url` | ✔ Supported | ✔ Supported | ❌ Error |
| `messages[].content[].cache_control` | ❌ Ignored | ✔ Supported (ephemeral only) | ❌ Ignored |
| `messages[].content[].file` | ❌ Error | ❌ Error | ❌ Ignored |
| `messages[].content[].input_audio` | ❌ Error | ❌ Ignored | ❌ Ignored |
| `messages[].content[].refusal` | ✔ Supported | ❌ Ignored | ❌ Ignored |
| `messages[].function_call` | ✔ Supported (deprecated) | ❌ Ignored | ❌ Ignored |
| `messages[].name` | ✔ Supported | ❌ Ignored | ❌ Ignored |
| `messages[].refusal` | ✔ Supported | ❌ Ignored | ❌ Ignored |
| `messages[].tool_call_id` | ✔ Supported | ✔ Supported | ❌ Ignored |
| `messages[].tool_calls` | ✔ Supported | ✔ Only `function` tools | ❌ Ignored |
| `messages[].reasoning_details` | ❌ Ignored | ✔ OpenRouter format `reasoning.text` | ❌ Ignored |
| `audio` | ❌ Error | ❌ Ignored | ❌ Ignored |
| `frequency_penalty` | ✔ Supported | ❌ Ignored | ❌ Ignored |
| `logit_bias` | ✔ Supported | ❌ Ignored | ❌ Ignored |
| `logprobs` | ✔ Supported | ❌ Ignored | ❌ Ignored |
| `max_tokens` | ❌ Error (deprecated) | ❌ Error (deprecated) | ❌ Error (deprecated) |
| `max_completion_tokens` | ✔ Supported (4096 default, 131072 max) | ✔ Supported (4096 default, 131072 max) | ✔ Supported (4096 default, 131072 max) |
| `metadata` | ❌ Ignored | ❌ Ignored | ❌ Ignored |
| `modalities` | ❌ Ignored | ❌ Ignored | ❌ Ignored |
| `n` | ✔ Supported | ❌ Ignored | ❌ Ignored |
| `parallel_tool_calls` | ✔ Supported | ❌ Ignored | ❌ Ignored |
| `prediction` | ✔ Supported | ❌ Ignored | ❌ Ignored |
| `presence_penalty` | ✔ Supported | ❌ Ignored | ❌ Ignored |
| `prompt_cache_key` | ✔ Supported | ❌ Ignored | ❌ Ignored |
| `reasoning_effort` | ✔ Supported | ❌ Ignored (use `reasoning` object) | ❌ Ignored |
| `reasoning` | See sub-fields | See sub-fields | See sub-fields |
| `reasoning.effort` | ✔ Supported (overrides `reasoning_effort`) | ✔ Converted to `reasoning.max_tokens` | ❌ Ignored |
| `reasoning.max_tokens` | ❌ Ignored | ✔ Supported | ❌ Ignored |
| `response_format` | ✔ Supported | ✔ Only `json_schema` | ❌ Ignored |
| `safety_identifier` | ❌ Ignored | ❌ Ignored | ❌ Ignored |
| `service_tier` | ❌ Error | ❌ Error | ❌ Error |
| `stop` | ✔ Supported | ❌ Ignored | ❌ Ignored |
| `store` | ❌ Error | ❌ Error | ❌ Error |
| `stream` | ✔ Supported | ✔ Supported | ✔ Supported |
| `stream_options` | See sub-fields | See sub-fields | See sub-fields |
| `stream_options.include_obfuscation` | ❌ Ignored | ❌ Ignored | ❌ Ignored |
| `stream_options.include_usage` | ✔ Supported | ✔ Supported | ✔ Supported |
| `temperature` | ✔ Supported | ✔ Supported | ✔ Supported |
| `tool_choice` | ✔ Supported | ✔ Only `function` tools | ❌ Ignored |
| `tools` | ✔ Supported | ✔ Only `function` tools | ❌ Error |
| `top_logprobs` | ✔ Supported | ❌ Ignored | ❌ Ignored |
| `top_p` | ✔ Supported | ✔ Supported | ✔ Supported |
| `verbosity` | ✔ Supported | ❌ Ignored | ❌ Ignored |
| `web_search_options` | ❌ Error | ❌ Ignored | ❌ Ignored |

See moreShow less

Expand

Response fields

| Field | OpenAI Models | Claude Models | Other Models |
| --- | --- | --- | --- |
| `id` | ✔ Supported | ✔ Supported | ✔ Supported |
| `object` | ✔ Supported | ✔ Supported | ✔ Supported |
| `created` | ✔ Supported | ✔ Supported | ✔ Supported |
| `model` | ✔ Supported | ✔ Supported | ✔ Supported |
| `choices` | See sub-fields | See sub-fields | See sub-fields |
| `choices[].index` | ✔ Supported | ✔ Single choice only | ✔ Single choice only |
| `choices[].finish_reason` | ✔ Supported | ❌ Not supported | ✔ Only `stop` |
| `choices[].logprobs` | ✔ Supported | ❌ Not supported | ❌ Not supported |
| `choices[].message` (non-streaming) | See sub-fields | See sub-fields | See sub-fields |
| `choices[].message.content` | ✔ Supported | ✔ Supported | ✔ Supported |
| `choices[].message.role` | ✔ Supported | ✔ Supported | ✔ Supported |
| `choices[].message.refusal` | ✔ Supported | ❌ Not supported | ❌ Not supported |
| `choices[].message.annotations` | ❌ Not supported | ❌ Not supported | ❌ Not supported |
| `choices[].message.audio` | ❌ Not supported | ❌ Not supported | ❌ Not supported |
| `choices[].message.function_call` | ✔ Supported | ❌ Not supported | ❌ Not supported |
| `choices[].message.tool_calls` | ✔ Supported | ✔ Only `function` tools | ❌ Not supported |
| `choices[].message.reasoning` | ❌ Not supported | ✔ OpenRouter format | ❌ Not supported |
| `choices[].delta` (streaming) | See sub-fields | See sub-fields | See sub-fields |
| `choices[].delta.content` | ✔ Supported | ✔ Supported | ✔ Supported |
| `choices[].delta.role` | ✔ Supported | ❌ Not supported | ❌ Not supported |
| `choices[].delta.refusal` | ✔ Supported | ❌ Not supported | ❌ Not supported |
| `choices[].delta.function_call` | ✔ Supported | ❌ Not supported | ❌ Not supported |
| `choices[].delta.tool_calls` | ✔ Supported | ✔ Only `function` tools | ❌ Not supported |
| `choices[].delta.reasoning` | ❌ Not supported | ✔ OpenRouter format | ❌ Not supported |
| `usage` | See sub-fields | See sub-fields | See sub-fields |
| `usage.prompt_tokens` | ✔ Supported | ✔ Supported | ✔ Supported |
| `usage.completion_tokens` | ✔ Supported | ✔ Supported | ✔ Supported |
| `usage.total_tokens` | ✔ Supported | ✔ Supported | ✔ Supported |
| `usage.prompt_tokens_details` | See sub-fields | See sub-fields | See sub-fields |
| `usage.prompt_tokens_details.audio_tokens` | ❌ Not supported | ❌ Not supported | ❌ Not supported |
| `usage.prompt_tokens_details.cached_tokens` | ✔ Only cache reads | ✔ Cache read + write | ❌ Not supported |
| `usage.completion_tokens_details` | See sub-fields | See sub-fields | See sub-fields |
| `usage.completion_tokens_details.accepted_prediction_tokens` | ✔ Supported | ❌ Not supported | ❌ Not supported |
| `usage.completion_tokens_details.audio_tokens` | ❌ Not supported | ❌ Not supported | ❌ Not supported |
| `usage.completion_tokens_details.reasoning_tokens` | ✔ Supported | ❌ Not supported | ❌ Not supported |
| `usage.completion_tokens_details.rejected_prediction_tokens` | ✔ Supported | ❌ Not supported | ❌ Not supported |
| `service_tier` | ✔ Supported | ❌ Not supported | ❌ Not supported |
| `system_fingerprint` | ✔ Supported | ❌ Not supported | ❌ Not supported |

See moreShow less

Expand

Request headers

| Header | Support |
| --- | --- |
| `Authorization` | ✔ Required |
| `Content-Type` | ✔ Supported (`application/json`) |
| `Accept` | ✔ Supported (`application/json`, `text/event-stream`) |

See moreShow less

Expand

Response headers

| Header | Support |
| --- | --- |
| `openai-organization` | ❌ Not supported |
| `openai-version` | ❌ Not supported |
| `openai-processing-ms` | ❌ Not supported |
| `x-ratelimit-limit-requests` | ❌ Not supported |
| `x-ratelimit-limit-tokens` | ❌ Not supported |
| `x-ratelimit-remaining-requests` | ❌ Not supported |
| `x-ratelimit-remaining-tokens` | ❌ Not supported |
| `x-ratelimit-reset-requests` | ❌ Not supported |
| `x-ratelimit-reset-tokens` | ❌ Not supported |
| `retry-after` | ❌ Not supported |

See moreShow less

Expand

#### Learn more

For additional usage examples, see the [OpenAI Chat Completions API reference](https://platform.openai.com/docs/guides/completions/)
or the [OpenAI Cookbook](https://cookbook.openai.com/).

In addition to providing compatibility with the Chat Completions API, Snowflake supports OpenRouter-compatible features
for Claude models. These features are exposed as extra fields on the request:

1. For prompt caching, use the `cache_control` field. See the [OpenRouter prompt caching documentation](https://openrouter.ai/docs/features/prompt-caching).
2. For reasoning tokens, use the `reasoning` field. See the [OpenRouter reasoning documentation](https://openrouter.ai/docs/use-cases/reasoning-tokens).

## Messages API reference

### POST /api/v2/cortex/v1/messages

Generates a response using a Claude model. The request and response format follows the
[Anthropic Messages API specification](https://docs.anthropic.com/en/api/messages).

Expand

```
POST https://<account_identifier>.snowflakecomputing.com/api/v2/cortex/v1/messages
```

Show lessSee more

Scroll to top

Note

The Messages API supports **Claude models only**. For other models, use the Chat Completions API.

#### Required headers

`Authorization: Bearer token`
:   Authorization for the request. `token` is a JSON web token (JWT), OAuth token, or
    [programmatic access token](../programmatic-access-tokens). For details, see
    [Authenticating Snowflake REST APIs with Snowflake](../../developer-guide/snowflake-rest-api/authentication).

`Content-Type: application/json`
:   Specifies that the body of the request is in JSON format.

`anthropic-version: 2023-06-01`
:   Required Anthropic API version header.

#### Optional headers

`X-Snowflake-Authorization-Token-Type: type`
:   Defines the type of authorization token.

    If you omit the `X-Snowflake-Authorization-Token-Type` header, Snowflake determines the token type by examining the token.

    Even though this header is optional, you can choose to specify this header. You can set the header to one of the following values:

    * `KEYPAIR_JWT` (for key-pair authentication)
    * `OAUTH` (for OAuth)
    * `PROGRAMMATIC_ACCESS_TOKEN` (for [programmatic access tokens](../programmatic-access-tokens))

`anthropic-beta: feature`
:   Enables beta features. Only Bedrock-compatible beta headers are supported.

#### Required JSON fields

| Field | Type | Description |
| --- | --- | --- |
| `model` | string | The Claude model to use (see [Model availability](#label-cortex-complete-llm-model-availability)). |
| `max_tokens` | integer | The maximum number of tokens to generate. |
| `messages` | array | An array of message objects. Each message has a `role` (`user` or `assistant`) and `content` (string or array of content blocks). |

See moreShow less

Expand

#### Supported features

The Messages API supports the standard Anthropic Messages API feature set for Claude models, including:

* Text generation and multi-turn conversations
* Streaming (`"stream": true`)
* System messages (via top-level `system` field)
* Tool calling (Anthropic format with `name`, `description`, `input_schema`)
* Image input (base64 source blocks)
* Prompt caching (`cache_control` on content blocks)
* Extended thinking (`thinking` parameter with `budget_tokens`)

For full request and response schema details, see the
[Anthropic Messages API documentation](https://docs.anthropic.com/en/api/messages).

#### Limitations

* **Claude models only.** OpenAI, Llama, Mistral, and other models are not available through this endpoint.
* **No flex processing or priority tier.** The `service_tier` field is not supported.
* **Bedrock beta headers only.** Only Bedrock-compatible `anthropic-beta` header values are supported.
* Error messages are generated by Snowflake, not by Anthropic.

#### Status codes

200 `OK`
:   Request completed successfully.

400 `invalid_request_error`
:   The request body is malformed or contains invalid values.

400 `unknown model model_name`
:   The specified model does not exist or is not a Claude model.

402 `budget exceeded`
:   The model consumption budget was exceeded.

403 `Not Authorized`
:   Account not enabled for REST API, or the default role does not have the `snowflake.cortex_user` database role.

429 `too many requests`
:   The usage quota has been exceeded. Try again later.

503 `inference timed out`
:   The request took too long.

## Rate limits

To ensure high performance for all Snowflake customers, Cortex REST API requests are subject to rate limits.
Requests exceeding the limits may receive an HTTP 429 response. Snowflake may occasionally adjust these limits.

The default limits in the following tables are applied per account and independently for each model.
Ensure your application handles 429 responses gracefully by retrying with
[exponential backoff](https://platform.openai.com/docs/guides/rate-limits#retrying-with-exponential-backoff).

If you need to increase the limits, contact Snowflake Support.

Cortex REST API rate limits

| Model | Tokens Processed  per Minute (TPM) | Requests per  Minute (RPM) | Max output (tokens) |
| --- | --- | --- | --- |
| `claude-3-5-sonnet` | 300,000 | 300 | 16,384 |
| `claude-3-7-sonnet` | 300,000 | 300 | 16,384 |
| `claude-sonnet-4-5` | 600,000 | 600 | 16,384 |
| `claude-haiku-4-5` | 600,000 | 600 | 16,384 |
| `claude-4-sonnet` | 300,000 | 300 | 16,384 |
| `claude-4-opus` | 75,000 | 75 | 16,384 |
| `deepseek-r1` | 100,000 | 100 | 16,384 |
| `llama3.1-8b` | 400,000 | 400 | 16,384 |
| `llama3.1-70b` | 200,000 | 200 | 16,384 |
| `llama3.1-405b` | 100,000 | 100 | 16,384 |
| `mistral-7b` | 400,000 | 400 | 16,384 |
| `mistral-large2` | 200,000 | 200 | 16,384 |
| `openai-gpt-4.1` | 300,000 | 300 | 16,384 |
| `openai-gpt-5` | 300,000 | 300 | 16,384 |
| `openai-gpt-5-chat` | 300,000 | 300 | 16,384 |
| `openai-gpt-5-mini` | 1,000,000 | 1,000 | 16,384 |
| `openai-gpt-5-nano` | 5,000,000 | 5,000 | 16,384 |

See moreShow less

Expand

### Increase rate limits with cross-region inference

If you set up [cross-region inference](cross-region-inference) in your Snowflake Account,
the rate limits are higher for the following models:

Cortex REST API rate limits with cross-region inference

| Model | Tokens Processed  per Minute (TPM) | Requests per  Minute (RPM) | Max output (tokens) |
| --- | --- | --- | --- |
| `claude-3-7-sonnet` | 600,000 | 600 | 16,384 |
| `claude-haiku-4-5` | 600,000 | 600 | 16,384 |
| `claude-sonnet-4-5` | 600,000 | 600 | 16,384 |
| `claude-4-sonnet` | 1,200,000 | 1,200 | 16,384 |
| `claude-4-opus` | 150,000 | 150 | 16,384 |
| `llama3.1-8b` | 800,000 | 400 | 16,384 |
| `llama3.1-70b` | 400,000 | 200 | 16,384 |
| `llama3.1-405b` | 200,000 | 100 | 16,384 |

See moreShow less

Expand

### Troubleshooting rate limit events

Offending either the TPM or RPM limits will result in a 429 response code. If
your REST API usage is below the request per minute rate limit but still
received a 429 response code, double check the token usage rate.

Cortex REST API implements rate limits using the
[Sliding Window Counter](https://blog.cloudflare.com/counting-things-a-lot-of-different-things/#sliding-windows-to-the-rescue)
pattern. The counters are stored in a highly-available Redis cluster only
accessible by Snowflake Cortex within Snowflake’s private network.

The sliding-window counter assumes that client traffic to the API in the previous time window is
uniformly distributed. When traffic is spiky, this assumption could overestimate the rate of
requests, but recovers quickly given the window is short. Please contact
Snowflake Support if you are subject to the overestimation and want to increase
the limits.

## Known issues

### Session token expiration

We recommended authenticating with one of the three methods defined in [Authenticating Snowflake REST APIs with Snowflake](../../developer-guide/snowflake-rest-api/authentication). However, if you choose to authenticate with a Snowflake session token, you must handle token refresh to ensure uninterrupted API access.

Session tokens expire periodically. If a request is executed with an expired session token, the REST API returns a `200 OK` response that includes error code `390112`. When this occurs, the operation is not performed.

To handle this behavior, your application should:

1. Check each API response for error code `390112`, even when the HTTP status code is `200 OK`.
2. When error code `390112` is detected, refresh the session token and retry the request.

Note

This behavior only affects applications using Snowflake session tokens. If you authenticate using
[key pair authentication](../../developer-guide/snowflake-rest-api/authentication.html#label-sfrest-api-authenticating-key-pair),
[OAuth](../../developer-guide/snowflake-rest-api/authentication.html#label-sfrest-authenticating-oauth), or
[programmatic access tokens (PATs)](../../developer-guide/snowflake-rest-api/authentication.html#label-sfrest-authenticating-pat),
you do not need to implement this error handling.

## Cost considerations

Snowflake Cortex REST API requests incur compute costs based on the number of tokens processed. Refer to the
[Snowflake Service Consumption Table](https://www.snowflake.com/legal-files/CreditConsumptionTable.pdf) for each model’s cost in dollars per million tokens.

A token is the smallest unit of text processed by Snowflake Cortex LLM functions,
approximately equal to four characters of text. The equivalence of raw input or output text to tokens can vary by model.

Both input and output tokens incur compute cost. If you use the API to provide a conversational or chat user experience,
all previous prompts and responses are processed to generate each new response, with corresponding costs.
