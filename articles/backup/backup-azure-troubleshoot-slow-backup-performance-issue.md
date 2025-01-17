---
title: Felsökning av långsam säkerhetskopiering av filer och mappar i Azure Backup
description: Innehåller felsökningsinformation för att hjälpa dig att diagnostisera orsaken till problem med Azure Backup-prestanda
services: backup
author: saurabhsensharma
manager: saurabhsensharma
ms.service: backup
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: saurabhsensharma
ms.openlocfilehash: 1bc9c7b4f6e2a4f2a7c712d966caac74b73518df
ms.sourcegitcommit: d2785f020e134c3680ca1c8500aa2c0211aa1e24
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/04/2019
ms.locfileid: "67565671"
---
# <a name="troubleshoot-slow-backup-of-files-and-folders-in-azure-backup"></a>Felsökning av långsam säkerhetskopiering av filer och mappar i Azure Backup
Den här artikeln innehåller felsökningsinformation för att hjälpa dig att diagnostisera orsaken till långsamma prestanda vid säkerhetskopiering för filer och mappar när du använder Azure Backup. När du använder Azure Backup-agenten för säkerhetskopiering av filer, kan säkerhetskopieringen ta längre tid än förväntat. Den här fördröjningen kan orsakas av en eller flera av följande:

* [Det finns flaskhalsar i prestanda på datorn som säkerhetskopieras.](#cause1)
* [En annan process eller ett antivirusprogram hindrar Azure Backup-processen.](#cause2)
* [Backup-agenten körs på en Azure-dator (VM).](#cause3)  
* [Du säkerhetskopierar ett stort antal (miljoner) filer.](#cause4)

Innan du startar felsökning av problem, rekommenderar vi att du hämtar och installerar den [senaste Azure Backup-agenten](https://aka.ms/azurebackup_agent). Vi gör frekventa uppdateringar för Backup-agenten för att åtgärda problemen, lägga till funktioner och förbättra prestanda.

Vi rekommenderar också starkt att du läser den [Azure Backup service FAQ](backup-azure-backup-faq.md) att kontrollera att du inte har någon av de vanligaste konfigurationsproblemen som.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

<a id="cause1"></a>

## <a name="cause-performance-bottlenecks-on-the-computer"></a>Orsak: Flaskhalsar i prestanda på datorn
Flaskhalsar på datorn som säkerhetskopieras kan orsaka fördröjningar. Datorns möjligheten att läsa eller skriva till disk eller tillgänglig bandbredd för att skicka data över nätverket, kan till exempel orsaka flaskhalsar.

Windows tillhandahåller ett inbyggda verktyg som kallas [Prestandaövervakaren](https://technet.microsoft.com/magazine/2008.08.pulse.aspx) (Perfmon) för att identifiera dessa flaskhalsar.

Här följer några prestandaräknare och adressintervall som kan vara till hjälp diagnosticera flaskhalsar för optimal säkerhetskopiering.

| Räknaren | Status |
| --- | --- |
| Logisk Disk (fysiska disken)--% inaktiv |• 100% inaktiv till 50% inaktiv = felfri</br>• 49% inaktiv till 20% inaktiv = varnings- eller Övervakare</br>• 19% inaktiv till 0% inaktiv = kritiska eller av Spec |
| Logisk Disk (fysiska disken)--% genomsn. Disk sek läsning eller skrivning |• 0,001 ms till 0.015 ms = felfri</br>• 0.015 ms till 0.025 ms = varnings- eller Övervakare</br>• 0.026 ms eller längre = kritiska eller av Spec |
| Logisk Disk (fysiska disken) – Aktuell diskkölängd (för alla instanser) |80 begäranden i mer 6 minuter |
| Minne – icke växlingsbart systemminne-byte |• Mindre än 60% av poolen som förbrukas = felfri<br>• 61% till 80% av pool förbrukas = varnings- eller Övervakare</br>• Är större än 80% pool förbrukas = kritiska eller av Spec |
| Minne – växlingsbart systemminne-byte |• Mindre än 60% av poolen som förbrukas = felfri</br>• 61% till 80% av pool förbrukas = varnings- eller Övervakare</br>• Är större än 80% pool förbrukas = kritiska eller av Spec |
| Minne – tillgängliga megabyte |• 50% av ledigt minne som är tillgängliga eller mer = felfri</br>• 25% av ledigt minne som är tillgängliga = skärm</br>• 10% av ledigt minne som är tillgängliga = varning</br>• Mindre än 100 MB eller 5% av ledigt minne som är tillgängliga = kritiska eller av Spec |
| Processor--\%Processor Time (all instances) |• Less than 60% consumed = Healthy</br>• 61% to 90% consumed = Monitor or Caution</br>• 91% to 100% consumed = Critical |

> [!NOTE]
> If you determine that the infrastructure is the culprit, we recommend that you defragment the disks regularly for better performance.
>
>

<a id="cause2"></a>

## Cause: Another process or antivirus software interfering with Azure Backup
We've seen several instances where other processes in the Windows system have negatively affected performance of the Azure Backup agent process. For example, if you use both the Azure Backup agent and another program to back up data, or if antivirus software is running and has a lock on files to be backed up, the multiple locks on files might cause contention. In this situation, the backup might fail, or the job might take longer than expected.

The best recommendation in this scenario is to turn off the other backup program to see whether the backup time for the Azure Backup agent changes. Usually, making sure that multiple backup jobs are not running at the same time is sufficient to prevent them from affecting each other.

For antivirus programs, we recommend that you exclude the following files and locations:

* C:\Program Files\Microsoft Azure Recovery Services Agent\bin\cbengine.exe as a process
* C:\Program Files\Microsoft Azure Recovery Services Agent\ folders
* Scratch location (if you're not using the standard location)

<a id="cause3"></a>

## Cause: Backup agent running on an Azure virtual machine
If you're running the Backup agent on a VM, performance will be slower than when you run it on a physical machine. This is expected due to IOPS limitations.  However, you can optimize the performance by switching the data drives that are being backed up to Azure Premium Storage. We're working on fixing this issue, and the fix will be available in a future release.

<a id="cause4"></a>

## Cause: Backing up a large number (millions) of files
Moving a large volume of data will take longer than moving a smaller volume of data. In some cases, backup time is related to not only the size of the data, but also the number of files or folders. This is especially true when millions of small files (a few bytes to a few kilobytes) are being backed up.

This behavior occurs because while you're backing up the data and moving it to Azure, Azure is simultaneously cataloging your files. In some rare scenarios, the catalog operation might take longer than expected.

The following indicators can help you understand the bottleneck and accordingly work on the next steps:

* **UI is showing progress for the data transfer**. The data is still being transferred. The network bandwidth or the size of data might be causing delays.
* **UI is not showing progress for the data transfer**. Open the logs located at C:\Program Files\Microsoft Azure Recovery Services Agent\Temp, and then check for the FileProvider::EndData entry in the logs. This entry signifies that the data transfer finished and the catalog operation is happening. Don't cancel the backup jobs. Instead, wait a little longer for the catalog operation to finish. If the problem persists, contact [Azure support](https://portal.azure.com/#create/Microsoft.Support).Processor –\`processortid (alla instanser)es and folders in Azure Backup
description: Provides troubleshooting guidance to help you diagnose the cause of Azure Backup performance issues
services: backup
author: saurabhsensharma
manager: saurabhsensharma
ms.service: backup
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: saurabhsensharma
---
# Troubleshoot slow backup of files and folders in Azure Backup
This article provides troubleshooting guidance to help you diagnose the cause of slow backup performance for files and folders when you're using Azure Backup. When you use the Azure Backup agent to back up files, the backup process might take longer than expected. This delay might be caused by one or more of the following:

* [There are performance bottlenecks on the computer that’s being backed up.](#cause1)
* [Another process or antivirus software is interfering with the Azure Backup process.](#cause2)
* [The Backup agent is running on an Azure virtual machine (VM).](#cause3)  
* [You're backing up a large number (millions) of files.](#cause4)

Before you start troubleshooting issues, we recommend that you download and install the [latest Azure Backup agent](https://aka.ms/azurebackup_agent). We make frequent
updates to the Backup agent to fix various issues, add features, and improve performance.

We also strongly recommend that you review the [Azure Backup service FAQ](backup-azure-backup-faq.md) to make sure you're not experiencing any of the common configuration issues.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

<a id="cause1"></a>

## Cause: Performance bottlenecks on the computer
Bottlenecks on the computer that's being backed up can cause delays. For example, the computer's ability to read or write to disk, or available bandwidth to send data over the network, can cause bottlenecks.

Windows provides a built-in tool that's called [Performance Monitor](https://technet.microsoft.com/magazine/2008.08.pulse.aspx) (Perfmon) to detect these bottlenecks.

Here are some performance counters and ranges that can be helpful in diagnosing bottlenecks for optimal backups.

| Counter | Status |
| --- | --- |
| Logical Disk(Physical Disk)--%idle |• 100% idle to 50% idle = Healthy</br>• 49% idle to 20% idle = Warning or Monitor</br>• 19% idle to 0% idle = Critical or Out of Spec |
| Logical Disk(Physical Disk)--%Avg. Disk Sec Read or Write |• 0.001 ms to 0.015 ms  = Healthy</br>• 0.015 ms to 0.025 ms = Warning or Monitor</br>• 0.026 ms or longer = Critical or Out of Spec |
| Logical Disk(Physical Disk)--Current Disk Queue Length (for all instances) |80 requests for more than 6 minutes |
| Memory--Pool Non Paged Bytes |• Less than 60% of pool consumed = Healthy<br>• 61% to 80% of pool consumed = Warning or Monitor</br>• Greater than 80% pool consumed = Critical or Out of Spec |
| Memory--Pool Paged Bytes |• Less than 60% of pool consumed = Healthy</br>• 61% to 80% of pool consumed = Warning or Monitor</br>• Greater than 80% pool consumed = Critical or Out of Spec |
| Memory--Available Megabytes |• 50% of free memory available or more = Healthy</br>• 25% of free memory available = Monitor</br>• 10% of free memory available = Warning</br>• Less than 100 MB or 5% of free memory available = Critical or Out of Spec |
| Processor--\%Processor Time (all instances) |• Mindre än 60% förbrukas = felfri</br>• 61% till 90% förbrukas = övervakaren eller varning</br>• 91 till 100% förbrukas = kritiskt |

> [!NOTE]
> Om du har fastställt att infrastrukturen är sällan, rekommenderar vi att du defragmenterar diskar för bättre prestanda med jämna mellanrum.
>
>

<a id="cause2"></a>

## <a name="cause-another-process-or-antivirus-software-interfering-with-azure-backup"></a>Orsak: En annan process eller ett antivirusprogram som stör Azure Backup
Vi har sett flera instanser där andra processer i Windows-systemet negativt påverkas prestandan för Azure Backup agent-processen. Till exempel om du använder både Azure Backup-agenten och ett annat program för säkerhetskopiering av data, eller om antivirusprogrammet körs och har ett lås på filer som ska säkerhetskopieras, multipeln låser på orsaka filerna konkurrens. I det här fallet säkerhetskopieringen misslyckas eller jobbet kan ta längre tid än förväntat.

Bästa val i det här scenariot är att stänga av den andra säkerhetskopieringsprogrammet för att se om det ändrar säkerhetskopieringstiden för Azure Backup-agenten. Se till att flera säkerhetskopieringsjobb inte körs på samma gång är vanligtvis är tillräckliga för att förhindra att de påverkar varandra.

För antivirusprogram rekommenderar vi att du undantar följande filer och platser:

* C:\Program Files\Microsoft Azure Recovery Services Agent\bin\cbengine.exe som en process
* C:\Program Files\Microsoft Azure Recovery Services Agent\ mappar
* Tillfällig plats (om du inte använder standardplatsen)

<a id="cause3"></a>

## <a name="cause-backup-agent-running-on-an-azure-virtual-machine"></a>Orsak: Backup-agenten som körs på virtuella Azure-datorer
Om du kör Backup-agenten på en virtuell dator, kommer vara långsammare än när du kör på en fysisk dator. Detta förväntas på grund av begränsningar av IOPS.  Men kan du optimera prestanda genom att växla mellan de enheter som ska säkerhetskopieras till Azure Premium Storage. Vi arbetar på att åtgärda problemet och korrigeringen kommer att finnas i en framtida version.

<a id="cause4"></a>

## <a name="cause-backing-up-a-large-number-millions-of-files"></a>Orsak: Säkerhetskopiering av ett stort antal (miljoner) filer
Flytta stora mängder data tar längre tid än att flytta en mindre mängd data. I vissa fall kan rör säkerhetskopieringstiden inte bara storleken på data, utan också hur många filer eller mappar. Detta gäller särskilt när miljontals små filer (några byte till några få kilobyte) säkerhetskopieras.

Detta beror på att när du säkerhetskopiering av data och flytta den till Azure, Azure samtidigt omkatalogiseras, dina filer. I vissa sällsynta fall kan kan katalogen åtgärden ta längre tid än förväntat.

Följande indikatorer kan hjälpa dig att förstå flaskhalsen och därefter arbetar med nästa steg:

* **Användargränssnittet visar förloppet för dataöverföringen**. Data överförs fortfarande. Nätverkets bandbredd eller storleken på data som orsakar fördröjningar.
* **Användargränssnittet visas inte förloppet för dataöverföringen**. Öppna loggar i C:\Program Files\Microsoft Azure Recovery Services Agent\Temp och sedan söka efter posten FileProvider::EndData i loggarna. Den här posten innebär det att dataöverföringen har slutförts och catalog åtgärden sker. Avbryt inte säkerhetskopieringsjobben. I stället vänta lite längre catalog-åtgärden slutförs. Om problemet kvarstår kontaktar du [Azure-supporten](https://portal.azure.com/#create/Microsoft.Support).
