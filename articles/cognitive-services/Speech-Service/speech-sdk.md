---
title: Om tal SDK – Speech Services
titleSuffix: Azure Cognitive Services
description: Tal Software Development Kit (SDK) får dina program inbyggda funktioner i tjänsten tal, vilket gör det enklare att utveckla programvara. Den här artikeln innehåller mer information om SDK för Windows, Linux och Android.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: wolfma
ms.openlocfilehash: 120dd4a6a342732f3e07d87786009779d23be829
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/05/2019
ms.locfileid: "67606410"
---
# <a name="about-the-speech-sdk"></a>Om tal SDK

Tal Software Development Kit (SDK) får dina program över Speech-tjänsterna, vilket gör det enklare att utveckla talbaserade programvara. För närvarande SDK: erna ger åtkomst till **tal till text**, **text till tal**, **talöversättning**, **avsiktsigenkänning**, och **Bot Framework Direct Line tal kanal**. En allmän översikt över funktioner och plattformar som stöds finns i dokumentationen [startsidan](https://aka.ms/csspeech).

[!INCLUDE [Speech SDK Platforms](../../../includes/cognitive-services-speech-service-speech-sdk-platforms.md)]

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

## <a name="get-the-sdk"></a>Hämta SDK

### <a name="windows"></a>Windows

För Windows stöder vi följande språk:

* C#UWP och .NET, C++: Du kan referera till och använder den senaste versionen av vår tal SDK NuGet-paketet. Paketet innehåller 32-bitars och 64-bitars klientbibliotek och hanterade (.NET)-bibliotek. SDK: N kan installeras i Visual Studio med hjälp av NuGet. Sök efter **Microsoft.CognitiveServices.Speech**.

* Java: Du kan referera till och använder den senaste versionen av vår tal SDK Maven-paketet som stöder endast Windows x64. I Maven-projekt lägger du till `https://csspeechstorage.blob.core.windows.net/maven/` som ytterligare lagringsplats och referens `com.microsoft.cognitiveservices.speech:client-sdk:1.6.0` som ett beroende.

### <a name="linux"></a>Linux

> [!NOTE]
> För närvarande kan vi stöder endast Ubuntu 16.04, Ubuntu 18.04 och Debian 9 på en dator (x86 eller x64 för C++ utvecklings- och x64 för .NET Core, Java och Python).

Kontrollera att du har de bibliotek som krävs installerat genom att köra följande shell-kommandon:

I Ubuntu:

```sh
sudo apt-get update
sudo apt-get install libssl1.0.0 libasound2
```

On Debian 9:

```sh
sudo apt-get update
sudo apt-get install libssl1.0.2 libasound2
```

* C#: Du kan referera till och använder den senaste versionen av vår tal SDK NuGet-paketet. Lägg till följande paketreferens i projektet om du vill referera till SDK:

  ```xml
  <PackageReference Include="Microsoft.CognitiveServices.Speech" Version="1.6.0" />
  ```

* Java: Du kan referera till och använder den senaste versionen av vår tal SDK Maven-paketet. I Maven-projekt lägger du till `https://csspeechstorage.blob.core.windows.net/maven/` som ytterligare lagringsplats och referens `com.microsoft.cognitiveservices.speech:client-sdk:1.6.0` som ett beroende.

* C++: Hämta SDK: N som en [.tar-paketet](https://aka.ms/csspeech/linuxbinary) och packa upp filerna i en katalog med ditt val. I följande tabell visas mappstrukturen SDK:

  |Sökväg|Beskrivning|
  |-|-|
  |`license.md`|Licens|
  |`ThirdPartyNotices.md`|Meddelanden från tredje part|
  |`include`|Header-filer för C och C++|
  |`lib/x64`|Interna x64 bibliotek för att länka med ditt program|
  |`lib/x86`|Interna x86 bibliotek för att länka med ditt program|

  Om du vill skapa ett program, kopiera eller flytta nödvändiga binärfiler (och bibliotek) i din utvecklingsmiljö. Lägga till dem som krävs i Byggprocessen.

### <a name="android"></a>Android

Java SDK för Android kommer som ett [AAR (Android-biblioteket)](https://developer.android.com/studio/projects/android-library), som innehåller de nödvändiga bibliotek och behörigheter som krävs för Android. Den finns i ett Maven-centrallager på `https://csspeechstorage.blob.core.windows.net/maven/` som paket `com.microsoft.cognitiveservices.speech:client-sdk:1.6.0`.

Om du vill använda paketet från Android Studio-projekt gör du följande ändringar:

* I projektet på servernivå build.gradle-filen lägger du till följande för att den `repository` avsnittet:

  ```gradle
  maven { url 'https://csspeechstorage.blob.core.windows.net/maven/' }
  ```

* I modul på servernivå build.gradle-filen lägger du till följande för att den `dependencies` avsnittet:

  ```gradle
  implementation 'com.microsoft.cognitiveservices.speech:client-sdk:1.6.0'
  ```

Java SDK är också en del av den [tal Devices SDK](speech-devices-sdk.md).

[!INCLUDE [Get the samples](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]

## <a name="next-steps"></a>Nästa steg

* [Hämta en kostnadsfri utvärderingsprenumeration på Speech](https://azure.microsoft.com/try/cognitive-services/)
* [Se hur du kan känna igen tal i C#](quickstart-csharp-dotnet-windows.md)
