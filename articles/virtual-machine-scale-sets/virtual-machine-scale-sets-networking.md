---
title: Nätverk för skalningsuppsättningar för virtuella Azure-datorer | Microsoft Docs
description: Konfigurationsnätverksegenskaper för skalningsuppsättningar för virtuella Azure-datorer.
services: virtual-machine-scale-sets
documentationcenter: ''
author: mayanknayar
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/17/2017
ms.author: manayar
ms.openlocfilehash: a9141adfb1dd05efd73061379be89ddf27ab3832
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60803061"
---
# <a name="networking-for-azure-virtual-machine-scale-sets"></a>Nätverk för skalningsuppsättningar för virtuella Azure-datorer

När du distribuerar en skalningsuppsättning för en virtuell Azure-dator via portalen är vissa nätverksegenskaper standard, till exempel en Azure Load Balancer med inkommande NAT-regler. Den här artikeln beskriver hur du använder några mer avancerade nätverksfunktioner som du kan konfigurera med skalningsuppsättningar.

Du kan konfigurera alla funktioner som beskrivs i den här artikeln med hjälp av Azure Resource Manager-mallar. Azure CLI- och PowerShell-exempel ingår också i de valda funktionerna.

## <a name="accelerated-networking"></a>Accelererat nätverk
Azure accelererat nätverk förbättrar nätverkets prestanda genom att aktivera SR-I/O-virtualisering till en virtuell dator. Mer information om hur du använder accelererat nätverk finns i artikeln Accelererat nätverk för virtuella [Windows](../virtual-network/create-vm-accelerated-networking-powershell.md)- eller [Linux](../virtual-network/create-vm-accelerated-networking-cli.md)-datorer. Om du vill använda accelererat nätverk med skalningsuppsättningar ställer du in enableAcceleratedNetworking till **sant** i inställningarna för skalningsuppsättningens networkInterfaceConfigurations. Exempel:
```json
"networkProfile": {
    "networkInterfaceConfigurations": [
    {
      "name": "niconfig1",
      "properties": {
        "primary": true,
        "enableAcceleratedNetworking" : true,
        "ipConfigurations": [
          ...
        ]
      }
    }
   ]
}
```

## <a name="create-a-scale-set-that-references-an-existing-azure-load-balancer"></a>Skapa en skalningsuppsättning som refererar till en befintlig Azure Load Balancer
När en skalningsuppsättning skapas med hjälp av Azure Portal skapas en ny lastbalanserare för de flesta konfigurationsalternativen. Om du skapar en skalningsuppsättning som måste referera till en befintlig lastbalanserare kan du göra detta med hjälp av CLI. Följande exempelskript skapar en lastbalanserare och sedan en skalningsuppsättning som refererar till den:
```bash
az network lb create \
    -g lbtest \
    -n mylb \
    --vnet-name myvnet \
    --subnet mysubnet \
    --public-ip-address-allocation Static \
    --backend-pool-name mybackendpool

az vmss create \
    -g lbtest \
    -n myvmss \
    --image Canonical:UbuntuServer:16.04-LTS:latest \
    --admin-username negat \
    --ssh-key-value /home/myuser/.ssh/id_rsa.pub \
    --upgrade-policy-mode Automatic \
    --instance-count 3 \
    --vnet-name myvnet \
    --subnet mysubnet \
    --lb mylb \
    --backend-pool-name mybackendpool
```

## <a name="create-a-scale-set-that-references-an-application-gateway"></a>Skapa en skalningsuppsättning som refererar till en programgateway
Om du vill skapa en skalningsuppsättning som använder en programgateway refererar du till programgatewayens backend-adresspool i avsnittet ipConfigurations i skaluppsättningen, som i den här ARM-mallkonfigurationen:
```json
"ipConfigurations": [{
  "name": "{config-name}",
  "properties": {
  "subnet": {
    "id": "{subnet-id}"
  },
  "ApplicationGatewayBackendAddressPools": [{
    "id": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/applicationGateways/{gateway-name}/backendAddressPools/{pool-name}"
  }]
}]
```

