---
title: Aktivera brandvägg för webbaserade program – Azure CLI
description: Lär dig hur du begränsar webbtrafik med en brandvägg för webbaserade program på en programgateway med Azure CLI.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: tutorial
ms.date: 5/20/2019
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 1822fe032a7c7a6382dbae2cb9f7095d1d076008
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65955486"
---
# <a name="enable-web-application-firewall-using-the-azure-cli"></a>Aktivera brandvägg för webbaserade program med hjälp av Azure CLI

Du kan begränsa webbtrafiken för en [programgateway](overview.md) med hjälp av en [brandvägg för webbaserade program](waf-overview.md) (WAF). I brandväggen används [OWASP](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project)-regler till att skydda programmet. De här reglerna kan exempelvis skydda mot attacker som SQL-inmatning, skriptattacker mellan webbplatser och sessionskapningar.

I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Konfigurera nätverket
> * Skapa en programgateway med WAF aktiverat
> * Skapa en VM-skalningsuppsättning
> * Skapa ett lagringskonto och konfigurera diagnostik

![Exempel på brandvägg för webbaserade program](./media/tutorial-restrict-web-traffic-cli/scenario-waf.png)

Om du vill kan du slutföra den här proceduren med [Azure PowerShell](tutorial-restrict-web-traffic-powershell.md).

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt krävs Azure CLI version 2.0.4 eller senare för att du ska kunna följa anvisningarna i den här artikeln. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

