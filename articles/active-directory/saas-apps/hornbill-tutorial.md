---
title: 'Självstudier: Azure Active Directory-integrering med Hornbill | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Hornbill.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 173061e4-ac1d-458f-bb9b-e9a2493aab0e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/15/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: c5bcd0401e1806b958ad47297dccfe26093cf461
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67101156"
---
# <a name="tutorial-azure-active-directory-integration-with-hornbill"></a>Självstudier: Azure Active Directory-integrering med Hornbill

I den här självstudien lär du dig att integrera Hornbill med Azure Active Directory (AD Azure).
Integreringen av Hornbill med Azure AD medför följande fördelar:

* Du kan i Azure AD styra vem som har åtkomst till Hornbill.
* Du kan göra så att dina användare automatiskt loggas in på Hornbill (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure portal.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

För att konfigurera Azure AD-integrering med Hornbill behöver du följande:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Hornbill-prenumeration med enkel inloggning aktiverat

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Hornbill har stöd för **SP**-initierad enkel inloggning
* Hornbill har stöd för **just-in-time**-användaretablering

## <a name="adding-hornbill-from-the-gallery"></a>Lägga till Hornbill från galleriet

För att konfigurera integreringen av Hornbill i Azure AD behöver du lägga till Hornbill från galleriet till din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Hornbill från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)** , klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **Hornbill**, väljer **Hornbill** i resultatpanelen och klickar på knappen **Lägg till** för att lägga till programmet.

     ![Hornbill i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet konfigurerar och testar du enkel inloggning i Azure AD med Hornbill baserat på en testanvändare med namnet **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i Hornbill upprättas.

För att kunna konfigurera och testa enkel inloggning i Azure AD med Hornbill slutför du följande byggstenar:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Konfigurera enkel inloggning för Hornbill](#configure-hornbill-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Skapa Hornbill-testanvändare](#create-hornbill-test-user)** – för att ha en motsvarighet för Britta Simon i Hornbill som är länkad till Azure AD-representationen av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning i Azure AD med Hornbill:

1. I [Azure-portalen](https://portal.azure.com/) går du till programintegreringssidan för **Hornbill** och väljer **Enkel inloggning**.

    ![Konfigurera enkel inloggning för länken](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Information om enkel inloggning med Hornbill-domän och URL:er](common/sp-identifier.png)

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<SUBDOMAIN>.hornbill.com/<INSTANCE_NAME>/`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://<SUBDOMAIN>.hornbill.com/<INSTANCE_NAME>/lib/saml/auth/simplesaml/module.php/saml/sp/metadata.php/saml`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med faktisk inloggnings-URL och identifierare. Kontakta [kundsupporten för Hornbill](https://www.hornbill.com/support/?request/) och be om dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på kopieringsknappen för att kopiera **App Federation Metadata-URL** och spara den på datorn.

    ![Länk för hämtning av certifikat](common/copy-metadataurl.png)

### <a name="configure-hornbill-single-sign-on"></a>Konfigurera enkel inloggning för Hornbill

1. I ett annat webbläsarfönster loggar du in på Hornbill som säkerhetsadministratör.

2. På startsidan klickar du på **System**.

    ![Hornbill-systemet](./media/hornbill-tutorial/tutorial_hornbill_system.png)

3. Gå till **Security** (Säkerhet).

    ![Säkerhet för Hornbill](./media/hornbill-tutorial/tutorial_hornbill_security.png)

4. Klicka på **SSO Profiles** (Profil för enkel inloggning).

    ![Hornbill, enkel inloggning](./media/hornbill-tutorial/tutorial_hornbill_sso.png)

5. Till höger på sidan klickar du på **Add logo** (Lägg till logotyp).

    ![Hornbill, lägg till](./media/hornbill-tutorial/tutorial_hornbill_addlogo.png)

6. I fältet **Profile Details** (Profilinformation) klickar du på **Import SAML Meta logo** (Importera SAML-metalogotyp).

    ![Hornbill-logotyp](./media/hornbill-tutorial/tutorial_hornbill_logo.png)

7. På popup-sidan i textrutan **URL** klistrar du in den **URL för appfederationsmetadata** som du har kopierat från Azure-portalen och klickar på **Process** (Bearbeta).

    ![Hornbill-bearbetning](./media/hornbill-tutorial/tutorial_hornbill_process.png)

8. När du har klickat på Process (Bearbeta) fylls värdena i avsnittet **Profile Details** (Profilinformation) i automatiskt.

    ![Hornbill, sida 1](./media/hornbill-tutorial/tutorial_hornbill_ssopage.png)

    ![Hornbill, sida 2](./media/hornbill-tutorial/tutorial_hornbill_ssopage1.png)

    ![Hornbill, sida 3](./media/hornbill-tutorial/tutorial_hornbill_ssopage2.png)

9. Klicka på **Spara ändringar**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I den **namn** anger **BrittaSimon**.
  
    b. I den **användarnamn** skriver **brittasimon\@yourcompanydomain.extension**  
    Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet gör du det möjligt för Britta Simon att använda enkel inloggning med Azure genom att ge åtkomst till Hornbill.

1. I Azure-portalen väljer du **Företagsprogram**, **Alla program** och sedan **Hornbill**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I programlistan väljer du **Hornbill**.

    ![Länken för Hornbill i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-hornbill-test-user"></a>Skapa Hornbill-testanvändare

I det här avsnittet skapas en användare som heter Britta Simon i Hornbill. Hornbill har stöd för just-in-time-användaretablering, vilket är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om det inte redan finns någon användare i Hornbill skapas en ny efter autentisering.

> [!Note]
> Om du behöver skapa en användare manuellt kontaktar du  [kundsupporten för Hornbill](https://www.hornbill.com/support/?request/).

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på Hornbill-panelen i åtkomstpanelen bör du automatiskt loggas in på Hornbill som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

