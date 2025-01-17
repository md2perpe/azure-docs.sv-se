---
title: Snabbstart – skicka en fråga till den lokala företag i Bing med hjälp av Node.js | Microsoft Docs
titleSuffix: Azure Cognitive Services
description: Börja använda Bing-API för sökning av lokala företag i noden.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: quickstart
ms.date: 11/01/2018
ms.author: rosh
ms.openlocfilehash: 07b238f900a7f0ba15ad7b37ba7e194567fa0f6a
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/05/2019
ms.locfileid: "67592712"
---
# <a name="quickstart-send-a-query-to-the-bing-local-business-search-api-using-nodejs"></a>Snabbstart: Skicka en fråga till den lokala företag i Bing med hjälp av Node.js

Använd den här snabbstarten för att börja skicka begäranden till den lokala företag i Bing, vilket är en Azure Cognitive Service. Medan detta enkla program är skriven i Node.js är API: et en RESTful-webb-tjänst som är kompatibel med alla programmeringsspråk som HTTP-förfrågningar och JSON-parsning.

Det här exempelprogrammet hämtar lokala svarsdata från API: et för sökfrågan `hotel in Bellevue`.

## <a name="prerequisites"></a>Förutsättningar

* Den senaste versionen av [Node.js](https://nodejs.org/en/download/).

* [Begäransbiblioteket för JavaScript](https://github.com/request/request)

Du måste ha en [Cognitive Services API-konto](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) med Bing-API: er. Det räcker med en [kostnadsfri utvärderingsversion](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) för den här snabbstarten. Använd åtkomstnyckel som tillhandahållits av den kostnadsfria utvärderingsversionen.  Se även [Priser för Cognitive Services – API för Bing-sökning](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

## <a name="code-scenario"></a>Kodscenario

Följande kod hämtar definierar och skickar en begäran. Den implementeras i följande steg:

1. Deklarera variabler för att specificera slutpunkten med hjälp av värd och sökväg.
2. Ange frågan och Lägg till Frågeparametern.
3. Skapa en hanterarfunktion för svaret.
4. Definiera sökfunktionen som skapar begäran och lägger till Ocp-Apim-Subscription-Key-huvudet.
5. Köra sökfunktionen.

Här följer den fullständiga koden för demon:

```
'use strict';

let https = require('https');

// Replace the subscriptionKey string value with your valid subscription key.
let subscriptionKey = 'your-access-key';

let host = 'api.cognitive.microsoft.com/bing';
let path = '/v7.0/localbusinesses/search';

let mkt = 'en-US';
let q = 'hotel in Bellevue';

let params = '?q=' + encodeURI(q) + "&mkt=" + mkt;

let response_handler = function (response) {
    let body = '';
    response.on('data', function (d) {
        body += d;
    });
    response.on('end', function () {
        let body_ = JSON.parse(body);
        let body__ = JSON.stringify(body_, null, '  ');
        console.log(body__);
    });
    response.on('error', function (e) {
        console.log('Error: ' + e.message);
    });
};

let Search = function () {
    let request_params = {
        method: 'GET',
        hostname: host,
        path: path + params,
        headers: {
            'Ocp-Apim-Subscription-Key': subscriptionKey,
        }
    };

    let req = https.request(request_params, response_handler);
    req.end();
}

Search();

```

## <a name="next-steps"></a>Nästa steg

* [Lokala företag Search-Snabbstart](local-quickstart.md)
* [Lokala företag Search-Java-Snabbstart](local-search-java-quickstart.md)
* [Lokala företag Search Python-Snabbstart](local-search-python-quickstart.md)
