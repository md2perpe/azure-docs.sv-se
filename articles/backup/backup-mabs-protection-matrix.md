---
title: Vad kan Azure Backup Server säkerhetskopiera
description: Den här artikeln innehåller en lista över alla arbetsbelastningar, datatyper och installationer som skyddar Azure Backup Server stödmatris.
services: backup
author: rayne-wiselman
ms.service: backup
keywords: ''
ms.date: 11/13/2018
ms.topic: conceptual
ms.author: raynew
manager: carmonm
ms.openlocfilehash: 21c832235eeb86dcea94e70f34abaf4ec3489c3c
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2019
ms.locfileid: "67509615"
---
# <a name="azure-backup-server-protection-matrix"></a>Skyddsöversikt för Azure Backup Server

Den här artikeln visar de olika servrar och arbetsbelastningar som du kan skydda med Azure Backup Server. Följande matrisen visar vad som kan skyddas med Azure Backup Server.

## <a name="protection-support-matrix"></a>Supportmatris för skydd

|Arbetsbelastning|Version|Azure Backup Server</br> installation|Azure Backup</br> Server V3|Azure Backup</br> Server V2|Skydd och återställning|
|------------|-----------|---------------|--------------|---------------|-----------------|
|Klientdatorer (64-bitars och 32-bitars)|Windows 10|Fysisk server<br /><br />Virtuell Hyper-V-dator<br /><br />Virtuell VMware-dator|Y|Y|Volym, resurs, mapp, filer, deduplicerade volymer<br /><br />Skyddade volymer måste vara NTFS. FAT och FAT32 stöds inte.<br /><br />Volymer måste vara minst 1 GB. DPM använder Volume Shadow Copy Service (VSS) för att ta dataögonblicksbilden och ögonblicksbilden fungerar bara om volymen är minst 1 GB.|
|Klientdatorer (64-bitars och 32-bitars)|Windows 8.1|Fysisk server<br /><br />Virtuell Hyper-V-dator|Y|Y|Filer<br /><br />Skyddade volymer måste vara NTFS. FAT och FAT32 stöds inte.<br /><br />Volymer måste vara minst 1 GB. DPM använder Volume Shadow Copy Service (VSS) för att ta dataögonblicksbilden och ögonblicksbilden fungerar bara om volymen är minst 1 GB.|
|Klientdatorer (64-bitars och 32-bitars)|Windows 8.1|Windows-dator i VMWare (skyddar arbetsbelastningar som körs i Windows-dator i VMWare)|Y|Y|Volym, resurs, mapp, filer, deduplicerade volymer<br /><br />Skyddade volymer måste vara NTFS. FAT och FAT32 stöds inte.<br /><br />Volymer måste vara minst 1 GB. DPM använder Volume Shadow Copy Service (VSS) för att ta dataögonblicksbilden och ögonblicksbilden fungerar bara om volymen är minst 1 GB.|
|Klientdatorer (64-bitars och 32-bitars)|Windows 8|Fysisk server<br /><br />En lokal Hyper-V-dator|Y|Y|Volym, resurs, mapp, filer, deduplicerade volymer<br /><br />Skyddade volymer måste vara NTFS. FAT och FAT32 stöds inte.<br /><br />Volymer måste vara minst 1 GB. DPM använder Volume Shadow Copy Service (VSS) för att ta dataögonblicksbilden och ögonblicksbilden fungerar bara om volymen är minst 1 GB.|
|Klientdatorer (64-bitars och 32-bitars)|Windows 8|Windows-dator i VMWare (skyddar arbetsbelastningar som körs i Windows-dator i VMWare)|Y|Y|Volym, resurs, mapp, filer, deduplicerade volymer<br /><br />Skyddade volymer måste vara NTFS. FAT och FAT32 stöds inte.<br /><br />Volymer måste vara minst 1 GB. DPM använder Volume Shadow Copy Service (VSS) för att ta dataögonblicksbilden och ögonblicksbilden fungerar bara om volymen är minst 1 GB.|
|Klientdatorer (64-bitars och 32-bitars)|Windows 7|Fysisk server<br /><br />En lokal Hyper-V-dator|Y|Y|Volym, resurs, mapp, filer, deduplicerade volymer<br /><br />Skyddade volymer måste vara NTFS. FAT och FAT32 stöds inte.<br /><br />Volymer måste vara minst 1 GB. DPM använder Volume Shadow Copy Service (VSS) för att ta dataögonblicksbilden och ögonblicksbilden fungerar bara om volymen är minst 1 GB.|
|Klientdatorer (64-bitars och 32-bitars)|Windows 7|Windows-dator i VMWare (skyddar arbetsbelastningar som körs i Windows-dator i VMWare)|Y|Y|Volym, resurs, mapp, filer, deduplicerade volymer<br /><br />Skyddade volymer måste vara NTFS. FAT och FAT32 stöds inte.<br /><br />Volymer måste vara minst 1 GB. DPM använder Volume Shadow Copy Service (VSS) för att ta dataögonblicksbilden och ögonblicksbilden fungerar bara om volymen är minst 1 GB.|
|Servrar (32-bitars och 64-bitars)|Windows Server 2016|Azure-dator (när arbetsbelastningen körs som Azure-dator)<br /><br />Windows-dator i VMWare (skyddar arbetsbelastningar som körs i Windows-dator i VMWare)<br /><br />Fysisk server<br /><br />En lokal Hyper-V-dator<br /> <br /> Azure Stack|Y<br /><br />Inte Nano-server|Y<br /><br />Inte Nano-server|Volym, resurs, mapp, fil, systemtillstånd/utan operativsystem), deduplicerade volymer|
|Servrar (32-bitars och 64-bitars)|Windows Server 2012 R2 – Datacenter och Standard|Azure-dator (när arbetsbelastningen körs som Azure-dator)<br /> <br /> Azure Stack|Y|Y|Volym, resurs, mapp, fil<br /><br />DPM måste köras på Windows Server 2012 R2 för att skydda Windows Server 2012 deduplicerade volymer.|
|Servrar (32-bitars och 64-bitars)|Windows Server 2012 R2 – Datacenter och Standard|Windows-dator i VMWare (skyddar arbetsbelastningar som körs i Windows-dator i VMWare)<br /> <br /> Azure Stack|Y|Y|Volym, resurs, mapp, fil, systemtillstånd/utan operativsystem)<br /><br />DPM måste köras på Windows Server 2012 eller 2012 R2 för att skydda deduplicerade Windows Server 2012-volymer.|
|Servrar (32-bitars och 64-bitars)|Windows Server 2012/2012 med SP1 – Datacenter och Standard|Fysisk server<br /><br />En lokal Hyper-V-dator<br /> <br /> Azure Stack|Y|Y|Volym, resurs, mapp, fil, systemtillstånd/utan operativsystem<br /><br />DPM måste köras på Windows Server 2012 R2 för att skydda Windows Server 2012 deduplicerade volymer.|
|Servrar (32-bitars och 64-bitars)|Windows Server 2012/2012 med SP1 – Datacenter och Standard|Azure-dator (när arbetsbelastningen körs som Azure-dator)<br /> <br /> Azure Stack|Y|Y|Volym, resurs, mapp, fil<br /><br />DPM måste köras på Windows Server 2012 R2 för att skydda Windows Server 2012 deduplicerade volymer.|
|Servrar (32-bitars och 64-bitars)|Windows Server 2012/2012 med SP1 – Datacenter och Standard|Windows-dator i VMWare (skyddar arbetsbelastningar som körs i Windows-dator i VMWare)<br /> <br /> Azure Stack|Y|Y|Volym, resurs, mapp, fil, systemtillstånd/utan operativsystem<br /><br />DPM måste köras på Windows Server 2012 R2 för att skydda Windows Server 2012 deduplicerade volymer.|
|Servrar (32-bitars och 64-bitars)|Windows Server 2008 R2 SP1 – Standard eller Enterprise|Fysisk server<br /><br />En lokal Hyper-V-dator<br /> <br /> Azure Stack|Y<br /><br />Du måste köra SP1 och installera [Windows Management Frame 4.0](https://www.microsoft.com/download/details.aspx?id=40855)|Y<br /><br />Du måste köra SP1 och installera [Windows Management Frame 4.0](https://www.microsoft.com/download/details.aspx?id=40855)|Volym, resurs, mapp, fil, systemtillstånd/utan operativsystem|
|Servrar (32-bitars och 64-bitars)|Windows Server 2008 R2 SP1 – Standard eller Enterprise|Azure-dator (när arbetsbelastningen körs som Azure-dator)<br /> <br /> Azure Stack|Y<br /><br />Du måste köra SP1 och installera [Windows Management Frame 4.0](https://www.microsoft.com/download/details.aspx?id=40855)|Y<br /><br />Du måste köra SP1 och installera [Windows Management Frame 4.0](https://www.microsoft.com/download/details.aspx?id=40855)|Volym, resurs, mapp, fil|
|Servrar (32-bitars och 64-bitars)|Windows Server 2008 R2 SP1 – Standard eller Enterprise|Windows-dator i VMWare (skyddar arbetsbelastningar som körs i Windows-dator i VMWare)<br /> <br /> Azure Stack|Y<br /><br />Du måste köra SP1 och installera [Windows Management Frame 4.0](https://www.microsoft.com/download/details.aspx?id=40855)|Y<br /><br />Du måste köra SP1 och installera [Windows Management Frame 4.0](https://www.microsoft.com/download/details.aspx?id=40855)|Volym, resurs, mapp, fil, systemtillstånd/utan operativsystem|
|Servrar (32-bitars och 64-bitars)|Windows Server 2008 SP2|Fysisk server<br /><br />En lokal Hyper-V-dator<br /> <br /> Azure Stack|N|N|Volym, resurs, mapp, fil, systemtillstånd/utan operativsystem|
|Servrar (32-bitars och 64-bitars)|Windows Server 2008 SP2|Windows-dator i VMWare (skyddar arbetsbelastningar som körs i Windows-dator i VMWare)<br /> <br /> Azure Stack|Y|Y|Volym, resurs, mapp, fil, systemtillstånd/utan operativsystem|
|Servrar (32-bitars och 64-bitars)|Windows Storage Server 2008|Fysisk server<br /><br />En lokal Hyper-V-dator<br /> <br /> Azure Stack|Y|Y|Volym, resurs, mapp, fil, systemtillstånd/utan operativsystem|
|SQL Server|SQL Server 2017|Fysisk server <br /><br /> En lokal Hyper-V-dator <br /> <br /> Virtuell Azure-dator <br /><br /> Windows-dator i VMWare (skyddar arbetsbelastningar som körs i Windows-dator i VMWare)<br /> <br /> Azure Stack|Y|N|Alla distributionsscenarier: databas|
|SQL Server|SQL Server 2016 SP2|Fysisk server <br /><br /> En lokal Hyper-V-dator <br /> <br /> Virtuell Azure-dator <br /><br /> Windows-dator i VMWare (skyddar arbetsbelastningar som körs i Windows-dator i VMWare)<br /> <br /> Azure Stack|Y|Y|Alla distributionsscenarier: databas|
|SQL Server|SQL Server 2016 SP1|Fysisk server <br /><br /> En lokal Hyper-V-dator <br /> <br /> Virtuell Azure-dator <br /><br /> Windows-dator i VMWare (skyddar arbetsbelastningar som körs i Windows-dator i VMWare)<br /> <br /> Azure Stack|Y|Y|Alla distributionsscenarier: databas|
|SQL Server|SQL Server 2016|Fysisk server <br /><br /> En lokal Hyper-V-dator <br /> <br /> Virtuell Azure-dator <br /><br /> Windows-dator i VMWare (skyddar arbetsbelastningar som körs i Windows-dator i VMWare)<br /> <br /> Azure Stack|Y|Y |Alla distributionsscenarier: databas|
|SQL Server|SQL Server 2014|Azure-dator (när arbetsbelastningen körs som Azure-dator)<br /> <br /> Azure Stack|Y|Y|Alla distributionsscenarier: databas|
|SQL Server|SQL Server 2014|Windows-dator i VMWare (skyddar arbetsbelastningar som körs i Windows-dator i VMWare)<br /> <br /> Azure Stack|Y|Y|Alla distributionsscenarier: databas|
|SQL Server|SQLServer 2012 med SP2|Fysisk server<br /><br />En lokal Hyper-V-dator<br /> <br /> Azure Stack|Y|Y|Alla distributionsscenarier: databas|
|SQL Server|SQLServer 2012 med SP2|Azure-dator (när arbetsbelastningen körs som Azure-dator)<br /> <br /> Azure Stack|Y|Y|Alla distributionsscenarier: databas|
|SQL Server|SQLServer 2012 med SP2|Windows-dator i VMWare (skyddar arbetsbelastningar som körs i Windows-dator i VMWare)<br /> <br /> Azure Stack|Y|Y|Alla distributionsscenarier: databas|
|SQL Server|SQLServer 2012, SQLServer 2012 med SP1|Fysisk server<br /><br />En lokal Hyper-V-dator<br /> <br /> Azure Stack|Y|Y|Alla distributionsscenarier: databas|
|SQL Server|SQLServer 2012, SQLServer 2012 med SP1|Azure-dator (när arbetsbelastningen körs som Azure-dator)<br /> <br /> Azure Stack|Y|Y|Alla distributionsscenarier: databas|
|SQL Server|SQLServer 2012, SQLServer 2012 med SP1|Windows-dator i VMWare (skyddar arbetsbelastningar som körs i Windows-dator i VMWare)<br /> <br /> Azure Stack|Y|Y|Alla distributionsscenarier: databas|
|SQL Server|SQL Server 2008 R2|Fysisk server<br /><br />En lokal Hyper-V-dator<br /> <br /> Azure Stack|Y|Y|Alla distributionsscenarier: databas|
|SQL Server|SQL Server 2008 R2|Azure-dator (när arbetsbelastningen körs som Azure-dator)<br /> <br /> Azure Stack|Y|Y|Alla distributionsscenarier: databas|
|SQL Server|SQL Server 2008 R2|Windows-dator i VMWare (skyddar arbetsbelastningar som körs i Windows-dator i VMWare)<br /> <br /> Azure Stack|Y|Y|Alla distributionsscenarier: databas|
|SQL Server|SQL Server 2008|Fysisk server<br /><br />En lokal Hyper-V-dator<br /> <br /> Azure Stack|Y|Y|Alla distributionsscenarier: databas|
|SQL Server|SQL Server 2008|Azure-dator (när arbetsbelastningen körs som Azure-dator)<br /> <br /> Azure Stack|Y|Y|Alla distributionsscenarier: databas|
|SQL Server|SQL Server 2008|Windows-dator i VMWare (skyddar arbetsbelastningar som körs i Windows-dator i VMWare)<br /> <br /> Azure Stack|Y|Y|Alla distributionsscenarier: databas|
|Exchange|Exchange 2016|Fysisk server<br/><br/> En lokal Hyper-V-dator<br /> <br /> Azure Stack|Y|Y|Skydda (alla distributionsscenarier): Fristående Exchange server, databas under en grupp för databastillgänglighet (DAG)<br /><br />Återställa (alla distributionsscenarier): Postlåda, postlådedatabaser under en DAG<br/><br/> Säkerhetskopiering av Exchange över ReFS inte stöds |
|Exchange|Exchange 2016|Windows-dator i VMWare (skyddar arbetsbelastningar som körs i Windows-dator i VMWare)<br /> <br /> Azure Stack|Y|Y|Skydda (alla distributionsscenarier): Fristående Exchange server, databas under en grupp för databastillgänglighet (DAG)<br /><br />Återställa (alla distributionsscenarier): Postlåda, postlådedatabaser under en DAG<br/><br/> Säkerhetskopiering av Exchange över ReFS inte stöds |
|Exchange|Exchange 2013|Fysisk server<br /><br />En lokal Hyper-V-dator<br /> <br /> Azure Stack|Y|Y|Skydda (alla distributionsscenarier): Fristående Exchange server, databas under en grupp för databastillgänglighet (DAG)<br /><br />Återställa (alla distributionsscenarier): Postlåda, postlådedatabaser under en DAG<br/><br/> Säkerhetskopiering av Exchange över ReFS inte stöds |
|Exchange|Exchange 2013|Windows-dator i VMWare (skyddar arbetsbelastningar som körs i Windows-dator i VMWare)<br /> <br /> Azure Stack|Y|Y|Skydda (alla distributionsscenarier): Fristående Exchange server, databas under en grupp för databastillgänglighet (DAG)<br /><br />Återställa (alla distributionsscenarier): Postlåda, postlådedatabaser under en DAG<br/><br/> Säkerhetskopiering av Exchange över ReFS inte stöds |
|Exchange|Exchange 2010|Fysisk server<br /><br />En lokal Hyper-V-dator<br /> <br /> Azure Stack|Y|Y|Skydda (alla distributionsscenarier): Fristående Exchange server, databas under en grupp för databastillgänglighet (DAG)<br /><br />Återställa (alla distributionsscenarier):  Postlåda, postlådedatabaser under en DAG<br/><br/> Säkerhetskopiering av Exchange över ReFS inte stöds |
|Exchange|Exchange 2010|Windows-dator i VMWare (skyddar arbetsbelastningar som körs i Windows-dator i VMWare)<br /> <br /> Azure Stack|Y|Y|Skydda (alla distributionsscenarier): Fristående Exchange server, databas under en grupp för databastillgänglighet (DAG)<br /><br />Återställa (alla distributionsscenarier):  Postlåda, postlådedatabaser under en DAG<br/><br/> Säkerhetskopiering av Exchange över ReFS inte stöds |
|SharePoint|SharePoint 2016|Fysisk server<br /><br />En lokal Hyper-V-dator<br /><br />Azure-dator (när arbetsbelastningen körs som Azure-dator)<br /><br />Windows-dator i VMWare (skyddar arbetsbelastningar som körs i Windows-dator i VMWare)<br /> <br /> Azure Stack|Y|Y|Skydda (alla distributionsscenarier):  Servergrupp, klientwebbserverinnehåll<br /><br />Återställa (alla distributionsscenarier):  Servergrupp, databas, webbprogram, fil- eller listobjekt, SharePoint-sökning, webbserver för klientdel<br /><br />Observera att skydda en SharePoint-servergrupp som använder SQL Server 2012 AlwaysOn-funktionen för innehållsdatabaser inte stöds.|
|SharePoint|SharePoint 2013|Fysisk server<br /><br />En lokal Hyper-V-dator<br /> <br /> Azure Stack|Y|Y|Skydda (alla distributionsscenarier):  Servergrupp, klientwebbserverinnehåll<br /><br />Återställa (alla distributionsscenarier):  Servergrupp, databas, webbprogram, fil- eller listobjekt, SharePoint-sökning, webbserver för klientdel<br /><br />Observera att skydda en SharePoint-servergrupp som använder SQL Server 2012 AlwaysOn-funktionen för innehållsdatabaser inte stöds.|
|SharePoint|SharePoint 2013|Azure-dator (när arbetsbelastningen körs som Azure-dator) – DPM 2012 R2 Samlad uppdatering 3 och senare<br /> <br /> Azure Stack|Y|Y|Skydda (alla distributionsscenarier):  Servergrupp, SharePoint-sökning, klientwebbserverinnehåll<br /><br />Återställa (alla distributionsscenarier):  Servergrupp, databas, webbprogram, fil- eller listobjekt, SharePoint-sökning, webbserver för klientdel<br /><br />Observera att skydda en SharePoint-servergrupp som använder SQL Server 2012 AlwaysOn-funktionen för innehållsdatabaser inte stöds.|
|SharePoint|SharePoint 2013|Windows-dator i VMWare (skyddar arbetsbelastningar som körs i Windows-dator i VMWare)<br /> <br /> Azure Stack|Y|Y|Skydda (alla distributionsscenarier):  Servergrupp, SharePoint-sökning, klientwebbserverinnehåll<br /><br />Återställa (alla distributionsscenarier):  Servergrupp, databas, webbprogram, fil- eller listobjekt, SharePoint-sökning, webbserver för klientdel<br /><br />Observera att skydda en SharePoint-servergrupp som använder SQL Server 2012 AlwaysOn-funktionen för innehållsdatabaser inte stöds.|
|SharePoint|SharePoint 2010|Fysisk server<br /><br />En lokal Hyper-V-dator<br /> <br /> Azure Stack|Y|Y|Skydda (alla distributionsscenarier): Servergrupp, SharePoint-sökning, klientwebbserverinnehåll<br /><br />Återställa (alla distributionsscenarier): Servergrupp, databas, webbprogram, fil- eller listobjekt, SharePoint-sökning, webbserver för klientdel|
|SharePoint|SharePoint 2010|Azure-dator (när arbetsbelastningen körs som Azure-dator)<br /> <br /> Azure Stack|Y|Y|Skydda (alla distributionsscenarier): Servergrupp, SharePoint-sökning, klientwebbserverinnehåll<br /><br />Återställa (alla distributionsscenarier): Servergrupp, databas, webbprogram, fil- eller listobjekt, SharePoint-sökning, webbserver för klientdel|
|SharePoint|SharePoint 2010|Windows-dator i VMWare (skyddar arbetsbelastningar som körs i Windows-dator i VMWare)<br /> <br /> Azure Stack|Y|Y|Skydda (alla distributionsscenarier): Servergrupp, SharePoint-sökning, klientwebbserverinnehåll<br /><br />Återställa (alla distributionsscenarier): Servergrupp, databas, webbprogram, fil- eller listobjekt, SharePoint-sökning, webbserver för klientdel|
|Hyper-V-värd - DPM-skyddsagenten på Hyper-V-värdserver, kluster eller VM|Windows Server 2016|Fysisk server<br /><br />En lokal Hyper-V-dator|Y|Y|Skydda: Hyper-V-datorer, klusterdelade volymer (CSV)<br /><br />Återställ: Virtuell dator, återställning av filer och mappen, volymer, virtuella hårddiskar|
|Hyper-V-värd - DPM-skyddsagenten på Hyper-V-värdserver, kluster eller VM|Windows Server 2012 R2 – Datacenter och Standard|Fysisk server<br /><br />En lokal Hyper-V-dator|Y|Y|Skydda: Hyper-V-datorer, klusterdelade volymer (CSV)<br /><br />Återställ: Virtuell dator, återställning av filer och mappen, volymer, virtuella hårddiskar|
|Hyper-V-värd - DPM-skyddsagenten på Hyper-V-värdserver, kluster eller VM|Windows Server 2012 – Datacenter och Standard|Fysisk server<br /><br />En lokal Hyper-V-dator|Y|Y|Skydda: Hyper-V-datorer, klusterdelade volymer (CSV)<br /><br />Återställ: Virtuell dator, återställning av filer och mappen, volymer, virtuella hårddiskar|
|Hyper-V-värd - DPM-skyddsagenten på Hyper-V-värdserver, kluster eller VM|Windows Server 2008 R2 SP1 – Enterprise och Standard|Fysisk server<br /><br />En lokal Hyper-V-dator|Y|Y|Skydda: Hyper-V-datorer, klusterdelade volymer (CSV)<br /><br />Återställ: Virtuell dator, återställning av filer och mappen, volymer, virtuella hårddiskar|
|Hyper-V-värd - DPM-skyddsagenten på Hyper-V-värdserver, kluster eller VM|Windows Server 2008 SP2|Fysisk server<br /><br />En lokal Hyper-V-dator|N|N|Skydda: Hyper-V-datorer, klusterdelade volymer (CSV)<br /><br />Återställ: Virtuell dator, återställning av filer och mappen, volymer, virtuella hårddiskar|
|VMwares virtuella datorer|VMware vCenter/vSphere ESX/ESXi  Licensed Version 5.5/6.0/6.5 |Fysisk server <br/>En lokal Hyper-V-dator <br/> Windows-dator i VMWare|Y|Y|Virtuella VMware-datorer på klusterdelade volymer (CSV), NFS, och SAN-lagring<br /> Återställning av filer och mappar är endast tillgänglig för Windows-datorer, VMware vApps stöds inte.|
|VMwares virtuella datorer|[VMware vSphere Licensed Version 6.7](backup-azure-backup-server-vmware.md#vmware-vsphere-67) |Fysisk server <br/>En lokal Hyper-V-dator <br/> Windows-dator i VMWare|Y|N|Virtuella VMware-datorer på klusterdelade volymer (CSV), NFS, och SAN-lagring<br /> Återställning av filer och mappar är endast tillgänglig för Windows-datorer, VMware vApps stöds inte.|
|Linux|Linux körs som Hyper-V eller VMware-gäst|Fysisk server <br/>En lokal Hyper-V-dator <br/> Windows-dator i VMWare|Y|Y|Hyper-V måste köras på Windows Server 2012 R2 eller Windows Server 2016. Skydda: Hela den virtuella datorn<br /><br />Återställ: Hela den virtuella datorn <br/><br/> En fullständig lista över Linux-distributioner som stöds och versioner finns i artikeln [Linux på distributioner som godkänts av Azure](../virtual-machines/linux/endorsed-distros.md).|

## <a name="azure-expressroute-support"></a>Support för Azure ExpressRoute

Om Azure ExpressRoute har konfigurerats med privata eller Microsoft-peering, kan inte den användas för att säkerhetskopiera data till Azure.

Om Azure ExpressRoute har konfigurerats med offentlig Peering, kan den användas för att säkerhetskopiera data till Azure.

>[!NOTE]
>Offentlig Peering är inaktuell för nya kretsar.

## <a name="cluster-support"></a>Stöd för kluster
Azure Backup Server kan skydda data i följande klustrade program:

-   Filservrar

-   SQL Server

-   Hyper-V - om du skyddar ett Hyper-V-kluster med uppskalad DPM-skydd kan du inte lägga till sekundärt skydd för de skyddade Hyper-V-arbetsbelastningarna.

    Om du kör Hyper-V på Windows Server 2008 R2, se till att installera uppdateringen som beskrivs i KB [975354](https://support.microsoft.com/en-us/kb/975354).
    Om du kör Hyper-V på Windows Server 2008 R2 i en klusterkonfiguration, måste du installera SP2 och KB [971394](https://support.microsoft.com/en-us/kb/971394).

-   Exchange Server – Azure Backup Server kan skydda icke delade diskkluster för Exchange Server-versioner stöds (kluster-kontinuerlig replikering) och kan även skydda Exchange Server som konfigurerats för kontinuerlig lokal replikering.

-   SQLServer – Azure Backup Server stöder inte säkerhetskopiering av SQL Server-databaser på klusterdelade volymer (CSV).

Azure Backup Server kan skydda arbetsbelastningar i ett kluster som finns i samma domän som DPM-servern och en underordnad eller betrodd domän. Om du vill skydda datakällor i ej betrodda domäner eller arbetsgrupper kan du använda NTLM eller certifikatautentisering för en enskild server eller certifikatautentisering endast för ett kluster.
