---
title: Konfigurera en utvecklingsmiljö i Mac OS X så att den fungerar med Azure Service Fabric | Microsoft Docs
description: Installera runtime, SDK och verktyg och skapa ett lokalt utvecklingskluster. När du är färdig med den här konfigurationen kommer du att kunna börja skapa appar i Mac OS X.
services: service-fabric
documentationcenter: linux
author: suhuruli
manager: chackdan
editor: ''
ms.assetid: bf84458f-4b87-4de1-9844-19909e368deb
ms.service: service-fabric
ms.devlang: linux
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/17/2017
ms.author: suhuruli
ms.openlocfilehash: 84d1f52b5fb8f18d3578bad28930f74534b1409f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60947603"
---
# <a name="set-up-your-development-environment-on-mac-os-x"></a>Konfigurera din utvecklingsmiljö i Mac OS X
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started.md)
> * [Linux](service-fabric-get-started-linux.md)
> * [OSX](service-fabric-get-started-mac.md)
>
>  

Du kan skapa Azure Service Fabric-program som körs i Linux-kluster i Mac OS X. Det här dokumentet visar hur du konfigurerar din utvecklingsmiljö i Mac.

## <a name="prerequisites"></a>Nödvändiga komponenter
Azure Service Fabric kan inte köras internt i Mac OS X. För att du ska kunna köra ett lokalt Service Fabric-kluster tillhandahåller vi en förkonfigurerad Docker-containeravbildning. Innan du börjar behöver du:

