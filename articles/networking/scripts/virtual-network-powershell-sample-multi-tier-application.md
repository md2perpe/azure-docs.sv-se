---
title: Azure PowerShell-skriptexempel – Skapa ett nätverk för flernivåprogram | Microsoft Docs
description: Azure PowerShell-skriptexempel – Skapa ett virtuellt nätverk för flernivåprogram.
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
ms.openlocfilehash: d5433cdd6e6702a289fe0a4b6eab87e9f196fb1c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66156904"
---
# <a name="create-a-network-for-multi-tier-applications"></a>Skapa ett nätverk för flernivåprogram

Det här skriptexemplet skapar ett virtuellt nätverk med klient- och serverdelsundernät. Trafik till klientdelsundernätet är begränsad till HTTP och SSH, medan trafik till serverdelsundernätet är begränsad till MySQL, port 3306. När du kört skriptet har du två virtuella datorer, en i varje undernät, som du kan distribuera webbservern och MySQL-programvaran till.

Om det behövs installerar du Azure PowerShell med hjälp av instruktionerna i [Azure PowerShell-guiden](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/) och kör sedan `Connect-AzAccount` för att skapa en anslutning till Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exempelskript

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../powershell_scripts/virtual-network/virtual-network-multi-tier-application/virtual-network-multi-tier-application.ps1  "Virtual network for multi-tier application")]

## <a name="clean-up-deployment"></a>Rensa distribution 

Kör följande kommando för att ta bort resursgruppen, den virtuella datorn och alla relaterade resurser.

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Förklaring av skript

I det här skriptet används följande kommandon för att skapa en resursgrupp, ett virtuellt nätverk och nätverkssäkerhetsgrupper. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Skapar en resursgrupp där alla resurser lagras. |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Skapar ett virtuellt Azure-nätverk och klientdelsundernät. |
| [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) | Skapar ett serverdelsundernät. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Skapar en offentlig IP-adress för att komma åt den virtuella datorn från Internet. |
| [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) | Skapar virtuella nätverksgränssnitt och kopplar dem till det virtuella nätverkets klient- och serverdelsundernät. |
| [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) | Skapar nätverkssäkerhetsgrupper (NSG) som är kopplade till klient- och serverdelsundernäten. |
| [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) |Skapar NSG-regler som tillåter eller blockerar specifika portar till specifika undernät. |
| [New-AzVM](/powershell/module/az.compute/new-azvm) | Skapar virtuella datorer och kopplar ett nätverkskort till varje virtuell dator. Det här kommandot anger även den virtuella datoravbildning som ska användas samt administrativa autentiseringsuppgifter. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Tar bort en resursgrupp och alla resurser den innehåller. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell finns i [Azure PowerShell-dokumentationen](https://docs.microsoft.com/powershell/azure/overview).

Ytterligare exempel på PowerShell-nätverksskript finns i [dokumentation för översikt över Azure-nätverk](../powershell-samples.md?toc=%2fazure%2fnetworking%2ftoc.json).
