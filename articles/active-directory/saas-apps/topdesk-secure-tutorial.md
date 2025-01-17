---
title: 'Självstudier: Azure Active Directory-integrering med TOPdesk – Secure | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och TOPdesk – Secure.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 8e06ee33-18f9-4c05-9168-e6b162079d88
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/27/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: eded8eb446d36a321acf46231eee3e764ba41504
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67088453"
---
# <a name="tutorial-azure-active-directory-integration-with-topdesk---secure"></a>Självstudier: Azure Active Directory-integrering med TOPdesk – Secure

I den här självstudien lär du dig att integrera TOPdesk – Secure med Azure Active Directory (AD Azure).
Genom att integrera TOPdesk – Secure med Azure AD får du följande fördelar:

* Du kan i Azure AD styra vem som har åtkomst till TOPdesk – Secure.
* Du kan göra så att dina användare automatiskt loggas in på TOPdesk – Secure (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure portal.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

För att konfigurera Azure AD-integrering med TOPdesk – Secure behöver du följande:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* TOPdesk – Secure-prenumeration med enkel inloggning aktiverat

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* TOPdesk – Secure stöder **IDP**-initierad enkel inloggning

## <a name="adding-topdesk---secure-from-the-gallery"></a>Lägga till TOPdesk – Secure från galleriet

För att konfigurera integreringen av TOPdesk – Secure till Azure AD behöver du lägga till TOPdesk – Secure från galleriet till listan över hanterade SaaS-appar.

**Lägg till TOPdesk – Secure från galleriet genom att utföra följande steg:**

1. I den **[Azure-portalen](https://portal.azure.com)** , klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **TOPdesk - Secure**, väljer **TOPdesk - Secure** i resultatpanelen och klickar på knappen **Lägg till** för att lägga till programmet.

     ![TOPdesk – Secure i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet konfigurerar och testar du enkel inloggning för Azure AD med TOPdesk – Secure baserat på en testanvändare som heter **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i TOPdesk – Secure upprättas.

För att konfigurera och testa enkel inloggning för Azure AD med TOPdesk – Secure behöver du slutföra följande byggstenar:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Konfigurera enkel inloggning för TOPdesk – Secure](#configure-topdesk---secure-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Skapa TOPdesk – Secure-testanvändare](#create-topdesk---secure-test-user)** – för att ha en motsvarighet för Britta Simon i TOPdesk – Secure som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning i Azure AD med TOPdesk – Secure:

1. På [Azure-portalen](https://portal.azure.com/) går du till sidan för **TOPdesk – Secure**-programintegrering och väljer **Enkel inloggning**.

    ![Konfigurera enkel inloggning för länken](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![TOPdesk – Secure-domän och information om URL:er för enkel inloggning](common/sp-identifier-reply.png)

    a. I textrutan **Inloggnings-URL** skriver du en URL med följande mönster: `https://<companyname>.topdesk.net`

    b. I rutan **Identifierare** skriver du en URL med följande mönster: `https://<companyname>.topdesk.net/tas/secure/login/verify`

    c. I textrutan **svars-URL** skriver du en URL med följande mönster: `https://<companyname>.topdesk.net/tas/public/login/saml`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med den faktiska inloggnings-URL:en, identifieraren och svars-URL:en. Hämta dessa värden genom att kontakta [supportteamet för TOPdesk – Secure-klienten](https://www.topdesk.com/us/support/). Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för hämtning av certifikat](common/metadataxml.png)

6. I avsnittet **Konfigurera TOPdesk – Secure** kopierar du lämpliga URL:er efter behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggningswebbadress

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-topdesk---secure-single-sign-on"></a>Configure enkel inloggning för TOPdesk – Secure

1. Logga in på din **TOPdesk – Secure**-företagsplats som administratör.

2. På menyn **TOPdesk** klickar du på **Inställningar**.

    ![Inställningar](./media/topdesk-secure-tutorial/ic790598.png "Inställningar")

3. Klicka på **Inloggningsinställningar**.

    ![Inloggningsinställningar](./media/topdesk-secure-tutorial/ic790599.png "Inloggningsinställningar")

4. Expandera menyn **Inloggningsinställningar** och klicka sedan på **Allmänt**.

    ![Allmänt](./media/topdesk-secure-tutorial/ic790600.png "Allmänt")

5. I avsnittet **Skydda** i avsnittet för konfiguration av **SAML-inloggning** utför du följande steg:

    ![Tekniska inställningar](./media/topdesk-secure-tutorial/ic790855.png "Tekniska inställningar")

    a. Klicka på **Ladda ned** för att ladda ned den offentliga metadatafilen, och spara den lokalt på datorn.

    b. Öppna metadatafilen och leta upp noden **AssertionConsumerService**.

    ![Konsumenttjänst för försäkran](./media/topdesk-secure-tutorial/ic790856.png "Konsumenttjänst för försäkran")

    c. Kopiera värdet **AssertionConsumerService**, klistra in det i textrutan flr svars-URL i avsnittet om **TOPdesk – Secure-domän och URL:er**.

6. Skapa en certifikatfil genom att utföra följande steg:

    ![Certifikat](./media/topdesk-secure-tutorial/ic790606.png "Certifikat")

    a. Öppna den nedladdade metadatafilen från Azure-portalen.

    b. Expandera den **RoleDescriptor**-nod som har en **xsi:type** som är **fed:ApplicationServiceType**.

    c. Kopiera värdet för noden **X509Certificate**.

    d. Spara det kopierade **X509Certificate**-värdet lokalt på datorn i en fil.

7. I avsnittet **Offentligt** klickar du på **Lägg till**.

    ![Lägg till](./media/topdesk-secure-tutorial/ic790607.png "Lägg till")

8. I dialogrutan **SAML-konfigurationsassistent** utför du följande steg:

    ![SAML-konfigurationsassistent](./media/topdesk-secure-tutorial/ic790608.png "SAML-konfigurationsassistent")

    a. Ladda upp den nedladdade metadatafilen från Azure-portalen genom att gå till **Federation Metadata** och klicka på **Bläddra**.

    b. Ladda upp certifikatfilen genom att gå till **Certifikat (RSA)** och klicka på **Bläddra**.

    c. För **Private key(RSA, PKCS8, DER)** kan du ladda upp din egen privata nyckel eller kontakta [supportteamet för TOPdesk – Secure-klienten](https://www.topdesk.com/us/support) för att få den privata nyckeln.

    d. Ladda upp den logotypfil som du fick från TOPdesk-supportteamet genom att gå till **logotypikonen** och klicka på **Bläddra**.

    e. I textrutan **Användarnamnsattribut** skriver du `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.

    f. I textrutan **Visningsnamn** skriver du ett namn för konfigurationen.

    g. Klicka på **Spara**.

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

I det här avsnittet gör du det möjligt för Britta Simon att använda enkel inloggning med Azure genom att ge åtkomst till TOPdesk – Secure.

1. På Azure-portalen väljer du **Företagsprogram**, **Alla program** och sedan **TOPdesk – Secure**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I programlistan skriver du in och väljer **TOPdesk – Secure**.

    ![TOPdesk – Secure-länken i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-topdesk---secure-test-user"></a>Skapa TOPdesk – Secure-testanvändare

För att göra det möjligt för Azure AD-användare att logga in på TOPdesk – Secure måste de etableras i TOPdesk – Secure.  
När det gäller TOPdesk – Secure är etablering en manuell aktivitet.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Konfigurera användaretablering genom att utföra följande steg:

1. Logga in på din **TOPdesk – Secure**-företagsplats som administratör.

2. På menyn längst upp klickar du på **TOPdesk \> New (Ny) \> Support Files (Supportfiler) \> Operator (Operatör)** .

    ![Operatör](./media/topdesk-secure-tutorial/ic790610.png "Operatör")

3. I dialogrutan **New Operator** (Ny operatör) utför du följande steg:

    ![Ny operatör](./media/topdesk-secure-tutorial/ic790611.png "Ny operatör")

    a. Klicka på fliken **Allmänt**.

    b. I textrutan **Efternamn** skriver du efternamnet på användaren: **Simon**.

    c. Välj en **plats** för kontot i avsnittet **Plats**.

    d. I textrutan **Inloggningsnamn** i avsnittet **TOPdesk Login** (TOPdesk-inloggning) skriver du ett inloggningsnamn för användaren.

    e. Klicka på **Spara**.

> [!NOTE]
> Du kan använda andra verktyg eller API:er för TOPdesk – Secure-kontoskapande som tillhandahålls av TOPdesk – Secure för att etablera AAD-användarkonton.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på TOPdesk – Secure-panelen i åtkomstpanelen bör du automatiskt loggas in på TOPdesk – Secure som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

