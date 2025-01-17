---
title: Artikel om begränsningar för kända problem/migrering med online migrering till Azure SQL Database | Microsoft Docs
description: Läs mer om begränsningar för kända problem/migrering med online migrering till Azure SQL Database.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 04/09/2019
ms.openlocfilehash: 00ed2f20884c3cd8f49307bd726f14f3007f884f
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60534461"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-to-azure-sql-db"></a>Begränsningar för kända problem/migrering med online migreringar till Azure SQL DB

Kända problem och begränsningar som är associerade med online migreringar från SQL Server till Azure SQL Database beskrivs nedan.

> [!IMPORTANT]
> Migrering av SQL_variant datatyper stöds inte med online migrering av SQL Server till Azure SQL Database.

### <a name="migration-of-temporal-tables-not-supported"></a>Migrering av temporala tabeller som inte stöds

**Symtom**

Om din källdatabas består av en eller flera temporala tabeller, Databasmigreringen misslyckas under åtgärden ”fullständiga Datainläsningen” och följande felmeddelande:

```
{ "resourceId":"/subscriptions/<subscription id>/resourceGroups/migrateready/providers/Microsoft.DataMigration/services/<DMS Service name>", "errorType":"Database migration error", "errorEvents":"["Capture functionalities could not be set. RetCode: SQL_ERROR SqlState: 42000 NativeError: 13570 Message: [Microsoft][SQL Server Native Client 11.0][SQL Server]The use of replication is not supported with system-versioned temporal table '[Application. Cities]' Line: 1 Column: -1 "]" }
```

 ![Den temporala tabellen fel exempel](media/known-issues-azure-sql-online/dms-temporal-tables-errors.png)

**Lösning**

1. Hitta temporala tabeller i din käll-schemat med frågan nedan.
     ``` 
     select name,temporal_type,temporal_type_desc,* from sys.tables where temporal_type <>0
     ```
2. Undanta dessa tabeller från den **konfigurera migreringsinställningarna** bladet, där du anger tabeller för migrering.

3. Kör migreringsaktiviteten.

**Resurser**

Mer information finns i artikeln [Temporala tabeller](https://docs.microsoft.com/sql/relational-databases/tables/temporal-tables?view=sql-server-2017).
 
### <a name="migration-of-tables-includes-one-or-more-columns-with-the-hierarchyid-data-type"></a>Migrering av tabeller innehåller en eller flera kolumner med datatypen hierarchyid

**Symtom**

Du kan se ett SQL-undantag föreslå ”ntext är inte kompatibel med hierarchyid” under ”fullständiga Datainläsningen”-åtgärd:
     
![exempel för hierarchyid-fel](media/known-issues-azure-sql-online/dms-hierarchyid-errors.png)

**Lösning**

1. Hitta användartabeller som innehåller kolumner med datatypen hierarchyid med frågan nedan.

      ``` 
      select object_name(object_id) 'Table name' from sys.columns where system_type_id =240 and object_id in (select object_id from sys.objects where type='U')
      ``` 

2. Undanta dessa tabeller från den **konfigurera migreringsinställningarna** bladet, där du anger tabeller för migrering.

3. Kör migreringsaktiviteten.

### <a name="migration-failures-with-various-integrity-violations-with-active-triggers-in-the-schema-during-full-data-load-or-incremental-data-sync"></a>Migrering fel med olika integritet överträdelser med active utlösare i schemat under ”fullständiga Datainläsningen” eller ”inkrementell datasynkronisering”

**Lösning**

1. Hitta utlösare som är aktiva i källdatabasen med frågan nedan:

     ```
     select * from sys.triggers where is_disabled =0
     ```

2. Inaktivera utlösare på källdatabasen med hjälp av anvisningarna i artikeln [inaktivera UTLÖSAREN (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/disable-trigger-transact-sql?view=sql-server-2017).

3. Kör migreringsaktiviteten igen.

### <a name="support-for-lob-data-types"></a>Stöd för LOB-datatyper

**Symtom**

Om längden på kolumnen för stora objekt (LOB) är större än 32 KB, kan data hämta trunkeras på målet. Du kan kontrollera längden på LOB-kolumn med hjälp av frågan nedan: 

``` 
SELECT max(DATALENGTH(ColumnName)) as LEN from TableName
```

**Lösning**

Om du har en LOB-kolumn som är större än 32 KB, kontakta teknikteamet på [be Azure Databasmigreringar](mailto:AskAzureDatabaseMigrations@service.microsoft.com).

### <a name="issues-with-timestamp-columns"></a>Problem med tidsstämpelkolumner

**Symtom**

DMS migrera inte tidsstämpel källvärdet; i stället genererar DMS ett nytt tidsstämpelvärde i måltabellen.

**Lösning**

Om du behöver DMS att migrera det exakta tidsstämpelvärde som lagras i källtabellen, kontakta teknikteamet på [be Azure Databasmigreringar](mailto:AskAzureDatabaseMigrations@service.microsoft.com).

### <a name="data-migration-errors-dont-provide-additional-details-on-the-database-detailed-status-blade"></a>Migreringsfel innehåller inte mer information på bladet Database detaljerad statusinformation om.

**Symtom**

När du stöter på fel för migrering i statusvyn för databaser information att välja den **migreringsfel** länken i övre menyfliksområdet kan inte innehålla ytterligare information som är specifika för migreringen felen.

![migreringsfel ingen information-exempel](media/known-issues-azure-sql-online/dms-data-migration-errors-no-details.png)

**Lösning**

Gå till information om specifika felet genom att följa stegen nedan.

1. Stäng bladet Database detaljerad statusinformation om du vill visa skärmen aktivitet migrering.

     ![skärmen aktivitet för migrering](media/known-issues-azure-sql-online/dms-migration-activity-screen.png)

2. Välj **se felinformationen** att visa felmeddelanden som hjälper dig att felsöka migreringsfel.
