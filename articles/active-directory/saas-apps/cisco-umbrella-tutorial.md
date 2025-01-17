---
title: 'Självstudie: Azure Active Directory-katalogintegrering med Cisco Umbrella | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Cisco Umbrella.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 675dca98-f119-4463-8350-d6a45d5601e3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 255af92dfecdf891568d7d57c17512bb178a51e5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67105569"
---
# <a name="tutorial-azure-active-directory-integration-with-cisco-umbrella"></a>Självstudie: Azure Active Directory-katalogintegrering med Cisco Umbrella

Lär dig hur du integrerar Cisco Umbrella med Azure Active Directory (AD Azure) i den här självstudien.
När du integrerar Cisco Umbrella med Azure AD får du följande fördelar:

* I Azure AD kan du styra vem som har åtkomst till Cisco Umbrella.
* Du kan göra så att dina användare automatiskt loggas in på Cisco Umbrella (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure portal.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill konfigurera Azure AD-integrering med Cisco Umbrella behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Cisco Umbrella-prenumeration med enkel inloggning aktiverat

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Cisco Umbrella har stöd för **SP och IDP**-initierad enkel inloggning

## <a name="adding-cisco-umbrella-from-the-gallery"></a>Lägga till Cisco Umbrella från galleriet

För att konfigurera integrering av Cisco Umbrella i Azure AD måste du lägga till Cisco Umbrella från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Cisco Umbrella från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)** , klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **Cisco Umbrella**, väljer **Cisco Umbrella** på resultatpanelen och klickar på **Lägg till** för att lägga till programmet.

     ![Cisco Umbrella i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet konfigurerar och testar du enkel inloggning Azure AD med [programnamn] baserat på en testanvändare som kallas **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i [Programnamn] upprättas.

För att konfigurera och testa enkel inloggning med Azure AD med [Programnamn] behöver du utföra följande byggstenar:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Konfigurera enkel inloggning för Cisco Umbrella](#configure-cisco-umbrella-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Skapa Cisco Umbrella-testanvändare](#create-cisco-umbrella-test-user)** – för att ha en motsvarighet till Britta Simon i Cisco Umbrella som är länkad till Azure AD-representationen för användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Konfigurera enkel inloggning med Azure AD med [Programnamn] genom att utföra följande steg:

1. På [Azure-portalen](https://portal.azure.com/)på sidan **Cisco Umbrella** markerar du **Enkel inloggning**.

    ![Konfigurera enkel inloggning för länken](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** behöver användaren inte utföra några steg eftersom appen redan är förintegrerad med Azure.

    ![Information om Cisco Umbrella-domänen och URL:er med enkel inloggning](common/both-preintegrated-signon.png)

    a. Om du vill konfigurera appen i **SP**-initierat läge utför du följande steg:

    b. Klicka på **Ange ytterligare URL:er**.

    c. I rutan **Inloggnings-URL** anger du en URL: `https://login.umbrella.com/sso`

5. På sidan **Konfigurera enkel inloggning med SAML**, i avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Metadata XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för hämtning av certifikat](common/metadataxml.png)

6. I avsnittet **om att konfigurera Cisco Umbrella** kopierar du lämpliga URL:er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggningswebbadress

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-cisco-umbrella-single-sign-on"></a>Konfigurera enkel inloggning för Cisco Umbrella

1. I ett annat webbläsarfönster loggar du in på företagswebbplatsen Cisco Umbrella som administratör.

2. Till vänster i menyn klickar du på **Administratör** och går till **Autentisering** och klickar sedan på **SAML**.

    ![Administratören](./media/cisco-umbrella-tutorial/tutorial_cisco-umbrella_admin.png)

3. Välj **Other** (Övrigt) och klicka på **NÄSTA**.

    ![Övrigt](./media/cisco-umbrella-tutorial/tutorial_cisco-umbrella_other.png)

4. På sidan med **Cisco Umbrella-metadata** klickar du på **NÄSTA**.

    ![Metadata](./media/cisco-umbrella-tutorial/tutorial_cisco-umbrella_metadata.png)

5. Om du redan har konfigurerat SAML på fliken **Upload Metadata** (Ladda upp metadata) väljer du alternativet **Click here to change them** (Klicka här om du vill ändra dem) och följ stegen nedan.

    ![Nästa](./media/cisco-umbrella-tutorial/tutorial_cisco-umbrella_next.png)

6. I **Alternativ A: Ladda upp XML-filen**, ladda upp filen **Federation Metadata XML** som du laddade ned från Azure-portalen. När du har laddat upp metadata fylls värdena nedan i automatiskt. Klicka sedan på **NÄSTA**.

    ![Choosefile](./media/cisco-umbrella-tutorial/tutorial_cisco-umbrella_choosefile.png)

7. Under **Validate SAML Configuration** (Validera SAML-konfiguration) klickar du på **TEST YOUR SAML CONFIGURATION** (Testa din SAML-konfiguration).

    ![Testet](./media/cisco-umbrella-tutorial/tutorial_cisco-umbrella_test.png)

8. Klicka på **SPARA**.

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

I det här avsnittet ska du göra det möjligt för Britta Simon att använda enkel inloggning i Azure genom att bevilja åtkomst till Cisco Umbrella.

1. På Azure-portalen väljer du **Företagsprogram**, **Alla program** och sedan **Cisco Umbrella**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan över program skriver och väljer du **Cisco Umbrella**.

    ![I listan över program väljer du Cisco Umbrella-länken](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-cisco-umbrella-test-user"></a>Skapa testanvändare för Cisco Umbrella

Om du vill göra det möjligt för Azure AD-användare att logga in på Cisco Umbrella måste de etableras i Cisco Umbrella.  
När det gäller Cisco Umbrella sker etablering manuellt.

**Utför följande steg för att etablera ett användarkonto:**

1. I ett annat webbläsarfönster loggar du in på företagswebbplatsen Cisco Umbrella som administratör.

2. Till vänster i menyn klickar du på **Administratör** och går till **Konton**.

    ![Kontot](./media/cisco-umbrella-tutorial/tutorial_cisco-umbrella_account.png)

3. På sidan **Konton** klickar du på **Lägg till** överst till höger på sidan och utför följande steg.

    ![Användaren](./media/cisco-umbrella-tutorial/tutorial_cisco-umbrella_createuser.png)

    a. I fältet **Förnamn** anger förnamnet, som **Britta**.

    b. I fältet **Efternamn** anger du efternamnet, som **simon**.

    c. Välj din roll från **Välj delegerad administratörsroll**.
  
    d. I den **e-postadress** fältet, anger du e-postadress för användaren som **brittasimon\@contoso.com**.

    e. I fältet **Lösenord** anger du ditt lösenord.

    f. I fältet **Bekräfta lösenord** anger du ditt lösenord igen.

    g. Klicka på **SKAPA**.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på Cisco Umbrella-panelen i åtkomstpanelen bör du automatiskt loggas in på Cisco Umbrella som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
