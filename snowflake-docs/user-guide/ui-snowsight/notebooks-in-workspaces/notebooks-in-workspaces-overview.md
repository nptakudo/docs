---
title: "Snowflake Notebooks in Workspaces"
url: "https://docs.snowflake.com/en/user-guide/ui-snowsight/notebooks-in-workspaces/notebooks-in-workspaces-overview"
---

# Snowflake Notebooks in Workspaces

Feature — Generally Available

Available to all AWS and Azure commercial regions. PrivateLink is supported.

## Overview

The new Snowflake Notebooks experience in Workspaces offers enhanced performance, improved developer productivity, and Jupyter compatibility.
The Workspaces environment supports easy file management, allowing you to iterate on individual notebooks and project files. Create folders,
upload files, and organize notebooks. Notebook files open in tabs in your workspace and are editable and executable.

This new offering includes:

* **Familiar Jupyter experience** - Supports a Jupyter notebook environment with direct access to governed Snowflake data.
* **Enhanced IDE features** - Editing tools, file management, and access to terminal for increased productivity.
* **Powerful for AI/ML** - Runs in a pre-built container environment optimized for scalable AI/ML development with fully-managed access to CPUs and GPUs.
* **Governed collaboration** - Allows multiple users to work in the same workspace with role-based access controls and version history through [Git-integrated workspaces](../workspaces-git) or [Shared workspaces](../workspaces-shared).
* **Schedule and orchestration** - Use the native scheduler or incorporate notebooks into orchestration scripts for production pipelines.

![../../../_images/notebooks-in-workspaces.png](../../../_images/notebooks-in-workspaces.png)

## Benefits for machine learning (ML) workflows

Notebooks in Workspaces provides two primary capabilities for ML workflows:

* **End-to-end workflow** - The platform enables users to consolidate their complete ML lifecycle, from source data access to model inference,
  within a single Jupyter notebook environment. This environment is integrated with the underlying data platform, allowing it to inherit existing
  governance and security controls for the data and code assets.
* **Scalable model development architecture** - The architecture supports the development of scalable models by providing open-source software
  (OSS) model development capabilities. Users can access distributed data loading and training across designated CPU or GPU compute pools. This
  design simplifies ML infrastructure management by abstracting the need for manual configuration of distributed compute resources.

For more information about Snowflake ML, see [Snowflake ML: End-to-End Machine Learning](../../../developer-guide/snowflake-ml/overview).

## Get started

Note

These quickstarts are only shown as examples. Following along with the example may require additional rights to third-party data,
products, or services that are not owned or provided by Snowflake. Snowflake does not guarantee the accuracy of these examples or
cover them under any Service Level Agreement.

* Watch the [introduction video](https://www.youtube.com/watch?v=_kFhFIvnIrQ) for an overview of Notebooks in Workspaces.
* Follow the [quickstart](https://www.snowflake.com/en/developers/guides/accelerate-topic-modeling-with-gpus-in-snowflake-ml/) to learn how
  to accelerate topic modeling with scikit-learn and pandas in Snowflake ML.
* Explore the [First Machine Learning Project notebooks](https://github.com/Snowflake-Labs/snowflake-demo-notebooks/tree/main/First_Machine_Learning_Project/Jupyter),
  a notebook series covering data preparation, exploratory data analysis, model training, and experiment tracking.
* Follow the [Build an End-to-End ML Workflow in Snowflake](https://www.snowflake.com/en/developers/guides/end-to-end-ml-workflow/)
  guide to walk through a complete machine learning workflow, from data preparation to model deployment.
* Follow the [Getting Started with Data Engineering using Snowflake Notebooks](https://www.snowflake.com/en/developers/guides/data-engineering-with-notebooks/)
  quickstart, with accompanying [code on GitHub](https://github.com/Snowflake-Labs/sfguide-data-engineering-with-notebooks), to learn how to build production data engineering pipelines using Notebooks in Workspaces.
* See an example of [Healthcare ML: Breast Cancer Classification with XGBoost](https://www.snowflake.com/en/developers/guides/healthcare-ml-breast-cancer-classification/)
  that demonstrates how to build a classification model in Snowflake.
