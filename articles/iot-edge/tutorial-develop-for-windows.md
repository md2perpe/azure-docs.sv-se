---
title: Utveckla-modulen för Windows-enheter – Azure IoT Edge | Microsoft Docs
description: Den här självstudiekursen beskriver hur du konfigurerar din Utvecklingsresurser för dator och molnet att utveckla IoT Edge-moduler med hjälp av Windows-behållare för Windows-enheter
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/06/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 94a287cd996bd18b757620254540f8dc0df499e8
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67051805"
---
# <a name="tutorial-develop-iot-edge-modules-for-windows-devices"></a>Självstudier: Utveckla IoT Edge-moduler för Windows-tjänster

Använd Visual Studio för att utveckla och distribuera koden till Windows-enheter som kör IoT Edge.

I snabbstarten, skapas en IoT Edge-enhet med en Windows-dator och distribueras en färdiga modul från Azure Marketplace. Den här självstudiekursen beskriver vad som krävs för att utveckla och distribuera din egen kod till en IoT Edge-enhet. Den här självstudien är en användbar förutsättning för alla andra självstudier, som anger att mer information om specifika programmeringsspråk eller Azure-tjänster. 

Den här självstudien används ett exempel på distribution av en  **C# modul till en Windows-enhet**. Det här exemplet valdes eftersom det är det vanligaste scenariot för utveckling. Om du är intresserad av att utveckla på ett annat språk eller planerar att distribuera Azure-tjänster som moduler kan kan den här självstudien fortfarande användbart om du vill veta mer om utvecklingsverktygen. När du förstår begreppen som utveckling, kan du välja din det språk du föredrar eller Azure-tjänst som fördjupar. 

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Ställ in din utvecklingsdator.
> * Använd IoT Edge-verktyg för Visual Studio för att skapa ett nytt projekt.
> * Skapa ditt projekt som en behållare och lagra den i ett Azure container registry.
> * Distribuera din kod till en IoT Edge-enhet. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="key-concepts"></a>Viktiga begrepp

Den här självstudiekursen beskriver utvecklingen av IoT Edge-modul. En *IoT Edge-modul*, eller bara ibland *modulen* för kort, är en behållare som innehåller körbar kod. Du kan distribuera en eller flera moduler till en IoT Edge-enhet. Moduler utför specifika uppgifter, t.ex. mata in data från sensorer, utföra dataanalys eller data rengöringen eller skicka meddelanden till en IoT-hubb. Mer information finns i [förstå Azure IoT Edge-moduler](iot-edge-modules.md).

När du utvecklar IoT Edge-moduler, är det viktigt att förstå skillnaden mellan utvecklingssystemet och målet IoT Edge-enhet där modulen så småningom ska distribueras. Behållaren som du skapar för att lagra din modul-kod måste matcha operativsystemet (OS) på den *målenheten*. Det här konceptet är enklare för utveckling för Windows-behållare, eftersom Windows-behållare endast köras på Windows-operativsystem. Men du kan till exempel använda en Windows-utvecklingsdator för att skapa moduler för Linux IoT Edge-enheter. I det här scenariot, skulle du behöva se till att utvecklingsdatorn kördes Linux-behållare. När du går igenom den här kursen, Tänk på skillnaden mellan *utvecklingsdator OS* och *behållare OS*.

Den här kursen riktar sig mot Windows-enheter som kör IoT Edge. Windows IoT Edge-enheter använder Windows-behållare. Vi rekommenderar att du använder Visual Studio för att utveckla för Windows-enheter, så det är den här självstudien använder. Du kan använda Visual Studio Code, även om det finns skillnader i stöd mellan de två verktyg.

I följande tabell visas stöds utvecklingsscenarier för **Windows-behållare** i Visual Studio Code och Visual Studio.

|   | Visual Studio-koden | Visual Studio 2017/2019 |
| - | ------------------ | ------------------ |
| **Azure-tjänster** | Azure Functions <br> Azure Stream Analytics |   |
| **Språk** | C#(Felsökning stöds inte) | C <br> C# |
| **Mer information** | [Azure IoT Edge for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) | [Azure IoT Edge-verktyg för Visual Studio 2017](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools)<br>[Azure IoT Edge-verktyg för Visual Studio-2019](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) |

## <a name="prerequisites"></a>Nödvändiga komponenter

En utvecklingsdator:

