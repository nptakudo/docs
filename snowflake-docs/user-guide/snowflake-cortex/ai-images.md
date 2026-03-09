---
title: "Cortex AI Functions: Images"
url: "https://docs.snowflake.com/en/user-guide/snowflake-cortex/ai-images"
---

# Cortex AI Functions: Images

With Cortex AI Images, you can accomplish the following:

* Compare images
* Caption images
* Classify images
* Extract entities from images
* Generate embedding vectors for use in retrieval systems
* Answer questions using data in graphs and charts

You can do those tasks with the following functions:

* [AI\_COMPLETE](../../sql-reference/functions/ai_complete)
* [AI\_EMBED](../../sql-reference/functions/ai_embed)
* [AI\_FILTER](../../sql-reference/functions/ai_filter)
* [AI\_CLASSIFY](../../sql-reference/functions/ai_classify)
* [AI\_SIMILARITY](../../sql-reference/functions/ai_similarity)

## Input requirements

COMPLETE Multimodal can process images with the following characteristics:

| Requirement | Value |
| --- | --- |
| Filename extensions | `.jpg`, `.jpeg`, `.png`, `.webp`, `.gif` |
| Stage encryption | Server-side encryption |
| Data type | [FILE](../../sql-reference/data-types-unstructured.html#label-data-types-file) |

See moreShow less

Expand

Note

Processing files from stages is currently incompatible with custom network policies.

## Analyze images

The COMPLETE function processes a single image or multiple images (for example, extracting differences in entities across various images) stored in a stage. See [Create stage for media files](aisql.html#label-cortex-llm-media-files) for information on creating a suitable stage.

The function call specifies the following:

* The multimodal model to be used
* A prompt
* The stage path of the image file(s) via a [FILE](../../sql-reference/data-types-unstructured.html#label-data-types-file) object

### Vision Q&A example

The following example uses Anthropic’s Claude Sonnet 3.5 model to summarize a pie chart `science-employment-slide.jpeg` stored in the `@myimages` stage.

![Pie chart showing the distribution of occupations where mathematics is considered "extremely important" in 2023](../../_images/science-employment-slide.jpeg)

The distribution of occupations where mathematics is considered “extremely important” in 2023

CopyExpand

```
SELECT AI_COMPLETE('claude-3-5-sonnet',
    'Summarize the insights from this pie chart in 100 words',
    TO_FILE('@myimages', 'science-employment-slide.jpeg'));
```

Show lessSee more

Scroll to top

Response:

Expand

```
This pie chart shows the distribution of occupations where mathematics is considered "extremely important" in 2023.
Data scientists dominate with nearly half (48.7%) of all such positions, followed by operations research analysts
at 29.6%. The remaining positions are distributed among statisticians (7.8%), actuaries (7.2%), physicists (5.1%),
mathematicians (0.6%), and other mathematical science occupations (1.1%). This distribution highlights the growing
importance of data science in mathematics-intensive careers, while traditional mathematics roles represent a smaller
share of the workforce.
```

Show lessSee more

Scroll to top

### Compare images example

Note

Currently, only Anthropic (`claude`) and Meta (`llama`) models can reference multiple images in a single prompt.
Multiple image support for other models may be available in a future release.

Use the [PROMPT helper function](../../sql-reference/functions/prompt) to process multiple images in a single COMPLETE call. The following example uses
Anthropic’s Claude Sonnet 3.5 model to compare two different ad creatives from the `@myimages` stage.

![Images of two ads for electric cars](../../_images/two-ad-creatives.png)

Image of two ads for electric cars

CopyExpand

```
SELECT AI_COMPLETE('claude-3-5-sonnet',
    PROMPT('Compare this image {0} to this image {1} and describe the ideal audience for each in two concise bullets no longer than 10 words',
    TO_FILE('@myimages', 'adcreative_1.png'),
    TO_FILE('@myimages', 'adcreative_2.png')
));
```

Show lessSee more

Scroll to top

Response:

Expand

```
First image ("Discover a New Energy"):
• Conservative luxury SUV buyers seeking a subtle transition to electrification

Second image ("Electrify Your Drive"):
• Young, tech-savvy urbanites attracted to bold, progressive automotive design
```

Show lessSee more

Scroll to top

### Classify images example

The following example uses AI\_CLASSIFY to classify an image for a real estate application.

![Image of a staged living room for real estate](../../_images/classify_example.png)

The following SQL uses the AI\_CLASSIFY function to classify the image as a picture of a living area, kitchen, bath, garden, or master bedroom.

CopyExpand

```
SELECT AI_CLASSIFY(TO_FILE('@my_images', 'REAL_ESTATE_STAGING.PNG'),
    ['Living Area', 'Kitchen', 'Bath', 'Garden', 'Master Bedroom']) AS room_classification;
```

Show lessSee more

Scroll to top

Response:

Expand

```
{ "labels": [ "Living Area" ] }
```

Show lessSee more

Scroll to top

The SQL below categorizes the objects found in the above image as a couch, window, table, television, or artwork.

CopyExpand

```
SELECT AI_CLASSIFY (TO_FILE ('@my_images', 'REAL_ESTATE_STAGING.PNG'),
    ['Couch', 'Window', 'Table', 'Television', 'Art'],  {'output_mode': 'multi'} )
    AS living_room_objects;
```

Show lessSee more

Scroll to top

Response:

Expand

```
{
  "labels": [
    "Art",
    "Couch",
    "Table",
    "Window"
  ]
}
```

Show lessSee more

Scroll to top

## Search images

You can use AI\_EMBED to find images that are similar to a target image. First, use the AI\_EMBED function to generate an
embedding vector for the target image, mapping its visual features into an abstract vector space, a numerical
representation of the image’s features. You can then use vector similarity functions to compare this embedding vector
to the embedding vectors of other images, producing a similarity score based on their common or similar visual features.
This score can be used to classify, rank, or filter images based on their similarity to the target image.

|  |  |
| --- | --- |
| [Crowd image of people in a city](../../_images/compare_example_1.png) | [Crowd image of people in a city](../../_images/compare_example_2.png) |

See moreShow less

Expand

For example, given the images above, the following SQL generates an embedding vector for each image, then compares the
vectors using cosine similarity. The result, about 0.5, indicates that the images are somewhat similar. Both photos are
taken in an urban setting and contain background crowds, but the main subjects are different.

CopyExpand

```
WITH ai_image_embeddings as (
    SELECT
        AI_EMBED('voyage-multimodal-3',
            TO_FILE ('@my_images', 'CITY_WALKING1.PNG')) as image1_embeddings,
        AI_EMBED('voyage-multimodal-3',
            TO_FILE ('@my_images', 'CITY_WALKING2.PNG')) as image2_embeddings
)
SELECT VECTOR_COSINE_SIMILARITY(image1_embeddings,image2_embeddings) as similarity FROM ai_image_embeddings;
```

Show lessSee more

Scroll to top

Expand

```
0.5359029029
```

Show lessSee more

Scroll to top

To find images that are similar to a target image, you can use AI\_SIMILARITY. The example below computes a similarity
score for possibly thousands of images, and returns the advertising creatives that are most similar to the motorcycle
advertisement below.

> [![Image of a motorcycle advertisement for image search](../../_images/image_search_example.png)](../../_images/image_search_example.png)

CopyExpand

```
SELECT
    TO_FILE('@ad_images', relative_path) as ALL_ADS
    FROM DIRECTORY(@ad_images)
WHERE AI_SIMILARITY(TO_FILE('@ad_images', 'image_226.jpg'), ALL_ADS) >= 0.5;
```

Show lessSee more

Scroll to top

The query returns images from a multimodal table where the similarity score is greater than 0.50. One of the images
identified (`image_226.jpg`) is the one we used as a reference.

Expand

```
+-----------------------------------------------------------+
| {} ALL_ADS                                                |
+-----------------------------------------------------------+
|  { "CONTENT_TYPE": "image/jpeg",                          |
|    "ETAG": "686897696a7c876b7e",                          |
|    "LAST_MODIFIED": "Wed, 26 Mar 2025 18:11:45 GMT",      |
|    "RELATIVE_PATH": "image_226.jpg",                      |
|    "SIZE": 39086,                                         |
|    "STAGE": "@ad_images" }                                |
+-----------------------------------------------------------+
|  { "CONTENT_TYPE": "image/jpeg",                          |
|    "ETAG": "e7b678c7a696798686",                          |
|    "LAST_MODIFIED": "Wed, 26 Mar 2025 18:11:57 GMT",      |
|    "RELATIVE_PATH": "image_441.jpg",                      |
|    "SIZE": 12650,                                         |
|    "STAGE": "@ad_images" },                               |
+-----------------------------------------------------------+
```

Show lessSee more

Scroll to top

## Model limitations

All models available to Snowflake Cortex have limitations on the total number of input and output tokens, known as the
model’s *context window*. The context window size is measured in tokens. Inputs exceeding the context window limit
result in an error. Output which would exceed the context window limit is truncated.

For text models, tokens generally represent approximately four characters of text, so the word count corresponding to a
limit is less than the token count.

For image models, the token count per image depends on the vision model’s architecture. Tokens within a prompt (for
example, “what animal is this?”) also contribute to the model’s context window.

| Model | Context window (tokens) | File types | File size | Images per prompt |
| --- | --- | --- | --- | --- |
| `openai-gpt-4.1` | 1,047,576 | .jpg, .jpeg, .png, .webp, .gif | 10MB | 5 |
| `openai-o4-mini` | 1,047,576 | .jpg, .jpeg, .png, .webp, .gif | 10MB | 5 |
| `claude-4-opus` | 200,000 | .jpg, .jpeg, .png, .webp, .gif | 3.75 MB [[L1]](#l1) | 20 |
| `claude-4-sonnet` | 200,000 | .jpg, .jpeg, .png, .webp, .gif | 3.75 MB [[L1]](#l1) | 20 |
| `claude-3-7-sonnet` | 200,000 | .jpg, .jpeg, .png, .webp, .gif | 3.75 MB [[L1]](#l1) | 20 |
| `claude-3-5-sonnet` | 200,000 | .jpg, .jpeg, .png, .webp, .gif | 3.75 MB [[L1]](#l1) | 20 |
| `llama4-maverick` | 128,000 | .jpg, .jpeg, .png, .webp, .gif, .bmp | 10 MB | 10 |
| `llama-4-scout` | 128,000 | .jpg, .jpeg, .png, .webp, .gif, .bmp | 10 MB | 10 |
| `pixtral-large` | 128,000 | .jpg, .jpeg, .png, .webp, .gif, .bmp | 10 MB | 1 |
| `voyage-multimodal-3` | 32,768 | .jpg, .png, .pg, .gif, .bmp | 10 MB | 1 |

See moreShow less

Expand

[L1]
([1](#id1),[2](#id2),[3](#id3),[4](#id4))

Images must be smaller than 8000x8000 pixels. Limits apply to each individual image.

## Cost considerations

Billing scales with the number of tokens processed. The number of tokens per image depends on the architecture of the vision model.

* Anthropic (`claude`) models’ formula is roughly: tokens = (Width in pixels x Height in pixels) / 750.
* Mistral (`pixtral`) models divide each image into batches of 16x16 pixels and converts each batch to a token.
  The total number of tokens is equivalent to roughly (Width in pixels / 16) \* (Height in pixels / 16).
* Meta (`llama`) models try to tile the image with square tiles. Depending on the image’s aspect ratio and size, the number of
  tiles can be up to 16, each represented by around 153 tokens.
* Open AI models rescale the image and tile it with square patches. For `openai-gpt-4.1`, depending on the image ratio
  and size, the number of tokens can be 211 (images up to 512x512px), 352 (non-square images with longer side length
  1024px), or from 630 tokens (square images at least 1024x1024px) to 913 tokens (non-square images with shorter side
  length 1024px). For `openai-o4-mini`, the rescaling logic is more involved, and the number of tokens varies from 86
  (128x512px) to 1428 (2048x1024px), and does not follow a linear pattern.
* `voyage-multimodal-3` operates on an array of image patches that are roughly 14x14px in size. The image is rescaled
  so that it is covered by a grid, which has a minimum of 64 patches and a maximum of 2500 patches. Two extra image
  tokens are added, so the input ranges from 66 to 2502 tokens, depending on the image size and aspect ratio.

Note

The COUNT\_TOKENS function does not currently support image inputs.

## Choosing a vision model

The COMPLETE function supports multiple models of varying capability, latency, and cost. To achieve optimal performance
per credit, choose a model that aligns with the content size and task complexity.

| Model | MMMU | Mathvista | ChartQA | DocVQA | VQAv2 |
| --- | --- | --- | --- | --- | --- |
| GPT-4o | 68.6 | 64.6 | 85.1 | 88.9 | 77.8 |
| `openai-gpt-4.1` | 75.0 | 72.0 |  |  |  |
| `openai-o4-mini` | 81.6 | 84.3 |  |  |  |
| `claude-3-5-sonnet` | 68.0 | 64.4 | 87.6 | 90.3 | 70.7 |
| `llama-4-maverick` | 73.4 | 73.7 | 90 | 94.4 |  |
| `llama-4-scout` | 69.4 | 70.7 | 88.8 | 94.4 |  |
| `pixtral-large` | 64.0 | 69.4 | 88.1 | 85.7 | 67 |

See moreShow less

Expand

The benchmarks are:

* MMMU: Evaluates multimodal models on multidisciplinary tasks that require college-level reasoning.
* Mathvista: Mathematical reasoning benchmark within a visual context.
* ChartQA: Evaluates complex reasoning questions about charts.
* DocVQA and VQv2: Benchmarks for visual question-answering on documents.

For multimodal embeddings, only the `voyage-multimodal-3` model is currently available. `voyage-multimodal-3` is a
state-of-art multimodal embedding model capable of embedding text and images. It can extract key visual features from
sources such as screenshots of PDFs, slides, tables, and figures, reducing the need for complex document parsing
workflows. According to Voyage AI internal benchmarks, the `voyage-multimodal-3` model outperforms competing models
such as OpenAI CLIP Large, Amazon Titan Multimodal, and Cohere Multimodal v3.

## Regional availability

Support for this feature is available natively to accounts in the following Snowflake regions:

| Model | AWS US West 2  (Oregon) | AWS US East 1  (N. Virginia) | AWS Europe Central 1  (Frankfurt) |
| --- | --- | --- | --- |
| `claude-3-5-sonnet` | ✔ | ✔ |  |
| `claude-3-7-sonnet` [[A1]](#a1) |  |  |  |
| `claude-4-sonnet` [[A1]](#a1) |  |  |  |
| `claude-4-opus` [[A1]](#a1) |  |  |  |
| `pixtral-large` | ✔ | ✔ | ✔ |
| `llama4-maverick` | ✔ |  |  |
| `llama4-scout` | ✔ |  |  |
| `voyage-multimodal-3` [[A1]](#a1) |  |  |  |

See moreShow less

Expand

[A1]
([1](#id5),[2](#id6),[3](#id7),[4](#id8))

Model is available via cross-region inference only.

AI\_COMPLETE is available in additional regions through [cross-region inference](cross-region-inference).

## Error Conditions

| Message | Explanation |
| --- | --- |
| Request failed for external function SYSTEM$COMPLETE\_WITH\_IMAGE\_INTERNAL with remote service error: 400 ‘“invalid image path” | Either the file extension or the file itself is not accepted by the model. The message might also mean that the file path is incorrect; that is, the file does not exist at the specified location. Filenames are case-sensitive. |
| Error in secure object | May indicate that the stage does not exist. Check the stage name and ensure that the stage exists and is accessible. Be sure to use the at (@) sign at the beginning of the stage path, such as `@myimages`. |
| Request failed for external function \_COMPLETE\_WITH\_PROMPT with remote service error: 400 ‘“invalid request parameters: unsupported image format: image/\*\* | Unsupported image format given to `claude-3-5-sonnet`, i.e. other than .jpeg, .png, .webp, or .gif. |
| Request failed for external function \_COMPLETE\_WITH\_PROMPT with remote service error: 400 ‘“invalid request parameters: Image data exceeds the limit of 5.00 MB” | The provided image given to `claude-3-5-sonnet` exceeds 5 MB. |

See moreShow less

Expand

## Legal

The data classification of inputs and outputs are as set forth in the following table.

| Input data classification | Output data classification | Designation |
| --- | --- | --- |
| Usage Data | Customer Data | Generally available functions are Covered AI Features. Preview functions are Preview AI Features. [[1]](#id10) |

See moreShow less

Expand

[[1](#id9)]

Represents the defined term used in the AI Terms and Acceptable Use Policy.

For additional information, refer to [Snowflake AI and ML](../../guides-overview-ai-features).