En resursgrupp är en logisk container där Azure-resurser distribueras och hanteras. Skapa en resursgrupp i Azure med namnet *myResourceGroup* med [az group create](/cli/azure/group#az-group-create).

```azurecli-interactive 
az group create --name myResourceGroupAG --location eastus
```

## <a name="create-network-resources"></a>Skapa nätverksresurser

Det virtuella nätverket och undernäten används till att ge programgatewayen och tillhörande resurser en nätverksanslutning. Skapa ett virtuellt nätverk med namnet *myVNet* och ett undernät med namnet *myAGSubnet* med [az network vnet create](/cli/azure/network/vnet#az-network-vnet-create) och [az network vnet subnet create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create). Skapa en offentlig IP-adress med namnet *myAGPublicIPAddress* med [az network public-ip create](/cli/azure/network/public-ip#az-network-public-ip-create).

```azurecli-interactive
az network vnet create \
  --name myVNet \
  --resource-group myResourceGroupAG \
  --location eastus \
  --address-prefix 10.0.0.0/16 \
  --subnet-name myBackendSubnet \
  --subnet-prefix 10.0.1.0/24

az network vnet subnet create \
  --name myAGSubnet \
  --resource-group myResourceGroupAG \
  --vnet-name myVNet \
  --address-prefix 10.0.2.0/24

az network public-ip create \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress
```

## <a name="create-an-application-gateway-with-a-waf"></a>Skapa en programgateway med en WAF

Du kan använda [az network application-gateway create](/cli/azure/network/application-gateway) till att skapa en programgateway med namnet *myAppGateway*. När du skapar en programgateway med hjälp av Azure CLI anger du konfigurationsinformation som kapacitet, sku och HTTP-inställningar. Programgatewayen tilldelas till *myAGSubnet* och *myAGPublicIPAddress* som du skapade tidigare.

```azurecli-interactive
az network application-gateway create \
  --name myAppGateway \
  --location eastus \
  --resource-group myResourceGroupAG \
  --vnet-name myVNet \
  --subnet myAGSubnet \
  --capacity 2 \
  --sku WAF_Medium \
  --http-settings-cookie-based-affinity Disabled \
  --frontend-port 80 \
  --http-settings-port 80 \
  --http-settings-protocol Http \
  --public-ip-address myAGPublicIPAddress

az network application-gateway waf-config set \
  --enabled true \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --firewall-mode Detection \
  --rule-set-version 3.0
```

Det kan ta flera minuter att skapa programgatewayen. När programgatewayen har skapats ser du de här nya funktionerna:

- *appGatewayBackendPool* – en programgateway måste ha minst en serverdelsadresspool.
- *appGatewayBackendHttpSettings* – anger att port 80 och ett HTTP-protokoll används för kommunikation.
- *appGatewayHttpListener* – standardlyssnaren som är associerad med *appGatewayBackendPool*.
- *appGatewayFrontendIP* – tilldelar *myAGPublicIPAddress* till *appGatewayHttpListener*.
- *regel 1* – standardroutningsregel som är associerad med *appGatewayHttpListener*.

## <a name="create-a-virtual-machine-scale-set"></a>Skapa en VM-skalningsuppsättning

I det här exemplet skapar du en VM-skalningsuppsättning som tillhandahåller två servrar till serverdelspoolen i programgatewayen. De virtuella datorerna i skalningsuppsättningen är associerade med undernätet *myBackendSubnet*. Du kan skapa skalningsuppsättningen med [az vmss create](/cli/azure/vmss#az-vmss-create).

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

```azurecli-interactive
az vmss extension set \
  --publisher Microsoft.Azure.Extensions \
  --version 2.0 \
  --name CustomScript \
  --resource-group myResourceGroupAG \
  --vmss-name myvmss \
  --settings '{ "fileUris": ["https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/install_nginx.sh"],"commandToExecute": "./install_nginx.sh" }'
```

## <a name="create-a-storage-account-and-configure-diagnostics"></a>Skapa ett lagringskonto och konfigurera diagnostik

I den här artikeln använder ett lagringskonto i application gateway för att lagra data för identifiering och skydd. Du kan även använda Azure Monitor-loggar eller Event Hub till att registrera data. 

### <a name="create-a-storage-account"></a>skapar ett lagringskonto

Skapa ett lagringskonto med namnet *myagstore1* med [az storage account create](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create).

```azurecli-interactive
az storage account create \
  --name myagstore1 \
  --resource-group myResourceGroupAG \
  --location eastus \
  --sku Standard_LRS \
  --encryption blob
```

### <a name="configure-diagnostics"></a>Konfigurera diagnostiken

Konfigurera diagnostik för registrering av data i loggarna ApplicationGatewayAccessLog, ApplicationGatewayPerformanceLog och ApplicationGatewayFirewallLog. Ersätt `<subscriptionId>` med ditt prenumerations-id och konfigurera diagnostiken med [az monitor diagnostic-settings create](/cli/azure/monitor/diagnostic-settings?view=azure-cli-latest#az-monitor-diagnostic-settings-create).

```azurecli-interactive
appgwid=$(az network application-gateway show --name myAppGateway --resource-group myResourceGroupAG --query id -o tsv)

storeid=$(az storage account show --name myagstore1 --resource-group myResourceGroupAG --query id -o tsv)

az monitor diagnostic-settings create --name appgwdiag --resource $appgwid \
  --logs '[ { "category": "ApplicationGatewayAccessLog", "enabled": true, "retentionPolicy": { "days": 30, "enabled": true } }, { "category": "ApplicationGatewayPerformanceLog", "enabled": true, "retentionPolicy": { "days": 30, "enabled": true } }, { "category": "ApplicationGatewayFirewallLog", "enabled": true, "retentionPolicy": { "days": 30, "enabled": true } } ]' \
  --storage-account $storeid
```

## <a name="test-the-application-gateway"></a>Testa programgatewayen

Hämta den offentliga IP-adressen för programgatewayen med [az network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show). Kopiera den offentliga IP-adressen och klistra in den i webbläsarens adressfält.

```azurepowershell-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [ipAddress] \
  --output tsv
```

![Testa basadressen i programgatewayen](./media/tutorial-restrict-web-traffic-cli/application-gateway-nginxtest.png)

## <a name="clean-up-resources"></a>Rensa resurser

När du inte behöver dem längre tar du bort resursgruppen, programgatewayen och alla relaterade resurser.

```azurecli-interactive
az group delete --name myResourceGroupAG 
```

## <a name="next-steps"></a>Nästa steg

* [Skapa en programgateway med SSL-avslutning](./tutorial-ssl-cli.md)