* Windows 10 med 1809 uppdateringen eller senare.
* Du kan använda din egen dator eller en virtuell dator, beroende på dina inställningar för utveckling.
* Installera [Git](https://git-scm.com/). 

En Azure IoT Edge-enhet på Windows:

* Vi rekommenderar att du inte kör IoT Edge på utvecklingsdatorn, men i stället använda en separat enhet. Denna skillnad mellan utvecklingssystemet och IoT Edge-enhet mer korrekt speglar en SANT distributionsscenariot och hjälper till att hålla de olika begreppen direkt.
* Om du inte har en andra enhet som är tillgängliga kan använda snabbstartsartikeln för att skapa en IoT Edge-enhet i Azure med en [Windows VM](quickstart.md).

Molnresurser:

* En kostnadsfri eller standard-nivån [IoT-hubb](../iot-hub/iot-hub-create-through-portal.md) i Azure. 

## <a name="install-container-engine"></a>Installationsprogram för behållare

IoT Edge-moduler är paketerade som behållare, så du behöver en motor för behållare på en utvecklingsdator för att skapa och hantera behållare. Vi rekommenderar att du använder Docker Desktop för utveckling på grund av dess många funktioner och dess popularitet som en behållare. Med Docker Desktop på en Windows-dator kan växla du mellan Linux-behållare och Windows-behållare så att du enkelt kan utveckla moduler för olika typer av IoT Edge-enheter. 

Använd Docker-dokumentation för att installera på utvecklingsdatorn: 

* [Installera Docker Desktop för Windows](https://docs.docker.com/docker-for-windows/install/)

  * När du installerar Docker Desktop för Windows, blir du tillfrågad om du vill använda Linux eller Windows-behållare. Den här självstudien använder **Windows-behållare**. Mer information finns i [växla mellan Windows och Linux-behållare](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers).


## <a name="set-up-visual-studio-and-tools"></a>Konfigurera Visual Studio och verktyg

IoT-tillägg för Visual Studio hjälper dig att utveckla IoT Edge-moduler. Dessa tillägg ger projektmallar, automatisera skapandet av manifestet distributionen och gör att du kan övervaka och hantera IoT Edge-enheter. I det här avsnittet ska du installera Visual Studio och IoT Edge-tillägget och sedan ställa in ditt Azure-konto för att hantera IoT Hub-resurser från Visual Studio. 

Den här självstudien Lär hur utveckling för Visual Studio-2019. Om du använder Visual Studio 2017 (version 15.7 eller högre) kan är stegen ungefär samma sätt. Om du föredrar att använda Visual Studio Code, läser du anvisningarna i [använda Visual Studio Code för att utveckla och felsöka moduler för Azure IoT Edge](how-to-vs-code-develop-module.md). 

1. Förbered Visual Studio 2019 på utvecklingsdatorn. 

   * Om du inte redan har Visual Studio på en utvecklingsdator [installera Visual Studio 2019](https://docs.microsoft.com/visualstudio/install/install-visual-studio) med följande arbetsbelastningar: 

      * Azure Development
      * Skrivbordsutveckling medC++
      * .NET Core plattformsoberoende utveckling

   * Om du redan har Visual Studio 2019 på utvecklingsdatorn, följer du stegen i [ändra Visual Studio](https://docs.microsoft.com/visualstudio/install/modify-visual-studio) att lägga till nödvändiga arbetsbelastningar.

2. Ladda ned och installera den [Azure IoT Edge Tools](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) tillägget för Visual Studio-2019. 

   Om du använder Visual Studio 2017 (version 15.7 eller högre) kan hämta och installera den [Azure IoT Edge-verktyg för Visual Studio 2017](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools).

3. När installationen har slutförts, öppna 2019 för Visual Studio och välj **Fortsätt utan kod**.

4. Välj **visa** > **Cloud Explorer**. 

5. Välj profilikonen i cloud explorer och logga in på ditt Azure-konto om du inte redan loggat in. 

6. När du har loggat in visas dina Azure-prenumerationer. Expandera den prenumeration som har IoT hub. 

7. I din prenumeration, expandera **IoT-hubbar** sedan din IoT-hubb. Du bör se en lista över dina IoT-enheter och kan använda den här explorer ska hanteras. 

   ![Få åtkomst till IoT Hub-resurser i Cloud Explorer](./media/tutorial-develop-for-windows/cloud-explorer-view-hub.png)

[!INCLUDE [iot-edge-create-container-registry](../../includes/iot-edge-create-container-registry.md)]

## <a name="create-a-new-module-project"></a>Skapa ett nytt projekt i modulen

Verktyg för Azure IoT Edge-tillägget innehåller projektmallar för alla stöds IoT Edge modulen språk i Visual Studio. Dessa mallar kan alla filer och kod som du behöver distribuera en fungerande-modul för att testa IoT Edge, eller ge dig en utgångspunkt för att anpassa mallen med egen affärslogik. 

1. Välj **filen** > **nya** > **projekt...**

2. Sök efter i fönstret nytt projekt **IoT Edge** och välj den **Azure IoT Edge (Windows amd64)** projekt. Klicka på **Nästa**. 

   ![Skapa ett nytt projekt i Azure IoT Edge](./media/tutorial-develop-for-windows/new-project.png)

3. I Konfigurera din nya projektfönstret Byt namn på projektet och lösningen till något beskrivande liknande **CSharpTutorialApp**. Klicka på **skapa** att skapa projektet.

   ![Konfigurera ett nytt Azure IoT Edge-projekt](./media/tutorial-develop-for-windows/configure-project.png)
 

4. Konfigurera ditt projekt i IoT Edge-programmet och modulfönstret, med följande värden: 

   | Fält | Värde |
   | ----- | ----- |
   | Välj en mall | Välj  **C# modulen**. | 
   | Modulen projektnamn | Acceptera standardvärdet **IoTEdgeModule1**. | 
   | Lagringsplatsen för docker-avbildningen | En bildlagringsplats innehåller namnet på containerregistret och namnet på containeravbildningen. Behållaravbildningen innehåller redan från modulen Projekt namn-värde. Ersätt **localhost:5000** med värdet för inloggningsservern från ditt Azure-containerregister. Du kan hämta inloggningsservern från sidan Översikt för ditt containerregister på Azure-portalen. <br><br> Sista avbildningslagringsplatsen ut \<registernamn\>.azurecr.io/iotedgemodule1. |

   ![Konfigurera ditt projekt för målenhet och Modultyp behållarregister](./media/tutorial-develop-for-windows/add-module-to-solution.png)

5. Välj **Ja** att tillämpa ändringarna. 

När det nya projektet har lästs in i Visual Studio-fönstret kan du ta en stund att bekanta dig med de filer som den skapats: 

* Ett projekt med IoT Edge kallas **CSharpTutorialApp**.
    * Den **moduler** mappen finns länkar till de moduler som ingår i projektet. I det här fallet ska bara IoTEdgeModule1. 
    * Den **deployment.template.json** filen är en mall för att skapa ett manifest för distribution. En *distribution manifest* är en fil som definierar exakt vilka moduler som du vill distribuerad på en enhet, hur de ska konfigureras och hur de kan kommunicera med varandra och molnet. 
* Ett projekt med IoT Edge-modulen kallas **IoTEdgeModule1**.
    * Den **program.cs** filen innehåller standard C# modulen kod som medföljer projektmallen. Standardmodul hämtar indata från en källa och skickar dem vidare till IoT Hub. 
    * Den **module.json** hold filinformation om modulen, inklusive fullständig avbildningslagringsplatsen avbildningen version och vilka Dockerfile som ska användas för varje plattform som stöds.

### <a name="provide-your-registry-credentials-to-the-iot-edge-agent"></a>Ange dina autentiseringsuppgifter för registret till IoT Edge-agenten

IoT Edge-körningen behöver dina autentiseringsuppgifter för registret att hämta dina behållaravbildningar till IoT Edge-enhet. Lägg till dessa autentiseringsuppgifter för distribution av mallen. 

1. Öppna den **deployment.template.json** fil.

2. Hitta den **registryCredentials** -egenskapen i $edgeAgent önskade egenskaper. 

3. Uppdatera egenskapen med dina autentiseringsuppgifter efter det här formatet: 

   ```json
   "registryCredentials": {
     "<registry name>": {
       "username": "<username>",
       "password": "<password>",
       "address": "<registry name>.azurecr.io"
     }
   }
   ```

4. Spara filen deployment.template.json. 

### <a name="review-the-sample-code"></a>Granska exempelkoden

Den mall som du skapade innehåller exempelkod för en IoT Edge-modul. Det här exemplet modulen tar bara emot meddelanden och skickar dem på. Pipeline-funktionen visar ett viktigt begrepp i IoT Edge, vilket är hur moduler kommunicerar med varandra.

Varje modul kan ha flera *inkommande* och *utdata* köer som har deklarerats i sin kod. IoT Edge hub som körs på enheten skickar meddelanden från utdata från en modul i indata för en eller flera moduler. Det aktuella språket för deklarerar indata och utdata varierar mellan olika språk, men konceptet är detsamma för både alla moduler. Mer information om routning mellan moduler finns i [deklarera vägar](module-composition.md#declare-routes).

Exemplet C# koden som medföljer projektmallen använder den [ModuleClient klass](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient?view=azure-dotnet) från IoT Hub-SDK för .NET. 

1. I den **program.cs** filen, hitta den **SetInputMessageHandlerAsync** metod.

2. Den [SetInputMessageHandlerAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient.setinputmessagehandlerasync?view=azure-dotnet) metod konfigurerar en indatakö att ta emot inkommande meddelanden. Granska den här metoden och se hur det initierar en indatakö som kallas **indata1**. 

   ![Hitta Indatanamnet i konstruktorn SetInputMessageHandlserAsync](./media/tutorial-develop-for-windows/declare-input-queue.png)

3. Leta sedan reda på **SendEventAsync** metod.

4. Den [SendEventAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient.sendeventasync?view=azure-dotnet) metoden behandlar mottagna meddelanden och ställer in en utgående kö skickar dem vidare. Granska den här metoden och se att den initierar en utgående kö som kallas **output1**. 

   ![Hitta utdatanamnet i konstruktorn SendEventAsync](./media/tutorial-develop-for-windows/declare-output-queue.png)

5. Öppna den **deployment.template.json** fil.

6. Hitta den **moduler** egenskapen för $edgeAgent önskade egenskaper. 

   Det bör finnas två moduler som anges här. Först är **tempSensor**, som ingår i alla mallar som standard att tillhandahålla simulerade temperaturdata som du kan använda för att testa dina moduler. Andra är den **IotEdgeModule1** modul som du har skapat som en del av det här projektet.

   Den här egenskapen moduler anger vilka moduler som ska ingå i distributionen till din enhet eller enheter. 

7. Hitta den **vägar** egenskapen för $edgeHub önskade egenskaper. 

   En av funktionerna om IoT Edge hub-modulen är att dirigera meddelanden mellan alla moduler i en distribution. Granska värdena i egenskapen vägar. Den första rutten **IotEdgeModule1ToIoTHub**, använder jokertecken ( **\*** ) att inkludera alla meddelanden som kommer från alla utgående kö i modulen IoTEdgeModule1. Dessa meddelanden går du till *$uppströms*, vilket är ett reserverat namn som anger IoT Hub. Den andra vägen **sensorToIotEdgeModule1**, tar meddelanden som kommer från modulen tempSensor och skickar dem till den *indata1* indatakö IotEdgeModule1-modulen. 

   ![Granska vägar i deployment.template.json](./media/tutorial-develop-for-windows/deployment-routes.png)


## <a name="build-and-push-your-solution"></a>Skapa och push-överföra lösningen

Du har granskat kod som modulen och Distributionsmall att förstå vissa begrepp i samband med distributionen. Nu är du redo att skapa IotEdgeModule1 behållaravbildning och push-överföra den till behållarregistret. Det här steget genererar även distribution manifestet baserat på informationen i mallfilen och modulinformation från lösningsfiler med tillägget för IoT-verktyg för Visual Studio. 

### <a name="sign-in-to-docker"></a>Logga in på Docker

Ange din behållare autentiseringsuppgifter för registret på Docker på utvecklingsdatorn så att den kan skicka en behållaravbildning som ska lagras i registret. 

1. Öppna PowerShell eller Kommandotolken.

2. Logga in på Docker med Azure container registry-autentiseringsuppgifter som du sparade när du har skapat registret. 

   ```cmd
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Du får en säkerhetsvarning som rekommenderar användning av `--password-stdin`. Även om den bästa metoden rekommenderas för produktionsscenarier, ligger utanför omfånget för den här självstudien. Mer information finns i den [docker-inloggning](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) referens.

### <a name="build-and-push"></a>Skapa och skicka

Utvecklingsdatorn har nu åtkomst till ditt behållarregister och din IoT Edge-enheter kommer för. Det är dags att projektkoden i en behållaravbildning. 

1. Högerklicka på den **CSharpTutorialApp** projektmappen, och välj **Build and Push IoT Edge-moduler**. 

   ![Skapa och skicka IoT Edge-moduler](./media/tutorial-develop-for-windows/build-and-push-modules.png)

   Versions- och push-kommando startar tre åtgärder. Först skapar den en ny mapp i lösningen kallas **config** som innehåller fullständig distribution manifest, byggas ut information i distributionsmallen och andra lösningsfiler. Dessutom körs `docker build` att skapa behållaravbildningen baserat på dockerfile som är lämpliga för din mål-arkitektur. Sedan körs `docker push` att skicka lagringsplatsen för avbildningen till behållarregistret. 

   Den här processen kan ta flera minuter första gången, men är snabbare nästa gång du kör kommandona. 

2. Öppna den **deployment.windows amd64.json** fil i den nyligen skapade config-mappen. (Den config-mappen kan inte förekomma i Solution Explorer i Visual Studio. Om så är fallet, väljer du den **visa alla filer** ikonen i Solution Explorer-verktygsfältet.)

3. Hitta den **bild** parameter i avsnittet IotEdgeModule1. Observera att bilden innehåller fullständiga avbildningslagringsplatsen med taggen namn, version och arkitektur från filen module.json.

4. Öppna den **module.json** filen i mappen IotEdgeModule1. 

5. Ändra det lägre versionsnumret för avbildningen modulen. (Versionen, inte $schema-version.) Till exempel öka versionsnumret patch till **0.0.2** som om vi hade gjort en liten korrigering i modulen koden. 

   >[!TIP]
   >Modulversioner aktivera versionskontroll och gör att du kan testa ändringar på en liten uppsättning enheter innan du distribuerar uppdateringar till produktion. Om du inte öka Modulversion innan du skapar och push-överföring, över du lagringsplatsen i ditt behållarregister. 

6. Spara dina ändringar i filen module.json.

7. Högerklicka på den **CSharpTutorialApp** projektet mappen igen och välj **Build and Push IoT Edge-moduler** igen. 

8. Öppna den **deployment.windows amd64.json** filen igen. Observera att en ny fil inte har skapats när du körde versions- och push-kommandot igen. I stället har samma fil uppdaterats för att återspegla ändringarna. IotEdgeModule1 bild pekar nu på 0.0.2 version av behållaren. Den här ändringen i manifestet distribution är hur du berätta för IoT Edge-enhet att det finns en ny version av en modul för att hämta. 

9. Att ytterligare kontrollera det kommandot versions- och push gjorde, gå till den [Azure-portalen](https://portal.azure.com) och navigera till ditt behållarregister. 

10. Välj i ditt behållarregister **databaser** sedan **iotedgemodule1**. Kontrollera att båda versionerna av avbildningen pushats till registret.

    ![Visa båda bild-versioner i container registry](./media/tutorial-develop-for-windows/view-repository-versions.png)

### <a name="troubleshoot"></a>Felsöka

Om det uppstår problem när du bygger och push-överföra avbildningen modulen har den ofta att göra med Docker-konfigurationen på utvecklingsdatorn. Använd följande kontroller för att granska konfigurationen: 

* Stötte du den `docker login` kommando med hjälp av de autentiseringsuppgifter som du kopierade från ditt behållarregister? Dessa autentiseringsuppgifter skiljer sig från de som används för att logga in på Azure. 
* Stämmer containerlagringsplatsen? Har den din rätt namnet på behållarregistret och din rätt Modulnamn? Öppna den **module.json** filen i mappen IotEdgeModule1 att kontrollera. Värdet för databasen bör se ut så  **\<registernamn\>.azurecr.io/iotedgemodule1**. 
* Om du har använt ett annat namn än **IotEdgeModule1** för, är det namnet konsekvent i hela lösningen?
* Din dator kör samma typ av behållare som du skapar? Den här självstudien är för Windows IoT Edge-enheter så att dina Visual Studio-filer ska ha den **windows amd64** tillägget och Docker Desktop bör köra Windows-behållare. 

## <a name="deploy-modules-to-device"></a>Distribuera moduler till enhet

Du har verifierat att de inbyggda behållaravbildningarna lagras på ditt behållarregister, så är det dags att distribuera dem till en enhet. Se till att din IoT Edge-enhet är igång. 

1. Öppna Cloud Explorer i Visual Studio och expandera informationen för din IoT-hubb. 

2. Välj namnet på den enhet som du vill distribuera till. I den **åtgärder** väljer **skapa distribution**.

   ![Skapa distribution för en enskild enhet](./media/tutorial-develop-for-windows/create-deployment.png)


3. I Utforskaren, navigera till mappen konfiguration av dina projekt och välj den **deployment.windows amd64.json** fil. Den här filen finns ofta `C:\Users\<username>\source\repos\CSharpTutorialApp\CSharpTutorialApp\config\deployment.windows-amd64.json`

   Använd inte filen deployment.template.json, som inte har fullständig modulen bild värdena i den. 

4. Visa information för din IoT Edge-enhet i Cloud Explorer Se moduler på enheten.

5. Använd den **uppdatera** knappen för att uppdatera enhetens status för att se att tempSensor och IotEdgeModule1 moduler distribueras din enhet. 


   ![Visa moduler som körs på din IoT Edge-enhet](./media/tutorial-develop-for-windows/view-running-modules.png)

## <a name="view-messages-from-device"></a>Visa meddelanden från enhet

IotEdgeModule1 koden tar emot meddelanden via dess inkommande kö och skickar dem via den utgående kön. Distribution av manifestet deklarerats vägar som skickas meddelanden från tempSensor till IotEdgeModule1 och sedan vidarebefordras meddelanden från IotEdgeModule1 till IoT Hub. Azure IoT Edge-verktyg för Visual Studio kan du se meddelanden när de anländer till IoT Hub från dina enskilda enheter. 

1. I Visual Studio cloud explorer, väljer du namnet på IoT Edge-enhet som du distribuerat till. 

2. I den **åtgärder** menyn och välj **starta inbyggda händelse slutpunkt för övervakning av**.

3. Titta på den **utdata** avsnittet i Visual Studio för att se meddelanden som inkommer på din IoT-hubb. 

   Det kan ta några minuter för båda modulerna att starta. IoT Edge-körningen behöver att ta emot nya distribution manifestet, hämta modulen bilder från behållaren runtime och starta sedan varje ny modul. Om du 

   ![Visa inkommande enhet till moln-meddelanden](./media/tutorial-develop-for-windows/view-d2c-messages.png)

## <a name="view-changes-on-device"></a>Visa ändringar på enheten

Om du vill se vad som händer på din enhet själva kan du använda kommandon i det här avsnittet för att granska IoT Edge-körningen och moduler som körs på din enhet. 

Kommandona i det här avsnittet är för din IoT Edge-enhet, inte på din utvecklingsdator. Om du använder en virtuell dator för din IoT Edge-enhet kan ansluta till den nu. Gå till den virtuella datorns översiktssidan i Azure, och välj **Connect** att komma åt fjärrskrivbordsanslutningen. På enheten, öppnar du ett kommando eller PowerShell-fönster för att köra den `iotedge` kommandon.

* Visa alla moduler som har distribuerats till enheten och kontrollera deras status:

   ```cmd
   iotedge list
   ```

   Du bör se fyra moduler: de två moduler för IoT Edge-körning och tempSensor IotEdgeModule1. Alla fyra bör anges som körs.

* Granska loggarna för en specifik modul:

   ```cmd
   iotedge logs <module name>
   ```

   IoT Edge-moduler är skiftlägeskänsliga. 

   TempSensor och IotEdgeModule1 loggar ska visa meddelanden som de bearbetning. Modulen edgeAgent ansvarar för att starta andra moduler så att loggar har information om hur du implementerar distribution manifestet. Om alla moduler visas inte i listan eller inte körs, har förmodligen edgeAgent loggarna felen. Modulen edgeHub ansvarar för kommunikationen mellan moduler och IoT Hub. Om modulerna som är igång och körs, men meddelanden inte anländer till din IoT-hubb, har förmodligen edgeHub loggarna felen. 

## <a name="next-steps"></a>Nästa steg

I den här självstudien får du ställer in Visual Studio 2019 på utvecklingsdatorn och distribuerat din första IoT Edge-modul från den. Nu när du vet de grundläggande begreppen kan du prova att lägga till funktioner till en modul så att den kan analysera informationen som passerar genom den. Välj det språk du föredrar: 

> [!div class="nextstepaction"] 
> [C](tutorial-c-module-windows.md)
> [C#](tutorial-csharp-module-windows.md)
