---
title: Självstudie – Konfigurera en Azure DNS-aliaspost för att referera till en offentlig IP-adress i Azure.
description: Den här självstudien visar hur du konfigurerar en Azure DNS-aliaspost för att referera till en offentlig IP-adress i Azure.
services: dns
author: vhorne
ms.service: dns
ms.topic: tutorial
ms.date: 9/25/2018
ms.author: victorh
ms.openlocfilehash: 7dcbfdaf00b0e628541cfd1a3b79df8cf8334ed3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61430621"
---
# <a name="tutorial-configure-an-alias-record-to-refer-to-an-azure-public-ip-address"></a>Självstudier: Konfigurera en aliaspost för att referera till en Azure offentlig IP-adress 

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa en nätverksinfrastruktur.
> * Skapa en virtuell webbserverdator.
> * Skapa en aliaspost.
> * Testa aliasposten.


Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter
Du måste ha ett domännamn tillgängligt som du kan ha i Azure DNS för att testa med. Du måste ha fullständig kontroll över den här domänen. Fullständig behörighet omfattar möjligheten att ange namnserverposter (NS-poster) för domänen.

Anvisningar för att vara värd för din domän i Azure DNS finns i [självstudien: Använda Azure DNS som värd för din domän.](dns-delegate-domain-azure-dns.md)

Den exempeldomän som används i den här självstudien är contoso.com, men du använder ditt eget domännamn.

## <a name="create-the-network-infrastructure"></a>Skapa nätverksinfrastrukturen
Skapa först ett virtuellt nätverk och ett undernät för att placera dina webbservrar i.
1. Logga in på Azure Portal på https://portal.azure.com.
2. Längst upp till vänster i portalen väljer du **Skapa en resurs**. Ange *resursgrupp* i sökrutan och skapa en resursgrupp med namnet **RG-DNS-Alias-pip**.
3. Välj **Skapa en resurs** > **Nätverk** > **Virtuellt nätverk**.
4. Skapa ett virtuellt nätverk med namnet **VNet-Server**. Placera det i resursgruppen **RG-DNS-Alias-pip** och ge undernätet namnet **SN-Web**.

## <a name="create-a-web-server-virtual-machine"></a>Skapa en virtuell webbserverdator
1. Välj **Skapa en resurs** > **Windows Server 2016 VM**.
2. Ange **Web-01** som namn och placera den virtuella datorn i resursgruppen **RG-DNS-Alias-TM**. Ange ett användarnamn och ett lösenord, och välj **OK**.
3. För **Storlek** väljer du en SKU med 8 GB RAM.
4. Som **Inställningar** väljer du det virtuella nätverket **VNet-Servers** och undernätet **SN-Web**. För offentliga inkommande portar väljer du **HTTP** > **HTTPS** > **RDP (3389)** och sedan **OK**.
5. På sidan **Sammanfattning** väljer du **Skapa**.

Den här proceduren tar några minuter att slutföra.

### <a name="install-iis"></a>Installera IIS

Installera IIS på **Web-01**.

1. Anslut till **Web-01** och logga in.
2. På instrumentpanelen **Serverhanteraren** väljer du **Lägg till roller och funktioner**.
3. Välj **Nästa** tre gånger. Välj **Webbserver (IIS)** på sidan **Serverroller**.
4. Välj **Lägg till funktioner** och sedan **Nästa**.
5. Välj **Nästa** fyra gånger och sedan **Installera**. Den här proceduren tar några minuter att slutföra.
6. När installationen är klar väljer du **Stäng**.
7. Öppna en webbläsare. Gå till **localhost** för att kontrollera att IIS-standardwebbsidan visas.

## <a name="create-an-alias-record"></a>Skapa en aliaspost

Skapa en aliaspost som pekar på den offentliga IP-adressen.

1. Välj din Azure DNS-zon för att öppna zonen.
2. Välj **Uppsättning av poster**.
3. I textrutan **Namn** väljer du **web01**.
4. Lämna **Typ** som en **A**-post.
5. Välj kryssrutan **Aliaspostuppsättning**.
6. Välj **Välj Azure-tjänst** och välj sedan den offentliga IP-adressen **Web-01-ip**.

## <a name="test-the-alias-record"></a>Testa aliasposten

1. I resursgruppen **RG-DNS-Alias-pip** väljer du den virtuella datorn **Web-01**. Anteckna den offentliga IP-adressen.
1. Från en webbläsare bläddrar du till det fullständigt kvalificerade domännamnet för den virtuella datorn Web01-01. Ett exempel är **web01.contoso.com**. Du ser nu IIS-standardwebbsidan.
2. Stäng webbläsaren.
3. Stoppa den virtuella datorn **Web-01** och starta sedan om den.
4. När den virtuella datorn startar om antecknar du den nya offentliga IP-adressen för den virtuella datorn.
5. Öppna en ny webbläsare. Bläddra åter till det fullständigt kvalificerade domännamnet för den virtuella datorn Web01-01. Ett exempel är **web01.contoso.com**.

Den här proceduren lyckas eftersom du använde en aliaspost för att peka mot den offentliga IP-adressresursen i stället för en standardmässig A-post.

## <a name="clean-up-resources"></a>Rensa resurser

När du inte längre behöver de resurser som skapades för den här självstudien tar du bort resursgruppen **RG-DNS-Alias-pip**.


## <a name="next-steps"></a>Nästa steg

I den här självstudien konfigurerade du en aliaspost för att referera till en offentlig IP-adress i Azure. Lär dig mer om Azure DNS och webbappar genom att fortsätta med självstudien för webbappar.

> [!div class="nextstepaction"]
> [Skapa DNS-poster för en webbapp i en anpassad domän](./dns-web-sites-custom-domain.md)
