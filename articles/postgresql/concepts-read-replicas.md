---
title: Läsa repliker i Azure Database för PostgreSQL – enskild Server
description: Den här artikeln beskriver Läs replica-funktionen i Azure Database för PostgreSQL – enskild Server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 06/14/2019
ms.openlocfilehash: c98247b0ba8b670a59dec9aa3ec87e949f1dda78
ms.sourcegitcommit: 72f1d1210980d2f75e490f879521bc73d76a17e1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/14/2019
ms.locfileid: "67147932"
---
# <a name="read-replicas-in-azure-database-for-postgresql---single-server"></a>Läsa repliker i Azure Database för PostgreSQL – enskild Server

Läs replica-funktionen kan du replikera data från en Azure Database for PostgreSQL-server till en skrivskyddad server. Du kan replikera från huvudservern till upp till fem repliker. Repliker uppdateras asynkront med PostgreSQL-motorn interna replikeringsteknik.

> [!IMPORTANT]
> Du kan skapa en skrivskyddad replik i samma region som din huvudservern eller i alla andra Azure-regioner valfri. Replikering över flera regioner är för närvarande i offentlig förhandsversion.

Repliker är nya servrar som du hanterar liknar vanliga Azure Database for PostgreSQL-servrar. Var finns replik du faktureras för den etablerade beräkningen i virtuella kärnor och lagring i GB / månad.

Lär dig hur du [skapa och hantera repliker](howto-read-replicas-portal.md).

## <a name="when-to-use-a-read-replica"></a>När du ska använda en skrivskyddad replik
Läs replica-funktionen hjälper till att förbättra prestanda och skalning för läsintensiva arbetsbelastningar. Läs arbetsbelastningar kan isoleras repliker, medan skrivning arbetsbelastningar kan dirigeras till huvudservern.

Ett vanligt scenario är att ha BI och analytiska arbetsbelastningar använder den skrivskyddade repliken som datakälla för rapportering.

Eftersom repliker är skrivskyddade, minska inte de direkt behovet av write-kapacitet på huvudmålservern. Den här funktionen är inte riktad mot write-intensiva arbetsbelastningar.

Läs replica-funktionen använder asynkron replikering för PostgreSQL. Funktionen är inte avsedd för synkron replikeringsscenarier. Det blir en mätbara fördröjning mellan huvudservern och repliken. Data på repliken blir så småningom konsekventa med data i bakgrunden. Använd denna funktion för arbetsbelastningar som kan hantera den här fördröjningen.

