---
title: Installera och köra behållare – formuläret Igenkännande
titleSuffix: Azure Cognitive Services
description: Lär dig hur du använder formigenkänningscontainern för att parsa formulär- och tabelldata.
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: dapine
ms.openlocfilehash: f88cfb1d74897abddeaea6aaec9d7f7b1960be6e
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2019
ms.locfileid: "67704247"
---
# <a name="install-and-run-form-recognizer-containers"></a>Installera och köra formuläret Igenkännande behållare

Azure formuläret Igenkännande gäller maskininlärningsteknik för att identifiera och extrahera nyckel / värde-par och tabeller från formulär. Det kopplar värden och tabellposter med nyckel / värde-par och matar ut strukturerade data som innehåller relationerna i den ursprungliga filen. 

För att minska komplexiteten och enkelt integrera en anpassad formuläret Igenkännande-modell i arbetsflödesprocessen automation eller andra program, kan du anropa modellen med hjälp av ett enkelt REST API. Endast fem formuläret dokument (eller ett tomt formulär och två ifyllda formulär) behövs, så du kan få resultat snabbt, korrekt, och skräddarsys för din specifikt innehåll. Det krävs inga tung manuella åtgärder eller omfattande data science-expertis. Och det krävs ingen märkning av data eller data anteckning.

|Funktion|Funktioner|
|-|-|
|Formigenkänning| <li>Bearbetar PDF, PNG och JPG-filer<li>Träna anpassade modeller med minst 5 former av samma layout <li>Extraherar nyckel / värde-par och tabellinformation <li>Använder funktionen Azure Cognitive Services datorn Vision API identifiera Text för att identifiera och extrahera utskrivna text från bilder i formulär<li>Kräver inte anteckning eller etikettering|

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Innan du använder formuläret Igenkännande behållare måste du uppfylla följande krav:

