---
title: CLI-exempel – Virtuella datorer med lastbalanserare i en zon – Azure | Microsoft Docs
description: Det här CLI-skriptexemplet visar hur du belastningsutjämnar trafik till virtuella datorer inom en specifik tillgänglighetszon
services: load-balancer
documentationcenter: load-balancer
author: KumudD
manager: jeconnoc
editor: tysonn
tags: ''
Customer intent: As an IT administrator, I want to create a load balancer that load balances incoming internet traffic to virtual machines within a specific zone in a region.
ms.assetid: ''
ms.service: load-balancer
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 06/14/2018
ms.author: kumud
ms.openlocfilehash: 121d3f941f3d00373bd2821534c3e601107e97e6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60506968"
---
# <a name="azure-cli-script-example-load-balance-traffic-to-vms-for-high-availability"></a>Exempel för Azure CLI-skript: Belastningsutjämna trafik till virtuella datorer för hög tillgänglighet

Det här Azure CLI-skriptexemplet skapar allt som behövs för att köra flera virtuella Ubuntu-datorer, konfigurerade med hög tillgänglighet och belastningsutjämning inom en specifik tillgänglighetszon. När du har kört skriptet har du tre virtuella datorer i en enda tillgänglighetszon inom ett område som kan nås via en Azure Standard Load Balancer. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exempelskript

```azurecli-interactive
  #!/bin/bash

  # Create a resource group.
   az group create \
    --name myResourceGroup \
    --location westeurope

  # Create a virtual network.
   az network vnet create \
    --resource-group myResourceGroup \
    --location westeurope \
    --name myVnet \
    --subnet-name mySubnet

  # Create a zonal Standard public IP address.
   az network public-ip create \
    --resource-group myResourceGroup \
    --name myPublicIP \
    --sku Standard
    --zone 1

  # Create an Azure Load Balancer.
   az network lb create \
    --resource-group myResourceGroupLB \
    --name myLoadBalancer \
    --public-ip-address myPublicIP \
    --frontend-ip-name myFrontEndPool \
    --backend-pool-name myBackEndPool \
    --sku Standard

  # Creates an LB probe on port 80.
   az network lb probe create \
    --resource-group myResourceGroup \
    --lb-name myLoadBalancer \
    --name myHealthProbe \
    --protocol tcp \
    --port 80

  # Creates an LB rule for port 80.
   az network lb rule create \
    --resource-group myResourceGroup \
    --lb-name myLoadBalancer \
    --name myLoadBalancerRuleWeb \
    --protocol tcp \
    --frontend-port 80 \
    --backend-port 80 \
    --frontend-ip-name myFrontEndPool \
    --backend-pool-name myBackEndPool \
    --probe-name myHealthProbe

  # Create three NAT rules for port 22.
   for i in `seq 1 3`; do
    az network lb inbound-nat-rule create \
     --resource-group myResourceGroup \
     --lb-name myLoadBalancer \
     --name myLoadBalancerRuleSSH$i \
     --protocol tcp \
     --frontend-port 422$i \
     --backend-port 22 \
     --frontend-ip-name myFrontEndPool
   done

  # Create a network security group
   az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup

  # Create a network security group rule for port 22.
   az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name myNetworkSecurityGroupRuleSSH \
    --protocol tcp \
    --direction inbound \
    --source-address-prefix '*' \
    --source-port-range '*'  \
    --destination-address-prefix '*' \
    --destination-port-range 22 \
    --access allow \
    --priority 1000

  # Create a network security group rule for port 80.
    az network nsg rule create \
     --resource-group myResourceGroup \
     --nsg-name myNetworkSecurityGroup \
     --name myNetworkSecurityGroupRuleHTTP \
     --protocol tcp \
     --direction inbound \
     --source-address-prefix '*' \
     --source-port-range '*' \
     --destination-address-prefix '*' \
     --destination-port-range 80 \
     --access allow \
     --priority 2000

  # Create three virtual network cards and associate with public IP address and NSG.
  for i in `seq 1 3`; do
   az network nic create \
     --resource-group myResourceGroup \
     --name myNic$i \
     --vnet-name myVnet \
     --subnet mySubnet \
     --network-security-group myNetworkSecurityGroup \
     --lb-name myLoadBalancer \
     --lb-address-pools myBackEndPool \
     --lb-inbound-nat-rules myLoadBalancerRuleSSH$i
  done

# Create three virtual machines, this creates SSH keys if not present.
for i in `seq 1 3`; do
  az vm create \
    --resource-group myResourceGroup \
    --name myVM$i \
    --zone 1 \
    --nics myNic$i \
    --image UbuntuLTS \
    --generate-ssh-keys \
    --no-wait
done

```

## <a name="clean-up-deployment"></a>Rensa distribution 

Kör följande kommando för att ta bort resursgruppen, den virtuella datorn och alla relaterade resurser.

```azurecli
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Förklaring av skript

I det här skriptet används följande kommandon för att skapa en resursgrupp, virtuell dator, tillgänglighetsuppsättning, lastbalanserare och alla relaterade resurser. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az-group-create) | Skapar en resursgrupp där alla resurser lagras. |
| [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet#az-network-vnet-create) | Skapar ett virtuellt Azure-nätverk och undernät. |
| [az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip#az-network-public-ip-create) | Skapar en offentlig IP-adress med en statisk IP-adress och ett tillhörande DNS-namn. |
| [az network lb create](https://docs.microsoft.com/cli/azure/network/lb#az-network-lb-create) | Skapar en Azure-lastbalanserare. |
| [az network lb probe create](https://docs.microsoft.com/cli/azure/network/lb/probe#az-network-lb-probe-create) | Skapar en lastbalanseringsavsökning. En lastbalanseringsavsökning används för att övervaka varje virtuell dator i lastbalanseringsuppsättningen. Om en virtuell dator blir otillgänglig dirigeras trafiken förbi den. |
| [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule#az-network-lb-rule-create) | Skapar en lastbalanseringsregel. I det här exemplet skapas en regel för port 80. När HTTP-trafik anländer vid lastbalanseraren dirigeras den till port 80, en av de virtuella datorerna i lastbalanseringsuppsättningen. |
| [az network lb inbound-nat-rule create](https://docs.microsoft.com/cli/azure/network/lb/inbound-nat-rule#az-network-lb-inbound-nat-rule-create) | Skapar en lastbalanseringsregel för NAT (Network Address Translation).  NAT-regler mappar en port för lastbalanseraren till en port på en virtuell dator. I det här exemplet skapas en NAT-regel för SSH-trafik till varje virtuell dator i lastbalanseringsuppsättningen.  |
| [az network nsg create](https://docs.microsoft.com/cli/azure/network/nsg#az-network-nsg-create) | Skapar en nätverkssäkerhetsgrupp (NSG), som är en säkerhetsgräns mellan internet och den virtuella datorn. |
| [az network nsg rule create](https://docs.microsoft.com/cli/azure/network/nsg/rule#az-network-nsg-rule-create) | Skapar en NSG-regel för att tillåta inkommande trafik. I det här exemplet öppnas port 22 för SSH-trafik. |
| [az network nic create](https://docs.microsoft.com/cli/azure/network/nic#az-network-nic-create) | Skapar ett virtuellt nätverkskort och ansluter det till det virtuella nätverket, undernätet och NSG. |
| [az vm create](/cli/azure/vm#az-vm-create) | Skapar den virtuella datorn och ansluter den till nätverkskortet, ett virtuellt nätverk, ett undernät och en NSG. Kommandot specificerar även vilken avbildning av virtuell dator som ska användas samt administrativa autentiseringsuppgifter.  |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#az-vm-extension-set) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](https://docs.microsoft.com/cli/azure).

Ytterligare exempel på Azure Networking CLI-skript finns i [Azure Networking-dokumentationen](../cli-samples.md).