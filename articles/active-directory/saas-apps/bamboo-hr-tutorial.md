---
title: 'Självstudier: Integrering av Azure Active Directory med BambooHR | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och BambooHR.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: f826b5d2-9c64-47df-bbbf-0adf9eb0fa71
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/19/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2bc3de858102c02d49a7cefeef15de7e1c82220e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67106472"
---
# <a name="tutorial-azure-active-directory-integration-with-bamboohr"></a>Självstudier: Integrering av Azure Active Directory med BambooHR

I de här självstudierna får du lära dig hur du integrerar BambooHR med Azure Active Directory (Azure AD).
Genom att integrera BambooHR med Azure AD får du följande fördelar:

* Du kan styra vem som har åtkomst till BambooHR i Azure AD.
* Du kan göra så att dina användare loggas in automatiskt på BambooHR (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure portal.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill konfigurera integrering av Azure AD med BambooHR, behöver du följande:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* BambooHR-prenumeration med enkel inloggning aktiverat

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* BambooHR stöder **SP**-initierad enkel inloggning

## <a name="adding-bamboohr-from-the-gallery"></a>Lägga till BambooHR från galleriet

För att konfigurera integreringen av BambooHR i Azure AD, behöver du lägga till BambooHR från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till BambooHR från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)** , klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **BambooHR**, väljer **BambooHR** i resultatpanelen och klickar sedan på knappen **Lägg till** för att lägga till programmet.

     ![BambooHR i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD på BambooHR baserat på en testanvändare med namnet **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i BambooHR upprättas.

Om du vill konfigurera och testa enkel inloggning med Azure AD på BambooHR, måste du slutföra följande steg:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Konfigurera enkel inloggning på BambooHR](#configure-bamboohr-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Skapa en testanvändare för BambooHR](#create-bamboohr-test-user)**  – för att få en motsvarighet till Britta Simon i BambooHR som är länkad till en Azure AD-representationen av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning med Azure AD på BambooHR:

1. I [Azure Portal](https://portal.azure.com/), på sidan för **BambooHR**-programintegrering, väljer du **Enkel inloggning**.

    ![Konfigurera enkel inloggning för länken](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Information om enkel inloggning på BambooHR-domäner och URL:er](common/sp-identifier.png)

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<company>.bamboohr.com`

    b. I textrutan **Identifierare (entitets-ID)** skriver du en URL med följande mönster: `BambooHR-SAML`

    > [!NOTE]
    > Värdet för **inloggnings-URL** är inte verkligt. Uppdatera värdet med faktiskt inloggnings-URL. Kontakta [BambooHR-klientens supportteam](https://www.bamboohr.com/contact.php) för att få värdet. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

4. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för hämtning av certifikat](common/certificatebase64.png)

6. I avsnittet **Konfigurera BambooHR** kopierar du lämpliga URL:er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggningswebbadress

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-bamboohr-single-sign-on"></a>Konfigurera enkel inloggning på BambooHR

1. I ett nytt webbläsarfönster loggar du in på din BambooHR-företagsplats som administratör.

2. Gör följande på startsidan:
   
    ![Sidan för enkel inloggning på BambooHR](./media/bamboo-hr-tutorial/ic796691.png "Enkel inloggning")   

    a. Välj **Appar**.
   
    b. I fönstret **Appar** väljer du **Enkel inloggning**.
   
    c. Välj **Enkel inloggning med SAML**.

3. I fönstret **Enkel inloggning med SAML** gör du följande:
   
    ![Fönstret Enkel inloggning med SAML](./media/bamboo-hr-tutorial/IC796692.png "Enkel inloggning med SAML")
   
    a. I rutan för **inloggnings-Url för enkel inloggning** klistrar du in den **inloggnings-URL** som du kopierade från Azure-portalen i steg 6.
      
    b. I Anteckningar öppnar du det base64-kodade certifikat som du laddade ned från Azure-portalen, kopierar innehållet och klistrar sedan in det i rutan för **X.509-certifikatet**.
   
    c. Välj **Spara**.

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

I det här avsnittet gör du det möjligt för Britta Simon att använda enkel inloggning med Azure genom att ge åtkomst till BambooHR.

1. I Azure-portalen väljer du **Företagsprogram**, **Alla program** och sedan **BambooHR**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan över program skriver och väljer du **BambooHR**.

    ![BambooHR-länken i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-bamboohr-test-user"></a>Skapa BambooHR-testanvändare

Om du vill göra det möjligt för Azure AD-användare att logga in på BambooHR, konfigurerar du dem manuellt i BambooHR genom att göra följande:

1. Logga in på din **BambooHR**-webbplats som administratör.

2. Välj **Inställningar** i verktygsfältet högst upp.
   
    ![Knappen Inställningar](./media/bamboo-hr-tutorial/IC796694.png "Inställning")

3. Välj **Översikt**.

4. Välj **Säkerhet** > **Användare** i det vänstra fönstret.

5. Skriv användarnamn, lösenord och e-postadress för det giltiga Azure AD-konto som du vill konfigurera.

6. Välj **Spara**.
        
>[!NOTE]
>Om du vill konfigurera Azure AD-användarkonton kan du också använda verktyg för att skapa BambooHR-användarkonton eller API:er.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på BambooHR-panelen i åtkomstpanelen bör du automatiskt loggas in på BambooHR som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

