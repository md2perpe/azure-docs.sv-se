---
title: Snabbstart för att skicka telemetri till Azure IoT Hub (C) | Microsoft Docs
description: I den här snabbstarten kör du två C-exempelprogram som skickar simulerad telemetri till en IoT-hubb, läser telemetrin från IoT-hubben och bearbetar den i molnet.
author: wesmc7777
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: quickstart
ms.custom: mvc
ms.date: 04/10/2019
ms.author: wesmc
ms.openlocfilehash: 92575f2fc8e6dbcfc5767a179ddf60df1bce0c83
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/17/2019
ms.locfileid: "65872624"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-and-read-it-with-a-back-end-application-c"></a>Snabbstart: Skicka telemetri från en enhet till en IoT-hubb och läsa den med ett serverdelsprogram (C)

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

IoT Hub är en Azure-tjänst som gör att du kan mata in stora mängder telemetri från IoT-enheter i molnet för lagring eller bearbetning. I den här snabbstarten skickar du telemetri från ett simulerat enhetsprogram via IoT Hub till ett serverdelsprogram för bearbetning.

Snabbstarten använder ett C-exempelprogram från [SDK för Azure IoT-enheter så att C](iot-hub-device-sdk-c-intro.md) kan skicka telemetri till en IoT-hubb. SDK:n för Azure IoT-enheter är skrivna i [ANSI C (C99)](https://wikipedia.org/wiki/C99) för portabilitet och bred plattformskompatibilitet. Innan du kör exempelkoden skapar du en IoT-hubb och registrerar den simulerade enheten med den hubben.

Den här artikeln är skriven för Windows men du kan genomföra den här snabbstarten även på Linux.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

* Installera [Visual Studio 2019](https://www.visualstudio.com/vs/) med den [”utveckling för stationära datorer med C++'](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/) arbetsbelastning aktiverat.
* Installera den senaste versionen av [Git](https://git-scm.com/download/).
* Kör följande kommando för att lägga till Microsoft Azure IoT-tillägget för Azure CLI i Cloud Shell-instans. IOT-tillägget lägger till IoT Hub, IoT Edge och IoT Device Provisioning-tjänsten (DPS) för vissa kommandon i Azure CLI.

   ```azurecli-interactive
   az extension add --name azure-cli-iot-ext
   ```

## <a name="prepare-the-development-environment"></a>Förbereda utvecklingsmiljön

Den här snabbstarten du ska använda den [Azure IoT-enhetens SDK för C](iot-hub-device-sdk-c-intro.md). 

Du kan använda SDK:n genom att installera paketen och biblioteken för följande miljöer:

* **Linux**: apt-get-paket är tillgängliga för Ubuntu 16.04 och 18.04 med hjälp av följande CPU-arkitekturer: amd64 arm64, armhf, och i386. Mer information finns i [Använda apt-get för att skapa ett klientprojekt för C-enheten i Ubuntu](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/ubuntu_apt-get_sample_setup.md).

* **mbed**: För utvecklare som skapar enhetsprogram på mbed-plattformen, har vi publicerat ett bibliotek och exempel som komma igång på några minuter med Azure IoT Hub. Mer information finns i [Använda mbed-biblioteket](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/readme.md#mbed).

* **Arduino**: Om du utvecklar på Arduino, kan du använda Azure IoT-biblioteket i Arduino IDE-biblioteket manager. Mer information finns i [Azure IoT Hub-biblioteket för Arduino](https://github.com/azure/azure-iot-arduino).

* **iOS**: SDK:n för IoT Hub-enheter är tillgänglig som CocoaPods för Mac- och iOS-enhetsutveckling. Mer information finns i [iOS-exempel för Microsoft Azure IoT](https://cocoapods.org/pods/AzureIoTHubClient).

Men i den här snabbstarten, förbereder du en utvecklingsmiljö som används för att klona och skapa den [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) från GitHub. SDK:n på GitHub inkluderar den exempelkod som används i den här snabbstarten. 

1. Ladda ned den [CMake-buildsystemet](https://cmake.org/download/).

    Det är viktigt som Visual Studio-krav (Visual Studio och ' skrivbordsutveckling med C++' arbetsbelastning) är installerade på din dator, **innan** startar den `CMake` installation. När förutsättningarna är uppfyllda och nedladdningen har verifierats installerar du CMake-byggesystemet.

2. Öppna en kommandotolk eller Git Bash-gränssnittet. Kör följande kommando för att klona [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) GitHub-lagringsplatsen:
    
    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive
    ```
    Den här åtgärden kan förväntas ta flera minuter att slutföra.


3. Skapa en `cmake`-underkatalog i rotkatalogen på git-lagringsplatsen och navigera till den mappen. 

    ```cmd/sh
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

4. Kör följande kommando som skapar en version av SDK:n som är specifik för din utvecklingsklientsplattform. En Visual Studio-lösning för den simulerade enheten genereras i `cmake`-katalogen. 

    ```cmd
    cmake ..
    ```
    
    Om `cmake` inte hittar din C++ kompilerare kan du få kompileringsfel när du kör kommandot ovan. Om det händer ska du försöka köra det här kommandot i [kommandotolken i Visual Studio](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs). 

    När bygget är klart ser de sista utdataraderna ut ungefär som följande utdata:

    ```cmd/sh
    $ cmake ..
    -- Building for: Visual Studio 15 2017
    -- Selecting Windows SDK version 10.0.16299.0 to target Windows 10.0.17134.
    -- The C compiler identification is MSVC 19.12.25835.0
    -- The CXX compiler identification is MSVC 19.12.25835.0

    ...

    -- Configuring done
    -- Generating done
    -- Build files have been written to: E:/IoT Testing/azure-iot-sdk-c/cmake
    ```


## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registrera en enhet

En enhet måste vara registrerad vid din IoT-hubb innan den kan ansluta. I det här avsnittet ska du använda Azure Cloud Shell med den [IoT-tillägget](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot?view=azure-cli-latest) att registrera en simulerad enhet.

1. Kör följande kommando i Azure Cloud Shell för att skapa enhetens identitet.

   **YourIoTHubName**: Ersätt platshållaren nedan med det namn du valde för din IoT-hubb.

   **MyCDevice**: Det här är det namn du angav för den registrerade enheten. Använd MyCDevice som et visas. Om du väljer ett annat namn för din enhet, måste du också använda det namnet i den här artikeln och uppdatera namnet i exempelprogrammen innan du kör dem på enheten.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyCDevice
    ```

2. Kör följande kommandon i Azure Cloud Shell för att hämta den _enhetsanslutningssträngen_ för enheten som du registrerade:

   **YourIoTHubName**: Ersätt platshållaren nedan med det namn du valde för din IoT-hubb.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyCDevice --output table
    ```

    Anteckna enhetsanslutningssträngen. Den ser ut ungefär som:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}`

    Du kommer att använda det här värdet senare i snabbstarten.

## <a name="send-simulated-telemetry"></a>Skicka simulerad telemetri

Det simulerade enhetsprogrammet ansluter till en enhetsspecifik slutpunkt på din IoT-hubb och skickar en sträng som simulerad telemetri.

1. Använd en textredigerare och öppna källfilen iothub_convenience_sample.c och granska exempelkoden för att skicka telemetri. Filen finns på följande plats:

    ```
    \azure-iot-sdk-c\iothub_client\samples\iothub_convenience_sample\iothub_convenience_sample.c
    ```

2. Hitta deklarationen för konstanten `connectionString`:

    ```C
    /* Paste in your device connection string  */
    static const char* connectionString = "[device connection string]";
    ```
    Ersätt värdet för konstanten `connectionString` med enhetens anslutningssträng som du antecknade tidigare. Spara dina ändringar i **iothub_convenience_sample.c**.

3. I ett lokalt terminalfönster navigerar du till projektkatalogen *iothub_convenience_sample* i CMake-katalogen som du skapade i Azure IoT C SDK.

    ```
    cd /azure-iot-sdk-c/cmake/iothub_client/samples/iothub_convenience_sample
    ```

4. Kör CMake i ditt lokala terminalfönster för att skapa exemplet med det uppdaterade `connectionString`-värdet:

    ```cmd/sh
    cmake --build . --target iothub_convenience_sample --config Debug
    ```

5. Kör det simulerade enhetsprogrammet genom att köra följande kommandon i det lokala terminalfönstret:

    ```cmd/sh
    Debug\iothub_convenience_sample.exe
    ```

    Följande skärmbild visar utdata när det simulerade enhetsprogrammet skickar telemetri till IoT-hubben:

    ![Kör den simulerade enheten](media/quickstart-send-telemetry-c/simulated-device-app.png)

## <a name="read-the-telemetry-from-your-hub"></a>Läsa telemetrin från din hubb


I det här avsnittet ska du använda Azure Cloud Shell med den [IoT-tillägget](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot?view=azure-cli-latest) att övervaka enhetsmeddelanden som skickas av den simulerade enheten.

1. Med Azure Cloud Shell kör du följande kommando för att ansluta och läsa meddelanden från IoT-hubben:

   **YourIoTHubName**: Ersätt platshållaren nedan med det namn du valde för din IoT-hubb.

    ```azurecli-interactive
    az iot hub monitor-events --hub-name YourIoTHubName --output table
    ```

    ![Läsa enhetsmeddelanden med hjälp av Azure CLI](media/quickstart-send-telemetry-c/read-device-to-cloud-messages-app.png)

    

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du konfigurerat en IoT-hubb, registrerat en enhet, skickat simulerad telemetri till hubben med hjälp av ett C-program och läst telemetrin från hubben med hjälp av Azure Cloud Shell.

Om du vill lära dig mer om hur du utvecklar med Azure IoT Hub C SDK kan du fortsätta till följande instruktioner:

> [!div class="nextstepaction"]
> [Utveckla med hjälp av Azure IoT Hub C SDK](iot-hub-devguide-develop-for-constrained-devices.md)