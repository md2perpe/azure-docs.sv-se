---
title: Skydda ditt innehåll med Media Services dynamisk kryptering – Azure | Microsoft Docs
description: Den här artikeln ger en översikt över innehållsskydd med dynamisk kryptering. Den berättar även om att strömma protokoll och krypteringstyper.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/28/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 140e6c338d12732d1e41ccd9dabef1de7d5cf8d8
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67068850"
---
# <a name="content-protection-with-dynamic-encryption"></a>Innehållsskydd med dynamisk kryptering

Du kan använda Azure Media Services för att skydda dina mediefiler från den tidpunkt som den lämnar din dator via lagrings-, bearbetnings- och leverans. Med medietjänster kan leverera du live och på begäran innehållet krypteras dynamiskt med Advanced Encryption Standard (AES-128) eller någon av de tre största digital rights management (DRM) system: Microsoft PlayReady, Google Widevine och FairPlay för Apple. Media Services tillhandahåller också en tjänst för att leverera AES-nycklar och DRM (PlayReady, Widevine och FairPlay) licenser till auktoriserade klienter. 

Följande bild illustrerar arbetsflödet för Media Services-content protection: 

![Skydda innehåll](./media/content-protection/content-protection.svg)

&#42;*dynamisk kryptering stöder AES-128 ”clear key”, CBCS och CENC. Mer information finns i supportmatrisen [här](#streaming-protocols-and-encryption-types).*

Den här artikeln beskriver begrepp och termer som är relevanta för att förstå innehållsskydd med Media Services.

## <a name="main-components-of-a-content-protection-system"></a>Huvudkomponenterna i ett system för innehållsskydd

För att slutföra utformningen ”content protection” system/program, måste du helt förstå omfattningen för arbetet. I följande lista ger en översikt av tre delar som skulle du behöva implementera. 

