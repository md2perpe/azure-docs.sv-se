---
title: Diagnostisera en virtuell dator problem med nätverksroutning – Azure PowerShell | Microsoft Docs
description: I den här artikeln får du lära dig hur du diagnostiserar en virtuell dator problem med nätverksroutning med nästa hopp-funktionen i Azure Network Watcher.
services: network-watcher
documentationcenter: network-watcher
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
Customer intent: I need to diagnose virtual machine (VM) network routing problem that prevents communication to different destinations.
ms.assetid: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: network-watcher
ms.workload: infrastructure
ms.date: 04/20/2018
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: 08d273ce6e6ecb1b10d3c39a0954d430a3cb674a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66730740"
---
# <a name="diagnose-a-virtual-machine-network-routing-problem---azure-powershell"></a>Diagnostisera en virtuell dator problem med nätverksroutning – Azure PowerShell

I den här artikeln får du distribuera en virtuell dator (VM) och kontrollera kommunikation till en IP-adress och URL: en. Du lär dig också hur du fastställer orsaken till ett kommunikationsfel och hur du löser problemet.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda PowerShell lokalt kräver den här artikeln Azure PowerShell- `Az` modulen. Kör `Get-Module -ListAvailable Az` för att hitta den installerade versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-Az-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Connect-AzAccount` för att skapa en anslutning till Azure.



## <a name="create-a-vm"></a>Skapa en virtuell dator

Innan du kan skapa en virtuell dator måste du skapa en resursgrupp som innehåller den virtuella datorn. Skapa en resursgrupp med [New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup). I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på platsen *eastus*.

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location EastUS
```

Skapa den virtuella datorn med hjälp av [New-AzVM](/powershell/module/az.compute/new-azvm). När du kör det här steget uppmanas du att ange autentiseringsuppgifter. De värden som du anger konfigureras som användarnamn och lösenord för den virtuella datorn.

```azurepowershell-interactive
$vM = New-AzVm `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVm" `
    -Location "East US"
```

Det tar några minuter att skapa den virtuella datorn. Fortsätt inte med de återstående stegen förrän den virtuella datorn har skapats och PowerShell returnerar utdata.

## <a name="test-network-communication"></a>Testa nätverkskommunikationen

Om du vill testa nätverkskommunikation med Network Watcher måste du först aktivera en network watcher i den region som den virtuella datorn som du vill testa har och sedan använda Network Watcher nästa hopp-funktionen för att testa kommunikation.

## <a name="enable-network-watcher"></a>Aktivera nätverksbevakare

Om du redan har en network watcher som aktiverats i regionen USA, östra, använda [Get-AzNetworkWatcher](/powershell/module/az.network/get-aznetworkwatcher) att hämta network watcher. I följande exempel hämtas en befintlig nätverksbevakare med namnet *NetworkWatcher_eastus* som finns i resursgruppen *NetworkWatcherRG*:

```azurepowershell-interactive
$networkWatcher = Get-AzNetworkWatcher `
  -Name NetworkWatcher_eastus `
  -ResourceGroupName NetworkWatcherRG
```

Om du inte redan har en network watcher som aktiverats i regionen östra USA kan du använda [New AzNetworkWatcher](/powershell/module/az.network/new-aznetworkwatcher) att skapa en network watcher i regionen östra USA:

```azurepowershell-interactive
$networkWatcher = New-AzNetworkWatcher `
  -Name "NetworkWatcher_eastus" `
  -ResourceGroupName "NetworkWatcherRG" `
  -Location "East US"
```

### <a name="use-next-hop"></a>Använda funktionen för nästa hopp

Azure skapar automatiskt vägar till olika standardmål. Du kan skapa egna vägar som ersätter standardvägarna. Ibland kan egna vägar göra att kommunikationen misslyckas. Testa routning från en virtuell dator med den [Get-AzNetworkWatcherNextHop](/powershell/module/az.network/get-aznetworkwatchernexthop) kommando för att fastställa nexthop routning när trafik är avsedd för en specifik adress.

