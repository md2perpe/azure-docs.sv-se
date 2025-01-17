---
title: 'Självstudier: Azure Active Directory-integrering med Brightspace by Desire2Learn | Microsoft Docs'
description: Lär dig att konfigurera enkel inloggning mellan Azure Active Directory och Brightspace by Desire2Learn.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: e2d3065b-1f6c-4c45-af78-0d5da3266999
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/08/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 69b15cefc3280b4fc6006a2e492cfcbc9eed88e9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67105901"
---
# <a name="tutorial-azure-active-directory-integration-with-brightspace-by-desire2learn"></a>Självstudier: Azure Active Directory-integrering med Brightspace by Desire2Learn

I den här självstudien lär du dig att integrera Brightspace by Desire2Learn med Azure Active Directory (Azure AD).
Genom att integrera Brightspace by Desire2Learn med Azure AD får du följande fördelar:

* Du kan styra vem som har åtkomst till Brightspace by Desire2Learn i Azure AD.
* Du kan låta dina användare loggas in automatiskt på Brightspace by Desire2Learn (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure portal.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Du behöver följande för att kunna konfigurera Azure AD-integrering med Brightspace by Desire2Learn:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* En aktiverad Brightspace av Desire2Learn-prenumeration med enkel inloggning

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Brightspace av Desire2Learn har stöd för **IDP**-initierad enkel inloggning

## <a name="adding-brightspace-by-desire2learn-from-the-gallery"></a>Lägga till Brightspace av Desire2Learn från galleriet

När du konfigurerar integreringen av Brightspace by Desire2Learn med Azure AD, måste du lägga till Brightspace by Desire2Learn från galleriet i din lista med hanterade SaaS-appar.

**Utför följande steg för att lägga till Brightspace av Desire2Learn från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)** , klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **Brightspace by Desire2Learn**. Välj **Brightspace by Desire2Learn** i resultatpanelen och klicka sedan på **Lägg till** för att lägga till programmet.

     ![Brightspace av Desire2Learn i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet konfigurerar och testar du enkel inloggning i Azure AD med Brightspace by Desire2Learn baserat på en testanvändare med namnet **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i Brightspace by Desire2Learn upprättas.

Du behöver slutföra följande byggstenar för att kunna konfigurera och testa enkel inloggning i Azure AD med Brightspace by Desire2Learn:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Konfigurera enkel inloggning för Brightspace by Desire2Learn](#configure-brightspace-by-desire2learn-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Skapa Brightspace by Desire2Learn-testanvändare](#create-brightspace-by-desire2learn-test-user)** – för att ha en motsvarighet till Britta Simon i Brightspace by Desire2Learn som är länkad till Azure AD-representationen av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning i Azure AD med Brightspace by Desire2Learn:

1. I [Azure-portalen](https://portal.azure.com/) går du till programintegreringssidan för **Brightspace by Desire2Learn** och väljer **Enkel inloggning**.

    ![Konfigurera enkel inloggning för länken](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. På sidan **Konfigurera enkel inloggning med SAML** utför du följande steg:

    ![Information om Brightspace by Desire2Learn-domän och URL:er med enkel inloggning](common/idp-intiated.png)

    a. I textrutan **Identifierare** skriver du en URL med följande mönster:
    
    | |
    |--|
    | `https://<companyname>.tenants.brightspace.com/samlLogin`|
    | `https://<companyname>.desire2learn.com/shibboleth-sp`|

    b. I textrutan **Svars-URL** skriver du en URL med följande mönster: `https://<companyname>.desire2learn.com/d2l/lp/auth/login/samlLogin.d2l`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska identifieraren och svars-URL. Kontakta [Brightspace av Desire2Learn-klientens supportteam](https://www.d2l.com/contact/) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för hämtning av certifikat](common/metadataxml.png)

6. I avsnittet **Konfigurera Brightspace by Desire2Learn** kopierar du lämpliga URL:er efter behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggningswebbadress

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-brightspace-by-desire2learn-single-sign-on"></a>Konfigurera Brightspace av Desire2Learn med enkel inloggning

För att konfigurera enkel inloggning på **Brightspace by Desire2Learn**-sidan måste du skicka den nedladdade **XML:en med federationsmetadata** och lämpliga kopierade URL:er från Azure-portalen till [Brightspace by Desire2Learn-supportteamet](https://www.d2l.com/contact/). De ställer du in SAML SSO ansluta till korrekt inställda på båda sidorna.

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

I det här avsnittet låter du Britta Simon använda enkel inloggning i Azure genom att ge henne åtkomst till Brightspace by Desire2Learn.

1. I Azure-portalen väljer du **Företagsprogram**, **Alla program** och sedan **Brightspace by Desire2Learn**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer du **Brightspace av Desire2Learn**.

    ![Brightspace av Desire2Learn-länken i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-brightspace-by-desire2learn-test-user"></a>Skapa Brightspace by Desire2Learn-testanvändare

I det här avsnittet skapar du en användare med namnet Britta Simon i Brightspace by Desire2Learn. Kontakta  [Brightspace av Desire2Learn-supportteamet](https://www.d2l.com/contact/) för att lägga till användarna i Brightspace av Desire2Learn-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

> [!NOTE]
> Du kan använda andra verktyg eller API:er för Brightspace by Desire2Learn-kontoskapande som tillhandahålls av Brightspace by Desire2Learn när du etablerar Azure Active Directory-användarkonton.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på Brightspace by Desire2Learn-ikonen i åtkomstpanelen bör du automatiskt loggas in på den Brightspace by Desire2Learn som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)