---
title: Så här filtrerar du sökresultat - API för webbsökning i Bing
titleSuffix: Azure Cognitive Services
description: Lär dig mer om att filtrera och visa sökresultat från Bing Web Search API.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: 8B837DC2-70F1-41C7-9496-11EDFD1A888D
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 07/08/2019
ms.author: scottwhi
ms.openlocfilehash: a89d73b63680415aa8e516926b8e1d6c59ffbbad
ms.sourcegitcommit: c0419208061b2b5579f6e16f78d9d45513bb7bbc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/08/2019
ms.locfileid: "67626009"
---
# <a name="filtering-the-answers-that-the-search-response-includes"></a>Filtrera de svar som Sök-svaret innehåller  

När du frågar webben returnerar Bing alla relevant innehåll som hittas för sökningen. Om sökfrågan ”färdas + dinghies”, kan svaret innehålla följande svar:

```json
{
    "_type" : "SearchResponse",
    "webPages" : {
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3A43C...",
        "totalEstimatedMatches" : 262000,
        "value" : [...]
    },
    "images" : {
        "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#Images",
        "readLink" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=sail...",
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3A43CA5CA6464E5D...",
        "isFamilyFriendly" : true,
        "value" : [...]
    },
    "rankingResponse" : {
        "mainline" : {
            "items" : [...]
        }
    }
}    
```

## <a name="query-parameters"></a>Frågeparametrar

För att filtrera de svar som returneras av Bing använder den nedan frågeparametrar när du anropar API: et.  

### <a name="responsefilter"></a>ResponseFilter

