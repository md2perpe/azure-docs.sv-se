---
title: Azure-snabbstart – Ställ in och hämta en hemlighet från Key Vault med hjälp av PowerShell | Microsoft Docs
description: ''
services: key-vault
author: barclayn
manager: barbkess
tags: azure-resource-manager
ms.service: key-vault
ms.topic: quickstart
ms.custom: mvc
ms.date: 01/07/2019
ms.author: barclayn
ms.openlocfilehash: 8d6260d462b4c244dfb41630e06710a1ce8baf6c
ms.sourcegitcommit: 1aefdf876c95bf6c07b12eb8c5fab98e92948000
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/06/2019
ms.locfileid: "66726781"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-using-powershell"></a>Snabbstart: Ange och hämta en hemlighet från Azure Key Vault med hjälp av PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Azure Key Vault är en molntjänst som fungerar som säkert lager för hemligheter. Du kan på ett säkert sätt lagra nycklar, lösenord, certifikat och andra hemligheter. Mer information om Key Vault finns i [översikten](key-vault-overview.md). I den här snabbstarten använder du PowerShell till att skapa ett nyckelvalv. Sedan lagrar du en hemlighet i valvet du skapade.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda PowerShell lokalt kräver den här självstudien Azure PowerShell-modul version 1.0.0 eller senare. Skriv `$PSVersionTable.PSVersion` för att hitta versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-az-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Login-AzAccount` för att skapa en anslutning till Azure.

```azurepowershell-interactive
Login-AzAccount
```

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en Azure-resursgrupp med [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). En resursgrupp är en logisk container där Azure-resurser distribueras och hanteras. 

```azurepowershell-interactive
New-AzResourceGroup -Name ContosoResourceGroup -Location EastUS
```

## <a name="create-a-key-vault"></a>Skapa en Key Vault-lösning

Sedan skapar du ett nyckelvalv. När du utför det här steget behöver du en del information:

Även om vi använder ”Contoso KeyVault2” som namn på vårt nyckelvalv i den här snabbstarten måste du använda ett unikt namn.

- **Valvnamn** Contoso-Vault2.
- **Resursgruppnamn** ContosoResourceGroup.
- **Plats** Östra USA.

```azurepowershell-interactive
New-AzKeyVault -Name 'Contoso-Vault2' -ResourceGroupName 'ContosoResourceGroup' -Location 'East US'
```

Utdata från denna cmdlet visar egenskaper för nyckelvalvet du precis skapade. Anteckna de två egenskaperna som visas nedan:

* **Valvnamn**: I det här exemplet är detta **Contoso-Vault2**. Du ska använda det här namnet för andra Key Vault-cmdlets.
* **Valvets URI**: I det här exemplet är det https://contosokeyvault.vault.azure.net/. Program som använder ditt valv via dess REST-API måste använda denna URI.

När du har skapat valvet så är ditt Azure-konto det enda kontot med behörighet att göra någonting i valvet.

![Utdata när kommandot för att skapa Key Vault har slutförts](./media/quick-create-powershell/output-after-creating-keyvault.png)

## <a name="adding-a-secret-to-key-vault"></a>Lägga till en hemlighet i nyckelvalvet

När du ska lägga till en hemlighet i valvet behöver du bara utföra ett fåtal steg. I det här fallet lägger du till ett lösenord som kan användas av ett program. Lösenordet kallas **ExamplePassword** och lagrar värdet **hVFkk965BuUv**.

Konvertera först värdet **hVFkk965BuUv** till en säker sträng genom att skriva följande:

```azurepowershell-interactive
$secretvalue = ConvertTo-SecureString 'hVFkk965BuUv' -AsPlainText -Force
```

Skriv sedan PowerShell-kommandona nedan för att skapa en hemlighet i nyckelvalvet med namnet **ExamplePassword**, som har värdet **hVFkk965BuUv** :

```azurepowershell-interactive
$secret = Set-AzKeyVaultSecret -VaultName 'ContosoKeyVault' -Name 'ExamplePassword' -SecretValue $secretvalue
```

Så här visar du värdet som finns i hemligheten som oformaterad text:

```azurepowershell-interactive
(Get-AzKeyVaultSecret -vaultName "Contosokeyvault" -name "ExamplePassword").SecretValueText
```

Nu har du skapat ett nyckelvalv, lagrat en hemlighet och hämtat den.

## <a name="clean-up-resources"></a>Rensa resurser

 De andra snabbstarterna och självstudierna i den här samlingen bygger på den här snabbstarten. Om du planerar att fortsätta med andra snabbstarter och självstudier kan du lämna kvar de här resurserna.

När du inte behöver resursgruppen längre kan du använda kommandot [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) till att ta bort resursgruppen, nyckelvalvet och alla relaterade resurser.

```azurepowershell-interactive
Remove-AzResourceGroup -Name ContosoResourceGroup
```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du skapat ett nyckelvalv och lagrat en programnyckel i det. Om du vill lära dig mer om Key Vault och hur du kan använda det med dina program fortsätter du till självstudien om att använda webbprogram med Key Vault.

Om du vill lära dig att läsa en hemlighet från Key Vault via en webbapp genom att använda hanterade identiteter för Azure-resurser fortsätter du med självstudien

> [!div class="nextstepaction"]
> [Konfigurera en Azure-webbapp att läsa en hemlighet från Key Vault](quick-create-net.md).
