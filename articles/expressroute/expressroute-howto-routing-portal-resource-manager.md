---
title: 'Konfigurera peering för en krets - ExpressRoute: Azure | Microsoft Docs'
description: Den här artikeln beskrivs stegen för att skapa och etablera ExpressRoute privat och Microsofts peering. Den här artikeln visar även hur du kontrollerar status, uppdatera eller ta bort peerings för en krets.
services: expressroute
author: mialdrid
ms.service: expressroute
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: mialdrid
ms.custom: seodec18
ms.openlocfilehash: 40ecdb3f83dba741d1430a912a3f17500a36da6e
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/01/2019
ms.locfileid: "67484337"
---
# <a name="create-and-modify-peering-for-an-expressroute-circuit"></a>Skapa och ändra peering för en ExpressRoute-krets

Den här artikeln hjälper dig att skapa och hantera routningskonfiguration för en Azure Resource Manager (ARM) ExpressRoute-krets med Azure-portalen. Du kan också kontrollera status, uppdatera eller ta bort och Avetablerar peerings för en ExpressRoute-krets. Om du vill använda en annan metod för att arbeta med din krets väljer du en artikel i listan nedan:

> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-routing-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-routing-arm.md)
> * [Azure CLI](howto-routing-cli.md)
> * [Video - privat peering](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit)
> * [Video - offentlig peering](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-public-peering-for-your-expressroute-circuit)
> * [Video - Microsoft-peering](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit)
> * [PowerShell (klassisk)](expressroute-howto-routing-classic.md)
> 

Du kan konfigurera privat Azure och Microsoft-peering för en ExpressRoute-krets (Azures offentliga peering är inaktuellt för nya kretsar). Du kan konfigurera peerings i valfri ordning. Dock måste du se till att du slutför konfigurationen av en peering i taget. Läs mer om routning domäner och peerings [om kretsar och peerings](expressroute-circuit-peerings.md).

## <a name="configuration-prerequisites"></a>Förutsättningar för konfiguration

* Kontrollera att du har granskat sidorna [Förutsättningar](expressroute-prerequisites.md), [Routningskrav](expressroute-routing.md) sidan och [Arbetsflöden](expressroute-workflows.md) sidan innan du påbörjar konfigurationen.
* Du måste ha en aktiv ExpressRoute-krets. Följ anvisningarna för att [Skapa en ExpressRoute-krets](expressroute-howto-circuit-portal-resource-manager.md) och aktivera kretsen av anslutningsprovidern innan du fortsätter. ExpressRoute-kretsen måste vara i ett etablerat och aktiverat tillstånd för att kunna konfigurera peering (s). 
* Om du planerar att använda en delad nyckel/MD5-hash, måste du använda det på båda sidorna av tunneln och begränsa antalet alfanumeriska tecken till högst 25. Specialtecken stöds inte. 

Dessa anvisningar gäller endast för kretsar som skapats med tjänstleverantörer som erbjuder tjänster för Layer 2-anslutning. Om du använder en leverantör av tjänster som erbjuder hanterade Layer 3-tjänster (vanligtvis en IPVPN, t.ex. MPLS), anslutningsleverantören konfigurerar och hanterar routning åt dig. 

> [!IMPORTANT]
> Vi gör för närvarande inte reklam för peerings som konfigurerats av tjänstleverantörer via tjänsthanteringsportalen. Vi arbetar på att kunna aktivera den här funktionen snart. Kontrollera med din tjänstleverantör innan du konfigurerar BGP-peerings.
> 
> 

## <a name="msft"></a>Microsoft-peering

Det här avsnittet hjälper dig att skapa, hämta, uppdatera och ta bort Microsofts peeringskonfiguration för en ExpressRoute-krets.

> [!IMPORTANT]
> Microsoft-peering av ExpressRoute-kretsar som konfigurerades före den 1 augusti 2017 kommer att ha alla service-prefix som annonseras via Microsoft-peering, även om flödesfilter inte har definierats. Microsoft-peering av ExpressRoute-kretsar som är konfigurerade på eller efter den 1 augusti 2017 har inte alla prefix annonseras förrän ett flödesfilter är kopplad till kretsen. Mer information finns i [konfigurera ett flödesfilter för Microsoft-peering](how-to-routefilter-powershell.md).
> 
> 

### <a name="to-create-microsoft-peering"></a>Så här skapar du Microsoft-peering

