---
title: Liveuppspelning begrepp i Azure Media Services - Live-händelser och Live utdata | Microsoft Docs
description: Den här artikeln ger en översikt över direktsänd strömning begrepp i Azure Media Services v3.
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
ms.date: 06/19/2019
ms.author: juliako
ms.openlocfilehash: f26467a250314fa8a6fe401f4ec1d6a999b6bb4d
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2019
ms.locfileid: "67296215"
---
# <a name="live-events-and-live-outputs"></a>Livehändelser och liveresultat

Azure Media Services kan du leverera händelser till dina kunder på Azure-molnet. Om du vill konfigurera din strömmade direktsändningar av evenemang i Media Services v3, måste du förstå begrepp som diskuteras i den här artikeln.

> [!TIP]
> För kunder som migrerar från Media Services v2 API: er, den **direktsänd händelse** entitet ersätter **kanal** i v2 och **Live utdata** ersätter **programmet**.

## <a name="live-events"></a>Livehändelser

[Livehändelser](https://docs.microsoft.com/rest/api/media/liveevents) ansvarar för att mata in och bearbeta direktsända videofeeds. När du skapar en Live-händelse skapas en primär och sekundär slutpunkt för indata som du kan använda för att skicka en direktsänd signal från en fjärransluten kodare. Remote livekodare skickar bidraget till som indata slutpunkten med hjälp av antingen den [RTMP](https://www.adobe.com/devnet/rtmp.html) eller [Smooth Streaming](https://msdn.microsoft.com/library/ff469518.aspx) (fragmenterad MP4) Ange protokollet. För RTMP-infogningsprotokollet, innehållet kan skickas i klartext (`rtmp://`) eller på ett säkert sätt krypterad under överföringen (`rtmps://`). För Smooth Streaming-inmatningsprotokollet, URL-scheman som stöds är `http://` eller `https://`.  

## <a name="live-event-types"></a>Live händelsetyper

En [direktsänd händelse](https://docs.microsoft.com/rest/api/media/liveevents) kan vara något av två typer: direkt och live encoding. Typerna anges under skapas med hjälp av [LiveEventEncodingType](https://docs.microsoft.com/rest/api/media/liveevents/create#liveeventencodingtype):

* **LiveEventEncodingType.None** -en lokal livekodare skickar flera bithastighet. De infogade strömmarna passerar genom Live-händelsen utan vidare bearbetning. 
* **LiveEventEncodingType.Standard** – med en lokal livekodare skickar en enda bithastighet till direktsänd händelse och Media Services skapar strömmar med flera bithastigheter. Om flödet bidrag är av 720p eller högre upplösning på **Default720p** förinställning kommer koda av 6 lösning/bithastighet värdepar.
* **LiveEventEncodingType.Premium1080p** – med en lokal livekodare skickar en enda bithastighet till direktsänd händelse och Media Services skapar strömmar med flera bithastigheter. Förinställningen Default1080p anger utdata uppsättning lösning/bithastigheter för utdata-par. 

### <a name="pass-through"></a>Direkt

![direkt](./media/live-streaming/pass-through.svg)

När du använder en genomströmning av en **livehändelse** förlitar du dig på din lokala livekodare för att generera en videoström för flera bithastigheter och skicka den som bidragsflöde till livehändelsen (med RTMP eller fragmenterat MP4-protokoll). Livehändelsen passerar sedan via inkommande videoströmmar utan vidare bearbetning. Sådana en direkt direktsänd händelse är optimerad för tidskrävande direktsändningar eller 24 x 365 linjär liveuppspelning. När du skapar den här typen av livehändelse kan du ange Ingen (LiveEventEncodingType.None).

Du kan skicka bidragsflödet med upplösningar på upp till 4K och i en bildfrekvens på 60 bilder/sekund, med antingen videocodecen H.264/AVC eller H.265/HEVC och ljudcodecen AAC (AAC-LC, HE-AACv1 eller HE-AACv2).  Mer information finns i artikeln med en [jämförelse av livehändelser](live-event-types-comparison.md).

> [!NOTE]
> Genomströmningsmetoden är det mest ekonomiska sättet för liveuppspelning när du utför flera händelser under en längre tid och du redan har investerat i lokala kodare. Se [prisuppgifter](https://azure.microsoft.com/pricing/details/media-services/).
> 

Se ett .NET-kodexempel i [MediaV3LiveApp](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/Program.cs#L126).

### <a name="live-encoding"></a>Live Encoding  

![livekodning](./media/live-streaming/live-encoding.svg)

När du använder livekodning med Media Services konfigurerar du din lokala livekodare till att skicka en video med enkel bithastighet som bidragsflöde livehändelsen (med hjälp av RTMP eller protokollet Fragmented-Mp4). Du skulle ställa in en direktsänd händelse så att den kodar den inkommande, enkel bithastigheten, strömma till en [flera video bithastighet](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming), och tillgängliggör utdata för leverans till enheter via protokoll, till exempel MPEG-DASH, HLS eller spela upp och jämna ut Direktuppspelning.

När du använder direktsänd kodning, du kan skicka bidraget feed endast på lösningar till 1080p-lösning på en bildfrekvens av 30 bilder/sekund, med H.264/AVC video-codec och AAC (AAC-LC, HE-AACv1 eller HE-AACv2) ljudcodec. Observera att direkt Live-händelser har stöd för lösningar upp till 4 K på 60 bildrutor per sekund. Mer information finns i artikeln med en [jämförelse av livehändelser](live-event-types-comparison.md).

Upplösningar och bithastigheter för utdata i utdata från livekodaren bestäms av förinställningen. Om du använder en **Standard** live encoder (LiveEventEncodingType.Standard), kommer *Default720p* förinställning anger en uppsättning med 6 lösning/bitars rate-par, som kommer från 720 p på 3.5Mbps ned 192 p med 200 kbit/s. Annars, om du använder en **Premium1080p** live encoder (LiveEventEncodingType.Premium1080p), kommer *Default1080p* förinställning anger en uppsättning med 6 lösning/bitars rate-par, som kommer från 1080 p på 3.5Mbps ned 180 p med 200 kbit/s. Mer information finns i [Systemförinställningar](live-event-types-comparison.md#system-presets).

> [!NOTE]
> Om du vill anpassa live förinställningen för kodningen kan öppna ett supportärende via Azure-portalen. Du bör ange den önskade tabellen med upplösning och bithastigheter. Kontrollera att det finns ett lager på 720p (om du begär en förinställning för en Standard live-kodare) eller på 1080p (om du begär en förinställning för en Premium1080p live-kodare) och högst 6 lager.

## <a name="live-event-creation-options"></a>Live-alternativ för skapande av händelse

När du skapar en Live-händelse, kan du ange följande alternativ:

* Strömningsprotokollet för livehändelsen (för närvarande stöds protokollen RTMP och Smooth Streaming).<br/>Du kan inte ändra protokollalternativ när livehändelsen eller dess associerade liveutdata körs. Om du behöver olika protokoll får du skapa separata livehändelser för varje strömningsprotokoll.  
* IP-begränsningar på infogning och förhandsgranskning. Du kan definiera de IP-adresser som får mata in en video till den här livehändelsen. Tillåtna IP-adresser kan anges som en enskild IP-adress (till exempel 10.0.0.1), ett IP-intervall med IP-adress och en CIDR-nätmask (till exempel 10.0.0.1/22) eller ett IP-intervall med en IP-adress och en prickad decimalnätmask (till exempel 10.0.0.1(255.255.252.0)).<br/>Om inga IP-adresser har angetts och det saknas regeldefinitioner, kommer ingen IP-adress att tillåtas. Skapa en regel för att tillåta IP-adresser och ange 0.0.0.0/0.<br/>IP-adresserna måste vara i något av följande format: IpV4-adress med 4 siffror, CIDR-adressintervall.
* När du skapar händelsen, kan du ange att den ska startas automatiskt. <br/>När autostart är angett till true (sant) startas live-händelsen efter skapandet. Faktureringen startar när Live-händelsen börjar köras. Du måste explicit anropa Stop på livehändelseresursen för att stoppa ytterligare fakturering. Du kan också starta händelsen när du är redo att börja direktuppspelning. 

    Mer information finns i [livehändelsetillstånd och fakturering](live-event-states-billing.md).

## <a name="live-event-ingest-urls"></a>Live-händelse mata in URL: er

När livehändelsen har skapats kan du få infognings-URL:er som du tillhandahåller till den lokala livekodaren. Livekodaren använder dessa URL:er för att mata in en direktsänd dataström. Mer information finns i [rekommenderas en lokal livekodare](recommended-on-premises-live-encoders.md). 

Du kan antingen använda icke-anpassade eller anpassade URL:er. 

> [!NOTE] 
> För en inmatning ange URL: en ska vara förutsägande, ”anpassad”-läge.

* Icke-anpassad URL

    Icke-anpassad URL är standardläget i Media Services v3. Du eventuellt hämta livehändelsen snabbt, men inmatnings-URL är endast känd när livehändelsen startas. URL:en ändras om du stoppar/startar livehändelsen. <br/>Icke-anpassad är användbart i situationer när en slutanvändare vill strömma med en app där appen vill göra en livehändelse omedelbart, och det inte är något problem att ha en dynamisk inmatnings-URL.
    
    Om ett klientprogram inte behöver förväg Generera en inmatning URL innan Live-händelse skapas, kan bara Media Services för att automatiskt skapa åtkomst-Token för live-händelse.
* Anpassad URL

    Anpassat läge föredras av stora mediesändningsföretag som använder maskinvarusändningskodare och inte vill konfigurera om sina kodare när de startar livehändelsen. De vill ha en förutsägande infognings-URL som inte ändras med tiden.
    
    Om du vill ange det här läget du ställer in `vanityUrl` till `true` vid tidpunkten för skapandet (standardvärdet är `false`). Du måste också skicka din egen åtkomst-token (`LiveEventInput.accessToken`) vid tidpunkten för skapandet. Du kan ange token värde för att undvika en slumpmässig token i URL: en. Åtkomst-token måste vara en giltig GUID-sträng (med eller utan bindestrecken). När läget har angetts kan inte uppdateras.

    Åtkomst-token måste vara unikt i ditt datacenter. Om programmet behöver för att använda en anpassad URL, bör du alltid vill skapa en ny GUID-instans för ditt åtkomst-token (istället för att alla befintliga GUID). 

    Använd följande API: er för att aktivera anpassad URL och ange åtkomst-token till ett giltigt GUID (till exempel `"accessToken": "1fce2e4b-fb15-4718-8adc-68c6eb4c26a7"`).  
    
    |Språk|Aktivera anpassad URL|Ange åtkomst-token|
    |---|---|---|
    |REST|[properties.vanityUrl](https://docs.microsoft.com/rest/api/media/liveevents/create#liveevent)|[LiveEventInput.accessToken](https://docs.microsoft.com/rest/api/media/liveevents/create#liveeventinput)|
    |CLI|[--vanity-url](https://docs.microsoft.com/cli/azure/ams/live-event?view=azure-cli-latest#az-ams-live-event-create)|[--access-token](https://docs.microsoft.com/cli/azure/ams/live-event?view=azure-cli-latest#optional-parameters)|
    |.NET|[LiveEvent.VanityUrl](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.liveevent.vanityurl?view=azure-dotnet#Microsoft_Azure_Management_Media_Models_LiveEvent_VanityUrl)|[LiveEventInput.AccessToken](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.liveeventinput.accesstoken?view=azure-dotnet#Microsoft_Azure_Management_Media_Models_LiveEventInput_AccessToken)|
    
### <a name="live-ingest-url-naming-rules"></a>Liveinmatning regler för namngivning av URL: en

* Den *slumpmässiga* strängen nedan är ett 128-bitars hexadecimalt tal (som består av 32 tecken mellan 0 och 9 och a–f).
* *din åtkomsttoken* -giltiga GUID-strängen som du anger när du använder läget för anpassad. Till exempel `"1fce2e4b-fb15-4718-8adc-68c6eb4c26a7"`.
* *Livesända* -anger stream-namnet för en viss anslutning. Stream namn-värde läggs vanligtvis av livekodaren som du använder. Du kan konfigurera livekodaren för att använda ett namn som beskriver anslutningen, till exempel: ”video1_audio1”, ”video2_audio1”, ”strömma”.

#### <a name="non-vanity-url"></a>Icke-anpassad URL

##### <a name="rtmp"></a>RTMP

`rtmp://<random 128bit hex string>.channel.media.azure.net:1935/live/<auto-generated access token>/<stream name>`<br/>
`rtmp://<random 128bit hex string>.channel.media.azure.net:1936/live/<auto-generated access token>/<stream name>`<br/>
`rtmps://<random 128bit hex string>.channel.media.azure.net:2935/live/<auto-generated access token>/<stream name>`<br/>
`rtmps://<random 128bit hex string>.channel.media.azure.net:2936/live/<auto-generated access token>/<stream name>`<br/>

##### <a name="smooth-streaming"></a>Smooth Streaming

`http://<random 128bit hex string>.channel.media.azure.net/<auto-generated access token>/ingest.isml/streams(<stream name>)`<br/>
`https://<random 128bit hex string>.channel.media.azure.net/<auto-generated access token>/ingest.isml/streams(<stream name>)`<br/>

#### <a name="vanity-url"></a>Anpassad URL

##### <a name="rtmp"></a>RTMP

`rtmp://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:1935/live/<your access token>/<stream name>`<br/>
`rtmp://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:1936/live/<your access token>/<stream name>`<br/>
`rtmps://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:2935/live/<your access token>/<stream name>`<br/>
`rtmps://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:2936/live/<your access token>/<stream name>`<br/>

##### <a name="smooth-streaming"></a>Smooth Streaming

`http://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net/<your access token>/ingest.isml/streams(<stream name>)`<br/>
`https://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net/<your access token>/ingest.isml/streams(<stream name>)`<br/>

## <a name="live-event-preview-url"></a>Live-händelse förhandsgransknings-URL

När den **direktsänd händelse** börjar ta emot bidrag feed, dess förhandsgranskningsslutpunkten kan använda för att förhandsgranska och validera att du får den direktsända dataströmmen innan du publicerar ytterligare. När du har kontrollerat att förhandsgranska dataströmmen är bra, du kan använda Live-händelse så att den direktsända dataströmmen analysleverans via en eller flera (färdiga) **Strömningsslutpunkter**. För att åstadkomma detta måste du skapa ett nytt [Live utdata](https://docs.microsoft.com/rest/api/media/liveoutputs) på den **direktsänd händelse**. 

> [!IMPORTANT]
> Kontrollera att videon flödar till förhandsgransknings-URL innan du fortsätter!

## <a name="live-event-long-running-operations"></a>Live-händelse långvariga åtgärder

Mer information finns i [långvariga åtgärder](media-services-apis-overview.md#long-running-operations)

## <a name="live-outputs"></a>Liveutdata

När du har dataströmmen väl flödar till Live-händelse, kan du påbörja strömningshändelsen genom att skapa en [tillgången](https://docs.microsoft.com/rest/api/media/assets), [Live utdata](https://docs.microsoft.com/rest/api/media/liveoutputs), och [Strömningspositionerare](https://docs.microsoft.com/rest/api/media/streaminglocators). Live utdata kommer arkiverar dataströmmen och gör den tillgänglig för visning via den [Strömningsslutpunkt](https://docs.microsoft.com/rest/api/media/streamingendpoints).  

> [!NOTE]
> Liveutdata startar när de skapas och avbryts när de tas bort. När du tar bort liveutdata tar du inte bort den underliggande tillgången och innehållet i tillgången. 

Förhållandet mellan en **direktsänd händelse** och dess **Live utdata** är liknar traditionella TV-sändning, där en kanal (**direktsänd händelse**) representerar en konstant strömma video och en inspelning (**Live utdata**) är begränsad till en viss tidpunkt-segment (till exempel kvällar nyheter från 18:30:00 till 19:00:00). Du kan spela TV med hjälp av en DVR-spelare – den motsvarande funktionen i livehändelser hanteras via egenskapen **ArchiveWindowLength**. Det är en ISO 8601-timespan varaktighet (till exempel PTHH:MM:SS) som anger kapaciteten för DVR och kan anges från minst 3 minuter till högst 25 timmar.

Den **Live utdata** objekt som liknar en inspelningar som ska fånga upp och registrera den direktsända dataströmmen till en tillgång i Media Services-kontot. Inspelat innehåll ska behållas i Azure Storage-kontot som hör till ditt konto till den behållare som definieras av tillgången resursen. Den **Live utdata** också låter dig styra vissa egenskaper för den utgående direktsända dataströmmen, till exempel hur mycket av dataströmmen som sparas i arkivet inspelningen (till exempel kapaciteten för moln DVR) och om huruvida användarna kan starta Titta på den direktsända dataströmmen. Arkivet på disken är en cirkulär Arkiv ”fönster” som innehåller endast mängden innehåll som har angetts i den **archiveWindowLength** egenskapen för den **Live utdata**. Innehållet som ligger utanför det här fönstret tas automatiskt bort från storage-behållare och kan inte återställas. Du kan skapa flera **Live utdata** (upp till tre maximalt) på en **direktsänd händelse** med olika Arkiv längd och inställningar.  

Om du har publicerat den **Live utdata**'s **tillgången** med hjälp av en **Strömningspositionerare**, **direktsänd händelse** (upp till DVR fönstret längd) kommer fortsätta att vara synliga tills positionerare för direktuppspelning slutar att gälla eller tas bort, beroende på vilket som inträffar först.

Mer information finns i [med molnbaserade DVR](live-event-cloud-dvr.md).

## <a name="ask-questions-give-feedback-get-updates"></a>Ställ frågor, ge feedback, få uppdateringar

Kolla in den [Azure Media Services-community](media-services-community.md) artikeln olika sätt du kan ställa frågor, ge feedback och få uppdateringar om Media Services.

## <a name="next-steps"></a>Nästa steg

[Live direktuppspelning självstudien](stream-live-tutorial-with-api.md)
