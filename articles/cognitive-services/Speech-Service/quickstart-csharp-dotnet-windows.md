---
title: 'Snabbstart: Taligenkänning, .NET Framework (Windows) – Speech Services'
titleSuffix: Azure Cognitive Services
description: Använd den här guiden för att skapa ett tal till text-konsolprogram med hjälp av .NET-ramverket för Windows och Speech SDK. När du är klar kan du använda datorns mikrofon för att transkribera tal till text i realtid.
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: wolfma
ms.openlocfilehash: d8738357a3bad6626ef6d79248aef1c4d2cb1ead
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/05/2019
ms.locfileid: "67603078"
---
# <a name="quickstart-recognize-speech-with-the-speech-sdk-for-net-framework-windows"></a>Snabbstart: Taligenkänning med Speech SDK för .NET Framework (Windows)

Snabbstarter kan också användas för [text till tal](quickstart-text-to-speech-dotnet-windows.md) och [talöversättning](quickstart-translate-speech-dotnetframework-windows.md).

Om du vill kan du välja olika programmeringsspråk och/eller miljön:<br/>
[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Använd den här guiden för att skapa ett tal till text-konsolprogram med hjälp av .NET-ramverket för Windows och Speech SDK. När du är klar kan du använda datorns mikrofon för att transkribera tal till text i realtid.

För en snabb demonstration (utan att skapa Visual Studio-projektet på egen hand enligt nedan):

Få senaste [Cognitive Services tal SDK-exempel](https://github.com/Azure-Samples/cognitive-services-speech-sdk) från GitHub.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra det här projektet behöver du:

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)
* En prenumerationsnyckel för Speech Service. [Skaffa en utan kostnad](get-started.md).
* Åtkomst till datorns mikrofon

## <a name="create-a-visual-studio-project"></a>Skapa ett Visual Studio-projekt

[!INCLUDE [Create project](../../../includes/cognitive-services-speech-service-create-speech-project-vs-csharp.md)]

## <a name="add-sample-code"></a>Lägga till exempelkod

1. Öppna `Program.cs` och ersätt den automatiskt genererade koden med det här exemplet:

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-dotnet-windows/helloworld/Program.cs#code)]

1. Leta upp och ersätta strängen `YourSubscriptionKey` med din prenumerationsnyckel för Speech Services.

1. Leta upp och ersätt strängen `YourServiceRegion` med den [region](regions.md) som är associerad med din prenumeration. Om du till exempel använder den kostnadsfria utvärderingsversionen är regionen `westus`.

1. Spara ändringarna i projektet.

## <a name="build-and-run-the-app"></a>Skapa och kör appen

1. På menyraden väljer du **Skapa** > **Skapa lösning**. Koden bör nu kompileras utan fel.

    ![Skärmbild av Visual Studio-programmet med Skapa lösning markerat](media/sdk/qs-csharp-dotnet-windows-08-build.png "Slutförd byggprocess")

1. På menyraden väljer du **Felsök** > **Starta felsökning** eller trycker på **F5** för att starta programmet.

    ![Skärmbild av Visual Studio-programmet, med Starta felsökning markerat](media/sdk/qs-csharp-dotnet-windows-09-start-debugging.png "Starta appen i felsökningsläge")

1. Ett konsolfönster visas, där du uppmanas att tala. Säg nu något på engelska. Ditt tal överförs till Speech Services och transkriberas till text i realtid. Resultatet skrivs till konsolen.

    ![Skärmbild av konsolens utdata efter lyckad taligenkänning](media/sdk/qs-csharp-dotnet-windows-10-console-output.png "Konsolens utdata efter lyckad taligenkänning")

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Utforska C#-exempel på GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Se också

- [Anpassa akustiska modeller](how-to-customize-acoustic-models.md)
- [Anpassa språkmodeller](how-to-customize-language-model.md)
