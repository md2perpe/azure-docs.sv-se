---
title: Konfigurera användarens medgivande till ett program – Azure Active Directory | Microsoft Docs
description: Lär dig mer om att hantera hur användare godkänna att behörigheter för programmet. Du kan förenkla användarupplevelsen genom att tilldela administratörens godkännande. Dessa metoder gäller alla användare i din Azure Active Directory (Azure AD)-klient.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 10/22/2018
ms.author: mimart
ms.reviewer: arvindh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4951984d05e75b0271cf6592c77c54ad13678994
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2019
ms.locfileid: "67476557"
---
# <a name="configure-the-way-end-users-consent-to-an-application-in-azure-active-directory"></a>Konfigurera hur slutanvändare samtycka till ett program i Azure Active Directory
Lär dig mer om att konfigurera hur användare godkänna att behörigheter för programmet. Du kan förenkla användarupplevelsen genom att tilldela administratörens godkännande. Den här artikeln innehåller olika sätt du kan konfigurera användarens medgivande. Metoderna som gäller för alla användare i din Azure Active Directory (Azure AD)-klient. 

Mer information om principer för program finns i [Azure Active Directory-ramverket för medgivande](../develop/consent-framework.md).

## <a name="prerequisites"></a>Förutsättningar

Bevilja administratörens godkännande måste du logga in som global administratör, programadministratör eller en molnprogramadministratör.

Om du vill begränsa åtkomsten till program, måste du Kräv Användartilldelning och därefter tilldela användare eller grupper till programmet.  Mer information finns i [metoder för att tilldela användare och grupper](methods-for-assigning-users-and-groups.md).

## <a name="grant-admin-consent-to-enterprise-apps-in-the-azure-portal"></a>Bevilja administratörens godkännande till enterprise-appar i Azure portal

Så här ger administratörens godkännande till en företagsapp:

1. Logga in på den [Azure-portalen](https://portal.azure.com) som global administratör, programadministratör eller en molnprogramadministratör.
2. Klicka på **alla tjänster** överst i den vänstra navigeringsmenyn. Den **Azure Active Directory-tillägget** öppnas.
3. Skriv i sökrutan filtrera **”Azure Active Directory”** och välj den **Azure Active Directory** objekt.
4. Navigeringsmenyn klickar du på **företagsprogram**.
5. Välj appen om samtycke.
6. Välj **behörigheter** och klicka sedan på **bevilja administratörens godkännande**. Du uppmanas att logga in på Administrera programmet.
7. Logga in med ett konto som har behörighet att bevilja administratörens godkännande för programmet. 
8. Accepterar du behörigheter för programmet.

Det här alternativet fungerar bara om programmet är: 

- Registrerade i klientorganisationen, eller
- Registrerade i en annan Azure AD-klient och samtyckt av minst en slutanvändare. När en användare har samtyckt till ett program, Azure AD innehåller programmet under **företagsappar** i Azure-portalen.

## <a name="grant-admin-consent-when-registering-an-app-in-the-azure-portal"></a>Bevilja administratörens godkännande när du registrerar en app i Azure portal

Så här ger administratörens godkännande när du registrerar en app: 

1. Logga in på [Azure Portal](https://portal.azure.com) som global administratör.
2. Navigera till den **Appregistreringar** bladet.
3. Välj ett program för medgivande.
4. Välj **API-behörigheter**.
5. Klicka på **bevilja administratörens godkännande**.


## <a name="grant-admin-consent-through-a-url-request"></a>Bevilja administratörens godkännande via en URL-begäran

Så här ger administratörens godkännande via en URL-begäran:

1. Skapa en begäran att *login.microsoftonline.com* med dina konfigurationer och lägger till på `&prompt=admin_consent`. 
2. Appen har beviljats medgivande för alla användare för när du har loggat in med autentiseringsuppgifter som administratör.


## <a name="force-user-consent-through-a-url-request"></a>Tvinga användargodkännande via en URL-begäran

Om du vill kräva av slutanvändarna samtycker till att ett program varje gång de autentiserar, lägger du till `&prompt=consent` med autentiseringen URL för begäran.

## <a name="next-steps"></a>Nästa steg

[Medgivande och integrera appar till AzureAD](../develop/quickstart-v1-integrate-apps-with-azure-ad.md)

[Medgivande och ge behörighet till för AzureAD v2.0 konvergerat appar](../develop/active-directory-v2-scopes.md)

[AzureAD StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory)
