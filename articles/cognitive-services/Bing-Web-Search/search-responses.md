---
title: Webbsökning i Bing struktur och svaret svarstyper
titleSuffix: Azure Cognitive Services
description: Läs mer om svar typer och används av Bing Web Search API-svar.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: 5380713af17a751ab695fee90895b4f6b0d2369b
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/03/2019
ms.locfileid: "67542816"
---
# <a name="bing-web-search-api-response-structure-and-answer-types"></a>Webbsökning i Bing struktur och svaret svarstyper  

När du skickar webbsökning i Bing en sökbegäran, returneras en [ `SearchResponse` ](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#searchresponse) objekt i svarstexten. Objektet innehåller ett fält för varje svar som Bing var relevanta för frågan. Detta exempel visar ett svarsobjekt om Bing returneras alla svar på:

```json
{
    "_type": "SearchResponse",
    "queryContext": {...},
    "webPages": {...},
    "images": {...},
    "relatedSearches": {...},
    "videos": {...},
    "news": {...},
    "spellSuggestion": {...},
    "computation": {...},
    "timeZone": {...},
    "rankingResponse": {...}
}, ...
```

Webbsökning i Bing returnerar vanligtvis en delmängd av svaren. Exempel: om frågetermen har *avseglingen dinghies*, svaret kan innehålla `webPages`, `images`, och `rankingResponse`. Om du inte har använt [responseFilter](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#responsefilter) för att filtrera ut webbsidor svaret alltid innehåller den `webpages` och `rankingResponse` svar.

[!INCLUDE [cognitive-services-bing-url-note](../../../includes/cognitive-services-bing-url-note.md)]

## <a name="webpages-answer"></a>Webbsidor svar

Den [webbsidor](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#webanswer) svaret innehåller en lista med länkar till webbsidor som webbsökning i Bing fastställt var relevanta för frågan. Varje [webbsida](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#webpage) i listan innehåller: sidans namn, url, visa URL, en kort beskrivning av innehållet och det datum som Bing att hitta innehållet.

```json
{
    "id": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.0",
    "name": "Dinghy sailing",
    "url": "https:\/\/www.bing.com\/cr?IG=3A43CA5...",
    "displayUrl": "https:\/\/en.contoso.com\/wiki\/Dinghy_sailing",
    "snippet": "Dinghy sailing is the activity of sailing small boats...",
    "dateLastCrawled": "2017-04-05T16:25:00"
}, ...
```

Använd `name` och `url` att skapa en hyperlänk som tar användaren till webbsidan.

<!-- Remove until this can be replaced with a sanitized version.
The following shows an example of how you might display the webpage in a search results page.

![Rendered webpage example](./media/cognitive-services-bing-web-api/bing-rendered-webpage-example.PNG)
-->

## <a name="images-answer"></a>Svara på avbildningar

Den [avbildningar](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images) svaret innehåller en lista med avbildningar som Bing trodde var relevanta för frågan. Varje [bild](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#image) i listan innehåller URL: en för bilden, dess storlek, dess storlek och dess Kodningsformatet. Bildobjektet inkluderar även URL för en miniatyrbild av bilden och miniatyrbildens mått.

```json
{
    "name": "Rich Passage Sailing Dinghy",
    "webSearchUrl": "https:\/\/www.bing.com\/cr?IG=3A43CA5CA64...",
    "thumbnailUrl": "https:\/\/tse1.mm.bing.net\/th?id=OIP....",
    "datePublished": "2011-10-29T11:26:00",
    "contentUrl": "http:\/\/upload.contoso.com\/sailing\/...",
    "hostPageUrl": "http:\/\/www.bing.com\/cr?IG=3A43CA5CA6464....",
    "contentSize": "79239 B",
    "encodingFormat": "jpeg",
    "hostPageDisplayUrl": "http:\/\/en.contoso.com\/wiki\/File...",
    "width": 526,
    "height": 688,
    "thumbnail": {
        "width": 229,
        "height": 300
    },
    "insightsSourcesSummary": {
        "shoppingSourcesCount": 0,
        "recipeSourcesCount": 0
    }
}, ...
```

Beroende på användarens enhet skulle du normalt visar en delmängd av miniatyrbilder, med ett alternativ för användaren att [Bläddra igenom](paging-webpages.md) återstående avbildningar.

<!-- Remove until this can be replaced with a sanitized version.
![List of thumbnail images](./media/cognitive-services-bing-web-api/bing-web-image-thumbnails.PNG)
-->

Du kan även expandera miniatyrbilden när användaren hovrar markören över den. Se till att tillskriva bildkällan om du expanderar bilden. Till exempel genom att extrahera värden från `hostPageDisplayUrl` och visa den nedanför bilden. Information om att ändra storlek på miniatyrbilden finns i [Resizing and Cropping Thumbnails](./resize-and-crop-thumbnails.md) (Ändra storlek på och beskär miniatyrbilder).

<!-- Remove until this can be replaced with a sanitized version.
![Expanded view of thumbnail image](./media/cognitive-services-bing-web-api/bing-web-image-thumbnail-expansion.PNG)
-->

Om användaren klickar på miniatyrbilden använda `webSearchUrl` ska användaren att Bings sökresultatsidan för avbildningar som innehåller ett collage av avbildningarna.

Mer information om avbildningen svar och avbildningar finns i [Image Search API](../bing-image-search/search-the-web.md).

## <a name="related-searches-answer"></a>Svara på relaterade sökningar

Den [relatedSearches](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#searchresponse-relatedsearches) svaret innehåller en lista över de mest populära relaterade frågor som gjorts av andra användare. Varje [fråga](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#query_obj) innehåller en frågesträng i listan (`text`), en frågesträng med markering tecken av träffar (`displayText`), och en URL (`webSearchUrl`) till Bings sökresultatsidan frågan.

```json
{
    "text": "dinghy racing teams",
    "displayText": "dinghy racing teams",
    "webSearchUrl": "https:\/\/www.bing.com\/cr?IG=96C4CF214A0..."
}, ...
```

Använd den `displayText` frågesträng och `webSearchUrl` URL: en för att skapa en hyperlänk som tar användaren till Bing search resultat för relaterade frågan. Du kan också använda den `text` frågesträng i API för webbsökning i frågan och visa resultaten själv.

Information om hur du hanterar fokus markörer i `displayText`, se [når markering](./hit-highlighting.md).

Nedan visas ett exempel på hur Närliggande sökningar i Bing.com.

![Relaterade sökningar exempel på Bing](./media/cognitive-services-bing-web-api/bing-web-rendered-relatedsearches.GIF)

## <a name="videos-answer"></a>Svara på videor

Den [videor](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#videos) svaret innehåller en lista med videor som Bing trodde var relevanta för frågan. Varje [video](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#video) i listan innehåller URL: en för videon, dess varaktighet, dess storlek och dess Kodningsformatet. Videoobjektet innehåller även webbadressen till en miniatyrbild av videon och miniatyrbildens mått.

```json
{
    "name": "Sailing dinghy",
    "description": "Northwind Traders is a 12 foot gunter rigged...",
    "webSearchUrl": "https:\/\/www.bing.com\/cr?IG=1CAE739681D84...",
    "thumbnailUrl": "https:\/\/tse2.mm.bing.net\/th?id=OVP.wsKiL...",
    "datePublished": "2013-11-06T01:56:28",
    "publisher": [{
        "name": "Fabrikam"
    }],
    "contentUrl": "https:\/\/www.fabrikam.com\/watch?v=MrVBWZpJjX",
    "hostPageUrl": "https:\/\/www.bing.com\/cr?IG=1CAE739681D8400DB...",
    "encodingFormat": "mp4",
    "hostPageDisplayUrl": "https:\/\/www.fabrikam.com\/watch?v=MrBWZpJjXo",
    "width": 1280,
    "height": 720,
    "duration": "PT3M47S",
    "motionThumbnailUrl": "https:\/\/tse2.mm.bing.net\/th?id=OM.oa...",
    "embedHtml": "<iframe width=\"1280\" height=\"720\" src=\"http:\/\/www....><\/iframe>",
    "allowHttpsEmbed": true,
    "viewCount": 19089,
    "thumbnail": {
        "width": 300,
        "height": 168
    },
    "allowMobileEmbed": true,
    "isSuperfresh": false
}, ...
```

Beroende på användarens enhet kan visas du vanligtvis en delmängd av videor med ett alternativ för användare att visa de återstående videorna. Du kan visa en miniatyrbild för video med videons längd beskrivning (namn) och information (utgivaren).

<!-- Remove until this can be replaced with a sanitized version.
![List of video thumbnails](./media/cognitive-services-bing-web-api/bing-web-video-thumbnails.PNG)
-->

När användaren för muspekaren över miniatyrbilden kan du använda `motionThumbnailUrl` att spela upp en miniatyrbild av videon. Se till att du tillskriver ursprunget när du visar en video.

<!-- Remove until this can be replaced with a sanitized version.
![Motion thumbnail of a video](./media/cognitive-services-bing-web-api/bing-web-video-motion-thumbnail.PNG)
-->

Om användaren klickar på miniatyrbilden finns följande visningsalternativ för videon:

- Använd `hostPageUrl` att titta på videon på webbplatsen värden (till exempel YouTube)
- Använd `webSearchUrl` att visa videon i webbläsaren för Bing-video
- Använd `embedHtml` att bädda in videon i en egen upplevelse

Mer information om video svar och videor finns [Video API för webbsökning](../bing-video-search/search-the-web.md).

## <a name="news-answer"></a>Nyheter svar

Den [nyheter](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#news) svaret innehåller en lista över artiklar som Bing trodde var relevanta för frågan. Varje [nyhetsartikel](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#newsarticle) i listan innehåller artikels namn, beskrivning och webbadressen till artikeln på värdens webbplats. Om artikeln innehåller en bild innehåller objektet även en miniatyr för bilden.

```json
{
    "name": "WC Sailing Qualifies for America Trophy with...",
    "url": "http:\/\/www.bing.com\/cr?IG=3445EEF15DAF4FFFBF7...",
    "image": {
        "contentUrl": "http:\/\/www.contoso.com\/sports\/sail...",
        "thumbnail": {
            "contentUrl": "https:\/\/www.bing.com\/th?id=ON.1...",
            "width": 400,
            "height": 272
        }
    },
    "description": "The WC sailing team qualified for a...",
    "provider": [{
        "_type": "Organization",
        "name": "contoso.com"
    }],
    "datePublished": "2017-04-16T21:56:00"
}, ...
```

Beroende på användarens enhet kan du välja att visa en delmängd av nyhetsartiklar med ett alternativ för användare att hitta de återstående artiklarna. Använd `name` och `url` till att skapa en hyperlänk som tar användaren till nyhetsartikeln på värdens webbplats. Om artikeln innehåller en bild, ser du en avbildning klickbara med hjälp av `url`. Se till att du tillskriver artikeln med `provider`.

<!-- Remove until this can be replaced with a sanitized version.
The following shows an example of how you might display articles in a search results page.

![List of news articles](./media/cognitive-services-bing-web-api/bing-web-news-list.PNG)
-->

Mer information om nyheter svar och nyhetsartiklar finns i [nyheter Search API](../bing-news-search/search-the-web.md).

## <a name="computation-answer"></a>Beräkningen svar

Om användaren anger ett matematiska uttryck eller en fråga för konvertering av enhet, svaret kan innehålla en [beräkning](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#computation) svar. Den `computation` svaret innehåller normaliserat uttryck och dess resultat.

En enhet konvertering fråga är en fråga som konverterar en enhet till en annan. Till exempel *hur många fot i 10 meter?* eller *hur många matskedar i en 1/4 cup?*

Följande visar den `computation` för *hur många fot i 10 meter?*

```json
"computation": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#Computation",
    "expression": "10 meters",
    "value": "32.808399 feet"
}, ...
```

Följande visar exempel på matematiska frågor och deras motsvarande `computation` svar.

```
Query: (5+3)(10/2)+8
Encoded query: %285%2B3%29%2810%2F2%29%2B8
```

```json
"computation": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#Computation",
    "expression": "((5+3)*(10\/2))+8",
    "value": "48"
}
```

```
Query: sqrt(4^2+8^2)
Encoded query: sqrt%284^2%2B8^2%29
```

```json
"computation": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#Computation",
    "expression": "sqrt((4^2)+(8^2))",
    "value": "8.94427191"
}
```

```
Query: 30 6/8 - 18 8/16
Encoded query: 30%206%2F8%20-%2018%208%2F16
```

```json
"computation": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#WolframAlpha",
    "expression": "30 6\/8-18 8\/16",
    "value": "12.25"
}
```

```
Query: 8^2+11^2-2*8*11*cos(37)
Encoded query: 8^2%2B11^2-2*8*11*cos%2837%29
```

```json
"computation": {
        "id": "https:\/\/www.bing.com\/api\/v7\/#Computation",
        "expression": "(8^2)+(11^2)-(2*8*11*cos(37))",
        "value": "44.4401502"
}
```

Ett matematiska uttryck kan innehålla följande tecken:

|Symbol|Beskrivning|
|------------|-----------------|
|+|Tillägg|
|-|Subtraktion|
|/|Division|
|*|Multiplikation|
|^|Power|
|!|Fakultet|
|.|Decimal|
|()|Prioritet gruppering|
|[]|Funktion|

Ett matematiska uttryck kan innehålla följande konstanter:

|Symbol|Beskrivning|
|------------|-----------------|
|Pi|3.14159...|
|Grad|Grad|
|Jag|Tänkt tal|
|e|e 2.71828...|
|GoldenRatio|Gyllene förhållandet 1.61803...|

Ett matematiska uttryck kan innehålla följande funktioner:

|Symbol|Beskrivning|
|------------|-----------------|
|Sortera|Kvadratrot|
|Sin [x], Cos [x], Tan [x]<br />Csc[x], Sec[x], Cot[x]|Trigonometrifunktioner (med argument i radianer)|
|ArcSin[x], ArcCos[x], ArcTan[x]<br />ArcCsc[x], ArcSec[x], ArcCot[x]|Inverterade trigonometrifunktioner (ger resultat i radianer)|
|EXP [x] E ^ x|Exponential-funktionen|
|Log [x]|Naturlig logaritm|
|SINH [x] Cosh [x] Tanh [x]<br />Csch [x] Sech [x] Coth [x]|Hyperboliska funktioner|
|ArcSinh [x] ArcCosh [x] ArcTanh [x]<br />ArcCsch [x] ArcSech [x] ArcCoth [x]|Inverterade hyperboliska funktioner|

Matematiska uttryck som innehåller variabler (till exempel, 4 x + 6 = 18, där x är variabeln) stöds inte.

## <a name="timezone-answer"></a>Tidszon svar

Om användaren anger en tids- eller fråga, svaret kan innehålla en [tidszon](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#timezone) svar. Det här svaret stöder implicit eller explicit-frågor. En implicit fråga, till exempel *vilken tid är det?* , returnerar den lokala tiden baserat på användarens plats. En explicit-fråga som *vilken tid är det i Seattle?* , returnerar den lokala tiden för Seattle, WA.

Den `timeZone` svaret innehåller namnet på platsen, aktuella UTC-datum och tid på den angivna platsen och UTC-förskjutning. Om gränsen för platsen är inom olika tidszoner, innehåller svaret den aktuella UTC-datum och tid för alla tidszoner inom gränsen. Eftersom Florida tillstånd faller inom två tidszoner, innehåller till exempel svaret lokalt datum och tid för båda tidszoner.  

Om frågan begär ett tillstånd eller land/region, Bing avgör primära staden inom platsens geografisk gräns och returnerar den i den `primaryCityTime` fält. Om gränsen innehåller flera tidszoner, återstående tidszoner returneras i de `otherCityTimes` fält.

Exemplet nedan visar frågor som returnerar de `timeZone` svar.

```
Query: What time is it?

"timeZone": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#TimeZone",
    "primaryCityTime": {
        "location": "Redmond, Washington, United States",
        "time": "2015-10-27T08:38:12.1189231Z",
        "utcOffset": "UTC-7"
    }
}

Query: What time is it in the Pacific time zone?

"timeZone": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#TimeZone",
    "primaryCityTime": {
        "location": "Pacific Time Zone",
        "time": "2015-10-23T12:33:19.0728146Z",
        "utcOffset": "UTC-7"
    }
}

Query: Time in Florida?

"timeZone": {
        "id": "https:\/\/www.bing.com\/api\/v7\/#TimeZone",
        "primaryCityTime": {
            "location": "Tallahassee, Florida, United States",
            "time": "2015-10-23T13:04:56.6774389Z",
            "utcOffset": "UTC-4"
        },
        "otherCityTimes": [{
            "location": "Pensacola",
            "time": "2015-10-23T12:04:56.6664294Z",
            "utcOffset": "UTC-5"
        }]
}

Query: What time is it in the U.S.

"timeZone": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#TimeZone",
    "primaryCityTime": {
        "location": "Washington, D.C., United States",
        "time": "2015-10-23T15:27:59.8892745Z",
        "utcOffset": "UTC-4"
    },
    "otherCityTimes": [{
        "location": "Honolulu",
        "time": "2015-10-23T09:27:59.8892745Z",
        "utcOffset": "UTC-10"
    },
    {
        "location": "Anchorage",
        "time": "2015-10-23T11:27:59.8892745Z",
        "utcOffset": "UTC-8"
    },
    {
        "location": "Phoenix",
        "time": "2015-10-23T12:27:59.8892745Z",
        "utcOffset": "UTC-7"
    },
    {
        "location": "Los Angeles",
        "time": "2015-10-23T12:27:59.8942788Z",
        "utcOffset": "UTC-7"
    },
    {
        "location": "Denver",
        "time": "2015-10-23T13:27:59.8812681Z",
        "utcOffset": "UTC-6"
    },
    {
        "location": "Chicago",
        "time": "2015-10-23T14:27:59.8892745Z",
        "utcOffset": "UTC-5"
    }]
}
```

## <a name="spellsuggestion-answer"></a>SpellSuggestion svar

Om Bing avgör att användaren kan ha avsedd att söka efter något annat, svaret innehåller en [SpellSuggestions](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#spellsuggestions) objekt. Exempel: om en användare söker efter *carlos penna*, Bing kan bestämma att användaren sannolikt avsett att söka efter Carlos Pena i stället (baserat på senaste sökningar av *carlos penna*). Nedan visas ett exempel stavningskontroll svar.

```json
"spellSuggestions": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#SpellSuggestions",
    "value": [{
        "text": "carlos pena",
        "displayText": "carlos pena"
    }]
}, ...
```

## <a name="response-headers"></a>Svarshuvuden

Svar från Bing Web Search API kan innehålla följande huvuden:

|||
|-|-|
|`X-MSEdge-ClientID`|Unikt ID som Bing har tilldelat till användaren|
|`BingAPIs-Market`|På marknaden som användes för att uppfylla begäran|
|`BingAPIs-TraceId`|Loggposten på Bing API-servern för den här förfrågan (för stöd)|

Det är särskilt viktigt att bevara klient-ID och lämna tillbaka med efterföljande förfrågningar. När du gör detta sökningen använder de senaste kontexten i rangordning sökresultat och också ge en konsekvent användarupplevelse.

Men när du anropar API för webbsökning i Bing från JavaScript kanske i webbläsaren inbyggda säkerhetsfunktioner (CORS) hindrar dig från att komma åt värdena för dessa rubriker.

För att få åtkomst till rubrikerna du Bing Web Search API-begäran via en CORS-proxy. Svaret från en sådan proxy har ett `Access-Control-Expose-Headers`-huvud som vitlistar svarshuvuden och gör dem tillgängliga för JavaScript.

Det är enkelt att installera en proxy för CORS så att våra [självstudieappen](tutorial-bing-web-search-single-page-app.md) att komma åt valfria klientcertifikat-huvuden. [Installera Node.js](https://nodejs.org/en/download/) om du inte redan har det. Ange sedan följande kommando i en kommandotolk.

    npm install -g cors-proxy-server

Sedan ändra Bing Web Search API-slutpunkten i HTML-filen:

    http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/search

Slutligen startar du CORS-proxyn med följande kommando:

    cors-proxy-server

Lämna kommandofönstret öppet medan du använder självstudieappen. Om du stänger fönstret stoppas proxyn. I det expanderbara avsnittet om HTTP-huvuden nedan kan du nu se `X-MSEdge-ClientID`-huvudet (bland annat) under sökresultatet och du kan kontrollera att det är samma för varje begäran.

## <a name="response-headers-in-production"></a>Svarshuvuden i produktion

CORS-proxy-metoden som beskrivs i föregående svar är lämplig för utveckling, testning och utbildning.

I en produktionsmiljö bör du ha ett skript på servern på samma domän som den webbsida som använder Bing Web Search API. Det här skriptet ska göra API-anrop på begäran från JavaScript-webbsida och skicka alla resultat, inklusive rubriker, tillbaka till klienten. Eftersom de två resurserna (sidan eller skript) dela ett ursprung, CORS används inte och de särskilda rubrikerna är tillgängliga för JavaScript på webbsidan.

Den här metoden skyddar även din API-nyckel från exponeringen för allmänheten, eftersom endast serverskriptet måste den. Skriptet kan använda en annan metod för att se till att begäran har behörighet.

Nedan visas hur Bing använder stavning förslag.

![Bing stavning förslag exempel](./media/cognitive-services-bing-web-api/bing-web-spellingsuggestion.GIF)  

## <a name="next-steps"></a>Nästa steg  

* Granska [begärandebegränsning](throttling-requests.md) dokumentation.  

## <a name="see-also"></a>Se också  

* [Bing Web Search API-referens](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference)
