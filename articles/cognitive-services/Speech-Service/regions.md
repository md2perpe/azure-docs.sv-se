---
title: Regioner – Speech Services
titlesuffix: Azure Cognitive Services
description: Referens för regioner Speech-tjänsten.
services: cognitive-services
author: mahilleb-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/12/2019
ms.author: panosper
ms.custom: seodec18
ms.openlocfilehash: 1ad618e9eb70fd75f433030584c0f6538532928f
ms.sourcegitcommit: c63e5031aed4992d5adf45639addcef07c166224
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67466995"
---
# <a name="speech-service-supported-regions"></a>Med Taltjänsten regioner som stöds

Med taltjänsten kan ditt program kan omvandla ljud till text, utför talöversättning och konvertera text till tal. Tjänsten är tillgänglig i flera regioner med unika slutpunkter för taligenkänning SDK och REST API: er.

Se till att du använder den slutpunkt som matchar regionen för din prenumeration.

## <a name="speech-sdk"></a>Speech SDK

I den [tal SDK](speech-sdk.md), regioner har angetts som en sträng (till exempel som en parameter till `SpeechConfig.FromSubscription` i tal SDK för C#).

### <a name="speech-to-text-text-to-speech-and-translation"></a>Tal till text och text till tal översättning

Tal-SDK är tillgänglig i dessa regioner för **taligenkänning**, **text till tal**, och **translation**:

  Region | Parametern för tal-SDK | Tal anpassning av portalen
 ------|-------|--------
 Västra USA | `westus` | https://westus.cris.ai
 Västra USA 2 | `westus2` | https://westus2.cris.ai
 Östra USA | `eastus` | https://eastus.cris.ai
 USA, östra 2 | `eastus2` | https://eastus2.cris.ai
 Centrala USA | `centralus` | https://centralus.cris.ai
 Norra centrala USA | `northcentralus` | https://northcentralus.cris.ai
 Södra centrala USA | `southcentralus` | https://southcentralus.cris.ai
 Indien, centrala | `centralindia` | https://centralindia.cris.ai
 Östasien | `eastasia` | https://eastasia.cris.ai
 Sydostasien | `southeastasia` | https://southeastasia.cris.ai
 Östra Japan | `japaneast` | https://japaneast.cris.ai
 Sydkorea, centrala | `koreacentral` | https://koreacentral.cris.ai
 Östra Australien | `australiaeast` | https://australiaeast.cris.ai
 Centrala Kanada | `canadacentral` | https://canadacentral.cris.ai
 Norra Europa | `northeurope` | https://northeurope.cris.ai
 Västra Europa | `westeurope` | https://westeurope.cris.ai
 Storbritannien, södra | `uksouth` | https://uksouth.cris.ai
 Frankrike, centrala | `francecentral` | https://francecentral.cris.ai

### <a name="intent-recognition"></a>Avsiktsigenkänning

Tillgängliga regioner för **avsiktsigenkänning** via tal SDK är följande:

 Global region | Region | Parametern för tal-SDK
 ------|-------|--------
 Asien | Östasien | `eastasia`
 Asien | Sydostasien | `southeastasia`
 Australien | Östra Australien | `australiaeast`
 Europa | Norra Europa | `northeurope`
 Europa | Västra Europa | `westeurope`
 Nordamerika | Östra USA | `eastus`
 Nordamerika | USA, östra 2 | `eastus2`
 Nordamerika | Södra centrala USA | `southcentralus`
 Nordamerika | Västra centrala USA | `westcentralus`
 Nordamerika | Västra USA | `westus`
 Nordamerika | Västra USA 2 | `westus2`
 Sydamerika | Södra Brasilien | `brazilsouth`

Det här är en delmängd av publishing regioner som stöds av den [tjänst för Språkförståelse (LUIS)](/azure/cognitive-services/luis/luis-reference-regions).

### <a name="voice-first-virtual-assistants"></a>Röst första virtuella assistenter

Den [tal SDK](speech-sdk.md) stöder **röst första virtuella assistenter** funktioner i dessa regioner:

Region | Parametern för tal-SDK
-------|---------------------
Västra USA | `westus`
Västra USA 2 | `westus2`
Östra USA | `eastus`
USA, östra 2 | `eastus2`
Västra Europa | `westeurope`
Norra Europa | `northeurope`
Sydostasien | `southeastasia`

## <a name="rest-apis"></a>REST API:er

Speech-tjänsten exponerar även REST-slutpunkter för förfrågningar om tal till text och text till tal.

### <a name="speech-to-text"></a>Tal till text

Tal till text referensdokumentation finns [tal till text REST API](rest-speech-to-text.md).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-speech-to-text.md)]

### <a name="text-to-speech"></a>Text till tal

För text till tal-referensdokumentation finns i [REST-API för text till tal](rest-text-to-speech.md).

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]