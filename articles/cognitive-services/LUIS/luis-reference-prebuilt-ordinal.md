---
title: Ordningstalet fördefinierade entitet
titleSuffix: Azure
description: Den här artikeln innehåller ordningstal fördefinierade entitetsinformation i Språkförståelse (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 06/25/2019
ms.author: diberry
ms.openlocfilehash: 16529c8334ae9f2eed5715abb22dcbcdbebec7c4
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/01/2019
ms.locfileid: "67485112"
---
# <a name="ordinal-prebuilt-entity-for-a-luis-app"></a>Ordningstalet fördefinierade entitet för en LUIS-app
Ordningstal är en numerisk representation av ett objekt i en uppsättning: `first`, `second`, `third`. Eftersom den här entiteten har redan tränats, behöver du inte att lägga till exempel yttranden som innehåller ordningstal till programmet avsikter. Ordningstalet entitet stöds i [många kulturer](luis-reference-prebuilt-entities.md). 

## <a name="types-of-ordinal"></a>Typer av nummer
Ordningstal hanteras från den [identifierare fulltext](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/English/English-Numbers.yaml#L45) GitHub-lagringsplats

## <a name="resolution-for-prebuilt-ordinal-entity"></a>Lösning för fördefinierade ordningstal entitet

### <a name="api-version-2x"></a>API-version 2.x

I följande exempel visas av lösningen på den **builtin.ordinal** entitet.

```json
{
  "query": "Order the second option",
  "topScoringIntent": {
    "intent": "OrderFood",
    "score": 0.9993253
  },
  "intents": [
    {
      "intent": "OrderFood",
      "score": 0.9993253
    },
    {
      "intent": "None",
      "score": 0.05046708
    }
  ],
  "entities": [
    {
      "entity": "second",
      "type": "builtin.ordinal",
      "startIndex": 10,
      "endIndex": 15,
      "resolution": {
        "value": "2"
      }
    }
  ]
}
```

### <a name="preview-api-version-3x"></a>Förhandsversionen av API 3.x

Följande JSON är med i `verbose` parameteruppsättning till `false`:

```json
{
    "query": "Order the second option",
    "prediction": {
        "normalizedQuery": "order the second option",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.7124502
            }
        },
        "entities": {
            "ordinal": [
                {
                    "offset": 2,
                    "relativeTo": "start"
                }
            ]
        }
    }
}
```

Följande JSON är med i `verbose` parameteruppsättning till `true`:

```json
{
    "query": "Order the second option",
    "prediction": {
        "normalizedQuery": "order the second option",
        "topIntent": "None",
        "intents": {
            "None": {
                "score": 0.7124502
            }
        },
        "entities": {
            "ordinal": [
                {
                    "offset": 2,
                    "relativeTo": "start"
                }
            ],
            "$instance": {
                "ordinal": [
                  {
                    "type": "builtin.ordinal",
                    "text": "second",
                    "startIndex": 10,
                    "length": 6,
                    "modelTypeId": 2,
                    "modelType": "Prebuilt Entity Extractor",
                    "recognitionSources": [
                        "model"
                    ]
                  }
                ]
            }
        }
    }
}
```

## <a name="next-steps"></a>Nästa steg

Lär dig mer om den [OrdinalV2](luis-reference-prebuilt-ordinal-v2.md), [telefonnummer](luis-reference-prebuilt-phonenumber.md), och [temperatur](luis-reference-prebuilt-temperature.md) entiteter. 
