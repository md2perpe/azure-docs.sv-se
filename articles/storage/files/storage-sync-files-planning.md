---
title: Planera för distribution av Azure File Sync | Microsoft Docs
description: Lär dig att tänka på när du planerar för distribution av Azure Files.
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 2/7/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: a745fefa5ceb0f81cf8d66e7af9e308c0ecb40b9
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67449861"
---
# <a name="planning-for-an-azure-file-sync-deployment"></a>Planera för distribution av Azure File Sync
Använd Azure File Sync för att centralisera din organisations filresurser i Azure Files, samtidigt som den flexibilitet, prestanda och kompatibilitet för en lokal filserver. Azure File Sync omvandlar Windows Server till ett snabbt cacheminne för din Azure-filresurs. Du kan använda alla protokoll som är tillgänglig på Windows Server för att komma åt dina data lokalt, inklusive SMB, NFS och FTPS. Du kan ha så många cacheminnen som du behöver över hela världen.

Den här artikeln beskrivs viktiga överväganden för distribution av Azure File Sync. Vi rekommenderar att du också läser [planera för distribution av Azure Files](storage-files-planning.md). 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="azure-file-sync-terminology"></a>Azure File Sync-terminologi
Det är viktigt att du förstår termer som innan du hämtar in information om att planera för distribution av Azure File Sync.

### <a name="storage-sync-service"></a>Storage Sync-tjänsten
Storage Sync-tjänsten är översta Azure-resursen för Azure File Sync. Tjänst för Lagringssynkronisering resursen är en peer för resursen för lagringskonton och på samma sätt kan bara distribueras till Azure-resursgrupper. En resurs med distinkta på toppnivå från resursen för lagringskonton är nödvändigt eftersom Storage Sync-tjänsten kan skapa synkroniseringsrelationer med flera storage-konton via flera synkroniseringsgrupper. En prenumeration kan ha flera resurser som Storage Sync-tjänsten har distribuerats.

### <a name="sync-group"></a>Synkroniseringsgrupp
En synkroniseringsgrupp definierar synkroniseringstopologin för en uppsättning filer. Slutpunkter i en synkroniseringsgrupp synkroniseras med varandra. Om du har till exempel två distinkta grupper av filer som du vill hantera med Azure File Sync kan du skapa två synkroniseringsgrupper och lägga till olika slutpunkter för varje synkroniseringsgrupp. En tjänst för Lagringssynkronisering kan vara värd för så många synkroniseringsgrupper.  

### <a name="registered-server"></a>Registrerad server
Registrerad server-objektet representerar en förtroenderelation mellan din server (eller kluster) och Storage Sync-tjänsten. Du kan registrera så många servrar till en tjänst för Lagringssynkronisering-instans. Men kan en server (eller kluster) registreras med endast en Storage Sync-tjänsten i taget.

### <a name="azure-file-sync-agent"></a>Azure File Sync-agenten
Azure File Sync-agenten är ett nedladdningsbart paket som möjliggör att Windows Server kan synkroniseras med en Azure-filresurs. Azure File Sync-agenten har tre huvudkomponenter: 
- **FileSyncSvc.exe**: Bakgrunden Windows-tjänst som ansvarar för att övervaka ändringar på server-slutpunkter och för att initiera synkroniseringssessioner till Azure.
- **StorageSync.sys**: Azure File Sync filsystemsfilter, som ansvarar för lagringsnivåer filer till Azure Files (när molnet lagringsnivåer är aktiverad).
- **PowerShell-cmdletar för hantering av**: PowerShell-cmdletar som används för att interagera med Microsoft.StorageSync Azure-resursprovidern. Du hittar dem på följande platser (standard):
    - C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.PowerShell.Cmdlets.dll
    - C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll

### <a name="server-endpoint"></a>Server-slutpunkt
En serverslutpunkt representerar en viss plats på en registrerad server, till exempel en mapp på en servervolym. Flera serverslutpunkter kan finnas på samma volym om deras namnområden inte överlappar (till exempel `F:\sync1` och `F:\sync2`). Du kan konfigurera molnet lagringsnivåer principer separat för varje serverslutpunkt. 

Du kan skapa en serverslutpunkt via en monteringspunkt. Observera att monteringspunkter inom Serverslutpunkten hoppas över.  

Du kan skapa en serverslutpunkt på systemvolymen, men det finns två begränsningar om du gör det:
* Molnet lagringsnivåer kan inte aktiveras.
* Snabb namnområde återställning (där systemet snabbt stänger ned hela namnområdet och börjar sedan att återkalla innehåll) utförs inte.


