---
title: Snabbstart Ladda ned en granskningsrapport med Azure-portalen | Microsoft Docs
description: Lär dig hur du laddar ned en granskningsrapport med Azure-portalen
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 4de121ea-f4aa-4c8a-aae4-700c2c5e97a2
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
ms.openlocfilehash: a6cbea49fe39c92c8a2fc50e501cb4ef5cff74b1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60286456"
---
# <a name="quickstart-download-an-audit-report-using-the-azure-portal"></a>Snabbstart: Ladda ned en granskningsrapport med hjälp av Azure-portalen

I den här snabbstarten har du lära dig hur du hämtar en CSV-fil med granskningsloggarna för din klient för de senaste 24 timmarna. Du kan hämta upp till 250 000 poster från Azure-portalen. Poster är sorterade efter de senaste så att som standard, du får de senaste 250 000 posterna. 

## <a name="prerequisites"></a>Nödvändiga komponenter

Du behöver:

* En Azure Active Directory-klientorganisation. 
* En användare som har rollen **säkerhetsadministratör**, **säkerhetsläsare** eller **global administratör** för klienten. Dessutom kan alla användare i klienten kan komma åt sina egna granskningsloggar.

## <a name="quickstart-download-an-audit-report"></a>Snabbstart: Ladda ned en granskningsrapport

1. Navigera till [Azure-portalen](https://portal.azure.com).
2. Välj **Azure Active Directory** från det vänstra navigeringsfönstret och använd **Växla katalog** för att välja din active directory.
3. Från instrumentpanelen väljer du **Azure Active Directory** och sedan **Granskningsloggar**. 
4. Välj **senaste 24 timmarna** i filterlistrutan **datumintervall** och välj **Tillämpa** för att visa granskningsloggarna för de senaste 24 timmarna. 
5. Välj den **hämta** knapp, väljer **CSV** som filen formatera och ange ett filnamn för att hämta en CSV-fil som innehåller de filtrerade posterna. 

![Rapportering](./media/quickstart-download-audit-report/download-audit-logs.png)

## <a name="next-steps"></a>Nästa steg

* [Rapporter om inloggningsaktiviteter i Azure Active Directory-portalen](concept-sign-ins.md)
* [Azure Active Directory-rapporteringskvarhållning](reference-reports-data-retention.md)
* [Azure Active Directory-rapporteringssvarstider](reference-reports-latencies.md)
