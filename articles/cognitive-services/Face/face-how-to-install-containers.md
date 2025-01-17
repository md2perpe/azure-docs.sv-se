---
title: Installera och köra containrar
titlesuffix: Face - Azure Cognitive Services
description: Hämta, installera och köra behållare för ansikte i den här genomgången självstudien.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: dapine
ms.openlocfilehash: 84960e82e25f4b6cc59324f17ce46de7f9f7ac23
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2019
ms.locfileid: "67704660"
---
# <a name="install-and-run-face-containers"></a>Installera och köra Ansikts-behållare

Azure Cognitive Services-ansikte ger en standardiserad Linux-behållare för Docker som identifierar ansikten i bilder. Den identifierar också attribut, bland annat ansiktslandmärken, till exempel och rörliga och ögon, kön, ålder och andra dator-förväntad ansiktsdrag. Förutom identifiering Kontrollera ansikte om två ansikten i samma bild eller olika bilder är samma med hjälp av ett förtroenderesultat. Ansikte kan också jämföra ansikten mot en databas om det redan finns ett likartade eller identiska ansikte. Det kan också sortera liknande ansikten i grupper med hjälp av delade visual egenskaper.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Du måste uppfylla följande krav innan du använder Ansikts-API-behållare.

|Krävs|Syfte|
|--|--|
|Docker-motorn| Docker-motorn måste vara installerad på en [värddatorn](#the-host-computer). Docker innehåller paket som konfigurerar Docker-miljön på [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/), och [Linux](https://docs.docker.com/engine/installation/#supported-platforms). Få en genomgång om grunderna för Docker och behållare finns i den [översikt över Docker](https://docs.docker.com/engine/docker-overview/).<br><br> Docker måste konfigureras för att tillåta behållarna för att ansluta till och skicka faktureringsdata till Azure. <br><br> På Windows, måste Docker även konfigureras för att stödja Linux-behållare.<br><br>|
|Liknar processen med Docker | Du behöver en grundläggande förståelse för Docker-begrepp som register, databaser, behållare och behållaravbildningar. Du måste också kunskaper om grundläggande `docker` kommandon.| 
|Azure `Cognitive Services` resurs |Om du vill använda behållaren måste du ha:<br><br>En Azure Cognitive Services-resurs och associerade krypteringsnyckeln och fakturering slutpunkten URI. Båda värdena är tillgängliga på den **översikt** och **nycklar** sidor för resursen. De måste starta behållaren. Lägg till den `face/v1.0` routning till slutpunkten URI, som visas i exemplet nedan BILLING_ENDPOINT_URI: <br><br>**{BILLING_KEY}** : Resursnyckeln<br><br>**{BILLING_ENDPOINT_URI}** : endpoint URI exempel är `https://westus.api.cognitive.microsoft.com/face/v1.0`|

## <a name="request-access-to-the-private-container-registry"></a>Begär åtkomst till privat behållarregister

[!INCLUDE [Request access to private container registry](../../../includes/cognitive-services-containers-request-access.md)]

### <a name="the-host-computer"></a>Värddatorn

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Behållarkrav och rekommendationer

I följande tabell beskrivs de minsta och rekommenderade processorkärnor och minne för att allokera för varje Ansikts-API-behållare.

| Container | Minimum | Rekommenderas | Transaktioner per sekund<br>(Minimum, maximum)|
|-----------|---------|-------------|--|
|Ansikte | 1 kärna, 2 GB minne | 1 kärna, 4 GB minne |10, 20|

* Varje kärna måste vara minst 2,6 GHz eller snabbare.
* Transaktioner per sekund (TPS).

Kärnor och minne som motsvarar den `--cpus` och `--memory` inställningar som används som en del av den `docker run` kommando.

## <a name="get-the-container-image-with-docker-pull"></a>Hämta behållaravbildningen med docker pull

Behållaravbildningar för Ansikts-API är tillgängliga. 

| Container | Lagringsplats |
|-----------|------------|
| Ansikte | `containerpreview.azurecr.io/microsoft/cognitive-services-face:latest` |

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-face-container"></a>Docker pull för Ansikts-behållare

```
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-face:latest
```

## <a name="use-the-container"></a>Använd behållare

När behållaren är på den [värddatorn](#the-host-computer), använder du följande process för att arbeta med behållaren.

1. [Kör behållaren](#run-the-container-with-docker-run) fakturering med de nödvändiga inställningarna. Mer [exempel](./face-resource-container-config.md#example-docker-run-commands) av den `docker run` kommandot är tillgängliga. 
1. [Fråga förutsägelse behållarslutpunkten](#query-the-containers-prediction-endpoint). 

## <a name="run-the-container-with-docker-run"></a>Kör behållaren med docker kör

Använd den [docker kör](https://docs.docker.com/engine/reference/commandline/run/) kommando för att köra någon av tre behållarna. Kommandot använder följande parametrar.

| Platshållare | Värde |
|-------------|-------|
|{BILLING_KEY} | Den här nyckeln används för att starta behållaren och är tillgängligt på Azure `Cognitive Services` **nycklar** sidan. |
|{BILLING_ENDPOINT_URI} | Fakturering slutpunkten URI-värdet är tillgänglig på Azure `Cognitive Services` **översikt** sidan. Ett exempel är `https://westus.api.cognitive.microsoft.com/face/v1.0`.|

Lägg till den `face/v1.0` routning till slutpunkten URI, som visas i föregående exempel BILLING_ENDPOINT_URI. 

Ersätt parametrarna med dina egna värden i följande `docker run` kommandoexemplet:

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-face \
Eula=accept \
Billing={BILLING_ENDPOINT_URI} \
ApiKey={BILLING_KEY}
```

Det här kommandot:

* Kör en ansikts-behållare från behållaravbildningen.
* Tilldelar en CPU-kärna och 4 GB minne.
* Exponerar TCP-port 5000 och allokerar pseudo TTY för behållaren.
* Tar automatiskt bort behållaren när avslutas. Behållaravbildningen finns kvar på värddatorn. 

Mer [exempel](./face-resource-container-config.md#example-docker-run-commands) av den `docker run` kommandot är tillgängliga. 

> [!IMPORTANT]
> Den `Eula`, `Billing`, och `ApiKey` alternativ måste anges för att köra behållaren eller behållaren inte startar. Mer information finns i [fakturering](#billing).

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]


## <a name="query-the-containers-prediction-endpoint"></a>Fråga behållarslutpunkten förutsägelse

Behållaren innehåller REST-baserade frågan förutsägelse endpoint API: er. 

Använd värden `https://localhost:5000`, för behållaren API: er.


<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>Stoppa behållaren

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Felsökning

Om du kör behållaren med ett utgående [montera](./face-resource-container-config.md#mount-settings) och loggning är aktiverat, behållaren genererar loggfiler som är användbara för att felsöka problem som inträffar när du startar eller kör behållaren. 


## <a name="billing"></a>Fakturering

Ansikts-API-behållare skicka faktureringsinformation till Azure med hjälp av en Ansikts-API-resurs på ditt Azure-konto. 

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Mer information om alternativen finns i [konfigurera behållare](./face-resource-container-config.md).

<!--blogs/samples/video coures -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Sammanfattning

I den här artikeln beskrivs begrepp och arbetsflöde för hur du hämtar, installerar och kör Ansikts-API-behållare. Sammanfattningsvis:

* Ansikts-API innehåller tre Linux-behållare för Docker som ger extrahering av diskussionsämne, språkidentifiering och attitydanalys.
* Behållaravbildningar laddas ned från Azure Container Registry.
* Behållaravbildningar som körs i Docker.
* Du kan använda REST-API: et eller SDK: N för att anropa åtgärder i Ansikts-API-behållare genom att ange värden URI: N för behållaren.
* Du måste ange faktureringsinformation när du skapa en instans av en behållare.

> [!IMPORTANT]
> Cognitive Services-behållare är inte licensierad för att köra inte är ansluten till Azure för att mäta. Kunder måste aktivera behållarna för att kommunicera faktureringsinformation med tjänsten Avläsning av programvara vid alla tidpunkter. Cognitive Services-behållare Skicka inte kundinformation, till exempel bild eller text som analyseras, till Microsoft.

## <a name="next-steps"></a>Nästa steg

* Konfigurationsinställningar, se [konfigurera behållare](face-resource-container-config.md).
* Läs mer om hur du upptäcka och identifiera ansikten i [ansikte översikt](Overview.md).
* Information om de metoder som stöds av behållaren finns i den [Ansikts-API](//westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).
* Om du vill använda fler Cognitive Services-behållare, se [Cognitive Services-behållare](../cognitive-services-container-support.md).
