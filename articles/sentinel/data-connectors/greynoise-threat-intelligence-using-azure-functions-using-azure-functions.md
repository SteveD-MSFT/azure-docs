---
title: "GreyNoise Threat Intelligence (using Azure Functions)  connector for Microsoft Sentinel"
description: "Learn how to install the connector GreyNoise Threat Intelligence (using Azure Functions) to connect your data source to Microsoft Sentinel."
author: cwatson-cat
ms.topic: how-to
ms.date: 11/29/2023
ms.service: microsoft-sentinel
ms.author: cwatson
---

# GreyNoise Threat Intelligence (using Azure Functions) connector for Microsoft Sentinel

This Data Connector installs an Azure Function app to download GreyNoise indicators once per day and inserts them into the ThreatIntelligenceIndicator table in Microsoft Sentinel.

## Connector attributes

| Connector attribute | Description |
| --- | --- |
| **Log Analytics table(s)** | ThreatIntelligenceIndicator<br/> |
| **Data collection rules support** | Not currently supported |
| **Supported by** | [GreyNoise](https://www.greynoise.io/contact/general) |

## Query samples

**All Threat Intelligence APIs Indicators**
   ```kusto
ThreatIntelligenceIndicator 
   | where SourceSystem == 'GreyNoise'
   | sort by TimeGenerated desc
   ```



## Prerequisites

To integrate with GreyNoise Threat Intelligence (using Azure Functions) make sure you have: 

- **Microsoft.Web/sites permissions**: Read and write permissions to Azure Functions to create a Function App is required. [See the documentation to learn more about Azure Functions](/azure/azure-functions/).
- **GreyNoise API Key**: Retreive your GreyNoise API Key [here](https://viz.greynoise.io/account/api-key).


## Vendor installation instructions

You can connect GreyNoise Threat Intelligence to Microsoft Sentinel by following the below steps: 

The following steps create an Azure AAD application, retrieves a GreyNoise API key, and saves the values in an Azure Function App Configuration.

1. Retrieve API Key from GreyNoise Portal.

   Generate an API key from GreyNoise Portal https://docs.greynoise.io/docs/using-the-greynoise-api

2. In your Azure AD tenant, create an Azure Active Directory (AAD) application and acquire Tenant ID, Client ID and (note: hold off generating a Client Secret until Step 5).Also get the Log Analytics Workspace ID associated with your Microsoft Sentinel instance should be below.

   Follow the instructions here to create your Azure AAD app and save your Client ID and Tenant ID: [Connect your threat intelligence platform to Microsoft Sentinel with the upload indicators API](/azure/sentinel/connect-threat-intelligence-upload-api#instructions)
   NOTE: Wait until step 5 to generate your client secret.


3. Assign the AAD application the Microsoft Sentinel Contributor Role.

   Follow the instructions here to add the Microsoft Sentinel Contributor Role: [Connect your threat intelligence platform to Microsoft Sentinel with the upload indicators API](/azure/sentinel/connect-threat-intelligence-upload-api#assign-a-role-to-the-application)

4. Specify the AAD permissions to enable MS Graph API access to the upload-indicators API.

   Follow this section here to add **'ThreatIndicators.ReadWrite.OwnedBy'** permission to the AAD App: [Connect your threat intelligence platform to Microsoft Sentinel](/azure/sentinel/connect-threat-intelligence-tip#specify-the-permissions-required-by-the-application). 
   Back in your AAD App, ensure you grant admin consent for the permissions you just added. 
   Finally, in the 'Tokens and APIs' section, generate a client secret and save it. You will need it in Step 6. 

5. Deploy the Threat Intellegence (Preview) Solution which includes the Threat Intelligence Upload Indicators API (Preview)

   See Microsoft Sentinel Content Hub for this Solution, and install it this Microsoft Sentinel instance.

6. Deploy the Azure Function

   Click the Deploy to Azure button.

   [![Deploy To Azure](https://aka.ms/deploytoazurebutton)](https://aka.ms/sentinel-GreyNoise-azuredeploy)

   Fill in the appropriate values for each parameter. **Be aware** that the only valid values for the **GREYNOISE_CLASSIFICATIONS** parameter are **malicious** and/or **unknown**, which must be comma separated. Do not bring in **<i>benign</i>**, as this will bring in millions of IPs which are known good and will likely cause many unwanted alerts.

7. Send indicators to Sentinel

   The function app installed in Step 6 queries the GreyNoise GNQL API once per day, and submits each indicator found in STIX 2.1 format to the [Microsoft Upload Threat Intelligence Indicators API](/azure/sentinel/upload-indicators-api).

   Each indicator expires in ~24 hours from creation unless it's found on the next day's query, in which case the TI Indicator's **Valid Until** time is extended for another 24 hours, which keeps it active in Microsoft Sentinel.  

 For more information on the GreyNoise API and the GreyNoise Query Language (GNQL) [click here](https://developer.greynoise.io/docs/using-the-greynoise-api).



## Next steps

For more information, go to the [related solution](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/greynoiseintelligenceinc1681236078693.microsoft-sentinel-byol-greynoise?tab=Overview) in the Azure Marketplace.
