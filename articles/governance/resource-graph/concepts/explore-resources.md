---
title: Utforska dina Azure-resurser
description: Lär dig att använda Resource Graph-frågespråk för att utforska dina resurser och Upptäck hur de är anslutna.
author: DCtheGeek
ms.author: dacoulte
ms.date: 04/23/2019
ms.topic: conceptual
ms.service: resource-graph
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: 0b4a75558f5e82b707ae5d012acef4d2c5c4b7a0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64723806"
---
# <a name="explore-your-azure-resources-with-resource-graph"></a>Utforska dina Azure-resurser med resursgrafer

Azure Resource Graph ger möjlighet att utforska och identifiera dina Azure-resurser, snabbt och skalenligt. Utformad för snabba svar, är det ett bra sätt att lära dig om din miljö och även om de egenskaper som utgör din Azure-resurser.

[!INCLUDE [az-powershell-update](../../../../includes/updated-for-az.md)]

## <a name="explore-virtual-machines"></a>Utforska virtuella datorer

En gemensam resurs i Azure är en virtuell dator. Virtuella datorer har ett antal egenskaper som kan efterfrågas som en resurstyp. Varje egenskap innehåller ett alternativ för filtrering eller hitta exakt den resurs som du letar efter.

### <a name="virtual-machine-discovery"></a>Identifiering av virtuell dator

Låt oss börja med en enkel fråga för att hämta en enda virtuell dator från vår miljö och titta på egenskaperna som returneras.

```kusto
where type =~ 'Microsoft.Compute/virtualMachines'
| limit 1
```

```azurecli-interactive
az graph query -q "where type =~ 'Microsoft.Compute/virtualMachines' | limit 1"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type =~ 'Microsoft.Compute/virtualMachines' | limit 1" | ConvertTo-Json -Depth 100
```

> [!NOTE]
> Azure PowerShell `Search-AzGraph` cmdlet returnerar en **PSCustomObject** som standard. Ha utdata ser likadana ut som det som returneras av Azure CLI, den `ConvertTo-Json` cmdlet används. Standardvärdet för **djup** är _2_. Ange värdet till _100_ ska konvertera alla returnerade nivåer.

JSON-resultaten är strukturerade liknar följande exempel:

```json
[
  {
    "id": "/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/virtualMachines/ContosoVM1",
    "kind": "",
    "location": "westus2",
    "managedBy": "",
    "name": "ContosoVM1",
    "plan": {},
    "properties": {
      "hardwareProfile": {
        "vmSize": "Standard_B2s"
      },
      "networkProfile": {
        "networkInterfaces": [
          {
            "id": "/subscriptions/<subscriptionId>/MyResourceGroup/providers/Microsoft.Network/networkInterfaces/contosovm1535",
            "resourceGroup": "MyResourceGroup"
          }
        ]
      },
      "osProfile": {
        "adminUsername": "localAdmin",
        "computerName": "ContosoVM1",
        "secrets": [],
        "windowsConfiguration": {
          "enableAutomaticUpdates": true,
          "provisionVMAgent": true
        }
      },
      "provisioningState": "Succeeded",
      "storageProfile": {
        "dataDisks": [],
        "imageReference": {
          "offer": "WindowsServer",
          "publisher": "MicrosoftWindowsServer",
          "sku": "2016-Datacenter",
          "version": "latest"
        },
        "osDisk": {
          "caching": "ReadWrite",
          "createOption": "FromImage",
          "diskSizeGB": 127,
          "managedDisk": {
            "id": "/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166",
            "resourceGroup": "MyResourceGroup",
            "storageAccountType": "Premium_LRS"
          },
          "name": "ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166",
          "osType": "Windows"
        }
      },
      "vmId": "bbb9b451-6dc7-4117-bec5-c971eb1118c6"
    },
    "resourceGroup": "MyResourceGroup",
    "sku": {},
    "subscriptionId": "<subscriptionId>",
    "tags": {},
    "type": "microsoft.compute/virtualmachines"
  }
]
```

Berätta mer om den virtuella datorresursen, allt från SKU: N, OS, diskar, taggar, egenskaper och resursgrupp och prenumeration är medlem i.

