---
title: Vad är Video Indexer?
titlesuffix: Azure Media Services
description: Det här avsnittet ger en översikt över tjänsten Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: juliako
ms.openlocfilehash: 2c72c7c493c0a887adab147054c725a2e1c0659f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65799125"
---
# <a name="what-is-video-indexer"></a>Vad är Video Indexer?

Azure Video Indexer är ett molnprogram som bygger på Azure-medieanalys, Azure Search, Cognitive Services (som Ansikts-API, Microsoft Translator, API för visuellt innehåll och Custom Speech Service). På så sätt kan du extrahera insikter från dina videor med Video Indexer-video och ljud-modeller som beskrivs nedan:
  
## <a name="video-insights"></a>Videoinsikter

- **Ansiktsspårning**: Identifierar och grupperar ansikten visas i videon.
- **Kändisar identifiering**: Video Indexer identifierar automatiskt över 1 miljon kändisar – till exempel world ledare, aktörer, actresses, tävlande, forskare, företag och tekniska ledare i hela världen. Information om dessa kändisar kan också finnas på olika kända webbplatser, till exempel IMDB och Wikipedia.
- **Baserade ansiktsidentifiering**: Video Indexer träna en modell för ett visst konto. Sedan kan den identifiera ansikten i videon baserat på den tränade modellen. Mer information finns i [anpassa en Person modell från webbplatsen Video Indexer](customize-person-model-with-website.md) och [anpassa en Person modell med Video Indexer API](customize-person-model-with-api.md).
- **Miniatyr extrahering för ansikten** (”bästa ansikte”): Automatiskt identifierar bäst avbildade ansikte i varje grupp med ansikten (baserat på kvalitet, storlek och främre placering) och extrahera den som en bild tillgång.
- **Visual textigenkänning** (OCR): Extraherar text som visas visuellt i videon.
- **Visual innehållsmoderering**: Identifierar vuxet/vågat eller visuella objekt.
- **ID för etiketten**: Identifierar visuella objekt och åtgärder som visas.
- **Scen segmentering**: avgör när en scen ändras i videon baserat på visuella tips. En scen visar en enskild händelse och består av ett antal på varandra följande skärmbilder som semantiskt är relaterade. 
- **Som identifiering**: avgör när en som visar ändringar i videon baserat på visuella tips. En är en serie ramar som kommer från samma film kameran. Mer information finns i [scener, skärmbilder och nyckelrutor](scenes-shots-keyframes.md).
- **Svart ramtyp**: Identifierar svart ramar som visas i videon.
- **Extrahering av bildrutan**: Identifierar stabil nyckelrutor i en video.
- **Löpande krediter**: identifiera början och slutet av de löpande krediterna i slutet av och filmer.

## <a name="audio-insights"></a>Ljud insikter

- **Automatisk språkidentifiering**: Identifierar automatiskt dominerande talat språk. Språk som stöds är engelska, spanska, franska, tyska, italienska, kinesiska (förenklad), japanska, ryska och brasiliansk portugisiska kommer vid engelska när språket inte kan identifieras.
- **Ljudutskrift**: Konverterar tal till text i 12 språk och tillåter tillägg. Språk som stöds är engelska, spanska, franska, tyska, italienska, kinesiska (förenklad), japanska, arabiska, ryska, portugisiska (Brasilien), Hindi och koreanska.
- **Textning**: Skapar textning i tre format: VTT, TTML, SRT.
- **Två channel bearbetning**: Automatiskt identifierar, avgränsa avskrift och slår ihop till en enda tidslinje.
- **Buller minskning**: Rensar upp telefoni ljud- eller störningar inspelningar (baserat på Skype filter).
- **Avskriften anpassning** (CRIS): Träna anpassad tal till text modeller för att skapa branschspecifika avskrifter. Mer information finns i [anpassa en språkmodell från webbplatsen Video Indexer](customize-language-model-with-website.md) och [anpassa en språkmodell med API: er för Video Indexer](customize-language-model-with-api.md).
- **Talare uppräkning**: Mappar och förstår vilka talare ekrar vilka ord och när.
- **Talare statistik**: Innehåller statistik för talare tal förhållanden.
- **Textbaserade innehållsmoderering**: Identifierar explicit text i ljudavskrifter.
- **Ljud effekterna**: Identifierar ljud effekter som hand applåder, tal och åsidosatt inaktivitet.
- **Känsloigenkänning**: Identifierar känslor baserat på tal (vad som sägs) och röst toner (hur det som sägs).  Känslan kan vara: glädje, sorg, ilska eller rädsla.
- **Översättning**: Skapar översättningar av ljudavskrifter till 54 olika språk.

