---
title: Hur du använder Azure API Management med virtuella nätverk
description: Lär dig hur du konfigurerar en anslutning till ett virtuellt nätverk i Azure API Management och åtkomst till webbtjänster via den.
services: api-management
documentationcenter: ''
author: vlvinogr
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/01/2019
ms.author: apimpm
ms.openlocfilehash: 73785422a7c45a12671e6cd53da89609190a8352
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66243291"
---
# <a name="how-to-use-azure-api-management-with-virtual-networks"></a>Hur du använder Azure API Management med virtuella nätverk
Azure-nätverk (Vnet) kan du placera någon av dina Azure-resurser i ett icke-internet-dirigerbara nätverk som du styr åtkomst till. Dessa nätverk kan sedan anslutas till ditt lokala nätverk med olika VPN-teknologier. Om du vill veta börjar mer om Azure Virtual Networks med den här informationen: [Översikt över Azure Virtual Network](../virtual-network/virtual-networks-overview.md).

Azure API Management kan distribueras i virtuella nätverk (VNET), så att den kan komma åt backend-tjänster i nätverket. Developer-portalen och API-gateway kan konfigureras för att vara tillgänglig från Internet eller endast i det virtuella nätverket.

> [!NOTE]
> Azure API Management har stöd för både klassiska och Azure Resource Manager-nätverk.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [premium-dev.md](../../includes/api-management-availability-premium-dev.md)]

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill utföra stegen som beskrivs i den här artikeln, måste du ha:

+ En aktiv Azure-prenumeration.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ En APIM-instansen. Mer information finns i [skapa en Azure API Management-instans](get-started-create-service-instance.md).

## <a name="enable-vpn"> </a>Aktivera anslutning mellan virtuella nätverk

### <a name="enable-vnet-connectivity-using-the-azure-portal"></a>Aktivera VNET-anslutning med Azure portal

