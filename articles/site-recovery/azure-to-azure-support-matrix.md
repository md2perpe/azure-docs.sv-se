---
title: Stöd matrix för haveriberedskap för virtuella Azure-datorer mellan Azure-regioner med Azure Site Recovery | Microsoft Docs
description: Sammanfattar krav och support för haveriberedskap för virtuella Azure-datorer från en region till en annan med Azure Site Recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 06/27/2019
ms.author: raynew
ms.openlocfilehash: 55275144746dbc1a3ead7c7c12a6901ab6f9269e
ms.sourcegitcommit: 6cb4dd784dd5a6c72edaff56cf6bcdcd8c579ee7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2019
ms.locfileid: "67514129"
---
# <a name="support-matrix-for-replicating-azure-vms-from-one-region-to-another"></a>Stödmatris för replikering av virtuella Azure-datorer från en region till en annan

Den här artikeln sammanfattas support och krav när du ställer in för haveriberedskap för virtuella Azure-datorer från en Azure-region till en annan, med den [Azure Site Recovery](site-recovery-overview.md) service.


## <a name="deployment-method-support"></a>Metoden distributionsstöd

**Distribution** |  **Support**
--- | ---
**Azure Portal** | Stöds.
**PowerShell** | Stöds. [Läs mer](azure-to-azure-powershell.md)
**REST-API** | Stöds.
**CLI** | Stöds för närvarande inte


## <a name="resource-support"></a>Resursstöd för

**Resursåtgärden** | **Detaljer**
--- | --- | ---
**Flytta valv mellan resursgrupper** | Stöds inte
**Flytta resurser för beräkning/lagringsnätverk mellan resursgrupper** | Stöds ej.<br/><br/> Om du flyttar en virtuell dator eller tillhörande komponenter, till exempel lagringsnätverk/när Virtuellt datorn replikeras, måste du inaktivera och återaktivera replikering för den virtuella datorn.
**Replikera virtuella Azure-datorer från en prenumeration till en annan för katastrofåterställning** | Stöd för inom samma Azure Active Directory-klientorganisation.
**Migrera virtuella datorer mellan regioner i stöds geografiska kluster (inom och mellan prenumerationer)** | Stöd för inom samma Azure Active Directory-klientorganisation.
**Migrera virtuella datorer i samma region** | Stöds ej.

## <a name="region-support"></a>Regionsstöd

Du kan replikera och återställa virtuella datorer mellan alla två regioner inom samma geografiska kluster. Geografisk kluster definieras att hålla datafördröjning och datasuveränitet i åtanke.


**Geografisk kluster** | **Azure-regioner**
-- | --
Sydamerika | Kanada, östra, Kanada, centrala, södra centrala USA, USA, västra centrala, USA, östra, USA, östra 2, västra USA, västra USA 2, centrala USA, norra centrala USA
Europa | Storbritannien, västra, Storbritannien, södra, Nordeuropa, Västeuropa, Frankrike, centrala och Frankrike, södra, Sydafrika, västra, Sydafrika, Norra
Asien | Södra Indien, centrala Indien, Sydostasien, östra Asien, östra, Japan, Japan västra, centrala Korea, södra
Australien   | Östra Australien, sydöstra Australien, Australien centrala; Australien centrala 2
Azure Government    | Virginia (USA-förvaltad region), Iowa (USA-förvaltad region), USA-förvaltad region Arizona, US GOV Texas, US DOD, östra, US DOD, centrala 
Tyskland | Tyskland, centrala, Tyskland, nordöstra
Kina | Kina, östra, Kina, norra, North2 för Kina, östra 2 Kina

>[!NOTE]
>
> - För **södra Brasilien**, du kan replikera och redundansväxla till dessa regioner: Södra centrala USA, västra centrala USA, östra USA, östra USA 2, västra USA, västra USA 2 och norra centrala USA.
> - Södra Brasilien kan bara användas som en källa-region där virtuella datorer kan replikera med Site Recovery. Den fungera inte som en målregion. Det här är på grund av problem med nätverkssvarstiden på grund av geografiska avstånd.
> - Du kan arbeta i regioner som du har lämplig åtkomst.
> - Om den region där du vill skapa ett valv inte redan visas, kontrollera att din prenumeration har behörighet att skapa resurser i den regionen.
> - Om du inte ser en region inom ett geografiskt kluster när du aktiverar replikering, kontrollera att din prenumeration har behörighet att skapa virtuella datorer i den regionen.



