---
title: Mått i Azure Monitor - Azure Event Hubs | Microsoft Docs
description: Den här artikeln innehåller information om hur du använder Azure-övervakning för att övervaka Azure Event Hubs
services: event-hubs
documentationcenter: .NET
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: b5df69e9670c01b576afe242b39532acb1e1c526
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60685910"
---
# <a name="azure-event-hubs-metrics-in-azure-monitor"></a>Azure Event Hubs-mått i Azure Monitor

Event Hubs mått ger dig tillståndet för Event Hubs-resurser i Azure-prenumerationen. Med en omfattande uppsättning mätvärden, kan du utvärdera den övergripande hälsan för händelsehubbar inte bara på namnområdesnivå, utan även på enhetsnivå. Statistiken kan vara viktigt eftersom de hjälper dig att övervaka status för dina event hubs. Mått kan också hjälpa till att felsöka problem med grundorsaken utan att behöva kontakta Azure-supporten.

Azure Monitor innehåller enhetligt användargränssnitt för övervakning över olika Azure-tjänster. Mer information finns i [övervakning i Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview.md) och [hämta Azure Monitor-mått med .NET](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) i GitHub.

## <a name="access-metrics"></a>Åtkomst-mått

Azure Monitor innehåller flera sätt att åtkomst mått. Du kan antingen åtkomst mätvärden via den [Azure-portalen](https://portal.azure.com), eller använda Azure Monitor-API: er (REST och .NET) och lösningar för dataanalys, till exempel Operations Management Suite och Event Hubs. Mer information finns i [övervakningsdata som samlas in av Azure Monitor](../azure-monitor/platform/data-platform.md).

Mått är aktiverade som standard och du kan komma åt de senaste 30 dagarna data. Om du vill behålla data under en längre tid kan du arkivera måttdata till ett Azure Storage-konto. Detta är konfigurerat i [diagnostikinställningar](../azure-monitor/platform/diagnostic-logs-overview.md#diagnostic-settings) i Azure Monitor.

## <a name="access-metrics-in-the-portal"></a>Åtkomst-mått i portal

Du kan övervaka mått med tiden i den [Azure-portalen](https://portal.azure.com). I följande exempel visas hur du visar lyckade begäranden och inkommande begäranden på kontonivå:

![Visa lyckad mått][1]

Du kan också komma åt mått direkt via namnområdet. Ditt namnområde för att göra det, och klicka sedan på **mått**. Välj event hub för att visa mått som filtrerats till i omfånget för event hub, och klicka sedan på **mått**.

För mätvärden som stöd för dimensioner, måste du filtrera med önskad dimension-värde som visas i följande exempel:

![Filtrera med dimensionsvärde][2]

## <a name="billing"></a>Fakturering

Med hjälp av mätvärden i Azure Monitor är för närvarande kostnadsfritt. Om du använder ytterligare lösningar som mata in måttdata kan debiteras du dock av dessa lösningar. Exempelvis debiteras du per Azure Storage om du arkiverar måttdata till ett Azure Storage-konto. Du debiteras också av Azure om du strömma måttdata till Azure Monitor-loggar för avancerad analys.

Följande mått ger dig en översikt över hälsotillståndet för din tjänst. 

> [!NOTE]
> Vi avvecklar flera mått när de flyttas under ett annat namn. Detta kräver att du uppdaterar dina referenser. Mått som markerats med nyckelordet ”inaktuell” stöds inte framöver.

Alla mått-värden skickas till Azure Monitor varje minut. Tidskornighet definierar tidsintervallet som mått visas. Tidsintervallet som stöds för alla Event Hubs-mått är 1 minut.

## <a name="request-metrics"></a>Begäran-mått

Räknar antalet förfrågningar om data och hantering av åtgärder.

| Måttnamn | Beskrivning |
| ------------------- | ----------------- |
| Inkommande förfrågningar (förhandsversion) | Antal begäranden som görs till Azure Event Hubs-tjänsten under en angiven period. <br/><br/> Enhet: Count <br/> Mängdtyp: Totalt <br/> Dimensioner: EntityName |
| Lyckade begäranden (förhandsversion)   | Antal lyckade begäranden som görs till Azure Event Hubs-tjänsten under en angiven period. <br/><br/> Enhet: Count <br/> Mängdtyp: Totalt <br/> Dimensioner: EntityName |
| Fel (förhandsversion) | Antal begäranden som inte bearbetas på grund av ett fel i tjänsten Azure Event Hubs under en angiven period. <br/><br/>Enhet: Count <br/> Mängdtyp: Totalt <br/> Dimensioner: EntityName |
|Användarfel (förhandsversion)|Antal begäranden som inte bearbetas på grund av användarfel under en angiven period.<br/><br/> Enhet: Count <br/> Mängdtyp: Totalt <br/> Dimensioner: EntityName|
|Kvoten har överskridits-fel (förhandsversion)|Antalet begäranden överskred tillgänglig kvot. Se [i den här artikeln](event-hubs-quotas.md) för mer information om Event Hubs-kvoter.<br/><br/> Enhet: Count <br/> Mängdtyp: Totalt <br/> Dimensioner: EntityName|

## <a name="throughput-metrics"></a>Mått för dataflöde

| Måttnamn | Beskrivning |
| ------------------- | ----------------- |
|Begränsade begäranden (förhandsversion)|Antal begäranden som har begränsats eftersom enhet datadataflödesanvändning överskreds.<br/><br/> Enhet: Count <br/> Mängdtyp: Totalt <br/> Dimensioner: EntityName|

## <a name="message-metrics"></a>Meddelande-mått

| Måttnamn | Beskrivning |
| ------------------- | ----------------- |
|Inkommande meddelanden (förhandsversion)|Antal händelser eller meddelanden som skickas till Event Hubs under en angiven period.<br/><br/> Enhet: Count <br/> Mängdtyp: Totalt <br/> Dimensioner: EntityName|
|Utgående meddelanden (förhandsversion)|Antal händelser eller meddelanden hämtas från Event Hubs under en angiven period.<br/><br/> Enhet: Count <br/> Mängdtyp: Totalt <br/> Dimensioner: EntityName|
|Inkommande byte (förhandsversion)|Antal byte som skickas till Azure Event Hubs-tjänsten under en angiven period.<br/><br/> Enhet: Byte <br/> Mängdtyp: Totalt <br/> Dimensioner: EntityName|
|Utgående byte (förhandsversion)|Antalet byte som hämtas från Azure Event Hubs-tjänsten under en angiven period.<br/><br/> Enhet: Byte <br/> Mängdtyp: Totalt <br/> Dimensioner: EntityName|

## <a name="connection-metrics"></a>Anslutningsmått

| Måttnamn | Beskrivning |
| ------------------- | ----------------- |
|ActiveConnections (förhandsversion)|Antal aktiva anslutningar för ett namnområde och på en entitet.<br/><br/> Enhet: Antal <br/> Mängdtyp: Totalt <br/> Dimensioner: EntityName|
|Öppnade anslutningar (förhandsversion)|Antal öppna anslutningar.<br/><br/> Enhet: Count <br/> Mängdtyp: Totalt <br/> Dimensioner: EntityName|
|Anslutningar stängd (förhandsversion)|Antal stängda anslutningar.<br/><br/> Enhet: Count <br/> Mängdtyp: Totalt <br/> Dimensioner: EntityName|

## <a name="event-hubs-capture-metrics"></a>Event Hubs Capture mått

Du kan övervaka Event Hubs Capture mått när du aktiverar avbildningsfunktionen för händelsehubbar. Följande mått beskrivs vad du kan övervaka med avbildningsfunktionen aktiverad.

| Måttnamn | Beskrivning |
| ------------------- | ----------------- |
|Hämta kvarvarande uppgifter (förhandsversion)|Antal byte som ännu samlas in till det valda målet.<br/><br/> Enhet: Byte <br/> Mängdtyp: Totalt <br/> Dimensioner: EntityName|
|Hämtade meddelanden (förhandsversion)|Antal meddelanden eller händelser som har hämtats till det valda målet under en angiven period.<br/><br/> Enhet: Count <br/> Mängdtyp: Totalt <br/> Dimensioner: EntityName|
|Hämtade byte (förhandsversion)|Antal byte som har hämtats till det valda målet under en angiven period.<br/><br/> Enhet: Byte <br/> Mängdtyp: Totalt <br/> Dimensioner: EntityName|

## <a name="metrics-dimensions"></a>Mått dimensioner

Azure Event Hubs stöder följande dimensioner för mått i Azure Monitor. Det är valfritt att lägga till dimensioner i dina mått. Om du inte lägger till dimensioner, har mått angetts på namnområdesnivå. 

| Måttnamn | Beskrivning |
| ------------------- | ----------------- |
|EntityName| Händelsehubbar har stöd för event hub-enheter i namnområdet.|

## <a name="next-steps"></a>Nästa steg

* Se den [Azure övervakning-översikt](../monitoring-and-diagnostics/monitoring-overview.md).
* [Hämta Azure Monitor-mått med .NET](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) i GitHub. 

Besök följande länkar för mer utförlig information om Event Hubs:

* Kom igång med en [kurs om händelsehubbar](event-hubs-dotnet-standard-getstarted-send.md)
* [Vanliga frågor och svar om Event Hubs](event-hubs-faq.md)
* [Exempelprogram som använder Event Hubs](https://github.com/Azure/azure-event-hubs/tree/master/samples)

[1]: ./media/event-hubs-metrics-azure-monitor/event-hubs-monitor1.png
[2]: ./media/event-hubs-metrics-azure-monitor/event-hubs-monitor2.png



