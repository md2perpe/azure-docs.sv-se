---
title: 'Självstudier: Azure Active Directory-katalogintegrering med GTNexus SSO System | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och GTNexus SSO System.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: e677a161-1662-4eb3-b48a-b2835470b59e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/08/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4e707351e6eaa25774b43557b2e6a817485ef527
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67101443"
---
# <a name="tutorial-azure-active-directory-integration-with-gtnexus-sso-system"></a>Självstudier: Azure Active Directory-katalogintegrering med GTNexus SSO System

I den här självstudien lär du dig att integrera GTNexus SSO System med Azure Active Directory (AD Azure).
Genom att integrera GTNexus SSO System med Azure AD får du följande fördelar:

* Du kan i Azure AD styra vem som har åtkomst till GTNexus SSO System.
* Du kan låta dina användare automatiskt loggas in på GTNexus SSO System (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure portal.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

För att konfigurera Azure AD-integrering med GTNexus SSO System behöver du följande:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Prenumeration på GTNexus SSO System med enkel inloggning aktiverat

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* GTNexus SSO System stöder **IDP**-initierad enkel inloggning

## <a name="adding-gtnexus-sso-system-from-the-gallery"></a>Lägga till GTNexus SSO System från galleriet

För att konfigurera integreringen av GTNexus SSO System med Azure AD måste du lägga till GTNexus SSO System från galleriet i din lista över hanterade SaaS-appar.

**Lägg till GTNexus SSO System från galleriet genom att utföra följande steg:**

1. I den **[Azure-portalen](https://portal.azure.com)** , klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **GTNexus SSO System**, väljer **GTNexus SSO System** i resultatpanelen och klickar på knappen **Lägg till** för att lägga till programmet.

     ![GTNexus SSO System i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet konfigurerar och testar du enkel inloggning för Azure AD med GTNexus SSO System baserat på en testanvändare som heter **Britta Simon**.
För att enkel inloggning ska fungera så måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i GTNexus SSO System upprättas.

För att konfigurera och testa enkel inloggning för Azure AD med GTNexus SSO System från Microsoft behöver du slutföra följande byggstenar:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Konfigurera enkel inloggning för GTNexus SSO System](#configure-gtnexus-sso-system-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Skapa GTNexus SSO System-testanvändare](#create-gtnexus-sso-system-test-user)** – för att ha en motsvarighet för Britta Simon i JGTNexus SSO System som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning i Azure AD med GTNexus SSO System:

1. I [Azure-portalen](https://portal.azure.com/), på sidan för **GTNexus SSO System**-programintegrering, väljer du **Enkel inloggning**.

    ![Konfigurera enkel inloggning för länken](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg om du har **metadatafilen för tjänstleverantör**:

    a. Klicka på **Ladda upp metadatafil**.

    ![image](common/upload-metadata.png)

    b. Klicka på **mappen logotyp** att välja metadatafilen och klicka på **överför**.

    ![image](common/browse-upload-metadata.png)

    c. När metadatafilen har laddats upp fylls värdena **Identifierare** och **Svars-URL** i automatiskt i textrutan för avsnittet GTNexus SSO System:

    ![image](common/idp-intiated.png)

    > [!Note]
    > Om värdena **Identifierare** och **Svars-URL** inte fylls i automatiskt fyller du i värdena manuellt enligt dina krav.

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar på **Hämta** för att hämta **Metadata-XML för federationen** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för hämtning av certifikat](common/metadataxml.png)

### <a name="configure-gtnexus-sso-system-single-sign-on"></a>Konfigurera enkel inloggning för GTNexus SSO System

För att konfigurera enkel inloggning på **GTNexus SSO System**-sidan behöver du skicka **Federation Metadata-XML** till [supportteamet för GTNexus SSO System](mailto:support@gtnexus.com). De ställer du in SAML SSO ansluta till korrekt inställda på båda sidorna.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I den **användarnamn** fälttyp **brittasimon\@yourcompanydomain.extension**  
    Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet gör du det möjligt för Britta Simon att använda enkel inloggning med Azure genom att ge åtkomst till GTNexus SSO System.

1. I Azure-portalen väljer du **Företagsprogram**, **Alla program** och sedan **GTNexus SSO System**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer du **GTNexus SSO System**.

    ![GTNexus SSO System-länken i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-gtnexus-sso-system-test-user"></a>Skapa GTNexus SSO System-testanvändare

I det här avsnittet ska du skapa en användare med namnet Britta Simon i GTNexus SSO System. Arbeta med [GTNexus SSO System-supportteamet](mailto:support@gtnexus.com) för att lägga till användare i GTNexus SSO System-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på GTNexus SSO System-panelen i åtkomstpanelen bör du automatiskt loggas in på GTNexus SSO System som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
