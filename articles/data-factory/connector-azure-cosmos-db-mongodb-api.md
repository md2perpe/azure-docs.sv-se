---
title: Kopiera data till och från Azure Cosmos DB API för MongoDB med Data Factory | Microsoft Docs
description: Lär dig hur du kopierar data från datalager som stöds till eller från Azure Cosmos DB API för MongoDB till mottagarens datalager med Data Factory.
services: data-factory, cosmosdb
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: multiple
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 12/20/2018
ms.author: jingwang
ms.openlocfilehash: 82418c03039219adedf45828d769d278a14499ff
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61259725"
---
# <a name="copy-data-to-or-from-azure-cosmos-dbs-api-for-mongodb-by-using-azure-data-factory"></a>Kopiera data till eller från Azure Cosmos DB API för MongoDB med hjälp av Azure Data Factory

Den här artikeln beskrivs hur du använder Kopieringsaktivitet i Azure Data Factory för att kopiera data från och till Azure Cosmos DB API för MongoDB. Artikeln bygger vidare på [Kopieringsaktivitet i Azure Data Factory](copy-activity-overview.md), som anger en allmän översikt över Kopieringsaktivitet.

>[!NOTE]
>Den här anslutningen endast stöder kopiera data till och från Azure Cosmos DB API för MongoDB. SQL-API finns i [Cosmos DB SQL API-kopplingen](connector-azure-cosmos-db.md). Andra API-typer stöds inte nu.

## <a name="supported-capabilities"></a>Funktioner som stöds

