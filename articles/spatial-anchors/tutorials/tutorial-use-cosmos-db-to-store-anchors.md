---
title: Självstudie – dela Azure Spatial ankare flera sessioner och enheter med ett Azure Cosmos DB-serverdel | Microsoft Docs
description: I den här självstudien får lära du att dela Azure Spatial ankare identifierare i Android/iOS-enheter i Unity med en backend tjänst och Azure Cosmos DB.
author: ramonarguelles
manager: vicenterivera
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/24/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: fc172b5327d72687fea7d13ddb706ecc7ab630b6
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/27/2019
ms.locfileid: "67135361"
---
# <a name="tutorial-share-azure-spatial-anchors-across-sessions-and-devices-with-an-azure-cosmos-db-back-end"></a>Självstudier: Dela Azure Spatial ankare flera sessioner och enheter med ett Azure Cosmos DB-serverdel

I den här självstudien får du lära dig hur du använder [Azure Spatial ankare](../overview.md) att skapa fästpunkter under en session och leta sedan upp dem i en annan session, på samma enhet eller på ett annat. Den andra sessionen kan exempelvis vara en annan dag. Dessa samma ankare kan också finnas av flera enheter på samma plats och på samma gång.

![GIF som illustrerar objektet persistence](./media/persistence.gif)

[Azure Spatial ankare](../overview.md) är en plattformsoberoende developer-tjänst som du kan använda för att skapa Mixad verklighet upplevelser med objekt som kvarstår befann i enheter över tid. När du är klar har du en app som kan distribueras till två eller fler enheter. Spatial ankare som skapats av en instans dela sina identifierare med andra med hjälp av Azure Cosmos DB.

Du lär dig följande:

> [!div class="checklist"]
> * Distribuera en ASP.NET Core-webbapp i Azure som kan användas för att dela ankare, lagra dem i Azure Cosmos DB.
> * Konfigurera AzureSpatialAnchorsLocalSharedDemo scenen i Unity-exempel från Azure snabbstarter för att dra nytta av delning ankare Web App.
> * Distribuera en app till en eller flera enheter och kör den.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [Share Anchors Sample Prerequisites](../../../includes/spatial-anchors-share-sample-prereqs.md)]

Det är värt att, även om du ska använda Unity och Azure Cosmos DB i den här självstudien, det är bara för att ge dig ett exempel på hur du delar Spatial ankare identifierare på enheter. Du kan använda andra språk och serverdelstekniker för att uppnå samma resultat. ASP.NET Core-webbapp som används i den här självstudien kräver också .NET Core 2.2 SDK. Den fungerar på rätt sätt på Web Apps för Windows, men det för närvarande körs inte i Web Apps för Linux.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="create-a-database-account"></a>Skapa ett databaskonto

[!INCLUDE [cosmos-db-create-dbaccount-table](../../../includes/cosmos-db-create-dbaccount-table.md)]

Kopiera den `Connection String` eftersom du behöver den.

## <a name="download-the-unity-sample-project"></a>Ladda ned exempelprojektet Unity

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

## <a name="deploy-the-sharing-anchors-service"></a>Distribuera delningsapplikationen fästpunkter tjänsten

Öppna Visual Studio och öppna projektet i den `Sharing\SharingServiceSample` mapp.

### <a name="configure-the-service-to-use-your-azure-cosmos-db-database"></a>Konfigurera tjänsten så att din Azure Cosmos DB-databas

I **Solution Explorer**öppnar `SharingService\Startup.cs`.

Leta upp `#define INMEMORY_DEMO` överst i filen och kommentera som linje ut. Spara filen.

I **Solution Explorer**öppnar `SharingService\appsettings.json`.

Leta upp den `StorageConnectionString` egenskap och ange det värde som ska vara samma som den `Connection String` värde som du kopierade i den [skapa en databas konto steg](#create-a-database-account). Spara filen.

[!INCLUDE [Publish Azure](../../../includes/spatial-anchors-publish-azure.md)]

[!INCLUDE [Run Share Anchors Sample](../../../includes/spatial-anchors-run-share-sample.md)]

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du använda Azure Cosmos DB för att dela fästpunktsidentifierare mellan enheter. Om du vill veta mer om hur du använder Azure Spatial fästpunkter i en ny Unity HoloLens-app kan du fortsätta till nästa självstudie.

> [!div class="nextstepaction"]
> [Startar en ny Android-app](./tutorial-new-unity-hololens-app.md)