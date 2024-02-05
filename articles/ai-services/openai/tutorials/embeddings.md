---
title: Azure OpenAI Service embeddings tutorial
titleSuffix: Azure OpenAI
description: Learn how to use Azure OpenAI's embeddings API for document search with the BillSum dataset
#services: cognitive-services
manager: nitinme
ms.service: azure-ai-openai
ms.topic: tutorial
ms.date: 11/25/2023
author: mrbullwinkle #noabenefraim
ms.author: mbullwin
zone_pivot_groups: "openai-embeddings"
recommendations: false
ms.custom: devx-track-python
---

# Tutorial: Explore Azure OpenAI Service embeddings and document search

This tutorial will walk you through using the Azure OpenAI [embeddings](../concepts/understand-embeddings.md) API to perform **document search** where you'll query a knowledge base to find the most relevant document.

In this tutorial, you learn how to:

> [!div class="checklist"]
> * Install Azure OpenAI.
> * Download a sample dataset and prepare it for analysis.
> * Create environment variables for your resources endpoint and API key.
> * Use the **text-embedding-ada-002 (Version 2)** model
> * Use [cosine similarity](../concepts/understand-embeddings.md) to rank search results.

> [!IMPORTANT]
> We strongly recommend using `text-embedding-ada-002 (Version 2)`. This model/version provides parity with OpenAI's `text-embedding-ada-002`. To learn more about the improvements offered by this model, please refer to [OpenAI's blog post](https://openai.com/blog/new-and-improved-embedding-model). Even if you are currently using Version 1 you should migrate to Version 2 to take advantage of the latest weights/updated token limit. Version 1 and Version 2 are not interchangeable, so document embedding and document search must be done using the same version of the model.  

::: zone pivot="programming-language-python"
[!INCLUDE [Python](../includes/embeddings-python.md)]
::: zone-end

::: zone pivot="programming-language-powershell"
[!INCLUDE [PowerShell](../includes/embeddings-powershell.md)]
::: zone-end

Using this approach, you can use embeddings as a search mechanism across documents in a knowledge base. The user can then take the top search result and use it for their downstream task, which prompted their initial query.

## Clean up resources

If you created an OpenAI resource solely for completing this tutorial and want to clean up and remove an OpenAI resource, you'll need to delete your deployed models, and then delete the resource or associated resource group if it's dedicated to your test resource. Deleting the resource group also deletes any other resources associated with it.

- [Portal](../../multi-service-resource.md?pivots=azportal#clean-up-resources)
- [Azure CLI](../../multi-service-resource.md?pivots=azcli#clean-up-resources)

## Next steps

Learn more about Azure OpenAI's models:
> [!div class="nextstepaction"]
> [Azure OpenAI Service models](../concepts/models.md)
* Store your embeddings and perform vector (similarity) search using your choice of Azure service:
  * [Azure AI Search](../../../search/vector-search-overview.md)
  * [Azure Cosmos DB for MongoDB vCore](../../../cosmos-db/mongodb/vcore/vector-search.md)
  * [Azure Cosmos DB for NoSQL](../../../cosmos-db/vector-search.md)
  * [Azure Cosmos DB for PostgreSQL](../../../cosmos-db/postgresql/howto-use-pgvector.md)
  * [Azure Cache for Redis](../../../azure-cache-for-redis/cache-tutorial-vector-similarity.md)
