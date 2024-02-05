---
title: Overview - Oracle Database@Azure 
description: Learn about Oracle Database@Azure.
author: jjaygbay1
ms.service: virtual-machines
ms.subservice: oracle
ms.collection: linux
ms.topic: article
ms.date: 12/12/2023
ms.custom: engagement-fy23
ms.author: jacobjaygbay
---


# Overview - Oracle Database@Azure 

Oracle Database@Azure is an Oracle database service running on Oracle Cloud Infrastructure (OCI), colocated in Microsoft data centers. This ensures that the Oracle Database@Azure service has the fastest possible access to Azure resources and applications.

Oracle Database@Azure allows you to subscribe to the Oracle Database Service inside your Azure environment. All infrastructure for your Oracle Database Service is located in Azure's physical data centers, giving your critical database workloads the high-performance and low-latency they require. Like other Azure services, Oracle Database@Azure uses an Azure Virtual Network for networking, managed within the Azure environment. The service uses the Azure tenancy's identity management and authorization, which can be either the Azure native identity service or a federated identity provider. The service allows you to monitor database metrics, audit logs, events, logging data, and telemetry natively in Azure.

Oracle Database@Azure runs on infrastructure managed by Oracle's expert Cloud Infrastructure operations team. The operations team performs software patching, infrastructure updates, and other operations through a connection to OCI. While the service requires that customers have an OCI tenancy, most service activities take place in the Azure environment.

## Oracle Database@Azure interfaces

You can provision Oracle Database@Azure using the Azure portal and Azure APIs, SDKs and Terraform. Management of Oracle database system infrastructure and VM cluster resources takes place in the Azure portal as well.

For Oracle Container Databases (CDB) and Oracle Pluggable Databases (PDB), some management tasks are completed using the OCI console.

Database and application developers work in the Azure portal or use Azure tools (Azure API, SDK, Terraform) to interact with Oracle Database@Azure databases.

## Purchase Oracle Database@Azure

To purchase Oracle Database@Azure, contact [Oracle's sales team](https://go.oracle.com/LP=138489) or your Oracle sales representative for a sale offer. Oracle Sales team creates an Azure Private Offer in the Azure Marketplace for your service. After an offer has been created for your organization, you can accept the offer and complete the purchase in the Azure portal's Marketplace service. For more information on Azure private offers, see [Overview of the commercial marketplace and enterprise procurement](/marketplace/procurement-overview).

Billing and payment for the service is done through Azure. Payment for Oracle Database@Azure counts toward your Microsoft Azure Consumption Commitment (MACC). Existing Oracle Database software customers can use the Bring Your Own License (BYOL) option or Unlimited License Agreements (ULAs). On your regular Microsoft Azure invoices, you can see charges for Oracle Database@Azure alongside charges for your other Azure Marketplace services.

## Available regions

Oracle Database@Azure is available in the following locations. Oracle Database@Azure infrastructure resources must be provisioned in the Azure regions listed.

### North America (NA)

|Azure region|
|------------|
|East US (Virginia)|

## Next steps
 
- [Onboard with Oracle Database@Azure](onboard-oracle-database.md)
- [Provision and manage Oracle Database@Azure](provision-oracle-database.md)
- [Oracle Database@Azure support information](oracle-database-support.md)
- [Network planning for Oracle Database@Azure](oracle-database-network-plan.md)
- [Groups and roles for Oracle Database@Azure](oracle-database-groups-roles.md)
