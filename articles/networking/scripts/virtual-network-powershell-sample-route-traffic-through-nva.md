---
title: Azure PowerShell-skriptexempel – Dirigera trafik via en virtuell nätverksinstallation | Microsoft Docs
description: Azure PowerShell-skriptexempel – Dirigera trafik via en virtuell nätverksinstallation för brandvägg.
services: virtual-network
documentationcenter: virtual-network
author: georgewallace
manager: timlt
editor: tysonn
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: powershell
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 05/16/2017
ms.author: gwallace
ms.openlocfilehash: 7470ef655e07e2eff2e80bfc104a1b42fa950a0e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66156800"
---
# <a name="route-traffic-through-a-network-virtual-appliance"></a>Dirigera trafik via en virtuell nätverksinstallation

Det här skriptexemplet skapar ett virtuellt nätverk med klient- och serverdelsundernät. Det skapar också en virtuell dator med aktiverad IP-vidarebefordran för att dirigera trafik mellan de två undernäten. När du kört skriptet kan du distribuera nätverksprogramvara, till exempel ett brandväggsprogram, till den virtuella datorn.

Om det behövs installerar du Azure PowerShell med hjälp av instruktionerna i [Azure PowerShell-guiden](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/) och kör sedan `Connect-AzAccount` för att skapa en anslutning till Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exempelskript

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../powershell_scripts/virtual-network/route-traffic-through-nva/route-traffic-through-nva.ps1 "Route traffic through a network virtual appliance")]

## <a name="clean-up-deployment"></a>Rensa distribution 

Kör följande kommando för att ta bort resursgruppen, den virtuella datorn och alla relaterade resurser.

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```
## <a name="script-explanation"></a>Förklaring av skript

I det här skriptet används följande kommandon för att skapa en resursgrupp, ett virtuellt nätverk och nätverkssäkerhetsgrupper. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)  | Skapar en resursgrupp där alla resurser lagras. |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Skapar ett virtuellt Azure-nätverk och klientdelsundernät. |
| [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) | Skapar serverdels- och DMZ-undernät. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Skapar en offentlig IP-adress för att komma åt den virtuella datorn från Internet. |
| [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) | Skapar ett virtuellt nätverksgränssnitt och aktiverar IP-vidarebefordran för det. |
| [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) | Skapar en nätverkssäkerhetsgrupp (NSG). |
| [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) | Skapar NSG-regler som tillåter inkommande HTTP- och HTTPS-portar till den virtuella datorn. |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig)| Associerar NSG:erna och routningstabellerna med undernät. |
| [New-AzRouteTable](/powershell/module/az.network/new-azroutetable)| Skapar en routningstabell för alla vägar. |
| [New-AzRouteConfig](/powershell/module/az.network/new-azrouteconfig)| Skapar vägar för att dirigera trafik mellan undernät och Internet via den virtuella datorn. |
| [New-AzVM](/powershell/module/az.compute/new-azvm) | Skapar en virtuell dator och kopplar nätverkskortet till den. Det här kommandot anger även den virtuella datoravbildning som ska användas samt administrativa autentiseringsuppgifter. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup)  | Tar bort en resursgrupp och alla resurser den innehåller. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell finns i [Azure PowerShell-dokumentationen](https://docs.microsoft.com/powershell/azure/overview).

Ytterligare exempel på PowerShell-nätverksskript finns i [dokumentation för översikt över Azure-nätverk](../powershell-samples.md?toc=%2fazure%2fnetworking%2ftoc.json).
