---
title: "Collaboration Data Clean Rooms schema reference"
url: "https://docs.snowflake.com/en/user-guide/cleanrooms/v2/spec-reference"
---

# Collaboration Data Clean Rooms schema reference

[![Snowflake logo in black (no text)](../../../_images/logo-snowflake-black.png)](../../../_images/logo-snowflake-black.png) [Preview Feature](../../../release-notes/preview-features) — Open

Available to all accounts.

Required version

To use Collaboration Data Clean Rooms, you must upgrade to Snowflake Data Clean Rooms version 12.9 or later.

This topic describes the specification schemata for all collaboration resources. Specifications are shown in YAML format.

Specifications have a schema version field `api_version`. Use the API version number shown here; support for earlier schema versions isn’t guaranteed.

**Current DCR Collaboration API version:** 2.0.0

## Collaboration specification

Defines the high-level collaboration. The specification defines which analysis runners are invited, and for each analysis runner, which data and templates they can access and run. Any templates or data offerings that are listed here must be registered before they are included in the collaboration specification.

The owner submits this definition by calling `COLLABORATION.INITIALIZE`.

**Schema:**

CopyExpand

```
api_version: 2.0.0              # Required: Must be "2.0.0"
spec_type: collaboration        # Required: Must be "collaboration"
name: <collaboration_name>      # Required: Unique name (max 75 chars)
version: <version_string>       # Optional: Version identifier (max 20 chars)
description: <collaboration_description>  # Optional: Description (max 1,000 chars)
owner: <owner_alias>            # Optional: Alias of owner

collaborator_identifier_aliases:  # Required: Map aliases to account identifiers
  <alias_1>: <account_identifier_1>  # One or more alias mappings...

analysis_runners:               # Required: Who can run analyses
  <analysis_runner_alias>:      # One or more analysis runner definitions...
    data_providers:             # Required: Data providers for this runner
      <provider_alias>:         # One or more provider definitions...
        data_offerings:         # Required: List of offerings (can be empty [])
          - id: <data_offering_id>  # Zero or more data offering IDs...
    templates:                  # Optional: Templates this runner can use
      - id: <template_id>       # One or more template IDs...
    activation_destinations:    # Optional: Where results can be sent
      snowflake_collaborators:  # Optional: Collaborators who can receive results
        - <collaborator_alias>  # One or more collaborator aliases...
```

Show lessSee more

Scroll to top

`api_version`
:   The version of the Collaboration API used. Must be `2.0.0`.

`spec_type`
:   Specification type identifier. Must be `collaboration`.

`name: collaboration_name`
:   User-friendly name for this collaboration. Must be unique in the creator’s account and follow [Snowflake identifier rules](../../../sql-reference/identifiers-syntax) (maximum 75 characters).

`version` (*Optional*)
:   A version identifier for this collaboration (maximum 20 characters). Must follow [Snowflake identifier rules](../../../sql-reference/identifiers-syntax). A good format to use is *YYYY\_MM\_DD\_V#*. For example: `2025_10_22_V1`.

`description: collaboration_description` (*Optional*)
:   A human-readable description of the collaboration (maximum 1,000 characters), for collaborators to read.

