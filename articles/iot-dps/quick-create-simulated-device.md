---
title: Etablera en simulerad TPM-enhet på Azure IoT Hub med C | Microsoft Docs
description: Den här snabbstarten använder enskilda registreringar. I den här snabbstarten skapade och etablerade du en simulerad TPM-enhet med C-enhets-SDK för Azure IoT Hub Device Provisioning Service.
author: wesmc7777
ms.author: wesmc
ms.date: 04/10/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: ca6914967d855123c70bf746a9d68d2e045e76d9
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65908684"
---
# <a name="quickstart-provision-a-simulated-tpm-device-using-the-azure-iot-c-sdk"></a>Snabbstart: Etablera en simulerad TPM-enhet med Azure IoT C SDK

[!INCLUDE [iot-dps-selector-quick-create-simulated-device-tpm](../../includes/iot-dps-selector-quick-create-simulated-device-tpm.md)]

I den här snabbstarten får lära dig att skapa och köra en TPM-enhetssimulator (Trusted Platform Module) på en Windows-utvecklingsdator. Du ansluter denna simulerade enhet till en IoT-hubb med hjälp av en instans av enhetsetableringstjänst. Exempelkoden från [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) används för att registrera enheten med en instans av enhetsetableringstjänst och simulera en startsekvens för enheten.

Om du inte känner till processen för automatisk etablering bör du gå igenom [Begrepp inom automatisk etablering](concepts-auto-provisioning.md). Se även till att slutföra stegen i [Set up IoT Hub Device Provisioning Service with the Azure portal](./quick-setup-auto-provision.md) (Konfigurera IoT Hub-enhetsetableringstjänsten med Azure-portalen) innan du fortsätter med den här snabbstarten. 

