---
title: Skapa Måttaviseringar för loggar i Azure Monitor
description: Självstudiekurs om hur du skapar nästan i realtid måttaviseringar på populära log analytics-data.
author: msvijayn
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/17/2018
ms.author: vinagara
ms.subservice: alerts
ms.openlocfilehash: 1c744e0063d5c56b2ca17f2b6c6fa694ad13a26c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64872581"
---
# <a name="create-metric-alerts-for-logs-in-azure-monitor"></a>Skapa Måttaviseringar för loggar i Azure Monitor

## <a name="overview"></a>Översikt

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Azure Monitor stöder [måttaviseringstypen](../../azure-monitor/platform/alerts-metric-near-real-time.md) som har fördelar över den [klassiska aviseringar](../../azure-monitor/platform/alerts-classic-portal.md). Mått är tillgängliga för [lång lista med Azure-tjänster](../../azure-monitor/platform/metrics-supported.md). Den här artikeln beskriver användningen av en delmängd (det vill säga) för resurs - `Microsoft.OperationalInsights/workspaces`.

Du kan använda måttaviseringar på den populära Log Analytics loggar extraherade som mått som en del av mått från loggar, inklusive resurser i Azure eller lokalt. De Log Analytics-lösningarna som stöds finns nedan:

- [Prestandaräknare](../../azure-monitor/platform/data-sources-performance-counters.md) för Windows och Linux-datorer
- [Pulsslagsposter för Agenthälsa](../../azure-monitor/insights/solution-agenthealth.md)
- [Hantering av uppdateringar](../../automation/automation-update-management.md) poster
- [Händelsedata](../../azure-monitor/platform/data-sources-windows-events.md) loggar

Det finns många fördelar för att använda **mått aviseringar för loggar** över frågebaserade [Loggaviseringar](../../azure-monitor/platform/alerts-log.md) i Azure; vissa av dem anges nedan:

- Måttaviseringar erbjuder nästan i realtid övervakning funktions- och aviseringar för mått för loggar förgreningar data från log källa för att se till att samma.
- Måttaviseringar är tillståndskänsliga - bara meddela när när aviseringen utlöses och när när varningen har åtgärdats; till skillnad från aviseringar, som är tillståndslösa och hålla aktiveringen vid varje intervall om villkoret för aviseringen är uppfyllt.
- Måttaviseringar för logg tillhandahåller flera dimensioner så att filtrering till specifika värden som datorer, OS-typ, etc. enklare; utan att behöva penning fråga i analytics.

> [!NOTE]
> Specifika mått och/eller dimension endast visas om det finns data för den valda perioden. De här måtten är tillgängliga för kunder med Azure Log Analytics-arbetsytor.