## <a name="audio-and-video-insights-multi-channels"></a>Ljud- och insights (med flera kanaler)

När indexering av en kanal delresultat för dessa modeller kommer att vara tillgänglig

- **Extrahering av nyckelord**: Extraherar nyckelord från tal- och visual text.
- **Anpassar extrahering**: Extraherar varumärken från tal- och visual text.
- **Avsnittet inferens**: Gör inferens av viktigaste avsnitten från avskrifter. 1: a-nivå IPTC taxonomi ingår.
- **Artefakter**: Extraherar omfattande uppsättning ”nästa nivå med information om” artefakter för de olika modellerna.
- **Attitydanalys**: Identifierar positivt, negativt och neutral sentiment från tal- och visual text.
 
När Video Indexer är klar med bearbetning och analys kan du granska, moderera, söka och publicera videoinsikterna.

Både innehållsansvariga och utvecklare kan ha nytta av Video Indexer-tjänsten. Innehållsansvariga kan använda Video Indexer-webbportalen för att använda tjänsten utan att behöva skriva en enda rad kod. Se [Komma igång med Video Indexer-webbplatsen](video-indexer-get-started.md). Utvecklare kan dra nytta av API:er för att bearbeta innehållet i stor skala. Se [Använda Video Indexer REST API](video-indexer-use-apis.md). Tjänsten gör det även möjligt för kunderna att använda widgetar för att publicera videoströmmar och extrahera insikter i sina egna program. Se [Bädda in visuella widgetar i ditt program](video-indexer-embed-widgets.md).

Du kan registrera dig för tjänsten med ditt befintliga AAD-, LinkedIn-, Facebook-, Google- eller MSA-konto. Mer information finns i [Komma igång](video-indexer-get-started.md).

## <a name="scenarios"></a>Scenarier

Nedan visas några scenarier där Video Indexer kan vara användbart

- Sökning – Insikter som extraheras från videon kan användas för att förbättra sökupplevelsen i ett videobibliotek. Indexering av tal och ansikten kan till exempel göra det möjligt att söka efter det ställe i en video där en viss person säger en viss sak eller när två personer har en scen tillsammans. Sökning baserad på sådana insikter från videofilmer kan användas av nyhetsbyråer, skolor och högskolor, media, ägare av underhållningsinnehåll, LOB-program på företag och i allmänhet av branscher som har videobibliotek som användare behöver söka i.
- Skapa innehåll – insikter extraheras från videor och hjälper dig att effektivt skapa innehåll som släpfordon, sociala medieinnehåll, nyheter klipp etc. från befintligt innehåll i arkivet organisation 
- Intäkter – Video Indexer kan öka värdet för videofilmer. Till exempel kan branscher som är beroende av annonsintäkter (som nyhetsmedier, sociala medier osv.) leverera mer relevant reklam genom att använda de extraherade insikterna som ytterligare signaler till annonsservern (att presentera reklam för sportskor är mer relevant under en fotbollsmatch än under en simtävling).
- Användarengagemang – Videoinsikter kan användas för att förbättra användarnas engagemang genom positionering av relevanta videor. Ett exempel är en utbildningsvideo där sfärer förklaras under de första 30 minuterna och pyramider under efterföljande 30 minuter. En student som läser om pyramiderna har mer nytta av videon om den positioneras med start från 30-minutersmarkeringen.

## <a name="next-steps"></a>Nästa steg

Nu är du redo att börja använda Video Indexer. Mer information finns i följande artiklar:

- [Komma igång med Video Indexer-webbplatsen](video-indexer-get-started.md)
- [Bearbeta innehåll med Video Indexer REST API](video-indexer-use-apis.md)
- [Bädda in visuella widgetar i ditt program](video-indexer-embed-widgets.md)
