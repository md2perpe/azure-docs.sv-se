---
title: Snabbstart – distribuera Docker-behållare till Azure Container Instances – PowerShell
description: I den här snabbstarten använder du Azure PowerShell till att snabbt distribuera en behållarbaserad webbapp som körs i en isolerad Azure container-instans
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: quickstart
ms.date: 03/21/2019
ms.author: danlep
ms.custom: seodec18, mvc
ms.openlocfilehash: c6baf10308f04d5f08ba651bd70ac2b48dfc013c
ms.sourcegitcommit: 1aefdf876c95bf6c07b12eb8c5fab98e92948000
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/06/2019
ms.locfileid: "66729455"
---
# <a name="quickstart-deploy-a-container-instance-in-azure-using-azure-powershell"></a>Snabbstart: Distribuera en behållarinstans i Azure med hjälp av Azure PowerShell

Använd Azure Container Instances för att köra serverlös Docker-behållare i Azure med enkelhet och hastighet. Distribuera ett program till en container-instans på begäran när du inte behöver en fullständig behållarplattform orchestration som Azure Kubernetes Service.

I den här snabbstarten använder du Azure PowerShell för att distribuera en isolerad Windows-behållare och tillgängliggöra dess program med ett fullständigt kvalificerat domännamn (FQDN). Några sekunder efter du kör en enkel distribution-kommando som du kan bläddra till det program som körs i behållaren:

![App som distribuerats via Azure Container Instances visas i webbläsaren][qs-powershell-01]

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda PowerShell lokalt behöver du ha Azure PowerShell-modulen för den här självstudien. Kör `Get-Module -ListAvailable Az` för att hitta versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-Az-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Connect-AzAccount` för att skapa en anslutning till Azure.

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Azure-containerinstanser måste, precis som alla Azure-resurser, distribueras i en resursgrupp. Resursgrupper gör det enkelt att organisera och hantera relaterade Azure-resurser.

Skapa först en resursgrupp med namnet *myResourceGroup* på platsen *eastus* (USA, östra) med följande kommando [New-AzResourceGroup][New-AzResourceGroup]:

 ```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-a-container"></a>Skapa en container

Nu när du har en resursgrupp kan du köra en container i Azure. Om du vill skapa en containerinstans med Azure PowerShell anger du ett resursgruppsnamn, ett instansnamn för containern och avbildningen av dockercontainern till cmdleten [New-AzContainerGroup][New-AzContainerGroup]. I den här snabbstarten använder du offentligt `mcr.microsoft.com/windows/servercore/iis:nanoserver` bild. Den här bilden Microsoft Internet Information Services (IIS) för att köra i Nano Server-paket.

Du kan exponera dina containrar till internet genom att ange en eller flera portar som ska öppnas, en DNS-namnetikett eller båda. I den här snabbstarten distribuerar du en container med en DNS-namnetikett så att IIS kan nås offentligt.

Köra ett kommando som liknar följande att starta en behållarinstans. Ange en `-DnsNameLabel` värde som är unikt i Azure-regionen där du skapar instansen. Om du får felmeddelandet ”DNS name label not available” (DNS-namnetikett inte tillgänglig) kan du prova en annan DNS-namnetikett.

 ```azurepowershell-interactive
New-AzContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer -Image mcr.microsoft.com/windows/servercore/iis:nanoserver -OsType Windows -DnsNameLabel aci-demo-win
```

Inom några sekunder bör du få ett första svar från Azure. Containerns `ProvisioningState` är initialt **Skapa**, men bör flyttas till **Lyckades** inom en minut eller två. Kontrollera distributionsstatusen med hjälp av cmdleten [Get-AzContainerGroup][Get-AzContainerGroup]:

 ```azurepowershell-interactive
Get-AzContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer
```

Containerns etableringsstatus, fullständiga kvalificerade domännamn (FQDN) och IP-adress visas i cmdletens utdata:

```console
PS Azure:\> Get-AzContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer


ResourceGroupName        : myResourceGroup
Id                       : /subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ContainerInstance/containerGroups/mycontainer
Name                     : mycontainer
Type                     : Microsoft.ContainerInstance/containerGroups
Location                 : eastus
Tags                     :
ProvisioningState        : Creating
Containers               : {mycontainer}
ImageRegistryCredentials :
RestartPolicy            : Always
IpAddress                : 52.226.19.87
DnsNameLabel             : aci-demo-win
Fqdn                     : aci-demo-win.eastus.azurecontainer.io
Ports                    : {80}
OsType                   : Windows
Volumes                  :
State                    : Pending
Events                   : {}
```

När containerns `ProvisioningState` är **Lyckades** navigerar du till dess `Fqdn` i webbläsaren. Om du ser en webbsida som liknar följande – grattis! Du har distribuerat ett program som körs i en dockercontainer till Azure.

![IIS som distribuerats via Azure Container Instances visas i webbläsaren][qs-powershell-01]

## <a name="clean-up-resources"></a>Rensa resurser

När du är klar med containern kan du ta bort den med cmdleten [Remove-AzContainerGroup][Remove-AzContainerGroup]:

 ```azurepowershell-interactive
Remove-AzContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer
```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du skapat en Azure-containerinstans utifrån en avbildning som finns i det offentliga Docker Hub-registret. Om du vill skapa en container på egen hand och distribuera den från ett privat Azure-containerregister går du vidare till självstudien för Azure Container Instances.

> [!div class="nextstepaction"]
> [Azure Container Instances-självstudie](./container-instances-tutorial-prepare-app.md)

<!-- IMAGES -->
[qs-powershell-01]: ./media/container-instances-quickstart-powershell/qs-powershell-01.png

<!-- LINKS -->
[New-AzResourceGroup]: /powershell/module/az.resources/new-Azresourcegroup
[New-AzContainerGroup]: /powershell/module/az.containerinstance/new-Azcontainergroup
[Get-AzContainerGroup]: /powershell/module/az.containerinstance/get-Azcontainergroup
[Remove-AzContainerGroup]: /powershell/module/az.containerinstance/remove-Azcontainergroup
