---
title: Ingest CEF logs to Microsoft Sentinel with the Azure Monitor Agent 
description: Ingest and filter CEF-based logs from security devices and appliances to your Microsoft Sentinel workspace using the data connector based on the Azure Monitor Agent (AMA).
author: yelevin
ms.author: yelevin
ms.topic: how-to
ms.date: 12/20/2023
#Customer intent: As a security operator, I want to ingest and filter CEF-based logs from security devices and appliances to my Microsoft Sentinel workspace, so that security analysts can monitor activity on these systems and detect security threats.
---

# Ingest CEF logs with the Azure Monitor Agent

This article describes how to use the **Common Event Format (CEF) via AMA (Preview)** connector to quickly filter and ingest logs in the Common Event Format (CEF) from multiple security devices and appliances over Syslog.

The connector uses the Azure Monitor Agent (AMA), which takes instructions from Data Collection Rules (DCRs). DCRs specify the systems to monitor, and they define filters to apply to the logs before they're ingested, for better performance and more efficient querying and analysis.

You can also collect (non-CEF) Syslog logs with the Azure Monitor Agent. Learn how to [configure Syslog and create a DCR](../azure-monitor/agents/data-collection-syslog.md).

> [!IMPORTANT]
>
> The CEF via AMA connector is currently in PREVIEW. The [Azure Preview Supplemental Terms](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) include additional legal terms that apply to Azure features that are in beta, preview, or otherwise not yet released into general availability.

The AMA is installed on a Linux machine that acts as a log forwarder, and the AMA collects logs sent by your security devices and appliances in the CEF format. 

