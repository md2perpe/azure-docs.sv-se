---
title: 'Självstudier: Azure Active Directory-integrering med Shmoop för skolor | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Shmoop för skolor.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1d75560a-55b3-42e9-bda1-92b01c572d8e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: 0cf318161bff8950280fc5a8b007e08ef683ad18
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67090775"
---
# <a name="tutorial-azure-active-directory-integration-with-shmoop-for-schools"></a>Självstudier: Azure Active Directory-integrering med Shmoop för skolor

I den här självstudien får du lära dig hur du integrerar Shmoop för skolor med Azure Active Directory (AD Azure).
Integrera Shmoop för skolor med Azure AD ger dig följande fördelar:

* Du kan styra i Azure AD som har åtkomst till Shmoop för skolor.
* Du kan aktivera användarna att vara automatiskt inloggad till Shmoop för skolor (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure portal.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill konfigurera Azure AD-integrering med Shmoop för skolor, behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få en [kostnadsfritt konto](https://azure.microsoft.com/free/)
* Shmoop för skolor enkel inloggning aktiverad prenumeration
* Med hjälp av din produktionsmiljö endast om det behövs.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Har stöd för Shmoop för skolor **SP** -initierad SSO
* Har stöd för Shmoop för skolor **Just In Time** etableringen av användare

## <a name="adding-shmoop-for-schools-from-the-gallery"></a>Att lägga till Shmoop för skolor från galleriet

För att konfigurera integrering av Shmoop för skolor i Azure AD, som du behöver lägga till Shmoop för skolor från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Shmoop för skolor från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)** , klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver **Shmoop för skolor**väljer **Shmoop för skolor** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![Shmoop för skolor i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Shmoop för skolor, baserat på en testanvändare kallas **Britta Simon**.
För enkel inloggning ska fungera, måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Shmoop för skolor upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med Shmoop för skolor, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Konfigurera Shmoop för skolor enkel inloggning](#configure-shmoop-for-schools-single-sign-on)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Skapa Shmoop för skolor testanvändare](#create-shmoop-for-schools-test-user)**  – du har en motsvarighet för Britta Simon i Shmoop för skolor som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Om du vill konfigurera Azure AD enkel inloggning med Shmoop för skolor, utför du följande steg:

1. I den [Azure-portalen](https://portal.azure.com/)på den **Shmoop för skolor** application integration markerar **enkel inloggning**.

    ![Konfigurera enkel inloggning för länken](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Shmoop för skolor domän och URL: er med enkel inloggning för information](common/sp-identifier.png)

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://schools.shmoop.com/public-api/saml2/start/<uniqueid>`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://schools.shmoop.com/<uniqueid>`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med faktisk inloggnings-URL och identifierare. Kontakta [Shmoop för skolor klienten supportteamet](mailto:support@shmoop.com) att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. Shmoop för skolor programmet förväntar sig SAML-intyg i ett visst format. Konfigurera följande anspråk för det här programmet. Du kan hantera värdena för dessa attribut i avsnittet **Användarattribut** på sidan för programintegrering. Följande skärmbild visar hur du konfigurerar intyg:

    ![image](common/edit-attribute.png)

    > [!NOTE]
    > Shmoop för skolan stöder två roller för användare: **Lärare** och **Student**. Ställa in dessa roller i Azure AD så att användare kan tilldelas till rätt roller. Information om hur du konfigurerar roller i Azure AD finns i [hantera åtkomst med RBAC och Azure portal](../../role-based-access-control/role-assignments-portal.md).

6. I avsnittet **Användaranspråk** i dialogrutan **Användarattribut** så redigerar du anspråken genom att använda **Redigera-ikonen** eller lägga till anspråken genom att använda **Lägg till nytt anspråk** för att konfigurera SAML-tokenattribut som det visas i bilden ovan och utföra följande steg: 

    | Namn |  Källattribut|
    | --------- | --------------- |
    | role      | user.assignedroles |

    a. Klicka på **Lägg till nytt anspråk** för att öppna dialogrutan **Hantera användaranspråk**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. I textrutan **Namn** skriver du det attributnamn som visas för den raden.

    c. Lämna **Namnrymd** tom.

    d. Välj Källa som **Attribut**.

    e. Från listan över **Källattribut** skriver du det attributvärde som visas för den raden.

    f. Klicka på **Ok**

    g. Klicka på **Spara**.

7. Klicka på kopieringsknappen i avsnittet **SAML-signeringscertifikat** på sidan **Konfigurera enkel inloggning med SAML** om du vill kopiera **URL:en för federationsmetadata** och spara den på datorn.

    ![Länk för hämtning av certifikat](common/copy-metadataurl.png)

### <a name="configure-shmoop-for-schools-single-sign-on"></a>Konfigurera Shmoop för skolor enkel inloggning

Att konfigurera enkel inloggning på **Shmoop för skolor** sida, som du behöver skicka den **Appfederationsmetadata** till [Shmoop för skolor supportteam](mailto:support@shmoop.com). De ställer du in SAML SSO ansluta till korrekt inställda på båda sidorna.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I den **användarnamn** fälttyp `brittasimon@yourcompanydomain.extension`  
    Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Shmoop för skolor.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**och välj sedan **Shmoop för skolor**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **Shmoop för skolor**.

    ![Länken Shmoop för skolor i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-shmoop-for-schools-test-user"></a>Skapa Shmoop för skolor testanvändare

I det här avsnittet skapas en användare som kallas Britta Simon i Shmoop för skolor. Shmoop för skolor stöder etableringen av just-in-time-användare som är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i Shmoop för skolor, skapas en ny efter autentisering.

> [!NOTE]
> Om du vill skapa en användare manuellt kan du kontakta den [Shmoop för skolor supportteam](mailto:support@shmoop.com).

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Shmoop för skolor i åtkomstpanelen, bör det vara loggas in automatiskt till den Shmoop för skolor som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
