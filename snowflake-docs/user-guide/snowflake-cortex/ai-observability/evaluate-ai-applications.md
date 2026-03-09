---
title: "Evaluate AI applications"
url: "https://docs.snowflake.com/en/user-guide/snowflake-cortex/ai-observability/evaluate-ai-applications"
---

# Evaluate AI applications

To evaluate a generative AI application, follow these steps:

1. Build the app and instrument it using Trulens SDK (Applications built using Python are supported).
2. Register the app in Snowflake.
3. Create a run by specifying the input dataset.
4. Execute the run to generate traces and compute evaluation metrics.
5. View the evaluation results in Snowsight.

## Instrument the app

After you create your generative AI application in Python, import the TruLens SDK to instrument it. The TruLens SDK provides an `@instrument()` decorator to instrument the functions in your application to generate the traces and compute the metric.

* To use the decorator, add the following import to your Python application:

  CopyExpand

  ```
  from trulens.core.otel.instrument import instrument
  ```

  Show lessSee more

  Scroll to top

You can change the granularity of the `@instrument()` decorator depending on your requirements.

### Scenario 1: Trace a function

You can add `@instrument()` before the function you need to trace. This automatically captures the inputs to the function, the outputs (return values), and the latency of execution. For example, the following code demonstrates tracing an `answer_query` function that automatically captures input query and the final response:

CopyExpand

```
@instrument()
def answer_query(self, query: str) -> str:
    context_str = self.retrieve_context(query)
    return self.generate_completion(query, context_str)
```

Show lessSee more

Scroll to top

### Scenario 2: Trace a function with a specific span type

A span type specifies the nature of the function and improves the readability and understanding of the traces. For example, in a RAG application you can specify span type as `RETRIEVAL` for your search service (or retriever) and specify the span type as `GENERATION` for the LLM inference call. The following span types are supported:

* `RETRIEVAL`: Span type for retrieval or search functions
* `GENERATION`: Span type for model inference calls from an LLM
* `RECORD_ROOT`: Span type for the main function in your application

If you don’t specify a span type with the `@instrument()`, an `UNKNOWN` span type is assigned by default. To use span attributes, add the following import to your Python application.

CopyExpand

```
from trulens.otel.semconv.trace import SpanAttributes
```

Show lessSee more

Scroll to top

The following code snippet demonstrates tracing a RAG application. The span type must always be prefixed with `SpanAttributes.SpanType`.

CopyExpand

```
@instrument(span_type=SpanAttributes.SpanType.RETRIEVAL)
def retrieve_context(self, query: str) -> list:
    """
    Retrieve relevant text from vector store.
    """
    return self.retrieve(query)

@instrument(span_type=SpanAttributes.SpanType.GENERATION)
def generate_completion(self, query: str, context_str: list) -> str:
    """
    Generate answer from context by calling an LLM.
    """
    return response

@instrument(span_type=SpanAttributes.SpanType.RECORD_ROOT)
def answer_query(self, query: str) -> str:
    context_str = self.retrieve_context(query)
    return self.generate_completion(query, context_str)
```

Show lessSee more

Scroll to top

### Scenario 3: Trace a function and compute evaluations

In addition to providing span types, you must assign relevant parameters in your application to span attributes to compute the metrics. For example, to compute context relevance in a RAG application, you must assign the relevant query and retrieval results parameter to appropriate attributes `RETRIEVAL.QUERY_TEXT` and `RETRIEVAL.RETRIEVED_CONTEXTS` respectively. The attributes required to compute each individual metric can be found in the Metrics page.

The following span attributes are supported for each span type:

* `RECORD_ROOT`: `INPUT`, `OUTPUT`, `GROUND_TRUTH_OUTPUT`
* `RETRIEVAL`: `QUERY_TEXT`, `RETRIEVED_CONTEXTS`
* `GENERATION`: None

To use span attributes, you need to add the following import to your Python application.

CopyExpand

```
from trulens.otel.semconv.trace import SpanAttributes
```

Show lessSee more

Scroll to top

The following code snippet provides an example to compute context relevance for a retrieval service. The attributes must always follow the format `SpanAttributes.<span type>.<attribute name>` (e.g., `SpanAttributes.RETRIEVAL.QUERY_TEXT`).

CopyExpand

```
@instrument(
    span_type=SpanAttributes.SpanType.RETRIEVAL,
    attributes={
        SpanAttributes.RETRIEVAL.QUERY_TEXT: "query",
        SpanAttributes.RETRIEVAL.RETRIEVED_CONTEXTS: "return",
    }
)
def retrieve_context(self, query: str) -> list:
    """
    Retrieve relevant text from vector store.
    """
    return self.retrieve(query)
```

Show lessSee more

Scroll to top

In the preceding example, `query` represents the input parameter to `retrieve_context()` and `return` represents the value returned. These are assigned to the attributes `RETRIEVAL.QUERY_TEXT` and `RETRIEVAL.RETRIEVED_CONTEXTS` to compute context relevance.

### Auto-instrument framework applications