`owner: owner_alias` (*Optional*)
:   Alias or [Data Sharing Account Identifier](../../admin-account-identifier.html#label-account-name-data-sharing) of the collaboration owner. If not specified, the
    account that registers this spec will be assigned as the owner.

`collaborator_identifier_aliases`
:   A mapping of collaborator aliases to their [Data Sharing Account Identifiers](../../admin-account-identifier.html#label-account-name-data-sharing). Only users listed here can participate in the collaboration. Use the aliases defined here to refer to all collaborators, rather than using their data sharing account identifier directly.

`analysis_runners`
:   Describes who can run an analysis in this collaboration. Each analysis runner is keyed by a unique alias. You must allow at least one account to run an analysis in this collaboration.

    `<analysis_runner_alias>`
    :   Alias of account that can run an analysis in this collaboration. Alias is defined in the `collaborator_identifier_aliases` list.

    `data_providers`
    :   Data providers whose data this analysis runner can access. Each provider is keyed by the alias that is defined in `collaborator_identifier_aliases`.

        `data_offerings`
        :   A list of data offerings from this data provider that the analysis runner can access, or an empty array `[]`. If items are present,
            each item has the following properties:

            * `data_offering_id`: The reference ID for this data offering, generated when the data provider calls `REGISTRY.REGISTER_DATA_OFFERING`.

    `templates` (*Optional*)
    :   The templates that can be used by this analysis runner. Each template is referenced by its ID.

    `activation_destinations` (*Optional*)
    :   Defines activation settings for the analysis results.

        `snowflake_collaborators` (*Optional*)
        :   List of collaborators who can receive activated analysis results. Use the alias from the `collaborator_identifier_aliases` list in this spec. All collaborators listed here must have the permissions described in [Activate query results](using.html#label-dcr-collaboration-activating-results).

### Examples

CopyExpand

```
api_version: 2.0.0
spec_type: collaboration
name: my_sample_collaboration
owner: Owner
collaborator_identifier_aliases:
  Owner: ENG.OWNER
  AnalysisRunner_1: ENG.CONSUMER_1
  DataProvider_1: ENG.PROVIDER_1
  DataProvider_2: ENG.PROVIDER_2
  AnalysisRunner_2: ENG.PROVIDER_3
analysis_runners:
  AnalysisRunner_1:
    data_providers:
      DataProvider_1:
        data_offerings:
        - id: DCR_PREPROD_CI_PROVIDER_ANY_NAME_ZUDFTMULHQ_iuDfn_v0
      DataProvider_2:
        data_offerings: []
    templates:
    - id: test_sca_three_party_template_JOaVG_v0
  AnalysisRunner_2:
    data_providers:
      DataProvider_2:
        data_offerings: []
    templates:
    - id: test_sca_three_party_template_JOaVG_v0
```

Show lessSee more

Scroll to top

## Data offering specification

Defines a set of tables that a provider is willing to share with analysis runners, as well as sharing rules, such as policies, column formats, and whether the table must be used with a template.

The data provider submits this definition by calling `REGISTRY.REGISTER_DATA_OFFERING`, which returns an offering ID that can be used in the collaboration definition.

A data offering won’t be available in a collaboration until the account that registered the data offering joins the collaboration.

You must have the REGISTER DATA OFFERING account privilege to join any collaboration in which you can activate data; that is, you are an analysis runner and the collaboration specification includes an `activation_destinations` field. For more information, see the [access management API reference guide](v2-api-reference.html#label-dcr-collaboration-access-management-api).

**Schema:**

CopyExpand

```
api_version: 2.0.0              # Required: Must be "2.0.0"
spec_type: data_offering        # Required: Must be "data_offering"
name: <data_offering_name>      # Required: Unique name (max 75 chars)
version: <version_string>       # Required: Version identifier (max 20 chars)
description: <data_offering_description>  # Optional: Description (max 1,000 chars)

datasets:                       # Required: Tables to share
  - alias: <dataset_name>       # One or more dataset items...
    data_object_fqn: <database.schema.table_name>  # Required: Fully-qualified table name
    allowed_analyses: <allowed_analysis_type>      # Required: template_only or template_and_freeform_sql
    object_class: <object_class>    # Optional: ads_log or custom
    schema_and_template_policies:   # Required: Column definitions
      <column_name>:                # One or more column definitions...
        category: <category_type>   # Required: join_standard, join_custom, timestamp, or passthrough
        column_type: <format_type>  # Required for join_standard category, omitted for other categories.
        activation_allowed: <true_or_false>  # Optional: Whether column can be used for activation
    freeform_sql_policies:      # Optional: Policies for freeform SQL queries
      aggregation_policy:       # Optional: Single aggregation policy
        name: <fully_qualified_policy_name>
        entity_keys:            # Optional: Entity key columns
          - <column_name>       # One or more column names...
      join_policy:              # Optional: Single join policy
        name: <fully_qualified_policy_name>
        columns:                # Optional: Columns this policy applies to
          - <column_name>       # One or more column names...
      masking_policies:         # Optional: Masking policies
        - name: <fully_qualified_policy_name>  # One or more masking policy items...
          columns:              # Optional: Columns this policy applies to
            - <column_name>     # One or more column names...
      projection_policies:      # Optional: Projection policies
        - name: <fully_qualified_policy_name>  # One or more projection policy items...
          columns:              # Optional: Columns this policy applies to
            - <column_name>     # One or more column names...
      row_access_policy:        # Optional: Row access policies
        - name: <fully_qualified_policy_name>  # One or more row access policy items...
          columns:              # Optional: Columns this policy applies to
            - <column_name>     # One or more column names...
    require_freeform_sql_policy: <true_or_false>  # Optional: Require a policy for freeform SQL
```

Show lessSee more

Scroll to top

`api_version`
:   The version of the Collaboration API used. Must be `2.0.0`.

`spec_type`
:   Specification type identifier. Must be `data_offering`.

`version`
:   A custom version identifier for this data offering specification (maximum 20 characters). Must follow [Snowflake identifier rules](../../../sql-reference/identifiers-syntax). The version string is given its own column in the response to `VIEW_DATA_OFFERINGS` and `VIEW_REGISTERED_DATA_OFFERINGS`, so use a value that can be sorted by increasing value. Example: `V0`

`name: data_offering_name`
:   A name for a set of tables and columns to expose to collaborators. This name is used as the data offering reference value in a collaboration definition. You can create multiple data offerings with overlapping tables and columns for different use cases. Must follow [Snowflake identifier rules](../../../sql-reference/identifiers-syntax) with a maximum of 75 characters and be unique within your Snowflake data clean room account.
    The `name_version` pair must be unique for all data offerings in this account.

`description: data_offering_description` (*Optional*)
:   A description of the data offering (maximum 1,000 characters).

`datasets`
:   A list of one or more datasets to make available to the collaboration.

    `alias: dataset_name`
    :   A name for this data object, used in `collaboration.run`. Must follow [Snowflake identifier rules](../../../sql-reference/identifiers-syntax) and be unique within this offering.

    `data_object_fqn: fully_qualified_table_name`
    :   Describes a single table available to collaborators. Provide the fully-qualified name of the source object in your account (`database.schema.table_name`). Maximum length is 773 characters.

    `allowed_analyses: allowed_analysis_type`
    :   The type of analyses that collaborators can run against this table. Required field with the following values:

        * `template_only`: The analysis runner can query this table only by using a template listed in the collaboration definition.
        * `template_and_freeform_sql`: The analysis runner can query this table by using either a template listed in the collaboration definition, or by using [free-form SQL queries](using.html#label-dcr-collab-enable-free-form-queries) in a code environment.

    `object_class` (*Optional*)
    :   The type of object. One of the following values:

        * `ads_log`: The tables and columns listed here must fit the ad log requirements.
        * `custom`: A custom set of tables and columns that doesn’t have any special requirements.

    `schema_and_template_policies`
    :   Provide a list of column names from the table listed by `data_object_fqn` and define the policies and format of each column. Only columns listed here are available to collaborators. Each column has the following descriptors:

        `category: category_type`
        :   The category determines whether any column renaming is applied, and any data format enforcement that should be applied.
            `category` and `column_type` [determine the column name exposed to the analysis runner](using.html#label-dcr-source-column-renaming). The following values are supported:

            * `join_standard`: This is a joinable column with data in a format specified in the `column_type` field. This column is renamed to the `column_type` value in the shared data offering.
            * `join_custom`: This is a joinable column in any format. Use this when there isn’t an appropriate `column_type` for your join column. The original column name is used in the shared data offering.
            * `timestamp`: This is a projectable column that specifies a timestamp for any event. The column is renamed as `timestamp` in the shared data offering.
            * `passthrough`: This is a projectable column of any other type. The original column name is used in the shared data offering.

        `column_type: <format_type>` (*Required when category=join\_standard, ignored for other category types*)
        :   The format of the data. If the data doesn’t conform to this format, your call to `REGISTER_DATA_OFFERING` will fail. Provide this field for columns where `category = join_standard`. `category` and `column_type` [determine the column name exposed to the analysis runner](using.html#label-dcr-source-column-renaming). You can’t assign the same `column_type` value to multiple columns in the same table. The following format types are supported:

            * `email`: A raw email address.
            * `hashed_email_sha256`: A SHA256 hashed email.
            * `hashed_email_b64_encoded`: A base64-encoded hashed email.
            * `phone`: A phone number without punctuation. For example: `2015551212`.
            * `hashed_phone_sha256`: A SHA256 hashed phone number. The original number should be in the `phone` format.
            * `hashed_phone_b64_encoded`: A base64-encoded hashed phone number.
            * `device_id`: A raw device ID, such as a mobile advertising ID or a CTV device ID.
            * `hashed_device_id_sha256`: SHA256 hashed device ID. The original should be in the `device_id` format.
            * `hashed_device_b64_encoded`: A base64-encoded hashed device ID.
            * `ip_address`: A raw IP address in IPv4 format.
            * `hashed_ip_address_sha256`: SHA256 hashed IPv4 address. The original should be in the `ip_address` format.
            * `hashed_ip_address_b64_encoded`: A base64-encoded hashed IP address.

        `activation_allowed` (*Optional*)
        :   Whether this column can be used for activation purposes. Default is `false`.

> `freeform_sql_policies` (*Optional*)
> :   If `allowed_analyses` is `template_and_freeform_sql`, this optional field lists any Snowflake policies that should be applied in free-form SQL queries run on this data offering. For more information, see [Apply the policy on the data offering (free-form query usage only)](using.html#label-dcr-collab-freeform-sql-policies).

> > The following types are supported:
> >
> > `aggregation_policy` (*Optional*)
> > :   A single [aggregation policy](../../aggregation-policies) configuration.
> >
> >     * `name`: The fully-qualified policy name.
> >     * `entity_keys` (*Optional*): List of column names that serve as entity keys for the aggregation policy.
> >
> > `join_policy` (*Optional*)
> > :   A single [join policy](../../join-policies) configuration.
> >
> >     * `name`: The fully-qualified policy name.
> >     * `columns` (*Optional*): List of column names this policy applies to.
> >
> > `masking_policies` (*Optional*)
> > :   An array of [masking policy](../../security-column-intro) configurations.
> >
> >     * `name`: The fully-qualified policy name.
> >     * `columns` (*Optional*): List of column names this policy applies to.
> >
> > `projection_policies` (*Optional*)
> > :   An array of [projection policy](../../projection-policies) configurations.
> >
> >     * `name`: The fully-qualified policy name.
> >     * `columns` (*Optional*): List of column names this policy applies to.
> >
> > `row_access_policy` (*Optional*)
> > :   An array of [row access policy](../../security-row-intro) configurations.
> >
> >     * `name`: The fully-qualified policy name.
> >     * `columns` (*Optional*): List of column names this policy applies to.
>
> `require_freeform_sql_policy` (*Optional*)
> :   Whether this data source must have a `freeform_sql_policies` defined. This is used as a failsafe to prevent adding a data source that supports free-form SQL queries without assigning policies to it.

## Template resource specification

Defines a single template in a collaboration. Sent to `REGISTRY.REGISTER_TEMPLATE` to register a template to use in a collaboration.

**Schema:**

CopyExpand

```
api_version: 2.0.0              # Required: Must be "2.0.0"
spec_type: template             # Required: Must be "template"
name: <template_name>           # Required: Unique name (max 75 chars)
version: <version_string>       # Required: Version identifier (max 20 chars)
type: <template_type>           # Required: sql_analysis or sql_activation
description: <template_description>  # Optional: High-level description (max 1,000 chars)
methodology: <methodology_description>  # Optional: Detailed description (max 1,000 chars)

parameters:                     # Optional: User-provided parameters
  - name: <parameter_name>      # One or more parameter items...
    description: <parameter_description>  # Optional: Description (max 500 chars)
    required: <true_or_false>   # Optional: Whether required (default: false)
    default: <default_value>    # Optional: Default value
    type: <data_type>           # Optional: String, integer, number, Boolean, array, or object

template: |                     # Required: JinjaSQL template content
  <template_content>
```

Show lessSee more

Scroll to top

`api_version`
:   The version of the Collaboration API used. Must be `2.0.0`.

`spec_type`
:   Specification type identifier. Must be `template`.

`name`
:   A unique, user-friendly name for this template. Must follow [Snowflake identifier rules](../../../sql-reference/identifiers-syntax) with a maximum of 75 characters.
    The `name_version` pair must be unique for all templates in this account.

`version`
:   A version identifier for this template (maximum 20 characters). Must follow [Snowflake identifier rules](../../../sql-reference/identifiers-syntax). The version string is given its own column in the response to `VIEW_TEMPLATES` and `VIEW_REGISTERED_TEMPLATES`, so use a value that can be sorted by increasing value. Example: `V0`

`type`
:   The template type. One of the following values:

    * `sql_analysis`: Template for data analysis operations.
    * `sql_activation`: Template for data activation operations.

`description: template_description` (*Optional*)
:   A high-level description of what this template does (maximum 1,000 characters).

`methodology: methodology_description` (*Optional*)
:   A more detailed description of how this template works (maximum 1,000 characters).

`parameters` (*Optional*)
:   The list of all user-provided parameters in this template. Each item can have the following fields:

    * `name`: Parameter name as a valid [Snowflake identifier](../../../sql-reference/identifiers-syntax).
    * `description` (*Optional*): Human-readable description of the parameter (maximum 500 characters).
    * `required` (*Optional*): Whether or not the parameter is required. Default is `false`.
    * `default` (*Optional*): Default value for the parameter, which can be any data type.
    * `type` (*Optional*): Expected data type of the parameter. One of: `string`, `integer`, `number`, `boolean`, `array`, or `object`.

`template`
:   The template content. For SQL templates, this contains the [JinjaSQL template](../custom-templates).
    For more information, see [Template design for a collaboration](using.html#label-dcr-design-collaboration-template).

    The column names exposed to the template are determined by the `category` and `column_type` values for the column in the [data offering definition](#label-dcr-collaboration-data-yaml). For more information, see [Source column renaming](using.html#label-dcr-source-column-renaming).

**Example:**

CopyExpand

```
api_version: 2.0.0
spec_type: template
name: trivial_template
version: V1
type: sql_analysis
description: Simple one-row template.
methodology: Always returns "1". Requires one source table.

parameters:
  - name: row_count
    description: Count of rows
    required: true

template: |
    SELECT 1 FROM IDENTIFIER( {{ source_table[0] }} ) LIMIT {{ row_count }};
```

Show lessSee more

Scroll to top

## Analysis request specification

Specifies all the information that analysis runners need to run an analysis, including which template to use, which tables to pass to the template, and any variable values used by a template. If not using free-form SQL to query data, any analysis runners that want to run an analysis use this specification to define the template and input data.

**Schema:**

CopyExpand

```
api_version: 2.0.0              # Required: Must be "2.0.0"
spec_type: analysis             # Required: Must be "analysis"
template: <template_id>         # Required: ID of the template to use
name: <analysis_name>           # Optional: Unique name (max 75 chars)
version: <version_string>       # Optional: Version identifier (max 20 chars)
description: <analysis_description>  # Optional: Description (max 1,000 chars)

template_configuration:         # Optional: Values used when running the template
  view_mappings:                # Optional: Mappings for shared data
    source_tables:              # Optional: Tables from data offerings. Populates the source_table array variable.
      - <source_table_name>     # One or more source table names...
    <argument_name>: <view_name>  # Custom argument to template view name mapping
  local_view_mappings:          # Optional: Mappings for local data
    my_tables:                  # Optional: Tables from local data offerings. Populates the my_table array variable.
      - <my_table_name>         # One or more local table names...
    <argument_name>: <view_name>  # Custom argument to local template view name mapping
  arguments:                    # Optional: Template arguments as key-value pairs
    <argument_name>: <argument_value>  # One or more argument key-value pairs...
  activation:                   # Required for activation templates
    snowflake_collaborator: <alias>  # Collaborator alias for activation destination
    segment_name: <segment_name>     # Unique segment name for this activation
```

Show lessSee more

Scroll to top

`api_version`
:   The version of the Collaboration API used. Must be `2.0.0`.

`spec_type`
:   Specification type identifier. Must be `analysis`.

`template: template_name`
:   The ID of the template to use for this analysis, as defined in a [template YAML](#label-dcr-collaboration-template-yaml). Must follow [Snowflake identifier rules](../../../sql-reference/identifiers-syntax) with a maximum of 75 characters.

`name` (*Optional*)
:   A unique, user-friendly name for this analysis. Must follow [Snowflake identifier rules](../../../sql-reference/identifiers-syntax) with a maximum of 75 characters and be unique within your Snowflake data clean room account.

`version` (*Optional*)
:   A version identifier for this analysis specification (maximum 20 characters). Must follow [Snowflake identifier rules](../../../sql-reference/identifiers-syntax) and be unique within your account for this analysis name. A good format to use is *YYYY\_MM\_DD\_V#*. For example: `2025_10_22_V1`.

`description` (*Optional*)
:   A high-level description of what this analysis does (maximum 1,000 characters).

`template_configuration` (*Optional*)
:   Values used when running the specified template.

    `view_mappings` (*Optional*)
    :   Mapping of argument names to template view names for shared data offerings.

        `source_tables` (*Optional*)
        :   List of table names to populate the `source_table` template variable. Use the table aliases specified in the data offering spec. You can get a list of available tables by calling `COLLABORATION.VIEW_DATA_OFFERINGS`. Format of each entry is `collaborator alias.data offering ID.dataset alias`.

        `argument_name: view_name`
        :   Custom mapping of an argument name to a template view name (maximum 255 characters each).

    `local_view_mappings` (*Optional*)
    :   Mapping of argument names to local template view names for private datasets.

        `my_tables` (*Optional*)
        :   List of table names to populate the `my_table` template variable. This is available only to private datasets that you linked by calling `LINK_LOCAL_DATA_OFFERING`. Format of each entry is `collaborator alias.data offering ID.dataset alias`.

        `argument_name: view_name`
        :   Custom mapping of an argument name to a local template view name (maximum 255 characters each).

    `arguments` (*Optional*)
    :   Template arguments as key-value pairs. Argument values can be strings, numbers, Booleans, arrays, or objects depending on the template requirements.

    `activation` (*Required for activation templates*)
    :   Activation-specific configuration required when running activation templates.

        `snowflake_collaborator`
        :   Collaborator alias for the activation destination (maximum 25 characters). Must match an alias defined in the `collaborator_identifier_aliases` section of the collaboration specification, and the collaborator must be listed in the `activation_destinations` section.

        `segment_name`
        :   Unique segment name for this activation (maximum 255 characters). Used to identify and track activation results. Must follow [Snowflake identifier rules](../../../sql-reference/identifiers-syntax).
