---
title: 'Snabbstart: Söka efter entiteter med SDK för entitetssökning i Bing för C#'
titleSuffix: Azure Cognitive Services
description: Använd den här snabbstarten för att söka efter entiteter med SDK för entitetssökning i Bing för C#.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: quickstart
ms.date: 02/01/2019
ms.author: aahi
ms.openlocfilehash: 13ef0734345df17adb2303471b8cb4178f95a2f6
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65813752"
---
# <a name="send-a-search-request-with-the-bing-entity-search-sdk-for-c"></a>Skicka en sökbegäran med SDK för entitetssökning i Bing för C#

Använd den här snabbstarten om du vill börja söka efter entiteter med SDK för entitetssökning i Bing för C#. Även om entitetssökning i Bing har ett REST API som är kompatibelt med de flesta programmeringsspråk så tillhandahåller SDK:n ett enkelt sätt att integrera tjänsten i dina program. Källkoden för det här exemplet finns på [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingEntitySearch).


## <a name="prerequisites"></a>Nödvändiga komponenter

* En utgåva av [Visual Studio 2017 eller senare](https://www.visualstudio.com/downloads/).
* [Json.NET](https://www.newtonsoft.com/json) framework, tillgänglig som ett NuGet-paket.
* Om du använder Linux/Mac OS kan det här programmet köras med [Mono](https://www.mono-project.com/).
* [NuGet-paket för SDK för Nyhetssökning i Bing](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.EntitySearch/1.2.0). Installering av det här paketet installerar även följande:
    * Microsoft.Rest.ClientRuntime
    * Microsoft.Rest.ClientRuntime.Azure
    * Newtonsoft.Json

Lägg till Bing entitet Search SDK i Visual Studio-projektet genom att använda den **hantera NuGet-paket** alternativet från **Solution Explorer**, och Lägg till den `Microsoft.Azure.CognitiveServices.Search.EntitySearch` paketet.


[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-entity-search-signup-requirements.md)]

## <a name="create-and-initialize-an-application"></a>Skapa och initiera ett program

1. skapa en ny C#-konsollösning i Visual Studio. Lägg sedan till följande i huvudkodfilen.

    ```csharp
    using System;
    using System.Linq;
    using System.Text;
    using Microsoft.Azure.CognitiveServices.Search.EntitySearch;
    using Microsoft.Azure.CognitiveServices.Search.EntitySearch.Models;
    using Newtonsoft.Json;
    ```

## <a name="create-a-client-and-send-a-search-request"></a>Skapa en klient och skicka en sökbegäran

1. Skapa en ny sökklient. Lägg till din prenumerationsnyckel genom att skapa en ny `ApiKeyServiceClientCredentials`.

    ```csharp
    var client = new EntitySearchAPI(new ApiKeyServiceClientCredentials("YOUR-ACCESS-KEY"));
    ```

1. Använd klientens `Entities.Search()`-funktion för att söka efter din fråga:
    
    ```csharp
    var entityData = client.Entities.Search(query: "Satya Nadella");
    ```

## <a name="get-and-print-an-entity-description"></a>Hämta och skriva ut en entitetsbeskrivning

1. Om API:et returnerade sökresultat hämtar du huvudentiteten från `entityData`.

    ```csharp
    var mainEntity = entityData.Entities.Value.Where(thing => thing.EntityPresentationInfo.EntityScenario == EntityScenario.DominantEntity).FirstOrDefault();
    ```

2. Skriva ut beskrivningen av huvudentiteten 

    ```csharp
    Console.WriteLine(mainEntity.Description);
    ```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa en enkelsidig webbapp](../tutorial-bing-entities-search-single-page-app.md)

* [Vad är API:et för entitetssökning i Bing?](../overview.md )