---
title: 'Snabbstart: Sök efter bilder – API för bildsökning i Bing och Java'
titleSuffix: Azure Cognitive Services
description: Använd den här snabbstarten när du vill skicka bildsökningsbegäranden till REST-API:et för bildsökning i Bing med hjälp av Java och få JSON-svar.
services: cognitive-services
documentationcenter: ''
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: quickstart
ms.date: 02/06/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: 620e2ad66547939c24cbcf369705e4ab3ce59753
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66383672"
---
# <a name="quickstart-search-for-images-using-the-bing-image-search-rest-api-and-java"></a>Snabbstart: Sök efter bilder med API:et för bildsökning i Bing och Java

Använd den här snabbstarten till att börja skicka sökbegäranden till API:et för bildsökning i Bing. Java-programmet skickar en sökfråga till API:et och visar webbadressen till den första bilden i resultatet. Även om det här programmet är skrivet i Java, är API:et en RESTful-webbtjänst som är kompatibel med de flesta programmeringsspråk.

Källkoden för det här exemplet finns på [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/Search/BingImageSearchv7Quickstart.java) tillsammans med ytterligare felhantering och kommentarer.

## <a name="prerequisites"></a>Nödvändiga komponenter

* [Java Development Kit (JDK)](https://aka.ms/azure-jdks)

* [Gson-biblioteket](https://github.com/google/gson)

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>Skapa och initiera ett projekt

1. Skapa ett nytt Java-projekt i valfri IDE eller redigeringsprogram och importera följande bibliotek.

    ```java
    import java.net.*;
    import java.util.*;
    import java.io.*;
    import javax.net.ssl.HttpsURLConnection;
    import com.google.gson.Gson;
    import com.google.gson.GsonBuilder;
    import com.google.gson.JsonObject;
    import com.google.gson.JsonParser;
    ```

2. Skapa variabler för API-slutpunkter, din prenumerationsnyckel och sökvillkor.

    ```java
    static String subscriptionKey = "enter key here";
    static String host = "https://api.cognitive.microsoft.com";
    static String path = "/bing/v7.0/images/search";
    static String searchTerm = "tropical ocean";
    ```

## <a name="construct-the-search-request-and-query"></a>Konstruera sökbegäran och fråga

1. Använd variablerna från det sista steget för att formatera en sök-URL för API-begäran. Sökordet måste vara URL-kodat innan det läggs till i begäran.

    ```java
    // construct the search request URL (in the form of endpoint + query string)
    URL url = new URL(host + path + "?q=" +  URLEncoder.encode(searchQuery, "UTF-8"));
    HttpsURLConnection connection = (HttpsURLConnection)url.openConnection();
    connection.setRequestProperty("Ocp-Apim-Subscription-Key", subscriptionKey);
    ```

## <a name="receive-and-process-the-json-response"></a>Ta emot och bearbeta JSON-svaret

1. Ta emot JSON-svar från API för bildsökning i Bing och konstruera resultatobjektet.

    ```java
    // receive JSON body
    InputStream stream = connection.getInputStream();
    String response = new Scanner(stream).useDelimiter("\\A").next();
    // construct result object for return
    SearchResults results = new SearchResults(new HashMap<String, String>(), response);
    ```
2. Separera de Bing-relaterade HTTP-huvudena från JSON-texten
    ```java
    // extract Bing-related HTTP headers
    Map<String, List<String>> headers = connection.getHeaderFields();
    for (String header : headers.keySet()) {
        if (header == null) continue;      // may have null key
        if (header.startsWith("BingAPIs-") || header.startsWith("X-MSEdge-")) {
            results.relevantHeaders.put(header, headers.get(header).get(0));
        }
    }
    ```

3. Stäng dataströmmen och parsa svaret. Hämta det totala antalet returnerade sökresultat och miniatyr-URL:en till det första avbildningsresultatet.

    ```java
    stream.close();
    JsonParser parser = new JsonParser();
    JsonObject json = parser.parse(result.jsonResponse).getAsJsonObject();
    //get the first image result from the JSON object, along with the total
    //number of images returned by the Bing Image Search API.
    String total = json.get("totalEstimatedMatches").getAsString();
    JsonArray results = json.getAsJsonArray("value");
    JsonObject first_result = (JsonObject)results.get(0);
    String resultURL = first_result.get("thumbnailUrl").getAsString();
    ```

## <a name="example-json-response"></a>Exempel på JSON-svar

Svar från API för bildsökning i Bing returneras som JSON. Det här exempelsvaret har trunkerats för att visa ett enskilt resultat.

```json
{
"_type":"Images",
"instrumentation":{
    "_type":"ResponseInstrumentation"
},
"readLink":"images\/search?q=tropical ocean",
"webSearchUrl":"https:\/\/www.bing.com\/images\/search?q=tropical ocean&FORM=OIIARP",
"totalEstimatedMatches":842,
"nextOffset":47,
"value":[
    {
        "webSearchUrl":"https:\/\/www.bing.com\/images\/search?view=detailv2&FORM=OIIRPO&q=tropical+ocean&id=8607ACDACB243BDEA7E1EF78127DA931E680E3A5&simid=608027248313960152",
        "name":"My Life in the Ocean | The greatest WordPress.com site in ...",
        "thumbnailUrl":"https:\/\/tse3.mm.bing.net\/th?id=OIP.fmwSKKmKpmZtJiBDps1kLAHaEo&pid=Api",
        "datePublished":"2017-11-03T08:51:00.0000000Z",
        "contentUrl":"https:\/\/mylifeintheocean.files.wordpress.com\/2012\/11\/tropical-ocean-wallpaper-1920x12003.jpg",
        "hostPageUrl":"https:\/\/mylifeintheocean.wordpress.com\/",
        "contentSize":"897388 B",
        "encodingFormat":"jpeg",
        "hostPageDisplayUrl":"https:\/\/mylifeintheocean.wordpress.com",
        "width":1920,
        "height":1200,
        "thumbnail":{
        "width":474,
        "height":296
        },
        "imageInsightsToken":"ccid_fmwSKKmK*mid_8607ACDACB243BDEA7E1EF78127DA931E680E3A5*simid_608027248313960152*thid_OIP.fmwSKKmKpmZtJiBDps1kLAHaEo",
        "insightsMetadata":{
        "recipeSourcesCount":0,
        "bestRepresentativeQuery":{
            "text":"Tropical Beaches Desktop Wallpaper",
            "displayText":"Tropical Beaches Desktop Wallpaper",
            "webSearchUrl":"https:\/\/www.bing.com\/images\/search?q=Tropical+Beaches+Desktop+Wallpaper&id=8607ACDACB243BDEA7E1EF78127DA931E680E3A5&FORM=IDBQDM"
        },
        "pagesIncludingCount":115,
        "availableSizesCount":44
        },
        "imageId":"8607ACDACB243BDEA7E1EF78127DA931E680E3A5",
        "accentColor":"0050B2"
    }]
}
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Självstudie om enkel app för bildsökning i Bing](../tutorial-bing-image-search-single-page-app.md)

## <a name="see-also"></a>Se också

* [Vad är bildsökning i Bing?](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/overview)  
* [Prova en interaktiv demo online](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/) 
* [Prissättning](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/) av API:er för Bing-sökning. 
* [Hämta en kostnadsfri åtkomstnyckel för Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=bing-image-search-api)  
* [Dokumentation om Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services)
* [API-referens för bildsökning i Bing](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)
