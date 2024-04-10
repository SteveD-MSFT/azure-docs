---
title: "Document Intelligence (formerly Form Recognizer) Studio"
titleSuffix: Azure AI services
description: "Concept: Form and document processing, data extraction, and analysis using Document Intelligence Studio "
author: laujan
manager: nitinme
ms.service: azure-ai-document-intelligence
ms.custom:
  - ignite-2023
ms.topic: conceptual
ms.date: 01/19/2024
ms.author: lajanuar
monikerRange: '>=doc-intel-3.0.0'
---


# Document Intelligence Studio

::: moniker range="doc-intel-4.0.0"
[!INCLUDE [preview-version-notice](includes/preview-notice.md)]

**This content applies to:**![checkmark](media/yes-icon.png) **v4.0 (preview)** | **Previous versions:** ![blue-checkmark](media/blue-yes-icon.png) [**v3.1 (GA)**](?view=doc-intel-3.1.0&preserve-view=tru) ![blue-checkmark](media/blue-yes-icon.png) [**v3.0 (GA)**](?view=doc-intel-3.0.0&preserve-view=tru)
::: moniker-end

::: moniker range="doc-intel-3.1.0"
**This content applies to:** ![checkmark](media/yes-icon.png) **v3.1 (GA)** | **Latest version:** ![purple-checkmark](media/purple-yes-icon.png) [**v4.0 (preview)**](?view=doc-intel-4.0.0&preserve-view=true) | **Previous versions:** ![blue-checkmark](media/blue-yes-icon.png) [**v3.0**](?view=doc-intel-3.0.0&preserve-view=true)
::: moniker-end

::: moniker range="doc-intel-3.0.0"
**This content applies to:** ![checkmark](media/yes-icon.png) **v3.0 (GA)** | **Latest versions:** ![purple-checkmark](media/purple-yes-icon.png) [**v4.0 (preview)**](?view=doc-intel-4.0.0&preserve-view=true) ![purple-checkmark](media/purple-yes-icon.png) [**v3.1 (preview)**](?view=doc-intel-3.1.0&preserve-view=true)
::: moniker-end