Du kan filtrera vilka typer av svar med Bing i svaret (till exempel bilder, videor och nyheter) med hjälp av den [responseFilter](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#responsefilter) frågeparameter som är en kommaavgränsad lista med svar. Ett svar inkluderas i svaret om Bing hittar relevant innehåll för den. 

Om du vill exkludera specifika svar från svaret som bilder, Lägg till åtkomstgruppen en `-` tecknet till svarstypen. Exempel:

```
&responseFilter=-images,-videos
```

Följande visar hur du använder `responseFilter` att begäran bilder, videor och nyheter för avseglingen dinghies. När du kodar frågesträngen ändra kommatecken till %2 C.  

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&responseFilter=images%2Cvideos%2Cnews&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-Search-ClientIP: 999.999.999.999  
X-Search-Location:  47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

Nedan visas svaret på den tidigare frågan. Eftersom Bing inte gick att hitta relevant video och Nyhetsresultat, innehålla inte svaret dem.

```json
{
    "_type" : "SearchResponse",
    "images" : {
        "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#Images",
        "readLink" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=sail...",
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3AD78B183C56456C...",
        "isFamilyFriendly" : true,
        "value" : [...]
    },
    "rankingResponse" : {
        "mainline" : {
            "items" : [{
                "answerType" : "Images",
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#Images"
                }
            }]
        }
    }
}
```

Även om Bing inte returnerade video och nyheter resultaten i föregående svar, innebär det inte att video och nyheter innehåll inte finns. Det innebär helt enkelt att sidan inte inkluderar dem. Men om du [sidan](./paging-webpages.md) via fler resultat de efterföljande sidorna skulle förmodligen inkludera. Även om du anropar den [Bing](../bing-video-search/search-the-web.md) och [nyheter Search API](../bing-news-search/search-the-web.md) slutpunkter direkt, svaret skulle sannolikt innehålla resultat.

Du är avråder från att använda `responseFilter` att få resultat från en enda API. Om du vill innehåll från en enda Bing-API, anropa det API: et direkt. Till exempel för att få endast bilder, skicka en begäran till avbildningen Search API-slutpunkter, `https://api.cognitive.microsoft.com/bing/v7.0/images/search` eller något av de andra [avbildningar](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#endpoints) slutpunkter. Anrop av ett enda API är viktiga inte bara av prestandaskäl men eftersom innehållsspecifika-API: erna ger bättre resultat. Du kan till exempel använda filter som inte är tillgängliga i API för webbsökning att filtrera resultaten.  

### <a name="site"></a>Plats

För att få resultat från från en specifik domän kan inkludera den `site:` frågeparameter i frågesträngen.  

```
https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies+site:contososailing.com&mkt=en-us
```

> [!NOTE]
> Beroende på frågan, om du använder den `site:` fråga-operator, det finns risk att svaret kan innehålla vuxet innehåll oavsett den [safeSearch](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#safesearch) inställningen. Du bör endast använda `site:` om du är medveten om innehållet på webbplatsen och ditt scenario tillåter möjligheten att det förekommer innehåll som är olämpligt för barn.

### <a name="freshness"></a>Uppdateringsbarhet

För att begränsa svar Webbresultat till webbsidor som Bing identifieras under en viss period, ange den [färskhet](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#freshness) frågeparameter till något av följande skiftlägeskänsliga värden:

* `Day` – Returnera webbsidor som Bing identifierats under de senaste 24 timmarna
* `Week` – Returnera webbsidor som Bing identifierats under de senaste 7 dagarna
* `Month` – Returnera webbsidor som identifierats under de senaste 30 dagarna

Du kan också ange den här parametern till ett eget datumintervall i formuläret `YYYY-MM-DD..YYYY-MM-DD`. 

`https://<host>/bing/v7.0/search?q=ipad+updates&freshness=2019-02-01..2019-05-30`

För att begränsa resultaten till ett enda datum, ange parametern färskhet till ett visst datum:

`https://<host>/bing/v7.0/search?q=ipad+updates&freshness=2019-02-04`

Resultaten kan omfatta webbsidor som faller utanför den angivna perioden om antalet webbsidor som Bing matchar till dina filterkriterier är mindre än antalet webbsidor som du har begärt (eller Standardnumret Bing returnerar).

## <a name="limiting-the-number-of-answers-in-the-response"></a>Begränsning av antalet svar i svaret

Bing kan returnera flera svar typer i JSON-svar. Exempel: Om du frågar *färdas + dinghies*, Bing återge `webpages`, `images`, `videos`, och `relatedSearches`.

```json
{
    "_type" : "SearchResponse",
    "queryContext" : {
        "originalQuery" : "sailing dinghies"
    },
    "webPages" : {...},
    "images" : {...},
    "relatedSearches" : {...},
    "videos" : {...},
    "rankingResponse" : {...}
}
```

Begränsar antalet svar som Bing returnerar de två översta svar (webbsidor och avbildningar), ange den [answerCount](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#answercount) frågeparameter till 2.

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&answerCount=2&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-Search-ClientIP: 999.999.999.999  
X-Search-Location:  47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

Svaret innehåller endast `webPages` och `images`.

```json
{
    "_type" : "SearchResponse",
    "queryContext" : {
        "originalQuery" : "sailing dinghies"
    },
    "webPages" : {...},
    "images" : {...},
    "rankingResponse" : {...}
}
```

Om du lägger till den `responseFilter` frågeparameter till den föregående frågan och Ställ in den till webbsidor och nyheter och svaret innehåller endast webbsidor eftersom nyheter inte rangordnas.

```json
{
    "_type" : "SearchResponse",
    "queryContext" : {
        "originalQuery" : "sailing dinghies"
    },
    "webPages" : {...},
    "rankingResponse" : {...}
}
```

## <a name="promoting-answers-that-are-not-ranked"></a>Uppgradera svar som inte rangordnas

Om upp rangordnas svar som Bing returnerar för en fråga är webbsidor, bilder, videor och relatedSearches, skulle svaret innehålla svaren. Om du ställer in [answerCount](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#answercount) till två (2), Bing returnerar de övre två rankad svar: webbsidor och bilder. Om du vill Bing för att inkludera bilder och videor i svaret, ange den [marknadsföra](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#promote) frågeparameter och ge den värdet bilder och videor.

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&answerCount=2&promote=images%2Cvideos&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-Search-ClientIP: 999.999.999.999  
X-Search-Location:  47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

Följande är svaret på begäran. Bing returnerar den övre två svar, webbsidor och avbildningar och främjar videor i svaret.

```json
{
    "_type" : "SearchResponse",
    "queryContext" : {
        "originalQuery" : "sailiing dinghies"
    },
    "webPages" : {...},
    "images" : {...},
    "videos" : {...},
    "rankingResponse" : {...}
}
```

Om du ställer in `promote` till nyheter, svaret inte innehåller nyheter svaret eftersom det inte är en rankad svar&mdash;du ge endast rangordnas svar.

Svaren som du vill flytta upp räknas inte mot den `answerCount` gränsen. Exempel: om rankad svaren är nyheter, bilder och videor och anger `answerCount` till 1 och `promote` till nyheter, svaret innehåller nyheter och bilder. Eller, om de översta svar är videor, bilder och nyheter, svaret innehåller videor och nyheter.

Du kan använda `promote` endast om du anger den `answerCount` frågeparameter.
