---
title: Återhämtning och haveriberedskap i Azure SignalR Service
description: En översikt över hur du konfigurerar flera SignalR Service-instanser för att uppnå återhämtning och haveriberedskap
author: chenkennt
ms.service: signalr
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: kenchen
ms.openlocfilehash: eb70e65db4a086afc60e91cadf55a8844b102591
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61402157"
---
# <a name="resiliency-and-disaster-recovery"></a>Återhämtning och haveriberedskap

Återhämtning och haveriberedskap är ett vanligt behov för onlinesystem. Azure SignalR Service garanterar redan 99,9 % tillgänglighet, men det är fortfarande en regional tjänst.
Din tjänstinstans körs alltid i en region och inte redundansväxla till en annan region när det finns ett avbrott på inom hela regionen.

I stället innehåller vår tjänst-SDK en funktion som stöder flera SignalR Service-instanser och automatiskt växlar till andra instanser när vissa av dem inte är tillgängliga.
Med den här funktionen kan du återställa när det sker en incident, men du måste själv konfigurera rätt systemtopologi. Du lär dig att göra det i det här dokumentet.

## <a name="high-available-architecture-for-signalr-service"></a>Arkitektur med hög tillgänglighet för SignalR Service

För att få återhämtningsförmåga mellan regioner för SignalR Service behöver du konfigurera flera tjänstinstanser i olika regioner. När en region är nere kan de andra därmed användas som reserver.
När flera tjänstinstanser ansluts till appservern finns det två roller, den primära och den sekundära.
Den primära är en instans som tar onlinetrafik, medan den sekundära är en helt funktionell instans som dock utgör reserv till den primära.
I vår SDK-implementering returnerar förhandling endast primära slutpunkter, så i normala fall ansluter klienter bara till primära slutpunkter.
Men när primära instansen är nere returnerar förhandling sekundära slutpunkter så att klienten fortfarande kan göra anslutningar.
Primära instansen och app-servern är anslutna via normal serveranslutningar men sekundära instans och app-servern är anslutna via en särskild typ av anslutning kallas svaga anslutning.
Den största skillnaden i en svag anslutning är att den inte acceptera klient anslutningsroutning eftersom sekundära instans finns i en annan region. Routning en klient till en annan region är inte en föredra (ökar svarstid).

En tjänstinstans kan ha olika roller vid anslutning till flera appservrar.
En typisk konfiguration för scenarier mellan regioner är att ha två (eller fler) par med SignalR Service-instanser och appservrar.
I varje par finns appservern och SignalR Service i samma region, och SignalR Service är ansluten till appservern som en primär roll.
Appservern och SignalR Service är även anslutna mellan varje par app, men SignalR blir en sekundär vid anslutning till en server i en annan region.

Med den här topologin kan meddelanden från en server fortfarande levereras till alla klienter eftersom alla appservrar och SignalR Service-instanser är sammankopplade.
Men när en klient ansluts dirigeras den alltid till appservern i samma region för att uppnå optimal nätverkssvarstid.

Nedan visas ett diagram som illustrerar en sådan topologi:

![topologi](media/signalr-concept-disaster-recovery/topology.png)

## <a name="configure-app-servers-with-multiple-signalr-service-instances"></a>Konfigurera appservrar med flera SignalR Service-instanser

När du har skapat SignalR Service och appservrarna i varje region kan du konfigurera appservrarna att ansluta till alla SignalR Service-instanser.

Det finns två sätt att göra det:

### <a name="through-config"></a>Med config

Du borde ha känt hur du ställer in SignalR tjänstanslutningssträngen via miljö variabler/app settings/web.cofig via en config-post med namnet `Azure:SignalR:ConnectionString`.
Om du har flera slutpunkter kan du ange dem i flera config-poster, var och en i följande format:

```
Azure:SignalR:Connection:<name>:<role>
```

Här `<name>` är namnet på slutpunkten, och `<role>` är dess roll (primär eller sekundär).
Namnet är valfritt men användbart om du vill anpassa routningsbeteendet mellan flera slutpunkter ytterligare.

### <a name="through-code"></a>Med kod

Om du hellre vill lagra anslutningssträngen någon annanstans kan du läsa den i koden och använda dem som parametrar när du anropar `AddAzureSignalR()` (i ASP.NET Core) eller `MapAzureSignalR()` (i ASP.NET).

Här är exempelkoden:

ASP.NET Core:

```cs
services.AddSignalR()
        .AddAzureSignalR(options => options.Endpoints = new ServiceEndpoint[]
        {
            new ServiceEndpoint("<connection_string1>", EndpointType.Primary, "region1"),
            new ServiceEndpoint("<connection_string2>", EndpointType.Secondary, "region2"),
        });
```

