---
title: Vanliga frågor och svar om säkerhetskopiering av virtuella Azure-datorer med Azure Backup
description: Svar på vanliga frågor om hur du säkerhetskopierar virtuella Azure-datorer med Azure Backup.
services: backup
author: sogup
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: sogup
ms.openlocfilehash: 0248e169f5d502cce8723f594f438b87ab088f3a
ms.sourcegitcommit: 978e1b8cac3da254f9d6309e0195c45b38c24eb5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/03/2019
ms.locfileid: "67551600"
---
# <a name="frequently-asked-questions-back-up-azure-vms"></a>Vanliga frågor: Säkerhetskopiera virtuella Azure-datorer

Den här artikeln innehåller vanliga frågor och svar om säkerhetskopiering av virtuella Azure-datorer med den [Azure Backup](backup-introduction-to-azure-backup.md) service.


## <a name="backup"></a>Backup

### <a name="which-vm-images-can-be-enabled-for-backup-when-i-create-them"></a>Vilka VM-avbildningar kan aktiveras för säkerhetskopiering när jag skapar dem?
När du skapar en virtuell dator kan du aktivera säkerhetskopiering för virtuella datorer som kör [operativsystem som stöds](backup-support-matrix-iaas.md#supported-backup-actions)

### <a name="is-the-backup-cost-included-in-the-vm-cost"></a>Är kostnaden för säkerhetskopiering i VM-kostnad?

Nej. Kostnader för Backup skiljer sig från en virtuell dators kostnader. Läs mer om [priser för Azure Backup](https://azure.microsoft.com/pricing/details/backup/).

### <a name="which-permissions-are-required-to-enable-backup-for-a-vm"></a>Vilka behörigheter krävs för att aktivera säkerhetskopiering för en virtuell dator?

Om du är deltagare VM, kan du aktivera säkerhetskopiering på den virtuella datorn. Om du använder en anpassad roll, behöver du följande behörigheter till Aktivera säkerhetskopiering på den virtuella datorn:

- Microsoft.RecoveryServices/Vaults/write
- Microsoft.RecoveryServices/Vaults/read
- Microsoft.RecoveryServices/locations/*
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/*/read
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write
- Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write
- Microsoft.RecoveryServices/Vaults/backupPolicies/read
- Microsoft.RecoveryServices/Vaults/backupPolicies/write

Om dina Recovery Services-valv och en virtuell dator har olika resursgrupper kan du kontrollera att du har skrivbehörighet i resursgruppen för Recovery Services-valvet.  


### <a name="does-an-on-demand-backup-job-use-the-same-retention-schedule-as-scheduled-backups"></a>Använder en säkerhetskopiering på begäran samma kvarhållningsschema som schemalagda säkerhetskopieringar?
Nej. Ange kvarhållningsintervallet för en säkerhetskopiering på begäran. Som standard den finns kvar i 30 dagar när den utlöses från portalen.

### <a name="i-recently-enabled-azure-disk-encryption-on-some-vms-will-my-backups-continue-to-work"></a>Jag nyligen har aktiverat Azure Disk Encryption på vissa virtuella datorer. Kommer mina säkerhetskopior att fungera även i fortsättningen?
Ange behörigheter för Azure Backup för att få åtkomst till Key Vault. Ange behörigheter i PowerShell enligt beskrivningen i den **Aktivera säkerhetskopiering** i avsnittet den [Azure Backup PowerShell](backup-azure-vms-automation.md) dokumentation.

### <a name="i-migrated-vm-disks-to-managed-disks-will-my-backups-continue-to-work"></a>Jag har migrerat Virtuella datordiskar till hanterade diskar. Kommer mina säkerhetskopior att fungera även i fortsättningen?
Ja, säkerhetskopieringen fungerar smidigt. Det finns inget behov att konfigurera om vad som helst.

### <a name="why-cant-i-see-my-vm-in-the-configure-backup-wizard"></a>Varför kan jag inte se min virtuella dator i guiden Konfigurera säkerhetskopiering?
Guiden visar endast virtuella datorer i samma region som valvet och som inte redan som säkerhetskopieras.

### <a name="my-vm-is-shut-down-will-an-on-demand-or-a-scheduled-backup-work"></a>Den virtuella datorn är avstängd. Kommer en på begäran eller arbete med en schemalagd säkerhetskopiering?
Ja. Säkerhetskopieringar som körs när en dator är avstängd. Återställningspunkten har markerats som krascher konsekvent.

### <a name="can-i-cancel-an-in-progress-backup-job"></a>Kan jag säga upp en pågående säkerhetskopiering?
Ja. Du kan avbryta säkerhetskopieringsjobbet på en **tar ögonblicksbild** tillstånd. Du kan inte avbryta ett jobb om dataöverföring från ögonblicksbilden håller på att skapas.

### <a name="i-enabled-lock-on-resource-group-created-by-azure-backup-service-ie-azurebackuprggeonumber-will-my-backups-continue-to-work"></a>Jag har aktiverat låset på resursgruppen som skapats av Azure Backup-tjänsten (dvs.) `AzureBackupRG_<geo>_<number>`), kommer Mina säkerhetskopior att fortsätta att fungera?
Om du låser den resursgrupp som skapats av Azure Backup-tjänsten startar säkerhetskopieringar misslyckas eftersom det inte finns en maxgräns på 18 återställningspunkter.

Användaren måste ta bort låset och ta bort samlingen med återställningspunkter från resursgruppen för att att framtida säkerhetskopieringar ska lyckas, [gör så här](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#clean-up-restore-point-collection-from-azure-portal) att ta bort samlingen med återställningspunkter.


### <a name="does-azure-backup-support-standard-ssd-managed-disk"></a>Har Azure stöd för säkerhetskopiering SSD hanterade standarddiskar?
Azure Backup stöder [standard SSD-hanterade diskar](https://azure.microsoft.com/blog/announcing-general-availability-of-standard-ssd-disks-for-azure-virtual-machine-workloads/). SSD-hanterade diskar har en ny typ av beständig lagring för virtuella Azure-datorer. Stöd för SSD hanterade diskar finns i den [omedelbar återställning](backup-instant-restore-capability.md).

### <a name="can-we-back-up-a-vm-with-a-write-accelerator-wa-enabled-disk"></a>Kan vi säkerhetskopiera en virtuell dator med en WA Write Accelerator-aktiverade disk?
Ögonblicksbilder kan inte utföras på disken WA-aktiverade. Azure Backup-tjänsten kan dock utesluta WA-aktiverade disken från en säkerhetskopia.

### <a name="i-have-a-vm-with-write-accelerator-wa-disks-and-sap-hana-installed-how-do-i-back-up"></a>Jag har en virtuell dator med Write Accelerator (WA) diskar och SAP HANA installerad. Hur kan jag säkerhetskopiera?
Azure Backup kan inte säkerhetskopiera disken WA-aktiverade men kan undanta den från en säkerhetskopia. Säkerhetskopian Ange inte dock databaskonsekvens eftersom informationen på WA-aktiverade inte har säkerhetskopierats. Du kan säkerhetskopiera diskar med den här konfigurationen om du vill att operativsystemdisken, och säkerhetskopiering av diskar som inte är WA-aktiverade.

Vi använder privat förhandsgranskning för en SAP HANA-säkerhetskopia med ett Återställningspunktmål på 15 minuter. Den bygger på ett liknande sätt till SQL DB-säkerhetskopiering och använder gränssnittet backInt för lösningar från tredje part som certifierats av SAP HANA. Om du vill kan du maila `AskAzureBackupTeam@microsoft.com` med ämnet **registrera dig för privat förhandsgranskning för säkerhetskopiering av SAP HANA på Azure virtuella datorer i**.

### <a name="what-is-the-maximum-delay-i-can-expect-in-backup-start-time-from-the-scheduled-backup-time-i-have-set-in-my-vm-backup-policy"></a>Vad är den längsta tillåtna fördröjningen som jag kan förvänta sig i starttid för säkerhetskopiering från den schemalagda tiden för säkerhetskopiering som jag har angett i min princip för säkerhetskopiering av virtuell dator?
Den schemalagda säkerhetskopieringen aktiveras inom två timmar efter den schemalagda tiden för säkerhetskopiering. För t.ex. Om 100 virtuella datorer har starttid för säkerhetskopiering schemalagd vid 02:00:00, sedan har av högst 4:00 am alla 100VMs säkerhetskopieringsjobb pågår. Om schemalagda säkerhetskopieringar har pausats på grund av avbrott och återupptas/göras kan säkerhetskopiering starta utanför det här schemalagda 2 hr-fönstret.

### <a name="what-is-the-minimum-allowed-retention-range-for-daily-backup-point"></a>Vad är det minsta tillåtna kvarhållningsintervallet för daglig säkerhetskopieringspunkt?
Princip för säkerhetskopiering av Azure virtuella datorer stöder ett minsta Kvarhållningsintervall på 7 dagar upp till 9 999 dagar. Alla ändringar av säkerhetskopieringsprincip för en befintlig virtuell dator med mindre än 7 dagar kräver en uppdatering för att uppfylla minsta Kvarhållningsintervall på 7 dagar.

## <a name="restore"></a>Återställ

### <a name="how-do-i-decide-whether-to-restore-disks-only-or-a-full-vm"></a>Hur avgör jag om du vill återställa endast diskar eller en fullständig virtuell dator?
Tänk på en återställning av virtuella datorer som ett alternativ för Snabbregistrering för en Azure virtuell dator. Det här alternativet ändras disk namn, behållare som används av diskar, offentliga IP-adresser och namn för nätverksgränssnitt. Ändringen har unika resurser när en virtuell dator skapas. Den virtuella datorn inte har lagts till i en tillgänglighetsuppsättning.

Du kan använda alternativet Återställ disk om du vill:
  * Anpassa den virtuella datorn som skapas. Till exempel ändra storlek.
  * Lägg till konfigurationsinställningar som inte var det vid tidpunkten för säkerhetskopieringen.
  * Kontrollen Namngivningskonventionen för resurser som skapas.
  * Lägg till den virtuella datorn i en tillgänglighetsuppsättning.
  * Lägga till någon annan inställning som måste konfigureras med PowerShell eller en mall.

### <a name="can-i-restore-backups-of-unmanaged-vm-disks-after-i-upgrade-to-managed-disks"></a>Kan jag återställa säkerhetskopior av ohanterade Virtuella datordiskar efter uppgraderingen till managed disks?
Ja, du kan använda säkerhetskopior som har gjorts innan diskar har migrerats från ohanterade till hanterade.
- Som standard skapar en ohanterad virtuell dator i en virtuell dator återställningsjobbet.
- Du kan dock återställa diskar och använda dem för att skapa en hanterad virtuell dator.

### <a name="how-do-i-restore-a-vm-to-a-restore-point-before-the-vm-was-migrated-to-managed-disks"></a>Hur återställer jag en virtuell dator till en återställningspunkt innan den virtuella datorn har migrerats till hanterade diskar?
Som standard skapar en virtuell dator med ohanterade diskar i en virtuell dator återställningsjobbet. Skapa en virtuell dator med hanterade diskar:
1. [Återställa till ohanterade diskar](tutorial-restore-disk.md#restore-a-vm-disk).
2. [Konvertera de återställda diskarna till managed disks](tutorial-restore-disk.md#convert-the-restored-disk-to-a-managed-disk).
3. [Skapa en virtuell dator med hanterade diskar](tutorial-restore-disk.md#create-a-vm-from-the-restored-disk).

[Läs mer](backup-azure-vms-automation.md#restore-an-azure-vm) om hur du gör detta i PowerShell.

### <a name="can-i-restore-the-vm-thats-been-deleted"></a>Kan jag återställa den virtuella datorn som har tagits bort?
Ja. Även om du tar bort den virtuella datorn går du till motsvarande säkerhetskopiering objektet i valvet och återställa från en återställningspunkt.

### <a name="how-to-restore-a-vm-to-the-same-availability-sets"></a>Hur du återställer en virtuell dator med samma tillgänglighetsuppsättningar?
När du återställer till tillgänglighetsuppsättningar är aktiverat för hanterad Disk Azure-dator, genom att tillhandahålla ett alternativ i mallen vid återställning som hanterade diskar. Den här mallen har den indataparameter med namnet **tillgänglighetsuppsättningar**.

### <a name="how-do-we-get-faster-restore-performances"></a>Hur får vi snabbare återställning prestanda?
För snabbare återställningsprestanda vi flyttar till [omedelbar återställning](backup-instant-restore-capability.md) kapaciteten.

## <a name="manage-vm-backups"></a>Hantera säkerhetskopior av virtuella datorer

### <a name="what-happens-if-i-modify-a-backup-policy"></a>Vad händer om jag ändrar en princip för säkerhetskopiering?
Den virtuella datorn säkerhetskopieras med schema och kvarhållning inställningarna i den ändrade eller nya principen.

- Om kvarhållningen utökas markeras befintliga återställningspunkter och sparas i enlighet med den nya principen.
- Om kvarhållningen minskar är återställningspunkter markerad för rensning under nästa rensningsjobb och tas sedan bort.

### <a name="how-do-i-move-a-vm-backed-up-by-azure-backup-to-a-different-resource-group"></a>Hur gör jag för att flytta en virtuell dator som backas upp av Azure Backup till en annan resursgrupp?

1. Tillfälligt stoppa säkerhetskopiering och behåller säkerhetskopierade data.
2. Flytta den virtuella datorn till målresursgruppen.
3. Återaktiveras säkerhetskopiering i samma eller ett nytt valv.

Du kan återställa den virtuella datorn från tillgängliga återställningspunkter som skapades innan åtgärden för att flytta.

### <a name="is-there-a-limit-on-number-of-vms-that-can-beassociated-with-a-same-backup-policy"></a>Finns det en gräns för antalet virtuella datorer som kan associeras med en samma princip för säkerhetskopiering?
Ja, det finns en gräns på 100 virtuella datorer som kan associeras till samma säkerhetskopieringsprincip från portalen. Vi rekommenderar att mer än 100 virtuella datorer, skapa flera principer för säkerhetskopiering med samma schema eller annan schema.
