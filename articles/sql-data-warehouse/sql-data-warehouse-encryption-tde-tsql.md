---
title: Transparent datakryptering i SQL Data Warehouse (T-SQL) | Microsoft Docs
description: Transparent datakryptering (TDE) i SQL Data Warehouse (T-SQL)
services: sql-data-warehouse
author: KavithaJonnakuti
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: security
ms.date: 04/30/2019
ms.author: kavithaj
ms.reviewer: rortloff
ms.openlocfilehash: 1d62bc5cb080f2167f13bd58e9a8a334b8ae5163
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65146075"
---
# <a name="get-started-with-transparent-data-encryption-tde"></a>Kom igång med Transparent datakryptering (TDE)
> [!div class="op_single_selector"]
> * [Säkerhetsöversikt](sql-data-warehouse-overview-manage-security.md)
> * [Autentisering](sql-data-warehouse-authentication.md)
> * [Kryptering (Portal)](sql-data-warehouse-encryption-tde.md)
> * [Kryptering (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)
> 
> 

## <a name="required-permissions"></a>Behörigheter som krävs
Om du vill aktivera Transparent datakryptering (TDE), måste du vara administratör eller medlem i rollen dbmanager.

## <a name="enabling-encryption"></a>Aktivera kryptering
Följ stegen nedan för att aktivera transparent Datakryptering för ett SQL Data Warehouse:

1. Ansluta till den *master* databas på servern som är värd för databasen med en inloggning som är en administratör eller medlem i den **dbmanager** roll i master-databasen
2. Kör följande instruktion för att kryptera databasen.

```sql
ALTER DATABASE [AdventureWorks] SET ENCRYPTION ON;
```

## <a name="disabling-encryption"></a>Inaktivering av kryptering
Följ stegen nedan för att inaktivera TDE för ett SQL Data Warehouse:

1. Ansluta till den *master* databasen med en inloggning som är en administratör eller medlem i den **dbmanager** roll i master-databasen
2. Kör följande instruktion för att kryptera databasen.

```sql
ALTER DATABASE [AdventureWorks] SET ENCRYPTION OFF;
```

> [!NOTE]
> Ett pausat SQL Data Warehouse måste det köras innan du gör ändringar i inställningarna för TDE.
> 
> 

## <a name="verifying-encryption"></a>Verifiera kryptering
Följ stegen nedan för att kontrollera status för datakryptering för ett SQL Data Warehouse:

1. Ansluta till den *master* eller instans-databas med en inloggning som är en administratör eller medlem i den **dbmanager** roll i master-databasen
2. Kör följande instruktion för att kryptera databasen.

```sql
SELECT
    [name],
    [is_encrypted]
FROM
    sys.databases;
```

Ett resultat av ```1``` anger en krypterad databas ```0``` anger en icke-krypterade databas.

## <a name="encryption-dmvs"></a>Kryptering DMV: er
* [sys.databases][sys.databases] 
* [sys.dm_pdw_nodes_database_encryption_keys][sys.dm_pdw_nodes_database_encryption_keys]

<!--Anchors-->
[Transparent Data Encryption (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx
[sys.databases]: https://msdn.microsoft.com/library/ms178534.aspx  
[sys.dm_pdw_nodes_database_encryption_keys]: https://msdn.microsoft.com/library/mt203922.aspx  

<!--Image references-->

<!--Link references-->
