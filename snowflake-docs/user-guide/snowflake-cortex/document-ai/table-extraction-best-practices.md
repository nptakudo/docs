---
title: "Document AI table extraction: Best practices"
url: "https://docs.snowflake.com/en/user-guide/snowflake-cortex/document-ai/table-extraction-best-practices"
---

# Document AI table extraction: Best practices

[![Snowflake logo in black (no text)](../../../_images/logo-snowflake-black.png)](../../../_images/logo-snowflake-black.png) Deprecated Feature

Document AI and the [<model\_build\_name>!PREDICT](../../../sql-reference/classes/document-intelligence/methods/predict) method are deprecated. For more information, see [Document AI decommission (Pending)](../../../release-notes/bcr-bundles/un-bundled/bcr-2156).

[![Snowflake logo in black (no text)](../../../_images/logo-snowflake-black.png)](../../../_images/logo-snowflake-black.png) Feature — Generally Available

Available to accounts in [AWS, Microsoft Azure, and Google Cloud commercial regions](../../intro-regions.html#label-na-general-regions), with some exceptions. For more information, see [Document AI availability](limitations.html#label-document-ai-availability).

With Document AI, you can extract information from entities in a form of a single value or a list of values, or from tables
based on a list of specified columns. For information about working with table extraction in the Document AI interface,
see [Define values for a Document AI model build](prepare-model-build.html#label-document-ai-define-values).

This topic provides best practices when working with table extraction in Document AI.

## Use one model build for both entity and table extraction

To extract both entity and table values, use the [table extraction processing type](prepare-model-build.html#label-document-ai-define-values)
when creating a model build. Define values for all tables, and then add an additional table to group all entities in it.
For column names, use the names of extracted entities. Note that training might be needed to improve the results.

## Use one model build for a specific type of document

Each model build must contain documents of the same type, and the data that you want to extract should be similar for most
of the tables. If the number of columns in the source document differs from one document to another, but all documents contain
a defined subset of columns to be extracted and the common columns have the same or a similar name and location, then those common
columns can be extracted.

For example, invoices may have different numbers of columns with various data, but if all of the tables have the same first
three columns — `Item Description`, `Quantity`, and `Price` — that data can be extracted.

## Use natural language to define column names

You can copy the column names from the document so that they’re exactly the same.
For example, don’t name the columns `product_code` or `REPORT_DATE`; instead, name them `Product Code` or `Report Date`.

## When reviewing, specify the empty rows correctly

When you review the answers provided by the model, for empty rows, use `-` in each cell, instead of `None`. Rows with `None`
in each cell are ignored by the model during training.

## Define the columns in the same order they appear in the document

To improve accuracy, define the columns in the same order as they appear in the document, which is usually from left to right,
or top to bottom for the [transposed tables](#label-document-ai-table-extraction-best-practices-transpose-tables). If you
choose to define the order differently, training might be needed.

However, for columns that group multiple rows, such as `Invoice Number` and `Invoice Date`, add the columns at the beginning.
For example:

* `Invoice Number`
* `Invoice Date`
* `Item Code`
* `Item Name`
* `Quantity`

## Define values using casing from the document

When possible, define values using casing (uppercase and lowercase) from the document. If the casing in the document is varied, use
capitalization.

## Use the locator field only when needed

[Locator field](prepare-model-build.html#label-document-ai-define-values) is optional; in most cases, you don’t have to fill it in. However, if there
are multiple similar tables in a document, the model might answer inaccurately. If the answers come from a different source table
than expected, try using the locator field. Add information that helps the model identify the right table, such as the table title
or number.

## Add a section column to describe the layout of the table

If the table is divided into multiple named sections, add a section column. This helps the model understand the layout better and
improve the accuracy. For example, you can name the column `Section`, `Item section`, or `Item category`. If there is a second
level of nesting in the sections, you can add two columns: `Section` and `Subsection`.

## To group values, create an additional column

As described in [Use one model build for both entity and table extraction](#label-document-ai-table-extraction-best-practices-one-model-build), you can add an additional table
to contain all the entities. However, you can also add a column to the existing table instead of creating a separate table. In this
way, you can join results from the whole document set in a single table; for example:

| Invoice Number | Item Details | Item Price | Quantity |
| --- | --- | --- | --- |
| A | Item A1 | 10.00 | 1 |
| A | Item A2 | 20.00 | 1 |
| A | Item A3 | 30.00 | 1 |
| B | Item B1 | 15.00 | 1 |
| B | Item B2 | 25.00 | 1 |
| B | Item B3 | 35.00 | 1 |

See moreShow less

Expand

Note that the value in the first column is repeated for corresponding items.

If you want to add more columns or the table is large, Snowflake recommends creating an additional table with a single row (as explained
in the [Use one model build for both entity and table extraction](#label-document-ai-table-extraction-best-practices-one-model-build) section), to optimize cost.

## Make the column names distinguishable between documents

Try to semantically distinguish a column. Don’t use names such as `col1`, `val1`, `item1`.

In some cases, transposition can work better, especially when the row names don’t differ between documents or differ slightly and
are within a closed set of values.

Note that training on the specified column set might improve the results.

## Use the parent name as a prefix when working with hierarchical headers

To extract information from tables with hierarchical headers, join the header path using each parent name as a prefix. For example,
for the following table, define the columns as:

* `Category A Type X Column 1`
* `Category A Type Y Column 2`
* `Category A Type Y Column 3`
* `Category B Column 4`
* `Category B Column 5`

![A table with headers named Category A and Category B, where Category A includes subheaders: Type X and Type Y.](../../../_images/hierarchical-table-example.png)

## Transpose the tables if needed

You can extract information from transposed tables by using values from the first column of the table in the document as column names
in the output table.

For example, for the following table, name the columns:

* `Type A: Item 1`
* `Type A: Item 2`
* `Type B: Item 3`
* `Type B: Item 4`

[![An example of a table that can be transposed.](../../../_images/transposing-table-example.png)](../../../_images/transposing-table-example.png)

Note that this example includes [hierarchical headers](#label-document-ai-table-extraction-best-practices-hierarchical).

## For large tables, split the document

The model for table extraction returns answers that are up to 2048 tokens long. It means that the model stops extracting when it reaches
that limit. You can approach this in the following ways:

* If the table covers several pages, split the document into multiple one-page documents, and join the results in postprocessing.
* If the table is so dense that the data can’t be extracted even from a single page, divide the table by columns.

  For example, if the table contains 10 columns, try defining two separate values: one with 5 columns from the left half, and the other
  with 5 columns from the right half of the table. You might need to experiment with the column choice for best results.

## Create names for the columns that don’t have a name in the document

If the first column in the document doesn’t have a name, you must create that name yourself when defining the value. You can approach it
in the following ways:

* Use the table title or a significant part of the title.
* Create a descriptive name that represents the data in the column, e.g. `description`, `type of asset`, `year`, `category`.

## Compare data from two different periods of time

If you want to compare data from two different periods of time, for example, years 2023 and 2024 in financial documents such as annual reports,
you can prefix the columns with “current” and “previous”. Note that training might be needed to improve the results.
