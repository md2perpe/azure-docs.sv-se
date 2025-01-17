---
title: Skapa en zonindelat Linux-VM med Azure CLI | Microsoft Docs
description: Skapa en Linux-VM i en tillgänglighetszon med Azure CLI
services: virtual-machines-linux
documentationcenter: virtual-machines
author: dlepow
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/05/2018
ms.author: danlep
ms.custom: ''
ms.openlocfilehash: cdd9910bfef96f56cfa8c8e81363ff9bdb40f444
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60328739"
---
# <a name="create-a-linux-virtual-machine-in-an-availability-zone-with-the-azure-cli"></a>Skapa en Linux-dator i en tillgänglighetszon med Azure CLI

Den här artikeln beskriver hur använder Azure CLI för att skapa en Linux-VM i en Azure-tillgänglighetszon. En [tillgänglighetszon](../../availability-zones/az-overview.md) är en fysiskt separat zon i en Azure-region. Använd tillgänglighetszoner för att skydda dina appar och data från ett osannolikt fel eller förlust av ett helt datacenter.

Om du vill använda en tillgänglighetszon skapar du din virtuella dator i en [Azure-region som stöds](../../availability-zones/az-overview.md#services-support-by-region).

Se till att du har installerat senast [Azure CLI](/cli/azure/install-az-cli2) och inloggad på ett Azure-konto med [az-inloggning](/cli/azure/reference-index).


## <a name="check-vm-sku-availability"></a>Kontrollera tillgänglighet för SKU för virtuell dator
Tillgängligheten för VM-storlek eller SKU: er kan variera beroende på region och zon. När du planerar för användningen av tillgänglighetszoner kan du visa tillgängliga VM SKU:er via Azure-region och zon. Den möjligheten säkerställer att du väljer en lämplig VM-storlek och hämtar önskad elasticitet i flera zoner. Mer information om olika VM-typer och -storlekar finns i [Översikt över VM-storlekar](sizes.md).

Du kan visa de tillgängliga VM SKU: er med den [az vm list-skus](/cli/azure/vm) kommando. Följande exempel visar tillgängliga VM SKU:er i regionen *usaöstra2*:

```azurecli
az vm list-skus --location eastus2 --output table
```

Utdata liknar följande komprimerade exempel som visar tillgänglighetszoner som varje VM-storlek är tillgänglig i:

```azurecli
ResourceType      Locations  Name               [...]    Tier       Size     Zones
----------------  ---------  -----------------           ---------  -------  -------
virtualMachines   eastus2    Standard_DS1_v2             Standard   DS1_v2   1,2,3
virtualMachines   eastus2    Standard_DS2_v2             Standard   DS2_v2   1,2,3
[...]
virtualMachines   eastus2    Standard_F1s                Standard   F1s      1,2,3
virtualMachines   eastus2    Standard_F2s                Standard   F2s      1,2,3
[...]
virtualMachines   eastus2    Standard_D2s_v3             Standard   D2_v3    1,2,3
virtualMachines   eastus2    Standard_D4s_v3             Standard   D4_v3    1,2,3
[...]
virtualMachines   eastus2    Standard_E2_v3              Standard   E2_v3    1,2,3
virtualMachines   eastus2    Standard_E4_v3              Standard   E4_v3    1,2,3
```


## <a name="create-resource-group"></a>Skapa resursgrupp

Skapa en resursgrupp med kommandot [az group create](/cli/azure/group).  

En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras. En resursgrupp måste skapas före den virtuella datorn. I det här exemplet, en resursgrupp med namnet *myResourceGroupVM* skapas i den *usaöstra2* region. Östra USA 2 är en av de Azure-regioner som har stöd för tillgänglighetszoner.

```azurecli 
az group create --name myResourceGroupVM --location eastus2
```

Resursgruppens namn anges när du skapar eller ändrar en virtuell dator som du kan se i den här artikeln.

## <a name="create-virtual-machine"></a>Skapa en virtuell dator

Skapa en virtuell dator med kommandot [az vm create](/cli/azure/vm). 

När du skapar en virtuell dator finns flera tillgängliga alternativ, som t.ex. avbildning av operativsystemet, bestämning av diskstorlek och administrativa autentiseringsuppgifter. I det här exemplet skapas en virtuell dator med namnet *myVM* som kör Ubuntu Server. Den virtuella datorn skapas i tillgänglighetszon *1*. Som standard skapas den virtuella datorn i den *Standard_DS1_v2* storlek.

```azurecli-interactive 
az vm create --resource-group myResourceGroupVM --name myVM --location eastus2 --image UbuntuLTS --generate-ssh-keys --zone 1
```

Det kan ta några minuter att skapa den virtuella datorn. När den virtuella datorn skapats visar Azure CLI information om den virtuella datorn. Anteckna den `zones` värde som anger den tillgänglighetszon som Virtuellt datorn körs. 

```azurecli 
{
  "fqdns": "",
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroupVM/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus2",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "52.174.34.95",
  "resourceGroup": "myResourceGroupVM",
  "zones": "1"
}
```

## <a name="confirm-zone-for-managed-disk-and-ip-address"></a>Bekräfta zon för hanterad disk och IP-adress

När den virtuella datorn har distribuerats i en tillgänglighetszon, skapas en hanterad disk för den virtuella datorn i samma tillgänglighetszon. Som standard skapas också en offentlig IP-adress i zonen. I följande exempel få information om dessa resurser.

Kontrollera att den Virtuella datorns hanterad disk i tillgänglighetszon genom att använda den [az vm show](/cli/azure/vm) kommando för att returnera disk-ID. I det här exemplet lagras disk-ID i en variabel som används i ett senare steg. 

```azurecli-interactive
osdiskname=$(az vm show -g myResourceGroupVM -n myVM --query "storageProfile.osDisk.name" -o tsv)
```
Nu kan du få information om hanterade diskar:

```azurecli-interactive
az disk show --resource-group myResourceGroupVM --name $osdiskname
```

Utdata visar att de hanterade diskarna är i samma tillgänglighetszon som den virtuella datorn:

```azurecli
{
  "creationData": {
    "createOption": "FromImage",
    "imageReference": {
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/Providers/Microsoft.Compute/Locations/westeurope/Publishers/Canonical/ArtifactTypes/VMImage/Offers/UbuntuServer/Skus/16.04-LTS/Versions/latest",
      "lun": null
    },
    "sourceResourceId": null,
    "sourceUri": null,
    "storageAccountId": null
  },
  "diskSizeGb": 30,
  "encryptionSettings": null,
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroupVM/providers/Microsoft.Compute/disks/osdisk_761c570dab",
  "location": "eastus2",
  "managedBy": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroupVM/providers/Microsoft.Compute/virtualMachines/myVM",
  "name": "myVM_osdisk_761c570dab",
  "osType": "Linux",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroupVM",
  "sku": {
    "name": "Premium_LRS",
    "tier": "Premium"
  },
  "tags": {},
  "timeCreated": "2018-03-05T22:16:06.892752+00:00",
  "type": "Microsoft.Compute/disks",
  "zones": [
    "1"
  ]
}
```

Använd den [az vm list-ip-adresser](/cli/azure/vm) kommando för att returnera namnet på offentlig IP-adressresurs i *myVM*. I det här exemplet har namnet lagras i en variabel som används i ett senare steg.

```azurecli
ipaddressname=$(az vm list-ip-addresses -g myResourceGroupVM -n myVM --query "[].virtualMachine.network.publicIpAddresses[].name" -o tsv)
```

Nu kan du få information om IP-adress:

```azurecli
az network public-ip show --resource-group myResourceGroupVM --name $ipaddressname
```

Utdata visar att IP-adressen är i samma tillgänglighetszon som den virtuella datorn:

```azurecli
{
  "dnsSettings": null,
  "etag": "W/\"b7ad25eb-3191-4c8f-9cec-c5e4a3a37d35\"",
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroupVM/providers/Microsoft.Network/publicIPAddresses/myVMPublicIP",
  "idleTimeoutInMinutes": 4,
  "ipAddress": "52.174.34.95",
  "ipConfiguration": {
    "etag": null,
    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroupVM/providers/Microsoft.Network/networkInterfaces/myVMVMNic/ipConfigurations/ipconfigmyVM",
    "name": null,
    "privateIpAddress": null,
    "privateIpAllocationMethod": null,
    "provisioningState": null,
    "publicIpAddress": null,
    "resourceGroup": "myResourceGroupVM",
    "subnet": null
  },
  "location": "eastUS2",
  "name": "myVMPublicIP",
  "provisioningState": "Succeeded",
  "publicIpAddressVersion": "IPv4",
  "publicIpAllocationMethod": "Dynamic",
  "resourceGroup": "myResourceGroupVM",
  "resourceGuid": "8c70a073-09be-4504-0000-000000000000",
  "tags": {},
  "type": "Microsoft.Network/publicIPAddresses",
  "zones": [
    "1"
  ]
}
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du skapar en virtuell dator i en tillgänglighetszon. Läs mer om [regioner och tillgänglighet](regions-and-availability.md) för virtuella Azure-datorer.




