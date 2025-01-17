---
title: 'Självstudier: Azure Active Directory-integrering med Front | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Front.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 88270b6d-2571-434a-b139-b6ccc3a2b19f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/15/2019
ms.author: jeedes
ms.openlocfilehash: 8fb5685338ee3d8888f99cc36f4868ef897de5a8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67101922"
---
# <a name="tutorial-azure-active-directory-integration-with-front"></a>Självstudier: Azure Active Directory-integrering med Front

I den här självstudien lär du dig att integrera Front med Azure Active Directory (AD Azure).
Integreringen av Front med Azure AD medför följande fördelar:

* Du kan i Azure AD styra vem som har åtkomst till Front.
* Du kan göra så att dina användare loggas in automatiskt på Front (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure portal.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

För att konfigurera Azure AD-integrering med Front behöver du följande:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Front-prenumeration med enkel inloggning aktiverat

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Front stöder **IDP**-initierad enkel inloggning

## <a name="adding-front-from-the-gallery"></a>Lägga till Front från galleriet

För att konfigurera integreringen av Front i Azure AD måste du lägga till Front från galleriet till din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Front från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)** , klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **Front**, väljer **Front** i resultatpanelen och klickar på knappen **Lägg till** för att lägga till programmet.

     ![Front i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet konfigurerar och testar du enkel inloggning i Azure AD med Front baserat på en testanvändare med namnet **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i Front upprättas.

För att konfigurera och testa enkel inloggning för Azure AD med Front behöver du slutföra följande byggstenar:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Konfigurera enkel inloggning för Front](#configure-front-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Skapa Front-testanvändare](#create-front-test-user)** – för att ha en motsvarighet för Britta Simon i Front som är länkad till Azure AD-representationen av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning med Azure AD för Front:

1. I [Azure-portalen](https://portal.azure.com/) går du till programintegreringssidan för **Front** och väljer **Enkel inloggning**.

    ![Konfigurera enkel inloggning för länken](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. På sidan **Konfigurera enkel inloggning med SAML** utför du följande steg:

    ![Front-domän och information om URL:er för enkel inloggning](common/idp-intiated.png)

    a. I textrutan **Identifierare** skriver du en URL med följande mönster: `https://<companyname>.frontapp.com`

    b. I textrutan **Svars-URL** skriver du en URL med följande mönster: `https://<companyname>.frontapp.com/sso/saml/callback`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska identifieraren och svars-URL. Kontakta [kundsupporten för Front](mailto:support@frontapp.com) och be om dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för hämtning av certifikat](common/certificatebase64.png)

6. I avsnittet **Konfigurera Front** kopierar du lämpliga URL:er efter behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggningswebbadress

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-front-single-sign-on"></a>Konfigurera enkel inloggning för Front

1. Logga in på din Front-klientorganisation som administratör.

2. Gå till **Inställningar (kugghjulsikonen längst ned i det vänstra sidofältet) > Inställningar**.
   
    ![Konfigurera enkel inloggning på appsidan](./media/front-tutorial/tutorial_front_000.png)

3. Klicka på länken **Enkel inloggning**.
   
    ![Konfigurera enkel inloggning på appsidan](./media/front-tutorial/tutorial_front_001.png)

4. Välj **SAML** i listrutan för **Enkel inloggning**.
   
    ![Konfigurera enkel inloggning på appsidan](./media/front-tutorial/tutorial_front_002.png)

5. I textrutan **Entry Point** (Startpunkt) anger du värdet för **inloggnings-URL** från konfigurationsguiden för Azure AD-programmet.
    
    ![Konfigurera enkel inloggning på appsidan](./media/front-tutorial/tutorial_front_003.png)

6. Öppna det nedladdade **certifikatet (Base64)** i Anteckningar, kopiera dess innehåll till Urklipp och klistra in det i textrutan **Signing certificate** (Signeringscertifikat).
    
    ![Konfigurera enkel inloggning på appsidan](./media/front-tutorial/tutorial_front_004.png)

7. I avsnittet **Service provider settings** (Inställningar för serviceprovider) utför du följande steg:

    ![Konfigurera enkel inloggning på appsidan](./media/front-tutorial/tutorial_front_005.png)

    a. Kopiera värdet för **entitets-ID** och klistra in det i textrutan **Identifierare** i avsnittet **Front Domain and URLs** (Domän och URL:er för Front) i Azure-portalen.

    b. Kopiera värdet för **ACS URL** och klistra in det i textrutan **Svars-URL** i avsnittet **Front Domain and URLs** (Domän och URL:er för Front) i Azure-portalen.
    
8. Klicka på **spara** knappen.

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

I det här avsnittet gör du det möjligt för Britta Simon att använda enkel inloggning med Azure genom att ge åtkomst till Front.

1. I Azure-portalen väljer du **Företagsprogram**, **Alla program** och sedan **Front**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I programlistan väljer du **Front**.

    ![Länken för Front i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-front-test-user"></a>Skapa Front-testanvändare

I det här avsnittet skapar du en användare med namnet Britta Simon i Front. Ta hjälp av [kundsupporten för Front](mailto:support@frontapp.com) för att lägga till användarna på Front-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på Front-panelen i åtkomstpanelen bör du automatiskt loggas in på Front som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

