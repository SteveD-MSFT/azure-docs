---
title: Create and manage prompt flow runtimes
titleSuffix: Azure AI Studio
description: Learn how to create and manage prompt flow runtimes in Azure AI Studio.
manager: nitinme
ms.service: azure-ai-studio
ms.custom:
  - ignite-2023
ms.topic: how-to
ms.date: 11/15/2023
ms.reviewer: eur
ms.author: eur
author: eric-urban
---

# Create and manage prompt flow runtimes in Azure AI Studio

[!INCLUDE [Azure AI Studio preview](../includes/preview-ai-studio.md)]

In Azure AI Studio, you can create and manage prompt flow runtimes. You need a runtime to use prompt flow.

A prompt flow runtime has computing resources that are required for the application to run, including a Docker image that contains all necessary dependency packages. In addition to flow execution, Azure AI Studio uses the runtime to ensure the accuracy and functionality of the tools incorporated within the flow when you make updates to the prompt or code content.

Azure AI Studio supports the following types of runtimes:

|Runtime type|Underlying compute type|Life cycle management| Customize packages              |
|------------|----------------------|---------------------|---------------------|
|Automatic runtime        |Serverless compute| Automatic | Easily customize Python packages|
|Compute instance runtime | Compute instance | Manual | |

If you're a new user, we recommend that you use an automatic runtime. You can easily customize the environment for this runtime.  

If you have a compute instance, you can use it to build your compute instance runtime.

## Create a runtime

### Create an automatic runtime on a flow page

Automatic is the default option for a runtime. You can start an automatic runtime by selecting an option from the runtime dropdown list on a flow page:

- Select **Start**. Start creating an automatic runtime by using the environment defined in `flow.dag.yaml` in the flow folder on the virtual machine (VM) size where you have a quota in the project.

  :::image type="content" source="../media/prompt-flow/how-to-create-manage-runtime/runtime-create-automatic-init.png" alt-text="Screenshot of prompt flow with default settings for starting an automatic runtime on a flow page." lightbox = "../media/prompt-flow/how-to-create-manage-runtime/runtime-create-automatic-init.png":::

- Select **Start with advanced settings**. In the advanced settings, you can:

  - Customize the VM size that the runtime uses.
  - Customize the idle time, which saves code by deleting the runtime automatically if it isn't in use.
  - Set the user-assigned managed identity. The automatic runtime uses this identity to pull a base image and install packages. Make sure that the user-assigned managed identity has Azure Container Registry pull permission.

    If you don't set this identity, you use the user identity by default. [Learn more about how to create and update user-assigned identities for a project](../../machine-learning/how-to-identity-based-service-authentication.md#to-create-a-workspace-with-multiple-user-assigned-identities-use-one-of-the-following-methods).

    :::image type="content" source="../media/prompt-flow/how-to-create-manage-runtime/runtime-creation-automatic-settings.png" alt-text="Screenshot of prompt flow with advanced settings for starting an automatic runtime on a flow page." lightbox = "../media/prompt-flow/how-to-create-manage-runtime/runtime-creation-automatic-settings.png":::

### Create a compute instance runtime on a runtime page

