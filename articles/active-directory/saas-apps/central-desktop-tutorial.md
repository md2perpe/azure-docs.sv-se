---
title: 'Självstudier: Azure Active Directory-integrering med Central Desktop | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Central Desktop.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: b805d485-93db-49b4-807a-18d446c7090e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/12/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 58fcd21c7e6e3f2336d3e809584451dc38f6276b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67105737"
---
# <a name="tutorial-azure-active-directory-integration-with-central-desktop"></a>Självstudier: Azure Active Directory-integrering med Central Desktop

I den här självstudien lär du dig att integrera Central Desktop med Azure Active Directory (AD Azure).
Integreringen av Central Desktop med Azure AD medför följande fördelar:

* Du kan i Azure AD styra vem som har åtkomst till Central Desktop.
* Du kan göra så att dina användare loggas in automatiskt på Central Desktop (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure portal.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

För att konfigurera Azure AD-integrering med Central Desktop behöver du följande:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Central Desktop-prenumeration med enkel inloggning aktiverat

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Central Desktop har stöd för **SP**-initierad enkel inloggning

## <a name="adding-central-desktop-from-the-gallery"></a>Lägga till Central Desktop från galleriet

För att konfigurera integreringen av Central Desktop i Azure AD måste du lägga till Central Desktop från galleriet till din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Central Desktop från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)** , klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **Central Desktop**, väljer **Central Desktop** i resultatpanelen och klickar på knappen **Lägg till** för att lägga till programmet.

     ![Central Desktop i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet konfigurerar och testar du enkel inloggning i Azure AD med Central Desktop baserat på en testanvändare med namnet **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i Central Desktop upprättas.

För att konfigurera och testa enkel inloggning för Azure AD med Central Desktop behöver du slutföra följande byggstenar:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Konfigurera enkel inloggning för Central Desktop](#configure-central-desktop-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Skapa Central Desktop-testanvändare](#create-central-desktop-test-user)** – för att ha en motsvarighet för Britta Simon i Central Desktop som är länkad till Azure AD-representationen av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning med Azure AD för Central Desktop:

1. I [Azure-portalen](https://portal.azure.com/) går du till programintegreringssidan för **Central Desktop** och väljer **Enkel inloggning**.

    ![Konfigurera enkel inloggning för länken](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Central Desktop-domän och information om URL:er för enkel inloggning](common/sp-identifier-reply.png)

    a. I textrutan **Inloggnings-URL** skriver du en URL med följande mönster: `https://<companyname>.centraldesktop.com`

    b. I rutan **Identifierare** skriver du en URL med följande mönster:
    
    | |
    |--|
    | `https://<companyname>.centraldesktop.com/saml2-metadata.php`|
    | `https://<companyname>.imeetcentral.com/saml2-metadata.php`|
    | |

    c. I textrutan **svars-URL** skriver du en URL med följande mönster: `https://<companyname>.centraldesktop.com/saml2-assertion.php`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med den faktiska inloggnings-URL:en, identifieraren och svars-URL:en. Kontakta [kundsupporten för Central Desktop](https://imeetcentral.com/contact-us) och be om dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Konfigurera enkel inloggning med SAML**, i avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (RAW)** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för hämtning av certifikat](common/certificateraw.png)

6. I avsnittet **Konfigurera Central Desktop** kopierar du lämpliga URL:er efter behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggningswebbadress

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-central-desktop-single-sign-on"></a>Konfigurera enkel inloggning för Central Desktop

1. Logga in på din **Central Desktop**-klientorganisation.

2. Gå till **Inställningar**. Välj **Avancerat** och sedan **Enkel inloggning**.

    ![Konfiguration – Avancerat](./media/central-desktop-tutorial/ic769563.png "Konfiguration – Avancerat")

3. På sidan **Single Sign On Settings** (Inställningar för enkel inloggning) utför du följande steg:

    ![Inställningar för enkel inloggning](./media/central-desktop-tutorial/ic769564.png "Inställningar för enkel inloggning")

    a. Välj **Enable SAML v2 Single Sign On** (Aktivera enkel inloggning med SAML v2).

    b. I textrutan **SSO URL** (URL för enkel inloggning) klistrar du in värdet för den **Azure AD-identifierare** som du kopierade från Azure-portalen.

    c. I rutan **SSO Login URL** (Inloggnings-URL för enkel inloggning) klistrar du in det värde för **inloggnings-URL** som du kopierade från Azure-portalen.

    d. I rutan **SSO Logout URL** (Utloggnings-URL för enkel inloggning) klistrar du in det värde för **inloggnings-URL** som du kopierade från Azure-portalen.

4. I avsnittet **Message Signature Verification Method** (Verifieringsmetod för meddelandesignatur) utför du följande steg:

    ![Verifieringsmetod för meddelandesignatur](./media/central-desktop-tutorial/ic769565.png "Verifieringsmetod för meddelandesignatur")
    
    a. Välj **Certifikat**.

    b. I listan **SSO Certificate** (Certifikat för enkel inloggning) väljer du **RSH SHA256**.

    c. Öppna det nedladdade certifikatet i Anteckningar. Kopiera innehållet i certifikatet och klistra in det i fältet **SSO Certificate** (Certifikat för enkel inloggning).

    d. Välj **Display a link to your SAMLv2 login page** (Visa en länk till din SAMLv2-inloggningssida).

    e. Välj **Uppdatera**.

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

I det här avsnittet gör du det möjligt för Britta Simon att använda enkel inloggning med Azure genom att ge åtkomst till Central Desktop.

1. I Azure-portalen väljer du **Företagsprogram**, **Alla program** och sedan **Central Desktop**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I programlistan väljer du **Central Desktop**.

    ![Länken för Central Desktop i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-central-desktop-test-user"></a>Skapa Central Desktop-testanvändare

För att Azure AD-användare ska kunna logga in måste de etableras i Central Desktop-programmet. Det här avsnittet beskriver hur du skapar Azure AD-användarkonton i Central Desktop.

> [!NOTE]
> För att etablera Azure AD-användarkonton kan du använda andra verktyg eller API:er för att skapa Central Desktop-användarkonton som tillhandahålls av Central Desktop.

**Så här etablerar du användarkonton till Central Desktop:**

1. Logga in på din Central Desktop-klientorganisation.

2. Välj **Personer** och sedan **Add Internal Members** (Lägg till interna medlemmar).

    ![Personer](./media/central-desktop-tutorial/ic781051.png "Personer")

3. I rutan **Email Address of New Members** (E-postadress för nya medlemmar) skriver du ett Azure AD-konto som du vill etablera och väljer sedan **Nästa**.

    ![E-postadress för nya medlemmar](./media/central-desktop-tutorial/ic781052.png "E-postadress för nya medlemmar")

4. Välj **Add Internal member(s)** (Lägg till interna medlemmar).

    ![Lägg till intern medlem](./media/central-desktop-tutorial/ic781053.png "Lägg till intern medlem")
  
   > [!NOTE]
   > De användare som du lägger till får ett e-postmeddelande som innehåller en bekräftelselänk för att aktivera sina konton.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på Central Desktop-panelen i åtkomstpanelen bör du automatiskt loggas in på Central Desktop som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
