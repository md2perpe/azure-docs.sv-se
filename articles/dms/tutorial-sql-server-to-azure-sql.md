---
title: 'Självstudier: Använd Azure Database Migration Service för att utföra en migrering offline av SQL Server till en enkel/pooldatabas i Azure SQL Database | Microsoft Docs'
description: Lär dig hur du migrerar från SQL Server lokalt till en enkel databas eller en pooldatabas i Azure SQL Database offline genom att använda Azure Database Migration Service.
services: dms
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 07/09/2019
ms.openlocfilehash: c4d2e70d0f200f3a31948cfaa43def8e1b699878
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2019
ms.locfileid: "67706888"
---
# <a name="tutorial-migrate-sql-server-to-a-single-database-or-pooled-database-in-azure-sql-database-offline-using-dms"></a>Självstudier: Migrera SQL Server till en enkel databas eller pooldatabas i Azure SQL Database offline med DMS

Du kan använda Azure Database Migration Service till att migrera databaserna från en lokal SQL Server-instans till [Azure SQL Database](https://docs.microsoft.com/azure/sql-database/). I den här självstudien migrerar du databasen **Adventureworks2012** från en återställd databas till en lokal instans av SQL Server 2016 (eller senare) till en enkel databas eller en pooldatabas i Azure SQL Database med hjälp av Azure Database Migration Service.

I den här guiden får du lära dig att:
> [!div class="checklist"]
>
> - Utvärdera din lokala databas med hjälp av Data Migration Assistant.
> - Migrera exempelschemat med hjälp av Data Migration Assistant.
> - Skapa en instans av Azure Database Migration Service.
> - Skapa ett migreringsprojekt med hjälp av Azure Database Migration Service.
> - Köra migreringen.
> - Övervaka migreringen.
> - Ladda ned en migreringsrapport.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

I den artikeln beskrivs en offlinemigrering från SQL Server till en enkel databas eller en pooldatabas i Azure SQL Database. En onlinemigrering beskrivs i [Migrera SQL Server till Azure SQL Database online med DMS](tutorial-sql-server-azure-sql-online.md).

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här kursen behöver du:

- Ladda ned och installera [SQLServer 2016 eller senare](https://www.microsoft.com/sql-server/sql-server-downloads).
- Aktivera TCP/IP-protokollet, som är inaktiverat som standard under SQL Server Express-installation, genom att följa instruktionerna i artikeln om att [aktivera eller inaktivera ett servernätverksprotokoll](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure).
- Skapa en enkel databas (eller en pooldatabas) i Azure SQL Database. Det gör du genom att följa anvisningarna i artikeln [Skapa en enkel databas i Azure SQL Database med Azure-portalen](https://docs.microsoft.com/azure/sql-database/sql-database-single-database-get-started).

    > [!NOTE]
    > Om du använder SQL Server Integration Services (SSIS) och vill migrera katalogdatabasen för dina SSIS-projekt/-paket (SSISDB) från SQL Server till Azure SQL Database skapas och hanteras målet SSISDB och automatiskt åt dig när du etablerar SSIS i Azure Data Factory (ADF). Mer information om migrera SSIS-paket finns i artikeln [Migrate SQL Server Integration Services packages to Azure](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages) (Migrera SQL Server Integration Services-paket till Azure).
  
- Ladda ned och installera [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) version 3.3 eller senare.
- Skapa ett Azure-nätverk (VNet) för Azure Database Migration Service med hjälp av Azure Resource Manager-distributionsmodellen, som tillhandahåller plats-till-plats-anslutning till dina lokala källservrar genom att använda antingen [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) eller [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). Mer information om hur du skapar ett virtuellt nätverk finns i den [dokumentation om virtuella nätverk](https://docs.microsoft.com/azure/virtual-network/), och särskilt artiklarna i snabbstarten med stegvis information.

    > [!NOTE]
    > Under installationen av virtuellt nätverk, om du använder ExpressRoute med nätverks-peering till Microsoft, lägger du till följande tjänst [slutpunkter](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) till undernätet där tjänsten ska etableras:
    > - Mål-database-slutpunkten (till exempel SQL-slutpunkten och Cosmos DB-slutpunkt)
    > - Slutpunkt för lagring
    > - Service bus-slutpunkt
    >
    > Den här konfigurationen är nödvändigt eftersom Azure Database Migration Service saknar Internetanslutning.

- Kontrollera att dina VNET-regler för nätverkssäkerhetsgruppen inte blockerar följande portar för inkommande kommunikation: 443, 53, 9354, 445, 12000. Mer information om Azure VNet NSG-trafikfiltrering finns i artikeln [filtrera nätverkstrafik med nätverkssäkerhetsgrupper](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
- Konfigurera din [Windows-brandvägg för databasmotoråtkomst](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Öppna Windows-brandväggen så att Azure Database Migration Service kommer åt käll-SQL Server, som har standardinställningen TCP-port 1433.
- Om du kör flera namngivna SQL Server-instanser med dynamiska portar kan du vilja aktivera SQL Browser Service och tillåta åtkomst till UDP-port 1434 via dina brandväggar så att Azure Database Migration Service kan ansluta till en namngiven instans på källservern.
- När du använder en brandväggsinstallation framför dina källdatabaser kanske du måste lägga till brandväggsregler för att tillåta Azure Database Migration Service att komma åt källdatabaserna för migrering.
- Skapa en IP-[brandväggsregel](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) på servernivå för Azure SQL Database-servern för att tillåta åtkomst för Azure Database Migration Service till måldatabaserna. Ange undernätsintervallet för det virtuella nätverket som används för Azure Database Migration Service.
- Kontrollera att autentiseringsuppgifterna som används för att ansluta till SQL Server-källinstansen har [CONTROL SERVER](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql)-behörigheter.
- Kontrollera att autentiseringsuppgifterna som används för att ansluta Azure SQL Database-målinstansen har CONTROL DATABASE-behörighet i Azure SQL-måldatabaserna.

## <a name="assess-your-on-premises-database"></a>Utvärdera din lokala databas

Innan du kan migrera data från en lokal SQL Server-instans till en enkel databas eller pooldatabas i Azure SQL Database, måste du utvärdera SQL Server-databasen för att se om det finns några blockeringsproblem som kan förhindra migreringen. Använd Data Migration Assistant version 3.3 eller senare och följ stegen som beskrivs i artikeln om att [utföra en SQL Server-migreringsutvärdering](https://docs.microsoft.com/sql/dma/dma-assesssqlonprem) för att slutföra den lokala databasutvärderingen. En sammanfattning av de steg som krävs följer:

1. I Data Migration Assistant väljer du ikonen Nytt (+) och väljer sedan **Utvärdering** projekttyp.
2. Ange ett projektnamn. I textrutan **Typ av källserver** väljer du **SQL Server**, i textrutan **Målservertyp** väljer du **Azure SQL Database**. Välj sedan **Skapa** för att skapa projektet.

    När du utvärderar SQL Server-källdatabasen som ska migreras till en enkel databas eller pooldatabas i Azure SQL Database, kan du välja en eller båda av följande utvärderingsrapporttyper:

   - Kontrollera databaskompatibilitet
   - Kontrollera funktionsparitet

    Båda rapporttyperna väljs som standard.

3. I Data Migration Assistant, på sidan **Alternativ** väljer du **Näsa**.
4. På sidan **Välj källor** i dialogrutan **Anslut till en server**, anger du anslutningsinformationen till SQL Server och väljer sedan **Anslut**.
5. I dialogrutan **Lägg till källor** väljer du **AdventureWorks2012**. Välj **Lägg till** och välj sedan **Starta utvärderingen**.

    > [!NOTE]
    > Om du använder SSIS så har DMA för närvarande inget stöd för utvärdering av SSISDB-källan. Dock utvärderas/verifieras SSIS-projekt/-paket när de omdistribueras till SSISDB-målet med Azure SQL Database. Mer information om att migrera SSIS-paket finns i artikeln [Migrate SQL Server Integration Services packages to Azure](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages) (Migrera SQL Server Integration Services-paket till Azure).

    När utvärderingen är klar visas resultatet enligt följande bild:

    ![Utvärdera datamigrering](media/tutorial-sql-server-to-azure-sql/dma-assessments.png)

    För enkla databaser eller pooldatabaser i Azure SQL Database identifierar utvärderingarna funktionsparitetsproblem och blockeringsproblem för migrering vid distribution till en enkel databas eller en pooldatabas.

    - Kategorin **SQL Server-funktionsparitet** innehåller en omfattande uppsättning rekommendationer, alternativa metoder tillgängliga i Azure och minimeringsåtgärder för att hjälpa dig att planera insatsen för dina migreringsprojekt.
    - Kategorin **Kompatibilitetsproblem** identifierar funktioner som stöds delvis eller inte alls, som speglar kompatibilitetsproblem som kan blockera migreringen av lokala SQL Server-databaser till Azure SQL Database. Det ges även rekommendationer, så att du kan hantera problemen.

6. Granska utvärderingsresultaten för blockeringsproblem för migreringen och funktionsparitetsproblem genom att välja de specifika alternativen.

## <a name="migrate-the-sample-schema"></a>Migrera exempelschemat

När du är nöjd med utvärderingen och finner att den valda databasen är en giltig kandidat för migrering till en enkel databas eller en pooldatabas i Azure SQL Database använder du DMA för att migrera schemat till Azure SQL Database.

> [!NOTE]
> Innan du skapar ett migreringsprojekt i Data Migration Assistant ska du redan ha etablerat en Azure SQL-databas så som anges i förutsättningarna. För den här självstudiekursen antas namnet på Azure SQL Database vara **AdventureWorksAzure** men du kan ange ett valfritt namn.
> [!IMPORTANT]
> Om du använder SSIS stöder DMA för närvarande inte migrering av käll-SSISDB, men du kan distribuera om ditt SSIS-projekt/-paket till SSISDB-målet med Azure SQL Database. Mer information om att migrera SSIS-paket finns i artikeln [Migrate SQL Server Integration Services packages to Azure](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages) (Migrera SQL Server Integration Services-paket till Azure).

Att migrera den **AdventureWorks2012** schemat till en enkel databas eller databas Azure SQL Database, utför följande steg:

1. I Data Migration Assistant väljer du ikonen Nytt (+) och sedan under **Projekttyp** väljer du **Migrering**.
2. Ange ett projektnamn i textrutan **Typ av källserver**, välj **SQL Server**. I textrutan **Målservertyp** väljer du sedan **Azure SQL Database**.
3. Under **Migreringsomfång** väljer du **Endast schema**.

    När du har utfört de föregående stegen bör Data Migration Assistant-gränssnittet visas i följande bild:

    ![Skapa Data Migration Assistant-projekt](media/tutorial-sql-server-to-azure-sql/dma-create-project.png)

4. Välj **Skapa** för att skapa projektet.
5. I Data Migration Assistant anger du källanslutningsinformationen för SQL Server. Välj **Anslut** och välj databasen **AdventureWorks2012**.

    ![Källanslutningsinformation för Data Migration Assistant](media/tutorial-sql-server-to-azure-sql/dma-source-connect.png)

6. Välj **Next**, under **Anslut till målserver**, ange målanslutningsinformationen för Azure SQL-databasen, välj **Anslut** och välj sedan databasen **AdventureWorksAzure** som du har företablerat i Azure SQL-databasen.

    ![Målanslutningsinformation för Data Migration Assistant](media/tutorial-sql-server-to-azure-sql/dma-target-connect.png)

7. Välj **Nästa** för att komma till sidan **Välj objekt**, där du kan ange schemaobjekten i databasen **AdventureWorks2012** som måste distribueras till Azure SQL Database.

    Som standard markeras alla objekt.

    ![Skapa SQL-skript](media/tutorial-sql-server-to-azure-sql/dma-assessment-source.png)

8. Välj **Skapa SQL-skript** för att skapa SQL-skripten och granska sedan om det finns fel i skripten.

    ![Schemaskript](media/tutorial-sql-server-to-azure-sql/dma-schema-script.png)

9. Välj **Distribuera schema** för att distribuera schemat till Azure SQL Database, och när schemat har distribuerats kontrollerar du om det finns avvikelser på målservern.

    ![Distribuera schema](media/tutorial-sql-server-to-azure-sql/dma-schema-deploy.png)

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registrera resursprovidern Microsoft.DataMigration

1. Logga in på Azure-portalen och välj **Alla tjänster** och sedan **Prenumerationer**.

   ![Visa portalprenumerationer](media/tutorial-sql-server-to-azure-sql/portal-select-subscription1.png)

2. Välj den prenumeration där du vill skapa en instans av Azure Database Migration Service och välj sedan **Resursprovidrar**.

    ![Visa resursprovidrar](media/tutorial-sql-server-to-azure-sql/portal-select-resource-provider.png)

3. Sök efter migreringen och välj sedan **Registrera** till höger om **Microsoft.DataMigration**.

    ![Registrera resursprovider](media/tutorial-sql-server-to-azure-sql/portal-register-resource-provider.png)    

## <a name="create-an-instance"></a>Skapa en instans

1. I Azure Portal väljer du + **Skapa en resurs**, söker efter Azure Database Migration Service och väljer sedan **Azure Database Migration Service** i listrutan.

    ![Azure Marketplace](media/tutorial-sql-server-to-azure-sql/portal-marketplace.png)

2. På sidan **Azure Database Migration Service** väljer du **Skapa**.

    ![Skapa Azure Database Migration Service-instans](media/tutorial-sql-server-to-azure-sql/dms-create1.png)
  
3. På sidan **Create Migration Service** anger du ett namn för tjänsten, prenumerationen och en ny eller befintlig resursgrupp.

4. Välj den plats där du vill skapa instansen av Azure Database Migration Service.

5. Välj ett befintligt virtuellt nätverk eller skapa en ny.

    Det virtuella nätverket tillhandahåller Azure Database Migration Service åtkomst till SQL Server-källans och målets Azure SQL-databasinstans.

    Mer information om hur du skapar ett virtuellt nätverk i Azure-portalen finns i artikeln [skapa ett virtuellt nätverk med Azure portal](https://aka.ms/DMSVnet).

6. Välj en prisnivå.

    Mer information om kostnader och prisnivåer finns på [sidan med priser](https://aka.ms/dms-pricing).

    ![Konfigurera Azure Database Migration Service-instansinställningar](media/tutorial-sql-server-to-azure-sql/dms-settings2.png)

7. Välj **Skapa** för att skapa tjänsten.

## <a name="create-a-migration-project"></a>Skapa ett migreringsprojekt

När tjänsten har skapats letar du reda på den i Azure Portal, öppnar den och skapar sedan ett nytt migreringsprojekt.

1. I Azure Portal väljer du **Alla tjänster**, söker efter Azure Database Migration Service och väljer sedan **Azure Database Migration Services**.

     ![Leta reda på alla instanser Azure Database Migration Service](media/tutorial-sql-server-to-azure-sql/dms-search.png)

2. På sidan **Azure Database Migration Services** söker du efter namnet på Azure Database Migration Service-instansen du har skapat och väljer sedan instansen.

    ![Hitta din instans av Azure Database Migration Service](media/tutorial-sql-server-to-azure-sql/dms-instance-search.png)

3. Välj + **Nytt migreringsprojekt**.
4. På sidan **Nytt migreringsprojekt** anger du namnet på projektet, I textrutan **Typ av källserver** väljer du **SQL Server**, i textrutan **Målservertyp** väljer du **Azure SQL Database** och sedan för **Välj typ av aktivitet** väljer du **Offline-datamigrering**. 

    ![Skapa Database Migration Service-projekt](media/tutorial-sql-server-to-azure-sql/dms-create-project2.png)

5. Välj **Skapa och kör aktivitet** för att skapa projektet och köra migreringsaktiviteten.

## <a name="specify-source-details"></a>Ange källinformation

1. På sidan **Migreringskällinformation** anger du anslutningsinformationen för SQL Server-källinstansen.

    Se till att använda ett fullständigt domännamn (FQDN) för SQL Server-källinstansens namn. Du kan även använda IP-adressen i situationer då DNS-namnmatchning inte är möjlig.

2. Om du inte har installerat ett betrott certifikat på källservern markerar du kryssrutan **Lita på servercertifikatet**.

    När ett betrott certifikat inte har installerats genererar SQL Server ett självsignerat certifikat när instansen har startats. Detta certifikat används till att kryptera autentiseringsuppgifterna för klientanslutningar.

    > [!CAUTION]
    > SSL-anslutningar som har krypterats med ett självsignerat certifikat ger inte stark säkerhet. De är sårbara för man-in-the-middle-attacker. Du bör inte lita på SSL som använder självsignerade certifikat i en produktionsmiljö eller på servrar som är anslutna till internet.

   ![Källinformation](media/tutorial-sql-server-to-azure-sql/dms-source-details2.png)

    > [!IMPORTANT]
    > Om du använder SSIS stöder DMS för närvarande inte migrering av käll-SSISDB, men du kan distribuera om ditt SSIS-projekt/-paket till SSISDB-målet med Azure SQL Database. Mer information om att migrera SSIS-paket finns i artikeln [Migrate SQL Server Integration Services packages to Azure](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages) (Migrera SQL Server Integration Services-paket till Azure).

## <a name="specify-target-details"></a>Ange målinformation

1. Välj **Spara** och ange på sidan **Information om migreringsmål** anslutningsinformationen för Azure SQL Database-målservern, som är den företablerade Azure SQL Database som **AdventureWorks2012**-schemat distribuerades till med Data Migration Assistant.

    ![Välja mål](media/tutorial-sql-server-to-azure-sql/dms-select-target2.png)

2. Välj **Spara** och mappa sedan på sidan **Mappa till måldatabaser** käll- och måldatabasen för migrering.

    Om måldatabasen innehåller samma databasnamn som källdatabasen väljer Azure Database Migration Service måldatabasen som standard.

    ![Mappa till måldatabaser](media/tutorial-sql-server-to-azure-sql/dms-map-targets-activity2.png)

3. Välj **Spara**. Expandera tabellistan på sidan **Välj tabeller** och granska sedan listan över berörda fält.

    Observera att Azure Database Migration Service automatiskt väljer alla tomma källtabeller som finns på Azure SQL Database-målinstansen. Om du vill migrera tabeller på nytt som redan innehåller data måste du uttryckligen välja tabellerna på det här bladet.

    ![Välj tabeller](media/tutorial-sql-server-to-azure-sql/dms-configure-setting-activity2.png)

4. Välj **Spara**. På sidan **Migreringssammanfattning**, i textrutan **Aktivitetsnamn** anger du ett namn på migreringsaktiviteten.

5. Expandera avsnittet **Verifieringsalternativ** för att visa sidan **Välj verifieringsalternativ** och ange sedan om du ska verifiera de migrerade databaserna för **Schemajämförelse**, **Datakonsekvens** och **Frågeriktighet**.

    ![Välja verifieringsalternativ](media/tutorial-sql-server-to-azure-sql/dms-configuration2.png)

6. Välj **Spara** och granska sammanfattningen för att kontrollera att käll- och målinformationen matchar det du tidigare har angett.

    ![Migreringssammanfattning](media/tutorial-sql-server-to-azure-sql/dms-run-migration2.png)

## <a name="run-the-migration"></a>Köra migreringen

- Välj **Kör migrering**.

    Migreringsaktivitetsfönstret visas och **Status** för aktiviteten är **Väntande**.

    ![Aktivitetsstatus](media/tutorial-sql-server-to-azure-sql/dms-activity-status1.png)

## <a name="monitor-the-migration"></a>Övervaka migreringen

1. På migreringsaktivitetssidan väljer du **Uppdatera** för att uppdatera visningen tills **Status** för migreringen är **Slutförd**.

    ![Aktivitetsstatus Slutförd](media/tutorial-sql-server-to-azure-sql/dms-completed-activity1.png)

2. När migreringen har slutförts väljer du **Ladda ned rapport** för att få en rapport som visar informationen som rör migreringsprocessen.

3. Verifiera måldatabasen/måldatabaserna på Azure SQL Database-målservern.

### <a name="additional-resources"></a>Ytterligare resurser

- Praktisk labbuppgift: [SQL-migrering med Azure Data Migration Service (DMS)](https://www.microsoft.com/handsonlabs/SelfPacedLabs/?storyGuid=3b671509-c3cd-4495-8e8f-354acfa09587).
- Information om kända problem och begränsningar när du utför onlinemigreringar till Azure SQL Database finns i artikeln [Known issues and workarounds with Azure SQL Database online migrations](known-issues-azure-sql-online.md) (Kända problem och lösningar med Azure SQL Database-onlinemigreringar).
- Mer information om Azure Database Migration Service finns i artikeln [What is the Azure Database Migration Service?](https://docs.microsoft.com/azure/dms/dms-overview) (Vad är Azure Database Migration Service?).
- Information om Azure SQL Database finns i artikeln [Vad är tjänsten SQL Database?](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview).
