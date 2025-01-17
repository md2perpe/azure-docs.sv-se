---
title: Konfigurera Azure-SSIS Integration Runtime för redundans för SQL-databas | Microsoft Docs
description: Den här artikeln beskrivs hur du konfigurerar Azure-SSIS Integration Runtime med Azure SQL Database geo-replikering och redundans för SSIDB-databasen
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.date: 08/14/2018
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: f0612a688bb1e0fd79325b9a1f9b43731a210d10
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66399245"
---
# <a name="configure-the-azure-ssis-integration-runtime-with-azure-sql-database-geo-replication-and-failover"></a>Konfigurera Azure-SSIS Integration Runtime med Azure SQL Database geo-replikering och redundans

Den här artikeln beskriver hur du konfigurerar Azure-SSIS Integration Runtime med Azure SQL Database geo-replikering för SSIDB-databasen. När det uppstår redundans, kan du se till att Azure-SSIS IR ser till att arbeta med den sekundära databasen.

Mer information om geo-replikering och redundans för SQL Database finns i [översikt: Aktiv geo-replikering och automatisk redundans grupper](../sql-database/sql-database-geo-replication-overview.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="scenario-1---azure-ssis-ir-is-pointing-to-read-write-listener-endpoint"></a>Scenario 1 – Azure-SSIS IR pekar du skrivskyddad lyssnarslutpunkt

### <a name="conditions"></a>Villkor

Det här avsnittet gäller när följande villkor är uppfyllda:

- Azure-SSIS IR pekar på skrivskyddad lyssnarslutpunkt av redundansgruppen.

  AND

- SQL Database-servern är *inte* konfigurerats med slutpunkt-regel för virtuella nätverk-tjänsten.

### <a name="solution"></a>Lösning

När redundansväxlingen sker, är det transparent för Azure-SSIS IR. Azure-SSIS IR ansluter automatiskt till den nya primärt av redundansgruppen.

## <a name="scenario-2---azure-ssis-ir-is-pointing-to-primary-server-endpoint"></a>Scenario 2 – Azure-SSIS IR pekar på primär server-slutpunkt

### <a name="conditions"></a>Villkor

Det här avsnittet gäller om någon av följande villkor föreligger:

- Azure-SSIS IR pekar på den primära Serverslutpunkten för redundansgruppen. Den här slutpunkten ändras vid redundans.

  ELLER

- Azure SQL Database-servern har konfigurerats med slutpunkt-regel för virtuella nätverk-tjänsten.

  ELLER

- Databasservern är en SQL Database Managed Instance konfigurerats med ett virtuellt nätverk.

### <a name="solution"></a>Lösning

Vid redundans som du behöver göra följande:

1. Stoppa Azure-SSIS IR.

2. Konfigurera om IR så att den pekar till den nya primära slutpunkten och till ett virtuellt nätverk i det nya området.

3. Starta om i IR.

I följande avsnitt beskrivs de här stegen i detalj.

### <a name="prerequisites"></a>Nödvändiga komponenter

- Kontrollera att du har aktiverat haveriberedskap för Azure SQL Database-servern om servern har ett avbrott på samma gång. Mer information finns i [översikt över affärskontinuitet med Azure SQL Database](../sql-database/sql-database-business-continuity.md).

- Om du använder ett virtuellt nätverk i den aktuella regionen, som du behöver använda ett annat virtuellt nätverk i det nya området för att ansluta din Azure-SSIS integration runtime. Mer information finns i [ansluta en Azure-SSIS integration runtime till ett virtuellt nätverk](join-azure-ssis-integration-runtime-virtual-network.md).

- Om du använder en anpassad installation kan du behöva förbereda en annan SAS-URI för blob-behållare som lagrar dina anpassade installationsskriptet och associerade filer, så att det fortsätter att vara tillgänglig under ett avbrott. Mer information finns i [konfigurera en anpassad installation på en Azure-SSIS integration runtime](how-to-configure-azure-ssis-ir-custom-setup.md).

### <a name="steps"></a>Steg

Följ dessa steg för att stoppa din Azure-SSIS IR, växla IR till en ny region och starta den igen.

1. Stoppa IR i den ursprungliga regionen.

2. Anropa följande kommando i PowerShell för att uppdatera IR med de nya inställningarna.

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -Location "new region" `
                    -CatalogServerEndpoint "Azure SQL Database server endpoint" `
                    -CatalogAdminCredential "Azure SQL Database server admin credentials" `
                    -VNetId "new VNet" `
                    -Subnet "new subnet" `
                    -SetupScriptContainerSasUri "new custom setup SAS URI"
    ```

    Mer information om det här PowerShell-kommandot finns i [skapa Azure-SSIS integration runtime i Azure Data Factory](create-azure-ssis-integration-runtime.md)

3. Starta IR igen.

## <a name="scenario-3---attaching-an-existing-ssisdb-ssis-catalog-to-a-new-azure-ssis-ir"></a>Scenario 3 – koppla en befintlig SSISDB (SSIS-katalogen) till en ny Azure-SSIS IR

När en ADF eller Azure-SSIS IR olycka inträffar i aktuell region, kan du göra din SSISDB är fortfarande fungerar med en ny Azure-SSIS IR i en ny region.

### <a name="prerequisites"></a>Nödvändiga komponenter

- Om du använder ett virtuellt nätverk i den aktuella regionen, som du behöver använda ett annat virtuellt nätverk i det nya området för att ansluta din Azure-SSIS integration runtime. Mer information finns i [ansluta en Azure-SSIS integration runtime till ett virtuellt nätverk](join-azure-ssis-integration-runtime-virtual-network.md).

- Om du använder en anpassad installation kan du behöva förbereda en annan SAS-URI för blob-behållare som lagrar dina anpassade installationsskriptet och associerade filer, så att det fortsätter att vara tillgänglig under ett avbrott. Mer information finns i [konfigurera en anpassad installation på en Azure-SSIS integration runtime](how-to-configure-azure-ssis-ir-custom-setup.md).

### <a name="steps"></a>Steg

Följ dessa steg för att stoppa din Azure-SSIS IR, växla IR till en ny region och starta den igen.

1. Köra den lagrade proceduren för att göra SSISDB bifogas **\<new_data_factory_name\>** eller  **\<new_integration_runtime_name\>** .
   
  ```SQL
    EXEC [catalog].[failover_integration_runtime] @data_factory_name='<new_data_factory_name>', @integration_runtime_name='<new_integration_runtime_name>'
   ```

2. Skapa en ny datafabrik med namnet **\<new_data_factory_name\>** i det nya området. Mer information finns i Skapa en datafabrik.

     ```powershell
     Set-AzDataFactoryV2 -ResourceGroupName "new resource group name" `
                         -Location "new region"`
                         -Name "<new_data_factory_name>"
     ```
    Mer information om det här PowerShell-kommandot finns i [skapa en Azure-datafabrik med hjälp av PowerShell](quickstart-create-data-factory-powershell.md)

3. Skapa en ny Azure-SSIS IR med namnet **\<new_integration_runtime_name\>** i det nya området med Azure PowerShell.

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName "new resource group name" `
                                           -DataFactoryName "new data factory name" `
                                           -Name "<new_integration_runtime_name>" `
                                           -Description $AzureSSISDescription `
                                           -Type Managed `
                                           -Location $AzureSSISLocation `
                                           -NodeSize $AzureSSISNodeSize `
                                           -NodeCount $AzureSSISNodeNumber `
                                           -Edition $AzureSSISEdition `
                                           -LicenseType $AzureSSISLicenseType `
                                           -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                           -VnetId "new vnet" `
                                           -Subnet "new subnet" `
                                           -CatalogServerEndpoint $SSISDBServerEndpoint `
                                           -CatalogPricingTier $SSISDBPricingTier
    ```

    Mer information om det här PowerShell-kommandot finns i [skapa Azure-SSIS integration runtime i Azure Data Factory](create-azure-ssis-integration-runtime.md)

4. Starta IR igen.

## <a name="next-steps"></a>Nästa steg

Överväg dessa andra konfigurationsalternativ för Azure-SSIS IR:

- [Konfigurera Azure-SSIS Integration Runtime för höga prestanda](configure-azure-ssis-integration-runtime-performance.md)

- [Anpassa konfigurationen av Azure SSIS-integreringskörningen](how-to-configure-azure-ssis-ir-custom-setup.md)

- [Etablera Enterprise Edition för Azure-SSIS Integration Runtime](how-to-configure-azure-ssis-ir-enterprise-edition.md)
