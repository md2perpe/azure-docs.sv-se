---
title: Aktivera Azure Monitor (förhandsversion) för en hybridmiljö | Microsoft Docs
description: Den här artikeln beskrivs hur du aktiverar Azure Monitor för virtuella datorer för en hybridmolnmiljö som innehåller en eller flera virtuella datorer.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/07/2019
ms.author: magoedte
ms.openlocfilehash: bc26cc0654aac9416bf31ffccf426648e3a8b8d2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67122555"
---
# <a name="enable-azure-monitor-for-vms-preview-for-a-hybrid-environment"></a>Aktivera Azure Monitor för virtuella datorer (förhandsversion) för en hybridmiljö

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Den här artikeln förklarar hur du aktiverar Azure Monitor för virtuella datorer (förhandsversion) för virtuella datorer eller fysiska datorer i ditt datacenter eller andra moln. I slutet av den här processen du kommer har börjat övervakning av dina virtuella datorer i din miljö och lär dig om de har problem prestanda eller tillgänglighet. 

Innan du börjar bör du granska den [krav](vminsights-enable-overview.md) och kontrollera att din prenumeration och resurser som uppfyller kraven. Granska kraven och distributionsmetoder för den [Log Analytics Linux och Windows-agenten](../../log-analytics/log-analytics-agent-overview.md).

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

>[!NOTE]
>I Azure Monitor för virtuella datorer kartan beroendeagenten överföra inte några data själva och det kräver inte ändringar i brandväggar eller portar. Kartdata överförs alltid genom Log Analytics-agenten i Azure Monitor-tjänsten, antingen direkt eller via den [Operations Management Suite-gateway](../../azure-monitor/platform/gateway.md) om din IT-säkerhetsprinciper inte tillåter datorer i nätverket ansluta till internet.

Stegen för att slutföra den här uppgiften kan sammanfattas på följande sätt:

1. Installera Log Analytics-agenten för Windows eller Linux. Innan du installerar agenten, granska de [översikt över Log Analytics-agenten](../platform/log-analytics-agent.md) artikeln om du vill förstå systemkraven och metoder för distribution.

