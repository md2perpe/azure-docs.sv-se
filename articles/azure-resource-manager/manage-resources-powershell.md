---
title: Hantera Azure-resurser med hjälp av Azure PowerShell | Microsoft Docs
description: Använda Azure PowerShell och Azure Resource Manager för att hantera dina resurser.
services: azure-resource-manager
documentationcenter: ''
author: mumian
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.openlocfilehash: 22224fba6a001fa424029caa60beb726a2775e26
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67206456"
---
# <a name="manage-azure-resources-by-using-azure-powershell"></a>Hantera Azure-resurser med hjälp av Azure PowerShell

Lär dig hur du använder Azure PowerShell med [Azure Resource Manager](resource-group-overview.md) att hantera dina Azure-resurser. För att hantera resursgrupper, se [hantera Azure-resursgrupper med hjälp av Azure PowerShell](./manage-resource-groups-powershell.md).

Andra artiklar om hur du hanterar resurser:

- [Hantera Azure-resurser med hjälp av Azure portal](./manage-resources-portal.md)
- [Hantera Azure-resurser med hjälp av Azure CLI](./manage-resources-cli.md)

## <a name="deploy-resources-to-an-existing-resource-group"></a>Distribuera resurser till en befintlig resursgrupp

Du kan distribuera Azure-resurser direkt med hjälp av Azure PowerShell eller distribuera en Resource Manager-mall för att skapa Azure-resurser.

### <a name="deploy-a-resource"></a>Distribution av en resurs

Följande skript skapar ett lagringskonto.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

# Create the storage account.
$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroupName `
  -Name $storageAccountName `
  -Location $location `
  -SkuName "Standard_LRS"

# Retrieve the context.
$ctx = $storageAccount.Context
```

### <a name="deploy-a-template"></a>Distribuera en mall

Följande skript skapar distribuera en snabbstartsmall för att skapa ett lagringskonto. Mer information finns i [ Snabbstart: Skapa Azure Resource Manager-mallar med hjälp av Visual Studio Code](./resource-manager-quickstart-create-templates-use-visual-studio-code.md?tabs=PowerShell).

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json"
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -Location $location
```

Mer information finns i [distribuera resurser med Resource Manager-mallar och Azure PowerShell](./resource-group-template-deploy.md).

## <a name="deploy-a-resource-group-and-resources"></a>Distribuera en resursgrupp och resurser

Du kan skapa en resursgrupp och distribuera resurser i gruppen. Mer information finns i [skapa resursgrupp och distribuera resurser](./deploy-to-subscription.md#create-resource-group-and-deploy-resources).

## <a name="deploy-resources-to-multiple-subscriptions-or-resource-groups"></a>Distribuera resurser till flera prenumerationer eller resursgrupper

Normalt kan distribuera du alla resurser i mallen till en enda resursgrupp. Det finns dock scenarier där du vill distribuera en uppsättning resurser tillsammans, men placera dem i olika resursgrupper eller prenumerationer. Mer information finns i [distribuera Azure-resurser till flera prenumerationer eller resursgrupper](./resource-manager-cross-resource-group-deployment.md).

## <a name="delete-resources"></a>Ta bort resurser

Följande skript visar hur du tar bort ett lagringskonto.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

Remove-AzStorageAccount -ResourceGroupName $resourceGroupName -AccountName $storageAccountName
```

Mer information om hur Azure Resource Manager beställningar borttagningen av resurser finns i [borttagning av Azure Resource Manager resource](./resource-group-delete.md).

## <a name="move-resources"></a>Flytta resurser

Följande skript visar hur du tar bort ett lagringskonto från en resursgrupp till en annan resursgrupp.

```azurepowershell-interactive
$srcResourceGroupName = Read-Host -Prompt "Enter the source Resource Group name"
$destResourceGroupName = Read-Host -Prompt "Enter the destination Resource Group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

$storageAccount = Get-AzResource -ResourceGroupName $srcResourceGroupName -ResourceName $storageAccountName
Move-AzResource -DestinationResourceGroupName $destResourceGroupName -ResourceId $storageAccount.ResourceId
```

Mer information finns i [Flytta resurser till en ny resursgrupp eller prenumeration](resource-group-move-resources.md).

## <a name="lock-resources"></a>Lås resurser

Låsning förhindrar andra användare i din organisation av misstag tar bort eller ändrar kritiska resurser, till exempel Azure-prenumeration, resursgrupp eller resurs. 

Följande skript låser ett storage-konto så att kontot inte kan tas bort.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

New-AzResourceLock -LockName LockStorage -LockLevel CanNotDelete -ResourceGroupName $resourceGroupName -ResourceName $storageAccountName -ResourceType Microsoft.Storage/storageAccounts 
```

Följande skript hämtar alla Lås för ett lagringskonto:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

Get-AzResourceLock -ResourceGroupName $resourceGroupName -ResourceName $storageAccountName -ResourceType Microsoft.Storage/storageAccounts
```

Följande skript tar bort ett lås för ett lagringskonto:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

$lockId = (Get-AzResourceLock -ResourceGroupName $resourceGroupName -ResourceName $storageAccountName -ResourceType Microsoft.Storage/storageAccounts).LockId
Remove-AzResourceLock -LockId $lockId
```

Mer information finns i [Låsa resurser med Azure Resource Manager](resource-group-lock-resources.md).

## <a name="tag-resources"></a>Tagga resurser

Taggar hjälper dig att organisera din resursgrupp och resurser logiskt. Mer information finns i [med taggar för att organisera Azure-resurser](./resource-group-using-tags.md#powershell).

## <a name="manage-access-to-resources"></a>Hantera åtkomst till resurser

[Rollbaserad åtkomstkontroll (RBAC)](../role-based-access-control/overview.md) är sättet som du hantera åtkomst till resurser i Azure. Mer information finns i [hantera åtkomst med RBAC och Azure PowerShell](../role-based-access-control/role-assignments-powershell.md).

## <a name="next-steps"></a>Nästa steg

- Läs Azure Resource Manager i [översikt över Azure Resource Manager](./resource-group-overview.md).
- Läs Resource Manager-mallens syntax i [förstå strukturen och syntaxen för Azure Resource Manager-mallar](./resource-group-authoring-templates.md).
- Läs hur du utvecklar mallar i den [stegvisa självstudier](/azure/azure-resource-manager/).
- Mallscheman för Azure Resource Manager-finns [mallreferensen](/azure/templates/).
