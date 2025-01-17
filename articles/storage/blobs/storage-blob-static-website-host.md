---
title: 'Självstudie: Hantera en statisk webbplats på Blob Storage – Azure Storage'
description: Lär dig hur du konfigurerar ett lagringskonto för hantering av statisk webbplats och distribuera en statisk webbplats till Azure Storage.
services: storage
author: normesta
ms.service: storage
ms.topic: tutorial
ms.date: 12/31/2018
ms.author: normesta
ms.reviewer: seguler
ms.custom: seodec18
ms.openlocfilehash: e3f2dd74d00b36016549cd33d76b866de241d977
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65148487"
---
<!---Customer intent: I want to host files for a static website in Blob storage and access the website from an Azure endpoint.--->

# <a name="tutorial-host-a-static-website-on-blob-storage"></a>Självstudie: Hantera en statisk webbplats på Blob Storage

Den här självstudien ingår i en serie. Där lär du dig att skapa och distribuera en statisk webbplats till Azure Storage. När du är klar har du en statisk webbplats som användare kan nå offentligt. 

I del ett i den här serien lärde du dig att:

> [!div class="checklist"]
> * Konfigurera hantering av en statisk webbplats
> * Distribuera en Hello World-webbplats

## <a name="prerequisites"></a>Nödvändiga komponenter

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

I den här självstudien används [Visual Studio Code](https://code.visualstudio.com/download), ett kostnadsfritt verktyg för programmerare, för att skapa den statiska webbplatsen och distribuera den till ett Azure Storage-konto.

När du har installerat Visual Studio Code installerar du tillägget för Azure Storage-förhandsversion. Det här tillägget integrerar Azure Storage-hanteringsfunktioner med Visual Studio Code. Du använder tillägget för att distribuera din statiska webbplats till Azure Storage. Så här installerar du tillägget:

1. Starta Visual Studio Code.
2. Klicka på **Tillägg** i verktygsfältet. Sök efter *Azure Storage* och välj tillägget **Azure Storage** i listan. Klicka sedan på knappen **Installera** för att installera tillägget.

    ![Installera Azure Storage-tillägget i VS Code](media/storage-blob-static-website-host/install-extension-vs-code.png)

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Kom igång genom att logga in på [Azure-portalen](https://portal.azure.com/).

## <a name="configure-static-website-hosting"></a>Konfigurera hantering av en statisk webbplats

Det första steget är att konfigurera ditt lagringskonto för att hantera en statisk webbplats på Azure-portalen. När du konfigurerar ditt konto för hantering av en statisk webbplats skapar Azure Storage automatiskt en container med namnet *$web*. Containern *$web* kommer att innehålla filerna för den statiska webbplatsen. 

1. Öppna [Azure-portalen](https://portal.azure.com/) i webbläsaren. 
1. Leta upp ditt lagringskonto och visa kontoöversikten.
1. Välj **Statisk webbplats** för att visa konfigurationssidan för statiska webbplatser.
1. Välj **Aktivera** för att aktivera hantering av statisk webbplats för lagringskontot.
1. I fältet **Indexdokumentnamn** anger du standardindexsidan *index.html*. Standardindexsidan visas när en användare navigerar till roten för den statiska webbplatsen.  
1. I fältet **Sökväg för feldokument** anger du standardfelsidan *404. html*. Standardfelsidan visas när en användare försöker navigera till en sida som inte finns på den statiska webbplatsen.
1. Klicka på **Spara**. Azure-portalen visar nu slutpunkten för den statiska webbplatsen. 

    ![Aktivera hantering av statisk webbplats för ett lagringskonto](media/storage-blob-static-website-host/enable-static-website-hosting.png)

## <a name="deploy-a-hello-world-website"></a>Distribuera en Hello World-webbplats

Nu skapar du en Hello World-webbplats med Visual Studio Code och distribuerar den till statiska webbplats som hanteras i Azure Storage-kontot.

1. Skapa en tom mapp med namnet *mywebsite* på det lokala filsystemet. 
1. Starta Visual Studio Code och öppna den mapp som just skapade från panelen **Utforskaren**.

    ![Öppna mapp i Visual Studio Code](media/storage-blob-static-website-host/open-folder-vs-code.png)

1. Skapa standardindexfilen i mappen *mywebsite* mapp och ge den namnet *index.html*.

    ![Skapa standardindexfilen i Visual Studio Code](media/storage-blob-static-website-host/create-index-file-vs-code.png)

1. Öppna *index.html* i redigeringsprogrammet, klistra in följande text i filen och spara den:

    ```
    <h1>Hello World!</h1>
    ```

1. Skapa standardfelfilen och ge den namnet *404.html*.
1. Öppna *404.html* i redigeringsprogrammet, klistra in följande text i filen och spara den:

    ```
    <h1>404</h1>
    ```

1. Högerklicka under mappen *mywebsite* i panelen **Utforskaren** och välj **Distribuera till statisk webbplats...**  för att distribuera webbplatsen. Du uppmanas att logga in på Azure för att hämta en lista över prenumerationer.

1. Välj den prenumeration som innehåller det lagringskonto som du aktiverade hantering av statisk webbplats för. Välj sedan lagringskontot när du tillfrågas.

Visual Studio Code laddar nu upp filerna till webbslutpunkten och visar statusfältet för processen. Starta webbplatsen för att visa den i Azure.

![Visa distribution av statisk webbplats i Azure](media/storage-blob-static-website-host/view-static-website-endpoint.png)

Du har slutfört självstudien och distribuerat en statisk webbplats till Azure.

## <a name="next-steps"></a>Nästa steg

I del ett i den här självstudien lärde du dig att konfigurera ett Azure Storage-konto för hantering av en statisk webbplats, och du skapade och distribuerade en statisk webbplats till en Azure-slutpunkt.

Nu kan du gå vidare till del två, där du konfigurerar en anpassad domän med SSL för din statiska webbplats med Azure CDN.

> [!div class="nextstepaction"]
> [Använda Azure CDN för att aktivera en anpassad domän med SSL för en statisk webbplats](storage-blob-static-website-custom-domain.md)
