---
title: Azure Service Bus-mått i Azure Monitor | Microsoft Docs
description: Använda Azure Monitor för att övervaka Service Bus-entiteter
services: service-bus-messaging
documentationcenter: .NET
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.topic: article
ms.date: 11/06/2018
ms.author: aschhab
ms.openlocfilehash: 80a4b1e60202b88f6ed3c1574bd4684575a9b153
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/03/2019
ms.locfileid: "67538062"
---
# <a name="azure-service-bus-metrics-in-azure-monitor"></a>Azure Service Bus-mått i Azure Monitor

Service Bus mått ger dig tillståndet för resurser i din Azure-prenumeration. Med en omfattande uppsättning mätvärden, kan du utvärdera den övergripande hälsan för din Service Bus-resurser, inte bara på namnområdesnivå, utan även på enhetsnivå. Statistiken kan vara viktigt eftersom de hjälper dig att övervaka status för Service Bus. Mått kan också hjälpa till att felsöka problem med grundorsaken utan att behöva kontakta Azure-supporten.

Azure Monitor innehåller enhetligt användargränssnitt för övervakning över olika Azure-tjänster. Mer information finns i [övervakning i Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview.md) och [hämta Azure Monitor-mått med .NET](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) i GitHub.

> [!IMPORTANT]
> När det inte har en interaktion med en entitet i 2 timmar, startas mått som visar ”0” som ett värde tills enheten inte längre är inaktiv.

## <a name="access-metrics"></a>Åtkomst-mått