1. Sign in to [Azure AI Studio](https://ai.azure.com) and select your project from the **Build** page. If you don't have a project, create one.

1. On the collapsible left menu, select **Settings**.

1. In the **Compute instances** section, select **View all**.

    :::image type="content" source="../media/compute/compute-view-settings.png" alt-text="Screenshot of project settings with the option to view all compute instances." lightbox="../media/compute/compute-view-settings.png":::

1. Make sure that a compute instance is available and running. If you don't have a compute instance, you can [create one in Azure AI Studio](./create-manage-compute.md).

1. Select the **Prompt flow runtimes** tab.

    :::image type="content" source="../media/compute/compute-runtime.png" alt-text="Screenshot of where to select prompt flow runtimes from the compute instances page." lightbox="../media/compute/compute-runtime.png":::

1. Select **Create**.

    :::image type="content" source="../media/compute/runtime-create.png" alt-text="Screenshot of the button for creating a runtime." lightbox="../media/compute/runtime-create.png":::

1. Select the compute instance for the runtime, and then select **Create**.

    :::image type="content" source="../media/compute/runtime-select-compute.png" alt-text="Screenshot of the option to select a compute instance during runtime creation." lightbox="../media/compute/runtime-select-compute.png":::

1. Acknowledge the warning that the compute instance will be restarted by selecting **Confirm**.

    :::image type="content" source="../media/compute/runtime-create-confirm.png" alt-text="Screenshot of the option to confirm automatic restart via the runtime creation." lightbox="../media/compute/runtime-create-confirm.png":::

1. On the page for runtime details, monitor the status of the runtime. The runtime has a status of **Not available** until it's ready. This process can take a few minutes.

    :::image type="content" source="../media/compute/runtime-creation-in-progress.png" alt-text="Screenshot of a runtime with a status that shows it's not yet available." lightbox="../media/compute/runtime-creation-in-progress.png":::

1. When the runtime is ready, the status changes to **Running**. You might need to select **Refresh** to see the updated status.

    :::image type="content" source="../media/compute/runtime-running.png" alt-text="Screenshot of a runtime with a running status." lightbox="../media/compute/runtime-running.png":::

1. Select the runtime on the **Prompt flow runtimes** tab to see its details.

    :::image type="content" source="../media/compute/runtime-details.png" alt-text="Screenshot of runtime details, including environment." lightbox="../media/compute/runtime-details.png":::

## Update a runtime on the UI

### Update an automatic runtime on a flow page

On a flow page, you can use the following options to manage an automatic runtime:

- **Install packages** Open `requirements.txt` in prompt flow UI, you can add packages in it.
- **View installed packages** shows the packages that are installed in the runtime. It includes the packages baked to base image and packages specify in the `requirements.txt` file in the flow folder.
- **Reset** deletes the current runtime and creates a new one with the same environment. If you encounter a package conflict, you can try this option.
- **Edit** opens the runtime configuration page, where you can define the VM side and the idle time for the runtime.
- **Stop** deletes the current runtime. If there's no active runtime on the underlying compute, the compute resource is also deleted.

:::image type="content" source="../media/prompt-flow/how-to-create-manage-runtime/runtime-create-automatic-actions.png" alt-text="Screenshot of actions for an automatic runtime on a flow page." lightbox = "../media/prompt-flow/how-to-create-manage-runtime/runtime-create-automatic-actions.png":::

You can also customize the environment that you use to run this flow by adding packages in the `requirements.txt` file in the flow folder. After you add more packages in this file, you can choose either of these options:

- **Save and install** triggers `pip install -r requirements.txt` in the flow folder. The process can take a few minutes, depending on the packages that you install.
- **Save only** just saves the `requirements.txt` file. You can install the packages later yourself.

:::image type="content" source="../media/prompt-flow/how-to-create-manage-runtime/runtime-create-automatic-save-install.png" alt-text="Screenshot of the option to save and install packages for an automatic runtime on a flow page." lightbox = "../media/prompt-flow/how-to-create-manage-runtime/runtime-create-automatic-save-install.png":::

> [!NOTE]
> You can change the location and even the file name of `requirements.txt`, but be sure to also change it in the `flow.dag.yaml` file in the flow folder.
>
> Don't pin the version of `promptflow` and `promptflow-tools` in `requirements.txt`, because we already include them in the runtime base image.

#### Add packages in a private feed in Azure DevOps

If you want to use a private feed in Azure DevOps, follow these steps:

1. Create a user-assigned managed identity and add this identity in the Azure DevOps organization. To learn more, see [Use service principals and managed identities](/azure/devops/integrate/get-started/authentication/service-principal-managed-identity).

   > [!NOTE]
   > If the **Add Users** button isn't visible, you probably don't have the necessary permissions to perform this action.

1. [Add or update user-assigned identities to your project](../../machine-learning/how-to-identity-based-service-authentication.md#to-create-a-workspace-with-multiple-user-assigned-identities-use-one-of-the-following-methods).

1. Add `{private}` to your private feed URL. For example, if you want to install `test_package` from `test_feed` in Azure devops, add `-i https://{private}@{test_feed_url_in_azure_devops}` in `requirements.txt`:

    ```txt
    -i https://{private}@{test_feed_url_in_azure_devops}
    test_package
    ```

1. Specify the user-assigned managed identity in **Start with advanced settings** if automatic runtime isn't running, or use the **Edit** button if automatic runtime is running.

    :::image type="content" source="../media/prompt-flow/how-to-create-manage-runtime/runtime-advanced-setting-msi.png" alt-text="Screenshot that shows the toggle for using a workspace user-assigned managed identity." lightbox = "../media/prompt-flow/how-to-create-manage-runtime/runtime-advanced-setting-msi.png":::

#### Change the base image for automatic runtime (preview)

By default, we use the latest prompt flow image as the base image. If you want to use a different base image, you need build your own base image, this docker image should be built from prompt flow base image that is `mcr.microsoft.com/azureml/promptflow/promptflow-runtime-stable:<newest_version>`. If possible use the [latest version of the base image](https://mcr.microsoft.com/v2/azureml/promptflow/promptflow-runtime-stable/tags/list). To use the new base image, you need to reset the runtime via the `reset` command. This process takes several minutes as it pulls the new base image and reinstalls packages.

:::image type="content" source="../media/prompt-flow/how-to-create-manage-runtime/runtime-creation-automatic-image-flow-dag.png" alt-text="Screenshot of actions for customizing a base image for an automatic runtime on a flow page." lightbox = "../media/prompt-flow/how-to-create-manage-runtime/runtime-creation-automatic-image-flow-dag.png":::

```yaml
environment:
    image: <your-custom-image>
    python_requirements_txt: requirements.txt
```

### Update a compute instance runtime on a runtime page

Azure AI Studio gets regular updates to the base image (`mcr.microsoft.com/azureml/promptflow/promptflow-runtime-stable`) to include the latest features and bug fixes. To get the best experience and performance, periodically update your runtime to the [latest version](https://mcr.microsoft.com/v2/azureml/promptflow/promptflow-runtime-stable/tags/list).

Go to the page for runtime details and select **Update**. On the **Edit compute instance runtime** pane, you can update the runtime environment. If you select **Use default environment**, the system tries to update your runtime to the latest version.

Every time you open the page for runtime details, AI Studio checks whether there are new versions of the runtime. If new versions are available, a notification appears at the top of the page. You can also manually check the latest version by selecting the **Check version** button.

## Switch compute instance runtime to automatic runtime

Automatic runtime has following advantages over compute instance runtime:
- Automatic manage lifecycle of runtime and underlying compute. You don't need to manually create and managed them anymore.
- Easily customize packages by adding packages in the `requirements.txt` file in the flow folder, instead of creating a custom environment.

We would recommend you to switch to automatic runtime if you're using compute instance runtime. If you have a compute instance runtime, you can switch it to an automatic runtime (preview) by using the following steps:
- Prepare your `requirements.txt` file in the flow folder. Make sure that you don't pin the version of `promptflow` and `promptflow-tools` in `requirements.txt`, because we already include them in the runtime base image. Packages specified in `requirements.txt` will be installed when the runtime is started. 
- If you want to keep the automatic runtime (preview) as long running compute like compute instance, you can disable the idle shutdown toggle under automatic runtime (preview) `edit` option.


## Next steps

- [Learn more about prompt flow](./prompt-flow.md)
- [Develop a flow](./flow-develop.md)
- [Develop an evaluation flow](./flow-develop-evaluation.md)
