---
title: Aktivera Azure Disk Encryption för virtuella Linux IaaS-datorer
description: Den här artikeln innehåller anvisningar om hur du aktiverar Microsoft Azure Disk Encryption för virtuella Linux IaaS-datorer.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 04/05/2019
ms.custom: seodec18
ms.openlocfilehash: ffdda51c4739dfad6c326a5c90f4a93ebfb321cd
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2019
ms.locfileid: "67294907"
---
# <a name="enable-azure-disk-encryption-for-linux-iaas-vms"></a>Aktivera Azure Disk Encryption för virtuella Linux IaaS-datorer 

Du kan aktivera många diskkryptering scenarier och stegen kan variera beroende på scenario. Följande avsnitt beskriver scenarier i större detalj för virtuella Linux IaaS-datorer. Innan du kan använda diskkryptering, den [som krävs för Azure Disk Encryption](azure-security-disk-encryption-prerequisites.md) måste slutföras och [ytterligare krav för Linux IaaS-datorer](azure-security-disk-encryption-prerequisites.md#additional-prerequisites-for-linux-iaas-vms) avsnittet bör granskas.

Ta en [ögonblicksbild](../virtual-machines/windows/snapshot-copy-managed-disk.md) och/eller säkerhetskopierar innan diskar krypteras. Säkerhetskopior Se till att ett återställningsalternativ är möjligt om ett oväntat fel inträffar under krypteringen. Virtuella datorer med hanterade diskar kräver en säkerhetskopia innan kryptering sker. Du kan använda cmdleten Set-AzVMDiskEncryptionExtension för att kryptera hanterade diskar genom att ange parametern - skipVmBackup när en säkerhetskopia görs. Mer information om hur du säkerhetskopiera och återställa krypterade virtuella datorer finns i den [Azure Backup](../backup/backup-azure-vms-encryption.md) artikeln. 

>[!WARNING]
> - Om du tidigare har använt [Azure Disk Encryption med Azure AD-app](azure-security-disk-encryption-prerequisites-aad.md) för att kryptera den här virtuella datorn, måste du fortsätta använda det här alternativet för att kryptera den virtuella datorn. Du kan inte använda [Azure Disk Encryption](azure-security-disk-encryption-prerequisites.md) på den här krypterade virtuella datorn eftersom det är inte ett scenario som stöds, betydelse växla från AAD-programmet för det här krypterade virtuella datorer stöds inte ännu.
 > - Azure Disk Encryption måste Key Vault och de virtuella datorerna för att finnas i samma region. Skapa och använda ett Nyckelvalv som är i samma region som den virtuella datorn måste vara krypterade.
> - När du krypterar Linux OS-volymer, bör den virtuella datorn betraktas som otillgänglig. Vi rekommenderar starkt för att undvika SSH-inloggningar medan kryptering pågår att undvika problem som blockerar alla öppna filer som måste användas under krypteringsprocessen. Kontrollera förloppet, den [Get-AzVMDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) eller [vm encryption show](/cli/azure/vm/encryption#az-vm-encryption-show) kommandon kan användas. Den här processen kan förväntas ta några timmar för en 30GB OS-volym, plus extra tid för att kryptera datavolymer. Data volym kryptering tiden kommer att vara proportion till storleken och antalet datavolymerna såvida inte encrypt formatera alla alternativet används. 
> - Inaktivera kryptering på den virtuella Linux-datorer stöds bara för datavolymer. Det stöds inte på data eller operativsystemvolymer om operativsystemvolymen har krypterats.  

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="bkmk_RunningLinux"> </a> Aktivera kryptering på en befintlig eller körs IaaS Linux virtuell dator
I det här scenariot kan du aktivera kryptering med hjälp av Resource Manager-mall, PowerShell-cmdletar eller CLI-kommandon. Om du behöver information för databasschema för tillägget för virtuell dator finns i den [Azure Disk Encryption for Linux-tillägget](../virtual-machines/extensions/azure-disk-enc-linux.md) artikeln.

>[!IMPORTANT]
 >Det är obligatoriskt att ögonblicksbilden och/eller säkerhetskopiera en hanterad disk baserat VM-instans utanför och innan du kan aktivera Azure Disk Encryption. En ögonblicksbild av den hantera disken kan tas från portalen eller [Azure Backup](../backup/backup-azure-vms-encryption.md) kan användas. Säkerhetskopior Se till att ett återställningsalternativ är möjligt när det gäller ett oväntat fel under krypteringen. Cmdleten Set-AzVMDiskEncryptionExtension kan användas för att kryptera hanterade diskar genom att ange parametern - skipVmBackup när en säkerhetskopia görs. Kommandot Set-AzVMDiskEncryptionExtension misslyckas mot virtuella datorer med hanterade diskar baserade tills en säkerhetskopia har gjorts och den här parametern har angetts. 
>
>Kryptering eller inaktivera kryptering leda till den virtuella datorn ska startas om. 
>

### <a name="bkmk_RunningLinuxCLI"> </a>Aktivera kryptering på en befintlig eller som kör Linux-VM med Azure CLI 
Du kan aktivera diskkryptering på en krypterad virtuell Hårddisk genom att installera och använda den [Azure CLI 2.0](/cli/azure) kommandoradsverktyget. Du kan använda det i webbläsaren med [Azure Cloud Shell](../cloud-shell/overview.md) eller installera det på din lokala dator och använda det i PowerShell-sessioner. Om du vill möjliggöra kryptering på befintliga eller kör IaaS virtuella Linux-datorer i Azure måste du använda följande CLI-kommandon:

Använd den [az vm encryption aktivera](/cli/azure/vm/encryption#az-vm-encryption-enable) kommando för att aktivera kryptering på en aktiv IaaS virtuell dator i Azure.

- **Kryptera en aktiv virtuell dator:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
     ```

- **Kryptera en aktiv virtuell dator med hjälp av KEK:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

    >[!NOTE]
    > Syntaxen för värdet för disk-kryptering-keyvault-parametern är fullständig ID-sträng: / subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br>
Syntaxen för värdet för parametern krypteringsnyckel-är den fullständiga URI som KEK som i: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Kontrollera att diskarna är krypterade:** Om du vill kontrollera statusen för kryptering av en IaaS-VM, använda den [az vm encryption show](/cli/azure/vm/encryption#az-vm-encryption-show) kommando. 

     ```azurecli-interactive
     az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
     ```

- **Inaktivera kryptering:** Inaktivera kryptering med den [az vm encryption inaktivera](/cli/azure/vm/encryption#az-vm-encryption-disable) kommando. Inaktivera kryptering tillåts endast på datavolymer för virtuella Linux-datorer.

     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type DATA
     ```

### <a name="bkmk_RunningLinuxPSH"> </a> Aktivera kryptering på en befintlig eller som kör Linux-VM med hjälp av PowerShell
Använd den [Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) cmdlet för att aktivera kryptering på en aktiv IaaS virtuell dator i Azure. Ta en [ögonblicksbild](../virtual-machines/windows/snapshot-copy-managed-disk.md) och/eller säkerhetskopiera den virtuella datorn med [Azure Backup](../backup/backup-azure-vms-encryption.md) innan diskar krypteras. Parametern - skipVmBackup har redan angetts i PowerShell-skript för att kryptera en som kör Linux-VM.

-  **Kryptera en aktiv virtuell dator:** Skriptet nedan initierar dina variabler och kör cmdleten Set-AzVMDiskEncryptionExtension. Den resursgrupp, virtuell dator och nyckelvalv, bör redan har skapats som krav. Ersätt MyVirtualMachineResourceGroup och MySecureVM MySecureVault med dina värden. Ändra parametern - VolumeType för att ange vilka diskar som du krypterar.

     ```azurepowershell
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MySecureVM';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;
      $sequenceVersion = [Guid]::NewGuid();  

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType '[All|OS|Data]' -SequenceVersion $sequenceVersion -skipVmBackup;
     ```
- **Kryptera en aktiv virtuell dator med hjälp av KEK:** Du kan behöva lägga till parametern - VolumeType om du krypterar datadiskar och inte OS-disken. 

     ```azurepowershell
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MyExtraSecureVM';
      $KeyVaultName = 'MySecureVault';
      $keyEncryptionKeyName = 'MyKeyEncryptionKey';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;
      $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;
      $sequenceVersion = [Guid]::NewGuid();  

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId -VolumeType '[All|OS|Data]' -SequenceVersion $sequenceVersion -skipVmBackup;
     ```

    >[!NOTE]
    > Syntaxen för värdet för disk-kryptering-keyvault-parametern är fullständig ID-sträng: / subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> Syntaxen för värdet för parametern krypteringsnyckel-är den fullständiga URI som KEK som i: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 
    
- **Kontrollera att diskarna är krypterade:** Om du vill kontrollera statusen för kryptering av en IaaS-VM, använda den [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) cmdlet. 
    
     ```azurepowershell-interactive 
     Get-AzVmDiskEncryptionStatus -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```
    
- **Inaktivera diskkryptering:** Om du vill inaktivera kryptering, använda den [inaktivera AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) cmdlet. Inaktivera kryptering tillåts endast på datavolymer för virtuella Linux-datorer.
     
     ```azurepowershell-interactive 
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```


### <a name="bkmk_RunningLinux"> </a> Aktivera kryptering på en befintlig eller körs IaaS Linux-dator med en mall

Du kan aktivera diskkryptering på en befintlig eller körs IaaS Linux-dator i Azure med hjälp av den [Resource Manager-mall](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm-without-aad).

1. Klicka på **distribuera till Azure** på Azure-snabbstartsmall.

2. Välj prenumeration, resursgrupp, resursgruppens plats, parametrar, juridiska villkor och avtal. Klicka på **skapa** att aktivera kryptering på den befintliga eller körs IaaS-VM.

I följande tabell visas Resource Manager-mallens parametrar för befintliga eller köra virtuella datorer:

| Parameter | Beskrivning |
| --- | --- |
| vmName | Namnet på den virtuella datorn att köra krypteringsåtgärden. |
| keyVaultName | Namnet på nyckelvalvet som BitLocker-nyckel ska överföras till. Du kan hämta den med hjälp av cmdleten `(Get-AzKeyVault -ResourceGroupName <MyKeyVaultResourceGroupName>). Vaultname` eller Azure CLI-kommando `az keyvault list --resource-group "MyKeyVaultResourceGroupName"`|
| keyVaultResourceGroup | Namnet på resursgruppen som innehåller nyckelvalvet|
|  KeyEncryptionKeyURL | URL för nyckelkrypteringsnyckel som används för att kryptera den genererade BitLocker-nyckeln. Den här parametern är valfri om du väljer **nokek** i listrutan UseExistingKek. Om du väljer **kek** i listrutan UseExistingKek måste du ange den _keyEncryptionKeyURL_ värde. |
| VolumeType | Typ av volym som krypteringsåtgärden utförs på. Giltiga värden är _OS_, _Data_, och _alla_. 
| forceUpdateTag | Skicka in ett unikt värde som en GUID varje gång åtgärden måste vara tvinga kör. |
| resizeOSDisk | Vill du ändra storlek OS-partition för att uppta fullständig OS-VHD innan du delar systemvolym. |
| location | Plats för alla resurser. |



## <a name="encrypt-virtual-machine-scale-sets"></a>Kryptera VM-skalningsuppsättningar
[Azure VM-skalningsuppsättningar](../virtual-machine-scale-sets/overview.md) kan du skapa och hantera grupper med identiska, belastningsutjämnade virtuella datorer. Antal VM-instanser kan automatiskt öka eller minska som svar på efterfrågan eller ett definierat schema. Använda CLI eller Azure PowerShell för att kryptera VM-skalningsuppsättningar. Endast kryptering av data-diskar stöds i Linux skalningsuppsättningarna för virtuella datorer.

Ett batch-filen exempel på disken för Linux scale set datakryptering finns [här](https://github.com/Azure-Samples/azure-cli-samples/tree/master/disk-encryption/vmss). Det här exemplet skapar en resursgrupp, en Linux-skalningsuppsättning, monterar en datadisk på 5 GB och krypterar virtuella datorns skalningsuppsättning.

### <a name="encrypt-virtual-machine-scale-sets-with-azure-cli"></a>Kryptera VM-skalningsuppsättningar med Azure CLI

Använd den [az vmss-kryptering aktiverar](/cli/azure/vmss/encryption#az-vmss-encryption-enable) att aktivera kryptering på en Windows VM-skalningsuppsättning. Om du anger uppgraderingsprincipen för skalningsuppsättningen att manuellt starta kryptering med [az vmss update-instances](/cli/azure/vmss#az-vmss-update-instances). Den resursgrupp, virtuell dator och nyckelvalvet bör redan har skapats som krav. 

-  **Kryptera en VM-skalningsuppsättning**
    ```azurecli-interactive
    az vmss encryption enable --resource-group "MyVMScaleSetResourceGroup" --name "MySecureVmss" --volume-type DATA --disk-encryption-keyvault "MySecureVault"
    ```

-  **Kryptera en aktiv virtuell dator skalningsuppsättning med KEK du omsluter nyckeln**
     ```azurecli-interactive
     az vmss encryption enable --resource-group "MyVMScaleSetResourceGroup" --name "MySecureVmss" --volume-type DATA --disk-encryption-keyvault "MySecureVault" --key-encryption-key "MyKEK" --key-encryption-keyvault "MySecureVault"
     ```

    >[!NOTE]
    > Syntaxen för värdet för disk-kryptering-keyvault-parametern är fullständig ID-sträng: / subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> Syntaxen för värdet för parametern krypteringsnyckel-är den fullständiga URI som KEK som i: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Hämta krypteringsstatus för en VM-skalningsuppsättning:** Använd [az vmss kryptering show](/cli/azure/vmss/encryption#az-vmss-encryption-show)

    ```azurecli-interactive
    az vmss encryption show --resource-group "MyVMScaleSetResourceGroup" --name "MySecureVmss"
    ```

- **Inaktivera kryptering på en VM-skalningsuppsättning**: Använd [az vmss kryptering inaktivera](/cli/azure/vmss/encryption#az-vmss-encryption-disable)
    ```azurecli-interactive
    az vmss encryption disable --resource-group "MyVMScaleSetResourceGroup" --name "MySecureVmss"
    ```

### <a name="encrypt-virtual-machine-scale-sets-with-azure-powershell"></a>Kryptera VM-skalningsuppsättningar med Azure PowerShell

Använd den [Set-AzVmssDiskEncryptionExtension](/powershell/module/az.compute/set-azvmssdiskencryptionextension) cmdlet för att aktivera kryptering på en Windows VM-skalningsuppsättning. Den resursgrupp, virtuell dator och nyckelvalvet bör redan har skapats som krav.

-  **Kryptera en VM-skalningsuppsättning**:
      ```powershell
     $KVRGname = 'MyKeyVaultResourceGroup';
     $VMSSRGname = 'MyVMScaleSetResourceGroup';
      $VmssName = "MySecureVmss";
      $KeyVaultName= "MySecureVault";
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;
      Set-AzVmssDiskEncryptionExtension -ResourceGroupName $VMSSRGname -VMScaleSetName $VmssName -VolumeType Data -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId;
      ```

-  **Kryptera en aktiv virtuell dator skalningsuppsättning med KEK du omsluter nyckeln**:
      ```powershell
     $KVRGname = 'MyKeyVaultResourceGroup';
     $VMSSRGname = 'MyVMScaleSetResourceGroup';
      $VmssName = "MySecureVmss";
      $KeyVaultName= "MySecureVault";
      $keyEncryptionKeyName = "MyKeyEncryptionKey";
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;
      $KeyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;
      Set-AzVmssDiskEncryptionExtension -ResourceGroupName $VMSSRGname -VMScaleSetName $VmssName -VolumeType Data -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl  -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $KeyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId;
      ```

    >[!NOTE]
    > Syntaxen för värdet för disk-kryptering-keyvault-parametern är fullständig ID-sträng: / subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> Syntaxen för värdet för parametern krypteringsnyckel-är den fullständiga URI som KEK som i: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 

- **Hämta krypteringsstatus för en virtuell dator uppsättning**: Använd den [Get-AzVmssVMDiskEncryption](/powershell/module/az.compute/get-azvmssvmdiskencryption) cmdlet.
    
    ```powershell
    Get-AzVmssVMDiskEncryption -ResourceGroupName "MyVMScaleSetResourceGroup" -VMScaleSetName "MySecureVmss"
    ```

- **Inaktivera kryptering på en VM-skalningsuppsättning**: Använd den [inaktivera AzVmssDiskEncryption](/powershell/module/az.compute/disable-azvmssdiskencryption) cmdlet. 

    ```powershell
    Disable-AzVmssDiskEncryption -ResourceGroupName "MyVMScaleSetResourceGroup" -VMScaleSetName "MySecureVmss"
    ```

### <a name="azure-resource-manager-templates-for-linux-virtual-machine-scale-sets"></a>Anger om Azure Resource Manager-mallar för Linux VM-skalningsuppsättning

Om du vill kryptera eller dekryptera Linux VM-skalningsuppsättning genom att använda Azure Resource Manager-mallar och anvisningarna nedan:

- [Aktivera kryptering på en Linux VM-skalningsuppsättning](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-vmss-linux)
- [Inaktivera kryptering på en Linux VM-skalningsuppsättning](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-vmss-linux)

     1. Klicka på **Distribuera till Azure**.
     2. Fyll i de obligatoriska fälten och samtycker till villkoren.
     3. Klicka på **köp** att distribuera mallen.

## <a name="bkmk_EFA"> </a>Använd EncryptFormatAll funktion för datadiskar på Linux IaaS-datorer

Den **EncryptFormatAll** parametern minskar tid för Linux datadiskar måste vara krypterade. Partitioner som uppfyller vissa kriterier formateras (med dess aktuella filsystem). De kommer att återmonteras tillbaka till föregående Kommandokörning. Om du vill exkludera en datadisk som uppfyller villkoren kan demontera du den innan du kör kommandot.

 När du har kört det här kommandot kommer eventuella enheter som har monterats tidigare att formateras. Krypteringslagret kan sedan startas ovanpå nu tom enheten. När det här alternativet är markerat krypteras också tillfälliga resursdisk som är ansluten till den virtuella datorn. Om den tillfälliga enheten återställs den formateras om och omkrypteras med hjälp av Azure Disk Encryption-lösningen vid nästa tillfälle för den virtuella datorn.

>[!WARNING]
> EncryptFormatAll bör inte användas när det nödvändiga data på en virtuell dators datavolymer. Du kan undanta diskar från kryptering av demontera dem. Du bör först prova EncryptFormatAll först på en virtuell testdator, Förstå funktionsparametern och dess de innan du försöker på den Virtuella produktionsdatorn. Alternativet EncryptFormatAll formaterar datadisken och alla data på den kommer att gå förlorade. Kontrollera att du vill utesluta diskar är korrekt demonterats innan du fortsätter. </br></br>
 >Om du konfigurerar den här parametern vid uppdatering av krypteringsinställningar, kan det leda till en omstart innan du själva krypteringen. I det här fallet vill du också ta bort disken som du inte vill formaterade från fstab-filen. På samma sätt bör du lägga till den partition som du vill kryptera-formaterad till fstab-filen innan du påbörjar krypteringsåtgärden. 

### <a name="bkmk_EFACriteria"> </a> EncryptFormatAll kriterier
Parametern går dock alla partitioner och krypterar dem så länge de uppfyller **alla** av följande villkor: 
- Är inte en rot-OS-Start-partition
- Krypteras inte redan
- Är inte en BEK volym
- Är inte en RAID-volym
- Är inte en LVM-volym
- Är monterad

Kryptera diskar som utgör RAID- eller LVM volymen i stället för RAID- eller LVM volymen.

### <a name="bkmk_EFAPSH"> </a> Använd parametern EncryptFormatAll med Azure CLI
Använd den [az vm encryption aktivera](/cli/azure/vm/encryption#az-vm-encryption-enable) kommando för att aktivera kryptering på en aktiv IaaS virtuell dator i Azure.

-  **Kryptera en aktiv virtuell dator med hjälp av EncryptFormatAll:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --encrypt-format-all
     ```

### <a name="bkmk_EFAPSH"> </a> Använd parametern EncryptFormatAll med en PowerShell-cmdlet
Använd den [Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) cmdlet med parametern EncryptFormatAll. 

**Kryptera en aktiv virtuell dator med hjälp av EncryptFormatAll:** Till exempel skriptet nedan initierar dina variabler och kör cmdleten Set-AzVMDiskEncryptionExtension med parametern EncryptFormatAll. Den resursgrupp, virtuell dator och nyckelvalvet bör redan har skapats som krav. Ersätt MyVirtualMachineResourceGroup och MySecureVM MySecureVault med dina värden.
  
```azurepowershell
$KVRGname = 'MyKeyVaultResourceGroup';
$VMRGName = 'MyVirtualMachineResourceGroup';
$vmName = 'MySecureVM';
$KeyVaultName = 'MySecureVault';
$KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
$diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
$KeyVaultResourceId = $KeyVault.ResourceId;

Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -EncryptFormatAll
```


### <a name="bkmk_EFALVM"> </a> Använd parametern EncryptFormatAll med logiska Volume Manager (LVM) 
Vi rekommenderar en LVM-på-crypt-installation. Ersätt enhetens sökväg och monteringspunkter med det passar ditt användningsområde för alla i följande exempel. Den här konfigurationen kan du göra på följande sätt:

- Lägga till datadiskar som kommer utgör den virtuella datorn.
- Formatera, montera och lägga till diskarna i fstab-filen.

    1. Formatera den nytillagda disken. Vi använder symlinks som genereras av Azure här. Med hjälp av symlinks undviker problem som rör enhetsnamn ändras. Mer information finns i den [felsöka enhetsnamn problem](../virtual-machines/linux/troubleshoot-device-names-problems.md) artikeln.
    
         `mkfs -t ext4 /dev/disk/azure/scsi1/lun0`
    
    2. Montera diskarna.
         
         `mount /dev/disk/azure/scsi1/lun0 /mnt/mountpoint`
    
    3. Lägg till i fstab.
         
        `echo "/dev/disk/azure/scsi1/lun0 /mnt/mountpoint ext4 defaults,nofail 1 2" >> /etc/fstab`
    
    4. Kör Set-AzVMDiskEncryptionExtension PowerShell-cmdlet med - EncryptFormatAll att kryptera dessa diskar.
         ```azurepowershell-interactive
         Set-AzVMDiskEncryptionExtension -ResourceGroupName "MySecureGroup" -VMName "MySecureVM" -DiskEncryptionKeyVaultUrl "https://mykeyvault.vault.azure.net/" -EncryptFormatAll
         ```
    5. Konfigurera LVM ovanpå dessa nya diskar. Observera enheterna som är krypterade är upplåst när den virtuella datorn har slutförts startar. LVM-montering måste därför också fördröjas senare.


## <a name="bkmk_VHDpre"> </a> Nya virtuella IaaS-datorer skapas från kund-krypterad VHD och kryptering nycklar
Du kan aktivera kryptering med hjälp av PowerShell-cmdletar eller CLI-kommandona i det här scenariot. 

Följ instruktionerna i tillägget för att förbereda förkrypterade avbildningar som kan användas i Azure. När avbildningen har skapats kan använda du stegen i nästa avsnitt för att skapa en krypterad virtuell Azure-dator.

* [Förbered en förkrypterade Windows virtuell Hårddisk](azure-security-disk-encryption-appendix.md#bkmk_preWin)
* [Förbereda en förkrypterade Linux-VHD](azure-security-disk-encryption-appendix.md#bkmk_preLinux)

>[!IMPORTANT]
 >Det är obligatoriskt att ögonblicksbilden och/eller säkerhetskopiera en hanterad disk baserat VM-instans utanför och innan du kan aktivera Azure Disk Encryption. En ögonblicksbild av den hantera disken kan tas från portalen eller [Azure Backup](../backup/backup-azure-vms-encryption.md) kan användas. Säkerhetskopior Se till att ett återställningsalternativ är möjligt när det gäller ett oväntat fel under krypteringen. Cmdleten Set-AzVMDiskEncryptionExtension kan användas för att kryptera hanterade diskar genom att ange parametern - skipVmBackup när en säkerhetskopia görs. Kommandot Set-AzVMDiskEncryptionExtension misslyckas mot virtuella datorer med hanterade diskar baserade tills en säkerhetskopia har gjorts och den här parametern har angetts. 
>
>Kryptering eller inaktivera kryptering leda till den virtuella datorn ska startas om. 



### <a name="bkmk_VHDprePSH"> </a> Använda Azure PowerShell för att kryptera virtuella IaaS-datorer med förkrypterade virtuella hårddiskar 
Du kan aktivera diskkryptering på en krypterad virtuell Hårddisk med hjälp av PowerShell-cmdleten [Set-AzVMOSDisk](/powershell/module/Az.Compute/Set-AzVMOSDisk#examples). I exemplet nedan visas några vanliga parametrar. 

```azurepowershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Windows -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MyVirtualMachineResourceGroup"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>Aktivera kryptering på en nyligen tillagd datadisk

Du kan lägga till en ny disk med [az vm disk attach](../virtual-machines/linux/add-disk.md), eller [via Azure portal](../virtual-machines/linux/attach-disk-portal.md). Innan du kan kryptera måste du först montera den nyligen anslutna disken. Du måste begära kryptering av data-enheten eftersom enheten inte att använda medan kryptering pågår. 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-cli"></a>Aktivera kryptering på en nyligen tillagd disk med Azure CLI

 Om den virtuella datorn som har krypterats med ”alla” sedan--volymtyp parametern bör vara alla. Alla innehåller både operativsystem och datadiskar. Om den virtuella datorn tidigare har krypterats med en volymtyp av ”OS” och sedan--volymtyp parametern bör ändras till alla så att både Operativsystemet och den nya datadisken inkluderas. Om den virtuella datorn har krypterats med endast volymtyp ”data”, kan det finnas ”Data” som visas nedan. Lägga till och koppla en ny datadisk till en virtuell dator är inte tillräckligt med förberedelse för kryptering. Den nyligen anslutna disken måste också vara formaterad och korrekt monterade i den virtuella datorn innan du aktiverar krypteringen. I Linux måste disken monteras i/etc/fstab med en [beständiga block enhetsnamn](https://docs.microsoft.com/azure/virtual-machines/linux/troubleshoot-device-names-problems).  

Till skillnad från Powershell-syntax kräver CLI inte att användaren anger en unik teckensekvens version när du aktiverar kryptering. CLI genererar automatiskt och använder en egen unik teckensekvens värde.

-  **Kryptera datavolymer med en aktiv virtuell dator:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type "Data"
     ```

- **Kryptera datavolymer med en aktiv virtuell dator med hjälp av KEK:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "Data"
     ```

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Aktivera kryptering på en nyligen tillagd disk med Azure PowerShell
 När du använder Powershell för att kryptera en ny disk för Linux, måste en ny sekvens-version anges. Sekvens-versionen måste vara unikt. Skriptet nedan genererar ett GUID för sekvens-versionen. Ta en [ögonblicksbild](../virtual-machines/windows/snapshot-copy-managed-disk.md) och/eller säkerhetskopiera den virtuella datorn med [Azure Backup](../backup/backup-azure-vms-encryption.md) innan diskar krypteras. Parametern - skipVmBackup har redan angetts i PowerShell-skript för att kryptera en nyligen tillagd datadisk.
 

-  **Kryptera datavolymer med en aktiv virtuell dator:** Skriptet nedan initierar dina variabler och kör cmdleten Set-AzVMDiskEncryptionExtension. Den resursgrupp, virtuell dator och nyckelvalvet bör redan har skapats som krav. Ersätt MyVirtualMachineResourceGroup och MySecureVM MySecureVault med dina värden. Godkända värden för parametern - VolumeType är alla, OS- och Data. Om den virtuella datorn har tidigare har krypterats med en volymtyp av ”OS” eller ”alla”, ska sedan parametern - VolumeType ändras till alla så att både Operativsystemet och den nya datadisken inkluderas.

      ```azurepowershell
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MySecureVM';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;
      $sequenceVersion = [Guid]::NewGuid();

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType 'data' –SequenceVersion $sequenceVersion -skipVmBackup;
      ```
- **Kryptera datavolymer med en aktiv virtuell dator med hjälp av KEK:** Godkända värden för parametern - VolumeType är alla, OS- och Data. Om den virtuella datorn har tidigare har krypterats med en volymtyp av ”OS” eller ”alla”, ska sedan parametern - VolumeType ändras till alla så att både Operativsystemet och den nya datadisken inkluderas.

     ```azurepowershell
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MyExtraSecureVM';
      $KeyVaultName = 'MySecureVault';
      $keyEncryptionKeyName = 'MyKeyEncryptionKey';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;
      $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;
      $sequenceVersion = [Guid]::NewGuid();

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId -VolumeType 'data' –SequenceVersion $sequenceVersion -skipVmBackup;
     ```

    >[!NOTE]
    > Syntaxen för värdet för disk-kryptering-keyvault-parametern är fullständig ID-sträng: / subscriptions/[subscription-id-guid]/resourceGroups/[KVresource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br> Syntaxen för värdet för parametern krypteringsnyckel-är den fullständiga URI som KEK som i: https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] 


## <a name="disable-encryption-for-linux-vms"></a>Inaktivera kryptering för virtuella Linux-datorer
Du kan inaktivera kryptering med Azure PowerShell, Azure CLI, eller med en Resource Manager-mall. 

>[!IMPORTANT]
>Inaktivera kryptering med Azure Disk Encryption på virtuella Linux-datorer stöds bara för datavolymer. Det stöds inte på data eller operativsystemvolymer om operativsystemvolymen har krypterats.  

- **Inaktivera disk encryption med Azure PowerShell:** Om du vill inaktivera kryptering, använda den [inaktivera AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) cmdlet. 
     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM' [-VolumeType {ALL, DATA, OS}]
     ```

- **Inaktivera kryptering med Azure CLI:** Inaktivera kryptering med den [az vm encryption inaktivera](/cli/azure/vm/encryption#az-vm-encryption-disable) kommando. 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```
- **Inaktivera kryptering med en Resource Manager-mall:** Använd den [inaktivera kryptering på en som kör Linux VM](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-linux-vm-without-aad) mall för att inaktivera kryptering.
     1. Klicka på **Distribuera till Azure**.
     2. Välj prenumeration, resursgrupp, plats, VM, juridiska villkor och avtal.
     3.  Klicka på **köp** att inaktivera diskkryptering på en aktiv virtuell Windows-dator. 


## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Aktivera Azure Disk Encryption för Windows](azure-security-disk-encryption-windows.md)
