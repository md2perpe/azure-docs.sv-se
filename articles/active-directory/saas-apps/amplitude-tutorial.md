---
title: 'Självstudier: Azure Active Directory-integrering med Amplitude | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Amplitude.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 496c9ffa-c833-41fa-8d17-2dc3044954d1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5eba2aad0c758aeff189ba8638d92c46ed0902c1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67107090"
---
# <a name="tutorial-azure-active-directory-integration-with-amplitude"></a>Självstudier: Azure Active Directory-katalogintegrering med Amplitude

I den här självstudien lär du dig att integrera Amplitude med Azure Active Directory (Azure AD).
Genom att integrera Amplitude med Azure AD får du följande fördelar:

* Du kan styra i vem som har åtkomst till Amplitude från Azure AD.
* Du kan konfigurera inställningar så att dina användare automatiskt loggas in i Amplitude (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure portal.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill konfigurera Azure AD-integrering med Amplitude behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Amplitude-prenumeration med enkel inloggning aktiverat

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Amplitude har stöd för **SP- och IDP**-initierad enkel inloggning
* Amplitude stöder **just-in-time**-användaretablering

## <a name="adding-amplitude-from-the-gallery"></a>Lägga till Amplitude från galleriet

För att konfigurera integrering av Amplitude i Azure AD, behöver du lägga till Amplitude från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Amplitude från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)** , klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **Amplitude**, väljer **Amplitude** i resultatpanelen och klickar på knappen **Lägg till** för att lägga till programmet.

    ![Amplitude i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD med Amplitude baserat på en testanvändare med namnet **Britta Simon**.
För att enkel inloggning ska fungera så måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i Amplitude upprättas.

Om du vill konfigurera och testa enkel inloggning med Azure AD för Amplitude, måste du utföra följande uppgifter:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Konfigurera enkel inloggning för Amplitude](#configure-amplitude-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Skapa Amplitude-testanvändare](#create-amplitude-test-user)** – för att ha en motsvarighet för Britta Simon i Amplitude som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning med Azure AD för Amplitude:

1. Välj **Enkel inloggning** på sidan för programintegrering av **Amplitude** på [Azure-portalen](https://portal.azure.com/).

    ![Konfigurera enkel inloggning för länken](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration**, om du vill konfigurera appen i **IDP**-initierat läge, gör du följande:

    ![Information om enkel inloggning med Amplitude-domäner och -URL:er](common/idp-intiated.png)

    a. I textrutan **Identifierare** skriver du URL:en: `https://amplitude.com/saml/sso/metadata`

    b. I textrutan **Svars-URL** skriver du in en URL med följande mönster: `https://analytics.amplitude.com/saml/sso/<uniqueid>`

    > [!NOTE]
    > Värdet för svars-URL:en är inte verkligt. Senare i den här självstudien får du svars-URL-värdet.

5. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    ![Information om enkel inloggning med Amplitude-domäner och -URL:er](common/metadata-upload-additional-signon.png)

    I rutan **Inloggnings-URL** anger du följande URL: `https://analytics.amplitude.com/sso`

6. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar på **Hämta** för att hämta **Metadata-XML för federationen** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för hämtning av certifikat](common/metadataxml.png)

7. I avsnittet **Konfigurera Amplitude** kopierar du lämpliga URL:er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggningswebbadress

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-amplitude-single-sign-on"></a>Konfigurera enkel inloggning med Amplitude

1. Logga in på Amplitudes företagswebbplats som administratör.

2. Klicka på **Plan Admin** (avtalsadministratör) från det vänstra navigeringsfältet.

    ![Konfigurera enkel inloggning](./media/amplitude-tutorial/configure1.png)

3. Välj **Microsoft Azure Active Directory-metadata** från **SSO-integreringen**.

    ![Konfigurera enkel inloggning](./media/amplitude-tutorial/configure2.png)

4. I avsnittet **Konfigurera enkel inloggning** utför du följande steg:

    ![Konfigurera enkel inloggning](./media/amplitude-tutorial/configure3.png)

    a. Öppna hämtade **Xml-metadata** från Azure-portalen i anteckningar och klistra in innehållet i textrutan för **Microsoft Azure Active Directory-metadata**.

    b. Kopiera värdet i **svars-URL:en (ACS)** och klistra in det i textrutan för **svars-URL** under **Grundläggande SAML-konfiguration** på Azure-portalen.

    c. Klicka på **Spara**

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

I det här avsnittet gör du det möjligt för Britta Simon att använda enkel inloggning med Azure genom att ge åtkomst till Amplitude.

1. I Azure-portalen väljer du **Företagsprogram**, **Alla program** och sedan **Amplitude**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer du **Amplitude**.

    ![Amplitude-länken i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-amplitude-test-user"></a>Skapa Amplitude-testanvändare

I det här avsnittet skapas en användare som heter Britta Simon i Amplitude. Amplitude stöder just-in-time-etablering av användare, vilket är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om det inte redan finns någon användare i Amplitude skapas en ny efter autentisering.

> [!Note]
> Om du vill skapa en användare manuellt kan du kontakta [supportteamet för Amplitude](https://amplitude.zendesk.com).

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på Amplitude-panelen i åtkomstpanelen så borde du automatiskt loggas in på den Amplitude som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