Du kan kopiera data från Azure Cosmos DB API för MongoDB till alla mottagarens datalager eller kopiera data från alla dataarkiv till Azure Cosmos DB API för MongoDB. En lista över data lagrar att det stöder Kopieringsaktiviteten som källor och mottagare, finns i [datalager och format som stöds](copy-activity-overview.md#supported-data-stores-and-formats).

Du kan använda Azure Cosmos DB: s API för MongoDB-anslutningsprogrammet kan:

- Kopiera data från och till den [Azure Cosmos DB-API för MongoDB](https://docs.microsoft.com/azure/cosmos-db/mongodb-introduction).
- Skriva till Azure Cosmos DB som **infoga** eller **upsert**.
- Importera och exportera JSON-dokument som – är, eller kopiera data från eller till en datauppsättning i tabellformat. Exempel: en SQL-databas och en CSV-fil. Dokument som att kopiera-är eller från JSON-filer eller till eller från en annan Azure Cosmos DB-samling, finns i Importera eller exportera JSON-dokument.

## <a name="get-started"></a>Kom igång

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Följande avsnitt innehåller information om egenskaper som du kan använda för att definiera Data Factory-entiteter som är specifika för Azure Cosmos DB-API för MongoDB.

## <a name="linked-service-properties"></a>Länkade tjänstegenskaper

Följande egenskaper har stöd för Azure Cosmos DB: s API för MongoDB länkade tjänsten:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| type | Den **typ** egenskapen måste anges till **CosmosDbMongoDbApi**. | Ja |
| connectionString |Ange anslutningssträngen för ditt Azure Cosmos DB API för MongoDB. Du hittar den i Azure portal -> din Cosmos DB-bladet -> primär eller sekundär anslutningssträng med mönstret för `mongodb://<cosmosdb-name>:<password>@<cosmosdb-name>.documents.azure.com:10255/?ssl=true&replicaSet=globaldb`. <br/><br />Markera det här fältet som en **SecureString** Skriv för att lagra den på ett säkert sätt i Data Factory. Du kan också [refererar till en hemlighet som lagras i Azure Key Vault](store-credentials-in-key-vault.md). |Ja |
| database | Namnet på databasen som du vill komma åt. | Ja |
| connectVia | Den [Integreringskörningen](concepts-integration-runtime.md) för att ansluta till datalagret. Du kan använda Azure Integration Runtime eller en lokal integration runtime (om ditt datalager finns i ett privat nätverk). Om egenskapen inte anges används standard Azure Integration Runtime. |Nej |

**Exempel**

```json
{
    "name": "CosmosDbMongoDBAPILinkedService",
    "properties": {
        "type": "CosmosDbMongoDbApi",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "mongodb://<cosmosdb-name>:<password>@<cosmosdb-name>.documents.azure.com:10255/?ssl=true&replicaSet=globaldb"
            },
            "database": "myDatabase"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Egenskaper för datamängd

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera datauppsättningar finns i [datauppsättningar och länkade tjänster](concepts-datasets-linked-services.md). Följande egenskaper har stöd för Azure Cosmos DB-API för MongoDB-datauppsättning:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| type | Den **typ** egenskap måste anges till **CosmosDbMongoDbApiCollection**. |Ja |
| collectionName |Namnet på Azure Cosmos DB-samling. |Ja |

**Exempel**

```json
{
    "name": "CosmosDbMongoDBAPIDataset",
    "properties": {
        "type": "CosmosDbMongoDbApiCollection",
        "linkedServiceName":{
            "referenceName": "<Azure Cosmos DB's API for MongoDB linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "collectionName": "<collection name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Kopiera egenskaper för aktivitet

Det här avsnittet innehåller en lista över egenskaper som har stöd för Azure Cosmos DB: s API för MongoDB-källa och mottagare.

En fullständig lista över avsnitt och egenskaper som är tillgängliga för att definiera aktiviteter finns i [Pipelines](concepts-pipelines-activities.md).

### <a name="azure-cosmos-dbs-api-for-mongodb-as-source"></a>Azure Cosmos DB-API för MongoDB som källa

Följande egenskaper stöds i Kopieringsaktiviteten **källa** avsnittet:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| type | Den **typ** egenskapen för aktiviteten kopieringskälla måste anges till **CosmosDbMongoDbApiSource**. |Ja |
| filter | Anger val av filter som använder frågeoperatorer. Om du vill returnera alla dokument i en samling, utelämnar den här parametern eller skicka ett tomt dokument ({}). | Nej |
| cursorMethods.project | Anger fälten att returnera i dokumenten för projektion. Om du vill returnera alla fält i matchande dokument, utelämnar du den här parametern. | Nej |
| cursorMethods.sort | Anger den ordning som frågan returnerar matchande dokument. Referera till [cursor.sort()](https://docs.mongodb.com/manual/reference/method/cursor.sort/#cursor.sort). | Nej |
| cursorMethods.limit | Anger det maximala antalet dokument som returnerar servern. Referera till [cursor.limit()](https://docs.mongodb.com/manual/reference/method/cursor.limit/#cursor.limit).  | Nej | 
| cursorMethods.skip | Anger hur många av dokument att hoppa över och från där MongoDB börjar att returnera resultat. Referera till [cursor.skip()](https://docs.mongodb.com/manual/reference/method/cursor.skip/#cursor.skip). | Nej |
| batchSize | Anger antalet dokument att returnera i varje batch för svaret från MongoDB-instans. I de flesta fall påverkar ändra batchstorleken inte användaren eller programmet. Cosmos DB-gränser som varje batch inte får överskrida 40MB i storlek, vilket är summan av batchSize antalet dokumentens storlek, så minska det här värdet om dokumentstorleken på din är stor. | Nej<br/>(standardvärdet är **100**) |

>[!TIP]
>ADM-stödet förbrukar BSON dokument i **strikt läge**. Kontrollera att din filterfråga är i strikt läge i stället för Shell-läge. Mer beskrivning finns på [MongoDB manuell](https://docs.mongodb.com/manual/reference/mongodb-extended-json/index.html).

**Exempel**

```json
"activities":[
    {
        "name": "CopyFromCosmosDBMongoDBAPI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure Cosmos DB's API for MongoDB input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "CosmosDbMongoDbApiSource",
                "filter": "{datetimeData: {$gte: ISODate(\"2018-12-11T00:00:00.000Z\"),$lt: ISODate(\"2018-12-12T00:00:00.000Z\")}, _id: ObjectId(\"5acd7c3d0000000000000000\") }",
                "cursorMethods": {
                    "project": "{ _id : 1, name : 1, age: 1, datetimeData: 1 }",
                    "sort": "{ age : 1 }",
                    "skip": 3,
                    "limit": 3
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="azure-cosmos-dbs-api-for-mongodb-as-sink"></a>Azure Cosmos DB-API för MongoDB som mottagare

Följande egenskaper stöds i Kopieringsaktiviteten **mottagare** avsnittet:

| Egenskap | Beskrivning | Krävs |
|:--- |:--- |:--- |
| type | Den **typ** egenskapen för mottagare för Kopieringsaktivitet måste anges till **CosmosDbMongoDbApiSink**. |Ja |
| writeBehavior |Beskriver hur du skriver data till Azure Cosmos DB. Tillåtna värden: **infoga** och **upsert**.<br/><br/>Beteendet för **upsert** är att ersätta dokumentet om ett dokument med samma ID redan finns, annars Infoga dokumentet.<br /><br />**Obs!** Data Factory genererar automatiskt ett ID för ett dokument om ett ID inte har angetts i det ursprungliga dokumentet eller genom kolumnmappning. Det innebär att måste du se till att, för **upsert** för att fungera som förväntat, dokumentet har ett ID. |Nej<br />(standardvärdet är **infoga**) |
| writeBatchSize | Den **writeBatchSize** egenskapen styr storleken på dokument att skriva i varje batch. Du kan prova att öka värdet för **writeBatchSize** att förbättra prestanda och minska värdet om dokumentstorleken på din är stor. |Nej<br />(standardvärdet är **10 000**) |
| writeBatchTimeout | Väntetid för batch Infoga åtgärden slutförs innan tidsgränsen uppnås. Tillåtna värdet är tidsintervallet. | Nej<br/>(standardvärdet är **00:30:00** – 30 minuter) |

**Exempel**

```json
"activities":[
    {
        "name": "CopyToCosmosDBMongoDBAPI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Document DB output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "CosmosDbMongoDbApiSink",
                "writeBehavior": "upsert"
            }
        }
    }
]
```

>[!TIP]
>Importera JSON-dokument som – är, referera till [importera eller exportera JSON-dokument](#import-or-export-json-documents) avsnittet, för att kopiera från tabular-formade data, se [schemamappning](#schema-mapping).

## <a name="import-or-export-json-documents"></a>Importera eller exportera JSON-dokument

Du kan använda den här Azure Cosmos DB-anslutningen för att enkelt:

* Importera JSON-dokument från olika källor till Azure Cosmos DB, såsom från Azure Blob storage, Azure Data Lake Store och andra filbaserade butiker som har stöd för Azure Data Factory.
* Exportera JSON-dokument från en Azure Cosmos DB-samling till olika filbaserade lager.
* Kopiera mellan två Azure Cosmos DB-samlingar som – är.

Hoppa över ”strukturen” för att uppnå kopian schemaoberoende (kallas även *schemat*) i avsnittet datauppsättning och schemamappning i kopieringsaktiviteten.

## <a name="schema-mapping"></a>Schemamappning

Kopiera data från Azure Cosmos DB API för MongoDB till tabular mottagare eller återförts, referera till [schemamappning](copy-activity-schema-and-type-mapping.md#schema-mapping).

Specifikt för skrivning till Cosmos DB för att se till att du fylla i Cosmos DB med rätt objekt-ID från dina källdata, till exempel du har en ”id”-kolumnen i SQL-databastabell och vill använda värdet för den som dokument-ID i MongoDB för insert/upsert , du måste ange rätt schemamappning enligt MongoDB strikt läge definition (`_id.$oid`) enligt följande:

![Mappa ID i MongoDB-mottagare](./media/connector-azure-cosmos-db-mongodb-api/map-id-in-mongodb-sink.png)

Körningsmiljön för aktiviteten nedan BSON ObjectId skapas efter kopiering i mottagare:

```json
{
    "_id": ObjectId("592e07800000000000000000")
}
``` 

## <a name="next-steps"></a>Nästa steg

En lista över datalager som Kopieringsaktiviteten som källor och egenskaperna i Azure Data Factory finns i [datalager som stöds](copy-activity-overview.md##supported-data-stores-and-formats).