Azure Monitor innehåller flera sätt att åtkomst mått. Du kan antingen åtkomst mätvärden via den [Azure-portalen](https://portal.azure.com), eller använda Azure Monitor-API: er (REST och .NET) och lösningar för dataanalys, till exempel Azure Monitor-loggar och Event Hubs. Mer information finns i [mått i Azure Monitor](../azure-monitor/platform/data-platform-metrics.md).

Mått är aktiverade som standard och du kan komma åt de senaste 30 dagarna data. Om du vill behålla data under en längre tid kan du arkivera måttdata till ett Azure Storage-konto. Det här värdet har konfigurerats i [diagnostikinställningar](../azure-monitor/platform/diagnostic-logs-overview.md#diagnostic-settings) i Azure Monitor.

## <a name="access-metrics-in-the-portal"></a>Åtkomst-mått i portal

Du kan övervaka mått med tiden i den [Azure-portalen](https://portal.azure.com). I följande exempel visas hur du visar lyckade begäranden och inkommande begäranden på kontonivå:

![][1]

Du kan också komma åt mått direkt via namnområdet. Ditt namnområde för att göra det, och klicka sedan på **mått**. Om du vill visa mått som filtrerats till i omfånget för entiteten, väljer du entitet och klicka sedan på **mått**.

![][2]

För mätvärden som stöd för dimensioner, måste du filtrera med önskad dimension-värde.

## <a name="billing"></a>Fakturering

Mått och aviseringar i Azure Monitor debiteras på basis av per avisering. Dessa kostnader ska vara tillgänglig på portalen när aviseringen har konfigurerats och innan den sparas. 

Ytterligare lösningar som mata in måttdata debiteras direkt av dessa lösningar. Exempelvis debiteras du per Azure Storage om du arkiverar måttdata till ett Azure Storage-konto. Du debiteras också av Log Analytics om du strömma mätvärden till Log Analytics för avancerad analys.

Följande mått ger dig en översikt över hälsotillståndet för din tjänst. 

> [!NOTE]
> Vi avvecklar flera mått när de flyttas under ett annat namn. Detta kräver att du uppdaterar dina referenser. Mått som markerats med nyckelordet ”inaktuell” stöds inte framöver.

Alla mått-värden skickas till Azure Monitor varje minut. Tidskornighet definierar tidsintervallet som mått visas. Tidsintervallet som stöds för alla Service Bus-mått är 1 minut.

## <a name="request-metrics"></a>Begäran-mått

Räknar antalet förfrågningar om data och hantering av åtgärder.

| Måttnamn | Beskrivning |
| ------------------- | ----------------- |
| Inkommande begäranden| Antalet begäranden som görs till Service Bus-tjänsten under en angiven period. <br/><br/> Enhet: Count <br/> Mängdtyp: Totalt <br/> Dimensioner: EntityName|
|Lyckade begäranden|Antal lyckade begäranden som görs till Service Bus-tjänsten under en angiven period.<br/><br/> Enhet: Antal <br/> Mängdtyp: Totalt <br/> Dimensioner: EntityName|
|Serverfel|Antal begäranden som inte bearbetas på grund av ett fel i Service Bus-tjänsten under en angiven period.<br/><br/> Enhet: Antal <br/> Mängdtyp: Totalt <br/> Dimensioner: EntityName|
|Användarfel (se följande avsnitt)|Antal begäranden som inte bearbetas på grund av användarfel under en angiven period.<br/><br/> Enhet: Count <br/> Mängdtyp: Totalt <br/> Dimensioner: EntityName|
|Begränsade begäranden|Antal begäranden som har begränsats eftersom användningen har överskridits.<br/><br/> Enhet: Count <br/> Mängdtyp: Totalt <br/> Dimensioner: EntityName|

### <a name="user-errors"></a>Användarfel

Följande två typer av fel är klassificerade som användarfel:

1. Fel på klientsidan (i HTTP som skulle vara 400 fel).
2. Fel som uppstår vid bearbetning av meddelanden, till exempel [MessageLockLostException](/dotnet/api/microsoft.azure.servicebus.messagelocklostexception).


## <a name="message-metrics"></a>Meddelande-mått

| Måttnamn | Beskrivning |
| ------------------- | ----------------- |
|Inkommande meddelanden|Antal händelser eller meddelanden som skickas till Service Bus under en angiven period.<br/><br/> Enhet: Count <br/> Mängdtyp: Totalt <br/> Dimensioner: EntityName|
|Utgående meddelanden|Antal händelser eller meddelanden som tas emot från Service Bus under en angiven period.<br/><br/> Enhet: Antal <br/> Mängdtyp: Totalt <br/> Dimensioner: EntityName|
| Meddelanden| Antal meddelanden i kö/ämne. <br/><br/> Enhet: Count <br/> Mängdtyp: Medel <br/> Dimensioner: EntityName |
| ActiveMessages| Antal aktiva meddelanden i kö/ämne. <br/><br/> Enhet: Count <br/> Mängdtyp: Medel <br/> Dimensioner: EntityName |
| Lettered förlorade meddelanden| Antal lettered förlorade meddelanden i kö/ämne. <br/><br/> Enhet: Antal <br/> Mängdtyp: Medel <br/>Dimensioner: EntityName |
| Schemalagda meddelanden| Antal schemalagda meddelanden i kö/ämne. <br/><br/> Enhet: Antal <br/> Mängdtyp: Medel  <br/> Dimensioner: EntityName |

## <a name="connection-metrics"></a>Anslutningsmått

| Måttnamn | Beskrivning |
| ------------------- | ----------------- |
|ActiveConnections|Antal aktiva anslutningar för ett namnområde och på en entitet.<br/><br/> Enhet: Antal <br/> Mängdtyp: Totalt <br/> Dimensioner: EntityName|

## <a name="resource-usage-metrics"></a>Användningsstatistik för resursen

> [!NOTE] 
> Följande mått är endast tillgängligt med den **premium** nivå. 

| Måttnamn | Beskrivning |
| ------------------- | ----------------- |
|Processoranvändning per namnområde|Procent CPU-användning av namnområdet.<br/><br/> Enhet: Procent <br/> Mängdtyp: Maximal <br/> Dimensioner: EntityName|
|Minnesstorleksanvändning per namnområde|Procentandel minnesanvändningen för namnområdet.<br/><br/> Enhet: Procent <br/> Mängdtyp: Maximal <br/> Dimensioner: EntityName|

## <a name="metrics-dimensions"></a>Mått dimensioner

Azure Service Bus stöder följande dimensioner för mått i Azure Monitor. Det är valfritt att lägga till dimensioner i dina mått. Om du inte lägger till dimensioner, har mått angetts på namnområdesnivå. 

|Dimensionsnamn|Beskrivning|
| ------------------- | ----------------- |
|EntityName| Service Bus stöder meddelandeentiteter under namnområdet.|

## <a name="set-up-alerts-on-metrics"></a>Konfigurera aviseringar i mått

1. På den **mått** fliken den **Service Bus Namespace** väljer **konfigurera aviseringar**. 

    ![Mått sidan – Konfigurera alerts-menyn](./media/service-bus-metrics-azure-monitor/metrics-page-configure-alerts-menu.png)
2. Välj den **Välj mål** alternativet och gör följande på den **väljer du en resurs** sidan: 
    1. Välj **Service Bus-namnområden** för den **filtrera efter resurstyp** fält. 
    2. Välj din prenumeration för den **filtrera efter prenumeration** fält.
    3. Välj den **service bus-namnområde** i listan. 
    4. Välj **Done** (Klar). 
    
        ![Välj namnområde](./media/service-bus-metrics-azure-monitor/select-namespace.png)
1. Välj **lägga till villkor**, och gör följande på den **konfigurera signallogiken** sidan:
    1. Välj **mått** för **signalera typ**. 
    2. Välj en signal. Exempel: **Tjänsten fel**. 

        ![Välj serverfel](./media/service-bus-metrics-azure-monitor/select-server-errors.png)
    1. Välj **är större än** för **villkor**.
    2. Välj **totala** för **tidsmängd**. 
    3. Ange **5** för **tröskelvärdet**. 
    4. Välj **Done** (Klar).    

        ![Ange villkor](./media/service-bus-metrics-azure-monitor/specify-condition.png)    
1. På den **skapa regeln** expanderar **definiera Aviseringsinformationen**, och göra följande:
    1. Ange en **namn** för aviseringen. 
    2. Ange en **beskrivning** för aviseringen.
    3. Välj **allvarlighetsgrad** för aviseringen. 

        ![Aviseringsinformation](./media/service-bus-metrics-azure-monitor/alert-details.png)
1. På den **skapa regeln** expanderar **definiera åtgärdsgruppen**väljer **ny åtgärdsgrupp**, och gör följande på den **gruppsidanförLäggtillåtgärd**. 
    1. Ange ett namn för åtgärdsgruppen.
    2. Ange ett kort namn för åtgärdsgruppen. 
    3. Välj din prenumeration. 
    4. Välj en resursgrupp. 
    5. Den här genomgången ska ange **skicka e-postmeddelande** för **ÅTGÄRDSNAMN**.
    6. Välj **e-post/SMS/Push/röst** för **ÅTGÄRDSTYP**. 
    7. Välj **redigera information**. 
    8. På den **e-post/SMS/Push/röst** gör du följande åtgärder:
        1. Välj **e-post**. 
        2. Skriv den **e-postadress**. 
        3. Välj **OK**.

            ![Aviseringsinformation](./media/service-bus-metrics-azure-monitor/add-action-group.png)
        4. På den **Lägg till åtgärdsgrupp** väljer **OK**. 
1. På den **skapa regeln** väljer **skapa varningsregel**. 

    ![Skapa aviseringsregel knapp](./media/service-bus-metrics-azure-monitor/create-alert-rule.png)

## <a name="next-steps"></a>Nästa steg

Se den [översikt över Azure Monitor](../monitoring-and-diagnostics/monitoring-overview.md).

[1]: ./media/service-bus-metrics-azure-monitor/service-bus-monitor1.png
[2]: ./media/service-bus-metrics-azure-monitor/service-bus-monitor2.png


