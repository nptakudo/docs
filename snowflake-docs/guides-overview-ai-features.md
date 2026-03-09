---
title: "Snowflake AI and ML"
url: "https://docs.snowflake.com/en/guides-overview-ai-features"
---

# Snowflake AI and ML

Snowflake offers two broad categories of powerful, intelligent features based on Artificial Intelligence (AI) and
Machine Learning (ML). These features can help you do more with your data in less time than ever before.

* **Snowflake Cortex** is a suite of AI features that use large language models (LLMs) to understand unstructured data,
  answer freeform questions, and provide intelligent assistance. This suite of Snowflake AI Features comprises:

  + [Cortex Agents](user-guide/snowflake-cortex/cortex-agents)
  + [Snowflake Cortex AI Functions (including LLM functions)](user-guide/snowflake-cortex/aisql)
  + [Cortex Analyst](user-guide/snowflake-cortex/cortex-analyst)
  + [Cortex Fine-tuning](user-guide/snowflake-cortex/cortex-finetuning)
  + [Cortex Search](user-guide/snowflake-cortex/cortex-search/cortex-search-overview)
  + [Snowflake Intelligence](user-guide/snowflake-cortex/snowflake-intelligence)
  + [Cortex Code in Snowsight](user-guide/cortex-code/cortex-code-snowsight)
  + [Cortex Code CLI](user-guide/cortex-code/cortex-code-cli)
* **Snowflake ML** provides functionality for you to build your own models.

  + [ML Functions](guides-overview-ml-functions) simplify the process of creating and using traditional machine
    learning models to detect patterns in your structured data. These powerful out-of-the-box analysis tools help
    time-strapped analysts, data engineers, and data scientists understand, predict, and classify data, without any
    programming.
  + For data scientists and developers, [Snowflake ML](developer-guide/snowflake-ml/overview) lets you develop
    and operationalize custom models to solve your unique data challenges, while keeping your data inside Snowflake.
    Snowflake ML incorporates model development classes based on popular ML frameworks, along with ML Ops capabilities
    such as a feature store, a model registry, framework connectors, and immutable data snapshots.

## Use of Snowflake AI features

Snowflake AI Features and their underlying models are designed with the following principles in mind:

* **Full security.** Except as you elect, all AI models run inside of Snowflake’s security and governance perimeter. Your data is not
  available to other customers or model developers.
* **Data privacy.** Snowflake never uses your Customer Data to train models made available to our customer base.
* **Control.** You have control over your team’s use of Snowflake AI Features through familiar
  [role-based access control](user-guide/security-access-control-overview).

## AI/ML model update process

Snowflake is continually working to improve the quality of its offerings, including the models powering the Snowflake AI Features.
This section describes how updates to those models fit into [Snowflake’s Behavior Change](release-notes/intro-bcr-releases) process.

### Behavior change process for models

At Snowflake, feature updates are announced and deployed in the following 3 types of releases:

* [Bundled Behavior Changes](release-notes/intro-bcr-releases) - Once a month release that introduces behavior changes.
* [Unbundled Behavior Changes](release-notes/bcr-bundles/un-bundled/unbundled-behavior-changes) - Unbundled releases are not
  associated with a bundled or standard weekly release.
* [What’s new](release-notes/new-features) - Newly released features or important updates to existing features.

Model updates follow a similar pattern of announcements. For model updates, the following would constitute a **behavior change**:

* Required syntax changes (e.g. specifying a new model or model version in the function parameter).
* Required prompts or input updates to get similar results.
* Significant changes in structure of the model output.
* Deprecation of a model.

**Bundled behavior changes** would include most anticipated behavior changes, including:

* Model deprecation in the ordinary course, such as planned deprecation by the model provider or Snowflake (including those on which
  fine-tuning is permitted).
* Model updates, e.g. new versions or new models, that may result in changes to syntax, prompts, or output structure.

**Unbundled behavior** changes would typically be reserved for the following:

* Model deprecation for emergency reasons, e.g. concerns about the quality of a model or its outputs.

Lastly, **What’s new** denotes general improvements that would likely not constitute a behavior change and therefore would be
automatically included. This would typically be the following:

* Model updates or new versions (whether provided by a third party or Snowflake) that improve results but have no anticipated material
  effect on how you interact with the model.

The following table shows some examples of model updates and how they would be announced:

| Type of update | Unbundled behavior change | Bundled behavior change | What’s new |
| --- | --- | --- | --- |
| A new version of the Jamba model is released but has no anticipated material effect on how you interact with the model. |  |  | ✔ |
| A new Llama model is made available through Snowflake. |  |  | ✔ |
| One of the Mistral models is deprecated. |  | ✔ |  |
| An update to the TRANSLATE model results in a change in the output structure. |  | ✔ |  |
| A model is deprecated due to safety concerns regarding the model output. | ✔ |  |  |

See moreShow less

Expand

## Legal notices

Where your configuration of Cortex Code uses a model provided on the
[Model and Service Pass-Through Terms](https://www.snowflake.com/en/legal/optional-offerings/offering-specific-terms/ai-features/model-pass-through-terms/),
your use of that model is further subject to the terms for that model on that page.

The data classification of inputs and outputs are as set forth in the following table.

| Input data classification | Output data classification | Designation |
| --- | --- | --- |
| Usage Data | Customer Data | Cortex Code CLI: Covered AI Features. Cortex Code in Snowsight: Preview AI Features. [[1]](#id2) |

See moreShow less

Expand

[[1](#id1)]

Represents the defined term used in the AI Terms and Acceptable Use Policy.

For additional information, refer to [Snowflake AI and ML](#).
