---
title: Använda R för att fråga Azure SQL Database
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Den här artikeln visar hur du använder ett R-skript för att ansluta till en Azure SQL database och frågar den med hjälp av Transact-SQL-uttryck.
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: python
ms.topic: quickstart
author: garyericson
ms.author: garye
ms.reviewer: davidph, carlrab
manager: cgronlun
ms.date: 05/29/2019
ms.openlocfilehash: 1d4b17cf1e0349bf877c676cb4e591fc20ad4113
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66416360"
---
# <a name="quickstart-use-r-to-query-an-azure-sql-database-preview"></a>Snabbstart: Använda R för att fråga en Azure SQL-databas (förhandsversion)

 Den här snabbstarten visar hur du använder [R](https://www.r-project.org/) med Machine Learning-tjänster som ansluter till en Azure SQL-databas och använder Transact-SQL-uttryck för att köra frågor mot data. Machine Learning Services är en funktion i Azure SQL-databas som används för att köra R-skript i databasen. Mer information finns i [Azure SQL Database Machine Learning Services med R (förhandsversion)](sql-database-machine-learning-services-overview.md).

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Nödvändiga komponenter

Kontrollera att du har följande så att du kan genomföra den här snabbstarten:

- En Azure SQL-databas. Du kan använda någon av dessa snabbstarter för att skapa och därefter konfigurera en databas i Azure SQL Database:

<!-- Managed instance is not supported during the preview
  || Single database | Managed instance |
  |:--- |:--- |:---|
  | Create| [Portal](sql-database-single-database-get-started.md) | [Portal](sql-database-managed-instance-get-started.md) |
  || [CLI](scripts/sql-database-create-and-configure-database-cli.md) | [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md) | [PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md) |
  | Configure | [Server-level IP firewall rule](sql-database-server-level-firewall-rule.md) | [Connectivity from a VM](sql-database-managed-instance-configure-vm.md) |
  ||| [Connectivity from on-site](sql-database-managed-instance-configure-p2s.md) |
  | Load data | Adventure Works loaded per quickstart | [Restore Wide World Importers](sql-database-managed-instance-get-started-restore.md) |
  ||| Restore or import Adventure Works from [BACPAC](sql-database-import.md) file from [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works) |
  |||
-->

  || Enskild databas |
  |:--- |:--- |
  | Skapa| [Portal](sql-database-single-database-get-started.md) |
  || [CLI](scripts/sql-database-create-and-configure-database-cli.md) |
  || [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md) |
  | Konfigurera | [IP-brandväggsregel på servernivå](sql-database-server-level-firewall-rule.md) |
  | Läsa in data | AdventureWorks som lästs in per snabbstart |
  |||

  > [!NOTE]
  > Distributionsalternativ för hanterad instans stöds inte i förhandsversionen av Azure SQL Database Machine Learning Services med R.

<!-- Managed instance is not supported during the preview
  > [!IMPORTANT]
  > The scripts in this article are written to use the Adventure Works database. With a managed instance, you must either import the Adventure Works database into an instance database or modify the scripts in this article to use the Wide World Importers database.
-->

- Machine Learning-tjänster (med R) aktiverat. Under den offentliga förhandsversionen introducerar Microsoft dig och aktiverar maskininlärning för din befintliga eller nya databas. Följ stegen i [Registrera dig för förhandsversionen](sql-database-machine-learning-services-overview.md#signup).

- Senast [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS). Du kan köra R-skript med hjälp av andra databashantering eller Frågeverktyg, men i den här snabbstarten ska du använda SSMS.

## <a name="get-sql-server-connection-information"></a>Hämta anslutningsinformation för en SQL-server

Skaffa den anslutningsinformation du behöver för att ansluta till Azure SQL-databasen. Du behöver det fullständiga servernamnet eller värdnamnet, databasnamnet och inloggningsinformationen för de kommande procedurerna.

1. Logga in på [Azure Portal](https://portal.azure.com/).

2. Navigera till sidan **SQL-databaser** eller **SQL-hanterade instanser**.

3. På **översiktssidan** granskar du det fullständigt kvalificerade servernamnet bredvid **Servernamn** för en enkel databas eller det fullständigt kvalificerade servernamnet bredvid **Värd** för en hanterad instans. Om du vill kopiera servernamnet eller värdnamnet hovrar du över det och väljer ikonen **Kopiera**.

## <a name="create-code-to-query-your-sql-database"></a>Skapa kod för att fråga din SQL-databas

1. Öppna **SQL Server Management Studio** och anslut till din SQL-databas.

   Om du behöver hjälp med anslutningen läser [Snabbstart: Använda SQL Server Management Studio för att ansluta till och köra frågor mot en Azure SQL-databas](sql-database-connect-query-ssms.md).

1. Skicka det fullständiga R-skriptet till den [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) lagrade proceduren.

   Skriptet skickas via den `@script` argumentet. Allt i den `@script` argumentet måste vara giltig R-kod.
   
   >[!IMPORTANT]
   >Koden i det här exemplet använder AdventureWorksLT-exempeldata, som du kan välja som källa när du skapar din databas. Om din databas har andra data använder du tabeller från din egen databas i SELECT-frågan. 

    ```sql
    EXECUTE sp_execute_external_script
    @language = N'R'
    , @script = N'OutputDataSet <- InputDataSet;'
    , @input_data_1 = N'SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName FROM [SalesLT].[ProductCategory] pc JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid'
    ```

   > [!NOTE]
   > Om det uppstår några fel kan det bero på att den offentliga förhandsversionen av Machine Learning Services (med R) inte har aktiverats för din SQL-databas. Se [krav](#prerequisites) ovan.

## <a name="run-the-code"></a>Kör koden

1. Kör den [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) lagrade proceduren.

1. Kontrollera att de 20 främsta kategori/produkt raderna returneras i de **meddelanden** fönster.

## <a name="next-steps"></a>Nästa steg

- [Utforma din första Azure SQL-databas](sql-database-design-first-database.md)
- [Azure SQL Database Machine Learning Services (med R)](sql-database-machine-learning-services-overview.md)
- [Skapa och kör enkelt R-skript i Azure SQL Database Machine Learning Services (förhandsversion)](sql-database-quickstart-r-create-script.md)
- [Skriva avancerade R-funktioner i Azure SQL Database med Machine Learning Services (förhandsversion)](sql-database-machine-learning-services-functions.md)
