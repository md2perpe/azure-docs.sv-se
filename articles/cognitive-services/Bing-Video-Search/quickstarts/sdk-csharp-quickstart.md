---
title: 'Snabbstart: Sök efter videor med SDK för videosökning i Bing för C#'
titleSuffix: Azure Cognitive Services
description: Använd den här snabbstarten för att skicka videosökningsbegäranden med SDK för videosökning i Bing för C#.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: quickstart
ms.date: 06/26/2019
ms.author: aahi
ms.openlocfilehash: 3673f18ff38b2ae98180f470b9f76f1fc57ee8b6
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67442540"
---
# <a name="quickstart-perform-a-video-search-with-the-bing-video-search-sdk-for-c"></a>Snabbstart: Utför en videosökning med SDK för videosökning i Bing för C#

Använd den här snabbstarten om du vill börja söka efter nyheter med SDK för videosökning i Bing för C#. Även om Videosökning i Bing har ett REST API som är kompatibelt med de flesta programmeringsspråk så är SDK:n ett enkelt sätt att integrera tjänsten i dina program. Källkoden för det här exemplet finns på [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingVideoSearch) med ytterligare kommentarer och funktioner.

## <a name="prerequisites"></a>Förutsättningar

* En utgåva av [Visual Studio 2017 eller senare](https://visualstudio.microsoft.com/downloads/).
* Json.NET framework, tillgänglig som ett [NuGet-paket](https://www.nuget.org/packages/Newtonsoft.Json/).

Om du vill lägga till Search SDK för Bing-Video i projektet, Välj **hantera NuGet-paket** från **Solution Explorer** i Visual Studio. Lägg till paketet `Microsoft.Azure.CognitiveServices.Search.VideoSearch`.

När du installerar [[SDK-paketet för NuGet-videosökning]](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.VideoSearch/1.2.0) installeras även följande beroenden:

* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json

[!INCLUDE [cognitive-services-bing-video-search-signup-requirements](../../../../includes/cognitive-services-bing-video-search-signup-requirements.md)]


## <a name="create-and-initialize-a-project"></a>Skapa och initiera ett projekt

1. Skapa en ny C#-konsollösning i Visual Studio. Lägg sedan till följande i huvudkodfilen.

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using Microsoft.Azure.CognitiveServices.Search.VideoSearch;
    using Microsoft.Azure.CognitiveServices.Search.VideoSearch.Models;
    ```

2. Instantiera klienten genom att skapa ett nytt `ApiKeyServiceClientCredentials`-objekt med prenumerationsnyckeln och anropa konstruktorn.

    ```csharp
    var client = new VideoSearchAPI(new ApiKeyServiceClientCredentials("YOUR-ACCESS-KEY"));
    ```

## <a name="send-a-search-request-and-process-the-results"></a>Skicka en sökbegäran och bearbeta resultatet

1. Använd klienten för att skicka en sökbegäran. Använd ”SwiftKey” för sökfrågan.

    ```csharp
    var videoResults = client.Videos.SearchAsync(query: "SwiftKey").Result;
    ```

2. Om alla resultat returnerades hämtar du det första med `videoResults.Value[0]`. Skriv sedan ut videons ID, rubrik och url.

    ```csharp
    if (videoResults.Value.Count > 0)
    {
        var firstVideoResult = videoResults.Value[0];

        Console.WriteLine($"\r\nVideo result count: {videoResults.Value.Count}");
        Console.WriteLine($"First video id: {firstVideoResult.VideoId}");
        Console.WriteLine($"First video name: {firstVideoResult.Name}");
        Console.WriteLine($"First video url: {firstVideoResult.ContentUrl}");
    }
    else
    {
        Console.WriteLine("Couldn't find video results!");
    }
    ```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa en webbapp med en sida](../tutorial-bing-video-search-single-page-app.md)

## <a name="see-also"></a>Se också 

* [Vad är API för videosökning i Bing?](../overview.md)
* [Exempel med Cognitive Services SDK för .NET](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)