## <a name="metrics-and-dimensions-supported-for-logs"></a>Mått och dimensioner som stöds för loggar

 måttaviseringar stöd för aviseringar för mått med dimensioner. Du kan använda dimensioner för att filtrera dina mått till rätt nivå. En fullständig lista över mått som stöds för loggar från [Log Analytics-arbetsytor](../../azure-monitor/platform/metrics-supported.md#microsoftoperationalinsightsworkspaces) visas; över lösningar som stöds.

> [!NOTE]
> Visa mått som stöds för extraheras från Log Analytics-arbetsytan via [Azure Monitor - mått](../../azure-monitor/platform/metrics-charts.md); en mått Avisera för log måste skapas för dessa mått. Dimensioner som valts i måttet aviseringar för loggar - visas endast för utforskning via Azure Monitor - mått.

## <a name="creating-metric-alert-for-log-analytics"></a>Skapa en måttavisering för Log Analytics

Måttdata från populära loggar skickas innan den bearbetas i Log Analytics i Azure Monitor - mått. På så sätt kan användarna möjlighet att utnyttja funktionerna i mått plattformen samt metrisk varning – inklusive med aviseringar med frekvens så lågt som 1 minut.
Nedan visas ett sätt att utforma en måttavisering för loggar.

## <a name="prerequisites-for-metric-alert-for-logs"></a>Krav för metrisk varning för loggar

Innan mått för loggar som samlats in på Log Analytics data fungerar kan måste följande anges och är tillgängliga:

1. **Aktiva Log Analytics-arbetsytan**: Ett giltigt och aktivt Log Analytics-arbetsytan måste finnas. Mer information finns i [skapar en Log Analytics-arbetsyta i Azure-portalen](../../azure-monitor/learn/quick-create-workspace.md).
2. **Agenten har konfigurerats för Log Analytics-arbetsytan**: Agenten måste konfigureras för virtuella Azure-datorer (och/eller) den lokala virtuella datorer att skicka data till Log Analytics-arbetsyta som används i tidigare steg. Mer information finns i [Log Analytics - översikt över Övervakningsagenten](../../azure-monitor/platform/agents-overview.md).
3. **Stöds Log Analytics-lösningar har installerats**: Log Analytics-lösningen ska vara konfigurerad och skicka data till Log Analytics-arbetsyta – stöd för lösningar är [prestandaräknare för Windows och Linux](../../azure-monitor/platform/data-sources-performance-counters.md), [pulsslagsposter för Agenthälsa](../../azure-monitor/insights/solution-agenthealth.md) , [Uppdateringshantering](../../automation/automation-update-management.md), och [händelsedata](../../azure-monitor/platform/data-sources-windows-events.md).
4. **Logga Analyslösningar som konfigurerats för att skicka loggar**: Log Analytics-lösning bör ha det nödvändiga loggar/data som motsvarar [mått som stöds för Log Analytics-arbetsytor](../../azure-monitor/platform/metrics-supported.md#microsoftoperationalinsightsworkspaces) aktiverat. Till exempel för *% tillgängligt minne* räknare för den måste konfigureras i [prestandaräknare](../../azure-monitor/platform/data-sources-performance-counters.md) lösning första.

## <a name="configuring-metric-alert-for-logs"></a>Konfigurera metrisk varning för loggar

 måttaviseringar kan skapas och hanteras med hjälp av Azure portal, Resource Manager-mallar, REST API, PowerShell och Azure CLI. Eftersom mått aviseringar för loggar är en variant av måttaviseringar - när kraven är klar, kan du skapa metrisk varning för loggar för angivna Log Analytics-arbetsyta. Alla egenskaper och funktioner för [måttaviseringar](../../azure-monitor/platform/alerts-metric-near-real-time.md) kommer att användas på måttaviseringar för loggar också, inklusive nyttolast schemat och tillämpliga kvotgränser faktureras pris.

Steg för steg-information och exempel – i [skapa och hantera måttaviseringar](https://aka.ms/createmetricalert). Mer specifikt för mått aviseringar för loggar – Följ instruktionerna för att hantera aviseringar för mått och kontrollera följande:

- Mål för metrisk varning är ett giltigt *Log Analytics-arbetsyta*
- Signal som valts för metrisk varning för valda *Log Analytics-arbetsyta* är av typen **mått**
- Filter för särskilda villkor eller resursen med hjälp av dimensionsfilter; mått för loggar är flerdimensionella
- När du konfigurerar *Signallogiken*, kan du skapa en avisering som sträcker sig över flera värden för dimensionen (till exempel dator)
- Om **inte** med hjälp av Azure portal för att skapa en måttavisering för valda *Log Analytics-arbetsyta*; och sedan användaren manuellt måste först skapa en regel för explicit för att konvertera loggdata till ett mått med hjälp av [Azure Monitor - schemalagda Frågeregler](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules).

> [!NOTE]
> När du skapar måttavisering för Log Analytics-arbetsytan via Azure portal – motsvarande regel för att konvertera loggdata till mått via [Azure Monitor - schemalagda Frågeregler](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) skapas automatiskt i bakgrunden,  *utan behov av en användaråtgärd eller åtgärd*. Metrisk varning för skapande av loggar använda andra metoder än Azure-portalen finns i [resursmall för måttet aviseringar för loggar](#resource-template-for-metric-alerts-for-logs) avsnittet om exemplet metoder för att skapa en ScheduledQueryRule baserat logg till mått regeln innan metrisk varning Skapa - annan det inte finns några data för metrisk varning i loggar som skapas.

## <a name="resource-template-for-metric-alerts-for-logs"></a>Resursmall för Måttaviseringar för loggar

Som tidigare nämnts är processen för att skapa måttaviseringar från loggar två pronged:

1. Skapa en regel för att extrahera mått från stöds loggar med scheduledQueryRule API
2. Skapa en måttavisering för måttet som extraheras från log (i steg 1) och Log Analytics-arbetsyta som en målresurs

### <a name="metric-alerts-for-logs-with-static-threshold"></a>Måttaviseringar för loggar med statiskt tröskelvärde

Om du vill göra samma sak, kan en använda exemplet Azure Resource Manager-mall nedan – där skapandet av en statisk tröskelvärdet måttavisering beror på regeln för att extrahera mått från loggar via scheduledQueryRule har skapats.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "convertRuleName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the rule to convert log to metric"
            }
        },
        "convertRuleDescription": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Description for log converted to metric"
            }
        },
        "convertRuleRegion": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the region used by workspace"
            }
        },
        "convertRuleStatus": {
            "type": "string",
            "defaultValue": "true",
            "metadata": {
                "description": "Specifies whether the log conversion rule is enabled"
            }
        },
        "convertRuleMetric": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the metric once extraction done from logs."
            }
        },
        "alertName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the alert"
            }
        },
        "alertDescription": {
            "type": "string",
            "defaultValue": "This is a metric alert",
            "metadata": {
                "description": "Description of alert"
            }
        },
        "alertSeverity": {
            "type": "int",
            "defaultValue": 3,
            "allowedValues": [
                0,
                1,
                2,
                3,
                4
            ],
            "metadata": {
                "description": "Severity of alert {0,1,2,3,4}"
            }
        },
        "isEnabled": {
            "type": "bool",
            "defaultValue": true,
            "metadata": {
                "description": "Specifies whether the alert is enabled"
            }
        },
        "resourceId": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Full Resource ID of the resource emitting the metric that will be used for the comparison. For example /subscriptions/00000000-0000-0000-0000-0000-00000000/resourceGroups/ResourceGroupName/providers/Microsoft.compute/virtualMachines/VM_xyz"
            }
        },
        "metricName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the metric used in the comparison to activate the alert."
            }
        },
        "operator": {
            "type": "string",
            "defaultValue": "GreaterThan",
            "allowedValues": [
                "Equals",
                "NotEquals",
                "GreaterThan",
                "GreaterThanOrEqual",
                "LessThan",
                "LessThanOrEqual"
            ],
            "metadata": {
                "description": "Operator comparing the current value with the threshold value."
            }
        },
        "threshold": {
            "type": "string",
            "defaultValue": "0",
            "metadata": {
                "description": "The threshold value at which the alert is activated."
            }
        },
        "timeAggregation": {
            "type": "string",
            "defaultValue": "Average",
            "allowedValues": [
                "Average",
                "Minimum",
                "Maximum",
                "Total"
            ],
            "metadata": {
                "description": "How the data that is collected should be combined over time."
            }
        },
        "windowSize": {
            "type": "string",
            "defaultValue": "PT5M",
            "metadata": {
                "description": "Period of time used to monitor alert activity based on the threshold. Must be between five minutes and one day. ISO 8601 duration format."
            }
        },
        "evaluationFrequency": {
            "type": "string",
            "defaultValue": "PT1M",
            "metadata": {
                "description": "how often the metric alert is evaluated represented in ISO 8601 duration format"
            }
        },
        "actionGroupId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The ID of the action group that is triggered when the alert is activated or deactivated"
            }
        }
    },
    "variables": {
        "convertRuleTag": "hidden-link:/subscriptions/1234-56789-1234-567a/resourceGroups/resourceGroupName/providers/Microsoft.OperationalInsights/workspaces/workspaceName",
        "convertRuleSourceWorkspace": {
            "SourceId": "/subscriptions/1234-56789-1234-567a/resourceGroups/resourceGroupName/providers/Microsoft.OperationalInsights/workspaces/workspaceName"
        }
    },
    "resources": [
        {
            "name": "[parameters('convertRuleName')]",
            "type": "Microsoft.Insights/scheduledQueryRules",
            "apiVersion": "2018-04-16",
            "location": "[parameters('convertRuleRegion')]",
            "tags": {
                "[variables('convertRuleTag')]": "Resource"
            },
            "properties": {
                "description": "[parameters('convertRuleDescription')]",
                "enabled": "[parameters('convertRuleStatus')]",
                "source": {
                    "dataSourceId": "[variables('convertRuleSourceWorkspace').SourceId]"
                },
                "action": {
                    "odata.type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.Microsoft.AppInsights.Nexus.DataContracts.Resources.ScheduledQueryRules.LogToMetricAction",
                    "criteria": [{
                            "metricName": "[parameters('convertRuleMetric')]",
                            "dimensions": []
                        }
                    ]
                }
            }
        },
        {
            "name": "[parameters('alertName')]",
            "type": "Microsoft.Insights/metricAlerts",
            "location": "global",
            "apiVersion": "2018-03-01",
            "tags": {},
            "dependsOn":["[resourceId('Microsoft.Insights/scheduledQueryRules',parameters('convertRuleName'))]"],
            "properties": {
                "description": "[parameters('alertDescription')]",
                "severity": "[parameters('alertSeverity')]",
                "enabled": "[parameters('isEnabled')]",
                "scopes": ["[parameters('resourceId')]"],
                "evaluationFrequency":"[parameters('evaluationFrequency')]",
                "windowSize": "[parameters('windowSize')]",
                "criteria": {
                    "odata.type": "Microsoft.Azure.Monitor.SingleResourceMultipleMetricCriteria",
                    "allOf": [
                        {
                            "name" : "1st criterion",
                            "metricName": "[parameters('metricName')]",
                            "dimensions":[],
                            "operator": "[parameters('operator')]",
                            "threshold" : "[parameters('threshold')]",
                            "timeAggregation": "[parameters('timeAggregation')]"
                        }
                    ]
                },
                "actions": [
                    {
                        "actionGroupId": "[parameters('actionGroupId')]"
                    }
                ]
            }
        }
    ]
}
```

Anta att ovanstående JSON sparas som metricfromLogsAlertStatic.json - och du kan kombinera det med en JSON-fil som ska användas som parameter för att skapa en resurs mallbaserad. En parameter JSON-exempelfilen nedan:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "convertRuleName": {
            "value": "TestLogtoMetricRule" 
        },
        "convertRuleDescription": {
            "value": "Test rule to extract metrics from logs via template"
        },
        "convertRuleRegion": {
            "value": "West Central US"
        },
        "convertRuleStatus": {
            "value": "true"
        },
        "convertRuleMetric": {
            "value": "Average_% Idle Time"
        },
        "alertName": {
            "value": "TestMetricAlertonLog"
        },
        "alertDescription": {
            "value": "New multi-dimensional metric alert created via template"
        },
        "alertSeverity": {
            "value":3
        },
        "isEnabled": {
            "value": true
        },
        "resourceId": {
            "value": "/subscriptions/1234-56789-1234-567a/resourceGroups/myRG/providers/Microsoft.OperationalInsights/workspaces/workspaceName"
        },
        "metricName":{
            "value": "Average_% Idle Time"
        },
        "operator": {
            "value": "GreaterThan"
        },
        "threshold":{
            "value": "1"
        },
        "timeAggregation":{
            "value": "Average"
        },
        "actionGroupId": {
            "value": "/subscriptions/1234-56789-1234-567a/resourceGroups/myRG/providers/microsoft.insights/actionGroups/actionGroupName"
        }
    }
}
```

