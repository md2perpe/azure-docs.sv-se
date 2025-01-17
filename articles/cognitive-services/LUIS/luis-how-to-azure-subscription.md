---
title: Prenumerationsnycklar
titleSuffix: Language Understanding - Azure Cognitive Services
description: Du behöver inte skapa prenumerationsnycklar om du vill använda dina kostnadsfria första 1000 endpoint-frågor. Om du får en _slut på kvot för_ fel i form av ett HTTP 403 eller 429, måste du skapa en nyckel och tilldela den till din app.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 06/18/2019
ms.author: diberry
ms.openlocfilehash: 7f82bf5a40df0554d4f98b2d835fcbd69279be43
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67204149"
---
# <a name="using-subscription-keys-with-your-luis-app"></a>Använda prenumerationsnycklar med LUIS-appen

När du börjar använda Språkförståelse (LUIS) behöver du inte skapa prenumerationsnycklar. Du får 1000 endpoint frågor att. 

Testning och prototyper endast kan du använda den kostnadsfria nivån av (F0). Produktionssystem, använda en [betald](https://aka.ms/luis-price-tier) nivå. Använd inte den [redigering nyckeln](luis-concept-keys.md#authoring-key) för slutpunkten frågor i produktion.


<a name="create-luis-service"></a>
<a name="create-language-understanding-endpoint-key-in-the-azure-portal"/>

## <a name="create-prediction-endpoint-runtime-resource-in-the-azure-portal"></a>Skapa körningsresurser för förutsägelse slutpunkten i Azure portal

Du skapar den [förutsägelse slutpunktsresurs](get-started-portal-deploy-app.md#create-the-endpoint-resource) i Azure-portalen. Den här resursen bör endast användas för slutpunkt-förutsägelsefrågor. Använd inte den här resursen för redigering ändringar till appen.

<a name="programmatic-key" ></a>
<a name="authoring-key" ></a>
<a name="endpoint-key" ></a>
<a name="use-endpoint-key-in-query" ></a>
<a name="api-usage-of-ocp-apim-subscription-key" ></a>
<a name="key-limits" ></a>
<a name="key-limit-errors" ></a>
<a name="key-concepts"></a>
<a name="authoring-key"></a>
<a name="create-and-use-an-endpoint-key"></a>
<a name="assign-endpoint-key"></a>
<a name="assign-resource"></a>


## <a name="assign-resource-key-to-luis-app-in-luis-portal"></a>Tilldela Resursnyckeln LUIS-app i LUIS-portalen

Varje gång du skapar en ny resurs för LUIS kan du behöva [tilldelar resursen LUIS-app](get-started-portal-deploy-app.md#assign-the-resource-key-to-the-luis-app-in-the-luis-portal). När den är tilldelad, behöver du inte göra det här steget igen om du inte skapar en ny resurs. Du kan skapa en ny resurs att utöka regionerna som din app eller för ett högre antal förutsägelsefrågor.

<!-- content moved to luis-reference-regions.md, need replacement links-->
<a name="regions-and-keys"></a>
<a name="publishing-to-europe"></a>
<a name="publishing-to-australia"></a>

### <a name="unassign-resource"></a>Ta bort resurs
När du frigör slutpunktsnyckeln bort den inte från Azure. Det är bara avlänkas från LUIS. 

När en slutpunktsnyckeln otilldelade, eller inte har tilldelats appen, ett begärande till slutpunkten URL returnerar ett fel: `401 This application cannot be accessed with the current subscription`. 

### <a name="include-all-predicted-intent-scores"></a>Inkludera alla avsikt förutsägelsepoängen
Den **inkludera alla förutse avsikt poäng** kryssrutan tillåter frågesvaret slutpunkten att inkludera förutsägelse poängen för varje avsikt. 

Den här inställningen kan din chattrobot eller LUIS-anropa program kan besluta programmässiga baserat på poäng för returnerade avsikter. I allmänhet är de främsta två avsikterna mest intressanta. Om den översta poängen är avsiktlig, din chattrobot kan du ställa en fråga för uppföljning som gör en slutgiltig val mellan avsikt ingen och andra flest poäng avsikten ingen. 

Avsikter och deras resultat är också inkluderat endpoint-loggarna. Du kan [exportera](luis-how-to-start-new-app.md#export-app) dessa loggar och analysera poängen. 

```JSON
{
  "query": "book a flight to Cairo",
  "topScoringIntent": {
    "intent": "None",
    "score": 0.5223427
  },
  "intents": [
    {
      "intent": "None",
      "score": 0.5223427
    },
    {
      "intent": "BookFlight",
      "score": 0.372391433
    }
  ],
  "entities": []
}
```

### <a name="enable-bing-spell-checker"></a>Aktivera Bing-stavningskontroll 
I den **slutpunkts-url-inställningar**, **Bing-stavningskontroll** växlingen möjliggör LUIS för att korrigera felstavade ord. innan förutsägelse. Skapa en  **[stavningskontroll i Bing nyckeln](https://azure.microsoft.com/try/cognitive-services/?api=spellcheck-api)** . 

Lägg till den **stavningskontroll = true** Frågeparametern och **bing-stavningskontroll-kontroll-subscription-key = {YOUR_BING_KEY_HERE}** . Ersätt den `{YOUR_BING_KEY_HERE}` med din nyckel för Bing stavningskontroll för installation.

```JSON
{
  "query": "Book a flite to London?",
  "alteredQuery": "Book a flight to London?",
  "topScoringIntent": {
    "intent": "BookFlight",
    "score": 0.780123
  },
  "entities": []
}
```

### <a name="publishing-regions"></a>Publicera regioner

Läs mer om hur du publicerar [regioner](luis-reference-regions.md) inklusive publicering i [Europa](luis-reference-regions.md#publishing-to-europe), och [Australien](luis-reference-regions.md#publishing-to-australia). Publicera regioner skiljer sig från redigering regioner. Skapa en app i regionen redigering som motsvarar regionen publicerar du vill använda för frågan slutpunkten.

## <a name="assign-resource-without-luis-portal"></a>Tilldela resurs utan LUIS-portalen

För automation, till exempel en CI/CD-pipeline, kanske du vill automatisera tilldelningen av en LUIS-resurs till en LUIS-app. För att kunna göra det, måste du utföra följande steg:

1. Skaffa en Azure Resource Manager-token från detta [webbplats](https://resources.azure.com/api/token?plaintext=true). Denna token upphör så Använd den omedelbart. Begäran returnerar en Azure Resource Manager-token.

    ![Azure Resource Manager-token för begäran och ta emot Azure Resource Manager-token](./media/luis-manage-keys/get-arm-token.png)

1. Använd token för att begära LUIS-resurser mellan prenumerationer, från den [hämta LUIS azure konton API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be313cec181ae720aa2b26c), ditt användarkonto har åtkomst till. 

    Det här INLÄGGET API kräver följande inställningar:

    |Huvud|Värde|
    |--|--|
    |`Authorization`|Värdet för `Authorization` är `Bearer {token}`. Observera att token-värde måste föregås av ordet `Bearer` eller ett blanksteg.| 
    |`Ocp-Apim-Subscription-Key`|Din [redigering nyckeln](luis-how-to-account-settings.md).|

    Den här API: T returnerar en matris av JSON-objekt LUIS prenumerationer, inklusive prenumerations-ID, resursgrupp och resursnamn, returneras som kontonamn. Hitta ett objekt i matrisen med LUIS resursen ska tilldelas LUIS-app. 

1. Tilldela token till LUIS-resursen med den [tilldelar en LUIS azure-konton till ett program](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be32228e8473de116325515) API. 

    Det här INLÄGGET API kräver följande inställningar:

    |Typ|Inställning|Värde|
    |--|--|--|
    |Huvud|`Authorization`|Värdet för `Authorization` är `Bearer {token}`. Observera att token-värde måste föregås av ordet `Bearer` eller ett blanksteg.|
    |Huvud|`Ocp-Apim-Subscription-Key`|Din [redigering nyckeln](luis-how-to-account-settings.md).|
    |Huvud|`Content-type`|`application/json`|
    |QueryString|`appid`|LUIS-app-ID. 
    |Innehåll||{”AzureSubscriptionId”: ”ddda2925-af7f-4b05-9ba1-2155c5fe8a8e”<br>”ResourceGroup”: ”resourcegroup-2”,<br>”AccountName”: ”luis-uswest-S0-2”}|

    När detta API lyckas returnerar 201 - skapade status. 

## <a name="change-pricing-tier"></a>Ändra prisnivå

1.  I [Azure](https://portal.azure.com), hitta LUIS-prenumerationen. Välj den LUIS-prenumerationen.
    ![Hitta din LUIS-prenumeration](./media/luis-usage-tiers/find.png)
1.  Välj **prisnivå** om du vill se alla tillgängliga prisnivåer. 
    ![Visa prisnivåer](./media/luis-usage-tiers/subscription.png)
1.  Välj prisnivå nivå och välj **Välj** att spara ändringen. 
    ![Ändra LUIS betalning nivå](./media/luis-usage-tiers/plans.png)
1.  När prissättningsändringen är klar, kontrollerar den nya prisnivån i ett popup-fönster. 
    ![Kontrollera din betalning LUIS-nivå](./media/luis-usage-tiers/updated.png)
1. Kom ihåg att [tilldela den här slutpunktsnyckeln](#assign-endpoint-key) på den **publicera** sidan och använda det på alla endpoint-frågor. 

## <a name="fix-http-status-code-403-and-429"></a>Åtgärda HTTP-statuskod 403 och 429

Du får fel 403 och 429 statuskoder när du överskrider transaktioner per sekund eller transaktioner per månad för din prisnivå.

### <a name="when-you-receive-an-http-403-error-status-code"></a>När du får en statuskod för HTTP 403-fel

När du använder alla dessa kostnadsfria 1000 endpoint frågor eller du överskrider din prisnivå transaktioner månadskvot, får du en statuskod för HTTP 403-fel. 

Om du vill åtgärda det här felet, måste du antingen [ändra din prisnivå](luis-how-to-azure-subscription.md#change-pricing-tier) till en högre nivå eller [skapa en ny resurs](get-started-portal-deploy-app.md#create-the-endpoint-resource) och [tilldela den till din app](get-started-portal-deploy-app.md#assign-the-resource-key-to-the-luis-app-in-the-luis-portal).

Lösningar för det här felet är:

* I den [Azure-portalen](https://portal.azure.com), på ditt språk förstå resurs, på den **resurshantering -> prisnivå**, ändra din prisnivå till en högre nivå för TPS. Du behöver inte göra något i Language Understanding-portalen om resursen har redan tilldelats en Luis-app.
*  Om din användning som överskrider den högsta prisnivån, lägger du till fler Language Understanding-resurser med en belastningsutjämnare framför dem. Den [Språkförståelse behållare](luis-container-howto.md) med Kubernetes eller Docker Compose kan hjälpa dig med detta.

### <a name="when-you-receive-an-http-429-error-status-code"></a>När du får en statuskod för HTTP 429-fel

Den här statuskod returneras när dina transaktioner per sekund överskrider din prisnivå.  

Lösningar är:

* Du kan [öka din prisnivå](#change-pricing-tier), om du inte är på högsta nivå.
* Om din användning som överskrider den högsta prisnivån, lägger du till fler Language Understanding-resurser med en belastningsutjämnare framför dem. Den [Språkförståelse behållare](luis-container-howto.md) med Kubernetes eller Docker Compose kan hjälpa dig med detta.
* Du kan hantera din klientbegäranden för program med en [återförsöksprincip](https://docs.microsoft.com/azure/architecture/best-practices/transient-faults#general-guidelines) du implementera själv när du får den här statuskoden. 

## <a name="viewing-summary-usage"></a>Visa sammanfattning av användning
Du kan visa information om LUIS användning i Azure. Den **översikt** visar senaste sammanfattningsinformation inklusive anrop och fel. Om du gör en LUIS-slutpunktsförfrågan, sedan omedelbart titta på den **översiktssidan**, vänta fem minuter för användningen visas.

![Visa sammanfattning av användning](./media/luis-usage-tiers/overview.png)

## <a name="customizing-usage-charts"></a>Anpassa diagram för användning
Mått ger en mer detaljerad översikt över data.

![Standard-mått](./media/luis-usage-tiers/metrics-default.png)

Du kan konfigurera din måttdiagram för lång tid och Måttyp. 

![Anpassade mått](./media/luis-usage-tiers/metrics-custom.png)

## <a name="total-transactions-threshold-alert"></a>Totalt antal transaktioner tröskelvärde för avisering
Om du vill veta när du har nått ett visst transaktion tröskelvärde, till exempel 10 000 transaktioner, kan du skapa en avisering. 

![Standard-aviseringar](./media/luis-usage-tiers/alert-default.png)

Lägg till en måttavisering för den **Totalt antal anrop** mått för en viss tidsperiod. Lägg till e-postadresserna för alla personer som ska ta emot aviseringen. Lägg till webhooks för alla system som ska ta emot aviseringen. Du kan också köra en logikapp när aviseringen utlöses. 

## <a name="next-steps"></a>Nästa steg

Lär dig hur du använder [versioner](luis-how-to-manage-versions.md) att hantera ändringar i LUIS-appen.
