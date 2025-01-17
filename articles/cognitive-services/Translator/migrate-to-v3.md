---
title: Migrera till V3 - API för textöversättning
titlesuffix: Azure Cognitive Services
description: Lär dig hur du migrerar från V2 till V3 av Translator Text API.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: swmachan
ms.openlocfilehash: 8a2530a4eb4365a022ab11279b344a5d2852430b
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67448300"
---
# <a name="translator-text-api-v2-to-v3-migration"></a>Translator Text API V2 till V3-migrering

> [!NOTE]
> V2 upphörde den 30 April 2018. Migrera dina program till V3 för att kunna dra nytta av nya funktioner som är tillgängliga i V3.
> 
> Microsoft Translator Hub dras in den 17 maj 2019. [Visa information om viktiga migrering och datum](https://www.microsoft.com/translator/business/hub/).  

Microsoft Translator-teamet har släppt Version 3 (V3) av Translator Text API. Den här versionen innehåller nya funktioner, föråldrad metoder och ett nytt format för att skicka till och ta emot data från tjänsten Microsoft Translator. Det här dokumentet innehåller information för att ändra programmen kan använda V3. 

Slutet av det här dokumentet innehåller användbara länkar du vill veta mer.

## <a name="summary-of-features"></a>Sammanfattning av funktioner

* Ingen spårning - i V3 Nej-spårning gäller för alla prisnivåer i Azure-portalen. Den här funktionen innebär att ingen text som skickas till V3-API kommer att sparas av Microsoft.
* JSON - XML ersätts av JSON. Alla data skickas till tjänsten och tas emot från tjänsten är i JSON-format.
* Flera språk för målet i en enskild begäran – The översätta metoden accepterar flera ”till”-språk för översättning i en enskild begäran. En enskild begäran kan exempelvis vara ”från” engelska och att tyska, spanska och japanska eller någon annan grupp av språk.
* Tvåspråkig ordlista – en tvåspråkig ordlista metod har lagts till API: et. Den här metoden innehåller 'lookup ”och” exempel ”.
* Transkribera – en transliterate metod har lagts till API: et. Den här metoden konverterar ord och meningar i ett skript (t.ex. Arabiska) till ett annat skript (t.ex. Latinsk).
* Språk – en ny metod som ”språk” levererar språkinformation, i JSON-format för användning med ”översätta', '-ordlista' och 'transkribera' metoder.
* Ny i Översätt - nya funktioner har lagts till metoden ”översätta” att ha stöd för vissa funktioner som fanns i V2-API som separata metoder. Ett exempel är TranslateArray.
* Tala metoden - Text till tal-funktioner stöds inte längre i Microsoft Translator-API. Text till tal-funktionen är tillgänglig i [Microsoft Speech Service](https://docs.microsoft.com/azure/cognitive-services/speech-service/text-to-speech).

Följande lista över V2 och V3 metoder identifierar V3-metoder och API: er som tillhandahåller de funktioner som medföljde V2.

| V2 API-metoden   | V3 API-kompatibilitet |
|:----------- |:-------------|
| `Translate`     | [Översätta](reference/v3-0-translate.md)          |
| `TranslateArray`      | [Översätta](reference/v3-0-translate.md)        |
| `GetLanguageNames`      | [Språk](reference/v3-0-languages.md)         |
| `GetLanguagesForTranslate`     | [Språk](reference/v3-0-languages.md)       |
| `GetLanguagesForSpeak`      | [Microsoft Speech Service](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#text-to-speech)         |
| `Speak`     | [Microsoft Speech Service](https://docs.microsoft.com/azure/cognitive-services/speech-service/text-to-speech)          |
| `Detect`     | [Identifiera](reference/v3-0-detect.md)         |
| `DetectArray`     | [Identifiera](reference/v3-0-detect.md)         |
| `AddTranslation`     | Funktionen stöds inte längre       |
| `AddTranslationArray`    | Funktionen stöds inte längre          |
| `BreakSentences`      | [BreakSentence](reference/v3-0-break-sentence.md)       |
| `GetTranslations`      | Funktionen stöds inte längre         |
| `GetTranslationsArray`      | Funktionen stöds inte längre         |

## <a name="move-to-json-format"></a>Flytta till JSON-format

Microsoft Translator Text Translation V2 accepterat och returnerade data i XML-format. I V3 är alla data som skickas och tas emot med hjälp av API: et i JSON-format. XML-kommer inte längre godkända eller returneras i V3.

Den här ändringen påverkar flera aspekter av ett program som skrivits för V2 Text Translation-API. Som exempel: Språk-API: et returnerar språkinformation om för textöversättning, transkriberingsspråk och två ordlista-metoder. Du kan begära språkinformation för alla för alla metoder i ett anrop eller begär dem individuellt.

Metoden språk kräver inte autentisering; Du kan se alla språkinformation för V3 i JSON genom att klicka på följande länk:

[https://api.cognitive.microsofttranslator.com/languages?api-version=3.0&scope=translation, ordlista, transkriberingsspråk](https://api.cognitive.microsofttranslator.com/languages?api-version=3.0&scope=translation,dictionary,transliteration)

## <a name="authentication-key"></a>Autentiseringsnyckel

Den autentiseringsnyckel som du använder för V2 kommer accepteras för V3. Du behöver inte skaffa en ny prenumeration. Du kommer att kunna blanda V2 och V3 i dina appar under yearlong migreringen, vilket gör det enklare för dig att släppa nya versioner medan du fortfarande migrerar från V2-XML till V3-JSON.

## <a name="pricing-model"></a>Prissättningsmodell

Microsoft Translator V3 debiteras på samma sätt som V2 har prissätts; per tecken inklusive blanksteg. De nya funktionerna i V3 gör några ändringar i vilka tecken räknas för fakturering.

| V3-metod   | Tecken som räknas för fakturering |
|:----------- |:-------------|
| `Languages`     | Inga tecken som har skickats, ingen räknas, utan kostnad.          |
| `Translate`     | Antal baseras på hur många tecken har skickats för översättning och hur många språk tecknen översätts till. 50 tecken har skickats och 5 språk som begärs blir 50 x 5.           |
| `Transliterate`     | Antalet tecken som skickats för transkriberingsspråk räknas.         |
| `Dictionary lookup & example`     | Antalet tecken som skickats för ordlista Sök- och räknas.         |
| `BreakSentence`     | Ingen extra kostnad.       |
| `Detect`     | Ingen extra kostnad.      |

## <a name="v3-end-points"></a>V3-slutpunkter

Global

* api.cognitive.microsofttranslator.com

## <a name="v3-api-text-translations-methods"></a>V3 API text översättningar-metoder

[`Languages`](reference/v3-0-languages.md)

[`Translate`](reference/v3-0-translate.md)

[`Transliterate`](reference/v3-0-transliterate.md)

[`BreakSentence`](reference/v3-0-break-sentence.md)

[`Detect`](reference/v3-0-detect.md)

[`Dictionary/lookup`](reference/v3-0-dictionary-lookup.md)

[`Dictionary/example`](reference/v3-0-dictionary-examples.md)

## <a name="compatibility-and-customization"></a>Kompatibilitet och anpassning

> [!NOTE]
> 
> Microsoft Translator Hub dras in den 17 maj 2019. [Visa information om viktiga migrering och datum](https://www.microsoft.com/translator/business/hub/).   

Microsoft Translator V3 använder neural maskinöversättning som standard. Det kan därför inte användas med Microsoft Translator Hub. Translator-hubben har endast stöd för äldre statistiska maskinöversättning. Anpassning av neural översättning är nu tillgänglig med hjälp av anpassade Translator. [Läs mer om hur du anpassar neural maskinöversättning](custom-translator/overview.md)

Neural översättning med text API V3 stöder inte användning av standard kategorier (SMT, tal, tekniska, generalnn).

| |Slutpunkt|    Dataskyddsförordningen för Processor|  Använd Translator Hub| Använda anpassade Translator (förhandsversion)|
|:-----|:-----|:-----|:-----|:-----|
|Translator Text API-Version 2| api.microsofttranslator.com|    Nej  |Ja    |Nej|
|Translator Text API-Version 3| api.cognitive.microsofttranslator.com|  Ja|    Nej| Ja|

**Translator Text API-Version 3**
* Är allmänt tillgängliga och stöds fullt ut.
* Dataskyddsförordningen som en processor och uppfyller alla krav för ISO 20001 och 20018 samt SOC 3-certifiering. 
* Kan du anropa neuralt nätverk översättningssystem som du har anpassat med anpassade Translator (förhandsversion), den nya Translator NMT anpassning av funktionen. 
* Ger inte åtkomst till anpassade översättningssystem som skapats med hjälp av Microsoft Translator Hub.

Du använder Version 3 av API för textöversättning om du använder api.cognitive.microsofttranslator.com-slutpunkten.

**Translator Text API-Version 2**
* Inte uppfyller alla ISO 20001,20018 och certifieringskrav för SOC 3. 
* Tillåter inte att anropa neuralt nätverk översättningssystem som du har anpassat med funktionen Translator anpassning.
* Ger åtkomst till anpassade översättningssystem som skapats med hjälp av Microsoft Translator Hub.
* Du använder Version 2 av API för textöversättning om du använder api.microsofttranslator.com-slutpunkten.

Ingen version av Translator API skapar en post för dina översättningar. Dina översättningar delas aldrig med vem som helst. Mer information om den [Translator Nej-spårning](https://www.aka.ms/NoTrace) webbsidan.

## <a name="links"></a>Länkar

* [Sekretesspolicy för Microsoft](https://privacy.microsoft.com/privacystatement)
* [Juridisk Information om Microsoft Azure](https://azure.microsoft.com/support/legal)
* [Villkoren för Online Services](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Visa V3.0 dokumentation](reference/v3-0-reference.md)
