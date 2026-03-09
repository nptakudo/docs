---
title: "Prepare a Document AI model build"
url: "https://docs.snowflake.com/en/user-guide/snowflake-cortex/document-ai/prepare-model-build"
---

# Prepare a Document AI model build

[![Snowflake logo in black (no text)](../../../_images/logo-snowflake-black.png)](../../../_images/logo-snowflake-black.png) Deprecated Feature

Document AI and the [<model\_build\_name>!PREDICT](../../../sql-reference/classes/document-intelligence/methods/predict) method are deprecated. For more information, see [Document AI decommission (Pending)](../../../release-notes/bcr-bundles/un-bundled/bcr-2156).

[![Snowflake logo in black (no text)](../../../_images/logo-snowflake-black.png)](../../../_images/logo-snowflake-black.png) Feature — Generally Available

Available to accounts in [AWS, Microsoft Azure, and Google Cloud commercial regions](../../intro-regions.html#label-na-general-regions), with some exceptions. For more information, see [Document AI availability](limitations.html#label-document-ai-availability).

This topic describes preparing a Document AI model build.

You create and manage Document AI model builds in Snowsight. The Document AI model build represents a single type of document;
for example, a model build for extracting information from invoice documents. The Document AI model build includes the model, the data values
to be extracted, and the documents uploaded to test and train the model. Document AI stores any published or trained models in the
[Snowflake Model Registry](../../../developer-guide/snowflake-ml/model-registry/overview).

The Document AI model build is an instance of the DOCUMENT\_INTELLIGENCE class. Snowflake provides the DOCUMENT\_INTELLIGENCE class in the
SNOWFLAKE.ML schema. For more information about classes, see [Snowflake classes](../../../sql-reference/snowflake-db-classes).

In Snowsight, the Document AI model build view is divided into the following tabs:

* Build Details: View information about the model build, such as the number of documents, number of data values
  to extract, model accuracy, and extracting query.
* Documents: Review the list of documents uploaded to test and train the model.
* Values: View the list of data values to extract.

For more information about roles and privileges for Document AI, see [Setting up Document AI](setting-up).

## Create a Document AI model build

1. Sign in to [Snowsight](../../ui-snowsight-gs.html#label-snowsight-getting-started-sign-in) using an account role that is granted the SNOWFLAKE.DOCUMENT\_INTELLIGENCE\_CREATOR role.
2. In the navigation menu, select AI & ML » AI Studio.
3. Next to the Document Processing Playground, select Open. In the Document Processing Playground, to access Document AI, select Go to Document AI model builds.
4. Select a warehouse.

   The list of existing model builds appears.
5. Select + Build.
6. In the dialog that appears, enter a name for your model build, select its location (database and schema), and then select Create.

   The model build is created.

Note

* Document AI does not support double quotes around identifiers for the database and schema.
* Document AI does not support altering a database or a schema where the model build is located.

The status of the newly created model build is set to Draft. When you publish the model build, the status changes to Published.
If you train the model, the status of the model build changes to Trained.

## Delete a Document AI model build

Attention

When you delete the Document AI model build, you delete the model and all uploaded documents used to train the model. Before you
delete a model build, ensure that it isn’t part of a document processing pipeline. If you delete a model build used in a document
processing pipeline, the pipeline will fail.

Snowflake does not keep any model build data, so deleted model builds and training data cannot be recovered; they must be recreated.

To delete a Document AI model build, including the documents uploaded to the model build:

1. Sign in to [Snowsight](../../ui-snowsight-gs.html#label-snowsight-getting-started-sign-in).
2. In the navigation menu, select AI & ML » AI Studio.
3. Next to the Document Processing Playground, select Open. In the Document Processing Playground, to access Document AI, select Go to Document AI model builds.
4. Select a warehouse.
5. Select the … (more) menu next to the model build name, and then select Delete.
6. To confirm deletion, in the Delete Build dialog, select Delete.

## Upload documents to a Document AI model build

To test and train the Document AI model, manually add the documents to your model build in Snowsight.

Note

Before you upload documents to the model build, ensure that the documents meet the requirements listed in [Prepare your documents for Document AI](preparing-documents).

To upload documents to an existing Document AI model build:

1. Sign in to [Snowsight](../../ui-snowsight-gs.html#label-snowsight-getting-started-sign-in).
2. In the navigation menu, select AI & ML » AI Studio.
3. Next to the Document Processing Playground, select Open. In the Document Processing Playground, to access Document AI, select Go to Document AI model builds.
4. Select a warehouse.
5. From the list of model builds, select the name of the build to add documents to.
6. Select the Build Details tab.
7. Select Upload documents.
8. Select Browse, or drag the documents to a dialog.
9. Select Upload.

After you upload the document, you can view its status in the Documents tab.

The document can have one of the following statuses:

* Processing: The document is being processed by OCR.
* To review: The OCR process was successful and you can now review the document.
* In progress: The review is in progress, meaning that you have at least one value defined for this document.
* Accepted: You reviewed the document and accepted all values.
* Error: An error occurred during OCR.

## Delete documents from a Document AI model build

Attention

You can’t delete documents that were used for training.

When you delete a document, you also delete the reviewed data values in that document.

To delete documents from a Document AI model build:

1. Sign in to [Snowsight](../../ui-snowsight-gs.html#label-snowsight-getting-started-sign-in).
2. In the navigation menu, select AI & ML » AI Studio.
3. Next to the Document Processing Playground, select Open. In the Document Processing Playground, to access Document AI, select Go to Document AI model builds.
4. Select a warehouse.
5. From the list of model builds, select the name of the model build.
6. Select the Documents tab.
7. Select the … (more) menu next to the document name, and then select Delete.
8. To confirm deletion, in the Delete Document dialog, select Delete.

## Define values for a Document AI model build

Data values are the information you want to extract from documents.

To define values for the Document AI model build:

1. Sign in to [Snowsight](../../ui-snowsight-gs.html#label-snowsight-getting-started-sign-in).
2. In the navigation menu, select AI & ML » AI Studio.
3. Next to the Document Processing Playground, select Open. In the Document Processing Playground, to access Document AI, select Go to Document AI model builds.
4. Select a warehouse.
5. From the list of model builds, select the name of the model build to define values for.
6. Select the Build Details tab.
7. Select Define values.
8. In the Documents review view, select the document processing type for your model build:

   * For entity extraction, select + Entity.
   * For table extraction, select + Table.

Note

To switch between entity and table extraction, remove all values, and select the document processing type.
You can change the processing type until you publish or train the model.

### Defining values for entity extraction

For each value, enter a value name and a question asked in natural language. For more information about optimizing
questions for the model, see [Question optimization for extracting information with Document AI](optimizing-questions).

As a result of this procedure, the model provides an answer to the question and a confidence score. The confidence score describes
how confident the model is that the answer is correct. For example, a confidence score of `0.9` means that there is 90% confidence
that the answer is correct.

### Defining values for table extraction

In table extraction, a single value corresponds to a table. The value name is referred to as a table key. To extract several tables
from a document, define separate value (table key) for each table.

To define values for table extraction:

1. Sign in to [Snowsight](../../ui-snowsight-gs.html#label-snowsight-getting-started-sign-in).
2. In the navigation menu, select AI & ML » AI Studio.
3. Next to the Document Processing Playground, select Open. In the Document Processing Playground, to access Document AI, select Go to Document AI model builds.
4. Select a warehouse.
5. From the list of model builds, select the name of the model build to define values for.
6. Select the Build Details tab.
7. Select Define values.
8. In the Documents review view, select + Table.
9. For each value, enter a table key, locator, and column names, where:

   > |  | Explanation | Example |
   > | --- | --- | --- |
   > | Table key | A unique key to distinguish the value from others | `stock_options` |
   > | Locator (optional) | A fragment of the document in natural language that helps locate the table; usually a phrase which describes the table, such as its heading or a specific section title | `A summary of Stock Option Activity Transactions`, `Table 6` |
   > | Column names | Defined in natural language, as they appear in a document | * `Type of transaction` * `Number of options` * `Weighted average exercise price` |
   >
   > See moreShow less
   >
   > Expand
10. Select Extract.
11. Optional: To define another table, select + Table.
12. Optional: To remove a value, select [![Document AI vertical more icon](../../../_images/vertical-more-icon2.png)](../../../_images/vertical-more-icon2.png) » Delete value.

For information about reviewing tables, see [Reviewing answers for table extraction](#label-document-ai-review-answers-table-extraction).

For best practices on working with table extraction, see [Document AI table extraction: Best practices](table-extraction-best-practices).

## Review answers and evaluate results

Before you use the Document AI model to extract information or decide to train the model through fine-tuning, you need to review the answers
that the model provides.

### Reviewing answers for entity extraction

When you review the answers for entity extraction, you might encounter the following scenarios:

| Returned answer | User action |
| --- | --- |
| Correct | Select the checkmark. Confirm only the answers that are fully correct. |
| Incorrect | Enter the correct value manually.  To review the value provided by the model after you manually changed the value, select the down arrow. |
| List of answers | To remove answers from the list or to add more answers, select the … (more) menu. |
| None | If the document contains the answer, enter the value manually.  If the document does not contain the answer, confirm the empty response by selecting the checkmark. |

See moreShow less

Expand

Tip

You can re-order the answers by dragging them.

To highlight the answer in a document, select ![Document AI locate answer icon](../../../_images/locate-answer-icon.png).

### Reviewing answers for table extraction

To review answers for table extraction after you defined values:

1. Sign in to [Snowsight](../../ui-snowsight-gs.html#label-snowsight-getting-started-sign-in).
2. In the navigation menu, select AI & ML » AI Studio.
3. Next to the Document Processing Playground, select Open. In the Document Processing Playground, to access Document AI, select Go to Document AI model builds.
4. Select a warehouse.
5. From the list of model builds, select the name of the model build to define values for.
6. Select the Build Details tab.
7. Select Define values.
8. In the Documents review view, for a given table (value), select Preview » Review table.
9. If a cell in the table is not correct, make changes to correct the cell.
10. If a table doesn’t exist in the document, remove the rows to represent an empty answer. Select [![Document AI vertical more icon](../../../_images/vertical-more-icon2.png)](../../../_images/vertical-more-icon2.png) » Delete all answers.
11. Optional: Select [![Document AI vertical more icon](../../../_images/vertical-more-icon2.png)](../../../_images/vertical-more-icon2.png) » Reset accepted answers.
12. When all cells in the table are correct, select Validate all.

Tip

You can re-order the rows of a table by dragging them.

To add or remove a row, hover over to the left of a table row and select [![Document AI drag indicator icon](../../../_images/drag-indicator-icon.png)](../../../_images/drag-indicator-icon.png).

#### Export and import CSV files

You can export the results to a CSV file, review the table, and import the CSV file back into Document AI.

To export a CSV file:

1. Sign in to [Snowsight](../../ui-snowsight-gs.html#label-snowsight-getting-started-sign-in).
2. In the navigation menu, select AI & ML » AI Studio.
3. Next to the Document Processing Playground, select Open. In the Document Processing Playground, to access Document AI, select Go to Document AI model builds.
4. Select a warehouse.
5. From the list of model builds, select the name of the model build to define values for.
6. Select the Build Details tab.
7. Select Define values.
8. In the Documents review view, for a given table (value), select Preview.
9. Select Review table.
10. Select Export CSV.

Before you import a CSV file, ensure that:

* The column names and the number of columns in the imported CSV file match the original table.
* The empty cells are specified:

  + For all cells in an empty row that occurs between other non-empty rows, use `-`.
  + For single empty cells in a row, use `None`.
* The data separator is comma. If the data itself includes commas, use double quotation marks for separation, for example:

  Expand

  ```
  column1data, column2data, "data, with commas, inside", column4data
  ```

  Show lessSee more

  Scroll to top

To import a CSV file:

1. Sign in to [Snowsight](../../ui-snowsight-gs.html#label-snowsight-getting-started-sign-in).
2. In the navigation menu, select AI & ML » AI Studio.
3. Next to the Document Processing Playground, select Open. In the Document Processing Playground, to access Document AI, select Go to Document AI model builds.
4. Select a warehouse.
5. From the list of model builds, select the name of the model build to define values for.
6. Select the Build Details tab.
7. Select Define values.
8. In the Documents review view, for a given table (value), select Preview.
9. Select Review table.
10. Select Import CSV.

Note

The imported table replaces the previously edited table.

If there is no data to extract (a table doesn’t exist in the document), import an empty CSV file with header only.

## Evaluate a Document AI model

To evaluate a Document AI model (either the foundation model or the fine-tuned model), analyze the accuracy.
Accuracy describes how often the model provides a correct answer. A higher accuracy indicates that the model is better at extraction.
To see the accuracy, review the answers to all questions.

To view the accuracy:

1. Sign in to [Snowsight](../../ui-snowsight-gs.html#label-snowsight-getting-started-sign-in).
2. In the navigation menu, select AI & ML » AI Studio.
3. Next to the Document Processing Playground, select Open. In the Document Processing Playground, to access Document AI, select Go to Document AI model builds.
4. Select a warehouse.
5. From the list of model builds, select the name of the model build to evaluate.
6. Select the Build Details tab, which displays Model accuracy.

If the Document AI model reliably answers your questions and the accuracy is satisfactory, publish the model build.
See [Publish a Document AI model build](#label-document-ai-publish-model-build).

To improve the results of the Document AI model, train the model. See [Train a Document AI model](#label-document-ai-train-model).

Tip

To evaluate the Document AI model after training, review the newly uploaded documents.

## Publish a Document AI model build

1. Sign in to [Snowsight](../../ui-snowsight-gs.html#label-snowsight-getting-started-sign-in).
2. In the navigation menu, select AI & ML » AI Studio.
3. Next to the Document Processing Playground, select Open. In the Document Processing Playground, to access Document AI, select Go to Document AI model builds.
4. Select a warehouse.
5. From the list of model builds, select the name of the model build to publish.
6. Select the Build Details tab.
7. Under Model accuracy, select Publish version.
8. In the dialog that appears, select Publish to confirm.

After you publish the model build, you can see an [extracting query](extract-information.html#label-document-ai-extracting-query).

If you added new data values (asked new questions) after you trained the model or published the model build, you must
publish the model build again.

Note

To enable another role to use the model after the Document AI model build is published, copy
the Document AI model, and grant the OWNERSHIP privilege on the copied model to that role.
For more information on copying models, see [Copy Document AI models between databases, schemas, and accounts](copy-models).

## Train a Document AI model

The foundation model (the Snowflake Arctic-TILT model) has already been pre-trained and fine-tuned, but you can improve its
accuracy by fine-tuning the foundation model on your documents through supervised fine-tuning. During training,
the parameters of the foundation model are adapted to the documents and annotations you provided. All the models
resulting from training are saved in the model build within your account.

Snowflake recommends reviewing the results for at least 20 documents before training.

Tip

To assess the quality of the model, split your documents into two sets. Review one set of documents,
and use the unreviewed documents to assess the model after training.

To start training the model:

1. Sign in to [Snowsight](../../ui-snowsight-gs.html#label-snowsight-getting-started-sign-in).
2. In the navigation menu, select AI & ML » AI Studio.
3. Next to the Document Processing Playground, select Open. In the Document Processing Playground, to access Document AI, select Go to Document AI model builds.
4. Select a warehouse.
5. From the list of model builds, select the name of the model build to train.
6. Select the Build Details tab.
7. Under Model accuracy, select Train model.
8. In the dialog that appears, select Start training to confirm.

When the training is complete, a notification appears.

You can now re-evaluate your Document AI model. To see the accuracy for the fine-tuned model after the training, review the
second set of documents. Note that you can fine-tune your model multiple times to get satisfactory results.

You do not need to publish the model build if you trained the model and did not add new data values (ask new questions) after the training.

Note

You can start multiple trainings for multiple model builds at the same time.
Note that the trainings are queued, and you can run no more than three trainings at the same time.

### Training time estimation

Training time of a Document AI model depends on both the number of values to be extracted and the number of pages in a document.

The following table lists the estimated training time for a batch of 20 documents (the minimum number required for training) and
10 values, depending on the number of pages in each document.

| Number of pages in each document | Estimated training time for 20 documents (hours) |
| --- | --- |
| 1 | 0.5 |
| 10 | 1.5 |
| 25 | 4 |
| 50 | 8 |
| 75 | 12.5 |
| 100 | 16.5 |
| 125 | 20.5 |

See moreShow less

Expand

Note

The table lists estimated training time. Note that the actual time needed for training might vary. Generally,
doubling the number of values or the number of documents doubles training time.

The maximum training time is 48 hours.
