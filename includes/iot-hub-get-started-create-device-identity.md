---
title: ta med fil
description: ta med fil
services: iot-hub
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 09/07/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: b2bce9788006a564def9bd8c1375a85dc4184b67
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66814751"
---
## <a name="create-a-device-identity"></a>Skapa en enhetsidentitet

I det här avsnittet använder du Azure CLI för att skapa en enhetsidentitet i den här självstudien. Azure CLI installeras automatiskt i den [Azure Cloud Shell](~/articles/cloud-shell/overview.md), eller så kan du [installera Azure CLI lokalt](/cli/azure/install-azure-cli). Enhets-ID är skiftlägeskänsliga.

1. Kör följande kommando i kommandoradsverktyget miljön där du använder Azure CLI för att installera IoT-tillägget:

    ```cmd/sh
    az extension add --name azure-cli-iot-ext
    ```

1. Om du kör Azure CLI lokalt, använder du följande kommando för att logga in på ditt Azure-konto (om du använder Cloud Shell, du har loggat in automatiskt och du behöver inte köra det här kommandot):

    ```cmd/sh
    az login
    ```

1. Slutligen skapar en ny enhetsidentitet som kallas `myDeviceId` och hämta enhetens anslutningssträng med följande kommandon:

    ```cmd/sh
    az iot hub device-identity create --device-id myDeviceId --hub-name {Your IoT Hub name}
    az iot hub device-identity show-connection-string --device-id myDeviceId --hub-name {Your IoT Hub name} -o table
    ```

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

Anteckna enhetsanslutningssträngen från resultatet. Den här enhetens anslutningssträng används av app för enheter för att ansluta till din IoT-hubb som en enhet.

<!-- images and links -->
