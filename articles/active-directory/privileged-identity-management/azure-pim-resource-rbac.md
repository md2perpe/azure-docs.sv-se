---
title: Visa aktivitet och granska historik för Azure-resursroller i PIM - Azure Active Directory | Microsoft Docs
description: Visa aktivitet och granskningshistorik för Azure-resursroller i Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 04/09/2019
ms.author: rolyon
ms.collection: M365-identity-device-management
ms.openlocfilehash: 84bd491d992ed15df288d9226b58bfe832e0692a
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2019
ms.locfileid: "67476502"
---
# <a name="view-activity-and-audit-history-for-azure-resource-roles-in-pim"></a>Visa aktivitet och granskningshistorik för Azure-resursroller i PIM

Med Azure Active Directory (Azure AD) Privileged Identity Management (PIM), kan du visa inloggningsaktivitet, aktiveringar och granskningshistorik för Azure-resursroller inom din organisation. Detta inkluderar prenumerationer, resursgrupper och även virtuella datorer. Alla resurser i Azure-portalen som utnyttjar Azure rollbaserad åtkomstkontroll (RBAC)-funktioner kan dra nytta av säkerhets- och livscykel hanteringsfunktioner i PIM.

## <a name="view-activity-and-activations"></a>Visa aktivitet och aktiveringar

Om du vill se vilka åtgärder som en viss användare har gjort i olika resurser, kan du visa aktiviteten Azure-resurs som är associerat med en viss aktiveringsperioden.

1. Öppna **Azure AD Privileged Identity Management**.

1. Klicka på **Azure-resurser**.

1. Klicka på den resurs som du vill visa aktivitet och aktiveringar för.

1. Klicka på **roller** eller **medlemmar**.

1. Klicka på en användare.

    Du kan se en grafisk vy över användaråtgärder i Azure-resurser efter datum. Den visar även de senaste rollaktiveringar under den samma tidsperioden.

    ![Information om användare med resursen aktivitet sammanfattning och rollen aktiveringar](media/azure-pim-resource-rbac/rbac-user-details.png)

1. Klicka på en specifik rollaktivering om du vill visa detaljer och motsvarande Azure-resurs-aktivitet som inträffade när användaren var aktiv.

    ![Rollaktivering som valts och aktivitetsinformation som visas efter datum](media/azure-pim-resource-rbac/rbac-user-resource-activity.png)

## <a name="export-role-assignments-with-children"></a>Exportera rolltilldelningar med underordnade

Du kan ha efterföljandekrav där du måste ange en fullständig lista över rolltilldelningar till granskare. PIM låter dig fråga rolltilldelningar i en specifik resurs, vilket innefattar rolltilldelningar för alla underordnade resurser. Tidigare, det var svårt för administratörer att få en fullständig lista över rolltilldelningar för en prenumeration och de tvungna att exportera rolltilldelningar för varje specifik resurs. Använda PIM kan fråga du efter alla aktiva och berättigade rolltilldelningar i en prenumeration inklusive rolltilldelningar för alla resursgrupper och resurser.

1. Öppna **Azure AD Privileged Identity Management**.

1. Klicka på **Azure-resurser**.

1. Klicka på den resurs som du vill exportera rolltilldelningar för, till exempel en prenumeration.

1. Klicka på **medlemmar**.

1. Klicka på **exportera** att öppna fönstret Export medlemskap.

    ![Exportera medlemskap fönstret om du vill exportera alla medlemmar](media/azure-pim-resource-rbac/export-membership.png)

1. Klicka på **exportera alla medlemmar** att exportera alla rolltilldelningar i en CSV-fil.

    ![Exporterade rolltilldelningar i CSV-fil som visas i Excel](media/azure-pim-resource-rbac/export-csv.png)

## <a name="view-resource-audit-history"></a>Visa resurs granskningshistorik

Resursgranskning ger dig en överblick över alla roll-aktivitet för en resurs.

1. Öppna **Azure AD Privileged Identity Management**.

1. Klicka på **Azure-resurser**.

1. Klicka på den resurs som du vill visa granskningshistorik för.

1. Klicka på **resursgranskning**.

1. Filtrera historiken med ett fördefinierat datum eller ett anpassat intervall.

    ![Granska resurslistan med filter](media/azure-pim-resource-rbac/rbac-resource-audit.png)

1. För **granskningstyp**väljer **aktivera (tilldelad + aktiverad)** .

    ![Granska resurslistan som filtreras efter aktivera granskningstyp](media/azure-pim-resource-rbac/rbac-audit-activity.png)

1. Under **åtgärd**, klickar du på **(aktivitet)** för en användare att se användarens aktivitetsinformation i Azure-resurser.

    ![Användaren aktivitetsinformation för en viss åtgärd](media/azure-pim-resource-rbac/rbac-audit-activity-details.png)

## <a name="view-my-audit"></a>Visa min granskning

Min granskning kan du visa din personliga roll-aktivitet.

1. Öppna **Azure AD Privileged Identity Management**.

1. Klicka på **Azure-resurser**.

1. Klicka på den resurs som du vill visa granskningshistorik för.

1. Klicka på **min granskning**.

1. Filtrera historiken med ett fördefinierat datum eller ett anpassat intervall.

    ![Granska listan för den aktuella användaren](media/azure-pim-resource-rbac/my-audit-time.png)

## <a name="next-steps"></a>Nästa steg

- [Tilldela Azure-resursroller i PIM](pim-resource-roles-assign-roles.md)
- [Godkänn eller neka begäranden för Azure-resursroller i PIM](pim-resource-roles-approval-workflow.md)
- [Visa granskningshistorik för Azure AD-roller i PIM](pim-how-to-use-audit-log.md)
