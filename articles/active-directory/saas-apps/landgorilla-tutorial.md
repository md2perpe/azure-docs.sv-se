---
title: 'Självstudier: Azure Active Directory-integrering med mark Gorilla klienten | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och mark Gorilla.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/13/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d12519a2bd0a5e3951d0ca9ae51a54bd728a782b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65987901"
---
# <a name="tutorial-azure-active-directory-integration-with-land-gorilla-client"></a>Självstudier: Azure Active Directory-integrering med mark Gorilla klienten

I den här självstudien får du lära dig hur du integrerar mark Gorilla klienten med Azure Active Directory (AD Azure).

Integrera mark Gorilla klienten med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till mark Gorilla klienten
- Du kan aktivera användarna att automatiskt få loggat in på mark Gorilla klienten (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats - Azure-hanteringsportalen

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).


## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill konfigurera Azure AD-integrering med mark Gorilla klienten behöver du följande objekt:

- En Azure AD-prenumeration
- En mark Gorilla klienten enkel inloggning aktiverad prenumeration


> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.


Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Du bör inte använda din produktionsmiljö såvida inte detta är nödvändigt.
- Om du inte har en Azure AD-utvärderingsmiljö kan du skaffa en månads utvärderingsperiod [här](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till mark Gorilla klienten från galleriet
1. Konfigurera och testa Azure AD enkel inloggning


## <a name="adding-land-gorilla-client-from-the-gallery"></a>Att lägga till mark Gorilla klienten från galleriet
Om du vill konfigurera integreringen av mark Gorilla klient till Azure AD, som du behöver lägga till mark Gorilla klienten från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till mark Gorilla klienten från galleriet:**

1. I den  **[Azure-hanteringsportalen](https://portal.azure.com)** , klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
1. Klicka på **Lägg till** knappen överst i dialogrutan.

    ![Program][3]

1. I sökrutan skriver **mark Gorilla klienten**.

    ![Skapa en Azure AD-användare för testning](./media/landgorilla-tutorial/tutorial_landgorilla_search.png)

1. I resultatpanelen väljer **mark Gorilla klienten**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en Azure AD-användare för testning](./media/landgorilla-tutorial/tutorial_landgorilla_addfromgallery.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med mark Gorilla klient med en testanvändare som kallas ”B. Simon ”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i mark Gorilla klienten är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i mark Gorilla klienten upprättas.

Den här länken relationen upprättas genom att tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** i mark Gorilla-klienten.

Om du vill konfigurera och testa Azure AD enkel inloggning med mark Gorilla klienten, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med begränsad grupp.
1. **[Skapa en testanvändare mark Gorilla](#creating-a-land-gorilla-test-user)**  – om du vill testa Azure AD enkel inloggning med B. Simon.
1. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera B. Simon att använda Azure AD enkel inloggning.
1. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet ska du aktivera Azure AD enkel inloggning i Azure-hanteringsportalen och konfigurera enkel inloggning i ditt Land Gorilla klientprogram.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med mark Gorilla klienten:**

1. I hanteringsportalen för Azure på den **mark Gorilla klienten** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan som **läge** Välj **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/landgorilla-tutorial/tutorial_landgorilla_samlbase.png)

1. På den **mark Gorilla klienten domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/landgorilla-tutorial/tutorial_landgorilla_url_02.png)

    a. I den **identifierare** textrutan skriver värde med någon av följande mönster: 
    
    `https://<customer domain>.landgorilla.com/` 
    
    `https://www.<customer domain>.landgorilla.com`

    b. I den **svars-URL** textrutan anger du ett URL-Adressen med något av följande mönster:

    `https://<customer domain>.landgorilla.com/simplesaml/module.php/core/authenticate.php`

    `https://www.<customer domain>.landgorilla.com/simplesaml/module.php/core/authenticate.php`

    `https://<customer domain>.landgorilla.com/simplesaml/module.php/saml/sp/saml2-acs.php/default-sp`
    
    `https://www.<customer domain>.landgorilla.com/simplesaml/module.php/saml/sp/saml2-acs.php/default-sp`

    > [!NOTE] 
    > Observera att detta inte är de verkliga värdena. Du måste uppdatera dessa värden med de faktiska identifierare och svars-URL. Här föreslår vi att du använder det unika värdet för strängen i identifieraren. Kontakta [mark Gorilla klienten team](https://www.landgorilla.com/support/) att hämta dessa värden. 

1. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara XML-filen på datorn.

    ![Konfigurera enkel inloggning](./media/landgorilla-tutorial/tutorial_landgorilla_certificate.png) 

1. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/landgorilla-tutorial/tutorial_general_400.png) 

1. Om du vill hämta SSO-konfiguration slutförd för ditt program mark Gorilla slutet, kontakta [mark Gorilla klienten supportteamet](https://www.landgorilla.com/support/) och ge dem med de hämtade **”XML-Metadata för** fil.


### <a name="creating-an-azure-ad-test-user"></a>Skapa en Azure AD-användare för testning
Målet med det här avsnittet är att skapa en testanvändare i Azure Management portal kallas B. Simon.

![Skapa en Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-hanteringsportalen**, i det vänstra navigeringsfönstret klickar du på **Azure Active Directory** ikon.

    ![Skapa en Azure AD-användare för testning](./media/landgorilla-tutorial/create_aaduser_01.png) 

1. Gå till **användare och grupper** och klicka på **alla användare** att visa en lista över användare.
    
    ![Skapa en Azure AD-användare för testning](./media/landgorilla-tutorial/create_aaduser_02.png) 

1. Överst i dialogrutan klickar du på **Lägg till** att öppna den **användaren** dialogrutan.
 
    ![Skapa en Azure AD-användare för testning](./media/landgorilla-tutorial/create_aaduser_03.png) 

1. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en Azure AD-användare för testning](./media/landgorilla-tutorial/create_aaduser_04.png) 

    a. I den **namn** textrutan typ **B. Simon**.

    b. I den **användarnamn** textrutan skriver den **e-postadress** för B. Simon.

    c. Välj **visa lösenord** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**. 

### <a name="creating-a-land-gorilla-test-user"></a>Skapa en testanvändare mark Gorilla

Kontakta [mark Gorilla supportteamet](https://www.landgorilla.com/support/) att lägga till användare i mark Gorilla-plattformen.
    
### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du B. Simon att använda Azure enkel inloggning genom att ge dem åtkomst till mark Gorilla klient.

![Tilldela användare][200] 

**Om du vill tilldela mark Gorilla klient B. Simon utför du följande steg:**

1. Öppna vyn program i Azure-hanteringsportalen och sedan gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **mark Gorilla klienten**.

    ![Konfigurera enkel inloggning](./media/landgorilla-tutorial/tutorial_landgorilla_app.png) 

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202] 

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

1. På **användare och grupper** dialogrutan **B. Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    


### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen mark Gorilla klienten i åtkomstpanelen du bör få automatiskt loggat in på ditt Land Gorilla klientprogram.


## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/landgorilla-tutorial/tutorial_general_01.png
[2]: ./media/landgorilla-tutorial/tutorial_general_02.png
[3]: ./media/landgorilla-tutorial/tutorial_general_03.png
[4]: ./media/landgorilla-tutorial/tutorial_general_04.png

[100]: ./media/landgorilla-tutorial/tutorial_general_100.png
[200]: ./media/landgorilla-tutorial/tutorial_general_200.png
[201]: ./media/landgorilla-tutorial/tutorial_general_201.png
[202]: ./media/landgorilla-tutorial/tutorial_general_202.png
[203]: ./media/landgorilla-tutorial/tutorial_general_203.png
