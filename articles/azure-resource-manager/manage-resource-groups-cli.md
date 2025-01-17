---
title: Hantera grupper i Azure Resource Manager med hjälp av Azure CLI | Microsoft Docs
description: Använda Azure CLI för att hantera dina Azure Resource Manager-grupper.
services: azure-resource-manager
documentationcenter: ''
author: mumian
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.openlocfilehash: c50a96b2598b89d5072a9441162d198163156c8d
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2019
ms.locfileid: "67296277"
---
# <a name="manage-azure-resource-manager-resource-groups-by-using-azure-cli"></a>Hantera resursgrupper i Azure Resource Manager med hjälp av Azure CLI

Lär dig hur du använder Azure CLI med [Azure Resource Manager](resource-group-overview.md) att hantera dina Azure-resursgrupper. Hantera Azure-resurser finns i [hantera Azure-resurser med hjälp av Azure CLI](./manage-resources-cli.md).

Andra artiklar om hur du hanterar resursgrupper:

- [Hantera Azure-resursgrupper med hjälp av Azure-portalen](./manage-resources-portal.md)
- [Hantera Azure-resursgrupper med hjälp av Azure PowerShell](./manage-resources-powershell.md)

## <a name="what-is-a-resource-group"></a>Vad är en resursgrupp

En resursgrupp är en container som innehåller relaterade resurser för en Azure-lösning. Resursgruppen kan innehålla alla resurser för lösningen, eller endast de resurser som du vill hantera som en grupp. Du bestämmer hur du vill allokera resurser till resursgrupper baserat på vad som är lämpligast för din organisation. I allmänhet ska du lägga till resurser som delar samma livscykel i samma resursgrupp så att du enkelt kan distribuera, uppdatera och ta bort dem som en grupp.

Resursgruppen lagrar metadata om resurserna. När du anger en plats för resursgruppen anger du därför var metadatan ska lagras. På grund av regelefterlevnadsskäl kan du behöva säkerställa att din data lagras inom en viss region.

Resursgruppen lagrar metadata om resurserna. När du anger en plats för resursgruppen anger du var metadatan ska lagras.

## <a name="create-resource-groups"></a>Skapa resursgrupper

Följande CLI-skript skapar en resursgrupp och sedan visar resursgruppen.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the location (i.e. centralus):" &&
read location &&
az group create --name $resourceGroupName --location $location
```

## <a name="list-resource-groups"></a>Lista över resursgrupper

Följande CLI-skript visar resursgrupper i din prenumeration.

```azurecli-interactive
az group list
```

Hämta en resursgrupp:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group show --name $resourceGroupName
```

## <a name="delete-resource-groups"></a>Ta bort resursgrupper

Följande CLI-skript tar bort en resursgrupp:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName
```

Mer information om hur Azure Resource Manager beställningar borttagningen av resurser finns i [borttagning av Azure Resource Manager resource](./resource-group-delete.md).

## <a name="deploy-resources-to-an-existing-resource-group"></a>Distribuera resurser till en befintlig resursgrupp

Se [distribuera resurser till en befintlig resursgrupp](./manage-resources-cli.md#deploy-resources-to-an-existing-resource-group).

## <a name="deploy-a-resource-group-and-resources"></a>Distribuera en resursgrupp och resurser

Du kan skapa en resursgrupp och distribuera resurser i gruppen med hjälp av Resource Manager-mall. Mer information finns i [skapa resursgrupp och distribuera resurser](./deploy-to-subscription.md#create-resource-group-and-deploy-resources).

## <a name="redeploy-when-deployment-fails"></a>Distribuera om när distributionen misslyckas

Den här funktionen kallas även *återställning vid fel*. Mer information finns i [omdistribuera när distributionen misslyckas](./resource-group-template-deploy-cli.md#redeploy-when-deployment-fails).

## <a name="move-to-another-resource-group-or-subscription"></a>Flytta till en annan resursgrupp eller prenumeration

Du kan flytta resurserna i gruppen till en annan resursgrupp. Mer information finns i [flytta resurser](./manage-resources-cli.md#move-resources).

## <a name="lock-resource-groups"></a>Lås resursgrupper

Låsning förhindrar andra användare i din organisation av misstag tar bort eller ändrar kritiska resurser, till exempel Azure-prenumeration, resursgrupp eller resurs. 

Följande skript låser en resursgrupp så resursgruppen inte kan tas bort.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az lock create --name LockGroup --lock-type CanNotDelete --resource-group $resourceGroupName  
```

Följande skript hämtar alla Lås för en resursgrupp:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az lock list --resource-group $resourceGroupName  
```

Följande skript tar bort ett lås:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the lock name:" &&
read lockName &&
az lock delete --name $lockName --resource-group $resourceGroupName
```

Mer information finns i [Låsa resurser med Azure Resource Manager](resource-group-lock-resources.md).

## <a name="tag-resource-groups"></a>Resursgrupper för taggen

Du kan lägga till taggar till resursgrupper och resurser och organisera dem logiskt dina tillgångar. Mer information finns i [med taggar för att organisera Azure-resurser](./resource-group-using-tags.md#azure-cli).

## <a name="export-resource-groups-to-templates"></a>Exportera resursgrupper till mallar

När du har skapat en resursgrupp kan du visa Resource Manager-mallen för resursgruppen. Exportera mallen erbjuder två fördelar:

- Automatisera framtida distributioner av lösningen eftersom mallen innehåller alla hela infrastrukturen.
- Lär dig mer om mallsyntaxen genom att titta på den JavaScript Object Notation (JSON) som representerar din lösning.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group export --name $resourceGroupName  
```

Skriptet visar mallen på konsolen.  Kopiera JSON och spara som en fil.

Mer information finns i [enstaka och flera resurs export till mallen i Azure-portalen](./export-template-portal.md).

## <a name="manage-access-to-resource-groups"></a>Hantera åtkomst till resursgrupper

[Rollbaserad åtkomstkontroll (RBAC)](../role-based-access-control/overview.md) är sättet som du hantera åtkomst till resurser i Azure. Mer information finns i [hantera åtkomst med RBAC och Azure CLI](../role-based-access-control/role-assignments-cli.md).

## <a name="next-steps"></a>Nästa steg

- Läs Azure Resource Manager i [översikt över Azure Resource Manager](./resource-group-overview.md).
- Läs Resource Manager-mallens syntax i [förstå strukturen och syntaxen för Azure Resource Manager-mallar](./resource-group-authoring-templates.md).
- Läs hur du utvecklar mallar i den [stegvisa självstudier](/azure/azure-resource-manager/).
- Mallscheman för Azure Resource Manager-finns [mallreferensen](/azure/templates/).