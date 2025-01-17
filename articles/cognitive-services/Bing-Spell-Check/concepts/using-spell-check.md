---
title: Använda API:et för stavningskontroll i Bing
titlesuffix: Azure Cognitive Services
description: Lär dig om lägen och inställningar för Stavningskontroll i Bing samt annan information som berör API:et.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: overview
ms.date: 02/20/2019
ms.author: aahi
ms.openlocfilehash: b847615e8440a8992c8130d12cd6111afe3d33d2
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66390226"
---
# <a name="using-the-bing-spell-check-api"></a>Använda API:et för stavningskontroll i Bing

Använd den här artikeln för att lära dig om användning av API för stavningskontroll i Bing för att utföra kontextbaserad kontroll av grammatik och stavning. Medan de flesta stavningskontroller använder ordlistebaserade regeluppsättningar utnyttjar stavningskontroll i Bing maskininlärning och statistisk maskinöversättning för att ge noggranna och kontextbaserade korrigeringar. 

## <a name="spell-check-modes"></a>Stavningskontrollslägen

Detta API har två korrigeringslägen, `Proof` och `Spell`.  Prova några exempel [här](https://azure.microsoft.com/services/cognitive-services/spell-check/).

### <a name="proof---for-documents"></a>Korrektur – för dokument 

Standardläget är `Proof`. Stavningsläget `Proof` ger de mest omfattande kontrollerna, och lägger till versaler, grundläggande interpunktion och andra funktioner när du skapar dokument. Det är bara tillgängligt för en-US (amerikansk engelska), es-ES (spanska), pt-BR (portugisiska) (Obs! endast i betaversion för spanska och portugisiska). För övriga marknader anger du lägesfrågeparametern till Stavning. 

> [!NOTE]
> Om frågetexten är längre än 4 096 tecken kortas den ner till 4 096 tecken innan den bearbetas. 

### <a name="spell----for-web-searchesqueries"></a>Stavningskontroll – för webbsökningar/frågor

`Spell` är mer aggressivt för att returnera bättre sökresultat. Läget `Spell` hittar de flesta stavfel men hittar inte en del grammatikfel som `Proof` fångar upp, som versaler/gemener och upprepade ord.

> [!NOTE]
> * Den högsta frågelängd som stöds visas nedan. Om frågan överskrider maxlängden ändras inte frågan och dess resultat.
>    * 130 tecken för följande språkkoder: en, de, es, fr, pl, pt, sv, ru, nl, nb, tr-tr, den, zh, ko. 
>    * 65 tecken för alla andra.
> * Stavningskontrollsläget stöder inte hakparenteser (`[` och `]`) i frågor, och kan orsaka inkonsekventa resultat. Vi rekommenderar att du tar bort dem från dina frågor när du använder läget för stavningskontroll.

## <a name="market-setting"></a>Marknadsinställning

En [marknadskod](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference#market-codes) ska anges med frågeparametern `mkt` i din begäran. Annars använder API:et en standardmarknad baserat på begärans IP-adress.


## <a name="http-post-and-get-support"></a>Stöd för HTTP POST och GET

Detta API har stöd för HTTP POST eller HTTP GET. Vilket du ska använda beror på längden på den text som du ska korrekturläsa. Om strängarna alltid är högst än 1 500 tecken använder du GET (hämtning). Om du vill kunna använda strängar på upp till 10 000 tecken använder du POST (publicering). Textsträngen får innehålla giltiga UTF-8-tecken.

I följande exempel visas en POST-begäran för kontroll av stavning och grammatik i en textsträng. Exemplet innehåller frågeparametern [mode](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference#mode) (läge) för fullständighetens skull (den kunde ha utelämnats eftersom standard för `mode` är Korrektur). Frågeparametern [text](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference#text) innehåller den sträng som ska korrigeras.
  
```  
POST https://api.cognitive.microsoft.com/bing/v7.0/spellcheck?mode=proof&mkt=en-us HTTP/1.1  
Content-Type: application/x-www-form-urlencoded  
Content-Length: 47  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
 
text=when+its+your+turn+turn,+john,+come+runing  
``` 

Om du använder HTTP GET måste du även ange frågeparametern `text` i webbadressens frågesträng
  
Nedan visas svaret på den tidigare begäran. Svaret innehåller ett [SpellCheck](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference#spellcheck)-objekt. 
  
```json
{  
    "_type" : "SpellCheck",  
    "flaggedTokens" : [{  
        "offset" : 5,  
        "token" : "its",  
        "type" : "UnknownToken",  
        "suggestions" : [{  
            "suggestion" : "it's",  
            "score" : 1  
        }]  
    },  
    {  
        "offset" : 25,  
        "token" : "john",  
        "type" : "UnknownToken",  
        "suggestions" : [{  
            "suggestion" : "John",  
            "score" : 1  
        }]  
    },  
    {  
        "offset" : 19,  
        "token" : "turn",  
        "type" : "RepeatedToken",  
        "suggestions" : [{  
            "suggestion" : "",  
            "score" : 1  
        }]  
    },  
    {  
        "offset" : 35,  
        "token" : "runing",  
        "type" : "UnknownToken",  
        "suggestions" : [{  
            "suggestion" : "running",  
            "score" : 1  
        }]  
    }]  
}  
```  
  
I fältet [flaggedTokens](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference#flaggedtokens) visas en lista över de stavnings- och grammatikfel som hittats i [text](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference#text)-strängen. I fältet `token` finns det ord som ska bytas ut. Du använder den nollbaserade förskjutningen i fältet `offset` för att hitta token i `text`-strängen. Sedan ersätter du ordet på den platsen med ordet i fältet `suggestion`. 

Om fältet `type` är RepeatedToken kan du ändå ersätta token med `suggestion`, men behöver troligen också att ta bort avslutande blanksteget.

## <a name="throttling-requests"></a>Begränsningsbegäranden

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>Nästa steg

- [Vad är API för stavningskontroll i Bing?](../overview.md)
- [API-referens för stavningskontroll i Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference)
