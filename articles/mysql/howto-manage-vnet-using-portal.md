---
title: Skapa och hantera Azure Database för MySQL-VNet-tjänstslutpunkter och regler med hjälp av Azure portal | Microsoft Docs
description: Skapa och hantera Azure Database för MySQL-VNet-tjänstslutpunkter och regler med hjälp av Azure portal
author: bolzmj
ms.author: mbolz
ms.service: mysql
ms.topic: conceptual
ms.date: 10/22/2018
ms.openlocfilehash: b932ad3148bb1f5ddd229c2674f8a20bbfe1afa8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61458578"
---
# <a name="create-and-manage-azure-database-for-mysql-vnet-service-endpoints-and-vnet-rules-by-using-the-azure-portal"></a>Skapa och hantera Azure Database för MySQL-VNet-tjänstslutpunkter och VNet-regler med hjälp av Azure portal
Virtuella nätverk (VNet) services-slutpunkter och regler kan du utöka det privata adressutrymmet för ett virtuellt nätverk till Azure Database för MySQL-server. En översikt över Azure Database för MySQL-VNet-tjänstslutpunkter, inklusive begränsningar, finns i [Azure Database för MySQL Server VNet-tjänstslutpunkter](concepts-data-access-and-security-vnet.md). VNet-tjänstslutpunkter är tillgängliga i alla regioner som stöds för Azure Database for MySQL.

> [!NOTE]
> Stöd för VNet-tjänstslutpunkter är endast för generell användning och Minnesoptimerad servrar.
> VNet peering om trafik flödar genom en gemensam Gateway för virtuellt nätverk med tjänstslutpunkter och ska flöda till peer-datorn kan du skapa en ACL/VNet-regel som tillåter Azure Virtual Machines i Gateway virtuella nätverk till Azure Database for MySQL-server.

## <a name="create-a-vnet-rule-and-enable-service-endpoints-in-the-azure-portal"></a>Skapa en VNet-regel och aktivera tjänstslutpunkter i Azure portal

1. På sidan MySQL server under inställningar klickar du **anslutningssäkerhet** att öppna fönstret anslutningssäkerhet för Azure Database för MySQL. 

2. Se till att Tillåt åtkomst till Azure-tjänster kontroll har angetts till **OFF**.

> [!Important]
> Om du lämnar den kontroll som är inställt på på godkänner kommunikation från alla undernät i Azure Database för MySQL-servern. Lämna inställt på på kontrollen kan vara långa åtkomst från en säkerhetssynpunkt. Microsoft Azure Virtual Network service endpoint-funktionen, tillsammans med virtuellt nätverk regeln funktionen i Azure Database för MySQL kan tillsammans minska ytan din säkerhet.

3. Klicka på **+ att lägga till befintligt virtuellt nätverk**. Om du inte har ett befintligt VNet kan du klicka **+ Skapa nytt virtuellt nätverk** att skapa en. Gå till [Snabbstart: Skapa ett virtuellt nätverk med Azure portal](../virtual-network/quick-create-portal.md)

   ![Azure portal – Klicka på anslutningssäkerhet](./media/howto-manage-vnet-using-portal/1-connection-security.png)

4. Ange ett VNet-Regelnamn, Välj prenumeration, virtuellt nätverk och namn på undernät och klicka sedan på **aktivera**. Detta aktiverar automatiskt VNet-tjänstslutpunkter på undernät med den **Microsoft.SQL** tjänsttaggen.

   ![Azure portal – konfigurera virtuellt nätverk](./media/howto-manage-vnet-using-portal/2-configure-vnet.png)

   Kontot måste ha tillräcklig behörighet för att skapa ett virtuellt nätverk och tjänstslutpunkten.

   Tjänstslutpunkter kan konfigureras i virtuella nätverk separat, av en användare med skrivbehörighet för det virtuella nätverket.
    
   Om du vill skydda Azure-tjänstresurser i ett virtuellt nätverk, måste användaren ha behörighet att ”Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/” för undernäten läggas till. Den här behörigheten ingår som standard i de inbyggda tjänstadministratörsrollerna och kan ändras genom att skapa anpassade roller.
    
   Lär dig mer om [inbyggda roller](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles) och att tilldela specifika behörigheter till [anpassade roller](https://docs.microsoft.com/azure/active-directory/role-based-access-control-custom-roles).
    
   Virtuella nätverk och Azure-tjänstresurser kan finnas i samma eller olika prenumerationer. Om VNet och Azure-tjänstresurser finns i olika prenumerationer bör resurserna finnas under samma Active Directory (AD)-klient.

   > [!IMPORTANT]
   > Vi rekommenderar starkt att den här artikeln om tjänsten endpoint konfigurationer och överväganden innan du konfigurerar tjänstslutpunkter. **Tjänstslutpunkt för virtuellt nätverk:** En [tjänstslutpunkt för virtuellt nätverk](../virtual-network/virtual-network-service-endpoints-overview.md) är ett undernät som egenskaper innehåller en eller flera formella Azure-tjänst-typnamn. Services-slutpunkter för virtuellt nätverk använder namnet på tjänsten **Microsoft.Sql**, som refererar till Azure-tjänsten med namnet SQL-databas. Den här tjänsttaggen gäller även för Azure SQL Database, Azure Database för PostgreSQL och MySQL-tjänster. Det är viktigt att tänka på när du använder den **Microsoft.Sql** servicetagg till en VNet-tjänstslutpunkt konfigurerar den för alla Azure Database-tjänster, inklusive Azure SQL Database, Azure Database for PostgreSQL-tjänstens slutpunkt trafik och Azure Database for MySQL-servrar i undernät. 
   > 

5. När du har aktiverat, klickar du på **OK** så ser du att VNet-tjänstslutpunkter aktiveras tillsammans med en VNet-regel.

   ![VNet-tjänstslutpunkter och VNet-regel skapas](./media/howto-manage-vnet-using-portal/3-vnet-service-endpoints-enabled-vnet-rule-created.png)

## <a name="next-steps"></a>Nästa steg
- På samma sätt kan du skapa skript till [aktivera VNet tjänstens slutpunkter och skapa en VNET-regel för Azure Database for MySQL med Azure CLI](howto-manage-vnet-using-cli.md).
- Hjälp med att ansluta till en Azure Database for MySQL-server finns i [anslutningsbibliotek för Azure Database for MySQL](./concepts-connection-libraries.md)
