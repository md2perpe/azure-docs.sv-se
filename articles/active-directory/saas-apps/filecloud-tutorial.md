---
title: 'Självstudier: Azure Active Directory-integrering med FileCloud | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och FileCloud.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 2263e583-3eb2-4a06-982d-33f5f54858f4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/12/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7271a1cf0520eef8aa9b64fab36349caf3c0ffcd
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67102647"
---
# <a name="tutorial-azure-active-directory-integration-with-filecloud"></a>Självstudier: Azure Active Directory-integrering med FileCloud

I den här självstudien lär du dig att integrera FileCloud med Azure Active Directory (AD Azure).
Integreringen av FileCloud med Azure AD medför följande fördelar:

* Du kan i Azure AD styra vem som har åtkomst till FileCloud.
* Du kan göra så att dina användare loggas in automatiskt på FileCloud (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure portal.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

För att konfigurera Azure AD-integrering med FileCloud behöver du följande:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* FileCloud-prenumeration med enkel inloggning aktiverat

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* FileCloud har stöd för **SP**-initierad enkel inloggning

* FileCloud har stöd för **just-in-time**-användaretablering

## <a name="adding-filecloud-from-the-gallery"></a>Lägga till FileCloud från galleriet

För att konfigurera integreringen av FileCloud i Azure AD måste du lägga till FileCloud från galleriet till din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till FileCloud från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)** , klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **FileCloud**, väljer **FileCloud** i resultatpanelen och klickar på knappen **Lägg till** för att lägga till programmet.

     ![FileCloud i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet konfigurerar och testar du enkel inloggning i Azure AD med FileCloud baserat på en testanvändare med namnet **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i FileCloud upprättas.

För att konfigurera och testa enkel inloggning för Azure AD med FileCloud behöver du slutföra följande byggstenar:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Konfigurera enkel inloggning för FileCloud](#configure-filecloud-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Skapa FileCloud-testanvändare](#create-filecloud-test-user)** – för att ha en motsvarighet för Britta Simon i FileCloud som är länkad till Azure AD-representationen av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning med Azure AD för FileCloud:

1. I [Azure-portalen](https://portal.azure.com/) går du till programintegreringssidan för **FileCloud** och väljer **Enkel inloggning**.

    ![Konfigurera enkel inloggning för länken](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![FileCloud-domän och information om URL:er för enkel inloggning](common/sp-identifier.png)

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<subdomain>.filecloudonline.com`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://<subdomain>.filecloudonline.com/simplesaml/module.php/saml/sp/metadata.php/default-sp`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med faktisk inloggnings-URL och identifierare. Kontakta [kundsupporten för FileCloud](mailto:support@codelathe.com) och be om dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för hämtning av certifikat](common/metadataxml.png)

6. I avsnittet **Konfigurera FileCloud** kopierar du lämpliga URL:er efter behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggningswebbadress

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-filecloud-single-sign-on"></a>Konfigurera enkel inloggning för FileCloud

1. Logga in på din FileCloud-klientorganisation som administratör i ett annat webbläsarfönster.

2. I det vänstra navigeringsfönstret klickar du på **Inställningar**. 
   
    ![Konfigurera enkel inloggning på appsidan](./media/filecloud-tutorial/tutorial_filecloud_000.png)

3. Klicka på fliken **Enkel inloggning** i avsnittet Inställningar. 
   
    ![Konfigurera enkel inloggning på appsidan](./media/filecloud-tutorial/tutorial_filecloud_001.png)

4. Välj **SAML** som **Default SSO Type** (Standard-SSO-typ) på panelen **Single Sign On (SSO) Settings** (Inställningar för enkel inloggning (SSO)).
   
    ![Konfigurera enkel inloggning på appsidan](./media/filecloud-tutorial/tutorial_filecloud_002.png)

5. I textrutan **IdP End Point URL** (Slutpunkts-URL för IdP) klistrar du in det värde för **Azure AD-identifierare** som du har kopierat från Azure-portalen.

    ![Konfigurera enkel inloggning på appsidan](./media/filecloud-tutorial/tutorial_filecloud_003.png)

6. Öppna den nedladdade metadatafilen i Anteckningar, kopiera innehållet i den till Urklipp och klistra in det i textrutan **IdP Meta Data** på panelen **SAML-inställningar**.

    ![Konfigurera enkel inloggning på appsidan](./media/filecloud-tutorial/tutorial_filecloud_004.png)

7. Klicka på **spara** knappen.

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

I det här avsnittet gör du det möjligt för Britta Simon att använda enkel inloggning med Azure genom att ge åtkomst till FileCloud.

1. I Azure-portalen väljer du **Företagsprogram**, **Alla program** och sedan **FileCloud**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I programlistan väljer du **FileCloud**.

    ![Länken för FileCloud i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-filecloud-test-user"></a>Skapa FileCloud-testanvändare

I det här avsnittet skapas en användare som heter Britta Simon i FileCloud. FileCloud har stöd för just-in-time-användaretablering, vilket är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om det inte redan finns någon användare i FileCloud skapas en ny efter autentisering.

>[!NOTE]
>Om du behöver skapa en användare manuellt kontaktar du [kundsupporten för FileCloud](mailto:support@codelathe.com).

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på FileCloud-panelen i åtkomstpanelen bör du automatiskt loggas in på FileCloud som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

