---
title: "Quickstart: Build and deploy your app from your local filesystem to Azure Container Apps"
description: Build your container app from local source and deploy in Azure Container Apps using az containerapp up.
services: container-apps
author: craigshoemaker
ms.service: container-apps
ms.custom:
  - devx-track-azurecli
  - ignite-2023
ms.topic: quickstart
ms.date: 01/26/2024
ms.author: cshoe
zone_pivot_groups: container-apps-code-to-cloud-segmemts
---


# Quickstart: Build and deploy from local source code to Azure Container Apps

This article demonstrates how to build and deploy a microservice to Azure Container Apps from local source code using the programming language of your choice. In this quickstart, you create a backend web API service that returns a static collection of music albums.  

> [!NOTE]
> This sample application is available in two versions. One version where the source contains a Dockerfile. The other version has no Dockerfile. Select the version that best reflects your source code. If you are new to containers, select the **No  Dockerfile** option at the top.

The following screenshot shows the output from the album API service you deploy.

:::image type="content" source="media/quickstart-code-to-cloud/azure-container-apps-album-api.png" alt-text="Screenshot of response from albums API endpoint.":::

## Prerequisites

To complete this project, you need the following items:

| Requirement  | Instructions |
|--|--|
| Azure account | If you don't have one, [create an account for free](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). You need the *Contributor* or *Owner* permission on the Azure subscription to proceed. <br><br>Refer to [Assign Azure roles using the Azure portal](../role-based-access-control/role-assignments-portal.md?tabs=current) for details. |
| Azure CLI | Install the [Azure CLI](/cli/azure/install-azure-cli).|


## Setup

To sign in to Azure from the CLI, run the following command and follow the prompts to complete the authentication process.

# [Bash](#tab/bash)

```azurecli
az login
```

# [Azure PowerShell](#tab/azure-powershell)

```azurepowershell
az login
```

---

Ensure you're running the latest version of the CLI via the upgrade command.

# [Bash](#tab/bash)

```azurecli
az upgrade
```

# [Azure PowerShell](#tab/azure-powershell)

```azurepowershell
az upgrade
```

---

Next, install or update the Azure Container Apps extension for the CLI.

# [Bash](#tab/bash)

```azurecli
az extension add --name containerapp --upgrade
```

# [Azure PowerShell](#tab/azure-powershell)


```azurepowershell
az extension add --name containerapp --upgrade
```

---

Register the `Microsoft.App` and `Microsoft.OperationalInsights` namespaces if you haven't already registered them in your Azure subscription.

# [Bash](#tab/bash)

```azurecli
az provider register --namespace Microsoft.App
```

```azurecli
az provider register --namespace Microsoft.OperationalInsights
```

# [Azure PowerShell](#tab/azure-powershell)

```azurepowershell
az provider register --namespace Microsoft.App
```

```azurepowershell
az provider register --namespace Microsoft.OperationalInsights
```

---

Now that your Azure CLI setup is complete, you can define the environment variables that are used throughout this article.

# [Bash](#tab/bash)

Define the following variables in your bash shell.

```azurecli
export RESOURCE_GROUP="album-containerapps"
export LOCATION="canadacentral"
export ENVIRONMENT="env-album-containerapps"
export API_NAME="album-api"
```

# [Azure PowerShell](#tab/azure-powershell)

Define the following variables in your PowerShell console.

```powershell
$RESOURCE_GROUP="album-containerapps"
$LOCATION="canadacentral"
$ENVIRONMENT="env-album-containerapps"
$API_NAME="album-api"
```

---

## Get the sample code

Download and extract the API sample application in the language of your choice.

::: zone pivot="with-dockerfile"

# [C#](#tab/csharp)

