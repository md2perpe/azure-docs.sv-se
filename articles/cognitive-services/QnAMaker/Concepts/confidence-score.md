---
title: Förtroendepoäng – QnA Maker
titleSuffix: Azure Cognitive Services
description: Förtroendepoäng anger var säker på att svaret är rätt matchning för den angivna användarfrågan.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 06/17/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: c14c607e4c563bbeeaff02b2c2478cc4b4d96ee5
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/17/2019
ms.locfileid: "67165141"
---
# <a name="confidence-score-of-a-qna-maker-knowledge-base"></a>Förtroendepoäng för en kunskapsbas med QnA Maker
När en användarfråga matchas mot en kunskapsbas, returnerar QnA Maker relevanta svar, tillsammans med ett förtroenderesultat. Det här resultatet indikerar var säker på att svaret är rätt matchning för den angivna användarfrågan. 

Förtroendepoäng är ett tal mellan 0 och 100. Ett resultat på 100 är troligen en exakt matchning, samtidigt som ett resultat på 0 innebär att inget matchande svar hittades. Ju högre alternativet score - ju större tillförlitlighet i svaret. För en viss fråga kan det finnas flera svar returneras. I så fall returneras svar i fallande förtroendepoäng.

I exemplet nedan ser du frågor och svar om enheter, med 2 frågor. 


![Exempel på frågor och svar om par](../media/qnamaker-concepts-confidencescore/ranker-example-qna.png)

Du kan förvänta dig poäng som exempel poäng intervallet nedan – för olika typer av användarfrågor för ovanstående exempel:


![Rankningen poäng intervall](../media/qnamaker-concepts-confidencescore/ranker-score-range.png)


Följande tabell visar vanliga förtroende som är associerade för ett visst poängintervall.

|Poäng värde|Poäng betydelse|Exempelfråga|
|--|--|--|
|90 - 100|En nästan exakt matchning av användarfråga och en KB-fråga|”Mina ändringar inte har uppdaterats i KB efter publicering”|
|> 70|Hög exakthet - vanligtvis ett bra svar som helt besvarar användarens fråga|”Jag har publicerat min Kunskapsbas men uppdateras inte”.|
|50 - 70|Medium förtroende - vanligtvis ett ganska bra svar som ska svara på det huvudsakliga syftet med användarfrågan|”Ska jag spara min uppdateringar innan jag publicera min Kunskapsbas”?|
|30 - 50|Låg förtroende - vanligtvis ett relaterade svar, som delvis besvarar användarens avsikt|”Vad gör spara och träna”?|
|< 30|Mycket låg förtroende - vanligtvis svarar inte användarens fråga, men har vissa ord eller fraser som matchande |”Var kan jag lägga till synonymer min Kunskapsbas”|
|0|Ingen matchning, så att svaret inte returneras.|”Hur mycket tjänsten kostar”|

## <a name="choose-a-score-threshold"></a>Välj ett poäng tröskelvärde
Tabellen ovan visar resultat som förväntas på de flesta KB-artiklar. Men eftersom varje KB är annorlunda och har olika typer av ord, avsikter och mål-rekommenderar vi du testa och välj tröskelvärdet passar som bäst dig. Tröskelvärdet är som standard till 0, så att alla möjliga svar returneras. Den rekommenderade tröskeln som ska fungera för de flesta KB-artiklar, är **50**.

När du väljer tröskeln för ditt, Kom ihåg balans mellan precision och täckning och justera tröskeln för ditt baserat på dina krav.

- Om **Precision** (eller precision) är viktigare för ditt scenario och öka din tröskelvärdet. På så sätt kan varje gång du kommer tillbaka ett svar är en mycket mer CONFIDENT användningsfall och mycket mer troligt svar användare söker efter. I det här fallet kan du få lämnar fler frågor obehandlade. *Till exempel:* om du gör tröskelvärdet **70**, du kan gå miste om vissa tvetydig exempel gilla ”vad är spara och träna”?.

