---
title: V1 till v2 API-migrering
titleSuffix: Azure Cognitive Services
description: 'Version 1-slutpunkten och redigera Language Understanding API: er är inaktuella. Använd den här guiden för att förstå hur du migrerar till version 2-slutpunkten och redigera API: er.'
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 04/02/2019
ms.author: diberry
ms.openlocfilehash: a4e176552fcbab9d54da696439c7e46750be3944
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65522364"
---
# <a name="api-v1-to-v2-migration-guide-for-luis-apps"></a>API v1 till v2-Migreringsguide för LUIS-appar
Version 1 [endpoint](https://aka.ms/v1-endpoint-api-docs) och [redigering](https://aka.ms/v1-authoring-api-docs) API: er är inaktuella. Använd den här guiden för att förstå hur du migrerar till version 2 [endpoint](https://go.microsoft.com/fwlink/?linkid=2092356) och [redigering](https://go.microsoft.com/fwlink/?linkid=2092087) API: er. 

## <a name="new-azure-regions"></a>Nya Azure-regioner
LUIS har nya [regioner](https://aka.ms/LUIS-regions) för LUIS APIs. LUIS innehåller en annan portal för regionsgrupper. Programmet måste skrivas i samma region som du förväntar dig att fråga. Program migreras inte automatiskt regioner. Du kan exportera en app från en region och sedan importera till en annan för att vara tillgängligt i en ny region.

## <a name="authoring-route-changes"></a>Redigera vägen ändringar
Redigering API vägen ändras från att använda den **prog** väg till med hjälp av den **api** väg.


| version | route |
|--|--|
|1|/Luis/v1.0/**prog**/apps|
|2|/Luis/**api**/v2.0/apps|


## <a name="endpoint-route-changes"></a>Slutpunkten vägen ändringar
Slutpunkten API har nya parametrar för frågesträngen som ett annat svar. Om flaggan utförlig är true, returneras alla avsikter oavsett poäng, i en matris med namnet avsikter, förutom topScoringIntent.

| version | Hämta väg |
|--|--|
|1|/Luis/v1/Application? ID = {appId} & q = {q}|
|2|/ luis/v2.0/apps/{appId}?q={q} [& timezoneOffset] [& utförlig] [& stavningskontroll] [& mellanlagring] [& bing-stavningskontroll-kontroll-subscription-key] [& logg]|


V1-slutpunkten lyckat svar:
```json
{
  "odata.metadata":"https://dialogice.cloudapp.net/odata/$metadata#domain","value":[
    {
      "id":"bccb84ee-4bd6-4460-a340-0595b12db294","q":"turn on the camera","response":"[{\"intent\":\"OpenCamera\",\"score\":0.976928055},{\"intent\":\"None\",\"score\":0.0230718572}]"
    }
  ]
}
```

v2-slutpunkten lyckat svar:
```json
{
  "query": "forward to frank 30 dollars through HSBC",
  "topScoringIntent": {
    "intent": "give",
    "score": 0.3964121
  },
  "entities": [
    {
      "entity": "30",
      "type": "builtin.number",
      "startIndex": 17,
      "endIndex": 18,
      "resolution": {
        "value": "30"
      }
    },
    {
      "entity": "frank",
      "type": "frank",
      "startIndex": 11,
      "endIndex": 15,
      "score": 0.935219169
    },
    {
      "entity": "30 dollars",
      "type": "builtin.currency",
      "startIndex": 17,
      "endIndex": 26,
      "resolution": {
        "unit": "Dollar",
        "value": "30"
      }
    },
    {
      "entity": "hsbc",
      "type": "Bank",
      "startIndex": 36,
      "endIndex": 39,
      "resolution": {
        "values": [
          "BankeName"
        ]
      }
    }
  ]
}
```

## <a name="key-management-no-longer-in-api"></a>Nyckelhantering inte längre i API: et
Prenumeration slutpunktsnyckeln API: er är inaktuella returnerar 410 sidan finns inte.

| version | route |
|--|--|
|1|/Luis/v1.0/prog/subscriptions|
|1|/ luis/v1.0/prog/subscriptions/{subscriptionKey}|

Azure [endpoint nycklar](luis-how-to-azure-subscription.md) genereras i Azure-portalen. Du tilldelar nyckeln till en LUIS-app på den **[publicera](luis-how-to-azure-subscription.md)** sidan. Du behöver inte veta det faktiska nyckelvärdet. LUIS använder prenumerationens namn för att göra tilldelningen. 

## <a name="new-versioning-route"></a>Ny väg för versionshantering
V2-modellen finns nu i en [version](luis-how-to-manage-versions.md). Ett versionsnamn är 10 tecken i flödet. Standardversionen är ”0.1”.

| version | route |
|--|--|
|1|/Luis/v1.0/**prog**/apps/ {appId} / entiteter|
|2|/Luis/**api**/v2.0/apps/{appId}/**versioner**/ {versionId} / entiteter|

## <a name="metadata-renamed"></a>Metadata har bytt namn
Flera API: er som returnerar LUIS metadata har nya namn.

| V1 vägnamn | v2 vägnamn |
|--|--|
|PersonalAssistantApps |assistenter|
|applicationcultures|kulturer|
|applicationdomains|domäner|
|applicationusagescenarios|usagescenarios|


## <a name="sample-renamed-to-suggest"></a>”Exempel” filnamnstillägget ”föreslå”
LUIS föreslår yttranden från befintliga [endpoint yttranden](luis-how-to-review-endpoint-utterances.md) som kan förbättra modellen. I den tidigare versionen det hette **exempel**. I den nya versionen, namnet ändras från exemplet för att **föreslår**. Detta kallas **[granska endpoint yttranden](luis-how-to-review-endpoint-utterances.md)** på LUIS-webbplatsen.

| version | route |
|--|--|
|1|/Luis/v1.0/**prog**/apps/ {appId} /entities/ {entityId} /**exempel**|
|1|/Luis/v1.0/**prog**/apps/ {appId} /intents/ {intentId} /**exempel**|
|2|/Luis/**api**/v2.0/apps/{appId}/**versioner**/ {versionId} /entities/ {entityId} /**föreslå**|
|2|/Luis/**api**/v2.0/apps/{appId}/**versioner**/ {versionId} /intents/ {intentId} /**föreslå**|


## <a name="create-app-from-prebuilt-domains"></a>Skapa app från fördefinierade domäner
[Fördefinierade domäner](luis-how-to-use-prebuilt-domains.md) ger en fördefinierad domänmodell. Fördefinierade domäner kan du snabbt utveckla ditt LUIS-program för gemensamma domäner. Detta API kan du skapa en ny app baserat på fördefinierade domän. Svaret är ny appID.

|v2-väg|verb|
|--|--|
|/Luis/API/v2.0/Apps/customprebuiltdomains  |Get, post|
|/ luis/api/v2.0/apps/customprebuiltdomains/{culture}  |Hämta|

## <a name="importing-1x-app-into-2x"></a>Importera 1.x-app till 2.x
Exporterade 1.x appens JSON har vissa områden som du behöver ändra innan du importerar till [LUIS] [ LUIS] 2.0. 

### <a name="prebuilt-entities"></a>Fördefinierade entiteter 
Den [förskapade entiteter](luis-prebuilt-entities.md) har ändrats. Kontrollera att du använder V2 förskapade entiteter. Detta inkluderar med [datetimeV2](luis-reference-prebuilt-datetimev2.md), i stället för datum/tid. 

### <a name="actions"></a>Åtgärder
Egenskapen åtgärder är inte längre giltig. Det bör vara en tom 

### <a name="labeled-utterances"></a>Taggade yttranden
V1 tillåtna taggade yttranden till innehåller mellanslag i början eller slutet av ordet eller frasen. Ta bort blankstegen. 

## <a name="common-reasons-for-http-response-status-codes"></a>Vanliga orsaker till statuskoder för HTTP-svar
Se [LUIS-API-svarskoder](luis-reference-response-codes.md).

## <a name="next-steps"></a>Nästa steg

Använd v2 API-dokumentationen för att uppdatera befintliga REST-anrop till LUIS [endpoint](https://go.microsoft.com/fwlink/?linkid=2092356) och [redigering](https://go.microsoft.com/fwlink/?linkid=2092087) API: er. 

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions
