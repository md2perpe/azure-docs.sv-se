---
title: Aktivera en pilot för Azure Multi-Factor Authentication
description: I den här självstudien aktiverar du Azure AD Multi-Factor Authentication för en pilotgrupp med användare
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: a229eef3d49964e71becf066d67f60b18da6fc18
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67113245"
---
# <a name="tutorial-complete-an-azure-multi-factor-authentication-pilot-roll-out"></a>Självstudier: Utföra en pilotlansering av Azure Multi-Factor Authentication

I de här självstudierna hur du du konfigurerar en princip för villkorlig åtkomst som att aktivera Azure Multi-Factor Authentication (MFA Azure) när du loggar in på Azure Portal. Principen distribueras till och testats på en specifik grupp med pilotanvändare. Distribution av Azure MFA med villkorlig åtkomst ger stor flexibilitet för organisationer och administratörer jämfört med traditionella tvingande metoden.

> [!div class="checklist"]
> * Aktivera Azure Multi-Factor Authentication
> * Testa Azure Multi-Factor Authentication

## <a name="prerequisites"></a>Nödvändiga komponenter

* En aktiv Azure AD-klientorganisation med minst en aktiverad utvärderingslicens.
* Ett konto med behörigheter som global administratör.
* En testanvändare som inte är administratör med ett lösenord som du känner till för testning. Om du behöver skapa en användare finns information i artikeln [Snabbstart: Lägga till nya användare i Azure Active Directory](../add-users-azure-active-directory.md).
* En pilotgrupp att testa med som icke-administratörsanvändaren är medlem i. Om du behöver skapa en grupp finns information i artikeln [Skapa en grupp och lägga till medlemmar i Azure Active Directory](../active-directory-groups-create-azure-portal.md).

## <a name="enable-azure-multi-factor-authentication"></a>Aktivera Azure Multi-Factor Authentication

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett konto som global administratör.
1. Bläddra till **Azure Active Directory**, **villkorlig åtkomst**
1. Välj **Ny princip**
1. Ge principen namnet **MFA Pilot**
1. Under **användare och grupper** väljer du alternativknappen **Välj användare och grupper**
    * Välj den pilotgrupp som skapades i avsnittet om förutsättningar i den här artikeln
    * Klicka på **Klar**
1. Under **Molnappar** väljer du alternativknappen **Välj appar**
    * Molnappen för Azure-portalen är **Microsoft Azure Management**
    * Klicka på **Välj**
    * Klicka på **Klar**
1. Hoppa över avsnittet **Villkor**
1. Under **Bevilja** ser du till att alternativknappen **Bevilja åtkomst** har markerats
    * Markera rutan för **Kräv multifaktorautentisering**
    * Klicka på **Välj**
1. Hoppa över avsnittet **Session**
1. Ställ in växeln **Aktivera princip** till **På**
1. Klicka på **Skapa**

## <a name="test-azure-multi-factor-authentication"></a>Testa Azure Multi-Factor Authentication

Du testar logga in på en resurs som inte får kräva MFA för att bevisa att din princip för villkorlig åtkomst fungerar, och sedan till Azure-portalen som kräver MFA.

1. Öppna ett nytt webbläsarfönster i InPrivate- eller <inkognitoläge och gå till [https://account.activedirectory.windowsazure.com](https://account.activedirectory.windowsazure.com).
   * Logga in med den testanvändare som skapades i avsnittet om förutsättningar i den här artikeln. Observera att den inte ska uppmana dig att slutföra MFA.
   * Stäng webbläsarfönstret.
2. Öppna ett nytt webbläsarfönster i InPrivate- eller <inkognitoläge och gå till [https://portal.azure.com](https://portal.azure.com).
   * Logga in med den testanvändare som skapades i avsnittet om förutsättningar i den här artikeln. Observera att det nu ska krävas att du registrerar dig för och använder multifaktorautentisering.
   * Stäng webbläsarfönstret.

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte längre vill använda funktioner som du har konfigurerat i den här självstudien kan du göra följande ändring.

1. Logga in på [Azure Portal](https://portal.azure.com).
1. Bläddra till **Azure Active Directory**, **villkorlig åtkomst**.
1. Välj den princip för villkorlig åtkomst som du skapade.
1. Klicka på **Ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här självstudien aktiverade du Azure Multi-Factor Authentication. Fortsätt till nästa självstudie för att se hur Azure Identity Protection kan integreras i funktionerna för självåterställning av lösenord samt multifaktorautentisering.

> [!div class="nextstepaction"]
> [Utvärdera risk vid inloggning](tutorial-risk-based-sspr-mfa.md)
