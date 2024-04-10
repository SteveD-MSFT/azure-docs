---
title: Radiology Insights model configuration
titleSuffix: Azure AI Health Insights
description: This article provides Radiology Insights model configuration information.
services: azure-health-insights
author: JanSchietse
manager: JoeriVDV
ms.service: azure-health-insights
ms.topic: overview
ms.date: 12/06/2023
ms.author: JanSchietse
---

# Radiology Insights model configuration

To interact with the Radiology Insights model, you can provide several model configuration parameters that modify the outcome of the responses.

> [!IMPORTANT]
> Model configuration is applied to ALL the patients within a request.

```json
  "configuration": {
    "inferenceOptions": {
      "followupRecommendationOptions": {
        "includeRecommendationsWithNoSpecifiedModality": false,
        "includeRecommendationsInReferences": false,
        "provideFocusedSentenceEvidence": false
      },
      "findingOptions": {
        "provideFocusedSentenceEvidence": false
      }
    },
    "locale": "en-US",
    "verbose": false,
    "includeEvidence": true
  }
```

## Case finding

Through the model configuration, the API allows you to seek evidence from the provided clinical documents as part of the inferences.

**Include Evidence** |**Behavior** 
---------------------- |-----------------------
true | Evidence is returned as part of the inferences
false  | No Evidence is returned

## Inference Options

**FindingOptions**
- provideFocusedSentenceEvidence
- type: boolean
- Provide a single focused sentence as evidence for the finding, default is false.

**FollowupRecommendationOptions**
- includeRecommendationsWithNoSpecifiedModality
    - type: boolean
    - description: Include/Exclude follow-up recommendations with no specific radiologic modality, default is false.


- includeRecommendationsInReferences
    - type: boolean
    - description: Include/Exclude follow-up recommendations in references to a guideline or article, default is false.

- provideFocusedSentenceEvidence
    - type: boolean
    - description: Provide a single focused sentence as evidence for the recommendation, default is false.

When includeEvidence is false, no evidence is returned. 

This configuration overrules includeRecommendationsWithNoSpecifiedModality and provideFocusedSentenceEvidence and no evidence is shown. 

When includeEvidence is true, it depends on the value set on the two other configurations whether the evidence of the inference or a single focused sentence is given as evidence. 

## Examples 


**Example 1** 

CDARecommendation_GuidelineFalseUnspecTrueLimited

The includeRecommendationsWithNoSpecifiedModality is true, includeRecommendationsInReferences is false,  provideFocusedSentenceEvidence for recommendations is true and includeEvidence is true. 

As a result, the model includes evidence for all inferences. 
- The model checks for follow-up recommendations with a specified modality.
- The model checks for follow-up recommendations with no specific radiologic modality.
- The model provides a single focused sentence as evidence for the recommendation. 

<details><summary>Examples request/response json</summary>
[!INCLUDE [Example input json](../includes/example-2-inference-follow-up-recommendation-json-request.md)]
[!INCLUDE [Example output json](../includes/example-2-inference-follow-up-recommendation-json-response.md)]
</details>



**Example 2**

CDARecommendation_GuidelineTrueUnspecFalseLimited

The includeRecommendationsWithNoSpecifiedModality is false, includeRecommendationsInReferences is true, provideFocusedSentenceEvidence for findings is true and includeEvidence is true. 

As a result, the model includes evidence for all inferences. 
- The model checks for follow-up recommendations with a specified modality.
- The model checks for a recommendation in a guideline.
- The model provides a single focused sentence as evidence for the finding. 


<details><summary>Examples request/response json</summary>
[!INCLUDE [Example input json](../includes/example-1-inference-follow-up-recommendation-json-request.md)]
[!INCLUDE [Example output json](../includes/example-1-inference-follow-up-recommendation-json-response.md)]
</details>



## Next steps

Refer to the following page to get better insights into the request and responses:

>[!div class="nextstepaction"]
> [Inference information](inferences.md) 
