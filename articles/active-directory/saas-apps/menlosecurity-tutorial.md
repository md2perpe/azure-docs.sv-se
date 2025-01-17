---
title: 'Självstudier: Azure Active Directory-integrering med Menlo Security | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Menlo Security.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 9e63fe6b-0ad0-405d-9e41-6a1a40a41df8
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/20/2019
ms.author: jeedes
ms.openlocfilehash: 8297b16ba5efa371adc4d9a20428f88a48545440
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67097704"
---
# <a name="tutorial-azure-active-directory-integration-with-menlo-security"></a>Självstudier: Azure Active Directory-integrering med Menlo Security

I den här självstudien lär du dig att integrera Menlo Security med Azure Active Directory (AD Azure).
Integreringen av Menlo Security med Azure AD medför följande fördelar:

* Du kan i Azure AD styra vem som har åtkomst till Menlo Security.
* Du kan göra så att dina användare automatiskt loggas in på Menlo Security (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure portal.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

För att konfigurera Azure AD-integrering med Menlo Security behöver du följande:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Menlo Security-prenumeration med enkel inloggning aktiverat

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Menlo Security har stöd för **SP**-initierad enkel inloggning

## <a name="adding-menlo-security-from-the-gallery"></a>Lägga till Menlo Security från galleriet

För att konfigurera integreringen av Menlo Security i Azure AD behöver du lägga till Menlo Security från galleriet till din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Menlo Security från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)** , klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **Menlo Security**, väljer **Menlo Security** i resultatpanelen och klickar på knappen **Lägg till** för att lägga till programmet.

     ![Menlo Security i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet konfigurerar och testar du enkel inloggning i Azure AD med Menlo Security baserat på en testanvändare med namnet **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i Menlo Security upprättas.

För att kunna konfigurera och testa enkel inloggning i Azure AD med Menlo Security slutför du följande byggstenar:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Konfigurera enkel inloggning för Menlo Security](#configure-menlo-security-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Skapa Menlo Security-testanvändare](#create-menlo-security-test-user)** – för att ha en motsvarighet för Britta Simon i Menlo Security som är länkad till Azure AD-representationen av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning i Azure AD med Menlo Security:

1. I [Azure-portalen](https://portal.azure.com/) går du till programintegreringssidan för **Menlo Security** och väljer **Enkel inloggning**.

    ![Konfigurera enkel inloggning för länken](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Information om enkel inloggning med Menlo Security-domän och URL:er](common/sp-identifier.png)

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<subdomain>.menlosecurity.com/account/login`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://<subdomain>.menlosecurity.com/safeview-auth-server/saml/metadata`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med faktisk inloggnings-URL och identifierare. Kontakta [kundsupporten för Menlo Security](https://www.menlosecurity.com/menlo-contact) och be om dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för hämtning av certifikat](common/certificatebase64.png)

6. I avsnittet **Konfigurera Menlo Security** kopierar du lämpliga URL:er efter behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggningswebbadress

    b. Microsoft Azure Active Directory-identifierare

    c. Utloggnings-URL

### <a name="configure-menlo-security-single-sign-on"></a>Konfigurera enkel inloggning för Menlo Security

1. För att konfigurera enkel inloggning på **Menlo Security**-sidan loggar du in på **Menlo Security**-webbplatsen som administratör.

2. Under **Inställningar** går du till **Autentisering** och utför följande åtgärder:
    
    ![Konfigurera enkel inloggning](./media/menlosecurity-tutorial/menlo_user_setup.png)

    a. Markera kryssrutan **Enable user authentication using SAML** (Aktivera användarautentisering med hjälp av SAML).

    b. Ange **Allow External Access** (Tillåt extern åtkomst) till **Ja**.

    c. Under **SAML Provider** väljer du **Azure Active Directory**.

    d. **SAML 2.0 Endpoint** (SAML 2.0-slutpunkt): Klistra in den **inloggnings-URL** som du har kopierat från Azure-portalen.

    e. **Service Identifier (Issuer)** (Tjänstidentifierare (Utfärdare)): Klistra in det värde för **Azure AD-identifierare** som du har kopierat från Azure-portalen.

    f. **X.509 Certificate** (X.509-certifikat): Öppna det **certifikat (Base64)** som laddats ned från Azure-portalen i Anteckningar och klistra in det i den här rutan.

    g. Spara inställningarna genom att klicka på **Spara**.

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

I det här avsnittet gör du det möjligt för Britta Simon att använda enkel inloggning med Azure genom att ge åtkomst till Menlo Security.

1. I Azure-portalen väljer du **Företagsprogram**, **Alla program** och sedan **Menlo Security**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I programlistan väljer du **Menlo Security**.

    ![Länken för Menlo Security i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-menlo-security-test-user"></a>Skapa Menlo Security-testanvändare

I det här avsnittet skapar du en användare med namnet Britta Simon i Menlo Security. Ta hjälp av [kundsupporten för Menlo Security](https://www.menlosecurity.com/menlo-contact) för att lägga till användarna i Menlo Security-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på Menlo Security-panelen i åtkomstpanelen bör du automatiskt loggas in på Menlo Security som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