>[!NOTE]
> Observera att programgatewayen måste finnas i samma virtuella nätverk som skalningsuppsättningen, men i ett annat undernät än skaluppsättningen.


## <a name="configurable-dns-settings"></a>Konfigurera DNS-inställningar
Som standard tar skalningsuppsättningar över specifika DNS-inställningar från de VNET och undernät som de skapades i. Du kan dock konfigurera DNS-inställningar för en skalningsuppsättning direkt.

### <a name="creating-a-scale-set-with-configurable-dns-servers"></a>Skapa en skalningsuppsättning med konfigurerbara DNS-servrar
Om du vill skapa en skalningsuppsättning med en anpassad DNS-konfiguration med hjälp av Azure CLI lägger du till argumentet --**dns-servers** till kommandot **vmss create** följt av ip-adresser som avgränsas av ett blanksteg. Exempel:
```bash
--dns-servers 10.0.0.6 10.0.0.5
```
Om du vill konfigurera anpassade DNS-servrar i en Azure-mall lägger du till en dnsSettings-egenskap till skalningsuppsättningens networkInterfaceConfigurations-avsnitt. Exempel:
```json
"dnsSettings":{
    "dnsServers":["10.0.0.6", "10.0.0.5"]
}
```

### <a name="creating-a-scale-set-with-configurable-virtual-machine-domain-names"></a>Skapa en skalningsuppsättning med konfigurerbara domännamn för virtuella datorer
För att skapa en skalningsuppsättning med ett anpassat DNS-namn för virtuella datorer med CLI lägger du till argumentet **--vm-domain-name** till kommandot **virtual machine scale set create** följt av en sträng som representerar domännamnet.

Om du vill konfigurera domännamnet i en Azure-mall lägger du till en **dnsSettings**-egenskap till skalningsuppsättningens **networkInterfaceConfigurations**-avsnitt. Exempel:

```json
"networkProfile": {
  "networkInterfaceConfigurations": [
    {
    "name": "nic1",
    "properties": {
      "primary": "true",
      "ipConfigurations": [
      {
        "name": "ip1",
        "properties": {
          "subnet": {
            "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/virtualNetworks/', variables('vnetName'), '/subnets/subnet1')]"
          },
          "publicIPAddressconfiguration": {
            "name": "publicip",
            "properties": {
            "idleTimeoutInMinutes": 10,
              "dnsSettings": {
                "domainNameLabel": "[parameters('vmssDnsName')]"
              }
            }
          }
        }
      }
    ]
    }
}
```

Utdata för en enskild virtuell dator dns-namn anges i följande format: 
```
<vm><vmindex>.<specifiedVmssDomainNameLabel>
```

## <a name="public-ipv4-per-virtual-machine"></a>Offentlig IPv4 per virtuell dator
I allmänhet kräver inte skalningsuppsättningar för virtuella Azure-datorer sina egna offentliga IP-adresser. I de flesta fall är det mer ekonomiskt och säkert att associera en offentlig IP-adress till en lastbalanserare eller till en enskild virtuell dator (kallas även en jumpbox) som sedan vidarebefordrar inkommande anslutningar till skalningsuppsättningar för virtuella datorer efter behov (till exempel via ingående NAT-regler).

Men vissa scenarier kräver att skalningsuppsättningarna för virtuella datorer har sina egna offentliga IP-adresser. Ett exempel är spel, där en konsol kan behöva ansluta direkt till en virtuell dator i molnet som utför bearbetningen av spelets fysik. Ett annat exempel är när virtuella datorer behöver göra externa anslutningar till varandra över regioner i en distribuerad databas.

