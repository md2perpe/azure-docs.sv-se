---
title: Skriptexempel för Azure CLI – Skapa en virtuell Linux-dator med NLB | Microsoft Docs
description: Skriptexempel för Azure CLI – Skapa en virtuell Linux-dator med NLB
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/27/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 2e9b536ae6c7056348ac964a2c85ccd698a80ae4
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2019
ms.locfileid: "67709373"
---
# <a name="create-a-highly-available-vm"></a>Skapa en virtuell dator med hög tillgänglighet

Det här skriptexemplet skapar allt som behövs för att köra flera virtuella Ubuntu-datorer, konfigurerade med hög tillgänglighet och belastningsutjämning. När du har kört skriptet har du tre virtuella datorer som är anslutna till en Azure-tillgänglighetsuppsättning och är tillgängliga via Azure Load Balancer.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exempelskript

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-vm-nlb/create-vm-nlb.sh "Quick Create VM")]

## <a name="clean-up-deployment"></a>Rensa distribution

Kör följande kommando för att ta bort resursgruppen, den virtuella datorn och alla relaterade resurser.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Förklaring av skript

I det här skriptet används följande kommandon för att skapa en resursgrupp, virtuell dator, tillgänglighetsuppsättning, lastbalanserare och alla relaterade resurser. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group) | Skapar en resursgrupp där alla resurser lagras. |
| [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet) | Skapar ett virtuellt Azure-nätverk och undernät. |
| [az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip) | Skapar en offentlig IP-adress med en statisk IP-adress och ett tillhörande DNS-namn. |
| [az network lb create](https://docs.microsoft.com/cli/azure/network/lb) | Skapar en Azure NLB (nätverkslastbalanserare). |
| [az network lb probe create](https://docs.microsoft.com/cli/azure/network/lb/probe) | Skapar en NLB-probe. En NLB-probe används för att övervaka de enskilda virtuella datorerna i NLB-uppsättningen. Om en virtuell dator blir otillgänglig dirigeras trafiken förbi den. |
| [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule) | Skapar en NLB-regel. I det här exemplet skapas en regel för port 80. När HTTP-trafik kommer in till NLB dirigeras den till port 80 på någon av de virtuella datorerna i NLB-uppsättningen. |
| [az network lb inbound-nat-rule create](https://docs.microsoft.com/cli/azure/network/lb/inbound-nat-rule) | Skapar en NAT-regel (Network Address Translation) för NLB.  NAT-reglerna mappar en port från NLB till en port på en virtuell dator. I det här exemplet skapas en NAT-regel för SSH-trafik till varje virtuell dator i NLB-uppsättningen.  |
| [az network nsg create](https://docs.microsoft.com/cli/azure/network/nsg) | Skapar en nätverkssäkerhetsgrupp (NSG), som är en säkerhetsgräns mellan internet och den virtuella datorn. |
| [az network nsg rule create](https://docs.microsoft.com/cli/azure/network/nsg/rule) | Skapar en NSG-regel för att tillåta inkommande trafik. I det här exemplet öppnas port 22 för SSH-trafik. |
| [az network nic create](https://docs.microsoft.com/cli/azure/network/nic) | Skapar ett virtuellt nätverkskort och ansluter det till det virtuella nätverket, undernätet och NSG. |
| [az vm availability-set create](https://docs.microsoft.com/cli/azure/network/lb/rule) | Skapar en tillgänglighetsuppsättning. Tillgänglighetsuppsättningar garanterar programmets drifttid genom att fördela virtuella datorer mellan fysiska resurser så att hela uppsättningen inte berörs om något fel inträffar. |
| [az vm create](https://docs.microsoft.com/cli/azure/vm/availability-set) | Skapar den virtuella datorn och ansluter den till nätverkskortet, ett virtuellt nätverk, ett undernät och en NSG. Kommandot specificerar även vilken avbildning av virtuell dator som ska användas samt administrativa autentiseringsuppgifter.  |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](https://docs.microsoft.com/cli/azure).

Ytterligare CLI-skriptexempel för virtuella datorer finns i [dokumentationen för virtuella Azure Linux-datorer](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
