---
title: 'Snabbstart: Känna igen tal, C# (.NET Core) – Speech Services'
titleSuffix: Azure Cognitive Services
description: Lär dig att känna igen tal i C# under .NET Core för Windows- eller macOS med hjälp av tal-SDK
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: wolfma
ms.openlocfilehash: 9c445ea685518cec77089ce644a79305ff7f3e9e
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/05/2019
ms.locfileid: "67606591"
---
# <a name="quickstart-recognize-speech-with-the-speech-sdk-for-net-core"></a>Snabbstart: Taligenkänning med Speech SDK för .NET Core

Snabbstarter kan också användas för [text till tal](quickstart-text-to-speech-dotnetcore.md) och [talöversättning](quickstart-translate-speech-dotnetcore-windows.md).

Om du vill kan du välja olika programmeringsspråk och/eller miljön:<br/>
[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

I den här artikeln skapar du en C# konsolapp för .NET Core för Windows- eller macOS med hjälp av Cognitive Services [tal SDK](speech-sdk.md). Du transkriberar tal till text i realtid från datorns mikrofon. Programmet har skapats med [NuGet-paketets tal-SDK](https://aka.ms/csspeech/nuget) och Microsoft Visual Studio 2017 (alla versioner).

> [!NOTE]
> .NET Core är en plattformsoberoende plattform med öppen källkod som implementerar [.NET Standard](https://docs.microsoft.com/dotnet/standard/net-standard)-specifikationen.

Du behöver en Speech Services-prenumeration för att slutföra den här snabbstarten. Du kan skaffa en utan kostnad. Se [testa Speech Services kostnadsfritt](get-started.md) mer information.

## <a name="prerequisites"></a>Förutsättningar

För den här snabbstarten krävs:

* [.NET Core SDK](https://dotnet.microsoft.com/download)
* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)
* En Azure-prenumerationsnyckel för Speech Service. [Skaffa en kostnadsfritt](get-started.md).

## <a name="create-a-visual-studio-project"></a>Skapa ett Visual Studio-projekt

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-dotnetcore-create-proj.md)]

## <a name="add-sample-code"></a>Lägga till exempelkod

1. Öppna `Program.cs` och ersätt all kod i den med följande.

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-dotnetcore/helloworld/Program.cs#code)]

1. Ersätt strängen `YourSubscriptionKey` i samma fil med din prenumerationsnyckel.

1. Ersätt även strängen `YourServiceRegion` med den [region](regions.md) som är associerad med din prenumeration (till exempel `westus` för en kostnadsfri provprenumeration).

1. Spara ändringarna i projektet.

## <a name="build-and-run-the-app"></a>Skapa och kör appen

1. Skapa programmet. I menyraden väljer du **Skapa** > **Skapa lösning**. Koden ska kompileras utan fel.

    ![Skärmbild av Visual Studio-programmet med Skapa lösning markerat](media/sdk/qs-csharp-dotnetcore-windows-05-build.png "Skapandet lyckades")

1. Starta programmet. I menyraden väljer du **Felsök** > **Starta felsökning**, eller tryck på **F5**.

    ![Skärmbild av Visual Studio-programmet, med Starta felsökning markerat](media/sdk/qs-csharp-dotnetcore-windows-06-start-debugging.png "Starta appen i felsökningsläge")

1. Ett konsolfönster öppnas där du uppmanas att säga något. Säg en engelsk fras eller en mening. Ditt tal överförs till Speech Services och transkriberas till text som visas i samma fönster.

    ![Skärmbild av konsolens utdata efter lyckad taligenkänning](media/sdk/qs-csharp-dotnetcore-windows-07-console-output.png "Konsolens utdata efter lyckad taligenkänning")

## <a name="next-steps"></a>Nästa steg

Ytterligare exempel, till exempel hur man läser tal från en ljudfil, finns på GitHub.

> [!div class="nextstepaction"]
> [Utforska C#-exempel på GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Se också

- [Anpassa akustiska modeller](how-to-customize-acoustic-models.md)
- [Anpassa språkmodeller](how-to-customize-language-model.md)