2. Ladda ned och installera Azure Monitor för virtuella datorer kartan beroendeagenten för [Windows](https://aka.ms/dependencyagentwindows) eller [Linux](https://aka.ms/dependencyagentlinux).

3. Aktivera insamling av prestandaräknare.

4. Distribuera Azure Monitor för virtuella datorer.

## <a name="install-the-dependency-agent-on-windows"></a>Installera beroendeagenten på Windows
Du kan installera beroendeagenten manuellt på Windows-datorer genom att köra `InstallDependencyAgent-Windows.exe`. Om du kör den här körbara filen utan några alternativ, startar en installationsguide som du kan följa för att installera agenten interaktivt.

>[!NOTE]
>*Administratören* behörighet krävs för att installera eller avinstallera agenten.

Följande tabell visar de parametrar som stöds av installationsprogrammet för agenten från kommandoraden.

| Parameter | Beskrivning |
|:--|:--|
| /? | Returnerar en lista över kommandoradsalternativ. |
| / S | Utför en tyst installation utan interaktion från användaren. |

Till exempel för att köra installationsprogrammet med det `/?` parameter, ange **InstallDependencyAgent Windows.exe /?** .

Filer för Windows beroendeagenten installeras i *C:\Program Files\Microsoft Beroendeagenten* som standard. Om beroendeagenten inte startar när installationen har slutförts, kontrollera loggarna för Detaljerad felinformation. Loggkatalogen är *%Programfiles%\Microsoft beroende Agent\logs*.

## <a name="install-the-dependency-agent-on-linux"></a>Installera beroendeagenten på Linux
Beroende-agenten är installerad på Linux-servrar från *InstallDependencyAgent Linux64.bin*, ett kommandoskript med en självextraherande binärfilen. Du kan köra filen med hjälp av `sh` eller Lägg till körbehörighet till själva filen.

>[!NOTE]
> Du måste ha rotbehörighet för att kunna installera eller konfigurera agenten.
>

| Parameter | Beskrivning |
|:--|:--|
| -hjälp | Hämta en lista med kommandoradsalternativ. |
| -s | Utför en tyst installation utan någon användarprompter. |
| – Kontrollera | Kontrollera behörigheter och operativsystemet, men inte installera agenten. |

Till exempel för att köra installationsprogrammet med det `-help` parameter, ange **InstallDependencyAgent Linux64.bin-hjälpa**.

Installera beroendeagenten för Linux som rot genom att köra kommandot `sh InstallDependencyAgent-Linux64.bin`.

Om det inte går att starta beroendeagenten, kontrollera loggarna för Detaljerad felinformation. På Linux-agenter till loggkatalogen är */var/opt/microsoft/dependency-agent/log*.

Filer för beroendeagenten placeras i följande kataloger:

| Filer | Plats |
|:--|:--|
| Kärnfiler | /opt/microsoft/dependency-agent |
| Loggfiler | /var/opt/microsoft/dependency-agent/log |
| Konfigurationsfiler | /etc/opt/microsoft/dependency-agent/config |
| Körbara tjänstfiler | /opt/microsoft/dependency-agent/bin/microsoft-dependency-agent<br>/opt/microsoft/dependency-agent/bin/microsoft-dependency-agent-manager |
| Binära lagringsfiler | /var/opt/microsoft/dependency-agent/storage |

## <a name="enable-performance-counters"></a>Aktivera prestandaräknare
Om Log Analytics-arbetsytan som refereras av lösningen inte redan har konfigurerats för att samla in prestandaräknare som krävs av lösningen, måste du aktivera dem. Du kan göra det på något av två sätt:
* Manuellt, enligt beskrivningen i [Windows och Linux prestanda datakällor i Log Analytics](../../azure-monitor/platform/data-sources-performance-counters.md)
* Genom att hämta och köra ett PowerShell.skript som är tillgänglig från den [Azure PowerShell-galleriet](https://www.powershellgallery.com/packages/Enable-VMInsightsPerfCounters/1.1)

## <a name="deploy-azure-monitor-for-vms"></a>Distribuera Azure Monitor för virtuella datorer
Den här metoden innehåller en JSON-mall som anger konfigurationen för att aktivera komponenterna för lösningen i Log Analytics-arbetsytan.

Om du inte vet hur du distribuerar resurser med hjälp av en mall, se:
* [Distribuera resurser med Resource Manager-mallar och Azure PowerShell](../../azure-resource-manager/resource-group-template-deploy.md)
* [Distribuera resurser med Resource Manager-mallar och Azure CLI](../../azure-resource-manager/resource-group-template-deploy-cli.md)

Om du vill använda Azure CLI, måste du först installera och använda CLI lokalt. Du måste köra Azure CLI version 2.0.27 eller senare. För att identifiera din version, kör `az --version`. Om du vill installera eller uppgradera Azure CLI, se [installera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

### <a name="create-and-execute-a-template"></a>Skapa och köra en mall

1. Kopiera och klistra in följande JSON-syntax i filen:

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "WorkspaceName": {
                "type": "string"
            },
            "WorkspaceLocation": {
                "type": "string"
            }
        },
        "resources": [
            {
                "apiVersion": "2017-03-15-preview",
                "type": "Microsoft.OperationalInsights/workspaces",
                "name": "[parameters('WorkspaceName')]",
                "location": "[parameters('WorkspaceLocation')]",
                "resources": [
                    {
                        "apiVersion": "2015-11-01-preview",
                        "location": "[parameters('WorkspaceLocation')]",
                        "name": "[concat('ServiceMap', '(', parameters('WorkspaceName'),')')]",
                        "type": "Microsoft.OperationsManagement/solutions",
                        "dependsOn": [
                            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        ],
                        "properties": {
                            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        },

                        "plan": {
                            "name": "[concat('ServiceMap', '(', parameters('WorkspaceName'),')')]",
                            "publisher": "Microsoft",
                            "product": "[Concat('OMSGallery/', 'ServiceMap')]",
                            "promotionCode": ""
                        }
                    },
                    {
                        "apiVersion": "2015-11-01-preview",
                        "location": "[parameters('WorkspaceLocation')]",
                        "name": "[concat('InfrastructureInsights', '(', parameters('WorkspaceName'),')')]",
                        "type": "Microsoft.OperationsManagement/solutions",
                        "dependsOn": [
                            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        ],
                        "properties": {
                            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('WorkspaceName'))]"
                        },
                        "plan": {
                            "name": "[concat('InfrastructureInsights', '(', parameters('WorkspaceName'),')')]",
                            "publisher": "Microsoft",
                            "product": "[Concat('OMSGallery/', 'InfrastructureInsights')]",
                            "promotionCode": ""
                        }
                    }
                ]
            }
        ]
    }
    ```

1. Spara filen som *installsolutionsforvminsights.json* till en lokal mapp.

1. Samla in värden för *WorkspaceName*, *ResourceGroupName*, och *WorkspaceLocation*. Värdet för *WorkspaceName* är namnet på Log Analytics-arbetsytan. Värdet för *WorkspaceLocation* är den region som arbetsytan är definierad i.

1. Är du redo att distribuera den här mallen med hjälp av följande PowerShell-kommando:

    ```powershell
    New-AzResourceGroupDeployment -Name DeploySolutions -TemplateFile InstallSolutionsForVMInsights.json -ResourceGroupName ResourceGroupName> -WorkspaceName <WorkspaceName> -WorkspaceLocation <WorkspaceLocation - example: eastus>
    ```

    Konfigurationsändringen kan ta några minuter att slutföra. När det är klart visas ett meddelande som liknar följande och som innehåller resultatet:

    ```powershell
    provisioningState       : Succeeded
    ```
   När du har aktiverat övervakning, kan det ta ungefär 10 minuter innan du kan visa hälsotillstånd och mått för hybrid-dator.

## <a name="next-steps"></a>Nästa steg

Nu när övervakning har aktiverats för dina virtuella datorer, är den här informationen tillgänglig för analys med Azure Monitor för virtuella datorer.
 
- Läs hur du använder funktionen hälsotillstånd i [visa Azure Monitor för virtuella datorer health](vminsights-health.md).
- Identifierade programberoenden finns [visa Azure Monitor för virtuella datorer kartan](vminsights-maps.md).
- För att identifiera flaskhalsar och totala användningen med den Virtuella datorns prestanda, se [visa Azure VM prestanda](vminsights-performance.md).
- Identifierade programberoenden finns [visa Azure Monitor för virtuella datorer kartan](vminsights-maps.md).