---
title: "Use Snowflake Collaboration Data Clean Rooms"
url: "https://docs.snowflake.com/en/user-guide/cleanrooms/v2/using"
---

# Use Snowflake Collaboration Data Clean Rooms

[![Snowflake logo in black (no text)](../../../_images/logo-snowflake-black.png)](../../../_images/logo-snowflake-black.png) [Preview Feature](../../../release-notes/preview-features) — Open

Available to all accounts.

Required version

To use Collaboration Data Clean Rooms, you must upgrade to Snowflake Data Clean Rooms version 12.9 or later.

This topic provides a high-level guide to using Collaboration Data Clean Rooms. It also provides details on all the key steps needed to create or participate in a collaboration.

## Requirements

* You must be [updated to the latest version of Snowflake Data Clean Rooms](../admin-tasks.html#label-dcr-updating-cleanrooms-environment).
* Owners and data providers must use Snowflake Enterprise Edition. Analysis runners can use Standard Edition.
* You need access to the Data Clean Rooms Collaboration API to be able to see or manage collaborations. For more information, see [Manage access to the DCR Collaboration API](v2-api-reference.html#label-dcr-collaboration-access-management-overview).
* You should disable secondary roles in your environment when using the Collaboration API:

  CopyExpand

  ```
  USE SECONDARY ROLES NONE;
  ```

  Show lessSee more

  Scroll to top

## Basic clean room collaboration workflow

Here is a simple clean room collaboration scenario:

1. The collaboration [owner](roles) optionally registers any templates or data offerings that they want to appear in the initial configuration of the collaboration.
2. The owner optionally asks any intended collaborators to register any templates or data offerings that they want to appear in the initial configuration of the collaboration. Collaborators then give the resource IDs of any items that they registered.
3. The owner then [creates a collaboration](#label-dcr-collaboration-create-collaboration). The collaboration is defined by a collaboration YAML spec that lists the collaborators, their roles, and all resources that should be present in the initial version of the collaboration.

   * When a collaboration is created, the set of collaborators and their roles is fixed: only collaborators with a role in the collaboration definition are invited to join. Similarly, the set of analysis runners is fixed. However, any collaborator can also become a data provider by linking new data into the collaboration.
   * If your collaboration includes users in other cloud hosting regions, they must [enable Cross-Cloud Auto-Fulfillment on their account](#label-dcr-collab-enabling-laf) before they can review and join the collaboration.
4. The owner joins the collaboration that they created, which makes the collaboration active. The collaboration is now visible and joinable by any collaborator in the spec.
5. Collaborators [review and join the collaboration](#label-dcr-collaboration-join-collaboration).
6. Collaborators can then optionally [add resources to the collaboration](#label-dcr-collaboration-add-resources), such as templates, and if they are a data provider, data offerings.
7. Analysis runners can then [run any templates assigned to them](#label-dcr-collab-run-an-analysis) in the collaboration, using any data available to them in the collaboration (and optionally unshared local data). The analysis runner bears the cost of the analysis. Templates can either return query results in the response or [activate results to the caller or another collaborator](#label-dcr-collaboration-activating-results).

The following sections describe the details of each of these steps.

## Create a collaboration

To create a collaboration, you design a [collaboration spec](spec-reference.html#label-dcr-collaboration-invitation-yaml) that defines all the collaborators [and their roles](roles). The collaboration owner optionally [registers and links any other resources](#label-dcr-collaboration-add-resources) that they want to make available in the initial collaboration, and includes the resources in the collaboration spec. If the owner expects to use resources from collaborators, the owner can also prompt those users to register their resources and give the owner the resource IDs to include in the collaboration spec.

The owner then calls INITIALIZE to begin creating the collaboration. By default, INITIALIZE also automatically joins the owner to
the collaboration. This is an asynchronous process, so they must call GET\_STATUS until the status is JOINED. When the collaboration
status is JOINED, the collaboration will be active, and all collaborators can see and join the collaboration.

CopyExpand

```
CALL SAMOOHA_BY_SNOWFLAKE_LOCAL_DB.COLLABORATION.INITIALIZE(
$$
api_version: 2.0.0
spec_type: collaboration
name: my_first_collaboration
owner: alice
collaborator_identifier_aliases:
  alice: example_com.acct_abc
  bob: another_example.acct_xyz
analysis_runners:
  bob:
    data_providers:
      alice:
        data_offerings: []
      bob:
        data_offerings: []
  alice:
    data_providers:
      alice:
        data_offerings: []
      bob:
        data_offerings: []
    templates: []
$$,
'APP_WH'
);
SET collaboration_name = '<collaboration_name>';

-- INITIALIZE automatically joins the owner. Check status until JOINED.
CALL SAMOOHA_BY_SNOWFLAKE_LOCAL_DB.COLLABORATION.GET_STATUS($collaboration_name);

-- Collaboration is visible here when it's joined.
CALL SAMOOHA_BY_SNOWFLAKE_LOCAL_DB.COLLABORATION.VIEW_COLLABORATIONS();
```

Show lessSee more

Scroll to top

## Add resources to a collaboration

Any collaborator can add resources to a collaboration, or remove resources that they have added to the collaboration. There are two steps to adding a resource to a collaboration:

1. The resource owner creates a [resource definition spec](spec-reference) for the resource and uses it to register the resource in their account. You can register the resource in your account’s [default registry](registries), or use a custom registry.
2. A collaborator links the resource into a collaboration. After the resource is linked in, it can be used by the designated collaborators. Some resource types, such as templates, can be linked in by any collaborator; other resources, such as data offerings, can be linked in only by users with a data provider role. Resources are typically available only to specific collaborators as defined by the collaboration specification and the resource sharer.

Resources can be added to a collaboration either before or after a collaboration is created.

Resources support versioning; however, creating a new resource with a new version doesn’t remove the previous version from the collaboration.

Resources are uniquely named by combining the user-provided name and version (and alias, for data offerings).

You can add the following resources to a collaboration:

### Templates

Templates are JinjaSQL clean room templates that can be run by specified collaborators.
Any collaborator can register and add a template to a collaboration, as long as all affected collaborators approve the request, as described in the following steps.
You can add or remove only templates that your account has registered.

**To add a template to a collaboration:**

1. [Design a template for the collaboration](#label-dcr-design-collaboration-template) and embed it in a [template specification](spec-reference.html#label-dcr-collaboration-template-yaml).
2. Register the template by calling `REGISTRY.REGISTER_TEMPLATE`. This returns a template ID.
3. Link the template. The process depends on whether the collaboration already exists:

   * **To add a template before the collaboration is created,** give the template ID to the collaboration owner, who adds it to the [collaboration spec](spec-reference.html#label-dcr-collaboration-invitation-yaml), defining who can run it.

     CopyExpand

     ```
      alice:
        data_providers:
          bob:
            data_offerings: []
        templates:
        - id: bob_template_v1 # Alice can run this template, seemingly registered by bob.
     ```

     Show lessSee more

     Scroll to top
   * **To add a template to an existing collaboration,** you must request permission from all collaborators affected by the template. Take the following steps to add a template to an existing collaboration:

     1. Call `REGISTER_TEMPLATE` to register the template in your account, which makes it available to add to collaborations.
     2. Call `ADD_TEMPLATE_REQUEST` with the template ID to start the approval flow to add the template to a specific collaboration, for specific users.

        All collaborators affected by the resource see the request when they call `VIEW_UPDATE_REQUESTS`.
     3. Collaborators who see the request with status PENDING should call `APPROVE_UPDATE_REQUEST` or `REJECT_UPDATE_REQUEST`.

        + If any collaborator rejects the request, the update request is rejected.
        + Collaborators can’t later change an approval to a rejection, or a rejection to an approval.

        When the request status is APPROVED, the template is available to the users specified in the add template request. If the request is REJECTED, any reason supplied by the rejecting party is visible in the request report. There might be a short delay after a template is approved by all users before the template is available. Call `view_templates` if you want to ensure that the template is available to use.

Tip

To see which templates you have registered, call `REGISTRY.VIEW_REGISTERED_TEMPLATES`.

#### Template design for a collaboration

Collaboration templates are the same as [clean room templates](../custom-templates), with a few special considerations:

* Shared tables listed in the collaboration are used to populate the template’s `source_table` variable.
* `my_table` is used only if an analysis runner wants to use local, unshared data. If you use the `my_table` variable in a template, be aware that tables assigned to that variable aren’t shared with the collaboration.
* Columns from the data sources might have new names when exposed to the template or user. See [Source column renaming](#label-dcr-source-column-renaming) to learn how and when source columns are renamed. Templates and user-provided arguments (such as a join column name) should use the final name, not the original name, if the column is renamed.
* Activation templates in a collaboration don’t need to be named `activation_template_name`. All other [activation template requirements](../custom-templates.html#label-dcr-custom-templates-activation) still apply.

For information about custom template syntax in Snowflake Data Clean Rooms, see [Custom clean room template reference](../custom-templates).

### Data offerings

A *data offering* is a set of one or more data views shared with specific analysis runners in a collaboration. Data offerings can be added by any data providers listed in a collaboration. Data offerings are exposed in a scoped format as `data offering ID.alias`, where the alias is a specific view in the data offering. You can share data offerings with a given collaborator only if you are listed as a data provider for that analysis runner in the [collaboration specification](spec-reference.html#label-dcr-collaboration-invitation-yaml).

A data offering is a live view of the data, not a snapshot of the data at the time the data offering is created or registered. Any Snowflake policies applied to the source data are active in the collaboration.

When you register a data offering, Snowflake creates a view for each data source listed in the [data offering specification](spec-reference.html#label-dcr-collaboration-data-yaml). The view includes only the columns listed in the data offering specification. When you link a data offering into a collaboration, Snowflake creates a copy of that view, access protected to any analysis runners who can access that data offering, according to the [collaboration specification](spec-reference.html#label-dcr-collaboration-invitation-yaml). If you move, rename, or change access permissions to the underlying tables, the data offering will become unusable through any previously registered links.

If you are using Snowflake Standard Edition, you can’t share data offerings with other collaborators, but you can [use your own data in a query](#label-dcr-using-local-data).

**Requirements:**

* You must have OWNERSHIP on any data that you want to share. If you don’t, you receive a “missing reference usage grant” error when you try to join the collaboration. [Learn how to handle this issue.](troubleshooting.html#label-dcr-database-missing-reference-usage-error)
* You must have the [data provider role](roles) in a collaboration.

Data offerings are added to a collaboration with the following steps:

1. Create a [data offering specification](spec-reference.html#label-dcr-collaboration-data-yaml) for your data.
2. Registers the data offering by calling `REGISTRY.REGISTER_DATA_OFFERING`, which returns a data offering ID.

   This step makes the data offering *available* to be linked into any collaboration that you can access. You can use the same data offering ID to share a data offering with multiple collaborations.
3. The next step depends on whether or not the collaboration has been created:

   * **If the collaboration hasn’t been created yet,** the data provider gives the data offering ID to the collaboration creator to add to the [collaboration definition](spec-reference.html#label-dcr-collaboration-invitation-yaml). When a data offering is added to the collaboration definition, the data offering will be visible to anyone in the collaboration after the data provider joins the collaboration.
   * **If the collaboration has been created,** the data provider joins the collaboration and calls `COLLABORATION.LINK_DATA_OFFERING` with the data offering ID, the collaboration name, and who the data can be shared with. There might be a short delay after a data offering is approved by all users before the data offering is available to use. Call `view_data_offerings` if you want to ensure that the data is available to use.

   You can remove data resources from a collaboration by calling `unlink_data_offering`.

Each data offering represents one or more tables or views. Individual tables are accessed using the syntax `collaborator alias.data offering ID.dataset alias`, where the data offering ID is a combination of the user-provided name and version values, and the alias is a single table in the offering. Consider the name, version, and alias as a scoping system when registering your data offerings.

For example, you might register the following data offering of sales data, where each table is specific to a US state:

CopyExpand

```
api_version: 2.0.0
spec_type: data_offering
version: v0
name: examplecorp_sales_by_state
datasets:
 - alias: AL
   data_object_fqn: mydb.mysch.al_data
 - alias: NY
   data_object_fqn: mydb.mysch.ny_data
 - alias: CA
   data_object_fqn: mydb.mysch.ca_data
```

Show lessSee more

Scroll to top

The analysis runner would then reference these tables as `data offering id.AL`, `data offering id.NY`, or `data offering id.CA`.

Data offerings aren’t visible in a collaboration until the user who registered the data offering joins the collaboration.

Tip

If you don’t have OWNERSHIP on data that you share, you will get an error when you try to join the collaboration or link your data offering. The error message gives information about a SQL command that an ACCOUNTADMIN must run to grant data access to the collaboration. After the ACCOUNTADMIN runs the command, you will be able to join the collaboration.
[See more information.](troubleshooting.html#label-dcr-database-missing-reference-usage-error)

When running a query, analysis runners pass the data offerings by ID to the `source_tables` parameter of `COLLABORATION.RUN`.

To see your registered data offerings, call `VIEW_REGISTERED_DATA_OFFERINGS`.

#### Apply usage policies to your data

There are two ways to apply a Snowflake column policy, such as a join or aggregation policy, to your shared data:

* [Apply the policy on the source data.](#label-dcr-collab-apply-policy-to-source-data) Any policies applied to the source data are enforced in the datasets
  exposed in a collaboration. Ensure that you communicate your policy to the user.
* [Apply the policy on the data offering when used in free-form queries.](#label-dcr-collab-freeform-sql-policies) If you allow free-form queries on your data offerings, you can specify column policies to enforce on free-form queries on your data. Specify column policies for free-form queries in the data offering specification. These policies are applied on top of any existing Snowflake policies on your source tables.

##### Apply the policy to your source data

Any Snowflake policies applied to the source data also apply to the data offering view in the collaboration.

If you apply Snowflake policies to your source data, be sure to let your collaborators know about them, so that they don’t unknowingly try to run a query that joins on a non-joinable column or doesn’t group when it should. You might mention any Snowflake policies in your data offering’s `description` field.

##### Apply the policy on the data offering (*free-form query usage only*)

You can apply Snowflake policies to [free-form queries](#label-dcr-collab-enable-free-form-queries) in your shared data without applying them to the source data. These policies are applied to your data when accessed using free-form queries in addition to any Snowflake policies applied directly to the source table.

**To add free-form SQL policies to your data:**

1. Create a [policy type supported by Collaboration Data Clean Rooms](spec-reference.html#label-dcr-freeform-sql-policies-field).
2. Add the following information to your data offerings definition:

   * Set `allowed_analyses: template_and_freeform_sql`.
   * Add a `freeform_sql_policies` section to the dataset definition.
   * Add the appropriate policy type sections under `freeform_sql_policies`, listing the Snowflake policies that you created, and which collaboration columns to apply them to.

Collaborators see policy types applied to your data when they call `COLLABORATION.VIEW_DATA_OFFERINGS`.

You can reuse a policy on multiple columns on multiple tables.

**Example:**

Policy creation and registrationData offering YAML

CopyExpand

```
CREATE OR REPLACE AGGREGATION POLICY my_db.public.my_agg_policy AS ()
  RETURNS AGGREGATION_CONSTRAINT ->
    AGGREGATION_CONSTRAINT(MIN_GROUP_SIZE => 5);
```

Show lessSee more

Scroll to top

CopyExpand

```
# Tell data clean rooms to set your aggregation policy on the hashed_email column of
# the data offering
api_version: 2.0.0
version: 1
name: my_favorite_dataset
datasets:
  - alias: test_freeform_restricted_agg
    data_object_fqn: samooha_provider_sample_database.audience_overlap.customers
    allowed_analyses: template_and_freeform_sql
    object_class: custom
    freeform_sql_policies:
      aggregation_policy:
        name: my_db.public.my_agg_policy
        entity_keys:
          - hashed_email
...
```

Show lessSee more

Scroll to top

## Source column renaming

Column names exposed to the template or free-form SQL caller are determined by the `category` and `column_type` values that describe the column in its [data offering definition](spec-reference.html#label-dcr-collaboration-data-yaml). Column renaming follows this rubric:

* If `category` for the column is `join_custom` or `passthrough`, the original column name is exposed.
* If `category` is `join_standard`, then the column is renamed as the `column_type` value.
* If `category` is `timestamp`, the column is renamed `timestamp` in the data offering.

For example, if the column in the source table is named `user_email_address`, how this column is exposed to the template or free-form SQL depends on how it is defined in the data offering definition:

* If the column category is `join_standard` and `column_type` is present:

  CopyExpand

  ```
  ... Snippet from data offering yaml ...
  schema_and_template_policies:
       user_email_address:
         category: join_standard
         column_type: hashed_email_sha256
  ```

  Show lessSee more

  Scroll to top

  Then the `column_type` value is used in queries and templates:

  CopyExpand

  ```
  SELECT HASHED_EMAIL_SHA256 FROM source_table[0];
  ```

  Show lessSee more

  Scroll to top
* If the column category is `join_custom`:

  CopyExpand

  ```
  ... Snippet from data offering yaml ...
  schema_and_template_policies:
       user_email_address:
         category: join_custom
         column_type: hashed_email_sha256
  ```

  Show lessSee more

  Scroll to top

  Then the original source column name is used in queries and templates:

  CopyExpand

  ```
  -- column_type is ignored for join_custom columns.
  SELECT user_email_address FROM source_table[0];
  ```

  Show lessSee more

  Scroll to top

## Join a collaboration

You must join a collaboration for any resources that you contributed to a collaboration to be available in the collaboration, or to be able to run an analysis in the collaboration.

* *The creator* is automatically joined when calling INITIALIZE (unless `auto_join_warehouse` is provided). If auto-join is disabled, the
  creator calls JOIN separately.
* *Non-creators* call REVIEW, and then JOIN.

  Important

  If your account is on a different cloud hosting region than the collaboration owner’s:

  + The `REVIEW` request will fail if [Cross-Cloud Auto-Fulfillment isn’t enabled on your account](#label-dcr-collab-enabling-laf).
  + `REVIEW` triggers additional asynchronous setup steps. Call `REVIEW` repeatedly until it returns a successful response, indicating that setup is complete.

Joining is an asynchronous process; call `GET_STATUS` to see when your status is listed as JOINED.

## Run an analysis

You can run analyses either by running a template in the query or by running a free-form SQL query on the collaboration data.
You must be a designated [analysis runner](roles) in a collaboration to be able to run an analysis.
The collaboration specification determines whether you can run a template, activate results, or run free-form SQL queries. Your capabilities, as well as the data and templates available for you to use, are described in the collaboration specification.

The analysis runner bears the cost of running an analysis.

### Run an analysis from a template

To run an analysis from a template, view the list of templates that you can run, view the list of data offerings that you can use, then call `COLLABORATION.RUN` with your values either as individual parameters or as an analysis specification in YAML format:

CopyExpand

```
-- See which data offerings are available.
CALL SAMOOHA_BY_SNOWFLAKE_LOCAL_DB.COLLABORATION.VIEW_DATA_OFFERINGS($collaboration_name);

-- See which templates you can run.
CALL SAMOOHA_BY_SNOWFLAKE_LOCAL_DB.COLLABORATION.VIEW_TEMPLATES($collaboration_name);

-- Pass in the arguments in analysis YAML format.
CALL SAMOOHA_BY_SNOWFLAKE_LOCAL_DB.COLLABORATION.RUN(
  $collaboration_name,
  $$
    api_version: 2.0.0
    spec_type: analysis
    name: My_analysis
    description: Sales results Q2 2025
    template: sales_join_template

    template_configuration:
      view_mappings:
        source_tables:
          -  user1_alias.data_offering_v1.table1
          -  user2_alias.another_data_offering_v1.table_2
      arguments:
         conv_purchase_id: PURCHASE_ID
         conv_purchase_amount: PURCHASE_AMOUNT
         publisher_impression_id: IMPRESSION_ID
         publisher_campaign_name: CAMPAIGN_NAME
         publisher_device_type: DEVICE_TYPE
  $$ );
```

Show lessSee more

Scroll to top

### Enable and run free-form SQL queries on your data

A data provider can enable analysis runners to run SQL queries against their collaboration data offerings. You must be a member of a collaboration, and be granted the analysis runner role with free-form SQL permission on a data offering to be able to run free-form SQL queries against that data.

#### Data provider steps

To enable collaborators to query a dataset from the command line, set `allowed_analyses: template_and_freeform_sql` in your dataset description. Users who join the collaboration can run free-form SQL queries on the datasets that they can access.

The following YAML defines a dataset that allows free-form queries:

CopyExpand

```
api_version: 2.0.0
version: 1
name: my_favorite_dataset
datasets:
  - alias: test_freeform_restricted_agg
    data_object_fqn: samooha_provider_sample_database.audience_overlap.customers
    object_class: custom
    allowed_analyses: template_and_freeform_sql
...
```

Show lessSee more

Scroll to top

#### Analysis runner steps

1. To see which datasets support free-form queries, after joining a collaboration, the analysis runner runs `COLLABORATION.VIEW_DATA_OFFERINGS`. The FREEFORM\_SQL\_VIEW\_NAME column in the results shows which tables can be accessed using free-form SQL, and the table name to use in the SQL query.

   CopyExpand

   ```
   CALL SAMOOHA_BY_SNOWFLAKE_LOCAL_DB.COLLABORATION.VIEW_DATA_OFFERINGS($collaboration_name);
   ```

   Show lessSee more

   Scroll to top

   Expand

   ```
   +-------------------------------+------------------------+------------------------------+------------------------------+---------------------------------------+
   |   template_view_name          | template_join_columns  | analysis_allowed_columns     | activation_allowed_columns   |      freeform_sql_view_name           |
   +-------------------------------+------------------------+------------------------------+------------------------------+---------------------------------------+
   | useralias.data_offering_alias |     ip_address         | email, name, age             |             SSN              | alias_name.test_data_offering_v0.customers|
   +-------------------------------+------------------------+------------------------------+------------------------------+---------------------------------------+
   ```

   Show lessSee more

   Scroll to top
2. The collaborator can then query the table listed in the FREEFORM\_SQL\_VIEW\_NAME column using free-form SQL queries:

   CopyExpand

   ```
   SELECT * FROM alias_name.test_data_offering_v0.customers;
   ```

   Show lessSee more

   Scroll to top

All policies applied to the table are enforced.

### Run an analysis with your own data when you use Standard Edition

If you use Standard Edition, you can run an analysis [in the standard way](#label-dcr-collab-run-an-analysis). However,
you can’t add your data to the collaboration description and share it with other users.

**To use your own data in a collaboration as a Standard Edition user:**

1. To register your data offering, call `REGISTRY.REGISTER_DATA_OFFERING`. You must specify column names.
2. Call `COLLABORATION.LINK_LOCAL_DATA_OFFERING`.

   Only you can see your offering when you call `COLLABORATION.VIEW_DATA_OFFERINGS`; other collaborators won’t see your data source listed.
3. Use the data offering ID when you call `COLLABORATION.RUN`, in either the `local_template_view_names` parameter, or the `local_view_mappings.my_tables` field if passing in an analysis YAML. `local_template_view_names` and `local_view_mappings.my_tables` populate the `my_table` parameter in the template.

The following example shows how to run a template using the YAML format version of the run procedure. This example includes the `my_tables` field, which is populated by calling `LINK_LOCAL_DATA_OFFERING`.

CopyExpand

```
-- See what data offerings are available. Your own local data will be listed here as well.
CALL SAMOOHA_BY_SNOWFLAKE_LOCAL_DB.COLLABORATION.VIEW_DATA_OFFERINGS($collaboration_name);

-- Pass in the arguments in analysis YAML format.
CALL SAMOOHA_BY_SNOWFLAKE_LOCAL_DB.COLLABORATION.RUN(
  $collaboration_name,
  $$
    api_version: 2.0.0
    spec_type: analysis
    name: my_analysis
    description: Cross-purchase results for Q4 2025
    template: mytemplate_v1

    template_configuration:
      view_mappings:
        source_tables:
          - ADVERTISER1.ADVERTISER_DATA_V1.CUSTOMERS
          - PUBLISHER.ADVERTISER_DATA_V1.CUSTOMERS
      local_view_mappings:
        my_tables:
          - PARTNER.MY_DATA_V1.MY_CUSTOMERS # Populate my_table array with my own table.
      arguments:  # Template arguments, as name: value pairs
         conv_purchase_id: PURCHASE_ID
         conv_purchase_amount: PURCHASE_AMOUNT
         publisher_impression_id: IMPRESSION_ID
         publisher_campaign_name: CAMPAIGN_NAME
         publisher_device_type: DEVICE_TYPE
  $$ );
```

Show lessSee more

Scroll to top

## Activate query results

Note

If you aren’t using the SAMOOHA\_APP\_ROLE role — that is, you are using a role managed by [access management procedures](v2-api-reference.html#label-dcr-collaboration-access-management-api) — you must have the REGISTER DATA OFFERING privilege to join any collaboration where you are an analysis runner and the collaboration specification includes an `activation_destinations` field.

**To activate the results of a query:**

1. Ensure that all activation columns have the following properties set in the appropriate spec:

   Data offering specCollaboration specAnalysis spec

   The [data offering specification](spec-reference.html#label-dcr-collaboration-data-yaml) for the table with the activated column, must set `activation_allowed: TRUE` for that column:

   CopyExpand

   ```
    api_version: 2.0.0
    spec_type: data_offering
    name: 2025_orders
    version: 2025_01_01_v1
    description: Activating Cleveland sales results for 2025

    datasets:
     - alias: customers
       data_object_fqn: db1.schema1.orders
       allowed_analyses: template_only
       object_class: custom
       schema_and_template_policies:
         email:
           category: join_standard
           column_type: hashed_email_sha256
           activation_allowed: TRUE
         purchase_amount:
           category: passthrough
           activation_allowed: TRUE
   ```

   Show lessSee more

   Scroll to top

   The [collaboration specification](spec-reference.html#label-dcr-collaboration-invitation-yaml) must provide `activation_destinations` values for the analysis runner. The data offering specification limits activation to designated analysis runners and templates.

   CopyExpand

   ```
   api_version: 2.0.0
   spec_type: collaboration
   name: simple_activation_collaboration
   description: Demonstrates a basic activation

   collaborator_identifier_aliases:
     advertiser_1: some_complex_identifier
     publisher_1: another_complex_identifier

   owner: publisher_1

   analysis_runners:
     advertiser_1:
       data_providers:
         advertiser_1:
           data_offerings:
             - id: customer_list
         publisher_1:
           data_offerings:
             - id: user1.2025_orders.sales
       templates:
         - id: activation_template_v0
       activation_destinations:
         snowflake_collaborators:
           - publisher_1
   ....
   ```

   Show lessSee more

   Scroll to top

   The [analysis specification](spec-reference.html#label-dcr-collaboration-analysis-yaml) must include an `activation` section with `snowflake_collaborator` and `segment_name` values, and call an [activation template](../custom-templates.html#label-dcr-custom-templates-activation) that follows [collaboration guidelines for custom templates](#label-dcr-design-collaboration-template). You can’t activate results by running a standard analysis template.

   CopyExpand

   ```
   api_version: 2.0.0
   spec_type: analysis
   name: my_analysis
   description: Description of the analysis
   template: my_activation_template
   template_configuration:
     view_mappings:
       source_tables:
         - alias1.schema1.table1
         - alias2.schema2.table2
     arguments:
       join_column: ip_address
       advertiser_activation_column: purchase_amount
       publisher_activation_column: device_type
     activation:
       snowflake_collaborator: publisher_1
       segment_name: q1_2025
   ```

   Show lessSee more

   Scroll to top

   Note

   Any column used in the template with the `activation_policy` filter applied must have its `activation_allowed` value set to TRUE in the data offering specification. The following example shows a template with the activation policy applied to two columns supplied by the analysis runner:

   CopyExpand

   ```
   BEGIN
     CREATE OR REPLACE TABLE cleanroom.activation_data_analysis_results AS
       SELECT count(*) AS ITEM_COUNT, c.status, c.age_band
       FROM IDENTIFIER({{ my_table[0] }}) AS c
       JOIN IDENTIFIER({{ source_table[0] }}) AS p
       ON {{ c_join_col | sqlsafe | activation_policy }} = {{ p_join_col | sqlsafe | activation_policy }}
       GROUP BY c.status, c.age_band
       ORDER BY c.age_band;
     RETURN 'analysis_results';
   END;
   ```

   Show lessSee more

   Scroll to top
2. The analysis runner calls `RUN` to run the analysis.

   * If activating to yourself, results are available immediately in the caller’s account in the table `consumers_database.ACTIVATION_RESULTS.CONSUMER_DIRECT_ACTIVATION_SUMMARY`. To learn how to view the query results, see the last step.
   * If activating to another collaborator:

     1. The collaborator calls `VIEW_ACTIVATIONS` until it returns a status of SHARED. Activating to another account can take considerable time for large result sets, as the data must be transferred to the collaborator’s account and decrypted.
     2. When the status of the activation is SHARED, the collaborator calls `PROCESS_ACTIVATION` to send the results to their account. The response to `PROCESS_ACTIVATION` includes the table and segment names. This sets the activation status to PROCESSED.
3. To retrieve query results, run the following SQL command, providing your results table name and, optionally, a segment name to filter results:

   CopyExpand

   ```
   SELECT *
     FROM <results_table_name>
       [WHERE segment = <segment_name>];
   ```

   Show lessSee more

   Scroll to top

## Leave or delete a collaboration

* Non-owners leave a collaboration by calling `COLLABORATION.LEAVE`. Any data offerings they have provided will be removed from the collaboration. You can’t rejoin a collaboration after leaving it.
* Collaboration owners can’t leave a collaboration; ownership can’t be transferred. A collaboration owner can drop a collaboration for all collaborators by calling `COLLABORATION.TEARDOWN`.

Both processes are asynchronous. You must call `GET_STATUS` to monitor the status, and call `LEAVE` or `TEARDOWN` again when `GET_STATUS` shows the status as LOCAL\_DROP\_PENDING.

## Enable Cross-Cloud Auto-Fulfillment

If you aren’t in the same cloud host region as the collaboration owner,
[Cross-Cloud Auto-Fulfillment](../../../collaboration/provider-listings-auto-fulfillment) (LAF) must be enabled for your account for you to join the collaboration. If you try to review a collaboration in another cloud region and LAF is not enabled for your account, or you don’t have proper permissions, you receive an error when you call `REVIEW` on the collaboration.

Note

When Cross-Cloud Auto-Fulfillment is used in a collaboration:

* Data is replicated into the account of each collaborator that can access that data.
* Data is also replicated into the owner’s region, whether or not they can access the data offering. However, their ability to access the data is determined by the data offering’s sharing rules.

You can determine your own cloud host region by running `SELECT CURRENT_REGION();`

**To enable Cross-Cloud Auto-Fulfillment in your account:**

1. An org admin must enable LAF on your account by calling [SYSTEM$ENABLE\_GLOBAL\_DATA\_SHARING\_FOR\_ACCOUNT](../../../sql-reference/functions/system_enable_global_data_sharing_for_account). For more information, see [Manage privileges for auto-fulfillment](../../../collaboration/provider-listings-auto-fulfillment-manage-privileges).
2. To have the proper privileges to review or join a LAF collaboration, use either SAMOOHA\_APP\_ROLE, or use a role granted the [MANAGE LISTING AUTO FULFILLMENT account-level privilege](v2-api-reference.html#label-dcr-grant-privilege-on-account-to-role).

Collaborators located in a different cloud host region will experience some additional data lag due to [replication frequency](../enabling-laf.html#label-dcr-laf-refresh-rates). The replication frequency isn’t yet configurable in a Collaboration Data Clean Room.

## Example: Two-party collaboration

The following example demonstrates a two-party collaboration, where one party (named “alice”) is the collaboration creator, a data provider for herself and bob, and an analysis runner. The other party (named “bob”) is a data provider for himself and alice, and an analysis runner.

The example demonstrates the following actions:

* Creating a collaboration.
* Registering templates and data offerings.
* Adding a template and data offering at collaboration creation time.
* Joining a collaboration.
* Adding templates and resources to an existing collaboration.
* Running an analysis.

To run this example, you must have two separate accounts with Snowflake Data Clean Rooms installed.

You can either download the files and upload them to your Snowflake account, or copy and paste the example code into worksheets in two separate accounts by using Snowsight.

File downloads“alice” code“bob” code

Download the source SQL files, and then upload them into two separate accounts that have Snowflake Data Clean Rooms installed:

* [`Collaboration owner "alice" worksheet`](../../../_downloads/162c93b64e33d38d9cdeb15a710fa8fd/demo-collaboration-hub-alice.sql)
* [`Collaboration member "bob" worksheet`](../../../_downloads/ff68e520998de6717efbfe424fdc56db/demo-collaboration-hub-bob.sql)

CopyExpand

```
-- Basic Snowflake Collaboration Data Clean Rooms example.
-- This file represents user "alice" in a two-collaborator clean room example.

-- Run this worksheet in a Snowflake account with access to the latest version of
-- Snowflake Data Clean Rooms.

-- This file demonstrates the following actions:
-- * How to register a template and a dataset
-- * How to create a collaboration with pre-registered resources.
-- * How to add a template to a collaboration that has already been created, and the
--   template approval flow.
-- * How to run an analysis.

-- This scenario involves two collaborators: bob and alice
-- bob and alice each submits one data source
-- bob and alice are data providers for themselves and each other
-- bob submits one template that only alice can use
-- alice submits one template that they can both use, and one template that only alice can use

-- For more information, read docs.snowflake.com/user-guide/cleanrooms/v2/using

USE WAREHOUSE APP_WH;
USE ROLE SAMOOHA_APP_ROLE;

-- Secondary roles must be disabled to call link_data_offerings.
USE SECONDARY ROLES NONE;

CREATE DATABASE IF NOT EXISTS ALICE_DB;
CREATE SCHEMA IF NOT EXISTS ALICE_DB.ALICE_SCH;
CREATE OR REPLACE TABLE ALICE_DB.ALICE_SCH.ALICE_DATA AS SELECT * FROM samooha_sample_database.demo.customers LIMIT 100;

-- Register a data offering to use in the initial collaboration definition.
CALL samooha_by_snowflake_local_db.registry.register_data_offering(
    $$
    api_version: 2.0.0
    spec_type: data_offering
    version: v1
    name: <alice data offering name>
    datasets:
     - alias: customer_list
       data_object_fqn: ALICE_DB.ALICE_SCH.ALICE_DATA
       object_class: custom
       allowed_analyses: template_only
       schema_and_template_policies:
         hashed_email:
           category: join_standard
           column_type: hashed_email_b64_encoded
         status:
           category: passthrough
    $$
    );
    
-- Save the ID of the registered data offering. 
SET alice_data_offering_id = '<data_offering_id>';

CALL samooha_by_snowflake_local_db.registry.view_registered_data_offerings();

-- Register a template to use in the initial collaboration definition.
CALL samooha_by_snowflake_local_db.registry.register_template(
$$
api_version: 2.0.0
spec_type: template
name: alice_only_template
version: <version_number>
type: sql_analysis
description: A test template
template:
  SELECT t1.status, COUNT(*)
    FROM IDENTIFIER( {{ source_table[0] }} ) AS t1
    JOIN IDENTIFIER( {{ source_table[1] }} ) AS t2
    ON t1.hashed_email_b64_encoded = t2.hashed_email_b64_encoded
    GROUP BY t1.status;
$$);

-- Save the ID of the registered template.
SET my_template_id = '<alice_only_template_id>';
CALL samooha_by_snowflake_local_db.registry.view_registered_templates();

-- Create a collaboration with the previously registered template and data offering.
-- The collaboration supports two collaborators, with aliases alice (this account) and bob.
-- Owner: alice
-- Analysis runners:
--   * alice, using her own data, and the template you created and registered earlier.
--   * bob, with no listed templates or data.
-- Data providers:
--   * alice and bob, for alice
--   * alice and bob, for bob
-- Resources added: The template and data offering alice registered earlier.
-- You will add more templates and data offerings to these users later. Only these
-- users are invited to the collaboration, and no additional users can be added later.
-- Replace the <...> placeholders with the appropriate values.
-- Account data sharing IDs are -- SELECT CURRENT_ORGANIZATION_NAME() || '.' || CURRENT_ACCOUNT_NAME();
CALL samooha_by_snowflake_local_db.collaboration.initialize(
$$
api_version: 2.0.0
spec_type: collaboration
name: my_first_collaboration_1_0
owner: alice
collaborator_identifier_aliases:
  alice: <my account data sharing ID>
  bob: <bob account data sharing ID>
analysis_runners:
  bob:
    data_providers:
      alice:
        data_offerings:
        - id: <alice data offering ID>
      bob:
        data_offerings: []
  alice:
    data_providers:
      alice:
        data_offerings:
        - id: <alice data offering ID>
      bob:
        data_offerings: []
    templates:
    - id: <alice only template ID>
$$,
'APP_WH'
);
SET collaboration_name = '<collaboration_name>';

-- INITIALIZE automatically joins the owner. Check status until JOINED.
CALL samooha_by_snowflake_local_db.collaboration.get_status($collaboration_name);

-- Collaboration is visible here when the owner has joined.
CALL samooha_by_snowflake_local_db.collaboration.view_collaborations();

-- Auto-approve any template requests from other collaborators that affect you.
CALL samooha_by_snowflake_local_db.collaboration.enable_template_auto_approval(
  $collaboration_name
);

-- SWITCH TO collaborator to join the collaboration and add a template
-- The template will be auto-approved.

-- Create a new template.
CALL samooha_by_snowflake_local_db.registry.register_template(
    $$
    api_version: 2.0.0
    spec_type: template
    name: both_use_template
    version: 2026_01_12_V1
    type: sql_analysis
    description: test_description
    template:
      select * from identifier({{ source_table[0] }}) limit 5;
    
    $$
);
SET both_use_template = '<template ID>';

-- Ask to add the template to the collaboration. You must ask bob, because you're
-- including bob in the sharing list. When you share a template with yourself,
-- you auto-approve it.
CALL samooha_by_snowflake_local_db.collaboration.add_template_request(
  $collaboration_name,
  $both_use_template,
  ['alice', 'bob']   -- List of collaborators who can use this template.
  );

-- SWITCH TO bob to approve the request. Request wasn't approved automatically
-- because bob didn't enable auto-approve.

-- See if bob approved the request.
CALL samooha_by_snowflake_local_db.collaboration.view_update_requests($collaboration_name);

-- See what the collaboration spec looks like now, after all the resource updates.
-- Collaboration updates are asynchronous, so if all changes that you made aren't present,
-- wait a minute or two, and then try again.
CALL samooha_by_snowflake_local_db.collaboration.view_collaborations() ->>
  SELECT "COLLABORATION_SPEC" FROM $1 WHERE "SOURCE_NAME" = $collaboration_name;

-- SWITCH TO bob to add a data offering.

-- Run an analysis.
-- Tables are scoped as <data_offering_id>.<alias>.
CALL samooha_by_snowflake_local_db.collaboration.view_data_offerings(
  $collaboration_name
);
SET $bob_data_offering = '<bob data offering ID>';

CALL samooha_by_snowflake_local_db.collaboration.view_templates(
  $collaboration_name
);

-- Run bob's template.
-- Replace the placeholders with your variables.
CALL samooha_by_snowflake_local_db.collaboration.run(
  $collaboration_name,
    $$
    api_version: 2.0.0
    spec_type: analysis
    description: <optional description of the analysis>
    template: '<alice_only_template>'
    template_configuration:
      view_mappings:
        source_tables:
          - '<alice_data_offering_view_name>'
          - '<bob_data_offering_view_name>'
    $$
  );

-- Multi-step cleanup process to delete the collaborations.
-- Doesn't delete registered resources.
CALL samooha_by_snowflake_local_db.collaboration.teardown($collaboration_name);
CALL samooha_by_snowflake_local_db.collaboration.get_status($collaboration_name);

-- When get_status reports LOCAL_DROP_PENDING, call teardown again.
CALL samooha_by_snowflake_local_db.collaboration.teardown($collaboration_name);

DROP DATABASE ALICE_DB;
```

Show lessSee more

Scroll to top

CopyExpand

```
-- Basic Snowflake Collaboration Data Clean Rooms example.
-- This file represents user "bob" in a two-collaborator clean room example.

-- Run this worksheet in a Snowflake account with access to the latest version of
-- Snowflake Data Clean Rooms.

-- This file  demonstrates the following actions:
-- * Joining a collaboration
-- * Registering and adding a template and a data offering to an existing collaboration.
-- * Running an analysis.

-- For more information, read docs.snowflake.com/user-guide/cleanrooms/v2/using

USE WAREHOUSE APP_WH;
USE ROLE SAMOOHA_APP_ROLE;

-- Secondary roles can't be active when calling join or link_data_offering.
USE SECONDARY ROLES NONE;

-- Create sample data.
CREATE DATABASE IF NOT EXISTS BOB_DB;
CREATE SCHEMA IF NOT EXISTS BOB_DB.BOB_SCH;
CREATE OR REPLACE TABLE BOB_DB.BOB_SCH.BOB_DATA AS SELECT * FROM samooha_sample_database.demo.customers_2 LIMIT 100;

-- See which collaborations you are invited to, or have joined.
CALL samooha_by_snowflake_local_db.collaboration.view_collaborations();

-- Use SOURCE_NAME column value from the response to view_collaborations().
SET collaboration_name = '<collaboration name>';

-- Use OWNER_ACCOUNT column value from the response to view_collaborations().
SET collaborator_data_sharing_id = '<collaborator_id>';

-- Review and join the collaboration.
-- Joining is asynchronous, so you must call get_status until the status is JOINED before
-- you can perform actions on the collaboration.
CALL samooha_by_snowflake_local_db.collaboration.review($collaboration_name, $collaborator_data_sharing_id);
CALL samooha_by_snowflake_local_db.collaboration.join($collaboration_name);
CALL samooha_by_snowflake_local_db.collaboration.get_status($collaboration_name);

-- Demonstrate the auto-approve flow.
-- Alice enabled auto-approve on her account, so this request will
-- be auto-approved, and the template will be added immediately.

-- Create a template.
CALL samooha_by_snowflake_local_db.registry.register_template(
    $$
    api_version: 2.0.0
    spec_type: template
    name: auto_approve_template
    version: V1
    type: sql_analysis
    description: test_description
    template:
      SELECT * FROM IDENTIFIER({{ SOURCE_TABLE[0] }}) LIMIT 10;
    $$
);
SET auto_approve_template = '<template_id>';

CALL samooha_by_snowflake_local_db.collaboration.add_template_request($collaboration_name, $auto_approve_template, ['alice', 'bob']);
CALL samooha_by_snowflake_local_db.collaboration.view_update_requests($collaboration_name);

-- SWITCH TO other account and request adding a template, and then come back to approve the request.

-- You haven't enabled template auto-approve, so you must approve the request before the template is added.
CALL samooha_by_snowflake_local_db.collaboration.view_update_requests($collaboration_name);
CALL samooha_by_snowflake_local_db.collaboration.approve_update_request(
  $collaboration_name,
  '<request_ID>'
);

-- SWITCH TO bob to see the request status.

-- Register your own data offering.
CALL samooha_by_snowflake_local_db.registry.register_data_offering(
    $$
    api_version: 2.0.0
    spec_type: data_offering
    version: v3
    name: bob_data
    datasets:
     - alias: my_customer_list
       data_object_fqn: BOB_DB.BOB_SCH.BOB_DATA
       object_class: custom
       allowed_analyses: template_only
       schema_and_template_policies:
         hashed_email:
           category: join_standard
           column_type: hashed_email_b64_encoded
         status:
           category: passthrough
    $$
);

SET my_data_id = '<data offering id>';

-- Share the data offering with yourself and alice.
CALL samooha_by_snowflake_local_db.collaboration.link_data_offering(
  $collaboration_name,
  $my_data_id,
  ['alice', 'bob']
);

CALL samooha_by_snowflake_local_db.collaboration.view_data_offerings(
  $collaboration_name
);

-- View templates that you can use in this collaboration. You can run only templates that list you in the
-- SHARED_WITH column.
CALL samooha_by_snowflake_local_db.collaboration.view_templates($collaboration_name);

-- Run an analysis with your template.
CALL samooha_by_snowflake_local_db.collaboration.run(
    $collaboration_name,
    $$
    api_version: 2.0.0
    spec_type: analysis
    description: <optional description of the analysis>
    template:  '<both_use_template>'
    template_configuration:
      view_mappings:
        source_tables:
          -  '<my_data_offering_view_name>'
          -  '<bob_data_offering_view_name>'
    $$
);
  
-- SWITCH TO other account to run an analysis.

-- Try running an analysis using alice-only template.
-- This will fail, because you aren't listed as an analysis
-- runner for this template.
CALL samooha_by_snowflake_local_db.collaboration.run(
  $collaboration_name,
  $$
  api_version: 2.0.0
  spec_type: analysis
  description: <optional description of the analysis>
  template: '<alice_only_template>'
  template_configuration:
    view_mappings:
      source_tables:
        - '<my_data_offering_view_name>'
        - '<bob_data_offering_view_name>'
  $$
);

-- Clean up resources.
DROP DATABASE BOB_DB;
```

Show lessSee more

Scroll to top