1. Konfigurera ExpressRoute-kretsen. Kontrollera den **Providerstatus** att se till att kretsen är helt etablerad av anslutningsprovidern innan du fortsätter.

   Om din anslutningsleverantör erbjuder hanteringstjänster till Layer 3, kan du be din anslutningsleverantör för att Microsoft peering åt dig. I så fall behöver du inte följa anvisningarna i nästa avsnitt. Men om anslutningsprovidern inte kan hantera routning åt dig, när du har skapat kretsen, fortsätter du med dessa steg.

   **Circuit - Providerstatus: Inte etablerats**

    [![](./media/expressroute-howto-routing-portal-resource-manager/not-provisioned-m.png "Providerstatus: Inte etablerats")](./media/expressroute-howto-routing-portal-resource-manager/not-provisioned-m-lightbox.png#lightbox)

   **Circuit - Providerstatus: etablerad**

   [![](./media/expressroute-howto-routing-portal-resource-manager/provisioned-m.png "Providerstatus = etablerad")](./media/expressroute-howto-routing-portal-resource-manager/provisioned-m-lightbox.png#lightbox)
2. Konfigurera Microsoft-peering för kretsen. Kontrollera att du har följande information innan du fortsätter.

   * Ett /30 undernät för den primära länken. Detta måste vara ett giltigt offentligt IPv4-prefix som du äger och har registrerat i en RIR/IR. Från det här undernätet ska du tilldela första riktlinje IP-adressen till routern som Microsoft använder den andra riktlinje IP-Adressen för dess router.
   * Ett /30 undernät för den sekundära länken. Detta måste vara ett giltigt offentligt IPv4-prefix som du äger och har registrerat i en RIR/IR. Från det här undernätet ska du tilldela första riktlinje IP-adressen till routern som Microsoft använder den andra riktlinje IP-Adressen för dess router.
   * Ett giltigt VLAN-ID att upprätta denna peering på. Se till att ingen annan peering i kretsen använder samma VLAN-ID. För både primära och sekundära länkar måste du använda samma VLAN-ID.
   * AS-tal för peering. Du kan använda både 2 byte och 4 byte som AS-tal.
   * Annonserade prefix: Du måste ange en lista över alla prefix som du planerar att annonsera i BGP-sessionen. Endast offentliga IP-adressprefix accepteras. Om du planerar att skicka en uppsättning prefix, kan du skicka en kommaavgränsad lista. Dessa prefix måste vara registrerade åt dig i ett RIR/IR.
   * **Valfritt –** kund-ASN: Om du har reklamprefix som inte är registrerade på peeringens AS number, kan du ange det AS-nummer som de är registrerade.
   * Routningens registernamn: Du kan ange RIR / IR mot vilken AS-numret och prefixet är registrerade.
   * **Valfritt –** en MD5-hash om du väljer att använda en.
3. Du kan välja den peering som du vill konfigurera, som visas i följande exempel. Välj Microsofts peering-rad.

   [![Välj Microsofts peering-rad](./media/expressroute-howto-routing-portal-resource-manager/select-peering-m.png "Välj Microsofts peering-rad")](./media/expressroute-howto-routing-portal-resource-manager/select-peering-m-lightbox.png#lightbox)
4. Konfigurera Microsoft-peering. **Spara** konfigurationen när du har angett alla parametrar. Följande bild visar en exempelkonfiguration:

   ![Konfigurera Microsoft-peering](./media/expressroute-howto-routing-portal-resource-manager/configuration-m.png)

   Om din krets kommer till en ”verifiering krävs” tillstånd, måste du öppna ett supportärende för att bevisa ägarskapet för prefixen till supportteamet. Du kan öppna ett supportärende direkt från portalen, enligt följande exempel:

   ![Verifiering krävs - supportärende](./media/expressroute-howto-routing-portal-resource-manager/ticket-portal-m.png)

5. När konfigurationen har tagits emot, visas något som liknar följande bild:

   ![Peering-status: Konfigurerad](./media/expressroute-howto-routing-portal-resource-manager/configured-m.png "Peering-status: Konfigurerad")]

### <a name="getmsft"></a>Så här visar du Microsofts peering-information

Du kan visa egenskaperna för Microsoft-peering genom att välja raden för peering.

[![Visa egenskaper för Microsoft-peering](./media/expressroute-howto-routing-portal-resource-manager/view-peering-m.png "visa egenskaper")](./media/expressroute-howto-routing-portal-resource-manager/view-peering-m-lightbox.png#lightbox)
### <a name="updatemsft"></a>Att uppdatera konfigurationen för Microsoft-peering

Du kan välja raden för peering som du vill ändra, ändra peering-egenskaperna och spara ändringarna.

![Välj peering-raden](./media/expressroute-howto-routing-portal-resource-manager/update-peering-m.png)

### <a name="deletemsft"></a>Ta bort Microsoft-peering

Du kan ta bort peering-konfigurationen genom att klicka på ikonen Ta bort enligt följande bild:

![Ta bort peering](./media/expressroute-howto-routing-portal-resource-manager/delete-peering-m.png)

## <a name="private"></a>Azures privata peering

Det här avsnittet hjälper dig att skapa, hämta, uppdatera och ta bort Azures privata peeringskonfiguration för en ExpressRoute-krets.

### <a name="to-create-azure-private-peering"></a>Så här skapar du Azures privata peering

1. Konfigurera ExpressRoute-kretsen. Se till att kretsen är helt etablerad av anslutningsprovidern innan du fortsätter. 

   Om din anslutningsleverantör erbjuder hanteringstjänster till Layer 3, kan du be anslutningsleverantören aktiverar Azures privata peering åt dig. I så fall behöver du inte följa anvisningarna i nästa avsnitt. Men om anslutningsprovidern inte kan hantera routning åt dig, när du har skapat kretsen, fortsätter du med nästa steg.

   **Circuit - Providerstatus: Inte etablerats**

   [![](./media/expressroute-howto-routing-portal-resource-manager/not-provisioned-p.png "Providerstatus = inte etablerats")](./media/expressroute-howto-routing-portal-resource-manager/not-provisioned-p-lightbox.png#lightbox)

   **Circuit - Providerstatus: etablerad**

   [![](./media/expressroute-howto-routing-portal-resource-manager/provisioned-p.png "Providerstatus = etablerats")](./media/expressroute-howto-routing-portal-resource-manager/provisioned-p-lightbox.png#lightbox)

2. Konfigurera Azures privata peering för kretsen. Kontrollera att du har följande objekt innan du fortsätter med nästa steg:

   * Ett /30 undernät för den primära länken. Undernätet får inte vara en del av något adressutrymme som reserverats för virtuella nätverk. Från det här undernätet ska du tilldela första riktlinje IP-adressen till routern som Microsoft använder den andra riktlinje IP-Adressen för dess router.
   * Ett /30 undernät för den sekundära länken. Undernätet får inte vara en del av något adressutrymme som reserverats för virtuella nätverk. Från det här undernätet ska du tilldela första riktlinje IP-adressen till routern som Microsoft använder den andra riktlinje IP-Adressen för dess router.
   * Ett giltigt VLAN-ID att upprätta denna peering på. Se till att ingen annan peering i kretsen använder samma VLAN-ID. För både primära och sekundära länkar måste du använda samma VLAN-ID.
   * AS-tal för peering. Du kan använda både 2 byte och 4 byte som AS-tal. Du kan använda ett privat AS-tal för den här peeringen förutom tal från 65515 till 65520, portintervallet.
   * **Valfritt –** en MD5-hash om du väljer att använda en.
3. Välj den Azure privata peering-raden som visas i följande exempel:

   [![Välj den privata peering-raden](./media/expressroute-howto-routing-portal-resource-manager/select-peering-p.png "väljer den privata peering-raden")](./media/expressroute-howto-routing-portal-resource-manager/select-peering-p-lightbox.png#lightbox)
4. Konfigurera privat peering. **Spara** konfigurationen när du har angett alla parametrar.

   ![Konfigurera privat peering](./media/expressroute-howto-routing-portal-resource-manager/configuration-p.png)
5. När konfigurationen har tagits emot, visas något som liknar följande exempel:

   ![Spara privat peering](./media/expressroute-howto-routing-portal-resource-manager/save-p.png)

### <a name="getprivate"></a>Så här visar du Azures privata peering-information

Du kan visa egenskaperna för Azures privata peering genom att välja den peeringen.

[![Visa egenskaper för privat peering](./media/expressroute-howto-routing-portal-resource-manager/view-p.png "visa egenskaper för privat peering")](./media/expressroute-howto-routing-portal-resource-manager/view-p-lightbox.png#lightbox)

### <a name="updateprivate"></a>Att uppdatera konfigurationen av Azures privata peering

Du kan välja raden för peering och ändra peering-egenskaperna. När du har uppdaterat, spara dina ändringar.

![Uppdatera privat peering](./media/expressroute-howto-routing-portal-resource-manager/update-peering-p.png)

### <a name="deleteprivate"></a>Att ta bort Azures privata peering

Du kan ta bort peering-konfigurationen genom att välja ikonen Ta bort enligt följande bild:

> [!WARNING]
> Du måste se till att alla virtuella nätverk och ExpressRoute Global räckvidd anslutningar tas bort innan du kör det här exemplet. 
> 
> 

![ta bort privat peering](./media/expressroute-howto-routing-portal-resource-manager/delete-p.png)

## <a name="public"></a>Azures offentliga peering

Det här avsnittet hjälper dig att skapa, hämta, uppdatera och ta bort Azures offentliga peeringskonfiguration för en ExpressRoute-krets.

> [!Note]
> Azures offentliga peering är inaktuell för nya kretsar. Mer information finns i [ExpressRoute-peering](expressroute-circuit-peerings.md).
>

### <a name="getpublic"></a>Så här visar du Azures offentliga peering-information

Visa egenskaperna för Azures offentliga peering genom att välja den peeringen.

### <a name="updatepublic"></a>Att uppdatera konfigurationen av Azures offentliga peering

Välj raden för peering och ändra peering-egenskaperna.

### <a name="deletepublic"></a>Att ta bort Azures offentliga peering

Ta bort peering-konfigurationen genom att välja ikonen Ta bort.

## <a name="next-steps"></a>Nästa steg

Nästa steg [länka ett virtuellt nätverk till en ExpressRoute-krets](expressroute-howto-linkvnet-portal-resource-manager.md)
* Mer information om ExpressRoute-arbetsflöden finns i [ExpressRoute-arbetsflöden](expressroute-workflows.md).
* Mer information om krets-peering finns i [ExpressRoute-kretsar och routningsdomäner](expressroute-circuit-peerings.md).
* Mer information om hur du arbetar med virtuella nätverk finns i [Översikt över virtuella nätverk](../virtual-network/virtual-networks-overview.md).