### <a name="creating-a-scale-set-with-public-ip-per-virtual-machine"></a>Skapa en skalningsuppsättning med en offentlig IP per virtuell dator
För att skapa en skalningsuppsättning som tilldelar varje virtuell dator en offentlig IP-adress med hjälp av CLI lägger du till parametern **--public-ip-per-vm** till kommandot **vmss create**. 

Kontrollera att API-versionen av resursen Microsoft.Compute/virtualMachineScaleSets är minst från **2017-03-30** om du vill skapa en skalningsuppsättning med en Azure-mall och lägg till en **publicIpAddressConfiguration**-JSON-egenskap till skalningsuppsättningens ipConfigurations-avsnitt. Exempel:

```json
"publicIpAddressConfiguration": {
    "name": "pub1",
    "properties": {
      "idleTimeoutInMinutes": 15
    }
}
```
Exempelmall: [201-vmss-public-ip-linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-public-ip-linux)

### <a name="querying-the-public-ip-addresses-of-the-virtual-machines-in-a-scale-set"></a>Ställ frågor till de offentliga IP-adresserna för de virtuella datorerna i en skalningsuppsättning
Om du vill se en lista över de offentliga IP-adresserna som tilldelats till skalningsuppsättningar för virtuella datorer med hjälp av CLI använder du kommandot **az vmss list-instance-public-ips**.

För visa skaluppsättningens offentliga IP-adresser med hjälp av PowerShell använder du kommandot _Get-AzPublicIpAddress_. Exempel:
```powershell
Get-AzPublicIpAddress -ResourceGroupName myrg -VirtualMachineScaleSetName myvmss
```

Du kan också fråga offentliga IP-adresser genom att referera till resurs-ID för den offentliga IP-adresskonfigurationen direkt. Exempel:
```powershell
Get-AzPublicIpAddress -ResourceGroupName myrg -Name myvmsspip
```

