---
title: Azure Functions skalning och värdtjänster | Microsoft Docs
description: Lär dig hur du väljer mellan Azure Functions-förbrukningsplanen och premiumplan.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: Azure functions, funktion, förbrukningsplan, premiumplan, händelsebearbetning, webhooks, dynamisk beräkning, serverlös arkitektur
ms.assetid: 5b63649c-ec7f-4564-b168-e0a74cb7e0f3
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 03/27/2019
ms.author: glenga
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3253cc7e379ae63880d533f14bc76e7af5a4425a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67050540"
---
# <a name="azure-functions-scale-and-hosting"></a>Azure Functions skalar och som är värd för

När du skapar en funktionsapp i Azure måste du välja en värdplan för din app. Det finns tre värdplaner för Azure Functions: [Förbrukningsplan](#consumption-plan), [premiumprenumerationen](#premium-plan), och [App Service-plan](#app-service-plan).

Värdplan som du väljer avgör följande:

* Hur skalas din funktionsapp.
* Resurserna som är tillgängliga för varje funktion app-instansen.
* Stöd för avancerade funktioner, till exempel VNET-anslutning.

Både förbrukning och Premium-prenumerationer kan du automatiskt till datorkraft när koden körs. Din app är skalade ut vid behov för att hantera belastningen och skalas när koden har körts. För förbrukningsplan kan behöver du även betala för virtuella datorer eller reserverad kapacitet i förväg.  

Premiumprenumerationen tillhandahåller ytterligare funktioner som premium compute-instanser, möjlighet att behålla instanser varma på obestämd tid och VNet-anslutning.

App Service-plan kan du dra nytta av dedikerad infrastruktur som du hanterar. Funktionsappen inte skalas baserat på händelser, vilket innebär att är aldrig skalas ned till noll. (Kräver att [alltid på](#always-on) är aktiverat.)

> [!NOTE]
> Du kan växla mellan förbrukning och Premium-prenumerationen genom att ändra egenskapen plan för funktionen app-resursen.

## <a name="hosting-plan-support"></a>Som är värd för planen support

Funktionsstöd hamnar i följande två kategorier:

* _Allmänt tillgänglig (GA)_ : fullständigt stöd och godkänts för användning i produktion.
* _Förhandsversion av_: fullständigt ännu inte stöds och godkänts för användning i produktion.

Följande tabell visar den aktuella nivån av stöd för tre värdplaner när du kör på Windows eller Linux:

| | Förbrukningsplan | Premiumplan | Dedikerad plan |
|-|:----------------:|:------------:|:----------------:|
| Windows | Allmän tillgänglighet (GA) | förhandsversion | Allmän tillgänglighet (GA) |
| Linux | förhandsversion | Gäller inte | Allmän tillgänglighet (GA) |

## <a name="consumption-plan"></a>Förbrukningsplan

När du använder förbrukningsplanen är dynamiskt instanser av Azure Functions-värden har lagts till och tas bort baserat på antalet inkommande händelser. Den här serverlösa planen skalas automatiskt och du debiteras för beräkningsresurser bara när dina funktioner körs. I en förbrukningsplan gör körning av en funktion timeout när en konfigurerbar tidsperiod.

Fakturering baseras på antalet körningar, körningstid och minne som används. Fakturering slås samman över alla funktioner i en funktionsapp. Mer information finns i den [Azure Functions sidan med priser](https://azure.microsoft.com/pricing/details/functions/).

Med förbrukningsplanen är standard värdplan och har följande fördelar:

* Betala endast när dina funktioner körs
* Skala ut automatiskt, även under perioder med hög att läsa in

Funktionsappar i samma region kan tilldelas till samma förbrukningsplan. Det finns ingen nackdel eller inverkan att ha flera appar som körs i samma användningsplan. Flera appar tilldelas samma förbrukningsplan har ingen inverkan på ökad flexibilitet, skalbarhet eller tillförlitlighet för varje app.

## <a name="premium-plan"></a>Premiumprenumerationen (förhandsversion)

När du använder Premium-prenumerationen är instanser av Azure Functions-värden har lagts till och tas bort baserat på antalet inkommande händelser precis som med förbrukningsplanen.  Premiumprenumerationen har stöd för följande funktioner:

* Permanent värmt instanser för att undvika eventuella kallstart
* VNet-anslutning
* Obegränsade körningstid
* Premium-instansstorlekar (en kärna, två kärnor och fyra kärnor instanser)
* Mer förutsägbara priser
* Högdensitet app tilldelning för planer med flera funktionsappar

Information om hur du kan konfigurera de här alternativen finns i den [Azure Functions premium plan dokumentet](functions-premium-plan.md).

I stället för fakturering per körning och minne som förbrukas bygger faktureringen för premiumplan på antalet kärnor sekunder körningstid och minne som används för nödvändiga och reserverade instanser.  Minst en instans måste vara varma at hela tiden. Det innebär att det finns en fast månatlig kostnad per active-avtal, oavsett antalet körningar.

Överväg premiumplan för Azure Functions i följande situationer:

* Dina funktionsappar kör kontinuerligt eller nästan kontinuerligt.
* Du behöver fler alternativ än vad som tillhandahålls av förbrukningsplanen som CPU eller minne.
* Koden ska köras längre än den [högsta körningstiden tillåtna](#timeout) på förbrukningsplanen.
* Du behöver funktioner som endast är tillgängliga på en premiumplan, till exempel VNET/VPN-anslutning.

> [!NOTE]
> Förhandsversionen av premium-plan stöder för närvarande endast Azure Functions på Windows.

När du kör JavaScript-funktioner på en premiumplan, bör du välja en instans som har färre virtuella processorer. Mer information finns i den [väljer enkel kärna premiumplaner](functions-reference-node.md#considerations-for-javascript-functions).  

## <a name="app-service-plan"></a>Dedikerad (App Service)-plan

Dina funktionsappar kan också köra på samma dedikerade virtuella datorer som andra App Service-appar (Basic, Standard, Premium och isolerade SKU: er).

Överväg en App Service-plan i följande situationer:

* Du har befintliga, underutnyttjade virtuella datorer som redan kör andra App Service-instanser.
* Du vill ange en anpassad avbildning som du vill köra dina funktioner.

Betala samma för funktionsappar i en App Service Plan som för andra App Service-resurser, t.ex. webbappar. Mer information om hur App Service-planen fungerar finns i den [Azure App Service-planer djupgående översikt över](../app-service/overview-hosting-plans.md).

Med en App Service-plan kan du manuellt skala ut genom att lägga till flera VM-instanser. Du kan också aktivera automatisk skalning. Mer information finns i [skala instansantalet manuellt eller automatiskt](../azure-monitor/platform/autoscale-get-started.md?toc=%2fazure%2fapp-service%2ftoc.json). Du kan även skala upp genom att välja en annan App Service-plan. Mer information finns i [skala upp en app i Azure](../app-service/web-sites-scale.md). 

När du kör JavaScript-funktioner i en App Service-plan, bör du välja en plan med färre virtuella processorer. Mer information finns i [väljer App Service-planer med enkel kärna](functions-reference-node.md#choose-single-vcpu-app-service-plans). 
<!-- Note: the portal links to this section via fwlink https://go.microsoft.com/fwlink/?linkid=830855 --> 

### <a name="always-on"></a> Alltid på

Om du kör i en App Service-plan, bör du aktivera den **alltid på** så att din funktionsapp körs korrekt. I en App Service-plan går att funktionskörningen inaktiv efter ett par minuter av inaktivitet, så att endast HTTP-utlösare ska ”aktivera” dina funktioner. Alltid är på endast tillgänglig på en App Service plan. I en förbrukningsplan aktiverar plattformen funktionsappar automatiskt.

[!INCLUDE [Timeout Duration section](../../includes/functions-timeout-duration.md)]


Även med alltid aktiverad, tidsgränsen för körningar för enskilda funktioner styrs av den `functionTimeout` i den [host.json](functions-host-json.md#functiontimeout) projektfilen.

## <a name="determine-the-hosting-plan-of-an-existing-application"></a>Fastställa värdplanen på ett befintligt program

Information om värdplanen som används av funktionsappen finns **App Service-plan / prisnivå** i den **översikt** fliken för funktionsappen i den [Azure-portalen](https://portal.azure.com). För App Service-planer anges också prisnivån.

![Visa skalning plan i portalen](./media/functions-scale/function-app-overview-portal.png)

Du kan också använda Azure CLI för att fastställa planen, enligt följande:

```azurecli-interactive
appServicePlanId=$(az functionapp show --name <my_function_app_name> --resource-group <my_resource_group> --query appServicePlanId --output tsv)
az appservice plan list --query "[?id=='$appServicePlanId'].sku.tier" --output tsv
```  

När utdata från det här kommandot är `dynamic`, funktionsappen är i förbrukningsplanen. När utdata från det här kommandot är `ElasticPremium`, funktionsappen är i Premium-prenumerationen. Alla andra värden tyda på olika nivåer av en App Service-plan.

## <a name="storage-account-requirements"></a>Krav för lagringskonto

En funktionsapp kräver ett allmänt Azure Storage-konto som har stöd för Azure Blob, kö, filer och tabell för lagring på ett schema. Detta beror på funktioner som förlitar sig på Azure Storage för åtgärder som att hantera utlösare och logga funktionskörningar, men vissa lagringskonton stöder inte köer och tabeller. Dessa konton, bland annat endast blob storage-konton (inklusive premiumlagring) och lagringskonton med replikering för zonredundant lagring, är filtrerat ut från din befintliga **Lagringskonto** val när du skapar en funktionsapp.

<!-- JH: Does using a Premium Storage account improve perf? -->

Mer information om lagringskontotyper finns [introduktion till Azure Storage-tjänsterna](../storage/common/storage-introduction.md#azure-storage-services).

## <a name="how-the-consumption-and-premium-plans-work"></a>Så här fungerar användnings- och premium-planer

I förbrukning och premium-prenumerationer skalar Azure Functions-infrastruktur Processorn och minnesresurser genom att lägga till ytterligare instanser för Functions-värden, baserat på antalet händelser som utlöses på dess funktioner. Varje instans av Functions-värd i förbrukningsplanen är begränsad till 1,5 GB minne och en processor.  En instans av värden är hela funktionsappen, vilket innebär att alla funktioner i en funktionen app-resurs i en instans och skala på samma gång. Funktionsappar som delar samma förbrukningsplan skalas oberoende av varandra.  I premiumplanen avgör storleken på din plan tillgängligt minne och CPU för alla appar i den här planen på instansen.  

Funktionen kodfiler lagras på Azure-filresurser på funktionens huvudlagringskontot. När du tar bort huvudlagringskontot av funktionsappen kodfiler funktionen tas bort och kan inte återställas.

> [!NOTE]
> När du använder en blob-utlösare i en förbrukningsplan, kan det vara upp till en 10 minuters fördröjning vid bearbetningen av nya blobbar. Den här fördröjningen uppstår när en funktionsapp är inaktiv. När appen körs behandlas BLOB-objekt direkt. Använd Premium-prenumerationen för att undvika denna kallstart fördröjning, eller Använd den [Event Grid-utlösare](functions-bindings-event-grid.md). Mer information finns i [referensartikeln för blob-utlösare bindning](functions-bindings-storage-blob.md#trigger).

### <a name="runtime-scaling"></a>Runtime skalning

Azure Functions använder en komponent som kallas den *skala controller* att övervaka frekvensen för händelser och avgöra om du vill skala ut eller skala in. Skala controller använder heuristisk sökning för varje Utlösartyp av. Till exempel när du använder en Azure Queue storage-utlösare, skalas utifrån kölängden och åldern på äldsta kömeddelandet.

Skala för Azure Functions är funktionsappen. När funktionsappen har skalats ut, tilldelas ytterligare resurser för att köra flera instanser av Azure Functions-värden. Eftersom beräknings-efterfrågan minskar, tar däremot funktionen ha instanser bort av kontrollanten skala. Antalet instanser så småningom att skalas ned till noll när inga funktioner som körs inom en funktionsapp.

![Skala controller tjänsthändelser och skapa instanser](./media/functions-scale/central-listener.png)

### <a name="understanding-scaling-behaviors"></a>Förstå skalningsbeteenden

Skalning kan variera på ett antal faktorer och skala på olika sätt beroende på utlösaren och den valda språket. Det finns några krångla med skalningsbeteenden känna till:

* En enskild funktionsapp skalar bara upp till högst 200 instanser. En enda instans kan bearbeta flera meddelande eller begäran i taget dock så det finns en set-gränsen för antalet samtidiga körningar.
* För HTTP-utlösare allokeras nya instanser endast högst en gång varje 1 sekund.
* För icke-HTTP-utlösare allokeras nya instanser endast högst en gång var 30: e sekund.

Olika utlösare kan också ha olika skalningsgränser samt dokumenterade nedan:

* [Händelsehubb](functions-bindings-event-hubs.md#trigger---scaling)

### <a name="best-practices-and-patterns-for-scalable-apps"></a>Bästa praxis och mönster för skalbara appar

Det finns många aspekter av en funktionsapp som påverkar hur väl den skalas, inklusive konfiguration av värdserver, runtime fotavtryck och Resursanvändning.  Mer information finns i den [skalbarhet i prestanda överväganden artikeln](functions-best-practices.md#scalability-best-practices). Du bör också känna till hur anslutningar ska fungera med ditt funktionen app. Mer information finns i [hur du hanterar anslutningar i Azure Functions](manage-connections.md).

### <a name="billing-model"></a>Faktureringsmodell

Faktureringen för de olika planerna beskrivs i detalj på den [Azure Functions sidan med priser](https://azure.microsoft.com/pricing/details/functions/). Användning sammanställs på funktionen app-nivå och räknar bara den tid som Funktionskoden körs. Här följer några enheter för fakturering:

* **Resursförbrukning i gigabyte-sekunder (GB-s)** . Beräknad som en kombination av minnesstorlek och körningstid för alla funktioner i en funktionsapp. 
* **Körningar**. Räknas varje gång en funktion körs som svar på en händelseutlösare.

Användbara frågor och information om hur du förstå fakturan förbrukning finns [på fakturering vanliga](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ).

[Azure Functions pricing page]: https://azure.microsoft.com/pricing/details/functions

## <a name="service-limits"></a>Tjänstbegränsningar

Följande tabell visar de begränsningar som gäller för funktionsappar när de körs i olika värdplaner:

[!INCLUDE [functions-limits](../../includes/functions-limits.md)]
