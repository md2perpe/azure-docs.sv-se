---
title: Azure SQL-databas – Läs frågor på repliker | Microsoft Docs
description: Azure SQL Database tillhandahåller möjligheten att belastningsutjämna skrivskyddade arbetsbelastningar med hjälp av kapaciteten för skrivskyddade repliker - kallas Lässkalning.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: sstein, carlrab
manager: craigg
ms.date: 06/03/2019
ms.openlocfilehash: 1b452fb0bac91429793f8d55e439c36c70784722
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66492726"
---
# <a name="use-read-only-replicas-to-load-balance-read-only-query-workloads"></a>Använda skrivskyddade repliker att belastningsutjämna skrivskyddad frågearbetsbelastningar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Som en del av den [arkitektur med hög tillgänglighet](./sql-database-high-availability.md#premium-and-business-critical-service-tier-availability), varje databas på tjänstnivån Premium, affärskritisk eller hyperskala etableras automatiskt med en primär replik och flera sekundära repliker. De sekundära replikerna etableras med samma beräkning storlek som den primära repliken. Den **Lässkalning** funktionen kan du belastningsutjämna SQL Database skrivskyddade arbetsbelastningar med hjälp av kapaciteten för en av de skrivskyddade replikerna istället för att dela Läs-och repliken. Det här sättet skrivskyddad arbetsbelastning isoleras från den huvudsakliga skrivskyddad arbetsbelastningen och påverkar inte dess prestanda. Funktionen är avsedd för de program som är logiskt separerade arbetsbelastningar i skrivskyddat läge, till exempel analytics. De kan få prestandafördelarna med hjälp av den här ytterligare kapacitet utan extra kostnad.

Följande diagram illustrerar den med hjälp av en affärskritisk databas.

![Skrivskyddade repliker](media/sql-database-read-scale-out/business-critical-service-tier-read-scale-out.png)

