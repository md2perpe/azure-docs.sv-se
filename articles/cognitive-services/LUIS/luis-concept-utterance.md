---
title: Bra exempel yttranden
titleSuffix: Language Understanding - Azure Cognitive Services
description: Yttranden indata från användaren som appen behöver att tolka. Samla in fraser som du tror att användare ska ange. Inkludera yttranden som betyda samma sak, men är konstruerade på olika sätt i word längd och word placering.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: fdf5508475d868ccb8c271daaac7449d3c940301
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65073158"
---
# <a name="understand-what-good-utterances-are-for-your-luis-app"></a>Förstå vad bra yttranden är avsedda för LUIS-app

**Yttranden** indata från användaren som appen behöver att tolka. För att träna LUIS för att extrahera avsikter och entiteter från dem, är det viktigt att samla in en mängd olika exempel yttranden för varje avsikt. Aktiv inlärning eller processen med att fortsätta att utbilda i nya yttranden är mycket viktigt att datorn lärt dig intelligens som innehåller LUIS.

Samla in yttranden som du tror att användare ska ange. Inkludera yttranden som betyda samma sak men skapas i en mängd olika sätt:

* Uttryck längd - kort, medel och lång för ditt klientprogram
* Längd för ord och fraser 
* Placering av Word - entiteten i början, i mitten och slutet av uttryck
* Grammatik 
* Pluralization
* Ordstamsigenkänning
* Substantiv och verb val
* Skiljetecken - bra olika med rätt, fel och ingen grammatik

## <a name="how-to-choose-varied-utterances"></a>Hur du väljer olika yttranden

När du först komma igång genom [lägger du till exempel yttranden](luis-how-to-add-example-utterances.md) till LUIS-modellen, här finns vissa principer att ha i åtanke.

### <a name="utterances-arent-always-well-formed"></a>Yttranden stöddata inte alltid

Det kan vara en mening, till exempel ”boka en biljett till Paris för mig” eller ett fragment i en mening, t.ex. ”bokning” eller ”Paris flygning”.  Användare kan du ofta göra stavfel. När du planerar din app bör tänka på om du använder [stavningskontroll i Bing](luis-tutorial-bing-spellcheck.md) att korrigera indata från användaren innan det skickas till LUIS. 

Om du inte stavningskontroll kontroll användaren yttranden, bör du tränar LUIS på yttranden som innehåller Skriv- och stavfel.

### <a name="use-the-representative-language-of-the-user"></a>Använd språket som är representativ för användaren

När du väljer yttranden, Tänk på att vad du tycker är en vanlig term eller frasen inte kanske korrekt för normal användare av ditt klientprogram. De kan inte ha domän upplevelse. Var försiktig när du använder termer eller fraser som en användare bara skulle säger om de vore en expert.

### <a name="choose-varied-terminology-as-well-as-phrasing"></a>Välj olika terminologi samt formulering

Du kommer märka att även om du gör för att skapa olika mening mönster kan du fortfarande upprepas vissa ordförråd.

Utföra dessa exempel yttranden:

|Exempel på yttranden|
|--|
|hur får jag en dator?|
|Var hittar jag en dator?|
|Jag vill ha en dator, hur går jag om den?|
|När kan jag har en dator?| 

Här, core termen inte är ”dator” olika. Använda alternativ som stationär dator, bärbar dator, arbetsstation eller även bara datorn. LUIS härleder smart synonymer från kontext, men när du skapar uttryck för träning, är det fortfarande är bättre att variera dem.

## <a name="example-utterances-in-each-intent"></a>Exempel yttranden i varje avsikt

Varje avsikt måste ha exempel yttranden, minst 15. Om du har ett intent som inte har yttranden exempel kan du inte träna LUIS. Om du har en syftet med en eller mycket få exempel yttranden kommer LUIS inte korrekt förutse avsikten. 

## <a name="add-small-groups-of-15-utterances-for-each-authoring-iteration"></a>Lägg till små grupper med 15 yttranden för varje redigering upprepning

Lägg inte till ett stort antal yttranden i varje iteration av modellen. Lägg till yttranden i mängder 15. [Träna](luis-how-to-train.md), [publicera](luis-how-to-publish-app.md), och [testa](luis-interactive-test.md) igen.  

LUIS skapar effektiva modeller med yttranden som väljs noggrant installationskommandot LUIS-modellen. Att lägga till för många yttranden inte värdefulla eftersom förvirring.  

