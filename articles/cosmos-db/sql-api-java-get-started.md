---
title: 'Självstudie om NoSQL: SQL API för Azure Cosmos DB Java SDK'
description: En självstudiekurs om NoSQL som skapar en onlinedatabas och ett Java-konsolprogram med hjälp av SQL API för Azure Cosmos DB. Azure SQL är en NoSQL-databas för JSON.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: tutorial
ms.date: 12/22/2018
ms.author: sngun
ms.openlocfilehash: 24de1fe0695d0f7634d8f20f1876b5000496dbea
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66475780"
---
# <a name="nosql-tutorial-build-a-sql-api-java-console-application"></a>Självstudie om NoSQL: Skapa ett Java-konsolprogram med SQL API

> [!div class="op_single_selector"]
> * [NET](sql-api-get-started.md)
> * [.NET (förhandsversion)](sql-api-dotnet-get-started-preview.md)
> * [.NET Core](sql-api-dotnetcore-get-started.md)
> * [.NET Core (förhandsversion)](sql-api-dotnet-core-get-started-preview.md)
> * [Java](sql-api-java-get-started.md)
> * [Async Java](sql-api-async-java-get-started.md)
> * [Node.js](sql-api-nodejs-get-started.md)
> 

Välkommen till självstudiekursen om NoSQL för SQL API för Azure Cosmos DB Java SDK! När du har genomfört den här självstudiekursen har du ett konsolprogram som skapar och skickar frågor till Azure Cosmos DB-resurser.

Vi går igenom:

* Skapa och ansluta till ett Azure Cosmos DB-konto
* Konfigurera en Visual Studio-lösning
* Skapa en onlinedatabas
* Skapa en samling
* Skapa JSON-dokument
* Skicka frågor till samlingen
* Skapa JSON-dokument
* Skicka frågor till samlingen
* Ersätta ett dokument
* Ta bort ett dokument
* Ta bort databasen

Nu sätter vi igång!

## <a name="prerequisites"></a>Nödvändiga komponenter
Se till att du har följande:

