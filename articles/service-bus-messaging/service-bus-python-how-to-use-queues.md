---
title: Hur du använder Azure Service Bus-köer med Python | Microsoft Docs
description: Lär dig hur du använder Azure Service Bus-köer från Python.
services: service-bus-messaging
documentationcenter: python
author: axisc
manager: timlt
editor: spelluru
ms.assetid: b95ee5cd-3b31-459c-a7f3-cf8bcf77858b
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 04/10/2019
ms.author: aschhab
ms.openlocfilehash: b74238ee49fe0d96d218f1800a33a9d60badc6d5
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/24/2019
ms.locfileid: "67341697"
---
# <a name="how-to-use-service-bus-queues-with-python"></a>Hur du använder Service Bus-köer med Python

[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

I de här självstudierna lär du dig att skapa Python-program för att skicka meddelanden till och ta emot meddelanden från en Service Bus-kö. 

## <a name="prerequisites"></a>Nödvändiga komponenter
1. En Azure-prenumeration. Du behöver ett Azure-konto för att slutföra den här självstudien. Du kan aktivera din [MSDN-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) eller registrera dig för en [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
2. Följ stegen i den [Använd Azure portal för att skapa en Service Bus-kö](service-bus-quickstart-portal.md) artikeln.
    1. Läsa snabbstartsidan **översikt** i Service Bus **köer**. 
    2. Skapa ett Service Bus **namnområde**. 
    3. Hämta den **anslutningssträngen**. 

        > [!NOTE]
        > Skapar du en **kö** i Service Bus-namnområde med hjälp av Python i den här självstudien. 
1. Installera Python eller [Python Azure Service Bus paket][Python Azure Service Bus package], finns i den [Python installationsguide](../python-how-to-install.md). Se fullständig dokumentation för Service Bus Python SDK [här](/python/api/overview/azure/servicebus?view=azure-python).

## <a name="create-a-queue"></a>Skapa en kö
Den **ServiceBusClient** objekt kan du arbeta med köer. Lägg till följande kod i den övre delen av alla Python-fil som du vill komma åt Service Bus via programmering:

```python
from azure.servicebus import ServiceBusClient
```

Följande kod skapar en **ServiceBusClient** objekt. Ersätt `<CONNECTION STRING>` med servicebus-connectionstring.

```python
sb_client = ServiceBusClient.from_connection_string('<CONNECTION STRING>')
```

Värdena för SAS-nyckelnamnet och värdet finns i den [Azure-portalen][Azure portal] anslutningsinformation, eller i Visual Studio **egenskaper** fönstret när du väljer Service Bus-namnområde i Server Explorer (som visas i föregående avsnitt).

```python
sb_client.create_queue("taskqueue")
```

Den `create_queue` metoden har stöd för ytterligare alternativ som gör att du kan åsidosätta standardinställningarna för kön som meddelandet tiden till live (TTL) eller största köstorlek också. I följande exempel anger den största köstorleken till 5 GB och TTL-värdet till 1 minut:

```python
sb_client.create_queue("taskqueue", max_size_in_megabytes=5120, default_message_time_to_live=datetime.timedelta(minutes=1))
```

Mer information finns i [dokumentation om Azure Service Bus Python](/python/api/overview/azure/servicebus?view=azure-python).

## <a name="send-messages-to-a-queue"></a>Skicka meddelanden till en kö
Att skicka ett meddelande till en Service Bus-kö, program-anrop i `send` metoden på den `ServiceBusClient` objekt.

I följande exempel visar hur du skickar ett testmeddelande till kön med namnet `taskqueue` med `send_queue_message`:

```python
from azure.servicebus import QueueClient, Message

# Create the QueueClient 
queue_client = QueueClient.from_connection_string("<CONNECTION STRING>", "<QUEUE NAME>")

# Send a test message to the queue
msg = Message(b'Test Message')
queue_client.send(msg)
```

Service Bus-köerna stöder en maximal meddelandestorlek på 256 kB på [standardnivån](service-bus-premium-messaging.md) och 1 MB på [premiumnivån](service-bus-premium-messaging.md). Rubriken, som inkluderar standardprogramegenskaperna och de anpassade programegenskaperna, kan ha en maximal storlek på 64 kB. Det finns ingen gräns för antalet meddelanden som kan finnas i en kö men det finns ett tak för den totala storleken för de meddelanden som ligger i en kö. Den här köstorleken definieras när kön skapas, med en övre gräns på 5 GB. Mer information om kvoter finns i [Service Bus-kvoter][Service Bus quotas].

Mer information finns i [dokumentation om Azure Service Bus Python](/python/api/overview/azure/servicebus?view=azure-python).

## <a name="receive-messages-from-a-queue"></a>Ta emot meddelanden från en kö
Meddelanden tas emot från en kö med hjälp av den `get_receiver` metoden på den `ServiceBusService` objekt:

```python
from azure.servicebus import QueueClient, Message

# Create the QueueClient 
queue_client = QueueClient.from_connection_string("<CONNECTION STRING>", "<QUEUE NAME>")

## Receive the message from the queue
with queue_client.get_receiver() as queue_receiver:
    messages = queue_receiver.fetch_next(timeout=3)
    for message in messages:
        print(message)
        message.complete()
```

Mer information finns i [dokumentation om Azure Service Bus Python](/python/api/overview/azure/servicebus?view=azure-python).


Meddelanden som tas bort från kön som de är skrivskyddade när parametern `peek_lock` är inställd på **FALSKT**. Du kan läsa (peek) och låsa meddelandet utan att ta bort det från kön genom att ange parametern `peek_lock` till **SANT**.

Beteende för att läsa och radera meddelandet som en del av åtgärden ta emot är den enklaste modellen och fungerar bäst för scenarier där ett program kan tolerera icke-bearbetning av ett meddelande om ett fel inträffar. För att förstå detta kan du föreställa dig ett scenario där konsumenten utfärdar en receive-begäran och sedan kraschar innan den kan bearbeta denna begäran. Eftersom Service Bus kommer att ha markerat meddelandet som Förbrukat, att sedan när programmet startas om och börjar förbruka meddelanden igen, ha missat meddelandet som förbrukades innan kraschen.

Om den `peek_lock` parametern är inställd på **SANT**, ta emot blir en åtgärd i två steg, vilket gör det möjligt att stödprogram som inte tolererar att saknas. När Service Bus tar emot en begäran letar det upp nästa meddelande som ska förbrukas, låser det för att förhindra att andra användare tar emot det och skickar sedan tillbaka det till programmet. När programmet har slutfört behandlingen av meddelandet (eller lagrar den på ett tillförlitligt sätt för framtida bearbetning), den är klar det andra steget i processen genom att anropa den **ta bort** metoden på den **meddelande** objekt. Den **ta bort** metoden kommer Markera meddelandet som Förbrukat och ta bort det från kön.

```python
msg.delete()
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Hantera programkrascher och oläsbara meddelanden
Service Bus innehåller funktioner som hjälper dig att återställa fel i programmet eller lösa problem med bearbetning av meddelanden på ett snyggt sätt. Om ett mottagarprogram är det går inte att bearbeta meddelandet av någon anledning så kan det anropa den **låsa upp** metoden på den **meddelande** objekt. Detta gör att Service Bus låser upp meddelandet i kön och gör det tillgängligt att tas emot igen, antingen genom samma användningsprogram eller ett annat användningsprogram.

Det finns också en tidsgräns som är associerade med ett meddelande som ligger låst i kön. Om programmet inte kan bearbeta meddelandet innan timeout för lås går ut (till exempel om programmet kraschar), kommer Service Bus så låser upp meddelandet automatiskt och göra det tillgängligt att tas emot igen.

I händelse av att programmet kraschar efter behandlingen av meddelandet men innan de **ta bort** metoden anropas sedan meddelandet att levereras till programmet när den startas om. Det här kallas ofta **bearbetning minst en gång**, det vill säga varje meddelande bearbetas minst en gång men i vissa situationer kan samma meddelande kan levereras. Om scenariot inte tolererar duplicerad bearbetning, bör programutvecklarna lägga till ytterligare logik i sina program för att hantera duplicerad meddelandeleverans. Detta uppnås ofta med hjälp av egenskapen **MessageId** för meddelandet. Detta är och förblir konstant under alla leveransförsök.

> [!NOTE]
> Du kan hantera Service Bus-resurser med [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/). Service Bus Explorer tillåter användare att ansluta till ett Service Bus-namnområde och administrera meddelandeentiteter på ett enkelt sätt. Verktyget tillhandahåller avancerade funktioner som import/export-funktionalitet eller möjligheten att testa ämne, köer, prenumerationer, relätjänster, meddelandehubbar och händelser hubs. 

## <a name="next-steps"></a>Nästa steg
Nu när du har lärt dig grunderna i Service Bus-köer, se följande artiklar om du vill veta mer.

* [Köer, ämnen och prenumerationer][Queues, topics, and subscriptions]

[Azure portal]: https://portal.azure.com
[Python Azure Service Bus package]: https://pypi.python.org/pypi/azure-servicebus  
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[Service Bus quotas]: service-bus-quotas.md

