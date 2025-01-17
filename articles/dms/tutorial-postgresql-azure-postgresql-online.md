---
title: 'Självstudier: Använda Azure Database Migration Service för att utföra en online-migrering av PostgreSQL till Azure Database för PostgreSQL | Microsoft Docs'
description: Lär dig att utföra en online-migrering från PostgreSQL på lokala till Azure Database för PostgreSQL med hjälp av Azure Database Migration Service.
services: dms
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 06/28/2019
ms.openlocfilehash: 96bfb80602efe8e63f814fc9bf6cff3ae52e5983
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67461539"
---
# <a name="tutorial-migrate-postgresql-to-azure-database-for-postgresql-online-using-dms"></a>Självstudier: Migrera PostgreSQL till Azure Database for PostgreSQL online med DMS

Du kan använda Azure Database Migration Service för att migrera databaserna från en lokal PostgreSQL-instans till [Azure Database for PostgreSQL](https://docs.microsoft.com/azure/postgresql/) med minimal avbrottstid. Migrering kan med andra ord ske med minimalt avbrott för programmet. I den här självstudien får du migrera den **DVD hyra** exempeldatabasen från en lokal instans av PostgreSQL 9.6 till Azure Database för PostgreSQL med hjälp av aktiviteten onlinemigrering i Azure Database Migration Service.

I den här guiden får du lära dig att:
> [!div class="checklist"]
>
> * Migrera exempel schemat med pg_dump-verktyget.
> * Skapa en instans av Azure Database Migration Service.
> * Skapa ett migreringsprojekt med hjälp av Azure Database Migration Service.
> * Köra migreringen.
> * Övervaka migreringen.

> [!NOTE]
> Med hjälp av Azure Database Migration Service för att utföra en online-migrering måste du skapa en instans baserat på premiumprisnivån.

> [!IMPORTANT]
> För en optimal migreringsupplevelse Microsoft rekommenderar att skapa en instans av Azure Database Migration Service i samma Azure-region som måldatabasen. Att flytta data mellan regioner eller geografiska områden kan göra migreringsprocessen långsammare och leda till fel.

## <a name="prerequisites"></a>Nödvändiga komponenter

För att slutföra den här kursen behöver du:

* Ladda ned och installera [PostgreSQL Community Edition](https://www.postgresql.org/download/) 9.5, 9.6 eller 10. PostgreSQL-källserverversionen måste vara 9.5.11, 9.6.7, 10 eller senare. Mer information finns i artikeln [Versioner av PostgreSQL Database som stöds](https://docs.microsoft.com/azure/postgresql/concepts-supported-versions).

    Dessutom måste den lokala PostgreSQL-version matcha Azure Database for PostgreSQL-versionen. Exempelvis kan PostgreSQL 9.5.11.5 endast migreras till Azure Database for PostgreSQL 9.5.11 och inte till version 9.6.7.

    > [!NOTE]
    > För PostgreSQL version 10 stöder för närvarande DMS endast migrering av version 10.3 till Azure Database för PostgreSQL.

* [Skapa en instans i Azure Database for PostgreSQL](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-portal).  
* Skapa ett Azure-nätverk (VNet) för Azure Database Migration Service med hjälp av Azure Resource Manager-distributionsmodellen, som tillhandahåller plats-till-plats-anslutning till dina lokala källservrar genom att använda antingen [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) eller [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). Mer information om hur du skapar ett virtuellt nätverk finns i den [dokumentation om virtuella nätverk](https://docs.microsoft.com/azure/virtual-network/), och särskilt artiklarna i snabbstarten med stegvis information.

    > [!NOTE]
    > Under installationen av virtuellt nätverk, om du använder ExpressRoute med nätverks-peering till Microsoft, lägger du till följande tjänst [slutpunkter](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) till undernätet där tjänsten ska etableras:
    > * Mål-database-slutpunkten (till exempel SQL-slutpunkten och Cosmos DB-slutpunkt)
    > * Slutpunkt för lagring
    > * Service bus-slutpunkt
    >
    > Den här konfigurationen är nödvändigt eftersom Azure Database Migration Service saknar Internetanslutning.

* Se till att dina VNet Nätverkssäkerhetsgrupp (NSG)-regler inte blockerar följande portar för inkommande kommunikation till Azure Database Migration Service: 443, 53, 9354, 445, 12000. Mer information om Azure VNet NSG-trafikfiltrering finns i artikeln [filtrera nätverkstrafik med nätverkssäkerhetsgrupper](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm).
* Konfigurera din [Windows-brandvägg för databasmotoråtkomst](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Öppna din Windows-brandväggen så att Azure Database Migration Service att få åtkomst till källan PostgreSQL-Server, vilket som standard är TCP-port 5432.
* När du använder en brandväggsinstallation framför dina källdatabaser kanske du måste lägga till brandväggsregler för att tillåta Azure Database Migration Service att komma åt källdatabaserna för migrering.
* Skapa en servernivå [brandväggsregel](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) för Azure Database for PostgreSQL så att Azure Database Migration Service att få åtkomst till måldatabaserna. Ange undernätsintervallet för det virtuella nätverket som används för Azure Database Migration Service.
* Det finns två metoder för att anropa CLI:

  * Välj knappen Cloud Shell längst upp till höger i Azure Portal:

       ![Cloud Shell-knappen i Azure Portal](media/tutorial-postgresql-to-azure-postgresql-online/cloud-shell-button.png)

  * Installera och kör CLI lokalt. CLI 2.0 är kommandoradsverktyget för att hantera Azure-resurser.

       Hämta CLI enligt instruktionerna i artikeln [Installera Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). Artikeln visas även de plattformar som har stöd för CLI 2.0.

       Om du vill konfigurera Windows-undersystem för Linux (WSL), följer du anvisningarna i [Installationsguide för Windows 10](https://docs.microsoft.com/windows/wsl/install-win10)

* Aktivera logisk replikering i filen postgresql.config och ange följande parametrar:

  * wal_level = **logical**
  * max_replication_slots = [antal platser], rekommenderad inställning **5 platser**
  * max_wal_senders = [antalet samtidiga uppgifter] – parametern max_wal_senders anger antal samtidiga aktiviteter som kan köras, rekommenderad inställning är **10 uppgifter**

## <a name="migrate-the-sample-schema"></a>Migrera exempelschemat

För att slutföra alla databasobjekt som tabellscheman, index och lagrade procedurer måste vi extrahera schemat från källdatabasen och tillämpa det på databasen.

1. Använd pg_dump -s-kommandot för att skapa en schemadumpfil för en databas. 

    ```
    pg_dump -o -h hostname -U db_username -d db_name -s > your_schema.sql
    ```

    Till exempel, för att dumpa en schemafil dvdrental databas:
    ```
    pg_dump -o -h localhost -U postgres -d dvdrental -s  > dvdrentalSchema.sql
    ```

    Mer information om hur du använder verktyget pg_dump finns i exemplen i självstudien [pg-dump](https://www.postgresql.org/docs/9.6/static/app-pgdump.html#PG-DUMP-EXAMPLES).

2. Skapa en tom databas i målmiljön, vilken är Azure Database for PostgreSQL.

    Mer information finns i artikeln [Skapa en Azure Database for PostgreSQL-server i Azure Portal](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-portal) om hur du ansluter och skapar en databas.

3. Importera schemat till måldatabasen som du skapade genom att återställa schemadumpfilen.

    ```
    psql -h hostname -U db_username -d db_name < your_schema.sql 
    ```

    Exempel:

    ```
    psql -h mypgserver-20170401.postgres.database.azure.com  -U postgres -d dvdrental < dvdrentalSchema.sql
    ```

4. Om du har sekundärnycklar i ditt schema misslyckas den första inläsningen och den kontinuerliga synkroniseringen av migreringen. Kör följande skript i PgAdmin eller i psql för att extrahera skriptet för att släppa sekundärnycklar och skriptet för att lägga till sekundärnycklar vid målet (Azure Database for PostgreSQL).
  
    ```
    SELECT Queries.tablename
           ,concat('alter table ', Queries.tablename, ' ', STRING_AGG(concat('DROP CONSTRAINT ', Queries.foreignkey), ',')) as DropQuery
                ,concat('alter table ', Queries.tablename, ' ',
                                                STRING_AGG(concat('ADD CONSTRAINT ', Queries.foreignkey, ' FOREIGN KEY (', column_name, ')', 'REFERENCES ', foreign_table_name, '(', foreign_column_name, ')' ), ',')) as AddQuery
        FROM
        (SELECT
        tc.table_schema,
        tc.constraint_name as foreignkey,
        tc.table_name as tableName,
        kcu.column_name,
        ccu.table_schema AS foreign_table_schema,
        ccu.table_name AS foreign_table_name,
        ccu.column_name AS foreign_column_name
    FROM
        information_schema.table_constraints AS tc
        JOIN information_schema.key_column_usage AS kcu
          ON tc.constraint_name = kcu.constraint_name
          AND tc.table_schema = kcu.table_schema
        JOIN information_schema.constraint_column_usage AS ccu
          ON ccu.constraint_name = tc.constraint_name
          AND ccu.table_schema = tc.table_schema
    WHERE constraint_type = 'FOREIGN KEY') Queries
      GROUP BY Queries.tablename;
    ```

    Kör släpp sekundärnyckeln (som är den andra kolumnen) i frågeresultatet.

5. Utlösare i dina data (infoga eller uppdatera utlösare) tillämpar den dataintegritet i målet före replikerade data från källan. Vi rekommenderar att du inaktiverar utlösare i alla tabeller **på angivna** under migreringen och sedan återaktivera utlösare när migreringen är slutföra.

    Om du vill inaktivera utlösare i måldatabasen använder du följande kommando:

    ```
    select concat ('alter table ', event_object_table, ' disable trigger ', trigger_name)
    from information_schema.triggers;
    ```

6. Om det finns data uppräkningstypen i alla tabeller, rekommenderar vi att du tillfälligt uppdatera den till en tecken varierande datatyp i måltabellen. När replikering av data är klar kan du återställa datatypen till ENUM.

## <a name="provisioning-an-instance-of-dms-using-the-cli"></a>Etablera en instans av DMS med hjälp av CLI

1. Installera tillägget dms-synkronisering:
   * Logga in på Azure genom att köra följande kommando:
       ```
       az login
       ```

   * När du uppmanas, öppna en webbläsare och ange en kod för att autentisera din enhet. Följ anvisningarna som anges.
   * Lägg till dms-tillägget:
       * Om du vill visa tillgängliga tillägg, kör du följande kommando:

           ```
           az extension list-available –otable
           ```

       * Kör följande kommando om du vill installera tillägget:

           ```
           az extension add –n dms-preview
           ```

   * För att verifiera att du har dms-tillägget installerat korrekt, kör du följande kommando:

       ```
       az extension list -otable
       ```
       Du bör se följande utdata:

       ```
       ExtensionType    Name
       ---------------  ------
       whl              dms
       ```

   * Visa alla kommandon som stöds i DMS genom att köra:

       ```
       az dms -h
       ```

   * Om du har flera Azure-prenumerationer kör du följande kommando för att välja den prenumeration du vill använda för att etablera en instans av DMS-tjänsten.

        ```
       az account set -s 97181df2-909d-420b-ab93-1bff15acb6b7
        ```

2. Etablera en instans av DMS genom att köra följande kommando:

   ```
   az dms create -l [location] -n <newServiceName> -g <yourResourceGroupName> --sku-name BusinessCritical_4vCores --subnet/subscriptions/{vnet subscription id}/resourceGroups/{vnet resource group}/providers/Microsoft.Network/virtualNetworks/{vnet name}/subnets/{subnet name} –tags tagName1=tagValue1 tagWithNoValue
   ```

   Till exempel skapar följande kommando en tjänst i:

   * Plats: USA, östra 2
   * Prenumeration: 97181df2-909d-420b-ab93-1bff15acb6b7
   * Namn på resursgrupp: PostgresDemo
   * DNS-tjänstnamn: PostgresCLI

   ```
   az dms create -l eastus2 -g PostgresDemo -n PostgresCLI --subnet /subscriptions/97181df2-909d-420b-ab93-1bff15acb6b7/resourceGroups/ERNetwork/providers/Microsoft.Network/virtualNetworks/AzureDMS-CORP-USC-VNET-5044/subnets/Subnet-1 --sku-name BusinessCritical_4vCores
   ```

   Det tar cirka 10–12 minuter att skapa en instans av DMS-tjänsten.

3. För att identifiera IP-adressen för DMS-agenten så att du kan lägga till den i filen Postgres pg_hba.conf, kör du följande kommando:

    ```
    az network nic list -g <ResourceGroupName>--query '[].ipConfigurations | [].privateIpAddress'
    ```

    Exempel:

    ```
    az network nic list -g PostgresDemo --query '[].ipConfigurations | [].privateIpAddress'
    ```

    Du bör få ett resultat liknande följande adress: 

    ```
    [
      "172.16.136.18"
    ]
    ```

4. Lägg till IP-adressen för DMS-agenten i Postgres pg_hba.conf-filen.

    * Anteckna DMS IP-adressen när du har slutfört etablering i DMS.
    * Lägg till IP-adressen till pg_hba.conf-filen på källan, på likande sätt som följande post:

        ```
        host    all     all     172.16.136.18/10    md5
        host    replication     postgres    172.16.136.18/10    md5
        ```

5. Skapa sedan ett PostgreSQL-migreringsprojekt genom att köra följande kommando:
    
    ```
    az dms project create -l <location> -g <ResourceGroupName> --service-name <yourServiceName> --source-platform PostgreSQL --target-platform AzureDbforPostgreSQL -n <newProjectName>
    ```

    Till exempel skapar följande kommando ett projekt med dessa parametrar:

   * Plats: Västra centrala USA
   * Namn på resursgrupp: PostgresDemo
   * Tjänstnamn: PostgresCLI
   * Projektnamn: PGMigration
   * Källplattform: PostgreSQL
   * Målplattform: AzureDbForPostgreSql

     ```
     az dms project create -l eastus2 -n PGMigration -g PostgresDemo --service-name PostgresCLI --source-platform PostgreSQL --target-platform AzureDbForPostgreSql
     ```

6. Skapa en uppgift för migrering av PostgreSQL med följande steg.

    Det här steget inkluderar användning av käll-IP, användar-ID och lösenord, mål-IP-, användar-ID, lösenord och aktivitetstyp för att upprätta en anslutning.

   * Om du vill se en fullständig lista över alternativ, kör du kommandot:

       ```
       az dms project task create -h
       ```

       För både käll- och målanslutning refererar indataparametern till en json-fil som har objektlistan.

       Formatet för anslutning till JSON-objekt för PostgreSQL-anslutningar.
        
       ```
       {
                   "userName": "user name",    // if this is missing or null, you will be prompted
                   "password": null,           // if this is missing or null (highly recommended) you will
               be prompted
                   "serverName": "server name",
                   "databaseName": "database name", // if this is missing, it will default to the 'postgres'
               server
                   "port": 5432                // if this is missing, it will default to 5432
               }
       ```

   * Det finns också en databas alternativet json-fil som innehåller de json-objekt. För PostgreSQL visas formatet för JSON-objekt för databasalternativet nedan:

       ```
       [
           {
               "name": "source database",
               "target_database_name": "target database",
           },
           ...n
       ]
       ```

   * Skapa en json-fil i anteckningar, kopiera följande kommandon och klistra in dem i filen och spara filen på C:\DMS\source.json.

        ```
       {
                   "userName": "postgres",    
                   "password": null,           
               be prompted
                   "serverName": "13.51.14.222",
                   "databaseName": "dvdrental", 
                   "port": 5432                
               }
        ```

   * Skapa en annan fil med namnet target.json och spara som C:\DMS\target.json. Inkludera följande kommandon:

       ```
       {
               "userName": " dms@builddemotarget",    
               "password": null,           
               "serverName": " builddemotarget.postgres.database.azure.com",
               "databaseName": "inventory", 
               "port": 5432                
           }
       ```

   * Skapa en json-fil för databasalternativ som visar inventeringen som den databas som ska migreras:

       ``` 
       [
           {
               "name": "dvdrental",
               "target_database_name": "dvdrental",
           }
       ]
       ```

   * Kör följande kommando, som tar in källan, målet och DB-alternativet för json-filer.

       ``` 
       az dms project task create -g PostgresDemo --project-name PGMigration --source-platform postgresql --target-platform azuredbforpostgresql --source-connection-json c:\DMS\source.json --database-options-json C:\DMS\option.json --service-name PostgresCLI --target-connection-json c:\DMS\target.json –task-type OnlineMigration -n runnowtask    
       ```

     Du har nu har skickat en migreringsuppgift.

7. Om du vill visa förloppet för uppgiften, kör du följande kommando:

   ```
   az dms project task show --service-name PostgresCLI --project-name PGMigration --resource-group PostgresDemo --name Runnowtask
   ```

   ELLER

    ```
   az dms project task show --service-name PostgresCLI --project-name PGMigration --resource-group PostgresDemo --name Runnowtask --expand output
    ```

8. Du kan också fråga efter migrationState från expanderade utdata:

    ```
    az dms project task show --service-name PostgresCLI --project-name PGMigration --resource-group PostgresDemo --name Runnowtask --expand output --query 'properties.output[].migrationState | [0]' "READY_TO_COMPLETE"
    ```

## <a name="understanding-migration-task-status"></a>Förstå aktivitetsstatus för migrering

Det finns flera parametrar som visar förloppet för migrering i utdatafilen. Använd udatafilen nedan som exempel:

    ```
    "output": [                                 Database Level
          {
            "appliedChanges": 0,        //Total incremental sync applied after full load
            "cdcDeleteCounter": 0       // Total delete operation  applied after full load
            "cdcInsertCounter": 0,      // Total insert operation applied after full load
            "cdcUpdateCounter": 0,      // Total update operation applied after full load
            "databaseName": "inventory",
            "endedOn": null,
            "fullLoadCompletedTables": 2,   //Number of tables completed full load
            "fullLoadErroredTables": 0, //Number of tables that contain migration error
            "fullLoadLoadingTables": 0, //Number of tables that are in loading status
            "fullLoadQueuedTables": 0,  //Number of tables that are in queued status
            "id": "db|inventory",
            "incomingChanges": 0,       //Number of changes after full load
            "initializationCompleted": true,
            "latency": 0,
            "migrationState": "READY_TO_COMPLETE",  //Status of migration task. READY_TO_COMPLETE means the database is ready for cutover
            "resultType": "DatabaseLevelOutput",
            "startedOn": "2018-07-05T23:36:02.27839+00:00"
          },
          {
            "databaseCount": 1,
            "endedOn": null,
            "id": "dd27aa3a-ed71-4bff-ab34-77db4261101c",
            "resultType": "MigrationLevelOutput",
            "sourceServer": "138.91.123.10",
            "sourceVersion": "PostgreSQL",
            "startedOn": "2018-07-05T23:36:02.27839+00:00",
            "state": "PENDING",
            "targetServer": "builddemotarget.postgres.database.azure.com",
            "targetVersion": "Azure Database for PostgreSQL"
          },
          {                                     Table 1
            "cdcDeleteCounter": 0,
            "cdcInsertCounter": 0,
            "cdcUpdateCounter": 0,
            "dataErrorsCount": 0,
            "databaseName": "inventory",
            "fullLoadEndedOn": "2018-07-05T23:36:20.740701+00:00",  //Full load completed time
            "fullLoadEstFinishTime": "1970-01-01T00:00:00+00:00",
            "fullLoadStartedOn": "2018-07-05T23:36:15.864552+00:00",    //Full load started time
            "fullLoadTotalRows": 10,                    //Number of rows loaded in full load
            "fullLoadTotalVolumeBytes": 7056,               //Volume in Bytes in full load
            "id": "or|inventory|public|actor",          
            "lastModifiedTime": "2018-07-05T23:36:16.880174+00:00",
            "resultType": "TableLevelOutput",
            "state": "COMPLETED",                   //State of migration for this table
            "tableName": "public.catalog",              //Table name
            "totalChangesApplied": 0                //Total sync changes that applied after full load
          },
          {                                     Table 2
            "cdcDeleteCounter": 0,
            "cdcInsertCounter": 50,
            "cdcUpdateCounter": 0,
            "dataErrorsCount": 0,
            "databaseName": "inventory",
            "fullLoadEndedOn": "2018-07-05T23:36:23.963138+00:00",
            "fullLoadEstFinishTime": "1970-01-01T00:00:00+00:00",
            "fullLoadStartedOn": "2018-07-05T23:36:19.302013+00:00",
            "fullLoadTotalRows": 112,
            "fullLoadTotalVolumeBytes": 46592,
            "id": "or|inventory|public|address",
            "lastModifiedTime": "2018-07-05T23:36:20.308646+00:00",
            "resultType": "TableLevelOutput",
            "state": "COMPLETED",
            "tableName": "public.orders",
            "totalChangesApplied": 0
          }
        ],                          DMS migration task state
        "state": "Running", //Migration task state – Running means it is still listening to any changes that might come in                  
        "taskType": null
      },
      "resourceGroup": "PostgresDemo",
      "type": "Microsoft.DataMigration/services/projects/tasks"
    ```

## <a name="cutover-migration-task"></a>Snabb migreringsuppgift

Databasen är klar för snabb migrering när fullständig inläsning är klar. Beroende på hur upptagen källservern är med nya transaktioner som kommer, kan DMS-aktiviteten fortfarande tillämpa ändringar när hela inläsningen är klar.

För att säkerställa att alla data har samlats in, verifiera radantal mellan käll- och måldatabaser. Du kan t.ex. använda följande kommando:

```
"migrationState": "READY_TO_COMPLETE", //Status of migration task. READY_TO_COMPLETE means database is ready for cutover
 "incomingChanges": 0,  //continue to check for a period of 5-10 minutes to make sure no new incoming changes that need to be applied to the target server
   "fullLoadTotalRows": 10, //full load for table 1
    "cdcDeleteCounter": 0,  //delete, insert and update counter on incremental sync after full load
    "cdcInsertCounter": 50,
    "cdcUpdateCounter": 0,
     "fullLoadTotalRows": 112,  //full load for table 2
```

1. Utför den snabba migreringen för databasen med hjälp av följande kommando:

    ```
    az dms project task cutover -h
    ```

    Exempel:

    ```
    az dms project task cutover --service-name PostgresCLI --project-name PGMigration --resource-group PostgresDemo --name Runnowtask  --database-name Inventory
    ```

2. Du kan övervaka förloppet för den snabba migreringen med följande kommando:

    ```
    az dms project task show --service-name PostgresCLI --project-name PGMigration --resource-group PostgresDemo --name Runnowtask
    ```

## <a name="service-project-task-cleanup"></a>Rensa tjänst, projekt, aktivitet

Om du vill avbryta eller ta bort en DMS-aktivitet, ett projekt eller en tjänst, utför du avbrottet i följande ordning:

* Avbryt den pågående uppgiften
* Ta bort uppgiften
* Ta bort projektet
* Ta bort DMS-tjänst

1. Om du vill avbryta en pågående aktivitet använder du följande kommando:

    ```
    az dms project task cancel --service-name PostgresCLI --project-name PGMigration --resource-group PostgresDemo --name Runnowtask
     ```

2. Om du vill ta bort en pågående aktivitet använder du följande kommando:
    ```
    az dms project task delete --service-name PostgresCLI --project-name PGMigration --resource-group PostgresDemo --name Runnowtask
    ```

3. Om du vill avbryta ett pågående projekt använder du följande kommando:
     ```
    az dms project task cancel -n runnowtask --project-name PGMigration -g PostgresDemo --service-name PostgresCLI
     ```

4. Om du vill ta bort ett pågående projekt använder du följande kommando:
    ```
    az dms project task delete -n runnowtask --project-name PGMigration -g PostgresDemo --service-name PostgresCLI
    ```

5. Om du vill ta bort en DMS-tjänst använder du följande kommando:

     ```
    az dms delete -g ProgresDemo -n PostgresCLI
     ```

## <a name="next-steps"></a>Nästa steg

* Information om kända problem och begränsningar när du utför onlinemigreringar till Azure Database for PostgreSQL finns i artikeln [Kända problem och lösningar för Azure Database for PostgreSQL-onlinemigreringar](known-issues-azure-postgresql-online.md).
* Mer information om Azure Database Migration Service finns i artikeln [What is the Azure Database Migration Service?](https://docs.microsoft.com/azure/dms/dms-overview) (Vad är Azure Database Migration Service?).
* Information om Azure Database för PostgreSQL, finns i artikeln [vad är Azure Database för PostgreSQL?](https://docs.microsoft.com/azure/postgresql/overview).