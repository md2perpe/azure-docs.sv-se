---
title: 'Självstudier: Använd Azure Database Migration Service för att migrera MongoDB till Azure Cosmos DB API för MongoDB offline | Microsoft Docs'
description: Lär dig att migrera från MongoDB på en plats till Azure Cosmos DB API för MongoDB offline med hjälp av Azure Database Migration Service.
services: dms
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 07/04/2019
ms.openlocfilehash: 2ff5ebefbe379edda94dcf8ac066027398e2f3f4
ms.sourcegitcommit: d2785f020e134c3680ca1c8500aa2c0211aa1e24
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/04/2019
ms.locfileid: "67565549"
---
# <a name="tutorial-migrate-mongodb-to-azure-cosmos-dbs-api-for-mongodb-offline-using-dms"></a>Självstudier: Migrera MongoDB till Azure Cosmos DB:s API för MongoDB offline med DMS

Du kan använda Azure Database Migration Service för att utföra en offlinemigrering för (engångs-) som används av databaser från en lokal eller ett molnkluster instans av MongoDB till Azure Cosmos DB-API för MongoDB.

I den här guiden får du lära dig att:
> [!div class="checklist"]
>
> * Skapa en instans av Azure Database Migration Service.
> * Skapa ett migreringsprojekt med Azure Database Migration Service.
> * Köra migreringen.
> * Övervaka migreringen.

