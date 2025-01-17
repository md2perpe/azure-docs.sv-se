---
title: Koppla samman virtuella nätverk med peerkoppling - självstudie - Azure Portal | Microsoft Docs
description: I den här självstudien får du lära dig hur du kopplar samman virtuella nätverk med peerkoppling, med hjälp av Azure Portal.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
Customer intent: I want to connect two virtual networks so that virtual machines in one virtual network can communicate with virtual machines in the other virtual network.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: tutorial
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 08/16/2018
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: 943cad871330e2f3b6e13b33dca582ab545fe4be
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64726564"
---
# <a name="tutorial-connect-virtual-networks-with-virtual-network-peering-using-the-azure-portal"></a>Självstudier: Ansluta virtuella nätverk med peerkoppling med hjälp av Azure portal

Du kan ansluta virtuella nätverk till varandra med peerkoppling. Dessa virtuella nätverk kan finnas i samma region eller andra regioner (kallas även Global VNet-peering). När virtuella nätverk har peerkopplats kan resurser i båda virtuella nätverken kommunicera med varandra, med samma svarstid och bandbredd som om resurserna fanns i samma virtuella nätverk. I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa två virtuella nätverk
> * Koppla samman två virtuella nätverk med en peerkoppling
> * Distribuera en virtuell dator (VM) till varje virtuellt nätverk
> * Kommunicera mellan virtuella datorer

Om du vill kan du slutföra den här självstudien med [Azure CLI](tutorial-connect-virtual-networks-cli.md) eller [Azure PowerShell](tutorial-connect-virtual-networks-powershell.md).

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="log-in-to-azure"></a>Logga in på Azure 

Logga in på Azure Portal på https://portal.azure.com.

## <a name="create-virtual-networks"></a>Skapa virtuella nätverk

1. Klicka på **+ Skapa en resurs** längst upp till vänster på Azure Portal.
2. Välj **Nätverk** och välj därefter **Virtuellt nätverk**.
3. Ange eller välj följande information, acceptera standardinställningarna för återstående inställningar och välj sedan **Skapa**:

    |Inställning|Värde|
    |---|---|
    |Namn|myVirtualNetwork1|
    |Adressutrymme|10.0.0.0/16|
    |Prenumeration| Välj din prenumeration.|
    |Resursgrupp| Välj **Skapa ny** och ange *myResourceGroup*.|
    |Location| Välj **USA, östra**.|
    |Undernätsnamn|Subnet1|
    |Undernätsadressintervall|10.0.0.0/24|

      ![Skapa ett virtuellt nätverk](./media/tutorial-connect-virtual-networks-portal/create-virtual-network.png)

4. Utför steg 1–3 igen, med följande ändringar:

    |Inställning|Värde|
    |---|---|
    |Namn|myVirtualNetwork2|
    |Adressutrymme|10.1.0.0/16|
    |Resursgrupp| Välj **Använd befintlig** och sedan **myResourceGroup**.|
    |Undernätsadressintervall|10.1.0.0/24|

## <a name="peer-virtual-networks"></a>Peerkoppla virtuella nätverk

1. I rutan Sök högst upp på Azure Portal börjar du skriva *MyVirtualNetwork1*. När **myVirtualNetwork1** visas i sökresultatet väljer du det.
2. Välj **Peering-sessioner** under **INSTÄLLNINGAR** och sedan **+ Lägg till** som du ser på följande bild:

    ![Skapa peerkoppling](./media/tutorial-connect-virtual-networks-portal/create-peering.png)

3. Ange eller välj följande information, acceptera standardinställningarna för återstående inställningar och välj sedan **OK**.

    |Inställning|Värde|
    |---|---|
    |Namn|myVirtualNetwork1-myVirtualNetwork2|
    |Prenumeration| Välj din prenumeration.|
    |Virtuellt nätverk|myVirtualNetwork2 – Välj det virtuella nätverket *myVirtualNetwork2* genom att välja **Virtuellt nätverk** och sedan **myVirtualNetwork2**. Du kan välja ett virtuellt nätverk i samma region eller i en annan region.|

    ![Inställningar för peerkoppling](./media/tutorial-connect-virtual-networks-portal/peering-settings.png)

    **PEERING-STATUS** är *Initierad* som du ser på följande bild:

    ![Peering-status](./media/tutorial-connect-virtual-networks-portal/peering-status.png)

    Om du inte ser status uppdaterar du webbläsaren.

4. I rutan **Sök** högst upp på Azure Portal börjar du skriva *MyVirtualNetwork2*. När **myVirtualNetwork2** visas i sökresultatet väljer du det.
5. Utför steg 2–3 igen, med följande ändringar, och välj sedan **OK**:

    |Inställning|Värde|
    |---|---|
    |Namn|myVirtualNetwork2-myVirtualNetwork1|
    |Virtuellt nätverk|myVirtualNetwork1|

    **PEERING-STATUS** är *Ansluten*. Azure har också ändrat peering-status för peerkopplingen *myVirtualNetwork2-myVirtualNetwork1* från *Initierad* till *Ansluten*. Peerkopplingen mellan virtuella nätverk är inte helt upprättad förrän peering-statusen för båda virtuella nätverken är *Ansluten*. 

## <a name="create-virtual-machines"></a>Skapa virtuella datorer

Skapa en virtuell dator i varje virtuellt nätverk så att du kan kommunicera mellan dem i ett senare steg.

### <a name="create-the-first-vm"></a>Skapa den första virtuella datorn