1. Azure Media Services-kod
  
   Den [DRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithDRM/Program.cs) exemplet visas hur du implementerar multi-DRM system med Media Services v3 med hjälp av .NET. Den visar även hur du använder Media Services licensnyckel/delivery service. Du kan kryptera varje tillgång med flera krypteringstyper (AES-128, PlayReady, Widevine, FairPlay). I dokumentationen om [direktuppspelningsprotokoll och krypteringstyper](#streaming-protocols-and-encryption-types) ser du vad som är bra att kombinera.
  
   I exempel visas hur du:

   1. Skapa och konfigurera en [Innehållsprinciper nyckeln](content-key-policy-concept.md). Du skapar en **innehåll nyckel princip** att konfigurera hur innehållsnyckeln (som ger säker åtkomst till dina tillgångar) levereras om du vill avsluta klienter.    

      * Definiera auktorisering för leverans av licens, att ange logik för auktorisering baserat på anspråk i JWT.
      * Konfigurera [PlayReady](playready-license-template-overview.md), [Widevine](widevine-license-template-overview.md), och/eller [FairPlay](fairplay-license-overview.md) licenser. Mallarna kan du konfigurera rättigheter och behörigheter för var och en av de använda DRM: er.

        ```
        ContentKeyPolicyPlayReadyConfiguration playReadyConfig = ConfigurePlayReadyLicenseTemplate();
        ContentKeyPolicyWidevineConfiguration widevineConfig = ConfigureWidevineLicenseTempate();
        ContentKeyPolicyFairPlayConfiguration fairPlayConfig = ConfigureFairPlayPolicyOptions();
        ```
   2. Skapa en [Strömningspositionerare](streaming-locators-concept.md) som är konfigurerad för att strömma krypterat tillgången. 
  
      Den **Strömningspositionerare** måste associeras med en [Streaming princip](streaming-policy-concept.md). I det här exemplet anger vi StreamingLocator.StreamingPolicyName till principen ”Predefined_MultiDrmCencStreaming”. PlayReady och Widevine krypteringar tillämpas, nyckeln levereras till klienten uppspelning baserat på de konfigurera DRM-licenserna. Om du dessutom vill kryptera strömmen med CBCS (FairPlay) använder du ”Predefined_MultiDrmStreaming”.
      
      Den Strömningspositionerare också är associerad med den **innehåll nyckel princip** som har definierats.
    
   3. Skapa en test-token.

      Den **GetTokenAsync** metoden visar hur du skapar ett test-token.
   4. Skapa strömnings-URL.

      Den **GetDASHStreamingUrlAsync** metoden visar hur du skapar strömnings-URL. I det här fallet, URL-strömmar på **DASH** innehåll.

2. Spelare med AES eller DRM-klienten. En videospelare app baserat på en spelare SDK (intern eller webbläsarbaserade) måste uppfylla följande krav:
   * Player SDK stöder de nödvändiga DRM-klienterna
   * Player SDK stöder de nödvändiga protokollen med direktuppspelning: Smooth, DASH, and/or HLS
   * Player SDK behöver kunna hantera Skicka en JWT-token i begäran för licens-förvärv
  
     Du kan skapa en spelare med hjälp av den [Azure Media Player API](https://amp.azure.net/libs/amp/latest/docs/). Använd den [Azure Media Player ProtectionInfo API](https://amp.azure.net/libs/amp/latest/docs/) att ange vilka DRM-teknik för att använda på olika DRM-plattformar.

     För testning AES eller CENC (Widevine och/eller PlayReady) krypterat innehåll, du kan använda [Azure Media Player](https://ampdemo.azureedge.net/azuremediaplayer.html). Kontrollera att du klickar på ”Avancerat” och kontrollera dina alternativ för kryptering.

     Om du vill testa FairPlay krypterat innehåll, använda [det här testet player](https://aka.ms/amtest). Media player stöder Widevine, PlayReady, och FairPlay DRM: er samt AES-128 Rensa nyckelkryptering. 
    
     Du måste välja rätt webbläsaren om du vill testa olika DRM: er: Chrome/Opera/Firefox för Widevine, Microsoft Edge/IE11 för PlayReady, Safari på macOS för FairPlay.

3. Skydda säkerhetstokentjänst (STS), som utfärdar JSON Web Token (JWT) som åtkomsttoken för åtkomst till serverdelen. Du kan använda AMS-licensleveranstjänster som backend-resurs. En STS har du definiera följande:

   * Utfärdare och målgrupp (eller omfång)
   * Anspråk som är beroende av affärskrav i innehållsskydd
   * Symmetriskt eller asymmetriskt verifiering för signaturverifiering
   * Stöd för nyckelförnyelse (vid behov)

     Du kan använda [STS verktyget](https://openidconnectweb.azurewebsites.net/DRMTool/Jwt) till testet STS, som har stöd för alla 3 typer av verifieringsnyckel: symmetrisk, asymmetrisk eller Azure AD med nyckelförnyelse. 

> [!NOTE]
> Vi rekommenderar starkt att fokusera och fullständigt Testa varje del (beskrivs ovan) innan du fortsätter till nästa del. Använd de verktyg som anges i listan ovan om du vill testa systemet ”content protection”.  

## <a name="streaming-protocols-and-encryption-types"></a>Protokoll för liveuppspelning och krypteringstyper

Du kan använda Media Services för att leverera ditt innehåll dynamiskt krypterad med Klartextnyckel eller DRM-kryptering med PlayReady, Widevine och FairPlay. För närvarande kan kryptera du HTTP Live Streaming (HLS), MPEG DASH och Smooth Streaming-format. Varje protokoll som stöder följande krypteringsmetoder:

### <a name="hls"></a>HLS

HLS-protokollet stöder följande behållare format och krypteringsscheman.

|Behållare-format|Krypteringsschemat|URL-exempel|
|---|---|---|
|Alla|AES|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-aapl,encryption=cbc)`|
|MPG2-TS |CBCS (FairPlay) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-aapl,encryption=cbcs-aapl)`|
|CMAF(fmp4) |CBCS (FairPlay) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-cmaf,encryption=cbcs-aapl)`|
|MPG2-TS |CENC (PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-aapl,encryption=cenc)`|
|CMAF(fmp4) |CENC (PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=m3u8-cmaf,encryption=cenc)`|

HLS/CMAF + FairPlay (inklusive – HEVC / H.265) stöds på följande enheter:

* iOS v11 eller högre 
* iPhone 8 eller senare
* MacOS high Sierra med Intel 7 Gen CPU

### <a name="mpeg-dash"></a>MPEG-DASH

MPEG-DASH-protokollet stöder följande behållare format och krypteringsscheman.

|Behållare-format|Krypteringsschemat|URL-exempel
|---|---|---|
|Alla|AES|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=mpd-time-csf,encryption=cbc)`|
|CSF(fmp4) |CENC (Widevine + PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=mpd-time-csf,encryption=cenc)`|
|CMAF(fmp4)|CENC (Widevine + PlayReady)|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(format=mpd-time-cmaf,encryption=cenc)`|

### <a name="smooth-streaming"></a>Smooth Streaming

Smooth Streaming-protokollet stöder följande behållare format och krypteringsscheman.

|Protocol|Behållare-format|Krypteringsschemat|
|---|---|---|
|fMP4|AES|`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(encryption=cbc)`|
|fMP4 | CENC (PlayReady) |`https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(encryption=cenc)`|

### <a name="browsers"></a>Webbläsare

Vanliga webbläsare har stöd för följande DRM-klienter:

|Webbläsare|Kryptering|
|---|---|
|Chrome|Widevine|
|Microsoft Edge, Internet Explorer 11|PlayReady|
|Firefox|Widevine|
|Opera|Widevine|
|Safari|FairPlay|

## <a name="aes-128-clear-key-vs-drm"></a>Rensa viktiga jämfört med AES-128 DRM

Kunder undrar ofta om de ska använda AES-kryptering eller ett DRM-system. Den viktigaste skillnaden mellan de två systemen är att med AES-kryptering innehållsnyckeln skickas till klienten via TLS så att nyckeln är krypterad under överföringen men utan ytterligare kryptering (”i klartext”). Därför kan nås av klientspelaren den nyckel som används för dekryptering av innehållet och kan visas i en nätverksspårning på klienten i oformaterad text. Rensa key AES-128-kryptering är lämplig för användningsfall där visningsprogrammet är en betrodd part (till exempel kryptering företagets videor som distribueras inom företaget kan ses av anställda).

DRM-system som PlayReady, Widevine och FairPlay får du en extra nivå av kryptering på den nyckel som används för dekryptering av innehållet jämfört med AES-128-klartextnyckel. Innehållsnyckeln krypteras till en nyckel som skyddas av DRM-körningen på ytterligare till valfri nivå transportkryptering som tillhandahålls av TLS. Dessutom hanteras dekrypteringen i en säker miljö på nivån operativsystemet där det är svårare för en obehörig användare för angrepp. DRM rekommenderas för användningsfall där visningsprogrammet inte kanske är en betrodd part och du behöver högsta säkerhetsnivån.

## <a name="dynamic-encryption-and-key-delivery-service"></a>Dynamisk kryptering och nyckelleveranstjänst

I Media Services v3, en innehållsnyckel är associerad med Strömningspositionerare (se [det här exemplet](protect-with-aes128.md)). Om du använder Media Services-nyckelleveranstjänst, kan du låta Azure Media Services generera innehållsnyckeln åt dig. Du bör skapa innehållsnyckeln själv om du använder du egna nyckelleveranstjänst, eller om du behöver hantera ett scenario med hög tillgänglighet, där du måste ha samma innehållsnyckeln i två datacenter.

När en dataströmmen har begärts av en spelare, använder Media Services den angivna nyckeln för att dynamiskt kryptera ditt innehåll med klartextnyckel för AES eller DRM-kryptering. Om du vill dekryptera dataströmmen begär spelaren nyckeln från Media Services nyckelleveranstjänst eller nyckelleveranstjänst som du har angett. När du beslutar om användaren har behörighet att hämta nyckel måste utvärderar tjänsten innehåll viktiga principen som du angav för nyckeln.

Media Services tillhandahåller en nyckelleveranstjänst för att leverera DRM (PlayReady, Widevine, FairPlay)-licenser och AES-nycklar till auktoriserade klienter. Du kan använda REST-API eller ett Media Services-klientbibliotek för att konfigurera principer för autentisering och auktorisering för dina licenser och nycklar.

### <a name="custom-key-and-license-acquisition-url"></a>Anpassad URL för anskaffning av nyckel och licens

Använd följande mallar om du vill ange en annan nyckel och licens för video-on-demand (inte Media Services). De två replaceable fälten i mallarna finns det så att du kan dela din strömmande princip över många tillgångar istället för att skapa en princip för strömning per tillgång. 

* EnvelopeEncryption.CustomKeyAcquisitionUrlTemplate - mall för URL: en för anpassad tjänst som levererar nycklar till slutanvändarens spelare. Krävs inte när du använder Azure Media Services för att utfärda nycklar. Mallen har stöd för replaceable token som uppdaterar tjänsten vid körning med värdet som är specifika för begäran.  Token värden som för närvarande stöds är {AlternativeMediaId}, som ersätts med värdet för StreamingLocatorId.AlternativeMediaId och {ContentKeyId}, som ersätts med värdet för identifieraren för den nyckel som begärts.
* StreamingPolicyPlayReadyConfiguration.CustomLicenseAcquisitionUrlTemplate - mall för URL: en för anpassad tjänst som levererar licenser till slutanvändare spelare. Krävs inte när du använder Azure Media Services för att utfärda licenser. Mallen har stöd för replaceable token som uppdaterar tjänsten vid körning med värdet som är specifika för begäran. Token värden som för närvarande stöds är {AlternativeMediaId}, som ersätts med värdet för StreamingLocatorId.AlternativeMediaId och {ContentKeyId}, som ersätts med värdet för identifieraren för den nyckel som begärts. 
* StreamingPolicyWidevineConfiguration.CustomLicenseAcquisitionUrlTemplate - Same as above, only for Widevine. 
* StreamingPolicyFairPlayConfiguration.CustomLicenseAcquisitionUrlTemplate - samma som ovan, endast för FairPlay.  

Exempel:

```csharp
streamingPolicy.EnvelopEncryption.customKeyAcquisitionUrlTemplate = "https://mykeyserver.hostname.com/envelopekey/{AlternativeMediaId}/{ContentKeyId}";
```

Den `ContentKeyId` har ett värde för nyckeln som begärs och `AlternativeMediaId` kan användas om du vill mappa begäran till en entitet på din sida. Till exempel den `AlternativeMediaId` kan användas för att hjälpa dig att granska behörigheter.

REST-exempel som använder anpassad nyckel och licens förvärv URL: er finns i [Streaming principer - skapa](https://docs.microsoft.com/rest/api/media/streamingpolicies/create)

## <a name="control-content-access"></a>Åtkomst till innehåll-kontroll

Du kan styra vem som har åtkomst till ditt innehåll genom att konfigurera principen för content-nyckel. Media Services stöder flera olika sätt att auktorisera användare som begär nycklar. Du måste konfigurera innehåll viktiga principen. Klienten (spelaren) måste uppfylla principen innan nyckeln kan levereras till klienten. Innehåll viktiga innehållsnyckeln kan ha **öppna** eller **token** begränsning. 

Med en tokenbegränsade innehåll viktiga princip skickas innehållsnyckeln endast till en klient som anger en giltig JSON Web Token (JWT) eller simple webbtoken (SWT) i nyckel/licens-begäran. Denna token måste utfärdas av en säkerhetstokentjänst (STS). Du kan använda Azure Active Directory som en STS eller distribuera en anpassad STS. STS måste konfigureras för att skapa en token som signerats med de angivna nyckeln och problemet anspråk som du angav i tokenbegränsningar konfigurationen. Media Services-nyckelleveranstjänst returnerar den begärda nyckel/licensen till klienten om token är giltig och att anspråken i token matchar de som konfigurerats för nyckel/licens.

När du konfigurerar den tokenbegränsade principen måste du ange primär verifieringsnyckel, utfärdare och målgrupp parametrar. Den primära Verifieringsnyckeln innehåller den nyckel som token signerats med. Utfärdaren är den säkra tokentjänst som utfärdar en token. Publik, vilket ibland kallas omfattning, beskriver syftet med denna token eller resursen token auktoriserar åtkomst till. Media Services-nyckelleveranstjänst verifierar att dessa värden i token matchar värden i mallen.

Kunder använder ofta en anpassad STS för att inkludera anpassade anspråk i token att välja mellan olika ContentKeyPolicyOptions med olika DRM-licens parametrar (en prenumerationslicens jämfört med en hyres-licens) eller att inkludera ett anspråk som representerar innehållsnyckeln identifierare för den nyckel som token ger åtkomst till.
 
## <a name="storage-side-encryption"></a>Kryptering för lagring på serversidan

Resurserna som ska krypteras av kryptering för lagring på serversidan för att skydda dina tillgångar i vila. I följande tabell visar hur kryptering för lagring på serversidan fungerar i Media Services v3:

|Krypteringsalternativet|Beskrivning|Media Services v3|
|---|---|---|
|Media Services-Lagringskryptering| AES-256-kryptering, viktiga hanteras av Media Services|Stöds inte<sup>(1)</sup>|
|[Kryptering av lagringstjänst för vilande Data](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)|Kryptering på serversidan som erbjuds av Azure Storage, nyckel hanteras av Azure eller av kunden|Stöds|
|[Storage Client Side Encryption](https://docs.microsoft.com/azure/storage/common/storage-client-side-encryption)|Client side encryption som erbjuds av Azure storage, nyckel som hanteras av kunden i Key Vault|Stöds inte|

<sup>1</sup> i Media Services v3 lagringskryptering (AES-256-kryptering) är bara stöds för bakåtkompatibilitet när dina tillgångar skapades med Media Services v2. Vilket innebär att v3 fungerar med befintliga lagring krypteras tillgångar, men tillåter inte skapandet av nya.

## <a name="troubleshoot"></a>Felsöka

Om du får den `MPE_ENC_ENCRYPTION_NOT_SET_IN_DELIVERY_POLICY` fel, kontrollera att du anger lämplig princip för direktuppspelning.

Om det uppstår fel som slutar med `_NOT_SPECIFIED_IN_URL`, se till att du anger krypteringsformat i URL: en. Till exempel `…/manifest(format=m3u8-cmaf,encryption=cbcs-aapl)`. Se [Streaming protokoll och krypteringstyper](#streaming-protocols-and-encryption-types).

## <a name="ask-questions-give-feedback-get-updates"></a>Ställ frågor, ge feedback, få uppdateringar

Kolla in den [Azure Media Services-community](media-services-community.md) artikeln olika sätt du kan ställa frågor, ge feedback och få uppdateringar om Media Services.

## <a name="next-steps"></a>Nästa steg

* [Skydda med AES-kryptering](protect-with-aes128.md)
* [Skydda med DRM](protect-with-drm.md)
* [Utforma multi-DRM innehållsskydd system med åtkomstkontroll](design-multi-drm-system-with-access-control.md)
* [Vanliga frågor och svar](frequently-asked-questions.md)

