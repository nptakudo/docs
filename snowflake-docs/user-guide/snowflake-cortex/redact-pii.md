---
title: "Detect and redact personally identifiable information (PII)"
url: "https://docs.snowflake.com/en/user-guide/snowflake-cortex/redact-pii"
---

# Detect and redact personally identifiable information (PII)

Personally identifiable information (PII) includes names, addresses, phone numbers, email addresses, tax identification numbers, and other
data that can be used (alone or with other information) to identify an individual. Most organizations have regulatory and compliance
requirements around handling PII data. [AI\_REDACT](../../sql-reference/functions/ai_redact) is a fully-managed Cortex AI Function that uses a large language model (LLM) to help you
detect, locate, and redact PII from unstructured text data.

AI\_REDACT can help you prepare text for call center coaching, sentiment analysis, insurance and medical analysis,
and machine learning (ML) model training, among other use cases.

Tip

Use AI\_PARSE\_DOCUMENT or AI\_TRANSCRIBE to convert document or speech data into text before applying AI\_REDACT.

## AI\_REDACT

The AI\_REDACT function has two modes of operation: `detect` and `redact`. The default is `redact`. Use AI\_REDACT in `detect` mode to
identify PII locations then programmatically choose which PII to redact. Use AI\_REDACT in `redact` mode to replace PII in the input text with placeholder values.

Important

AI\_REDACT performs detection and redaction in a best-effort manner using AI models. Always review the output to ensure compliance
with your organization’s data privacy policies. If AI\_REDACT fails to detect or redact any PII in your data,
[contact Snowflake Support](../contacting-support).

## Regional availability

