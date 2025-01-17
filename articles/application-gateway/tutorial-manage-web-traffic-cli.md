---
title: Hantera webbtrafik – Azure CLI
description: Lär dig hur du skapar en programgateway med en VM-skalningsuppsättning för hantering av webbtrafik i Azure CLI.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 5/1/2019
ms.author: victorh
ms.openlocfilehash: d60c756fcf0b527731b8a1f31a8d93f108c91665
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65146234"
---
# <a name="manage-web-traffic-with-an-application-gateway-using-the-azure-cli"></a>Hantera webbtrafik med en programgateway som använder Azure CLI

Programgatewayen används till att hantera och skydda webbtrafiken till de servrar du hanterar. Du kan använda Azure CLI för att skapa en [programgatewayen](overview.md) som använder en [virtual machine scale Sets](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) för backend-servrar. I det här exemplet innehåller två instanser av virtuella datorer i skalningsuppsättningen. Skalningsuppsättningen har lagts till Standardpool för serverdelen för programgatewayen.

I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Konfigurera nätverket
> * Skapa en programgateway
> * Skapa en VM-skalningsuppsättning med serverdelens standardpool

Om du vill kan du slutföra den här proceduren med [Azure PowerShell](tutorial-manage-web-traffic-powershell.md).

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt måste den här snabbstarten måste du köra Azure CLI version 2.0.4 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

En resursgrupp är en logisk container där Azure-resurser distribueras och hanteras. Skapa en resursgrupp med [az group create](/cli/azure/group#az-group-create).

I följande exempel skapas en resursgrupp med namnet *myResourceGroupAG* på platsen *eastus*.

```azurecli-interactive 
az group create --name myResourceGroupAG --location eastus
```

## <a name="create-network-resources"></a>Skapa nätverksresurser 

Skapa ett virtuellt nätverk med namnet *myVNet* och ett undernät med namnet *myAGSubnet* med [az network vnet create](/cli/azure/network/vnet). Du kan sedan lägga till undernätet med namnet *myBackendSubnet* som servrarna i serverdelen behöver med [az network vnet subnet create](/cli/azure/network/vnet/subnet). Skapa den offentliga IP-adressen med namnet *myAGPublicIPAddress* med [az network public-ip create](/cli/azure/network/public-ip).

```azurecli-interactive
az network vnet create \
  --name myVNet \
  --resource-group myResourceGroupAG \
  --location eastus \
  --address-prefix 10.0.0.0/16 \
  --subnet-name myAGSubnet \
  --subnet-prefix 10.0.1.0/24

az network vnet subnet create \
  --name myBackendSubnet \
  --resource-group myResourceGroupAG \
  --vnet-name myVNet \
  --address-prefix 10.0.2.0/24

az network public-ip create \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress
```

## <a name="create-an-application-gateway"></a>Skapa en programgateway

Använd [az network application-gateway create](/cli/azure/network/application-gateway) till att skapa en programgateway med namnet *myAppGateway*. När du skapar en programgateway med hjälp av Azure CLI anger du konfigurationsinformation som kapacitet, sku och HTTP-inställningar. Programgatewayen tilldelas till *myAGSubnet* och *myPublicIPAddress* som du skapade tidigare. 

```azurecli-interactive
az network application-gateway create \
  --name myAppGateway \
  --location eastus \
  --resource-group myResourceGroupAG \
  --vnet-name myVNet \
  --subnet myAGsubnet \
  --capacity 2 \
  --sku Standard_Medium \
  --http-settings-cookie-based-affinity Disabled \
  --frontend-port 80 \
  --http-settings-port 80 \
  --http-settings-protocol Http \
  --public-ip-address myAGPublicIPAddress
```

 Det kan ta flera minuter att skapa programgatewayen. När application gateway har skapats, visas de här nya funktionerna:

- *appGatewayBackendPool* – en programgateway måste ha minst en serverdelsadresspool.
- *appGatewayBackendHttpSettings* – anger att port 80 och ett HTTP-protokoll används för kommunikation.
- *appGatewayHttpListener* – standardlyssnaren som är associerad med *appGatewayBackendPool*.
- *appGatewayFrontendIP* – tilldelar *myAGPublicIPAddress* till *appGatewayHttpListener*.
- *regel 1* – standardroutningsregel som är associerad med *appGatewayHttpListener*.

## <a name="create-a-virtual-machine-scale-set"></a>Skapa en VM-skalningsuppsättning

I det här exemplet skapar du en VM-skalningsuppsättning som tillhandahåller servrar till serverdelspoolen i programgatewayen. De virtuella datorerna i skalningsuppsättningen är associerade med undernätet *myBackendSubnet* och *appGatewayBackendPool*. Du skapar skalningsuppsättningen med [az vmss create](/cli/azure/vmss#az-vmss-create).

```azurecli-interactive
az vmss create \
  --name myvmss \
  --resource-group myResourceGroupAG \
  --image UbuntuLTS \
  --admin-username azureuser \
  --admin-password Azure123456! \
  --instance-count 2 \
  --vnet-name myVNet \
  --subnet myBackendSubnet \
  --vm-sku Standard_DS2 \
  --upgrade-policy-mode Automatic \
  --app-gateway myAppGateway \
  --backend-pool-name appGatewayBackendPool
```

### <a name="install-nginx"></a>Installera NGINX

Nu kan du installera NGINX på VM-skaluppsättningen så att du kan testa HTTP-anslutningen till serverdelspoolen.

```azurecli-interactive
az vmss extension set \
  --publisher Microsoft.Azure.Extensions \
  --version 2.0 \
  --name CustomScript \
  --resource-group myResourceGroupAG \
  --vmss-name myvmss \
  --settings '{ "fileUris": ["https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/install_nginx.sh"], "commandToExecute": "./install_nginx.sh" }'
```

## <a name="test-the-application-gateway"></a>Testa programgatewayen

Hämta den offentliga IP-adressen för programgatewayen med [az network public-ip show](/cli/azure/network/public-ip). Kopiera den offentliga IP-adressen och klistra in den i webbläsarens adressfält.

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [ipAddress] \
  --output tsv
```

![Testa basadressen i programgatewayen](./media/tutorial-manage-web-traffic-cli/tutorial-nginxtest.png)

## <a name="clean-up-resources"></a>Rensa resurser

När du inte behöver dem längre tar du bort resursgruppen, programgatewayen och alla relaterade resurser.

```azurecli-interactive
az group delete --name myResourceGroupAG --location eastus
```

## <a name="next-steps"></a>Nästa steg

[Begränsa webbtrafik med en brandvägg för webbaserade program](./tutorial-restrict-web-traffic-cli.md)
