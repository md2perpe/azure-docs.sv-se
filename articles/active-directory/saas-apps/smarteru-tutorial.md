---
title: 'Självstudier: Azure Active Directory-integrering med SmarterU | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och SmarterU.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 95fe3212-d052-4ac8-87eb-ac5305227e85
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/19/2019
ms.author: jeedes
ms.openlocfilehash: 07c7a15ea713bb6f73e1174a681dcccb5e79e289
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67090520"
---
# <a name="tutorial-azure-active-directory-integration-with-smarteru"></a>Självstudier: Azure Active Directory-integrering med SmarterU

I den här självstudien får du lära dig hur du integrerar SmarterU med Azure Active Directory (AD Azure).
Integrera SmarterU med Azure AD ger dig följande fördelar:

* Du kan styra i Azure AD som har åtkomst till SmarterU.
* Du kan aktivera användarna att vara automatiskt inloggad till SmarterU (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure portal.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill konfigurera Azure AD-integrering med SmarterU, behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* SmarterU enkel inloggning aktiverat prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Har stöd för SmarterU **IDP** -initierad SSO

## <a name="adding-smarteru-from-the-gallery"></a>Att lägga till SmarterU från galleriet

För att konfigurera integrering av SmarterU i Azure AD, som du behöver lägga till SmarterU från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till SmarterU från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)** , klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver **SmarterU**väljer **SmarterU** resultatet panelen klickar **Lägg till** för att lägga till programmet.

     ![SmarterU i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med SmarterU baserat på en testanvändare kallas **Britta Simon**.
För enkel inloggning ska fungera, måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i SmarterU upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med SmarterU, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Konfigurera SmarterU Single Sign-On](#configure-smarteru-single-sign-on)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Skapa testanvändare SmarterU](#create-smarteru-test-user)**  – du har en motsvarighet för Britta Simon i SmarterU som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera Azure AD enkel inloggning med SmarterU:

1. I den [Azure-portalen](https://portal.azure.com/)på den **SmarterU** application integration markerar **enkel inloggning**.

    ![Konfigurera enkel inloggning för länken](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![SmarterU domän och URL: er med enkel inloggning för information](common/idp-identifier.png)

    I textrutan **Identifierare** skriver du URL:en: `https://www.smarteru.com/`

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar på **Hämta** för att hämta **Metadata-XML för federationen** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för hämtning av certifikat](common/metadataxml.png)

6. På den **konfigurera SmarterU** avsnittet, kopiera den lämpliga URL: er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggningswebbadress

    b. Microsoft Azure Active Directory-identifierare

    c. Utloggnings-URL

### <a name="configure-smarteru-single-sign-on"></a>Konfigurera SmarterU Single Sign-On

1. I ett annat webbläsarfönster, loggar du in din SmarterU företagets webbplats som administratör.

1. I verktygsfältet högst upp, klickar du på **kontoinställningar**.

    ![Kontoinställningar](./media/smarteru-tutorial/accountsettings.png)

1. På sidan för kontokonfiguration utför du följande steg:

    ![Externa auktorisering](./media/smarteru-tutorial/externalauthorizationconfiguration.png) 

    a. Välj **Aktivera externa auktorisering**.
  
    b. I den **Master inloggningen kontroll** väljer den **SmarterU** fliken.
  
    c. I den **standard användarinloggning** väljer den **SmarterU** fliken.
  
    d. Välj **Aktivera SAML**.
  
    e. Kopiera innehållet i den hämtade metadatafilen och klistra in den i den **IdP Metadata** textrutan.

    f. Välj en **identifierare attributet/krav**.
  
    g. Klicka på **Spara**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I fältet **Användarnamn** anger du **brittasimon@yourcompanydomain.extension**  
    Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till SmarterU.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**och välj sedan **SmarterU**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **SmarterU**.

    ![Länken SmarterU i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-smarteru-test-user"></a>Skapa SmarterU testanvändare

Om du vill aktivera Azure AD-användare att logga in på SmarterU, måste de etableras i SmarterU. När det gäller SmarterU är etablering en manuell aktivitet.

**Utför följande steg för att etablera ett användarkonto:**

1. logga in på din **SmarterU** klient.

1. Gå till **användare**.

1. I avsnittet användare utför du följande steg:

    ![Ny användare](./media/smarteru-tutorial/adduser.png)  

    a. Klicka på **+ användare**.

    b. Skriv relaterade attributvärdena för Azure AD-användarkonto i följande textrutor: **Primär e-postadress**, **anställnings-ID**, **lösenord**, **verifiera lösenord**, **Förnamn**, **efternamn**.

    c. Klicka på **Active**.

    d. Klicka på **Spara**.

> [!NOTE]
> Du kan använda alla andra SmarterU användare konto verktyg för att skapa eller API: er som tillhandahålls av SmarterU att etablera AAD-användarkonton.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen SmarterU i åtkomstpanelen, bör det vara loggas in automatiskt till SmarterU som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
