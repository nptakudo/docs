---
title: "CI/CD integrations on dbt Projects on Snowflake"
url: "https://docs.snowflake.com/en/user-guide/data-engineering/dbt-projects-on-snowflake-ci-cd"
---

# CI/CD integrations on dbt Projects on Snowflake

dbt project objects support using Snowflake CLI commands to integrate deployment and execution into your CI/CD workflows. For a detailed tutorial, see [Tutorial: Set up CI/CD integrations on dbt Projects on Snowflake](../tutorials/dbt-projects-on-snowflake-ci-cd-tutorial).

This topic explains how to use GitHub Actions to automatically test and deploy your dbt Projects on Snowflake whenever you open a pull request or merge to
main.

Continuous Integration (CI) runs your dbt project against a dev schema on each pull request. In other words, whenever someone opens or
updates a pull request in your code repository, you automatically run tests and builds on the new code. This helps catch problems early
before merging.

Continuous Deployment (CD) keeps a dbt project object in Snowflake up to date after your commits are merged. In other words, whenever code
gets merged into a branch, you automatically deploy the updated code to production. This ensures that your production environment stays
up-to-date, reliably and reproducibly.

CI/CD helps avoid manual, error-prone deployments, ensures changes are validated before being merged, and enables consistent, repeatable
deployments and versioning.

## Why use CI/CD for a dbt Project

dbt projects define all your data transformations in code, so frequent updates can easily introduce errors. CI catches these issues early by
testing every change in a separate dev environment before merging.

After changes are merged, CD automatically updates the official dbt project object in your Snowflake production environment. This removes
manual steps, reduces risk, keeps everything version-controlled, and supports a reliable, collaborative workflow.

## High-level prerequisites for using CI/CD on dbt Projects

* A dbt project stored in a Git repository (for example, GitHub).
* A Snowflake account and user with privileges as described in [Access control for dbt projects on Snowflake](dbt-projects-on-snowflake-access-control).
* Privileges to create and edit the following objects or access to an administrator who can create each of them on your behalf:

  + GitHub repository environment variables and secrets to hold Snowflake account, database and schema values, and workflow files (for example,
    `.github/workflows/…`) that define CI and CD jobs.
  + Snowflake service account to communicate with GitHub
* A separation between dev environment (for CI) and prod environment (for CD) in Snowflake (for example, separate databases or schemas for each
  environment).
* A way to permit your CI/CD runner (for example, GitHub Actions) to connect to Snowflake, such as OIDC or PAT. For more information, see
  [Safely configure the action in your CI/CD workflow](../../developer-guide/snowflake-cli/cicd/integrate-ci-cd.html#label-integrate-ci-cd-safely-configure).
* In your code repository, a `profiles.yml` file configured to point to dev and prod targets (for example, databases/schemas, warehouse).
* A network policy that allows inbound access from your Git provider into Snowflake.

## CI/CD workflow overview

The following steps outline the typical workflow with CI/CD.

1. Developer writes or modifies dbt code (models, tests, etc.) in a branch.
2. Developer opens a pull request.
3. CI kicks in: a tester instance of the dbt project object is deployed to the Snowflake dev environment, which runs the `dbt run`
   and `dbt test` commands.

   * If an operation fails, the pull request fails. The developer must fix and update, then rerun.
   * If all operations pass, the pull request is eligible for merge.
4. Pull request is merged to main.
5. CD kicks in: the production dbt project object in Snowflake is updated to reflect the latest code.
6. Optionally, automated scheduling (for example, via Snowflake tasks) can be deployed, so data pipelines run on a schedule without manual
   intervention.
