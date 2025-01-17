---
title: Snabbstart – skapa en Unity Android-app med Azure Spatial ankare | Microsoft Docs
description: I den här snabbstarten lär du dig att skapa en Android-app med Unity med hjälp av Spatial Anchors.
author: craigktreasure
manager: aliemami
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 642abfb99b40d67802b7194ad225ebcd2872a72b
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/27/2019
ms.locfileid: "67135123"
---
# <a name="quickstart-create-a-unity-android-app-with-azure-spatial-anchors"></a>Snabbstart: Skapa en Unity Android-app med Azure Spatial ankare

Den här snabbstarten beskrivs hur du skapar en Unity Android-app med hjälp av [Azure Spatial ankare](../overview.md). Azure Spatial Anchors är en plattformsoberoende utvecklartjänst som du kan använda för att skapa upplevelser med mixad verklighet med hjälp av objekt som bevarar sin plats mellan enheter över tid. När du är klar har du en ARCore Android-app som skapats med Unity och som kan spara och återkalla en spatial fästpunkt.

Du lär dig följande:

> [!div class="checklist"]
> * Skapa ett Spatial Anchors-konto
> * Förbereda Unity-bygginställningar
> * Ladda ned och importera ARCore SDK för Unity
> * Konfigurera kontoidentifierare och kontonyckel för Spatial Anchors
> * Exportera Android Studio-projektet
> * Distribuera och köra på en Android-enhet

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Nödvändiga komponenter

Kontrollera att du har följande så att du kan utföra den här snabbstarten:

- En Windows- eller macOS-dator med <a href="https://unity3d.com/get-unity/download" target="_blank">Unity 2018.3 +</a> och <a href="https://developer.android.com/studio/" target="_blank">Android Studio 3.3 +</a>.
  - Om körs på Windows kan du också se <a href="https://git-scm.com/download/win" target="_blank">Git för Windows</a>.
  - Om du kör på macOS, får du Git installerade via HomeBrew. Ange följande kommando i en enda rad terminalen: `/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"`. Kör sedan `brew install git`.
- En <a href="https://developer.android.com/studio/debug/dev-options" target="_blank">utvecklaraktiverad</a> och <a href="https://developers.google.com/ar/discover/supported-devices" target="_blank">ARCore-kapabel</a> Android-enhet.
- Din app måste använda version **1.7** av ARCore SDK för Unity.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="download-and-open-the-unity-sample-project"></a>Ladda ned och öppna exempelprojektet Unity

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

[!INCLUDE [Open Unity Project](../../../includes/spatial-anchors-open-unity-project.md)]

[!INCLUDE [Android Unity Build Settings](../../../includes/spatial-anchors-unity-android-build-settings.md)]

## <a name="configure-account-identifier-and-key"></a>Konfigurera kontoidentifierare och nyckel

I fönstret **Projekt** går du till `Assets/AzureSpatialAnchorsPlugin/Examples` och öppnar scenfilen `AzureSpatialAnchorsBasicDemo.unity`.

[!INCLUDE [Configure Unity Scene](../../../includes/spatial-anchors-unity-configure-scene.md)]

Spara scenen genom att välja **Arkiv** -> **Spara**.

## <a name="export-the-android-studio-project"></a>Exportera Android Studio-projektet

[!INCLUDE [Export Unity Project](../../../includes/spatial-anchors-unity-export-project-snip.md)]

Se till att kryssrutan **Exportera projekt** inte har en kryssmarkering. Klicka på **Skapa och kör**. Du uppmanas att spara din `.apk`-fil. Du kan välja ett valfritt namn för den.

Följ instruktionerna i appen för att placera och återkalla en fästpunkt.

> [!NOTE]
> Om du inte ser kameran som bakgrund när du kör appen (om du till exempel ser en tom, blå eller annan textur) behöver du förmodligen återimportera tillgångarna i Unity. Stoppa appen. Från den översta menyn i Unity väljer du **Assets -> Reimport all** (Tillgångar -> Återimportera alla). Kör sedan appen igen.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Självstudie: Dela Spatial Anchors mellan olika enheter](../tutorials/tutorial-share-anchors-across-devices.md)
