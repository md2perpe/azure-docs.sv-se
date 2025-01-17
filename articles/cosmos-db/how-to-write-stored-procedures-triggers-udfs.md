---
title: Skriva lagrade procedurer, utlösare och användardefinierade funktioner i Azure Cosmos DB
description: Lär dig hur du definierar lagrade procedurer, utlösare och användardefinierade funktioner i Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 05/21/2019
ms.author: mjbrown
ms.openlocfilehash: 66e0a7e13df9eddcd722492c9c894721517af5f9
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65968929"
---
# <a name="how-to-write-stored-procedures-triggers-and-user-defined-functions-in-azure-cosmos-db"></a>Skriva lagrade procedurer, utlösare och användardefinierade funktioner i Azure Cosmos DB

Azure Cosmos DB tillhandahåller språkintegrerad, transaktionell körning av JavaScript som gör att du kan skriva **lagrade procedurer**, **utlösare** och **användardefinierade funktioner**. När du använder SQL-API:et i Azure Cosmos DB kan du definiera de lagrade procedurerna, utlösarna och de användardefinierade funktionerna i JavaScript. Du kan skriva logiken i JavaScript och köra den i databasmotorn. Du kan skapa och köra utlösare, lagrade procedurer och användardefinierade funktioner med hjälp av [Azure Portal](https://portal.azure.com/), [det språkintegrerade fråge-API:et för JavaScript i Azure Cosmos DB](javascript-query-api.md) och [SQL-API:et för klient-SDK i Azure Cosmos DB](sql-api-dotnet-samples.md). 

Innan du kan anropa en lagrad procedur, utlösare eller användardefinierad funktion måste du registrera den. Mer information finns i [How to work with stored procedures, triggers, user-defined functions in Azure Cosmos DB](how-to-use-stored-procedures-triggers-udfs.md) (Arbeta med lagrade procedurer, utlösare och användardefinierade funktioner i Azure Cosmos DB).

> [!NOTE]
> När du kör en lagrad procedur med partitionerade behållare måste ett partitionsnyckelvärde anges i alternativen för begäran. Lagrade procedurer är alltid begränsade till en partitionsnyckel. Objekt som har ett annan partitionsnyckelvärde visas inte för den lagrade proceduren. Detta gäller även för utlösare.

## <a id="stored-procedures"></a>Skriva lagrade procedurer

Lagrade procedurer skrivs med JavaScript och kan skapa, uppdatera, läsa, fråga och ta bort objekt i en Azure Cosmos-container. Lagrade procedurer registreras per samling och kan användas med alla dokument eller bifogade filer i den samlingen.

**Exempel**

Här är en enkel lagrad procedur som returnerar svaret ”Hello World”.

```javascript
var helloWorldStoredProc = {
    id: "helloWorld",
    serverScript: function () {
        var context = getContext();
        var response = context.getResponse();

        response.setBody("Hello, World");
    }
}
```

Kontextobjektet ger åtkomst till alla åtgärder som kan utföras i Azure Cosmos DB, samt åtkomst till begärande- och svarsobjekten. I det här fallet kan du använda svarsobjekt för att ange svarsinnehållet som ska skickas tillbaka till klienten.

När den har skrivits måste den lagrade proceduren registreras med en samling. Mer information finns i artikeln [How to use stored procedures in Azure Cosmos DB](how-to-use-stored-procedures-triggers-udfs.md#stored-procedures) (Använda lagrade procedurer i Azure Cosmos DB).

### <a id="create-an-item"></a>Skapa ett objekt med hjälp av en lagrad procedur

När du skapar ett objekt med hjälp av den lagrade proceduren objektet infogas i Azure Cosmos DB-behållare och ett id för den nyligen skapade objekten returneras. Genereringen av ett objekt är en asynkron åtgärd och är beroende av JavaScript-motringningsfunktionerna. Motringningsfunktionerna har två parametrar – en för felobjektet om åtgärden misslyckas och en annan för ett returvärde; i detta fall objektet som skapats. I motringningen kan du antingen hantera undantaget eller utlösa ett fel. Om ingen motringning har definierats och det uppstår ett fel, genererar Azure Cosmos DB-körningen ett fel. 

Den lagrade proceduren innehåller också en parameter som ställer in beskrivningen. Det är ett booleskt värde. Om parametern har värdet true och beskrivningen saknas, genererar den lagrade proceduren ett undantag. I annat fall fortsätter resten av den lagrade proceduren att köras.

Följande exempel på en lagrad procedur använder ett nytt Azure Cosmos DB-objekt som indata, infogar det i Azure Cosmos DB-containern och returnerar ID:t för det nya objektet. I det här exemplet använder vi ToDoList-exemplet från [snabbstarten om .NET och SQL-API:et](create-sql-api-dotnet.md)

```javascript
function createToDoItem(itemToCreate) {

    var context = getContext();
    var container = context.getCollection();

    var accepted = container.createDocument(container.getSelfLink(),
        itemToCreate,
        function (err, itemCreated) {
            if (err) throw new Error('Error' + err.message);
            context.getResponse().setBody(itemCreated.id)
        });
    if (!accepted) return;
}
```

### <a name="arrays-as-input-parameters-for-stored-procedures"></a>Matriser som indataparametrar för lagrade procedurer 

När du definierar en lagrad procedur på Azure Portal skickas alltid indataparametrar som en sträng till den lagrade proceduren. Även om du skickar en matris med strängar som indata, konverteras matrisen till en sträng och skickas till den lagrade proceduren. Du kan undvika detta genom att definiera en funktion i den lagrade proceduren som parsar strängen som en matris. Följande kod visar hur du parsar en strängindataparameter som en matris:

```javascript
function sample(arr) {
    if (typeof arr === "string") arr = JSON.parse(arr);

    arr.forEach(function(a) {
        // do something here
        console.log(a);
    });
}
```

### <a id="transactions"></a>Transaktioner i lagrade procedurer

Du kan implementera transaktioner för objekt i en container med hjälp av en lagrad procedur. I följande exempel används transaktioner i en fotbollsapp för att byta spelare mellan två lag i en enda åtgärd. Den lagrade proceduren försöker läsa de två Azure Cosmos DB-objekten som motsvarar de spelar-ID:n som skickas som ett argument. Om båda spelarna hittas uppdaterar den lagrade proceduren objekten genom att byta deras lag. Om det uppstår ett fel utlöser den lagrade proceduren ett JavaScript-undantag som implicit avbryter transaktionen.

```javascript
// JavaScript source code
function tradePlayers(playerId1, playerId2) {
    var context = getContext();
    var container = context.getCollection();
    var response = context.getResponse();

    var player1Document, player2Document;

    // query for players
    var filterQuery = 
    {     
        'query' : 'SELECT * FROM Players p where p.id = @playerId1',
        'parameters' : [{'name':'@playerId1', 'value':playerId1}] 
    };
            
    var accept = container.queryDocuments(container.getSelfLink(), filterQuery, {},
        function (err, items, responseOptions) {
            if (err) throw new Error("Error" + err.message);

            if (items.length != 1) throw "Unable to find both names";
            player1Item = items[0];

            var filterQuery2 = 
            {     
                'query' : 'SELECT * FROM Players p where p.id = @playerId2',
                'parameters' : [{'name':'@playerId2', 'value':playerId2}] 
            };
            var accept2 = container.queryDocuments(container.getSelfLink(), filterQuery2, {},
                function (err2, items2, responseOptions2) {
                    if (err2) throw new Error("Error" + err2.message);
                    if (items2.length != 1) throw "Unable to find both names";
                    player2Item = items2[0];
                    swapTeams(player1Item, player2Item);
                    return;
                });
            if (!accept2) throw "Unable to read player details, abort ";
        });

    if (!accept) throw "Unable to read player details, abort ";

    // swap the two players’ teams
    function swapTeams(player1, player2) {
        var player2NewTeam = player1.team;
        player1.team = player2.team;
        player2.team = player2NewTeam;

        var accept = container.replaceDocument(player1._self, player1,
            function (err, itemReplaced) {
                if (err) throw "Unable to update player 1, abort ";

                var accept2 = container.replaceDocument(player2._self, player2,
                    function (err2, itemReplaced2) {
                        if (err) throw "Unable to update player 2, abort"
                    });

                if (!accept2) throw "Unable to update player 2, abort";
            });

        if (!accept) throw "Unable to update player 1, abort";
    }
}
```

### <a id="bounded-execution"></a>Begränsad körning i lagrade procedurer

Följande är ett exempel på en lagrad procedur som massimporterar objekt till en Azure Cosmos-container. Den lagrade proceduren hanterar begränsad körning genom att kontrollera det booleska returvärdet från `createDocument`, och använder sedan antalet objekt som infogats i varje anrop av den lagrade proceduren för att spåra och återuppta förloppet över batchar.

```javascript
function bulkImport(items) {
    var container = getContext().getCollection();
    var containerLink = container.getSelfLink();

    // The count of imported items, also used as current item index.
    var count = 0;

    // Validate input.
    if (!items) throw new Error("The array is undefined or null.");

    var itemsLength = items.length;
    if (itemsLength == 0) {
        getContext().getResponse().setBody(0);
    }

    // Call the create API to create an item.
    tryCreate(items[count], callback);

    // Note that there are 2 exit conditions:
    // 1) The createDocument request was not accepted.
    //    In this case the callback will not be called, we just call setBody and we are done.
    // 2) The callback was called items.length times.
    //    In this case all items were created and we don’t need to call tryCreate anymore. Just call setBody and we are done.
    function tryCreate(item, callback) {
        var isAccepted = container.createDocument(containerLink, item, callback);

        // If the request was accepted, callback will be called.
        // Otherwise report current count back to the client,
        // which will call the script again with remaining set of items.
        if (!isAccepted) getContext().getResponse().setBody(count);
    }

    // This is called when container.createDocument is done in order to process the result.
    function callback(err, item, options) {
        if (err) throw err;

        // One more item has been inserted, increment the count.
        count++;

        if (count >= itemsLength) {
            // If we created all items, we are done. Just set the response.
            getContext().getResponse().setBody(count);
        } else {
            // Create next document.
            tryCreate(items[count], callback);
        }
    }
}
```

## <a id="triggers"></a>Skriva utlösare

Azure Cosmos DB stöder för- och efterutlösare. Förutlösare körs innan ett databasobjekt ändras och efterutlösare körs när ett databasobjekt har ändrats.

### <a id="pre-triggers"></a>Förutlösare

Följande exempel visar hur en förutlösare används för att verifiera egenskaperna för ett Azure Cosmos DB-objekt som håller på att skapas. I det här exemplet använder vi ToDoList-exemplet från [snabbstarten om .NET och SQL-API:et](create-sql-api-dotnet.md) för att lägga till en tidsstämpelegenskap till ett nyligen tillagt objekt om det inte redan innehåller en.

```javascript
function validateToDoItemTimestamp() {
    var context = getContext();
    var request = context.getRequest();

    // item to be created in the current operation
    var itemToCreate = request.getBody();

    // validate properties
    if (!("timestamp" in itemToCreate)) {
        var ts = new Date();
        itemToCreate["timestamp"] = ts.getTime();
    }

    // update the item that will be created
    request.setBody(itemToCreate);
}
```

Förutlösare kan inte ha några indataparametrar. Begärandeobjektet i utlösaren används för att manipulera begärandemeddelandet som är associerat med åtgärden. I exemplet ovan körs förutlösaren när ett Azure Cosmos DB-objekt skapas, och själva begärandemeddelandet innehåller objektet som ska skapas i JSON-format.

När utlösare har registrerats kan du ange vilka åtgärder som de kan köras med. Den här utlösaren bör skapas med `TriggerOperation`-värdet `TriggerOperation.Create`, vilket innebär att det inte går att använda utlösaren i en ersättningsåtgärd som du ser i följande kod.

Exempel på hur du registrerar och anropar en förutlösare finns i artiklarna om [förutlösare](how-to-use-stored-procedures-triggers-udfs.md#pre-triggers) och [efterutlösare](how-to-use-stored-procedures-triggers-udfs.md#post-triggers). 

### <a id="post-triggers"></a>Efterutlösare

Följande exempel visar en efterutlösare. Den här utlösaren frågar efter metadataobjektet och uppdaterar det med information om det nyligen skapade objektet.


```javascript
function updateMetadata() {
var context = getContext();
var container = context.getCollection();
var response = context.getResponse();

// item that was created
var createdItem = response.getBody();

// query for metadata document
var filterQuery = 'SELECT * FROM root r WHERE r.id = "_metadata"';
var accept = container.queryDocuments(container.getSelfLink(), filterQuery,
    updateMetadataCallback);
if(!accept) throw "Unable to update metadata, abort";

function updateMetadataCallback(err, items, responseOptions) {
    if(err) throw new Error("Error" + err.message);
        if(items.length != 1) throw 'Unable to find metadata document';

        var metadataItem = items[0];

        // update metadata
        metadataItem.createdItems += 1;
        metadataItem.createdNames += " " + createdItem.id;
        var accept = container.replaceDocument(metadataItem._self,
            metadataItem, function(err, itemReplaced) {
                    if(err) throw "Unable to update metadata, abort";
            });
        if(!accept) throw "Unable to update metadata, abort";
        return;
}
```

En viktig sak att notera är den transaktionella körningen av utlösare i Azure Cosmos DB. Efter utlösaren körs som en del av samma transaktion för det underliggande objektet själva. Ett undantag under körningen efter utlösaren misslyckas hela transaktionen. Något allokerat kommer att återställas och ett undantag returneras.

Exempel på hur du registrerar och anropar en förutlösare finns i artiklarna om [förutlösare](how-to-use-stored-procedures-triggers-udfs.md#pre-triggers) och [efterutlösare](how-to-use-stored-procedures-triggers-udfs.md#post-triggers). 

## <a id="udfs"></a>Skriva användardefinierade funktioner

Följande exempel skapar en användardefinierad funktion som beräknar inkomstskatten för olika inkomstintervall. Den här användardefinierade funktionen används sedan i en fråga. I det här exemplet förutsätter vi att det finns en container med namnet ”Incomes” med följande egenskaper:

```json
{
   "name": "Satya Nadella",
   "country": "USA",
   "income": 70000
}
```

Följande är en funktionsdefinition som beräknar inkomstskatten för olika inkomstintervall:

```javascript
function tax(income) {

        if(income == undefined)
            throw 'no input';

        if (income < 1000)
            return income * 0.1;
        else if (income < 10000)
            return income * 0.2;
        else
            return income * 0.4;
    }
```

Exempel på hur du registrerar och använder användardefinierade funktioner finns i artikeln [How to use user-defined functions in Azure Cosmos DB](how-to-use-stored-procedures-triggers-udfs.md#udfs) (Använda användardefinierade funktioner i Azure Cosmos DB).

## <a name="next-steps"></a>Nästa steg

Lär dig mer om begrepp och hur du skriver och använder lagrade procedurer, utlösare och användardefinierade funktioner i Azure Cosmos DB:

* [Registrera och använda lagrade procedurer, utlösare och användardefinierade funktioner i Azure Cosmos DB](how-to-use-stored-procedures-triggers-udfs.md)

* [Skriva lagrade procedurer och utlösare med hjälp fråge-API:et för Javascript i Azure Cosmos DB](how-to-write-javascript-query-api.md)

* [Arbeta med lagrade procedurer, utlösare och användardefinierade funktioner i Azure Cosmos DB](stored-procedures-triggers-udfs.md)

* [Arbeta med det språkintegrerade fråge-API:et för JavaScript i Azure Cosmos DB](javascript-query-api.md)
