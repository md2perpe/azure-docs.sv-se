---
title: Lämna en organisation som gästanvändare – Azure Active Directory | Microsoft Docs
description: Visar hur en Azure AD B2B-gästanvändare lämna en organisation med hjälp av åtkomstpanelen.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 06/13/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 26d9eb883cc014c1bea092a12e22b6d144a37994
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67112972"
---
# <a name="leave-an-organization-as-a-guest-user"></a>Lämna en organisation som gästanvändare

En Azure Active Directory (Azure AD) B2B-gästanvändare kan välja att lämna en organisation när som helst om de inte längre behöver använda appar från den organisationen eller underhålla eventuella kopplingar. En användare kan lämna en organisation på egen hand, utan att behöva kontakta en administratör.

> [!NOTE]
> Gästanvändare kan inte lämna en organisation om deras konto är inaktiverat i antingen den hemnätverk eller resurs-klient. Om kontot har inaktiverats kan behöver gästanvändaren kontakta klientorganisationens administratör som kan ta bort gästkontot eller aktivera gästkontot så att användaren kan lämna organisationen.

## <a name="leave-an-organization"></a>Lämna en organisation

Följ dessa steg om du vill lämna en organisation.

1. Gå till sidan Åtkomstpanelsprofil genom att göra något av följande steg:
   
   - I den [Azure-portalen](https://portal.azure.com), klicka på ditt namn i övre högra och välj **visa konto**.
   - Öppna din [åtkomstpanelen](https://myapps.microsoft.com), klickar du på namnet på din uppe till höger och Nästa om du vill **organisationer**, välja inställningsikonen (kugghjulet).
 
   ![Skärmbild som visar användarinställningar i åtkomstpanelen](media/leave-the-organization/UserSettings.png) 

   > [!NOTE]
   > Om du inte redan är inloggad på organisationen som du vill lämna under **organisationer**, klickar du på den **logga in för att lämna organisationen** länken bredvid organisationens namn. När du har loggat in, klickar du på namnet på din igen i det övre högra hörnet och bredvid **organisationer**, välja inställningsikonen (kugghjulet).

3. Under **organisationer**, hitta den organisation som du vill lämna och välj **lämna organisationen**.

   ![Skärmbild som visar lämna organisationen alternativet i användargränssnittet](media/leave-the-organization/LeaveOrg.png)

4. När du ombeds bekräfta Välj **lämna**. 

## <a name="account-removal"></a>Kontot tagits bort

När en användare lämnar en organisation, användarkontot ”ej är permanent borttagen” i katalogen. Som standard användarobjektet flyttas till den **borttagna användare** område i Azure AD men inte tas bort permanent i 30 dagar. Den här mjuk borttagning kan administratören återställa användarkontot (inklusive grupper och behörigheter), om användaren gör en begäran om att återställa kontot inom 30-dagarsperiod.

Om du vill kan en Innehavaradministratör kan permanent ta bort kontot när som helst under 30-dagarsperiod. Gör så här:

1. I den [Azure-portalen](https://portal.azure.com)väljer **Azure Active Directory**.
2. Under **Hantera** väljer du **Användare**.
3. Välj **borttagna användare**.
4. Markera kryssrutan bredvid en borttagen användare och välj sedan **ta bort permanent**.

Om du permanent ta bort en användare är den här åtgärden oåterkallelig.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="next-steps"></a>Nästa steg

- En översikt över Azure AD B2B finns i [vad är Azure AD B2B-samarbete?](what-is-b2b.md)