* Ett aktivt Azure-konto. Om du inte har ett kan du registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/free/). 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [Git](https://git-scm.com/downloads).
* [Java Development Kit (JDK) 7+](https://aka.ms/azure-jdks).
* [Maven](https://maven.apache.org/download.cgi).

## <a name="step-1-create-an-azure-cosmos-db-account"></a>Steg 1: Skapa ett Azure Cosmos DB-konto
Nu ska vi skapa ett Azure Cosmos DB-konto. Om du redan har ett konto som du vill använda kan du gå vidare till [Klona GitHub-projektet](#GitClone). Om du använder Azure Cosmos DB-emulatorn följer du stegen i [Azure Cosmos DB-emulatorn](local-emulator.md) för att konfigurera emulatorn och gå vidare till [Klona GitHub-projektet](#GitClone).

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a id="GitClone"></a>Steg 2: Klona GitHub-projekt
Du kan komma igång genom att klona GitHub-lagringsplatsen. Gå till [Komma igång med Azure Cosmos DB och Java](https://github.com/Azure-Samples/documentdb-java-getting-started). Från en lokal katalog kör du till exempel följande för att hämta exempelprojektet lokalt.

    git clone git@github.com:Azure-Samples/azure-cosmos-db-documentdb-java-getting-started.git

    cd azure-cosmos-db-documentdb-java-getting-started

Katalogen innehåller en `pom.xml`-fil för projektet och en `src`-mapp med Java-källkod, inklusive `Program.java` som visar hur du utför enkla åtgärder med Azure Cosmos DB, t.ex. hur du skapar dokument och frågar data i en samling. `pom.xml` innehåller ett beroende för [Azure Cosmos DB Java SDK för Maven](https://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb).

    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-documentdb</artifactId>
        <version>LATEST</version>
    </dependency>

## <a id="Connect"></a>Steg 3: Ansluta till ett Azure Cosmos DB-konto
Gå sedan tillbaka till [Azure Portal](https://portal.azure.com) för att hämta din slutpunkt och primära huvudnyckel. Azure DB Cosmos-slutpunkten och den primära nyckeln krävs för att ditt program ska veta vart det ska ansluta, och för att Azure Cosmos DB ska lita på programmets anslutning.

Gå till ditt Azure Cosmos DB-konto på Azure Portal och klicka på **Nycklar**. Kopiera URI:n från portalen och klistra in den i `https://FILLME.documents.azure.com` i Program.java-filen. Kopiera sedan PRIMÄRNYCKELN från portalen och klistra in den i `FILLME`.

    this.client = new DocumentClient(
        "https://FILLME.documents.azure.com",
        "FILLME"
        , new ConnectionPolicy(),
        ConsistencyLevel.Session);

![Skärmbild av Azure Portal som används i NoSQL-självstudiekursen för att skapa en Java-konsolprogram. Visar ett Azure Cosmos DB-konto där den AKTIVA hubben är markerad, där knappen NYCKLAR är markerad på bladet för Azure Cosmos DB-kontot och där värdena för URI, PRIMÄR NYCKEL och SEKUNDÄR NYCKEL är markerade på bladet Nycklar][keys]

## <a name="step-4-create-a-database"></a>Steg 4: Skapa en databas
Azure Cosmos DB-[databasen](databases-containers-items.md#azure-cosmos-databases) kan skapas med metoden [createDatabase](/java/api/com.microsoft.azure.documentdb.documentclient.createdatabase) i **DocumentClient**-klassen. En databas är en logisk container för JSON-dokumentlagring, partitionerad över samlingarna.

    Database database = new Database();
    database.setId("familydb");
    this.client.createDatabase(database, null);

## <a id="CreateColl"></a>Steg 5: Skapa en samling
> [!WARNING]
> **createCollection** skapar en ny samling med reserverat dataflöde, vilket får priskonsekvenser. Mer information finns på vår [prissättningssida](https://azure.microsoft.com/pricing/details/cosmos-db/).
> 
> 

En samling kan skapas med metoden [createCollection](/java/api/com.microsoft.azure.documentdb.documentclient.createcollection) för klassen **DocumentClient**. En samling är en container för JSON-dokument och associerad JavaScript-applogik.


    DocumentCollection collectionInfo = new DocumentCollection();
    collectionInfo.setId("familycoll");

    // Azure Cosmos DB collections can be reserved with throughput specified in request units/second. 
    // Here we create a collection with 400 RU/s.
    RequestOptions requestOptions = new RequestOptions();
    requestOptions.setOfferThroughput(400);

    this.client.createCollection("/dbs/familydb", collectionInfo, requestOptions);

## <a id="CreateDoc"></a>Steg 6: Skapa JSON-dokument
Ett dokument kan skapas med hjälp av metoden [createDocument](/java/api/com.microsoft.azure.documentdb.documentclient.createdocument) för klassen **DocumentClient**. Dokument är användardefinierat (godtyckligt) JSON-innehåll. Vi kan nu infoga ett eller flera dokument. Om du redan har data som du vill lagra i databasen kan du använda [datamigreringsverktyget](import-data.md) för Azure Cosmos DB för att importera dina data till en databas.

    // Insert your Java objects as documents 
    Family andersenFamily = new Family();
    andersenFamily.setId("Andersen.1");
    andersenFamily.setLastName("Andersen")

    // More initialization skipped for brevity. You can have nested references
    andersenFamily.setParents(new Parent[] { parent1, parent2 });
    andersenFamily.setDistrict("WA5");
    Address address = new Address();
    address.setCity("Seattle");
    address.setCounty("King");
    address.setState("WA");

    andersenFamily.setAddress(address);
    andersenFamily.setRegistered(true);

    this.client.createDocument("/dbs/familydb/colls/familycoll", family, new RequestOptions(), true);

![Diagram som illustrerar den hierarkiska relationen mellan kontot, onlinedatabasen, samlingen och dokumenten som används i NoSQL-självstudiekursen för att skapa ett Java-konsolprogram](./media/sql-api-get-started/nosql-tutorial-account-database.png)

## <a id="Query"></a>Steg 7: Köra frågor mot Azure Cosmos DB-resurser
Azure Cosmos DB stöder [komplexa frågor](how-to-sql-query.md) mot JSON-dokument som lagras i varje samling.  Följande exempelkod visar hur du frågar dokument i Azure Cosmos DB med hjälp av SQL-syntaxen med [queryDocuments](/java/api/com.microsoft.azure.documentdb.documentclient.querydocuments)-metoden.

    FeedResponse<Document> queryResults = this.client.queryDocuments(
        "/dbs/familydb/colls/familycoll",
        "SELECT * FROM Family WHERE Family.lastName = 'Andersen'", 
        null);

    System.out.println("Running SQL query...");
    for (Document family : queryResults.getQueryIterable()) {
        System.out.println(String.format("\tRead %s", family));
    }

## <a id="ReplaceDocument"></a>Steg 8: Ersätta JSON-dokument
Azure Cosmos DB stöder uppdatering av JSON-dokument med hjälp av [replaceDocument](/java/api/com.microsoft.azure.documentdb.documentclient.replacedocument)-metoden.

    // Update a property
    andersenFamily.Children[0].Grade = 6;

    this.client.replaceDocument(
        "/dbs/familydb/colls/familycoll/docs/Andersen.1", 
        andersenFamily,
        null);

## <a id="DeleteDocument"></a>Steg 9: Ta bort JSON-dokument
På motsvarande sätt stöder Azure Cosmos DB borttagning av JSON-dokument med hjälp av [deleteDocument](/java/api/com.microsoft.azure.documentdb.documentclient.deletedocument)-metoden.  

    this.client.delete("/dbs/familydb/colls/familycoll/docs/Andersen.1", null);

## <a id="DeleteDatabase"></a>Steg 10: Ta bort databasen
Om du tar bort databasen du skapade försvinner databasen och alla underordnade resurser (t.ex. samlingar och dokument).

    this.client.deleteDatabase("/dbs/familydb", null);

## <a id="Run"></a>Steg 11: Kör ditt Java-konsolprogram i sin helhet!
Om du vill köra programmet från konsolen går du till projektmappen och kompilerar med hjälp av Maven:
    
    mvn package

Om du kör `mvn package` hämtas det senaste Azure Cosmos DB-biblioteket från Maven och `GetStarted-0.0.1-SNAPSHOT.jar` skapas. Kör sedan appen genom att köra:

    mvn exec:java -D exec.mainClass=GetStarted.Program

Grattis! Du har slutfört den här självstudiekursen om NoSQL och har ett fungerande Java-konsolprogram!

## <a name="next-steps"></a>Nästa steg
* Vill du ha en självstudie om Java-webbappar? Mer information finns i [Skapa ett webbprogram i Java med Azure Cosmos DB](sql-api-java-application.md).
* Lär dig hur du [övervakar ett Azure Cosmos DB-konto](monitor-accounts.md).
* Kör frågor mot vår exempeldatauppsättning i [Query Playground](https://www.documentdb.com/sql/demo).

[keys]: media/sql-api-get-started/nosql-tutorial-keys.png
