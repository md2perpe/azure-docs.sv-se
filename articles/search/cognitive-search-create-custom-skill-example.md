---
title: 'Exempel: Skapa en anpassad färdighet i kognitiv sökning pipeline – Azure Search'
description: Visar hur du använder Text översätta API i anpassade färdigheter som är mappad till en kognitiv sökning indexering pipeline i Azure Search.
manager: pablocas
author: luiscabrer
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: efa85491f4b183a044ec5d9e5e6e3d11eebedbe3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66428431"
---
# <a name="example-create-a-custom-skill-using-the-text-translate-api"></a>Exempel: Skapa en anpassad kompetens med hjälp av översätta Text-API

Lär dig hur du skapar ett web API anpassade färdighet i det här exemplet. Kompetensen ska ta emot SMS på valfritt språk och översätts till engelska. I exemplet används en [Azure Function](https://azure.microsoft.com/services/functions/) du omsluter den [översätta Text API](https://azure.microsoft.com/services/cognitive-services/translator-text-api/) så att den implementerar gränssnittet anpassade färdigheter.

## <a name="prerequisites"></a>Nödvändiga komponenter

+ Läs mer om [anpassade färdighet gränssnittet](cognitive-search-custom-skill-interface.md) artikel om du inte är bekant med indata/utdata-gränssnitt som en anpassad färdighet bör implementera.

+ [Registrera dig för Translator Text API](../cognitive-services/translator/translator-text-how-to-signup.md), och få en API-nyckel för att använda den.

+ Installera [Visual Studio 2019](https://www.visualstudio.com/vs/) eller senare, inklusive arbetsbelastningen Azure development.

## <a name="create-an-azure-function"></a>Skapa en Azure-funktion

Även om det här exemplet använder en Azure-funktion som värd för ett webb-API kan det inte krävs.  Förutsatt att du uppfyller de [gränssnitt för en kognitiva kunskaper](cognitive-search-custom-skill-interface.md), den metod som du använder är utan betydelse. Azure Functions kan dock göra det enkelt att skapa en anpassad kunskap.

### <a name="create-a-function-app"></a>Skapa en funktionsapp

1. I Visual Studio väljer **New** > **projekt** från menyn Arkiv.

1. I dialogrutan Nytt projekt väljer **installerad**, expandera **Visual C#**  > **molnet**väljer **Azure Functions**, ange ett Namn för projektet och välj **OK**. Funktionsappens namn måste vara ett giltigt C#-namnområde. Du kan inte använda understreck, bindestreck eller andra icke-alfanumeriska tecken.

1. Välj **v2 för Azure Functions (.NET Core)** . Du kan också göra det med version 1, men den kod som skrivs nedan baseras på v2-mall.

1. Välj den typ som ska vara **HTTP-utlösare**

1. För Storage-konto kan du välja **ingen**, som du inte behöver all lagring för den här funktionen.

1. Välj **OK** skapa funktionen projekt och HTTP-utlöst funktion.

### <a name="modify-the-code-to-call-the-translate-cognitive-service"></a>Ändra koden för att anropa Cognitive Service översätta

Visual Studio skapar ett projekt och i det en klass som innehåller formaterad exempelkod för den valda typen. Attributet *FunctionName* i metoden anger namnet på funktionen. Attributet *HttpTrigger* anger att funktionen utlöses av en HTTP-förfrågan.

Nu kan byta ut all innehållet i filen *Function1.cs* med följande kod:

```csharp
using System;
using System.Net.Http;
using System.Collections.Generic;
using System.Threading.Tasks;
using System.IO;
using System.Text;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Azure.WebJobs.Host;
using Newtonsoft.Json;

namespace TranslateFunction
{
    // This function will simply translate messages sent to it.
    public static class Function1
    {
        static string path = "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0";

        // NOTE: Replace this example key with a valid subscription key.
        static string key = "<enter your api key here>";

        #region classes used to serialize the response
        private class WebApiResponseError
        {
            public string message { get; set; }
        }

        private class WebApiResponseWarning
        {
            public string message { get; set; }
        }

        private class WebApiResponseRecord
        {
            public string recordId { get; set; }
            public Dictionary<string, object> data { get; set; }
            public List<WebApiResponseError> errors { get; set; }
            public List<WebApiResponseWarning> warnings { get; set; }
        }

        private class WebApiEnricherResponse
        {
            public List<WebApiResponseRecord> values { get; set; }
        }
        #endregion

        [FunctionName("Translate")]
        public static IActionResult Run(
            [HttpTrigger(AuthorizationLevel.Function, "post", Route = null)]HttpRequest req,
            TraceWriter log)
        {
            log.Info("C# HTTP trigger function processed a request.");

            string recordId = null;
            string originalText = null;
            string toLanguage = null;
            string translatedText = null;

            string requestBody = new StreamReader(req.Body).ReadToEnd();
            dynamic data = JsonConvert.DeserializeObject(requestBody);

            // Validation
            if (data?.values == null)
            {
                return new BadRequestObjectResult(" Could not find values array");
            }
            if (data?.values.HasValues == false || data?.values.First.HasValues == false)
            {
                // It could not find a record, then return empty values array.
                return new BadRequestObjectResult(" Could not find valid records in values array");
            }

            recordId = data?.values?.First?.recordId?.Value as string;
            originalText = data?.values?.First?.data?.text?.Value as string;
            toLanguage = data?.values?.First?.data?.language?.Value as string;

            if (recordId == null)
            {
                return new BadRequestObjectResult("recordId cannot be null");
            }

            translatedText = TranslateText(originalText, toLanguage).Result;
        
            // Put together response.
            WebApiResponseRecord responseRecord = new WebApiResponseRecord();
            responseRecord.data = new Dictionary<string, object>();
            responseRecord.recordId = recordId;
            responseRecord.data.Add("text", translatedText);

            WebApiEnricherResponse response = new WebApiEnricherResponse();
            response.values = new List<WebApiResponseRecord>();
            response.values.Add(responseRecord);

            return (ActionResult)new OkObjectResult(response);
        }


        /// <summary>
        /// Use Cognitive Service to translate text from one language to another.
        /// </summary>
        /// <param name="originalText">The text to translate.</param>
        /// <param name="toLanguage">The language you want to translate to.</param>
        /// <returns>Asynchronous task that returns the translated text. </returns>
        async static Task<string> TranslateText(string originalText, string toLanguage)
        {
            System.Object[] body = new System.Object[] { new { Text = originalText } };
            var requestBody = JsonConvert.SerializeObject(body);

            var uri = $"{path}&to={toLanguage}";

            string result = "";

            using (var client = new HttpClient())
            using (var request = new HttpRequestMessage())
            {
                request.Method = HttpMethod.Post;
                request.RequestUri = new Uri(uri);
                request.Content = new StringContent(requestBody, Encoding.UTF8, "application/json");
                request.Headers.Add("Ocp-Apim-Subscription-Key", key);

                var response = await client.SendAsync(request);
                var responseBody = await response.Content.ReadAsStringAsync();

                dynamic data = JsonConvert.DeserializeObject(responseBody);
                result = data?.First?.translations?.First?.text?.Value as string;

            }
            return result;
        }
    }
}
```

Kontrollera att du anger din egen *nyckel* värde i den *TranslateText* metod baserat på den nyckel som du fick när du registrerar dig för översätta Text-API.

Det här exemplet är en enkel enricher som fungerar bara på en post i taget. Detta blir viktigt senare när du ställer in batchstorlek för gruppens kunskaper avgör.

## <a name="test-the-function-from-visual-studio"></a>Testa funktionen från Visual Studio

Tryck på **F5** att köra de program och testa funktionen beteenden. I det här fallet använder vi funktionen nedan för att översätta en text på spanska till engelska. Använda Postman eller Fiddler för att utfärda en uppmaning som liknar den nedan:

```http
POST https://localhost:7071/api/Translate
```
### <a name="request-body"></a>Begärandetext
```json
{
   "values": [
        {
            "recordId": "a1",
            "data":
            {
               "text":  "Este es un contrato en Inglés",
               "language": "en"
            }
        }
   ]
}
```
### <a name="response"></a>Svar
Du bör se ett svar som liknar följande exempel:

```json
{
    "values": [
        {
            "recordId": "a1",
            "data": {
                "text": "This is a contract in English"
            },
            "errors": null,
            "warnings": null
        }
    ]
}
```

## <a name="publish-the-function-to-azure"></a>Publicera funktionen till Azure

När du är nöjd med funktionen beteendet kan publicera du den.

1. I **Solution Explorer** högerklickar du på projektet och väljer **Publicera**. Välj **skapa en ny** > **publicera**.

1. Om du inte redan anslutit Visual Studio till ditt Azure-konto, väljer **Lägg till ett konto...**

1. Följ den anvisningarna på skärmen. Du uppmanas att ange Azure-konto, resursgruppens namn, värdplanen och storage-konto som du vill använda. Du kan skapa en ny resursgrupp, en ny värdplan och ett lagringskonto om du inte redan har dessa. När du är klar väljer **skapa**

1. När distributionen är klar ser du webbplatsens URL. Det är adressen till funktionsappen i Azure. 

1. I den [Azure-portalen](https://portal.azure.com), navigera till resursgruppen och leta efter funktionen översätta du publicerat. Under den **hantera** avsnittet bör du se Värdnycklar. Välj den **kopia** ikonen för den *standard* värdnyckel.  

## <a name="test-the-function-in-azure"></a>Testa funktionen i Azure

Nu när du har standard-värdnyckeln kan du testa din funktion på följande sätt:

```http
POST https://translatecogsrch.azurewebsites.net/api/Translate?code=[enter default host key here]
```
### <a name="request-body"></a>Begärandetext
```json
{
   "values": [
        {
            "recordId": "a1",
            "data":
            {
               "text":  "Este es un contrato en Inglés",
               "language": "en"
            }
        }
   ]
}
```

Det här exemplet ska generera ett liknande resultat som du såg tidigare när du kör funktionen i den lokala miljön.

## <a name="connect-to-your-pipeline"></a>Ansluta till din pipeline
Nu när du har en ny anpassad kunskap kan du lägga till det din kompetens. Exemplet nedan visar hur du anropar färdigheten. Eftersom färdigheten kan inte hantera batchar, lägger du till en instruktion om att Maximal batchstorlek ska bara ```1``` att skicka dokument i taget.

```json
{
    "skills": [
      ...,  
      {
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "description": "Our new translator custom skill",
        "uri": "https://translatecogsrch.azurewebsites.net/api/Translate?code=[enter default host key here]",
        "batchSize":1,
        "context": "/document",
        "inputs": [
          {
            "name": "text",
            "source": "/document/content"
          },
          {
            "name": "language",
            "source": "/document/destinationLanguage"
          }
        ],
        "outputs": [
          {
            "name": "text",
            "targetName": "translatedText"
          }
        ]
      }
  ]
}
```

## <a name="next-steps"></a>Nästa steg
Grattis! Du har skapat din första anpassade enricher. Nu kan du följa samma mönster för att lägga till dina egna anpassade funktioner. 

+ [Lägg till en anpassad kompetens för en pipeline för kognitiv sökning](cognitive-search-custom-skill-interface.md)
+ [Hur du definierar en kompetens](cognitive-search-defining-skillset.md)
+ [Skapa kompetens (REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [Avancerad och mappning](cognitive-search-output-field-mapping.md)