Är det bättre att börja med ett par yttranden sedan [granska endpoint yttranden](luis-how-to-review-endpoint-utterances.md) för rätt avsikt extrahering av förutsägelser och entiteten.

## <a name="utterance-normalization"></a>Uttryck normalisering

Uttryck normalisering är processen att ignorera effekterna av interpunktion och diakritiska tecken under träning och förutsägelser.

## <a name="utterance-normalization-for-diacritics-and-punctuation"></a>Uttryck normalisering för diakritiska tecken och skiljetecken

Uttryck normalisering definieras när du skapar eller importera appen eftersom den är en inställning i JSON-filen för appen. Uttryck-normaliseringsinställningarna är inaktiverade som standard. 

Diakritiska tecken är varumärken eller kännetecken i texten, till exempel: 

```
İ ı Ş Ğ ş ğ ö ü
```

Om din app aktiverar normalisering, bedömer i den **Test** fönstret, batch tester och slutpunkten frågor kommer att ändras för alla yttranden med diakritiska tecken eller skiljetecken.

Aktivera uttryck normalisering för diakritiska tecken eller skiljetecken i LUIS JSON appfilen i den `settings` parametern.

```JSON
"settings": [
    {"name": "NormalizePunctuation", "value": "true"},
    {"name": "NormalizeDiacritics", "value": "true"}
] 
```

Normaliserar **skiljetecken** innebär att innan dina modeller utbilda och innan slutpunkten frågor hämta förutse skiljetecken tas bort från yttranden. 

Normaliserar **diakritiska tecken** ersätter tecknen med dem i uttryck med vanliga tecken. Till exempel: `Je parle français` blir `Je parle francais`. 

Normalisering innebär inte att du kommer inte se skiljetecken och diakritiska tecken i ditt exempel yttranden eller förutsägelse svar bara att de kommer att ignoreras under träning och förutsägelser.


### <a name="punctuation-marks"></a>Skiljetecken

Om skiljetecken inte är normaliserat LUIS går inte att Ignorera skiljetecken, som standard, eftersom alla klientprogram kan placera betydelse på dessa märken. Kontrollera att ditt exempel yttranden använder både interpunktion och inga skiljetecken för båda formaten för att returnera samma relativa poängen. 

Om skiljetecken har ingen specifik betydelse i klientprogrammet, bör du [Ignorerar skiljetecken](#utterance-normalization) av normaliserar skiljetecken. 

### <a name="ignoring-words-and-punctuation"></a>Ignorerar ord och skiljetecken

Om du vill ignorera vissa specifika ord eller skiljetecken i mönster kan använda en [mönstret](luis-concept-patterns.md#pattern-syntax) med den _Ignorera_ syntaxen för hakparenteser, `[]`. 

## <a name="training-utterances"></a>Utbildning yttranden

Utbildning som är allmänt icke-deterministisk: uttryck förutsägelsen kan variera något mellan versioner eller appar. Du kan ta bort icke-deterministisk utbildning genom att uppdatera den [inställningarna version](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) API med den `UseAllTrainingData` namn/värde-par att använda alla träningsdata.

## <a name="testing-utterances"></a>Testa yttranden 

Utvecklare bör börja testa sina LUIS-program med verklig trafik genom att skicka yttranden till den [förutsägelse endpoint](luis-how-to-azure-subscription.md) URL: en. Dessa uttryck används för att förbättra prestandan för avsikter och entiteter med [granska yttranden](luis-how-to-review-endpoint-utterances.md). Tester som skickades med webbplatsen LUIS testning fönstret skickas inte via slutpunkten och därför bidrar inte till aktiv inlärning. 

## <a name="review-utterances"></a>Granska yttranden

När din modell är tränade publicerade och mottagande [endpoint](luis-glossary.md#endpoint) frågor, [granska talade](luis-how-to-review-endpoint-utterances.md) föreslås av LUIS. LUIS väljer endpoint yttranden som har låg poäng för avsikt eller entitet. 

## <a name="best-practices"></a>Bästa praxis

Granska [bästa praxis](luis-concept-best-practices.md) och använda dem som en del av din vanliga redigering cykel.

## <a name="next-steps"></a>Nästa steg
Se [lägger du till exempel yttranden](luis-how-to-add-example-utterances.md) information om hur du tränar en LUIS-app för att förstå användaren yttranden.

