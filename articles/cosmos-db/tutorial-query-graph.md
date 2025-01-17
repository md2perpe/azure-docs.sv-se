---
title: Hur frågar man efter diagramdata SQL i Azure Cosmos DB?
description: Lär dig att fråga efter diagramdata SQL i Azure Cosmos DB
author: luisbosquez
ms.author: lbosq
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: tutorial
ms.date: 01/02/2018
ms.reviewer: sngun
ms.openlocfilehash: 2bc79801864481562967702a7c52a7670950199b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61293120"
---
# <a name="tutorial-query-azure-cosmos-db-gremlin-api-by-using-gremlin"></a>Självstudier: Köra frågor mot Azure Cosmos DB Gremlin API med hjälp av Gremlin

Azure Cosmos DB [Gremlin-API](graph-introduction.md) stöder [Gremlin](https://github.com/tinkerpop/gremlin/wiki)-frågor. Den här artikeln innehåller exempeldokument och frågor för att komma igång. En detaljerad Gremlin-referens finns i artikeln [Gremlin-support](gremlin-support.md).

Den här artikeln beskriver följande uppgifter: 

> [!div class="checklist"]
> * Fråga efter data med Gremlin

## <a name="prerequisites"></a>Nödvändiga komponenter

För att de här frågorna ska fungera måste du ha ett konto i Azure Cosmos DB och ha diagramdata i containern. Har du detta? Slutför [snabbstarten på 5 minuter](create-graph-dotnet.md) eller [självstudien för utvecklare](tutorial-query-graph.md) om du behöver skapa ett konto och fylla i databasen. Du kan köra följande frågor från [Gremlin-konsolen](https://tinkerpop.apache.org/docs/current/reference/#gremlin-console) eller din favoritdrivrutin för Gremlin.

## <a name="count-vertices-in-the-graph"></a>Antal hörn i diagrammet

Följande kodfragment visar hur du räknar antalet hörn i diagrammet:

```
g.V().count()
```

## <a name="filters"></a>Filter

Du kan använda filter med Gremlins `has` och `hasLabel`-steg, samt kombinera dem med hjälp av `and`, `or` och `not` för att skapa fler komplexa filter. Azure Cosmos DB innehåller schemaoberoende indexering av alla egenskaper i dina hörn och grader för snabba frågor:

```
g.V().hasLabel('person').has('age', gt(40))
```

## <a name="projection"></a>Projektion

Du kan projicera vissa egenskaper i frågeresultaten med hjälp av steget `values`:

```
g.V().hasLabel('person').values('firstName')
```

## <a name="find-related-edges-and-vertices"></a>Hitta relaterade kanter och hörn

Hittills har vi endast sett frågeoperatorer som fungerar i alla databaser. Diagram är snabba och effektiva vid bläddringsåtgärder när du behöver navigera till relaterade kanter och hörn. Nu ska vi hitta alla Thomas vänner. Vi kan göra detta med hjälp av Gremlins `outE`-steg för att söka efter alla ut-kanter från Thomas och sedan bläddra till in-hörnen från dessa kanter med Gremlins `inV`-steg:

```cs
g.V('thomas').outE('knows').inV().hasLabel('person')
```

Nästa fråga utför två hopp för att hitta alla Thomas ”vänner till vänner”, genom att anropa `outE` och `inV` två gånger. 

```cs
g.V('thomas').outE('knows').inV().hasLabel('person').outE('knows').inV().hasLabel('person')
```

Du kan skapa mer komplexa frågor och implementera kraftfull bläddringslogik i diagrammet med Gremlin, inklusive att blanda filteruttryck, utföra loopar med hjälp av `loop`-steget och implementera villkorlig navigering med hjälp av `choose`-steget. Läs mer om vad du kan göra med [Gremlin-support](gremlin-support.md)!

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du gjort följande:

> [!div class="checklist"]
> * Lärt dig hur man frågar med Graph 

Du kan nu gå vidare till avsnittet Begrepp om du vill ha mer information om Cosmos DB.

> [!div class="nextstepaction"]
> [Global distribution](distribute-data-globally.md) 

