---
title: Återställ en Azure SQL database från en säkerhetskopia | Microsoft Docs
description: Läs mer om Point-in-Time-återställning, som gör det möjligt för dig att återställa en Azure SQL-databas till en tidigare tidpunkt (upp till 35 dagar).
services: sql-database
ms.service: sql-database
ms.subservice: backup-restore
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
manager: craigg
ms.date: 04/30/2019
ms.openlocfilehash: 47bf59adb33f3685b31430c652b31880d383833e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65232645"
---
# <a name="recover-an-azure-sql-database-using-automated-database-backups"></a>Återställa en Azure SQL database med hjälp av automatiska databassäkerhetskopieringar

Som standard lagras SQL Database-säkerhetskopior i geo-replikerade blob storage (RA-GRS). Följande alternativ är tillgängliga för databas återställning med hjälp av [automatiska databassäkerhetskopieringar](sql-database-automated-backups.md):

- Skapa en ny databas på samma SQL-databasserver återställt till en viss tidpunkt inom kvarhållningsperioden.
- Skapa en databas på samma SQL Database-server återställs till borttagning av tid för en borttagen databas.
- Skapa en ny databas på valfri SQL Database-server i samma region återställas till det datum då de senaste säkerhetskopiorna.
- Skapa en ny databas på någon SQL Database-server i alla andra regioner återställas till det datum då de senaste replikerade säkerhetskopiorna.

Om du har konfigurerat [säkerhetskopiera långsiktig kvarhållning](sql-database-long-term-retention.md), du kan också skapa en ny databas från alla LTR backup på en SQL Database-server.

> [!IMPORTANT]
> Du kan inte skriva över en befintlig databas under återställning.

När du använder Standard eller Premium-tjänstnivån, tillkommer en återställd databas en extra lagringsutrymme kostnad under följande förhållanden:

- Återställning av P11 – P15 S4 – S12 eller P1 – P6 om den maximala databasstorleken är större än 500 GB.
- Återställning av P1 – P6 till S4 – S12 om den maximala databasstorleken är större än 250 GB.

Extra kostnaden är icurred när maxstorleken på den återställda databasen är större än mängden lagringsutrymme som ingår i måldatabasen tjänstnivå och prestandanivå servicenivå. Extra lagringsutrymme utöver det inkluderade debiteras extra. Prisinformation om extra lagringsutrymme finns i den [SQL Database-sidan med priser](https://azure.microsoft.com/pricing/details/sql-database/). Om den faktiska mängden använt utrymme är mindre än mängden lagringsutrymme som ingår, kan du undvika detta extra kostnader genom att ange den maximala databasstorleken till mängden som ingår.

> [!NOTE]
> [Automatisk säkerhetskopiering av databaser](sql-database-automated-backups.md) används när du skapar en [databaskopieringen](sql-database-copy.md).

## <a name="recovery-time"></a>Tiden för återställning

Tiden för återställning för att återställa en databas med hjälp av automatiska databassäkerhetskopieringar påverkas av flera faktorer:

- Storleken på databasen
- Beräkningsstorleken för databasen
- Antalet transaktionsloggar som ingår
- Antal aktiviteter som ska återupprepas om du vill återställa till återställningspunkten
- Nätverkets bandbredd om återställningen till en annan region
- Antalet samtidiga återställning begäranden som bearbetas i målregionen

För en stor och/eller mycket aktiv databas, kan återställningen ta flera timmar. Om det finns långvarig avbrott i en region, är det möjligt att det finns stora mängder geo-återställning begäranden som bearbetas av andra regioner. När det finns många begäranden, öka återställningstiden för databaser i den regionen. De flesta databasen återställer klara på mindre än 12 timmar.

Det finns begränsningar för antalet samtidiga återställning begäranden för en enskild prenumeration.  Dessa begränsningar gäller för valfri kombination av punkt i tiden återställningar, geo-återställning och återställningar från säkerhetskopiering med LTR):

