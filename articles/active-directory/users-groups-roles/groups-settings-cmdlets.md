---
title: Konfigurera inställningar med hjälp av PowerShell - Azure Active Directory | Microsoft Docs
description: Hur hanterar inställningarna för grupper med hjälp av Azure Active Directory-cmdletar
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 02/26/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: c5ccc4ef6c095eacd29590504d46756ead856574
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67058617"
---
# <a name="azure-active-directory-cmdlets-for-configuring-group-settings"></a>Azure Active Directory-cmdletar för att konfigurera gruppinställningar
Den här artikeln innehåller anvisningar för att använda Azure Active Directory (AD Azure) PowerShell-cmdletar för att skapa och uppdatera grupper. Det här innehållet gäller endast för Office 365-grupper (kallas ibland för enhetliga grupper). 

> [!IMPORTANT]
> Vissa inställningar kräver en Azure Active Directory Premium P1-licens. Mer information finns i den [mallinställningar](#template-settings) tabell.

Mer information om hur du förhindrar att icke-administratörer kan skapa säkerhetsgrupper finns i Ange `Set-MsolCompanySettings -UsersPermissionToCreateGroupsEnabled $False` enligt beskrivningen i [Set-MSOLCompanySettings](https://docs.microsoft.com/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0). 

Office 365 kan grupper konfigureras med hjälp av ett inställningsobjekt och ett SettingsTemplate-objekt. Först ser du alla för inställningsobjekt i katalogen, eftersom din katalog är konfigurerad med standardinställningarna. Om du vill ändra standardinställningarna, måste du skapa ett nytt inställningsobjekt med en mall för inställningar. Inställningar för mallar har definierats av Microsoft. Det finns flera olika inställningar mallar. Om du vill konfigurera inställningar för din katalog i Office 365-grupper, kan du använda mallen med namnet ”Group.Unified”. Med mallen med namnet ”Group.Unified.Guest” om du vill konfigurera inställningar för Office 365-grupper på en enda grupp. Den här mallen används för att hantera gäståtkomst i Office 365-grupp. 

Cmdletarna som ingår i Azure Active Directory PowerShell V2-modulen. Mer information hur du ladda ned och installera modulen på datorn kan du läsa artikeln [Azure Active Directory PowerShell Version 2](https://docs.microsoft.com/powershell/azuread/). Du kan installera version 2-versionen av modulen från [PowerShell-galleriet](https://www.powershellgallery.com/packages/AzureAD/).



## <a name="create-settings-at-the-directory-level"></a>Skapa inställningar på directory-nivå
De här stegen skapar inställningar på directory nivå, som gäller för alla Office 365-grupper i katalogen. Cmdleten Get-AzureADDirectorySettingTemplate är endast tillgängligt i den [förhandsversion av Azure AD PowerShell-modulen för Graph](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.137).

1. Du måste ange ID för SettingsTemplate som du vill använda i DirectorySettings-cmdlets. Om du inte känner till detta ID måste returnerar denna cmdlet listan över alla inställningar-mallar:
  
   ```powershell
   Get-AzureADDirectorySettingTemplate
   ```
   Den här cmdletanrop returnerar alla mallar som är tillgängliga:
  
   ```powershell
   Id                                   DisplayName         Description
   --                                   -----------         -----------
   62375ab9-6b52-47ed-826b-58e47e0e304b Group.Unified       ...
   08d542b9-071f-4e16-94b0-74abb372e3d9 Group.Unified.Guest Settings for a specific Office 365 group
   16933506-8a8d-4f0d-ad58-e1db05a5b929 Company.BuiltIn     Setting templates define the different settings that can be used for the associ...
   4bc7f740-180e-4586-adb6-38b2e9024e6b Application...
   898f1161-d651-43d1-805c-3b0b388a9fc2 Custom Policy       Settings ...
   5cf42378-d67d-4f36-ba46-e8b86229381d Password Rule       Settings ...
   ```
2. Om du vill lägga till en riktlinje URL för användning, måste du först hämta SettingsTemplate-objekt som definierar användning riktlinje URL-värdet; det vill säga Group.Unified mallen:
  
   ```powershell
   $Template = Get-AzureADDirectorySettingTemplate -Id 62375ab9-6b52-47ed-826b-58e47e0e304b
   ```
3. Skapa sedan ett nytt inställningsobjekt baserat på mallen:
  
   ```powershell
   $Setting = $template.CreateDirectorySetting()
   ```  
4. Uppdatera sedan riktlinje användarvärde:
  
   ```powershell
   $Setting["UsageGuidelinesUrl"] = "https://guideline.example.com"
   ```  
5. Sedan Använd inställningen:
  
   ```powershell
   Set-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id -DirectorySetting $Setting
   ```
6. Du kan läsa värden med hjälp av:

   ```powershell
   $Setting.Values
   ```  
## <a name="update-settings-at-the-directory-level"></a>Uppdatera inställningar på directory-nivå
Uppdatera värdet för UsageGuideLinesUrl i inställningen mallen genom att helt enkelt redigera URL: en med steg 4 och sedan utför steg 5 om du vill ange det nya värdet.

Ta bort värdet för UsageGuideLinesUrl genom att redigera Webbadressen för att vara en tom sträng med steg 4 ovan:

   ```powershell
   $Setting["UsageGuidelinesUrl"] = ""
   ```  
Utför steg 5 om du vill ange det nya värdet.

## <a name="template-settings"></a>Mallinställningar
Här följer inställningarna som anges i Group.Unified SettingsTemplate. Om inget annat anges kräver dessa funktioner en Azure Active Directory Premium P1-licens. 

| **Inställning** | **Beskrivning** |
| --- | --- |
|  <ul><li>EnableGroupCreation<li>Ange: Boolean<li>standard: True |Flagga som anger om Office 365 skapas tillåts i katalogen genom att icke-administratörer. Den här inställningen kräver inte en Azure Active Directory Premium P1-licens.|
|  <ul><li>GroupCreationAllowedGroupId<li>Ange: String<li>Standard ”:” |GUID för gruppen som medlemmar har behörighet att skapa Office 365-grupper även om EnableGroupCreation false. |
|  <ul><li>UsageGuidelinesUrl<li>Ange: String<li>Standard ”:” |En länk till riktlinjer för användning av gruppen. |
|  <ul><li>ClassificationDescriptions<li>Ange: String<li>Standard ”:” | En kommaavgränsad lista över klassificering beskrivningar. Värdet för ClassificationDescriptions är endast giltig i det här formatet:<br>$setting [”ClassificationDescriptions”] = ”klassificering: beskrivning, klassificering: Description”<br>Klassificeringen matchar där strängar i ClassificationList.|
|  <ul><li>DefaultClassification<li>Ange: String<li>Standard ”:” | Den klassificering som ska användas som standardklassificeringen för en grupp om inget har angetts.|
|  <ul><li>PrefixSuffixNamingRequirement<li>Ange: String<li>Standard ”:” | Sträng med högst 64 tecken som definierar den namngivningskonvention som konfigurerats för Office 365-grupper. Mer information finns i [framtvinga en namnprincip för Office 365-grupper](groups-naming-policy.md). |
| <ul><li>CustomBlockedWordsList<li>Ange: String<li>Standard ”:” | Kommaavgränsad sträng med fraser som användare inte tillåts att använda i namn eller alias. Mer information finns i [framtvinga en namnprincip för Office 365-grupper](groups-naming-policy.md). |
| <ul><li>EnableMSStandardBlockedWords<li>Ange: Boolean<li>standard: ”False” | Använd inte
|  <ul><li>AllowGuestsToBeGroupOwner<li>Ange: Boolean<li>standard: False | Booleskt värde som anger huruvida en gästanvändare kan vara ägare till grupper. |
|  <ul><li>AllowGuestsToAccessGroups<li>Ange: Boolean<li>standard: True | Booleskt värde som anger huruvida en gästanvändare kan ha åtkomst till innehåll för Office 365-grupper.  Den här inställningen kräver inte en Azure Active Directory Premium P1-licens.|
|  <ul><li>GuestUsageGuidelinesUrl<li>Ange: String<li>Standard ”:” | Url till en länk till riktlinjer för gäst-användning. |
|  <ul><li>AllowToAddGuests<li>Ange: Boolean<li>standard: True | Ett booleskt värde som anger om eller inte är tillåtet att lägga till gäster i den här katalogen.|
|  <ul><li>ClassificationList<li>Ange: String<li>Standard ”:” |En kommaavgränsad lista över giltiga klassificeringsvärden som kan tillämpas på Office 365-grupper. |

## <a name="example-configure-guest-policy-for-groups-at-the-directory-level"></a>Exempel: Konfigurera princip för gäst för grupper på nivån för katalogen
1. Hämta alla mallar för inställningen:
   ```powershell
   Get-AzureADDirectorySettingTemplate
   ```
2. Om du vill ange gäst-principen för grupper på nivån för katalogen, behöver du Group.Unified mall
   ```powershell
   $Template = Get-AzureADDirectorySettingTemplate -Id 62375ab9-6b52-47ed-826b-58e47e0e304b
   ```
3. Skapa sedan ett nytt inställningsobjekt baserat på mallen:
  
   ```powershell
   $Setting = $template.CreateDirectorySetting()
   ```  
4. Uppdatera AllowToAddGuests inställningen
   ```powershell
   $Setting["AllowToAddGuests"] = $False
   ```  
5. Sedan Använd inställningen:
  
   ```powershell
   Set-AzureADDirectorySetting -Id (Get-AzureADDirectorySetting | where -Property DisplayName -Value "Group.Unified" -EQ).id -DirectorySetting $Setting
   ```
6. Du kan läsa värden med hjälp av:

   ```powershell
   $Setting.Values
   ```   

## <a name="read-settings-at-the-directory-level"></a>Läsinställningar på directory-nivå

Om du känner till namnet på den inställning som du vill hämta kan du använda den nedan cmdlet för att hämta det aktuella Inställningsvärdet. I det här exemplet hämtar vi värdet för en inställning med namnet ”UsageGuidelinesUrl”. 

   ```powershell
   (Get-AzureADDirectorySetting).Values | Where-Object -Property Name -Value UsageGuidelinesUrl -EQ
   ```
De här stegen läsa inställningar på directory nivå, som gäller för alla Office-grupper i katalogen.

1. Läsa alla befintliga directory-inställningar:
   ```powershell
   Get-AzureADDirectorySetting -All $True
   ```
   Denna cmdlet returnerar en lista över alla inställningar på directory:
   ```powershell
   Id                                   DisplayName   TemplateId                           Values
   --                                   -----------   ----------                           ------
   c391b57d-5783-4c53-9236-cefb5c6ef323 Group.Unified 62375ab9-6b52-47ed-826b-58e47e0e304b {class SettingValue {...
   ```

2. Läsa alla inställningar för en specifik grupp:
   ```powershell
   Get-AzureADObjectSetting -TargetObjectId ab6a3887-776a-4db7-9da4-ea2b0d63c504 -TargetType Groups
   ```

3. Läsa alla värden som directory inställningar i en viss katalog settings-objekt med hjälp av inställningarna för ID-GUID:
   ```powershell
   (Get-AzureADDirectorySetting -Id c391b57d-5783-4c53-9236-cefb5c6ef323).values
   ```
   Denna cmdlet returnerar namn och värden i det här inställningsobjektet för den här specifika gruppen:
   ```powershell
   Name                          Value
   ----                          -----
   ClassificationDescriptions
   DefaultClassification
   PrefixSuffixNamingRequirement
   CustomBlockedWordsList        
   AllowGuestsToBeGroupOwner     False 
   AllowGuestsToAccessGroups     True
   GuestUsageGuidelinesUrl
   GroupCreationAllowedGroupId
   AllowToAddGuests              True
   UsageGuidelinesUrl            https://guideline.example.com
   ClassificationList
   EnableGroupCreation           True
   ```

## <a name="remove-settings-at-the-directory-level"></a>Ta bort inställningar på directory-nivå
Det här steget tar bort inställningar på directory nivå, som gäller för alla Office-grupper i katalogen.
   ```powershell
   Remove-AzureADDirectorySetting –Id c391b57d-5783-4c53-9236-cefb5c6ef323c
   ```

## <a name="create-settings-for-a-specific-group"></a>Skapa inställningar för en viss grupp

1. Sök efter inställningar mallen med namnet ”Groups.Unified.Guest”
   ```powershell
   Get-AzureADDirectorySettingTemplate
  
   Id                                   DisplayName            Description
   --                                   -----------            -----------
   62375ab9-6b52-47ed-826b-58e47e0e304b Group.Unified          ...
   08d542b9-071f-4e16-94b0-74abb372e3d9 Group.Unified.Guest    Settings for a specific Office 365 group
   4bc7f740-180e-4586-adb6-38b2e9024e6b Application            ...
   898f1161-d651-43d1-805c-3b0b388a9fc2 Custom Policy Settings ...
   5cf42378-d67d-4f36-ba46-e8b86229381d Password Rule Settings ...
   ```
2. Hämta mallobjektet för mallen Groups.Unified.Guest:
   ```powershell
   $Template1 = Get-AzureADDirectorySettingTemplate -Id 08d542b9-071f-4e16-94b0-74abb372e3d9
   ```
3. Skapa ett nytt inställningsobjekt från mallen:
   ```powershell
   $SettingCopy = $Template1.CreateDirectorySetting()
   ```

4. Ange inställningen till det obligatoriska värdet:
   ```powershell
   $SettingCopy["AllowToAddGuests"]=$False
   ```
5. Hämta ID för gruppen som du vill använda den här inställningen om du vill:
   ```powershell
   $groupID= (Get-AzureADGroup -SearchString "YourGroupName").ObjectId
   ```
6. Skapa den nya inställningen för den nödvändiga gruppen i katalogen:
   ```powershell
   New-AzureADObjectSetting -TargetType Groups -TargetObjectId $groupID -DirectorySetting $SettingCopy
   ```
7. Kör följande kommando för att kontrollera inställningarna för:
   ```powershell
   Get-AzureADObjectSetting -TargetObjectId $groupID -TargetType Groups | fl Values
   ```

## <a name="update-settings-for-a-specific-group"></a>Uppdatera inställningarna för en specifik grupp
1. Hämta ID för gruppen vars inställning som du vill uppdatera:
   ```powershell
   $groupID= (Get-AzureADGroup -SearchString "YourGroupName").ObjectId
   ```
2. Hämta inställningen i gruppen:
   ```powershell
   $Setting = Get-AzureADObjectSetting -TargetObjectId $groupID -TargetType Groups
   ```
3. Uppdatera inställningen för gruppen som du behöver, t.ex.
   ```powershell
   $Setting["AllowToAddGuests"] = $True
   ```
4. Sedan hämta ID för inställningen för den här specifika gruppen:
   ```powershell
   Get-AzureADObjectSetting -TargetObjectId $groupID -TargetType Groups
   ```
   Du får ett svar som liknar detta:
   ```powershell
   Id                                   DisplayName            TemplateId                             Values
   --                                   -----------            -----------                            ----------
   2dbee4ca-c3b6-4f0d-9610-d15569639e1a Group.Unified.Guest    08d542b9-071f-4e16-94b0-74abb372e3d9   {class SettingValue {...
   ```
5. Du kan ställa in det nya värdet för den här inställningen:
   ```powershell
   Set-AzureADObjectSetting -TargetType Groups -TargetObjectId $groupID -Id 2dbee4ca-c3b6-4f0d-9610-d15569639e1a -DirectorySetting $Setting
   ```
6. Du kan läsa värdet för inställningen för att kontrollera att den har uppdaterats på rätt sätt:
   ```powershell
   Get-AzureADObjectSetting -TargetObjectId $groupID -TargetType Groups | fl Values
   ```

## <a name="cmdlet-syntax-reference"></a>Cmdlet-referens för syntax
Du kan hitta fler Azure Active Directory PowerShell-dokumentation på [Azure Active Directory-cmdletar](/powershell/azure/install-adv2?view=azureadps-2.0).

## <a name="additional-reading"></a>Ytterligare resurser

* [Hantera åtkomst till resurser med Azure Active Directory-grupper](../fundamentals/active-directory-manage-groups.md)
* [Integrera dina lokala identiteter med Azure Active Directory](../hybrid/whatis-hybrid-identity.md)
