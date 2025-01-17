---
title: 'Distribuera en virtuell dator från dina VHD: er för Azure Marketplace'
description: Beskriver hur du registrerar en virtuell dator från en Azure-distribuerade virtuella Hårddisken.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: article
ms.date: 11/30/2018
ms.author: pabutler
ms.openlocfilehash: a393620f28d45ec494c4e899f01e7e9a92b3ceba
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64938293"
---
# <a name="deploy-a-vm-from-your-vhds"></a>Distribuera en virtuell dator från dina VHD: er

Det här avsnittet beskrivs hur du distribuerar en virtuell dator (VM) från en Azure-distribuerade virtuella hårddisk (VHD).  Den visar de verktyg som krävs och hur du använder dem för att skapa en VM-avbildning för användaren och sedan distribuera den till Azure med hjälp av PowerShell-skript.

När du har laddat upp dina virtuella hårddiskar (VHD) – generaliserad operativsystemet VHD och noll eller fler disk virtuella hårddiskar – till Azure storage-kontot, kan du registrera dem som en avbildning för användaren. Du kan sedan testa avbildningen. Du kan inte direkt distribuera den virtuella datorn genom att tillhandahålla VHD-URL eftersom operativsystemet VHD är generaliserad.

Mer information om VM-avbildningar finns i följande blogginlägg:

- [VM-avbildning](https://azure.microsoft.com/blog/vm-image-blog-post/)
- [Bild-dator med PowerShell, hur du'](https://azure.microsoft.com/blog/vm-image-powershell-how-to-blog-post/)

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="prerequisite-install-the-necessary-tools"></a>Förutsättning: Installera nödvändiga verktyg

Om du inte redan har gjort det installerar du Azure PowerShell och Azure CLI, med hjälp av följande anvisningar:

- [Installera Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps)
- [Installera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)


## <a name="deployment-steps"></a>Distributionssteg

Du vill använda följande steg för att skapa och distribuera en VM-avbildning för användaren:

1. Skapa VM-användaravbildning, vilket innebär att samla in och generalisera avbildningen. 
2. Skapa certifikat och lagra dem i en ny Azure Key Vault. Ett certifikat krävs för att upprätta en säker WinRM-anslutning till den virtuella datorn.  En Azure Resource Manager-mall och Azure PowerShell-skript tillhandahålls. 
3. Distribuera den virtuella datorn från en användaravbildning av virtuell dator med hjälp av den angivna mallen och skript.

När den virtuella datorn har distribuerats kan du är redo att [certifiera din avbildning](./cpp-certify-vm.md).

1. Klicka på **New** och Sök efter **Malldistributionen**och välj sedan **skapa din egen mall i redigeraren**.  <br/>
   ![Skapa mall för distribution av virtuell Hårddisk i Azure-portalen](./media/publishvm_021.png)

1. Kopiera och klistra in det här [JSON-mall](./cpp-deploy-json-template.md) i redigeraren och klicka på **spara**. <br/>
   ![Spara mall för distribution av virtuell Hårddisk i Azure-portalen](./media/publishvm_022.png)

1. Ange parametervärden för den visade **anpassad distribution** egenskapssidor.

   <table> <tr> <td valign="top"> <img src="./media/publishvm_023.png" alt="Custom deployment property page 1"> </td> <td valign="top"> <img src="./media/publishvm_024.png" alt="Custom deployment property page 2"> </td> </tr> </table> <br/> 

   |  **Parametern**              |   **Beskrivning**                                                            |
   |  -------------              |   ---------------                                                            |
   | Användaren Lagringskontonamn   | Lagringskontonamn där den generaliserade virtuella Hårddisken finns                    |
   | Namnet på användaren lagringsbehållaren | Behållarens namn där den generaliserade virtuella Hårddisken finns                          |
   | DNS-namn för offentlig IP-adress      | Offentliga IP-DNS-namn                                                           |
   | Systemadministratörsanvändarens namn             | Användarnamn för administratörskontot för nya virtuella datorn                                  |
   | Adminlösenord              | Lösenordet för administratörskontot för nya virtuella datorn                                  |
   | OS-typ                     | VM-operativsystem: `Windows` \| `Linux`                                    |
   | Prenumerations-ID:t             | Identifierare för den valda prenumerationen                                      |
   | Location                    | Geografisk plats för distributionen                                        |
   | Storlek på virtuell dator                     | [Azure VM-storlek](https://docs.microsoft.com/azure/virtual-machines/windows/sizes), till exempel `Standard_A2` |
   | Offentliga IP-adressnamn      | Namnet på din offentliga IP-adress                                               |
   | Namn på virtuell dator                     | Namnet på den nya virtuella datorn                                                           |
   | Namn på virtuellt nätverk        | Namnet på det virtuella nätverket som används av den virtuella datorn                                   |
   | Namn på nätverkskort                    | Namnet på nätverkskortet som kör det virtuella nätverket               |
   | VHD-URL                     | Fullständig VHD URL-adress för OS-Disk                                                     |
   |  |  |
            
1. När du anger dessa värden klickar du på **köp**. 

Azure börjar distribution: den skapar en ny virtuell dator med den angivna ohanterade VHD i sökvägen för angivna lagringskontot.  Du kan följa förloppet i Azure portal genom att klicka på **virtuella datorer** till vänster i portalen.  När den virtuella datorn har skapats ändras status från `Starting` till `Running`. 


### <a name="deploy-a-vm-from-powershell"></a>Distribuera en virtuell dator från PowerShell

Använd följande cmdlets för att distribuera en stor virtuell dator från den generaliserade avbildningen som nyss skapade.

``` powershell
    $img = Get-AzureVMImage -ImageName "myVMImage"
    $user = "user123"
    $pass = "adminPassword123"
    $myVM = New-AzureVMConfig -Name "VMImageVM" -InstanceSize "Large" -ImageName $img.ImageName | Add-AzureProvisioningConfig -Windows -AdminUsername $user -Password $pass
    New-AzureVM -ServiceName "VMImageCloudService" -VMs $myVM -Location "West US" -WaitForBoot
```


## <a name="next-steps"></a>Nästa steg

Därefter [skapa en virtuell datoravbildning för användaren](cpp-create-user-image.md) för din lösning.