Testa utgående kommunikation från den virtuella datorn till någon av IP-adresserna för www.bing.com:

```azurepowershell-interactive
Get-AzNetworkWatcherNextHop `
  -NetworkWatcher $networkWatcher `
  -TargetVirtualMachineId $VM.Id `
  -SourceIPAddress 192.168.1.4 `
  -DestinationIPAddress 13.107.21.200
```

Efter några sekunder utdata informerar dig som den **NextHopType** är **Internet**, och att den **RouteTableId** är **Systemväg**. Det här resultatet talar om att det finns en giltig väg till målet.

Testa utgående kommunikation från den virtuella datorn till 172.31.0.100:

```azurepowershell-interactive
Get-AzNetworkWatcherNextHop `
  -NetworkWatcher $networkWatcher `
  -TargetVirtualMachineId $VM.Id `
  -SourceIPAddress 192.168.1.4 `
  -DestinationIPAddress 172.31.0.100
```

Informerar dig om de utdata som returneras som **ingen** är den **NextHopType**, och att den **RouteTableId** är också **Systemväg**. Resultatet visar att det visserligen finns en giltig systemväg till målet, men att det inte finns något nästa hopp för att dirigera trafiken till målet.

## <a name="view-details-of-a-route"></a>Visa information om en väg

Om du vill analysera routning ytterligare, granska de effektiva vägarna för nätverksgränssnittet med den [Get-AzEffectiveRouteTable](/powershell/module/az.network/get-azeffectiveroutetable) kommando:

```azurepowershell-interactive
Get-AzEffectiveRouteTable `
  -NetworkInterfaceName myVm `
  -ResourceGroupName myResourceGroup |
  Format-table
```

Utdata som innehåller följande text returneras:

```powershell
Name State  Source  AddressPrefix           NextHopType NextHopIpAddress
---- -----  ------  -------------           ----------- ----------------
     Active Default {192.168.0.0/16}        VnetLocal   {}              
     Active Default {0.0.0.0/0}             Internet    {}              
     Active Default {10.0.0.0/8}            None        {}              
     Active Default {100.64.0.0/10}         None        {}              
     Active Default {172.16.0.0/12}         None        {}              
```

Som du ser i föregående utdata vägen med den **AddressPrefix** av **0.0.0.0/0** dirigerar all trafik som inte är avsedda för adresser inom annan väg adressprefix med ett nexthop för **Internet**. Som du ser också i utdata, men det finns en standardväg till 172.16.0.0/12 prefix, som innehåller 172.31.0.100 adressen, den **nextHopType** är **ingen**. Azure skapar en standardväg till 172.16.0.0/12 men anger inte en nästa hopptyp förrän det finns någon anledning till det. Om du till exempel att du lagt till adressintervallet 172.16.0.0/12 adressutrymmet för det virtuella nätverket, Azure ändras den **nextHopType** till **virtuellt nätverk** för vägen. En kontroll visas sedan **virtuellt nätverk** som den **nextHopType**.

## <a name="clean-up-resources"></a>Rensa resurser

När du inte längre behövs kan du använda [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) att ta bort resursgruppen och alla resurser den innehåller:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du skapat en virtuell dator och diagnostiseras nätverksroutning från den virtuella datorn. Du har lärt dig att Azure skapar flera standardvägar och testat routning till två olika mål. Läs mer om [routning i Azure](../virtual-network/virtual-networks-udr-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) och hur du [skapar egna vägar](../virtual-network/manage-route-table.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#create-a-route).

För utgående VM-anslutningar kan också bestämma svarstiden du och tillåtna respektive nekade nätverkstrafik mellan den virtuella datorn och en slutpunkt med Network Watchers [anslutningsfelsökning](network-watcher-connectivity-powershell.md) kapaciteten. Du kan övervaka kommunikation mellan en virtuell dator och en slutpunkt, till exempel en IP-adress eller URL: en, över tid med Network Watcher anslutning monitor-funktionen. Läs hur genom att läsa [övervaka en nätverksanslutning](connection-monitor.md).
