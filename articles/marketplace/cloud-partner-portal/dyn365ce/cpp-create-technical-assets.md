---
title: Skapa Dynamics 365 för kundengagemang tekniska resurser | Azure Marketplace
description: Skapa tekniska resurser för en Dynamics 365 för kundengagemang programmet erbjudande.
services: Dynamics 365 for Customer Engagement, Azure, Marketplace, Cloud Partner Portal, AppSource
author: v-miclar
ms.service: marketplace
ms.topic: article
ms.date: 12/29/2018
ms.author: pabutler
ms.openlocfilehash: eff175264677d6b8ffb885229b5e68b306424335
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64943082"
---
# <a name="create-technical-assets-for-azure-application-offer"></a>Skapa tekniska resurser för erbjudande för Azure-program

Vanligtvis du utvecklar lösningar med hjälp av den [SDK för Dynamics 365 för kundengagemang appar](https://docs.microsoft.com/dynamics365/customer-engagement/developer/get-started-sdk).  Lösningar som tar olika former, enligt beskrivningen i [programmeringsmodeller för Dynamics 365 för kundengagemang appar](https://docs.microsoft.com/dynamics365/customer-engagement/developer/programming-models).  Välja formuläret som bäst motsvarar dina krav för lösningen.  När du utvecklar en lösning, finns det ett antal problem som du måste lösa som utökningsbarhet val, komponenter och versionskompatibilitet.  Mer information finns i [introduktion till lösningar](https://docs.microsoft.com/dynamics365/customer-engagement/developer/introduction-solutions).

De flesta av Dynamics 365-lösningar som publicerats till AppSource är hanterade program som distribueras som paketfilerna.


## <a name="creating-and-storing-the-package"></a>Skapar och lagrar paketet

Parallell dokumentationen genom att skapa Dynamics 365 för kundengagemang erbjuder finns i avsnittet [publicera din app på AppSource](https://docs.microsoft.com/dynamics365/customer-engagement/developer/publish-app-appsource).  Följande innehåller avsnitt om information om hur du skapar paketet lösningsfilen och överföra den till Azure storage:

- [Steg 4: Skapa ett AppSource-paket för din app](https://docs.microsoft.com/dynamics365/customer-engagement/developer/create-package-app-appsource) – beskriver hur du skapar en komprimerad (zip)-fil som representerar det hanterade programmet och innehåller: lösningsmappen tillgångar, anpassad kod DLL-filen, filen MIME-typ, AppSource paketikon, licens villkor (HTML)-fil och innehållsfil (XML).
- [Steg 5: Store AppSource-paketet på Azure Storage och generera en URL med SAS-nyckel](https://docs.microsoft.com/dynamics365/customer-engagement/developer/store-appsource-package-azure-storage) -förklarar hur du lagrar en AppSource paketfil i Microsoft Azure Blob storage-kontot och använda en signatur för delad åtkomst (SAS) för att dela paketfilen. Din paketfilen hämtas från Azure Storage-plats för certifiering och välj sedan AppSource utvärderingsversioner och publicering.


## <a name="next-steps"></a>Nästa steg

Om du inte redan har gjort det, [skapa din Dynamics 365 för kundengagemang erbjudande](./cpp-create-offer.md).  Så du kommer att vara redo att [publicera ditt erbjudande](./cpp-publish-offer.md).