Lässkalning-funktionen är aktiverad som standard för nya Premium affärskritiska databaser, och hyperskala. Om din SQL-anslutningssträng konfigureras med `ApplicationIntent=ReadOnly`, programmet omdirigeras av gatewayen till en skrivskyddad replik av databasen. Information om hur du använder den `ApplicationIntent` egenskap, finns i [att ange Programavsikt](https://docs.microsoft.com/sql/relational-databases/native-client/features/sql-server-native-client-support-for-high-availability-disaster-recovery#specifying-application-intent).

Om du vill se till att programmet ansluter till den primära repliken oavsett den `ApplicationIntent` ställer i SQL-anslutningssträngen måste du uttryckligen inaktivera Läs skalbar när du skapar databasen eller när ändra dess konfiguration. Till exempel om du uppgraderar din databas från nivån Standard- eller generell användning till nivån Premium, affärskritisk eller hyperskala och se alla dina anslutningar fortsätter att gå till den primära repliken till, inaktivera lässkalning. Mer information om hur du inaktiverar det, finns i [aktivera och inaktivera Lässkalning](#enable-and-disable-read-scale-out).

> [!NOTE]
> Fråga Data Store, Extended Events, SQL Profiler och granska funktioner stöds inte på skrivskyddade repliker. 

## <a name="data-consistency"></a>Datakonsekvens

En av fördelarna med repliker är att alla kopior alltid är i ett konsekvent tillstånd, men vid olika tidpunkter det finnas viss liten fördröjning mellan olika repliker. Lässkalning stöder konsekvens på sessionsnivå. Det innebär om skrivskyddade sessionen återansluts när ett anslutningsfel som orsakas av repliken otillgänglighet, det kan omdirigeras till en replik som inte är 100% uppdaterade med läs-och repliken. På samma sätt, om ett program skriver data med hjälp av en Skriv-session och läser den med hjälp av en skrivskyddad session omedelbart, det är möjligt att de senaste uppdateringarna inte visas direkt på repliken. Fördröjningen orsakas av en asynkron transaction log gör om-åtgärd.

> [!NOTE]
> Replikeringsfördröjningar för regionen är låga och den här situationen är ovanligt.

## <a name="connect-to-a-read-only-replica"></a>Ansluta till en skrivskyddad replik

När du aktiverar Lässkalning för en databas, den `ApplicationIntent` alternativet i anslutningssträngen som tillhandahålls av klienten avgör om anslutningen dirigeras till skrivning repliken eller till en skrivskyddad replik. Mer specifikt om den `ApplicationIntent` värdet är `ReadWrite` (standardvärde) anslutningen dirigeras till Läs-och databasrepliken. Det här är identiska med befintliga beteende. Om den `ApplicationIntent` värdet är `ReadOnly`, anslutningen dirigeras till en skrivskyddad replik.

Till exempel ansluter följande anslutningssträng klienten till en skrivskyddad replik (Ersätt objekten i hakparenteser med rätt värden för din miljö och släppa hakparenteser):

```SQL
Server=tcp:<server>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadOnly;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

Något av följande anslutningssträngar ansluter klienten till en skrivskyddad replik (Ersätt objekten i hakparenteser med rätt värden för din miljö och släppa hakparenteser):

```SQL
Server=tcp:<server>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadWrite;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;

Server=tcp:<server>.database.windows.net;Database=<mydatabase>;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

## <a name="verify-that-a-connection-is-to-a-read-only-replica"></a>Kontrollera att det är en anslutning till en skrivskyddad replik

Du kan kontrollera om du är ansluten till en skrivskyddad replik genom att köra följande fråga. READ_ONLY när du är ansluten till en skrivskyddad replik returneras.

```SQL
SELECT DATABASEPROPERTYEX(DB_NAME(), 'Updateability')
```

> [!NOTE]
> Vid en given tidpunkt kan endast en av AlwaysON-repliker nås av ReadOnly-sessioner.

## <a name="monitoring-and-troubleshooting-read-only-replica"></a>Övervakning och felsökning skrivskyddad replik

När du är ansluten till en skrivskyddad replik, du kan komma åt den prestanda mått med hjälp av den `sys.dm_db_resource_stats` DMV. För att komma åt fråga plan statistik, använder den `sys.dm_exec_query_stats`, `sys.dm_exec_query_plan` och `sys.dm_exec_sql_text` DMV: er.

> [!NOTE]
> DMV `sys.resource_stats` returnerar CPU-användning och lagring data för den primära repliken i den logiska huvuddatabasen.


## <a name="enable-and-disable-read-scale-out"></a>Aktivera och inaktivera Lässkalning

Lässkalning är aktiverat som standard på tjänstnivåerna Premium, affärskritisk och hyperskala. Kan inte aktiveras Lässkalning på tjänstnivåerna Basic, Standard- eller generell användning. Lässkalning är automatiskt inaktiverad på hyperskala databaser som har konfigurerats med 0 repliker. 

Du kan inaktivera och återaktivera Lässkalning på enskilda databaser och elastiska pooler databaser på Premium- eller affärskritiska tjänstnivån på följande sätt.

> [!NOTE]
> Möjlighet att inaktivera Lässkalning tillhandahålls för bakåtkompatibilitet.

### <a name="azure-portal"></a>Azure Portal

Du kan hantera Läs Sacle ut-inställningen på den **konfigurera** databasbladet. 

### <a name="powershell"></a>PowerShell

Hantera Lässkalning i Azure PowerShell kräver December 2016 Azure PowerShell-versionen eller senare. Den senaste versionen av PowerShell, se [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps).

Du kan inaktivera eller återaktivera Lässkalning i Azure PowerShell genom att aktivera den [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) cmdlet och skickar det önskade värdet – `Enabled` eller `Disabled` --för den `-ReadScale` parametern. 

Inaktivera Läs skala ut på en befintlig databas (Ersätt objekten i hakparenteser med rätt värden för din miljö och släppa hakparenteser):

```powershell
Set-AzSqlDatabase -ResourceGroupName <myresourcegroup> -ServerName <myserver> -DatabaseName <mydatabase> -ReadScale Disabled
```
Inaktivera Läs skala ut på en ny databas (Ersätt objekten i hakparenteser med rätt värden för din miljö och släppa hakparenteser):

```powershell
New-AzSqlDatabase -ResourceGroupName <myresourcegroup> -ServerName <myserver> -DatabaseName <mydatabase> -ReadScale Disabled -Edition Premium
```

Återaktivera Läs skala ut på en befintlig databas (Ersätt objekten i hakparenteser med rätt värden för din miljö och släppa hakparenteser):

```powershell
Set-AzSqlDatabase -ResourceGroupName <myresourcegroup> -ServerName <myserver> -DatabaseName <mydatabase> -ReadScale Enabled
```

### <a name="rest-api"></a>REST-API

Att skapa en databas med skrivskyddade skalbar inaktiverad, eller ändra inställningen för en befintlig databas måste du använda följande metod med det `readScale` egenskapen `Enabled` eller `Disabled` som i den nedan exempelförfrågan.

```rest
Method: PUT
URL: https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{GroupName}/providers/Microsoft.Sql/servers/{ServerName}/databases/{DatabaseName}?api-version= 2014-04-01-preview
Body:
{
   "properties":
   {
      "readScale":"Disabled"
   }
}
```

Mer information finns i [databaser – skapa eller uppdatera](https://docs.microsoft.com/rest/api/sql/databases/createorupdate).

## <a name="using-tempdb-on-read-only-replica"></a>Med hjälp av TempDB på skrivskyddade replik

TempDB-databasen inte har replikerats till de skrivskyddade replikerna. Varje replik har sin egen version av TempDB-databasen som skapades när repliken skapas. Det säkerställer att TempDB är uppdateringsbar och kan ändras vid körning av din fråga. Om din skrivskyddad arbetsbelastning är beroende av TempDB-objekt, bör du skapa de här objekten som en del av din Frågeskript. 

## <a name="using-read-scale-out-with-geo-replicated-databases"></a>Med geo-replikerade databaser Lässkalning

Om du använder Lässkalning för att belastningsutjämna skrivskyddade arbetsbelastningar för en databas som är geo-replikerade (till exempel som en medlem i en redundansgrupp), kontrollera att det skrivskyddade skalbarhet är aktiverat på både primär och sekundär geo-replikerade databaser. Den här konfigurationen garanterar att samma upplevelse för Utjämning av nätverksbelastning fortsätter när ditt program som ansluter till den nya primärt efter en redundansväxling. Om du ansluter till geo-replikerad sekundär databas med lässkala aktiverad sessioner till `ApplicationIntent=ReadOnly` kommer att dirigeras till en av replikerna på samma sätt som vi dirigera anslutningar på den primära databasen.  Sessioner utan `ApplicationIntent=ReadOnly` kommer att dirigeras till den primära repliken för den georeplikerade sekundärt, vilket också är skrivskyddad. Eftersom geo-replikerad sekundär databas har en annan slutpunkt än den primära databasen, historiskt att få åtkomst till sekundärt det inte krävs för att ange `ApplicationIntent=ReadOnly`. Att säkerställa bakåtkompatibilitet, `sys.geo_replication_links` DMV visar `secondary_allow_connections=2` (alla klientanslutning tillåts).

> [!NOTE]
> Resursallokering eller några andra Utjämning av nätverksbelastning routning mellan lokala repliker av den sekundära databasen stöds inte.

## <a name="next-steps"></a>Nästa steg

- Läs om hur SQL-databas hyperskala erbjudande [hyperskala tjänstnivå](./sql-database-service-tier-hyperscale.md).
