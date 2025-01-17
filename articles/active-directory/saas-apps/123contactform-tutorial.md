---
title: 'Självstudier: Azure Active Directory-integrering med 123ContactForm | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och 123ContactForm.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 5211910a-ab96-4709-959a-524c4d57c43e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 66e15d58f0c9d82d9ac73747cbd995d8de5cdf97
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67107582"
---
# <a name="tutorial-azure-active-directory-integration-with-123contactform"></a>Självstudier: Azure Active Directory-integrering med 123ContactForm

I den här självstudien lär du dig att integrera 123ContactForm med Azure Active Directory (AD Azure).
Integreringen av 123ContactForm med Azure AD medför följande fördelar:

* Du kan i Azure AD styra vem som har åtkomst till 123ContactForm.
* Du kan göra så att dina användare automatiskt loggas in på 123ContactForm (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure portal.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

För att konfigurera Azure AD-integrering med 123ContactForm behöver du följande:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* 123ContactForm-prenumeration med enkel inloggning aktiverat

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* 123ContactForm har stöd för **SP- och IDP**-initierad enkel inloggning
* 123ContactForm har stöd för **just-in-time**-användaretablering

## <a name="adding-123contactform-from-the-gallery"></a>Lägga till 123ContactForm från galleriet

För att konfigurera integrering av 123ContactForm i Azure AD behöver du lägga till 123ContactForm från galleriet till din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till 123ContactForm från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)** , klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **123ContactForm**, väljer **123ContactForm** i resultatpanelen och klickar på knappen **Lägg till** för att lägga till programmet.

     ![123ContactForm i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD till 123ContactForm baserat på en testanvändare med namnet **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i 123ContactForm upprättas.

För att konfigurera och testa enkel inloggning för Azure AD med 123ContactForm behöver du slutföra följande byggstenar:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Konfigurera enkel inloggning för 123ContactForm](#configure-123contactform-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Skapa 123ContactForm-testanvändare](#create-123contactform-test-user)** – för att ha en motsvarighet för Britta Simon i 123ContactForm som är länkad till Azure AD-representationen av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning med Azure AD för 123ContactForm:

1. I [Azure-portalen](https://portal.azure.com/) går du till programintegreringssidan för **123ContactForm** och väljer **Enkel inloggning**.

    ![Konfigurera enkel inloggning för länken](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration**, om du vill konfigurera appen i **IDP**-initierat läge, gör du följande:

    ![123ContactForm-domän och information om URL:er för enkel inloggning](common/idp-intiated.png)

    a. I textrutan **Identifierare** skriver du en URL med följande mönster: `https://www.123contactform.com/saml/azure_ad/<tenant_id>/metadata`

    b. I textrutan **Svars-URL** skriver du en URL med följande mönster: `https://www.123contactform.com/saml/azure_ad/<tenant_id>/acs`

5. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    ![123ContactForm-domän och information om URL:er för enkel inloggning](common/metadata-upload-additional-signon.png)

    I textrutan **Inloggnings-URL** skriver du in en URL med följande mönster: `https://www.123contactform.com/saml/azure_ad/<tenant_id>/sso`

    > [!NOTE]
    > Dessa värden är inte verkliga. Du behöver uppdatera de här värdena från faktiska URL:er och identifierare. Detta förklaras senare i självstudien.

6. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar på **Hämta** för att hämta **Metadata-XML för federationen** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för hämtning av certifikat](common/metadataxml.png)

7. I avsnittet **Konfigurera 123ContactForm** kopierar du lämpliga URL:er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggningswebbadress

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-123contactform-single-sign-on"></a>Konfigurera enkel inloggning för 123ContactForm

1. För att konfigurera enkel inloggning på **123ContactForm**-sidan går du till [https://www.123contactform.com/form-2709121/](https://www.123contactform.com/form-2709121/) och utför följande steg:

    ![Konfigurera enkel inloggning](./media/123contactform-tutorial/submit.png) 

    a. I den **e-post** textrutan typ e-postmeddelandet för användaren som **BrittaSimon\@Contoso.com**.

    b. Klicka på **Ladda upp** och bläddra till den nedladdade metadata-XML-filen som du har laddat ned från Azure-portalen.

    c. Klicka på **SUBMIT FORM** (Skicka formulär).

2. I **Microsoft Azure AD - Single sign-on - Configure App Settings** (Microsoft Azure AD – Enkel inloggning – Konfigurera appinställningar) utför du följande steg:

    ![Konfigurera enkel inloggning](./media/123contactform-tutorial/url3.png)

    a. Om du vill konfigurera programmet i **IDP-initierat läge** kopierar du värdet för **IDENTIFIER** (Identifierare) för din instans och klistrar in det i textrutan **Identifierare** i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

    b. Om du vill konfigurera programmet i **IDP-initierat läge** kopierar du värdet för **REPLY URL** (Svars-URL) för din instans och klistrar in det i textrutan **Svars-URL** i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

    c. Om du vill konfigurera programmet i **SP-initierat läge** kopierar du värdet för **SIGN ON URL** (Inloggnings-URL) för din instans och klistrar in det i textrutan **Inloggnings-URL** i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

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

I det här avsnittet gör du det möjligt för Britta Simon att använda enkel inloggning med Azure genom att ge åtkomst till 123ContactForm.

1. I Azure-portalen väljer du **Företagsprogram**, **Alla program** och sedan **123ContactForm**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I programlistan väljer du **123ContactForm**.

    ![Länken för 123ContactForm i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-123contactform-test-user"></a>Skapa 123ContactForm-testanvändare

I det här avsnittet skapas en användare som heter Britta Simon i 123ContactForm. 123ContactForm har stöd för just-in-time-användaretablering, vilket är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om det inte redan finns någon användare i 123ContactForm skapas en ny efter autentisering.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på 123ContactForm-panelen i åtkomstpanelen bör du automatiskt loggas in på 123ContactForm som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)