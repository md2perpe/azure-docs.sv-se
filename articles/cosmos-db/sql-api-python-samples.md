---
title: SQL API Python-exempel för Azure Cosmos DB
description: Hitta Python-exempel på GitHub för vanliga uppgifter i Azure Cosmos DB, inklusive CRUD-åtgärder.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: python
ms.topic: sample
ms.date: 03/14/2018
ms.author: sngun
ms.openlocfilehash: cf296d8bb494307dbb58b9de522d55a83892c6d5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60546631"
---
# <a name="azure-cosmos-db-python-examples"></a>Azure Cosmos DB Python-exempel

> [!div class="op_single_selector"]
> * [.NET-exempel](sql-api-dotnet-samples.md)
> * [Java-exempel](sql-api-java-samples.md)
> * [Async Java-exempel](sql-api-async-java-samples.md)
> * [Node.js-exempel](sql-api-nodejs-samples.md)
> * [Python-exempel](sql-api-python-samples.md)
> * [Azure-kodexempelgalleri](https://azure.microsoft.com/resources/samples/?sort=0&service=cosmos-db)
> 
> 

Exempellösningarna som utför CRUD-åtgärder och andra vanliga åtgärder på Azure Cosmos DB-resurser finns på GitHub-lagringsplatsen [azure-documentdb-python](https://github.com/Azure/azure-documentdb-python). Den här artikeln innehåller:

* Länkar till uppgifterna i var och en av Python-exempelprojektfilerna. 
* Länkar till det relaterade API-referensinnehållet.

**Förutsättningar**

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

- Du kan [aktivera Visual Studio-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio): Din Visual Studio-prenumeration ger dig krediter varje månad som kan användas för Azure-betaltjänster.

[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

Du behöver också [Python SDK](sql-api-sdk-python.md). 
   
   > [!NOTE]
   > Varje exempel är självständigt. Det konfigurerar sig själv och rensar upp efter sig. Exemplen gör flera anrop till [CosmosClient.CreateContainer](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient#createcontainer-database-link--collection--options-none-). Varje gång det är klart faktureras prenumerationen för en timmes användning. Mer information om Azure Cosmos DB-fakturering finns i [Prissättning för Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/).
   > 
   > 

## <a name="database-examples"></a>Databasexempel
Den [Program.py](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DatabaseManagement/Program.py) -filen för den [DatabaseManagement](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DatabaseManagement) projektet visas hur du gör följande uppgifter. Läs mer om Azure Cosmos-databaserna innan du kör följande exempel, i [arbetar med databaser, behållare och objekt](databases-containers-items.md) konceptuella artikeln. 

| Aktivitet | API-referens |
| --- | --- |
| [Skapa en databas](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DatabaseManagement/Program.py#L65-L76) |[CosmosClient.CreateDatabase](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient#createdatabase-database--options-none-) |
| [Läsa en databas via ID](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DatabaseManagement/Program.py#L79-L96) |[CosmosClient.ReadDatabase](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient#readdatabase-database-link--options-none-) |
| [Lista databaser för ett konto](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DatabaseManagement/Program.py#L99-L110) |[CosmosClient.ReadDatabases](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient#readdatabases-options-none-) |
| [Ta bort en databas](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DatabaseManagement/Program.py#L113-L126) |[CosmosClient.DeleteDatabase](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient#deletedatabase-database-link--options-none-) |

## <a name="collection-examples"></a>Samlingsexempel
Den [Program.py](https://github.com/Azure/azure-documentdb-python/blob/master/samples/CollectionManagement/Program.py) -filen för den [CollectionManagement](https://github.com/Azure/azure-documentdb-python/blob/master/samples/CollectionManagement) projektet visas hur du gör följande uppgifter. Läs om Azure Cosmos-samlingar innan du kör följande exempel i [arbetar med databaser, behållare och objekt](databases-containers-items.md) konceptuella artikeln. 

| Aktivitet | API-referens |
| --- | --- |
| [Skapa en samling](https://github.com/Azure/azure-documentdb-python/blob/master/samples/CollectionManagement/Program.py#L84-L135) |[CosmosClient.CreateContainer](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient#createcontainer-database-link--collection--options-none-) |
| [Läsa en lista med alla samlingar i en databas](https://github.com/Azure/azure-documentdb-python/blob/master/samples/CollectionManagement/Program.py#L210-L222) |[CosmosClient.ReadContainers](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient#readcontainers-database-link--options-none-) |
| [Hämta en samling via ID](https://github.com/Azure/azure-documentdb-python/blob/master/samples/CollectionManagement/Program.py#L190-L208) |[CosmosClient.ReadContainer](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient#readcontainer-collection-link--options-none-) |
| [Ändra dataflödet för en samling](https://github.com/Azure/azure-documentdb-python/blob/master/samples/CollectionManagement/Program.py#L184-L188) | [CosmosClient.ReplaceOffer](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient#replaceoffer-offer-link--offer-)|
| [Ta bort en samling](https://github.com/Azure/azure-documentdb-python/blob/master/samples/CollectionManagement/Program.py#L224-L238) |[CosmosClient.DeleteContainer](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient#deletecontainer-collection-link--options-none-) |

## <a name="document-examples"></a>Exempel på dokument
Den [Program.py](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DocumentManagement/Program.py) -filen för den [DocumentManagement](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DocumentManagement) projektet visas hur du gör följande uppgifter. Läs mer om Azure Cosmos-dokument innan du kör följande exempel, i [arbetar med databaser, behållare och objekt](databases-containers-items.md) konceptuella artikeln. 

| Aktivitet | API-referens |
| --- | --- |
| [Skapa ett dokument](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DocumentManagement/Program.py#L55-L66) |[CosmosClient.CreateItem](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient#createitem-database-or-container-link--document--options-none-) |
| [Skapa en samling med dokument](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DocumentManagement/Program.py#L55-L66) |[CosmosClient.CreateItem](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient#createitem-database-or-container-link--document--options-none-) |
| [Läsa ett dokument via ID](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DocumentManagement/Program.py#L69-L78) |[CosmosClient.ReadItem](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient#readitem-document-link--options-none-) |
| [Läsa alla dokument i en samling](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DocumentManagement/Program.py#L81-L92) |[CosmosClient.ReadItems](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient#readitems-collection-link--feed-options-none-) |
| [Ersätta dokument med villkorlig ETag-kontroll](https://github.com/Azure/azure-cosmos-python/blob/a21f6fb4bad3f59909ef43558b598f9fb476b7bc/test/crud_tests.py#L1216-L1218) | [CosmosClient.ReplaceItem](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient#replaceitem-document-link--new-document--options-none-) |

## <a name="indexing-examples"></a>Indexeringsexempel
Den [Program.py](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py) -filen för den [IndexManagement](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement) projektet visas hur du gör följande uppgifter.  Läs mer om indexering i Azure Cosmos DB innan du kör följande exempel, i [indexeringsprinciper](index-policy.md), [indexering typer](index-types.md), och [indexering sökvägar](index-paths.md) konceptuella artiklar. 

| Aktivitet | API-referens |
| --- | --- |
| [Använda manuell (istället för automatisk) indexering](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L245-L246) | Princip för automatisk indexering |
| [Undanta angivna dokumentsökvägar från indexet](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L294-L367) | Indexeringsprincip med undantagna sökvägar|
| [Undanta ett dokument från indexet](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L204-L210) |[IndexingDirective.Exclude](/python/api/azure-cosmos/azure.cosmos.documents.indexingdirective#exclude) |
| [Ange indexeringsläge](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L533) |[IndexingMode](/python/api/azure-cosmos/azure.cosmos.documents.indexingmode) |
| [Använda intervallindex i strängar](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L440-L456) | Indexeringsprincip med inkluderade sökvägar|
| [Utföra en indextransformering](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L514-L559) |[CosmosClient.ReplaceContainer](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient#replacecontainer-collection-link--collection--options-none-) |

## <a name="query-examples"></a>Exempelfrågor
Exempelprojekten visar också hur du gör följande i fråga. Mer information om SQL-referens för frågor i Azure Cosmos DB innan du kör följande exempel finns [SQL-fråga exempel](how-to-sql-query.md) konceptuella artikeln. Mer information om SQL-referens för frågor i Azure Cosmos DB innan du kör följande exempel finns [SQL-fråga exempel](how-to-sql-query.md) konceptuella artikeln. 


| Aktivitet | API-referens |
| --- | --- |
| [Fråga ett konto för en databas](https://github.com/Azure/azure-documentdb-python/blob/master/samples/DatabaseManagement/Program.py#L49-L62) |[CosmosClient.QueryDatabases](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient#querydatabases-query--options-none-) |
| [Fråga för dokument](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L152-L169) |[CosmosClient.QueryItems](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient#queryitems-database-or-container-link--query--options-none--partition-key-none-) |
| [Tvinga en intervallsökning av en hash-indexerad sökväg](https://github.com/Azure/azure-documentdb-python/blob/master/samples/IndexManagement/Program.py#L409-L415) |[HttpHeaders.EnableScanInQuery](/python/api/azure-cosmos/azure.cosmos.http_constants.httpheaders#enablescaninquery) |

