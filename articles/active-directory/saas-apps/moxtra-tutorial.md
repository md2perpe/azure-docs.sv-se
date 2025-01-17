---
title: 'Självstudier: Azure Active Directory-integrering med Moxtra | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Moxtra.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 2aed2d4b-1dcd-4839-8fed-9419d107c61c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/01/2019
ms.author: jeedes
ms.openlocfilehash: f22f70c587556793c2b02fc79fa6175584b0c5cc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67096643"
---
# <a name="tutorial-azure-active-directory-integration-with-moxtra"></a>Självstudier: Azure Active Directory-integrering med Moxtra

I den här självstudien får du lära dig hur du integrerar Moxtra med Azure Active Directory (AD Azure).
Integrera Moxtra med Azure AD ger dig följande fördelar:

* Du kan styra i Azure AD som har åtkomst till Moxtra.
* Du kan aktivera användarna att vara automatiskt inloggad till Moxtra (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure portal.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill konfigurera Azure AD-integrering med Moxtra, behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Moxtra enkel inloggning aktiverat prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Har stöd för Moxtra **SP** -initierad SSO

## <a name="adding-moxtra-from-the-gallery"></a>Att lägga till Moxtra från galleriet

För att konfigurera integrering av Moxtra i Azure AD, som du behöver lägga till Moxtra från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Moxtra från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)** , klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver **Moxtra**väljer **Moxtra** resultatet panelen klickar **Lägg till** för att lägga till programmet.

     ![Moxtra i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Moxtra baserat på en testanvändare kallas **Britta Simon**.
För enkel inloggning ska fungera, måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Moxtra upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med Moxtra, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Konfigurera Moxtra Single Sign-On](#configure-moxtra-single-sign-on)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Skapa testanvändare Moxtra](#create-moxtra-test-user)**  – du har en motsvarighet för Britta Simon i Moxtra som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera Azure AD enkel inloggning med Moxtra:

1. I den [Azure-portalen](https://portal.azure.com/)på den **Moxtra** application integration markerar **enkel inloggning**.

    ![Konfigurera enkel inloggning för länken](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Moxtra domän och URL: er med enkel inloggning för information](common/sp-signonurl.png)

    I textrutan **Inloggnings-URL** skriver du in en URL med följande mönster: `https://www.moxtra.com/service/#login`

5. Moxtra program som förväntar SAML-intyg i ett visst format, vilket kräver att du kan lägga till anpassade attributmappningar i SAML-tokenattribut konfigurationen. I följande skärmbild visas listan över standardattribut. Klicka på ikonen  **Redigera** för att öppna dialogrutan **Användarattribut** .

    ![image](common/edit-attribute.png)

6. Förutom ovanstående Moxtra program som förväntar få fler attribut som ska skickas tillbaka i SAML-svar. I avsnittet **Användaranspråk** i dialogrutan **Användarattribut** utför du följande steg för att lägga till SAML-tokenattributet enligt det som visas i tabellen nedan: 

    | Namn | Källattribut|
    | ------------------- | -------------------- |    
    | förnamn | user.givenname |
    | lastname | user.surname |
    | idpid    | < Azure AD Identifier >

    > [!Note]
    > Värdet för **idpid** attributet inte är verkliga. Du kan få det faktiska värdet från **konfigurera Moxtra** avsnitt från steg 8. 

    a. Klicka på **Lägg till nytt anspråk** för att öppna dialogrutan **Hantera användaranspråk**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. I textrutan **Namn** skriver du det attributnamn som visas för den raden.

    c. Lämna **Namnrymd** tom.

    d. Välj Källa som **Attribut**.

    e. Från listan över **Källattribut** skriver du det attributvärde som visas för den raden.

    f. Klicka på **Ok**

    g. Klicka på **Spara**.

7. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för hämtning av certifikat](common/certificatebase64.png)

8. På den **konfigurera Moxtra** avsnittet, kopiera den lämpliga URL: er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggningswebbadress

    b. Microsoft Azure Active Directory-identifierare

    c. Utloggnings-URL

### <a name="configure-moxtra-single-sign-on"></a>Konfigurera Moxtra Single Sign-On

1. I ett nytt webbläsarfönster inloggning till webbplatsen Moxtra företag som administratör.

2. I verktygsfältet till vänster, klickar du på **-administratörskonsolen > SAML enkel inloggning**, och klicka sedan på **New**.
   
    ![Konfigurera enkel inloggning](./media/moxtra-tutorial/tutorial_moxtra_06.png) 

3. På den **SAML** utför följande steg:
   
    ![Konfigurera enkel inloggning](./media/moxtra-tutorial/tutorial_moxtra_08.png)   
 
    a. I den **namn** textrutan anger du ett namn för din konfiguration (t.ex.: *SAML*). 
  
    b. I den **IdP entitets-ID** textrutan klistra in värdet för **Azure AD-identifierare** som du har kopierat från Azure-portalen. 
 
    c. I textrutan för **inloggnings-URL** klistrar du in värdet för **inloggnings-URL:en** som du har kopierat från Azure-portalen. 
 
    d. I den **AuthnContextClassRef** textrutan typ **urn: oasis: namn: tc: SAML:2.0:ac:classes:Password**. 
 
    e. I den **NameID-Format** textrutan typ **urn: oasis: namn: tc: SAML:1.1:nameid-format: e-postadress**. 
 
    f. Öppna certifikat som du har hämtat från Azure-portalen i anteckningar, kopiera innehållet och klistra in den i den **certifikat** textrutan.    
 
    g. Skriv din e-postdomän SAML i textrutan SAML e-domän.    
  
    >[!NOTE]
    >Om du vill se stegen för att verifiera domänen, klickar du på den ”**jag**” nedan.

    h. Klicka på **Uppdatera**.

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

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Moxtra.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**och välj sedan **Moxtra**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **Moxtra**.

    ![Länken Moxtra i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-moxtra-test-user"></a>Skapa Moxtra testanvändare

Målet med det här avsnittet är att skapa en användare som kallas Britta Simon i Moxtra.

**Om du vill skapa en användare som kallas Britta Simon i Moxtra, utför du följande steg:**

1. Logga in på webbplatsen Moxtra företag som administratör.

1. I verktygsfältet till vänster, klickar du på **-administratörskonsolen > Användarhantering**, och sedan **Lägg till användare**.
   
    ![Konfigurera enkel inloggning](./media/moxtra-tutorial/tutorial_moxtra_10.png) 

1. I dialogrutan **Lägg till användare** utför du följande steg:
  
    a. I textrutan **Förnamn** skriver du **Britta**.
  
    b. I textrutan **Efternamn** skriver du **Simon**.
  
    c. I den **e-post** textrutan typ Brittas e-postadressen samma som på Azure-portalen.
  
    d. I den **Division** textrutan typ **Dev**.
  
    e. I den **avdelning** textrutan typ **IT**.
  
    f. Välj **administratör**.
  
    g. Klicka på **Lägg till**.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Moxtra i åtkomstpanelen, bör det vara loggas in automatiskt till Moxtra som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

