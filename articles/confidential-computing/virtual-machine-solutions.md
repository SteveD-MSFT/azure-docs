---
title: Confidential VM solutions
description: Azure Confidential Computing offers multiple options for confidential virtual machines on AMD and Intel processors.
author: ju-shim
ms.author: jushiman
ms.reviewer: mattmcinnes
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.workload: infrastructure
ms.custom: devx-track-azurecli
ms.topic: conceptual
ms.date: 11/15/2023
---

# Azure Confidential VM options

Azure offers multiple confidential VMs options leveraging Trusted Execution Environments (TEE) technologies from both AMD and Intel to harden the virtualization environment. These technologies enable you to provision confidential computing environments with excellent price-to-performance without code changes. 

AMD confidential VMs leverage [Secure Encrypted Virtualization-Secure Nested Paging (SEV-SNP)](https://www.amd.com/system/files/TechDocs/SEV-SNP-strengthening-vm-isolation-with-integrity-protection-and-more.pdf) which was introduced with 3rd Gen AMD EPYC™ processors. Intel confidential VMs use [Trust Domain Extensions (TDX)](https://www.intel.com/content/dam/develop/external/us/en/documents/tdx-whitepaper-v4.pdf) which was introduced with 4th Gen Intel® Xeon® processors.

## Sizes

You can create confidential VMs in the following size families:

| Size Family          | TEE | Description                                                                         |
| ------------------ | ------------ | ----------------------------------------------------------------------------------- |
| **DCasv5-series** | AMD SEV-SNP | General purpose CVM with remote storage. No local temporary disk.                  |
| **DCesv5-series** | Intel TDX | General purpose CVM with remote storage. No local temporary disk.                  |
| **DCadsv5-series** | AMD SEV-SNP | General purpose CVM with local temporary disk.                                        |
| **DCedsv5-series** | Intel TDX | General purpose CVM with local temporary disk.                                        |
| **ECasv5-series** | AMD SEV-SNP | Memory-optimized CVM with remote storage. No local temporary disk. |
| **ECesv5-series** | Intel TDX | Memory-optimized CVM with remote storage. No local temporary disk. |
| **ECadsv5-series** | AMD SEV-SNP | Memory-optimized CVM with local temporary disk.                      |
| **ECedsv5-series** | Intel TDX | Memory-optimized CVM with local temporary disk. |
| **ECiesv5-series** | Intel TDX | Isolated memory-optimized CVM with local temporary disk.                      |
| **ECiedsv5-series** | Intel TDX | Isolated memory-optimized CVM with local temporary disk. |

> [!NOTE]
> Memory-optimized confidential VMs offer double the ratio of memory per vCPU count.

## Azure CLI commands

You can use the [Azure CLI](/cli/azure/install-azure-cli) with your confidential VMs.

To see a list of confidential VM sizes, run the following command. Replace `<vm-series>` with the series you want to use. The output shows information about available regions and availability zones.

```azurecli-interactive
vm_series='DCASv5'
az vm list-skus \
    --size dc \
    --query "[?family=='standard${vm_series}Family'].{name:name,locations:locationInfo[0].location,AZ_a:locationInfo[0].zones[0],AZ_b:locationInfo[0].zones[1],AZ_c:locationInfo[0].zones[2]}" \
    --all \
    --output table
```

For a more detailed list, run the following command instead:

```azurecli-interactive
vm_series='DCASv5'
az vm list-skus \
    --size dc \
    --query "[?family=='standard${vm_series}Family']" 
```

## Deployment considerations

Consider the following settings and choices before deploying confidential VMs.

### Azure subscription

To deploy a confidential VM instance, consider a [pay-as-you-go subscription](/azure/virtual-machines/linux/azure-hybrid-benefit-linux) or other purchase option. If you're using an [Azure free account](https://azure.microsoft.com/free/), the quota doesn't allow the appropriate number of Azure compute cores.

You might need to increase the cores quota in your Azure subscription from the default value. Default limits vary depending on your subscription category. Your subscription might also limit the number of cores you can deploy in certain VM size families, including the confidential VM sizes. 

To request a quota increase, [open an online customer support request](../azure-portal/supportability/per-vm-quota-requests.md). 

If you have large-scale capacity needs, contact Azure Support. Azure quotas are credit limits, not capacity guarantees. You only incur charges for cores that you use.

### Pricing

For pricing options, see the [Linux Virtual Machines Pricing](https://azure.microsoft.com/pricing/details/virtual-machines/linux/). 

### Regional availability

For availability information, see which [VM products are available by Azure region](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines).

### Resizing

Confidential VMs run on specialized hardware, so you can only [resize confidential VM instances](confidential-vm-faq.yml#can-i-convert-a-dcasv5-ecasv5-cvm-into-a-dcesv5-ecesv5-cvm-or-a-dcesv5-ecesv5-cvm-into-a-dcasv5-ecasv5-cvm-) to other confidential sizes in the same region. For example, if you have a DCasv5-series VM, you can resize to another DCasv5-series instance or a DCesv5-series instance. 

It's not possible to resize a non-confidential VM to a confidential VM.

### Disk encryption

OS images for confidential VMs have to meet certain security and compatibility requirements. Qualified images support the secure mounting, attestation, optional [confidential OS disk encryption](confidential-vm-overview.md#confidential-os-disk-encryption), and isolation from underlying cloud infrastructure. These images include:

- Ubuntu 20.04 LTS (AMD SEV-SNP supported only)
- Ubuntu 22.04 LTS
- Windows Server 2019 Datacenter - x64 Gen 2 (AMD SEV-SNP supported only)
- Windows Server 2019 Datacenter Server Core - x64 Gen 2 (AMD SEV-SNP supported only)
- Windows Server 2022 Datacenter - x64 Gen 2
- Windows Server 2022 Datacenter: Azure Edition Core - x64 Gen 2
- Windows Server 2022 Datacenter: Azure Edition - x64 Gen 2
- Windows Server 2022 Datacenter Server Core - x64 Gen 2
- Windows 11 Enterprise N, version 22H2 -x64 Gen 2
- Windows 11 Pro, version 22H2 ZH-CN -x64 Gen 2
- Windows 11 Pro, version 22H2 -x64 Gen 2
- Windows 11 Pro N, version 22H2 -x64 Gen 2
- Windows 11 Enterprise, version 22H2 -x64 Gen 2
- Windows 11 Enterprise multi-session, version 22H2 -x64 Gen 2

For more information about supported and unsupported VM scenarios, see [support for generation 2 VMs on Azure](../virtual-machines/generation-2.md). 

### High availability and disaster recovery

You're responsible for creating high availability and disaster recovery solutions for your confidential VMs. Planning for these scenarios helps minimize and avoid prolonged downtime.

### Deployment with ARM templates

Azure Resource Manager is the deployment and management service for Azure. You can:

- Secure and organize your resources after deployment with the management features, like access control, locks, and tags. 
- Create, update, and delete resources in your Azure subscription using the management layer.
- Use [Azure Resource Manager templates (ARM templates)](../azure-resource-manager/templates/overview.md) to deploy confidential VMs on AMD processors. There is an available [ARM template for confidential VMs](https://aka.ms/CVMTemplate). 

Make sure to specify the following properties for your VM in the parameters section (`parameters`): 

- VM size (`vmSize`). Choose from the different [confidential VM families and sizes](#sizes).
- OS image name (`osImageName`). Choose from the [qualified OS images](#disk-encryption).
- Disk encryption type (`securityType`). Choose from VMGS-only encryption (`VMGuestStateOnly`) or full OS disk pre-encryption (`DiskWithVMGuestState`), which might result in longer provisioning times. For Intel TDX instances only we also support another security type (`NonPersistedTPM`) which has no VMGS or OS disk encryption.

## Next steps 

> [!div class="nextstepaction"]
> [Deploy a confidential VM from the Azure portal](quick-create-confidential-vm-portal.md)

For more information see our [Confidential VM FAQ](confidential-vm-faq.yml).