1. Navigera till APIM-instansen i den [Azure-portalen](https://portal.azure.com/).
2. Välj **virtuellt nätverk**.
3. Konfigurera API Management-instans som ska distribueras i ett virtuellt nätverk.

    ![Meny för virtuellt nätverk med API Management][api-management-using-vnet-menu]
4. Välj typ av lägga till:

   * **Externa**: portalen för API Management gateway och utvecklarportal är tillgängliga från det offentliga internet via en extern belastningsutjämnare. Gatewayen kan komma åt resurser i det virtuella nätverket.

     ![Offentlig peering][api-management-vnet-public]

   * **Interna**: portalen för API Management gateway och utvecklarportal är enbart tillgänglig från det virtuella nätverket via en intern belastningsutjämnare. Gatewayen kan komma åt resurser i det virtuella nätverket.

     ![Privat peering][api-management-vnet-private]

     Du kommer nu se en lista över alla regioner där API Management-tjänsten har etablerats. Välj ett virtuellt nätverk och undernät för varje region. I listan fylls med både klassiska och Resource Manager-nätverk som är tillgängliga i dina Azure-prenumerationer som har konfigurerats i den region som du konfigurerar.

     > [!NOTE]
     > **Tjänsteslutpunkt** innehåller Gateway/Proxy, Azure-portalen, Developer-portalen, GIT och direkt av programvara i ovanstående diagram.
     > **Hanteringsslutpunkten** i ovanstående diagram är den slutpunkt som finns på tjänsten för att hantera konfiguration via Azure portal och Powershell.
     > Observera också, att, även om diagrammet visar IP-adresser för olika, API Management-tjänsten **endast** svarar på dess konfigurerade värdnamn.

     > [!IMPORTANT]
     > När du distribuerar en Azure API Management-instans till ett Resource Manager-VNET, måste tjänsten vara i ett dedikerat undernät som innehåller inga andra resurser förutom Azure API Management-instanser. Vid ett försök att distribuera en Azure API Management-instans till ett Resource Manager-VNET-undernät som innehåller andra resurser kan distributionen misslyckas.
     >

     ![Välj VPN][api-management-setup-vpn-select]

5. Klicka på **spara** i det övre navigeringsfältet.
6. Klicka på **tillämpa nätverkskonfiguration** i det övre navigeringsfältet.

> [!NOTE]
> VIP-adressen för API Management-instans ändrar varje gång VNET är aktiverat eller inaktiverat.
> VIP-adressen ändras också när API Management har flyttats från **externa** till **internt** eller tvärtom
>

> [!IMPORTANT]
> Om du tar bort API Management från ett virtuellt nätverk eller ändra den som den har distribuerats i kan det tidigare virtuella nätverket vara låst för upp till två timmar. Under den här perioden kommer det inte vara möjligt att ta bort det virtuella nätverket eller distribuera en ny resurs till den.

## <a name="enable-vnet-powershell"> </a>Aktivera VNET-anslutning med PowerShell-cmdletar
Du kan också aktivera VNET-anslutning med PowerShell-cmdletar

* **Skapa en API Management-tjänst i ett virtuellt nätverk**: Använd cmdleten [New AzApiManagement](/powershell/module/az.apimanagement/new-azapimanagement) att skapa en Azure API Management-tjänsten i ett virtuellt nätverk.

* **Distribuera en befintlig API Management-tjänst i ett virtuellt nätverk**: Använd cmdleten [uppdatering AzApiManagementRegion](/powershell/module/az.apimanagement/update-azapimanagementregion) att flytta en befintlig Azure API Management-tjänst i ett virtuellt nätverk.

## <a name="connect-vnet"> </a>Ansluta till en webbtjänst som finns inom ett virtuellt nätverk
När API Management-tjänsten är ansluten till det virtuella nätverket, är åtkomst till backend-tjänster inom den inte skiljer sig från att komma åt offentliga tjänster. Skriver i den lokala IP-adressen eller värdnamnet (om en DNS-server har konfigurerats för det virtuella nätverket) för webbtjänsten i den **-webbtjänstens URL** fältet när du skapar ett nytt API eller redigera en befintlig.

![Lägg till API från VPN][api-management-setup-vpn-add-api]

## <a name="network-configuration-issues"> </a>Vanliga problem för konfiguration av nätverk
Följande är en lista över vanliga felkonfigurationsproblem som kan uppstå när du distribuerar API Management-tjänsten till ett virtuellt nätverk.

* **Installationen av anpassad DNS-server**: API Management-tjänsten är beroende av flera Azure-tjänster. När API Management finns i ett virtuellt nätverk med en anpassad DNS-server, måste den matcha värdnamnen för de Azure-tjänsterna. Följ [detta](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) vägledning för installationen av anpassad DNS. Se tabellen portar och andra krav som referens.

> [!IMPORTANT]
> Om du planerar att använda en anpassad DNS-servrar för det virtuella nätverket, bör du konfigurera den **innan** distribuera API Management-tjänsten till den. Annars måste du uppdatera API Management-tjänsten varje gång du ändrar DNS-servrar genom att köra den [gäller åtgärden för konfiguration](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/ApiManagementService/ApplyNetworkConfigurationUpdates)

* **Portar som krävs för API Management**: Inkommande och utgående trafik till undernätet där API Management har distribuerats kan kontrolleras med hjälp av [Nätverkssäkerhetsgrupp][Network Security Group]. Om någon av de här portarna är otillgängliga API Management kanske inte fungerar korrekt och kan bli otillgängliga. Med en eller flera av de här portarna blockerad är ett annat vanligt felkonfiguration problem när du använder API Management med ett virtuellt nätverk.

<a name="required-ports"> </a> När en instans för API Management finns i ett virtuellt nätverk, används portarna i följande tabell.

| Källa / målportar | Direction          | Transport-protokoll |   [Tjänsttaggar](../virtual-network/security-overview.md#service-tags) <br> Källa / mål   | Syfte (*)                                                 | Typ av virtuellt nätverk |
|------------------------------|--------------------|--------------------|---------------------------------------|-------------------------------------------------------------|----------------------|
| * / 80, 443                  | Inkommande            | TCP                | INTERNET / VIRTUAL_NETWORK            | Klientkommunikation till API Management                      | Extern             |
| * / 3443                     | Inkommande            | TCP                | ApiManagement / VIRTUAL_NETWORK       | Hanteringsslutpunkten för Azure-portalen och Powershell         | Externa och interna  |
| * / 80, 443                  | Utgående           | TCP                | VIRTUAL_NETWORK / Storage             | **Beroende på Azure Storage**                             | Externa och interna  |
| * / 80, 443                  | Utgående           | TCP                | VIRTUAL_NETWORK / AzureActiveDirectory | Azure Active Directory (om tillämpligt)                   | Externa och interna  |
| * / 1433                     | Utgående           | TCP                | VIRTUAL_NETWORK / SQL                 | **Åtkomst till Azure SQL-slutpunkter**                           | Externa och interna  |
| * / 5672                     | Utgående           | TCP                | VIRTUAL_NETWORK / EventHub            | Beroende för logg till Event Hub-principen och övervakningsagent | Externa och interna  |
| * / 445                      | Utgående           | TCP                | VIRTUAL_NETWORK / Storage             | Beroende på Azure-filresurs för GIT                      | Externa och interna  |
| * / 1886                     | Utgående           | TCP                | VIRTUAL_NETWORK / INTERNET            | Krävs för att publicera hälsostatus till Resource Health          | Externa och interna  |
| * / 443                     | Utgående           | TCP                | VIRTUAL_NETWORK / AzureMonitor         | Publicera diagnostik loggar och mått                        | Externa och interna  |
| * / 25                       | Utgående           | TCP                | VIRTUAL_NETWORK / INTERNET            | Ansluta till SMTP-relä för att skicka e-post                    | Externa och interna  |
| * / 587                      | Utgående           | TCP                | VIRTUAL_NETWORK / INTERNET            | Ansluta till SMTP-relä för att skicka e-post                    | Externa och interna  |
| * / 25028                    | Utgående           | TCP                | VIRTUAL_NETWORK / INTERNET            | Ansluta till SMTP-relä för att skicka e-post                    | Externa och interna  |
| * / 6381 - 6383              | Inkommande och utgående | TCP                | VIRTUAL_NETWORK / VIRTUAL_NETWORK     | Få åtkomst till Azure Cache för Redis-instanser mellan RoleInstances          | Externa och interna  |
| * / \*                        | Inkommande            | TCP                | AZURE_LOAD_BALANCER / VIRTUAL_NETWORK | Belastningsutjämnare för Azure-infrastrukturen                          | Externa och interna  |

>[!IMPORTANT]
> Portar som de *syfte* är **fetstil** krävs för API Management-tjänsten ska distribueras. Blockera andra portar men medför försämring i möjligheten att använda och övervaka tjänsten som körs.

+ **SSL-funktionen**: Om du vill aktivera Kedjebyggande för SSL-certifikat och verifiering API Management måste-tjänsten utgående nätverksanslutning till ocsp.msocsp.com, mscrl.microsoft.com och crl.microsoft.com. Det här beroendet är inte nödvändigt, om alla certifikat som du överför till API Management innehåller fullständig kedja till rot-CA: N.

+ **DNS-åtkomst**: Utgående åtkomst på port 53 krävs för kommunikation med DNS-servrar. Om en anpassad DNS-server finns på den andra änden av en VPN-gateway, måste DNS-servern vara nåbar från det undernät som är värd för API Management.

+ **Mått och hälsoövervakning**: Utgående nätverksanslutning till Azure Monitoring slutpunkter, vilket löser under följande domäner:

    | Azure Environment | Slutpunkter                                                                                                                                                                                                                                                                                                                                                              |
    |-------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Azure Public      | <ul><li>prod.warmpath.msftcloudes.com</li><li>shoebox2.metrics.nsatc.net</li><li>prod3.metrics.nsatc.net</li><li>prod3-black.prod3.metrics.nsatc.net</li><li>prod3-red.prod3.metrics.nsatc.net</li><li>prod.warm.ingestion.msftcloudes.com</li><li>`azure region`. warm.ingestion.msftcloudes.com där `East US 2` är eastus2.warm.ingestion.msftcloudes.com</li></ul> |
    | Azure Government  | <ul><li>fairfax.warmpath.usgovcloudapi.net</li><li>shoebox2.metrics.nsatc.net</li><li>prod3.metrics.nsatc.net</li></ul>                                                                                                                                                                                                                                                |
    | Azure Kina       | <ul><li>mooncake.warmpath.chinacloudapi.cn</li><li>shoebox2.metrics.nsatc.net</li><li>prod3.metrics.nsatc.net</li></ul>                                                                                                                                                                                                                                                |

+ **SMTP-relä**: Utgående nätverksanslutning för SMTP-relä som matchar under värden `smtpi-co1.msn.com`, `smtpi-ch1.msn.com`, `smtpi-db3.msn.com`, `smtpi-sin.msn.com` och `ies.global.microsoft.com`

+ **Utvecklarportalen CAPTCHA**: Utgående nätverksanslutning för developer-portalen CAPTCHA som matchar under värden `client.hip.live.com`.

+ **Azure-portalen diagnostik**: Aktivera flödet av diagnostikloggar från Azure-portalen när du använder API Management-tillägget från i ett virtuellt nätverk, utgående åtkomst till `dc.services.visualstudio.com` på port 443 krävs. Det underlättar vid felsökning av problem kan du står inför när du använder tillägget.

+ **Tvingad tunneltrafik trafik till en lokal brandväggen med hjälp av Express Route eller nätverket virtuell installation**: En vanlig kund-konfiguration är att definiera egna standardväg (0.0.0.0/0) som tvingar all trafik från API Management delegerad undernätet till flow via en brandvägg på plats eller till en virtuell nätverksinstallation. Det här flödet i nätverkstrafiken delar utan undantag anslutning med Azure API Management eftersom den utgående trafiken är antingen blockerade lokalt eller NAT skulle med ett okänt uppsättning adresser som inte längre att fungera med olika Azure-slutpunkter. Lösningen måste du göra några saker:

  * Aktivera tjänstslutpunkter på undernät där API Management-tjänsten har distribuerats. [Tjänstslutpunkter] [ ServiceEndpoints] måste aktiveras för Azure Sql, Azure Storage, Azure EventHub och Azure ServiceBus. Aktiverar slutpunkter direkt från API Management delegerade undernätet till dessa tjänster gör att de kan använda Microsoft Azure-stamnätverk att tillhandahålla optimal routning för tjänstens trafik. Om du använder Tjänsteslutpunkter med en tvingad tunneltrafik Api Management tunneltrafik ovan Azure-tjänsterna trafik inte Tvingad. Den andra API Management service-beroende trafik blir Tvingad tunneltrafik och kan antingen förloras eller API Management-tjänsten fungerar inte korrekt.
    
  * All kontroll plan trafik från Internet till hanteringsslutpunkten för API Management-tjänsten dirigeras via en specifik uppsättning inkommande IP-adresser med API Management. När trafiken dirigeras tvingande mappas inte svaren symmetriskt tillbaka till dessa inkommande käll-IP-adresser. Om du vill ändra begränsningen måste vi lägga till följande användardefinierade vägar ([Udr][UDRs]) att styra trafik tillbaka till Azure genom att ange mål för dessa värdvägar till ”Internet”. Uppsättningen med inkommande IP-adresser för kontroll plan trafik är följande:
    
    > 13.84.189.17/32, 13.85.22.63/32, 23.96.224.175/32, 23.101.166.38/32, 52.162.110.80/32, 104.214.19.224/32, 13.64.39.16/32, 40.81.47.216/32, 51.145.179.78/32, 52.142.95.35/32, 40.90.185.46/32, 20.40.125.155/32

  * För andra API Management-tjänstberoenden som är tvingande dirigering, bör det finnas ett sätt att lösa värdnamnet och nå ut till slutpunkten. Dessa inkluderar
      - Mått och hälsoövervakning
      - Azure-portalen diagnostik
      - SMTP-relä
      - Utvecklarportalen CAPTCHA

## <a name="troubleshooting"> </a>Felsökning
* **Den inledande installationen**: När den första distributionen av API Management-tjänsten i ett undernät inte lyckas är det bäst att först distribuera en virtuell dator i samma undernät. Nästa fjärrskrivbord till den virtuella datorn och verifiera att det finns anslutning till en av varje resurs nedan i din azure-prenumeration
    * Azure Storage-blob
    * Azure SQL Database
    * Azure Storage Table

  > [!IMPORTANT]
  > När du har godkänt anslutningen, se till att ta bort alla resurser som har distribuerats i undernätet, innan du distribuerar API Management till undernätet.

* **Inkrementella uppdateringar**: När du gör ändringar i nätverket, referera till [NetworkStatus API](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/networkstatus), för att verifiera att API Management-tjänsten inte har förlorat åtkomst till någon av kritiska resurser som den är beroende. Statusen ska uppdateras var 15: e minut.

* **Resursnavigeringslänkar**: När du distribuerar till Resource Manager style vnet-undernät, reserverar API Management undernät, genom att skapa en länk-resursnavigeringen. Om undernätet innehåller redan en resurs från en annan leverantör, distributionen ska **misslyckas**. När du flyttar en API Management-tjänsten till ett annat undernät eller ta bort det, kommer vi på samma sätt kan ta bort den resursnavigeringslänken.

## <a name="subnet-size"> </a> Nödvändiga storleken för undernät
Azure reserverar vissa IP-adresser i varje undernät och det går inte att använda dessa adresser. Första och sista IP-adresserna i undernäten är reserverade för protokollöverensstämmelse, tillsammans med tre fler adresser som används för Azure-tjänster. Mer information finns i [finns det några begränsningar med IP-adresser inom dessa undernät?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)

Förutom IP-adresser som används av infrastrukturen som virtuella Azure-nätverket, använder varje Api Management-instans i undernätet två IP-adresser per enhet för Premium-SKU eller en IP-adress i Developer-SKU: n. Varje instans reserverar ytterligare IP-adress för den externa belastningsutjämnaren. När du distribuerar i internt vnet kräver ytterligare IP-adress för den interna belastningsutjämnaren.

Beräkningen ovan minimistorleken på undernätet, där du kan distribuera API Management är /29 som ger tre IP-adresser.

## <a name="routing"> </a> Routning
+ En belastningsutjämnad offentlig IP-adress (VIP) kommer att reserveras för att ge åtkomst till alla tjänstslutpunkter.
+ En IP-adress från ett undernät på en IP-adressintervall (DIP) används för att komma åt resurser i det virtuella nätverket och en offentlig IP-adress (VIP) som används för att få åtkomst till resurser utanför det virtuella nätverket.
+ Belastningsutjämnad offentlig IP-adressen finns på bladet översikt/Essentials i Azure-portalen.

## <a name="limitations"> </a>Begränsningar
* Ett undernät som innehåller API Management-instanser får inte innehålla några andra Azure-resurstyper.
* Undernätet och API Management-tjänsten måste vara i samma prenumeration.
* Ett undernät som innehåller API Management-instanser kan inte flyttas mellan prenumerationer.
* För flera regioner API Management-distributioner konfigurerad i läget för internt virtuellt nätverk, ansvarar användarna för att hantera belastningsutjämning i flera regioner som de äger routning.
* Anslutningen från en resurs i ett globalt peer-kopplade VNET i en annan region till API Management-tjänsten internt läge fungerar inte på grund av plattformsbegränsning. Mer information finns i [resurser i ett virtuellt nätverk kan inte kommunicera med Azure intern belastningsutjämnare i peerkopplade virtuella nätverk](../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints)


## <a name="related-content"> </a>Relaterat innehåll
* [Anslut ett virtuellt nätverk till serverdelen med Vpn-Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md#s2smulti)
* [Ansluta ett virtuellt nätverk från olika distributionsmodeller](../vpn-gateway/vpn-gateway-connect-different-deployment-models-powershell.md)
* [Hur du använder API: et Inspector att spåra anropar i Azure API Management](api-management-howto-api-inspector.md)
* [Virtual Network Faq](../virtual-network/virtual-networks-faq.md)
* [Tjänsttaggar](../virtual-network/security-overview.md#service-tags)

[api-management-using-vnet-menu]: ./media/api-management-using-with-vnet/api-management-menu-vnet.png
[api-management-setup-vpn-select]: ./media/api-management-using-with-vnet/api-management-using-vnet-select.png
[api-management-setup-vpn-add-api]: ./media/api-management-using-with-vnet/api-management-using-vnet-add-api.png
[api-management-vnet-private]: ./media/api-management-using-with-vnet/api-management-vnet-internal.png
[api-management-vnet-public]: ./media/api-management-using-with-vnet/api-management-vnet-external.png

[Enable VPN connections]: #enable-vpn
[Connect to a web service behind VPN]: #connect-vpn
[Related content]: #related-content

[UDRs]: ../virtual-network/virtual-networks-udr-overview.md
[Network Security Group]: ../virtual-network/security-overview.md
[ServiceEndpoints]: ../virtual-network/virtual-network-service-endpoints-overview.md
[ServiceTags]: ../virtual-network/security-overview.md#service-tags
