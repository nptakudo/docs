---
title: "AI Observability in Snowflake Cortex"
url: "https://docs.snowflake.com/en/user-guide/snowflake-cortex/ai-observability"
---

# AI Observability in Snowflake Cortex

Use AI Observability in Snowflake Cortex to evaluate and trace your generative AI applications.
With AI Observability, you can make your applications more trustworthy and transparent.
Use it to measure the performance of your AI applications by running systematic evaluations.
You can use the information from the evaluations to iterate on your application configurations and optimize performance.
You can also use it to log application traces for debugging purposes.

Use AI Observability to benchmark performance, thus making your applications trustworthy and providing greater confidence for production deployments.

AI Observability has the following features:

* **Evaluations:** Use AI Observability to systematically evaluate the performance of your generative AI applications and agents using the LLM-as-a-judge technique.
  You can use metrics, such as accuracy, latency, usage, and cost, to quickly iterate on your application configurations and optimize performance.
* **Comparison:** Compare multiple evaluations side by side and assess the quality and accuracy of responses. You can analyze the responses across different LLMs, prompts, and inference configurations to identify the best configuration for production deployments.
* **Tracing:** Trace every step of application executions across input prompts, retrieved context, tool use, and LLM inference. Use it to debug individual records and refine the app for accuracy, latency, and cost.

AI Observability can be used to evaluate a variety of task types, such as retrieval-augmented generation (RAG) and summarization. For example, the context relevance score can help you detect the quality of the search results retrieval corresponding to a user query. You can use the answer relevance and groundedness scores to detect the truthfulness and relevance of the final response based on the retrieved context.

For summarization, you can measure the factual correctness and comprehensiveness of the LLM-generated summaries based on original input and avoid prompts and LLMs that have a higher frequency of hallucinations in your generative AI applications.

To get started, learn about the [Key concepts](#label-ai-observability-key-concepts), and then take a quick walkthrough with the [AI Observability Tutorial](ai-observability/tutorial). You can then use the information in [Evaluate AI applications](ai-observability/evaluate-ai-applications) for an in-depth walkthrough.

To review a specific concept, see the [Snowflake AI Observability Reference](ai-observability/reference).

## Access control and prerequisites

Before you start using AI Observability:

1. To create and execute runs, your role must have the following roles or privileges granted. For more information, see [Required privileges](ai-observability/reference.html#label-ai-observability-required-privileges):

   * CORTEX\_USER database role
   * CREATE EXTERNAL AGENT privilege on the schema
   * CREATE TASK privilege on the schema
   * EXECUTE TASK global privilege
2. Install the following Trulens Python packages in your Python project:

   * `trulens-core`
   * `trulens-connectors-snowflake`
   * `trulens-providers-cortex`

   The version of the package that you’re using in your Python project should be version 2.1.2 or later.

TruLens is the platform that Snowflake uses to track your applications. For more information, see the [TruLens documentation](https://trulens.org/getting_started).

## Key concepts

### Applications

An application is an end-to-end generative AI application that is designed using multiple components such as LLMs, tools (such as search retrievers or APIs), and additional custom logic. For example, an application can contain a RAG pipeline with retrievers, re-rankers, and LLMs chained together. You can enable AI observability for applications that can run in any environment (such as Snowflake, cloud, or on-premises).

### External Agent

Applications are represented in Snowflake as an EXTERNAL AGENT. An EXTERNAL AGENT object in Snowflake is used to store application and evaluation metadata (such as the application name, version name, or run name). It does not store the application code, application definition, execution traces or evaluation results. While the application can be hosted in any environment (such as Snowflake, cloud, or on-premises), the execution traces and evaluation results are stored in an event table in your Snowflake account. For more information, see [Observability data](ai-observability/reference.html#label-ai-observability-data).

In addition to storing application and evaluation metadata, the EXTERNAL AGENT object is also used to govern access to the traces and evaluation results for the application.
For more information, see [Required privileges](ai-observability/reference.html#label-ai-observability-required-privileges).

### Versions

Applications can have multiple versions. Each version represents a different implementation. For example, these versions can represent different retrievers, prompts, LLMs or inference configurations.

### Dataset

A dataset represents a set of inputs. You can configure it to also represent a set of expected outputs (the ground truth) to test the application. Using the dataset, you can invoke the application to do the following tasks:

* Generate the output.
* Capture the traces.
* Compute evaluation metrics.

You can use a dataset containing both the inputs and the generated outputs to compute the evaluation metrics without invoking the application. For a list of fields supported in the dataset, see [Dataset and attributes](ai-observability/reference.html#label-ai-observability-dataset-and-attributes).

### Runs

A run is an evaluation job. It uses the dataset and the application version that you’ve specified to compute evaluation metrics.

A run has an invocation stage and a computation stage. The invocation stage triggers the application to generate the output and corresponding traces. The computation stage computes the evaluation metrics specified for the run. Multiple computations can be performed to add new metrics to an existing run. For the list of statuses associated with the execution of a run, see [Runs](ai-observability/reference.html#label-ai-observability-runs).

### Metrics

Evaluation metrics are scores that you use to assess generative AI application performance based on your own criteria. These metrics use LLMs to grade outputs and provide detailed scoring information. For a comprehensive list of metrics and their definitions, see [Evaluation metrics](ai-observability/reference.html#label-ai-observability-evaluation-metrics).

### Traces

Traces are comprehensive records that capture the inputs, outputs, and intermediate steps of the interactions with an LLM application.
Traces provide a detailed view of the application’s execution. Use traces to analyze and understand the model’s behavior at each stage.
You can compare the traces of different application versions to identify improvements, debug issues, and verify intended performance. For information about accessing traces associated with each record, see [Evaluate AI applications](ai-observability/evaluate-ai-applications).

## Pricing

AI Observability uses LLM judges to compute the evaluation metrics. For server-side evaluations, LLMs on Cortex AI are used as LLM judges. The LLM judges are invoked via the [COMPLETE (SNOWFLAKE.CORTEX)](../../sql-reference/functions/complete-snowflake-cortex) function to perform evaluations.
You incur charges for the Cortex Complete function calls. The LLM used to perform the evaluations determines how much you’re charged.
Additionally, you’re charged the following:

* Warehouse charges for tasks used to manage evaluation runs
* Warehouse charges for queries used to compute evaluation metrics
* Storage charges for the evaluation results
* Warehouse charges to retrieve the evaluation results to be viewed in Snowsight
