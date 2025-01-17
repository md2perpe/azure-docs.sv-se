---
title: Snabbstart Ladda ned en inloggningsrapport med Azure-portalen | Microsoft Docs
description: Lär dig hur du laddar ned en inloggningsrapport med Azure-portalen
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 9131f208-1f90-4cc1-9c29-085cacd69317
ms.service: active-directory
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 86bc72f69903134afa3750ad6b72486a713b6cc0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60285894"
---
# <a name="quickstart-download-a-sign-in-report-using-the-azure-portal"></a>Snabbstart: Hämta en inloggningsrapport med Azure-portalen

I den här snabbstarten får du lära dig hur du hämtar inloggningsloggarna för din klient för de senaste 24 timmarna. Du kan hämta upp till 250 000 poster från Azure-portalen. Poster är sorterade efter de senaste så att som standard, du får de senaste 250 000 posterna. 

## <a name="prerequisites"></a>Nödvändiga komponenter

Du behöver:

* En Azure Active Directory-klient, med en premiumlicens för att visa inloggningsrapporten. Se [Kom igång med Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) för att uppgradera din Azure Active Directory-version. Observera att om du inte har några aktivitetsdata före uppgraderingen tar det ett par dagar innan data visas i rapporterna när du har uppgraderat till en premiumlicens.
* En användare som har rollen **säkerhetsadministratör,**,  **säkerhetsläsare**, **rapportläsare** eller **global administratör** för klienten. Dessutom kan alla användare i klienten kan komma åt sina egna inloggningsloggar.

## <a name="quickstart-download-a-sign-in-report"></a>Snabbstart: Ladda ned en inloggningsrapport

1. Navigera till [Azure-portalen](https://portal.azure.com).
2. Välj **Azure Active Directory** från det vänstra navigeringsfönstret och använd **Växla katalog** för att välja din active directory.
3. Från instrumentpanelen väljer du **Azure Active Directory** och sedan **Inloggningsloggar**. 
4. Välj **senaste 24 timmarna** i filterlistrutan **datum** och välj **Tillämpa** för att visa inloggningsloggarna för de senaste 24 timmarna. 
5. Välj den **hämta** knapp, väljer **CSV** som filen formatera och ange ett filnamn för att hämta en CSV-fil som innehåller de filtrerade posterna. 

![Rapportering](./media/quickstart-download-sign-in-report/download-sign-ins.png)

## <a name="next-steps"></a>Nästa steg

* [Rapporter om inloggningsaktiviteter i Azure Active Directory-portalen](concept-sign-ins.md)
* [Azure Active Directory-rapporteringskvarhållning](reference-reports-data-retention.md)
* [Azure Active Directory-rapporteringssvarstider](reference-reports-latencies.md)
