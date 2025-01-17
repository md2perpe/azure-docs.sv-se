---
title: 'Självstudier: Azure Active Directory-integrering med 8x8 Virtual Office | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och 8x8 Virtual Office.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: b34a6edf-e745-4aec-b0b2-7337473d64c5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/27/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1fb9ee12c0373f2d7038417935709caae4a6eb43
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67107491"
---
# <a name="tutorial-azure-active-directory-integration-with-8x8-virtual-office"></a>Självstudier: Azure Active Directory-integrering med 8x8 Virtual Office

Lär dig hur du integrerar 8x8 Virtual Office med Azure Active Directory (Azure AD) i den här självstudien.
När du integrerar 8x8 Virtual Office med Azure AD får du följande fördelar:

* Du kan styra vem som har åtkomst till 8x8 Virtual Office i Azure AD.
* Du kan göra så att dina användare automatiskt loggas in på 8x8 Virtual Office (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure portal.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill konfigurera Azure AD-integrering med 8x8 Virtual Office behöver du följande:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* 8x8 Virtual Office-prenumeration med enkel inloggning aktiverat

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.


* 8x8 Virtual Office stöder **IDP**-initierad enkel inloggning

* 8x8 Virtual Office stöder **just-in-time**-användaretablering

## <a name="adding-8x8-virtual-office-from-the-gallery"></a>Lägga till 8x8 Virtual Office från galleriet

För att konfigurera integreringen av 8x8 Virtual Office i Azure AD måste du lägga till 8x8 Virtual Office från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till 8x8 Virtual Office från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)** , klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **8x8 Virtual Office**, väljer **8x8 Virtual Office** i resultatpanelen och klickar på **Lägg till** för att lägga till programmet.

     ![8x8 Virtual Office i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD med 8x8 Virtual Office baserat på en testanvändare med namnet **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i 8x8 Virtual Office upprättas.

Konfigurera och testa enkel inloggning med Azure AD med 8x8 Virtual Office genom att slutföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Konfigurera enkel inloggning för 8x8 Virtual Office](#configure-8x8-virtual-office-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Skapa en 8x8 Virtual Office-testanvändare](#create-8x8-virtual-office-test-user)** – för att ha en motsvarighet till Britta Simon i 8x8 Virtual Office som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning med Azure AD med 8x8 Virtual Office:

1. På [Azure-portalen](https://portal.azure.com/) går du till sidan för **8x8 Virtual Office**-programintegrering och väljer **Enkel inloggning**.

    ![Konfigurera enkel inloggning för länken](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I dialogrutan **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Information om enkel inloggning med 8x8 Virtual Office-domänen och URL:er](common/idp-intiated.png)

    a. I textrutan **Identifierare** skriver du en URL med följande mönster: `https://sso.8x8.com/saml2`

    b. I textrutan **Svars-URL** skriver du in en URL med följande mönster: `https://sso.8x8.com/saml2`

4. På sidan **Konfigurera enkel inloggning med SAML**, i avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (RAW)** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för hämtning av certifikat](common/certificateraw.png)

6. I avsnittet **om att konfigurera 8x8 Virtual Office** kopierar du lämpliga URL:er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggningswebbadress

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-8x8-virtual-office-single-sign-on"></a>Konfigurera enkel inloggning för 8x8 Virtual Office

1. Logga in på 8x8 Virtual Office-klienten som administratör.

1. Välj **Virtual Office Account Mgr** (Kontohanteraren i Virtual Office) i programfönstret.

    ![Konfigurera på programsidan](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_001.png)

1. Välj **Business** (Företag) som kontotyp för hantering och klicka på **Sign In** (Logga in).

    ![Konfigurera på programsidan](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_002.png)

1. Klicka på fliken **ACCOUNTS** (KONTON) i menylistan.

   ![Konfigurera på programsidan](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_003.png)

1. Klicka på **Single Sign On** (Enkel inloggning) i listan över konton.
  
   ![Konfigurera på programsidan](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_004.png)

1. Välj autentiseringsmetoden **Single Sign On** (Enkel inloggning) och klicka på **SAML**.

   ![Konfigurera på programsidan](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_005.png)

1. Utför följande steg i avsnittet **SAML Single Sign on** (Enkel inloggning med SAML):

   ![Konfigurera på programsidan](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_006.png)

   a. I textrutan **Sign In URL** (Inloggnings-URL) klistrar du in **inloggnings-URL-värdet** som du har kopierat från Azure-portalen.

   b. I textrutan **Sign Out URL** (Utloggnings-URL) klistrar du in **utloggnings-URL-värdet** som du har kopierat från Azure-portalen.

   c. I textrutan **Issuer URL** (Utfärdar-URL) klistrar du in det värde för **Azure AD-identifierare** som du har kopierat från Azure-portalen.

   d. Klicka på **Browse** (Bläddra) för att ladda upp det certifikat som du har laddat ned från Azure-portalen.

   e. Klicka på knappen **Spara**.

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

I det här avsnittet gör du det möjligt för Britta Simon att använda enkel inloggning med Azure genom att ge åtkomst till 8x8 Virtual Office.

1. I Azure-portalen väljer du **Företagsprogram**, **Alla program** och sedan **8x8 Virtual Office**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan över program skriver och väljer du **8x8 Virtual Office**.

    ![8x8 Virtual Office-länken i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-8x8-virtual-office-test-user"></a>Skapa en testanvändare i 8x8 Virtual Office

I det här avsnittet skapas en användare som heter Britta Simon i 8x8 Virtual Office. 8x8 Virtual Office stöder **just-in-time-etablering av användare**, vilket är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om det inte redan finns någon användare i 8x8 Virtual Office skapas en ny efter autentisering.

> [!NOTE]
> Om du behöver skapa en användare manuellt kontaktar du [8x8 Virtual Office-supporten](https://www.8x8.com/about-us/contact-us).

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på 8x8 Virtual Office-panelen i åtkomstpanelen bör du automatiskt loggas in på 8x8 Virtual Office som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

