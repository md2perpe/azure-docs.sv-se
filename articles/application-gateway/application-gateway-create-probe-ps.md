---
title: Skapa en anpassad avsökning – Azure Application Gateway – PowerShell | Microsoft Docs
description: Lär dig hur du skapar en anpassad avsökning för Programgateway med hjälp av PowerShell i Resource Manager
services: application-gateway
documentationcenter: na
author: vhorne
manager: jpconnock
editor: ''
tags: azure-resource-manager
ms.assetid: 68feb660-7fa4-4f69-a7e4-bdd7bdc474db
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/26/2017
ms.author: victorh
ms.openlocfilehash: acd70bacd23755cd764bc782a297d80db3622424
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66135247"
---
# <a name="create-a-custom-probe-for-azure-application-gateway-by-using-powershell-for-azure-resource-manager"></a>Skapa en anpassad avsökning för Azure Application Gateway med hjälp av PowerShell för Azure Resource Manager

> [!div class="op_single_selector"]
> * [Azure Portal](application-gateway-create-probe-portal.md)
> * [PowerShell och Azure Resource Manager](application-gateway-create-probe-ps.md)
> * [PowerShell och den klassiska Azure-portalen](application-gateway-create-probe-classic-ps.md)

I den här artikeln får du till en anpassad avsökning i en befintlig application gateway med PowerShell. Anpassade avsökningar är användbara för program som har en specifik hälsokontrollsida eller för program som inte uppger ett lyckat svar på standardwebbprogrammet.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="create-an-application-gateway-with-a-custom-probe"></a>Skapa en Programgateway med en anpassad avsökning

### <a name="sign-in-and-create-resource-group"></a>Logga in och skapa resursgrupp

1. Använd `Connect-AzAccount` att autentisera.

   ```powershell
   Connect-AzAccount
   ```

1. Hämta prenumerationerna för kontot.

   ```powershell
   Get-AzSubscription
   ```

1. Välj vilka av dina Azure-prenumerationer som du vill använda.

   ```powershell
   Select-AzSubscription -Subscriptionid '{subscriptionGuid}'
   ```

1. Skapa en resursgrupp. Du kan hoppa över det här steget om du har en befintlig resursgrupp.

   ```powershell
   New-AzResourceGroup -Name appgw-rg -Location 'West US'
   ```

Azure Resource Manager kräver att alla resursgrupper anger en plats. Den här platsen används som standardplats för resurserna i den resursgruppen. Se till att alla kommandon för att skapa en Programgateway använder samma resursgrupp.

I exemplet ovan skapade vi resursgruppen **appgw-RG** på plats **västra USA**.

### <a name="create-a-virtual-network-and-a-subnet"></a>Skapa ett virtuellt nätverk och ett undernät

I följande exempel skapas ett virtuellt nätverk och ett undernät för application gateway. Application gateway kräver ett eget undernät för användning. Därför bör är undernätet som skapas för application gateway vara mindre än adressutrymmet för det virtuella nätverket så att andra undernät skapas och används.

```powershell
# Assign the address range 10.0.0.0/24 to a subnet variable to be used to create a virtual network.
$subnet = New-AzVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24

# Create a virtual network named appgwvnet in resource group appgw-rg for the West US region using the prefix 10.0.0.0/16 with subnet 10.0.0.0/24.
$vnet = New-AzVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location 'West US' -AddressPrefix 10.0.0.0/16 -Subnet $subnet

# Assign a subnet variable for the next steps, which create an application gateway.
$subnet = $vnet.Subnets[0]
```

### <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Skapa en offentlig IP-adress för frontend-konfigurationen

Skapa en offentlig IP-resurs, **publicIP01**, i resursgruppen **appgw-rg** för regionen USA, västra. Det här exemplet använder en offentlig IP-adress för frontend IP-adressen för application gateway.  Application gateway kräver offentliga IP-adressen har ett dynamiskt skapade DNS-namn därför den `-DomainNameLabel` kan inte anges under genereringen av offentliga IP-adress.

