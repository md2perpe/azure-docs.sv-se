---
title: 'Självstudier: Azure Active Directory-integrering med IdeaScale | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och IdeaScale.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: e16dda6b-fdf9-43cc-9bbb-a523f085a8af
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: a312e0ca70ef3a37b43f1ffb68a974a8aedaf982
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67100782"
---
# <a name="tutorial-azure-active-directory-integration-with-ideascale"></a>Självstudier: Azure Active Directory-katalogintegrering med IdeaScale

I den här självstudien lär du dig att integrera IdeaScale med Azure Active Directory (AD Azure).
När du integrerar IdeaScale med Azure AD innebär det följande fördelar:

* Du kan styra vem som har åtkomst till IdeaScale från Azure AD.
* Du kan göra så att dina användare automatiskt loggas in på IdeaScale (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure portal.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

För att konfigurera Azure AD-integrering med IdeaScale behöver du följande:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* IdeaScale-prenumeration med enkel inloggning aktiverat

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* IdeaScale har stöd för **SP**-initierad enkel inloggning

## <a name="adding-ideascale-from-the-gallery"></a>Lägga till IdeaScale från galleriet

För att konfigurera integreringen av IdeaScale i Azure AD måste du lägga till IdeaScale från galleriet till din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till IdeaScale från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)** , klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **IdeaScale**, väljer **IdeaScale** i resultatpanelen och klickar på knappen **Lägg till** för att lägga till programmet.

     ![IdeaScale i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa enkel inloggning i Azure AD med IdeaScale baserat på testanvändaren **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i IdeaScale upprättas.

För att konfigurera och testa enkel inloggning i Azure AD med IdeaScale måste du utföra följande uppgifter:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Konfigurera enkel inloggning för IdeaScale](#configure-ideascale-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Skapa IdeaScale-testanvändare](#create-ideascale-test-user)** – för att ha en motsvarighet för Britta Simon i IdeaScale som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning i Azure AD med IdeaScale:

1. Välj **Enkel inloggning** på sidan för programintegrering av **IdeaScale** på [Azure-portalen](https://portal.azure.com/).

    ![Konfigurera enkel inloggning för länken](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![IdeaScale-domän och webbadresser med information om enkel inloggning](common/sp-identifier.png)

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<companyname>.ideascale.com`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster:
    
    | |
    |--|
    | `http://<companyname>.ideascale.com`  |
    | `https://<companyname>.ideascale.com` |

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med faktisk inloggnings-URL och identifierare. Kontakta [supportteamet för IdeaScale-klienten](https://support.ideascale.com/) för att få dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för hämtning av certifikat](common/metadataxml.png)

6. I avsnittet **Konfigurera IdeaScale** kopierar du lämpliga URL:er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggningswebbadress

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-ideascale-single-sign-on"></a>Konfigurera enkel inloggning för IdeaScale

1. I ett annat webbläsarfönster loggar du in på IdeaScale-företagsplatsen som administratör.

2. Gå till **Community-inställningar**.

    ![Community-inställningar](./media/ideascale-tutorial/ic790847.png "Community-inställningar")

3. Gå till **Säkerhet \> Inställningar för enkel inloggning**.

    ![Inställningar för enkel inloggning](./media/ideascale-tutorial/ic790848.png "Inställningar för enkel inloggning")

4. Som **typ av enkel inloggning** väljer du **SAML 2.0**.

    ![Typ av enkel inloggning](./media/ideascale-tutorial/ic790849.png "Typ av enkel inloggning")

5. I dialogrutan **Inställningar för enkel inloggning** utför du följande steg:

    ![Inställningar för enkel inloggning](./media/ideascale-tutorial/ic790850.png "Inställningar för enkel inloggning")

    a. I textrutan för **SAML-entitets-ID för IdP** klistrar du in värdet för **Azure Ad-identifieraren**, som du har kopierat från Azure-portalen.

    b. Öppna den nedladdade metadatafilen från Azure-portalen i Anteckningar, kopiera innehållet och klistra in det i textrutan för **IdP-metadata för SAML**.

    c. I textrutan för **URL för lyckad utloggning** klistrar du in värdet för **utloggnings-URL:en** som du har kopierat från Azure-portalen.

    d. Klicka på **Spara ändringar**.

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

I det här avsnittet gör du det möjligt för Britta Simon att använda enkel inloggning med Azure genom att ge åtkomst till IdeaScale.

1. I Azure-portalen väljer du **Företagsprogram**, **Alla program** och sedan **IdeaScale**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer du **IdeaScale**.

    ![IdeaScale-länken i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-ideascale-test-user"></a>Skapa IdeaScale-testanvändare

Om du vill att Azure AD-användare ska kunna logga in på IdeaScale måste de vara etablerade i IdeaScale. I IdeaScale görs etablering manuellt.

**Utför följande steg för att konfigurera användarförsörjning:**

1. Logga in på din **IdeaScale**-företagsplats som administratör.

2. Gå till **Community-inställningar**.

    ![Community-inställningar](./media/ideascale-tutorial/ic790847.png "Community-inställningar")

3. Gå till **Grundinställningar \> Medlemshantering**.

4. Klicka på **Lägg till medlem**.

    ![Medlemshantering](./media/ideascale-tutorial/ic790852.png "Medlemshantering")

5. I avsnittet Lägga till ny medlem utför du följande steg:

    ![Lägga till ny medlem](./media/ideascale-tutorial/ic790853.png "Lägga till ny medlem")

    a. I textrutan **E-postadresser** skriver du in e-postadressen till ett giltigt Azure AD-konto som du vill etablera.

    b. Klicka på **Spara ändringar**.

    > [!NOTE]
    > Azure Active Directory-kontoinnehavaren får ett e-postmeddelande med en länk för att bekräfta kontot innan det blir aktivt.

> [!NOTE]
> Du kan använda andra verktyg eller API:er för IdeaScale-kontoskapande som tillhandahålls av IdeaScale för att etablera AAD-användarkonton.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på IdeaScale-panelen i åtkomstpanelen bör du automatiskt loggas in på IdeaScale som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

