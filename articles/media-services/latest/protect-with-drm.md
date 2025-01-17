---
title: Använda DRM dynamisk kryptering och licens delivery-tjänsten med Azure Media Services | Microsoft Docs
description: Du kan använda Azure Media Services till att leverera strömmar som krypterats med Microsoft PlayReady, Google Widevine eller Apple FairPlay-licenser.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/25/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 4f1618260f6bfa0491e919e8aab1841e61603e5b
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2019
ms.locfileid: "67273264"
---
# <a name="tutorial-use-drm-dynamic-encryption-and-license-delivery-service"></a>Självstudier: Använda leveranstjänst för dynamisk DRM-krypteringstjänst

> [!NOTE]
> Även om i självstudiekursen använder vi den [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.liveevent?view=azure-dotnet) exempel de allmänna stegen är desamma för [REST API](https://docs.microsoft.com/rest/api/media/liveevents), [CLI](https://docs.microsoft.com/cli/azure/ams/live-event?view=azure-cli-latest), eller andra stöds [SDK: er](media-services-apis-overview.md#sdks) .

Du kan använda Azure Media Services till att leverera strömmar som krypterats med Microsoft PlayReady, Google Widevine eller Apple FairPlay-licenser. Mer detaljerad förklaring finns i [innehållsskydd med dynamisk kryptering](content-protection-overview.md).

Media Services är vidare en tjänst för att leverera PlayReady-, Widevine och FairPlay-DRM-licenser. När en användare begär ett DRM-skyddat innehåll, begär spelarappen en licens från Media Services-licenstjänsten. Om spelarappen är auktoriserad utfärdar Media Services-licensen en licens till spelaren. En licens innehåller en krypteringsnyckel som kan användas av klientspelaren för att dekryptera och direktuppspela innehållet.

Den här artikeln bygger på exemplet om [kryptering med DRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM). 

Exemplet som beskrivs i den här artikeln ger följande resultat:

![AMS med DRM-skyddad video](./media/protect-with-drm/ams_player.png)

I den här självstudiekursen lär du dig att:    

> [!div class="checklist"]
> * Skapa en kodning transformering
> * Ange signeringsnyckel som används för verifiering av din token
> * Ange krav för principen för content-nyckel
> * Skapa en StreamingLocator med den angivna principen för direktuppspelning
> * Skapa en URL används för att spela upp din fil

## <a name="prerequisites"></a>Nödvändiga komponenter

Följande krävs för att kunna genomföra självstudien.

* Gå igenom artikeln med [översikten om innehållsskydd](content-protection-overview.md).
* Granska den [utforma multi-DRM innehållsskydd system med åtkomstkontroll](design-multi-drm-system-with-access-control.md)
* Installera Visual Studio Code eller Visual Studio
* Skapa ett nytt Azure Media Services-konto, som beskrivs i [den här snabbstarten](create-account-cli-quickstart.md).
* Hämta autentiseringsuppgifter som krävs för att använda API:er för Media Services med hjälp av [åtkomst till API:er](access-api-cli-how-to.md)
* Ställ in lämpliga värden i programmets konfigurationsfil (appsettings.json).

## <a name="download-code"></a>Ladda ned kod

Klona en GitHub-lagringsplats som innehåller hela det .NET-exempel som diskuteras i den här artikeln till din dator med följande kommando:

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```
 
Du hittar exemplet om att kryptera med DRM i mappen [EncryptWithDRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM).

> [!NOTE]
> Exemplet skapar unika resurser varje gång du kör programmet. Normalt återanvänder du befintliga resurser som transformeringar och policyer (om befintlig resurs har nödvändiga konfigurationer). 

## <a name="start-using-media-services-apis-with-net-sdk"></a>Börja med att använda Media Services-API:er med .NET SDK

Om du vill börja använda API:er för Media Services med .NET, måste du skapa ett **AzureMediaServicesClient**-objekt. När du skapar objektet måste du ange de autentiseringsuppgifter som krävs för att klienten ska kunna ansluta till Azure med hjälp av Azure AD. I den kod som du har klonat i början av artikeln skapade funktionen **GetCredentialsAsync** objektet ServiceClientCredentials baserat på de autentiseringsuppgifter som anges i den lokala konfigurationsfilen. 

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CreateMediaServicesClient)]

## <a name="create-an-output-asset"></a>Skapa en utdatatillgång  

[Utdatatillgången](assets-concept.md) lagrar resultatet av ditt kodningsjobb.  

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CreateOutputAsset)]
 
## <a name="get-or-create-an-encoding-transform"></a>Hämta eller skapa en kodtransformering

När du skapar en ny instans för en [Transformering](transforms-jobs-concept.md), måste du ange vilken utdata du vill att den ska skapa. Den nödvändiga parametern är en `transformOutput` objekt som visas i koden nedan. Varje TransformOutput innehåller en **förinställda**. Förinställning beskriver de stegvisa anvisningarna/video eller ljud bearbetningen som ska användas för att generera den önskade TransformOutput. Det exempel som beskrivs i artikeln använder en inbyggd förinställning som kallas **AdaptiveStreaming**. Förinställningen kodar indatavideon i en automatiskt genererad bithastighetsstege (par för bithastighetsupplösning) som baseras på indatans upplösning och bithastighet, samt producerar ISO MP4-filer med H.264-video och AAC-ljud som motsvarar varje par för bithastighetsupplösningen. 

Innan du skapar en ny **transformering** bör du först kontrollera om det redan finns en. Det gör du med metoden **Get** som visas i koden nedan.  I Media Services v3 returnerar **Get**-metoderna i entiteter **null** om entiteten inte finns (skiftlägesokänslig kontroll av namnet).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#EnsureTransformExists)]

## <a name="submit-job"></a>Skicka jobb

Som nämns ovan är objektet **Transformering** receptet och ett [Jobb](transforms-jobs-concept.md) är det faktiska begärandet till Media Services om att tillämpa **transformeringen** på en indatavideo eller ett ljudinnehåll. **Jobb** anger information som platsen för indatavideon och platsen för utdatan.

I den här självstudiekursen skapar vi jobbets indata baserat på en fil som matas direkt från en [HTTP-källwebbadress](job-input-from-http-how-to.md).

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#SubmitJob)]

## <a name="wait-for-the-job-to-complete"></a>Vänta tills jobbet är klart

Jobbet tar en stund att slutföra och du meddelas när detta sker. Kodexemplet nedan visar hur du kontrollerar **jobbets** status i tjänsten. Vi rekommenderar inte att utföra avsökning av produktionsprogram på grund av att svarstiden kan bli lång. Avsökningen kan begränsas om den överanvänds på ett konto. Utvecklare bör i stället använda Event Grid. Se [Dirigera händelser till en anpassad webbslutpunkt](job-state-events-cli-how-to.md).

**Jobbet** går vanligtvis igenom följande tillstånd: **Schemalagd**, **I kö**, **Bearbetar**, **Slutfört** (sluttillstånd). Om jobbet har påträffat ett fel visas tillståndet **Fel**. Om jobbet avbryts visas **Avbryter** och **Avbruten** när det är klart.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#WaitForJobToFinish)]

## <a name="create-a-content-key-policy"></a>Skapa en princip som är viktiga för innehåll

En innehållsnyckel ger säker åtkomst till dina tillgångar. Du måste skapa en [innehåll nyckel princip](content-key-policy-concept.md) när kryptera ditt innehåll med en DRM. Principen konfigurerar hur innehållsnyckeln levereras om du vill avsluta klienter. Innehållsnyckeln är associerad med en Strömningspositionerare. Media Services tillhandahåller också nyckelleveranstjänsten som levererar krypteringsnycklar och licenser till behöriga användare. 

Måste du ange krav (begränsningar) på den **innehåll nyckel princip** som måste uppfyllas för att leverera nycklar med den angivna konfigurationen. I det här exemplet anger vi följande konfigurationer och krav:

* Konfiguration 

    Licenserna för [PlayReady](playready-license-template-overview.md) och [Widevine](widevine-license-template-overview.md) är konfigurerade så att de kan levereras av Media Services-licensleveranstjänsten. Det här exempelprogrammet konfigurerar inte [FairPlay](fairplay-license-overview.md)-licensen men det innehåller en metod som du kan använda för att konfigurera FairPlay. Du kan lägga till FairPlay-konfiguration som ytterligare ett alternativ.

* Begränsning

    Programmet anger en JWT-tokentypbegränsning för principen.

När en ström begärs av en spelare krypterar Media Services innehållet med den angivna nyckeln. Om spelaren vill dekryptera dataströmmen begär hon eller han nyckeln från nyckelleveranstjänsten. För att avgöra om användaren är behörig för nyckeln utvärderas den innehållsnyckelprincip som du angav för nyckeln.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetOrCreateContentKeyPolicy)]

## <a name="create-a-streaming-locator"></a>Skapa en positionerare för direktuppspelning

När kodningen är klar och innehållsnyckelprincipen är inställd är nästa steg att göra videon i utdatatillgången tillgänglig för uppspelning av klienterna. Du kan göra det i två steg: 

1. Skapa en [positionerare för direktuppspelning](streaming-locators-concept.md)
2. Skapa webbadresserna för direktuppspelning som klienter kan använda. 

Processen för att skapa den **Strömningspositionerare** kallas för publicering. Som standard kan din **positionerare för direktuppspelning** användas omedelbart efter API-anropen. Den fungerar tills den tas bort, såvida du inte konfigurerar valfria start- och sluttider. 

När du skapar en **Strömningspositionerare**, måste du ange önskad `StreamingPolicyName`. I den här självstudien använder en av fördefinierade Streaming principer, som talar om hur du publicerar innehåll för direktuppspelning för Azure Media Services. I det här exemplet ställer vi in StreamingLocator.StreamingPolicyName för principen ”Predefined_MultiDrmCencStreaming”. PlayReady och Widevine krypteringar tillämpas, nyckeln levereras till klienten uppspelning baserat på de konfigurera DRM-licenserna. Om du dessutom vill kryptera strömmen med CBCS (FairPlay) använder du ”Predefined_MultiDrmStreaming”. 

> [!IMPORTANT]
> Om du använder en anpassad [strömningsprincip](streaming-policy-concept.md) bör du skapa en begränsad uppsättning av sådana principer för ditt Media Service-konto, och återanvända dem för dina StreamingLocators när samma krypterings- och protokollalternativ krävs. Media Service-kontot har en kvot för antalet StreamingPolicy-poster. Du bör inte skapa en ny StreamingPolicy för varje StreamingLocator.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CreateStreamingLocator)]

## <a name="get-a-test-token"></a>Hämta en testtoken
        
I den här självstudien anger vi att innehållsnyckelprincipen ska ha en tokenbegränsning. Den tokenbegränsade principen måste åtföljas av en token utfärdad av en säker tokentjänst (Secure Token Service – STS). Media Services stöder token i formaten [JSON Web Token](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3) (JWT) och det är det vi anger i det här exemplet.

ContentKeyIdentifierClaim används i ContentKeyPolicy, vilket innebär att den token som presenteras för nyckelleveranstjänst måste innehålla identifieraren för ContentKey. I det här exemplet vi anger inte en innehållsnyckel när du skapar den Strömningspositionerare, systemet skapar ett slumpmässigt för oss. För att generera testtoken måste vi få ContentKeyId och placera det i ContentKeyIdentifierClaim-anspråket.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetToken)]

## <a name="build-a-streaming-url"></a>Skapa en strömnings-URL

Nu när [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators) har skapats kan du hämta direktuppspelningswebbadresserna. Om du vill skapa en URL, måste du sammanfoga den [StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints) värdnamn och **Strömningspositionerare** sökväg. I det här exemplet används *standardvärdet* **Slutpunkt för direktuppspelning**. När du skapar ett Media Service-konto första gången kommer detta *standardvärde* för **StreamingEndpoint** vara i ett stoppat tillstånd, så du måste anropa **Starta**.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetMPEGStreamingUrl)]

När du kör appen, kan du se följande:

![Skydda med DRM](./media/protect-with-drm/playready_encrypted_url.png)

Du kan öppna en webbläsare och klistra in den webbadress som bildas för att starta Azure Media Player-demosidan där webbadressen en och token redan är ifyllda. 
 
## <a name="clean-up-resources-in-your-media-services-account"></a>Rensa resurser på ditt Media Services-konto

Vanligtvis bör du rensa bort allt utom objekt som du tänker återanvända (om du t.ex. återanvänder transformeringar behåller du StreamingLocators osv.). Om du vill att ditt konto ska vara rensat efter experimentet, bör du ta bort de resurser som du inte tänker återanvända.  Följande kod tar exempelvis bort Jobb.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#CleanUp)]

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte längre behöver någon av resurserna i resursgruppen, inklusive Media Services och de lagringskonton som du skapade för självstudien, tar du bort resursgruppen som du skapade tidigare. 

Kör följande CLI-kommando:

```azurecli
az group delete --name amsResourceGroup
```

## <a name="ask-questions-give-feedback-get-updates"></a>Ställ frågor, ge feedback, få uppdateringar

Kolla in den [Azure Media Services-community](media-services-community.md) artikeln olika sätt du kan ställa frågor, ge feedback och få uppdateringar om Media Services.

## <a name="next-steps"></a>Nästa steg

Ta del av

> [!div class="nextstepaction"]
> [Skydda med AES-128](protect-with-aes128.md)

