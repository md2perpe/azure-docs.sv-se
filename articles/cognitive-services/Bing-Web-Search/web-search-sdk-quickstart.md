---
title: 'Snabbstart: Använda SDK för webbsökning i Bing för C#'
titleSuffix: Azure Cognitive Services
description: SDK för Webbsökning i Bing gör det enkelt att integrera Webbsökning i Bing i C#-programmet. I den här snabbstarten lär du dig att instansiera en klient, skicka en begäran och skriva ut svaret.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: quickstart
ms.date: 04/15/2019
ms.author: aahi
ms.openlocfilehash: b1660034f1937d2d8ff9e70139407619626886a8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60498873"
---
# <a name="quickstart-use-the-bing-web-search-sdk-for-c"></a>Snabbstart: Använda SDK för webbsökning i Bing för C#

SDK för Webbsökning i Bing gör det enkelt att integrera Webbsökning i Bing i C#-programmet. I den här snabbstarten lär du dig att instansiera en klient, skicka en begäran och skriva ut svaret.

[!INCLUDE [bing-web-search-quickstart-signup](../../../includes/bing-web-search-quickstart-signup.md)]

Se även [Priser för Cognitive Services – API för Bing-sökning](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

## <a name="prerequisites"></a>Nödvändiga komponenter
Här följer några saker som du behöver innan du kör den här snabbstarten:

* [Visual Studio](https://visualstudio.microsoft.com/downloads/) eller
* [Visual Studio Code 2017](https://code.visualstudio.com/download)
  * [C# för Visual Studio Code](https://visualstudio.microsoft.com/downloads/)
  * [NuGet-pakethanteraren](https://github.com/jmrog/vscode-nuget-package-manager)
* [.NET Core SDK](https://www.microsoft.com/net/download)

## <a name="create-a-project-and-install-dependencies"></a>Skapa ett projekt och installera beroenden

> [!TIP]
> Hämta den senaste koden som en Visual Studio-lösning från [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/).

Det första steget är att skapa ett nytt konsollprojekt. Om du behöver hjälp med att konfigurera ett konsollprojekt kan du läsa [Hello World – ditt första Program (C# Programmeringsguide)](https://docs.microsoft.com/dotnet/csharp/programming-guide/inside-a-program/hello-world-your-first-program). För att kunna använda API för webbsökning i Bing i ditt program behöver du installera `Microsoft.Azure.CognitiveServices.Search.WebSearch` med hjälp av NuGet-pakethanteraren.

[SDK-paketet för webbsökning](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.WebSearch/1.2.0) installeras också:

* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json

## <a name="declare-dependencies"></a>Deklarera beroenden

Öppna projektet i Visual Studio eller i Visual Studio Code och importera dessa beroenden:

```csharp
using System;
using System.Collections.Generic;
using Microsoft.Azure.CognitiveServices.Search.WebSearch;
using Microsoft.Azure.CognitiveServices.Search.WebSearch.Models;
using System.Linq;
```

## <a name="create-project-scaffolding"></a>Skapa projektställningar

När du skapade det nya konsolprojektet bör en namnrymd och en klass för programmet ha skapats. Ditt program bör se ut som i följande exempel:

```csharp
namespace WebSearchSDK
{
    class YOUR_PROGRAM
    {

        // The code in the following sections goes here.

    }
}
```

I följande avsnitt bygger vi exempelprogrammet i den här klassen.

## <a name="construct-a-request"></a>Skapa en begäran

Den här koden konstruerar sökfrågan.

```csharp
public static async void WebResults(WebSearchClient client)
{
    try
    {
        var webData = await client.Web.SearchAsync(query: "Yosemite National Park");
        Console.WriteLine("Searching for \"Yosemite National Park\"");

        // Code for handling responses is provided in the next section...

    }
    catch (Exception ex)
    {
        Console.WriteLine("Encountered exception. " + ex.Message);
    }
}
```

## <a name="handle-the-response"></a>Hantera svaret

Nu lägger vi till kod för att parsa svaret och skriva ut resultatet. `Name` och `Url` för den första webbplatsen, bilden, nyhetsartikeln och videon skrivs ut om de förekommer i svarsobjektet.

```csharp
if (webData?.WebPages?.Value?.Count > 0)
{
    // find the first web page
    var firstWebPagesResult = webData.WebPages.Value.FirstOrDefault();

    if (firstWebPagesResult != null)
    {
        Console.WriteLine("Webpage Results # {0}", webData.WebPages.Value.Count);
        Console.WriteLine("First web page name: {0} ", firstWebPagesResult.Name);
        Console.WriteLine("First web page URL: {0} ", firstWebPagesResult.Url);
    }
    else
    {
        Console.WriteLine("Didn't find any web pages...");
    }
}
else
{
    Console.WriteLine("Didn't find any web pages...");
}

/*
 * Images
 * If the search response contains images, the first result's name
 * and url are printed.
 */
if (webData?.Images?.Value?.Count > 0)
{
    // find the first image result
    var firstImageResult = webData.Images.Value.FirstOrDefault();

    if (firstImageResult != null)
    {
        Console.WriteLine("Image Results # {0}", webData.Images.Value.Count);
        Console.WriteLine("First Image result name: {0} ", firstImageResult.Name);
        Console.WriteLine("First Image result URL: {0} ", firstImageResult.ContentUrl);
    }
    else
    {
        Console.WriteLine("Didn't find any images...");
    }
}
else
{
    Console.WriteLine("Didn't find any images...");
}

/*
 * News
 * If the search response contains news articles, the first result's name
 * and url are printed.
 */
if (webData?.News?.Value?.Count > 0)
{
    // find the first news result
    var firstNewsResult = webData.News.Value.FirstOrDefault();

    if (firstNewsResult != null)
    {
        Console.WriteLine("\r\nNews Results # {0}", webData.News.Value.Count);
        Console.WriteLine("First news result name: {0} ", firstNewsResult.Name);
        Console.WriteLine("First news result URL: {0} ", firstNewsResult.Url);
    }
    else
    {
        Console.WriteLine("Didn't find any news articles...");
    }
}
else
{
    Console.WriteLine("Didn't find any news articles...");
}

/*
 * Videos
 * If the search response contains videos, the first result's name
 * and url are printed.
 */
if (webData?.Videos?.Value?.Count > 0)
{
    // find the first video result
    var firstVideoResult = webData.Videos.Value.FirstOrDefault();

    if (firstVideoResult != null)
    {
        Console.WriteLine("\r\nVideo Results # {0}", webData.Videos.Value.Count);
        Console.WriteLine("First Video result name: {0} ", firstVideoResult.Name);
        Console.WriteLine("First Video result URL: {0} ", firstVideoResult.ContentUrl);
    }
    else
    {
        Console.WriteLine("Didn't find any videos...");
    }
}
else
{
    Console.WriteLine("Didn't find any videos...");
}
```

## <a name="declare-the-main-method"></a>Deklarera main-metoden

I det här programmet innehåller main-metoden kod som instansierar klienten, verifierar `subscriptionKey` och anropar `WebResults`. Se till att du anger en giltig prenumerationsnyckel för ditt Azure-konto innan du fortsätter.

```csharp
static void Main(string[] args)
{
    var client = new WebSearchClient(new ApiKeyServiceClientCredentials("YOUR_SUBSCRIPTION_KEY"));

    WebResults(client);

    Console.WriteLine("Press any key to exit...");
    Console.ReadKey();
}
```

## <a name="run-the-application"></a>Köra programmet

Nu kör vi programmet!

```console
dotnet run
```

## <a name="define-functions-and-filter-results"></a>Definiera funktioner och filtrera resultat

Nu när du har gjort ditt första anrop till API för webbsökning i Bing tittar vi på några funktioner som demonstrerar SDK-funktionaliteten för att förfina frågor och filtrera resultaten. Varje funktion kan läggas till i ditt C#-program som skapades i föregående avsnitt.

### <a name="limit-the-number-of-results-returned-by-bing"></a>Begränsa antalet resultat som returneras av Bing

I det här exemplet används parametrarna `count` och `offset` för att begränsa antalet resultat som returneras för "Best restaurants in Seattle" (bästa restaurangerna i Seattle). `Name` och `Url` för det första resultatet skrivs ut.

1. Lägg till den här koden i konsolprojektet:

    ```csharp
    public static async void WebResultsWithCountAndOffset(WebSearchClient client)
    {
        try
        {
            var webData = await client.Web.SearchAsync(query: "Best restaurants in Seattle", offset: 10, count: 20).Result;
            Console.WriteLine("\r\nSearching for \" Best restaurants in Seattle \"");

            if (webData?.WebPages?.Value?.Count > 0)
            {
                var firstWebPagesResult = webData.WebPages.Value.FirstOrDefault();

                if (firstWebPagesResult != null)
                {
                    Console.WriteLine("Web Results #{0}", webData.WebPages.Value.Count);
                    Console.WriteLine("First web page name: {0} ", firstWebPagesResult.Name);
                    Console.WriteLine("First web page URL: {0} ", firstWebPagesResult.Url);
                }
                else
                {
                    Console.WriteLine("Couldn't find first web result!");
                }
            }
            else
            {
                Console.WriteLine("Didn't see any Web data..");
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine("Encountered exception. " + ex.Message);
        }
    }
    ```

2. Lägg till `WebResultsWithCountAndOffset` i `main`:

    ```csharp
    static void Main(string[] args)
    {
        var client = new WebSearchClient(new ApiKeyServiceClientCredentials("YOUR_SUBSCRIPTION_KEY"));

        WebResults(client);
        // Search with count and offset...
        WebResultsWithCountAndOffset(client);  

        Console.WriteLine("Press any key to exit...");
        Console.ReadKey();
    }
    ```

3. Kör appen.

### <a name="filter-for-news"></a>Filtrera efter nyheter

Det här exemplet används parametern `response_filter` för att filtrera sökresultaten. De sökresultat som returneras är begränsade till nyhetsartiklar för "Microsoft". `Name` och `Url` för det första resultatet skrivs ut.

1. Lägg till den här koden i konsolprojektet:

    ```csharp
    public static async void WebSearchWithResponseFilter(WebSearchClient client)
    {
        try
        {
            IList<string> responseFilterstrings = new List<string>() { "news" };
            var webData = await client.Web.SearchAsync(query: "Microsoft", responseFilter: responseFilterstrings).Result;
            Console.WriteLine("\r\nSearching for \" Microsoft \" with response filter \"news\"");

            if (webData?.News?.Value?.Count > 0)
            {
                var firstNewsResult = webData.News.Value.FirstOrDefault();

                if (firstNewsResult != null)
                {
                    Console.WriteLine("News Results #{0}", webData.News.Value.Count);
                    Console.WriteLine("First news result name: {0} ", firstNewsResult.Name);
                    Console.WriteLine("First news result URL: {0} ", firstNewsResult.Url);
                }
                else
                {
                    Console.WriteLine("Couldn't find first News results!");
                }
            }
            else
            {
                Console.WriteLine("Didn't see any News data..");
            }

        }
        catch (Exception ex)
        {
            Console.WriteLine("Encountered exception. " + ex.Message);
        }
    }
    ```

2. Lägg till `WebResultsWithCountAndOffset` i `main`:

    ```csharp
    static void Main(string[] args)
    {
        var client = new WebSearchClient(new ApiKeyServiceClientCredentials("YOUR_SUBSCRIPTION_KEY"));

        WebResults(client);
        // Search with count and offset...
        WebResultsWithCountAndOffset(client);  
        // Search with news filter...
        WebSearchWithResponseFilter(client);

        Console.WriteLine("Press any key to exit...");
        Console.ReadKey();
    }
    ```

3. Kör appen.

### <a name="use-safe-search-answer-count-and-the-promote-filter"></a>Använd säker sökning, svarsantal och befordringsfiltret

I det här exemplet används parametrarna `answer_count`, `promote` och `safe_search` för att filtrera sökresultat för "Music Videos" (musikvideor). `Name` och `ContentUrl` för det första resultatet visas.

1. Lägg till den här koden i konsolprojektet:

    ```csharp
    public static async void WebSearchWithAnswerCountPromoteAndSafeSearch(WebSearchClient client)
    {
        try
        {
            IList<string> promoteAnswertypeStrings = new List<string>() { "videos" };
            var webData = await client.Web.SearchAsync(query: "Music Videos", answerCount: 2, promote: promoteAnswertypeStrings, safeSearch: SafeSearch.Strict).Result;
            Console.WriteLine("\r\nSearching for \"Music Videos\"");

            if (webData?.Videos?.Value?.Count > 0)
            {
                var firstVideosResult = webData.Videos.Value.FirstOrDefault();

                if (firstVideosResult != null)
                {
                    Console.WriteLine("Video Results # {0}", webData.Videos.Value.Count);
                    Console.WriteLine("First Video result name: {0} ", firstVideosResult.Name);
                    Console.WriteLine("First Video result URL: {0} ", firstVideosResult.ContentUrl);
                }
                else
                {
                    Console.WriteLine("Couldn't find videos results!");
                }
            }
            else
            {
                Console.WriteLine("Didn't see any data..");
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine("Encountered exception. " + ex.Message);
        }
    }
    ```

2. Lägg till `WebResultsWithCountAndOffset` i `main`:

    ```csharp
    static void Main(string[] args)
    {
        var client = new WebSearchClient(new ApiKeyServiceClientCredentials("YOUR_SUBSCRIPTION_KEY"));

        WebResults(client);
        // Search with count and offset...
        WebResultsWithCountAndOffset(client);  
        // Search with news filter...
        WebSearchWithResponseFilter(client);
        // Search with answer count, promote, and safe search
        WebSearchWithAnswerCountPromoteAndSafeSearch(client);

        Console.WriteLine("Press any key to exit...");
        Console.ReadKey();
    }
    ```

3. Kör appen.

## <a name="clean-up-resources"></a>Rensa resurser

När du är klar med det här projektet bör du ta bort din prenumerationsnyckel från programmets kod.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Exempel med Cognitive Services SDK för Node.js](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/)
