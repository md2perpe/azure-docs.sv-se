---
title: Samla in Azure-tjänstloggar och mått i Log Analytics-arbetsyta | Microsoft Docs
description: Konfigurera diagnostik på Azure-resurser att skriva loggar och mått till Log Analytics-arbetsyta i Azure Monitor.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: 84105740-3697-4109-bc59-2452c1131bfe
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/12/2017
ms.author: magoedte
ms.openlocfilehash: d086b6f844deb06d98edec8d8ec0f5670d84f066
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66129707"
---
# <a name="collect-azure-service-logs-and-metrics-into-log-analytics-workspace-in-azure-monitor"></a>Samla in Azure-tjänstloggar och mått i Log Analytics-arbetsyta i Azure Monitor

Det finns fyra olika sätt att samla in loggar och mått för Azure-tjänster:

1. Azure Diagnostics-data direkt till Log Analytics-arbetsyta i Azure Monitor (*diagnostik* i tabellen nedan)
2. Azure Diagnostics-data till Azure-lagring till Log Analytics-arbetsyta i Azure Monitor (*Storage* i tabellen nedan)
3. Anslutningsappar för Azure-tjänster (*kopplingar* i tabellen nedan)
4. Skript för att samla in och publicera sedan data till Log Analytics-arbetsyta i Azure Monitor (tomma i följande tabell och för tjänster som inte visas)


