---
title: Hantera principer för indexering i Azure Cosmos DB
description: Lär dig hur du hanterar indexera principer i Azure Cosmos DB
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: sample
ms.date: 06/27/2019
ms.author: thweiss
ms.openlocfilehash: 9fe58e9d49a46fd03a2938f2860a3a5d476813af
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67441778"
---
# <a name="manage-indexing-policies-in-azure-cosmos-db"></a>Hantera principer för indexering i Azure Cosmos DB

I Azure Cosmos DB data har indexerats följa [indexeringsprinciper](index-policy.md) som har definierats för varje behållare. Standard indexering principen för nya behållare framtvingar intervallet index för en sträng eller ett tal och spatialindex för alla GeoJSON-objekt av typen punkt. Den här principen kan åsidosättas:

- Från Azure portal
- Med hjälp av Azure CLI
- med någon av de SDK: er

En [indexering principuppdatering](index-policy.md#modifying-the-indexing-policy) utlöser ett index omvandling. Det går också att spåra förloppet för den här omvandlingen från SDK: erna.

> [!NOTE]
> Som en del av uppgraderingen SDK och -portalen kan utvecklas vi index principen för att anpassas till en ny indexlayout som vi har distribuerat till nya behållare. Med den här nya layout indexeras alla primitiva datatyper som intervall med full precision (-1). Därför exponeras index typer och precision inte för användaren längre. I framtiden, behöver användarna du bara lägga till sökvägar i avsnittet includedPaths och ignorera indexKinds och precision. Den här ändringen har ingen inverkan på prestanda och du kan fortsätta att uppdatera indexeringsprincip med samma syntax. Du kan fortsätta att använda alla exempel i vår befintliga dokumentation för att uppdatera index.

## <a name="use-the-azure-portal"></a>Använda Azure-portalen

Azure Cosmos-behållare lagra sina indexeringsprincip som ett JSON-dokument som Azure-portalen kan du redigera direkt.

1. Logga in på [Azure Portal](https://portal.azure.com/).

1. Skapa ett nytt Azure Cosmos DB-konto eller välj ett befintligt konto.

1. Öppna den **Datautforskaren** rutan och välj den behållare som du vill arbeta med.

1. Klicka på **skala och inställningar**.

1. Ändra indexera JSON-dokumentet för principen (se exempel [nedan](#indexing-policy-examples))

1. Klicka på **spara** när du är klar.

![Hantera indexering med hjälp av Azure-portalen](./media/how-to-manage-indexing-policy/indexing-policy-portal.png)

## <a name="use-the-azure-cli"></a>Använda Azure CLI

Den [az cosmosdb collection update](/cli/azure/cosmosdb/collection#az-cosmosdb-collection-update) kommandot från Azure CLI kan du ersätta JSON-definition för en behållare indexeringsprincip:

```azurecli-interactive
az cosmosdb collection update \
    --resource-group-name $resourceGroupName \
    --name $accountName \
    --db-name $databaseName \
    --collection-name $containerName \
    --indexing-policy "{\"indexingMode\": \"consistent\", \"includedPaths\": [{ \"path\": \"/*\", \"indexes\": [{ \"dataType\": \"String\", \"kind\": \"Range\" }] }], \"excludedPaths\": [{ \"path\": \"/headquarters/employees/?\" } ]}"
```

## <a name="use-the-net-sdk-v2"></a>Använd .NET SDK V2

Den `DocumentCollection` objekt från den [.NET SDK v2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/) (se [den här snabbstarten](create-sql-api-dotnet.md) rörande användningen) exponerar en `IndexingPolicy` egenskap som du kan ändra den `IndexingMode` och lägga till eller ta bort `IncludedPaths` och `ExcludedPaths`.

```csharp
// retrieve the container's details
ResourceResponse<DocumentCollection> containerResponse = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("database", "container"));
// set the indexing mode to Consistent
containerResponse.Resource.IndexingPolicy.IndexingMode = IndexingMode.Consistent;
// add an excluded path
containerResponse.Resource.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/headquarters/employees/?" });
// update the container with our changes
await client.ReplaceDocumentCollectionAsync(containerResponse.Resource);
```

Om du vill följa upp förloppet för omvandling av index, skicka en `RequestOptions` objekt som anger den `PopulateQuotaInfo` egenskap `true`.

```csharp
// retrieve the container's details
ResourceResponse<DocumentCollection> container = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("database", "container"), new RequestOptions { PopulateQuotaInfo = true });
// retrieve the index transformation progress from the result
long indexTransformationProgress = container.IndexTransformationProgress;
```

## <a name="use-the-java-sdk"></a>Använda Java SDK

Den `DocumentCollection` objekt från den [Java SDK](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb) (se [snabbstarten](create-sql-api-java.md) rörande användningen) exponerar `getIndexingPolicy()` och `setIndexingPolicy()` metoder. Den `IndexingPolicy` objekt de manipulera kan du ändra indexering läge och lägga till eller ta bort undantagna och undantagna sökvägar.

```java
// retrieve the container's details
Observable<ResourceResponse<DocumentCollection>> containerResponse = client.readCollection(String.format("/dbs/%s/colls/%s", "database", "container"), null);
containerResponse.subscribe(result -> {
    DocumentCollection container = result.getResource();
    IndexingPolicy indexingPolicy = container.getIndexingPolicy();
    // set the indexing mode to Consistent
    indexingPolicy.setIndexingMode(IndexingMode.Consistent);
    Collection<ExcludedPath> excludedPaths = new ArrayList<>();
    ExcludedPath excludedPath = new ExcludedPath();
    excludedPath.setPath("/*");
    // add an excluded path
    excludedPaths.add(excludedPath);
    indexingPolicy.setExcludedPaths(excludedPaths);
    // update the container with our changes
    client.replaceCollection(container, null);
});
```

Om du vill spåra förloppet index omvandling i en behållare, skicka en `RequestOptions` objekt som begär kvotinformation fyllas, sedan hämta värdet från den `x-ms-documentdb-collection-index-transformation-progress` svarshuvudet.

```java
// set the RequestOptions object
RequestOptions requestOptions = new RequestOptions();
requestOptions.setPopulateQuotaInfo(true);
// retrieve the container's details
Observable<ResourceResponse<DocumentCollection>> containerResponse = client.readCollection(String.format("/dbs/%s/colls/%s", "database", "container"), requestOptions);
containerResponse.subscribe(result -> {
    // retrieve the index transformation progress from the response headers
    String indexTransformationProgress = result.getResponseHeaders().get("x-ms-documentdb-collection-index-transformation-progress");
});
```

## <a name="use-the-nodejs-sdk"></a>Använda Node.js-SDK

Den `ContainerDefinition` gränssnitt från [Node.js SDK](https://www.npmjs.com/package/@azure/cosmos) (se [den här snabbstarten](create-sql-api-nodejs.md) rörande användningen) visar en `indexingPolicy` egenskap som du kan ändra den `indexingMode` och lägga till eller ta bort `includedPaths` och `excludedPaths`.

```javascript
// retrieve the container's details
const containerResponse = await client.database('database').container('container').read();
// set the indexing mode to Consistent
containerResponse.body.indexingPolicy.indexingMode = "consistent";
// add an excluded path
containerResponse.body.indexingPolicy.excludedPaths.push({ path: '/headquarters/employees/?' });
// update the container with our changes
const replaceResponse = await client.database('database').container('container').replace(containerResponse.body);
```

Om du vill spåra förloppet index omvandling i en behållare, skicka en `RequestOptions` objekt som anger den `populateQuotaInfo` egenskap `true`, hämta värdet från den `x-ms-documentdb-collection-index-transformation-progress` svarshuvudet.

```javascript
// retrieve the container's details
const containerResponse = await client.database('database').container('container').read({
    populateQuotaInfo: true
});
// retrieve the index transformation progress from the response headers
const indexTransformationProgress = replaceResponse.headers['x-ms-documentdb-collection-index-transformation-progress'];
```

## <a name="use-the-python-sdk"></a>Använda Python SDK

När du använder den [Python SDK](https://pypi.org/project/azure-cosmos/) (se [snabbstarten](create-sql-api-python.md) rörande användningen), konfigurationen av behållaren hanteras som en ordlista. Det är möjligt att komma åt indexprincip och alla dess attribut från den här ordlistan.

```python
containerPath = 'dbs/database/colls/collection'
# retrieve the container's details
container = client.ReadContainer(containerPath)
# set the indexing mode to Consistent
container['indexingPolicy']['indexingMode'] = 'consistent'
# add an excluded path
container['indexingPolicy']['excludedPaths'] = [{"path" : "/headquarters/employees/?"}]
# update the container with our changes
response = client.ReplaceContainer(containerPath, container)
```

## <a name="indexing-policy-examples"></a>Exempel på indexering

Här följer några exempel på indexering principer som visas i deras JSON-format, vilket är hur de är tillgängliga på Azure-portalen. Samma parametrar kan anges via Azure CLI eller alla SDK: N.

### <a name="opt-out-policy-to-selectively-exclude-some-property-paths"></a>Slipp princip för att selektivt undanta vissa sökvägar för egenskapen
```
    {
        "indexingMode": "consistent",
        "includedPaths": [
            {
                "path": "/*",
                "indexes": [
                    {
                        "kind": "Range",
                        "dataType": "Number"
                    },
                    {
                        "kind": "Range",
                        "dataType": "String"
                    },
                    {
                        "kind": "Spatial",
                        "dataType": "Point"
                    }
                ]
            }
        ],
        "excludedPaths": [
            {
                "path": "/path/to/single/excluded/property/?"
            },
            {
                "path": "/path/to/root/of/multiple/excluded/properties/*"
            }
        ]
    }
```

### <a name="opt-in-policy-to-selectively-include-some-property-paths"></a>Delta i principen för att selektivt ta med vissa egenskapen sökvägar
```
    {
        "indexingMode": "consistent",
        "includedPaths": [
            {
                "path": "/path/to/included/property/?",
                "indexes": [
                    {
                        "kind": "Range",
                        "dataType": "Number"
                    }
                ]
            },
            {
                "path": "/path/to/root/of/multiple/included/properties/*",
                "indexes": [
                    {
                        "kind": "Range",
                        "dataType": "Number"
                    }
                ]
            }
        ],
        "excludedPaths": [
            {
                "path": "/*"
            }
        ]
    }
```

Obs! Allmänt rekommenderar vi att du använder en **avstår** indexeringspolicy så att Azure Cosmos DB proaktivt Indexera nya egenskaper som kan läggas till i din modell.

### <a name="using-a-spatial-index-on-a-specific-property-path-only"></a>Med hjälp av en spatialindexet på en specifik egenskapssökväg
```
    {
        "indexingMode": "consistent",
        "includedPaths": [
            {
                "path": "/*",
                "indexes": [
                    {
                        "kind": "Range",
                        "dataType": "Number"
                    },
                    {
                        "kind": "Range",
                        "dataType": "String"
                    }
                ]
            },
            {
                "path": "/path/to/geojson/property/?",
                "indexes": [
                    {
                        "kind": "Spatial",
                        "dataType": "Point"
                    }
                ]
            }
        ],
        "excludedPaths": []
    }
```

### <a name="excluding-all-property-paths-but-keeping-indexing-active"></a>Exkludera alla sökvägar för egenskapen men behåller indexering active

Den här principen kan användas i situationer där den [Time-to-Live (TTL) funktionen](time-to-live.md) är aktiv, men inga sekundära index krävs (för att använda Azure Cosmos DB som en ren nyckel / värde-lager).
```
    {
        "indexingMode": "consistent",
        "includedPaths": [],
        "excludedPaths": [{
            "path": "/*"
        }]
    }
```

### <a name="no-indexing"></a>Ingen indexering
```
    {
        "indexingMode": "none"
    }
```

## <a name="composite-indexing-policy-examples"></a>Sammansatta indexering princip-exempel

Förutom eller undantas sökvägar för enskilda egenskaper, kan du även ange ett sammansatt index. Om du vill köra en fråga som har en `ORDER BY` sats för flera egenskaper, en [sammansatta index](index-policy.md#composite-indexes) på dessa egenskaper krävs.

### <a name="composite-index-defined-for-name-asc-age-desc"></a>Sammansatta index har definierats för (asc namn, ålder desc):
```
    {  
        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[  

        ],
        "compositeIndexes":[  
            [  
                {  
                    "path":"/name",
                    "order":"ascending"
                },
                {  
                    "path":"/age",
                    "order":"descending"
                }
            ]
        ]
    }
```

Den här sammansatta index skulle kunna stödja följande två frågor:

Fråga #1:
```sql
    SELECT *
    FROM c
    ORDER BY name asc, age desc    
```

Fråga #2:
```sql
    SELECT *
    FROM c
    ORDER BY name desc, age asc
```

### <a name="composite-index-defined-for-name-asc-age-asc-and-name-asc-age-desc"></a>Sammansatta index som definierats för (asc namn, ålder asc) och (namnge asc, ålder desc):

Du kan definiera flera olika sammansatta index i samma indexeringsprincipen. 
```
    {  
        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[  

        ],
        "compositeIndexes":[  
            [  
                {  
                    "path":"/name",
                    "order":"ascending"
                },
                {  
                    "path":"/age",
                    "order":"ascending"
                }
            ],
            [  
                {  
                    "path":"/name",
                    "order":"ascending"
                },
                {  
                    "path":"/age",
                    "order":"descending"
                }
            ]
        ]
    }
```

### <a name="composite-index-defined-for-name-asc-age-asc"></a>Sammansatta index har definierats för (asc namn, ålder asc):

Det är valfritt att ange vilken ordning. Om inte anges, stigande ordning.
```
{  
        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[  

        ],
        "compositeIndexes":[  
            [  
                {  
                    "path":"/name",
                },
                {  
                    "path":"/age",
                }
            ]
        ]
}
```

## <a name="next-steps"></a>Nästa steg

Läs mer om indexering i följande artiklar:

- [Indexering, översikt](index-overview.md)
- [Indexeringspolicy](index-policy.md)
