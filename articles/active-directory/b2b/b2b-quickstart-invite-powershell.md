---
title: 'Snabbstart: Lägga till en gästanvändare med PowerShell – Azure Active Directory | Microsoft Docs'
description: I den här snabbstarten får du lära dig hur du använder PowerShell för att skicka en inbjudan till en extern Azure AD B2B-samarbetsanvändare.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5325529acb66735db0c27c0f5bdfca819e8ec4d8
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65812308"
---
# <a name="quickstart-add-a-guest-user-with-powershell"></a>Snabbstart: Lägga till en gästanvändare med PowerShell

Det finns många sätt att bjuda in externa partners till dina appar och tjänster med Azure Active Directory B2B-samarbete. I den förra snabbstarten såg du hur du lägger till gästanvändare direkt i Azure Active Directory-administratörsportalen. Du kan också använda PowerShell för att lägga till gästanvändare antingen ett i taget eller i grupp. I den här snabbstarten får du använda kommandot New-AzureADMSInvitation för att lägga till en gästanvändare till din Azure-klientorganisation.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar. 

## <a name="prerequisites"></a>Nödvändiga komponenter

### <a name="install-the-latest-azureadpreview-module"></a>Installera den senaste AzureADPreview-modulen
Se till att du installerar den senaste versionen av Azure AD PowerShell för Graph-modulen (AzureADPreview). 

Kontrollera först vilka moduler du har installerat. Öppna Windows PowerShell som upphöjd användare (Kör som administratör) och kör följande kommando:
 
```powershell  
Get-Module -ListAvailable AzureAD*
```

Om modulen AzureADPreview visas utan meddelande som anger att det finns en senare version så är du klar. Annars gör du något av följande, baserat på utdata:

- Om inga resultat returneras kör du följande kommando för att installera AzureADPreview-modulen:
  
   ```powershell  
   Install-Module AzureADPreview
   ```
- Om enbart AzureAD-modulen visas i resultaten så kör du följande kommandon för att installera AzureADPreview-modulen: 

   ```powershell 
   Uninstall-Module AzureAD 
   Install-Module AzureADPreview 
   ```
- Om endast AzureADPreview-modulen visas i resultaten, men du får ett meddelande som anger att det finns en senare version så uppdaterar du modulen genom att köra följande kommandon: 

   ```powershell 
   Uninstall-Module AzureADPreview 
   Install-Module AzureADPreview 
  ```

Du får eventuellt en uppmaning om att installera modulen från en icke betrodd lagringsplats. Det här inträffar om du inte tidigare angett PSGallery-lagringsplatsen som en betrodd lagringsplats. Installera modulen genom att trycka på **Y**.

### <a name="get-a-test-email-account"></a>Skaffa ett test-e-postkonto

Du behöver ett test-e-postkonto som du kan skicka inbjudningen till. Kontot måste finnas utanför din organisation. Du kan använda valfri typ av konto, inklusive sociala konton som gmail.com- eller outlook.com-adresser.

## <a name="sign-in-to-your-tenant"></a>Logga in på din klientorganisation

Kör följande kommando för att ansluta till klientorganisationens domän:

```powershell
Connect-AzureAD -TenantDomain "<Tenant_Domain_Name>"
```
Till exempel `Connect-AzureAD -TenantDomain "contoso.onmicrosoft.com"`.

Ange dina autentiseringsuppgifter när du uppmanas att göra det.

## <a name="send-an-invitation"></a>Skicka en inbjudan

1. Om du vill skicka en inbjudan till ditt test-e-postkonto, kör du följande PowerShell-kommando (Ersätt **”Sanda”** och **sanda\@fabrikam.com** med din testning e-konto och e-postadress): 

   ```powershell
   New-AzureADMSInvitation -InvitedUserDisplayName "Sanda" -InvitedUserEmailAddress sanda@fabrikam.com -InviteRedirectURL https://myapps.azure.com -SendInvitationMessage $true
   ```
2. Kommandot skickar en inbjudan till den e-postadress som angetts. Kontrollera resultatet som bör se ut ungefär så här:

   ![PowerShell-utdata som visar väntande godkännande av användare](media/quickstart-invite-powershell/powershell-azureadmsinvitation-result.png)

## <a name="verify-the-user-exists-in-the-directory"></a>Kontrollera att användaren finns i katalogen

1. Verifiera att den inbjudna användaren lades till i Azure AD genom att köra följande kommando:
 
   ```powershell
   Get-AzureADUser -Filter "UserType eq 'Guest'"
   ```
3. Kontrollera utdata att kontrollera att användaren du inbjuden visas med en användarens huvudnamn (UPN) i formatet *e-postadress*EXT #\@*domän*. Till exempel *sanda_fabrikam.com#EXT#\@contoso.onmicrosoft.com*, där contoso.onmicrosoft.com är organisationen som du har skickat inbjudningarna.

   ![PowerShell-utdata som visar att gästanvändaren har lagts till](media/quickstart-invite-powershell/powershell-guest-user-added.png)

## <a name="clean-up-resources"></a>Rensa resurser

När den inte längre behövs kan du ta bort testanvändarkontot i katalogen. Kör följande kommando för att ta bort ett användarkonto:

```powershell
 Remove-AzureADUser -ObjectId "<UPN>"
```
Exempel: `Remove-AzureADUser -ObjectId "sanda_fabrikam.com#EXT#@contoso.onmicrosoft.com"`


## <a name="next-steps"></a>Nästa steg
I den här snabbstarten har du bjudit in och lagt till en enda gästanvändare till din katalog med PowerShell. Nu får du lära dig hur du bjuder in gästanvändare gruppvis med PowerShell.

> [!div class="nextstepaction"]
> [Självstudie: Massinbjuda Azure AD B2B-samarbetsanvändare](tutorial-bulk-invite.md)