* Minst 4 GB RAM-minne.
* Senaste versionen av [Docker](https://www.docker.com/).

>[!TIP]
>
>Följ anvisningarna i [Docker-dokumentationen](https://docs.docker.com/docker-for-mac/install/#what-to-know-before-you-install) när du ska installera Docker på din Mac. [Verifiera installationen](https://docs.docker.com/docker-for-mac/#check-versions-of-docker-engine-compose-and-machine) när du är färdig.
>

## <a name="create-a-local-container-and-set-up-service-fabric"></a>Skapa en lokal container och konfigurera Service Fabric
Utför följande steg för att konfigurera en lokal Docker-container och köra ett Service Fabric-kluster på den:

1. Uppdatera konfigurationen av Docker-daemon på värden med följande inställningar och starta om Docker-daemon: 

    ```json
    {
        "ipv6": true,
        "fixed-cidr-v6": "fd00::/64"
    }
    ```
    Du kan uppdatera inställningarna direkt i filen daemon.json som finns på Docker-installationssökvägen. Du kan direkt ändra konfigurationsinställningarna för daemon i Docker. Välj **Docker-ikonen** och sedan **Inställningar** > **Daemon** > **Avancerat**.
    
    >[!NOTE]
    >
    >Ändra daemon direkt i Docker är rekommenderat eftersom platsen för filen daemon.json kan variera från en dator till en dator. Den kan till exempel vara ~/Library/Containers/com.docker.docker/Data/database/com.docker.driver.amd64-linux/etc/docker/daemon.json.
    >

    >[!TIP]
    >Vi rekommenderar att öka du ökar de resurser som Docker tilldelas vid testning av stora program. Detta kan du göra genom att först välja **Docker-ikonen** och sedan välja **Avancerat** för att justera antalet kärnor och minne.

2. Skapa din Service Fabric Image genom att skapa en fil med namnet `Dockerfile` i en ny katalog:

    ```Dockerfile
    FROM microsoft/service-fabric-onebox
    WORKDIR /home/ClusterDeployer
    RUN ./setup.sh
    #Generate the local
    RUN locale-gen en_US.UTF-8
    #Set environment variables
    ENV LANG=en_US.UTF-8
    ENV LANGUAGE=en_US:en
    ENV LC_ALL=en_US.UTF-8
    EXPOSE 19080 19000 80 443
    #Start SSH before running the cluster
    CMD /etc/init.d/ssh start && ./run.sh
    ```

    >[!NOTE]
    >Du kan anpassa den här filen genom att lägga till fler program eller beroenden i din container.
    >Om du t.ex. lägger till `RUN apt-get install nodejs -y` tillåter du stöd för `nodejs`-program som körbara gästfiler.
    
    >[!TIP]
    > Som standard hämtas avbildningen med den senaste Service Fabric-versionen. Läs mer om vissa revideringar på sidan [Docker-hubb](https://hub.docker.com/r/microsoft/service-fabric-onebox/)

3. Om du vill skapa din återanvändbara avbildning från `Dockerfile`, så öppna en terminal och `cd` som direkt håller din `Dockerfile` och kör:

    ```bash 
    docker build -t mysfcluster .
    ```
    
    >[!NOTE]
    >Den här åtgärden kan ta lite tid, men det behöver bara göras en gång.

4. Nu kan du snabbt starta en lokal kopia av Service Fabric, närhelst du så behöver, genom att köra:

    ```bash 
    docker run --name sftestcluster -d -v /var/run/docker.sock:/var/run/docker.sock -p 19080:19080 -p 19000:19000 -p 25100-25200:25100-25200 mysfcluster
    ```

    >[!TIP]
    >Ange ett namn för din containerinstans så att du kan hantera den enklare. 
    >
    >Om programmet lyssnar på vissa portar måste du ange portarna med hjälp av ytterligare `-p`-taggar. Om programmet till exempel lyssnar på port 8080 lägger du till följande `-p`-tagg:
    >
    >`docker run -itd -p 19080:19080 -p 8080:8080 --name sfonebox microsoft/service-fabric-onebox`
    >

5. Det tar en stund att starta klustret. När den körs, kan du se loggar med följande kommando eller gå till instrumentpanelen och visa klusterhälsotillståndet [ http://localhost:19080 ](http://localhost:19080):

    ```bash 
    docker logs sftestcluster
    ```



6. Stoppa och rensa behållaren, använder du följande kommando. Vi kommer dock att använda den här behållaren i nästa steg.

    ```bash 
    docker rm -f sftestcluster
    ```

### <a name="known-limitations"></a>Kända begränsningar 
 
 Följande begränsningar är kända begränsningar i lokala kluster som körs i en container för Mac: 
 
 * DNS-tjänsten körs inte och stöds inte [Problem 132](https://github.com/Microsoft/service-fabric/issues/132)

## <a name="set-up-the-service-fabric-cli-sfctl-on-your-mac"></a>Konfigurera Service Fabric CLI (sfctl) på Mac

Följ anvisningarna på [Service Fabric CLI](service-fabric-cli.md#cli-mac) för att installera Service Fabric CLI (`sfctl`) på en Mac-dator.
Med CLI-kommandona kan du interagera med Service Fabric-entiteter som kluster, program och tjänster.

1. Om du vill ansluta till klustret innan du distribuerar programmen kör du kommandot nedan. 

```bash
sfctl cluster select --endpoint http://localhost:19080
```

## <a name="create-your-application-on-your-mac-by-using-yeoman"></a>Skapa ditt program på Mac med Yeoman

Service Fabric har ramverktyg som hjälper dig att skapa ett Service Fabric-program från terminalen med en Yeoman-mallgenerator. Följ stegen nedan för att se till att Service Fabric Yeoman-mallgeneratorn fungerar i datorn:

1. Node.js och Node Package Manager (NPM) måste vara installerade på din Mac. Du kan installera programvaran med hjälp av [HomeBrew](https://brew.sh/) enligt följande:

    ```bash
    brew install node
    node -v
    npm -v
    ```
2. Installera [Yeoman](https://yeoman.io/)-mallgeneratorn på datorn från NPM:

    ```bash
    npm install -g yo
    ```
3. Installera den Yeoman-generator du vill använda enligt instruktionerna i [dokumentationen](service-fabric-get-started-linux.md#set-up-yeoman-generators-for-containers-and-guest-executables) för att komma igång. Gör så här om du vill skapa Service Fabric-program med hjälp av Yeoman:

    ```bash
    npm install -g generator-azuresfjava       # for Service Fabric Java Applications
    npm install -g generator-azuresfguest      # for Service Fabric Guest executables
    npm install -g generator-azuresfcontainer  # for Service Fabric Container Applications
    ```
4. När du har installerat generatorerna skapar du körbara gästprogram eller containertjänster genom att köra `yo azuresfguest` respektive `yo azuresfcontainer`.

5. Om du vill skapa ett Service Fabric Java-program på Mac så måste du ha JDK 1.8 och Gradle installerade på värddatorn. Du kan installera programvaran med hjälp av [HomeBrew](https://brew.sh/) enligt följande: 

    ```bash
    brew update
    brew cask install java
    brew install gradle
    ```

    >[!TIP]
    > Glöm inte att verifiera att du har rätt version av JDK som är installerad. 

## <a name="deploy-your-application-on-your-mac-from-the-terminal"></a>Distribuera ditt program på Mac från terminalen

När du har skapat ett Service Fabric-program så kan du distribuera det med [CLI:t för Service Fabric](service-fabric-cli.md#cli-mac):

1. Anslut till Service Fabric-klustret som körs i containerinstansen på din Mac:

    ```bash
    sfctl cluster select --endpoint http://localhost:19080
    ```

2. Gå till projektkatalogen och kör installationsskriptet:

    ```bash
    cd MyProject
    bash install.sh
    ```

## <a name="set-up-net-core-20-development"></a>Konfigurera .NET Core 2.0 för utveckling

Installera [.NET Core 2.0 SDK för Mac](https://www.microsoft.com/net/core#macos) om du vill börja [skapa Service Fabric-program i C#](service-fabric-create-your-first-linux-application-with-csharp.md). Paket för .NET Core 2.0 Service Fabric-program finns på NuGet.org, som för närvarande finns som förhandsversion.

## <a name="install-the-service-fabric-plug-in-for-eclipse-on-your-mac"></a>Installera plugin-programmet för Service Fabric till Eclipse på din Mac

Azure Service Fabric har ett plugin-program för Eclipse Neon (eller senare) för Java IDE. Med det här plugin-programmet blir det enklare att skapa och distribuera Java-tjänster. Gör [så här](service-fabric-get-started-eclipse.md#install-or-update-the-service-fabric-plug-in-in-eclipse) om du vill installera eller uppdatera Service Fabric plugin-programmet för Eclipse till den senaste versionen. De andra stegen i [Service Fabric för Eclipse-dokumentationen](service-fabric-get-started-eclipse.md) är också relevanta: skapa ett program, lägga till en tjänst i ett program, avinstallera ett program och så vidare.

Det sista steget är att skapa en instans av containern med en sökväg som delas med värden. Den här typen av instansiering krävs för att plugin-programmet ska fungera med Docker-containern på din Mac. Exempel:

```bash
docker run -itd -p 19080:19080 -v /Users/sayantan/work/workspaces/mySFWorkspace:/tmp/mySFWorkspace --name sfonebox microsoft/service-fabric-onebox
```

Attributen har följande definitioner:
* `/Users/sayantan/work/workspaces/mySFWorkspace` är den fullständiga sökvägen till arbetsytan på din Mac.
* `/tmp/mySFWorkspace` är sökvägen inuti containern som arbetsytan ska mappas till.

>[!NOTE]
> 
>Om du har ett annat namn/en annan sökväg för din arbetsyta uppdaterar du de här värdena i `docker run`-kommandot.
> 
>Om du startar containern med ett annat namn än `sfonebox` så bör du uppdatera namnvärdet i filen testclient.sh i Java-programmet som är Service Fabric-aktör.
>

## <a name="next-steps"></a>Nästa steg
<!-- Links -->
* [Skapa och distribuera ditt första Service Fabric-program med Java i Linux med hjälp av Yeoman](service-fabric-create-your-first-linux-application-with-java.md)
* [Skapa och distribuera ditt första Service Fabric Java-program i Linux med Service Fabric-plugin-programmet för Eclipse](service-fabric-get-started-eclipse.md)
* [Skapa ett Service Fabric-kluster i Azure-portalen](service-fabric-cluster-creation-via-portal.md)
* [Skapa ett Service Fabric-kluster med Azure Resource Manager](service-fabric-cluster-creation-via-arm.md)
* [Förstå Service Fabric-programmodellen](service-fabric-application-model.md)
* [Använd Service Fabric CLI för att hantera dina program](service-fabric-application-lifecycle-sfctl.md)
* [Förbereda en Linux-utvecklingsmiljö i Windows](service-fabric-local-linux-cluster-windows.md)

<!-- Images -->
[cluster-setup-script]: ./media/service-fabric-get-started-mac/cluster-setup-mac.png
[sfx-mac]: ./media/service-fabric-get-started-mac/sfx-mac.png
[sf-eclipse-plugin-install]: ./media/service-fabric-get-started-mac/sf-eclipse-plugin-install.png
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship
