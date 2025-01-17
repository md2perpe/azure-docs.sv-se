---
title: Filter och Azure Media Services dynamiska manifest | Microsoft Docs
description: Det här avsnittet beskriver hur du skapar filter så att klienten kan använda dem till stream vissa delar av en dataström. Media Services skapar dynamiska manifest för att uppnå den här selektiva strömning.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 06/11/2019
ms.author: juliako
ms.openlocfilehash: ab07b87d724f2006b6b5c0e4f472140f92230dea
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67080405"
---
# <a name="pre-filtering-manifests-with-dynamic-packager"></a>Före filtrering manifest med dynamiska Paketeraren

När du ska leverera innehåll till enheter för direktuppspelning med anpassningsbar bithastighet måste ofta du publicera flera versioner av ett manifest target specifika enhetsfunktioner eller tillgänglig nätverksbandbredd. Den [dynamisk Paketeraren](dynamic-packaging-overview.md) kan du ange filter som kan filtrera ut specifika codec upplösningar och bithastigheter för utdata ljud spåra kombinationer på direkt tar bort behovet av att skapa flera kopior. Du behöver bara publicera en ny URL med en specifik uppsättning filter som konfigurerats för att dina målenheter (iOS, Android, SmartTV eller webbläsare) och nätverksfunktioner (hög bandbredd, Mobil eller med låg bandbredd-scenario). I det här fallet klienter kan manipulera strömning av innehåll via frågesträngen (genom att ange tillgänglig [tillgången filter eller kontofilter](filters-concept.md)) och Använd filter för att stream vissa delar av en dataström.

Vissa leverans scenarier kräver att du gör att en kund är inte har tillgång till specifika spår. Till exempel kanske du inte vill publicera ett manifest som innehåller HD spårar till en specifik prenumerations-nivå. Eller så kan du ta bort specifika med anpassningsbar bithastighet (ABR) spårar för att minska kostnaden för leverans till en specifik enhet som inte skulle ha nytta av ytterligare spår. I det här fallet kunde du koppla en lista med förinställda filter med din [Strömningspositionerare](streaming-locators-concept.md) skapats. Klienter kan inte ändra hur innehåll strömmas i det här fallet, den definieras av den **Strömningspositionerare**.

