---
title: 'Självstudier: Azure Active Directory-integrering med Pavaso Digital Close | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Pavaso Digital Close.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 23d83f82-39c3-4fc2-88c9-f8074cc44d43
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/03/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: cab39e4af44428118966b78eacaec5cc3220daeb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67094789"
---
# <a name="tutorial-azure-active-directory-integration-with-pavaso-digital-close"></a>Självstudier: Azure Active Directory-integrering med Pavaso Digital Close

I den här självstudien lär du dig att integrera Pavaso Digital Close med Azure Active Directory (AD Azure).
Genom att integrera Pavaso Digital Close med Azure AD får du följande fördelar:

* Du kan i Azure AD styra vem som har åtkomst till Pavaso Digital Close.
* Du kan göra så att dina användare automatiskt loggas in på Pavaso Digital Close (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure portal.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

För att konfigurera Azure AD-integrering med Pavaso Digital Close behöver du följande:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Pavaso Digital Close-prenumeration med enkel inloggning aktiverat

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Pavaso Digital Close stöder **IDP**-initierad enkel inloggning

## <a name="adding-pavaso-digital-close-from-the-gallery"></a>Lägga till Pavaso Digital Close från galleriet

För att konfigurera integreringen av Pavaso Digital Close till Azure AD behöver du lägga till Pavaso Digital Close från galleriet till listan över hanterade SaaS-appar.

**Lägg till Pavaso Digital Close från galleriet genom att utföra följande steg:**

1. I den **[Azure-portalen](https://portal.azure.com)** , klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **Pavaso Digital Close**, väljer **Pavaso Digital Close** i resultatpanelen och klickar på knappen **Lägg till** för att lägga till programmet.

     ![Pavaso Digital Close i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet konfigurerar och testar du enkel inloggning Azure AD med [programnamn] baserat på en testanvändare som kallas **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i [Programnamn] upprättas.

För att konfigurera och testa enkel inloggning med Azure AD med [Programnamn] behöver du utföra följande byggstenar:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Konfigurera enkel inloggning för Pavaso Digital Close](#configure-pavaso-digital-close-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Skapa Pavaso Digital Close-testanvändare](#create-pavaso-digital-close-test-user)** – för att ha en motsvarighet för Britta Simon i Pavaso Digital Close som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Konfigurera enkel inloggning med Azure AD med [Programnamn] genom att utföra följande steg:

1. På [Azure-portalen](https://portal.azure.com/) går du till sidan för **Pavaso Digital Close**-programintegrering och väljer **Enkel inloggning**.

    ![Konfigurera enkel inloggning för länken](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration**, om du vill konfigurera appen i **IDP**-initierat läge, gör du följande:

    ![Pavaso Digital Close-domän och information om URL:er för enkel inloggning](common/idp-intiated.png)

    a. I textrutan **Identifierare** skriver du en URL med följande mönster: `https://<SUBDOMAIN>.pavaso.com/AuthServices`

    b. I textrutan **Svars-URL** skriver du en URL med följande mönster: `https://<SUBDOMAIN>.pavaso.com/AuthServices/Acs`

5. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    I textrutan **Inloggnings-URL** skriver du en URL med följande mönster: `https://<SUBDOMAIN>.pavaso.com`.

    ![Pavaso Digital Close-domän och information om URL:er för enkel inloggning](common/metadata-upload-additional-signon.png)

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. Hämta dessa värden genom att kontakta [supportteamet för Pavaso Digital Close-klienten](mailto:support@pavaso.com). Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

6. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för hämtning av certifikat](common/metadataxml.png)

7. I avsnittet **Konfigurera Pavaso Digital Close** kopierar du lämpliga URL:er efter behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggningswebbadress

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-pavaso-digital-close-single-sign-on"></a>Konfigurera enkel inloggning för Pavaso Digital Close

För att konfigurera enkel inloggning på **Pavaso Digital Close**-sidan behöver du skicka det nedladdade **Federation Metadata-XML** och lämpliga kopierade URL:er från Azure-portalen till [supportteamet för Pavaso Digital Close](mailto:support@pavaso.com). De ställer du in SAML SSO ansluta till korrekt inställda på båda sidorna.

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

I det här avsnittet ser du till att Britta Simon ska kunna använda enkel inloggning i Azure genom att bevilja åtkomst till Pavaso Digital Close.

1. På Azure-portalen väljer du **Företagsprogram**, **Alla program** och sedan **Pavaso Digital Close**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I programlistan väljer du **Pavaso Digital Close**.

    ![Pavaso Digital Close-länken i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-pavaso-digital-close-test-user"></a>Skapa Pavaso Digital Close-testanvändare

I det här avsnittet skapar du en användare med namnet Britta Simon i Pavaso Digital Close. Kontakta [supportteamet för Pavaso Digital Close](mailto:support@pavaso.com) för att lägga till användare i Pavaso Digital Close-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på Pavaso Digital Close-panelen i åtkomstpanelen bör du automatiskt loggas in på Pavaso Digital Close som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