| Tjänst                 | Resurstyp                           | Loggar        | Mått     | Lösning |
| --- | --- | --- | --- | --- |
| Programgateways    | Microsoft.Network/applicationGateways   | Diagnostik | Diagnostik | [Azure Application Gateway Analytics](../insights/azure-networking-analytics.md#azure-application-gateway-analytics-solution-in-azure-monitor) |
| Application insights    |                                         | Koppling   | Koppling   | [Application Insights-anslutningsprogram](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/) (förhandsversion) |
| Automation-konton     | Microsoft.Automation/AutomationAccounts | Diagnostik |             | [Mer information](../../automation/automation-manage-send-joblogs-log-analytics.md)|
| Batch-konton          | Microsoft.Batch/batchAccounts           | Diagnostik | Diagnostik | |
| Klassiska molntjänster  |                                         | Storage     |             | [Mer information](azure-storage-iis-table.md) |
| Cognitive Services      | Microsoft.CognitiveServices/accounts    |             | Diagnostik | |
| Data Lake analytics     | Microsoft.DataLakeAnalytics/accounts    | Diagnostik |             | |
| Data Lake store         | Microsoft.DataLakeStore/accounts        | Diagnostik |             | |
| Namnområde för händelsehubb     | Microsoft.EventHub/namespaces           | Diagnostik | Diagnostik | |
| IoT-hubbar                | Microsoft.Devices/IotHubs               |             | Diagnostik | |
| Key Vault               | Microsoft.KeyVault/vaults               | Diagnostik |             | [KeyVault-analys](../insights/azure-key-vault.md) |
| Lastbalanserare          | Microsoft.Network/loadBalancers         | Diagnostik |             |  |
| Logic Apps              | Microsoft.Logic/workflows <br> Microsoft.Logic/integrationAccounts | Diagnostik | Diagnostik | |
| Nätverkssäkerhetsgrupper | Microsoft.Network/networksecuritygroups | Diagnostik |             | [Azure Network Security Group Analytics](../insights/azure-networking-analytics.md#azure-network-security-group-analytics-solution-in-azure-monitor) |
| Recovery-valv         | Microsoft.RecoveryServices/vaults       |             |             | [Azure Recovery Services-analys (förhandsversion)](https://github.com/krnese/AzureDeploy/blob/master/OMS/MSOMS/Solutions/recoveryservices/)|
| Söktjänster         | Microsoft.Search/searchServices         | Diagnostik | Diagnostik | |
| Service Bus-namnområde   | Microsoft.ServiceBus/namespaces         | Diagnostik | Diagnostik | [Service Bus-analys (förhandsversion)](https://github.com/Azure/azure-quickstart-templates/tree/master/oms-servicebus-solution)|
| Service Fabric          |                                         | Storage     |             | [Service Fabric-analys (förhandsversion)](../../service-fabric/service-fabric-diagnostics-oms-setup.md) |
| SQL (v12)               | Microsoft.Sql/servers/databases <br> Microsoft.Sql/servers/elasticPools |             | Diagnostik | [Azure SQL-analys (förhandsversion)](../insights/azure-sql.md) |
| Storage                 |                                         |             | Skript      | [Azure Storage-analys (förhandsversion)](https://github.com/Azure/azure-quickstart-templates/tree/master/oms-azure-storage-analytics-solution) |
| Virtuella datorer        | Microsoft.Compute/virtualMachines       | Anknytning   | Anknytning <br> Diagnostik  | |
| Skalningsuppsättningar för virtuella datorer | Microsoft.Compute/virtualMachines <br> Microsoft.Compute/virtualMachineScaleSets/virtualMachines |             | Diagnostik | |
| Server webbgrupper        | Microsoft.Web/serverfarms               |             | Diagnostik | |
| Webbplatser               | Microsoft.Web/sites <br> Microsoft.Web/sites/slots |             | Diagnostik | [Azure Web Apps-analys (förhandsversion)](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-web-apps-analytics) |


> [!NOTE]
> För att övervaka Azure-datorer (både Linux och Windows), vi rekommenderar att du installerar den [Log Analytics VM-tillägget](../../azure-monitor/learn/quick-collect-azurevm.md). Agenten ger dig information som samlas in från dina virtuella datorer. Du kan också använda tillägget för VM-skalningsuppsättningar.
>
>

## <a name="azure-diagnostics-direct-to-log-analytics"></a>Azure Diagnostics-data direkt till Log Analytics
Många Azure-resurser kan skriva diagnostikloggar och mått direkt till en Log Analytics-arbetsyta i Azure Monitor och det är det bästa sättet att samla in data för analys. När du använder Azure-diagnostik kan data skrivs direkt till arbetsytan och behöver du inte först skriva data till lagring.

Azure-resurser som stöder [Azure monitor](../../azure-monitor/overview.md) kan skicka sina loggar och mått direkt till Log Analytics-arbetsytan.

> [!NOTE]
> Skicka flerdimensionella mätvärden till en Log Analytics Workspace via diagnostikinställningar stöds inte för närvarande. Mått med dimensioner exporteras som tillplattade endimensionella mått som aggregeras över dimensionsvärden.
>
> *Till exempel*: Måttet för inkommande meddelanden i en händelsehubb kan utforskas och visas för varje enskild kö. Men när exporteras via diagnostikinställningar mått representeras som alla inkommande meddelanden för alla köer i hubben.
>
>

* Information för tillgängliga mått i [stöds mått med Azure Monitor](../../azure-monitor/platform/metrics-supported.md).
* Information om tillgängliga loggar finns i [tjänster och -schemat stöds för diagnostikloggar](../../azure-monitor/platform/diagnostic-logs-schema.md).

### <a name="enable-diagnostics-with-powershell"></a>Aktivera diagnostik med PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Följande PowerShell-exempel visar hur du använder [Set-AzDiagnosticSetting](/powershell/module/Az.Monitor/Set-AzDiagnosticSetting) att aktivera diagnostik på en grupp. Samma metod som fungerar för alla resurser som stöds – ange `$resourceId` till resurs-id för den resurs du vill aktivera diagnostik för.

```powershell
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$resourceId = "/SUBSCRIPTIONS/ec11ca60-1234-491e-5678-0ea07feae25c/RESOURCEGROUPS/DEMO/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/DEMO"

Set-AzDiagnosticSetting -ResourceId $ResourceId  -WorkspaceId $workspaceId -Enabled $true
```

### <a name="enable-diagnostics-with-resource-manager-templates"></a>Aktivera diagnostik med Resource Manager-mallar

Aktivera diagnostik på en resurs när den har skapats och har diagnostiken skickas till Log Analytics-arbetsytan du kan använda en mall som liknar den nedan. Det här exemplet är för ett Automation-konto men fungerar för alla resurstyper som stöds.

```json
        {
            "type": "Microsoft.Automation/automationAccounts/providers/diagnosticSettings",
            "name": "[concat(parameters('omsAutomationAccountName'), '/', 'Microsoft.Insights/service')]",
            "apiVersion": "2015-07-01",
            "dependsOn": [
                "[concat('Microsoft.Automation/automationAccounts/', parameters('omsAutomationAccountName'))]",
                "[concat('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspaceName'))]"
            ],
            "properties": {
                "workspaceId": "[resourceId('Microsoft.OperationalInsights/workspaces', parameters('omsWorkspaceName'))]",
                "logs": [
                    {
                        "category": "JobLogs",
                        "enabled": true
                    },
                    {
                        "category": "JobStreams",
                        "enabled": true
                    }
                ]
            }
        }
```

[!INCLUDE [log-analytics-troubleshoot-azure-diagnostics](../../../includes/log-analytics-troubleshoot-azure-diagnostics.md)]

## <a name="azure-diagnostics-to-storage-then-to-log-analytics"></a>Azure Diagnostics-data till lagring och sedan till Log Analytics

Det är möjligt att skicka dem till Azure storage och sedan konfigurera Log Analytics-arbetsytan om du vill läsa loggarna från storage för att samla in loggar från inom några resurser.

Azure Monitor kan använda den här metoden för att samla in diagnostik från Azure storage för följande resurser och loggar:

| Resurs | Loggar |
| --- | --- |
| Service Fabric |ETWEvent <br> Drifthändelse <br> Tillförlitliga aktörer-händelse <br> Händelse för tillförlitlig tjänst |
| Virtuella datorer |Linux Syslog <br> Windows-händelse <br> IIS-logg <br> Windows ETWEvent |
| Web-roller <br> Worker-roller |Linux Syslog <br> Windows-händelse <br> IIS-logg <br> Windows ETWEvent |

> [!NOTE]
> Du debiteras normal datataxa Azure för lagring och transaktioner när du skickar diagnostik till ett lagringskonto och för när Log Analytics-arbetsytan läser data från ditt lagringskonto.
>
>

Se [blobblagring för IIS- och table storage för händelser](azure-storage-iis-table.md) mer information om hur Azure Monitor kan samla in dessa loggar.

## <a name="connectors-for-azure-services"></a>Anslutningsappar för Azure-tjänster

Det finns en koppling för Application Insights, vilket gör att data som samlas in av Application Insights som ska skickas till Log Analytics-arbetsytan.

Läs mer om den [Application Insights connector](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/).

## <a name="scripts-to-collect-and-post-data-to-log-analytics-workspace"></a>Skript för att samla in och skicka data till Log Analytics-arbetsyta

Du kan använda ett Azure Automation-skript för Azure-tjänster som inte uppger ett enkelt sätt att skicka loggar och mått till en Log Analytics-arbetsyta för att samla in logg och mått. Skriptet kan sedan skicka data till en arbetsyta med hjälp av den [API för datainsamling](../../azure-monitor/platform/data-collector-api.md)

Azure-mall-galleriet har [exempel på användning av Azure Automation](https://azure.microsoft.com/resources/templates/?term=OMS) att samla in data från tjänster och skicka den till Azure Monitor.

## <a name="next-steps"></a>Nästa steg

* [Använda blob storage för IIS- och table storage för händelser](azure-storage-iis-table.md) att läsa loggarna för Azure-tjänster som diagnostik för skrivning till tabellagring eller IIS-loggar som skrivs till blob-lagring.
* [Aktivera lösningar](../../azure-monitor/insights/solutions.md) att ge insikter i data.
* [Använda sökfrågor](../../azure-monitor/log-query/log-query-overview.md) att analysera data.