```powershell
$publicip = New-AzPublicIpAddress -ResourceGroupName appgw-rg -Name publicIP01 -Location 'West US' -AllocationMethod Dynamic
```

### <a name="create-an-application-gateway"></a>Skapa en programgateway

Du ställer in alla konfigurationsobjekt innan du skapar programgatewayen. I följande exempel skapar konfigurationsobjekten som behövs för en programgatewayresurs.

| **Komponent** | **Beskrivning** |
|---|---|
| **IP-gatewaykonfiguration** | En IP-konfiguration för en Programgateway.|
| **Serverdelspool** | En pool med IP-adresser, FQDN eller nätverkskort som är till programservrar som är värdar för webbprogrammet|
| **Hälsoavsökning** | En anpassad avsökning som används för att övervaka hälsotillståndet för medlemmarna i serverdelspoolen|
| **HTTP-inställningar** | En samling inställningar inklusive, port, protokoll, cookie-baserad tillhörighet, avsökning och timeout.  Inställningarna avgör hur trafiken dirigeras till medlemmarna i serverdelspoolen|
| **Frontend-port** | Den port som application gateway lyssnar efter trafik på|
| **Lyssnare** | En kombination av protokoll, klientdelens IP-konfiguration och frontend-port. Det här är vad lyssnar efter inkommande begäranden.
|**Regeln**| Vägar trafiken till den korrekta serverdelen baserat på HTTP-inställningar.|

```powershell
# Creates an application gateway Frontend IP configuration named gatewayIP01
$gipconfig = New-AzApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet

#Creates a back-end IP address pool named pool01 with IP addresses 134.170.185.46, 134.170.188.221, 134.170.185.50.
$pool = New-AzApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221, 134.170.185.50

# Creates a probe that will check health at http://contoso.com/path/path.htm
$probe = New-AzApplicationGatewayProbeConfig -Name probe01 -Protocol Http -HostName 'contoso.com' -Path '/path/path.htm' -Interval 30 -Timeout 120 -UnhealthyThreshold 8

# Creates the backend http settings to be used. This component references the $probe created in the previous command.
$poolSetting = New-AzApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Disabled -Probe $probe -RequestTimeout 80

# Creates a frontend port for the application gateway to listen on port 80 that will be used by the listener.
$fp = New-AzApplicationGatewayFrontendPort -Name frontendport01 -Port 80

# Creates a frontend IP configuration. This associates the $publicip variable defined previously with the front-end IP that will be used by the listener.
$fipconfig = New-AzApplicationGatewayFrontendIPConfig -Name fipconfig01 -PublicIPAddress $publicip

# Creates the listener. The listener is a combination of protocol and the frontend IP configuration $fipconfig and frontend port $fp created in previous steps.
$listener = New-AzApplicationGatewayHttpListener -Name listener01  -Protocol Http -FrontendIPConfiguration $fipconfig -FrontendPort $fp

# Creates the rule that routes traffic to the backend pools.  In this example we create a basic rule that uses the previous defined http settings and backend address pool.  It also associates the listener to the rule
$rule = New-AzApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

# Sets the SKU of the application gateway, in this example we create a small standard application gateway with 2 instances.
$sku = New-AzApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

# The final step creates the application gateway with all the previously defined components.
$appgw = New-AzApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location 'West US' -BackendAddressPools $pool -Probes $probe -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku
```

## <a name="add-a-probe-to-an-existing-application-gateway"></a>Lägg till en avsökning i en befintlig Programgateway

Följande kodavsnitt lägger till en avsökning i en befintlig application gateway.