1. Klicka på **+ Skapa en resurs** längst upp till vänster på Azure Portal.
2. Välj **Compute**, och välj sedan **Windows Server 2016 Datacenter**. Du kan välja ett annat operativsystem, men i återstående steg förutsätts att du har valt **Windows Server 2016 Datacenter**. 
3. Ange eller välj följande information för **Grundinställningar**, acceptera standardinställningarna för återstående inställningar och välj sedan **Skapa**:

    |Inställning|Värde|
    |---|---|
    |Namn|myVm1|
    |Användarnamn| Ange ett valfritt användarnamn.|
    |Lösenord| Ange ett valfritt lösenord. Lösenordet måste vara minst 12 tecken långt och uppfylla [de definierade kraven på komplexitet](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    |Resursgrupp| Välj **Använd befintlig** och sedan **myResourceGroup**.|
    |Location| Välj **USA, östra**.|
4. Välj en VM-storlek i **Välj en storlek**.
5. Välj följande värden för **Inställningar** och sedan **OK**:

    |Inställning|Värde|
    |---|---|
    |Virtuellt nätverk| myVirtualNetwork1 – Om det inte redan är valt väljer du **Virtuellt nätverk** och sedan **myVirtualNetwork1** under **Välj ett virtuellt nätverk**.|
    |Undernät| Subnet1 – Om det inte redan är valt väljer du **Undernät** och sedan **Subnet1** under **Välj undernät**.|
    
    ![Inställningar för virtuella datorer](./media/tutorial-connect-virtual-networks-portal/virtual-machine-settings.png)
 
6. Under **Skapa** i **sammanfattningen** väljer du **Skapa** för att starta VM-distributionen.

### <a name="create-the-second-vm"></a>Skapa den andra virtuella datorn

Utför steg 1–6 igen, med följande ändringar:

|Inställning|Värde|
|---|---|
|Namn | myVm2|
|Virtuellt nätverk | myVirtualNetwork2|

Det tar några minuter att skapa de virtuella datorerna. Fortsätt inte med återstående steg förrän båda virtuella datorerna har skapats.

## <a name="communicate-between-vms"></a>Kommunicera mellan virtuella datorer

1. I rutan *Sök* högst upp i portalen börjar du skriva *myVm1*. När **myVm1** visas i sökresultatet väljer du den.
2. Skapa en fjärrskrivbordsanslutning till den virtuella datorn *myVm1* genom att välja **Anslut** enligt bilden nedan:

    ![Ansluta till den virtuella datorn](./media/tutorial-connect-virtual-networks-portal/connect-to-virtual-machine.png)  

3. Öppna den nedladdade RDP-filen för att ansluta till den virtuella datorn. Välj **Anslut** om du uppmanas att göra det.
4. Ange användarnamnet och lösenordet du angav när du skapade den virtuella datorn (du kanske måste välja **Fler alternativ** och sedan **Använd ett annat konto** för att ange autentiseringsuppgifterna du angav när du skapade den virtuella datorn) och välj **OK**.
5. Du kan få en certifikatvarning under inloggningen. Välj **Ja** för att fortsätta med anslutningen.
6. I ett senare steg används ping för att kommunicera med den virtuella datorn *myVm2* från den virtuella datorn *myVm1*. Ping använder som standard ICMP (Internet Control Message Protocol), som nekas via Windows-brandväggen. På den virtuella datorn *myVm1* aktiverar du ICMP via Windows-brandväggen, så att du kan pinga den här virtuella datorn från *myVm2* i ett senare steg, med hjälp av PowerShell:

    ```powershell
    New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
    ```
    
    Även om ping används för att kommunicera mellan virtuella datorer i den här självstudien rekommenderar vi inte att du tillåter att ICMP kommer igenom Windows-brandväggen för produktionsdistribution.

7. Du ansluter till den virtuella datorn *myVm2* genom att ange följande kommando från en kommandotolk på den virtuella datorn *myVm1*:

    ```
    mstsc /v:10.1.0.4
    ```
    
8. Eftersom du har aktiverat ping på *myVm1* kan du nu pinga IP-adressen:

    ```
    ping 10.0.0.4
    ```
    
9. Koppla från RDP-sessionerna till både *myVm1* och *myVm2*.

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort resursgruppen och alla resurser den innehåller när de inte längre behövs: 

1. Skriv *myResourceGroup* i rutan **Sök** högst upp i portalen. När du ser **myResourceGroup** i sökresultatet väljer du den.
2. Välj **Ta bort resursgrupp**.
3. Skriv *myResourceGroup* där du uppmanas att **skriva resursgruppens namn:** (Skriv resursgruppens namn) och välj **Ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig hur du kopplar samman två nätverk i samma Azure-region, med peerkoppling av virtuella nätverk. Du kan också peerkoppla virtuella nätverk i olika [regioner som stöds](virtual-network-manage-peering.md#cross-region) och i [olika Azure-prenumerationer](create-peering-different-subscriptions.md#portal), samt skapa [nav- och ekernätverksdesigner](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json#vnet-peering) med peerkoppling. Mer information om peerkoppling av virtuella nätverk finns i [Översikt över peerkoppling av virtuella nätverk](virtual-network-peering-overview.md) och [Hantera peerkopplingar av virtuella nätverk](virtual-network-manage-peering.md).

Information om hur du ansluter din egen dator till ett virtuellt nätverk via en VPN-anslutning, och interagerar med resurser i ett virtuellt nätverk, eller i peerkopplade virtuella nätverk, finns i [Ansluta datorn till ett virtuellt nätverk](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