## <a name="cache-storage"></a>Cachelagring

Den här tabellen sammanfattar stödet för cachelagringskonto som används av Site Recovery under replikeringen.

**Inställning** | **Support** | **Detaljer**
--- | --- | ---
Generell användning V2-lagringskonton (frekvent och lågfrekvent nivå) | Stöds | Användning av GPv2 rekommenderas inte eftersom transaktionskostnader för V2 är betydligt högre än V1-lagringskonton.
Azure Storage-brandväggar för virtuella nätverk  | Stöds | Om du använder brandvägg är aktiverad cachelagringskontot eller mållagringskontot, se till att du [”Tillåt att betrodda Microsoft-tjänster”](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions).


## <a name="replicated-machine-operating-systems"></a>Replikerade datorn operativsystem

Site Recovery har stöd för replikering av virtuella Azure-datorer som kör operativsystemen som anges i det här avsnittet.

### <a name="windows"></a>Windows

**Operativsystem** | **Detaljer**
--- | ---
Windows Server 2019 | Server Core, Server med Skrivbordsmiljö
Windows Server 2016  | Server Core, Server med Skrivbordsmiljö
Windows Server 2012 R2 |
Windows Server 2012 |
Windows Server 2008 R2 | Med SP1 eller senare
Windows 10 (x64) |
Windows 8.1 (x64) |
Windows 8 (x64) |
Windows 7 (x64) | Med SP1 eller senare (Windows 7 RTM stöds inte)

#### <a name="linux"></a>Linux

