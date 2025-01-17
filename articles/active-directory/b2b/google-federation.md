---
title: Lägga till Google som en identitetsprovider för B2B - Azure Active Directory | Microsoft Docs
description: Federera med Google om du vill aktivera gästanvändare rätt att logga in på din Azure AD-appar med sina egna Gmail-konto
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 735c3db14963c1f3cfe700a97dee9fedb70e29f5
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67441113"
---
# <a name="add-google-as-an-identity-provider-for-b2b-guest-users-preview"></a>Lägga till Google som en identitetsprovider för B2B-gästanvändare (förhandsgranskning)

|     |
| --- |
| Google federation är en funktion i offentliga förhandsversionen av Azure Active Directory. Mer information om förhandsversioner finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

Genom att konfigurera federation med Google, kan du tillåta inbjudna användare att logga in på din delade appar och resurser med sina egna Google-konton utan att behöva skapa Microsoft-Accounts (MSA: er) eller Azure AD-konton.  
> [!NOTE]
> Din Google-gästanvändare måste logga in med en länk som innehåller klient-kontext (till exempel `https://myapps.microsoft.com/?tenantid=<tenant id>` eller `https://portal.azure.com/<tenant id>`, eller när det gäller en verifierad domän `https://myapps.microsoft.com/<verified domain>.onmicrosoft.com`). Direktlänkar till program och resurser fungerar också så länge som de innehåller klient-kontext. Gästanvändare kan för närvarande inte att logga in med slutpunkter som har ingen klient-kontext. Till exempel `https://myapps.microsoft.com`, `https://portal.azure.com`, eller team vanliga slutpunkten kommer att orsaka fel.
 
## <a name="what-is-the-experience-for-the-google-user"></a>Vad är upplevelsen för Google-användare?
När du skickar en inbjudan till en Google Gmail-användare bör gästanvändaren åtkomst till dina delade appar eller resurser med hjälp av en länk som innehåller klient-kontext. Upplevelse för dem som varierar beroende på om de har redan loggat in på Google:
  - Om gästanvändaren inte har loggat in på Google, uppmanas de att logga in på Google.
  - Om gästanvändaren har redan loggat in till Google, uppmanas de att välja det konto som de vill använda. De måste välja det konto du använde för att bjuda in dem.

Om gästanvändaren ser felet ”huvud för lång”, de kan försöka ta bort deras cookies eller de kan öppna ett privat eller incognito-fönster och försök logga in igen.

![Skärmbild som visar Google-inloggningssidan](media/google-federation/google-sign-in.png)

## <a name="step-1-configure-a-google-developer-project"></a>Steg 1: Konfigurera ett Google developer-projekt
Skapa först ett nytt projekt i konsolen för Google-utvecklare att skaffa en klient-ID och en klienthemlighet som du kan senare lägga till Azure AD. 
1. Gå till Google APIs på https://console.developers.google.com, och logga in med ditt Google-konto. Vi rekommenderar att du använder en delad grupp Google-konto.
2. Skapa ett nytt projekt: På instrumentpanelen, väljer **skapa projekt**, och välj sedan **skapa**. På sidan nytt projekt kan du ange en **projektnamn**, och välj sedan **skapa**.
   
   ![Skärmbild som visar sidan för ett nytt projekt för Google](media/google-federation/google-new-project.png)

3. Kontrollera att det nya projektet är markerad i menyn projekt. Öppna menyn längst upp till vänster och välj sedan **API: er och tjänster** > **autentiseringsuppgifter**.

   ![Skärmbild som visar Google-API: et autentiseringsuppgifter alternativ](media/google-federation/google-api.png)
 
4. Välj den **OAuth godkännandeskärmen** fliken och ange en **programnamn**. (Lämna de andra inställningarna.)

   ![Skärmbild som visar Google OAuth-medgivande skärm](media/google-federation/google-oauth-consent-screen.png)

5. Bläddra till den **auktoriserade domäner** och ange microsoftonline.com.

   ![Skärmbild som visar avsnittet auktoriserade domäner](media/google-federation/google-oauth-authorized-domains.png)

6. Välj **Spara**.

7. Välj den **autentiseringsuppgifter** fliken. I den **skapa autentiseringsuppgifter** menyn, Välj **OAuth klient-ID**.

   ![Skärmbild som visar Google APIs skapa inloggningsinformation](media/google-federation/google-api-credentials.png)