[Document Intelligence Studio](https://documentintelligence.ai.azure.com/) is an online tool for visually exploring, understanding, and integrating features from the Document Intelligence service into your applications. Use the Document Intelligence Studio to:

* Learn more about the different capabilities in Document Intelligence.
* Use your Document Intelligence resource to test models on sample documents or upload your own documents.
* Experiment with different add-on and preview features to adapt the output to your needs.
* Train custom classification models to classify documents.
* Train custom extraction models to extract fields from documents.
* Get sample code for the language-specific SDKs to integrate into your applications.

Use the [Document Intelligence Studio quickstart](quickstarts/try-document-intelligence-studio.md) to get started analyzing documents with document analysis or prebuilt models. Build custom models and reference the models in your applications using one of the [language specific SDKs](quickstarts/get-started-sdks-rest-api.md?view=doc-intel-3.0.0&preserve-view=true) and other quickstarts.

The following image shows the landing page for Document Intelligence Studio.

:::image border="true" type="content" source="media/studio/welcome-to-studio.png" alt-text="Document Intelligence Studio Homepage":::

## Getting started

If you're visiting the Studio for the first time, follow the [getting started guide](studio-overview.md#get-started-using-document-intelligence-studio) to set up the Studio for use.

## Analyze options

* Document Intelligence supports sophisticated analysis capabilities. The Studio allows one entry point (Analyze options button) for configuring the add-on capabilities with ease.
* Depending on the document extraction scenario, configure the analysis range, document page range, optional detection, and premium detection features.

    :::image type="content" source="media/studio/analyze-options.png" alt-text="Screenshot of the analyze-options dialog window.":::

    > [!NOTE]
    > Font extraction is not visualized in Document Intelligence Studio. However, you can check the styles section of the JSON output for the font detection results.

✔️ **Auto labeling documents with prebuilt models or one of your own models**

* In custom extraction model labeling page, you can now auto label your documents using one of Document Intelligent Service prebuilt models or your trained models.

    :::image type="content" source="media/studio/auto-label.gif" alt-text="Animated screenshot showing auto labeling in Studio.":::

* For some documents, duplicate labels after running autolabel are possible. Make sure to modify the labels so that there are no duplicate labels in the labeling page afterwards.

    :::image type="content" source="media/studio/duplicate-labels.png" alt-text="Screenshot showing duplicate label warning after auto labeling.":::

✔️ **Auto labeling tables**

* In custom extraction model labeling page, you can now auto label the tables in the document without having to label the tables manually.

    :::image type="content" source="media/studio/auto-table-label.gif" alt-text="Animated screenshot showing auto table labeling in Studio.":::

✔️ **Add test files directly to your training dataset**

* Once you train a custom extraction model, make use of the test page to improve your model quality by uploading test documents to training dataset if needed.

* If a low confidence score is returned for some labels, make sure they're correctly labeled. If not, add them to the training dataset and relabel to improve the model quality.

:::image type="content" source="media/studio/add-from-test.gif" alt-text="Animated screenshot showing how to add test files to training dataset.":::

✔️ **Make use of the document list options and filters in custom projects**

* In custom extraction model labeling page, you can now navigate through your training documents with ease by making use of the search, filter and sort by feature.

* Utilize the grid view to preview documents or use the list view to scroll through the documents more easily.

    :::image type="content" source="media/studio/document-options.png" alt-text="Screenshot of document list view options and filters.":::

✔️ **Project sharing**

* Share custom extraction projects with ease. For more information, see [Project sharing with custom models](how-to-guides/project-share-custom-models.md).

## Document Intelligence model support

* **Read**: Try out Document Intelligence's Read feature to extract text lines, words, detected languages, and handwritten style if detected. Start with the [Studio Read feature](https://documentintelligence.ai.azure.com/studio/read). Explore with sample documents and your documents. Use the interactive visualization and JSON output to understand how the feature works. See the [Read overview](concept-read.md) to learn more and get started with the [Python SDK quickstart for Layout](quickstarts/get-started-sdks-rest-api.md?view=doc-intel-3.0.0&preserve-view=true).

* **Layout**: Try out Document Intelligence's Layout feature to extract text, tables, selection marks, and structure information. Start with the [Studio Layout feature](https://documentintelligence.ai.azure.com/studio/layout). Explore with sample documents and your documents. Use the interactive visualization and JSON output to understand how the feature works. See the [Layout overview](concept-layout.md) to learn more and get started with the [Python SDK quickstart for Layout](quickstarts/get-started-sdks-rest-api.md?view=doc-intel-3.0.0&preserve-view=true#layout-model).

* **Prebuilt models**: Document Intelligence's prebuilt models enable you to add intelligent document processing to your apps and flows without having to train and build your own models. As an example, start with the [Studio Invoice feature](https://documentintelligence.ai.azure.com/studio/prebuilt?formType=invoice). Explore with sample documents and your documents. Use the interactive visualization, extracted fields list, and JSON output to understand how the feature works. See the [Models overview](concept-model-overview.md) to learn more and get started with the [Python SDK quickstart for Prebuilt Invoice](quickstarts/get-started-sdks-rest-api.md?view=doc-intel-3.0.0&preserve-view=true#prebuilt-model).

* **Custom extraction models**: Document Intelligence's custom models enable you to extract fields and values from models trained with your data, tailored to your forms and documents. Create standalone custom models or combine two or more custom models to create a composed model to extract data from multiple form types. Start with the [Studio Custom models feature](https://documentintelligence.ai.azure.com/studio/custommodel/projects).  Use the help wizard, labeling interface, training step, and visualizations to understand how the feature works. Test the custom model with your sample documents and iterate to improve the model. See the [Custom models overview](concept-custom.md) to learn more.

* **Custom classification models**: Document classification is a new scenario supported by Document Intelligence. the document classifier API supports classification and splitting scenarios. Train a classification model to identify the different types of documents your application supports. The input file for the classification model can contain multiple documents and classifies each document within an associated page range. See [custom classification models](concept-custom-classifier.md) to learn more.

* **Add-on Capabilities**: Document Intelligence now supports more sophisticated analysis capabilities. These optional capabilities can be enabled and disabled in the studio using the `Analze Options` button in each model page. There are four add-on capabilities available: highResolution, formula, font, and barcode extraction capabilities. See [Add-on capabilities](concept-add-on-capabilities.md) to learn more.

## Next steps

* Visit the [Document Intelligence Studio](https://documentintelligence.ai.azure.com/) to begin using the models and features.

* Get started with our [Document Intelligence Studio quickstart](quickstarts/try-document-intelligence-studio.md).
