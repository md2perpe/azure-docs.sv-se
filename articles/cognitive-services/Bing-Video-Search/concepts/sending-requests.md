---
title: Skicka sökbegäranden till API:et för videosökning i Bing
titlesuffix: Azure Cognitive Services
description: Lär dig mer om hur du skickar sökfrågor till API:et för videosökning i Bing.
services: cognitive-services
author: aahi
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: quickstart
ms.date: 06/27/2019
ms.author: aahill
ms.openlocfilehash: 93c2a5f9cd9fb3141e79559429ae69c0c42a96c1
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/03/2019
ms.locfileid: "67542681"
---
# <a name="sending-search-requests-to-the-bing-video-search-api"></a>Skicka sökbegäranden till API:et för videosökning i Bing

I den här artikeln beskrivs parametrarna och attributen för begäranden som skickas till API:et för videosökning i Bing samt det JSON-svarsobjekt som det returnerar. 

[!INCLUDE [cognitive-services-bing-video-search-signup-requirements](../../../../includes/cognitive-services-bing-video-search-signup-requirements.md)]

## <a name="suggest-search-terms-with-the-bing-autosuggest-api"></a>Föreslå söktermer med API:et för automatiska förslag i Bing

Om du tillhandahåller en sökruta där användaren anger sin sökterm bör du använda [API för automatiska förslag i Bing ](../../bing-autosuggest/get-suggested-search-terms.md) för att ge bättre funktioner. API:t returnerar föreslagna frågesträngar baserat på partiella söktermer som användaren skriver in.

När användaren har angett sin sökterm URL-kodar du den innan du anger frågeparametern [q](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#query). Om användaren till exempel anger *segeljollar* ställer du in `q` till `sailing+dinghies` eller `sailing%20dinghies`.

## <a name="sending-a-request"></a>Skicka en begäran

För att få resultat från en videosökning skickar du en GET-begäran till följande slutpunkt:  
  
```
https://api.cognitive.microsoft.com/bing/v7.0/videos/search
```
   
Begäran måste använda HTTPS-protokollet.

Vi rekommenderar att alla förfrågningar kommer från en server. Om nyckeln distribueras som en del av ett klientprogram ökar risken för att en illvillig tredje part kan komma åt den. Anrop från en server innebär dessutom att det bara finns en enda uppgraderingspunkt för framtida versioner av API:et.

  
Begäran måste innehålla frågeparametern [q](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#query), som innehåller användarens sökterm. Även om det är valfritt bör begäran även innehålla frågeparametern [mkt](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#mkt), som identifierar marknaden som du vill att resultatet ska komma från. En lista över valfria frågeparametrar som `pricing` finns i [Frågeparametrar](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#query-parameters). Alla frågeparametervärden måste vara URL-kodade.  
  
Begäran måste innehålla huvudet [Ocp-Apim-Subscription-Key](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#subscriptionkey). Även om det är valfritt rekommenderar vi även att följande huvuden finns med:  
  
-   [User-Agent](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#useragent)  
-   [X-MSEdge-ClientID](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#clientid)  
-   [X-Search-ClientIP](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#clientip)  
-   [X-Search-Location](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#location)  

Sidhuvuden för klientens IP-adress och platsen är viktiga för att returnera platsmedvetet innehåll.  

En lista över alla sidhuvuden för begäranden och svar finns i [Sidhuvuden](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#headers).

## <a name="example-search-request"></a>Exempel på sökbegäran

Nedan visas en sökbegäran som innehåller alla föreslagna frågeparametrar och sidhuvuden. Om det är den första gången du anropar ett Bing-API inkluderar du inte klientens ID-huvud. Inkludera endast klient-ID om du har anropat ett Bing-API förut och om Bing returnerade ett klient-ID för användar- och enhetskombinationen. 
  
```  
GET https://api.cognitive.microsoft.com/bing/v7.0/videos/search?q=sailing+dinghies&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-Search-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

## <a name="example-json-response"></a>Exempel på JSON-svar

Nedan visas svaret på den tidigare begäran. Exemplet visar också Bing-specifika svarshuvuden.

[!INCLUDE [cognitive-services-bing-url-note](../../../../includes/cognitive-services-bing-url-note.md)]

```json
BingAPIs-TraceId: 76DD2C2549B94F9FB55B4BD6FEB6AC
X-MSEdge-ClientID: 1C3352B306E669780D58D607B96869
BingAPIs-Market: en-US

{
    "_type" : "Videos",
    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=81EF7545D5694...",
    "totalEstimatedMatches" : 1000,
    "value" : [
        {
            "name" : "How to sail - What to Wear for Dinghy Sailing",
            "description" : "An informative video on what to wear when...",
            "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=81EF7545D56...",
            "thumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?id=OVP.DYWCvh...",
            "datePublished" : "2014-03-04T11:51:53",
            "publisher" : [
                {
                    "name" : "Fabrikam"
                }
            ],
            "creator" : {
                "name" : "Marcus Appel"
            },
            "contentUrl" : "https:\/\/www.fabrikam.com\/watch?v=vzmPjHZ--g",
            "hostPageUrl" : "https:\/\/www.bing.com\/cr?IG=81EF7545D56944...",
            "encodingFormat" : "h264",
            "hostPageDisplayUrl" : "https:\/\/www.fabrikam.com\/watch?v=vzmPjBZ--g",
            "width" : 1280,
            "height" : 720,
            "duration" : "PT2M47S",
            "motionThumbnailUrl" : "https:\/\/tse3.mm.bing.net\/th?id=OM.Y6...",
            "embedHtml" : "<iframe width=\"1280\" height=\"720\" src=\"https:...><\/iframe>",
            "allowHttpsEmbed" : true,
            "viewCount" : 8743,
            "thumbnail" : {
                "width" : 300,
                "height" : 168
            },
            "videoId" : "6DB795E11A6E3CBAAD636DB795E11E3CBAAD63",
            "allowMobileEmbed" : true,
            "isSuperfresh" : false
        },
        . . .
    ],
    "nextOffset" : 0,
    "pivotSuggestions" : [
        {
            "pivot" : "sailing",
            "suggestions" : []
        },
        {
            "pivot" : "dinghies",
            "suggestions" : [
                {
                    "text" : "Sailing Cruising",
                    "displayText" : "Cruising",
                    "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=81EF754...",
                    "searchLink" : "https:\/\/api.cognitive.microsoft.com...",
                    "thumbnail" : {
                        "thumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?q=Sailing..."
                    }
                },
                . . .
            ]
        }
    ]
}
```

## <a name="next-steps"></a>Nästa steg

Testa API:et. Gå till [testningskonsolen för videosöknings-API:et](https://dev.cognitive.microsoft.com/docs/services/56b43f3ccf5ff8098cef3809/operations/58113fe5e31dac0a1ce6b0a8). 

Mer information om hur du använder svarsobjekten finns i [Searching the Web for Videos](../search-the-web.md) (Söka efter videor på webben).

Mer information om hur du får insikter om en video, till exempel relaterade sökningar, finns i [Video Insights](../video-insights.md) (Videoinsikter).  
  
Mer information om videor som är populära i sociala medier finns i [Populära videor](../trending-videos.md).  
