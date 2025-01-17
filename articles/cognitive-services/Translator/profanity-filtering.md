---
title: Svordomar filtrering – Translator Text API
titlesuffix: Azure Cognitive Services
description: Använd siris filtrering i Translator Text API.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: cc6bf9766912aa55d7869d90976d3089cfd6cf4c
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67448266"
---
# <a name="add-profanity-filtering-with-the-translator-text-api"></a>Lägg till svordomar filtrering med Translator Text API

Tjänsten Translator behåller normalt svordomar som finns i källan i översättningen. Graden av svordomar och kontext som gör ord olämpliga skiljer sig åt mellan kulturer. Därför graden av svordomar i målspråket framhävas eller minskas.

Om du vill undvika att se svordomar i översättning, även om svordomar finns i källtext, Använd svordomar filtrering alternativ som är tillgängligt i metoden Translate(). Det här alternativet kan du välja om du vill se svordomar bort, markeras med lämpliga taggar eller vidta någon åtgärd krävs.

Metoden Translate() tar parametern ”alternativ” som innehåller det nya elementet ”ProfanityAction”. De godkända värdena för ProfanityAction är ”NoAction”, ”markerat” och ”har tagits bort”.

## <a name="accepted-values-of-profanityaction-and-examples"></a>Godkända värden för ProfanityAction och exempel
|ProfanityAction värde | Åtgärd | Exempel: Käll - japanska | Exempel: Mål - engelska|
| :---|:---|:---|:---|
| NoAction | Standard. Samma som inte ange alternativet. Svordomar Överför från källan till målet. | 彼は変態です。 | Han är en tölp. |
| Markerad | Olämpliga ord. omges av XML-taggar \<svordomar >... \</profanity >. | 彼は変態です。 | Han är en \<svordomar > jerk\</profanity >. |
| Borttaget | Olämpliga ord. tas bort från utdata utan ersättning. | 彼は。 | Han är en. |

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Tillämpa svordomar filtrering med Translator API-anrop](reference/v3-0-translate.md)
