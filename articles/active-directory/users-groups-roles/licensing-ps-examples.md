---
title: PowerShell och Graph-exempel för licensiering grupper – Azure Active Directory | Microsoft Docs
description: PowerShell och Graph exempel och scenarier för gruppbaserad licensiering i Azure Active Directory
services: active-directory
keywords: Azure AD-licensiering
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.subservice: users-groups-roles
ms.topic: article
ms.workload: identity
ms.date: 03/18/2019
ms.author: curtand
ms.reviewer: sumitp
ms.collection: M365-identity-device-management
ms.openlocfilehash: f95c0596d7a2b55867cdb7ed9355006500e89242
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67065503"
---
# <a name="powershell-and-graph-examples-for-group-based-licensing-in-azure-ad"></a>PowerShell och Graph-exempel för gruppbaserad licensiering i Azure AD

Alla funktioner för gruppbaserad licensiering är tillgänglig via den [Azure-portalen](https://portal.azure.com), och för närvarande stöd för PowerShell och Microsoft Graph är begränsat till skrivskyddade åtgärder. Det finns dock några användbara uppgifter som kan utföras med hjälp av befintliga [MSOnline PowerShell-cmdletar](https://docs.microsoft.com/powershell/msonline/v1/azureactivedirectory) och Microsoft Graph. Det här dokumentet innehåller exempel på vad som är möjligt.

> [!NOTE]
> Innan du kör cmdlet: ar, se till att du ansluter till din organisation börja genom att köra den `Connect-MsolService`  cmdlet.

> [!WARNING]
> Den här koden finns som ett exempel i demonstrationssyfte. Överväg att testa det först i liten skala eller i en separat test-klient om du planerar att använda den i din miljö. Du kan behöva justera koden för att uppfylla de specifika behoven i din miljö.

## <a name="view-product-licenses-assigned-to-a-group"></a>Visa produktlicenser tilldelas en grupp

Den [Get-MsolGroup](/powershell/module/msonline/get-msolgroup?view=azureadps-1.0) cmdlet kan användas för att hämta gruppobjekt och kontrollera den *licenser* egenskapen: visas en lista med alla produktlicenser som tilldelats gruppen.

```powershell
(Get-MsolGroup -ObjectId 99c4216a-56de-42c4-a4ac-e411cd8c7c41).Licenses
| Select SkuPartNumber
```
Utdata:
```
SkuPartNumber
-------------
ENTERPRISEPREMIUM
EMSPREMIUM
```

> [!NOTE]
> Data är begränsad till produktinformation (SKU). Det går inte att lista service-planer som inaktiverade i licensen.

Använd följande exempel för att hämta samma data från Microsoft Graph.

```
GET https://graph.microsoft.com/v1.0/groups/99c4216a-56de-42c4-a4ac-e411cd8c7c41$select=assignedLicenses
```
Utdata:
```
HTTP/1.1 200 OK
{
  “value”: [
{
  “assignedLicenses”: [
     {
          "accountId":"f1b45b40-57df-41f7-9596-7f2313883635",
          "skuId":"c7df2760-2c81-4ef7-b578-5b5392b571df",
      "disabledPlans":[]
     },
     {
          "accountId":"f1b45b40-57df-41f7-9596-7f2313883635",
          "skuId":" b05e124f-c7cc-45a0-a6aa-8cf78c946968",
      "disabledPlans":[]
     },
  ],
}
  ]
}
```

## <a name="get-all-groups-with-licenses"></a>Hämta alla grupper med licenser

Du hittar alla grupper med en licens som tilldelas genom att köra följande kommando:
```powershell
Get-MsolGroup | Where {$_.Licenses}
```
Mer information kan visas om vilka produkter är tilldelade:
```powershell
Get-MsolGroup | Where {$_.Licenses} | Select `
    ObjectId, `
    DisplayName, `
    @{Name="Licenses";Expression={$_.Licenses | Select -ExpandProperty SkuPartNumber}}
```

Utdata:
```
ObjectId                             DisplayName              Licenses
--------                             -----------              --------
7023a314-6148-4d7b-b33f-6c775572879a EMS E5 – Licensed users  EMSPREMIUM
cf41f428-3b45-490b-b69f-a349c8a4c38e PowerBi - Licensed users POWER\_BI\_STANDARD
962f7189-59d9-4a29-983f-556ae56f19a5 O365 E3 - Licensed users ENTERPRISEPACK
c2652d63-9161-439b-b74e-fcd8228a7074 EMSandOffice             {ENTERPRISEPREMIUM,EMSPREMIUM}
```

## <a name="get-statistics-for-groups-with-licenses"></a>Få statistik för grupper med licenser
Du kan rapportera grundläggande statistik för grupper med licenser. I exemplet nedan visar skriptet Totalt antal, antal användare med licenser som redan har tilldelats av gruppen och antal användare för vilka licenser inte gick att tilldela av gruppen.

```powershell
#get all groups with licenses
Get-MsolGroup -All | Where {$_.Licenses}  | Foreach {
    $groupId = $_.ObjectId;
    $groupName = $_.DisplayName;
    $groupLicenses = $_.Licenses | Select -ExpandProperty SkuPartNumber
    $totalCount = 0;
    $licenseAssignedCount = 0;
    $licenseErrorCount = 0;

    Get-MsolGroupMember -All -GroupObjectId $groupId |
    #get full info about each user in the group
    Get-MsolUser -ObjectId {$_.ObjectId} |     Foreach {
        $user = $_;
        $totalCount++

        #check if any licenses are assigned via this group
        if($user.Licenses | ? {$_.GroupsAssigningLicense -ieq $groupId })
        {
            $licenseAssignedCount++
        }
        #check if user has any licenses that failed to be assigned from this group
        if ($user.IndirectLicenseErrors | ? {$_.ReferencedObjectId -ieq $groupId })
        {
            $licenseErrorCount++
        }     
    }

    #aggregate results for this group
    New-Object Object |
                    Add-Member -NotePropertyName GroupName -NotePropertyValue $groupName -PassThru |
                    Add-Member -NotePropertyName GroupId -NotePropertyValue $groupId -PassThru |
                    Add-Member -NotePropertyName GroupLicenses -NotePropertyValue $groupLicenses -PassThru |
                    Add-Member -NotePropertyName TotalUserCount -NotePropertyValue $totalCount -PassThru |
                    Add-Member -NotePropertyName LicensedUserCount -NotePropertyValue $licenseAssignedCount -PassThru |
                    Add-Member -NotePropertyName LicenseErrorCount -NotePropertyValue $licenseErrorCount -PassThru

    } | Format-Table
```


Utdata:
```
GroupName         GroupId                              GroupLicenses       TotalUserCount LicensedUserCount LicenseErrorCount
---------         -------                              -------------       -------------- ----------------- -----------------
Dynamics Licen... 9160c903-9f91-4597-8f79-22b6c47eafbf AAD_PREMIUM_P2                   0                 0                 0
O365 E5 - base... 055dcca3-fb75-4398-a1b8-f8c6f4c24e65 ENTERPRISEPREMIUM                2                 2                 0
O365 E5 - extr... 6b14a1fe-c3a9-4786-9ee4-3a2bb54dcb8e ENTERPRISEPREMIUM                3                 3                 0
EMS E5 - all s... 7023a314-6148-4d7b-b33f-6c775572879a EMSPREMIUM                       2                 2                 0
PowerBi - Lice... cf41f428-3b45-490b-b69f-a349c8a4c38e POWER_BI_STANDARD                2                 2                 0
O365 E3 - all ... 962f7189-59d9-4a29-983f-556ae56f19a5 ENTERPRISEPACK                   2                 2                 0
O365 E5 - EXO     102fb8f4-bbe7-462b-83ff-2145e7cdd6ed ENTERPRISEPREMIUM                1                 1                 0
Access to Offi... 11151866-5419-4d93-9141-0603bbf78b42 STANDARDPACK                     4                 3                 1
```

## <a name="get-all-groups-with-license-errors"></a>Hämta alla grupper med licens-fel
Hitta grupper som innehåller vissa användare för vilka licenser inte kunde tilldelas:
```powershell
Get-MsolGroup -HasLicenseErrorsOnly $true
```
Utdata:
```
ObjectId                             DisplayName             GroupType Description
--------                             -----------             --------- -----------
11151866-5419-4d93-9141-0603bbf78b42 Access to Office 365 E1 Security  Users who should have E1 licenses
```
Använd följande för att få samma data från Microsoft Graph
```
GET https://graph.microsoft.com/v1.0/groups?$filter=hasMembersWithLicenseErrors+eq+true
```
Utdata:
```
HTTP/1.1 200 OK
{
  "value":[
    {
      "odata.type": "Microsoft.DirectoryServices.Group",
      "objectType": "Group",
      "id": "11151866-5419-4d93-9141-0603bbf78b42",
      ... # other group properties.
    },
    {
      "odata.type": "Microsoft.DirectoryServices.Group",
      "objectType": "Group",
      "id": "c57cdc98-0dcd-4f90-a82f-c911b288bab9",
      ...
    },
    ... # other groups with license errors.
  ]
"odata.nextLink":"https://graph.microsoft.com/v1.0/ groups?$filter=hasMembersWithLicenseErrors+eq+true&$skipToken=<encodedPageToken>"
}
```


## <a name="get-all-users-with-license-errors-in-a-group"></a>Hämta alla användare med licens fel i en grupp

Med en grupp som innehåller vissa licens-relaterade fel kan du nu visa en lista med alla användare som påverkas av felen. En användare kan ha fel från andra grupper för. Men i det här exemplet vi begränsar resultaten enbart för fel som är relevanta för gruppen i fråga genom att markera den **ReferencedObjectId** egenskapen för varje **IndirectLicenseError** transaktionen på användaren.

```powershell
#a sample group with errors
$groupId = '11151866-5419-4d93-9141-0603bbf78b42'

#get all user members of the group
Get-MsolGroupMember -All -GroupObjectId $groupId |
    #get full information about user objects
    Get-MsolUser -ObjectId {$_.ObjectId} |
    #filter out users without license errors and users with license errors from other groups
    Where {$_.IndirectLicenseErrors -and $_.IndirectLicenseErrors.ReferencedObjectId -eq $groupId} |
    #display id, name and error detail. Note: we are filtering out license errors from other groups
    Select ObjectId, `
           DisplayName, `
           @{Name="LicenseError";Expression={$_.IndirectLicenseErrors | Where {$_.ReferencedObjectId -eq $groupId} | Select -ExpandProperty Error}}
```

Utdata:

```powershell
ObjectId                             DisplayName      License Error
--------                             -----------      ------------
6d325baf-22b7-46fa-a2fc-a2500613ca15 Catherine Gibson MutuallyExclusiveViolation
```

Använd följande för att få samma data från Microsoft Graph:

```powershell
GET https://graph.microsoft.com/v1.0/groups/11151866-5419-4d93-9141-0603bbf78b42/membersWithLicenseErrors
```

Utdata:
```powershell
HTTP/1.1 200 OK
{
  "value":[
    {
      "odata.type": "Microsoft.DirectoryServices.User",
      "objectType": "User",
      "id": "6d325baf-22b7-46fa-a2fc-a2500613ca15",
      ... # other user properties.
    },
    ... # other users.
  ],
  "odata.nextLink":"https://graph.microsoft.com/v1.0/groups/11151866-5419-4d93-9141-0603bbf78b42/membersWithLicenseErrors?$skipToken=<encodedPageToken>"
}

```

## <a name="get-all-users-with-license-errors-in-the-entire-tenant"></a>Hämta alla användare med licens fel i hela klientorganisationen

Följande skript kan användas för att hämta alla användare som har licens fel från en eller flera grupper. Skriptet skriver ut en rad per användare per licensfel, vilket gör att du tydligt identifiera källan för varje fel.

> [!NOTE]
> Det här skriptet räknar upp alla användare i klienten, vilket inte kanske är optimala för stora klienter.

```powershell
Get-MsolUser -All | Where {$_.IndirectLicenseErrors } | % {   
    $user = $_;
    $user.IndirectLicenseErrors | % {
            New-Object Object |
                Add-Member -NotePropertyName UserName -NotePropertyValue $user.DisplayName -PassThru |
                Add-Member -NotePropertyName UserId -NotePropertyValue $user.ObjectId -PassThru |
                Add-Member -NotePropertyName GroupId -NotePropertyValue $_.ReferencedObjectId -PassThru |
                Add-Member -NotePropertyName LicenseError -NotePropertyValue $_.Error -PassThru
        }
    }  
```

Utdata:

```powershell
UserName         UserId                               GroupId                              LicenseError
--------         ------                               -------                              ------------
Anna Bergman     0d0fd16d-872d-4e87-b0fb-83c610db12bc 7946137d-b00d-4336-975e-b1b81b0666d0 MutuallyExclusiveViolation
Catherine Gibson 6d325baf-22b7-46fa-a2fc-a2500613ca15 f2503e79-0edc-4253-8bed-3e158366466b CountViolation
Catherine Gibson 6d325baf-22b7-46fa-a2fc-a2500613ca15 11151866-5419-4d93-9141-0603bbf78b42 MutuallyExclusiveViolation
Drew Fogarty     f2af28fc-db0b-4909-873d-ddd2ab1fd58c 1ebd5028-6092-41d0-9668-129a3c471332 MutuallyExclusiveViolation
```

Här är en annan version av det skript som söker bara igenom grupper som innehåller licens-fel. Det kan vara mer optimerade för scenarier där du förväntar dig att ha några grupper med problem.

```powershell
$groupIds = Get-MsolGroup -HasLicenseErrorsOnly $true
    foreach ($groupId in $groupIds) {
    Get-MsolGroupMember -All -GroupObjectId $groupId.ObjectID |
        Get-MsolUser -ObjectId {$_.ObjectId} |
        Where {$_.IndirectLicenseErrors -and $_.IndirectLicenseErrors.ReferencedObjectId -eq $groupId.ObjectID} |
        Select DisplayName, `
               ObjectId, `
               @{Name="LicenseError";Expression={$_.IndirectLicenseErrors | Where {$_.ReferencedObjectId -eq $groupId.ObjectID} | Select -ExpandProperty Error}}
 
    } 
``` 

## <a name="check-if-user-license-is-assigned-directly-or-inherited-from-a-group"></a>Kontrollera om användarlicens tilldelas direkt eller ärvts från en grupp

Det är möjligt att kontrollera om en viss licens tilldelas från en grupp eller om den är tilldelad direkt för ett användarobjekt.

De två funktionerna för exemplet nedan kan användas för att analysera typ av uppgift på en enskild användare:

```powershell
#Returns TRUE if the user has the license assigned directly
function UserHasLicenseAssignedDirectly
{
    Param([Microsoft.Online.Administration.User]$user, [string]$skuId)

    foreach($license in $user.Licenses)
    {
        #we look for the specific license SKU in all licenses assigned to the user
        if ($license.AccountSkuId -ieq $skuId)
        {
            #GroupsAssigningLicense contains a collection of IDs of objects assigning the license
            #This could be a group object or a user object (contrary to what the name suggests)
            #If the collection is empty, this means the license is assigned directly - this is the case for users who have never been licensed via groups in the past
            if ($license.GroupsAssigningLicense.Count -eq 0)
            {
                return $true
            }

            #If the collection contains the ID of the user object, this means the license is assigned directly
            #Note: the license may also be assigned through one or more groups in addition to being assigned directly
            foreach ($assignmentSource in $license.GroupsAssigningLicense)
            {
                if ($assignmentSource -ieq $user.ObjectId)
                {
                    return $true
                }
            }
            return $false
        }
    }
    return $false
}
#Returns TRUE if the user is inheriting the license from a group
function UserHasLicenseAssignedFromGroup
{
    Param([Microsoft.Online.Administration.User]$user, [string]$skuId)

    foreach($license in $user.Licenses)
    {
        #we look for the specific license SKU in all licenses assigned to the user
        if ($license.AccountSkuId -ieq $skuId)
        {
            #GroupsAssigningLicense contains a collection of IDs of objects assigning the license
            #This could be a group object or a user object (contrary to what the name suggests)
            foreach ($assignmentSource in $license.GroupsAssigningLicense)
            {
                #If the collection contains at least one ID not matching the user ID this means that the license is inherited from a group.
                #Note: the license may also be assigned directly in addition to being inherited
                if ($assignmentSource -ine $user.ObjectId)
                {
                    return $true
                }
            }
            return $false
        }
    }
    return $false
}
```

Det här skriptet körs dessa funktioner på varje användare i klienten, med hjälp av SKU-ID som indata – i det här exemplet vi är intresserade av licensen för *Enterprise Mobility + Security*, som i våra klient representeras med ID  *Contoso:EMS*:

```powershell
#the license SKU we are interested in. use Get-MsolAccountSku to see a list of all identifiers in your tenant
$skuId = "contoso:EMS"

#find all users that have the SKU license assigned
Get-MsolUser -All | where {$_.isLicensed -eq $true -and $_.Licenses.AccountSKUID -eq $skuId} | select `
    ObjectId, `
    @{Name="SkuId";Expression={$skuId}}, `
    @{Name="AssignedDirectly";Expression={(UserHasLicenseAssignedDirectly $_ $skuId)}}, `
    @{Name="AssignedFromGroup";Expression={(UserHasLicenseAssignedFromGroup $_ $skuId)}}
```

Utdata:

```powershell
ObjectId                             SkuId       AssignedDirectly AssignedFromGroup
--------                             -----       ---------------- -----------------
157870f6-e050-4b3c-ad5e-0f0a377c8f4d contoso:EMS             True             False
1f3174e2-ee9d-49e9-b917-e8d84650f895 contoso:EMS            False              True
240622ac-b9b8-4d50-94e2-dad19a3bf4b5 contoso:EMS             True              True
```

Diagrammet har inte ett enkelt sätt att visa resultatet, men det kan ses från den här API: et:

```powershell
GET https://graph.microsoft.com/v1.0/users/e61ff361-5baf-41f0-b2fd-380a6a5e406a?$select=licenseAssignmentStates
```

Utdata:

```powershell
HTTP/1.1 200 OK
{
  "value":[
    {
      "odata.type": "Microsoft.DirectoryServices.User",
      "objectType": "User",
      "id": "e61ff361-5baf-41f0-b2fd-380a6a5e406a",
      "licenseAssignmentState":[
        {
          "skuId": "157870f6-e050-4b3c-ad5e-0f0a377c8f4d”,
          "disabledPlans":[],
          "assignedByGroup": null, # assigned directly.
          "state": "Active",
          "error": "None"
        },
        {
          "skuId": "1f3174e2-ee9d-49e9-b917-e8d84650f895",
          "disabledPlans":[],
          "assignedByGroup": “e61ff361-5baf-41f0-b2fd-380a6a5e406a”, # assigned by this group.
          "state": "Active",
          "error": "None"
        },
        {
          "skuId": "240622ac-b9b8-4d50-94e2-dad19a3bf4b5", 
          "disabledPlans":[
            "e61ff361-5baf-41f0-b2fd-380a6a5e406a"
          ],
          "assignedByGroup": "e61ff361-5baf-41f0-b2fd-380a6a5e406a",
          "state": "Active",
          "error": "None"
        },
        {
          "skuId": "240622ac-b9b8-4d50-94e2-dad19a3bf4b5",
          "disabledPlans":[],
          "assignedByGroup": null, # It is the same license as the previous one. It means the license is assigned directly once and inherited from group as well.
          "state": " Active ",
          "error": " None"
        }
      ],
      ...
    }
  ],
}

```

## <a name="remove-direct-licenses-for-users-with-group-licenses"></a>Ta bort direkt licenser för användare med grupplicenserna

Syftet med det här skriptet är att ta bort onödiga direkt licenser från användare som redan ärver samma licensen från en grupp. till exempel som en del av en [övergår till gruppbaserad licensiering](https://docs.microsoft.com/azure/active-directory/active-directory-licensing-group-migration-azure-portal).
> [!NOTE]
> Det är viktigt att först verifiera att direkt licenser som ska tas bort inte aktiverar fler service funktioner än ärvda licenser. I annat fall kan tar du bort direkt licensen inaktivera åtkomst till tjänster och data för användare. För närvarande går det inte att kontrollera via PowerShell vilka tjänster är aktiverade via ärvda licenser vs direkt. I skriptet anger vi den lägsta nivån av tjänster som vi vet har ärvts från grupper och kontrollera mot att för att se till att användare inte oväntat förlorar åtkomst till tjänster.

```powershell
#BEGIN: Helper functions used by the script

#Returns TRUE if the user has the license assigned directly
function UserHasLicenseAssignedDirectly
{
    Param([Microsoft.Online.Administration.User]$user, [string]$skuId)

    $license = GetUserLicense $user $skuId

    if ($license -ne $null)
    {
        #GroupsAssigningLicense contains a collection of IDs of objects assigning the license
        #This could be a group object or a user object (contrary to what the name suggests)
        #If the collection is empty, this means the license is assigned directly - this is the case for users who have never been licensed via groups in the past
        if ($license.GroupsAssigningLicense.Count -eq 0)
        {
            return $true
        }

        #If the collection contains the ID of the user object, this means the license is assigned directly
        #Note: the license may also be assigned through one or more groups in addition to being assigned directly
        foreach ($assignmentSource in $license.GroupsAssigningLicense)
        {
            if ($assignmentSource -ieq $user.ObjectId)
            {
                return $true
            }
        }
        return $false
    }
    return $false
}
#Returns TRUE if the user is inheriting the license from a specific group
function UserHasLicenseAssignedFromThisGroup
{
    Param([Microsoft.Online.Administration.User]$user, [string]$skuId, [Guid]$groupId)

    $license = GetUserLicense $user $skuId

    if ($license -ne $null)
    {
        #GroupsAssigningLicense contains a collection of IDs of objects assigning the license
        #This could be a group object or a user object (contrary to what the name suggests)
        foreach ($assignmentSource in $license.GroupsAssigningLicense)
        {
            #If the collection contains at least one ID not matching the user ID this means that the license is inherited from a group.
            #Note: the license may also be assigned directly in addition to being inherited
            if ($assignmentSource -ieq $groupId)
            {
                return $true
            }
        }
        return $false
    }
    return $false
}

#Returns the license object corresponding to the skuId. Returns NULL if not found
function GetUserLicense
{
    Param([Microsoft.Online.Administration.User]$user, [string]$skuId, [Guid]$groupId)
    #we look for the specific license SKU in all licenses assigned to the user
    foreach($license in $user.Licenses)
    {
        if ($license.AccountSkuId -ieq $skuId)
        {
            return $license
        }
    }
    return $null
}

#produces a list of disabled service plan names for a set of plans we want to leave enabled
function GetDisabledPlansForSKU
{
    Param([string]$skuId, [string[]]$enabledPlans)

    $allPlans = Get-MsolAccountSku | where {$_.AccountSkuId -ieq $skuId} | Select -ExpandProperty ServiceStatus | Where {$_.ProvisioningStatus -ine "PendingActivation" -and $_.ServicePlan.TargetClass -ieq "User"} | Select -ExpandProperty ServicePlan | Select -ExpandProperty ServiceName
    $disabledPlans = $allPlans | Where {$enabledPlans -inotcontains $_}

    return $disabledPlans
}

function GetUnexpectedEnabledPlansForUser
{
    Param([Microsoft.Online.Administration.User]$user, [string]$skuId, [string[]]$expectedDisabledPlans)

    $license = GetUserLicense $user $skuId

    $extraPlans = @();

    if($license -ne $null)
    {
        $userDisabledPlans = $license.ServiceStatus | where {$_.ProvisioningStatus -ieq "Disabled"} | Select -ExpandProperty ServicePlan | Select -ExpandProperty ServiceName

        $extraPlans = $expectedDisabledPlans | where {$userDisabledPlans -notcontains $_}
    }
    return $extraPlans
}
#END: helper functions

#BEGIN: executing the script
#the group to be processed
$groupId = "48ca647b-7e4d-41e5-aa66-40cab1e19101"

#license to be removed - Office 365 E3
$skuId = "contoso:ENTERPRISEPACK"

#minimum set of service plans we know are inherited from groups - we want to make sure that there aren't any users who have more services enabled
#which could mean that they may lose access after we remove direct licenses
$servicePlansFromGroups = ("EXCHANGE_S_ENTERPRISE", "SHAREPOINTENTERPRISE", "OFFICESUBSCRIPTION")

$expectedDisabledPlans = GetDisabledPlansForSKU $skuId $servicePlansFromGroups

#process all members in the group and get full info about each user in the group looping through group members. 
Get-MsolGroupMember -All -GroupObjectId $groupId | Get-MsolUser -ObjectId {$_.ObjectId} | Foreach {
        $user = $_;
        $operationResult = "";

        #check if Direct license exists on the user
        if (UserHasLicenseAssignedDirectly $user $skuId)
        {
            #check if the license is assigned from this group, as expected
            if (UserHasLicenseAssignedFromThisGroup $user $skuId $groupId)
            {
                #check if there are any extra plans we didn't expect - we are being extra careful not to remove unexpected services
                $extraPlans = GetUnexpectedEnabledPlansForUser $user $skuId $expectedDisabledPlans
                if ($extraPlans.Count -gt 0)
                {
                    $operationResult = "User has extra plans that may be lost - license removal was skipped. Extra plans: $extraPlans"
                }
                else
                {
                    #remove the direct license from user
                    Set-MsolUserLicense -ObjectId $user.ObjectId -RemoveLicenses $skuId
                    $operationResult = "Removed direct license from user."   
                }

            }
            else
            {
                $operationResult = "User does not inherit this license from this group. License removal was skipped."
            }
        }
        else
        {
            $operationResult = "User has no direct license to remove. Skipping."
        }

        #format output
        New-Object Object |
                    Add-Member -NotePropertyName UserId -NotePropertyValue $user.ObjectId -PassThru |
                    Add-Member -NotePropertyName OperationResult -NotePropertyValue $operationResult -PassThru
    } | Format-Table
#END: executing the script
```

Utdata:

```powershell
UserId                               OperationResult
------                               ---------------
7c7f860f-700a-462a-826c-f50633931362 Removed direct license from user.
0ddacdd5-0364-477d-9e4b-07eb6cdbc8ea User has extra plans that may be lost - license removal was skipped. Extra plans: SHAREPOINTWAC
aadbe4da-c4b5-4d84-800a-9400f31d7371 User has no direct license to remove. Skipping.
```
> [!NOTE]
> Uppdatera värdena för variablerna `$skuId` och `$groupId`  som bearbetas för borttagning av direkt licenser enligt din testmiljö innan du kör skriptet ovan. 

## <a name="next-steps"></a>Nästa steg

Mer information om funktionen för hantering av programvarulicenser via grupper finns i följande artiklar:

* [Vad är gruppbaserad licensiering i Azure Active Directory?](../fundamentals/active-directory-licensing-whatis-azure-portal.md)
* [Tilldela licenser till en grupp i Azure Active Directory](licensing-groups-assign.md)
* [Identifiera och lösa licensproblem för en grupp i Azure Active Directory](licensing-groups-resolve-problems.md)
* [Migrera enskilda licensierade användare till gruppbaserad licensiering i Azure Active Directory](licensing-groups-migrate-users.md)
* [Så här migrerar du användare mellan produktlicenser med gruppbaserad licensiering i Azure Active Directory](../users-groups-roles/licensing-groups-change-licenses.md)
* [Fler scenarier med gruppbaserad licensiering i Azure Active Directory](licensing-group-advanced.md)
* [PowerShell-exempel för gruppbaserad licensiering i Azure Active Directory](../users-groups-roles/licensing-ps-examples.md)
