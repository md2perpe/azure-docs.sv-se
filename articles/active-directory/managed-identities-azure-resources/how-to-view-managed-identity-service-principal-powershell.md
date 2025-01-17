---
title: Så här visar du tjänstens huvudnamn för en hanterad identitet med hjälp av PowerShell
description: Stegvisa instruktioner för att visa tjänstens huvudnamn för en hanterad identitet med hjälp av PowerShell.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/29/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5aa5ab96ae85833e59ed40161cafcc1eb88379be
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60441788"
---
# <a name="view-the-service-principal-of-a-managed-identity-using-powershell"></a>Visa tjänstens huvudnamn för en hanterad identitet med hjälp av PowerShell

Hanterade identiteter för Azure-resurser tillhandahåller Azure-tjänster med en automatiskt hanterad identitet i Azure Active Directory. Du kan använda den här identiteten för att autentisera till en tjänst som stöder Azure AD-autentisering utan autentiseringsuppgifter i din kod. 

I den här artikeln får lära du att visa tjänstens huvudnamn för en hanterad identitet med hjälp av PowerShell.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Nödvändiga komponenter

- Om du är bekant med hanterade identiteter för Azure-resurser kan du kolla den [översiktsavsnittet](overview.md).
- Om du inte redan har ett Azure-konto, [registrera dig för ett kostnadsfritt konto](https://azure.microsoft.com/free/).
- Aktivera [systemtilldelade identiteter på en virtuell dator](/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm#system-assigned-managed-identity) eller [program](/azure/app-service/overview-managed-identity#adding-a-system-assigned-identity).
- Installera den senaste versionen av [Azure PowerShell](/powershell/azure/install-az-ps)

## <a name="view-the-service-principal"></a>Visa tjänstens huvudnamn

Den här följande kommando visar hur du visa tjänstens huvudnamn för en virtuell dator eller ett program med systemtilldelade identiteter aktiverat. Ersätt `<VM or application name>` med dina egna värden.

```powershell
Get-AzADServicePrincipal -DisplayName <VM or application name>
```

## <a name="next-steps"></a>Nästa steg

Mer information om hur du visar tjänsthuvudnamn i Azure AD med hjälp av PowerShell finns i [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal).


