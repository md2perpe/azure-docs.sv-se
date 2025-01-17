---
title: Azure Key Vault-lösningen i Azure Monitor | Microsoft Docs
description: Du kan använda Azure Key Vault-lösningen i Azure Monitor för att granska Azure Key Vault-loggar.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: 5e25e6d6-dd20-4528-9820-6e2958a40dae
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: bwren
ms.openlocfilehash: 481b643f2f7201a2a1745c7aef9ddd81883da020
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60498533"
---
# <a name="azure-key-vault-analytics-solution-in-azure-monitor"></a>Azure Key Vault Analytics-lösning i Azure Monitor

![Key Vault-symbol](media/azure-key-vault/key-vault-analytics-symbol.png)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Du kan använda Azure Key Vault-lösningen i Azure Monitor för att granska Azure Key Vault AuditEvent-loggarna.

Om du vill använda lösningen måste du aktivera loggning av Azure Key Vault-diagnostik och dirigera diagnostik till en Log Analytics-arbetsyta. Du behöver inte skriva loggarna till Azure Blob storage.

> [!NOTE]
> I januari 2017 ändras sättet som stöds för att skicka loggar från Key Vault till Log Analytics. Om Key Vault-lösningen som du använder visar *(inaktuell)* rubriken, referera till [migrera från den gamla Key Vault-lösningen](#migrating-from-the-old-key-vault-solution) anvisningar om hur du ska följa.
>
>

## <a name="install-and-configure-the-solution"></a>Installera och konfigurera lösningen
Använd följande instruktioner för att installera och konfigurera Azure Key Vault-lösningen:

1. Använda processen som beskrivs i [lägga till Azure Monitor-lösningar från lösningsgalleriet](../../azure-monitor/insights/solutions.md) att lägga till Azure Key Vault-lösningen i Log Analytics-arbetsytan.
2. Aktivera diagnostikloggning för Key Vault-resurser för att övervaka, med hjälp av antingen den [portal](#enable-key-vault-diagnostics-in-the-portal) eller [PowerShell](#enable-key-vault-diagnostics-using-powershell)

### <a name="enable-key-vault-diagnostics-in-the-portal"></a>Aktivera Key Vault-diagnostik i portalen

1. I Azure-portalen går du till Key Vault-resursen för att övervaka
2. Välj *diagnostikinställningar* att öppna sidan

   ![Bild av Azure Key Vault-panel](media/azure-key-vault/log-analytics-keyvault-enable-diagnostics01.png)
3. Klicka på *slå på diagnostik* att öppna sidan

   ![Bild av Azure Key Vault-panel](media/azure-key-vault/log-analytics-keyvault-enable-diagnostics02.png)
4. Namnge den diagnostiska inställningen.
5. Klicka på kryssrutan för *skicka till Log Analytics*
6. Välj en befintlig Log Analytics-arbetsyta eller skapa en arbetsyta
7. Aktivera *AuditEvent* loggar, klickar du på kryssrutan under Log
8. Klicka på *spara* att aktivera loggning av diagnostik till Log Analytics-arbetsytan.

### <a name="enable-key-vault-diagnostics-using-powershell"></a>Aktivera diagnostik för Key Vault med hjälp av PowerShell
Följande PowerShell-skript innehåller ett exempel på hur du använder `Set-AzDiagnosticSetting` att aktivera Diagnostisk loggning för Key Vault:
```
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$kv = Get-AzKeyVault -VaultName 'ContosoKeyVault'

Set-AzDiagnosticSetting -ResourceId $kv.ResourceId  -WorkspaceId $workspaceId -Enabled $true
```



## <a name="review-azure-key-vault-data-collection-details"></a>Läs mer om Azure Key Vault data-samling
Azure Key Vault-lösningen samlar in diagnostikloggar direkt från Key Vault.
Det är inte nödvändigt att skriva loggar till Azure Blob storage och ingen agent krävs för insamling av data.

I följande tabell visas data samlingsmetoder och annan information om hur data samlas in för Azure Key Vault.

| Plattform | Direktagent | System Center Operations Manager-agenten | Azure | Operations Manager som krävs? | Operations Manager agent-data skickas via hanteringsgruppen | Insamlingsfrekvens |
| --- | --- | --- | --- | --- | --- | --- |
| Azure |  |  |&#8226; |  |  | anländer |

## <a name="use-azure-key-vault"></a>Använda Azure Key Vault
När du [installera lösningen för](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.KeyVaultAnalyticsOMS?tab=Overview), visa Key Vault-data genom att klicka på den **Key Vault-analys** panelen från Azure Monitor **översikt** sidan. Öppna den här sidan från den **Azure Monitor** menyn genom att klicka på **mer** under den **Insights** avsnittet. 

![Bild av Azure Key Vault-panel](media/azure-key-vault/log-analytics-keyvault-tile.png)

När du klickar på den **Key Vault-analys** panelen, du kan visa sammanfattningar av dina loggar och sedan kan du läsa informationen i följande kategorier:

* Mängden alla nyckelvalvsåtgärder över tid
* Det gick inte åtgärdsvolymer över tid
* Genomsnittlig svarstid för åtgärden
* Tjänstkvalitet för åtgärder med antalet åtgärder som tar mer än 1 000 ms och en lista över åtgärder som tar mer än 1 000 ms

![Bild av instrumentpanelen för Azure Key Vault](media/azure-key-vault/log-analytics-keyvault01.png)

![Bild av instrumentpanelen för Azure Key Vault](media/azure-key-vault/log-analytics-keyvault02.png)

### <a name="to-view-details-for-any-operation"></a>Att visa information om alla åtgärder
1. På den **översikt** klickar du på den **Key Vault-analys** panelen.
2. På den **Azure Key Vault** instrumentpanelen läser du sammanfattningsinformationen på något av bladen och klicka sedan på en om du vill visa detaljerad information om den på sidan log search.

    På någon av sidorna log search, kan du visa resultat genom tid, detaljerade resultat och sökhistoriken log. Du kan också filtrera efter fasetter att begränsa resultaten.

## <a name="azure-monitor-log-records"></a>Azure Monitor-loggposter
Azure Key Vault-lösningen analyserar poster som har en typ av **KeyVaults** som samlas in från [AuditEvent-loggarna](../../key-vault/key-vault-logging.md) i Azure-diagnostik.  Egenskaper för dessa poster finns i följande tabell:  

| Egenskap | Description |
|:--- |:--- |
| Typ |*AzureDiagnostics* |
| SourceSystem |*Azure* |
| CallerIpAddress |IP-adressen för klienten som gjorde begäran |
| Category | *AuditEvent* |
| CorrelationId |Ett valfritt GUID som klienten kan skicka för att korrelera loggar på klientsidan med loggar på tjänstsidan (Key Vault). |
| . durationMs |Hur lång tid i millisekunder som det tog att utföra REST-API-begäran. Nu omfattar inte Nätverksfördröjningen, så den tid du mäter på klientsidan inte kanske stämmer med den här gången. |
| httpStatusCode_d |HTTP-statuskod som returnerades av begäran (till exempel *200*) |
| id_s |Unikt ID för begäran |
| identity_claim_appid_g | GUID för program-ID |
| OperationName |Namnet på åtgärden, som beskrivs i [Azure Key Vault-loggning](../../key-vault/key-vault-logging.md) |
| OperationVersion |REST API-version som begärs av klienten (till exempel *2015-06-01*) |
| requestUri_s |URI för begäran |
| Resource |Namnet på nyckelvalvet |
| ResourceGroup |Resursgrupp för nyckelvalvet |
| ResourceId |Azure Resource Manager Resource-ID. För Key Vault-loggar är detta Key Vault-resurs-ID. |
| ResourceProvider |*MICROSOFT.KEYVAULT* |
| ResourceType | *VAULTS* |
| ResultSignature |HTTP-status (till exempel *OK*) |
| ResultType |Resultatet av REST API-begäran (till exempel *lyckades*) |
| SubscriptionId |Azure-prenumerations-ID för prenumerationen som innehåller Nyckelvalvet |

## <a name="migrating-from-the-old-key-vault-solution"></a>Migrera från den gamla Key Vault-lösningen
I januari 2017 ändras sättet som stöds för att skicka loggar från Key Vault till Log Analytics. Dessa ändringar ger följande fördelar:
+ Loggarna skrivs direkt till Log Analytics-arbetsytan utan att behöva använda ett lagringskonto
+ Mindre fördröjning från den tidpunkt när loggarna genereras till dem som det är tillgängligt i Log Analytics
+ Färre konfigurationssteg
+ Ett vanligt format för alla typer av Azure-diagnostik

Använda den uppdaterade lösningen:

1. [Konfigurera diagnostik skickas direkt till en Log Analytics-arbetsyta från Key Vault](#enable-key-vault-diagnostics-in-the-portal)  
2. Aktivera Azure Key Vault-lösningen med hjälp av metoden som beskrivs i [lägga till Azure Monitor-lösningar från lösningsgalleriet](../../azure-monitor/insights/solutions.md)
3. Uppdatera alla sparade frågor, instrumentpaneler eller aviseringar att använda den nya datatypen
   + Ändring från är: KeyVaults till AzureDiagnostics. Du kan använda resurstypens för att filtrera till Key Vault-loggar.
   + I stället för: `KeyVaults`, använda `AzureDiagnostics | where ResourceType'=="VAULTS"`
   + Fält: (Fältnamn är skiftlägeskänsliga)
   + För alla fält som har suffixet \_s, \_d, eller \_g i namnet, ändra det första tecknet till gemener
   + För alla fält som har suffixet \_o i namn data delas upp i enskilda fält baserat på de kapslade fältnamn. Till exempel är UPN-namnet för anroparen lagrad i ett fält `identity_claim_http_schemas_xmlsoap_org_ws_2005_05_identity_claims_upn_s`
   + Fältet CallerIpAddress ändras till CallerIPAddress
   + Fältet RemoteIPCountry finns inte längre
4. Ta bort den *Key Vault Analytics (inaktuell)* lösning. Om du använder PowerShell använder du `Set-AzureOperationalInsightsIntelligencePack -ResourceGroupName <resource group that the workspace is in> -WorkspaceName <name of the log analytics workspace> -IntelligencePackName "KeyVault" -Enabled $false`

Data som samlas in innan ändringen inte visas i den nya lösningen. Du kan fortsätta att fråga efter dessa data med hjälp av äldre typ och fältnamn.

## <a name="troubleshooting"></a>Felsökning
[!INCLUDE [log-analytics-troubleshoot-azure-diagnostics](../../../includes/log-analytics-troubleshoot-azure-diagnostics.md)]

## <a name="next-steps"></a>Nästa steg
* Använd [logga frågor i Azure Monitor](../../azure-monitor/log-query/log-query-overview.md) att visa detaljerade data i Azure Key Vault.
