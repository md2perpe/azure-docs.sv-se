---
title: 'Självstudier: Azure Active Directory-integrering med Zoho | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Zoho.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 9874e1f3-ade5-42e7-a700-e08b3731236a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/26/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 69c2599a2ddd09cbaf869bf4d9e21a8032855cce
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67086173"
---
# <a name="tutorial-azure-active-directory-integration-with-zoho"></a>Självstudier: Azure Active Directory-katalogintegrering med Zoho

I den här självstudien lär du dig hur du integrerar Zoho med Azure Active Directory (Azure AD).
Du får följande fördelar när du integrerar Zoho med Azure AD:

* Du kan styra i Azure AD vilka som har åtkomst till Zoho.
* Du kan göra så att dina användare automatiskt loggas in på Zoho (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure portal.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

För att kunna konfigurera Azure AD-integrering med Zoho behöver du följande:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Zoho-prenumeration med enkel inloggning aktiverat

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Zoho har stöd för **SP**-initierad enkel inloggning

## <a name="adding-zoho-from-the-gallery"></a>Lägga till Zoho från galleriet

För att kunna konfigurera integreringen av Zoho i Azure AD måste du lägga till Zoho från galleriet i din lista över hanterade SaaS-appar.

**Gör följande för att lägga till Zoho från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)** , klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. Skriv **Zoho** i sökrutan, välj **Zoho** på resultatpanelen och klicka på knappen **Lägg till** i programmet.

     ![Zoho i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD med Zoho baserat på en testanvändare med namnet **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i Zoho upprättas.

För att kunna konfigurera och testa enkel inloggning i Azure AD med Zoho måste du slutföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Konfigurera enkel inloggning för Zoho](#configure-zoho-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Skapa Zoho-testanvändare](#create-zoho-test-user)** – så att det finns en motsvarighet till Britta Simon i Zoho som är länkad till Azure AD-representationen av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Gör följande för att konfigurera enkel inloggning med Azure AD med Zoho:

1. I [Azure-portalen](https://portal.azure.com/) går du till sidan för programintegrering för **Zoho** och väljer **Enkel inloggning**.

    ![Konfigurera enkel inloggning för länken](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Zoho – enkel inloggning-information för domän och -URL:er](common/sp-signonurl.png)

    I textrutan **Inloggnings-URL** skriver du en URL med följande mönster: `https://<company name>.zohomail.com`

    > [!NOTE]
    > Värdet är inte verkligt. Uppdatera värdet med den faktiska inloggnings-URL:en. Kontakta [Zoho-supporten](https://www.zoho.com/mail/contact.html) för att få värdet. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

4. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för hämtning av certifikat](common/certificatebase64.png)

6. I avsnittet **Konfigurera Zoho** kopierar du lämpliga URL:er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggningswebbadress

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-zoho-single-sign-on"></a>Konfigurera enkel inloggning med Zoho

1. Logga in på din Zoho Mail-företagswebbplats som administratör.

2. Gå till **Kontrollpanelen**.
   
    ![Kontrollpanelen](./media/zoho-mail-tutorial/ic789607.png "Kontrollpanelen")

3. Klicka på fliken **SAML-autentisering**.
   
    ![SAML-autentisering](./media/zoho-mail-tutorial/ic789608.png "SAML-autentisering")

4. Gör följande i avsnittet **SAML-autentiseringsinformation**:
   
    ![SAML-autentiseringsinformation](./media/zoho-mail-tutorial/ic789609.png "SAML-autentiseringsinformation")
   
    a. I textrutan för **inloggnings-URL** klistrar du in **inloggnings-URL:en** som du har kopierat från Azure-portalen.
   
    b. I textrutan **Logout URL** (Utloggnings-URL) klistrar du in **inloggnings-URL:en** som du har kopierat från Azure-portalen.
   
    c. I textrutan **Change Password URL** (URL för Byt lösenord) klistrar du in **URL:en för Byt lösenord** som du har kopierat från Azure-portalen.
       
    d. Öppna ditt base-64-kodade certifikat som du har laddat ned från Azure-portalen i Anteckningar, kopierar innehållet i Urklipp och klistra sedan in den i textrutan **PublicKey**.
   
    e. Som **Algoritm** väljer du **RSA**.
   
    f. Klicka på **OK**.

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

I det här avsnittet gör du det möjligt för Britta Simon att använda enkel inloggning med Azure genom att bevilja åtkomst till Zoho.

1. I Azure-portalen väljer du **Företagsprogram**, **Alla program** och sedan **Zoho**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan över program skriver och väljer du **Zoho**.

    ![Zoho-länken i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-zoho-test-user"></a>Skapa Zoho-testanvändare

För att kunna göra det möjligt för Azure AD-användare att logga in på Zoho Mail måste de etableras i Zoho Mail. När det gäller Zoho Mail är etablering en manuell uppgift.

> [!NOTE]
> Du kan använda andra verktyg eller API:er för att skapa Zoho Mail-användarkonton som tillhandahålls av Zoho Mail för att etablera AAD-användarkonton.

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Gör följande för att etablera ett användarkonto:

1. Logga in på din **Zoho Mail**-företagswebbplats som administratör.

1. Gå till **Kontrollpanelen \> E-post och dokument**.

1. Gå till **Användarinformation \> Lägg till användare**.
   
    ![Lägg till användare](./media/zoho-mail-tutorial/ic789611.png "Lägg till användare")

1. Gör följande i dialogrutan **Lägg till användare**:
   
    ![Lägg till användare](./media/zoho-mail-tutorial/ic789612.png "Lägg till användare")
   
    a. I textrutan **Förnamn** skriver du förnamnet på användaren: **Britta**.

    b. I textrutan **Efternamn** skriver du efternamnet: **Simon**.

    c. I den **e-post-ID** textrutan typ e-post-id för användaren som **brittasimon\@contoso.com**.

    d. I textrutan **Lösenord** anger du användarens lösenord.
   
    e. Klicka på **OK**.  
      
    > [!NOTE]
    > Azure Active Directory-kontoinnehavaren får ett e-postmeddelande med en länk för att bekräfta kontot innan det blir aktivt.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på Zoho-panelen i åtkomstpanelen bör du automatiskt loggas in på Zoho som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

