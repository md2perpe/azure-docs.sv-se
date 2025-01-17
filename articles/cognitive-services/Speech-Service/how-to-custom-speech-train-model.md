---
title: Träna en modell för anpassat tal – Speech Services
titlesuffix: Azure Cognitive Services
description: Det är nödvändigt att förbättra noggrannheten för både Microsofts baslinje modell eller en anpassad modell som du planerar att skapa utbildning tal-till-text. En modell tränas med märkta mänskliga avskrifter och relaterade text. Dessa datauppsättningar tillsammans med tidigare överförda ljuddata används för att förfina och träna tal till text-modellen ska kunna identifiera ord, fraser, förkortningar, namn och andra villkor för olika produkter.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 49195efa54c8e6eca9186a9e2fc33da84ff34413
ms.sourcegitcommit: c0419208061b2b5579f6e16f78d9d45513bb7bbc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/08/2019
ms.locfileid: "67625392"
---
# <a name="train-a-model-for-custom-speech"></a>Träna en modell för anpassat tal

Det är nödvändigt att förbättra noggrannheten för både Microsofts baslinje modell eller en anpassad modell som du planerar att skapa utbildning tal-till-text. En modell tränas med märkta mänskliga avskrifter och relaterade text. Dessa datauppsättningar tillsammans med tidigare överförda ljuddata används för att förfina och träna tal till text-modellen ska kunna identifiera ord, fraser, förkortningar, namn och andra villkor för olika produkter. Den mer i domäner datauppsättningar som du anger (data som är relaterade till användare säger och vad du förväntar dig att identifiera), desto mer exakt din modell kommer att vara, vilket resulterar i bättre tolkning. Tänk på att genom orelaterade data i din utbildning kan du minska eller försämra korrektheten i din modell.

## <a name="use-training-to-resolve-accuracy-issues"></a>Använd träning för att lösa problem för noggrannhet

Om du får igenkänning av problem med din modell, med hjälp av mänskliga etikett kan betyg och relaterade data för ytterligare utbildning hjälpa till att förbättra noggrannheten. Använd den här tabellen för att avgöra vilken datauppsättning du ska använda för att lösa ditt problem:

| Användningsfall | Datatyp |
|----------|-----------|
| Förbättra taligenkänning på branschspecifika ordförråd och grammatik, till exempel medicinsk terminologi eller IT-jargong | Relaterade text (meningar/yttranden) |
| Definiera fonetiska och visas form av ett ord eller en term som har avvikande uttal, som produktnamn eller förkortningar. | Relaterade text (uttal) |
| Förbättra taligenkänning på talar formatmallar, uttal eller specifika bakgrundsljud | Ljud + mänskliga etikett avskrifter |
> [!IMPORTANT]
> Om du inte har överfört en datauppsättning, hittar du i [Förbered och testa dina data](how-to-custom-speech-test-data.md). Det här dokumentet innehåller anvisningar för att ladda upp data och riktlinjer för att skapa högkvalitativa datauppsättningar.

## <a name="train-and-evaluate-a-model"></a>Träna och utvärdera en modell

Det första steget för att träna en modell är att överföra träningsdata. Använd [Förbered och testa dina data](how-to-custom-speech-test-data.md) för stegvisa instruktioner för att förbereda mänskliga etikett avskrifter och relaterade text (yttranden och uttal). När du har överfört träningsdata, följer du dessa instruktioner för att starta träna din modell:

1. Gå till **tal till text > anpassat tal > utbildning**.
2. Klicka på **träningsmodell**.
3. Sedan ge utbildning en **namn** och **beskrivning**.
4. Från den **modell för scenariot och baslinje** nedrullningsbara menyn, Välj det scenario som passar bäst för din domän. Om du är osäker på vilket scenario du väljer, Välj **Allmänt**. Baslinje är startpunkten för utbildning. Om du inte har en inställning kan använda du den senaste versionen.
5. Från den **Välj träningsdata** väljer du en eller flera ljud + mänskliga etikett avskrift datauppsättningar som du vill använda för träning.
6. När utbildningen är klar kan välja du att utföra Precision testning på den nyligen tränade modellen. Det här steget är valfritt.
7. Välj **skapa** att bygga din anpassade modell.

Tabellen utbildning visas en ny post som motsvarar till den nya modellen. Tabellen visar även status:  Bearbetningsfunktioner, lyckades, misslyckades.

## <a name="evaluate-the-accuracy-of-a-trained-model"></a>Utvärdera det arbete du utfört en träningsmodell

Du kan granska data och utvärdera modellens Precision med hjälp av dessa dokument:

* [Granska dina data](how-to-custom-speech-inspect-data.md)
* [Utvärdera dina data](how-to-custom-speech-evaluate-data.md)


Om du väljer att testa noggrannhet, är det viktigt att välja en akustisk datauppsättning som skiljer sig från den som du använde för att få en realistisk uppfattning modellens prestanda med din modell.

## <a name="next-steps"></a>Nästa steg

* [Distribuera din modell](how-to-custom-speech-deploy-model.md)

## <a name="additional-resources"></a>Ytterligare resurser

* [Förbereda och testa dina data](how-to-custom-speech-test-data.md)
* [Granska dina data](how-to-custom-speech-inspect-data.md)
* [Utvärdera dina data](how-to-custom-speech-evaluate-data.md)
* [Träna din modell](how-to-custom-speech-train-model.md)