Läs repliker kan förbättra din haveriberedskapsplan. Du måste först ha en replik i en annan Azure-region från huvuddatabasen. Om det finns en region katastrof kan du stoppa replikering till den repliken och omdirigera arbetsbelastningen till den. Stoppa replikering kan repliken för att börja ta emot skrivningar, samt läser. Läs mer i den [replikeringsstopp](#stop-replication) avsnittet. 

## <a name="create-a-replica"></a>Skapa en replik
Huvudservern måste ha den `azure.replication_support` parameteruppsättning till **REPLIKEN**. När den här parametern har ändrats, krävs en omstart av servern för att ändringen ska börja gälla. (Den `azure.replication_support` parametern gäller för generell användning och Minnesoptimerad nivåer endast).

När du börjar skapa replica arbetsflöde, skapas en tom Azure Database for PostgreSQL-server. Den nya servern är fylld med de data som fanns på huvudservern. Tiden för skapandet beror på mängden data på huvudservern och hur lång tid sedan den senaste veckovisa fullständiga säkerhetskopieringen. Tiden kan variera mellan några minuter till flera timmar.

Varje replik är aktiverat för lagring [auto-väx](concepts-pricing-tiers.md#storage-auto-grow). Funktionen auto-grow kan repliken för att hålla jämna steg med data som replikeras till den och förhindra att ett avbrott i replikeringen på grund av av lagringsfel.

Läs replica-funktionen använder PostgreSQL fysiska replikering, inte logiska replikering. Strömning replikering med hjälp av replikering fack är standardläget för åtgärden. Om det behövs används loggöverföring för att komma ifatt.

Lär dig hur du [skapar en skrivskyddad replik i Azure-portalen](howto-read-replicas-portal.md).

## <a name="connect-to-a-replica"></a>Ansluta till en replik
När du skapar en replik, ärver den inte brandväggsregler eller VNet-tjänstslutpunkt för domänens huvudserver. Dessa regler måste konfigureras separat för repliken.

Repliken ärver administratörskontot som från huvudservern. Alla användarkonton på huvudservern replikeras till de skrivskyddade replikerna. Du kan bara ansluta till en skrivskyddad replik med hjälp av användarkonton som är tillgängliga på huvudservern.

Du kan ansluta till repliken med hjälp av dess värdnamn och ett giltigt användarkonto, precis som på en vanlig Azure-databas för PostgreSQL-server. För en server med namnet **repliken** med administratörsanvändarnamnet **myadmin**, du kan ansluta till repliken med hjälp av psql:

```
psql -h myreplica.postgres.database.azure.com -U myadmin@myreplica -d postgres
```

Ange lösenordet för användarkontot i Kommandotolken.

## <a name="monitor-replication"></a>Övervakare för replikering
Azure Database för PostgreSQL innehåller två för övervakning av replikeringen. De två måtten är **Max fördröjning mellan repliker** och **repliken fördröjning**. Läs hur du visar de här måtten i den **övervaka en replik** delen av den [Läs replik artikel](howto-read-replicas-portal.md).

Den **Max fördröjning mellan repliker** måttet visar förskjutningen i byte mellan huvudservern och de flesta släpar repliken. Detta mått är tillgängliga på den överordnade servern.

Den **repliken fördröjning** mått visar tiden sedan senast återupprepas transaktion. Om det finns inga transaktioner som inträffar på dina huvudservern, visar den här tidsförskjutningen i måttet. Detta mått är tillgängliga för replikservrar. Repliken fördröjning beräknas från de `pg_stat_wal_receiver` vy:

```SQL
EXTRACT (EPOCH FROM now() - pg_last_xact_replay_timestamp());
```

Ställa in en avisering om när repliken fördröjning når ett värde som inte är acceptabel för din arbetsbelastning. 

Mer information finns i fråga huvudservern direkt för att få replikeringsfördröjning i byte på alla repliker.

I PostgreSQL version 10:

```SQL
select pg_wal_lsn_diff(pg_current_wal_lsn(), stat.replay_lsn) 
AS total_log_delay_in_bytes from pg_stat_replication;
```

I PostgreSQL version 9,6 och tidigare:

```SQL
select pg_xlog_location_diff(pg_current_xlog_location(), stat.replay_location) 
AS total_log_delay_in_bytes from pg_stat_replication;
```

> [!NOTE]
> Om en huvudserver eller Läs repliken har startats om återspeglas den tid det tar att starta om och ta del av i repliken Lag-mått.

## <a name="stop-replication"></a>Stoppa replikering
Du kan stoppa replikering mellan en och en replik. Stoppa medför repliken att starta om och ta bort dess replikeringsinställningar. När replikeringen har stoppats mellan en huvudserver och en skrivskyddad replik, blir repliken en fristående server. Data i den fristående servern är de data som fanns på repliken när kommandot Stoppa replikering har startats. Den fristående servern ifatt inte med huvudservern.

> [!IMPORTANT]
> Den fristående servern kan inte göras i en replik igen.
> Kontrollera att repliken har alla data som du behöver innan du stoppa replikering på en skrivskyddad replik.

När du stoppar replikeringen förlorar repliken alla länkar till dess tidigare huvud- och andra repliker. Det finns ingen automatisk växling mellan huvud- och repliken. 

Lär dig hur du [Stoppa replikering till en replik](howto-read-replicas-portal.md).


## <a name="considerations"></a>Överväganden

Det här avsnittet sammanfattas överväganden om skrivskyddade replica-funktionen.

### <a name="prerequisites"></a>Nödvändiga komponenter
Innan du skapar en skrivskyddad replik i `azure.replication_support` parametern måste anges till **REPLIKEN** på huvudservern. När den här parametern har ändrats, krävs en omstart av servern för att ändringen ska börja gälla. Den `azure.replication_support` parametern gäller för generell användning och Minnesoptimerad nivåer endast.

### <a name="new-replicas"></a>Nya repliker
En skrivskyddad replik skapas som en ny Azure Database for PostgreSQL-server. En befintlig server kan inte göras i en replik. Du kan inte skapa en replik av en annan skrivskyddade replik.

### <a name="replica-configuration"></a>Repliken konfiguration
En replik skapas med hjälp av samma serverkonfiguration som huvudserver. När en replik skapas flera inställningar kan ändras oberoende från huvudservern: compute-generering, vCores, lagring och kvarhållningsperiod för säkerhetskopiering. Prisnivån kan också ändras oberoende av varandra, förutom till eller från Basic-nivån.

> [!IMPORTANT]
> Innan en huvudserver-konfiguration har uppdaterats till nya värden, uppdatera konfigurationen för repliken till samma eller högre värden. Den här åtgärden säkerställer att replikeringen kan hålla jämna steg med ändringar som görs till huvudservern.

PostgreSQL kräver värdet för den `max_connections` parametern på den skrivskyddade repliken ska vara större än eller lika med värdet master, annars repliken inte startar. Azure Database för PostgreSQL, den `max_connections` parametervärdet är baserat på SKU. Mer information finns i [begränsningar i Azure Database for PostgreSQL](concepts-limits.md). 

Om du försöker uppdatera server-värden, men inte följa gränserna, felmeddelande ett.

### <a name="maxpreparedtransactions"></a>max_prepared_transactions
[PostgreSQL kräver](https://www.postgresql.org/docs/10/runtime-config-resource.html#GUC-MAX-PREPARED-TRANSACTIONS) värdet för den `max_prepared_transactions` parametern på den skrivskyddade repliken ska vara större än eller lika med värdet master, annars repliken inte startar. Om du vill ändra `max_prepared_transactions` i bakgrunden först ändra den på replikerna.

### <a name="stopped-replicas"></a>Stoppad repliker
Om du stoppar replikering mellan en huvudserver och en skrivskyddad replik startar om repliken för att tillämpa ändringen. Stoppad repliken blir en fristående server som accepterar både läsningar och skrivningar. Den fristående servern kan inte göras i en replik igen.

### <a name="deleted-master-and-standalone-servers"></a>Borttagna huvud- och fristående servrar
När en huvudserver har tagits bort, blir alla dess skrivskyddade repliker fristående servrar. Replikerna startas om för den här ändringen.

## <a name="next-steps"></a>Nästa steg
Lär dig hur du [skapa och hantera skrivskyddade repliker i Azure portal](howto-read-replicas-portal.md).
