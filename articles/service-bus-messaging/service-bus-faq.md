---
title: Vanliga frågor (och svar FAQ) om Azure Service Bus | Microsoft Docs
description: Innehåller några vanliga frågor och svar om Azure Service Bus.
services: service-bus-messaging
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.topic: article
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: 8461764a3f1f682ffb97420a4efdf2803f518872
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64707149"
---
# <a name="service-bus-faq"></a>Vanliga frågor och svar om Service Bus

Den här artikeln beskriver några vanliga frågor om Microsoft Azure Service Bus. Du kan också besöka den [Azure stöd för vanliga frågor och svar](https://azure.microsoft.com/support/faq/) allmän Azure prissättning och information.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="general-questions-about-azure-service-bus"></a>Allmänna frågor om Azure Service Bus
### <a name="what-is-azure-service-bus"></a>Vad är Azure Service Bus?
[Azure Service Bus](service-bus-messaging-overview.md) är en asynkron meddelandetjänst molnplattform där du kan skicka data mellan fristående system. Microsoft erbjuder den här funktionen som en tjänst, vilket innebär att du inte behöver som värd för din egen maskinvara för att använda den.

### <a name="what-is-a-service-bus-namespace"></a>Vad är en Service Bus-namnrymd?
En [namnområde](service-bus-create-namespace-portal.md) tillhandahåller en omfångsbehållare för adressering av Service Bus-resurser i ditt program. Skapa ett namnområde är nödvändigt att använda Service Bus och är en av de första stegen i komma igång.

### <a name="what-is-an-azure-service-bus-queue"></a>Vad är en Azure Service Bus-kö?
En [Service Bus-kö](service-bus-queues-topics-subscriptions.md) är en entitet som meddelanden lagras. Köer är användbara när du har flera program eller flera delar i ett distribuerat program som behöver kommunicera med varandra. Kön är liknar ett distributionscenter eftersom flera produkter (meddelanden) tas emot och skickas sedan från den platsen.

### <a name="what-are-azure-service-bus-topics-and-subscriptions"></a>Vad är Azure Service Bus-ämnen och prenumerationer?
Ett ämne kan visualiseras som en kö och när du använder flera prenumerationer, blir den en rikare meddelanden modellen. i grunden en en-till-många-kommunikationsverktyg. Den här modellen för Publicera/prenumerera på (eller *pub/sub*) gör att ett program som skickar ett meddelande till ett ämne med flera prenumerationer har meddelandet tas emot av flera program.

### <a name="what-is-a-partitioned-entity"></a>Vad är en partitionerad entitet?
En konventionell kö eller ämne hanteras av en enda asynkron meddelandekö och lagras i ett meddelandearkiv. Stöds endast i Basic och Standard-meddelandenivåerna, en [partitionerad kö eller ämne](service-bus-partitioning.md) hanteras av flera koordinatorerna och förvaras i flera meddelandearkiv. Den här funktionen innebär att hela dataflödet för en partitionerad kö eller ett ämne begränsas inte längre av prestanda för en enda asynkron meddelandekö eller ett meddelandearkiv. Dessutom kan återges ett tillfälligt avbrott i ett meddelandearkiv inte en partitionerad kö eller ett ämne inte tillgänglig.

Sorteringen är inte garanterad när med partitionerade enheter. I händelse av att en partition är inte tillgänglig, kan du fortfarande skicka och ta emot meddelanden från andra partitioner.

 Partitionerade enheter stöds inte längre i den [Premium-SKU](service-bus-premium-messaging.md). 

### <a name="what-ports-do-i-need-to-open-on-the-firewall"></a>Vilka portar behöver jag att öppna i brandväggen? 
Du kan använda följande protokoll med Azure Service Bus för att skicka och ta emot meddelanden:

- Avancerade Message Queuing Protocol (AMQP)
- Service Bus-meddelandefunktionen Protocol (SBMP)
- HTTP

Se tabellen nedan för de utgående portar som du behöver öppna om du vill använda dessa protokoll ska kunna kommunicera med Azure Event Hubs. 

| Protocol | Portar | Information | 
| -------- | ----- | ------- | 
| AMQP | 5671 och 5672 | Se [AMQP-protokollguide](service-bus-amqp-protocol-guide.md) | 
| SBMP | 9350 till 9354 | Se [anslutningsläget](/dotnet/api/microsoft.servicebus.connectivitymode?view=azure-dotnet) |
| HTTP, HTTPS | 80, 443 | 

### <a name="what-ip-addresses-do-i-need-to-whitelist"></a>Vilka IP-adresser behöver jag godkänna?
Följ dessa steg för att hitta rätt IP-adresser till vitlista för dina anslutningar:

1. Kör följande kommando från en kommandotolk: 

    ```
    nslookup <YourNamespaceName>.servicebus.windows.net
    ```
2. Anteckna den IP-adressen som returneras i `Non-authoritative answer`. Den här IP-adressen är statisk. Den enda tidpunkt den skulle ändras är om du återställer namnområde till ett annat kluster.

Om du använder redundansen för ditt namnområde kan behöva du göra några ytterligare steg: 

1. Först måste köra du nslookup på namnområdet.

    ```
    nslookup <yournamespace>.servicebus.windows.net
    ```
2. Skriv ned namnet i den **icke-auktoritativt svar** avsnittet, vilket är i något av följande format: 

    ```
    <name>-s1.servicebus.windows.net
    <name>-s2.servicebus.windows.net
    <name>-s3.servicebus.windows.net
    ```
3. Köra nslookup för vart och ett med suffix s1, s2 och s3 att hämta IP-adresserna för alla tre instanser som körs i tre tillgänglighetszoner 


## <a name="best-practices"></a>Bästa praxis
### <a name="what-are-some-azure-service-bus-best-practices"></a>Vilka är några Metodtips för Azure Service Bus?
Se [Metodtips för prestandaförbättringar med hjälp av Service Bus] [ Best practices for performance improvements using Service Bus] – den här artikeln beskrivs hur du optimerar prestanda när du skickar meddelanden.

### <a name="what-should-i-know-before-creating-entities"></a>Vad bör jag veta innan du skapar entiteter?
Följande egenskaper för en kö och avsnittet är inte kan ändras. Överväg att den här begränsningen när du etablerar dina entiteter som de här egenskaperna inte kan ändras utan att skapa en ny entitet ersättning.

* Partitionering
* Sessioner
* Dubblettidentifiering
* Express entitet

## <a name="pricing"></a>Prissättning
Det här avsnittet får du svar på några vanliga frågor om Service Bus prissättningsstruktur.

Den [Service Bus priser och fakturering](https://azure.microsoft.com/pricing/details/service-bus/) artikeln förklarar faktureringsmätare i Service Bus. Specifik information om Service Bus prisalternativ finns i [Service Bus prisinformation](https://azure.microsoft.com/pricing/details/service-bus/).

Du kan också besöka den [Azure stöd för vanliga frågor och svar](https://azure.microsoft.com/support/faq/) för allmänna Azure information om priser. 

### <a name="how-do-you-charge-for-service-bus"></a>Hur tar du betalt för Service Bus?
Fullständig information om priser för Service Bus finns i [Service Bus prisinformation][Pricing overview]. Förutom priserna, debiteras du för överföring av associerade data för utanför datacentret där programmet har etablerats.

### <a name="what-usage-of-service-bus-is-subject-to-data-transfer-what-is-not"></a>Vilka användning av Service Bus är föremål för dataöverföring? Vad är inte?
Eventuell dataöverföring inom en viss Azure-region tillhandahålls utan kostnad, samt eventuella inkommande dataöverföring. Dataöverföring utanför en region är föremål för kostnader för utgående trafik, som du hittar [här](https://azure.microsoft.com/pricing/details/bandwidth/).

### <a name="does-service-bus-charge-for-storage"></a>Debiterar Service Bus för lagring?
Nej, debiteras inte Service Bus för lagring. Det finns dock en kvot för att begränsa den maximala mängden data som kan vara kvar per kö/ämne. Se nästa vanliga frågor och svar.

## <a name="quotas"></a>Kvoter

En lista över Service Bus-begränsningar och kvoter finns i den [översikt över Service Bus-kvoter][Quotas overview].

### <a name="does-service-bus-have-any-usage-quotas"></a>Finns det några användningskvoter Service Bus?
Som standard för alla moln anger tjänsten Microsoft en sammanställd månatliga kvot för användning som beräknas för alla prenumerationer för en kund. Om du behöver mer än de här gränserna kan kontakta du kundtjänst när du vill veta vad du behöver och kan justera gränserna på rätt sätt. För Service Bus är den sammanställda användningskvot 5 miljarder meddelanden per månad.

Microsoft förbehåller sig rätten att inaktivera ett kundkonto som har överskridit sina användningskvoter i en viss månad, e-postmeddelanden skickas och flera försök görs att kontakta en kund innan du vidtar åtgärder. Kunder som överskrider kvoterna är fortfarande ansvarig för kostnaderna kvoter.

Precis som med andra tjänster på Azure tillämpar Service Bus en uppsättning specifika kvoter så att det är verkliga användning av resurser. Du hittar mer information om dessa kvoter i den [översikt över Service Bus-kvoter][Quotas overview].

### <a name="how-to-handle-messages-of-size--1-mb"></a>Hur kan hantera meddelanden av storlek > 1 MB?
Service Bus-meddelandetjänster (köer och ämnen/prenumerationer) gör att programmet kan skicka meddelanden med storleken upp till 256 KB (standard-nivån) eller 1 MB (premium-nivån). Om du arbetar med meddelanden är större än 1 MB använder den anspråk mönster som beskrivs i [det här blogginlägget](https://www.serverless360.com/blog/deal-with-large-service-bus-messages-using-claim-check-pattern).

## <a name="troubleshooting"></a>Felsökning
### <a name="why-am-i-not-able-to-create-a-namespace-after-deleting-it-from-another-subscription"></a>Varför kan jag inte att skapa ett namnområde efter tas bort från en annan prenumeration? 
När du tar bort ett namnområde från en prenumeration kan du vänta i 4 timmar innan du återskapa den med samma namn i en annan prenumeration. I annat fall kan du få följande felmeddelande visas: `Namespace already exists`. 

### <a name="what-are-some-of-the-exceptions-generated-by-azure-service-bus-apis-and-their-suggested-actions"></a>Vilka är några av undantagen som genereras av Azure Service Bus-API: er och föreslagna åtgärder?
En lista över möjliga Service Bus-undantag finns i [undantag översikt][Exceptions overview].

### <a name="what-is-a-shared-access-signature-and-which-languages-support-generating-a-signature"></a>Vad är en signatur för delad åtkomst och vilka språk stöder du generera en signatur?
Signaturer för delad åtkomst är en autentiseringsmetod baserad på säkra SHA-256-hashvärden eller URI: er. Information om hur du skapar dina egna signaturer i Node.js, PHP, Java och C\#, finns i den [signaturer för delad åtkomst] [ Shared Access Signatures] artikeln.

## <a name="subscription-and-namespace-management"></a>Prenumeration och namnområde
### <a name="how-do-i-migrate-a-namespace-to-another-azure-subscription"></a>Hur migrerar jag ett namnområde till en annan Azure-prenumeration?

Du kan flytta ett namnområde från en Azure-prenumeration till en annan, med hjälp av antingen den [Azure-portalen](https://portal.azure.com) eller PowerShell-kommandon. För att köra åtgärden vara namnområdet redan aktiv. Användaren som kör kommandona måste vara administratör på både käll- och prenumerationer.

#### <a name="portal"></a>Portalen

Följ anvisningarna för att använda Azure-portalen för att migrera Service Bus-namnrymder till en annan prenumeration, [här](../azure-resource-manager/resource-group-move-resources.md#use-portal). 

#### <a name="powershell"></a>PowerShell

Följande sekvens med PowerShell-kommandon flyttar ett namnområde från en Azure-prenumeration till en annan. Om du vill utföra den här åtgärden namnområdet måste redan vara aktivt och användaren som kör PowerShell-kommandon måste vara administratör på både käll- och prenumerationer.

```powershell
# Create a new resource group in target subscription
Select-AzSubscription -SubscriptionId 'ffffffff-ffff-ffff-ffff-ffffffffffff'
New-AzResourceGroup -Name 'targetRG' -Location 'East US'

# Move namespace from source subscription to target subscription
Select-AzSubscription -SubscriptionId 'aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa'
$res = Find-AzResource -ResourceNameContains mynamespace -ResourceType 'Microsoft.ServiceBus/namespaces'
Move-AzResource -DestinationResourceGroupName 'targetRG' -DestinationSubscriptionId 'ffffffff-ffff-ffff-ffff-ffffffffffff' -ResourceId $res.ResourceId
```

## <a name="next-steps"></a>Nästa steg
Mer information om Service Bus finns i följande artiklar:

* [Introduktion till Azure Service Bus Premium (blogginlägg)](https://azure.microsoft.com/blog/introducing-azure-service-bus-premium-messaging/)
* [Introduktion till Azure Service Bus Premium (Channel9)](https://channel9.msdn.com/Blogs/Subscribe/Introducing-Azure-Service-Bus-Premium-Messaging)
* [Översikt över Service Bus](service-bus-messaging-overview.md)
* [Komma igång med Service Bus-köer](service-bus-dotnet-get-started-with-queues.md)

[Best practices for performance improvements using Service Bus]: service-bus-performance-improvements.md
[Best practices for insulating applications against Service Bus outages and disasters]: service-bus-outages-disasters.md
[Pricing overview]: https://azure.microsoft.com/pricing/details/service-bus/
[Quotas overview]: service-bus-quotas.md
[Exceptions overview]: service-bus-messaging-exceptions.md
[Shared Access Signatures]: service-bus-sas.md