- Om **täckning** (eller återkallande) är fler viktigt – och du vill att besvara så många frågor som möjligt, även om det finns bara en partiell relation till användarens fråga - sedan SÄNKA tröskelvärdet. Det innebär att det kan vara mer fall där svaret svarar inte användarens faktiska frågan, men ger andra relaterade något svar. *Till exempel:* om du gör tröskelvärdet **30**, du kan ge svar för frågor som ”var kan jag ändra min Kunskapsbas”?

> [!NOTE]
> Nyare versioner av QnA Maker är förbättringar av bedömnings logik och kan påverka din tröskelvärdet. När du uppdaterar tjänsten, se till att testa och justera tröskelvärdet om det behövs. Du kan kontrollera QnA Service-version [här](https://www.qnamaker.ai/UserSettings), och se hur du kan få de senaste uppdateringarna [här](../How-To/troubleshooting-runtime.md).

## <a name="set-threshold"></a>Angiven tröskel 

Ange tröskelvärdet resultat som en egenskap för den [GenerateAnswer API JSON-texten](../how-to/metadata-generateanswer-usage.md#generateanswer-request-configuration). Det innebär att du ställa in den för varje anrop till GenerateAnswer. 

I bot framework, ange att resultatet som en del av alternativ för objektet med [ C# ](../how-to/metadata-generateanswer-usage.md?#use-qna-maker-with-a-bot-in-c) eller [Node.js](../how-to/metadata-generateanswer-usage.md?#use-qna-maker-with-a-bot-in-nodejs).

## <a name="improve-confidence-scores"></a>Förbättra förtroende-poäng
För att förbättra förtroendepoäng för ett visst svar till en användarfråga, du kan lägga till användarfrågan kunskapsbasen som en annan fråga på det svaret. Du kan också använda skiftlägeskänsliga [word förändras](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/alterations/replace) att lägga till synonymer i nyckelord i din Kunskapsbas.


## <a name="similar-confidence-scores"></a>Liknande förtroende-poäng
När flera svar har en liknande förtroendepoäng, är det troligt att frågan var för allmän och därför matchade med samma sannolikhet med flera svar. Försök att strukturera dina kunskapsbaser bättre så att varje QnA-enhet har en distinkt avsikt.


## <a name="confidence-score-differences"></a>Förtroende poäng skillnader
Förtroendepoäng av ett svar kan ändras negligibly mellan test och publicerade versionen av kunskapsbasen även om innehållet är samma. Det beror på att innehållet i testet och publicerade kunskapsbasen finns i olika Azure Search-index. När du publicerar en kunskapsbas flyttar frågor och svar innehållet i kunskapsbasen från test-index till ett index för produktion i Azure search. Se hur [publicera](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base) åtgärden fungerar.

Om du har en kunskapsbas i olika regioner, använder en egen Azure Search-index i varje region. Eftersom olika index används kommer poängen inte att exakt samma. 


## <a name="no-match-found"></a>Ingen matchning hittades
När ingen bra matchning hittas av rankningen, förtroendepoäng 0,0 eller ”None” returneras och Standardsvaret är ”bra att hitta någon matchning i KB”. Du kan åsidosätta detta [Standardsvar](#change-default-answer) i koden bot eller program som anropar slutpunkten. Alternativt kan du kan också ange åsidosättning svaret i Azure och detta ändrar standardvärdet för alla kunskapsbaser som distribueras i en viss QnA Maker-tjänsten.

## <a name="change-default-answer"></a>Ändra Standardsvar

1. Gå till den [Azure-portalen](https://portal.azure.com) och navigera till den resursgrupp som representerar QnA Maker-tjänsten som du skapade.

2. Klicka för att öppna den **Apptjänst**.

    ![Få åtkomst till App service för QnA Maker i Azure-portalen](../media/qnamaker-concepts-confidencescore/set-default-response.png)

3. Klicka på **programinställningar** och redigera den **DefaultAnswer** till önskad Standardsvaret. Klicka på **Spara**.

    ![Välj programinställningar och sedan redigera DefaultAnswer för QnA Maker](../media/qnamaker-concepts-confidencescore/change-response.png)

4. Starta om App service

    ![När du ändrar DefaultAnswer, startar du om appservice QnA Maker](../media/qnamaker-faq/qnamaker-appservice-restart.png)


## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Datakällor som stöds](./data-sources-supported.md)

