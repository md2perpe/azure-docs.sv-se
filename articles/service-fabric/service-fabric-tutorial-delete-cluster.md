---
title: Ta bort ett Service Fabric-kluster i Azure | Microsoft Docs
description: I den här självstudien får du lära dig hur du tar bort ett Azure-baserat Service Fabric-kluster och alla dess resurser. Du kan ta bort resursgruppen som innehåller klustret eller ta bort resurser selektivt.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/26/2018
ms.author: aljo
ms.custom: mvc
ms.openlocfilehash: 0e5137a8183f378ee5960846e281222c6ecaaa47
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "66158088"
---
# <a name="tutorial-remove-a-service-fabric-cluster-running-in-azure"></a>Självstudier: Ta bort ett Service Fabric-kluster som körs i Azure

Den här självstudien är del fem i en serie och visar hur du ta bort ett Service Fabric-kluster som körs i Azure. Om du vill ta bort ett Service Fabric-kluster måste du även ta bort alla resurser som används av klustret. Du har två alternativ: antingen tar du bort resursgruppen som klustret finns i (vilket tar bort klusterresursen och alla andra resurser i resursgruppen) eller så tar du bort klusterresursen specifikt och dess tillhörande resurser (men inte andra resurser i resursgruppen).

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Ta bort en resursgrupp och alla dess resurser
> * Ta bort resurser selektivt från en resursgrupp

I den här självstudieserien får du lära du dig att:
> [!div class="checklist"]
> * Skapa ett säkert [Windows-kluster](service-fabric-tutorial-create-vnet-and-windows-cluster.md) i Azure med hjälp av en mall
> * [Övervaka ett kluster](service-fabric-tutorial-monitor-cluster.md)
> * [skala upp eller ned ett kluster](service-fabric-tutorial-scale-cluster.md)
> * [uppgradera körningen för ett kluster](service-fabric-tutorial-upgrade-cluster.md)
> * Ta bort ett kluster


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Nödvändiga komponenter

Innan du börjar den här självstudien:

* om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Installera [Azure Powershell](https://docs.microsoft.com/powershell/azure//install-Az-ps) eller [Azure CLI](/cli/azure/install-azure-cli).
* Skapa ett säkert [Windows-kluster](service-fabric-tutorial-create-vnet-and-windows-cluster.md) i Azure

## <a name="delete-the-resource-group-containing-the-service-fabric-cluster"></a>Ta bort resursgruppen som innehåller Service Fabric-klustret
Det enklaste sättet att ta bort klustret och alla de resurser det använder är att ta bort resursgruppen.

Logga in på Azure och välj prenumerations-ID som du vill ta bort klustret.  Du hittar ditt prenumerations-ID genom att logga in på [Azure Portal](https://portal.azure.com). Ta bort resursgruppen och alla klusterresurser med den [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) cmdlet eller [az group delete](/cli/azure/group?view=azure-cli-latest) kommando.

```powershell
Connect-AzAccount
Set-AzContext -SubscriptionId <guid>
$groupname = "sfclustertutorialgroup"
Remove-AzResourceGroup -Name $groupname -Force
```

```azurecli
az login
az account set --subscription <guid>
ResourceGroupName="sfclustertutorialgroup"
az group delete --name $ResourceGroupName
```

## <a name="selectively-delete-the-cluster-resource-and-the-associated-resources"></a>Ta bort klusterresursen och de associerade resurserna selektivt
Om resursgruppen endast innehåller resurser som är associerade med Service Fabric-klustret som du vill ta bort, är det enklare att ta bort hela resursgruppen. Om du vill ta bort resursen i resursgruppen selektivt, och behålla alla resurser i resursgruppen som inte är associerade med klustret, följer du dessa steg.

Visa en lista över resurserna i resursgruppen:

```powershell
Connect-AzAccount
Set-AzContext -SubscriptionId <guid>
$groupname = "sfclustertutorialgroup"
Get-AzResource -ResourceGroupName $groupname | ft
```

```azurecli
az login
az account set --subscription <guid>
ResourceGroupName="sfclustertutorialgroup"
az resource list --resource-group $ResourceGroupName
```

Kör följande skript för varje resurs som du vill ta bort:

```powershell
Remove-AzResource -ResourceName "<name of the Resource>" -ResourceType "<Resource Type>" -ResourceGroupName $groupname -Force
```

```azurecli
az resource delete --name "<name of the Resource>" --resource-type "<Resource Type>" --resource-group $ResourceGroupName
```

Kör följande skript om du vill ta bort klusterresursen:

```powershell
Remove-AzResource -ResourceName "<name of the Resource>" -ResourceType "Microsoft.ServiceFabric/clusters" -ResourceGroupName $groupname -Force
```

```azurecli
az resource delete --name "<name of the Resource>" --resource-type "Microsoft.ServiceFabric/clusters" --resource-group $ResourceGroupName
```

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Ta bort en resursgrupp och alla dess resurser
> * Ta bort resurser selektivt från en resursgrupp

Nu när du har slutfört den här självstudien kan du prova följande:
* Lär dig hur du granskar och hanterar ett Service Fabric-kluster med [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).
* Lär dig hur du [uppdaterar Windows-operativsystemet](service-fabric-patch-orchestration-application.md) för klusternoderna.
* Lär dig hur du aggregerar och samlar in händelser för [Windows-kluster](service-fabric-diagnostics-event-aggregation-wad.md) och hur du [konfigurerar Log Analytics](service-fabric-diagnostics-oms-setup.md) för att övervaka klusterhändelser.