Under förutsättning att ovanstående parameterfilen sparas som metricfromLogsAlertStatic.parameters.json; Skapa en måttavisering för loggar med [Resource-mall för att skapa i Azure-portalen](../../azure-resource-manager/resource-group-template-deploy-portal.md).

En kan också använda Azure Powershell-kommandot nedan samt:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "myRG" -TemplateFile metricfromLogsAlertStatic.json TemplateParameterFile metricfromLogsAlertStatic.parameters.json
```

Eller Använd distribuera resursmall med Azure CLI:

```CLI
az group deployment create --resource-group myRG --template-file metricfromLogsAlertStatic.json --parameters @metricfromLogsAlertStatic.parameters.json
```

### <a name="metric-alerts-for-logs-with-dynamic-thresholds"></a>Måttaviseringar för loggar med dynamiska tröskelvärden

Om du vill göra samma sak, kan en använda exemplet Azure Resource Manager-mall nedan – där skapandet av en metrisk varning med dynamiska tröskelvärden beror på regeln för att extrahera mått från loggar via scheduledQueryRule har skapats.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "convertRuleName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the rule to convert log to metric"
            }
        },
        "convertRuleDescription": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Description for log converted to metric"
            }
        },
        "convertRuleRegion": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the region used by workspace"
            }
        },
        "convertRuleStatus": {
            "type": "string",
            "defaultValue": "true",
            "metadata": {
                "description": "Specifies whether the log conversion rule is enabled"
            }
        },
        "convertRuleMetric": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the metric once extraction done from logs."
            }
        },
        "alertName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the alert"
            }
        },
        "alertDescription": {
            "type": "string",
            "defaultValue": "This is a metric alert",
            "metadata": {
                "description": "Description of alert"
            }
        },
        "alertSeverity": {
            "type": "int",
            "defaultValue": 3,
            "allowedValues": [
                0,
                1,
                2,
                3,
                4
            ],
            "metadata": {
                "description": "Severity of alert {0,1,2,3,4}"
            }
        },
        "isEnabled": {
            "type": "bool",
            "defaultValue": true,
            "metadata": {
                "description": "Specifies whether the alert is enabled"
            }
        },
        "resourceId": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Full Resource ID of the resource emitting the metric that will be used for the comparison. For example /subscriptions/00000000-0000-0000-0000-0000-00000000/resourceGroups/ResourceGroupName/providers/Microsoft.compute/virtualMachines/VM_xyz"
            }
        },
        "metricName": {
            "type": "string",
            "minLength": 1,
            "metadata": {
                "description": "Name of the metric used in the comparison to activate the alert."
            }
        },
        "operator": {
            "type": "string",
            "defaultValue": "GreaterOrLessThan",
            "allowedValues": [
                "GreaterThan",
                "LessThan",
                "GreaterOrLessThan"
            ],
            "metadata": {
                "description": "Operator comparing the current value with the threshold value."
            }
        },
        "alertSensitivity": {
            "type": "string",
            "defaultValue": "Medium",
            "allowedValues": [
                "High",
                "Medium",
                "Low"
            ],
            "metadata": {
                "description": "Tunes how 'noisy' the Dynamic Thresholds alerts will be: 'High' will result in more alerts while 'Low' will result in fewer alerts."
            }
        },
        "numberOfEvaluationPeriods": {
            "type": "string",
            "defaultValue": "4",
            "metadata": {
                "description": "The number of periods to check in the alert evaluation."
            }
        },
        "minFailingPeriodsToAlert": {
            "type": "string",
            "defaultValue": "3",
            "metadata": {
                "description": "The number of unhealthy periods to alert on (must be lower or equal to numberOfEvaluationPeriods)."
            }
        },
        "timeAggregation": {
            "type": "string",
            "defaultValue": "Average",
            "allowedValues": [
                "Average",
                "Minimum",
                "Maximum",
                "Total"
            ],
            "metadata": {
                "description": "How the data that is collected should be combined over time."
            }
        },
        "windowSize": {
            "type": "string",
            "defaultValue": "PT5M",
            "metadata": {
                "description": "Period of time used to monitor alert activity based on the threshold. Must be between five minutes and one day. ISO 8601 duration format."
            }
        },
        "evaluationFrequency": {
            "type": "string",
            "defaultValue": "PT1M",
            "metadata": {
                "description": "how often the metric alert is evaluated represented in ISO 8601 duration format"
            }
        },
        "actionGroupId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The ID of the action group that is triggered when the alert is activated or deactivated"
            }
        }
    },
    "variables": {
        "convertRuleTag": "hidden-link:/subscriptions/1234-56789-1234-567a/resourceGroups/resourceGroupName/providers/Microsoft.OperationalInsights/workspaces/workspaceName",
        "convertRuleSourceWorkspace": {
            "SourceId": "/subscriptions/1234-56789-1234-567a/resourceGroups/resourceGroupName/providers/Microsoft.OperationalInsights/workspaces/workspaceName"
        }
    },
    "resources": [
        {
            "name": "[parameters('convertRuleName')]",
            "type": "Microsoft.Insights/scheduledQueryRules",
            "apiVersion": "2018-04-16",
            "location": "[parameters('convertRuleRegion')]",
            "tags": {
                "[variables('convertRuleTag')]": "Resource"
            },
            "properties": {
                "description": "[parameters('convertRuleDescription')]",
                "enabled": "[parameters('convertRuleStatus')]",
                "source": {
                    "dataSourceId": "[variables('convertRuleSourceWorkspace').SourceId]"
                },
                "action": {
                    "odata.type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.Microsoft.AppInsights.Nexus.DataContracts.Resources.ScheduledQueryRules.LogToMetricAction",
                    "criteria": [{
                            "metricName": "[parameters('convertRuleMetric')]",
                            "dimensions": []
                        }
                    ]
                }
            }
        },
        {
            "name": "[parameters('alertName')]",
            "type": "Microsoft.Insights/metricAlerts",
            "location": "global",
            "apiVersion": "2018-03-01",
            "tags": {},
            "dependsOn":["[resourceId('Microsoft.Insights/scheduledQueryRules',parameters('convertRuleName'))]"],
            "properties": {
                "description": "[parameters('alertDescription')]",
                "severity": "[parameters('alertSeverity')]",
                "enabled": "[parameters('isEnabled')]",
                "scopes": ["[parameters('resourceId')]"],
                "evaluationFrequency":"[parameters('evaluationFrequency')]",
                "windowSize": "[parameters('windowSize')]",
                "criteria": {
                    "odata.type": "Microsoft.Azure.Monitor.MultipleResourceMultipleMetricCriteria",
                    "allOf": [
                        {
                            "criterionType": "DynamicThresholdCriterion",
                            "name" : "1st criterion",
                            "metricName": "[parameters('metricName')]",
                            "dimensions":[],
                            "operator": "[parameters('operator')]",
                            "alertSensitivity": "[parameters('alertSensitivity')]",
                            "failingPeriods": {
                                "numberOfEvaluationPeriods": "[parameters('numberOfEvaluationPeriods')]",
                                "minFailingPeriodsToAlert": "[parameters('minFailingPeriodsToAlert')]"
                            },
                            "timeAggregation": "[parameters('timeAggregation')]"
                        }
                    ]
                },
                "actions": [
                    {
                        "actionGroupId": "[parameters('actionGroupId')]"
                    }
                ]
            }
        }
    ]
}
```