Azure IoT Device Provisioning Service stöder två typer av registreringar:
- [Registreringsgrupper](concepts-service.md#enrollment-group): Används för att registrera flera relaterade enheter.
- [Enskilda registreringar](concepts-service.md#individual-enrollment): Används för att registrera en enskild enhet.

Den här artikeln visar enskilda registreringar.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Nödvändiga komponenter

* [Visual Studio](https://visualstudio.microsoft.com/vs/) 2015 eller senare med den [”utveckling för stationära datorer med C++'](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/) arbetsbelastning aktiverat.
* Senaste versionen av [Git](https://git-scm.com/download/) installerad.


<a id="setupdevbox"></a>

## <a name="prepare-a-development-environment-for-the-azure-iot-c-sdk"></a>Förbereda en utvecklingsmiljö för Azure IoT C SDK

I det här avsnittet förbereder du en utvecklingsmiljö som används för att skapa [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) och [TPM](https://docs.microsoft.com/windows/device-security/tpm/trusted-platform-module-overview)-enhetssimulatorexemplet.

1. Ladda ned den [CMake-buildsystemet](https://cmake.org/download/).

    Det är viktigt att förutsättningarna för Visual Studio (Visual Studio och arbetsbelastningen ”Desktop development with C++” (Skrivbordsutveckling med C++)) är installerade på datorn **innan** installationen av `CMake` påbörjas. När förutsättningarna är uppfyllda och nedladdningen har verifierats installerar du CMake-byggesystemet.

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

## <a name="build-the-sdk-and-run-the-tpm-device-simulator"></a>Skapa SDK och köra TPM-enhetssimulatorn

I det här avsnittet skapar du Azure IoT C SDK, som innehåller exempelkoden för TPM-enhetssimulatorn. Det här exemplet tillhandahåller en TPM-[attesteringsmekanism](concepts-security.md#attestation-mechanism) via SAS-tokenautentisering (signatur för delad åtkomst).

1. Från `cmake`-underkatalogen som du skapade i azure-iot-sdk-c git-lagringsplatsen kör du följande kommando för att skapa exemplet. En Visual Studio-lösning för den simulerade enheten genereras också av det här kompileringskommandot.

    ```cmd/sh
    cmake -Duse_prov_client:BOOL=ON -Duse_tpm_simulator:BOOL=ON ..
    ```

    Om `cmake` inte hittar din C++-kompilerare kan du få kompileringsfel när du kör kommandot ovan. Om det händer ska du försöka köra det här kommandot i [kommandotolken i Visual Studio](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs). 

    När bygget är klart ser de sista utdataraderna ut ungefär som följande utdata:

    ```cmd/sh
    $ cmake -Duse_prov_client:BOOL=ON -Duse_tpm_simulator:BOOL=ON ..
    -- Building for: Visual Studio 15 2017
    -- Selecting Windows SDK version 10.0.16299.0 to target Windows 10.0.17134.
    -- The C compiler identification is MSVC 19.12.25835.0
    -- The CXX compiler identification is MSVC 19.12.25835.0

    ...

    -- Configuring done
    -- Generating done
    -- Build files have been written to: E:/IoT Testing/azure-iot-sdk-c/cmake
    ```

2. Navigera till rotmappen på den git-lagringsplats som du klonade och kör [TPM](https://docs.microsoft.com/windows/device-security/tpm/trusted-platform-module-overview)-simulatorn med hjälp av den sökväg som visas nedan. Den här simulatorn lyssnar via en socket på portarna 2321 och 2322. Stäng inte det här kommandofönstret. Den här simulatorn måste fortsätta att köras till slutet av den här snabbstarten. 

   Om du är i mappen *cmake* kör du sedan följande kommandon:

    ```cmd/sh
    cd ..
    .\provisioning_client\deps\utpm\tools\tpm_simulator\Simulator.exe
    ```

    Du kommer inte se några utdata från simulatorn. Låt den fortsätta att köra simuleringen av en TPM-enhet.

<a id="simulatetpm"></a>

## <a name="read-cryptographic-keys-from-the-tpm-device"></a>Läsa kryptografiska nycklar från TPM-enheten

I det här avsnittet kompilerar och kör du ett exempel som läser bekräftelsenyckeln och registrerings-ID från den TPM-simulator som lät köra och lyssna via portarna 2321 och 2322. Dessa värden används för enhetsregistrering med din instans för enhetsetableringstjänst.

1. Starta Visual Studio och öppna den nya lösningsfilen med namnet `azure_iot_sdks.sln`. Den här lösningsfilen finns i den `cmake`-mapp som du tidigare skapade i roten på azure-iot-sdk-c git-lagringsplatsen.

2. I Visual Studio-menyn väljer du **Build (Skapa)** > **Build Solution (Skapa lösning)** för att skapa alla projekt i lösningen.

3. I fönstret *Solution Explorer* i Visual Studio går du till mappen **Provision (Etablera)\_Verktyg**. Högerklicka på projektet **tpm_device_provision** och markera **Set as Startup Project** (Ange som startprojekt). 

4. I Visual Studio-menyn väljer du **Felsökning** > **Starta utan felsökning** för att köra lösningen. Appen läser och visar ett **_registrerings-ID_** och en **_bekräftelsenyckel_**. Kopiera de här värdena. De kommer att användas i nästa avsnitt för enhetsregistrering. 


<a id="portalenrollment"></a>

## <a name="create-a-device-enrollment-entry-in-the-portal"></a>Skapa en post för enhetsregistrering i portalen

1. Logga in på Azure-portalen, klicka på knappen **Alla resurser** i den vänstra menyn och öppna Device Provisioning-tjänsten.

2. Välj fliken **Hantera registreringar** och klicka sedan på knappen **Lägg till enskild registrering** längst upp. 

3. På **Lägg till registrering** anger du följande information och klickar på knappen **Spara**.

    - **Mekanism:** Välj **TPM** som identitet för bestyrkande *mekanism*.
    - **Bekräftelsenyckel:** Ange den *bekräftelsenyckel* som du skapade för din TPM-enhet genom att köra projektet *tpm_device_provision*.
    - **Registrerings-ID**: Ange det *registrerings-ID* som du skapade för din TPM-enhet genom att köra projektet *tpm_device_provision*.
    - **IoT Edge-enhet:** Välj **inaktivera**.
    - **Enhets-ID för IoT Hub:** Ge enheten ett ID genom att ange **test-docs-device**.

      ![Ange information för enhetsregistrering i portalen](./media/quick-create-simulated-device/enter-device-enrollment.png)  

      Vid lyckad registrering visas *Registrerings-ID* för enheten i listan under fliken *Individual Enrollments* (Enskilda registreringar). 


<a id="firstbootsequence"></a>

## <a name="simulate-first-boot-sequence-for-the-device"></a>Simulera första startsekvens för enheten

I det här avsnittet konfigurerar du exempelkod för att använda [Advanced Message Queuing Protocol (AMQP)](https://wikipedia.org/wiki/Advanced_Message_Queuing_Protocol) för att skicka enhetens startsekvens till din instans av enhetsetableringstjänst. Den här startsekvensen medför att enheten identifieras och tilldelas till en IoT-hubb som är länkad till instansen av enhetsetableringstjänsten.

1. I Azure-portalen väljer du fliken **Översikt** för enhetsetableringstjänsten och kopierar värdet för **_ID-omfång_**.

    ![Extrahera information om enhetsetableringstjänstens slutpunkt från portalen](./media/quick-create-simulated-device/extract-dps-endpoints.png) 

2. I fönstret *Solution Explorer* i Visual Studio går du till mappen **Provision (Etablera)\_Exempel**. Expandera exempelprojektet som heter **prov\_dev\_client\_sample**. Expandera **Källfiler** och öppna **prov\_dev\_client\_sample.c**.

3. Längst upp i filen hittar du `#define`-uttrycken för varje enhetsprotokoll enligt nedan. Se till att endast `SAMPLE_AMQP` saknar kommentarer.

    För närvarande [stöds inte MQTT-protokollet för enskild TPM-registrering](https://github.com/Azure/azure-iot-sdk-c#provisioning-client-sdk).

    ```c
    //
    // The protocol you wish to use should be uncommented
    //
    //#define SAMPLE_MQTT
    //#define SAMPLE_MQTT_OVER_WEBSOCKETS
    #define SAMPLE_AMQP
    //#define SAMPLE_AMQP_OVER_WEBSOCKETS
    //#define SAMPLE_HTTP
    ```

4. Hitta konstanten `id_scope` och ersätt värdet med ditt värde för **ID-omfång** som du kopierade tidigare. 

    ```c
    static const char* id_scope = "0ne00002193";
    ```

5. Hitta definitionen för funktionen `main()` i samma fil. Kontrollera att variabeln `hsm_type` anges till `SECURE_DEVICE_TYPE_TPM` i stället för `SECURE_DEVICE_TYPE_X509` enligt nedan.

    ```c
    SECURE_DEVICE_TYPE hsm_type;
    hsm_type = SECURE_DEVICE_TYPE_TPM;
    //hsm_type = SECURE_DEVICE_TYPE_X509;
    ```

6. Högerklicka på projektet **prov\_dev\_client\_sample** och välj **Set as Startup Project** (Ange som startprojekt). 

7. I Visual Studio-menyn väljer du **Felsökning** > **Starta utan felsökning** för att köra lösningen. I meddelandet för att omkompilera projektet klickar du på **Ja**, för att omkompilera projektet innan du kör.

    Följande utdata är ett exempel på när etableringsenhetens klientprov lyckas med starten och ansluter till en instans av enhetsetableringstjänst för att hämta IoT-hubbinformation och registrera:

    ```cmd
    Provisioning API Version: 1.2.7
    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED

    Registering... Press enter key to interrupt.

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service:
    test-docs-hub.azure-devices.net, deviceId: test-docs-device
    ```

8. När den simulerade enheten har etablerats till IoT-hubben av etableringstjänsten visas enhets-ID med hubbens **IoT-enheter**. 

    ![Enheten är registrerad på IoT-hubben](./media/quick-create-simulated-device/hub-registration.png) 


## <a name="clean-up-resources"></a>Rensa resurser

Om du vill fortsätta att arbeta med och utforska enhetsklientexemplet ska du inte rensa de resurser som har skapats i den här snabbstarten. Om du inte planerar att fortsätta kan du använda stegen nedan för att ta bort alla resurser som har skapats i den här snabbstarten.

1. Stäng utdatafönstret för enhetsklientexemplet på datorn.
2. Stäng fönstret för TPM-simulatorn på datorn.
3. Klicka på **Alla resurser** på menyn till vänster på Azure-portalen och välj din Device Provisioning-tjänst. Öppna **Hantera registreringar** för tjänsten och klicka på fliken **Enskilda registreringar**. Välj *REGISTRERINGS-ID* för enheten du har registrerat i den här snabbstarten och klicka på knappen **Ta bort** högst upp. 
4. Klicka på **Alla resurser** på menyn till vänster på Azure-portalen och välj din IoT-hubb. Öppna **IoT-enheter** för din hubb, välj *ENHETS-ID* för den enhet som du har registrerat i den här snabbstarten och klicka på knappen **Ta bort** högst upp.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du skapat en TPM-simulerad enhet på datorn och etablerat den på IoT-hubben med hjälp av IoT Hub Device Provisioning-tjänsten. Information om hur du registrerar TPM-enheten programmässigt får du om du fortsätter till snabbstarten för programmässig registrering av en TPM-enhet. 

> [!div class="nextstepaction"]
> [Azure snabbstart – Registrera TPM-enhet på Azure IoT Hub Device Provisioning-tjänsten](quick-enroll-device-tpm-java.md)

