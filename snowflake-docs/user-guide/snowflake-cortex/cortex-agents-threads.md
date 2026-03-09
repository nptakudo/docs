---
title: "Use threads with the Cortex Agent REST API"
url: "https://docs.snowflake.com/en/user-guide/snowflake-cortex/cortex-agents-threads"
---

# Use threads with the Cortex Agent REST API

This guide explains how to create, continue, and manage threaded conversations using the Cortex Agent REST API.

The workflow for using threads includes the following steps:

1. Create a new thread and use it as part of an `agent:run` request to the Cortex Agent REST API.
2. Read the message IDs for the thread.
3. Choose a message to continue the thread from.

## Start a new thread and use it with Agent Run

You must create a new thread, then pass it as part of a request to `agent:run`.

1. Create a new thread using [Create thread](cortex-agents-threads-rest-api.html#label-cortex-agents-threads-rest-api-create).
2. Pass the ID of the newly created thread as part of a request to one of the `agent:run` REST API endpoints.

> * `agent:run` with Agent Object:
>
>   CopyExpand
>
>   ```
>   /api/v2/databases/{database}/schemas/{schema}/agents/{name}:run
>   ```
>
>   Show lessSee more
>
>   Scroll to top
> * `agent:run` without Agent Object:
>
>   CopyExpand
>
>   ```
>   /api/v2/cortex/agent:run
>   ```
>
>   Show lessSee more
>
>   Scroll to top
>
> As part of the request, pass the following:
>
> * `parent_message_id` must be `0`. This indicates that this request is the start of the thread.
> * Exactly one user message in `messages`.
>
> CopyExpand
>
> ```
> POST <agent run endpoint>
> {
>   "thread_id": 1234,
>   "parent_message_id": 0,
>   "messages": [
>     {
>       "role": "user",
>       "content": [
>         {
>           "type": "text",
>           "text": "What is the total revenue for 2025?"
>         }
>       ]
>     }
>   ],
> }
> ```
>
> Show lessSee more
>
> Scroll to top

## Read the returned message IDs

The Agent API streams back metadata events for each message in the conversation. The following output shows the structure of the metadata. Always listen for both user and assistant metadata events.

Expand

```
event: metadata
data: {"metadata": {"role":"user","message_id":123}}

event: metadata
data: {"metadata": {"role":"assistant","message_id":456}}
```

Show lessSee more

Scroll to top

In this output, the message IDs correspond to the following in the conversation:

* `123`: the persisted user message ID
* `456`: the persisted assistant message ID

Together, these IDs form the following thread:

CopyExpand

```
0 -> 123 (user) -> 456 (assistant)
```

Show lessSee more

Scroll to top

## Continue the conversation

For the next turn in the conversation, set `parent_message_id` to the last successful assistant message ID and pass new values in `messages`. In this example, the parent message ID is `456`.

Note

You must pass an assistant message ID as the `parent_message_id` to ensure the LLM functions as expected. You cannot pass a user message ID.
If you have lost track of the last message ID, use [Create thread](cortex-agents-threads-rest-api.html#label-cortex-agents-threads-rest-api-create) to list all messages in the thread.

CopyExpand

```
{
  "thread_id": 1234,
  "parent_message_id": 456,
  "messages": [
    {
      "role": "user",
      "content": [
        {
          "type": "text",
          "text": "What about last year?"
        }
      ]
    }
  ],

}
```

Show lessSee more

Scroll to top

Continue using the latest successful assistant message ID as the `parent_message_id` in subsequent requests.

### Fork a conversation

You can also fork the conversation by continuing from any earlier assistant message. To fork the conversation, pass the desired assistant message ID as the `parent_message_id` in a new request. In the following example, `3 (user) -> 4 (assistant)` and `5 (user) -> 6 (assistant)` represent two different forks from the same assistant response.

CopyExpand

```
0 -> 1 (user) -> 2 (assistant) -> 3 (user) -> 4 (assistant)
0 -> 1 (user) -> 2 (assistant) -> 5 (user) -> 6 (assistant)
```

Show lessSee more

Scroll to top

## Troubleshooting

In rare cases, the Agent API might fail to store the assistant message.
If assistant metadata is missing from the response, ignore the failed turn and continue from the last successful assistant message.

For example, consider the following thread:

CopyExpand

```
0 -> 1 (user) -> 2 (assistant) -> 3 (user) -> [assistant failed]
```

Show lessSee more

Scroll to top

To continue the conversation, pass message ID 2 as part of a new request because that is the last successful assistant message.

CopyExpand

```
0 -> 1 (user) -> 2 (assistant) -> 5 (user) -> 6 (assistant)
```

Show lessSee more

Scroll to top
