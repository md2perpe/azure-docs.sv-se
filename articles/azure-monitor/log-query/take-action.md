---
title: Användarinitierad Azure Automation Runbook-åtgärden i Log Analytics | Microsoft Docs
description: Den här artikeln beskriver hur du kör en Automation-runbook från en Log Analytics search resultat på begäran.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: log-analytics
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/06/2019
ms.author: magoedte
ms.openlocfilehash: 9194d5fe6553607ac5a0bb4e133da97f53790984
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61424762"
---
# <a name="take-action-with-an-automation-runbook-from-a-log-analytics-log-search-result"></a>Vidta åtgärder med en Automation-Runbook från en Log Analytics log search-resultat

> [!NOTE]
> Starta en runbook från sökresultaten är en funktion i den klassiska portalen för Log Search som upphör att gälla den 15 februari 2019. Du kan konfigurera en åtgärdsgrupp som kan starta en runbook utöver andra åtgärder från en [varningsregel](../platform/alerts-log.md) i Azure Monitor.

Från en log sökresultat i Azure Log Analytics kan du nu välja **vidta åtgärder** att köra en Automation-runbook.  Runbook kan användas för att åtgärda problemet eller vidta andra åtgärder, till exempel som samlar in information om felsökning finns skicka ett e-postmeddelande eller skapar en tjänstbegäran. 


## <a name="components-and-features-used"></a>Komponenter och funktioner som används
* [Azure Automation-konto](../../automation/automation-quickstart-create-account.md)
* [Log Analytics-arbetsyta](../../azure-monitor/log-query/log-query-overview.md)

## <a name="to-initiate-runbook-from-log-search"></a>Att starta runbook från loggsökning

Om du vill vidta åtgärder för en händelse och starta en runbook från sökresultaten log, börja med att skapa en loggsökning och från resultat som du kan anropa en runbook på begäran. Du kan göra detta från den klassiska log sökfunktionen i den [Azure-portalen](../../azure-monitor/log-query/log-query-overview.md). I det här exemplet utför vi en loggsökning från Azure-portalen med en grundläggande demonstration av den här funktionen.

1. I Azure-portalen klickar du på **alla tjänster** och välj **Log Analytics**.  
2. Välj Log Analytics-arbetsytan.
3. På arbetsytan, Välj **loggar (klassisk)** .  
4. På sidan Log Search utför en loggsökning.  
5. Från loggsökningen, klickar du på ellipsen till vänster om något av fälten och från popup-fönstret väljer **vidta åtgärder för**.<br><br> ![Välj vidta åtgärder från sökresultat](./media/take-action/log-search-takeaction-menuoption.png) 
6. Välj **kör en runbook** och välj en runbook ska köras.  Du kan välja valfri runbook i Automation-kontot som är länkad till Log Analytics-arbetsytan.  Observera följande:

    * Runbooks är ordnade efter taggar
    * Runbook-indataparameter med värden kan väljas direkt från fälten i sökresultatet.  En nedrullningsbar listruta öppnas och visar alla tillgängliga fält från resultatet för urvalet.  
    * Du kan välja att köra runbook en [hybrid runbook worker](../../automation/automation-hybrid-runbook-worker.md) att du har installerat på den dator som har problemet om du har en motsvarande Hybrid Runbook Worker-grupp som endast innehåller den här datorn som medlem.  Om namnet på den Hybrid Worker-gruppen matchar namnet på den dator som är i resultatet, väljs gruppen automatiskt.    

6. När du klickar på **kör**, öppnas sidan för runbook-jobbet så att du kan granska status för jobbet.   

Om du väljer en runbook som har konfigurerats för att vara [anropas från en Log Analytics-aviseringen](../../automation/automation-create-alert-triggered-runbook.md), den har en indataparameter med namnet **WebhookData** dvs **objekt** typen.  Om parametern är obligatorisk, måste du skicka sökresultaten till runbooken så att den kan konvertera den JSON-formaterade strängen till en objekttyp som gör att du kan filtrera efter specifika objekt som du hänvisar till i runbook-aktiviteter.  Det gör du genom att välja **sökresultat (objekt)** från den nedrullningsbara listan.<br><br> ![Välj Webhook-dataobjekt för runbook-parameter](media/take-action/select-runbook-and-properties.png)   
    
## <a name="next-steps"></a>Nästa steg

* Granska den [Log Analytics logga sökreferens](../../azure-monitor/log-query/log-query-overview.md) att visa alla sökfält och aspekter som är tillgängliga i Log Analytics.
* Information om hur du anropar en Automation-runbook automatiskt, granska [anropa en Azure Automation-runbook från en Log Analytics-aviseringen](../../automation/automation-create-alert-triggered-runbook.md).  