In addition to manual instrumentation using the `@instrument()` decorator, TruLens provides specialized wrappers that automatically instrument applications built with popular LLM frameworks. These wrappers provide integration and automatic tracing without requiring manual decoration of individual functions.

#### TruChain for LangChain

`TruChain` provides automatic instrumentation for applications built with [LangChain](https://www.langchain.com/). It automatically captures the execution of key LangChain classes including chains, LLMs, prompts, and retrievers.

CopyExpand

```
from trulens.apps.langchain import TruChain

# Wrap your LangChain application
tru_recorder = TruChain(
    rag_chain,
    app_name="my_langchain_app",
    app_version="v1.0"
)

# Use the recorder as a context manager
with tru_recorder as recording:
    response = rag_chain.invoke(input_query)
```

Show lessSee more

Scroll to top

`TruChain` supports:

* Automatic instrumentation of LangChain Expression Language (LCEL) chains
* Async support through the `ainvoke` method
* Built-in selectors (`on_input`, `on_output`, `on_context`) for RAG triad evaluation

#### TruGraph for LangGraph

`TruGraph` provides automatic instrumentation for applications built with [LangGraph](https://langchain-ai.github.io/langgraph/). It automatically detects LangGraph applications and instruments both LangChain and LangGraph components.

CopyExpand

```
from trulens.apps.langgraph import TruGraph

# Wrap your LangGraph application
tru_recorder = TruGraph(
    graph,
    app_name="my_langgraph_app",
    app_version="v1.0"
)

# Use the recorder as a context manager
with tru_recorder as recording:
    response = graph.invoke({"messages": [("user", input_query)]})
```

Show lessSee more

Scroll to top

`TruGraph` supports:

* Automatic `@task` instrumentation with intelligent attribute extraction
* Multi-agent evaluation capabilities
* Combined instrumentation of both LangChain and LangGraph components

#### TruLlama for LlamaIndex

`TruLlama` provides automatic instrumentation for applications built with [LlamaIndex](https://www.llamaindex.ai/). It automatically captures the execution of key LlamaIndex classes including query engines, retrievers, and response synthesizers.

CopyExpand

```
from trulens.apps.llamaindex import TruLlama

# Wrap your LlamaIndex query engine
tru_recorder = TruLlama(
    query_engine,
    app_name="my_llamaindex_app",
    app_version="v1.0"
)

# Use the recorder as a context manager
with tru_recorder as recording:
    response = query_engine.query(input_query)
```

Show lessSee more

Scroll to top

`TruLlama` supports:

* Automatic instrumentation of query engines, chat engines, and retrievers
* Async support through `aquery`, `achat`, and `astream_chat` methods
* Streaming support for LlamaIndex applications
* Built-in selectors (`on_input`, `on_output`, `on_context`) for RAG triad evaluation

For more information about framework-specific instrumentation, see the [TruLens documentation](https://www.trulens.org/component_guides/instrumentation/).

## Register app in Snowflake

To register your generative AI application in Snowflake for capturing traces and conducting evaluations, you need to create a `TruApp` object using the TruLens SDK that records the invocation (execution) of the user’s app and exports traces to Snowflake.

CopyExpand

```
tru_app = TruApp(
    app: Any,
    app_name: str,
    app_version: str,
    connector: SnowflakeConnector,
    main_method: callable  # i.e. app.query
)
```

Show lessSee more

Scroll to top

Note

If your application is built using LangChain, LangGraph, or LlamaIndex, you can use `TruChain`, `TruGraph`, or `TruLlama` respectively in place of `TruApp`. These framework-specific wrappers provide the same registration functionality while also enabling automatic instrumentation of your application. See [Auto-instrument framework applications](#label-evaluate-auto-instrument) for more details.

Parameters:

* `app: Any`: an instance of the user-defined application that will later be invoked during a run for evaluation. i.e. `app = RAG()`
* `app_name: str`: is the name of the application user can specify and will be maintained in the user’s Snowflake account.
* `app_version: str`: is the version user can specify for the app to allow experiments tracking and comparison.
* `connector: SnowflakeConnector`: a wrapper class that manages snowpark session and Snowflake DB connection.
* `main_method: callable` (Optional): is the entry point method for the user’s application, which tells the SDK how the app is expected to be called by users and where to start tracing the invocation of the user app (specified by app). For the example of RAG class, the main\_method can be specified as `app.answer_query`, assuming the answer method is the entry point of the app. Alternatively, instrument the entry point method with span attribute RECORD\_ROOT. In that case, this parameter is not required.

## Create Run

To begin an evaluation job, you need to create a run. Creating a run requires a run configuration to be specified. The `add_run()` function uses the run configuration to create a new run.

### Run Configuration

A run is created from a `RunConfig`

CopyExpand

```
run_config = RunConfig(
    run_name=run_name,
    description="desc",
    label="custom tag useful for grouping comparable runs",
    source_type="DATAFRAME",
    dataset_name="My test dataframe name",
    dataset_spec={
        "RETRIEVAL.QUERY_TEXT": "user_query_field",
        "RECORD_ROOT.INPUT": "user_query_field",
        "RECORD_ROOT.GROUND_TRUTH_OUTPUT": "golden_answer_field",
    },
    llm_judge_name: "mistral-large2"
)
```

Show lessSee more

Scroll to top

* `run_name: str`: name of the run, should be unique under the same `TruApp`
* `description: str` (optional): string description of the run
* `label: str` (optional): label used to group run together
* `source_type: str`: specifies the source of the dataset. It can either be `DATAFRAME` for a python dataframe or `TABLE` for a user table in the Snowflake account.
* `dataset_name: str`: any arbitrary name specified by the user if source\_type is `DATAFRAME`. Or, a valid Snowflake table name under the user’s account under current context (database and schema) or Snowflake fully-qualified name in the form of “database.schema.table\_name”.
* `dataset_spec: Dict[str, str]`: a dictionary mapping supported span attributes to user’s column names in the dataframe or table. The allowed keys are span attributes as specified in the Dataset page and the allowed values are column names in the user’s specified dataframe or table. For example, “golden\_answer\_field” in the run config example above must be a valid column name
* `llm_judge_name: str` (Optional): name to use as LLM judges during LLM-based metric computation. Please see the models page for supported judges. If not specified, the default value is `llama3.1-70b`

CopyExpand

```
run = tru_app.add_run(run_config=run_config)
```

Show lessSee more

Scroll to top

Request Parameters:

* `run_config: RunConfig`: contains the configuration for the run.

### Retrieve Run

Retrieves the run.

CopyExpand

```
run = tru_app.get_run(run_name=run_name)
```

Show lessSee more

Scroll to top

Request parameters:

* `run_name: str`: name of the run

### View Run metadata

Describes the details of the run.

CopyExpand

```
run.describe()
```

Show lessSee more

Scroll to top

### Invoke Run

You can invoke the run using the `run.start()` function. It reads the inputs from the dataset specified in the run configuration, invokes the application for each input, generates the traces, and ingests the information for storage in your Snowflake account. `run.start()` is a blocking call until the application is invoked for all inputs in your dataset and ingestion is completed or timed out.

CopyExpand

```
run.start()  # if source_type is "TABLE"

run.start(input_df=user_input_df)  # if source_type is "DATAFRAME"
```

Show lessSee more

Scroll to top

Request Parameters:

* `input_df: DataFrame` (Optional): is a pandas dataframe from the SDK. If the source\_type in run configuration is specified as `DATAFRAME`, this field is mandatory. If the source\_type is `TABLE`, this field is not required.

### Compute metrics

You can start metric computations using `run.compute_metrics()` after the application is invoked and all traces are ingested. As long as the status of the run is `INVOCATION_IN_PROGRESS`, computation cannot be started. Once the status is `INVOCATION_COMPLETED` or `INVOCATION_PARTIALLY_COMPLETED`, `run.compute_metrics()` can be initiated. `run.compute_metrics()` is an asynchronous non-blocking function. You can call `compute_metrics` multiple times on the same run with a different set of metrics, and each call will trigger a new computation job. Note that metrics once computed cannot be re-computed again for the same run.

CopyExpand

```
run.compute_metrics(metrics=[
    "coherence",
    "answer_relevance",
    "groundedness",
    "context_relevance",
    "correctness",
])
```

Show lessSee more

Scroll to top

Request Parameters:

* `metrics: List[str]`: list of string names of the metrics listed in Metrics. The name of metrics should be specified in snake cases. i.e. Context Relevance should be specified as `context_relevance`.

### Check Run Status

You can check the status of the run after it is in progress. The list of statuses are in Run Status section.

CopyExpand

```
run.get_status()
```

Show lessSee more

Scroll to top

### Cancel Run

You can cancel an existing run using `run.cancel()`. This operation will prevent any future updates to the run, including run status and metadata fields.

CopyExpand

```
run.cancel()
```

Show lessSee more

Scroll to top

### Delete Run

You can delete an existing run using `run.delete()`. This operation deletes the metadata associated with the run and the evaluation results cannot be accessed. However, the traces and evaluations generated as part of the runs are not deleted and remain stored. Please refer to Observability data section for more information about storage and deletion of evaluation and traces.

CopyExpand

```
run.delete()
```

Show lessSee more

Scroll to top

### List Runs for an application

You can see the list of all available runs corresponding to a specific `TruApp` application object using the `list_runs()` function.

CopyExpand

```
tru_app.list_runs()
```

Show lessSee more

Scroll to top

Response:

Return a list of all Runs created under the `tru_app`.

## View Evaluations and Traces

To view evaluation results do the following:

1. Sign in to [Snowsight](../../ui-snowsight-gs.html#label-snowsight-getting-started-sign-in).
2. In the navigation menu, select AI & ML » Evaluations.

Do the following to view the evaluation results for your application runs:

* To view runs corresponding to a specific application, select the application.
* To view the evaluation results for a run, select the run. You view the aggregated results and the results corresponding to each record.
* To view traces for a record, select it. You can view detailed traces, latency, inputs and outputs into each stage of the application, evaluation results, and explanation provided by the LLM judge for the accuracy score that have been generated.

To compare runs that use the same dataset, select multiple runs and select Compare to compare the outputs and the evaluation scores.