Du kan även visa de offentliga IP-adresserna som tilldelats till skalningsuppsättningar för virtuella datorer genom att fråga [Azure Resource Explorer](https://resources.azure.com) eller Azure REST-API version **2017-03-30** eller högre.

Fråga [Azure Resource Explorer](https://resources.azure.com):

1. Öppna [Azure Resource Explorer](https://resources.azure.com) i en webbläsare.
1. Expandera *prenumerationer* till vänster genom att klicka på *+* bredvid. Om du bara har ett objekt under *prenumerationer* kan den redan ha expanderats.
1. Expandera din prenumeration.
1. Expandera din resursgrupp.
1. Expandera *providers*.
1. Expandera *Microsoft.Compute*.
1. Expandera *virtualMachineScaleSets*.
1. Expandera din skalningsuppsättning.
1. Klicka på *publicipaddresses*.

Fråga Azure REST API:

```bash
GET https://management.azure.com/subscriptions/{your sub ID}/resourceGroups/{RG name}/providers/Microsoft.Compute/virtualMachineScaleSets/{scale set name}/publicipaddresses?api-version=2017-03-30
```

Exempel utdata från den [Azure Resource Explorer](https://resources.azure.com) och Azure REST API:
```json
{
  "value": [
    {
      "name": "pub1",
      "id": "/subscriptions/your-subscription-id/resourceGroups/your-rg/providers/Microsoft.Compute/virtualMachineScaleSets/pipvmss/virtualMachines/0/networkInterfaces/pipvmssnic/ipConfigurations/yourvmssipconfig/publicIPAddresses/pub1",
      "etag": "W/\"a64060d5-4dea-4379-a11d-b23cd49a3c8d\"",
      "properties": {
        "provisioningState": "Succeeded",
        "resourceGuid": "ee8cb20f-af8e-4cd6-892f-441ae2bf701f",
        "ipAddress": "13.84.190.11",
        "publicIPAddressVersion": "IPv4",
        "publicIPAllocationMethod": "Dynamic",
        "idleTimeoutInMinutes": 15,
        "ipConfiguration": {
          "id": "/subscriptions/your-subscription-id/resourceGroups/your-rg/providers/Microsoft.Compute/virtualMachineScaleSets/yourvmss/virtualMachines/0/networkInterfaces/yourvmssnic/ipConfigurations/yourvmssipconfig"
        }
      }
    },
    {
      "name": "pub1",
      "id": "/subscriptions/your-subscription-id/resourceGroups/your-rg/providers/Microsoft.Compute/virtualMachineScaleSets/yourvmss/virtualMachines/3/networkInterfaces/yourvmssnic/ipConfigurations/yourvmssipconfig/publicIPAddresses/pub1",
      "etag": "W/\"5f6ff30c-a24c-4818-883c-61ebd5f9eee8\"",
      "properties": {
        "provisioningState": "Succeeded",
        "resourceGuid": "036ce266-403f-41bd-8578-d446d7397c2f",
        "ipAddress": "13.84.159.176",
        "publicIPAddressVersion": "IPv4",
        "publicIPAllocationMethod": "Dynamic",
        "idleTimeoutInMinutes": 15,
        "ipConfiguration": {
          "id": "/subscriptions/your-subscription-id/resourceGroups/your-rg/providers/Microsoft.Compute/virtualMachineScaleSets/yourvmss/virtualMachines/3/networkInterfaces/yourvmssnic/ipConfigurations/yourvmssipconfig"
        }
      }
    }
```

## <a name="multiple-ip-addresses-per-nic"></a>Flera IP-adresser per nätverkskort
Varje nätverkskort som är kopplat till en virtuell dator i en skalningsuppsättning kan ha en eller flera associerade IP-konfigurationer. Varje konfiguration tilldelas en privat IP-adress. Varje konfiguration kan också ha en associerad offentlig IP-adressresurs. För att förstå hur många IP-adresser som kan tilldelas till ett nätverkskort och hur många offentliga IP-adresser du kan använda i en Azure-prenumeration kan du se [Azure-gränser](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

## <a name="multiple-nics-per-virtual-machine"></a>Flera nätverkskort per virtuell dator
Du kan ha upp till 8 nätverkskort per virtuell dator, beroende på datorns storlek. Det maximala antalet nätverkskort per dator är tillgänglig i [artikeln om VM-storlek](../virtual-machines/windows/sizes.md). Alla nätverkskort som är anslutna till en VM-instans måste ansluta till samma virtuella nätverk. Nätverkskorten kan ansluta till olika undernät, men alla undernät måste vara en del av samma virtuella nätverk.

Följande exempel är en nätverksprofil för skalningsuppsättningar som visar flera poster för nätverkskort och flera offentliga IP-adresser per virtuell dator:

```json
"networkProfile": {
    "networkInterfaceConfigurations": [
        {
        "name": "nic1",
        "properties": {
            "primary": "true",
            "ipConfigurations": [
            {
                "name": "ip1",
                "properties": {
                "subnet": {
                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/virtualNetworks/', variables('vnetName'), '/subnets/subnet1')]"
                },
                "publicipaddressconfiguration": {
                    "name": "pub1",
                    "properties": {
                    "idleTimeoutInMinutes": 15
                    }
                },
                "loadBalancerInboundNatPools": [
                    {
                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('lbName'), '/inboundNatPools/natPool1')]"
                    }
                ],
                "loadBalancerBackendAddressPools": [
                    {
                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('lbName'), '/backendAddressPools/addressPool1')]"
                    }
                ]
                }
            }
            ]
        }
        },
        {
        "name": "nic2",
        "properties": {
            "primary": "false",
            "ipConfigurations": [
            {
                "name": "ip1",
                "properties": {
                "subnet": {
                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/virtualNetworks/', variables('vnetName'), '/subnets/subnet1')]"
                },
                "publicipaddressconfiguration": {
                    "name": "pub1",
                    "properties": {
                    "idleTimeoutInMinutes": 15
                    }
                },
                "loadBalancerInboundNatPools": [
                    {
                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('lbName'), '/inboundNatPools/natPool1')]"
                    }
                ],
                "loadBalancerBackendAddressPools": [
                    {
                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('lbName'), '/backendAddressPools/addressPool1')]"
                    }
                ]
                }
            }
            ]
        }
        }
    ]
}
```

## <a name="nsg--asgs-per-scale-set"></a>NSG och ASG:er per skalningsuppsättning
Med [nätverkssäkerhetsgrupper](../virtual-network/security-overview.md) kan du filtrera trafik till och från Azure-resurser i en virtuellt Azure-nätverk med hjälp av säkerhetsregler. Med [programsäkerhetsgrupper](../virtual-network/security-overview.md#application-security-groups) kan du hantera nätverkssäkerhet för Azure-resurser och gruppera dem som ett tillägg i programmets struktur.

Nätverkssäkerhetsgrupper kan tillämpas direkt på en skalningsuppsättning genom att lägga till en referens till konfigurationsavsnittet för nätverksgränssnittet i egenskaperna för skalningsuppsättningen för virtuella datorer.

Programsäkerhetsgrupper kan även anges direkt för en skalningsuppsättning. Lägg till en referens till nätverksgränssnittets IP-konfigurationsavsnitt i egenskaperna för skalningsuppsättningen för den virtuella datorn.

Exempel: 
```json
"networkProfile": {
    "networkInterfaceConfigurations": [
        {
            "name": "nic1",
            "properties": {
                "primary": "true",
                "ipConfigurations": [
                    {
                        "name": "ip1",
                        "properties": {
                            "subnet": {
                                "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/virtualNetworks/', variables('vnetName'), '/subnets/subnet1')]"
                            },
                            "applicationSecurityGroups": [
                                {
                                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/applicationSecurityGroups/', variables('asgName'))]"
                                }
                            ],
                "loadBalancerInboundNatPools": [
                                {
                                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('lbName'), '/inboundNatPools/natPool1')]"
                                }
                            ],
                            "loadBalancerBackendAddressPools": [
                                {
                                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/loadBalancers/', variables('lbName'), '/backendAddressPools/addressPool1')]"
                                }
                            ]
                        }
                    }
                ],
                "networkSecurityGroup": {
                    "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/networkSecurityGroups/', variables('nsgName'))]"
                }
            }
        }
    ]
}
```

Verifiera att nätverkssäkerhetsgruppen är associerad med din skalningsuppsättning genom att använda kommandot `az vmss show`. I exemplet nedan används `--query` för att filtrera resultaten och visar endast relevanta avsnitt i utdata.

```bash
az vmss show \
    -g myResourceGroup \
    -n myScaleSet \
    --query virtualMachineProfile.networkProfile.networkInterfaceConfigurations[].networkSecurityGroup

[
  {
    "id": "/subscriptions/.../resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/nsgName",
    "resourceGroup": "myResourceGroup"
  }
]
```

Verifiera att programsäkerhetsgruppen är associerad med din skalningsuppsättning genom att använda kommandot `az vmss show`. I exemplet nedan används `--query` för att filtrera resultaten och visar endast relevanta avsnitt i utdata.

```bash
az vmss show \
    -g myResourceGroup \
    -n myScaleSet \
    --query virtualMachineProfile.networkProfile.networkInterfaceConfigurations[].ipConfigurations[].applicationSecurityGroups

[
  [
    {
      "id": "/subscriptions/.../resourceGroups/myResourceGroup/providers/Microsoft.Network/applicationSecurityGroups/asgName",
      "resourceGroup": "myResourceGroup"
    }
  ]
]
```



## <a name="next-steps"></a>Nästa steg
Mer information om virtuella Azure-nätverk finns i [Översikt över virtuella Azure-nätverk](../virtual-network/virtual-networks-overview.md).
