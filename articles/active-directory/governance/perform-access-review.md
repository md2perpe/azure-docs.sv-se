---
title: Granska åtkomst till grupper eller program i åtkomstgranskningar - Azure Active Directory | Microsoft Docs
description: Lär dig hur du granskar åtkomst av medlemmar i gruppen eller programmet åtkomst i Azure Active Directory-åtkomstgranskningar.
services: active-directory
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 05/21/2019
ms.author: rolyon
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: b6f73d3bf5e502a758dd46561059c15a2970d9b6
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2019
ms.locfileid: "67471829"
---
# <a name="review-access-to-groups-or-applications-in-azure-ad-access-reviews"></a>Granska åtkomst till grupper eller program i Azure AD-åtkomstgranskningar

Azure Active Directory (Azure AD) förenklar hur företag hantera åtkomst till grupper och program i Azure AD och andra Microsoft Online Services med en funktion som kallas åtkomst till Azure AD-åtkomstgranskningar.

Den här artikeln beskrivs hur en avsedda granskare utför en åtkomstgranskning för medlemmar i en grupp eller användare med åtkomst till ett program.

## <a name="prerequisites"></a>Förutsättningar

- Azure AD Premium P2

Mer information finns i [vilka användare måste ha licenser?](access-reviews-overview.md#which-users-must-have-licenses).

## <a name="open-the-access-review"></a>Öppna åtkomstgranskningen

Det första steget för att utföra en åtkomstgranskning är att leta upp och öppna åtkomstgranskningen.

1. Leta efter ett e-postmeddelande från Microsoft som ber dig att granska åtkomst. Här är ett e-postmeddelande med exempel för att granska åtkomsten för en grupp.

    ![Exempel e-post från Microsoft för att granska åtkomst till en grupp](./media/perform-access-review/access-review-email.png)

1. Klicka på den **börja granska** länk för att öppna åtkomstgranskningen.

Du kan hitta din väntande åtkomstgranskningar genom att följa dessa steg om du inte har e-postmeddelandet.

1. Logga in på MyApps-portalen på [ https://myapps.microsoft.com ](https://myapps.microsoft.com).

    ![Visa en lista över appar som du har behörighet att MyApps-portalen](./media/perform-access-review/myapps-access-panel.png)

1. I det övre högra hörnet på sidan klickar du på användarsymbolen, som visar ditt namn och standardorganisationen. Om mer än en organisation visas väljer du den organisation som begärde en åtkomstgranskning.

1. Klicka på den **Åtkomstgranskningar** ikonen för att visa en lista över väntande åtkomstgranskningar.

    Om panelen inte är synlig finns det inga åtkomstgranskningar att utföra för den organisationen, och ingen åtgärd krävs just nu.

    ![Väntande granskningar åtkomstlista för appar och grupper](./media/perform-access-review/access-reviews-list.png)

1. Klicka på den **påbörja granskning** länk för åtkomstgranskning som du vill utföra.

## <a name="perform-the-access-review"></a>Utför åtkomstgranskningen

När du har öppnat åtkomstgranskningen kan se du namnen på användare som behöver granskas.

Om begäran är att granska dina egna åtkomst, ser sidan annorlunda. Mer information finns i [granska åtkomst själv till grupper eller program](review-your-access.md).

![Öppna åtkomstgranskning lista de användare som behöver granskas](./media/perform-access-review/perform-access-review.png)

Det finns två sätt att du kan godkänna eller neka åtkomst:

- Du kan godkänna eller neka åtkomst för en eller flera användare, eller
- Du kan acceptera system-rekommendationer, vilket är det enklaste och snabbaste sättet.

### <a name="approve-or-deny-access-for-one-or-more-users"></a>Godkänn eller neka åtkomst för en eller flera användare

1. Granska listan över användare att bestämma om du vill godkänna eller neka fortsatt åtkomst.

1. För att godkänna eller neka åtkomst för en enskild användare, klickar du på raden för att öppna ett fönster för att ange åtgärden som ska vidtas. Lägg till markerade användare för att godkänna eller neka åtkomst för flera användare, och klicka sedan på den **granska X användare** knappen för att öppna ett fönster för att ange åtgärden som ska vidtas.

1. Klicka på **godkänna** eller **neka**. Om du är osäker kan du klicka på **vet inte**. Detta leder till användaren upprätthålla åtkomsten, men markeringen visas i granskningsloggarna.

    ![Åtgärd-fönstret som innehåller Godkänn neka, och inte känner till alternativ](./media/perform-access-review/approve-deny.png)

1. Om det behövs kan du ange en orsak i den **orsak** box.

    Administratören för åtkomstgranskningen kan kräva att du anger en orsak för godkännande av fortsatt åtkomst och gruppmedlemskap.

1. När du har angett åtgärden som ska vidtas, klickar du på **spara**.

    Om du vill ändra ditt svar, Välj raden och uppdatera svaret. Du kan till exempel godkänna en tidigare nekade användare eller neka en tidigare godkända användare. Du kan ändra ditt svar när som helst tills åtkomstgranskningen har avslutats.

    Om det finns flera granskare, registreras senaste skickade svaret. Tar ett exempel där en administratör anger två granskare – Alice och Bob. Alice öppnas åtkomstgranskningen först och godkänner åtkomst. Innan granskningen är klar öppnas åtkomstgranskningen Bob och nekar åtkomst. Senaste neka svaret är det registreras.

    > [!NOTE]
    > Om en användare nekas åtkomst, de tas inte bort direkt. De tas bort när granskningen har slutförts eller när en administratör slutar granskningen.

### <a name="approve-or-deny-access-based-on-recommendations"></a>Godkänna eller neka åtkomst baserat på rekommendationer

Om du vill göra åtkomstgranskningar enklare och snabbare för dig, erbjuder vi även rekommendationer som du kan acceptera för med ett enda klick. Rekommendationerna genereras baserat på användarens inloggningsaktivitet.

1. I det blå fältet längst ned på sidan, klickar du på **acceptera rekommendationer**.

    ![Öppna åtkomstgranskning lista som visar knappen Acceptera rekommendationer](./media/perform-access-review/accept-recommendations.png)

    Du ser en sammanfattning av de rekommenderade åtgärderna.

    ![Fönstret som visar en sammanfattning av de rekommenderade åtgärderna](./media/perform-access-review/accept-recommendations-summary.png)

1. Klicka på **Ok** att acceptera rekommendationer.

## <a name="next-steps"></a>Nästa steg

- [Slutför en åtkomstgranskning av grupper eller program](complete-access-review.md)