8. Under **programtyp**, Välj **webbprogram**, och sedan under **auktoriserade omdirigerings-URI: er**, anger du följande URI: er:
   - `https://login.microsoftonline.com` 
   - `https://login.microsoftonline.com/te/<directory id>/oauth2/authresp` <br>(där `<directory id>` är din katalog-ID)
   
     > [!NOTE]
     > Du hittar din katalog-ID genom att gå till https://portal.azure.com, och under **Azure Active Directory**, Välj **egenskaper** och kopiera den **katalog-ID**.

   ![Skärmbild som visar auktoriserade omdirigerings-URI: er avsnittet](media/google-federation/google-create-oauth-client-id.png)

9. Välj **Skapa**. Kopiera klient-ID och klienthemlighet som du ska använda när du lägger till identitetsleverantören i Azure AD-portalen.

   ![Skärmbild som visar OAuth-klient-ID och klienthemlighet](media/google-federation/google-auth-client-id-secret.png)

## <a name="step-2-configure-google-federation-in-azure-ad"></a>Steg 2: Konfigurera Google federation i Azure AD 
Nu ställer du Google-klient-ID och klienthemlighet, antingen genom att skriva in den i Azure AD-portalen eller med hjälp av PowerShell. Var noga med att testa federationkonfigurationen Google genom att bjuda in dig själv med hjälp av en Gmail-adress och försök att lösa in inbjudan med inbjudna Google-konto. 

#### <a name="to-configure-google-federation-in-the-azure-ad-portal"></a>Konfigurera Google federation i Azure AD-portalen 
1. Gå till [Azure-portalen](https://portal.azure.com). Välj **Azure Active Directory** i den vänstra rutan. 
2. Välj **organisationens relationer**.
3. Välj **identitetsprovidrar**, och klicka sedan på den **Google** knappen.
4. Ange ett namn. Ange klient-ID och klienthemlighet som du fick tidigare. Välj **Spara**. 

   ![Skärmbild som visar sidan Lägg till Google-providern](media/google-federation/google-identity-provider.png)

#### <a name="to-configure-google-federation-by-using-powershell"></a>Att konfigurera Google federation med hjälp av PowerShell
1. Installera den senaste versionen av Azure AD PowerShell för Graph-modul ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)).
2. Kör följande kommando: `Connect-AzureAD`.
3. Logga in med det hanterade kontot för Global administratör i Kommandotolken logga in.  
4. Kör följande kommando: 
   
   `New-AzureADMSIdentityProvider -Type Google -Name Google -ClientId [Client ID] -ClientSecret [Client secret]`
 
   > [!NOTE]
   > Använda klient-id och klienthemligheten från den app som du skapade i ”steg 1: Konfigurera ett Google developer-projekt ”. Mer information finns i den [New AzureADMSIdentityProvider](https://docs.microsoft.com/powershell/module/azuread/new-azureadmsidentityprovider?view=azureadps-2.0-preview) artikeln. 
 
## <a name="how-do-i-remove-google-federation"></a>Hur tar jag bort Google federation?
Du kan ta bort din Google federation-konfiguration. Om du gör det Google-gästanvändare som har redan utnyttjat sin inbjudan kommer inte att kunna logga in, men du kan ge dem åtkomst till dina resurser igen genom att ta bort dem från katalogen och bjuda in dem igen. 
 
### <a name="to-delete-google-federation-in-the-azure-ad-portal"></a>Ta bort Google federation i Azure AD-portalen: 
1. Gå till [Azure-portalen](https://portal.azure.com). Välj **Azure Active Directory** i den vänstra rutan. 
2. Välj **organisationens relationer**.
3. Välj **identitetsprovidrar**.
4. På den **Google** rad, väljer du snabbmenyn ( **...** ) och välj sedan **ta bort**. 
   
   ![Skärmbild som visar alternativet Ta bort för den sociala identitetsprovidern](media/google-federation/google-social-identity-providers.png)

1. Välj **Ja** att bekräfta borttagningen. 

### <a name="to-delete-google-federation-by-using-powershell"></a>Ta bort Google federation med hjälp av PowerShell: 
1. Installera den senaste versionen av Azure AD PowerShell för Graph-modul ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)).
2. Kör `Connect-AzureAD`.  
4. Logga in med det hantera globala administratörskontot i inloggningen i Kommandotolken.  
5. Ange följande kommando:

    `Remove-AzureADMSIdentityProvider -Id Google-OAUTH`

   > [!NOTE]
   > Mer information finns i [Remove-AzureADMSIdentityProvider](https://docs.microsoft.com/powershell/module/azuread/Remove-AzureADMSIdentityProvider?view=azureadps-2.0-preview). 