Anta att ovanstående JSON sparas som metricfromLogsAlertDynamic.json - och du kan kombinera det med en JSON-fil som ska användas som parameter för att skapa en resurs mallbaserad. En parameter JSON-exempelfilen nedan:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "convertRuleName": {
            "value": "TestLogtoMetricRule"
        },
        "convertRuleDescription": {
            "value": "Test rule to extract metrics from logs via template"
        },
        "convertRuleRegion": {
            "value": "West Central US"
        },
        "convertRuleStatus": {
            "value": "true"
        },
        "convertRuleMetric": {
            "value": "Average_% Idle Time"
        },
        "alertName": {
            "value": "TestMetricAlertonLog"
        },
        "alertDescription": {
            "value": "New multi-dimensional metric alert created via template"
        },
        "alertSeverity": {
            "value":3
        },
        "isEnabled": {
            "value": true
        },
        "resourceId": {
            "value": "/subscriptions/1234-56789-1234-567a/resourceGroups/myRG/providers/Microsoft.OperationalInsights/workspaces/workspaceName"
        },
        "metricName":{
            "value": "Average_% Idle Time"
        },
        "operator": {
            "value": "GreaterOrLessThan"
          },
          "alertSensitivity": {
              "value": "Medium"
          },
          "numberOfEvaluationPeriods": {
              "value": "4"
          },
          "minFailingPeriodsToAlert": {
              "value": "3"
          },
        "timeAggregation":{
            "value": "Average"
        },
        "actionGroupId": {
            "value": "/subscriptions/1234-56789-1234-567a/resourceGroups/myRG/providers/microsoft.insights/actionGroups/actionGroupName"
        }
    }
}
```

Under förutsättning att ovanstående parameterfilen sparas som metricfromLogsAlertDynamic.parameters.json; Skapa en måttavisering för loggar med [Resource-mall för att skapa i Azure-portalen](../../azure-resource-manager/resource-group-template-deploy-portal.md).

En kan också använda Azure Powershell-kommandot nedan samt:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "myRG" -TemplateFile metricfromLogsAlertDynamic.json TemplateParameterFile metricfromLogsAlertDynamic.parameters.json
```

Eller Använd distribuera resursmall med Azure CLI:

```CLI
az group deployment create --resource-group myRG --template-file metricfromLogsAlertDynamic.json --parameters @metricfromLogsAlertDynamic.parameters.json
```

## <a name="next-steps"></a>Nästa steg

- Läs mer om den [måttaviseringar](alerts-metric.md).
- Lär dig mer om [loggaviseringar i Azure](../../azure-monitor/platform/alerts-unified-log.md).
- Lär dig mer om [aviseringar i Azure](alerts-overview.md).
