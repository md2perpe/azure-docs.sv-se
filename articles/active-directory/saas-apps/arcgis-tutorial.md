---
title: 'Självstudier: Azure Active Directory-integrering med ArcGIS Online | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och ArcGIS Online.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: a9e132a4-29e7-48bf-beb9-4148e617c8a9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/19/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: db56cd7551ef8179aeff575fdd1f2578cbee74ee
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67106699"
---
# <a name="tutorial-azure-active-directory-integration-with-arcgis-online"></a>Självstudier: Azure Active Directory-katalogintegrering med ArcGIS Online

I den här självstudien får du lära dig hur du integrerar ArcGIS Online med Azure Active Directory (Azure AD).
Genom att integrera ArcGIS Online med Azure AD får du följande fördelar:

* Du kan i Azure AD styra vem som har åtkomst till ArcGIS Online.
* Du kan göra så att dina användare automatiskt loggas in på ArcGIS Online (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure portal.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

För att konfigurera Azure AD-integrering med ArcGIS Online behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* ArcGIS Online-prenumeration med enkel inloggning aktiverat

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* ArcGIS Online har stöd för **SP**-initierad enkel inloggning

## <a name="adding-arcgis-online-from-the-gallery"></a>Lägga till ArcGIS Online från galleriet

För att konfigurera integreringen av ArcGIS Online till Azure AD behöver du lägga till ArcGIS Online från galleriet i din lista över hanterade SaaS-appar.

**Om du vill lägga till ArcGIS Online från galleriet ska du utföra följande steg:**

1. I den **[Azure-portalen](https://portal.azure.com)** , klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **ArcGIS Online**, väljer **ArcGIS Online** från resultatpanelen och klickar på knappen **Lägg till** för att lägga till programmet.

     ![ArcGIS Online i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD med ArcGIS Online baserat på en testanvändare som heter **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i ArcGIS Online upprättas.

För att konfigurera och testa enkel inloggning med Azure AD med ArcGIS Online måste du utföra följande byggstenar:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Konfigurera enkel inloggning för ArcGIS Online](#configure-arcgis-online-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Skapa ArcGIS Online-testanvändare](#create-arcgis-online-test-user)** – för att ha en motsvarighet för Britta Simon i ArcGIS Online som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Konfigurera enkel inloggning med Azure AD med ArcGIS Online genom att utföra följande steg:

1. I [Azure Portal](https://portal.azure.com/) går du till sidan för **ArcGIS Online**-programintegrering och väljer **Enkel inloggning**.

    ![Konfigurera enkel inloggning för länken](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Information om enkel inloggning med ArcGIS Online-domän och URL:er](common/sp-identifier.png)

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<companyname>.maps.arcgis.com`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `<companyname>.maps.arcgis.com`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. Kontakta [ArcGIS Onlines kundsupportteam](https://support.esri.com/en/) för att få dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för hämtning av certifikat](common/metadataxml.png)

6. Om du vill automatisera konfigurationen i **ArcGIS Online**, måste du installera **webbläsartillägget Enkel inloggning för mina appar** genom att klicka på **Installera tillägget**.

    ![image](./media/arcgis-tutorial/install_extension.png)

7. När du har lagt till tillägg i webbläsaren klickar du på **Konfigurera ArcGIS Online**. Du omdirigeras då till ArcGIS Online-programmet. Därifrån anger du administratörsautentiseringsuppgifterna för att logga in på ArcGIS Online. Webbläsartillägget konfigurerar automatiskt programmet åt dig och automatiserar stegen i avsnitt **Konfigurera enkel inloggning med ArcGIS Online**.

### <a name="configure-arcgis-online-single-sign-on"></a>Konfigurera enkel inloggning med ArcGIS Online

1. Om du vill konfigurera ArcGIS Online manuellt öppnar du ett nytt webbläsarfönster och loggar in på din ArcGIS Online-företagsplats som administratör och utför följande steg:

2. Klicka på **Redigera inställningar**.

    ![Redigera inställningar](./media/arcgis-tutorial/ic784742.png "Redigera inställningar")

3. Klicka på **Säkerhet**.

    ![Säkerhet](./media/arcgis-tutorial/ic784743.png "Säkerhet")

4. Under **Företagsinloggningar**, klickar du på **SET IDENTITY PROVIDER**.

    ![Företagsinloggningar](./media/arcgis-tutorial/ic784744.png "Företagsinloggningar")

5. Utför följande steg på konfigurationssidan **Ange identitetsprovider**:

    ![Ange identitetsprovider](./media/arcgis-tutorial/ic784745.png "Ange identitetsprovider")

    a. I textrutan **Namn** skriver du namnet på din organisation.

    b. För **Metadata för företagsidentitetsprovider kommer att tillhandahållas med** väljer du **En fil**.

    c. För att ladda upp den nedladdade metadatafilen klickar du på **Välj fil**.

    d. Klicka på **SET IDENTITY PROVIDER**.

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

I det här avsnittet ser du till att Britta Simon ska kunna använda enkel inloggning i Azure genom att bevilja åtkomst till ArcGIS Online.

1. I Azure Portal väljer du **Företagsprogram**, **Alla program** och sedan **ArcGIS Online**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I programlistan anger du och väljer **ArcGIS Online**.

    ![ArcGIS Online-länken i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-arcgis-online-test-user"></a>Skapa ArcGIS Online-testanvändare

För att göra det möjligt för Azure AD-användare att logga in på ArcGIS Online måste de etableras i ArcGIS Online.  
När det gäller ArcGIS Online är etablering en manuell aktivitet.

**Utför följande steg för att etablera ett användarkonto:**

1. Logga in på din **ArcGIS**-klient.

2. Klicka på **BJUD IN MEDLEMMAR**.
   
    ![Bjud in medlemmar](./media/arcgis-tutorial/ic784747.png "Bjud in medlemmar")

3. Välj **Lägg till medlemmar automatiskt utan att skicka ett e-postmeddelande** och klicka sedan på **NÄSTA**.
   
    ![Lägg till medlemmar automatiskt](./media/arcgis-tutorial/ic784748.png "Lägg till medlemmar automatiskt")

4. I dialogrutan **Medlemmar** utför du följande steg:
   
     ![Lägg till och granska](./media/arcgis-tutorial/ic784749.png "Lägg till och granska")
    
     a. Ange **E-post**, **Förnamn** och **Efternamn** för ett giltigt AAD-konto som du vill etablera.
  
     b. Klicka på **LÄGG TILL OCH GRANSKA**.
5. Granska de data du har angett och klicka sedan på **LÄGG TILL MEDLEMMAR**.
   
    ![Lägg till medlem](./media/arcgis-tutorial/ic784750.png "Lägg till medlem")
        
    > [!NOTE]
    > Azure Active Directory-kontoinnehavaren får ett e-postmeddelande och följer en länk för att bekräfta kontot innan det blir aktivt.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på ArcGIS Online-panelen i åtkomstpanelen bör du automatiskt loggas in på ArcGIS Online som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

