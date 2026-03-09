---
title: "Understand schema generation and customization"
url: "https://docs.snowflake.com/en/user-guide/data-engineering/dbt-projects-on-snowflake-schema-customization"
---

# Understand schema generation and customization

dbt uses the default macro `generate_schema_name` to decide where a model is built.

By default, it uses your target schema (`target.schema`) specified from your dbt environment or profile. Unlike dbt Core behavior, the
target schema specified in the `profiles.yml` file must exist before you create your dbt Project in order for it to compile or execute
successfully.

Typically, each developer has their own target schema, for example `analytics_dev`. For larger projects, you can set a custom schema to
group models and specify the schema configuration key in your `dbt_project.yml` file. dbt appends it to the target schema (for example,
`<target_schema>_<custom_schema>`) to keep intermediate and user-facing models separate.

CopyExpand

```
--Models in `models/tasty_bytes/ will be built in the "*_staging" schema
models:
  tasty_bytes:
      +schema: staging
```

Show lessSee more

Scroll to top

A model’s custom schema doesn’t replace the target schema; rather, dbt combines them to avoid collisions. For example, `analytics_dev_staging`.
This is because if dbt ignored the target schema and only used the custom schema (in this case, `staging`), every developer would write to
the same schema and overwrite each other.

If you want different behavior (for example, use only the custom schema, prepend user names, add environment prefixes, etc.), override
`generate_schema_name` in `/macros/` to change how the final schema name is built. For more information and examples, see
[Changing the way dbt generates a schema name](https://docs.getdbt.com/docs/build/custom-schemas#changing-the-way-dbt-generates-a-schema-name) in the dbt documentation.