Du kan kombinera filtrering genom att ange [filter på Strömningspositionerare](filters-concept.md#associating-filters-with-streaming-locator) + ytterligare specifika filter som klienten anger i URL: en. Detta kan vara användbart att begränsa ytterligare spårar som metadata eller händelse strömmar, ljud språk eller beskrivande ljudspår. 

Den här möjligheten att ange olika filter på din stream ger en kraftfull **dynamiska Manifest** manipulering av lösning för att rikta in flera Användningsscenarier för din målenheter. Det här avsnittet förklarar begrepp som rör **dynamiska manifest** och ger exempel på scenarier där du kanske vill använda den här funktionen.

> [!NOTE]
> Dynamiska manifest ändras inte tillgången och standard-manifest för tillgången. 
> 

## <a name="manifests-overview"></a>Manifest-översikt

Media Services stöder HLS, MPEG DASH, Smooth Streaming-protokoll. Som en del av [dynamisk paketering](dynamic-packaging-overview.md), strömmande klientmanifesten (HLS Master Playlist, DASH Media Presentation beskrivning (MPD) och Smooth Streaming) genereras dynamiskt baserat på väljaren format i URL: en. Se leveransprotokoll i [i det här avsnittet](dynamic-packaging-overview.md#delivery-protocols). 

### <a name="get-and-examine-manifest-files"></a>Hämta och granska manifestfiler

Du anger en lista med filtervillkor spåra egenskap baserat som spårar för din dataström (Live eller Video på begäran) ska tas med i dynamiskt skapade manifest. Om du vill hämta och granska egenskaperna för spåren behövs för att läsa Smooth Streaming-manifestet först.

Den [överföra, koda och strömma filer med .NET](stream-files-tutorial-with-api.md) kursen visar hur du skapar en strömmande URL: er med .NET (visas den [att skapa URL: er](stream-files-tutorial-with-api.md#get-streaming-urls) avsnittet). Om du kör appen, någon av webbadresser pekar Smooth Streaming-manifest: `https://amsaccount-usw22.streaming.media.azure.net/00000000-0000-0000-0000-0000000000000/ignite.ism/manifest`.<br/>Kopiera och klistra in Webbadressen i adressfältet i en webbläsare. Filen laddas ned. Du kan öppna den i en textredigerare som du önskar.

REST-exempel finns i [överföra, koda och strömma filer med REST](stream-files-tutorial-with-rest.md#list-paths-and-build-streaming-urls).

### <a name="monitor-the-bitrate-of-a-video-stream"></a>Övervaka bithastigheten av en videoström

Du kan använda den [Demonstrationssida för Azure Media Player](https://aka.ms/amp) att övervaka bithastigheten av en videoström. Demo-sidan visar diagnostik information i den **diagnostik** fliken:

![Azure Media Player-diagnostik][amp_diagnostics]
 
### <a name="examples-urls-with-filters-in-query-string"></a>Exempel: URL: er med filter i frågesträngen

Filter kan tillämpas på protokoll för direktuppspelning med anpassningsbar bithastighet: HLS, MPEG-DASH och Smooth Streaming. I följande tabell visas några exempel på URL: er med filter:

|Protocol|Exempel|
|---|---|
|HLS|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|MPEG DASH|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|Smooth Streaming|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(filter=myAssetFilter)`|

## <a name="rendition-filtering"></a>Återgivningsfiltrering

Du kan välja att koda din tillgång till flera kodning profiler (H.264 baslinje, H.264 hög, AACL, AACH, Dolby Digital Plus) och flera olika bithastigheter kvalitet. Dock stöder inte alla klientenheter din tillgångs-profiler och bithastigheter för utdata. Äldre Android-enheter stöder till exempel bara H.264 baslinje + AACL. Skicka högre bithastighet till en enhet, vilket inte kan hämta fördelarna, slösar nätverksbandbredd och alla beräkningar som enheten. Sådana enheter måste avkoda alla angivna information endast för Nedskalning för visning.

Med dynamiska Manifest kan du skapa enhetsprofiler som mobila enheter, konsolen HD/SD osv och inkludera spårar och egenskaper som du vill ska vara en del av varje profil.

![Återgivning filtrering exempel][renditions2]

I följande exempel har en kodare använts för att koda en mezzanine tillgång till sju ISO MP4s video återgivningar (från 180p 1080p). Den kodade tillgången kan vara [dynamiskt paketerade](dynamic-packaging-overview.md) på någon av de följande protokollen med direktuppspelning: HLS, MPEG DASH och Smooth.  Längst ned i diagrammet visas HLS-manifest för tillgången utan filter (den innehåller alla sju återgivningar).  HLS-manifest som ett filter med namnet ”ott” användes visas i nedre vänstra hörnet. Filtret ”ott” anger för att ta bort alla bithastigheter för utdata nedan 1 Mbit/s, vilket resulterade i kvalitetsnivå längst ned två tas bort i svaret. I nederkant högra visas HLS manifestet som ett filter med namnet ”mobil” användes. Filtret ”mobil” anger för att ta bort återgivningar där den är större än 720p, vilket resulterade i två 1080p återgivningar tas bort.

![Återgivningsfiltrering][renditions1]

## <a name="removing-language-tracks"></a>Ta bort språkspår
Dina tillgångar kan innehålla flera ljud språk, till exempel engelska, spanska, franska, osv. Vanligtvis Player SDK-chefer standard ljudspår val och tillgängliga ljud spårar per användarens val. Är det svårt att utveckla sådana Player SDK: er, den kräver olika implementeringar över enhetsspecifika spelarramverk. Dessutom begränsas Player API: er på vissa plattformar och inkluderar inte ljudspår funktion där användare inte kan markera eller ändra standard ljudspår. Du kan styra beteendet genom att skapa filter som endast innehåller önskade ljud språk med tillgången filter.

![Språk spårar filtrering][language_filter]

## <a name="trimming-start-of-an-asset"></a>Ta bort början av en tillgång
I de flesta liveuppspelningshändelser kör operatörer vissa tester innan den faktiska händelsen. De kan till exempel innehålla en bakgrundsbild så här innan händelsen: ”Inleds tillfälligt”. Om programmet arkivering, test- och statiska data också arkiveras och ingår i presentationen. Den här informationen bör dock inte att visas för klienterna. Du kan skapa en starttidsfilter och ta bort oönskade data från manifestet med dynamiska Manifest.

![Ta bort start][trim_filter]

## <a name="creating-subclips-views-from-a-live-archive"></a>Skapa underklipp (vyer) från en live-arkivet
Många direktsändningar är långvariga och live-arkivet kan innehålla flera händelser. När livehändelsen är slut, kanske sändningsföretag vill dela upp live-arkivet till logiska programmet startas och stoppa sekvenser. Därefter publicera separat dessa virtuella program utan efter bearbetning av live-arkivet och inte skapa separata resurser (som inte får fördelen med de befintliga cachelagrade fragment i de CDN-nät). Exempel på sådana virtuella program är kvartal football eller basket spel, innings i basket eller enskilda händelser för alla sport-program.

Med dynamiska Manifest kan du skapa filter med start-och sluttider och skapa virtuella vyer över dina live-arkivet. 

![Underklipp filter][subclip_filter]

Filtrerade tillgång:

![Skidåkning][skiing]

## <a name="adjusting-presentation-window-dvr"></a>Justera presentationsfönstret (DVR)
Azure Media Services erbjuder för närvarande, cirkulär Arkiv där varaktigheten kan konfigureras mellan 5 minuter – 25: e timme. Manifest filtrering kan användas för att skapa en löpande DVR-perioden över upp arkivet, utan att ta bort media. Det finns många scenarier där sändningsföretag vill tillhandahålla en begränsad DVR-perioden för att flytta med live edge och samtidigt hålla ett större arkivering fönster. En broadcast kanske vill använda de data som ligger utanför fönstret DVR att markera klipp eller kan de vilja ange olika DVR-fönster för olika enheter. De flesta av de mobila enheterna hantera inte exempelvis stora DVR windows (du kan ha en 2-minut DVR-perioden för mobila enheter och en timme för stationära klienter).

![DVR-perioden][dvr_filter]

## <a name="adjusting-livebackoff-live-position"></a>Justera LiveBackoff (direktsända positionen)
Manifest filtrering kan användas för att ta bort några sekunder från live gränsen för en live. Filtrering kan sändningsföretag se presentationen på förhandsversion publikationen punkt och skapa annonsen infogas punkter innan användarna får den dataström (backas upp av med 30 sekunder). Programleverantörer kan sedan skicka dessa annonser till sina klientramverk i tid för dem att mottagna och bearbeta information innan annons-möjlighet.

Förutom annons-support användas inställningen LiveBackoff för att justera positionen för användarna så att när klienter avviker och når live edge kan de fortfarande få fragment från servern i stället för att få ett HTTP 404 eller 412 fel.

![livebackoff_filter][livebackoff_filter]

## <a name="combining-multiple-rules-in-a-single-filter"></a>Kombinera flera regler i ett enda filter
Du kan kombinera flera filtreringsregler i ett enda filter. Som exempel kan du definiera ”intervallet regeln” ta bort pekdatorer från en live-arkivet och även filtrerat bort tillgängliga bithastighet. När du använder flera filtreringsregler är slutresultatet skärningspunkten för alla regler.

![flera regler][multiple-rules]

## <a name="combining-multiple-filters-filter-composition"></a>Kombinera flera filter (filter sammansättning)

Du kan också kombinera flera filter i en enskild URL. 

Följande scenario visar varför du kanske vill kombinera filter:

1. Du måste filtrera din video egenskaper för mobila enheter som Android- eller iPAD (för att begränsa video egenskaper). Om du vill ta bort oönskade egenskaper, skapar du en kontofilter passar enhetsprofilerna. Kontofilter kan användas för alla dina tillgångar under samma media services-konto utan ytterligare association. 
2. Du bör också att trimma start- och tidpunkten för en tillgång. Om du vill göra detta kan du skapa ett filter för tillgången och ange starttid/sluttid. 
3. Du vill kombinera båda dessa filter (utan kombination, du måste lägga till kvalitet filtrering för ta bort filtret, vilket gör det svårare filter användning).

Om du vill kombinera filter, måste du ange filternamn i manifestet/spelningslistan URL med semikolonavgränsad lista. Vi antar att du har ett filter med namnet *MyMobileDevice* som filtrerar egenskaper och det finns en annan med namnet *MyStartTime* att ställa in en viss starttid. Du kan kombinera dem så här:

Du kan kombinera upp till tre filter. 

Mer information finns i [detta](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/) blogg.

## <a name="considerations-and-limitations"></a>Överväganden och begränsningar

- Värdena för **forceEndTimestamp**, **presentationWindowDuration**, och **liveBackoffDuration** ska inte anges för en VoD-filtret. De används endast för live-filtret scenarier. 
- Dynamiska manifest körs i GOP gränser (nyckel ramar) därför trimmar har GOP precision. 
- Du kan använda samma filternamnet för konto- och Tillgångsnivå filter. Tillgången filter har högre prioritet och åsidosätter kontofilter.
- Om du uppdaterar ett filter, kan det ta upp till 2 minuter för Strömningsslutpunkt att uppdatera reglerna. Om innehållet behandlades använda filter och cachelagras i proxyservrar och CDN cacheminnen, kan uppdaterar de här filtren resultera i player-fel. Det rekommenderas att rensa cacheminnet när du har uppdaterat filtret. Överväg att använda ett annat filter om det här alternativet inte är möjligt.
- Kunder måste hämta manifestet och parsa exakta startTimestamp och skala manuellt.
    
    - Att fastställa egenskaperna för spår i en tillgång [få och granska manifestfilen](#get-and-examine-manifest-files).
    - Formeln för att ange filtret tillgången tidsstämpel egenskaper: <br/>startTimestamp = &lt;starttid i manifestet&gt; +  &lt;förväntat filter starttiden på några sekunder&gt;* tidsskalan

## <a name="next-steps"></a>Nästa steg

I följande artiklar visar hur du skapar filter programmässigt.  

- [Skapa filter med REST API: er](filters-dynamic-manifest-rest-howto.md)
- [Skapa filter med .NET](filters-dynamic-manifest-dotnet-howto.md)
- [Skapa filter med CLI](filters-dynamic-manifest-cli-howto.md)

[renditions1]: ./media/filters-dynamic-manifest-overview/media-services-rendition-filter.png
[renditions2]: ./media/filters-dynamic-manifest-overview/media-services-rendition-filter2.png

[rendered_subclip]: ./media/filters-dynamic-manifests/media-services-rendered-subclip.png
[timeline_trim_event]: ./media/filters-dynamic-manifests/media-services-timeline-trim-event.png
[timeline_trim_subclip]: ./media/filters-dynamic-manifests/media-services-timeline-trim-subclip.png

[multiple-rules]:./media/filters-dynamic-manifest-overview/media-services-multiple-rules-filters.png

[subclip_filter]: ./media/filters-dynamic-manifest-overview/media-services-subclips-filter.png
[trim_event]: ./media/filters-dynamic-manifests/media-services-timeline-trim-event.png
[trim_subclip]: ./media/filters-dynamic-manifests/media-services-timeline-trim-subclip.png
[trim_filter]: ./media/filters-dynamic-manifest-overview/media-services-trim-filter.png
[redered_subclip]: ./media/filters-dynamic-manifests/media-services-rendered-subclip.png
[livebackoff_filter]: ./media/filters-dynamic-manifest-overview/media-services-livebackoff-filter.png
[language_filter]: ./media/filters-dynamic-manifest-overview/media-services-language-filter.png
[dvr_filter]: ./media/filters-dynamic-manifest-overview/media-services-dvr-filter.png
[skiing]: ./media/filters-dynamic-manifest-overview/media-services-skiing.png
[amp_diagnostics]: ./media/filters-dynamic-manifest-overview/amp_diagnostics.png
