---
title: Snabbstart – Skapa iOS-app med Azure Spatial Anchors | Microsoft Docs
description: I den här snabbstarten lär du dig att skapa en iOS-app med hjälp av Spatial Anchors.
author: craigktreasure
manager: aliemami
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: 56360238db8632e74a95c057a7fe643b5cea3151
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67206839"
---
# <a name="quickstart-create-an-ios-app-with-azure-spatial-anchors-in-either-swift-or-objective-c"></a>Snabbstart: Skapa en iOS-app med Azure Spatial Anchors i antingen Swift eller Objective-C

I den här snabbstarten beskrivs hur du skapar en iOS-app med hjälp av [Azure Spatial Anchors](../overview.md) i antingen Swift eller Objective-C. Azure Spatial Anchors är en plattformsoberoende utvecklartjänst som du kan använda för att skapa Mixed Reality-upplevelser med hjälp av objekt som bevarar sin plats mellan enheter över tid. När du är klar har du en ARKit iOS-app som kan spara och återkalla en spatial fästpunkt.

Du lär dig följande:

> [!div class="checklist"]
> * Skapa ett Spatial Anchors-konto
> * Konfigurera kontoidentifierare och kontonyckel för Spatial Anchors
> * Distribuera och köra på en iOS-enhet

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Nödvändiga komponenter

Kontrollera att du har följande så att du kan utföra den här snabbstarten:

- En utvecklaraktiverad macOS-dator med <a href="https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12" target="_blank">Xcode 10+</a> och <a href="https://cocoapods.org" target="_blank">CocoaPods</a> installerat.
- Git installerade via HomeBrew. Ange följande kommando i en enda rad terminalen: `/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"`. Kör sedan `brew install git`.
- En utvecklaraktiverad <a href="https://developer.apple.com/documentation/arkit/verifying_device_support_and_user_permission" target="_blank">ARKit-kompatibel</a> iOS-enhet.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="open-the-sample-project"></a>Öppna exempelprojektet

Använda terminalen för att utföra följande åtgärder.

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

Installera nödvändiga poddar med hjälp av CocoaPods:

# <a name="swifttabopenproject-swift"></a>[Swift](#tab/openproject-swift)

Navigera till `iOS/Swift/`.

```bash
cd ./iOS/Swift/
```

# <a name="objective-ctabopenproject-objc"></a>[Objective-C](#tab/openproject-objc)

Navigera till `iOS/Objective-C/`.

```bash
cd ./iOS/Objective-C/
```

---

Kör `pod install --repo-update` för att installera CocoaPods för projektet.

Öppna nu `.xcworkspace` i Xcode.

# <a name="swifttabopenproject-swift"></a>[Swift](#tab/openproject-swift)

```bash
open ./SampleSwift.xcworkspace
```

# <a name="objective-ctabopenproject-objc"></a>[Objective-C](#tab/openproject-objc)

```bash
open ./SampleObjC.xcworkspace
```

---

## <a name="configure-account-identifier-and-key"></a>Konfigurera kontoidentifierare och nyckel

Nästa steg är att konfigurera appen att använda ditt konto-ID och kontonyckeln. Du kopierade dem i en textredigerare när [konfigurerar Spatial ankare resursen](#create-a-spatial-anchors-resource).

# <a name="swifttabopenproject-swift"></a>[Swift](#tab/openproject-swift)

Öppna `iOS/Swift/SampleSwift/ViewControllers/BaseViewController.swift`.

Leta upp fältet `spatialAnchorsAccountKey` och ersätt `Set me` med kontonyckeln.

Leta upp fältet `spatialAnchorsAccountId` och ersätt `Set me` med kontoidentifieraren.

# <a name="objective-ctabopenproject-objc"></a>[Objective-C](#tab/openproject-objc)

Öppna `iOS/Objective-C/SampleObjC/BaseViewController.m`.

Leta upp fältet `SpatialAnchorsAccountKey` och ersätt `Set me` med kontonyckeln.

Leta upp fältet `SpatialAnchorsAccountId` och ersätt `Set me` med kontoidentifieraren.

---

## <a name="deploy-the-app-to-your-ios-device"></a>Distribuera appen till din iOS-enhet

Ansluta iOS-enheten till Mac-datorn och ange **det aktiva schemat** till iOS-enheten.

![Välja enheten](./media/get-started-ios/select-device.png)

Välj **Build and then run the current scheme** (Skapa och kör sedan det aktuella schemat).

![Distribuera och köra](./media/get-started-ios/deploy-run.png)

> [!NOTE]
> Om du ser felet `library not found for -lPods-SampleObjC` har du sannolikt öppnat filen `.xcodeproj` i stället för `.xcworkspace`. Öppna `.xcworkspace` och försök igen.

I Xcode stoppar du appen genom att trycka på **Stoppa**.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [Självstudie: Dela Spatial Anchors mellan olika enheter](../tutorials/tutorial-share-anchors-across-devices.md)
