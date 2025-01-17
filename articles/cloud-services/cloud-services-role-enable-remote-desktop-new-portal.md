---
title: Aktivera anslutning till fjärrskrivbord för en roll i Azure-molntjänster | Microsoft Docs
description: Hur du konfigurerar ditt tjänstprogram för azure-molnet för att tillåta anslutningar till fjärrskrivbord
services: cloud-services
documentationcenter: ''
author: mmccrory
manager: timlt
editor: ''
ms.assetid: 73ea1d64-1529-4d72-b58e-f6c10499e6bb
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2016
ms.author: memccror
ms.openlocfilehash: 0c36dc5fb6b2754fc93a02e29d8d8ae74df36da7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65963264"
---
# <a name="enable-remote-desktop-connection-for-a-role-in-azure-cloud-services"></a>Aktivera anslutning till fjärrskrivbord för en roll i Azure Cloud Services

> [!div class="op_single_selector"]
> * [Azure Portal](cloud-services-role-enable-remote-desktop-new-portal.md)
> * [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)
> * [Visual Studio](cloud-services-role-enable-remote-desktop-visual-studio.md)

Fjärrskrivbord kan du komma åt skrivbordet för en roll som körs i Azure. Du kan använda en anslutning till fjärrskrivbord för att felsöka och diagnostisera problem med ditt program när den körs.

Du kan aktivera en anslutning till fjärrskrivbord i din roll under utvecklingen genom att inkludera Remote Desktop-moduler i tjänstdefinitionsfilen eller du kan välja att aktivera fjärrskrivbord via Remote Desktop-tillägget. Den bästa lösningen är att använda Remote Desktop-tillägg som du kan aktivera fjärrskrivbord även när programmet distribueras utan att behöva distribuera programmet igen.

## <a name="configure-remote-desktop-from-the-azure-portal"></a>Konfigurera fjärrskrivbord från Azure portal

Azure-portalen använder metod för Remote Desktop-tillägget så att du kan aktivera Remote Desktop även efter att programmet har distribuerats. Den **fjärrskrivbord** inställningarna för din molntjänst kan du aktivera Fjärrskrivbord, ändra det lokala administratörskontot som används för att ansluta till de virtuella datorerna, certifikatet som används vid autentisering och ange förfallodatum datum.

1. Klicka på **molntjänster**, Välj namnet på Molntjänsten och välj sedan **Remote Desktop**.

    ![Fjärrskrivbord för cloud services](./media/cloud-services-role-enable-remote-desktop-new-portal/CloudServices_Remote_Desktop.png)

2. Välj om du vill aktivera Fjärrskrivbord för en enskild roll eller för alla roller och sedan ändra värdet för växlaren till **aktiverad**.

3. Fyll i fälten som krävs för användarnamn, lösenord, förfallodatum och certifikat.

    ![Fjärrskrivbord för cloud services](./media/cloud-services-role-enable-remote-desktop-new-portal/CloudServices_Remote_Desktop_Details.png)

   > [!WARNING]
   > Alla rollinstanser kommer att startas om när du först aktivera Fjärrskrivbord och väljer **OK** (markeringen). Certifikatet som används för att kryptera lösenordet måste installeras på vilken roll för att förhindra att en omstart. Att förhindra att en omstart [ladda upp ett certifikat för Molntjänsten](cloud-services-configure-ssl-certificate-portal.md#step-3-upload-a-certificate) och återgå sedan till den här dialogrutan.

4. I **roller**, Välj den roll som du vill uppdatera eller välj **alla** för alla roller.

5. När du är klar med dina uppdateringar väljer **spara**. Det tar en liten stund innan dina rollinstanser är redo att ta emot anslutningar.

## <a name="remote-into-role-instances"></a>Fjärranslut till rollinstanser

När fjärrskrivbord är aktiverat på rollerna, kan du upprätta en anslutning direkt från Azure portal:

1. Klicka på **instanser** att öppna den **instanser** inställningar.
2. Välj en rollinstans som har konfigurerats för fjärrskrivbord.
3. Klicka på **Connect** att ladda ned en RDP-fil för rollinstansen.

    ![Fjärrskrivbord för cloud services](./media/cloud-services-role-enable-remote-desktop-new-portal/CloudServices_Remote_Desktop_Connect.png)

4. Klicka på **öppna** och sedan **Connect** att starta anslutningen till fjärrskrivbordet.

>[!NOTE]
> Om Molntjänsten finns bakom en NSG, kan du behöva skapa regler som tillåter trafik på portarna **3389** och **20000**.  Fjärrskrivbord är port **3389**.  Cloud Service-instanser är belastningsutjämnad, så du inte kan styra vilken instans som ska ansluta till direkt.  Den *RemoteForwarder* och *RemoteAccess* agenter hantera RDP-trafik och tillåta att klienten kan skicka en RDP-cookie och ange en enskild instans att ansluta till.  Den *RemoteForwarder* och *RemoteAccess* agenter kräver den porten **20000*** är öppna, som kan blockeras om du har en NSG.

## <a name="additional-resources"></a>Ytterligare resurser

[Så här konfigurerar du Cloud Services](cloud-services-how-to-configure-portal.md)
