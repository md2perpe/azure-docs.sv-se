---
title: Language Understanding Bot C# v4
titleSuffix: Language Understanding - Azure Cognitive Services
description: Använd C# och skapa en chattrobot som är integrerad med språkförståelse (LUIS). Roboten har byggts med Bot Framework version 4 och Azure Web app bot service.
services: cognitive-services
author: diberry
ms.custom: seodec18
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 06/24/2019
ms.author: diberry
ms.openlocfilehash: 8a03d87441f26d3116aff8af33fd94da0ef9a909
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67438442"
---
# <a name="tutorial-use-a-web-app-bot-enabled-with-language-understanding-in-c"></a>Självstudier: Använd en Web App-robot som aktiverats med Language Understanding iC#

Använd C# att skapa en chattrobot som är integrerad med språkförståelse (LUIS). Roboten har skapats med Azure [Web app-robot](https://docs.microsoft.com/azure/bot-service/) resurs och [Bot Framework-version](https://github.com/Microsoft/botbuilder-dotnet) V4.

**I den här självstudiekursen får du lära du dig att:**

> [!div class="checklist"]
> * Skapa en webbappsrobot. Den här processen skapar en ny LUIS-app.
> * Ladda ned bot-projekt som skapats av bot webbtjänsten
> * Starta roboten och emulatorn lokalt på datorn
> * Visa uttrycksresultat i roboten

## <a name="prerequisites"></a>Förutsättningar

