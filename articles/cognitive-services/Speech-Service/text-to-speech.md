---
title: Text till tal med Azure Speech Services
titleSuffix: Azure Cognitive Services
description: Text till tal från Azure Speech Services är en tjänst som gör det möjligt för dina program, verktyg eller enheter att konvertera text till naturlig människoliknande syntetiskt tal. Välj från standard- och neural röster eller skapa din egen anpassade röst unik för din produkt eller varumärke. 75 + standard röster är tillgängliga i mer än 45 språk och nationella inställningar och 5 neural röster är tillgängliga i 4 språk och nationella inställningar.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/24/2019
ms.author: erhopf
ms.openlocfilehash: da7259585ad66ac9b58131ce834d82e7b3d4bcf2
ms.sourcegitcommit: c63e5031aed4992d5adf45639addcef07c166224
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67466952"
---
# <a name="what-is-text-to-speech"></a>Vad är text till tal?

Text till tal från Azure Speech Services är en tjänst som gör det möjligt för dina program, verktyg eller enheter att konvertera text till naturlig människoliknande syntetiskt tal. Välj från standard- och neural röster eller skapa din egen anpassade röst unik för din produkt eller varumärke. 75 + standard röster är tillgängliga i mer än 45 språk och nationella inställningar och 5 neural röster är tillgängliga i 4 språk och nationella inställningar. En fullständig lista finns i [språk som stöds](language-support.md#text-to-speech).

Text till tal tekniken kan skapare av innehåll kan interagera med användarna på olika sätt. Text till tal kan förbättra tillgängligheten genom att förse användarna med möjlighet att interagera med innehållet hörbart. Om användaren har en visual nedskrivningar learning funktionshinder, eller kräver navigeringsinformation samtidigt, kan text till tal förbättra en befintlig upplevelse. Text till tal är också en värdefull tillägg för röst robotar och virtuella assistenter.


Genom att använda tal syntes Markup Language (SSML), en XML-baserade märkspråk utvecklare som använder tjänsten text till tal kan ange hur indatatext konverteras till syntetiskt tal. Med SSML, kan du justera försäljningsargument uttal, talar hastighet och volym. Mer information finns i [SSML](#speech-synthesis-markup-language-ssml).

### <a name="standard-voices"></a>Standard röster

Standard röster skapas med hjälp av statistiska parametrisk syntes och/eller sammanfogning syntes tekniker. Dessa röster är mycket begripligt och ljud naturligt. Du kan enkelt aktivera dina program att tala i mer än 45 språk, med ett brett utbud av röstalternativ för. Dessa röster ger hög uttal noggrannhet, inklusive stöd för förkortningar, förkortning expanderar, datum/tid-tolkningar, polyphones med mera. Använd standard ton för att förbättra tillgängligheten för dina program och tjänster genom att tillåta användare att interagera med innehållet hörbart.

### <a name="neural-voices"></a>Neural röster

Neural röster använda djupa neurala nätverk för att övervinna gränserna för traditionella text till tal-system i matchar mönster av stress och intonation i talat språk och i synthesizing enheter av tal i en dator röst. Standard text till tal uppdelad prosody i separata språklig analys och akustiska förutsägelse steg som regleras av oberoende modeller, vilket kan resultera i muffled röst syntes. Vår neural funktion gör prosody prognoser och röst syntes samtidigt, vilket innebär att en mer flytande och naturligt standardrösttyper röst.

Neural röster kan användas för att göra interaktion med chattrobotar och virtuella assistenter mer naturligt och engagerande och konverterar digitala texter, till exempel e-böcker till audiobooks och förbättra navigering i bilen system. Med människoliknande naturlig prosody och rensa rörlighet ord minska neural röster avsevärt lyssnande utmattning när du interagerar med AI-system.

Få en neural stöder olika format, till exempel neutrala och trevligare. Exempel: röst Jessa (en-US) kan prata cheerfully, som är optimerad för varm, glada konversationen. Du kan justera voice-utdata som ton försäljningsargument, och snabbare med hjälp av [tal syntes Markup Language](speech-synthesis-markup.md). En fullständig lista över tillgängliga röster Se [språk som stöds](language-support.md#text-to-speech).

Mer information om fördelarna med att få en neural finns [Microsofts nya neural text till tal-tjänst hjälper datorer tala precis som andra](https://azure.microsoft.com/blog/microsoft-s-new-neural-text-to-speech-service-helps-machines-speak-like-people/).

### <a name="custom-voices"></a>Anpassade röster

Röst anpassning kan du skapa en identifierbara, en av en typ ton för ditt varumärke. För att skapa din anpassad rösttyp du gör en studio-registrering och ladda upp de associerade skript som träningsdata. Tjänsten skapar sedan en unik röst modell koll på inspelningen. Du kan använda den här anpassad rösttyp för att syntetisera tal. Mer information finns i [anpassade röster](how-to-customize-voice-font.md).

## <a name="speech-synthesis-markup-language-ssml"></a>Speech Synthesis Markup Language (SSML)

Tal syntes Markup Language (SSML) är en XML-baserade märkspråk som ger utvecklare möjlighet att ange hur indatatext konverteras till syntetiskt tal med hjälp av tjänsten text till tal. Jämfört med oformaterad text, hjälper SSML utvecklare att finjustera motivation uttal, talar hastighet, volym och flera av text till tal-utdata. Normal skiljetecken, till exempel pausar efter en tid eller med rätt intonation vid en mening som slutar med ett frågetecken hanteras automatiskt.

Alla Textinmatningar som skickas till tjänsten text till tal måste vara strukturerade som SSML. Mer information finns i [tal syntes Markup Language](speech-synthesis-markup.md).

### <a name="pricing-note"></a>Priser Obs!

När du använder tjänsten text till tal, debiteras du för varje tecken som konverteras till tal, inklusive skiljetecken. Även om själva SSML dokumentet inte är fakturerbara räknas valfria element som används för att justera hur texten omvandlas till tal, som fonem och försäljningsargument, som fakturerbara tecken. Här är en lista över vad som fakturerbara:

* Texten som skickas till tjänsten text till tal i SSML brödtexten i begäran
* Alla markeringar i textfältet i begärandetexten i SSML-format, förutom för `<speak>` och `<voice>` taggar
* Bokstäver, skiljetecken, blanksteg, tabbar, markup och alla blanksteg
* Varje kodpunkten som definierats i Unicode

Detaljerad information finns i [priser](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

> [!IMPORTANT]
> Varje kinesiska, japanska och koreanska språk tecken räknas som två tecken för fakturering.

## <a name="core-features"></a>Kärnfunktioner

Den här tabellen anger de grundläggande funktionerna för text till tal:

| Användningsfall | SDK | REST |
|----------|-----|------|
| Konvertera text till tal. | Ja | Ja |
| Ladda upp datauppsättningar för röst-anpassning. | Nej | Ja\* |
| Skapa och hantera modeller för röst-teckensnitt. | Nej | Ja\* |
| Skapa och hantera distributioner av röst teckensnitt. | Nej | Ja\* |
| Skapa och hantera röst teckensnitt tester. | Nej | Ja\* |
| Hantera prenumerationer. | Nej | Ja\* |

\* *De här tjänsterna är tillgängliga med hjälp av cris.ai-slutpunkt. Se [Swagger referens](https://westus.cris.ai/swagger/ui/index). Dessa anpassade ton och API: er hantering kan du implementera begränsning att gränserna begäranden till 25 per 5 sekunder medan talsyntes själva API: T implementerar begränsning som tillåter 200 begäranden per sekund som högst. När begränsning inträffar, kommer du att meddelas via meddelandehuvudena.*

## <a name="get-started-with-text-to-speech"></a>Kom igång med text till tal

Vi erbjuder snabbstarter som utformats för att du köra kod i mindre än 10 minuter. Den här tabellen innehåller en lista med text till tal snabbstarter ordnade efter språk.

### <a name="sdk-quickstarts"></a>SDK-snabbstarter

| Snabbstart (SDK) | Plattform | API-referens |
|------------|----------|---------------|
| [C#, .NET Core](quickstart-text-to-speech-dotnetcore.md) | Windows | [Bläddra](https://aka.ms/csspeech/csharpref) |
| [C#, .NET Framework](quickstart-text-to-speech-dotnet-windows.md) | Windows | [Bläddra](https://aka.ms/csspeech/csharpref) |
| [C#, UWP](quickstart-text-to-speech-csharp-uwp.md) | Windows | [Bläddra](https://aka.ms/csspeech/csharpref) |
| [C#, Unity](quickstart-text-to-speech-csharp-unity.md) | Windows, Android | [Bläddra](https://aka.ms/csspeech/csharpref) |
| [C++](quickstart-text-to-speech-cpp-windows.md) | Windows | [Bläddra](https://aka.ms/csspeech/cppref) |
| [C++](quickstart-text-to-speech-cpp-linux.md) | Linux | [Bläddra](https://aka.ms/csspeech/cppref) |

### <a name="rest-quickstarts"></a>REST-snabbstarter

| Snabbstart (REST) | Plattform | API-referens |
|------------|----------|---------------|
| [C#, .NET Core](quickstart-dotnet-text-to-speech.md) | Windows, macOS, Linux | [Bläddra](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| [Node.js](quickstart-nodejs-text-to-speech.md) | Fönstret macOS, Linux | [Bläddra](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| [Python](quickstart-python-text-to-speech.md) | Fönstret macOS, Linux | [Bläddra](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |

## <a name="sample-code"></a>Exempelkod

Exempelkod för text till tal är tillgänglig på GitHub. De här exemplen omfattar text till tal-konvertering i mest populära programmeringsspråk.

* [Text till tal-exempel (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
* [Text till tal-exempel (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS)

## <a name="reference-docs"></a>Referensdokument

* [Speech SDK](speech-sdk-reference.md)
* [Tal enheter SDK](speech-devices-sdk.md)
* [REST-API: Speech-to-text](rest-speech-to-text.md)
* [REST-API: Text till tal](rest-text-to-speech.md)
* [REST-API: Batch transkription och anpassning](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>Nästa steg

* [Skaffa en kostnadsfri Speech Services-prenumeration](get-started.md)
* [Skapa anpassade rösttyper](how-to-customize-voice-font.md)
