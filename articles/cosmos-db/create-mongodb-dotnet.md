---
title: Skapa en webbapp med Azure Cosmos DB:s API för MongoDB och .NET SDK
description: Presenterar ett .NET-kodexempel som du kan använda för att ansluta till och ställa frågor men hjälp av Azure Cosmos DB:s API för MongoDB.
author: rimman
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 05/21/2019
ms.author: rimman
ms.openlocfilehash: 73caa57fe7e721d69091bfb6ee74f7d88baf1ba3
ms.sourcegitcommit: 59fd8dc19fab17e846db5b9e262a25e1530e96f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65979096"
---
# <a name="quickstart-build-a-net-web-app-using-azure-cosmos-dbs-api-for-mongodb"></a>Snabbstart: Skapa en .NET-webbapp via Azure Cosmos DB API för MongoDB 

> [!div class="op_single_selector"]
> * [NET](create-mongodb-dotnet.md)
> * [Java](create-mongodb-java.md)
> * [Node.js](create-mongodb-nodejs.md)
> * [Python](create-mongodb-flask.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-golang.md)
>  

Azure Cosmos DB är Microsofts globalt distribuerade databastjänst för flera datamodeller. Du kan snabbt skapa och ställa frågor mot databaser med dokument, nyckel/värde-par och grafer. Du får fördelar av den globala distributionen och den horisontella skalningsförmågan som ligger i grunden hos Cosmos DB. 

Den här snabbstarten visar hur du skapar ett Cosmos-konto med [Azure Cosmos DB-API för MongoDB](mongodb-introduction.md). Sedan kommer du att skapa och distribuera en uppgiftslistewebbapp som är byggd med [MongoDB .NET-drivrutinen](https://docs.mongodb.com/ecosystem/drivers/csharp/).

## <a name="prerequisites-to-run-the-sample-app"></a>Nödvändiga förutsättningar för att köra exempelappen

Om du vill köra exemplet måste du ha [Visual Studio](https://www.visualstudio.com/downloads/) och ett giltigt Azure Cosmos DB-konto.

Om du inte redan har Visual Studio, hämta [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/) med den **ASP.NET och webbutveckling** arbetsbelastning som installeras med installationsprogrammet.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 

<a id="create-account"></a>
## <a name="create-a-database-account"></a>Skapa ett databaskonto

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

Exemplet som beskrivs i den här artikeln är kompatibelt med MongoDB.Driver version 2.6.1.

## <a name="clone-the-sample-app"></a>Klona exempelappen

Ladda först ned eller klona exempelappen från GitHub. 

1. Öppna en kommandotolk, skapa en ny mapp som heter git-samples och stäng sedan kommandotolken.

    ```bash
    md "C:\git-samples"
    ```

2. Öppna ett git-terminalfönster, t.ex. git bash, och använd kommandot `cd` för att ändra till den nya mappen där du vill installera exempelappen.

    ```bash
    cd "C:\git-samples"
    ```

3. Klona exempellagringsplatsen med följande kommando. Detta kommando skapar en kopia av exempelappen på din dator. 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-mongodb-dotnet-getting-started.git
    ```

Om du inte vill använda git kan du [ladda ned projektet som en ZIP-fil](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-dotnet-getting-started/archive/master.zip).

## <a name="review-the-code"></a>Granska koden

Det här steget är valfritt. Om du vill lära dig hur databasresurserna skapas i koden kan du granska följande kodavsnitt. Annars kan du gå vidare till [Uppdatera din anslutningssträng](#update-your-connection-string). 

Följande kodavsnitt är alla hämtade från filen Dal.cs i katalogen DAL.

* Initiera klienten.

    ```cs
        MongoClientSettings settings = new MongoClientSettings();
        settings.Server = new MongoServerAddress(host, 10255);
        settings.UseSsl = true;
        settings.SslSettings = new SslSettings();
        settings.SslSettings.EnabledSslProtocols = SslProtocols.Tls12;

        MongoIdentity identity = new MongoInternalIdentity(dbName, userName);
        MongoIdentityEvidence evidence = new PasswordEvidence(password);

        settings.Credential = new MongoCredential("SCRAM-SHA-1", identity, evidence);

        MongoClient client = new MongoClient(settings);
    ```

* Hämta databasen och samlingen.

    ```cs
    private string dbName = "Tasks";
    private string collectionName = "TasksList";

    var database = client.GetDatabase(dbName);
    var todoTaskCollection = database.GetCollection<MyTask>(collectionName);
    ```

* Hämta alla dokument.

    ```cs
    collection.Find(new BsonDocument()).ToList();
    ```

Skapa en uppgift och infoga den i samlingen

   ```csharp
    public void CreateTask(MyTask task)
    {
        var collection = GetTasksCollectionForEdit();
        try
        {
            collection.InsertOne(task);
        }
        catch (MongoCommandException ex)
        {
            string msg = ex.Message;
        }
    }
   ```
   På liknande sätt kan du uppdatera och ta bort dokument med hjälp av metoderna [collection.UpdateOne()](https://docs.mongodb.com/stitch/mongodb/actions/collection.updateOne/index.html) och [collection.DeleteOne()](https://docs.mongodb.com/stitch/mongodb/actions/collection.deleteOne/index.html). 

## <a name="update-your-connection-string"></a>Uppdatera din anslutningssträng

Gå nu tillbaka till Azure-portalen för att hämta information om din anslutningssträng och kopiera den till appen.

1. Klicka på **Anslutningssträng** och därefter på **Läs- och skrivnycklar** i den vänstra navigeringen i ditt Cosmos-konto i [Azure Portal](https://portal.azure.com/). Använd kopieringsknapparna till höger på skärmen och kopiera Användarnamn, Lösenord och Värd till filen Dal.cs i nästa steg.

2. Öppna filen **Dal.cs** i katalogen **DAL**. 

3. Kopiera ditt **användarnamn** från portalen (med kopieringsknappen) och gör det till värdet för **username** i filen **Dal.cs**. 

4. Kopiera sedan **värden** från portalen och gör den till värdet för **host** i filen **Dal.cs**. 

5. Kopiera slutligen **lösenordet** från portalen och gör den till värdet för **password** i filen **Dal.cs**. 

Du har nu uppdaterat din app med all information den behöver för att kommunicera med Cosmos DB. 
    
## <a name="run-the-web-app"></a>Kör webbappen

1. I Visual Studio högerklickar du på projektet i **Solution Explorer** och därefter på **Hantera NuGet-paket**. 

2. I NuGet-rutan **Bläddra** skriver du in *MongoDB.Driver*.

3. Installera biblioteket **MongoDB.Driver** från resultaten. Det här installerar MongoDB.Driver-paketet samt alla beroenden.

4. Tryck på Ctrl + F5 för att köra programmet. Appen visas i din webbläsare. 

5. Klicka på **Skapa** i webbläsaren och skapa några nya uppgifter i din uppgiftslisteapp.

## <a name="review-slas-in-the-azure-portal"></a>Granska serviceavtal i Azure-portalen

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du lärt dig hur du skapar ett Cosmos-konto, skapar en samling och kör en konsolapp. Du kan nu importera ytterligare data till din Cosmos-databas. 

> [!div class="nextstepaction"]
> [Importera MongoDB-data till Azure Cosmos DB](mongodb-migrate.md)