* [Robotemulator](https://aka.ms/abs/build/emulatordownload)
* [Visual Studio](https://visualstudio.microsoft.com/downloads/)


## <a name="create-a-web-app-bot-resource"></a>Skapa en web app-robot resurs

1. På [Azure-portalen](https://portal.azure.com) väljer du **Skapa ny resurs**.

1. I sökrutan söker du efter och väljer **Web App Bot** (Webbappsrobot). Välj **Skapa**.

1. Ange nödvändig information i **Robottjänst**:

    |Inställning|Syfte|Rekommenderad inställning|
    |--|--|--|
    |Robotnamn|Resursnamn|`luis-csharp-bot-` + `<your-name>`, till exempel `luis-csharp-bot-johnsmith`|
    |Prenumeration|Prenumeration där roboten ska skapas.|Din primära prenumeration.
    |Resursgrupp|Logisk grupp med Azure-resurser|Skapa en ny grupp för att lagra alla resurser som används med den här roboten och ge gruppen namnet `luis-csharp-bot-resource-group`.|
    |Location|Azure-region – Det här behöver inte vara samma som redigerings- eller publiceringsregionen för LUIS.|`westus`|
    |Prisnivå|Används för begränsningar och fakturering av tjänstbegäranden.|`F0` är den kostnadsfria nivån.
    |Appnamn|Namnet används som underdomänen när din robot distribueras till molnet (exempelvis humanresourcesbot.azurewebsites.net).|`luis-csharp-bot-` + `<your-name>`, till exempel `luis-csharp-bot-johnsmith`|
    |Robotmall|Bot Framework-inställningar – se nästa tabell|
    |LUIS-appens plats|Måste vara samma som LUIS-resursregionen|`westus`|
    |App service-plan/plats|Ändra inte från angivna standardvärdet.|
    |Application Insights|Ändra inte från angivna standardvärdet.|
    |Microsoft App-ID och lösenord|Ändra inte från angivna standardvärdet.|

1. I den **Bot mallen**, väljer du följande och välj sedan den **Välj** knappen under de här inställningarna:

    |Inställning|Syfte|Val|
    |--|--|--|
    |SDK-version|Bot Framework-version|**SDK v4**|
    |SDK-språk|Programmeringsspråk för robot|**C#**|
    |Bot|Typ av bot|**Basic bot** (Grundläggande robot)|
    
1. Välj **Skapa**. Nu skapas och distribueras robottjänsten till Azure. I en del av den här processen skapas en LUIS-app med namnet `luis-csharp-bot-XXXX`. Det här namnet är baserad på appnamnet /Azure Bot Service.

    [![Skapa en webbappsrobot](./media/bfv4-csharp/create-web-app-service.png)](./media/bfv4-csharp/create-web-app-service.png#lightbox)

    Vänta tills bot-tjänst skapas innan du fortsätter.

## <a name="the-bot-has-a-language-understanding-model"></a>Roboten har en Luis-modell

Bot serviceprocessen skapar också en ny LUIS-app med avsikter och exempel yttranden. Roboten tillhandahåller avsiktsmappning till den nya LUIS-appen för följande avsikter: 

|LUIS-avsikter för grundläggande robot|Exempeluttryck|
|--|--|
|Bok flygning|`Travel to Paris`|
|Avbryt|`bye`|
|Ingen|Vad som helst utanför appens domän.|

## <a name="test-the-bot-in-web-chat"></a>Testa roboten i Web Chat

1. När du fortfarande i Azure-portalen för den nya roboten, Välj **Test i Web Chat**. 
1. I den **Skriv meddelandet** textrutan Ange texten `hello`. Roboten svarar med information om bot framework, samt Exempelfrågor för specifika LUIS-modellen, till exempel en flygning till Paris för bokning. 

    ![Skärmbild av Azure-portalen anger du texten ”hello”.](./media/bfv4-csharp/ask-bot-question-in-portal-test-in-web-chat.png)

    Du kan använda funktionen test för att snabbt testa din robot. För mer Slutför testning, inklusive felsökning, ladda ned bot-koden och använder Visual Studio. 

## <a name="download-the-web-app-bot-source-code"></a>Ladda ned källkoden för web app-robot
Ladda ned koden för webbappsroboten så att du kan utveckla den och använda den på den lokala datorn. 

1. Välj **Skapa** i avsnittet **Bot management** (Robothantering) på Azure-portalen. 

1. Välj **Download Bot source code** (Ladda ned robotens källkod). 

    [![Ladda ned källkoden för webbappsroboten för grundläggande robot](../../../includes/media/cognitive-services-luis/bfv4/download-code.png)](../../../includes/media/cognitive-services-luis/bfv4/download-code.png#lightbox)

1. När popup frågan **omfattar appinställningar i den hämta zip-filen?** väljer **Ja**.

1. Om källkoden är zippad innehåller meddelandet en länk för att ladda ned koden. Klicka på länken. 

1. Spara ZIP-filen till din lokala dator och extrahera filerna. Öppna projektet med Visual Studio. 

## <a name="review-code-to-send-utterance-to-luis-and-get-response"></a>Granska koden för att skicka uttryck till LUIS och få svar

1. Öppna den **LuisHelper.cs** fil. Det är här som användaruttrycket som angetts i roboten skickas till LUIS. Svaret från LUIS returneras från metoden som en **BookDetails** objekt. När du skapar dina egna bot, bör du också skapa egna objekt för att returnera information från LUIS. 


    ```csharp
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License.
    
    using System;
    using System.Linq;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Bot.Builder;
    using Microsoft.Bot.Builder.AI.Luis;
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Logging;
    
    namespace Microsoft.BotBuilderSamples
    {
        public static class LuisHelper
        {
            public static async Task<BookingDetails> ExecuteLuisQuery(IConfiguration configuration, ILogger logger, ITurnContext turnContext, CancellationToken cancellationToken)
            {
                var bookingDetails = new BookingDetails();
    
                try
                {
                    // Create the LUIS settings from configuration.
                    var luisApplication = new LuisApplication(
                        configuration["LuisAppId"],
                        configuration["LuisAPIKey"],
                        "https://" + configuration["LuisAPIHostName"]
                    );
    
                    var recognizer = new LuisRecognizer(luisApplication);
    
                    // The actual call to LUIS
                    var recognizerResult = await recognizer.RecognizeAsync(turnContext, cancellationToken);
    
                    var (intent, score) = recognizerResult.GetTopScoringIntent();
                    if (intent == "Book_flight")
                    {
                        // We need to get the result from the LUIS JSON which at every level returns an array.
                        bookingDetails.Destination = recognizerResult.Entities["To"]?.FirstOrDefault()?["Airport"]?.FirstOrDefault()?.FirstOrDefault()?.ToString();
                        bookingDetails.Origin = recognizerResult.Entities["From"]?.FirstOrDefault()?["Airport"]?.FirstOrDefault()?.FirstOrDefault()?.ToString();
    
                        // This value will be a TIMEX. And we are only interested in a Date so grab the first result and drop the Time part.
                        // TIMEX is a format that represents DateTime expressions that include some ambiguity. e.g. missing a Year.
                        bookingDetails.TravelDate = recognizerResult.Entities["datetime"]?.FirstOrDefault()?["timex"]?.FirstOrDefault()?.ToString().Split('T')[0];
                    }
                }
                catch (Exception e)
                {
                    logger.LogWarning($"LUIS Exception: {e.Message} Check your LUIS configuration.");
                }
    
                return bookingDetails;
            }
        }
    }
    ```

1. Öppna **BookingDetails.cs** att visa hur objektet avlägsnar LUIS-information. 

    ```csharp
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License.
    
    namespace Microsoft.BotBuilderSamples
    {
        public class BookingDetails
        {
            public string Destination { get; set; }
    
            public string Origin { get; set; }
    
            public string TravelDate { get; set; }
        }
    }
    ```

1. Öppna **dialogrutor -> BookingDialog.cs** att förstå hur BookingDetails objektet används för att hantera flödet konversationen. Resor information uppmanas i steg och hela bokningen bekräftas och slutligen upprepas tillbaka till användaren. 

    ```csharp
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License.
    
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Bot.Builder;
    using Microsoft.Bot.Builder.Dialogs;
    using Microsoft.Recognizers.Text.DataTypes.TimexExpression;
    
    namespace Microsoft.BotBuilderSamples.Dialogs
    {
        public class BookingDialog : CancelAndHelpDialog
        {
            public BookingDialog()
                : base(nameof(BookingDialog))
            {
                AddDialog(new TextPrompt(nameof(TextPrompt)));
                AddDialog(new ConfirmPrompt(nameof(ConfirmPrompt)));
                AddDialog(new DateResolverDialog());
                AddDialog(new WaterfallDialog(nameof(WaterfallDialog), new WaterfallStep[]
                {
                    DestinationStepAsync,
                    OriginStepAsync,
                    TravelDateStepAsync,
                    ConfirmStepAsync,
                    FinalStepAsync,
                }));
    
                // The initial child Dialog to run.
                InitialDialogId = nameof(WaterfallDialog);
            }
    
            private async Task<DialogTurnResult> DestinationStepAsync(WaterfallStepContext stepContext, CancellationToken cancellationToken)
            {
                var bookingDetails = (BookingDetails)stepContext.Options;
    
                if (bookingDetails.Destination == null)
                {
                    return await stepContext.PromptAsync(nameof(TextPrompt), new PromptOptions { Prompt = MessageFactory.Text("Where would you like to travel to?") }, cancellationToken);
                }
                else
                {
                    return await stepContext.NextAsync(bookingDetails.Destination, cancellationToken);
                }
            }
    
            private async Task<DialogTurnResult> OriginStepAsync(WaterfallStepContext stepContext, CancellationToken cancellationToken)
            {
                var bookingDetails = (BookingDetails)stepContext.Options;
    
                bookingDetails.Destination = (string)stepContext.Result;
    
                if (bookingDetails.Origin == null)
                {
                    return await stepContext.PromptAsync(nameof(TextPrompt), new PromptOptions { Prompt = MessageFactory.Text("Where are you traveling from?") }, cancellationToken);
                }
                else
                {
                    return await stepContext.NextAsync(bookingDetails.Origin, cancellationToken);
                }
            }
            private async Task<DialogTurnResult> TravelDateStepAsync(WaterfallStepContext stepContext, CancellationToken cancellationToken)
            {
                var bookingDetails = (BookingDetails)stepContext.Options;
    
                bookingDetails.Origin = (string)stepContext.Result;
    
                if (bookingDetails.TravelDate == null || IsAmbiguous(bookingDetails.TravelDate))
                {
                    return await stepContext.BeginDialogAsync(nameof(DateResolverDialog), bookingDetails.TravelDate, cancellationToken);
                }
                else
                {
                    return await stepContext.NextAsync(bookingDetails.TravelDate, cancellationToken);
                }
            }
    
            private async Task<DialogTurnResult> ConfirmStepAsync(WaterfallStepContext stepContext, CancellationToken cancellationToken)
            {
                var bookingDetails = (BookingDetails)stepContext.Options;
    
                bookingDetails.TravelDate = (string)stepContext.Result;
    
                var msg = $"Please confirm, I have you traveling to: {bookingDetails.Destination} from: {bookingDetails.Origin} on: {bookingDetails.TravelDate}";
    
                return await stepContext.PromptAsync(nameof(ConfirmPrompt), new PromptOptions { Prompt = MessageFactory.Text(msg) }, cancellationToken);
            }
    
            private async Task<DialogTurnResult> FinalStepAsync(WaterfallStepContext stepContext, CancellationToken cancellationToken)
            {
                if ((bool)stepContext.Result)
                {
                    var bookingDetails = (BookingDetails)stepContext.Options;
    
                    return await stepContext.EndDialogAsync(bookingDetails, cancellationToken);
                }
                else
                {
                    return await stepContext.EndDialogAsync(null, cancellationToken);
                }
            }
    
            private static bool IsAmbiguous(string timex)
            {
                var timexProperty = new TimexProperty(timex);
                return !timexProperty.Types.Contains(Constants.TimexTypes.Definite);
            }
        }
    }
    ```


## <a name="start-the-bot-code-in-visual-studio"></a>Starta bot-kod i Visual Studio

Starta roboten i Visual Studio. Ett webbläsarfönster öppnas med webbappsrobotens webbplats på `http://localhost:3978/`. En startsida visas med information om din robot.

![En startsida visas med information om din robot.](./media/bfv4-csharp/running-bot-web-home-page-success.png)

## <a name="use-the-bot-emulator-to-test-the-bot"></a>Använda bot-emulatorn för att testa roboten

1. Börja Bot-emulatorn och välj **öppna Bot**.
1. I den **öppna en robot** standardarbetsytan i dialogrutan, ange din bot-URL: en, till exempel `http://localhost:3978/api/messages`. Den `/api/messages` vägen är webbadressen för roboten.
1. Ange den **Microsoft App-ID** och **lösenord för Microsoft App**, som finns i den **appsettings.json** filen i roten för bot-koden som du laddade ned.

    Alternativt kan du skapa en ny robot konfiguration och kopiera den `appId` och `appPassword` från den **appsettings.json** filen i Visual Studio-projektet för roboten. Namnet på konfigurationsfilen bot bör vara samma som namnet på bot. 

    ```json
    {
        "name": "<bot name>",
        "description": "<bot description>",
        "services": [
            {
                "type": "endpoint",
                "appId": "<appId from appsettings.json>",
                "appPassword": "<appPassword from appsettings.json>",
                "endpoint": "http://localhost:3978/api/messages",
                "id": "<don't change this value>",
                "name": "http://localhost:3978/api/messages"
            }
        ],
        "padlock": "",
        "version": "2.0",
        "overrides": null,
        "path": "<local path to .bot file>"
    }
    ```

1. Ange i emulatorn bot `Hello` och få samma svar för den grundläggande bot som du fick i det **Test i Web Chat**.

    [![Svar från grundläggande robot i emulatorn](./media/bfv4-csharp/ask-bot-emulator-a-question-and-get-response.png)](./media/bfv4-csharp/ask-bot-emulator-a-question-and-get-response.png#lightbox)


## <a name="ask-bot-a-question-for-the-book-flight-intent"></a>Fråga roboten för boken flygning avsikten

1. Boka en flygning i bot-emulatorn genom att ange följande uttryck: 

    ```bot
    Book a flight from Paris to Berlin on March 22, 2020
    ```

    Bot-emulatorn ombeds att bekräfta. 

1. Välj **Ja**. Roboten svarar med en sammanfattning av dess åtgärder. 
1. Loggen bot-emulatorns, Välj raden som innehåller `Luis Trace`. Detta visar JSON-svar från LUIS för avsikt och entiteter av uttryck.

    [![Svar från grundläggande robot i emulatorn](./media/bfv4-csharp/ask-luis-book-flight-question-get-json-response-in-bot-emulator.png)](./media/bfv4-csharp/ask-luis-book-flight-question-get-json-response-in-bot-emulator.png#lightbox)

[!INCLUDE [Bot Information](../../../includes/cognitive-services-qnamaker-luis-bot-info.md)]

## <a name="next-steps"></a>Nästa steg

Se fler [exempel](https://github.com/microsoft/botframework-solutions) med konversationsrobotar. 

> [!div class="nextstepaction"]
> [Skapa en Luis-app med en anpassad certifikatmottagare-domän](luis-quickstart-intents-only.md)
