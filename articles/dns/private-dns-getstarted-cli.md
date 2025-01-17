---
title: Skapa en privat Azure DNS-zon med Azure CLI
description: I den här proceduren ska du skapa och testa en privat DNS-zon och DNS-post i Azure DNS. Det här är en stegvis guide om hur du skapar och hanterar din första privata DNS-zon och DNS-post med hjälp av Azure CLI.
services: dns
author: vhorne
ms.service: dns
ms.topic: article
ms.date: 6/13/2019
ms.author: victorh
ms.openlocfilehash: d882a9c40efc5e9bcb1a5e1c02f1ac73970d57db
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67076416"
---
# <a name="create-an-azure-dns-private-zone-using-the-azure-cli"></a>Skapa en privat Azure DNS-zon med Azure CLI

[!INCLUDE [private-dns-public-preview-notice](../../includes/private-dns-public-preview-notice.md)]

Den här proceduren vägleder dig genom stegen för att skapa din första privata DNS-zon och DNS-post med hjälp av Azure CLI.

En DNS-zon används som värd åt DNS-posterna för en viss domän. Om du vill låta Azure DNS vara värd för din domän så måste du skapa en DNS-zon för det domännamnet. Varje DNS-post för din domän skapas sedan i den här DNS-zonen. Om du vill publicera en privat DNS-zon i det virtuella nätverket anger du den lista över virtuella nätverk som får lösa poster i zonen.  Dessa kallas *länkade* virtuella nätverk. När autoregistrering aktiveras, Azure DNS även uppdaterar zonposter när en virtuell dator skapas, ändringar dess ”IP-adress eller tas bort.

I den här proceduren ska du lära dig hur du:

> [!div class="checklist"]
> * Skapa en privat DNS-zon
> * Skapa virtuella testdatorer
> * Skapa en ytterligare DNS-post
> * Testa den privata zonen

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

Om du vill kan du slutföra den här proceduren med [Azure PowerShell](private-dns-getstarted-powershell.md).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-the-resource-group"></a>Skapa en resursgrupp

Först skapar du en resursgrupp som ska innehålla DNS-zonen: 

```azurecli
az group create --name MyAzureResourceGroup --location "East US"
```

## <a name="create-a-dns-private-zone"></a>Skapa en privat DNS-zon

I följande exempel skapas ett virtuellt nätverk med namnet **myAzureVNet**. Innan skriptet skapar en DNS-zon med namnet **private.contoso.com** i den **MyAzureResourceGroup** resursgruppen, länkar DNS-zonen till den **MyAzureVnet** virtuellt nätverk och aktiverar automatisk registrering.

```azurecli
az network vnet create \
  --name myAzureVNet \
  --resource-group MyAzureResourceGroup \
  --location eastus \
  --address-prefix 10.2.0.0/16 \
  --subnet-name backendSubnet \
  --subnet-prefixes 10.2.0.0/24

az network private-dns zone create -g MyAzureResourceGroup \
   -n private.contoso.com

az network private-dns link vnet create -g MyAzureResourceGroup -n MyDNSLink \
   -z private.contoso.com -v myAzureVNet -e true
```

Om du vill skapa en zon för namnmatchning (ingen automatisk värdnamn registrering) kan du använda den `-e false` parametern.

### <a name="list-dns-private-zones"></a>Lista privata DNS-zoner

Använd `az network private-dns zone list` om du vill räkna upp DNS-zoner. Om du vill ha hjälp, så gå till `az network dns zone list --help`.

När du anger resursgrupp så listas bara zonerna i resursgruppen:

```azurecli
az network private-dns zone list \
  -g MyAzureResourceGroup
```

Om du utesluter resursgruppen listas alla zoner i prenumerationen:

```azurecli
az network private-dns zone list 
```

## <a name="create-the-test-virtual-machines"></a>Skapa de virtuella testdatorerna

Nu skapar du två virtuella datorer så att du kan testa din privata DNS-zon:

```azurecli
az vm create \
 -n myVM01 \
 --admin-username AzureAdmin \
 -g MyAzureResourceGroup \
 -l eastus \
 --subnet backendSubnet \
 --vnet-name myAzureVnet \
 --nsg NSG01 \
 --nsg-rule RDP \
 --image win2016datacenter

az vm create \
 -n myVM02 \
 --admin-username AzureAdmin \
 -g MyAzureResourceGroup \
 -l eastus \
 --subnet backendSubnet \
 --vnet-name myAzureVnet \
 --nsg NSG01 \
 --nsg-rule RDP \
 --image win2016datacenter
```

Det här kan ta några minuter.

## <a name="create-an-additional-dns-record"></a>Skapa en ytterligare DNS-post

Skapa en DNS-post genom att använda kommandot `az network private-dns record-set [record type] add-record`. Mer information att till exempel lägga till A-poster finns i `az network private-dns record-set A add-record --help`.

 I följande exempel skapas en post med det relativa namnet **db** i DNS-zonen **private.contoso.com**, i resursgruppen **MyAzureResourceGroup**. Det fullständigt kvalificerade namnet på postuppsättningen är **db.private.contoso.com**. Posttypen är ”A” med IP-adressen ”10.2.0.4”.

```azurecli
az network private-dns record-set a add-record \
  -g MyAzureResourceGroup \
  -z private.contoso.com \
  -n db \
  -a 10.2.0.4
```

### <a name="view-dns-records"></a>Visa DNS-poster

Om du vill visa en lista med DNS-poster i din zon kör du:

```azurecli
az network private-dns record-set list \
  -g MyAzureResourceGroup \
  -z private.contoso.com
```

## <a name="test-the-private-zone"></a>Testa den privata zonen

Nu kan du testa namnmatchning för din **private.contoso.com** privat zon.

### <a name="configure-vms-to-allow-inbound-icmp"></a>Konfigurera virtuella datorer för att tillåta inkommande ICMP

Du kan använda ping-kommandot för att testa namnmatchning. Konfigurera därför brandväggen på båda de virtuella datorerna att tillåta inkommande ICMP-paket.

1. Anslut till myVM01 och öppna ett Windows PowerShell-fönster med administratörsbehörighet.
2. Kör följande kommando:

   ```powershell
   New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
   ```

Upprepa för myVM02.

### <a name="ping-the-vms-by-name"></a>Pinga de virtuella datorerna efter namn

1. Från Windows PowerShell-kommandotolken för myVM02 pingar du myVM01 med hjälp av det automatiskt registrerade värddatornamnet:

   ```
   ping myVM01.private.contoso.com
   ```

   Du bör se utdata som liknar följande:

   ```
   PS C:\> ping myvm01.private.contoso.com

   Pinging myvm01.private.contoso.com [10.2.0.4] with 32 bytes of data:
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time=1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128

   Ping statistics for 10.2.0.4:
       Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
   Approximate round trip times in milli-seconds:
       Minimum = 0ms, Maximum = 1ms, Average = 0ms
   PS C:\>
   ```

2. Nu pingar du det **db**-namn som du skapade tidigare:

   ```
   ping db.private.contoso.com
   ```

   Du bör se utdata som liknar följande:

   ```
   PS C:\> ping db.private.contoso.com

   Pinging db.private.contoso.com [10.2.0.4] with 32 bytes of data:
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128

   Ping statistics for 10.2.0.4:
       Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
   Approximate round trip times in milli-seconds:
       Minimum = 0ms, Maximum = 0ms, Average = 0ms
   PS C:\>
   ```

## <a name="delete-all-resources"></a>Ta bort alla resurser

När den inte längre behövs kan du ta bort den **MyAzureResourceGroup** resursgrupp för att ta bort de resurser som skapades i den här proceduren.

```azurecli
az group delete --name MyAzureResourceGroup
```

## <a name="next-steps"></a>Nästa steg

I den här proceduren du distribuerat en privat DNS-zon skapas en DNS-post och testas i zonen.
Härnäst kan du läsa mer om privata DNS-zoner.

> [!div class="nextstepaction"]
> [Använda Azure DNS för privata domäner](private-dns-overview.md)
