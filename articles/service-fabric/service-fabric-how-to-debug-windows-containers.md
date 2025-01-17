---
title: Felsöka Windows-behållare med Service Fabric och VS | Microsoft Docs
description: Lär dig mer om att felsöka Windows-behållare i Azure Service Fabric med Visual Studio 2019.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: msfussell
editor: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/14/2019
ms.author: aljo, mikhegn
ms.openlocfilehash: 15f288d5400b49ec05c9ffb936fd2097cc61bae8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66428147"
---
# <a name="how-to-debug-windows-containers-in-azure-service-fabric-using-visual-studio-2019"></a>Anvisningar: Felsöka Windows-behållare i Azure Service Fabric med Visual Studio 2019

Du kan felsöka .NET-program i behållare som Service Fabric-tjänster med Visual Studio-2019. Den här artikeln visar hur du konfigurerar din miljö och felsöka en .NET-program i en behållare i ett lokalt Service Fabric-kluster.

## <a name="prerequisites"></a>Nödvändiga komponenter

* På Windows 10, följer du snabbstarten till [konfigurera Windows 10 för att köra Windows-behållare](https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-10)
* På Windows Server 2016 kan du följa den här snabbstarten till [konfigurera Windows 2016 för att köra Windows-behållare](https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-server)
* Konfigurera din lokala Service Fabric-miljö genom att följa [förbereda utvecklingsmiljön i Windows](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started)

## <a name="configure-your-developer-environment-to-debug-containers"></a>Konfigurera din utvecklarmiljö för att felsöka behållare

1. Kontrollera Docker för Windows-tjänst körs innan du fortsätter med nästa steg.

1. För att stödja DNS-matchning mellan behållare, måste du konfigurera klustret för lokal utveckling med namnet på datorn. De här stegen är också nödvändigt om du vill adresstjänster via omvänd proxy.
   1. Öppna PowerShell som administratör
   2. Navigera till installationsmappen för SDK-kluster, vanligtvis `C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup`.
   3. Kör skript `DevClusterSetup.ps1`

      ``` PowerShell
        C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1
      ```

      > [!NOTE]
      > Du kan använda den `-CreateOneNodeCluster` att konfigurera ett kluster med en nod. Standard skapas ett lokala kluster med fem noder.
      >

      Läs mer om DNS-tjänsten i Service Fabric i [DNS-tjänsten i Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-dnsservice). Läs mer om hur du använder Service Fabric omvänd proxy från tjänster som körs i en behållare i [omvänd proxy särskild hantering för tjänster som körs i behållare](service-fabric-reverseproxy.md#special-handling-for-services-running-in-containers).

### <a name="known-limitations-when-debugging-containers-in-service-fabric"></a>Kända begränsningar när du felsöker behållare i Service Fabric

Nedan visas en lista över kända begränsningar med felsökning behållare i Service Fabric och möjliga lösningar:

* Med hjälp av localhost för ClusterFQDNorIP stöd inte för DNS-matchning i behållare.
    * Lösning: Ställ in det lokala klustret med namnet på datorn (se ovan)
* Kör Windows 10 på en virtuell dator får inte DNS-svaret tillbaka till behållaren.
    * Lösning: Inaktivera UDP kontrollsumma avlastning för IPv4 på nätverkskortet för virtuella datorer
    * Kör Windows 10 försämras nätverksprestanda på datorn.
    * https://github.com/Azure/service-fabric-issues/issues/1061
* Lösa tjänster i samma program med hjälp av DNS fungerar tjänstnamn inte på Windows 10, om programmet distribuerades med Docker Compose
    * Lösning: Använda servicename.applicationname för att lösa Tjänsteslutpunkter
    * https://github.com/Azure/service-fabric-issues/issues/1062
* Om du använder IP-adress för ClusterFQDNorIP, bryts ändra primära IP-adresser på värden DNS-funktioner.
    * Lösning: Återskapa klustret med den nya primära IP-Adressen på värden eller Använd namnet på datorn. Den här brott är avsiktligt.
* Om det fullständiga Domännamnet som klustret har skapats med inte matchas i nätverket, misslyckas DNS.
    * Lösning: Återskapa det lokala klustret med den primära IP-Adressen för värden. Det här felet är avsiktligt.
* När du felsöker en behållare, blir dockerloggar endast tillgängliga i utdatafönstret Visual Studio, inte via Service Fabric API: er, inklusive Service Fabric Explorer

## <a name="debug-a-net-application-running-in-docker-containers-on-service-fabric"></a>Felsöka en .NET-program som körs i docker-behållare i Service Fabric

1. Köra Visual Studio som administratör.

1. Öppna ett befintligt .NET-program eller skapa en ny.

1. Högerklicka på projektet och välj **Lägg till stöd för behållare Orchestrator -> Service Fabric ->**

1. Tryck på **F5** att starta felsökning av programmet.

    Visual Studio stöder konsolen och typer för ASP.NET-projekt för .NET och .NET Core.

## <a name="next-steps"></a>Nästa steg
Mer information om funktionerna i Service Fabric och behållare finns overview](service-fabric-containers-overview.md) för Service Fabric-behållare.
