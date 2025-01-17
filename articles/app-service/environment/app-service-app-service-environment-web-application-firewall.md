---
title: Konfigurera en brandvägg för webbaserade program (WAF) i en App Service-miljö – Azure
description: Lär dig hur du konfigurerar en brandvägg för webbaserade program framför din App Service-miljö.
services: app-service\web
documentationcenter: ''
author: naziml
manager: erikre
editor: jimbe
ms.assetid: a2101291-83ba-4169-98a2-2c0ed9a65e8d
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/03/2018
ms.author: naziml
ms.custom: seodec18
ms.openlocfilehash: c1930777f44266755f20400d063ec938ee631adb
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62130662"
---
# <a name="configuring-a-web-application-firewall-waf-for-app-service-environment"></a>Konfigurera en brandvägg för webbaserade program (WAF) för en App Service-miljö
## <a name="overview"></a>Översikt

Brandväggar för webbaserade program (WAF) skyddar dina webbprogram genom att kontrollera ingående webbtrafik för att blockera SQL-inmatningar, skriptkörning över flera webbplatser, överföring av skadlig kod och program-DDoS och andra attacker. De kontrollerar även svar från backend-webbservrar för dataförlustskydd (DLP). I kombination med den isolering och ytterligare skalning som tillhandahålls av App Service-miljöerna är det här en perfekt miljö att ha affärskritiska webbprogram som måste klara av skadliga begäranden och hög trafik. Azure tillhandahåller en WAF-funktion med [Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction).  Du kan läsa om hur du integrerar din App Service-miljö med en Application Gateway i dokumentet [Integrera din ILB ASE med en Application Gateway](https://docs.microsoft.com/azure/app-service/environment/integrate-with-application-gateway).

Det finns flera alternativ utöver Azure Application Gateway, till exempel [Barracuda WAF for Azure](https://www.barracuda.com/programs/azure), på [Azure Marketplace](https://azure.microsoft.com/marketplace/partners/barracudanetworks/waf-byol/). I resten av dokumentet fokuserar vi på hur du integrerar din App Service-miljö med en Barracuda WAF-enhet.

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)] 

## <a name="setup"></a>Konfiguration
I det här dokumentet konfigurerar vi App Service-miljön bakom flera belastningsutjämnade instanser av Barracuda WAF så att endast trafik från WAF kan nå App Service-miljön och att det inte går att komma åt miljön från DMZ. Vi har också Azure Traffic Manager framför Barracuda WAF-instanserna för att belastningsutjämna över Azure-datacenter och -regioner. Ett översiktsdiagram över hur konfigurationen skulle se ut:

![Arkitektur][Architecture] 

> [!NOTE]
> Med introduktionen av [ILB-stöd för App Service-miljön](app-service-environment-with-internal-load-balancer.md) kan du konfigurera ASE så att miljön inte kan nås från DMZ utan endast är tillgänglig för det privata nätverket. 
> 
> 

## <a name="configuring-your-app-service-environment"></a>Konfigurera App Service-miljön
Information om hur du konfigurerar App Service-miljön finns i [vår dokumentation](app-service-web-how-to-create-an-app-service-environment.md). När du har skapat en App Service-miljö kan du skapa webbappar, API-appar och [mobilappar](../../app-service-mobile/app-service-mobile-value-prop.md) i den här miljön, så skyddas de bakom den brandvägg som vi konfigurerar i nästa avsnitt.

