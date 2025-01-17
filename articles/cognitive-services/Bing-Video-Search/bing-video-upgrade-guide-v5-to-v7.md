---
title: Uppgradera Bing API för Videosökning v5 till v7
titlesuffix: Azure Cognitive Services
description: Identifierar delarna av programmet som du behöver uppdatera om du vill använda version 7.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: scottwhi
ms.openlocfilehash: 32dc928147af8fbb3c84bdb76e50cee4fdabe17d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66386637"
---
# <a name="video-search-api-upgrade-guide"></a>Video Uppgraderingsguide för API för webbsökning

Den här uppgraderingshandboken identifierar ändringar mellan version 5 och version 7 av den Videosökning i Bing. Använd den här guiden hjälper dig att identifiera delarna av programmet som du behöver uppdatera om du vill använda version 7.

## <a name="breaking-changes"></a>Icke-bakåtkompatibla ändringar

### <a name="endpoints"></a>Slutpunkter

- Versionsnumret för den slutpunkt som har ändrats från v5 till v7. Till exempel `https://api.cognitive.microsoft.com/bing/v7.0/videos/search`.

### <a name="error-response-objects-and-error-codes"></a>Fel svarsobjekt och felkoder

- Alla misslyckade begäranden ska nu innehålla en `ErrorResponse` objekt i svarstexten.

- Lagt till följande fält i den `Error` objekt.  
  - `subCode`&mdash;Partitionerar felkoden i diskreta buckets, om möjligt
  - `moreDetails`&mdash;Mer information om felet som beskrivs i den `message` fält
   

- Ersatt v5-felkoder med följande möjliga `code` och `subCode` värden.

|Kod|SubCode|Beskrivning
|-|-|-
|ServerError|UnexpectedError<br/>ResourceError<br/>NotImplemented|Bing returnerar ServerError när något av de underordnade kod inträffar. Svaret innehåller de här felen om HTTP-statuskoden är 500.
|InvalidRequest|ParameterMissing<br/>ParameterInvalidValue<br/>HttpNotAllowed<br/>Blockerad|Bing returnerar InvalidRequest när någon del av begäran inte är giltig. Till exempel en obligatorisk parameter saknas eller ett parametervärde är inte giltig.<br/><br/>Om felet är ParameterMissing eller ParameterInvalidValue, är HTTP-statuskod 400.<br/><br/>Om felet är HttpNotAllowed, HTTP-statuskod 410.
|RateLimitExceeded||Bing returnerar RateLimitExceeded varje gång du överskrider din frågor per sekund (QPS) eller frågor per månad (QPM) kvot.<br/><br/>Bing returnerar HTTP-statuskod 429 om du har överskridit Indexlagring och 403 om du har överskridit QPM.
|InvalidAuthorization|AuthorizationMissing<br/>AuthorizationRedundancy|Bing returnerar InvalidAuthorization när Bing inte kan autentisera anroparen. Till exempel den `Ocp-Apim-Subscription-Key` huvud saknas eller prenumerationsnyckeln är inte giltig.<br/><br/>Redundans inträffar om du anger mer än en autentiseringsmetod.<br/><br/>Om felet är InvalidAuthorization, är HTTP-statuskod 401.
|InsufficientAuthorization|AuthorizationDisabled<br/>AuthorizationExpired|Bing returnerar InsufficientAuthorization när anroparen inte har behörighet att komma åt resursen. Detta kan inträffa om prenumerationsnyckeln har inaktiverats eller har upphört att gälla. <br/><br/>Om felet är InsufficientAuthorization, är HTTP-statuskod 403.

- Följande mappar tidigare felkoder till de nya koderna. Om du har gått ett beroende på v5 felkoder, måste din kod uppdateras också.

|Versionskod 5|Version 7 code.subCode
|-|-
|RequestParameterMissing|InvalidRequest.ParameterMissing
RequestParameterInvalidValue|InvalidRequest.ParameterInvalidValue
ResourceAccessDenied|InsufficientAuthorization
ExceededVolume|RateLimitExceeded
ExceededQpsLimit|RateLimitExceeded
Inaktiverad|InsufficientAuthorization.AuthorizationDisabled
UnexpectedError|ServerError.UnexpectedError
DataSourceErrors|ServerError.ResourceError
AuthorizationMissing|InvalidAuthorization.AuthorizationMissing
HttpNotAllowed|InvalidRequest.HttpNotAllowed
UserAgentMissing|InvalidRequest.ParameterMissing
NotImplemented|ServerError.NotImplemented
InvalidAuthorization|InvalidAuthorization
InvalidAuthorizationMethod|InvalidAuthorization
MultipleAuthorizationMethod|InvalidAuthorization.AuthorizationRedundancy
ExpiredAuthorizationToken|InsufficientAuthorization.AuthorizationExpired
InsufficientScope|InsufficientAuthorization
Blockerad|InvalidRequest.Blocked

### <a name="query-parameters"></a>Frågeparametrar

- Byta namn på den `modulesRequested` frågeparameter till [moduler](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#modulesrequested).  

### <a name="object-changes"></a>Objekt ändras

- Byta namn på den `nextOffsetAddCount` i [videor](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#videos) till `nextOffset`. Hur du använder förskjutningen har också ändrats. Tidigare, anger du den [offset](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#offset) frågeparameter till den `nextOffset` värdet plus den föregående intervallförskjutning plus antal videor i resultatet. Nu kan du helt enkelt ställa in den `offset` frågeparameter till den `nextOffset` värde.  
  
- Ändra datatypen för den `relatedVideos` från `Video[]` till [VideosModule](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#videosmodule) (se [VideoDetails](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#videodetails)).

