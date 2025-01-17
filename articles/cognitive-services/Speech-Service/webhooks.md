---
title: Webhooks - Taltjänster
titlesuffix: Azure Cognitive Services
description: Webhook är HTTP-återuppringningar perfekt för att optimera din lösning när du hanterar långa processer som import, anpassning, precisionstester eller avskrifter av tidskrävande filer som körs.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: panosper
ms.openlocfilehash: a100049ddfc9d4859e303546c1b10e814cf96ebb
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/05/2019
ms.locfileid: "67606208"
---
# <a name="webhooks-for-speech-services"></a>Webhooks för Taltjänster

Webhooks är som HTTP-återanrop som tillåter programmet att acceptera data från Speech Services när den blir tillgänglig. Du kan använda webhooks för att optimera din användning av våra REST-API: er genom att eliminera behovet av att kontinuerligt söka efter ett svar. I nästa avsnitt lär du dig att använda webhooks med Speech Services.

## <a name="supported-operations"></a>Åtgärder som stöds

Speech Services stöder webhooks för alla långvariga åtgärder. Var och en av åtgärderna i listan nedan kan utlösa en HTTP-motringning när åtgärden har slutförts.

* DataImportCompletion
* ModelAdaptationCompletion
* AccuracyTestCompletion
* TranscriptionCompletion
* EndpointDeploymentCompletion
* EndpointDataCollectionCompletion

Nu ska vi skapa en webhook.

## <a name="create-a-webhook"></a>Skapa en webhook

Nu ska vi skapa en webhook för en offline avskrift. Scenariot: en användare har en lång körs ljudfil som de vill transkribera asynkront med API: et för Batch avskrift.

Webhooks kan skapas genom att göra en POST-begäran till https://\<region\>.cris.ai/api/speechtotext/v2.1/transcriptions/hooks.

Konfigurationsparametrar för begäran tillhandahålls som JSON:

```json
{
  "configuration": {
    "url": "https://your.callback.url/goes/here",
    "secret": "<my_secret>"
  },
  "events": [
    "TranscriptionCompletion"
  ],
  "active": true,
  "name": "TranscriptionCompletionWebHook",
  "description": "This is a Webhook created to trigger an HTTP POST request when my audio file transcription is completed.",
  "properties": {
      "Active" : "True"
  }

}
```
Alla POST-förfrågningar till API: et för Batch avskrift kräver en `name`. Den `description` och `properties` parametrar är valfria.

Den `Active` egenskapen används för att växla ringa tillbaka till din URL: en och inaktivera utan att behöva ta bort och återskapa webhook-registreringen. Om du behöver bara anropa en gång när processen har slutförts kan sedan ta bort webhook och växel den `Active` egenskapen till false.

Händelsetyp `TranscriptionCompletion` tillhandahålls i matrisen händelser. Den anropar tillbaka till din slutpunkt när en avskrift hämtar i ett avslutat tillstånd (`Succeeded` eller `Failed`). När du anropar tillbaka till den registrerade URL, begäran innehåller ett `X-MicrosoftSpeechServices-Event` huvud som innehåller en av de registrerade händelsetyperna. Det finns ett anrop per registrerade händelsetyp.

Det finns en händelsetyp som du inte kan prenumerera. Det är den `Ping` händelsetyp. En begäran med den här typen skickas till URL: en när du är klar skapar en webhook när med hjälp av ping-URL (se nedan).  

I konfigurationen, den `url` egenskapen måste anges. POST-förfrågningar skickas till denna URL. Den `secret` används för att skapa en SHA256-hash för nyttolasten, med hemligheten som en HMAC-nyckel. Hash-värdet har angetts som den `X-MicrosoftSpeechServices-Signature` rubrik vid anrop tillbaka till den registrerade URL. Den här rubriken är Base64-kodad.

Det här exemplet visar hur du validerar en nyttolast med hjälp av C#:

```csharp

private const string EventTypeHeaderName = "X-MicrosoftSpeechServices-Event";
private const string SignatureHeaderName = "X-MicrosoftSpeechServices-Signature";

[HttpPost]
public async Task<IActionResult> PostAsync([FromHeader(Name = EventTypeHeaderName)]WebHookEventType eventTypeHeader, [FromHeader(Name = SignatureHeaderName)]string signature)
{
    string body = string.Empty;
    using (var streamReader = new StreamReader(this.Request.Body))
    {
        body = await streamReader.ReadToEndAsync().ConfigureAwait(false);
        var secretBytes = Encoding.UTF8.GetBytes("my_secret");
        using (var hmacsha256 = new HMACSHA256(secretBytes))
        {
            var contentBytes = Encoding.UTF8.GetBytes(body);
            var contentHash = hmacsha256.ComputeHash(contentBytes);
            var storedHash = Convert.FromBase64String(signature);
            var validated = contentHash.SequenceEqual(storedHash);
        }
    }

    switch (eventTypeHeader)
    {
        case WebHookEventType.Ping:
            // Do your ping event related stuff here (or ignore this event)
            break;
        case WebHookEventType.TranscriptionCompletion:
            // Do your subscription related stuff here.
            break;
        default:
            break;
    }

    return this.Ok();
}

```
I det här kodstycket i `secret` är avkodas och verifieras. Du ser också att händelsetyp webhook har växlats. Det finns för närvarande en händelse per slutförda avskrift. Koden försöker fem gånger för varje händelse (med en sekunds fördröjning) innan den ger upp.

