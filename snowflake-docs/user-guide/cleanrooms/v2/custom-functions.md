---
title: "Upload and use custom functions in Collaboration Clean Rooms"
url: "https://docs.snowflake.com/en/user-guide/cleanrooms/v2/custom-functions"
---

# Upload and use custom functions in Collaboration Clean Rooms

[![Snowflake logo in black (no text)](../../../_images/logo-snowflake-black.png)](../../../_images/logo-snowflake-black.png) Preview Feature — Private

Support for this feature is currently not in production and is available only to selected accounts.

Required version

To use Collaboration Data Clean Rooms, you must upgrade to Snowflake Data Clean Rooms version 12.9 or later.

## Introduction

Any collaborator can upload custom Python UDFs and UDTFs into a collaboration. Templates in the collaboration can run these functions to perform complex data actions. Common usage includes machine learning or customized data manipulation within a query. Your uploaded code can import and use packages from an [approved bundle of Python packages](https://repo.anaconda.com/pkgs/snowflake/)
and the [Snowpark API packages](../demo-flows/snowpark.html#label-dcr-snowpark-udf).

Note

Python is the only coding language supported for custom UDFs.

The following sections show you how to upload and use a custom function.

## Define and use custom code bundles

Here is how to upload and use a custom function:

**The code submitter:**

1. [Creates and registers the code](#label-dcr-collaboration-create-and-register-code-bundle) by calling [REGISTER\_CODE\_SPEC](#label-dcr-collab-register-code-spec).

   The code can be inline in the spec, or [linked from a stage](#label-dcr-collab-code-bundles-staged-artifacts).
2. [Creates a template that references the code bundle](#label-dcr-collaboration-create-and-register-code-bundle-template) spec by ID in the template’s `code_specs` array. Add this field as a peer of the template and parameters fields as shown in this example:

   CopyExpand

   ```
    parameters:
      - name: <parameter_name>
        description: <parameter_description>
        required: <true_or_false>
        default: <default_value>
        type: <data_type>

    code_specs:             # Optional: List of code bundles used by this template
    - <code_spec_id>        # One or more code spec IDs.

    template: |
      <template_content>
   ```

   Show lessSee more

   Scroll to top
3. Registers the template and then [links the template into the collaboration](#label-dcr-collaboration-code-bundle-link-template).

   Note

   Snowflake scans uploaded code for security issues. If a security issue is found, the code, and the containing template, will not be added to the collaboration.

**The analysis runner:**

1. Runs the template in the standard way by calling `RUN`.

### Create and register the code bundle spec

The first step in uploading custom code is to create and register the code bundle spec.

Custom functions are defined in a YAML code bundle spec. Each code bundle exposes one or more functions that can be called by a template. The code bundle spec can either include the code in the spec inline, or [link to code that lives on a Snowflake stage](#label-dcr-collab-code-bundles-staged-artifacts).

A collaborator registers a spec by calling `REGISTRY.REGISTER_CODE_SPEC`, which returns the bundle ID. Any collaborator with any role can register and link a code bundle.

After the code bundle is linked into the collaboration, that code bundle is visible to anyone in the collaboration who can access a template that links the code bundle. Call `VIEW_CODE_SPECS` to list accessible code bundles in a collaboration.

Anyone who can see a code bundle in a collaboration can see and use it in their own templates in that collaboration. Any inline code can be viewed by any member of the collaboration, but staged artifact code can’t be viewed by collaborators.

The following code bundle spec that exposes a single Python UDF called `normalize_value`, which calls the `normalize` function defined in that spec:

CopyExpand

```
CALL SAMOOHA_BY_SNOWFLAKE_LOCAL_DB.REGISTRY.REGISTER_CODE_SPEC(
  $$
  api_version: 2.0.0
  spec_type: code_spec
  name: custom_udf
  version: v1
  functions:
    - name: normalize_value
      type: UDF
      language: PYTHON
      handler: normalize
      arguments:
        - name: value
          type: FLOAT
      returns: FLOAT
      code_body: |
        def normalize(value):
            return value / 100.0
  $$
);
```

Show lessSee more

Scroll to top

### Create and register the calling template

After the code spec is registered, the collaborator then registers a template that uses this code bundle. To use a code bundle, add the bundle spec ID in the template’s `code_specs` field.

A template calls a custom function using the syntax `cleanroom.spec_name$function_name`. Note the literal `.` and `$` name scoping marks.

Note

Use the spec name, not the spec ID, to reference a function in your template.

In the following example, a template uses function `normalize_value` from the code bundle `custom_udf`:

CopyExpand

```
CALL SAMOOHA_BY_SNOWFLAKE_LOCAL_DB.REGISTRY.REGISTER_TEMPLATE(
  $$
  api_version: 2.0.0
  spec_type: template
  name: normalization_template
  version: v1
  type: sql_analysis
  code_specs:
    - custom_udf_v1  -- Imports the code bundle.
  template: |
    SELECT cleanroom.custom_udf$normalize_value(metric, 0, 100)  -- Calls the UDF.
      AS normalized
        FROM {{ source_tables[0] }}
  $$
);
```

Show lessSee more

Scroll to top

### Add the template to a collaboration

Add the template that calls your function to the collaboration in the standard way. For more information, see [Templates](using.html#label-dcr-collaboration-add-templates-to-collaboration).

Snowflake validates and uploads to the collaboration when the calling template is added to a collaboration. Snowflake scans uploaded code for security issues before installing the code.

The following example shows a request to add a template to an existing collaboration:

CopyExpand

```
CALL SAMOOHA_BY_SNOWFLAKE_LOCAL_DB.COLLABORATION.ADD_TEMPLATE_REQUEST(
  'my_collaboration',
  'normalization_template_v1',
  ['consumer']
);
```

Show lessSee more

Scroll to top

### Submit a new version of your code bundle

Every registered code spec must have a unique name + version across all registries in your account. A template loads a specific name and version of a code spec. If you want to create or consume a new version of your code, you must submit a new version of the template that references the new code version in the code\_specs field. You don’t need to change the template body. For example:

**Step 1:** Consume version 1 of the code bundle:

CopyExpand

```
CALL SAMOOHA_BY_SNOWFLAKE_LOCAL_DB.REGISTRY.REGISTER_TEMPLATE(
  $$
  api_version: 2.0.0
  spec_type: template
  name: normalization_template
  version: v1
  type: sql_analysis
  code_specs:
    - custom_udf_v1  -- Bundle ID includes the version number.
  template: |
    SELECT cleanroom.custom_udf$normalize_value(metric, 0, 100)  -- Calls the UDF.
      AS normalized
        FROM {{ source_tables[0] }}
  $$
);
```

Show lessSee more

Scroll to top

**Step 2:** Update and register the new version of your code bundle, and then update your template to use the new version:

CopyExpand

```
CALL SAMOOHA_BY_SNOWFLAKE_LOCAL_DB.REGISTRY.REGISTER_TEMPLATE(
  $$
  api_version: 2.0.0
  spec_type: template
  name: normalization_template
  version: v2        -- Update the template version.
  type: sql_analysis
  code_specs:
    - custom_udf_v2  -- Use the new code bundle.
  template: |
    SELECT cleanroom.custom_udf$normalize_value(metric, 0, 100)  -- No change needed here.
      AS normalized
        FROM {{ source_tables[0] }}
  $$
);
```

Show lessSee more

Scroll to top

Notice that function names don’t include the version, so you don’t need to change the calling code in the template body when you upload a new version of a function.

## Code bundle specification

This specification defines a bundle of one or more code functions or procedures that can be called by a template.

A code bundle spec has a maximum of 5 functions plus procedures.

Identifiers in the code bundle spec have the following general requirements:

* **Names**: Must be valid [Snowflake identifiers](../../../sql-reference/identifiers-syntax) that start with a letter and contain only alphanumeric characters and underscores.
* **Quoted identifiers**: Double-quoted identifiers are supported for names with special characters.
* **Case sensitivity**: Unquoted identifiers are case-insensitive; quoted identifiers preserve case.

CopyExpand

```
api_version: 2.0.0              # Required: Must be "2.0.0"
spec_type: code_spec            # Required: Must be "code_spec"
name: <identifier>              # Required: Unique name of this code bundle.
version: <version_id>           # Required: Alphanumeric with underscores (max 20 chars)
description: <description_text> # Optional: Description (max 1,000 chars)

artifacts:                      # Optional: Staged files for import
  - alias: <identifier>         # One or more artifact items...
    stage_path: <stage_path>    # Required: Full stage path. See below for additional requirements.
    description: <description_text>  # Optional: Description (max 500 chars)
    content_hash: <sha256_hash>      # Optional: SHA-256 hash for integrity verification

functions:                      # Required if no procedures defined
  - name: <identifier>          # One or more functions...
    type: UDF | UDTF            # Required: Function type
    language: PYTHON            # Required: Currently only PYTHON supported
    runtime_version: <python_version>  # Optional: Python runtime (3.10 - 3.14)
    handler: <handler>          # Required: Handler function
    arguments:                  # Optional: One or more function arguments
      - name: <arg_name>        # Argument name
        type: <sql_type>        # Snowflake SQL type of this argument
    returns: <sql_type>         # Required: Snowflake return type
    packages:                   # Optional: Package dependencies
      - <package_name>          # One or more package items...
    imports:                    # Optional: Artifact aliases to import
      - <artifact_alias>        # One or more import items...
    code_body: |                # Optional: Inline Python code (max 12 MB)
      <inline_python_code>
    description: <description_text>  # Optional: Description of this function.

procedures:                     # Required if no functions defined
  - name: <identifier>          # One or more procedure items...
    language: PYTHON            # Required: Currently only PYTHON supported
    runtime_version: <python_version>  # Optional: Python runtime version
    handler: <handler>          # Required: Handler function
    arguments:                  # Optional: One or more procedure arguments
      - name: <arg_name>        # Argument name
        type: <sql_type>        # Snowflake SQL type of this argument
    returns: <sql_type>         # Optional: Return type
    packages:                   # Optional: Package dependencies
      - <package_name>          # One or more package items...
    imports:                    # Optional: Artifact aliases to import
      - <artifact_alias>        # One or more import items...
    code_body: |                # Optional: Inline Python code
      # inline python_code ...
    description: <description_text>  # Optional: Description of this procedure.
```

Show lessSee more

Scroll to top

`api_version`
:   The version of the Collaboration API used. Must be `2.0.0`.

`spec_type`
:   Specification type identifier. Must be `code_spec`.

`name: identifier`
:   A unique name for this code bundle spec within this registry. Must be a valid [Snowflake identifier](../../../sql-reference/identifiers-syntax) with a maximum of 75 characters. This is used as the last name segment when calling the function in a template: `cleanroom.code_spec_name$function_name`

`version: version_id`
:   Custom version identifier. Must be alphanumeric with underscores, maximum 20 characters.

`description: description_text` (*Optional*)
:   A description of the code bundle spec (maximum 1,000 characters).

`artifacts` (*Optional*)
:   A list of staged files or packages that can be imported by your functions or procedures, and [optionally exposed via handler functions](#label-dcr-collab-code-bundles-staged-artifacts). Maximum of 5 per spec.

    `alias: identifier`
    :   An alias for referencing this artifact in imports. When referencing this alias within this spec, use the bare alias name rather than `cleanroom.spec_name$alias`; that is, use the bare function name to reference another function in this spec.

    `stage_path: stage_path`
    :   Full stage path to the artifact file; For example, `@DB.SCHEMA.STAGE/path/file.whl`.

    * **The stage must be internal.** External stages are not supported.
    * **The stage must have DIRECTORY enabled**: The stage containing artifacts must have `DIRECTORY = TRUE` set.
    * **Stage path format**: Must follow `@[DB.]SCHEMA.STAGE/path/to/file.ext` format.
    * **No path traversal**: Stage paths can’t contain `..` or `\`.
    * **This artifact must exist**: The file must exist at the specified stage path when the code bundle is registered.
    * **The stage must have SNOWFLAKE\_SSE server-side encryption enabled.** When creating or altering the stage, set `ENCRYPTION = (TYPE = 'SNOWFLAKE_SSE')`.
    * **If you push, delete, or update a staged code file,** you must call `ALTER STAGE stage name REFRESH` to ensure that the collaboration has the latest information from the stage. Code updates are supported only before you register the code spec, as this is when the version is assigned and the hash checksum calculated.

    `description: description_text` (*Optional*)
    :   A description of the artifact (maximum 500 characters).

    `content_hash: sha256_hash` (*Optional*)
    :   SHA-256 hash for integrity verification (64 hex characters).

`functions` (*Required if no procedures are defined*)
:   A list of UDF or UDTF definitions.

    `name identifier`
    :   The function name to expose to the calling template. Must be a valid [Snowflake identifier](../../../sql-reference/identifiers-syntax).

    `type`
    :   The function type. One of `UDF` or `UDTF`.

    `language`
    :   The function language. Currently only `PYTHON` is supported.

    `runtime_version: python_version` (*Optional*)
    :   Python runtime version to use. Supported versions: `3.10` to `3.14`.

    `handler: handler`
    :   The name of the handler function in the function code to call when `name` is called.

    `arguments` (*Optional*)
    :   Function arguments as a list of name-type pairs. Types must be valid Snowflake SQL types.

    `returns: sql_type`
    :   The return type. For UDFs, use a SQL type such as `STRING` or `FLOAT`. For UDTFs, use `TABLE(column_definitions)`.

    `packages` (*Optional*)
    :   A list of packages used by this code. This can be any of [these Anaconda Python packages](https://repo.anaconda.com/pkgs/snowflake/) or [these Snowpark API packages](../demo-flows/snowpark.html#label-dcr-snowpark-udf). For example: `snowflake-snowpark-python`, `numpy`.

    `imports` (*Optional*)
    :   A list of artifacts to import. These must be aliases from the artifacts list in this spec.

    `code_body` (*Optional*)
    :   Inline Python code. Mutually exclusive with staged imports. Maximum size is 12 MB.

    `description: description_text` (*Optional*)
    :   A description of the function (maximum 500 characters).

`procedures` (*Required if no functions defined*)
:   A list of stored procedure definitions. Fields are similar to `functions`, except there is no `type` field.

## API reference

The following procedures are used to manage custom code bundles in a collaboration:

### REGISTER\_CODE\_SPEC

Schema:
:   REGISTRY

Registers a code bundle. This stores the code in the clean rooms environment in the REGISTRY.CODE\_SPECS table. After a code spec is registered, it can be used by a template.

Every code spec registered must have a unique name + version across all registries in your account.

#### Syntax

CopyExpand

```
REGISTER_CODE_SPEC( ['<registry_name>' ,] <code_spec> )
```

Show lessSee more

Scroll to top

#### Arguments

`registry_name` *(Optional)*
:   Name of a [custom registry](registries) in which to register this code spec. If not specified, registers the code bundle in the default account registry.

`code_spec`
:   Code bundle spec definition in YAML format, as a string.

#### Returns

The generated code bundle spec ID.

#### Examples

Register a code bundle in the default registry:

CopyExpand

```
CALL SAMOOHA_BY_SNOWFLAKE_LOCAL_DB.REGISTRY.REGISTER_CODE_SPEC(
  $$
  api_version: 2.0.0
  spec_type: code_spec
  name: custom_udf
  version: v1
  description: Custom UDF for data normalization

  functions:
    - name: normalize_value
      type: UDF
      language: PYTHON
      runtime_version: "3.10"
      handler: normalize
      arguments:
        - name: value
          type: FLOAT
        - name: min_val
          type: FLOAT
        - name: max_val
          type: FLOAT
      returns: FLOAT
      code_body: |
        def normalize(value, min_val, max_val):
            if max_val == min_val:
                return 0.0
            return (value - min_val) / (max_val - min_val)
  $$
);
```

Show lessSee more

Scroll to top

Register a code bundle in a custom registry:

CopyExpand

```
CALL SAMOOHA_BY_SNOWFLAKE_LOCAL_DB.REGISTRY.REGISTER_CODE_SPEC(
  'my_custom_registry',
  $$
  api_version: 2.0.0
  spec_type: code_spec
  name: custom_udf
  version: v1
  description: Custom UDF for data normalization

  functions:
    - name: normalize_value
      type: UDF
      language: PYTHON
      runtime_version: "3.10"
      handler: normalize
      arguments:
        - name: value
          type: FLOAT
        - name: min_val
          type: FLOAT
        - name: max_val
          type: FLOAT
      returns: FLOAT
      code_body: |
        def normalize(value, min_val, max_val):
            if max_val == min_val:
                return 0.0
            return (value - min_val) / (max_val - min_val)
  $$
);
```

Show lessSee more

Scroll to top

#### Access requirements

If you’re not using the SAMOOHA\_APP\_ROLE role, you must use a role that was granted any of the following privileges.

To register a code spec in the default registry:

* `GRANT_PRIVILEGE_ON_ACCOUNT_TO_ROLE('REGISTER CODE SPEC', 'role name')`

To register items in a custom registry:

* You have read and write privileges on any custom registry that you created yourself.
* To access a custom registry created by another user, you need `GRANT_PRIVILEGE_ON_OBJECT_TO_ROLE('REGISTER', 'REGISTRY', 'MY_REGISTRY', 'role name')`.

---

### VIEW\_REGISTERED\_CODE\_SPECS

Schema:
:   REGISTRY

Lists all code bundle specs registered by this role in the local account registry.

#### Syntax

CopyExpand

```
VIEW_REGISTERED_CODE_SPECS( [ '<registry_name>' ] )
```

Show lessSee more

Scroll to top

#### Arguments

`registry_name` *(Optional)*
:   Name of a [custom registry](registries) to list code specs from. If not specified, lists code specs from the default account registry.

#### Returns

A table that lists the details of all code bundles that you have registered in this account. The table includes the following columns:

* `CODE_SPEC_ID`: ID of the code bundle spec.
* `NAME`: Code bundle spec name.
* `VERSION`: Code bundle spec version.
* `CODE_SPEC`: Full YAML specification of the code bundle spec.

#### Example

CopyExpand

```
CALL SAMOOHA_BY_SNOWFLAKE_LOCAL_DB.REGISTRY.VIEW_REGISTERED_CODE_SPECS();
```

Show lessSee more

Scroll to top

#### Access requirements

If you’re not using the SAMOOHA\_APP\_ROLE role, you must use a role that was granted any of the following privileges.

To see items in the default registry:

* `GRANT_PRIVILEGE_ON_ACCOUNT_TO_ROLE('VIEW REGISTERED CODE SPECS', 'role name')`
* `GRANT_PRIVILEGE_ON_ACCOUNT_TO_ROLE('REVIEW COLLABORATION', 'role name')`
* `GRANT_PRIVILEGE_ON_ACCOUNT_TO_ROLE('CREATE COLLABORATION', 'role name')`

To see items in a specific registry:

* You have read and write privileges on any custom registry that you created yourself.
* To access a custom registry created by another user, you need `GRANT_PRIVILEGE_ON_OBJECT_TO_ROLE('READ', 'REGISTRY', 'MY_REGISTRY', 'role name')`.

---

### VIEW\_CODE\_SPECS

Schema:
:   COLLABORATION

Returns all code bundle specs that are referenced by any template that you created or can run in the specified collaboration.

#### Syntax

CopyExpand

```
VIEW_CODE_SPECS( <collaboration_name> )
```

Show lessSee more

Scroll to top

#### Arguments

`collaboration_name`
:   ID of the collaboration.

#### Returns

A table that lists the code bundles available in the specified collaboration. The table includes the following columns:

* `CODE_SPEC_ID`: ID of this code bundle spec.
* `CODE_SPEC`: Full YAML specification of the code bundle spec.
* `SHARED_BY`: Collaborator alias that shared the code bundle spec.

#### Example

CopyExpand

```
CALL SAMOOHA_BY_SNOWFLAKE_LOCAL_DB.COLLABORATION.VIEW_CODE_SPECS(
  $collaboration_id
);
```

Show lessSee more

Scroll to top

#### Access requirements

If you’re not using the SAMOOHA\_APP\_ROLE role, you must use a role that was granted any of the following privileges:

* `GRANT_PRIVILEGE_ON_OBJECT_TO_ROLE('VIEW CODE SPECS', 'COLLABORATION', 'collaboration name', 'role name')`
* `GRANT_PRIVILEGE_ON_ACCOUNT_TO_ROLE('REVIEW COLLABORATION', 'role name')`
* `GRANT_PRIVILEGE_ON_ACCOUNT_TO_ROLE('CREATE COLLABORATION', 'role name')`

## Example specs

### Inline UDF with code body

A simple UDF with inline Python code:

CopyExpand

```
api_version: 2.0.0
spec_type: code_spec
name: string_utils
version: v1
description: String utility functions

functions:
  - name: clean_string
    type: UDF
    language: PYTHON
    runtime_version: "3.10"
    handler: clean
    arguments:
      - name: input_str
        type: STRING
    returns: STRING
    description: Removes leading/trailing whitespace and converts to lowercase
    code_body: |
      def clean(input_str):
          if input_str is None:
              return None
          return input_str.strip().lower()

  - name: extract_domain
    type: UDF
    language: PYTHON
    runtime_version: "3.10"
    handler: extract
    arguments:
      - name: email
        type: STRING
    returns: STRING
    description: Extracts domain from email address
    code_body: |
      def extract(email):
          if email is None or '@' not in email:
              return None
          return email.split('@')[1]
```

Show lessSee more

Scroll to top

### UDTF (User-Defined Table Function)

This example YAML defines a UDTF that returns multiple rows:

CopyExpand

```
api_version: 2.0.0
spec_type: code_spec
name: tokenizer
version: v1
description: Text tokenization UDTF

functions:
  - name: tokenize_text
    type: UDTF
    language: PYTHON
    runtime_version: "3.10"
    handler: Tokenizer
    arguments:
      - name: text
        type: STRING
      - name: delimiter
        type: STRING
    returns: TABLE(token STRING, position INTEGER)
    description: Splits text into tokens and returns each with its position
    code_body: |
      class Tokenizer:
          def process(self, text, delimiter):
              if text is None:
                  return
              tokens = text.split(delimiter if delimiter else ' ')
              for i, token in enumerate(tokens):
                  yield (token.strip(), i)
```

Show lessSee more

Scroll to top

### Staged artifact with wheel package

Be sure to read the [stage\_path documentation requirements](#label-dcr-collab-code-bundle-spec-yaml) for linking to staged code in your code spec.

This example YAML uses a staged Python wheel package:

CopyExpand

```
api_version: 2.0.0
spec_type: code_spec
name: ml_scoring
version: v2
description: ML scoring functions using custom library

artifacts:
  - alias: ml_lib
    stage_path: "@MY_DB.PUBLIC.CODE_STAGE/libs/ml_scoring_lib-1.0.0-py3-none-any.whl"
    description: Custom ML scoring library
    content_hash: "a1b2c3d4e5f6..."

functions:
  - name: predict_score
    type: UDF
    language: PYTHON
    runtime_version: "3.10"
    handler: ml_scoring_lib.predictor.predict
    arguments:
      - name: features
        type: ARRAY
    returns: FLOAT
    packages:
      - numpy
      - scikit-learn
    imports:
      - ml_lib
    description: Predicts score using trained ML model
```

Show lessSee more

Scroll to top

### Stored procedure

This example YAML defines a stored procedure for data processing:

CopyExpand

```
api_version: 2.0.0
spec_type: code_spec
name: data_processor
version: v1
description: Data processing procedures

procedures:
  - name: aggregate_metrics
    language: PYTHON
    runtime_version: "3.10"
    handler: process
    arguments:
      - name: table_name
        type: STRING
      - name: group_column
        type: STRING
    returns: STRING
    packages:
      - snowflake-snowpark-python
    description: Aggregates metrics by specified column
    code_body: |
      def process(session, table_name, group_column):
          df = session.table(table_name)
          result = df.group_by(group_column).count()
          result.write.mode("overwrite").save_as_table("aggregated_results")
          return f"Aggregated {df.count()} rows into aggregated_results"
```

Show lessSee more

Scroll to top

### Multiple Python files as staged artifacts

Be sure to read the [stage\_path documentation requirements](#label-dcr-collab-code-bundle-spec-yaml) for linking to staged code in your code spec.

This example YAML uses multiple staged Python source files:

CopyExpand

```
api_version: 2.0.0
spec_type: code_spec
name: analytics_suite
version: v3
description: Analytics suite with multiple modules

artifacts:
  - alias: utils
    stage_path: "@MY_DB.PUBLIC.CODE_STAGE/analytics/utils.py"
    description: Utility functions
  - alias: transformers
    stage_path: "@MY_DB.PUBLIC.CODE_STAGE/analytics/transformers.py"
    description: Data transformation functions
  - alias: validators
    stage_path: "@MY_DB.PUBLIC.CODE_STAGE/analytics/validators.py"
    description: Validation functions

functions:
  - name: transform_and_validate
    type: UDF
    language: PYTHON
    runtime_version: "3.10"
    handler: transformers.transform_validate
    arguments:
      - name: data
        type: OBJECT
    returns: OBJECT
    imports:
      - utils
      - transformers
      - validators
    description: Transforms and validates input data
```

Show lessSee more

Scroll to top

## Troubleshooting code bundles

Error:
:   `CodeSpecAlreadyExistsException`

Cause:
:   Code bundle spec with same name and version already registered.

Solution:
:   Use a different version or update the existing version.

---

Error:
:   `SpecValidationError`

Cause:
:   YAML doesn’t conform to schema.

Solution:
:   Check required fields and format.

---

Error:
:   `CodeSpecStageNotAccessibleError`

Cause:
:   Stage referenced in artifact isn’t accessible.

Solution:
:   Grant access to stage or verify stage exists.

---

Error:
:   `CodeSpecArtifactNotFoundAtStageError`

Cause:
:   File not found at specified stage path.

Solution:
:   Upload file to stage before registering.

---

Error:
:   `StageDirectoryNotEnabledError`

Cause:
:   Stage doesn’t have DIRECTORY enabled.

Solution:
:   Enable directory on the stage: `ALTER STAGE ... SET DIRECTORY = (ENABLE = TRUE)`

---

Error:
:   `CodeSpecNotFoundForOwnerException`

Cause:
:   Template references unregistered code bundle spec.

Solution:
:   Register code bundle spec before registering template.