ASP.NET:

```cs
app.MapAzureSignalR(GetType().FullName, hub,  options => options.Endpoints = new ServiceEndpoint[]
    {
        new ServiceEndpoint("<connection_string1>", EndpointType.Primary, "region1"),
        new ServiceEndpoint("<connection_string2>", EndpointType.Secondary, "region2"),
    };
```

## <a name="failover-sequence-and-best-practice"></a>Redundanssekvens och bästa praxis

Nu har du rätt konfiguration av systemtopologi. När en SignalR Service-instans är nere dirigeras onlinetrafik till andra instanser.
Det här är vad som händer när en primär instans är nere (och återställs efter ett tag):

1. Den primära tjänstinstans är nere och alla serveranslutningar i den här instansen tas bort.
2. Alla servrar som är anslutna till den här instansen markerar den som offline. De slutar returnera den här slutpunkten och börjar returnera den sekundära slutpunkten.
3. Dessutom stängs alla klientanslutningar i den här instansen. Klienterna återansluts. Eftersom appservrarna nu returnerar den sekundära slutpunkten så ansluter klienterna till den sekundära instansen.
4. Nu tar den sekundära instansen all onlinetrafik. Alla meddelanden från servern till klienter kan fortfarande levereras eftersom den sekundära är ansluten till alla appservrar. Däremot dirigeras klient-till-server-meddelanden endast till appservern i samma region.
5. När den primära instansen har återställts och är online igen återupprättar appservern anslutningarna till den och markerar den som online. Förhandling returnerar nu den primära slutpunkten igen, så nya klienter återansluts till den primära. Men befintliga klienter kopplas inte från, utan fortsätter att dirigeras till den sekundära tills de själva koppar från.

Nedanstående diagram illustrerar hur redundansväxling sker i SignalR Service:

Fig.1 Före redundansväxling ![Före redundansväxling](media/signalr-concept-disaster-recovery/before-failover.png)

Fig.2 Efter redundansväxling ![Efter redundansväxling](media/signalr-concept-disaster-recovery/after-failover.png)

Fig.3 Strax efter den primära återställs ![Strax efter den primära återställs](media/signalr-concept-disaster-recovery/after-recover.png)

I det normala fallet har bara den primära appservern och SignalR Service onlinetrafik (i blått).
Efter redundansväxling blir den sekundära appservern och SignalR Service också aktiva.
När den primära SignalR Service är online igen ansluter nya klienter till den primära SignalR. Men befintliga klienter ansluter fortfarande till den sekundära så att båda instanserna har trafik.
När alla befintliga klienter har kopplats från återgår systemet till det normala (fig.1).

Det finns två huvudsakliga mönster för att implementera en arkitektur med hög tillgänglighet mellan regioner:

1. Det första mönstret är att ha ett par med en appserver och en SignalR Service-instans som tar all onlinetrafik samt ett annat par som reserv (detta kallas aktiv/passiv och illustreras i Fig.1). 
2. Det andra mönstret är att ha två (eller fler) par med appservrar och SignalR Service-instanser som var och en tar en del av onlinetrafiken och fungerar som reserv för andra par (detta kallas aktiv/aktiv och liknar fig.3).

SignalR Service har stöd för både mönstren. Den största skillnaden är hur du implementerar appservrar.
Om appservrarna är aktiva/passiva blir SignalR Service också aktiv/passiv (eftersom den primära appservern endast returnerar sin primära SignalR Service-instans).
Om appservrarna är aktiva/aktiva blir SignalR Service också aktiv/aktiv (eftersom alla appservrar returnerar sina egna primära SignalR-instanser så att de alla kan hämta trafik).

Noteras oavsett vilken mönster som du väljer att använda, måste du ansluta varje SignalR service-instans till en app-servern som primär.

På grund av hur SignalR-anslutningen fungerar (det är en lång anslutning) kommer klienter dessutom att råka ut för anslutningsavbrott i fall av katastrof och redundansväxling.
Du behöver hantera sådana fall på klientsidan för att göra det transparent för dina slutkunder. Till exempel bör du återansluta när en anslutning har kopplats från.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig att konfigurera programmet för att uppnå återhämtningsförmåga för SignalR Service. Om du vill få mer information om server/klient-anslutning och anslutningsroutning i SignalR Service kan du läsa [den här artikeln](signalr-concept-internals.md) om hur SignalR Service fungerar på insidan.

För att skala scenarier, till exempel horisontell partitionering, som använder flera instanser tillsammans för att hantera stora antal anslutningar, läsa [så här skalar du flera instanser](signalr-howto-scale-multi-instances.md)?