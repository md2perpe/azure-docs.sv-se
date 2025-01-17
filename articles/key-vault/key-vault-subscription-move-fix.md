---
title: Ändra den nyckelvalvsklient-ID efter en prenumerationsflytt - Azure Key Vault | Microsoft Docs
description: Lär dig hur du växlar klient-ID för ett nyckelvalv när en prenumeration flyttas till en annan klient
services: key-vault
author: amitbapat
manager: barbkess
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: ambapat
ms.openlocfilehash: f32146697be234a8a288ff991b1f7adf6e76dc7e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64724492"
---
# <a name="change-a-key-vault-tenant-id-after-a-subscription-move"></a>Ändra nyckelvalvsklient-ID efter en prenumerationsflytt

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="q-my-subscription-was-moved-from-tenant-a-to-tenant-b-how-do-i-change-the-tenant-id-for-my-existing-key-vault-and-set-correct-acls-for-principals-in-tenant-b"></a>F: Min prenumeration har flyttats från klient A till klient B. Hur gör jag för att ändra klient-ID för mitt befintliga nyckelvalv och ange rätt åtkomstkontrollistor för säkerhetsobjekt i klient B?

När du skapar ett nytt nyckelvalv i en prenumeration knyts det automatiskt till det standardmässiga Azure Active Directory-klient-ID:t för den prenumerationen. Alla åtkomstprincipposter knyts också till detta klient-ID. När du flyttar din Azure-prenumeration från klient A till klient B är ditt befintliga nyckelvalv otillgängligt för säkerhetsobjekten (användare och program) i klient B. Om du vill åtgärda problemet måste du:

* Ändra det klient-ID som är associerat med alla befintliga nyckelvalv i den här prenumerationen till klient B.
* Ta bort alla åtkomstprincipposter.
* Lägga till nya åtkomstprincipposter som är associerade med klient B.

Om du till exempel har nyckelvalvet ”myvault” i en prenumeration som har flyttats från klient A till klientens B gör du så här för att ändra klient-ID:t för detta nyckelvalv och ta bort gamla åtkomstprinciper.

<pre>
Select-AzSubscription -SubscriptionId YourSubscriptionID
$vaultResourceId = (Get-AzKeyVault -VaultName myvault).ResourceId
$vault = Get-AzResource –ResourceId $vaultResourceId -ExpandProperties
$vault.Properties.TenantId = (Get-AzContext).Tenant.TenantId
$vault.Properties.AccessPolicies = @()
Set-AzResource -ResourceId $vaultResourceId -Properties $vault.Properties
</pre>

Eftersom det här valvet fanns på klient A innan flytten är ursprungsvärdet **$vault. Properties.TenantId** klient a, medan **(Get-AzContext). Tenant.TenantId** är klient B.

Nu när ditt valv är associerat med rätt klient-ID och gamla åtkomstprincipposter har tagits bort kan du ange nya åtkomstprincipposter med [Set-AzKeyVaultAccessPolicy](https://docs.microsoft.com/powershell/module/az.keyvault/Set-azKeyVaultAccessPolicy).

## <a name="next-steps"></a>Nästa steg

Om du har några frågor om Azure Key Vault kan du besöka [Azure Key Vault-forumet](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureKeyVault).