**Operativsystem** | **Detaljer**
--- | ---
Red Hat Enterprise Linux | 6.7, 6.8, 6.9, 6.10, 7.0, 7.1, 7.2, 7.3, 7.4, 7.5, 7.6  
CentOS | 6.5, 6.6, 6.7, 6.8, 6.9, 6.10, 7.0, 7.1, 7.2, 7.3, 7.4, 7.5, 7.6
Ubuntu 14.04 LTS Server | [Stöds kernel-versioner](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)
Ubuntu 16.04 LTS Server | [Stöds kernel-version](#supported-ubuntu-kernel-versions-for-azure-virtual-machines)<br/><br/> Ubuntu-servrar som använder lösenordsbaserad autentisering och logga in och cloud-init-paketet för att konfigurera virtuella datorer, kan ha lösenordsbaserad inloggning i inaktiverats på redundans (beroende på cloudinit-konfiguration). Lösenordsbaserad inloggning kan återaktiveras på den virtuella datorn genom att återställa lösenordet från stödet > Felsökning > menyn Inställningar (på den redundansväxlade virtuella datorn i Azure-portalen.
Debian 7 | [Stöds kernel-versioner](#supported-debian-kernel-versions-for-azure-virtual-machines)
Debian 8 | [Stöds kernel-versioner](#supported-debian-kernel-versions-for-azure-virtual-machines)
SUSE Linux Enterprise Server 12 | SP1, SP2, SP3, SP4. [(Stöds kernel-versioner)](#supported-suse-linux-enterprise-server-12-kernel-versions-for-azure-virtual-machines)
SUSE Linux Enterprise Server 11 | SP3<br/><br/> Uppgradering av datorer som replikeras från SP3 till SP4 stöds inte. Om en replikerad dator har uppgraderats, måste du inaktivera replikering och återaktivera replikering efter uppgraderingen.
SUSE Linux Enterprise Server 11 | SP4
Oracle Linux | 6.4, 6.5, 6.6, 6.7, 6.8, 6.9, 6.10, 7.0, 7.1, 7.2, 7.3, 7.4, 7.5 <br/><br/> Köra antingen kompatibel Red Hat-kernel eller Unbreakable Enterprise Kernel version 3 (UEK3).


#### <a name="supported-ubuntu-kernel-versions-for-azure-virtual-machines"></a>Ubuntu kernel-versioner som stöds för Azure-datorer

**Versionen** | **Mobilitetstjänstversionen** | **Kernelversion** |
--- | --- | --- |
14.04 LTS | 9.25 | 3.13.0-24-Generic till 3.13.0-169-generic,<br/>3.16.0-25-Generic till 3.16.0-77-generic,<br/>3.19.0-18-Generic till 3.19.0-80-generic,<br/>4.2.0-18-Generic till 4.2.0-42-generic,<br/>4.4.0-21-Generic till 4.4.0-146-generic,<br/>4.15.0-1023-Azure till 4.15.0-1042-azure |
14.04 LTS | 9.24 | 3.13.0-24-Generic till 3.13.0-167-generic,<br/>3.16.0-25-Generic till 3.16.0-77-generic,<br/>3.19.0-18-Generic till 3.19.0-80-generic,<br/>4.2.0-18-Generic till 4.2.0-42-generic,<br/>4.4.0-21-Generic till 4.4.0-143-generic,<br/>4.15.0-1023-Azure till 4.15.0-1040-azure |
14.04 LTS | 9.23 | 3.13.0-24-Generic till 3.13.0-165-generic,<br/>3.16.0-25-Generic till 3.16.0-77-generic,<br/>3.19.0-18-Generic till 3.19.0-80-generic,<br/>4.2.0-18-Generic till 4.2.0-42-generic,<br/>4.4.0-21-Generic till 4.4.0-142-generic,<br/>4.15.0-1023-Azure till 4.15.0-1037-azure |
14.04 LTS | 9.22 | 3.13.0-24-Generic till 3.13.0-164-generic,<br/>3.16.0-25-Generic till 3.16.0-77-generic,<br/>3.19.0-18-Generic till 3.19.0-80-generic,<br/>4.2.0-18-Generic till 4.2.0-42-generic,<br/>4.4.0-21-Generic till 4.4.0-140-generic,<br/>4.15.0-1023-Azure till 4.15.0-1036-azure |
|||
16.04 LTS | 9.25 | 4.4.0-21-Generic till 4.4.0-146-generic,<br/>4.8.0-34-Generic till 4.8.0-58-generic,<br/>4.10.0-14-Generic till 4.10.0-42-generic,<br/>4.11.0-13-Generic till 4.11.0-14-generic,<br/>4.13.0-16-Generic till 4.13.0-45-generic,<br/>4.15.0-13-Generic till 4.15.0-48-generic<br/>4.11.0-1009-Azure till 4.11.0-1016-azure,<br/>4.13.0-1005-Azure till 4.13.0-1018-azure <br/>4.15.0-1012-Azure till 4.15.0-1042-azure|
16.04 LTS | 9.24 | 4.4.0-21-Generic till 4.4.0-143-generic,<br/>4.8.0-34-Generic till 4.8.0-58-generic,<br/>4.10.0-14-Generic till 4.10.0-42-generic,<br/>4.11.0-13-Generic till 4.11.0-14-generic,<br/>4.13.0-16-Generic till 4.13.0-45-generic,<br/>4.15.0-13-Generic till 4.15.0-46-generic<br/>4.11.0-1009-Azure till 4.11.0-1016-azure,<br/>4.13.0-1005-Azure till 4.13.0-1018-azure <br/>4.15.0-1012-Azure till 4.15.0-1040-azure|
16.04 LTS | 9.23 | 4.4.0-21-Generic till 4.4.0-142-generic,<br/>4.8.0-34-Generic till 4.8.0-58-generic,<br/>4.10.0-14-Generic till 4.10.0-42-generic,<br/>4.11.0-13-Generic till 4.11.0-14-generic,<br/>4.13.0-16-Generic till 4.13.0-45-generic,<br/>4.15.0-13-Generic till 4.15.0-45-generic<br/>4.11.0-1009-Azure till 4.11.0-1016-azure,<br/>4.13.0-1005-Azure till 4.13.0-1018-azure <br/>4.15.0-1012-Azure till 4.15.0-1037-azure|
16.04 LTS | 9.22 | 4.4.0-21-Generic till 4.4.0-140-generic,<br/>4.8.0-34-Generic till 4.8.0-58-generic,<br/>4.10.0-14-Generic till 4.10.0-42-generic,<br/>4.11.0-13-Generic till 4.11.0-14-generic,<br/>4.13.0-16-Generic till 4.13.0-45-generic,<br/>4.15.0-13-Generic till 4.15.0-43-generic<br/>4.11.0-1009-Azure till 4.11.0-1016-azure,<br/>4.13.0-1005-Azure till 4.13.0-1018-azure <br/>4.15.0-1012-Azure till 4.15.0-1036-azure|

#### <a name="supported-debian-kernel-versions-for-azure-virtual-machines"></a>Debian kernel-versioner som stöds för virtuella Azure-datorer

**Versionen** | **Mobilitetstjänstversionen** | **Kernelversion** |
--- | --- | --- |
Debian 7 | 9.22,9.23,9.24,9.25 | 3.2.0-4-amd64 till 3.2.0-6-amd64, 3.16.0-0.bpo.4-amd64 |
|||
Debian 8 | 9.25 | 3.16.0-4-amd64 till 3.16.0-8-amd64 4.9.0-0.bpo.4-amd64 till 4.9.0-0.bpo.8-amd64 |
Debian 8 | 9.22, 9.23, 9.24 | 3.16.0-4-amd64 till 3.16.0-7-amd64 4.9.0-0.bpo.4-amd64 till 4.9.0-0.bpo.8-amd64 |

#### <a name="supported-suse-linux-enterprise-server-12-kernel-versions-for-azure-virtual-machines"></a>SUSE Linux Enterprise Server 12 kernel-versioner som stöds för Azure-datorer

**Versionen** | **Mobilitetstjänstversionen** | **Kernelversion** |
--- | --- | --- |
SUSE Linux Enterprise Server 12 (SP1,SP2,SP3,SP4) | 9.25 | SP1 3.12.49-11-default till 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default till 3.12.74-60.64.107-default</br></br> SP2 4.4.21-69-default till 4.4.120-92.70-default</br></br>SP2(LTSS) 4.4.121-92.73-default till 4.4.121-92.104-default</br></br>SP3 4.4.73-5-default till 4.4.176-94.88-default</br></br>SP3 4.4.138-4.7-azure till 4.4.176-4.25-azure</br></br>SP4 4.12.14-94.41-default till 4.12.14-95.13-default</br>SP4 4.12.14-6.3-azure till 4.12.14-6.9-azure |
SUSE Linux Enterprise Server 12 (SP1,SP2,SP3,SP4) | 9.24 | SP1 3.12.49-11-default till 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default till 3.12.74-60.64.107-default</br></br> SP2 4.4.21-69-default till 4.4.120-92.70-default</br></br>SP2(LTSS) 4.4.121-92.73-default till 4.4.121-92.104-default</br></br>SP3 4.4.73-5-default till 4.4.176-94.88-default</br></br>SP4 4.12.14-94.41-default till 4.12.14-95.13-default |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4) | 9.23 | SP1 3.12.49-11-default till 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default till 3.12.74-60.64.107-default</br></br> SP2 4.4.21-69-default till 4.4.120-92.70-default</br></br>SP2(LTSS) 4.4.121-92.73-default till 4.4.121-92.101-default</br></br>SP3 4.4.73-5-default till 4.4.162-94.69-default</br></br>SP4 4.12.14-94.41-default till 4.12.14-95.6-default |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3) | 9.22 | SP1 3.12.49-11-default till 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default till 3.12.74-60.64.107-default</br></br> SP2 4.4.21-69-default till 4.4.120-92.70-default</br></br>SP2(LTSS) 4.4.121-92.73-default till 4.4.121-92.98-default</br></br>SP3 4.4.73-5-default till 4.4.162-94.72-default |

## <a name="replicated-machines---linux-file-systemguest-storage"></a>Replikerade datorer - Linux system/gäst fillagring

* Filsystem: ext3, ext4, ReiserFS (endast Suse Linux Enterprise Server), XFS, BTRFS
* Volymhanterare: LVM2
* Multipath programvara: Enheten Mapper


## <a name="replicated-machines---compute-settings"></a>Replikerade datorer - inställningarna för beräkning

**Inställning** | **Support** | **Detaljer**
--- | --- | ---
Size | Alla Azure VM-storlekar med minst 2 CPU-kärnor och 1 GB RAM-minne | Kontrollera [Azure VM-storlekar](../virtual-machines/windows/sizes.md).
Tillgänglighetsuppsättningar | Stöds | Om du aktiverar replikering för en virtuell Azure-dator med standardalternativen skapas en tillgänglighetsuppsättning automatiskt, baserat på de nationella inställningarna för källan. Du kan ändra dessa inställningar.
Tillgänglighetszoner | Stöds |
Hybrid Use-förmånen (HUB) | Stöds | Om den Virtuella källdatorn har en HUB licens aktiverad, ett redundanstest eller redundans använder virtuell dator också HUB-licens.
Skalningsuppsättning för virtuella datorer | Stöds inte |
Azure galleriavbildningar - Microsoft publicerat | Stöds | Om den virtuella datorn kör ett operativsystem som stöds.
Azure-galleriet-avbildningar – från tredje part publicerats | Stöds | Om den virtuella datorn kör ett operativsystem som stöds.
Anpassade avbildningar - från tredje part publicerats | Stöds | Om den virtuella datorn kör ett operativsystem som stöds.
Virtuella datorer migreras med hjälp av Site Recovery | Stöds | Om en VMware-VM eller fysiska datorn har migrerats till Azure med Site Recovery, måste du avinstallera den äldre versionen av mobilitetstjänsten som körs på datorn och starta om datorn innan du replikerar till en annan Azure-region.
RBAC-principer | Stöds inte | Rollbaserad åtkomst till principer för åtkomstkontroll (RBAC) på virtuella datorer inte har replikerats till den virtuella datorn vid redundansväxlingen i målregionen.
Tillägg | Stöds inte | Tillägg replikeras inte till den virtuella datorn vid redundansväxlingen i målregionen. Den måste installeras manuellt efter en redundansväxling.

## <a name="replicated-machines---disk-actions"></a>Replikerade datorer - disk åtgärder

**Åtgärd** | **Detaljer**
-- | ---
Ändra storlek på disk på den replikerade virtuella datorn | Stöds
Lägg till en disk till en replikerad virtuell dator | Stöds

## <a name="replicated-machines---storage"></a>Replikerade datorer - lagring

Den här tabellen sammanfattas stöd för Azure VM OS-disk, datadisk och tillfällig disk.

- Det är viktigt att Observera diskgränser för virtuella datorer och mål för [Linux](../virtual-machines/linux/disk-scalability-targets.md) och [Windows](../virtual-machines/windows/disk-scalability-targets.md) virtuella datorer för att undvika eventuella prestandaproblem.
- Om du distribuerar med standardinställningarna, skapar Site Recovery automatiskt diskar och lagringskonton baserat på inställningar för datakälla.
- Om du har ändrat kan du kontrollera att du följer riktlinjerna.

**Komponent** | **Support** | **Detaljer**
--- | --- | ---
Maximal storlek för OS-disk | 2 048 GB | [Läs mer](../virtual-machines/windows/managed-disks-overview.md) om VM-diskar.
Temporär disk | Stöds inte | Den temporära disken utelämnas alltid från replikering.<br/><br/> Lagra inte beständiga data på den temporära disken. [Läs mer](../virtual-machines/windows/managed-disks-overview.md).
Maximal Datastorlek för disk | 4 095 GB |
Minsta datadiskstorleken | Ingen begränsning för ohanterade diskar. 2 GB för hanterade diskar | 
Maxantalet för data-disk | Upp till 64 i enlighet med stöd för en viss Azure VM-storlek | [Läs mer](../virtual-machines/windows/sizes.md) om VM-storlekar.
Förändringstakten för data-disk | Högst 10 Mbit/s per disk för premiumlagring. Högst 2 Mbit/s per disk för standardlagring. | Om de genomsnittliga dataändringshastighet på disken är kontinuerligt högre än tillåtna, replikering ifatt inte.<br/><br/>  Om längsta har överskridits sporadiskt replikering kan komma ifatt, men du kan se något fördröjda återställningspunkter.
Datadisk - standardlagringskonto | Stöds |
Datadisk - premium storage-konto | Stöds | Om en virtuell dator har diskar som är fördelade på premium- och standardlagringskonton, kan du välja ett annat mållagringskonto för varje disk, så du får samma lagringskonfiguration för i målregionen.
Hanterad disk - standard | Stöd i Azure-regioner där Azure Site Recovery stöds. |
Hanterad disk - premium | Stöd i Azure-regioner där Azure Site Recovery stöds. |
Standard SSD | Stöds |
Redundans | LRS och GRS stöds.<br/><br/> ZRS stöds inte.
Frekventa och lågfrekventa lagring | Stöds inte | VM-diskar stöds inte på frekventa och lågfrekventa lagring
Lagringsutrymmen | Stöds |
Kryptering i vila (SSE) | Stöds | SSE är standardinställningen på storage-konton.   
Azure Disk Encryption (ADE) för Windows OS | Virtuella datorer som har aktiverats för [kryptering med Azure AD-app](https://aka.ms/ade-aad-app) stöds |
Azure Disk Encryption (ADE) för Linux OS | Stöds inte |
Lägg till frekvent | Stöds | Aktiverar replikering för en datadisk som du lägger till en replikerad virtuell dator i Azure har stöd för virtuella datorer som använder hanterade diskar.
Frekvent ta bort disken | Stöds inte | Om du tar bort datadisk på den virtuella datorn, måste du inaktivera replikering och aktiverar replikering igen för den virtuella datorn.
Uteslut disk | Stöd för. Du måste använda [Powershell](azure-to-azure-exclude-disks.md) att konfigurera. |  Temporära diskar undantas som standard.
Storage Spaces Direct  | Stöd för krascher konsekventa återställningspunkter. Konsekvent programåterställningspunkter stöds inte. |
Skalbar filserver  | Stöd för krascher konsekventa återställningspunkter. Konsekvent programåterställningspunkter stöds inte. |
LRS | Stöds |
GRS | Stöds |
RA-GRS | Stöds |
ZRS | Stöds inte |
Frekventa och lågfrekventa lagring | Stöds inte | Virtuella diskar stöds inte på frekventa och lågfrekventa lagring
Azure Storage-brandväggar för virtuella nätverk  | Stöds | Om begränsa åtkomst till virtuellt nätverk till storage-konton måste du aktivera [Tillåt att betrodda Microsoft-tjänster](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions).
Storage-konton för generell användning V2 (både frekvent och lågfrekvent nivå) | Ja | Transaktionen kostnaderna ökar avsevärt jämfört med generell användning V1-lagringskonton

>[!IMPORTANT]
> För att undvika prestandaproblem kan du kontrollera att du följer VM disk skalbarhets- och prestandamål för [Linux](../virtual-machines/linux/disk-scalability-targets.md) eller [Windows](../virtual-machines/windows/disk-scalability-targets.md) virtuella datorer. Om du använder standardinställningarna, skapar Site Recovery begärda diskar och lagringskonton, baserat på käll-konfigurationen. Om du anpassar och välja egna inställningar, följ disk mål för skalbarhet och prestanda för dina virtuella källdatorer.

## <a name="limits-and-data-change-rates"></a>Ändra priserna gränser och data

I följande tabell sammanfattas gränserna för Site Recovery.

- Dessa gränser är baserade på våra tester, men omfattar naturligtvis inte alla möjliga i/o kombinationer av program.
- De faktiska resultaten kan variera beroende på appen i/o blandningen.
- Det finns två begränsningar att överväga per disk data churn och per virtuell datordata omsättning.
- Till exempel om vi använder en Premium P20-disk som beskrivs i tabellen nedan, kan Site Recovery hantera 5 MB om dataomsättningen per disk, med på max för fem dessa diskar per virtuell dator, på grund av att gränsen på 25 MB/s totala dataomsättningen per virtuell dator.

**Lagringsmålet** | **Genomsnittlig källdisken i/o** |**Average Source Disk Data Churn** (Genomsnittlig dataomsättning för källdisk) | **Total Source Disk Data Churn Per Day** (Total dataomsättning per dag för källdisk)
---|---|---|---
Standard Storage | 8 kB | 2 MB/s | 168 GB per disk
Premium P10- eller P15-disk | 8 kB  | 2 MB/s | 168 GB per disk
Premium P10- eller P15-disk | 16 kB | 4 MB/s |  336 GB per disk
Premium P10- eller P15-disk | 32 kB eller mer | 8 MB/s | 672 GB per disk
Premium P20-, P30-, P40- eller P50-disk | 8 kB    | 5 MB/s | 421 GB per disk
Premium P20-, P30-, P40- eller P50-disk | minst 16 kB |20 MB/s | 1684 GB per disk

## <a name="replicated-machines---networking"></a>Replikerade datorer - nätverk
**Inställning** | **Support** | **Detaljer**
--- | --- | ---
NIC | Högsta antal som stöds för en viss Azure VM-storlek | Nätverkskort skapas när den virtuella datorn skapas under en redundansväxling.<br/><br/> Antal nätverkskort på den virtuella datorn som redundansväxlingen beror på antalet nätverkskort på den Virtuella källdatorn när replikering har aktiverats. Om du lägger till eller ta bort ett nätverkskort efter att ha aktiverat replikering, kan den inte påverkar antalet nätverkskort på den replikerade virtuella datorn efter redundans. Observera att ordningen för nätverkskort efter en redundansväxling inte är garanterat också vara samma som den ursprungliga ordningen.
Internet-lastbalanserare | Stöds | Associera förkonfigurerade belastningsutjämnaren med hjälp av en Azure Automation-skript i en återställningsplan.
Den interna belastningsutjämnaren | Stöds | Associera förkonfigurerade belastningsutjämnaren med hjälp av en Azure Automation-skript i en återställningsplan.
Offentlig IP-adress | Stöds | Koppla en befintlig offentlig IP-adress till nätverkskortet. Eller skapa en offentlig IP-adress och associera den med nätverkskortet med hjälp av en Azure Automation-skript i en återställningsplan.
NSG on NIC | Stöds | Associera NSG med nätverkskortet med hjälp av en Azure Automation-skript i en återställningsplan.
NSG på undernätet | Stöds | Koppla NSG: N med undernätet med hjälp av en Azure Automation-skript i en återställningsplan.
Reserverad (statiska) IP-adress | Stöds | Om nätverkskortet på den Virtuella källdatorn har en statisk IP-adress och målundernätet har den samma IP-adressen som är tillgängliga, den är tilldelad till den redundansväxlade virtuella datorn.<br/><br/> Om målundernätet inte har den samma IP-adressen som är tillgängliga, är en av de tillgängliga IP-adresserna i undernätet reserverad för den virtuella datorn.<br/><br/> Du kan också ange en fast IP-adress och nätmask i **replikerade objekt** > **inställningar** > **beräkning och nätverk**  >  **Nätverksgränssnitt**.
Dynamisk IP-adress | Stöds | Om nätverkskortet på källan har dynamiska IP-adresser, är nätverkskortet på den redundansväxlade virtuella datorn också dynamiskt som standard.<br/><br/> Du kan ändra detta till en fast IP-adress om det behövs.
Flera IP-adresser | Stöds inte | När du redundansväxlar en virtuell dator som har ett nätverkskort med flera IP-adresser, sparas endast primär IP-adressen för nätverkskortet i källregionen. Om du vill tilldela flera IP-adresser, kan du lägga till virtuella datorer till en [återställningsplanen](recovery-plan-overview.md) och bifoga ett skript för att tilldela ytterligare IP-adresser till planen, eller du kan göra ändringen manuellt eller med ett skript efter en redundansväxling. 
Traffic Manager     | Stöds | Du kan förkonfigurera Traffic Manager så att trafiken dirigeras till slutpunkten i källregionen regelbundet och till slutpunkten i målregionen vid redundans.
Azure DNS | Stöds |
Anpassad DNS  | Stöds |
Via oautentiserad proxyserver | Stöds | [Läs mer](site-recovery-azure-to-azure-networking-guidance.md)    
Autentiserad Proxy | Stöds inte | Om den virtuella datorn använder en autentiserad proxyserver för utgående anslutningar, kan inte replikeras med Azure Site Recovery.    
VPN plats-till-plats-anslutning till lokalt<br/><br/>(med eller utan ExpressRoute)| Stöds | Se till att Udr och NSG: er konfigureras så att Site Recovery-trafiken inte dirigeras till den lokala. [Läs mer](site-recovery-azure-to-azure-networking-guidance.md)    
Anslutning mellan virtuella nätverk | Stöds | [Läs mer](site-recovery-azure-to-azure-networking-guidance.md)  
Tjänstslutpunkter för virtuellt nätverk | Stöds | Om du är att begränsa åtkomst till virtuellt nätverk till storage-konton, kontrollerar du att betrodda Microsoft-tjänster har åtkomst till lagringskontot.
Snabbare nätverk | Stöds | Accelererat nätverk måste vara aktiverat på den Virtuella källdatorn. [Läs mer](azure-vm-disaster-recovery-with-accelerated-networking.md).



## <a name="next-steps"></a>Nästa steg
- Läs [nätverk vägledning](site-recovery-azure-to-azure-networking-guidance.md) för replikering av virtuella Azure-datorer.
- Distribuera återställning av [replikering av virtuella Azure-datorer](site-recovery-azure-to-azure.md).