- [Set up the connector](#set-up-the-common-event-format-cef-via-ama-connector)
- [Learn more about the connector](#how-microsoft-sentinel-collects-cef-logs-with-the-azure-monitor-agent)

> [!IMPORTANT]
>
> On **February 28th 2023**, we introduced changes to the CommonSecurityLog table schema. Following this change, you might need to review and update custom queries. For more details, see the **"Recommended actions"** section in [this blog post](https://techcommunity.microsoft.com/t5/microsoft-sentinel-blog/upcoming-changes-to-the-commonsecuritylog-table/ba-p/3643232). Out-of-the-box content (detections, hunting queries, workbooks, parsers, etc.) has been updated by Microsoft Sentinel.   

## Overview 

### What is Common Event Format (CEF)?

Many network and security devices and appliances send their logs in the Common Event Format (CEF) based on Syslog. This format includes more structured information than Syslog, with information presented in a parsed key-value arrangement. Logs in this format are still transmitted over the syslog port (514) by default, and they are received by the Syslog daemon.

If your appliance or system sends logs over Syslog using CEF, the integration with Microsoft Sentinel allows you to easily run analytics and queries across the data.

CEF normalizes the data, making it more immediately useful for analysis with Microsoft Sentinel. Microsoft Sentinel also allows you to ingest unparsed Syslog events, and to analyze them with query-time parsing. 

### How Microsoft Sentinel collects CEF logs with the Azure Monitor Agent

This diagram illustrates the architecture of CEF log collection in Microsoft Sentinel, using the **Common Event Format (CEF) via AMA (Preview)** connector.

:::image type="content" source="media/connect-cef-ama/cef-forwarder-diagram.png" alt-text="Diagram showing the CEF log forwarding procedure." lightbox="media/connect-cef-ama/cef-forwarder-diagram.png":::

The data ingestion process using the Azure Monitor Agent uses the following components and data flows:

- **CEF log sources:** These are your various security devices and appliances in your environment that produce logs in CEF format. These devices are configured to send their log messages over TCP port 514, not to their local Syslog daemon, but instead to the **Syslog daemon on the Log forwarder**.

- **Log forwarder:** This is a dedicated Linux VM that your organization sets up to collect the log messages from your CEF log sources. The VM can be on-premises, in Azure, or in another cloud. This log forwarder itself has two components:
    - The **Syslog daemon** (either `rsyslog` or `syslog-ng`) collects the log messages on TCP port 514. The daemon then sends these logs\* to the **Azure Monitor Agent**.
    - The **Azure Monitor Agent** that you install on the log forwarder by [setting up the data connector according to the instructions below](#set-up-the-common-event-format-cef-via-ama-connector). The agent parses the logs and then sends them to your **Microsoft Sentinel (Log Analytics) workspace**.

- Your **Microsoft Sentinel (Log Analytics) workspace:** CEF logs sent here end up in the *CommonSecurityLog* table, where you can query the logs and perform analytics on them to detect and respond to security threats.

    > [!NOTE]
    > 
    > \* The Syslog daemon sends logs to the Azure Monitor Agent in two different ways, depending on the AMA version:
    > - AMA versions **1.28.11** and above receive logs on **TCP port 28330**.
    > - Earlier versions of AMA receive logs via Unix domain socket.

## Set up the Common Event Format (CEF) via AMA connector

The setup process for the CEF via AMA connector has two parts:

1. **Install the Azure Monitor Agent and create a Data Collection Rule (DCR)**.
    - [Using the Azure portal](?tabs=portal#install-the-ama-and-create-a-data-collection-rule-dcr)
    - [Using the Azure Monitor Logs Ingestion API](?tabs=api#install-the-ama-and-create-a-data-collection-rule-dcr)

1. [**Run the "installation" script**](#run-the-installation-script) on the log forwarder to configure the Syslog daemon.

### Prerequisites

- You must have the Microsoft Sentinel **Common Event Format** solution enabled.
- Your Azure account must have the following roles/permissions:

  |Built-in role  |Scope  |Reason  |
  |---------|---------|---------|
  |- [Virtual Machine Contributor](../role-based-access-control/built-in-roles.md)</br>- [Azure Connected Machine<br>&nbsp;&nbsp;&nbsp;Resource Administrator](../role-based-access-control/built-in-roles.md)     |  - Virtual machines</br>- VM scale sets</br>- Azure Arc-enabled servers        |   To deploy the agent      |
  |Any role that includes the action<br>*Microsoft.Resources/deployments/\**    | - Subscription </br>- Resource group</br>- Existing data collection rule       |  To deploy Azure Resource Manager templates       |
  |[Monitoring Contributor ](../role-based-access-control/built-in-roles.md)    |- Subscription </br>- Resource group </br>- Existing data collection rule        | To create or edit data collection rules        |

- You must have a designated Linux VM (your **Log forwarder**) to collect logs.
    - [Create a Linux VM in the Azure portal](../virtual-machines/linux/quick-create-portal.md).
    - [Supported Linux operating systems for Azure Monitor Agent](../azure-monitor/agents/agents-overview.md#linux).

- If your log forwarder *isn't* an Azure virtual machine, it must have the Azure Arc [Connected Machine agent](../azure-arc/servers/overview.md) installed on it.

- The Linux log forwarder VM must have Python 2.7 or 3 installed. Use the ``python --version`` or ``python3 --version`` command to check.

- The log forwarder must have either the `syslog-ng` or `rsyslog` daemon enabled.

- For space requirements for your log forwarder, refer to the [Azure Monitor Agent Performance Benchmark](../azure-monitor/agents/azure-monitor-agent-performance.md). You can also review [this blog post](https://techcommunity.microsoft.com/t5/microsoft-sentinel-blog/designs-for-accomplishing-microsoft-sentinel-scalable-ingestion/ba-p/3741516), which includes designs for scalable ingestion.

- Your log sources (your security devices and appliances) must be configured to send their log messages to the log forwarder's Syslog daemon instead of to their local Syslog daemon.

#### Avoid data ingestion duplication

Using the same facility for both Syslog and CEF messages may result in data ingestion duplication between the CommonSecurityLog and Syslog tables. 

To avoid this scenario, use one of these methods:

- **If the source device enables configuration of the target facility**: On each source machine that sends logs to the log forwarder in CEF format, edit the Syslog configuration file to remove the facilities used to send CEF messages. This way, the facilities sent in CEF won't also be sent in Syslog. Make sure that each DCR you configure in the next steps uses the relevant facility for CEF or Syslog respectively.
- **If changing the facility for the source appliance isn't applicable**: Use an ingest time transformation to filter out CEF messages from the Syslog stream to avoid duplication. The data will be sent twice from the collector machine to the workspace:

    ```kusto
    source |
    where ProcessName !contains "CEF"
    ```

#### Log forwarder security considerations

Make sure to configure the machine's security according to your organization's security policy. For example, you can configure your network to align with your corporate network security policy and change the ports and protocols in the daemon to align with your requirements. To improve your machine security configuration, [secure your VM in Azure](../virtual-machines/security-policy.md), or review these [best practices for network security](../security/fundamentals/network-best-practices.md).

If your devices are sending Syslog and CEF logs over TLS (because, for example, your log forwarder is in the cloud), you need to configure the Syslog daemon (`rsyslog` or `syslog-ng`) to communicate in TLS:

- [Encrypt Syslog traffic with TLS – rsyslog](https://www.rsyslog.com/doc/v8-stable/tutorials/tls_cert_summary.html)
- [Encrypt log messages with TLS – syslog-ng](https://support.oneidentity.com/technical-documents/syslog-ng-open-source-edition/3.22/administration-guide/60#TOPIC-1209298)

### Install the AMA and create a Data Collection Rule (DCR)

You can perform this step in one of two ways:
- Deploy and configure the **CEF via AMA** data connector in the [Microsoft Sentinel portal](?tabs=portal#install-the-ama-and-create-a-data-collection-rule-dcr). With this setup, you can create, manage, and delete DCRs per workspace. The AMA will be installed automatically on the VMs you select in the connector configuration.  
    **&mdash;OR&mdash;**
- Send HTTP requests to the [Logs Ingestion API](?tabs=api#install-the-ama-and-create-a-data-collection-rule-dcr). With this setup, you can create, manage, and delete DCRs. This option is more flexible than the portal. For example, with the API, you can filter by specific log levels, where with the UI, you can only select a minimum log level. The downside is that you have to manually install the Azure Monitor Agent on the log forwarder before creating a DCR.

Select the appropriate tab below to see the instructions for each way.

# [Microsoft Sentinel portal](#tab/portal)

#### Open the connector page and start the DCR wizard

1. Open the [Azure portal](https://portal.azure.com/) and navigate to the **Microsoft Sentinel** service.

1. Select **Data connectors** from the navigation menu

1. Type *CEF* in the **Search** box. From the results, select the **Common Event Format (CEF) via AMA (Preview)** connector.

1. Select **Open connector page** on the details pane.

1. In the **Configuration** area, select **+Create data collection rule**. 

    :::image type="content" source="media/connect-cef-ama/cef-connector-page-create-dcr.png" alt-text="Screenshot showing the CEF via AMA connector page." lightbox="media/connect-cef-ama/cef-connector-page-create-dcr.png":::

1. In the **Basic** tab: 
    - Type a DCR name.
    - Select your subscription.
    - Select the resource group where your collector is defined.

    :::image type="content" source="media/connect-cef-ama/dcr-basics-tab.png" alt-text="Screenshot showing the DCR details in the Basic tab." lightbox="media/connect-cef-ama/dcr-basics-tab.png":::

1. Select **Next: Resources >**.

#### Define resources (VMs)

In the **Resources** tab, select the machines on which you want to install the AMA&mdash;in this case, your log forwarder machine. (If your log forwarder doesn't appear in the list, it might not have the Azure Connected Machine agent installed.)

1. Use the available filters or search box to find your log forwarder VM. You can expand a subscription in the list to see its resource groups, and a resource group to see its VMs.

1. Select the log forwarder VM that you want to install the AMA on. (The check box will appear next to the VM name when you hover over it.)

   :::image type="content" source="media/connect-cef-ama/dcr-select-resources.png" alt-text="Screenshot showing how to select resources when setting up the DCR." lightbox="media/connect-cef-ama/dcr-select-resources.png":::

1. Review your changes and select **Next: Collect >**. 

#### Select facilities and severities and create the DCR

> [!NOTE]
> Using the same facility for both Syslog and CEF messages may result in data ingestion duplication. Learn how to [avoid data ingestion duplication](#avoid-data-ingestion-duplication).

1. In the **Collect** tab, select the minimum log level for each facility. When you select a log level, Microsoft Sentinel collects logs for the selected level and other levels with higher severity. For example, if you select **LOG_ERR**, Microsoft Sentinel collects logs for the **LOG_ERR**, **LOG_CRIT**, **LOG_ALERT**, and **LOG_EMERG** levels.

   :::image type="content" source="media/connect-cef-ama/dcr-log-levels.png" alt-text="Screenshot showing how to select log levels when setting up the DCR.":::

1. Review your selections and select **Next: Review + create**. 

1. In the **Review and create** tab, select **Create**.

   :::image type="content" source="media/connect-cef-ama/dcr-review-create.png" alt-text="Screenshot showing how to review the configuration of the DCR and create it.":::

- The connector will install the Azure Monitor Agent on the machines you selected when creating your DCR.

- You will see notifications from the Azure portal when the DCR is created and the agent is installed.

- Select **Refresh** on the connector page to see the DCR displayed in the list.

# [Logs Ingestion API](#tab/api)

#### Install the Azure Monitor Agent

Follow these instructions, from the Azure Monitor documentation, to install the Azure Monitor Agent on your log forwarder. Remember to use the instructions for Linux, not those for Windows.
- [Install the AMA using PowerShell](../azure-monitor/agents/azure-monitor-agent-manage.md?tabs=azure-powershell)
- [Install the AMA using the Azure CLI](../azure-monitor/agents/azure-monitor-agent-manage.md?tabs=azure-cli)
- [Install the AMA using an Azure Resource Manager template](../azure-monitor/agents/azure-monitor-agent-manage.md?tabs=azure-resource-manager)

You can create Data Collection Rules (DCRs) using the [Azure Monitor Logs Ingestion API](/rest/api/monitor/data-collection-rules). Learn more about [DCRs](../azure-monitor/essentials/data-collection-rule-overview.md).

#### Create the Data Collection Rule

Prepare a DCR file in JSON format. When you send the [API request to create the DCR](#create-the-request-url-and-header), include the contents of this file as the request body.

The following is an example of a DCR creation request:

```json
{
  "location": "centralus",
  "kind": "Linux",
  "properties": {
    "dataSources": {
      "syslog": [
        {
          "name": "localsSyslog",
          "streams": [
            "Microsoft-CommonSecurityLog"
          ],
          "facilityNames": [
            "auth",
            "local0",
            "local1",
            "local2",
            "local3",
            "syslog"
          ],
          "logLevels": [
            "Critical",
            "Alert",
            "Emergency"
          ]
        },
        {
          "name": "authprivSyslog",
          "streams": [
            "Microsoft-CommonSecurityLog"
          ],
          "facilityNames": [
            "authpriv"
          ],
          "logLevels": [
            "Error",
            "Alert",
            "Critical",
            "Emergency"
          ]
        }
      ]
    },
    "destinations": {
      "logAnalytics": [
        {
          "workspaceResourceId": "/subscriptions/aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee/resourceGroups/ContosoRG/providers/Microsoft.OperationalInsights/workspaces/Contoso",
          "workspaceId": "11111111-2222-3333-4444-555555555555",
          "name": "DataCollectionEvent"
        }
      ]
    },
    "dataFlows": [
      {
        "streams": [
          "Microsoft-CommonSecurityLog"
        ],
        "destinations": [
          "DataCollectionEvent"
        ]
      }
    ]
  }
}
```

- Verify that the `streams` field is set to `Microsoft-CommonSecurityLog`.
- Add the filter and facility log levels in the `facilityNames` and `logLevels` parameters. See [examples below](#examples-of-facilities-and-log-levels-sections).

##### Create the request URL and header

1. Copy the request URL and header below by selecting the *copy* icon in the upper right corner of the frame.

    ```http
    PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Insights/dataCollectionRules/{dataCollectionRuleName}?api-version=2022-06-01
    ```

    - Substitute the appropriate values for the `{subscriptionId}` and `{resourceGroupName}` placeholders. 
    - Enter a name of your choice for the DCR in place of the `{dataCollectionRuleName}` placeholder.

    Example:
    ```http
    PUT https://management.azure.com/subscriptions/aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee/resourceGroups/ContosoRG/providers/Microsoft.Insights/dataCollectionRules/Contoso-DCR-01?api-version=2022-06-01
    ```

1. Paste the request URL and header in a REST API client of your choosing.

##### Create the request body and send the request

Copy and paste the DCR JSON file that you created (based on the example above) into the request body, and send it.
 
Here's the response you should receive according to the sample request above:

```json
  {
    "properties": {
      "immutableId": "dcr-0123456789abcdef0123456789abcdef",
      "dataSources": {
        "syslog": [
          {
            "streams": [
              "Microsoft-CommonSecurityLog"
            ],
            "facilityNames": [
              "auth",
              "local0",
              "local1",
              "local2",
              "local3",
              "syslog"
            ],
            "logLevels": [
              "Critical",
              "Alert",
              "Emergency"
            ],
            "name": "localsSyslog"
          },
          {
            "streams": [
              "Microsoft-CommonSecurityLog"
            ],
            "facilityNames": [
              "authpriv"
            ],
            "logLevels": [
              "Error",
              "Alert",
              "Critical",
              "Emergency"
            ],
            "name": "authprivSyslog"
          }
        ]
      },
      "destinations": {
        "logAnalytics": [
          {
            "workspaceResourceId": "/subscriptions/aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee/resourceGroups/ContosoRG/providers/Microsoft.OperationalInsights/workspaces/Contoso",
            "workspaceId": "11111111-2222-3333-4444-555555555555",
            "name": "DataCollectionEvent"
          }
        ]
      },
      "dataFlows": [
        {
          "streams": [
            "Microsoft-CommonSecurityLog"
          ],
          "destinations": [
            "DataCollectionEvent"
          ]
        }
      ],
      "provisioningState": "Succeeded"
    },
    "location": "centralus",
    "kind": "Linux",
    "id": "/subscriptions/aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee/resourceGroups/ContosoRG/providers/Microsoft.Insights/dataCollectionRules/Contoso-DCR-01",
    "name": "Contoso-DCR-01",
    "type": "Microsoft.Insights/dataCollectionRules",
    "etag": "\"00000000-0000-0000-0000-000000000000\"",
    "systemData": {
    }
  }
```

#### Associate the DCR with the log forwarder

Now you need to create a DCR Association (DCRA) that ties the DCR to the VM resource that hosts your log forwarder.

This procedure follows the same steps as creating the DCR.

##### Request URL and header

```http
PUT 
https://management.azure.com/subscriptions/aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee/resourceGroups/ContosoRG/providers/Microsoft.Compute/virtualMachines/LogForwarder-VM-1/providers/Microsoft.Insights/dataCollectionRuleAssociations/contoso-dcr-assoc?api-version=2022-06-01
```

##### Request body

```json
{
  "properties": {
    "dataCollectionRuleId": "/subscriptions/aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee/resourceGroups/ContosoRG/providers/Microsoft.Insights/dataCollectionRules/Contoso-DCR-01"
  }
}
```

Here's a sample response:

```json
{
    "properties": {
      "dataCollectionRuleId": "/subscriptions/aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee/resourceGroups/ContosoRG/providers/Microsoft.Insights/dataCollectionRules/Contoso-DCR-01"
    },
    "id": "/subscriptions/aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee/resourceGroups/ContosoRG/providers/Microsoft.Compute/virtualMachines/LogForwarder-VM-1/providers/Microsoft.Insights/dataCollectionRuleAssociations/contoso-dcr-assoc",
    "name": "contoso-dcr-assoc",
    "type": "Microsoft.Insights/dataCollectionRuleAssociations",
    "etag": "\"00000000-0000-0000-0000-000000000000\"",
    "systemData": {
    }
  }
```

#### Examples of facilities and log levels sections

Review these examples of the facilities and log levels settings. The `name` field includes the filter name.

This example collects events from the `cron`, `daemon`, `local0`, `local3` and `uucp` facilities, with the `Warning`, `Error`, `Critical`, `Alert`, and `Emergency` log levels:

```json
    "dataSources": {
      "syslog": [
        {
        "name": "SyslogStream0",
        "streams": [
          "Microsoft-CommonSecurityLog"
        ],
        "facilityNames": [ 
          "cron",
          "daemon",
          "local0",
          "local3", 
          "uucp"
        ],
        "logLevels": [ 
          "Warning", 
          "Error", 
          "Critical", 
          "Alert", 
          "Emergency"
        ]
      }
    ]
  }
```

This example collects events for:
- The `authpriv` and `mark` facilities with the `Info`, `Notice`, `Warning`, `Error`, `Critical`, `Alert`, and `Emergency` log levels 
- The `daemon` facility with the `Warning`, `Error`, `Critical`, `Alert`, and `Emergency` log levels 
- The `kern`, `local0`, `local5`, and `news` facilities with the `Critical`, `Alert`, and `Emergency` log levels 
- The `mail` and `uucp` facilities with the `Emergency` log level

```json
    "dataSources": {
      "syslog": [
        {
          "name": "SyslogStream1",
          "streams": [ 
            "Microsoft-CommonSecurityLog"
          ],
          "facilityNames": [ 
            "authpriv", 
            "mark"
          ],
          "logLevels": [
            "Info",
            "Notice", 
            "Warning", 
            "Error", 
            "Critical", 
            "Alert", 
            "Emergency"
          ]
        },
        {
          "name": "SyslogStream2",
          "streams": [ 
            "Microsoft-CommonSecurityLog"
          ],
          "facilityNames": [ 
            "daemon"
          ],
          "logLevels": [ 
            "Warning", 
            "Error", 
            "Critical", 
            "Alert", 
            "Emergency"
          ]
        },
        {
          "name": "SyslogStream3",
          "streams": [ 
            "Microsoft-CommonSecurityLog"
          ],
          "facilityNames": [ 
            "kern",
            "local0",
            "local5", 
            "news"
          ],
          "logLevels": [ 
            "Critical", 
            "Alert", 
            "Emergency"
          ]
        },
        {
          "name": "SyslogStream4",
          "streams": [ 
            "Microsoft-CommonSecurityLog"
          ],
          "facilityNames": [ 
            "mail",
            "uucp"
          ],
          "logLevels": [ 
            "Emergency"
          ]
        }
      ]
    }

```

---

### Run the "installation" script

The "installation" script doesn't actually install anything, but it configures the Syslog daemon on your log forwarder properly to collect the logs.

1. From the connector page, copy the command line that appears under **Run the following command to install and apply the CEF collector:** by selecting the *copy* icon next to it.

    :::image type="content" source="media/connect-cef-ama/run-install-script.png" alt-text="Screenshot of command line on connector page.":::

    You can also copy it from here:
    ```python
    sudo wget -O Forwarder_AMA_installer.py https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/Syslog/Forwarder_AMA_installer.py&&sudo python Forwarder_AMA_installer.py
    ```

1. Log in to the log forwarder machine where you just installed the AMA.

1. Paste the command you copied in the last step to launch the installation script.  
    The script configures the `rsyslog` or `syslog-ng` daemon to use the required protocol and restarts the daemon. The script opens port 514 to listen to incoming messages in both UDP and TCP protocols. To change this setting, refer to the Syslog daemon configuration file according to the daemon type running on the machine:
    - Rsyslog: `/etc/rsyslog.conf`
    - Syslog-ng: `/etc/syslog-ng/syslog-ng.conf`

    > [!NOTE] 
    > To avoid [Full Disk scenarios](../azure-monitor/agents/azure-monitor-agent-troubleshoot-linux-vm-rsyslog.md) where the agent can't function, we recommend that you set the `syslog-ng` or `rsyslog` configuration not to store unneeded logs. A Full Disk scenario disrupts the function of the installed AMA.
    > Read more about [RSyslog](https://www.rsyslog.com/doc/master/configuration/actions.html) or [Syslog-ng](https://www.syslog-ng.com/technical-documents/doc/syslog-ng-open-source-edition/3.26/administration-guide/34#TOPIC-1431029).

## Test the connector

1. To validate that the syslog daemon is running on the UDP port and that the AMA is listening, run this command:

    ```
    netstat -lnptv
    ```

    You should see the `rsyslog` or `syslog-ng` daemon listening on port 514. 

1. To capture messages sent from a logger or a connected device, run this command in the background:

    ```
    tcpdump -i any port 514 -A -vv &
    ```
1. After you complete the validation, we recommend that you stop the `tcpdump`: Type `fg` and then select <kbd>Ctrl</kbd>+<kbd>C</kbd>.
1. To send demo messages, do one of the following: 
    - Use the netcat utility. In this example, the utility reads data posted through the `echo` command with the newline switch turned off. The utility then writes the data to UDP port `514` on the localhost with no timeout. To execute the netcat utility, you might need to install an additional package.
    
        ```
        echo -n "<164>CEF:0|Mock-test|MOCK|common=event-format-test|end|TRAFFIC|1|rt=$common=event-formatted-receive_time" | nc -u -w0 localhost 514
        ```
    - Use the logger. This example writes the message to the `local 4` facility, at severity level `Warning`, to port `514`, on the local host, in the CEF RFC format. The `-t` and `--rfc3164` flags are used to comply with the expected RFC format.
    
        ```
        logger -p local4.warn -P 514 -n 127.0.0.1 --rfc3164 -t CEF "0|Mock-test|MOCK|common=event-format-test|end|TRAFFIC|1|rt=$common=event-formatted-receive_time"
        ```    

1. To verify that the connector is installed correctly, run the troubleshooting script with one of these commands:

    - For CEF logs, run:
        
        ```python
         sudo wget -O Sentinel_AMA_troubleshoot.py https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/Syslog/Sentinel_AMA_troubleshoot.py&&sudo python Sentinel_AMA_troubleshoot.py --cef
        ```

    - For Cisco Adaptive Security Appliance (ASA) logs, run:

        ```python
        sudo wget -O Sentinel_AMA_troubleshoot.py https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/Syslog/Sentinel_AMA_troubleshoot.py&&sudo python Sentinel_AMA_troubleshoot.py --asa
        ```
 
    - For Cisco Firepower Threat Defense (FTD) logs, run:
    
        ```python
        sudo wget -O Sentinel_AMA_troubleshoot.py https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/Syslog/Sentinel_AMA_troubleshoot.py&&sudo python Sentinel_AMA_troubleshoot.py --ftd
        ```

## Next steps

In this article, you learned how to set up data ingestion from security devices and appliances that support CEF over Syslog, using the **Common Event Format (CEF) via AMA (Preview)** connector. 

- Explore in greater depth how to [collect CEF or Syslog logs with the Azure Monitor Agent](../azure-monitor/agents/data-collection-syslog.md), including how to configure Syslog and create a DCR.
- See other articles about ingesting CEF and Syslog logs:
    - [Stream logs in both the CEF and Syslog format](connect-cef-syslog.md)
    - [Options for streaming logs in the CEF and Syslog format to Microsoft Sentinel](connect-cef-syslog-options.md)


To learn more about Microsoft Sentinel, see the following articles:
- Learn how to [get visibility into your data, and potential threats](get-visibility.md).
- Get started [detecting threats with Microsoft Sentinel](detect-threats-built-in.md).
- [Use workbooks](monitor-your-data.md) to monitor your data.
