---
title: Skydda din hanterade domän i Azure Active Directory Domain Services | Microsoft Docs
description: Skydda din hanterade domän
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 6b4665b5-4324-42ab-82c5-d36c01192c2a
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2019
ms.author: iainfou
ms.openlocfilehash: e94cd9ca049cfdfd2321ce046714506ed1f23390
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/01/2019
ms.locfileid: "67483286"
---
# <a name="secure-your-azure-ad-domain-services-managed-domain"></a>Skydda din Azure AD Domain Services-hanterad domän
Den här artikeln hjälper dig skydda din hanterade domän. Du kan inaktivera användningen av svaga krypteringssviter och inaktivera synkronisering av lösenordshash för NTLM autentiseringsuppgifter.

## <a name="install-the-required-powershell-modules"></a>Installera PowerShell-moduler som krävs

### <a name="install-and-configure-azure-ad-powershell"></a>Installera och konfigurera Azure AD PowerShell
Följ instruktionerna i artikeln om du vill [installerar Azure AD PowerShell-modulen och ansluter till Azure AD](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?toc=%2fazure%2factive-directory-domain-services%2ftoc.json).

### <a name="install-and-configure-azure-powershell"></a>Installera och konfigurera Azure PowerShell
Följ instruktionerna i artikeln om du vill [installera Azure PowerShell-modulen och ansluta till din Azure-prenumeration](https://docs.microsoft.com/powershell/azure/install-az-ps?toc=%2fazure%2factive-directory-domain-services%2ftoc.json).


## <a name="disable-weak-cipher-suites-and-ntlm-credential-hash-synchronization"></a>Inaktivera svaga chiffersviter och synkronisering av lösenordshash för NTLM autentiseringsuppgifter
Använd följande PowerShell-skript för att:
1. Inaktivera NTLM v1-support på den hanterade domänen.
2. Inaktiverar du synkroniseringen av NTLM-lösenordshashvärden från din lokala AD.
3. Inaktivera TLS v1 på den hanterade domänen.

```powershell
// Login to your Azure AD tenant
Login-AzAccount

// Retrieve the Azure AD Domain Services resource.
$DomainServicesResource = Get-AzResource -ResourceType "Microsoft.AAD/DomainServices"

// 1. Disable NTLM v1 support on the managed domain.
// 2. Disable the synchronization of NTLM password hashes from
//    on-premises AD to Azure AD and Azure AD Domain Services
// 3. Disable TLS v1 on the managed domain.
$securitySettings = @{"DomainSecuritySettings"=@{"NtlmV1"="Disabled";"SyncNtlmPasswords"="Disabled";"TlsV1"="Disabled"}}

// Apply the settings to the managed domain.
Set-AzResource -Id $DomainServicesResource.ResourceId -Properties $securitySettings -Verbose -Force
```

> [!IMPORTANT]
> Användare (och tjänstkonton) kan inte utföra enkla LDAP-bindningar om du har inaktiverat NTLM synkronisering av lösenordshash på din Azure AD Domain Services-instans.  Mer information om hur du inaktiverar NTLM synkronisering av lösenordshash [skydda din hanterade domän i Azure AD DOmain Services](secure-your-domain.md).
>
>

## <a name="next-steps"></a>Nästa steg
* [Förstå synkronisering i Azure AD Domain Services](synchronization.md)
