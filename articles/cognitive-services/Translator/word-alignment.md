---
title: Justering av Word - Translator Text API
titlesuffix: Azure Cognitive Services
description: Ta emot word justering information från Translator Text API.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.custom: seodec18
ms.openlocfilehash: 3db5e9651e307211e9dccee20bb8d69586bb9ef1
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67448169"
---
# <a name="how-to-receive-word-alignment-information"></a>Ta emot word justering information

## <a name="receiving-word-alignment-information"></a>Ta emot word justering information
Att ta emot information om justering, använda metoden Översätt och ta med valfria includeAlignment-parameter.

## <a name="alignment-information-format"></a>Justering informationsformat
Justering returneras som ett strängvärde i följande format för varje ord i källan. Information för varje ord är avgränsade med blanksteg, inklusive för icke-blankstegsavgränsad språk (skript) som kinesiska:

[[SourceTextStartIndex]:[SourceTextEndIndex]–[TgtTextStartIndex]:[TgtTextEndIndex]] *

Exempel justering sträng: "0:0-7:10 1:2-11:20 3:4-0:3 3:4-4:6 5:5-21:21".

Med andra ord avgränsas start och slut-index, ett streck separerar språk och blanksteg mellan ord. Ett ord som kan anpassas till noll, ett eller flera ord på andra språk och justerade orden kanske inte är sammanhängande. Om det finns ingen information om justering ska textjustering elementet vara tomt. Metoden returnerar inget fel i så fall.

## <a name="restrictions"></a>Begränsningar
Justering returneras bara i det här läget för en delmängd av språkpar:
* från engelska till andra språk.
* från ett annat språk till engelska förutom kinesiska, förenklad kinesiska, traditionell kinesiska och lettiska till engelska
* från japanska till koreanska eller koreanska till japanska du får inte justering information om meningen är en kapslade översättning. Exempel på en kapslade översättning är ”detta är ett test”, ”jag älskar du” och andra hög frekvens meningar.

## <a name="example"></a>Exempel

Exempel på JSON

```json
[
  {
    "translations": [
      {
        "text": "Kann ich morgen Ihr Auto fahren?",
        "to": "de",
        "alignment": {
          "proj": "0:2-0:3 4:4-5:7 6:10-25:30 12:15-16:18 17:19-20:23 21:28-9:14 29:29-31:31"
        }
      }
    ]
  }
]
```
