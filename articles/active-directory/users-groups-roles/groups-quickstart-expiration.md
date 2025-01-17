---
title: Snabbstart om förfalloprincip för Office 365-grupper – Azure Active Directory | Microsoft Docs
description: Förfallotid för Office 365-grupper – Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: quickstart
ms.date: 05/06/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 01cedadc115496fcf00df986b4ad4b9c5aab5139
ms.sourcegitcommit: 6ea7f0a6e9add35547c77eef26f34d2504796565
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/14/2019
ms.locfileid: "65606205"
---
# <a name="quickstart-set-office-365-groups-to-expire-in-azure-active-directory"></a>Snabbstart: Konfigurera Office 365-grupper att upphöra att gälla i Azure Active Directory

I den här snabbstarten anger du en förfalloprincip för dina Office 365-grupper. När användare kan konfigurera sina egna grupper kan det uppstå många oanvända grupper. Ett sätt att hantera oanvända grupper är att ställa in dessa grupper att upphöra, för att minska underhållet med att ta bort grupper manuellt.

Förfalloprinciper är enkla:

* Gruppägare meddelas om att förnya en grupp som håller på att upphöra
* Grupper som inte förnyas tas bort
* En borttagen Office 365-grupp kan återställas inom 30 dagar av en gruppägare eller en Azure AD-administratör

Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisite"></a>Förutsättning

 Rollen lägsta behörighet som krävs för att ställa in förfallodatum är administratören i organisationen.

## <a name="turn-on-user-creation-for-groups"></a>Aktivera användarskapande för grupper

1. Logga in på den [Azure-portalen](https://portal.azure.com) användare med ett administratörskonto.

2. Välj **Grupper** och välj sedan **Allmänt**.
  
   ![Inställningssidan för självbetjäning](./media/groups-quickstart-expiration/self-service-settings.png)

3. Ställ in **Användare kan skapa Office 365-grupper** till **Ja**.

4. Välj **Spara** för att spara gruppinställningarna när du är klar.

## <a name="set-group-expiration"></a>Ange gruppförfallotid

1. Logga in på den [Azure-portalen](https://portal.azure.com)väljer **Azure Active Directory** > **grupper** > **upphör att gälla** till Öppna inställningar för giltighetstid.
  
   ![Upphör att gälla inställningssidan för grupp](./media/groups-quickstart-expiration/expiration-settings.png)

2. Ange förfallointervallet. Välj ett förinställt värde eller ange ett anpassat värde över 31 dagar. 

3. Ange en e-postadress dit meddelanden om förfallotid ska skickas när en grupp inte har någon ägare.

4. För den här snabbstarten ställer du in **Aktivera förfallotid för de här Office 365-grupperna** till **Alla**.

5. Välj **Spara** för att spara förfalloinställningarna när du är klar.

Klart! I den här snabbstarten ställde du in en förfalloprincip för de valda Office 365-grupperna.

## <a name="clean-up-resources"></a>Rensa resurser

### <a name="to-remove-the-expiration-policy"></a>Ta bort en princip

1. Se till att du är inloggad på [Azure-portalen](https://portal.azure.com) med ett konto som är global administratör för klientorganisationen.
2. Välj **Azure Active Directory** > **Grupper** > **Utgångsdatum**.
3. Ställ in **Aktivera förfallotid för de här Office 365-grupperna** på **Inga**.

### <a name="to-turn-off-user-creation-for-groups"></a>Inaktivera användare skapas för grupper

1. Välj **Azure Active Directory** > **Grupper** > **Allmänt**. 
2. Ställ in **Användare kan skapa Office 365-grupper i Azure-portaler** till **Nej**.

## <a name="next-steps"></a>Nästa steg

Mer information om förfallodatum, inklusive PowerShell-instruktioner och tekniska begränsningar finns i följande artikel:

> [!div class="nextstepaction"]
> [Förfalloprincip PowerShell](groups-lifecycle.md)
