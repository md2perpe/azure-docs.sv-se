---
title: PowerShell-exempel – belastningsutjämna trafik till virtuella datorer för hög tillgänglighet – Azure
titlesuffix: Azure Load Balancer
description: Det här Azure PowerShell-skriptexemplet visar hur du belastningsutjämnar trafik till virtuella datorer för hög tillgänglighet
services: load-balancer
documentationcenter: load-balancer
author: KumudD
ms.service: load-balancer
ms.devlang: powershell
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 04/20/2018
ms.author: kumud
ms.openlocfilehash: 70752f9726ac78ec32d0735dfefe37675ae7be29
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "66170650"
---
# <a name="azure-powershell-script-example-load-balance-traffic-to-vms-for-high-availability"></a>Azure PowerShell-skriptexempel: Belastningsutjämna trafik till virtuella datorer för hög tillgänglighet

Det här Azure PowerShell-skriptexemplet skapar allt som behövs för att köra flera virtuella Windows-datorer, konfigurerade med hög tillgänglighet och belastningsutjämning. När du har kört skriptet har du tre virtuella datorer som är anslutna till en Azure-tillgänglighetsuppsättning och är tillgängliga via Azure Load Balancer.

Om det behövs installerar du Azure PowerShell med hjälp av instruktionerna i [Azure PowerShell-guiden](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/) och kör sedan `Connect-AzAccount` för att skapa en anslutning till Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exempelskript

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-vm-nlb/create-vm-nlb.ps1 "Quick Create VM")]

## <a name="clean-up-deployment"></a>Rensa distribution 

Kör följande kommando för att ta bort resursgruppen, den virtuella datorn och alla relaterade resurser.

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Förklaring av skript

I det här skriptet används följande kommandon för att skapa en resursgrupp, virtuell dator, tillgänglighetsuppsättning, lastbalanserare och alla relaterade resurser. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Skapar en resursgrupp där alla resurser lagras. |
| [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) | Skapar en undernätskonfiguration. Den här konfigurationen används med skapandeprocessen för virtuella nätverk. |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Skapar ett virtuellt Azure-nätverk och undernät. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress)  | Skapar en offentlig IP-adress med en statisk IP-adress och ett tillhörande DNS-namn. |
| [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer)  | Skapar en Azure-lastbalanserare. |
| [New-AzLoadBalancerProbeConfig](/powershell/module/az.network/new-azloadbalancerprobeconfig) | Skapar en lastbalanseringsavsökning. En lastbalanseringsavsökning används för att övervaka varje virtuell dator i lastbalanseringsuppsättningen. Om en virtuell dator blir otillgänglig dirigeras trafiken förbi den. |
| [New-AzLoadBalancerRuleConfig](/powershell/module/az.network/new-azloadbalancerruleconfig) | Skapar en lastbalanseringsregel. I det här exemplet skapas en regel för port 80. När HTTP-trafik anländer vid lastbalanseraren dirigeras den till port 80, en av de virtuella datorerna i lastbalanseringsuppsättningen. |
| [New-AzLoadBalancerInboundNatRuleConfig](/powershell/module/az.network/new-azloadbalancerinboundnatruleconfig) | Skapar en lastbalanseringsregel för NAT (Network Address Translation).  NAT-regler mappar en port för lastbalanseraren till en port på en virtuell dator. I det här exemplet skapas en NAT-regel för SSH-trafik till varje virtuell dator i lastbalanseringsuppsättningen.  |
| [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) | Skapar en nätverkssäkerhetsgrupp (NSG), som är en säkerhetsgräns mellan internet och den virtuella datorn. |
| [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) | Skapar en NSG-regel för att tillåta inkommande trafik. I det här exemplet öppnas port 22 för SSH-trafik. |
| [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) | Skapar ett virtuellt nätverkskort och ansluter det till det virtuella nätverket, undernätet och NSG. |
| [New-AzAvailabilitySet](/powershell/module/az.compute/new-azavailabilityset) | Skapar en tillgänglighetsuppsättning. Tillgänglighetsuppsättningar garanterar programmets drifttid genom att fördela virtuella datorer mellan fysiska resurser så att hela uppsättningen inte berörs om något fel inträffar. |
| [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig) | Skapar en virtuell datorkonfiguration. Den här konfigurationen omfattar information som virtuellt datornamn, operativsystem och administrativa autentiseringsuppgifter. Konfigurationen används vid skapande av virtuell dator. |
| [New-AzVM](/powershell/module/az.compute/new-azvm)  | Skapar den virtuella datorn och ansluter den till nätverkskortet, ett virtuellt nätverk, ett undernät och en NSG. Kommandot specificerar även vilken avbildning av virtuell dator som ska användas samt administrativa autentiseringsuppgifter.  |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell finns i [Azure PowerShell-dokumentationen](https://docs.microsoft.com/powershell/azure/overview).

Ytterligare exempel på PowerShell-nätverksskript finns i [dokumentation för översikt över Azure-nätverk](../powershell-samples.md?toc=%2fazure%2fnetworking%2ftoc.json).