See [Regional availability](aisql.html#label-cortex-llm-availability).

## Limitations

* AI\_REDACT uses AI models and may not find all personally identifiable information. Always review the output
  to ensure compliance with your organization’s data privacy policies. If AI\_REDACT fails to redact certain PII, contact [Snowflake Support](../contacting-support).
* The COUNT\_TOKENS and AI\_COUNT\_TOKENS functions do not yet support AI\_REDACT.
* AI\_REDACT works best with well-formed English text. Performance may vary with other languages or text with many
  spelling, punctuation, or grammatical errors.
* AI\_REDACT currently supports only US PII and some UK and Canadian PII, where noted in [Detected PII categories](#label-ai-redact-pii-categories).
* AI\_REDACT is currently limited in the number of tokens it can input and output. Input and output together can be up to
  4,096 tokens. Output is limited to 1,024 tokens. If the input text is longer, split it into smaller chunks and
  redact each chunk separately, perhaps using
  [SPLIT\_TEXT\_RECURSIVE\_CHARACTER](../../sql-reference/functions/split_text_recursive_character-snowflake-cortex).
  See [Chunking example](#label-ai-redact-pii-example-chunking) for an example of redacting text that exceeds token limits.

  Note

  A token is the smallest unit of data processed by the AI model. For English text, industry guidelines consider one token to be approximately four characters, or 0.75 words.

## Detected PII categories

AI\_REDACT supports the detection and redaction of the following categories of PII. The values in the Category column are the strings
that are supported in the optional `categories` argument.

> | Category | Notes |
> | --- | --- |
> | NAME | Recognizes full name, first name, middle name, and last name |
> | EMAIL |  |
> | PHONE\_NUMBER |  |
> | DATE\_OF\_BIRTH |  |
> | GENDER | Recognizes male, female, and nonbinary |
> | AGE |  |
> | ADDRESS | Identifies:   * complete postal address (US, UK, CA) * street address (US, UK, CA) * postal code (US, UK, CA) * city (US, UK, CA) * state (US) or province (CA) * county, borough, or township (US) |
> | NATIONAL\_ID | Identifies Social Security numbers (US) |
> | PASSPORT | Identifies passport numbers (US, UK, CA) |
> | TAX\_IDENTIFIER | Identifies Individual Taxpayer Numbers (ITNs) |
> | PAYMENT\_CARD\_DATA | Identifies complete card information, card number, expiration date, and CVV |
> | DRIVERS\_LICENSE | Identifies US, UK, and CA licenses |
> | IP\_ADDRESS |  |
>
> See moreShow less
>
> Expand

Note

AI\_REDACT supports partial matches for some PII categories. For example, a first name alone is sufficient to trigger
redaction with the [NAME] placeholder.

## Retain specific PII with detect mode

By default, AI\_REDACT replaces all detected PII with placeholder values. In some cases, you might want to retain certain PII while redacting
the rest. For example, you might want to redact all names in call center transcripts or customer reviews except for known employee names.

Use `detect` mode to build a selective redaction workflow:

1. Call AI\_REDACT with the `mode` argument set to `detect` to identify and locate PII in the input text.
2. Compare the detected spans against an allowlist of values you want to keep.
3. Redact only the PII that is not in the allowlist.

When you call AI\_REDACT in `detect` mode, the function returns an OBJECT containing a `spans` array. Each element
in the array is an OBJECT with the following fields:

| Field | Type | Description |
| --- | --- | --- |
| `category` | VARCHAR | The PII category, such as `NAME` or `ADDRESS`. See [Detected PII categories](#label-ai-redact-pii-categories) for supported categories. |
| `start` | NUMBER | The start index of the detected PII in the input text. |
| `end` | NUMBER | The end index of the detected PII in the input text. |
| `text` | VARCHAR | The matched PII text from the input. |

See moreShow less

Expand

For examples of using `detect` mode, see [Detection and selective redaction examples](#label-ai-detect-pii-examples).

## Handle row-level errors in multi-row queries

Important

If your query fails on every row, the cause might be a known constraint rather than a row-level error.
See [Limitations](#label-ai-redact-pii-limitations) for details on token limits, language support, and other restrictions.

AI\_REDACT raises an error if it cannot process the input text. When a query redacts multiple rows, an error causes the entire query to fail.
To allow processing to continue with other rows, you can set the session parameter `AI_SQL_ERROR_HANDLING_USE_FAIL_ON_ERROR` to FALSE.
Errors then return NULL instead of stopping the query.

CopyExpand

```
ALTER SESSION SET AI_SQL_ERROR_HANDLING_USE_FAIL_ON_ERROR=FALSE;
```

Show lessSee more

Scroll to top

With this parameter set to FALSE, you can also pass TRUE as the final argument to AI\_REDACT, which causes the return value to
be an OBJECT that contains separate fields for the redacted text and any error message. One of these fields is NULL
depending on whether the AI\_REDACT call processed successfully.

The following example shows how to use error handling when processing multiple rows:

1. Create a table with unredacted text.

   > CopyExpand
   >
   > ```
   > CREATE OR REPLACE TABLE raw_table AS
   >   SELECT 'My previous manager, Washington, used to live in Kirkland. His first name was Mike.' AS my_column
   >   UNION ALL
   >   SELECT 'My name is William and I live in San Francisco. You can reach me at (415).450.0973';
   > ```
   >
   > Show lessSee more
   >
   > Scroll to top
2. Set the session parameter.

   > CopyExpand
   >
   > ```
   > ALTER SESSION SET AI_SQL_ERROR_HANDLING_USE_FAIL_ON_ERROR=FALSE;
   > ```
   >
   > Show lessSee more
   >
   > Scroll to top
3. Create a redaction table with columns for `value` and `error`.

   > CopyExpand
   >
   > ```
   > CREATE OR REPLACE TABLE redaction_table (
   >   value VARCHAR,
   >   error VARCHAR
   >   );
   > ```
   >
   > Show lessSee more
   >
   > Scroll to top
4. Redact PII from `raw_table` and insert the rows into `redaction_table` to store the redacted text and error messages.

   > CopyExpand
   >
   > ```
   > INSERT INTO redaction_table
   > SELECT
   >     result:value::STRING AS value,
   >     result:error::STRING AS error
   >   FROM (SELECT AI_REDACT(my_column, TRUE) AS result FROM raw_table);
   > ```
   >
   > Show lessSee more
   >
   > Scroll to top

## Cost considerations

AI\_REDACT incurs costs based on the number of input and output tokens processed, as with other Cortex AI Functions.
See the [Snowflake Pricing Guide](https://www.snowflake.com/pricing/pricing-guide/) for details.

## Redaction examples

### Basic redaction examples

The following example redacts a name and an address from the input text.

CopyExpand

```
SELECT AI_REDACT(
  input => 'My name is John Smith and I live at twenty third street, San Francisco.'
  );
```

Show lessSee more

Scroll to top

Basic redaction output:

Expand

```
My name is [NAME] and I live at [ADDRESS]
```

Show lessSee more

Scroll to top

The following example redacts only names and email addresses from the input text. Note that the text only contains a
first name, which is recognized and redacted as [NAME]. The input text does not contain an email address, so no email
placeholder appears in the output.

CopyExpand

```
SELECT AI_REDACT(
  input => 'My name is John and I live at twenty third street, San Francisco.',
  categories => ['NAME', 'EMAIL']
  );
```

Show lessSee more

Scroll to top

Selective redaction output:

Expand

```
My name is [NAME] and I live at twenty third street, San Francisco.
```

Show lessSee more

Scroll to top

### End-to-end example

The following example processes rows from one table and inserts the redacted output into another table. You could use a similar approach to
store the redacted data in a column in an existing table. After redaction, the text is passed to the
[AI\_SENTIMENT](../../sql-reference/functions/ai_sentiment) function to extract overall sentiment information.

1. Create a table with unredacted text.

   > CopyExpand
   >
   > ```
   > CREATE OR REPLACE TABLE raw_table AS
   >   SELECT 'My previous manager, Washington, used to live in Kirkland. His first name was Mike.' AS my_column
   >   UNION ALL
   >   SELECT 'My name is William and I live in San Francisco. You can reach me at (415).450.0973';
   > ```
   >
   > Show lessSee more
   >
   > Scroll to top
2. View unredacted data.

   > CopyExpand
   >
   > ```
   > SELECT * FROM raw_table;
   > ```
   >
   > Show lessSee more
   >
   > Scroll to top
3. Create a redaction table.

   > CopyExpand
   >
   > ```
   > CREATE OR REPLACE TABLE redaction_table (value VARCHAR);
   > ```
   >
   > Show lessSee more
   >
   > Scroll to top
4. Redact PII from `raw_table` and insert the rows into `redaction_table`.

   > CopyExpand
   >
   > ```
   > INSERT INTO redaction_table
   >   SELECT AI_REDACT(my_column) AS value FROM raw_table;
   > ```
   >
   > Show lessSee more
   >
   > Scroll to top
5. View redacted results.

   > CopyExpand
   >
   > ```
   > SELECT * FROM redaction_table;
   > ```
   >
   > Show lessSee more
   >
   > Scroll to top
6. Run the AI\_SENTIMENT function on redacted text.

   > CopyExpand
   >
   > ```
   > SELECT
   >     value AS redacted_text,
   >     AI_SENTIMENT(value) AS summary_sentiment
   >   FROM redaction_table;
   > ```
   >
   > Show lessSee more
   >
   > Scroll to top

### Chunking example

This example illustrates how to redact PII from long text by splitting the text into smaller chunks, redacting each chunk separately,
and then recombining the redacted chunks into the final output. This approach works around AI\_REDACT’s token limits.

1. Create a table with patient data.

   > CopyExpand
   >
   > ```
   > CREATE OR REPLACE TABLE patients (
   >   patient_id INT PRIMARY KEY,
   >   patient_notes TEXT
   >   );
   > ```
   >
   > Show lessSee more
   >
   > Scroll to top
2. Split the text into chunks, apply AI\_REDACT to each chunk, and concatenate the redacted chunks.

   > CopyExpand
   >
   > ```
   > CREATE OR REPLACE TABLE final_temp_table AS
   >   WITH chunked_data AS (
   >     SELECT
   >         patient_id,
   >         chunk.value AS chunk_text,
   >         chunk.index AS chunk_index
   >       FROM
   >         patients,
   >         LATERAL FLATTEN(
   >             input => SNOWFLAKE.CORTEX.SPLIT_TEXT_RECURSIVE_CHARACTER(
   >                 patient_notes,
   >                 'none',
   >                 1000
   >                 )
   >             ) AS chunk
   >       WHERE
   >         patient_notes IS NOT NULL
   >         AND LENGTH(patient_notes) > 0
   >     ),
   >   redacted_chunks AS (
   >       SELECT
   >           patient_id,
   >           chunk_index,
   >           chunk_text,
   >           TO_VARIANT(results:value) AS redacted_chunk,
   >           TO_VARIANT(results:error) AS error_string
   >         FROM (
   >           SELECT
   >               patient_id,
   >               chunk_index,
   >               chunk_text,
   >               AI_REDACT(chunk_text,TRUE) AS results
   >             FROM
   >               chunked_data
   >         )
   >   ),
   >   final AS (
   >       SELECT
   >           chunk_text AS original,
   >           IFF(error_string IS NOT NULL, chunk_text, redacted_chunk) AS redacted_text,
   >           patient_id,
   >           chunk_index
   >         FROM
   >           redacted_chunks
   >   )
   >   SELECT * FROM final;
   > ```
   >
   > Show lessSee more
   >
   > Scroll to top
3. Query the results.

   > CopyExpand
   >
   > ```
   > SELECT
   >     patient_id,
   >     LISTAGG(redacted_text, '') WITHIN GROUP (ORDER BY chunk_index) AS full_output
   >   FROM final_temp_table
   >   GROUP BY patient_id;
   > ```
   >
   > Show lessSee more
   >
   > Scroll to top

## Detection and selective redaction examples

### Basic detection example

The following example identifies and returns the category, location, and text of each detected PII instance without redacting the input.

CopyExpand

```
SELECT AI_REDACT(
    input => 'My old manager, Washington, used to live in Washington. His first name was Mike.',
    return_error_details => FALSE,
    mode => 'detect'
    );
```

Show lessSee more

Scroll to top

Basic detection output:

Expand

```
{
  "spans": [
    {
      "category": "NAME",
      "end": 26,
      "start": 16,
      "text": "Washington"
    },
    {
      "category": "ADDRESS",
      "end": 54,
      "start": 44,
      "text": "Washington"
    },
    {
      "category": "NAME",
      "end": 79,
      "start": 75,
      "text": "Mike"
    }
  ]
}
```

Show lessSee more

Scroll to top

### End-to-end with allowlist example

The following example demonstrates a selective redaction workflow that uses `detect` mode and an allowlist. It loads a list of names to
retain from a staged file, uses AI\_REDACT in `detect` mode to identify PII locations, and then passes the results to a Python UDF that
redacts only the PII not in the allowlist.

1. Retain an allowlist of values by loading the list from a stage into a temporary table.

   > CopyExpand
   >
   > ```
   > CREATE OR REPLACE TEMP TABLE string_list (value STRING);
   >
   > COPY INTO string_list
   >   FROM @mystage/allowlist.txt
   >   FILE_FORMAT = (
   >     TYPE = 'CSV'
   >     RECORD_DELIMITER = '\n'
   >     FIELD_DELIMITER = '\t'   -- any char NOT in file
   >     TRIM_SPACE = TRUE
   >     SKIP_HEADER = 0
   >     );
   > ```
   >
   > Show lessSee more
   >
   > Scroll to top
2. View the allowlist table

   > CopyExpand
   >
   > ```
   > SELECT * FROM string_list;
   > ```
   >
   > Show lessSee more
   >
   > Scroll to top
   >
   > Allowlist table output:
   >
   > Expand
   >
   > ```
   > VALUE
   > Mike
   > David
   > ```
   >
   > Show lessSee more
   >
   > Scroll to top
3. Create a Python UDF that selectively redacts PII based on the allowlist.

   > CopyExpand
   >
   > ```
   > CREATE OR REPLACE FUNCTION redact_spans_with_allowlist(
   >   SPAN_DATA VARIANT,
   >   ALLOWLIST ARRAY,
   >   ORIGINAL_TEXT STRING
   >   )
   >   RETURNS STRING
   >   LANGUAGE PYTHON
   >   RUNTIME_VERSION = '3.8'
   >   HANDLER = 'redact_text'
   >   AS
   >   $$
   >   def redact_text(span_data, allowlist, original_text):
   >       spans = span_data.get('spans', [])
   >       # Sort descending to maintain index integrity
   >       sorted_spans = sorted(spans, key=lambda x: x['start'], reverse=True)
   >
   >       result = original_text
   >
   >       for span in sorted_spans:
   >           text_val = span.get('text')
   >           if text_val in allowlist:
   >               continue
   >
   >           start, end = span['start'], span['end']
   >           label = f"[{span['category']}]"
   >
   >           # Splice the string
   >           result = result[:start] + label + result[end:]
   >
   >       return result
   >   $$;
   > ```
   >
   > Show lessSee more
   >
   > Scroll to top
4. Test the UDF.

   > CopyExpand
   >
   > ```
   > SELECT redact_spans_with_allowlist(
   >   PARSE_JSON('{"spans": [{"category": "NAME", "end": 26, "start": 16, "text": "Washington"}, {"category": "NAME", "end": 79, "start": 75, "text": "Mike"}]}'),
   >   ARRAY_CONSTRUCT('Washington'), -- This will NOT be redacted
   >   'Hello, my name is Washington and his is Mike.'
   >   );
   > ```
   >
   > Show lessSee more
   >
   > Scroll to top
5. Run AI\_REDACT in `detect` mode.

   > CopyExpand
   >
   > ```
   > CREATE OR REPLACE TABLE raw (message TEXT);
   >
   > INSERT INTO raw (message) VALUES
   >   ('My old manager, Washington, used to live in Washington. His first name was Mike.');
   >
   > SELECT
   >     t.message AS message,
   >     AI_REDACT(input=>t.message, return_error_details=>FALSE, mode=>'detect') AS spans,
   >     redact_spans_with_allowlist(spans, l.str_list, message) AS result
   >   FROM raw t
   >     CROSS JOIN (
   >       SELECT ARRAY_AGG(value) AS str_list
   >         FROM string_list
   >       ) l;
   > ```
   >
   > Show lessSee more
   >
   > Scroll to top

End-to-end with allowlist example output:

| MESSAGE | SPANS | RESULT |
| --- | --- | --- |
| My old manager, Washington, used to live in Washington. His first name was Mike. | CopyExpand  ``` {   "spans": [     {"category": "NAME",     "end": 26,     "start": 16,     "text": "Washington"     },     {"category": "ADDRESS",     "end": 54,     "start": 44,     "text": "Washington"     },     {"category": "NAME",     "end": 79,     "start": 75,     "text": "Mike"     }   ] } ```  Show lessSee more  Scroll to top | My old manager, [NAME], used to live in [ADDRESS]. His first name was Mike. |

See moreShow less

Expand

## Legal notices

The data classification of inputs and outputs are as set forth in the following table.

| Input data classification | Output data classification | Designation |
| --- | --- | --- |
| Usage Data | Customer Data | Generally available functions are Covered AI Features. Preview functions are Preview AI Features. [[1]](#id3) |

See moreShow less

Expand

[[1](#id2)]

Represents the defined term used in the AI Terms and Acceptable Use Policy.

For additional information, refer to [Snowflake AI and ML](../../guides-overview-ai-features).
