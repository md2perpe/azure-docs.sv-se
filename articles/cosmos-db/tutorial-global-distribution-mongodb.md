---
title: Självstudie om global distribution med hjälp av Cosmos DB:s API för MongoDB
description: Lär dig hur du konfigurerar global distribution med hjälp av Azure Cosmos DB:s API för MongoDB.
author: rimman
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: tutorial
ms.date: 12/26/2018
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: 5ae5923253575fc3dea6b90b599b9fa3d79a85b8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60578815"
---
# <a name="set-up-global-distributed-database-using-azure-cosmos-dbs-api-for-mongodb"></a>Konfigurera en globalt distribuerad databas med hjälp av Azure Cosmos DB:s API för MongoDB

I den här artikeln visar vi dig hur du använder Azure-portalen till att konfigurera en globalt distribuerad databas och ansluter till den med hjälp av Azure Cosmos DB:s API för MongoDB.

Den här artikeln beskriver följande uppgifter: 

> [!div class="checklist"]
> * Konfigurera global distribution med Azure Portal
> * Konfigurera global distribution med [Azure Cosmos DB:s API för MongoDB](mongodb-introduction.md)

[!INCLUDE [cosmos-db-tutorial-global-distribution-portal](../../includes/cosmos-db-tutorial-global-distribution-portal.md)]

## <a name="verifying-your-regional-setup"></a>Verifiera dina regionala inställningar 
Ett enkelt sätt att kontrollera din globala konfiguration med Cosmos DB:s API för MongoDB är att köra kommandot *isMaster()* från Mongo-gränssnittet.

Från ditt Mongo-gränssnitt:

   ```
      db.isMaster()
   ```
   
Exempel på resultat:

   ```JSON
      {
         "_t": "IsMasterResponse",
         "ok": 1,
         "ismaster": true,
         "maxMessageSizeBytes": 4194304,
         "maxWriteBatchSize": 1000,
         "minWireVersion": 0,
         "maxWireVersion": 2,
         "tags": {
            "region": "South India"
         },
         "hosts": [
            "vishi-api-for-mongodb-southcentralus.documents.azure.com:10255",
            "vishi-api-for-mongodb-westeurope.documents.azure.com:10255",
            "vishi-api-for-mongodb-southindia.documents.azure.com:10255"
         ],
         "setName": "globaldb",
         "setVersion": 1,
         "primary": "vishi-api-for-mongodb-southindia.documents.azure.com:10255",
         "me": "vishi-api-for-mongodb-southindia.documents.azure.com:10255"
      }
   ```

## <a name="connecting-to-a-preferred-region"></a>Ansluta till en önskad region 

Med Azure Cosmos DB:s API för MongoDB kan du ange samlingens läsinställningar för en globalt distribuerad databas. För både läsningar med låg fördröjning och med globalt hög tillgänglighet rekommenderar vi att du ställer in din samlings läsinställningar på *närmaste*. Läsinställningen *närmaste* är konfigurerad att läsa från den närmaste regionen.

```csharp
var collection = database.GetCollection<BsonDocument>(collectionName);
collection = collection.WithReadPreference(new ReadPreference(ReadPreferenceMode.Nearest));
```

För program med en primär läs-/skrivregion och en sekundär region för haveriberedskapsscenarier, rekommenderar vi att ange samlingens läsinställning till *sekundärt önskad*. Läsinställningen *sekundärt önskad* är konfigurerad att läsa från den sekundära regionen när den primära regionen inte är tillgänglig.

```csharp
var collection = database.GetCollection<BsonDocument>(collectionName);
collection = collection.WithReadPreference(new ReadPreference(ReadPreferenceMode.SecondaryPreferred));
```

Slutligen kan du ange dina läsregioner manuellt, om du vill. Du kan ange regionstaggen i dina läsinställningar.

```csharp
var collection = database.GetCollection<BsonDocument>(collectionName);
var tag = new Tag("region", "Southeast Asia");
collection = collection.WithReadPreference(new ReadPreference(ReadPreferenceMode.Secondary, new[] { new TagSet(new[] { tag }) }));
```

Och med detta är den här självstudiekursen klar. Mer information om hur du kan hantera ditt globalt replikerade kontos konsekvens finns i [Konsekvensnivåer i Azure Cosmos DB](consistency-levels.md). Mer information om hur global databasreplikering fungerar i Azure Cosmos DB finns i [Distribuera data globalt med Azure Cosmos DB](distribute-data-globally.md).

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du gjort följande:

> [!div class="checklist"]
> * Konfigurera global distribution med Azure Portal
> * Konfigurera global distribution med Cosmos DB:s API för MongoDB

Du kan nu fortsätta till nästa självstudiekurs och lära dig hur du utvecklar lokalt med hjälp av den lokala Azure Cosmos DB-emulatorn.

> [!div class="nextstepaction"]
> [Utveckla lokalt med Azure Cosmos DB-emulatorn](local-emulator.md)
