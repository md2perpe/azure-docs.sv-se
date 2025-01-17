---
title: 'Självstudier: Azure Active Directory-integrering med Lifesize Cloud | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Lifesize Cloud.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 75fab335-fdcd-4066-b42c-cc738fcb6513
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 1/4/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f751eb9dfb8ef65bea80993ddbd3a682b1d47111
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67098044"
---
# <a name="tutorial-azure-active-directory-integration-with-lifesize-cloud"></a>Självstudier: Azure Active Directory-integrering med Lifesize Cloud

I den här självstudien lär du dig att integrera Lifesize Cloud med Azure Active Directory (AD Azure).
Genom att integrera Lifesize Cloud med Azure AD får du följande fördelar:

* Du kan i Azure AD styra vem som har åtkomst till Lifesize Cloud.
* Du kan göra så att dina användare automatiskt loggas in på Lifesize Cloud (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure portal.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

För att konfigurera Azure AD-integrering med Lifesize Cloud behöver du följande:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Lifesize Cloud-prenumeration med enkel inloggning aktiverat

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Lifesize Cloud stöder **IDP**-initierad enkel inloggning

* Lifesize Cloud stöder **automatisk** användaretablering

## <a name="adding-lifesize-cloud-from-the-gallery"></a>Lägga till Lifesize Cloud från galleriet

För att konfigurera integreringen av Lifesize Cloud till Azure AD behöver du lägga till Lifesize Cloud från galleriet till listan över hanterade SaaS-appar.

**Lägg till Lifesize Cloud från galleriet genom att utföra följande steg:**

1. I den **[Azure-portalen](https://portal.azure.com)** , klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **Lifesize Cloud**, väljer **Lifesize Cloud** i resultatpanelen och klickar på knappen **Lägg till** för att lägga till programmet.

     ![Lifesize Cloud i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet konfigurerar och testar du enkel inloggning för Azure AD med Lifesize Cloud baserat på en testanvändare som heter **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i Lifesize Cloud upprättas.

För att konfigurera och testa enkel inloggning för Azure AD med Lifesize Cloud behöver du slutföra följande byggstenar:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Konfigurera enkel inloggning för Lifesize Cloud](#configure-lifesize-cloud-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Skapa Lifesize Cloud-testanvändare](#create-lifesize-cloud-test-user)** – för att ha en motsvarighet för Britta Simon i Lifesize Cloud som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning i Azure AD med Lifesize Cloud:

1. På [Azure-portalen](https://portal.azure.com/) går du till sidan för **Lifesize Cloud**-programintegrering och väljer **Enkel inloggning**.

    ![Konfigurera enkel inloggning för länken](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Lifesize Cloud-domän och information om URL:er för enkel inloggning](common/sp-identifier-relay.png)

    a. I textrutan **Inloggnings-URL** skriver du en URL med följande mönster: `https://login.lifesizecloud.com/ls/?acs`

    b. I textrutan **Identifierare** skriver du en URL med följande mönster: `https://login.lifesizecloud.com/<companyname>`

    c. Klicka på **Ange ytterligare URL:er**.

    d. Skriv en URL med följande mönster i textrutan **Vidarebefordransstatus**: `https://webapp.lifesizecloud.com/?ent=<identifier>`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med faktisk inloggnings-URL, identifierare och vidarebefordransstatus. Kontakta [supportteamet för Lifesize Cloud-klienten](https://www.lifesize.com/en/support) för att hämta värden för inloggnings-URL och identifierare. Du kan hämta värdet för vidarebefordransstatus från SSO-konfigurationen. Detta förklaras senare i självstudien. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för hämtning av certifikat](common/certificatebase64.png)

6. I avsnittet **Konfigurera Lifesize Cloud** kopierar du lämpliga URL:er efter behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggningswebbadress

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-lifesize-cloud-single-sign-on"></a>Konfigurera enkel inloggning för Lifesize Cloud

1. För att konfigurera SSO för ditt program loggar du in på Lifesize Cloud-programmet med administratörsprivilegier.

2. I det övre högra hörnet klickar du på ditt namn och sedan på **Advanced Settings** (Avancerade inställningar).

    ![Konfigurera enkel inloggning](./media/lifesize-cloud-tutorial/tutorial_lifesizecloud_06.png)

3. I Advanced Settings (Avancerade inställningar) klickar du på länken för **SSO-konfiguration**. Då öppnas sidan för SSO-konfiguration för din instans.

    ![Konfigurera enkel inloggning](./media/lifesize-cloud-tutorial/tutorial_lifesizecloud_07.png)

4. Konfigurera nu följande värden i användargränssnittet för SSO-konfiguration.

    ![Konfigurera enkel inloggning](./media/lifesize-cloud-tutorial/tutorial_lifesizecloud_08.png)

    a. I textrutan **Identity Provider Issuer** (Utfärdare av identitetsprovider) klistrar du in värdet för den **Azure AD-identifierare** som du har kopierat från Azure-portalen.

    b.  I textrutan för **inloggnings-URL** klistrar du in värdet för **inloggnings-URL:en** som du har kopierat från Azure-portalen.

    c. Öppna ditt base-64-kodade certifikat som du har laddat ned från Azure-portalen i Anteckningar, kopiera innehållet till Urklipp och klistra sedan in den i textrutan **X.509 Certificate** (X.509-certifikat).
  
    d. I SAML-attributmappningar för textrutan Förnamn anger du värdet som `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`

    e. I SAML-attributmappningar för textrutan **Efternamn** anger du värdet som `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`

    f. I SAML-attributmappningar för textrutan **E-post** anger du värdet som `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

5. Du kan kontrollera konfigurationen genom att klicka på knappen **Testa**.

    >[!NOTE]
    >För att testningen ska lyckas behöver du slutföra konfigurationsguiden i Azure AD och även ge åtkomst till användare eller grupper som kan utföra testet.

6. Aktivera enkel inloggning genom att aktivera knappen **Aktivera enkel inloggning**.

7. Klicka nu på knappen **Uppdatera** så att alla inställningar sparas. Detta genererar RelayState-värdet. Kopiera RelayState-värdet, som genereras i textrutan, klistra in det i textrutan **Vidarebefordransstatus** i avsnittet om **Lifesize Cloud-domän och URL:er**.

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

I det här avsnittet gör du det möjligt för Britta Simon att använda enkel inloggning med Azure genom att ge åtkomst till Lifesize Cloud.

1. På Azure-portalen väljer du **Företagsprogram**, **Alla program** och sedan **Lifesize Cloud**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I programlistan väljer du **Lifesize Cloud**.

    ![Lifesize Cloud-länken i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-lifesize-cloud-test-user"></a>Skapa Lifesize Cloud-testanvändare

I det här avsnittet skapar du en användare med namnet Britta Simon i Lifesize Cloud. Lifesize Cloud stöder automatisk användaretablering. Efter en lyckad autentisering på Azure AD etableras användaren automatiskt i programmet.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på Lifesize Cloud-panelen i Åtkomstpanelen bör du se inloggningssidan för Lifesize Cloud-programmet. Här behöver du ange ditt användarnamn. Därefter omdirigeras du till programmets startsida.

Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
