---
title: 'Självstudier: Extrahera avbildningsinformation med hjälp av API för bildsökning i Bing och C#'
titleSuffix: Azure Cognitive Services
description: Använd den här artikeln om du vill skapa ett C#-program som extraherar avbildningsinformation med hjälp av API för bildsökning i Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: tutorial
ms.date: 05/15/2019
ms.author: aahi
ms.openlocfilehash: 995ccc8ffeb436526608803362cc361c74a75316
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65790039"
---
# <a name="tutorial-extract-image-details-using-the-bing-image-search-api-and-c"></a>Självstudier: Extrahera avbildningsinformation med hjälp av API för bildsökning i Bing och C#

Det finns flera [slutpunkter](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/image-search-endpoint) tillgängliga via API för bildsökning i Bing. Slutpunkten `/details` accepterar en POST-begäran med en avbildning och kan returnera olika typer av information om avbildningen. Det här C#-programmet skickar en avbildning med hjälp av detta API och visar informationen som returneras av Bing, vilket är JSON-objekt, som t.ex. följande:

![[JSON-resultat]](media/cognitive-services-bing-images-api/jsonResult.jpg)

I den här självstudien beskrivs hur du:

> [!div class="checklist"]
> * Använda `/details`-slutpunkten för Bildsökning i en `POST`-begäran
> * Ange huvuden för begäran
> * Specificera resultet med hjälp av URL-parametrar
> * Ladda upp avbildningsdata och skicka `POST`-begäran
> * Skriv ut JSON-resultat till konsolen

Källkoden till det här exemplet finns på [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/Tutorials/BingGetSimilarImages.cs).

## <a name="prerequisites"></a>Nödvändiga komponenter

* En utgåva av [Visual studio 2017 eller senare](https://visualstudio.microsoft.com/downloads/).

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="construct-an-image-details-search-request"></a>Skapa en sökbegäran för avbildningsinformation

Följande är `/details`-slutpunkten, vilken godkänner POST-förfrågningar med avbildningsdata i brödtexten.
```
https://api.cognitive.microsoft.com/bing/v7.0/images/details
```

När du skapar sökbegärans URL följer `modules`-parametern slutpunkten ovan och anger vilka typer av information som resultatet kommer att innehålla:

* `modules=All`
* `modules=RecognizedEntities` (personer eller platser som visas på bilden)

Specificera `modules=All` i POST-begäran för att få JSON-text som innehåller följande:

* `bestRepresentativeQuery` – en Bing-fråga som returnerar bilder som liknar den uppladdade bilden
* `detectedObjects` – objekt funna i bilden
* `image` – bildens metadata
* `imageInsightsToken` – en token för senare GET-förfrågningar som hämtar `RecognizedEntities` (personer eller platser som visas på bilden) från bilden.
* `imageTags` – taggar för bilden
* `pagesIncluding` – webbsidor som inkluderar bilden
* `relatedSearches` – sökningar som baseras på informationen i bilden.
* `visuallySimilarImages` – liknande bilder på webben.

Specificera `modules=RecognizedEntities` i POST-begäran för att bara få `imageInsightsToken`, som kan användas i en efterföljande GET-begäran för att identifiera personer eller platser på bilden.

## <a name="create-a-webclient-object-and-set-headers-for-the-api-request"></a>Skapa ett WebClient-objekt och ange huvuden för API-begäran

Skapa ett `WebClient`-objekt och konfigurera rubrikerna. Alla förfrågningar till API för sökning i Bing kräver en `Ocp-Apim-Subscription-Key`. En `POST`-begäran om att ladda upp en bild måste även specificera `ContentType: multipart/form-data`.

```javascript
WebClient client = new WebClient();
client.Headers["Ocp-Apim-Subscription-Key"] = accessKey;
client.Headers["ContentType"] = "multipart/form-data";
```

## <a name="upload-the-image-and-display-the-results"></a>Ladda upp bilden och visa resultaten

`WebClient`-klassens `UpLoadFile()`-metod formaterar data för `POST`-begäran, bl.a. för att formatera `RequestStream` och anropa `HttpWebRequest`.

Anropa `WebClient.UpLoadFile()` med `/details`-slutpunkten och den avbildningsfil som ska laddas upp. Använd JSON-svaret till att initiera en instans av `SearchResult`-strukturen och spara svaret.

```javascript        
const string uriBase = "https://api.cognitive.microsoft.com/bing/v7.0/images/details";
// The image to upload. Replace with your file and path.
const string imageFile = "your-image.jpg";
byte[] resp = client.UploadFile(uriBase + "?modules=All", imageFile);
var json = System.Text.Encoding.Default.GetString(resp);
// Create result object for return
var searchResult = new SearchResult()
{
    jsonResult = json,
    relevantHeaders = new Dictionary<String, String>()
};
```
Det här JSON-svaret kan sedan skrivas ut till konsolen.

## <a name="use-an-image-insights-token-in-a-request"></a>Använda en avbildningsinsiktstoken i en begäran

Om du vill använda `ImageInsightsToken` som returnerades med resultatet av en `POST`, så kan du lägga till den i en `GET` begäran. Exempel:

```
https://api.cognitive.microsoft.com/bing/v7.0/images/details?InsightsToken="bcid_A2C4BB81AA2C9EF8E049C5933C546449*ccid_osS7gaos*mid_BF7CC4FC4A882A3C3D56E644685BFF7B8BACEAF2
```

Om det finns identifierbara personer eller platser på bilden returnerar begäran information om dem.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Visa bilder och sökalternativ i en enda enskild webbapp ](tutorial-bing-image-search-single-page-app.md)

## <a name="see-also"></a>Se också

* [API-referens för bildsökning i Bing](//docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)
