---
title: Deploy Defender for Servers
description: Learn how to enable the Defender for Servers on your Azure subscription for Microsoft Defender for Cloud.
ms.topic: install-set-up-deploy
ms.date: 01/23/2024
---

# Deploy Defender for Servers

Defender for Servers in Microsoft Defender for Cloud brings threat detection and advanced defenses to your Windows and Linux machines that run in Azure, Amazon Web Services (AWS), Google Cloud Platform (GCP), and on-premises environments. This plan includes the integrated license for Microsoft Defender for Endpoint, security baselines and OS level assessments, vulnerability assessment scanning, adaptive application controls (AAC), file integrity monitoring (FIM), and more.

Microsoft Defender for Servers includes an automatic, native integration with Microsoft Defender for Endpoint. Learn more, [Protect your endpoints with Defender for Cloud's integrated EDR solution: Microsoft Defender for Endpoint](integration-defender-for-endpoint.md). With this integration enabled, you have access to the vulnerability findings from **Microsoft threat and vulnerability management**.

Defender for Servers offers two plan options with different levels of protection and their own cost. You can learn more about Defender for Cloud's pricing on [the pricing page](https://azure.microsoft.com/pricing/details/defender-for-cloud/).

## Prerequisites

- You need a Microsoft Azure subscription. If you don't have an Azure subscription, you can [sign up for a free subscription](https://azure.microsoft.com/pricing/free-trial/).

- You must [enable Microsoft Defender for Cloud](get-started.md#enable-defender-for-cloud-on-your-azure-subscription) on your Azure subscription.

- Review the [Defender for Servers deployment guide](plan-defender-for-servers.md).

## Enable the Defender for Servers plan

You can [enable the Defender for Servers plan on an Azure subscription, AWS account, or GCP project](#enable-on-an-azure-subscription-aws-account-or-gcp-project), or [enable the plan at the resource level](#enable-the-plan-at-the-resource-level).

## Enable on an Azure subscription, AWS account, or GCP project

You can enable the Defender for Servers plan from the Environment settings page to protect all the machines in an Azure subscription, AWS account, or GCP project.

**To enable the Defender for Servers plan**:

1. Sign in to the [Azure portal](https://portal.azure.com).

1. Search for and select **Microsoft Defender for Cloud**.

1. In the Defender for Cloud menu, select **Environment settings**.

1. Select the relevant Azure subscription, AWS account, or GCP project.

1. On the Defender plans page, toggle the Servers switch to **On**.

    :::image type="content" source="media/tutorial-enable-servers-plan/enable-servers-plan.png" alt-text="Screenshot that shows you how to toggle the Defender for Servers plan to on." lightbox="media/tutorial-enable-servers-plan/enable-servers-plan.png":::

Once the plan has been enabled, you have the ability to [configure the monitoring settings](configure-servers-coverage.md) to suit your needs.

### Select a Defender for Servers plan

When you enable the Defender for Servers plan, you're then given the option to select which plan - Plan 1 or Plan 2 - to enable. There are two plans you can choose from that offer different levels of protections for your resources.

[Review what's included each plan](plan-defender-for-servers-select-plan.md#plan-features).

**To select a Defender for Servers plan**:

1. Sign in to the [Azure portal](https://portal.azure.com).

1. Search for and select **Microsoft Defender for Cloud**.

1. In the Defender for Cloud menu, select **Environment settings**.

1. Select the relevant Azure subscription, AWS account, or GCP project.

1. Select **Change plans**.

    :::image type="content" source="media/tutorial-enable-servers-plan/servers-change-plan.png" alt-text="Screenshot that shows you where on the environment settings page to select change plans." lightbox="media/tutorial-enable-servers-plan/servers-change-plan.png":::

1. In the popup window, select **Plan 2** or **Plan 1**.

    :::image type="content" source="media/tutorial-enable-servers-plan/servers-plan-selection.png" alt-text="Screenshot of the popup where you can select plan 1 or plan 2." lightbox="media/tutorial-enable-servers-plan/servers-plan-selection.png":::

1. Select **Confirm**.

1. Select **Save**.

Once the plan has been enabled, you have the ability to [configure the monitoring settings](configure-servers-coverage.md) to suit your needs.

## Enable the plan at the resource level

While our recommendation is to enable Defender for Servers on the entire Azure subscription, to protect all existing and future resources in it, there are some cases where more flexibility is required to exclude specific resources or to manage security configurations at a lower hierarchy level than subscription. Resource level enablement is available for **Azure machines** and on-premises with **Azure Arc** as part of Defender for Servers plans:

- **Defender for Servers Plan 1**: you can enable / disable the plan at the resource level.
- **Defender for Servers Plan 2**: you can only disable the plan at the resource level. For example, it’s possible to enable the plan at the subscription level and disable specific resources, however it’s not possible to enable the plan only for specific resources.

### Supported resource types

Supported resource types include:

- Azure VMs
- On-premises with Azure Arc
- Azure Virtual Machine Scale Sets Flex

Once the plan has been enabled, you have the ability to [configure the monitoring settings](configure-servers-coverage.md) to suit your needs.

### Enablement via REST API

The ability to enable or disable Defender for Servers at the resource level is available exclusively via REST API. Learn how to [interact with the API](/rest/api/defenderforcloud/pricings) to manage your Defender for Servers at the resource or subscription level.

### The effect on Microsoft Defender for Endpoint deployment

 Automatic deployment of Microsoft Defender for Endpoint will be supported and aligned with the VM pricing state.

- Including VMs in Servers Plan 1 will trigger automatic deployment of Microsoft Defender for Endpoint (if not already deployed).
- Excluding VMs from Servers Plan 1/Plan 2 will also exclude from deployment of Microsoft Defender for Endpoint (if not already deployed).

> [!NOTE]
> In general, Defender for Servers does not uninstall Microsoft Defender for Endpoint deployments after Servers Plan 1/Plan 2 is deactivated (at the subscription or resource level). To manually remove Microsoft Defender for Endpoint (MDE) on your machine, follow the steps to [offboard devices](/microsoft-365/security/defender-endpoint/offboard-machines).

Since Microsoft Defender for Endpoint deployment for eligible machines is a near real-time service, certain configurations require attention to avoid unintentional agent deployments. For example:

- If you plan to roll out and enable Servers Plan 1/Plan 2 at the subscription level and exclude individual existing VMs, make sure you exclude the VMs before (or at the same time) you enable the plan at the subscription level.
- When you plan to exclude new VMs created under a subscription already enabled for P1/P2, make sure you exclude them during or shortly after creation time, to avoid unintentional deployment of Microsoft Defender for Endpoint.

Once the plan has been enabled, you have the ability to [configure the monitoring settings](configure-servers-coverage.md) to suit your needs.

### Enablement at scale

Use the following base script file to customize it for your specific needs.

1. [Download this file](https://github.com/Azure/Microsoft-Defender-for-Cloud/tree/main/Powershell%20scripts/Defender%20for%20Servers%20on%20resource%20level), save it as a PowerShell file, and run it.
1. Select whether to set pricing by **tag** or by **resource group**.
1. Follow the onscreen instructions.

Once the plan has been enabled, you have the ability to [configure the monitoring settings](configure-servers-coverage.md) to suit your needs.

### Monitoring coverage status

To monitor your coverage status, you can use the inventory. In the main menu, select **Inventory** and then check the **plan status** in the “Defender for cloud” column:

:::image type="content" source="media/tutorial-enable-servers-plan/select-inventory.png" alt-text="Screenshot that shows you where to select Inventory from the main menu." lightbox="media/tutorial-enable-servers-plan/select-inventory.png":::

> [!NOTE]
> Defender for Servers settings on each resource are inherited by the subscription-level settings by default. Once you change settings at the resource level, the resource will no longer inherit settings from its parent subscription unless you delete the settings you configured.

## Next steps

[Configure monitoring coverage](configure-servers-coverage.md)
[Overview of Microsoft Defender for Servers](defender-for-servers-introduction.md)
