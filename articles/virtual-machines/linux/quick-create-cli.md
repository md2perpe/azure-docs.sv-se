---
title: Snabbstart–-Skapa en virtuell Linux-dator med Azure CLI | Microsoft Docs
description: I den här snabbstarten lär du dig hur du använder Azure CLI för att skapa en virtuell Linux-dator
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/09/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 1335f4abebffaf8ae0034ec023d2a988be1c39eb
ms.sourcegitcommit: 6cb4dd784dd5a6c72edaff56cf6bcdcd8c579ee7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2019
ms.locfileid: "67514522"
---
# <a name="quickstart-create-a-linux-virtual-machine-with-the-azure-cli"></a>Snabbstart: Skapa en virtuell Linux-dator med Azure CLI

Azure CLI används för att skapa och hantera Azure-resurser från kommandoraden eller i skript. I den här snabbstarten beskrivs hur du använder Azure CLI för att distribuera en virtuell dator (VM) med Linux i Azure. I den här självstudien installerar vi Ubuntu 16.04 LTS. För att kunna se hur den virtuella datorn fungerar i praktiken ansluter du den till SSH och installerar NGINX-webbservern.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="launch-azure-cloud-shell"></a>Starta Azure Cloud Shell

Azure Cloud Shell är ett interaktivt gränssnitt som du kan använda för att utföra stegen i den här artikeln. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto. 

Om du vill öppna Cloud Shell väljer du bara **Prova** från det övre högra hörnet i ett kodblock. Du kan också starta Cloud Shell i en separat webbläsarflik genom att gå till [https://shell.azure.com/bash](https://shell.azure.com/bash). Kopiera kodblocket genom att välja **Kopiera**, klistra in det i Cloud Shell och kör det genom att trycka på RETUR.

Om du föredrar att installera och använda detta CLI lokalt måste du köra Azure CLI version 2.0.30 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en resursgrupp med kommandot [az group create](/cli/azure/group). En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras. I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på platsen *eastus*:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-virtual-machine"></a>Skapa en virtuell dator

Skapa en virtuell dator med kommandot [az vm create](/cli/azure/vm).

Följande exempel skapar virtuell dator med namnet *myVM* och lägger till ett användarkonto med namnet *azureuser*. Parametern `--generate-ssh-keys` används för att automatiskt generera en SSH-nyckel och placera den på standardnyckelplatsen ( *~/.ssh*). Om du i stället vill använda en specifik uppsättning nycklar använder du alternativet `--ssh-key-value`.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

Det tar några minuter att skapa den virtuella datorn och stödresurser. Utdataresultatet i följande exempel anger att den virtuella datorn har skapats.

```output
{
  "fqdns": "",
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myResourceGroup"
}
```

Skriv ned din egen `publicIpAddress` i utdataresultatet från din virtuella dator. Den här adressen används för att ansluta till den virtuella datorn i nästa steg.

## <a name="open-port-80-for-web-traffic"></a>Öppna port 80 för webbtrafik

Som standard öppnas SSH-anslutningar när du skapar en virtuell Linux-dator i Azure. Använd [az vm open-port](/cli/azure/vm) för att öppna TCP port 80 för användning med NGINX-webbservern:

```azurecli-interactive
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

## <a name="connect-to-virtual-machine"></a>Ansluta till den virtuella datorn

SSH till den virtuella datorn som normalt. Ersätt **publicIpAddress** med offentliga IP-adressen för den virtuella datorn enligt beskrivningen i föregående utdata från den virtuella datorn:

```bash
ssh azureuser@publicIpAddress
```

## <a name="install-web-server"></a>Installera webbservern

Om du vill se hur den virtuella datorn fungerar i praktiken installerar du NGINX-webbservern. Uppdatera dina paketkällor och installera det senaste NGINX-paketet.

```bash
sudo apt-get -y update
sudo apt-get -y install nginx
```

När du är klar avslutar du SSH-sessionen genom att skriva `exit`.

## <a name="view-the-web-server-in-action"></a>Se hur webbservern fungerar i praktiken

Använd valfri webbläsare för att visa välkomstsidan för NGINX. Använd den virtuella datorns offentliga IP-adress som webbadress. Följande exempel visar NGINX-standardwebbplatsen:

![NGINX-standardwebbplats](./media/quick-create-cli/nginx.png)

## <a name="clean-up-resources"></a>Rensa resurser

När den inte längre behövs du använda kommandot [az group delete](/cli/azure/group) för att ta bort resursgruppen, den virtuella datorn och alla relaterade resurser. 

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten distribuerade du en enkel virtuell dator, öppnade en nätverksport för webbtrafik och installerade en enkel webbserver. Om du vill veta mer om virtuella Azure-datorer fortsätter du till självstudien för virtuella Linux-datorer.


> [!div class="nextstepaction"]
> [Självstudier om virtuella Azure Linux-datorer](./tutorial-manage-vm.md)