## <a name="configuring-your-barracuda-waf-cloud-service"></a>Konfigurera Barracuda WAF-molntjänsten
Barracuda har en [detaljerad artikel](https://campus.barracuda.com/product/webapplicationfirewall/article/WAF/DeployWAFInAzure) om hur du distribuerar WAF på en virtuell dator i Azure. Men eftersom vi vill ha redundans och inte införa en felkritisk systemdel distribuerar du minst två WAF VM-instanser i samma molntjänst när du följer dessa instruktioner.

### <a name="adding-endpoints-to-cloud-service"></a>Lägga till slutpunkter i molntjänsten
När du har två eller fler WAF VM-instanser i din molntjänst kan du använda [Azure Portal](https://portal.azure.com/) för att lägga till HTTP- och HTTPS-slutpunkter som används av ditt program enligt följande bild:

![Konfigurera slutpunkt][ConfigureEndpoint]

Om du använder andra slutpunkter i dina program ser du till att lägga till dem också i den här listan. 

### <a name="configuring-barracuda-waf-through-its-management-portal"></a>Konfigurera Barracuda WAF via dess hanteringsportal
För Barracuda WAF används TCP-port 8000 för konfiguration via hanteringsportalen. Om du har flera WAF VM-instanser måste du upprepa dessa steg för varje VM-instans. 

> [!NOTE]
> När du är klar med WAF-konfigurationen tar du bort TCP/8000-slutpunkten från alla WAF VM för att skydda WAF.
> 
> 

Lägg till hanteringsslutpunkten enligt följande bild för att konfigurera Barracuda WAF.

![Lägga till hanteringsslutpunkt][AddManagementEndpoint]

Gå till hanteringsslutpunkten i din molntjänst via en webbläsare. Om din molntjänst till exempel heter test.cloudapp.net når du slutpunkten genom att gå till `http://test.cloudapp.net:8000`. Du bör se en inloggningssida som på bilden nedan så att du kan logga in med de autentiseringsuppgifter som du angav när du konfigurerade WAF VM.

![Inloggningssida för hanteringspanel][ManagementLoginPage]

När du har loggat in visas en instrumentpanel som ser ut som på bilden nedan. På instrumentpanelen visas grundläggande statistik om WAF-skyddet.

![Hanteringspanel][ManagementDashboard]

Om du klickar på fliken **Tjänster** kan du konfigurera WAF för de tjänster som skyddas av brandväggen. Mer information om hur du konfigurerar Barracuda WAF finns i [Barracudas dokumentation](https://techlib.barracuda.com/waf/getstarted1). I följande exempel visas en Azure App Service-app som har konfigurerats med trafik via HTTP och HTTPS.

![Lägga till tjänster att hantera][ManagementAddServices]

> [!NOTE]
> Beroende på hur dina program har konfigurerats och vilka funktioner som används i din Azure App Service-miljö behöver du vidarebefordra trafik för andra TCP-portar än 80 och 443, till exempel om du använder IP SSL för en App Service-app. En lista över nätverksportar som används i App Service-miljöer finns i [avsnittet om nätverksportar i dokumentationen om hur du kontrollerar inkommande trafik](app-service-app-service-environment-control-inbound-traffic.md).
> 
> 

## <a name="configuring-microsoft-azure-traffic-manager-optional"></a>Konfigurera Microsoft Azure Traffic Manager (valfritt)
Om ditt program är tillgängligt i flera regioner vill du balansutjämna dem bakom [Azure Traffic Manager](../../traffic-manager/traffic-manager-overview.md). Det gör du genom att lägga till en slutpunkt i [Azure Portal](https://portal.azure.com) med molntjänstnamnet för din WAF i Traffic Manager-profilen (se bilden nedan). 

![Traffic Manager-slutpunkt][TrafficManagerEndpoint]

Om ditt program kräver autentisering måste du se till att du har några resurser som inte kräver autentisering som Traffic Manager kan pinga för att se om ditt program är tillgängligt. Du kan konfigurera URL:en på sidan **Konfiguration** i [Azure Portal](https://portal.azure.com) (se följande bild):

![Konfigurera Traffic Manager][ConfigureTrafficManager]

För att kunna vidarebefordra Traffic Manager-ping från WAF till ditt program måste du konfigurera Website Translations (webbplatsöversättningar) på Barracuda WAF för att vidarebefordra trafik till ditt program enligt följande exempel:

![Website Translations (webbplatsöversättningar)][WebsiteTranslations]

## <a name="securing-traffic-to-app-service-environment-using-network-security-groups-nsg"></a>Skydda trafik till App Service-miljön med nätverkssäkerhetsgrupper
I [dokumentationen om hur du kontrollerar inkommande trafik](app-service-app-service-environment-control-inbound-traffic.md) finns information om hur du begränsar trafiken till din App Service-miljö från endast WAF med hjälp av VIP-adressen för din molntjänst. Här är ett Powershell-exempelkommando för att utföra detta för TCP-port 80.

    Get-AzureNetworkSecurityGroup -Name "RestrictWestUSAppAccess" | Set-AzureNetworkSecurityRule -Name "ALLOW HTTP Barracuda" -Type Inbound -Priority 201 -Action Allow -SourceAddressPrefix '191.0.0.1'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP

Ersätt SourceAddressPrefix med den virtuella IP-adressen (VIP) för molntjänsten för din WAF.

> [!NOTE]
> VIP för din molntjänst ändras när du tar bort och återskapar molntjänsten. Se till att uppdatera IP-adressen i nätverksresursgruppen när du har gjort det. 
> 
> 

<!-- IMAGES -->
[Architecture]: ./media/app-service-app-service-environment-web-application-firewall/Architecture.png
[ConfigureEndpoint]: ./media/app-service-app-service-environment-web-application-firewall/ConfigureEndpoint.png
[AddManagementEndpoint]: ./media/app-service-app-service-environment-web-application-firewall/AddManagementEndpoint.png
[ManagementAddServices]: ./media/app-service-app-service-environment-web-application-firewall/ManagementAddServices.png
[ManagementDashboard]: ./media/app-service-app-service-environment-web-application-firewall/ManagementDashboard.png
[ManagementLoginPage]: ./media/app-service-app-service-environment-web-application-firewall/ManagementLoginPage.png
[TrafficManagerEndpoint]: ./media/app-service-app-service-environment-web-application-firewall/TrafficManagerEndpoint.png
[ConfigureTrafficManager]: ./media/app-service-app-service-environment-web-application-firewall/ConfigureTrafficManager.png
[WebsiteTranslations]: ./media/app-service-app-service-environment-web-application-firewall/WebsiteTranslations.png
