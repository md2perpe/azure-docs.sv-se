---
title: 'Självstudier: Azure Active Directory-integrering med GoToMeeting | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och GoToMeeting.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: bcaf19f2-5809-4e1c-acbc-21a8d3498ccf
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/02/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d6068a8d2d50521d8513a2ebad5a62649ef61b1e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67105508"
---
# <a name="tutorial-azure-active-directory-integration-with-gotomeeting"></a>Självstudier: Azure Active Directory-integrering med GoToMeeting

I den här självstudien lär du dig att integrera GoToMeeting med Azure Active Directory (AD Azure).
Genom att integrera GoToMeeting med Azure AD får du följande fördelar:

* Du kan i Azure AD styra vem som har åtkomst till GoToMeeting.
* Du kan göra så att dina användare automatiskt loggas in på GoToMeeting (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure portal.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

För att konfigurera Azure AD-integrering med GoToMeeting behöver du följande:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* GoToMeeting-prenumeration med enkel inloggning aktiverat

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* GoToMeeting stöder **IDP**-initierad enkel inloggning

## <a name="adding-gotomeeting-from-the-gallery"></a>Lägga till GoToMeeting från galleriet

För att konfigurera integreringen av GoToMeeting till Azure AD behöver du lägga till GoToMeeting från galleriet till listan över hanterade SaaS-appar.

**Lägg till GoToMeeting från galleriet genom att utföra följande steg:**

1. I den **[Azure-portalen](https://portal.azure.com)** , klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **GoToMeeting**, väljer **GoToMeeting** i resultatpanelen och klickar på knappen **Lägg till** för att lägga till programmet.

     ![GoToMeeting i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet konfigurerar och testar du enkel inloggning för Azure AD med GoToMeeting baserat på en testanvändare som heter **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i GoToMeeting upprättas.

För att konfigurera och testa enkel inloggning för Azure AD med GoToMeeting behöver du slutföra följande byggstenar:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Konfigurera enkel inloggning för GoToMeeting](#configure-gotomeeting-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Skapa GoToMeeting-testanvändare](#create-gotomeeting-test-user)** – för att ha en motsvarighet för Britta Simon i GoToMeeting som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning i Azure AD med GoToMeeting:

1. På [Azure-portalen](https://portal.azure.com/) går du till sidan för **GoToMeeting**-programintegrering och väljer **Enkel inloggning**.

    ![Konfigurera enkel inloggning för länken](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. På sidan **Konfigurera enkel inloggning med SAML** klickar du på knappen **Redigera** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![GoToMeeting-domän och information om URL:er för enkel inloggning](common/both-allurls.png)

    a. I textrutan **Identifierare** skriver du en URL med följande mönster: `https://authentication.logmeininc.com/saml/sp`

    b. Skriv en URL med följande mönster i textrutan **Svars-URL**: `https://authentication.logmeininc.com/saml/acs`

    c. Klicka på **ange ytterligare URL:er** och konfigurera nedanstående URL:er

    d. **Inloggnings-URL** (behåll detta tomt)

    e. I textrutan **RelayState** skriver du en URL med följande mönster:

   - För GoToMeeting-appen använder du `https://global.gotomeeting.com`

   - För GoToTraining använder du `https://global.gototraining.com`

   - För GoToWebinar använder du `https://global.gotowebinar.com` 

   - För GoToAssist använder du `https://app.gotoassist.com`

     > [!NOTE]
     > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska identifieraren och svars-URL. Hämta dessa värden genom att kontakta [supportteamet för GoToMeeting-klienten](https://go.microsoft.com/fwlink/?linkid=845985). Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för hämtning av certifikat](common/certificatebase64.png)

6. I avsnittet **Konfigurera GoToMeeting** kopierar du lämpliga URL:er efter behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggningswebbadress

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-gotomeeting-single-sign-on"></a>Konfigurera enkel inloggning för GoToMeeting

1. I ett annat webbläsarfönster loggar du in på ditt [GoToMeeting-organisationscenter](https://organization.logmeininc.com/). Du uppmanas att bekräfta att IdP har uppdaterats.

2. Markera kryssrutan ”My Identity Provider has been updated with the new domain” (Min identitetsprovider har uppdaterats med den nya domänen). Klicka på **Klar** när du är klar.

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

I det här avsnittet gör du det möjligt för Britta Simon att använda enkel inloggning med Azure genom att ge åtkomst till GoToMeeting.

1. På Azure-portalen väljer du **Företagsprogram**, **Alla program** och sedan **GoToMeeting**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I programlistan väljer du **GoToMeeting**.

    ![GoToMeeting-länken i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-gotomeeting-test-user"></a>Skapa GoToMeeting-testanvändare

I det här avsnittet skapas en användare som heter Britta Simon i GoToMeeting. GoToMeeting stöder just-in-time-etablering, vilket är aktiverat som standard.

Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om det inte redan finns en användare i GoToMeeting skapas en ny när du försöker komma åt GoToMeeting.

> [!NOTE]
> Om du behöver skapa en användare manuellt kontaktar du [supportteamet för GoToMeeting](https://support.logmeininc.com/gotomeeting).

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på GoToMeeting-panelen i åtkomstpanelen bör du automatiskt loggas in på GoToMeeting som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

