---
title: Låsa App Service Environment utgående trafik – Azure
description: Beskriver hur du integrerar med Azure-Brandvägg för att skydda utgående trafik
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 955a4d84-94ca-418d-aa79-b57a5eb8cb85
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/11/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 6dae2d40650b9fdb8df2d3bdb74b2df78639dc11
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67058057"
---
# <a name="locking-down-an-app-service-environment"></a>Låsa en App Service Environment

App Service Environment (ASE) har ett antal externa beroenden som den behöver åtkomst till för att fungera korrekt. ASE bor i kundens Azure-nätverk (VNet). Kunder måste tillåta den ASE-beroende trafik, vilket är ett problem för kunder som vill låsa alla utgående trafiken från sina virtuella nätverk.

Det finns ett antal inkommande beroenden som har en ase-miljö. Inkommande hanteringstrafik kan inte skickas via en brandväggsenhet. Källadresser för den här trafiken är kända och publiceras i den [hanteringsadresser för App Service Environment](https://docs.microsoft.com/azure/app-service/environment/management-addresses) dokumentet. Du kan skapa regler för Nätverkssäkerhetsgruppen med den informationen för att skydda inkommande trafik.

De utgående ASE-beroendena definieras nästan helt och hållet med FQDN: er som inte har statiska adresser bakom dem. Bristen på statiska adresser innebär att Nätverkssäkerhetsgrupper (NSG) inte kan användas för att låsa den utgående trafiken från en ase-miljö. Adresserna ändras tillräckligt ofta att det går inte att ställa in regler baserat på aktuell upplösning och använda den för att skapa NSG: er. 

Lösning för att skydda utgående adresser ligger i att en brandväggsenhet som kan styra utgående trafik baserat på domännamn. Azure-brandväggen kan begränsa utgående HTTP och HTTPS-trafik baserat på det fullständiga Domännamnet för målet.  

## <a name="system-architecture"></a>Systemarkitektur

Distribuera en ASE med utgående trafik via en brandväggsenhet innebär att du ändrar vägar på ASE-undernät. Vägar fungerar på IP-nivå. Om du inte är försiktig definiera vägarna kan tvinga du TCP svars-trafik som källa för från en annan adress. Detta kallas asymmetrisk Routning och det bryter TCP.

Det måste finnas vägar definierade så att inkommande trafik till ASE kan svara tillbaka på samma sätt som trafiken kommer. Det här gäller för av inkommande hanteringsbegäranden och det är sant för inkommande programförfrågningar.

Trafik till och från en ase-miljö måste följa följande konventioner

* Trafik till Azure SQL, lagring och Event Hub stöds inte med användning av en brandväggsenhet. Den här trafiken måste skickas direkt till dessa tjänster. Sätt att göra som sker är att konfigurera tjänstslutpunkter för dessa tre tjänster. 
* Regler för tabell måste definieras som skickar inkommande trafik från var den kom.
* Regler för tabell måste definieras som skickar inkommande trafik från var den kom. 
* All annan trafik som lämnar ASE kan skickas till din brandväggsenhet med en regel för route-table.

![ASE med Azure-brandvägg anslutningsflödet][5]

## <a name="configuring-azure-firewall-with-your-ase"></a>Konfigurera Brandvägg för Azure med din ASE 

Stegen för att låsa utgående trafik från din befintliga ASE med Brandvägg för Azure är:

1. Aktivera Tjänsteslutpunkter för SQL, lagring och Event Hub på ditt ASE-undernät. Gör detta genom att gå till nätverk portalen > undernät och väljer Microsoft.EventHub, Microsoft.SQL och Microsoft.Storage i listrutan på slutpunkter Service. Eventuella Azure SQL-beroenden som dina appar har måste konfigureras med Tjänsteslutpunkter även när du har aktiverat till Azure SQL Tjänsteslutpunkter. 

   ![Välj Tjänsteslutpunkter][2]
  
1. Skapa ett undernät med namnet AzureFirewallSubnet i det virtuella nätverket där din ASE finns. Följ anvisningarna i den [dokumentation om Azure-brandvägg](https://docs.microsoft.com/azure/firewall/) att skapa din Azure-brandvägg.
1. I användargränssnittet för Azure-brandväggen > regler > program regelsamlingen, väljer Lägg till program-regelsamlingen. Ange ett namn, prioritet, och ange Tillåt. I avsnittet taggar FQDN, ange ett namn genom att ange källadresser * och välj App Service Environment FQDN-tagg och Windows Update. 
   
   ![Lägg till regel för program][1]
   
1. I användargränssnittet för Azure-brandväggen > regler > nätverk regelsamlingen, välj Lägg till nätverk regelsamlingen. Ange ett namn, prioritet och ange Tillåt. I avsnittet regler för att ange ett namn, Välj **alla**, ange * till käll- och adresser, och ange portarna som 123. Den här regeln gör det möjligt för systemet att utföra klockan synkronisering med NTP. Skapa en annan regel på samma sätt till port 12000 att prioritering systemproblem.

   ![Lägg till regel för NTP][3]

1. Skapa en routningstabell med hanteringsadresserna från [hanteringsadresser för App Service Environment]( https://docs.microsoft.com/azure/app-service/environment/management-addresses) med ett nexthop för Internet. Tabellen routningsposterna krävs för att undvika problem med asymmetrisk routning. Lägg till vägar för IP-adress-beroenden som anges nedan i beroenden för IP-adress med ett nexthop för Internet. Lägg till en virtuell installation väg till din routningstabellen för 0.0.0.0/0 med nästa hopp som din privata IP-adress för Brandvägg för Azure. 

   ![Skapa en routningstabell][4]
   
1. Tilldela routningstabellen som du skapade till ditt ASE-undernät.

#### <a name="deploying-your-ase-behind-a-firewall"></a>Distribuera din ASE bakom en brandvägg

Stegen för att distribuera din ASE bakom en brandvägg är samma som när du konfigurerar din befintliga ASE med en brandvägg för Azure, men kommer att behöva skapa ditt ASE-undernät och följ föregående steg. Om du vill skapa ASE i ett befintligt undernät, måste du använda en Resource Manager-mall enligt beskrivningen i dokumentet på [skapa ASE med en Resource Manager-mall](https://docs.microsoft.com/azure/app-service/environment/create-from-template).

## <a name="application-traffic"></a>Programtrafik 

Stegen ovan kan din ASE att fungera utan problem. Du måste fortfarande konfigurera saker att tillgodose behoven för ditt program. Det finns två problem för program i en ASE som har konfigurerats med Azure-brandvägg.  

- Programberoenden måste läggas till i Azure-brandväggen eller routningstabellen. 
- Vägar som måste skapas för programtrafik att undvika problem med asymmetrisk Routning

Om ditt program har beroenden, måste läggas till din Azure-brandvägg. Skapa regler för att tillåta HTTP/HTTPS-trafik och nätverk regler för allt annat. 

Om du vet det adressintervall som din programtrafik begäran kommer från, kan du lägga till att till i routningstabellen som har tilldelats till ditt ASE-undernät. Om adressintervallet är stora eller Ospecificerad, kan du använda en nätverksenhet som Application Gateway för att ge dig en adress för att lägga till i din routningstabellen. Mer information om hur du konfigurerar en Programgateway med din ILB ASE finns [integrera din ILB ASE med en Application Gateway](https://docs.microsoft.com/azure/app-service/environment/integrate-with-application-gateway)

Den här användningen av Application Gateway är bara ett exempel på hur du konfigurerar ditt system. Om du följer den här sökvägen, skulle du måste lägga till en väg i routningstabellen för ASE-undernät, så svara-trafik som skickas till Application Gateway skulle gå dit direkt. 

## <a name="logging"></a>Loggning 

Azure-brandväggen kan skicka loggar till Azure Storage, Event Hub eller Azure Monitor-loggar. För att integrera din app med ett mål som stöds, gå till Brandvägg för Azure-portalen > diagnostikloggar och aktivera loggar för dina önskade mål. Om du integrerar med Azure Monitor-loggar, ser du loggning för trafik som skickas till Azure-brandväggen. Om du vill se den trafik som nekas, öppnas portalen för Log Analytics-Arbetsyta > loggar och ange en fråga av typen 

    AzureDiagnostics | where msg_s contains "Deny" | where TimeGenerated >= ago(1h)
 
Integrera din Azure-brandvägg med Azure Monitor-loggar är användbart när du först hämtar ett program som fungerar när du inte är medvetna om alla beroenden för programmet. Du kan läsa mer om Azure Monitor-loggar från [analysera loggdata i Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview)
 
## <a name="dependencies"></a>Beroenden

Följande information är endast krävs om du vill konfigurera en brandväggsinstallation än Azure-brandväggen. 

- Tjänsteslutpunkt kan tjänster konfigureras med Tjänsteslutpunkter.
- IP-adress beroenden är för icke-HTTP/S-trafik (både TCP och UDP-trafik)
- FQDN HTTP/HTTPS-slutpunkter kan placeras i enheten för brandväggen.
- Jokertecken HTTP/HTTPS-slutpunkterna är beroenden som kan variera beroende på din ASE baserat på ett antal kvalificerare. 
- Linux-beroenden är endast ett problem om du distribuerar Linux-appar i din ASE. Om du inte distribuerar Linux-appar i din ASE, behöver inte dessa adresser som ska läggas till i brandväggen. 

#### <a name="service-endpoint-capable-dependencies"></a>Tjänsteslutpunkt kan beroenden 

| Slutpunkt |
|----------|
| Azure SQL |
| Azure Storage |
| Azure händelsehubb |

#### <a name="ip-address-dependencies"></a>IP-adress beroenden

| Slutpunkt | Information |
|----------| ----- |
| \*:123 | NTP-klockkontrollen. Trafiken är markerad på flera slutpunkter på porten 123 |
| \*:12000 | Den här porten används för systemövervakning av vissa. Om den blockeras och sedan några problem blir svårare att prioritering men din ASE fortsätter att fungera |
| 40.77.24.27:80 | Krävs för att övervakning och aviseringar om problem med ASE |
| 40.77.24.27:443 | Krävs för att övervakning och aviseringar om problem med ASE |
| 13.90.249.229:80 | Krävs för att övervakning och aviseringar om problem med ASE |
| 13.90.249.229:443 | Krävs för att övervakning och aviseringar om problem med ASE |
| 104.45.230.69:80 | Krävs för att övervakning och aviseringar om problem med ASE |
| 104.45.230.69:443 | Krävs för att övervakning och aviseringar om problem med ASE |
| 13.82.184.151:80 | Krävs för att övervakning och aviseringar om problem med ASE |
| 13.82.184.151:443 | Krävs för att övervakning och aviseringar om problem med ASE |

Med en Azure-brandväggen får automatiskt du allt nedan konfigurerad med FQDN-taggar. 

#### <a name="fqdn-httphttps-dependencies"></a>FQDN HTTP/HTTPS-beroenden 

| Slutpunkt |
|----------|
|graph.windows.net:443 |
|login.live.com:443 |
|login.windows.com:443 |
|login.windows.net:443 |
|login.microsoftonline.com:443 |
|client.wns.windows.com:443 |
|definitionupdates.microsoft.com:443 |
|go.microsoft.com:80 |
|go.microsoft.com:443 |
|www.microsoft.com:80 |
|www.microsoft.com:443 |
|wdcpalt.microsoft.com:443 |
|wdcp.microsoft.com:443 |
|ocsp.msocsp.com:443 |
|mscrl.microsoft.com:443 |
|mscrl.microsoft.com:80 |
|crl.microsoft.com:443 |
|crl.microsoft.com:80 |
|www.thawte.com:443 |
|crl3.digicert.com:80 |
|ocsp.digicert.com:80 |
|csc3-2009-2.crl.verisign.com:80 |
|crl.verisign.com:80 |
|ocsp.verisign.com:80 |
|cacerts.digicert.com:80 |
|azperfcounters1.blob.core.windows.net:443 |
|azurewatsonanalysis-prod.core.windows.net:443 |
|global.metrics.nsatc.net:80 |
|global.metrics.nsatc.net:443 |
|az-prod.metrics.nsatc.net:443 |
|antares.metrics.nsatc.net:443 |
|azglobal-black.azglobal.metrics.nsatc.net:443 |
|azglobal-red.azglobal.metrics.nsatc.net:443 |
|antares-black.antares.metrics.nsatc.net:443 |
|antares-red.antares.metrics.nsatc.net:443 |
|maupdateaccount.blob.core.windows.net:443 |
|clientconfig.passport.net:443 |
|packages.microsoft.com:443 |
|schemas.microsoft.com:80 |
|schemas.microsoft.com:443 |
|management.core.windows.net:443 |
|management.core.windows.net:80 |
|management.azure.com:443 |
|www.msftconnecttest.com:80 |
|shavamanifestcdnprod1.azureedge.net:443 |
|validation-v2.sls.microsoft.com:443 |
|flighting.cp.wd.microsoft.com:443 |
|dmd.metaservices.microsoft.com:80 |
|admin.core.windows.net:443 |
|azureprofileruploads.blob.core.windows.net:443 |
|azureprofileruploads2.blob.core.windows.net:443 |
|azureprofileruploads3.blob.core.windows.net:443 |
|azureprofileruploads4.blob.core.windows.net:443 |
|azureprofileruploads5.blob.core.windows.net:443 |

#### <a name="wildcard-httphttps-dependencies"></a>Jokertecken HTTP/HTTPS-beroenden 

| Slutpunkt |
|----------|
|gr-Prod-\*.cloudapp.net:443 |
| \*.management.azure.com:443 |
| \*.update.microsoft.com:443 |
| \*.windowsupdate.microsoft.com:443 |

#### <a name="linux-dependencies"></a>Linux-beroenden 

| Slutpunkt |
|----------|
|wawsinfraprodbay063.blob.core.windows.net:443 |
|registry-1.docker.io:443 |
|auth.docker.io:443 |
|production.cloudflare.docker.com:443 |
|download.docker.com:443 |
|us.archive.ubuntu.com:80 |
|download.mono-project.com:80 |
|packages.treasuredata.com:80|
|security.ubuntu.com:80 |

<!--Image references-->
[1]: ./media/firewall-integration/firewall-apprule.png
[2]: ./media/firewall-integration/firewall-serviceendpoints.png
[3]: ./media/firewall-integration/firewall-ntprule.png
[4]: ./media/firewall-integration/firewall-routetable.png
[5]: ./media/firewall-integration/firewall-topology.png
