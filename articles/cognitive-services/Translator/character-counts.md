---
title: Tecken antal - API för textöversättning
titlesuffix: Azure Cognitive Services
description: Hur räknas Translator Text API tecken.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: cfd5823009b66b6b525c7add1fb56953d3c1a507
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67445266"
---
# <a name="how-the-translator-text-api-counts-characters"></a>Hur Translator Text API räknar tecken

Translator Text API räknas varje Unicode-kodpunkt av indatatexten som ett tecken. Varje översättning av en text till ett språk räknas som en separat översättning, även om en begäran har gjorts i en enda API anrop översattes till flera språk. Längden på svaret spelar ingen roll.

Vad räknas är:

* Texten som skickas till Translator Text API i brödtexten i begäran
   * `Text` När du använder metoderna Översätt, Transliterate och ordlista sökning
   * `Text` och `Translation` när du använder metoden ordlista exempel
* Alla markeringar: HTML-, XML-taggar, o.s.v. i textfältet i begärandetexten. JSON-format som används för att skapa begäran (till exempel ”texten”:) räknas inte.
* En bokstav
* Skiljetecken
* Ett blanksteg, fliken, markup och alla slags tecken för tomt utrymme
* Varje kodpunkten som definierats i Unicode
* En upprepad översättning, även om du tidigare har översatt samma text

För skript utifrån ideogram, till exempel kinesiska och japanska Kanji, kommer Translator Text API fortfarande räkna antalet Unicode-koderna, ett tecken per ideogram. Undantag: Unicode surrogat antal som två tecken.

Antalet begäranden, ord, byte eller meningar är inte relevant i antal tecken.

Anrop till metoder för identifiering och BreakSentence räknas inte tecknet förbrukningen. Men förväntar vi sig att anropen för att identifiera och BreakSentence metoder är i en rimlig proportion till användningen av andra funktioner som räknas. Om antalet identifiera eller BreakSentence anrop du gör överskrider antalet andra räknade metoder av 100 gånger Microsoft förbehåller sig att begränsa användningen av identifierings- och BreakSentence metoder.


Mer information om antalet tecken som tillhör den [Microsoft Translator FAQ](https://www.microsoft.com/en-us/translator/faq.aspx).
