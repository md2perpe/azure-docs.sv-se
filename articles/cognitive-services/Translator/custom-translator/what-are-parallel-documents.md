---
title: Vad är parallella dokument? – Anpassade Translator
titleSuffix: Azure Cognitive Services
description: Parallell dokument är par i dokument om en sådan översättningen av den andra. Ett dokument i paret innehåller meningar i källspråket och det andra dokumentet innehåller dessa meningar översättas till mål-språk.
author: swmachan
manager: christw
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: 3b7997adfa38fa24bdf41f22c99f06e9a0ba1bf0
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67447397"
---
# <a name="what-are-parallel-documents"></a>Vad är parallella dokument?

Parallell dokument är par i dokument om en sådan översättningen av den andra. Ett dokument i paret innehåller meningar i källspråket och det andra dokumentet innehåller dessa meningar översättas till mål-språk.
Det spelar ingen roll vilket språk som har markerats som ”källa” och vilket språk som har markerats som ”target” – en parallell dokumentet kan användas för att träna en översättningssystemet i båda riktningarna.

## <a name="requirements"></a>Krav

Du behöver minst 10 000 unika parallella meningar att träna ett system. Som bästa praxis, kan du kontinuerligt lägga till mer parallella innehåll och omtrimning att förbättra kvaliteten på översättningssystemet.

Microsoft kräver att dokument som laddas upp till den anpassade Translator inte bryter mot en tredje parts upphovsrätt eller immateriell egenskaper. Mer information finns i den [användningsvillkor](https://azure.microsoft.com/support/legal/cognitive-services-terms/).
Ladda upp ett dokument med hjälp av portalen ändras inte ägarskapet för immateriell egendom i dokumentet.

## <a name="use-of-parallel-documents"></a>Användning av parallella dokument

Parallell dokument används av systemet:

1.  Du vill veta hur ord och fraser meningar mappas vanligtvis mellan de två språk.

2.  Lär dig hur du bearbetar rätt kontext beroende på de omgivande fraserna. Ett ord kanske inte alltid att översättas till exakt samma ord på andra språk.

Som bästa praxis, se till att det finns ett 1:1 mening samband mellan källan och målet språkversioner av dokument.

Om ditt projekt är domän (kategori) specifika bör dokument överensstämma med terminologin i den kategorin. Kvaliteten på översättningssystemet beror på antalet meningar i din dokumentet och kvaliteten på meningarna. Fler exempel dokumenten innehåller med olika användningsområden för ett ord som är specifik för din, bättre systemet kan göra under översättning.

Dokument som laddats upp är privata för varje arbetsyta och kan användas i samma projekt eller kurser som du vill. Meningar som extraheras från dina dokument lagras separat i databasen som vanlig Unicode-textfiler och är tillgängliga för du ta bort. Använd inte anpassade Translator som en dokumentdatabas, kan du inte kan hämta de dokument som du överförde i formatet du laddat upp dem.



## <a name="next-steps"></a>Nästa steg

- Lär dig hur du använder en [ordlista](what-is-dictionary.md) i anpassade Translator.
