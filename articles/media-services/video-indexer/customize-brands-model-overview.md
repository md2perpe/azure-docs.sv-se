---
title: Anpassa en varumärken modell i Video Indexer - Azure
titlesuffix: Azure Media Services
description: Den här artikeln ger en översikt över vad är en modell för varumärken i Video Indexer och hur du anpassar den.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: 863dbd9a6044ee33ae39ac9693a7d4f74382b9c7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65799680"
---
# <a name="customize-a-brands-model-in-video-indexer"></a>Anpassa en varumärken modell i Video Indexer

Video Indexer stöder varumärke identifiering från tal- och visual text under indexering och omindexering av video-och ljudinnehåll. Funktionen för identifiering av varumärke identifierar omnämnanden av produkter, tjänster och företag som föreslås av Bings varumärken databas. Till exempel identifierar om Microsoft nämns i en video- eller ljudinnehåll innehåll eller om den visas i visual text i en video, Video Indexer den som ett varumärke i innehållet. Varumärken åt från andra villkor med kontext.

Varumärke identifiering är användbart i en mängd olika affärsscenarier, till exempel innehållet Arkiv och identifiering, sammanhangsberoende reklam, analys av sociala medier, återförsäljnings tävla analys och många fler. Video Indexer varumärke identifiering kan du index varumärke omnämnanden i tal- och visual text med Bings varumärken databasen samt med anpassning genom att skapa en anpassad modell märken för varje Video Indexer-konto. Funktionen för anpassade modellen varumärken kan du välja huruvida Video Indexer identifierar varumärken från Bing varumärken databas, undanta vissa varumärken inte har identifierats (skapa en lista med blockerade användare av i stort sett), och är varumärken som ska vara en del av din modell som inte kanske är i Bings varumärken databasen (skapa en lista för tillåten av i stort sett). Anpassade varumärken modellen som du skapar kommer bara är tillgänglig i det konto som du skapade modellen.

## <a name="out-of-the-box-detection-example"></a>Slut på exempel med identifiering

I den [Microsoft Build 2017 dag 2](https://www.videoindexer.ai/media/ed6ede78ad/) presentation, varumärket ”Microsoft Windows” visas flera gånger. Ibland i avskriften, ibland som visual text och aldrig som ordagrant. Video Indexer identifierar med hög precision att en term är verkligen varumärke baserat på kontexten som täcker över 90k varumärken direkt och uppdateras kontinuerligt. Video Indexer identifierar varumärket från tal vid 02:25, och sedan igen vid 02:40 från visual text, vilket är en del av windows-tangenten.

![Översikt över varumärken](./media/content-model-customization/brands-overview.png)

Tala om windows i samband med konstruktion identifierar inte ordet ”Windows” som ett varumärke och samma för Box, Apple, Fox, osv., baserat på avancerade maskininlärningsalgoritmer som vet hur man disambiguate från kontexten. Varumärke hotidentifieringen i vår språk som stöds. Klicka här för [fullständig Microsoft Build 2017 Day 2 grundtanke video och index](https://www.videoindexer.ai/media/ed6ede78ad/).

Kolla in nästa steg för att göra dina egna varumärken.

## <a name="next-steps"></a>Nästa steg

[Anpassa varumärken modell med API: er](customize-brands-model-with-api.md)

[Anpassa varumärken modell med hjälp av webbplatsen](customize-brands-model-with-website.md)