> [!Note]  
> Endast icke flyttbara volymer stöds.  Enheter som är mappad från en fjärresurs stöds inte för slutpunktsökväg till en server.  Dessutom kan en serverslutpunkt kan finnas i Windows i molnet men systemvolymen lagringsnivåer stöds inte på systemvolymen.

Om du lägger till en plats på servern som har en befintlig uppsättning filer som en serverslutpunkt till en synkroniseringsgrupp slås filerna samman med andra filer som redan finns på andra slutpunkter i synkroniseringsgruppen.

### <a name="cloud-endpoint"></a>Molnslutpunkt
En slutpunkt i molnet är en Azure-filresurs som ingår i en synkroniseringsgrupp. Hela Azure filen resurs synkroniseringar och en Azure-filresurs kan vara medlem i endast en molnslutpunkt. En Azure-filresurs kan därför vara medlem i endast en synkroniseringsgrupp. Om du lägger till en Azure-filresurs som har en befintlig uppsättning filer som en slutpunkt i molnet till en synkroniseringsgrupp slås de befintliga filerna samman med andra filer som redan finns på andra slutpunkter i synkroniseringsgruppen.

> [!Important]  
> Azure File Sync stöder gör ändringar i Azure-filresursen direkt. Ändringar som görs på Azure-filresursen måste dock först identifieras av ett jobb med Azure File Sync ändra identifiering. Ett jobb för identifiering av ändring initieras för en molnslutpunkt bara en gång var 24: e timme. Dessutom kommer inte att uppdatera SMB tid för senaste ändring ändringar som gjorts i en Azure-filresurs via REST-protokollet och kan inte ses som en ändring av synkronisering. Mer information finns i [Azure Files vanliga frågor och svar](storage-files-faq.md#afs-change-detection).

### <a name="cloud-tiering"></a>Lagringsnivåer för moln 
Molnet lagringsnivåer är en valfri funktion i Azure File Sync som ofta öppnade filer cachelagras lokalt på servern medan alla andra filer nivåindelas till Azure Files utifrån principinställningar. Mer information finns i [förståelse av Molnlagringsnivå](storage-sync-cloud-tiering.md).

## <a name="azure-file-sync-system-requirements-and-interoperability"></a>Azure File Sync-systemkrav och samverkan 
Det här avsnittet beskrivs systemkraven för Azure File Sync-agenten och samverkan med Windows Server-funktioner och roller och lösningar från tredje part.

### <a name="evaluation-tool"></a>Utvärderingsverktyg för
Innan du distribuerar Azure File Sync, bör du utvärdera om den är kompatibel med ditt system med hjälp av verktyget Azure File Sync-utvärdering. Det här verktyget är en Azure PowerShell-cmdlet som söker efter potentiella problem med ditt filsystem och datauppsättningen, till exempel tecken som inte stöds eller en OS-version som inte stöds. Observera att dess kontroller omfatta de flesta, men inte alla funktioner nedan; Vi rekommenderar att du läser igenom resten av det här avsnittet noga för att se till att distributionen går smidigt. 

#### <a name="download-instructions"></a>Hämta anvisningar
1. Se till att du har den senaste versionen av PackageManagement och PowerShellGet installerat (Detta kan du installera förhandsversionsmoduler)
    
    ```powershell
        Install-Module -Name PackageManagement -Repository PSGallery -Force
        Install-Module -Name PowerShellGet -Repository PSGallery -Force
    ```
 
2. Starta om PowerShell
3. Installera modulerna
    
    ```powershell
        Install-Module -Name Az.StorageSync -AllowPrerelease -AllowClobber -Force
    ```

#### <a name="usage"></a>Användning  
Du kan anropa verktyget utvärdering på ett par olika sätt: du kan utföra Systemkontroller, datauppsättning kontroller eller båda. Om du vill utföra både system- och datauppsättningen kontrollerar du: 

```powershell
    Invoke-AzStorageSyncCompatibilityCheck -Path <path>
```

Så här testar endast din datauppsättning:
```powershell
    Invoke-AzStorageSyncCompatibilityCheck -Path <path> -SkipSystemChecks
```
 
Så här testar endast systemkrav:
```powershell
    Invoke-AzStorageSyncCompatibilityCheck -ComputerName <computer name>
```
 
Så här visar resultatet i CSV:
```powershell
    $errors = Invoke-AzStorageSyncCompatibilityCheck […]
    $errors | Select-Object -Property Type, Path, Level, Description | Export-Csv -Path <csv path>
```

### <a name="system-requirements"></a>Systemkrav
- En server som kör Windows Server 2012 R2, Windows Server 2016 eller Windows Server 2019:

    | Version | Godkända SKU: er | Distributionsalternativen som stöds |
    |---------|----------------|------------------------------|
    | Windows Server 2019 | Datacenter och Standard | Fullständig (server med ett gränssnitt) |
    | Windows Server 2016 | Datacenter och Standard | Fullständig (server med ett gränssnitt) |
    | Windows Server 2012 R2 | Datacenter och Standard | Fullständig (server med ett gränssnitt) |

    Framtida versioner av Windows Server läggs till när de blir tillgängliga. Tidigare versioner av Windows kan läggas till baserat på feedback från användare.

    > [!Important]  
    > Vi rekommenderar att alla servrar som du använder med Azure File Sync uppdaterad med de senaste uppdateringarna från Windows Update. 

- En server med minst 2 GiB minne.

    > [!Important]  
    > Om servern körs på en virtuell dator med dynamiskt minne aktiverat, kommer den virtuella datorn ska konfigureras med en minsta 2048 MiB minne.
    
- En lokalt ansluten volym som formaterats med NTFS-filsystemet.

### <a name="file-system-features"></a>Filsystem: funktioner

| Funktion | Supportstatus | Anteckningar |
|---------|----------------|-------|
| Åtkomstkontrollistor (ACL) | Stöds fullt ut | Windows-ACL: er bevaras av Azure File Sync och tillämpas av Windows Server på server-slutpunkter. Windows-ACL: er (ännu inte) stöds av Azure Files om filer används direkt i molnet. |
| Hårda länkar | Hoppades över | |
| Symboliska länkar | Hoppades över | |
| Monteringspunkter | Stöds delvis | Monteringspunkter kan vara roten av en serverslutpunkt, men de hoppas över om de ingår i en serverslutpunkt namnområde. |
| Vägkorsningar | Hoppades över | Till exempel Distributed File System DfrsrPrivate och DFSRoots mappar. |
| Referenspunkter | Hoppades över | |
| NTFS-komprimering | Stöds fullt ut | |
| Sparse-filer | Stöds fullt ut | Synkronisering av sparse-filer (blockeras inte), men de synkroniseras till molnet som en fullständig fil. Ändrar filinnehållet i molnet (eller på en annan server), är filen inte längre sparse när ändringen har hämtats. |
| Alternativa dataströmmar (ADS) | Bevaras, men inte synkroniserad | Till exempel synkroniseras inte taggar för dataklassificering som skapats av Filklassificeringsinfrastrukturen. Befintliga klassificeringstaggar på varje server-slutpunkter är orörda. |

> [!Note]  
> NTFS-volymer stöds. ReFS, FAT, FAT32 och andra filsystem stöds inte.

### <a name="files-skipped"></a>Filer som hoppades över

| Fil/mapp | Obs! |
|-|-|
| Desktop.ini | Filen som är specifika för system |
| ethumbs.DB$ | Temporär fil för miniatyrbilder |
| ~$\*.\* | Tillfällig Office-fil |
| \*.tmp | Temporär fil |
| \*.laccdb | Låsning Access DB-fil|
| 635D02A9D91C401B97884B82B3BCDAEA.* | Intern Sync-fil|
| \\System Volume Information | Mappen som är specifika för volym |
| $RECYCLE. BIN| Mapp |
| \\SyncShareState | Mapp för synkronisering |

### <a name="failover-clustering"></a>Redundanskluster
Windows Server Failover Clustering stöds av Azure File Sync för ”filserver för allmänt bruk” distributionsalternativ. Redundansklustring stöds inte på ”Scale-Out File Server for application data” (SOFS) eller på klusterdelade volymer (CSV).

> [!Note]  
> Azure File Sync-agenten måste installeras på varje nod i ett redundanskluster för synkronisering ska fungera korrekt.

### <a name="data-deduplication"></a>Datadeduplicering
**Agentversion 5.0.2.0**   
Datadeduplicering stöds på volymer med molnlagringsnivåer aktiverade på Windows Server 2016 och Windows Server 2019. Aktivera deduplicering på en volym med molnlagringsnivåer aktiverad kan du Cachelagra flera filer på plats utan att behöva etablera mer lagringsutrymme. Observera att dessa volym besparingar endast gäller lokala; ska inte vara deduplicerade data i Azure Files. 

**Windows Server 2012 R2 eller äldre agentversionerna**  
För volymer som inte har molnlagringsnivåer aktiverad, stöder Azure File Sync Windows Server-Datadeduplicering håller på att aktiveras på volymen.

**Anteckningar**
- Om Datadeduplicering är installerad innan du installerar Azure File Sync-agenten, krävs en omstart för Datadeduplicering och lagringsnivåer för moln på samma volym.
- Om Datadeduplicering är aktiverat på en volym när molnet lagringsnivåer är aktiverad, inledande dedupliceringsjobb för optimering optimerar filer på volymen som inte redan är nivåindelade och har följande inverkan på molnet lagringsnivåer:
    - Ledigt utrymme princip fortsätter att nivån filer enligt det lediga utrymmet på volymen med hjälp av den termiska kartan.
    - Datum principen hoppar över lagringsnivåer av filer som kan ha varit annars berättigad lagringsnivåer på grund av Deduplicering optimeringsjobbet får åtkomst till filerna.
- För pågående dedupliceringsjobben molnlagringsnivåer med datum princip kommer försenas av Datadeduplicering [MinimumFileAgeDays](https://docs.microsoft.com/powershell/module/deduplication/set-dedupvolume?view=win10-ps) inställningen, om filen inte redan är nivåindelade. 
    - Exempel: Om inställningen MinimumFileAgeDays är 7 dagar och principer för lagringsnivåer datum i molnet är 30 dagar, kommer principen datum delar filer efter 37 dagar.
    - Obs! När en fil är nivåer av Azure File Sync, optimering dedupliceringsjobb hoppar över filen.
- Om en server som kör Windows Server 2012 R2 med Azure File Sync-agenten installerad uppgraderas till Windows Server 2016 eller Windows Server 2019, måste följande steg utföras för att stödja Datadeduplicering och lagringsnivåer för moln på samma volym:  
    - Avinstallera Azure File Sync-agenten för Windows Server 2012 R2 och starta om servern.
    - Ladda ned Azure File Sync-agenten för den nya server-operativsystemsversionen (Windows Server 2016 eller Windows Server 2019).
    - Installera Azure File Sync-agenten och starta om servern.  
    
    Obs! Azure File Sync-konfigurationsinställningarna på servern bevaras när agenten avinstallerats och installerats om.

### <a name="distributed-file-system-dfs"></a>Distribuerat filsystem (DFS)
Azure File Sync har stöd för interop med DFS-namnområden (DFS-N) och DFS Replication (DFS-R).

**DFS Namespaces (DFS-N)** : Azure File Sync stöds fullt ut på DFS-N-servrar. Du kan installera Azure File Sync-agenten på en eller flera DFS-N-medlemmar att synkronisera data mellan slutpunkterna för server och molnslutpunkten. Mer information finns i [översikt över DFS-namnområden](https://docs.microsoft.com/windows-server/storage/dfs-namespaces/dfs-overview).
 
**DFS Replication (DFS-R)** : Eftersom DFS-R- och Azure File Sync är båda replikeringslösningar, i de flesta fall rekommenderar vi att ersätta DFS-R med Azure File Sync. Det finns dock flera scenarier där du vill använda DFS-R- och Azure File Sync tillsammans:

- Du migrerar från en DFS-R-distribution till en Azure File Sync-distribution. Mer information finns i [migrera en distribution av DFS Replication (DFS-R) till Azure File Sync](storage-sync-files-deployment-guide.md#migrate-a-dfs-replication-dfs-r-deployment-to-azure-file-sync).
- Alla lokala servrar som behöver en kopia av filinformationen kan anslutas direkt till internet.
- Grenen servrar konsolidera data på en enda navserver som du vill använda Azure File Sync.

För Azure File Sync och DFS-R att fungera sida-vid-sida:

1. Azure File Sync cloud lagringsnivåer måste inaktiveras på volymer med DFS-R replikerade mappar.
2. Serverslutpunkter ska inte konfigureras för DFS-R skrivskyddad replikering mappar.

Mer information finns i [översikt över DFS Replication](https://technet.microsoft.com/library/jj127250).

### <a name="sysprep"></a>Sysprep
Med hjälp av sysprep på en server som har Azure File Sync-agenten installerad stöds inte och kan leda till oväntade resultat. Agentregistreringen för installation och server ska inträffa efter distribution av server-avbildning och slutföra sysprep mini-installationen.

### <a name="windows-search"></a>Windows Search
Om molnet lagringsnivåer är aktiverat på en serverslutpunkt, filer som är nivåindelade överhoppade och indexeras inte av Windows Search. Icke-nivåindelade filer indexeras korrekt.

### <a name="antivirus-solutions"></a>Antiviruslösningar
Eftersom antivirus fungerar genom att skanna filer för känd skadlig kod, kan ett antivirusprogram orsaka återkallande av nivåindelade filer. I version 4.0 och senare av Azure File Sync-agenten har nivåindelade filer den säkra Windows FILE_ATTRIBUTE_RECALL_ON_DATA_ACCESS attributuppsättningen. Vi rekommenderar samråd med programvaruleverantören att lära dig hur du konfigurerar sin lösning om du vill hoppa över läsa filer med den här (många ske automatiskt) attributuppsättning. 

Microsofts interna antiviruslösningar, Windows Defender och System Center Endpoint Protection (SCEP), båda automatiskt hoppa över läsa filer som har den här replikuppsättningen. Vi har testat dem och identifierat en mindre problem: när du lägger till en server i en befintlig synkroniseringsgrupp filer mindre än 800 byte hämtas (hämtas) på den nya servern. Dessa filer finns kvar på den nya servern och kommer inte att vara nivåindelad eftersom de inte uppfyller kravet på lagringsnivåer storlek (> 64kb).

> [!Note]  
> Antivirusprogram kan kontrollera kompatibiliteten mellan deras produkter och Azure File Sync använder [Azure File Sync Antivirus kompatibilitet Test programsviten] (https://www.microsoft.com/download/details.aspx?id=58322), som är tillgänglig för hämtning på Microsoft Download Center.

### <a name="backup-solutions"></a>Lösningar för säkerhetskopiering
Som antivirus orsaka säkerhetskopieringslösningar återkallande av nivåindelade filer. Vi rekommenderar att du använder en lösning för säkerhetskopiering av molnet för att säkerhetskopiera Azure-filresursen i stället för en lokal säkerhetskopiering produkt.

Om du använder en lösning för säkerhetskopiering av en lokal ska säkerhetskopiering utföras på en server i synkroniseringsgruppen som har molnlagringsnivåer inaktiverad. När du utför en återställning, Använd volymnivå eller filnivå återställningsalternativen. Filer som har återställts med återställningsalternativet filnivå kommer att synkroniseras till alla slutpunkter i synkroniseringsgruppen och befintliga filer kommer att ersättas med den version som återställts från en säkerhetskopia.  Volymnivå återställningar ersätter inte nyare filversioner i Azure-filresurs eller andra serverslutpunkter.

> [!Note]  
> Återställning utan operativsystem (BMR) kan orsaka oväntade resultat och stöds inte för närvarande.

> [!Note]  
> VSS-ögonblicksbilder (inklusive fliken tidigare versioner) stöds inte på volymer som har molnlagringsnivåer aktiverad. Om molnet lagringsnivåer är aktiverad kan använda de Azure ögonblicksbilderna av filresurser för att återställa en fil från en säkerhetskopia.

### <a name="encryption-solutions"></a>Krypteringslösningar
Stöd för krypteringslösningar beror på hur de implementeras. Azure File Sync är känt att arbeta med:

- BitLocker-kryptering
- Azure Information Protection, Azure Rights Management Services (Azure RMS), och Active Directory RMS

Azure File Sync känns inte fungerar med:

- NTFS krypterade filsystem (EFS)

I allmänhet Azure File Sync ska ha stöd för samverkan med krypteringslösningar som finns nedanför filsystem, till exempel BitLocker, och med lösningar som är implementerade i filformat, till exempel Azure Information Protection. Inga särskilda interoperabilitet har gjorts för lösningar som finns ovanför filsystemet (till exempel NTFS EFS).

### <a name="other-hierarchical-storage-management-hsm-solutions"></a>Andra lösningar för hantering av hierarkisk lagring (HSM)
Ingen annan HSM-lösning bör användas tillsammans med Azure File Sync.

## <a name="region-availability"></a>Regional tillgänglighet
Azure File Sync är tillgänglig i följande regioner:

| Region | Plats för datacenter |
|--------|---------------------|
| Östra Australien | New South Wales |
| Sydöstra Australien | Victoria |
| Södra Brasilien | Sao Paolo tillstånd |
| Centrala Kanada | Toronto |
| Östra Kanada | Quebec City |
| Indien, centrala | Pune |
| Centrala USA | Iowa |
| Östasien | Hongkong SAR |
| East US | Virginia |
| USA, östra 2 | Virginia |
| Sydkorea, centrala| Seoul |
| Sydkorea, södra| Busan |
| Östra Japan | Tokyo, Saitama |
| Västra Japan | Osaka |
| Norra centrala USA | Illinois |
| Norra Europa | Irland |
| Södra centrala USA | Texas |
| Södra Indien | Chennai |
| Sydostasien | Singapore |
| Storbritannien, södra | London |
| Storbritannien, västra | Cardiff |
| Arizona (USA-förvaltad region) | Arizona |
| Texas (USA-förvaltad region) | Texas |
| Virginia (USA-förvaltad region) | Virginia |
| Västra Europa | Nederländerna |
| Västra centrala USA | Wyoming |
| Västra USA | Kalifornien |
| Västra USA 2 | Washington |

Azure File Sync stöder synkronisering endast med en Azure-filresurs som finns i samma region som Storage Sync-tjänsten.

### <a name="azure-disaster-recovery"></a>Azure-haveriberedskap
För att skydda mot förlust av en Azure-region, Azure File Sync kan integreras med den [geo-redundant lagringsredundans](../common/storage-redundancy-grs.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) (GRS)-alternativet. GRS-lagringen fungerar med hjälp av asynkrona Blockreplikering mellan lagring i den primära regionen, som du normalt interagerar, och lagring i den parade sekundära regionen. Microsoft kommer redundanslagring till den parade regionen vid katastrofåterställning vilket gör att en Azure-region till offlineläge tillfälligt eller permanent. 

> [!Warning]  
> Du bör inte initiera redundans för storage-konto om du använder din Azure-filresurs som en slutpunkt i molnet på ett GRS-lagringskonto. Synkronisering av orsaken till slutar fungera och kan också gör oväntade data går förlorade vid nyligen nivåindelade filer. När det gäller förlust av en Azure-region utlöser Microsoft redundans för storage-konto på ett sätt som är kompatibel med Azure File Sync.

Stöd för redundans-integrering mellan geo-redundant lagring och Azure File Sync kan är alla regioner för Azure File Sync kopplad till en sekundär region som matchar den sekundära regionen som används av storage. Dessa par är följande:

| Primär region      | Länkad region      |
|---------------------|--------------------|
| Östra Australien      | Sydöstra Australien|
| Sydöstra Australien | Östra Australien     |
| Södra Brasilien        | Södra centrala USA   |
| Centrala Kanada      | Östra Kanada        |
| Östra Kanada         | Centrala Kanada     |
| Indien, centrala       | Södra Indien        |
| Centrala USA          | USA, östra 2          |
| Östasien           | Sydostasien     |
| East US             | Västra USA            |
| USA, östra 2           | Centrala USA         |
| Östra Japan          | Västra Japan         |
| Västra Japan          | Östra Japan         |
| Sydkorea, centrala       | Sydkorea, södra        |
| Sydkorea, södra         | Sydkorea, centrala      |
| Norra Europa        | Västra Europa        |
| Norra centrala USA    | Södra centrala USA   |
| Södra centrala USA    | Norra centrala USA   |
| Södra Indien         | Indien, centrala      |
| Sydostasien      | Östasien          |
| Storbritannien, södra            | Storbritannien, västra            |
| Storbritannien, västra             | Storbritannien, södra           |
| Arizona (USA-förvaltad region)      | Texas (USA-förvaltad region)       |
| US Gov, Iowa         | Virginia (USA-förvaltad region)    |
| Virginia (USA-förvaltad region)      | Texas (USA-förvaltad region)       |
| Västra Europa         | Norra Europa       |
| Västra centrala USA     | Västra USA 2          |
| Västra USA             | East US            |
| Västra USA 2           | Västra centrala USA    |

## <a name="azure-file-sync-agent-update-policy"></a>Uppdateringsprincip för Azure File Sync-agenten
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="next-steps"></a>Nästa steg
* [Överväg att brandvägg och proxyinställningar](storage-sync-files-firewall-and-proxy.md)
* [Planera för en Azure Files-distribution](storage-files-planning.md)
* [Distribuera Azure Files](storage-files-deployment-guide.md)
* [Distribuera Azure File Sync](storage-sync-files-deployment-guide.md)
* [Övervaka Azure File Sync](storage-sync-files-monitoring.md)