```powershell
# Load the application gateway resource into a PowerShell variable by using Get-AzApplicationGateway.
$getgw =  Get-AzApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg

# Create the probe object that will check health at http://contoso.com/path/path.htm
$getgw = Add-AzApplicationGatewayProbeConfig -ApplicationGateway $getgw -Name probe01 -Protocol Http -HostName 'contoso.com' -Path '/path/custompath.htm' -Interval 30 -Timeout 120 -UnhealthyThreshold 8

# Set the backend HTTP settings to use the new probe
$getgw = Set-AzApplicationGatewayBackendHttpSettings -ApplicationGateway $getgw -Name $getgw.BackendHttpSettingsCollection.name -Port 80 -Protocol Http -CookieBasedAffinity Disabled -Probe $probe -RequestTimeout 120

# Save the application gateway with the configuration changes
Set-AzApplicationGateway -ApplicationGateway $getgw
```

## <a name="remove-a-probe-from-an-existing-application-gateway"></a>Ta bort en avsökning från en befintlig Programgateway

Följande kodavsnitt tar bort en avsökning från en befintlig application gateway.

```powershell
# Load the application gateway resource into a PowerShell variable by using Get-AzApplicationGateway.
$getgw =  Get-AzApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg

# Remove the probe from the application gateway configuration object
$getgw = Remove-AzApplicationGatewayProbeConfig -ApplicationGateway $getgw -Name $getgw.Probes.name

# Set the backend HTTP settings to remove the reference to the probe. The backend http settings now use the default probe
$getgw = Set-AzApplicationGatewayBackendHttpSettings -ApplicationGateway $getgw -Name $getgw.BackendHttpSettingsCollection.name -Port 80 -Protocol http -CookieBasedAffinity Disabled

# Save the application gateway with the configuration changes
Set-AzApplicationGateway -ApplicationGateway $getgw
```

## <a name="get-application-gateway-dns-name"></a>Hämta DNS-namn för programgatewayen

När du har skapat gatewayen, är nästa steg att konfigurera klientprogrammet för kommunikation. När du använder en offentlig IP-adress krävs ett dynamiskt tilldelat DNS-namn som inte är användarvänligt. För att säkerställa att slutanvändare kan nå programgatewayen kan en CNAME-post användas för att peka på den offentliga slutpunkten för programgatewayen. [Konfigurera ett eget domännamn i Azure](../cloud-services/cloud-services-custom-domain-name-portal.md). Gör detta genom att hämta information om programgatewayen och dess associerade IP/DNS-namn med PublicIPAddress-elementet kopplat till programgatewayen. programgatewayens DNS-namn ska användas för att skapa en CNAME-post som leder de två webbapparna till detta DNS-namn. Användning av A-poster rekommenderas inte eftersom VIP kan ändras vid omstart av programgatewayen.

```powershell
Get-AzPublicIpAddress -ResourceGroupName appgw-RG -Name publicIP01
```

```
Name                     : publicIP01
ResourceGroupName        : appgw-RG
Location                 : westus
Id                       : /subscriptions/<subscription_id>/resourceGroups/appgw-RG/providers/Microsoft.Network/publicIPAddresses/publicIP01
Etag                     : W/"00000d5b-54ed-4907-bae8-99bd5766d0e5"
ResourceGuid             : 00000000-0000-0000-0000-000000000000
ProvisioningState        : Succeeded
Tags                     : 
PublicIpAllocationMethod : Dynamic
IpAddress                : xx.xx.xxx.xx
PublicIpAddressVersion   : IPv4
IdleTimeoutInMinutes     : 4
IpConfiguration          : {
                                "Id": "/subscriptions/<subscription_id>/resourceGroups/appgw-RG/providers/Microsoft.Network/applicationGateways/appgwtest/frontendIP
                            Configurations/frontend1"
                            }
DnsSettings              : {
                                "Fqdn": "00000000-0000-xxxx-xxxx-xxxxxxxxxxxx.cloudapp.net"
                            }
```

## <a name="next-steps"></a>Nästa steg

Lär dig att konfigurera SSL-avlastning genom att besöka: [Konfigurera SSL-avlastning](application-gateway-ssl-arm.md)