### <a name="virtual-machines-by-location"></a>Virtuella datorer efter plats

Vi tar vad vi lärt oss om resursen för virtuella datorer kan använda den **plats** egenskapen du räknar alla virtuella datorer efter plats. Om du vill uppdatera frågan vi ta bort gränsen och sammanfatta antalet värden för platsen.

```kusto
where type =~ 'Microsoft.Compute/virtualMachines'
| summarize count() by location
```

```azurecli-interactive
az graph query -q "where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by location"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by location"
```

JSON-resultaten är strukturerade liknar följande exempel:

```json
[
  {
    "count_": 386,
    "location": "eastus"
  },
  {
    "count_": 215,
    "location": "southcentralus"
  },
  {
    "count_": 59,
    "location": "westus"
  }
]
```

Vi kan nu se hur många virtuella datorer har vi i varje Azure-region.

### <a name="virtual-machines-by-sku"></a>Virtuella datorer av SKU: N

Vi går tillbaka till den ursprungliga virtuella datorns egenskaper, försök att hitta alla virtuella datorer som har en SKU-storlek på **Standard_B2s**. Titta på JSON returneras, ser vi att den är lagrad i **properties.hardwareprofile.vmsize**. Vi uppdaterar fråga för att hitta alla virtuella datorer som matchar den här storleken och returnera bara namnet på den virtuella datorn och region.

```kusto
where type =~ 'Microsoft.Compute/virtualMachines' and properties.hardwareProfile.vmSize == 'Standard_B2s'
| project name, resourceGroup"
```

```azurecli-interactive
az graph query -q "where type =~ 'Microsoft.Compute/virtualMachines' and properties.hardwareProfile.vmSize == 'Standard_B2s' | project name, resourceGroup"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type =~ 'Microsoft.Compute/virtualMachines' and properties.hardwareProfile.vmSize == 'Standard_B2s' | project name, resourceGroup"
```

### <a name="virtual-machines-connected-to-premium-managed-disks"></a>Virtuella datorer är anslutna till premium managed disks

Om vi vill hämta information om premium-hanterade diskar som är kopplade till dessa **Standard_B2s** virtuella datorer, kan vi Expandera fråga för att ge oss resurs-ID för dessa hanterade diskar.

```kusto
where type =~ 'Microsoft.Compute/virtualmachines' and properties.hardwareProfile.vmSize == 'Standard_B2s'
| extend disk = properties.storageProfile.osDisk.managedDisk
| where disk.storageAccountType == 'Premium_LRS'
| project disk.id
```