I den här självstudien får migrera du en datauppsättning i MongoDB som finns i en Azure virtuell dator till Azure Cosmos DB-API för MongoDB med hjälp av Azure Database Migration Service. Om du inte har konfigurerat någon MongoDB-källa, kan du läsa artikeln [Installera och konfigurera MongoDB på en virtuell Windows-dator i Azure](https://docs.microsoft.com/azure/virtual-machines/windows/install-mongodb).

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här kursen behöver du:

* [Slutföra före migreringen](../cosmos-db/mongodb-pre-migration.md) steg, till exempel uppskatta dataflöde, välja en partitionsnyckel och indexeringsprincipen.
* [Skapa ett Azure Cosmos DB-API för MongoDB-konto](https://ms.portal.azure.com/#create/Microsoft.DocumentDB).
* Skapa ett Azure-nätverk (VNet) för Azure Database Migration Service med hjälp av Azure Resource Manager-distributionsmodellen, som tillhandahåller plats-till-plats-anslutning till dina lokala källservrar genom att använda antingen [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction)eller [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). Mer information om hur du skapar ett virtuellt nätverk finns i den [dokumentation om virtuella nätverk](https://docs.microsoft.com/azure/virtual-network/), och särskilt artiklarna i snabbstarten med stegvis information.

    > [!NOTE]
    > Under installationen av virtuellt nätverk, om du använder ExpressRoute med nätverks-peering till Microsoft, lägger du till följande tjänst [slutpunkter](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) till undernätet där tjänsten ska etableras:
    >
    > * Mål-database-slutpunkten (till exempel SQL-slutpunkten och Cosmos DB-slutpunkt)
    > * Slutpunkt för lagring
    > * Service bus-slutpunkt
    >
    > Den här konfigurationen är nödvändigt eftersom Azure Database Migration Service saknar Internetanslutning.

* Se till att dina VNet Nätverkssäkerhetsgrupp (NSG)-regler inte blockera följande kommunikationsportar: 53, 443, 445, 9354 och 10000 20000. Mer information om Azure VNet NSG-trafikfiltrering finns i artikeln [filtrera nätverkstrafik med nätverkssäkerhetsgrupper](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
* Öppna din Windows-brandväggen så att Azure Database Migration Service åtkomst till källservern MongoDB, vilket som standard är TCP-port 27017.
* När du använder en brandväggsinstallation framför din källdatabaserna, kan du behöva lägga till brandväggsregler som tillåter Azure Database Migration Service åtkomst till källdatabaserna för migrering.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registrera resursprovidern Microsoft.DataMigration

1. Logga in på Azure-portalen och välj **Alla tjänster** och sedan **Prenumerationer**.

   ![Visa portalprenumerationer](media/tutorial-mongodb-to-cosmosdb/portal-select-subscription1.png)

2. Välj den prenumeration där du vill skapa en instans av Azure Database Migration Service och välj sedan **Resursprovidrar**.

    ![Visa resursprovidrar](media/tutorial-mongodb-to-cosmosdb/portal-select-resource-provider.png)

3. Sök efter migreringen och välj sedan **Registrera** till höger om **Microsoft.DataMigration**.

    ![Registrera resursprovider](media/tutorial-mongodb-to-cosmosdb/portal-register-resource-provider.png)    

## <a name="create-an-instance"></a>Skapa en instans

1. I Azure Portal väljer du + **Skapa en resurs**, söker efter Azure Database Migration Service och väljer sedan **Azure Database Migration Service** i listrutan.

    ![Azure Marketplace](media/tutorial-mongodb-to-cosmosdb/portal-marketplace.png)

2. På sidan **Azure Database Migration Service** väljer du **Skapa**.

    ![Skapa Azure Database Migration Service-instans](media/tutorial-mongodb-to-cosmosdb/dms-create1.png)
  
3. På sidan **Create Migration Service** anger du ett namn för tjänsten, prenumerationen och en ny eller befintlig resursgrupp.

4. Välj den plats där du vill skapa en instans av Azure Database Migration Service. 

5. Välj ett befintligt virtuellt nätverk eller skapa en ny.

    Det virtuella nätverket ger Azure Database Migration Service åtkomst till källan MongoDB-instans och Azure Cosmos DB-kontot.

    Mer information om hur du skapar ett virtuellt nätverk i Azure-portalen finns i artikeln [skapa ett virtuellt nätverk med Azure portal](https://aka.ms/DMSVnet).

6. Välj en prisnivå.

    Mer information om kostnader och prisnivåer finns på [sidan med priser](https://aka.ms/dms-pricing).

    ![Konfigurera Azure Database Migration Service-instansinställningar](media/tutorial-mongodb-to-cosmosdb/dms-settings2.png)

7. Välj **Skapa** för att skapa tjänsten.

## <a name="create-a-migration-project"></a>Skapa ett migreringsprojekt

När tjänsten har skapats letar du reda på den i Azure Portal, öppnar den och skapar sedan ett nytt migreringsprojekt.

1. I Azure Portal väljer du **Alla tjänster**, söker efter Azure Database Migration Service och väljer sedan **Azure Database Migration Services**.

      ![Leta upp alla instanser av Azure Database Migration Service](media/tutorial-mongodb-to-cosmosdb/dms-search.png)

2. På den **Azure Database Migration Service** skärmen, Sök efter namnet på Azure Database Migration Service-instans som du skapade och välj sedan instansen.

3. Välj + **Nytt migreringsprojekt**.

4. På sidan **Nytt migreringsprojekt** anger du namnet på projektet. I textrutan **Typ av källserver** väljer du **MongoDB**, i textrutan **Målservertyp** väljer du **CosmosDB (MongoDB API)** och i **Välj typ av aktivitet** väljer du sedan **Offline-datamigrering**. 

    ![Skapa ett Database Migration Service-projekt](media/tutorial-mongodb-to-cosmosdb/dms-create-project.png)

5. Välj **Skapa och kör aktivitet** för att skapa projektet och köra migreringsaktiviteten.

## <a name="specify-source-details"></a>Ange källinformation

1. På sidan **Källinformation** anger du anslutningsinformationen för MongoDB-källservern.

    Det finns tre lägen för att ansluta till en datakälla:
   * **Standardläge** som accepterar ett fullständigt kvalificerat domännamn eller en IP-adress, portnummer och autentiseringsuppgifter för anslutningen.
   * **Anslutningssträngsläge** som tar emot en MongoDB-anslutningssträng som beskrivs i artikeln [URI-anslutningssträngens format](https://docs.mongodb.com/manual/reference/connection-string/).
   * **Data från Azure-lagring** som tar emot en blobcontainers SAS-URL. Välj **Bloben innehåller BSON-dumpar** om blobcontainern innehåller BSON-dumpar som producerats av MongoDB:s [bsondump-verktyg](https://docs.mongodb.com/manual/reference/program/bsondump/) och avmarkera den om containern innehåller JSON-filer.

     Om du väljer det här alternativet ska du se till att anslutningssträngen för lagringskontot har följande format:

     ```
     https://blobnameurl/container?SASKEY
     ```

     Baserat på typdumpsinformationen i Azure Storage bör du även beakta följande detalj.

     * För BSON-dumpar måste data i blob-containern vara i bsondump-format, så att datafilerna placeras i mappar som namnges efter de innehållande databaserna i formatet collection.bson. Metadatafiler (om sådana finns) bör namnges med formatet *samling*.metadata.json.

     * För JSON-dumpar måste filerna i blob-containern placeras i mappar som namnges efter de innehållande databaserna. I varje databasmapp måste datafiler placeras i en undermapp som heter ”data” och namnges med formatet *samling*.json. Metadatafiler (om sådana finns) måste placeras i en undermapp som heter ”metadata” och namnges med samma format, *samling*.json. Metadatafilerna måste vara i samma format som det som skapas av verktyget MongoDB bsondump.

    > [!IMPORTANT]
    > Det rekommenderas inte för att använda ett självsignerat certifikat på mongo-server. Men om ett sådant används, Anslut till servern med **anslutningssträngsläge** och kontrollera att anslutningssträngen har ””
    >
    >```
    >&sslVerifyCertificate=false
    >```

   Du kan även använda IP-adressen i situationer då DNS-namnmatchning inte är möjlig.

   ![Ange källinformation](media/tutorial-mongodb-to-cosmosdb/dms-specify-source.png)

2. Välj **Spara**.

## <a name="specify-target-details"></a>Ange målinformation

1. På sidan **Information om migreringsmål** anger du anslutningsinformationen för Azure Cosmos DB-målkontot, vilket är det företablerade Azure Cosmos DB-API för MongoDB-kontot som du ska migrera dina MongoDB-data till.

    ![Ange målinformation](media/tutorial-mongodb-to-cosmosdb/dms-specify-target.png)

2. Välj **Spara**.

## <a name="map-to-target-databases"></a>Mappa till måldatabaser

1. På skärmen **Mappa till måldatabaser** mappar du käll- och måldatabasen för migreringen.

    Om måldatabasen innehåller samma databasnamn som källdatabasen, väljer Azure Database Migration Service måldatabasen som standard.

    Om strängen **skapa** visas bredvid namnet på databasen, betyder det att Azure Database Migration Service kunde inte hitta måldatabasen tjänsten databasen skapas åt dig.

    I det här läget i migreringen, kan du [etablera ett dataflöde](https://docs.microsoft.com/azure/cosmos-db/set-throughput). I Cosmos DB kan du etablera dataflöden på databasnivå eller separat för varje samling. Dataflödet mäts i [enheter för programbegäran](https://docs.microsoft.com/azure/cosmos-db/request-units) (RU:er). Mer information om [Azure Cosmos DB-prissättning](https://azure.microsoft.com/pricing/details/cosmos-db/).

    ![Mappa till måldatabaser](media/tutorial-mongodb-to-cosmosdb/dms-map-target-databases.png)

2. Välj **Spara**.
3. På skärmen **Mängdinställning** expanderar du samlingslistan och granskar sedan listan över de samlingar som ska migreras.

    Azure Database Migration Service automatiskt väljer alla samlingar som finns på källan MongoDB-instans som inte finns på Azure Cosmos DB-kontot. Om du vill migrera samlingar som redan innehåller data på nytt, måste du uttryckligen välja samlingarna på det här bladet.

    Du kan ange hur många RU:er som du vill att samlingarna ska använda. Azure Database Migration Service föreslår smarta standardvärden som baseras på samlingens storlek.

    > [!NOTE]
    > Utför migrering av databas och samling parallellt med flera instanser av Azure Database Migration Service, om det behövs för att snabba på körningen.

    Du kan också ange en shardnyckel för att kunna utnyttja [partitionering i Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview) för optimal skalbarhet. Läs igenom [metodtipsen för att välja en shard-/partitionsnyckel](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview#choose-partitionkey).

    ![Välja tabeller för samlingar](media/tutorial-mongodb-to-cosmosdb/dms-collection-setting.png)

4. Välj **Spara**.

5. På sidan **Migreringssammanfattning**, i textrutan **Aktivitetsnamn** anger du ett namn på migreringsaktiviteten.

    ![Migreringssammanfattning](media/tutorial-mongodb-to-cosmosdb/dms-migration-summary.png)

## <a name="run-the-migration"></a>Köra migreringen

* Välj **Kör migrering**.

    Migreringsaktivitetsfönstret visas och **Status** för aktiviteten är **Inte startad**.

    ![Aktivitetsstatus](media/tutorial-mongodb-to-cosmosdb/dms-activity-status.png)

## <a name="monitor-the-migration"></a>Övervaka migreringen

* På migreringsaktivitetssidan väljer du **Uppdatera** för att uppdatera visningen tills **Status** för migreringen är **Slutförd**.

   > [!NOTE]
   > Du kan välja Aktivitet för att få information om databasens och samlingsnivåns migreringsmått.

    ![Aktivitetsstatus Slutförd](media/tutorial-mongodb-to-cosmosdb/dms-activity-completed.png)

## <a name="verify-data-in-cosmos-db"></a>Kontrollera data i Cosmos DB

* När migreringen är klar kan du kontrollera ditt Azure Cosmos DB-konto för att se om alla samlingar migrerades korrekt.

    ![Aktivitetsstatus Slutförd](media/tutorial-mongodb-to-cosmosdb/dms-cosmosdb-data-explorer.png)

## <a name="post-migration-optimization"></a>Uppgifter efter migrering optimering

När du har migrerat data som lagras i MongoDB-databas till Azure Cosmos DB-API för MongoDB kan du ansluta till Azure Cosmos DB och hantera data. Du kan också utföra andra uppgifter efter migrering optimering åtgärder, till exempel optimerar indexprincip, uppdatera standardkonsekvensnivå eller konfigurera global distribution för Azure Cosmos DB-kontot. Mer information finns i den [efter migrering optimering](../cosmos-db/mongodb-post-migration.md) artikeln.

## <a name="additional-resources"></a>Ytterligare resurser

* [Information om Cosmos DB-tjänsten](https://azure.microsoft.com/services/cosmos-db/)

## <a name="next-steps"></a>Nästa steg

* Se fler scenarier i migreringsvägledningen i Microsofts [Guide för databasmigrering](https://datamigration.microsoft.com/).
