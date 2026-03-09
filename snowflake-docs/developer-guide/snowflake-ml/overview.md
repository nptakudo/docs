---
title: "Snowflake ML: End-to-End Machine Learning"
url: "https://docs.snowflake.com/en/developer-guide/snowflake-ml/overview"
---

# Snowflake ML: End-to-End Machine Learning

Snowflake ML is an integrated set of capabilities for end-to-end machine learning in a single platform on top of your
governed data.
This is a unified environment for ML development and productionization that is optimized for large-scale distributed feature engineering, model training and inference on CPU and GPU compute without manual tuning or configuration.

![Snowflake ML Overview Diagram](../../_images/snowflake-ml_v5-NEWEST.png)

Scaling end-to-end ML workflows in Snowflake is seamless. You can do the following:

* Prepare data
* Create and use features with the Snowflake Feature Store
* Train models with CPUs or GPUs using any open-source package from Snowflake Notebooks on Container Runtime
* Create experiments to evaluate your trained models against set metrics
* Operationalize your pipelines using Snowflake ML Jobs
* Deploy your model for inference at scale with the Snowflake Model Registry
* Monitor your production models with ML Observability and Explainability
* Use ML Lineage to track the source data to features, datasets, and models throughout your ML pipeline

Snowflake ML is also flexible and modular. You can deploy the models that you’ve developed in Snowflake outside of Snowflake and externally-trained models can easily be brought into Snowflake for inference.

## Capabilties for data scientists and ML engineers

### Snowflake Notebooks on Container Runtime

[Snowflake Notebooks on Container Runtime](container-runtime-ml) provide a Jupyter-like environment for training and fine-tuning large-scale models in Snowflake, without infrastructure management. Start training with preinstalled packages such as PyTorch, XGBoost, or Scikit-learn, or install any package from open-source repositories like HuggingFace or PyPI.
Container Runtime is optimized to run on Snowflake’s infrastructure to provide you with highly efficient data loading, distributed model training, and hyperparameter tuning.

### Snowflake Feature Store

The [Snowflake Feature Store](feature-store/overview) is an integrated solution for defining, managing, storing and discovering ML features derived from your data. The Snowflake Feature Store supports automated, incremental refresh from batch and streaming data sources, so that feature pipelines need be defined only once to be continuously updated with new data.

### ML Jobs

Use [Snowflake ML Jobs](ml-jobs/overview) to develop and automate ML pipelines. ML Jobs also enable teams that prefer working from an external IDE (VS Code, PyCharm, SageMaker Notebooks) to dispatch functions, files or modules down to Snowflake’s Container Runtime.

### Experiments

Use <experiments> to record the results of your model training, and evaluate a collection of models in an organized way. Experiments help you select the best model for your use case to bring live to production. Training can either be logged in an experiment during model training on Snowflake, or you can upload your own metadata and artifacts from prior training. After concluding your training, view all of the results in Snowsight and pick the right model for your needs.

### Snowflake Model Registry and Model Serving

The [Snowflake Model Registry](model-registry/overview) allows for the logging and management of all your ML models, regardless of whether they’re trained on Snowflake or other platforms. You can use the models from the model registry to run inference at scale. You can use Model Serving to deploy the models to Snowpark Container Service for inference.

### ML Observability

[ML Observability](model-registry/model-observability) provides tools to monitor model performance metrics in Snowflake. You can track models in production, monitor performance and drift metrics, and set alerts for performance thresholds. Additionally, use the ML Explainability function to compute Shapley values for models in the Snowflake Model Registry, regardless of where they were trained.

### ML Lineage

[ML Lineage](ml-lineage) is a capability to trace end-to-end lineage of ML artifacts from source data to features, datasets, and models. This enables reproducibility, compliance, and debugging across the full lifecycle of ML assets.

### Snowflake Datasets

[Snowflake Datasets](dataset) provide an immutable, versioned snapshot of your data suitable for ingestion by your machine learning models.

## Capabilities for business analysts

For business analysts, use [ML Functions](../../guides-overview-ml-functions) to shorten development time for common scenarios such as forecasting and anomaly detection across your organization with SQL.

## Additional Resources

See the following resources to get started with Snowflake ML:

* [Train an end-to-end ML model](http://quickstarts.snowflake.com/guide/end-to-end-ml-workflow/)
* [Quickstarts](https://quickstarts.snowflake.com/guide/intro_to_machine_learning_with_snowpark_ml_for_python)
* [Snowflake ML webpage](https://www.snowflake.com/en/product/features/snowflake-ml/)

Contact your Snowflake representative for early access to documentation on other features currently under development.