> [!NOTE]
> Ett annat sätt att hämta SKU: N skulle ha varit med hjälp av den **alias** egenskapen **Microsoft.Compute/virtualMachines/sku.name**. Se den [Visa alias](../samples/starter.md#show-aliases) och [Visa alias distinkta värden](../samples/starter.md#distinct-alias-values) exempel.

```azurecli-interactive
az graph query -q "where type =~ 'Microsoft.Compute/virtualmachines' and properties.hardwareProfile.vmSize == 'Standard_B2s' | extend disk = properties.storageProfile.osDisk.managedDisk | where disk.storageAccountType == 'Premium_LRS' | project disk.id"
```

```azurepowershell-interactive
  Search-AzGraph -Query "where type =~ 'Microsoft.Compute/virtualmachines' and properties.hardwareProfile.vmSize == 'Standard_B2s' | extend disk = properties.storageProfile.osDisk.managedDisk | where disk.storageAccountType == 'Premium_LRS' | project disk.id"
```

Resultatet är en lista över disk-ID: N.

### <a name="managed-disk-discovery"></a>Identifiering av hanterad disk

Vi kommer den första posten från den föregående frågan för att utforska de egenskaper som finns på den hantera disken som har kopplats till den första virtuella datorn. Den uppdaterade frågan använder disk-ID och ändrar typen.

Exempel utdata från den föregående frågan till exempel:

```json
[
  {
    "disk_id": "/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166"
  }
]
```

```kusto
where type =~ 'Microsoft.Compute/disks' and id == '/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166'
```

Innan du kör frågan, hur vi visste den **typ** nu bör vara **Microsoft.Compute/disks**?
Om du tittar på det fullständiga ID: T visas **/providers/Microsoft.Compute/disks/** som en del av strängen. Den här strängen fragment ger en ledtråd om vilken typ som eftersöks. En alternativ metod är att ta bort gränsen efter typ och i stället bara söka efter ID-fältet. Eftersom det ID: T är unik, bara en post skulle returneras och **typ** egenskap på den innehåller den information.

> [!NOTE]
> I det här exemplet ska fungera måste du ersätta ID-fältet med ett resultat från din egen miljö.

```azurecli-interactive
az graph query -q "where type =~ 'Microsoft.Compute/disks' and id == '/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166'"
```

```azurepowershell-interactive
Search-AzGraph -Query "where type =~ 'Microsoft.Compute/disks' and id == '/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166'"
```

JSON-resultaten är strukturerade liknar följande exempel:

```json
[
  {
    "id": "/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/disks/ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166",
    "kind": "",
    "location": "westus2",
    "managedBy": "",
    "name": "ContosoVM1_OsDisk_1_9676b7e1b3c44e2cb672338ebe6f5166",
    "plan": {},
    "properties": {
      "creationData": {
        "createOption": "Empty"
      },
      "diskSizeGB": 127,
      "diskState": "ActiveSAS",
      "provisioningState": "Succeeded",
      "timeCreated": "2018-09-14T12:17:32.2570000Z"
    },
    "resourceGroup": "MyResourceGroup",
    "sku": {
      "name": "Premium_LRS",
      "tier": "Premium"
    },
    "subscriptionId": "<subscriptionId>",
    "tags": {
      "environment": "prod"
    },
    "type": "microsoft.compute/disks"
  }
]
```

## <a name="explore-virtual-machines-to-find-public-ip-addresses"></a>Utforska virtuella datorer för att hitta offentliga IP-adresser

Den här Azure CLI uppsättning frågor först söker efter och lagrar alla nätverksgränssnitt (NIC)-resurser som är anslutna till virtuella datorer. Sedan använder listan över nätverkskort för att hitta varje IP-adressresurs som är en offentlig IP-adress och lagra dessa värden. Slutligen innehåller den en lista över offentliga IP-adresser.

```azurecli-interactive
# Use Resource Graph to get all NICs and store in the 'nic' variable
az graph query -q "where type =~ 'Microsoft.Compute/virtualMachines' | project nic = tostring(properties['networkProfile']['networkInterfaces'][0]['id']) | where isnotempty(nic) | distinct nic | limit 20" --output table | tail -n +3 > nics.txt

# Review the output of the query stored in 'nics.txt'
cat nics.txt
```

Använd den `nics.txt` filen i nästa fråga att hämta relaterade nätverksgränssnittet resurser information där det finns en offentlig IP-adress som är kopplade till nätverkskortet.

```azurecli-interactive
# Use Resource Graph with the 'nics.txt' file to get all related public IP addresses and store in 'publicIp.txt' file
az graph query -q="where type =~ 'Microsoft.Network/networkInterfaces' | where id in ('$(awk -vORS="','" '{print $0}' nics.txt | sed 's/,$//')') | project publicIp = tostring(properties['ipConfigurations'][0]['properties']['publicIPAddress']['id']) | where isnotempty(publicIp) | distinct publicIp" --output table | tail -n +3 > ips.txt

# Review the output of the query stored in 'ips.txt'
cat ips.txt
```

Senaste, Använd listan över offentliga IP-adressresurser lagras i `ips.txt` skaffa faktiska offentliga IP-adress från dem och visa.

```azurecli-interactive
# Use Resource Graph with the 'ips.txt' file to get the IP address of the public IP address resources
az graph query -q="where type =~ 'Microsoft.Network/publicIPAddresses' | where id in ('$(awk -vORS="','" '{print $0}' ips.txt | sed 's/,$//')') | project ip = tostring(properties['ipAddress']) | where isnotempty(ip) | distinct ip" --output table
```

## <a name="next-steps"></a>Nästa steg

- Läs mer om [frågespråket](query-language.md)
- Se språket som användes i [Starter frågor](../samples/starter.md)
- Se avancerade använder i [avancerade frågor](../samples/advanced.md)