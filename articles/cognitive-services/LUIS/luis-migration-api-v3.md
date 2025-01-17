---
title: V2 till V3 API-migrering
titleSuffix: Azure Cognitive Services
description: 'Version 3-slutpunkten API: er har ändrats. Använd den här guiden för att förstå hur du migrerar till version 3-slutpunkten API: er.'
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 06/24/2019
ms.author: diberry
ms.openlocfilehash: 4c08c95a05d4f22e2338a7264409aec0f64a4755
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67442525"
---
# <a name="preview-migrate-to-api-version-3x-for-luis-apps"></a>Förhandsversion: Migrera till API-version 3.x för LUIS-appar

Fråga förutsägelse slutpunkten API: er har ändrats. Använd den här guiden för att förstå hur du migrerar till version 3-slutpunkten API: er. 

Den här V3-API: et tillhandahåller följande nya funktioner, bland annat viktiga JSON-begäran/svar eller ändringar: 

* [Externa enheter](#external-entities-passed-in-at-prediction-time)
* [Dynamisk listor](#dynamic-lists-passed-in-at-prediction-time)
* [Fördefinierade JSON entitetsändringarna](#prebuilt-entities-with-new-json)

<!--
* [Multi-intent detection of utterance](#detect-multiple-intents-within-single-utterance)
-->

Fråga förutsägelse slutpunkten [begäran](#request-changes) och [svar](#response-changes) har betydande förändringar för de nya funktionerna som visas ovan, inklusive följande:

* [Svaret objekt ändras](#top-level-json-changes)
* [Entitetsreferenser rollen namn i stället för entitetsnamn](#entity-role-name-instead-of-entity-name)
* [Egenskaper för att markera entiteter i yttranden](#marking-placement-of-entities-in-utterances)

Följande funktioner i LUIS **stöds inte** i V3-API:

* Bing Spell Check V7

[Referensdokumentation](https://aka.ms/luis-api-v3) är tillgänglig för V3.

## <a name="endpoint-url-changes-by-slot-name"></a>Slutpunkten URL ändringar av platsens namn

Formatet för V3 endpoint HTTP-anropet har ändrats.

|METODEN|URL|
|--|--|
|HÄMTA|https://<b>{REGION}</b>.api.cognitive.microsoft.com/luis/<b>v3.0-preview</b>/apps/<b>{APP-ID}</b>/slots/<b>{SLOT-NAME}</b>/predict?query=<b>{QUERY}</b>|
|POST|https://<b>{REGION}</b>.api.cognitive.microsoft.com/luis/<b>v3.0-preview</b>/apps/<b>{APP-ID}</b>/slots/<b>{SLOT-NAME}</b>/predict|
|||

## <a name="endpoint-url-changes-by-version-id"></a>Slutpunkten URL ändringar av versions-ID

Om du vill fråga efter version måste du först till [publicera via API: et](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c3b) med den `"directVersionPublish":true`. Fråga den slutpunkt som refererar till versions-ID i stället för plats-namn.


|METODEN|URL|
|--|--|
|HÄMTA|https://<b>{REGION}</b>.api.cognitive.microsoft.com/luis/<b>v3.0-preview</b>/apps/<b>{APP-ID}</b>/versions/<b>{VERSION-ID}</b>/predict?query=<b>{QUERY}</b>|
|POST|https://<b>{REGION}</b>.api.cognitive.microsoft.com/luis/<b>v3.0-preview</b>/apps/<b>{APP-ID}</b>/versions/<b>{VERSION-ID}</b>/predict|
|||

## <a name="prebuilt-entities-with-new-json"></a>Fördefinierade entiteter med nya JSON

V3 svar objektet förändringarna innefattar [förskapade entiteter](luis-reference-prebuilt-entities.md). 

## <a name="request-changes"></a>Begära ändringar 

### <a name="query-string-parameters"></a>Parametrar för frågesträngen

V3-API: et har olika parametrar för frågesträngen.

|Parameternamn|Typ|Version|Standard|Syfte|
|--|--|--|--|--|
|`log`|boolean|V2 & V3|false|Store-frågan i loggfilen.| 
|`query`|string|Endast v3|Ingen standard – det är obligatoriskt i GET-begäran|**I V2**, uttryck till att förutse finns i den `q` parametern. <br><br>**I V3**, funktionen skickas den `query` parametern.|
|`show-all-intents`|boolean|Endast v3|false|Returnera alla avsikter med motsvarande poängen på den **prediction.intents** objekt. Avsikter returneras som objekt i en överordnad `intents` objekt. På så sätt kan programmässig åtkomst utan att behöva hitta avsikten i en matris: `prediction.intents.give`. I V2 kan returnerade dessa i en matris. |
|`verbose`|boolean|V2 & V3|false|**I V2**när inställd på true, alla förväntade avsikter returnerades. Om du behöver alla förväntade avsikter använder V3-param av `show-all-intents`.<br><br>**I V3**, den här parametern innehåller entiteten metadata information om entiteten förutsägelse.  |



<!--
|`multiple-segments`|boolean|V3 only|Break utterance into segments and predict each segment for intents and entities.|
-->


### <a name="the-query-prediction-json-body-for-the-post-request"></a>Fråga förutsägelsen JSON brödtext för den `POST` begäran

```JSON
{
    "query":"your utterance here",
    "options":{
        "datetimeReference": "2019-05-05T12:00:00",
        "overridePredictions": true
    },
    "externalEntities":[],
    "dynamicLists":[]
}
```

|Egenskap|Typ|Version|Standard|Syfte|
|--|--|--|--|--|
|`dynamicLists`|array|Endast v3|Krävs inte.|[Dynamisk listor](#dynamic-lists-passed-in-at-prediction-time) kan du utöka en befintlig lista över tränade och publicerade entitet redan i LUIS-app.|
|`externalEntities`|array|Endast v3|Krävs inte.|[Externa enheter](#external-entities-passed-in-at-prediction-time) ge LUIS-appen möjlighet att identifiera och märka entiteter under körning, som kan användas som funktioner för befintliga entiteter. |
|`options.datetimeReference`|string|Endast v3|Inget standardvärde|Används för att fastställa [datetimeV2 offset](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity).|
|`options.overridePredictions`|boolean|Endast v3|false|Anger om användarens [externa entitet (med samma namn som befintlig entitet)](#override-existing-model-predictions) används eller befintlig entitet i modellen används för förutsägelse. |
|`query`|string|Endast v3|Krävs.|**I V2**, uttryck till att förutse finns i den `q` parametern. <br><br>**I V3**, funktionen skickas den `query` parametern.|



## <a name="response-changes"></a>Svaret ändringar

Svaret på frågan JSON som har ändrats för att tillåta större programmatisk åtkomst till de data som används oftast. 

### <a name="top-level-json-changes"></a>Översta JSON ändras

De översta JSON-egenskaperna för V2 är när `verbose` har angetts till true, som returnerar alla avsikter och deras riskpoäng i den `intents` egenskapen:

```JSON
{
    "query":"this is your utterance you want predicted",
    "topScoringIntent":{},
    "intents":[],
    "entities":[],
    "compositeEntities":[]
}
```

De översta JSON-egenskaperna för V3 är:

```JSON
{
    "query": "this is your utterance you want predicted",
    "prediction":{
        "normalizedQuery": "this is your utterance you want predicted - after normalization",
        "topIntent": "intent-name-1",
        "intents": {}, 
        "entities":{}
    }
}
```

Den `intents` objektet är en osorterad lista. Utgår inte från den första underordnat i den `intents` motsvarar den `topIntent`. Använd i stället de `topIntent` värde att söka efter poängen:

```nodejs
const topIntentName = response.prediction.topIntent;
const score = intents[topIntentName];
```

Schemaändringar för svar JSON Tillåt för:

* Rensa skillnaden mellan ursprungliga uttryck `query`, och returneras prognoser, `prediction`.
* Enklare Programmeringsåtkomst till förväntade data. I stället för att räkna upp en rad i V2, kan du komma åt värden av **med namnet** för både avsikter och entiteter. Rollnamnet returneras för förväntade entiteten roller, eftersom det är unikt i hela appen.
* Datatyper, respekteras om fastställas. Numeriska värden returneras inte längre som strängar.
* Skillnaden mellan första prioritet förutsägelse information och ytterligare metadata som returneras i de `$instance` objekt. 

### <a name="access-instance-for-entity-metadata"></a>Åtkomst `$instance` för metadata för entitet

Om du behöver enhetsmetadata frågesträngen måste använda den `verbose=true` flaggan och svaret innehåller metadata i den `$instance` objekt. Exempel visas i JSON-svaren i följande avsnitt.

### <a name="each-predicted-entity-is-represented-as-an-array"></a>Varje entitet som förväntade representeras som en matris

Den `prediction.entities.<entity-name>` objektet innehåller en matris, eftersom varje entitet går att förutse mer än en gång i uttryck. 

### <a name="list-entity-prediction-changes"></a>Lista entitetsändringarna för förutsägelse

JSON för en lista över entiteten förutsägelse har ändrats för att vara en matris med matriser:

```JSON
"entities":{
    "my_list_entity":[
        ["canonical-form-1","canonical-form-2"],
        ["canonical-form-2"]
    ]
}
```
Varje inre matris motsvarar texten i uttryck. Inre objektet är en matris eftersom samma text som kan visas i mer än en underordnad lista för en entitet i listan. 

Vid mappning mellan den `entities` objekt till den `$instance` objekt kan ordningen på objekten bevaras för listan entitet förutsägelser.

```nodejs
const item = 0; // order preserved, use same enumeration for both
const predictedCanonicalForm = entities.my_list_entity[item];
const associatedMetadata = entities.$instance.my_list_entity[item];
```

### <a name="entity-role-name-instead-of-entity-name"></a>Rollnamn för entitet i stället för entitetsnamn 

I V2 kan den `entities` matris returneras alla förväntade entiteter med enhetens namn som den unika identifieraren. I V3 är den primära identifieraren rollnamnet om entiteten använder roller och förutsägelser är för en entitet-roll. Detta är möjligt eftersom rollen entitetsnamn måste vara unikt för hela appen, inklusive andra modellen (avsikt, entity) namn.

I följande exempel: Överväg att ett uttryck som innehåller texten, `Yellow Bird Lane`. Den här texten förväntas som en anpassad `Location` entitets roll `Destination`.

|Uttryck-text|Entitetsnamn|Rollnamn|
|--|--|--|
|`Yellow Bird Lane`|`Location`|`Destination`|

I V2 kan entiteten identifieras av den _entitetsnamn_ med rollen som en egenskap för objektet:

```JSON
"entities":[
    {
        "entity": "Yellow Bird Lane",
        "type": "Location",
        "startIndex": 13,
        "endIndex": 20,
        "score": 0.786378264,
        "role": "Destination"
    }
]
```

I V3, entiteten refererar till den _Entitetsrollen_, om förutsägelser för rollen:

```JSON
"entities":{
    "Destination":[
        "Yellow Bird Lane"
    ]
}
```

I V3, samma resultat med den `verbose` flagga för att returnera metadata för entitet:

```JSON
"entities":{
    "Destination":[
        "Yellow Bird Lane"
    ],
    "$instance":{
        "Destination": [
            {
                "role": "Destination",
                "type": "Location",
                "text": "Yellow Bird Lane",
                "startIndex": 25,
                "length":16,
                "score": 0.9837309,
                "modelTypeId": 1,
                "modelType": "Entity Extractor"
            }
        ]
    }
}
```

## <a name="external-entities-passed-in-at-prediction-time"></a>Externa enheter som har skickats in tidpunkt för förutsägelse

Externa enheter ge LUIS-appen möjlighet att identifiera och märka entiteter under körning, som kan användas som funktioner för befintliga entiteter. På så sätt kan du använda en egen separat och anpassade entitet extraktorer innan du skickar frågor till din slutpunkt för förutsägelse. Eftersom detta görs i fråga förutsägelse slutpunkten, behöver du inte tränar om och publicerar din modell.

Klientprogrammet är att tillhandahålla en egen extraktor entitet genom att hantera entitet matchar och när platsen inom uttryck för den matchande entiteten och sedan skicka denna information i förfrågan. 

Externa enheter är mekanismen för att utöka alla entitetstypen samtidigt som fortfarande används som signalerar till andra modeller som roller, sammansatta och andra.

Detta är användbart för en entitet som har tillgängliga data endast vid körning av fråga förutsägelse. Exempel på den här typen av data ändras ofta data eller specifika per användare. Du kan utöka en LUIS kontakta entitet med extern information från en användares kontaktlista. 

### <a name="entity-already-exists-in-app"></a>Entiteten finns redan i appen

Värdet för `entityName` för externa entiteten, som skickas i slutpunkten POST brödtext måste redan finnas i tränade och publicerade appen när begäran gjordes. Spelar ingen roll vilken typ av enhet, alla typer stöds.

### <a name="first-turn-in-conversation"></a>Aktivera först i en konversation

Överväg en första uttryck i en chatt bot konversation där en användare anger du följande ofullständig information:

`Send Hazem a new message`

Begäran från chattrobot till LUIS kan skicka information i själva INLÄGG om `Hazem` så matchas direkt som en av användarens kontakter.

```json
    "externalEntities": [
        {
            "entityName":"contacts",
            "startIndex": 5,
            "entityLength": 5,
            "resolution": {
                "employeeID": "05013",
                "preferredContactType": "TeamsChat"
            }
        }
    ]
```

Förutsägelse svaret innehåller den externa entiteten, med alla andra förväntade entiteter, eftersom det har definierats i begäran.  

### <a name="second-turn-in-conversation"></a>Aktivera andra i en konversation

Nästa användare-uttryck i chattrobot använder en mer vagt term:

`Send him a calendar reminder for the party.`

I den föregående uttryck i uttryck använder `him` som en referens till `Hazem`. Konversationsanpassad chattrobot i själva POST kan mappa `him` för entiteten-värden som extraheras från den första uttryck `Hazem`.

```json
    "externalEntities": [
        {
            "entityName":"contacts",
            "startIndex": 5,
            "entityLength": 3,
            "resolution": {
                "employeeID": "05013",
                "preferredContactType": "TeamsChat"
            }
        }
    ]
```

Förutsägelse svaret innehåller den externa entiteten, med alla andra förväntade entiteter, eftersom det har definierats i begäran.  

### <a name="override-existing-model-predictions"></a>Åsidosätta befintliga modellens förutsägelser

Den `overridePredictions` alternativ egenskap anger att om användaren skickar en extern entitet som överlappar med en förväntad entitet med samma namn, LUIS väljer den entitet som skickats in eller entiteten finns i modellen. 

Anta exempelvis att frågan `today I'm free`. LUIS identifierar `today` som en datetimeV2 med följande svar:

```JSON
"datetimeV2": [
    {
        "type": "date",
        "values": [
            {
                "timex": "2019-06-21",
                "value": "2019-06-21"
            }
        ]
    }
]
```

Om användaren skickar externa entiteten:

```JSON
{
    "entityName": "datetimeV2",
    "startIndex": 0,
    "entityLength": 5,
    "resolution": {
        "date": "2019-06-21"
    }
}
```

Om den `overridePredictions` är inställd på `false`, LUIS returnerar ett svar som om den externa entiteten inte har skickats. 

```JSON
"datetimeV2": [
    {
        "type": "date",
        "values": [
            {
                "timex": "2019-06-21",
                "value": "2019-06-21"
            }
        ]
    }
]
```

Om den `overridePredictions` är inställd på `true`, LUIS returnerar ett svar, inklusive:

```JSON
"datetimeV2": [
    {
        "date": "2019-06-21"
    }
]
```



#### <a name="resolution"></a>Lösning

Den _valfritt_ `resolution` egenskapen returnerar förutsägelser svar, så att du kan skicka in metadata som associeras med den externa entitet och sedan tar emot det tillbaka i svaret. 

Det primära syftet är att utöka förskapade entiteter men det är inte begränsad till den typ av målentitet. 

Den `resolution` egenskapen kan vara ett tal, en sträng, ett objekt eller en matris:

* "Dallas"
* {"text": "value"}
* 12345 
* ["a", "b", "c"]



## <a name="dynamic-lists-passed-in-at-prediction-time"></a>Dynamisk listor som skickas när förutsägelse

Dynamisk listor kan du utöka en befintlig lista över tränade och publicerade entitet redan i LUIS-app. 

Använd den här funktionen när din entitet listvärden behöver ändras regelbundet. Den här funktionen kan du utöka en lista över redan utbildade och publicerade entitet:

* Vid tidpunkten för slutpunkten för frågan förutsägelsefrågan.
* För en enskild begäran.

Entiteten listan kan vara tom i LUIS-app, men det måste finnas. Lista entiteten i LUIS-app har inte ändrats, men möjligheten för förutsägelse på slutpunkten utökas med upp till 2 listor med ca 1 000 objekt.

### <a name="dynamic-list-json-request-body"></a>Listan med dynamiskt JSON-begärandetexten

Skickar in följande JSON-texten att lägga till en ny underordnad lista med synonymer i listan och förutsäga lista entitet för text, `LUIS`, med den `POST` förutsägelsefrågan som frågan:

```JSON
{
    "query": "Send Hazem a message to add an item to the meeting agenda about LUIS.",
    "options":{
        "timezoneOffset": "-8:00"
    },
    "dynamicLists": [
        {
            "listEntityName":"ProductList",
            "requestLists":[
                {
                    "name": "Azure Cognitive Services",
                    "canonicalForm": "Azure-Cognitive-Services",
                    "synonyms":[
                        "language understanding",
                        "luis",
                        "qna maker"
                    ]
                }
            ]
        }
    ]
}
```

Förutsägelse svaret innehåller entiteten, lista, med alla andra förväntade entiteter, eftersom det har definierats i begäran. 

## <a name="timezoneoffset-renamed-to-datetimereference"></a>TimezoneOffset bytt namn till datetimeReference

**I V2**, `timezoneOffset` [parametern](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity) skickas i förutsägelsefrågan som en frågesträngsparameter, oavsett om begäran skickas som ett GET eller POST-begäran. 

**I V3**, på samma sätt har angetts med parametern POST brödtext `datetimeReference`. 

## <a name="marking-placement-of-entities-in-utterances"></a>Markera placeringen av entiteter i yttranden

**I V2**, en entitet har markerats i ett uttryck med den `startIndex` och `endIndex`. 

**I V3**, entiteten är märkt med `startIndex` och `entityLength`.

## <a name="deprecation"></a>Utfasning 

V2 API gälla inte upphör att för minst nio månader efter V3-förhandsversion. 

## <a name="next-steps"></a>Nästa steg

Använd V3-API-dokumentationen för att uppdatera befintliga REST-anrop till LUIS [endpoint](https://aka.ms/luis-api-v3) API: er. 