|Obligatoriskt|Syfte|
|--|--|
|Docker-motorn| Du behöver Docker-motorn installerad på en [värddatorn](#the-host-computer). Docker innehåller paket som konfigurerar Docker-miljön på [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/), och [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Få en genomgång om grunderna för Docker och behållare finns i den [översikt över Docker](https://docs.docker.com/engine/docker-overview/).<br><br> Docker måste konfigureras för att tillåta behållarna för att ansluta till och skicka faktureringsdata till Azure. <br><br> På Windows konfigureras Docker också för att stödja Linux-behållare.<br><br>|
|Liknar processen med Docker | Du bör ha grundläggande kunskaper om Docker-begrepp som register, databaser, behållare, behållaravbildningar och kunskaper om grundläggande `docker` kommandon.|
|Azure CLI| Installera den [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) på värden.|
|Datorresurs för API för visuellt innehåll| Om du vill bearbeta skannade dokument och bilder, behöver du en resurs för visuellt innehåll. Du kan komma åt funktionen Identifiera Text som antingen en Azure-resurs (REST API eller SDK) eller en *cognitive services-identifiera – fulltext* [behållare](../Computer-vision/computer-vision-how-to-install-containers.md##get-the-container-image-with-docker-pull). Vanliga fakturering avgiften gäller. <br><br>Skicka både nyckel och fakturering slutpunkter för visuellt innehåll (Azure-molnet eller Cognitive Services-behållare). Använd den här nyckeln och fakturering slutpunkten som {COMPUTER_VISION_API_KEY} och {COMPUTER_VISION_BILLING_ENDPOINT_URI}.<br><br> Om du använder den *cognitive services-identifiera – fulltext* behållaren, se till att:<br><br>Din nyckel för visuellt innehåll för formuläret Igenkännande behållaren är nyckeln som anges i visuellt `docker run` kommandot för den *cognitive services-identifiera – fulltext* behållare.<br>Fakturering slutpunkten är behållarens slutpunkt (till exempel `https://localhost:5000`). Om du använder både visuellt behållare och formuläret Igenkännande behållare tillsammans på samma värd, de kan inte båda vara igång med standardporten *5000*.  |  
|Formuläret Igenkännande resurs |Om du vill använda de här behållarna, måste du ha:<br><br>En _formuläret Igenkännande_ Azure-resurs att hämta associerade krypteringsnyckeln och fakturering slutpunkt URI. Båda värdena är tillgängliga på Azure portal **formuläret Igenkännande översikt** och **formuläret Igenkännande översikt nycklar** sidor och båda värdena krävs för att starta behållaren.<br><br>**{BILLING_KEY}** : Resursnyckeln<br><br>**{BILLING_ENDPOINT_URI}** : endpoint URI exempel är `https://westus.api.cognitive.microsoft.com/forms/v1.0`| 

## <a name="request-access-to-the-container-registry"></a>Begär åtkomst till behållarregistret

Du måste först slutför och skicka den [Cognitive Services formuläret Igenkännande behållare åtkomst till formulär för](https://aka.ms/FormRecognizerRequestAccess) att begära åtkomst till behållaren. Gör det också loggar du in för visuellt innehåll. Du behöver inte registrera dig för formuläret visuellt separat. 

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

## <a name="the-host-computer"></a>Värddatorn

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Behållarkrav och rekommendationer

De minsta och rekommenderade CPU-kärnor och minne för att allokera för varje behållare i formuläret Igenkännande beskrivs i följande tabell:

| Container | Minimum | Rekommenderas |
|-----------|---------|-------------|
|cognitive-services-form-recognizer | 2 kärnor, 4 GB minne | 4 kärnor, 8 GB minne |

* Varje kärna måste vara minst 2,6 GHz (gigahertz) eller snabbare.
* TPS - transaktioner per sekund
* Kärnor och minne som motsvarar den `--cpus` och `--memory` inställningar som används som en del av den `docker run` kommando.

> [!Note]
> De minsta och rekommenderade värdena baseras på Docker gränser och *inte* värden machine resurser.

## <a name="get-the-container-image-with-the-docker-pull-command"></a>Hämta behållaravbildningen med kommandot docker pull

Behållaravbildningar för formuläret Igenkännande är tillgängliga i följande lagringsplats:

| Container | Lagringsplats |
|-----------|------------|
| cognitive-services-form-recognizer | `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer:latest` |

Om du planerar att använda den `cognitive-services-recognize-text` [behållare](../Computer-vision/computer-vision-how-to-install-containers.md##get-the-container-image-with-docker-pull), istället för formatet Igenkännande-tjänsten, kontrollera att du använder den `docker pull` med rätt behållarens namn: 

```
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text:latest
```

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-form-recognizer-container"></a>Docker pull för formuläret Igenkännande behållaren

#### <a name="form-recognizer"></a>Formigenkänning

Om du vill hämta formuläret Igenkännande-behållare, använder du följande kommando:

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer:latest
```

## <a name="how-to-use-the-container"></a>Hur du använder behållare

När behållaren är på den [värddatorn](#the-host-computer), använder du följande process för att arbeta med behållaren.

1. [Kör behållaren](#run-the-container-by-using-the-docker-run-command), med fakturering inställningar krävs men används inte. Mer [exempel](form-recognizer-container-configuration.md#example-docker-run-commands) av den `docker run` kommandot är tillgängliga.
1. [Fråga förutsägelse behållarslutpunkten](#query-the-containers-prediction-endpoint).

## <a name="run-the-container-by-using-the-docker-run-command"></a>Kör behållaren med hjälp av docker Kör kommando

Använd den [docker kör](https://docs.docker.com/engine/reference/commandline/run/) kommando för att köra någon av tre behållarna. Kommandot använder följande parametrar:

| Platshållare | Värde |
|-------------|-------|
|{BILLING_KEY} | Den här nyckeln används för att starta behållaren. Det är tillgängligt på Azure portal **formuläret Igenkännande nycklar** sidan.  |
|{BILLING_ENDPOINT_URI} | Fakturering slutpunkten URI-värdet är tillgänglig på Azure portal **formuläret Igenkännande översikt** sidan.|
|{COMPUTER_VISION_API_KEY}| Nyckeln är tillgänglig på Azure portal **datorn Vision API-nycklar** sidan.|
|{COMPUTER_VISION_ENDPOINT_URI}|Fakturering slutpunkten. Om du använder en molnbaserad visuellt resurs URI-värdet är tillgängliga på Azure portal **datorn översikt över API för visuellt innehåll** sidan. Om du använder en `cognitive-services-recognize-text` behållare, använda fakturering slutpunkts-URL som skickades till behållaren i den `docker run` kommando.|

Ersätt parametrarna med dina egna värden i följande exempel `docker run` kommando.

### <a name="form-recognizer"></a>Formigenkänning

```bash
docker run --rm -it -p 5000:5000 --memory 8g --cpus 2 \
--mount type=bind,source=c:\input,target=/input  \
--mount type=bind,source=c:\output,target=/output \
containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY} \
FormRecognizer:ComputerVisionApiKey={COMPUTER_VISION_API_KEY} \
FormRecognizer:ComputerVisionEndpointUri={COMPUTER_VISION_ENDPOINT_URI}
```

Det här kommandot:

* Kör en behållare för formuläret Igenkännande från behållaravbildningen.
* Allokerar 2 CPU-kärnor och 8 gigabyte (GB) minne.
* Exponerar TCP-port 5000 och allokerar en pseudo-TTY för behållaren.
* Tar automatiskt bort behållaren när avslutas. Behållaravbildningen finns kvar på värddatorn.
* Monterar en /input och en/Output-volym till behållaren.

[!INCLUDE [Running multiple containers on the same host H2](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]

### <a name="run-separate-containers-as-separate-docker-run-commands"></a>Kör separat behållare som separata docker kör kommandon

För en identifierare för formuläret och Text Igenkännande kombination som ligger lokalt på samma värd, använder du följande två exempel Docker CLI-kommandon:

Kör den första behållaren på port 5000. 

```bash 
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
--mount type=bind,source=c:\input,target=/input  \
--mount type=bind,source=c:\output,target=/output \
containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
FormRecognizer:ComputerVisionApiKey={COMPUTER_VISION_API_KEY} \
FormRecognizer:ComputerVisionEndpointUri={COMPUTER_VISION_ENDPOINT_URI}
```

Kör andra behållare på port 5001.


```bash 
docker run --rm -it -p 5001:5000 --memory 4g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text \
Eula=accept \
Billing={COMPUTER_VISION_ENDPOINT_URI} \
ApiKey={COMPUTER_VISION_API_KEY}
```
Varje efterföljande behållare ska vara på en annan port. 

### <a name="run-separate-containers-with-docker-compose"></a>Kör separat behållare med Docker Compose

Identifierare för formuläret och Text Igenkännande kombinationen som finns lokalt på samma värd, finns i följande exempel Docker Compose YAML-fil. Text Igenkännande `{COMPUTER_VISION_API_KEY}` måste vara samma för både den `formrecognizer` och `ocr` behållare. Den `{COMPUTER_VISION_ENDPOINT_URI}` används bara i den `ocr` behållare, eftersom den `formrecognizer` behållare använder den `ocr` namn och port. 

```docker
version: '3.3'
services:   
  ocr:
    image: "containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text"
    deploy:
      resources:
        limits:
          cpus: '2'
          memory: 8g
        reservations:
          cpus: '1'
          memory: 4g
    environment:
      eula: accept
      billing: "{COMPUTER_VISION_ENDPOINT_URI}"
      apikey: {COMPUTER_VISION_API_KEY}  

  formrecognizer:
    image: "containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer"
    deploy:
      resources:
        limits:
          cpus: '2'
          memory: 8g
        reservations:
          cpus: '1'
          memory: 4g
    environment:
      eula: accept
      billing: "{BILLING_ENDPOINT_URI}"
      apikey: {BILLING_KEY}
      FormRecognizer__ComputerVisionApiKey: {COMPUTER_VISION_API_KEY}
      FormRecognizer__ComputerVisionEndpointUri: "http://ocr:5000"
      FormRecognizer__SyncProcessTaskCancelLimitInSecs: 75
    links:
      - ocr
    volumes:
      - type: bind
        source: c:\output
        target: /output
      - type: bind
        source: c:\input
        target: /input
    ports:
      - "5000:5000"  
```


> [!IMPORTANT]
> Den `Eula`, `Billing`, och `ApiKey`, samt de `FormRecognizer:ComputerVisionApiKey` och `FormRecognizer:ComputerVisionEndpointUri` alternativ, måste anges för att köra behållaren, i annat fall startar inte behållaren. Mer information finns i [fakturering](#billing).

## <a name="query-the-containers-prediction-endpoint"></a>Fråga behållarslutpunkten förutsägelse

|Container|Slutpunkt|
|--|--|
|form-recognizer|http://localhost:5000


### <a name="form-recognizer"></a>Formigenkänning

Behållaren innehåller websocket-baserade frågan endpoint API: er, som du kommer åt via [formuläret Igenkännande services SDK-dokumentation](https://docs.microsoft.com/azure/cognitive-services/form-recognizer/).

Som standard använder SDK: N för formuläret Igenkännande onlinetjänsterna. Om du vill använda behållaren som du behöver ändra initieringsmetoden. Se följande exempel.

#### <a name="for-c"></a>FörC#

Byter från att använda det här anropet för initiering av Azure-molnet:

```C#
var config = FormRecognizerConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

på det här anropet som använder behållarslutpunkten:

```C#
var config = FormRecognizerConfig.FromEndpoint("ws://localhost:5000/formrecognizer/v1.0-preview/custom", "YourSubscriptionKey");
```

#### <a name="for-python"></a>För Python

Byter från att använda det här anropet för initiering av Azure-molnet:

```python
formrecognizer_config = formrecognizersdk.FormRecognizerConfig(subscription=formrecognizer_key, region=service_region)
```

på det här anropet som använder behållarslutpunkten:

```python
formrecognizer_config = formrecognizersdk.FormRecognizerConfig(subscription=formrecognizer_key, endpoint="ws://localhost:5000/formrecognizer/v1.0-preview/custom"
```

### <a name="form-recognizer"></a>Formigenkänning

Behållaren innehåller REST-slutpunkt API: er, som finns på den [formuläret Igenkännande API](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api/operations/AnalyzeWithCustomModel) sidan.


[!INCLUDE [Validate container is running - Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]


## <a name="stop-the-container"></a>Stoppa behållaren

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Felsökning

När du kör behållaren behållaren använder **stdout** och **stderr** utdata information som är användbara vid felsökning av problem som kan uppstå när du startar eller kör behållaren.

## <a name="billing"></a>Fakturering

Formuläret Igenkännande behållarna skicka faktureringsinformation till Azure med hjälp av en _formuläret Igenkännande_ resurs på ditt Azure-konto.

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Mer information om alternativen finns i [konfigurera behållare](form-recognizer-container-configuration.md).

<!--blogs/samples/video courses -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Sammanfattning

I den här artikeln beskrivs begrepp och arbetsflöde för att ladda ned, installera och formuläret Igenkännande behållare som körs. Sammanfattningsvis:

* Formuläret Igenkännande tillhandahåller en Linux-behållare för Docker.
* Behållaravbildningar laddas ned från det privata behållarregistret i Azure.
* Behållaravbildningar som körs i Docker.
* Du kan använda REST API eller REST-SDK för att anropa åtgärder i formuläret Igenkännande behållare genom att ange värden URI: N för behållaren.
* Du måste ange faktureringsinformationen när du skapa en instans av en behållare.

> [!IMPORTANT]
>  Cognitive Services-behållare är inte licensierad för att köra inte är ansluten till Azure för att mäta. Kunder måste du aktivera behållarna för att kommunicera faktureringsinformation med tjänsten Avläsning av programvara vid alla tidpunkter. Cognitive Services-behållare Skicka inte kunddata (t.ex, bild eller text som analyseras) till Microsoft.

## <a name="next-steps"></a>Nästa steg

* Granska [konfigurera behållare](form-recognizer-container-configuration.md) för konfigurationsinställningar.
* Använder mer [Cognitive Services-behållare](../cognitive-services-container-support.md).