| | **Max antal samtidiga begäranden som bearbetas** | **Max antal samtidiga begäranden som skickas** |
| :--- | --: | --: |
|Databas (per prenumeration)|10|60|
|Elastisk pool (per pool)|4|200|
||||

Det finns inte för närvarande en inbyggd metod för att återställa hela servern. Den [Azure SQL Database: Fullständig återställning av](https://gallery.technet.microsoft.com/Azure-SQL-Database-Full-82941666) skript är ett exempel på hur du kan utföra den här uppgiften.

> [!IMPORTANT]
> Om du vill återställa med hjälp av automatiska säkerhetskopieringar, måste du vara medlem i rollen SQL Server-deltagare i prenumeration eller vara prenumerationsägaren - läsa [RBAC: inbyggda roller](../role-based-access-control/built-in-roles.md). Du kan återställa med hjälp av Azure Portal, PowerShell eller REST-API:et. Du kan inte använda Transact-SQL.

## <a name="point-in-time-restore"></a>Återställning från tidpunkt

Du kan återställa en fristående, pooler, eller instans databasen till en tidigare tidpunkt med hjälp av Azure-portalen [PowerShell](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase), eller [REST API](https://docs.microsoft.com/rest/api/sql/databases). Begäran kan ange eventuella tjänstnivå eller beräkna storleken på den återställda databasen. Se till att du har tillräckligt med resurser på den server som du återställer databasen. När du är klar skapas en ny databas på samma server som den ursprungliga databasen. Den återställda databasen kommer att debiteras enligt normal taxa baserat på dess tjänstnivå och beräkningsstorleken. Du inte betalar avgifter förrän återställa databasen är klar.

Du kan vanligtvis återställer en databas till en tidigare tidpunkt för återställning. Du kan hantera den återställda databasen som en ersättning för den ursprungliga databasen eller använda den som en källdata för att uppdatera den ursprungliga databasen.

- **Databasersättning av**

  Om den återställda databasen är avsedd som en ersättning för den ursprungliga databasen, bör du ange orinal databasens beräkning storlek och tjänstnivå. Du kan byta namn på den ursprungliga databasen och ge den återställda databasen med den ursprungliga namn den [ALTER DATABASE](/sql/t-sql/statements/alter-database-azure-sql-database) i T-SQL.

- **Återställning av data**

  Om du planerar att hämta data från den återställda databasen att återställa efter ett fel för användaren eller programmet, måste du skriva och köra ett skript för återställning av data som extraherar data från den återställda databasen och gäller för den ursprungliga databasen. Även om återställningen kan ta lång tid att slutföra, visas databasen som ska återställas i databaslistan under återställningsprocessen. Om du tar bort databasen under återställningen återställningen kommer att avbrytas och du debiteras inte för den databas som inte gick att slutföra återställningen.

Återställa en enskild tilldelade i poler, eller databasen till en instans med hjälp av Azure-portalen, öppna sidan för din databas och klickar på **återställa** i verktygsfältet.

![punkt i tiden återställning](./media/sql-database-recovery-using-backups/point-in-time-recovery.png)

> [!IMPORTANT]
> Om du vill återställa en databas från en säkerhetskopia programmässigt Se [programmässigt utföra återställning med hjälp av automatisk säkerhetskopiering](sql-database-recovery-using-backups.md#programmatically-performing-recovery-using-automated-backups)

## <a name="deleted-database-restore"></a>Återställning av databasen som har tagits bort

Du kan återställa en borttagen databas till borttagningstid eller en tidigare tidpunkt tidpunkt på samma SQL Database-server med hjälp av Azure-portalen [PowerShell](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase), eller [REST (createMode = återställer)](https://docs.microsoft.com/rest/api/sql/databases/createorupdate). Du kan [återställa borttagen databas på hanterad instans med hjälp av PowerShell](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../recreate-dropped-database-on-azure-sql-managed-instance). 

> [!TIP]
> En PowerShell-exempelskript som visar hur du återställer en borttagen databas, se [återställa en SQL-databas med hjälp av PowerShell](scripts/sql-database-restore-database-powershell.md).
> [!IMPORTANT]
> Om du tar bort en Azure SQL Database-server-instans, raderas också alla databaser och kan inte återställas. Det finns för närvarande inget stöd för att återställa en borttagen server.

### <a name="deleted-database-restore-using-the-azure-portal"></a>Återställning av borttagna databasen med hjälp av Azure portal

Om du vill återställa en borttagen databas via Azure portal, öppnar sidan för din server och i området för åtgärder, klickar på **borttagna databaser**.

![deleted-database-restore-1](./media/sql-database-recovery-using-backups/deleted-database-restore-1.png)

![deleted-database-restore-2](./media/sql-database-recovery-using-backups/deleted-database-restore-2.png)

> [!IMPORTANT]
> Om du vill återställa en borttagen databas via programmering, se [programmässigt utföra återställning med hjälp av automatisk säkerhetskopiering](sql-database-recovery-using-backups.md#programmatically-performing-recovery-using-automated-backups)

## <a name="geo-restore"></a>Geo-återställning

Du kan återställa en SQL-databas på en server i valfri Azure-region från de senaste geo-replikerade säkerhetskopiorna. GEO-återställning använder en geo-replikerade säkerhetskopia som källa. Det kan begäras även om databasen eller datacenter är otillgängligt på grund av ett avbrott.

GEO-återställning är standardalternativet för återställning när databasen är inte tillgänglig på grund av en incident i regionen som värd. Du kan återställa databasen till en server i alla andra regioner. Det finns en fördröjning mellan när en säkerhetskopia görs och när det är geo-replikerade till ett Azure blob i en annan region. Därför kan kan den återställda databasen vara upp till en timme efter orignal-databasen. Följande bild visar återställning av databasen från den senaste tillgängliga säkerhetskopian i en annan region.

![GEO-återställning](./media/sql-database-geo-restore/geo-restore-2.png)

> [!TIP]
> En PowerShell-exempelskript som visar hur du utför en geo-återställning, se [återställa en SQL-databas med hjälp av PowerShell](scripts/sql-database-restore-database-powershell.md).

Point-in-time-återställning på en geo-secondary stöds inte för närvarande. Point-in-time-återställning kan göras endast på en primär databas. Detaljerad information om hur du använder geo-återställning för att återställa från ett avbrott finns i [återställa från ett avbrott](sql-database-disaster-recovery.md).

> [!IMPORTANT]
> GEO-återställning är den mest grundläggande haveriberedskapslösning som är tillgängliga i SQL Database. Den förlitar sig på skapas automatiskt geo-replikerade säkerhetskopior rpo = 1 timme och uppskattade återställningstiden på upp till 12 timmar. Det garanterar inte att målregionen har kapacitet för att återställa dina databaser efter en regional ourage eftersom en sharp ökning av begäran kommer att vara sannolikt. För icke företag kritiska program som använder relativt små databaser, finns geo-återställning en lämplig haveriberedskapslösning. För busniess viktiga program som använder stora databaser och måste garantera kontinuitet för företag, bör du använda [automatisk redundans grupper](sql-database-auto-failover-group.md). Den erbjuder en mycket lägre RPO och RTO och kapaciteten alltid är korrekt. Mer information om alternativ för affärskontinuitet finns [översikt över affärskontinuitet](sql-database-business-continuity.md).

### <a name="geo-restore-using-the-azure-portal"></a>GEO-återställning med hjälp av Azure portal

Att geo-återställning för en databas under dess [DTU-baserade modellen kvarhållningsperiod](sql-database-service-tiers-dtu.md) eller [vCore-baserade modellen kvarhållningsperiod](sql-database-service-tiers-vcore.md) med Azure portal, öppna sidan SQL-databaser och klicka sedan på **Lägg till** . I den **Välj källa** textrutan väljer **Backup**. Ange säkerhetskopieringen från som du vill utföra återställningen i regionen och på-servern valfri.

> [!Note]
> GEO-återställning med Azure-portalen är inte tillgänglig i hanterade instanser. Använd PowerShell i stället.

## <a name="programmatically-performing-recovery-using-automated-backups"></a>Programmässigt utföra återställning med hjälp av automatiska säkerhetskopieringar

Som tidigare beskrivs, utöver Azure portal, kan databasåterställning utföras via programmering med Azure PowerShell eller REST API. I följande tabeller beskrivs uppsättningen kommandon som är tillgängliga.

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Modulen PowerShell Azure Resource Manager är fortfarande stöds av Azure SQL Database, men alla framtida utveckling är för modulen Az.Sql. Dessa cmdlets finns i [i AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenten för kommandon i modulen Az och AzureRm-moduler är avsevärt identiska.

- Om du vill återställa en fristående eller en databas i pool, se [återställning AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase).

  | Cmdlet | Beskrivning |
  | --- | --- |
  | [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) |Hämtar en eller flera databaser. |
  | [Get-AzSqlDeletedDatabaseBackup](/powershell/module/az.sql/get-azsqldeleteddatabasebackup) | Hämtar en borttagen databas som du kan återställa. |
  | [Get-AzSqlDatabaseGeoBackup](/powershell/module/az.sql/get-azsqldatabasegeobackup) |Hämtar en geo-redundant säkerhetskopia av en databas. |
  | [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase) |Återställer en SQL-databas. |

  > [!TIP]
  > En PowerShell-exempelskript som visar hur du utför en point-in-time-återställning av en databas, se [återställa en SQL-databas med hjälp av PowerShell](scripts/sql-database-restore-database-powershell.md).

- Om du vill återställa en databas för hanterad instans, se [återställning AzSqlInstanceDatabase](/powershell/module/az.sql/restore-azsqlinstancedatabase).

  | Cmdlet | Beskrivning |
  | --- | --- |
  | [Get-AzSqlInstance](/powershell/module/az.sql/get-azsqlinstance) |Hämtar en eller flera hanterade instanser. |
  | [Get-AzSqlInstanceDatabase](/powershell/module/az.sql/get-azsqlinstancedatabase) | Hämtar en instans databaser. |
  | [Restore-AzSqlInstanceDatabase](/powershell/module/az.sql/restore-azsqlinstancedatabase) |Återställer en databasinstans. |

### <a name="rest-api"></a>REST-API

Du återställer en enda eller grupperade databas med hjälp av REST-API:

| API | Beskrivning |
| --- | --- |
| [REST (createMode=Recovery)](https://docs.microsoft.com/rest/api/sql/databases) |Återställer en databas |
| [Hämta skapa eller uppdatera Database-Status](https://docs.microsoft.com/rest/api/sql/operations) |Returnerar status under en återställning |

### <a name="azure-cli"></a>Azure CLI

- Om du vill återställa en enskild eller grupperade databas med Azure CLI, se [az sql db restore](/cli/azure/sql/db#az-sql-db-restore).
- Om du vill återställa en hanterad instans med hjälp av Azure CLI, se [az sql EXTEXTB återställning](/cli/azure/sql/midb#az-sql-midb-restore)

## <a name="summary"></a>Sammanfattning

Automatisk säkerhetskopiering skyddar dina databaser från användare och programfel, databasen oavsiktlig borttagning och långvarig avbrott. Den här inbyggda funktionen är tillgänglig för alla tjänstnivåer och storlekar.

## <a name="next-steps"></a>Nästa steg

- En översikt över affärskontinuitet och scenarier finns i [översikt över affärskontinuitet](sql-database-business-continuity.md).
- Vill veta mer om Azure SQL Database automatiska säkerhetskopior, se [SQL Database automatiska säkerhetskopior](sql-database-automated-backups.md).
- Läs om långsiktig kvarhållning i [långsiktig kvarhållning](sql-database-long-term-retention.md).
- Läs om hur du snabbare återställningsalternativ i [aktiv geo-replikering](sql-database-active-geo-replication.md) eller [automatisk redundans grupper](sql-database-auto-failover-group.md).
