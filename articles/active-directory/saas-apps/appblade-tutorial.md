---
title: 'Självstudier: Azure Active Directory-integrering med AppBlade | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och AppBlade.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3360d7aa-6518-4f99-88bd-b7f7258183e8
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7c1a0f15d67d76ca0e1b2e9b348b36be7dee3dd6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67106900"
---
# <a name="tutorial-azure-active-directory-integration-with-appblade"></a>Självstudier: Azure Active Directory-katalogintegrering med AppBlade

I den här självstudien lär du dig att integrera AppBlade med Azure Active Directory (Azure AD).
Genom att integrera AppBlade med Azure AD får du följande fördelar:

* Du kan styra vem som har åtkomst till AppBlade från Azure AD.
* Du kan låta dina användare automatiskt loggas in på AppBlade (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure portal.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill konfigurera Azure AD-integrering med AppBlade behöver du följande:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* AppBlade-prenumeration med enkel inloggning aktiverat

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* AppBlade stöder **SP**-initierad enkel inloggning
* AppBlade stöder **Just-in-time**-användaretablering

## <a name="adding-appblade-from-the-gallery"></a>Lägga till AppBlade från galleriet

För att konfigurera integrering av AppBlade i Azure AD behöver du lägga till AppBlade från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till AppBlade från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)** , klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **AppBlade**, väljer **AppBlade** i resultatpanelen och klickar på knappen **Lägg till** för att lägga till programmet.

     ![AppBlade i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD med AppBlade baserat på en testanvändare med namnet **Britta Simon**.
För att enkel inloggning ska fungera så måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i AppBlade upprättas.

Om du vill konfigurera och testa enkel inloggning med Azure AD för AppBlade, måste du utföra följande uppgifter:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Konfigurera enkel inloggning för AppBlade](#configure-appblade-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Skapa AppBlade-testanvändare](#create-appblade-test-user)** – för att ha en motsvarighet för Britta Simon i AppBlade som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning med Azure AD för AppBlade:

1. Välj **Enkel inloggning** på sidan för programintegrering av **AppBlade** på [Azure-portalen](https://portal.azure.com/).

    ![Konfigurera enkel inloggning för länken](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Information om enkel inloggning med AppBlade-domäner och -URL:er](common/sp-signonurl.png)

    I textrutan **Inloggnings-URL** skriver du in en URL med följande mönster: `https://<companyname>.appblade.com/saml/<tenantid>`

    > [!NOTE]
    > Värdet är inte verkligt. Uppdatera värdet med den faktiska inloggnings-URL:en. Kontakta [supportteamet för AppBlade-klienten](mailto:support@appblade.com) för att hämta värdet. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för hämtning av certifikat](common/metadataxml.png)

6. I avsnittet **Konfigurera AppBlade** kopierar du lämpliga URL:er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggningswebbadress

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-appblade-single-sign-on"></a>Konfigurera enkel inloggning med AppBlade

För att konfigurera enkel inloggning på **AppBlade**-sidan behöver du skicka nedladdade **XML-federationsmetadata** och lämpliga kopierade URL:er från Azure-portalen till [supportteamet för AppBlade](mailto:support@appblade.com). Be dem också att konfigurera **utfärdar-URL för enkel inloggning** som `https://appblade.com/saml`. Inställningen är nödvändig för att enkel inloggning ska fungera.

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

I det här avsnittet gör du det möjligt för Britta Simon att använda enkel inloggning med Azure genom att ge åtkomst till AppBlade.

1. I Azure-portalen väljer du **Företagsprogram**, **Alla program** och sedan **AppBlade**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer du **AppBlade**.

    ![AppBlade-länken i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-appblade-test-user"></a>Skapa AppBlade-testanvändare

Målet med det här avsnittet är att skapa en användare som kallas Britta Simon i AppBlade. AppBlade stöder just-in-time-etablering, vilket är aktiverat som standard. **Kontrollera att domännamnet är konfigurerat med AppBlade för användaretablering. Därefter fungerar endast just-in-time-användaretableringen.**

Om användaren har en e-postadress som slutar med den domän som konfigurerats av AppBlade för ditt konto, ansluts användaren automatiskt till kontot som en medlem med den behörighetsnivå som du anger. Alternativen är ”Basic” (en grundläggande användare som bara kan installera program) , ”Gruppmedlem” (en användare som kan ladda upp nya programversioner och hantera projekt), eller ”Administratör” (fullständiga administratörsrättigheter till kontot). Det normala vore att välja Basic och sedan uppgradera användare manuellt via en administratörsinloggning (AppBlade måste konfigurera antingen en e-postbaserad administratörsinloggning i förväg eller uppgradera en användare för kundens räkning efter inloggning).

Det finns inget åtgärdsobjekt för dig i det här avsnittet. En ny användare har skapats under ett försök att komma åt AppBlade om det inte finns ännu.

> [!NOTE]
> Om du vill skapa en användare manuellt behöver du kontakta [supportteamet för AppBlade](mailto:support@appblade.com).

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på AppBlade-panelen i åtkomstpanelen så borde du automatiskt loggas in på den AppBlade som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
