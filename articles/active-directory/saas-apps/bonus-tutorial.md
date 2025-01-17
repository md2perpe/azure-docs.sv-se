---
title: 'Självstudier: Azure Active Directory-integrering med Bonusly | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Bonusly.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 29fea32a-fa20-47b2-9e24-26feb47b0ae6
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f605398896c93d15b7475c05c79361fdcefcfcab
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67442778"
---
# <a name="tutorial-azure-active-directory-integration-with-bonusly"></a>Självstudier: Azure Active Directory-integrering med Bonusly

I den här självstudien får du lära dig hur du integrerar Bonusly med Azure Active Directory (AD Azure).
Integrera Bonusly med Azure AD ger dig följande fördelar:

* Du kan styra i Azure AD som har åtkomst till Bonusly.
* Du kan aktivera användarna att vara automatiskt inloggad till Bonusly (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure portal.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Bonusly, behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få en [kostnadsfritt konto](https://azure.microsoft.com/free/)
* Bonusly enkel inloggning aktiverat prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Har stöd för bonusly **IDP** -initierad SSO

## <a name="adding-bonusly-from-the-gallery"></a>Att lägga till Bonusly från galleriet

För att konfigurera integrering av Bonusly i Azure AD, som du behöver lägga till Bonusly från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Bonusly från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)** , klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver **Bonusly**väljer **Bonusly** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![Bonusly i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Bonusly baserat på en testanvändare kallas **Britta Simon**.
För enkel inloggning ska fungera, måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Bonusly upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med Bonusly, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Konfigurera Bonusly enkel inloggning](#configure-bonusly-single-sign-on)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Skapa Bonusly testanvändare](#create-bonusly-test-user)**  – du har en motsvarighet för Britta Simon i Bonusly som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera Azure AD enkel inloggning med Bonusly:

1. I den [Azure-portalen](https://portal.azure.com/)på den **Bonusly** application integration markerar **enkel inloggning**.

    ![Konfigurera enkel inloggning för länken](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Bonusly domän och URL: er med enkel inloggning för information](common/idp-reply.png)

    I textrutan **Svars-URL** skriver du en URL med följande mönster: `https://Bonus.ly/saml/<tenant-name>`

    > [!NOTE]
    > Värdet är inte verkligt. Uppdatera värdet för med den faktiska svars-URL:en. Kontakta [Bonusly klienten supportteamet](https://bonus.ly/contact) att hämta värdet. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. I avsnittet **SAML-signeringscertifikat** klickar du på knappen **Redigera** för att öppna dialogrutan **SAML-signeringscertifikat**.

    ![Redigera SAML-signeringscertifikat](common/edit-certificate.png)

6. Kopiera **Tumavtryck** i avsnittet **SAML-signeringscertifikat** och spara det på datorn.

    ![Kopiera värdet för Tumavtryck](common/copy-thumbprint.png)

7. På den **konfigurera Bonusly** avsnittet, kopiera den lämpliga URL: er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggningswebbadress

    b. Microsoft Azure Active Directory-identifierare

    c. Utloggnings-URL

### <a name="configure-bonusly-single-sign-on"></a>Konfigurera Bonusly enkel inloggning

1. I ett annat webbläsarfönster, loggar du in din **Bonusly** klient.

1. I verktygsfältet högst upp, klickar du på **inställningar** och välj sedan **integreringar och appar**.

    ![Bonusly sociala avsnittet](./media/bonus-tutorial/ic773686.png "Bonusly")
1. Under **enkel inloggning**väljer **SAML**.

1. På den **SAML** dialogrutan utför följande steg:

    ![Bonusly Saml dialogrutan sidan](./media/bonus-tutorial/ic773687.png "Bonusly")

    a. I den **mål-URL för IDP: N SSO** textrutan klistra in värdet för **inloggnings-URL**, som du har kopierat från Azure-portalen.

    b. I den **inloggnings-URL för IDP: N** textrutan klistra in värdet för **inloggnings-URL**, som du har kopierat från Azure-portalen.

    c. I den **IDP: N utfärdar** textrutan klistra in värdet för **Azure AD-identifierare**, som du har kopierat från Azure-portalen.
    
    d. Klistra in den **tumavtryck** värdet som har kopierats från Azure-portalen till den **Cert fingeravtryck** textrutan.
    
    e. Klicka på **Spara**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I den **användarnamn** fälttyp `brittasimon@yourcompanydomain.extension`. Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Bonusly.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**och välj sedan **Bonusly**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **Bonusly**.

    ![Bonusly länken i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-bonusly-test-user"></a>Skapa Bonusly testanvändare

För att aktivera Azure AD-användare att logga in på Bonusly, måste de etableras i Bonusly. När det gäller Bonusly är etablering en manuell aktivitet.

> [!NOTE]
> Du kan använda alla andra Bonusly användare konto verktyg för att skapa eller API: er som tillhandahålls av Bonusly att etablera AAD-användarkonton. 

**Utför följande steg för att konfigurera användarförsörjning:**

1. I ett webbläsarfönster, loggar du in din Bonusly klient.

1. Klicka på **Inställningar**.

    ![Inställningar](./media/bonus-tutorial/ic781041.png "Inställningar")

1. Klicka på den **användare och bonus** fliken.

    ![Användare och bonus](./media/bonus-tutorial/ic781042.png "användare och bonus")

1. Klicka på **hantera användare**.

    ![Hantera användare](./media/bonus-tutorial/ic781043.png "Hantera användare")

1. Klicka på **lägga till användare**.

    ![Lägg till användare](./media/bonus-tutorial/ic781044.png "Lägg till användare")

1. I dialogrutan **Lägg till användare** utför du följande steg:

    ![Lägg till användare](./media/bonus-tutorial/ic781045.png "Lägg till användare")  

    a. I textrutan **Förnamn** skriver du förnamnet på användaren: **Britta**.

    b. I textrutan **Efternamn** skriver du efternamnet på användaren: **Simon**.

    c. I den **e-post** textrutan Ange e-postadress för användaren som `brittasimon\@contoso.com`.

    d. Klicka på **Spara**.

    > [!NOTE]
    > Azure AD-kontoinnehavare tar emot ett e-postmeddelande som innehåller en länk för att bekräfta kontot innan det blir aktiv.  

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Bonusly i åtkomstpanelen, du bör vara loggas in automatiskt Bonusly som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