### <a name="other-webhook-operations"></a>Andra webhook-åtgärder

Hämta alla registrerade webhooks: GET https://westus.cris.ai/api/speechtotext/v2.1/transcriptions/hooks

Hämta en specifika webhook: GET https://westus.cris.ai/api/speechtotext/v2.1/transcriptions/hooks/:id

Ta bort en specifika webhook: TA BORT https://westus.cris.ai/api/speechtotext/v2.1/transcriptions/hooks/:id

> [!Note]
> I exemplet ovan är regionen ”westus”. Detta ska ersättas av den region där du har skapat din Speech Services-resurs i Azure-portalen.

POST https://westus.cris.ai/api/speechtotext/v2.1/transcriptions/hooks/:id/ping brödtext: tom

Skickar en POST-begäran till registrerade URL: en. Förfrågan innehåller en `X-MicrosoftSpeechServices-Event` huvud med ett värde ping. Om webhooken har registrerats med en hemlighet, den innehåller en `X-MicrosoftSpeechServices-Signature` huvud med en SHA256-hash för nyttolasten med hemligheten som HMAC-nyckel. Hash-värdet är Base64-kodad.

POST https://westus.cris.ai/api/speechtotext/v2.1/transcriptions/hooks/:id/test brödtext: tom

Skickar en POST-begäran till registrerade URL: en om en entitet för prenumererade händelsetyp (avskrift) finns i systemet och är i rätt läge. Nyttolasten genereras från den sista enhet som skulle ha anropas webhook. Om ingen enhet finns svarar i INLÄGGET med 204. Om ett testbegäran kan göras, svarar med 200. Begärandetexten är av samma form som i GET-begäran för en specifik enhet webhook prenumererar för (till exempel avskrift). Begäran har den `X-MicrosoftSpeechServices-Event` och `X-MicrosoftSpeechServices-Signature` rubriker enligt beskrivningen innan.

### <a name="run-a-test"></a>Kör ett test

Ett snabbtest kan göras med hjälp av webbplatsen https://bin.webhookrelay.com. Därifrån kan du hämta anrop tillbaka URL: er ska skickas som parameter till HTTP-POST för att skapa en webhook som beskrivs tidigare i dokumentet.

Klicka på Skapa Bucket och följ de anvisningarna på skärmen anvisningarna för att hämta en hook. Använd sedan informationen i den här sidan för att registrera hooken med Speech-tjänsten. Nyttolasten för ett relay-meddelande – i svar för slutförandet av en avskrift-ser ut så här:

```json
{
    "results": [],
    "recordingsUrls": [
        "my recording URL"
    ],
    "models": [
        {
            "modelKind": "AcousticAndLanguage",
            "datasets": [],
            "id": "a09c8c8b-1090-443c-895c-3b1cf442dec4",
            "createdDateTime": "2019-03-26T12:48:46Z",
            "lastActionDateTime": "2019-03-26T14:04:47Z",
            "status": "Succeeded",
            "locale": "en-US",
            "name": "v4.13 Unified",
            "description": "Unified",
            "properties": {
                "Purpose": "OnlineTranscription,BatchTranscription,LanguageAdaptation",
                "ModelClass": "unified-v4"
            }
        }
    ],
    "statusMessage": "None.",
    "id": "d41615e1-a60e-444b-b063-129649810b3a",
    "createdDateTime": "2019-04-16T09:35:51Z",
    "lastActionDateTime": "2019-04-16T09:38:09Z",
    "status": "Succeeded",
    "locale": "en-US",
    "name": "Simple transcription",
    "description": "Simple transcription description",
    "properties": {
        "PunctuationMode": "DictatedAndAutomatic",
        "ProfanityFilterMode": "Masked",
        "AddWordLevelTimestamps": "True",
        "AddSentiment": "True",
        "Duration": "00:00:02"
    }
}
```
Meddelandet innehåller inspelning URL och modeller som används för att transkribera att inspelningen.

## <a name="next-steps"></a>Nästa steg

* [Hämta en kostnadsfri utvärderingsprenumeration på Speech](https://azure.microsoft.com/try/cognitive-services/)