[Download the source code](https://codeload.github.com/azure-samples/containerapps-albumapi-csharp/zip/refs/heads/main) to your machine.

Extract the download and change into the *containerapps-albumapi-csharp-main/src* folder.


# [Java](#tab/java)

[Download the source code](https://codeload.github.com/azure-samples/containerapps-albumapi-java/zip/refs/heads/main) to your machine.

Extract the download and change into the *containerapps-albumapi-java-main/src* folder.


# [JavaScript](#tab/javascript)

[Download the source code](https://codeload.github.com/azure-samples/containerapps-albumapi-javascript/zip/refs/heads/main) to your machine.

Extract the download and change into the *containerapps-albumapi-javascript-main/src* folder.


# [Python](#tab/python)

[Download the source code](https://codeload.github.com/azure-samples/containerapps-albumapi-python/zip/refs/heads/main) to your machine.

Extract the download and change into the *containerapps-albumapi-python-main/src* folder.


# [Go](#tab/go)

[Download the source code](https://codeload.github.com/azure-samples/containerapps-albumapi-go/zip/refs/heads/main) to your machine.

Extract the download and navigate into the *containerapps-albumapi-go-main/src* folder.


::: zone-end
::: zone pivot="without-dockerfile"


# [C#](#tab/csharp)

[Download the source code](https://codeload.github.com/azure-samples/containerapps-albumapi-csharp/zip/refs/heads/buildpack) to your machine.

Extract the download and change into the *containerapps-albumapi-csharp-buildpack/src* folder.


# [Java](#tab/java)

[Download the source code](https://codeload.github.com/azure-samples/containerapps-albumapi-java/zip/refs/heads/buildpack) to your machine.

Extract the download and change into the *containerapps-albumapi-java-buildpack/src* folder.

> [!NOTE] 
> The Java Builpack currently supports the [Maven tool](https://maven.apache.org/what-is-maven.html) to build your application.


# [JavaScript](#tab/javascript)

[Download the source code](https://codeload.github.com/azure-samples/containerapps-albumapi-javascript/zip/refs/heads/buildpack) to your machine.

Extract the download and change into the *containerapps-albumapi-javascript-buildpack/src* folder.


# [Python](#tab/python)

[Download the source code](https://codeload.github.com/azure-samples/containerapps-albumapi-python/zip/refs/heads/buildpack) to your machine.

Extract the download and change into the *containerapps-albumapi-python-buildpack/src* folder.


# [Go](#tab/go)

Azure Container Apps cloud build doesn't currently support Buildpacks for Go.

::: zone-end

---

## Build and deploy the container app

Build and deploy your first container app with the `containerapp up` command. This command will:

::: zone pivot="with-dockerfile"
- Create the resource group
- Create an Azure Container Registry
- Build the container image and push it to the registry
- Create the Container Apps environment with a Log Analytics workspace
- Create and deploy the container app using the built container image
::: zone-end

::: zone pivot="without-dockerfile"
- Create the resource group
- Create a default registry as part of your environment
- Detect the language and runtime of your application and build the image using the appropriate Buildpack
- Push the image into the Azure Container Apps default registry
- Create the Container Apps environment with a Log Analytics workspace
- Create and deploy the container app using the built container image
::: zone-end

::: zone pivot="with-dockerfile"

The `up` command uses the Dockerfile in the root of the repository to build the container image. The `EXPOSE` instruction in the Dockerfile defined the target port, which is the port used to send ingress traffic to the container.

::: zone-end
::: zone pivot="without-dockerfile"

If the `up` command doesn't find a Dockerfile, it automatically uses Buildpacks to turn your application source into a runnable container. Since the Buildpack is trying to run the build on your behalf, you need to tell the `up` command which port to send ingress traffic to.

::: zone-end


In the following code example, the `.` (dot) tells `containerapp up` to run in the `src` directory of the extracted sample API application.

# [Bash](#tab/bash)

::: zone pivot="with-dockerfile"

```azurecli
az containerapp up \
  --name $API_NAME \
  --resource-group $RESOURCE_GROUP \
  --location $LOCATION \
  --environment $ENVIRONMENT \
  --source .
```

::: zone-end
::: zone pivot="without-dockerfile"

```azurecli
az containerapp up \
  --name $API_NAME \
  --resource-group $RESOURCE_GROUP \
  --location $LOCATION \
  --environment $ENVIRONMENT \
  --ingress external \
  --target-port 8080 \
  --source .
```

::: zone-end


# [Azure PowerShell](#tab/azure-powershell)

::: zone pivot="with-dockerfile"

```powershell
az containerapp up `
    --name $API_NAME `
    --resource-group $RESOURCE_GROUP `
    --location $LOCATION `
    --environment $ENVIRONMENT `
    --source .
```

::: zone-end
::: zone pivot="without-dockerfile"

```powershell
az containerapp up `
    --name $API_NAME `
    --resource-group $RESOURCE_GROUP `
    --location $LOCATION `
    --environment $ENVIRONMENT `
    --ingress external `
    --target-port 8080 `
    --source .
```

::: zone-end


---


## Verify deployment

Copy the FQDN to a web browser.  From your web browser, go to the `/albums` endpoint of the FQDN.

:::image type="content" source="media/quickstart-code-to-cloud/azure-container-apps-album-api.png" alt-text="Screenshot of response from albums API endpoint.":::

[!INCLUDE [.NET data protection](../../includes/container-apps-net-data-protection-scaling.md)]

## Clean up resources

If you're not going to continue on to the [Deploy a frontend](communicate-between-microservices.md) tutorial, you can remove the Azure resources created during this quickstart with the following command.

>[!CAUTION]
> The following command deletes the specified resource group and all resources contained within it. If the group contains resources outside the scope of this quickstart, they are also deleted.

# [Bash](#tab/bash)

```azurecli
az group delete --name $RESOURCE_GROUP
```

# [Azure PowerShell](#tab/azure-powershell)

```powershell
az group delete --name $RESOURCE_GROUP
```

---

> [!TIP]
> Having issues? Let us know on GitHub by opening an issue in the [Azure Container Apps repo](https://github.com/microsoft/azure-container-apps).

## Next steps

After completing this quickstart, you can continue to [Tutorial: Communication between microservices in Azure Container Apps](communicate-between-microservices.md) to learn how to deploy a front end application that calls the API.

> [!div class="nextstepaction"]
> [Tutorial: Communication between microservices](communicate-between-microservices.md)
