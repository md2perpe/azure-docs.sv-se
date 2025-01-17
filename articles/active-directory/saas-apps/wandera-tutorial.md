---
title: 'Självstudier: Azure Active Directory-integrering med Wandera | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Wandera.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a4615e56-1deb-423e-ad19-2e74c0d6d17a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/04/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6178ad2eba4c90fc7e37a160b6a537b9d6db1410
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2019
ms.locfileid: "67712057"
---
# <a name="tutorial-integrate-wandera-with-azure-active-directory"></a>Självstudier: Integrera Wandera med Azure Active Directory

I de här självstudierna lär du dig att integrera Wandera med Azure Active Directory (AD Azure). När du integrerar Wandera med Azure AD, kan du:

* Styr i Azure AD som har åtkomst till Wandera.
* Ge dina användare att automatiskt inloggad till Wandera med sina Azure AD-konton.
* Hantera konton på en central plats – Azure portal.

Läs mer om integrering av SaaS-app med Azure AD i [vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få en månads kostnadsfri utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/).
* Aktiverat prenumeration Wandera enkel inloggning (SSO).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien, konfigurera och testa Azure AD enkel inloggning i en testmiljö.

* Har stöd för Wandera **IDP** -initierad SSO

## <a name="adding-wandera-from-the-gallery"></a>Att lägga till Wandera från galleriet

För att konfigurera integrering av Wandera i Azure AD, som du behöver lägga till Wandera från galleriet i din lista över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. I det vänstra navigeringsfönstret, väljer den **Azure Active Directory** service.
1. Gå till **företagsprogram** och välj sedan **alla program**.
1. Om du vill lägga till nytt program, Välj **nytt program**.
1. I den **Lägg till från galleriet** Skriv **Wandera** i sökrutan.
1. Välj **Wandera** från resultaten panelen och lägger sedan till appen. Vänta några sekunder medan appen läggs till i din klient.


## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

Konfigurera och testa Azure AD enkel inloggning med Wandera med en testanvändare kallas **B.Simon**. Du måste upprätta en länk förhållandet mellan en Azure AD-användare och den relaterade användaren i Wandera för SSO ska fungera.

Om du vill konfigurera och testa Azure AD enkel inloggning med Wandera, utför du följande byggblock:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)**  – om du vill ge användarna använda den här funktionen.
2. **[Konfigurera Wandera SSO](#configure-wandera-sso)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Skapa testanvändare Wandera](#create-wandera-test-user)**  – du har en motsvarighet för Britta Simon i Wandera som är länkad till en Azure AD-representation av användaren.
6. **[Testa SSO](#test-sso)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg om du vill aktivera enkel inloggning för Azure AD i Azure-portalen.

1. I den [Azure-portalen](https://portal.azure.com/)på den **Wandera** programsidan integration, hitta den **hantera** och väljer **enkel inloggning**.
1. På den **väljer du en metod för enkel inloggning** väljer **SAML**.
1. På den **ange in enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **SAML grundkonfiguration** att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. På den **SAML grundkonfiguration** anger värdena för följande fält:

    I textrutan **Svars-URL** skriver du en URL med följande mönster: `https://radar.wandera.com/saml/acs/<tenant id>`

    > [!NOTE]
    > Värdet är inte verkligt. Uppdatera värdet för med den faktiska svars-URL:en. Kontakta [Wandera klienten supportteamet](https://www.wandera.com/about-wandera/contact/#supportsection) att hämta värdet. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. På den **ange in enkel inloggning med SAML** sidan den **SAML-signeringscertifikat** avsnittet, hitta **XML-Metadata för** och välj **hämta** att hämta certifikatet och spara den på din dator.

    ![Länk för hämtning av certifikat](common/metadataxml.png)

1. På den **ange in enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **SAML-signeringscertifikat** att redigera inställningarna.

    ![Signeringsalternativ](common/signing-option.png)

    1. Välj **signering alternativet** som **inloggning SAML-svar och försäkran**.

    1. Välj **Signeringsalgoritm** som **SHA-256**.

1. På den **konfigurera Wandera** avsnittet, kopiera den lämpliga URL: er efter behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="configure-wandera-sso"></a>Configure Wandera SSO

Att konfigurera enkel inloggning på **Wandera** sida, som du behöver skicka de hämtade **XML-Metadata för** och lämpliga kopierade URL: er från Azure portal för att [Wandera supportteamet](https://www.wandera.com/about-wandera/contact/#supportsection). De ställer du in SAML SSO ansluta till korrekt inställda på båda sidorna.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet skapar du en användare i Azure-portalen kallas B.Simon.

1. På menyn till vänster i Azure-portalen väljer du **Azure Active Directory**väljer **användare**, och välj sedan **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I den **användaren** egenskaper, Följ dessa steg:
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. I den **användarnamn** fältet, anger du den username@companydomain.extension. Till exempel `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska du aktivera B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Wandera.

1. I Azure-portalen väljer du **företagsprogram**, och välj sedan **alla program**.
1. I listan med program väljer **Wandera**.
1. Appens översiktssidan, hitta den **hantera** och väljer **användare och grupper**.

   ![Länken ”användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i den **Lägg till tilldelning** dialogrutan.

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I den **användare och grupper** dialogrutan **B.Simon** från listan över användare klickar på **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-försäkran i den **Välj roll** dialogrutan Välj rätt roll för användaren i listan och klicka sedan på den **Välj** längst ned på skärmen.
1. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-wandera-test-user"></a>Skapa Wandera testanvändare

I det här avsnittet skapar du en användare som kallas Britta Simon i Wandera. Arbeta med [Wandera supportteamet](https://www.wandera.com/about-wandera/contact/#supportsection) att lägga till användare i Wandera-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="test-sso"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Wandera i åtkomstpanelen, bör det vara loggas in automatiskt till Wandera som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

