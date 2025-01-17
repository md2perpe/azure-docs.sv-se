---
title: 'Självstudier: Azure Active Directory-integrering med TimeOffManager | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och TimeOffManager.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3685912f-d5aa-4730-ab58-35a088fc1cc3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 0d57b3ce4e378a366148b5c3eef6bc4d5d9eb96d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67088552"
---
# <a name="tutorial-azure-active-directory-integration-with-timeoffmanager"></a>Självstudier: Azure Active Directory-integrering med TimeOffManager

I den här självstudien får du lära dig hur du integrerar TimeOffManager med Azure Active Directory (AD Azure).
Integrera TimeOffManager med Azure AD ger dig följande fördelar:

* Du kan styra i Azure AD som har åtkomst till TimeOffManager.
* Du kan aktivera användarna att vara automatiskt inloggad till TimeOffManager (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure portal.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill konfigurera Azure AD-integrering med TimeOffManager, behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* TimeOffManager enkel inloggning aktiverat prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Har stöd för TimeOffManager **IDP** -initierad SSO

* Har stöd för TimeOffManager **Just In Time** etableringen av användare

## <a name="adding-timeoffmanager-from-the-gallery"></a>Att lägga till TimeOffManager från galleriet

För att konfigurera integrering av TimeOffManager i Azure AD, som du behöver lägga till TimeOffManager från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till TimeOffManager från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)** , klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver **TimeOffManager**väljer **TimeOffManager** resultatet panelen klickar **Lägg till** för att lägga till programmet.

     ![TimeOffManager i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med TimeOffManager baserat på en testanvändare kallas **Britta Simon**.
För enkel inloggning ska fungera, måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i TimeOffManager upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med TimeOffManager, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Konfigurera TimeOffManager Single Sign-On](#configure-timeoffmanager-single-sign-on)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Skapa testanvändare TimeOffManager](#create-timeoffmanager-test-user)**  – du har en motsvarighet för Britta Simon i TimeOffManager som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera Azure AD enkel inloggning med TimeOffManager:

1. I den [Azure-portalen](https://portal.azure.com/)på den **TimeOffManager** application integration markerar **enkel inloggning**.

    ![Konfigurera enkel inloggning för länken](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![TimeOffManager domän och URL: er med enkel inloggning för information](common/idp-reply.png)

    I textrutan **Svars-URL** skriver du en URL med följande mönster: `https://www.timeoffmanager.com/cpanel/sso/consume.aspx?company_id=<companyid>`

    > [!NOTE]
    > Det här värdet är inte verkligt. Uppdatera det här värdet med den faktiska svars-URL:en. Du kan få det här värdet från **inställningssidan för enkelinloggning** som beskrivs senare i självstudien eller kontakta [TimeOffManager supportteamet](https://www.purelyhr.com/contact-us). Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. TimeOffManager program som förväntar SAML-intyg i ett visst format, vilket kräver att du kan lägga till anpassade attributmappningar i SAML-tokenattribut konfigurationen. I följande skärmbild visas listan över standardattribut. Klicka på ikonen  **Redigera** för att öppna dialogrutan **Användarattribut** .

    ![image](common/edit-attribute.png)

6. Förutom ovanstående TimeOffManager program som förväntar få fler attribut som ska skickas tillbaka i SAML-svar. I avsnittet **Användaranspråk** i dialogrutan **Användarattribut** utför du följande steg för att lägga till SAML-tokenattributet enligt det som visas i tabellen nedan: 

    | Namn | Källattribut|
    | --- | --- |
    | Förnamn |User.givenName |
    | Efternamn |User.surname |
    | E-post |User.mail |

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

8. På den **konfigurera TimeOffManager** avsnittet, kopiera den lämpliga URL: er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggningswebbadress

    b. Microsoft Azure Active Directory-identifierare

    c. Utloggnings-URL

### <a name="configure-timeoffmanager-single-sign-on"></a>Konfigurera TimeOffManager Single Sign-On

1. Logga in på webbplatsen TimeOffManager företag som en administratör i ett annat webbläsarfönster.

2. Gå till **konto \> kontoalternativ \> enkel inloggning inställningar**.
   
    ![Inställningar för enkel inloggning](./media/timeoffmanager-tutorial/ic795917.png "Inställningar för enkel inloggning")

3. I den **inställningar för enkel inloggning** avsnittet, utför följande steg:
   
    ![Inställningar för enkel inloggning](./media/timeoffmanager-tutorial/ic795918.png "Inställningar för enkel inloggning")
   
    a. Öppna din Base64-kodat certifikat i anteckningar, kopiera innehållet i den till Urklipp och klistra in hela certifikatet i **X.509-certifikat** textrutan.
   
    b. I **IDP: N utfärdar** textrutan klistra in värdet för **Azure AD-identifierare** som du har kopierat från Azure-portalen.
   
    c. I **slutpunkts-URL för IDP: N** textrutan klistra in värdet för **inloggnings-URL** som du har kopierat från Azure-portalen.
   
    d. Som **framtvinga SAML**väljer **nr**.
   
    e. Som **skapa automatiskt användare**väljer **Ja**.
   
    f. I textrutan för **utloggnings-URL** klistrar du in värdet för **utloggnings-URL:en** som du har kopierat från Azure-portalen.
   
    g. Klicka på **spara ändringar**.

4. I **inställningar för enkelinloggning** sidan, Kopiera värdet för **URL för Konsumenttjänst för försäkran** och klistra in den i den **svars-URL** textrutan under **grundläggande SAML Konfigurationen** avsnitt i Azure-portalen. 

      ![Inställningar för enkel inloggning](./media/timeoffmanager-tutorial/ic795915.png "Inställningar för enkel inloggning")

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I den **användarnamn** fälttyp brittasimon@yourcompanydomain.extension. Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till TimeOffManager.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**och välj sedan **TimeOffManager**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **TimeOffManager**.

    ![Länken TimeOffManager i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-timeoffmanager-test-user"></a>Skapa TimeOffManager testanvändare

I det här avsnittet skapas en användare som kallas Britta Simon i TimeOffManager. TimeOffManager stöder etableringen av just-in-time-användare som är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i TimeOffManager, skapas en ny efter autentisering.

>[!NOTE]
>Du kan använda alla andra TimeOffManager användare konto verktyg för att skapa eller API: er som tillhandahålls av TimeOffManager att etablera användarkonton i Azure AD.
> 

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen TimeOffManager i åtkomstpanelen, bör det vara loggas in automatiskt till TimeOffManager som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

