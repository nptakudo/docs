---
title: "Export Document AI model builds"
url: "https://docs.snowflake.com/en/user-guide/snowflake-cortex/document-ai/model-build-export"
---

# Export Document AI model builds

[![Snowflake logo in black (no text)](../../../_images/logo-snowflake-black.png)](../../../_images/logo-snowflake-black.png) Deprecated Feature

Document AI and the [<model\_build\_name>!PREDICT](../../../sql-reference/classes/document-intelligence/methods/predict) method are deprecated. For more information, see [Document AI decommission (Pending)](../../../release-notes/bcr-bundles/un-bundled/bcr-2156).

[![Snowflake logo in black (no text)](../../../_images/logo-snowflake-black.png)](../../../_images/logo-snowflake-black.png) Feature — Generally Available

Available to accounts in [AWS, Microsoft Azure, and Google Cloud commercial regions](../../intro-regions.html#label-na-general-regions), with some exceptions. For more information, see [Document AI availability](limitations.html#label-document-ai-availability).

You can export Document AI model builds to an internal stage. As a result, the document files are exported and the annotations file is generated.
You can then use the exported data for various purposes, such as creating [Snowflake Datasets](../../../developer-guide/snowflake-ml/dataset) and extracting information using the AI\_EXTRACT function.

## Prerequisites

* To use Document AI, you must have the privileges required. For more information about the privileges, see [Setting up Document AI](setting-up).
* To export a Document AI model build, you must have the WRITE privilege on a target stage.

  Note

  The target stage must be an internal stage.

## Export a Document AI model build

1. Sign in to [Snowsight](../../ui-snowsight-gs.html#label-snowsight-getting-started-sign-in).
2. In the navigation menu, select AI & ML » AI Studio.
3. Next to the Document Processing Playground, select Open. In the Document Processing Playground, to access Document AI, select Go to Document AI model builds.
4. Select a warehouse.

   The list of existing model builds appears.
5. Select the … (more) menu next to the model build name, and then select Export.
6. In the Export Build dialog that appears, select a target stage from the list, and then confirm by selecting Export.
7. When the export process is complete, close the dialog by selecting Close.

   Note

   You can close the dialog before the export process is complete. Closing the dialog doesn’t cancel the export process.

   The model build is exported to the target stage. This means that the target stage directory now contains all documents from the latest version of that
   Document AI model build, and the `annotations.jsonl` file.

## The annotations file

When you export a Document AI model build, the `annotations.jsonl` file is generated in the target stage directory. For each document you export,
the file contains the following information:

* `file`: Filename identifier
* `prompt`: JSON schema that describes the prompts
* `annotatedResponse`: User responses in a format consistent with the schema
* `modelResponse`: Responses that weren’t modified by the user

Consider the following line example of the `annotations.jsonl` file:

Expand

```
{
  "file": "13c36c6a8c98acc95b797f03cc4c6d38.pdf",
  "prompt": {
    "type": "object",
    "properties": {
      "table": {
        "description": "earning statement",
        "type": "object",
        "properties": {
          "deductions": {
            "description": "deductions",
            "type": "array"
          },
          "current total": {
            "description": "current total",
            "type": "array"
          }
        }
      },
      "name": {
        "description": "what is the name",
        "type": "array"
      },
      "address": {
        "description": "what is the address",
        "type": "array"
      }
    }
  },
  "annotatedResponse": {
    "table": {
      "deductions": [
        "9,897.82",
        "CPP",
        "El",
        "INCOME TAX",
        "UNION DUES",
        "LIFE INSURANCE",
        "LONG TERM DISABILITY",
        "CANADA SAVING BONDS"
      ],
      "current total": [
        "None",
        "65.03",
        "28.62",
        "305.90",
        "10.84",
        "4.94",
        "7.01",
        "8.00"
      ]
    },
    "name": [
      "ACME"
    ],
    "address": [
      "200 billing rd, suite 100, needham, MA 02494"
    ]
  },
  "modelResponse": {}
}
```

Show lessSee more

Scroll to top

## Work with the exported data

After you export a Document AI model build, you can create a table with the exported data for further processing:

1. Create a file format for the annotations file:

   CopyExpand

   ```
   CREATE OR REPLACE FILE FORMAT my_json
     TYPE = 'JSON';
   ```

   Show lessSee more

   Scroll to top
2. Create a table:

   CopyExpand

   ```
   CREATE OR REPLACE TABLE exported_data_table AS (
      SELECT
         input_file.$1:file AS file,
         input_file.$1:prompt AS prompt,
         input_file.$1:annotatedResponse AS response
      FROM '@docai_db.docai_schema.docai_stage/docai_test_2025_10_03_16_00_10/annotations.jsonl' (FILE_FORMAT => my_json) input_file
      WHERE response != '{}'
   );
   ```

   Show lessSee more

   Scroll to top

You can now either convert the exported data to a Dataset for further use in Snowflake, or run the AI\_EXTRACT function using that data:

* Create a Dataset for the exported data:

  CopyExpand

  ```
  CREATE DATASET my_dataset;

  ALTER DATASET my_dataset
  ADD VERSION 'v2' FROM (
    SELECT
      CONCAT('@docai_db.docai_schema.docai_stage/docai_test_2025_10_03_16_00_10/', file) AS file,
      prompt,
      response
    FROM exported_data_table
  );
  ```

  Show lessSee more

  Scroll to top

  For more information about Datasets, see [Snowflake Datasets](../../../developer-guide/snowflake-ml/dataset).
* Run AI\_EXTRACT using the exported data:

  CopyExpand

  ```
  SELECT
  AI_EXTRACT (
    file => TO_FILE('@docai_db.docai_schema.docai_stage/docai_test_2025_10_03_16_00_10', my_table.file),
    responseFormat => PARSE_JSON('{ "schema": ' || TO_VARIANT(my_table.prompt) || '}')
    )
  FROM docai_db.docai_schema.exported_data_table AS my_table;
  ```

  Show lessSee more

  Scroll to top
