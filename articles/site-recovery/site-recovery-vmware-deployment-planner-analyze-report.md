---
title: Analysera rapporten för Distributionshanteraren för Azure Site Recovery för haveriberedskap för VMware till Azure | Microsoft Docs
description: Den här artikeln beskriver hur du analyserar rapporten som genereras av Distributionshanteraren för Azure Site Recovery för VMware-haveriberedskap till Azure.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 3/20/2019
ms.author: mayg
ms.openlocfilehash: cbea6785239c70a3cdb229d0811497f051224238
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61472611"
---
# <a name="analyze-the-azure-site-recovery-deployment-planner-report-for-vmware-disaster-recovery-to-azure"></a>Analysera rapporten för Distributionshanteraren för Azure Site Recovery för VMware-haveriberedskap till Azure

Den genererade rapporten i Microsoft Excel innehåller följande ark:
## <a name="on-premises-summary"></a>Lokal sammanfattning
På sidan Lokal sammanfattning visas en översikt över den profilerade VMware-miljön.

![Lokal sammanfattning av VMware-miljön](media/site-recovery-vmware-deployment-planner-analyze-report/on-premises-summary-v2a.png)

**Startdatum** och **slutdatum**: Start- och slutdatumen för de profileringsdata som ingår i rapportgenereringen. Som standard är startdatumet det datum då profileringen startades och slutdatumet är det datum när profileringen avslutades. Om du angav parametrarna -StartDate och -EndDate när du genererade rapporten visas dessa värden.

**Totalt antal profilering dagar**: Det totala antalet dagar för profilering mellan start- och slutdatumen som rapporten genererats.

**Antal kompatibla virtuella datorer**: Det totala antalet kompatibla virtuella datorer som nödvändig nätverksbandbredd, nödvändigt antal lagringskonton, Microsoft Azure-kärnor, konfigurationsservrar och ytterligare processervrar beräknas.

**Totala antalet diskar för samtliga kompatibla virtuella datorer**: Det tal som används som en av indata för att bestämma antalet konfigurationsservrar och ytterligare processervrar som ska användas i distributionen.

**Genomsnittligt antal diskar per kompatibel virtuell dator**: Det genomsnittliga antalet diskar för samtliga kompatibla virtuella datorer.

**Genomsnittlig diskstorlek (GB)** : Den genomsnittliga diskstorleken beräknad för samtliga kompatibla virtuella datorer.

**Desired RPO (minuter)** : Antingen standard rpo-mål eller det värde som angavs för parametern ”DesiredRPO” när rapporten genererades för att uppskatta nödvändig bandbredd.

**Önskad bandbredd (Mbit/s)** : Det värde som du angav för parametern ”Bandwidth” när rapporten genererades för att beräkna möjligt Återställningspunktmål.

**Observerad vanliga dataomsättning per dag (GB)** : Den genomsnittliga dataomsättning som observerats under alla profileringsdagar. Det här värdet används som ett av invärdena i rekommendationen för att fastställa antalet konfigurationsservrar och ytterligare processervrar som ska användas i distributionen.

## <a name="recommendations"></a>Rekommendationer

>[!Note]
>När du replikerar direkt till hanterade diskar, kan du ignorera rekommendationen för antalet lagringskonton.

Rekommendationsbladet för VMware till Azure-rapporten innehåller följande information enligt vald önskat RPO:

![Rekommendationer för VMware till Azure-rapport](media/site-recovery-vmware-deployment-planner-analyze-report/Recommendations-v2a.png)

### <a name="profiled-data"></a>Profileringsdata
![Den profilerade datavyn i kapacitetsplaneraren](media/site-recovery-vmware-deployment-planner-analyze-report/profiled-data-v2a.png)

**Profileringsdata period**: Den period då Profileringen kördes. Som standard innehåller verktyget alla profileringsdata i beräkningen, om inte rapporten genereras för en viss tidsperiod med hjälp av parametrarna StartDate och EndDate.

**Servernamn**: Namn eller IP-adressen för den VMware vCenter- eller ESXi-värd vars virtuella datorer rapporten genereras.

**Desired RPO**: Återställningspunktmålet för din distribution. Som standard beräknas vilken nätverksbandbredd som krävs för RPO-värden på 15, 30 respektive 60 minuter. De aktuella värdena på bladet uppdateras baserat på vad du väljer. Om du använde parametern *DesiredRPOinMin* när du genererade rapporten så visas det här värdet i resultatet Desired RPO (Önskat RPO-mål).

### <a name="profiling-overview"></a>Profileringsöversikt

![Profilering av resultat i kapacitetsplaneraren](media/site-recovery-vmware-deployment-planner-analyze-report/profiling-overview-v2a.png)

**Totalt antal profilerade virtuella datorer**: Det totala antalet virtuella datorer vars profilerade data är tillgängliga. Om VMListFile innehåller namn på virtuella datorer som inte har profilerats så beaktas inte dessa virtuella datorer i rapporten och de ingår inte i värdet för antalet virtuella datorer som har profilerats.

**Kompatibla virtuella datorer**: Hur många virtuella datorer som kan skyddas till Azure med Site Recovery. Det här är det totala antalet kompatibla virtuella datorer som nödvändig nätverksbandbredd, antal lagringskonton, antal Azure-kärnor samt antal konfigurationsservrar och ytterligare processervrar beräknas för. Information om varje kompatibel virtuell dator finns i avsnittet ”Kompatibla virtuella datorer”.

**Inkompatibla virtuella datorer**: Antalet profilerade virtuella datorer som inte kan skyddas med Site Recovery. Orsaken till inkompatibiliteten beskrivs i avsnittet Inkompatibla virtuella datorer. Om VMListFile innehåller namnen på virtuella datorer som inte har profilerats undantas dessa virtuella datorer från antalet inkompatibla virtuella datorer. Dessa virtuella datorer visas under Data not found (Inga data hittades) i slutet av avsnittet Incompatible VMs (Inkompatibla virtuella datorer).

**Desired RPO**: Din önskade återställningspunkt mål i minuter. Rapporten genereras för tre RPO-värden: 15 (standard), 30 och 60 minuter. Rekommendationen angående bandbredd i rapporten förändras baserat på vilket alternativ du väljer i listrutan Desired RPO (Önskat RPO-mål) uppe till höger på bladet. Om du har genererat rapporten med ett anpassat värde för parametern *-DesiredRPO* visas det här anpassade värdet som standardvärde i listrutan Desired RPO (Önskat återställningspunktmål).

### <a name="required-network-bandwidth-mbps"></a>Required Network Bandwidth (Mbps) (Nödvändig nätverksbandbredd (Mbit/s))

![Nödvändig nätverksbandbredd i kapacitetsplaneraren](media/site-recovery-vmware-deployment-planner-analyze-report/required-network-bandwidth-v2a.png)

**Att uppfylla Återställningspunktmålet 100 procent av tiden:** Den rekommenderade bandbredden i Mbit/s som du bör allokera om rpo-MÅLET 100 procent av tiden. Den här mängden bandbredd måste vara reserverad för stabil deltareplikering av samtliga kompatibla virtuella datorer om du helt ska undvika överträdelser av RPO-målet.

**Att uppfylla RPO 90 procent av tiden**: Bandbreddspriserna eller av någon annan anledning, om du inte kan ange den bandbredd som krävs för att uppnå rpo-MÅLET 100 procent av tiden kan du gå med en lägre bandbreddsinställning som uppnår önskat RPO 90 procent av tiden. I rapporten ges även en ”tänk om”-analys av hur många RPO-överträdelser du kan förvänta dig och deras varaktighet, så att du bättre ska förstå vad som kan hända om du tilldelar den här lägre bandbredden.

**Dataflöde:** Dataflödet från servern där du körde kommandot GetThroughput till den Microsoft Azure-region där lagringskontot finns. Dataflödesvärdet är en uppskattning av den nivå du kan uppnå när du skyddar de kompatibla virtuella datorerna med Site Recovery, förutsatt att lagrings- och nätverksegenskaperna för konfigurationsservern/processervern förblir desamma som för den server där du körde verktyget.

Du bör ange den rekommenderade bandbredden för att uppfylla återställningspunktmålet 100 procent av tiden för replikering. Om inte verktyget rapporterar ökade dataflöden trots att du har ställt in bandbredden gör du följande:

1. Kontrollera om det finns någon tjänstkvalitet (QoS) för nätverket som begränsar dataflödet för Site Recovery.

2. Kontrollera om valvet för Site Recovery ligger i den närmaste Microsoft Azure-region som stöds fysiskt, så att du minimerar svarstiden i nätverket.

3. Kontrollera de lokala lagringsegenskaperna för att avgöra om du kan förbättra maskinvaran (till exempel hårddisk till SSD).

4. Ändra inställningarna för Site Recovery på processervern och [öka den mängd bandbredd i nätverket som används till replikering](./site-recovery-plan-capacity-vmware.md#control-network-bandwidth).

Om du kör verktyget på en konfigurations- eller processerver som redan har skyddade virtuella datorer bör du köra verktyget några gånger. Det uppnådda antalet dataflödesändringar beror på mängden omsättningsuppdateringar som bearbetas vid den aktuella tidpunkten.

För alla företagsdistributioner av Site Recovery bör du använda [ExpressRoute](https://aka.ms/expressroute).

### <a name="required-storage-accounts"></a>Nödvändiga lagringskonton
I följande diagram visas hur många lagringskonton (standard och premium) som behövs för att skydda alla kompatibla virtuella datorer. Om du vill veta vilket lagringskonto som ska användas för varje virtuell dator kan du läsa avsnittet ”Placering av VM-lagring”.

![Nödvändiga lagringskonton i kapacitetsplaneraren](media/site-recovery-vmware-deployment-planner-analyze-report/required-storage-accounts-v2a.png)

### <a name="required-number-of-azure-cores"></a>Nödvändigt antal Azure-kärnor
Resultatet är det totala antalet kärnor som ska konfigureras före redundansväxling eller redundanstest av alla kompatibla virtuella datorer. Om det inte finns tillräckligt många kärnor tillgängliga i prenumerationen kan inte Site Recovery skapa virtuella datorer vid redundanstestet eller redundansväxlingen.

![Nödvändigt antal Azure-kärnor i kapacitetsplaneraren](media/site-recovery-vmware-deployment-planner-analyze-report/required-cores-v2a.png)

### <a name="required-on-premises-infrastructure"></a>Krav på lokal infrastruktur
Det här är det totala antalet konfigurationsservrar och ytterligare processervrar som måste konfigureras för att skydda alla kompatibla virtuella datorer. Beroende på vilka [storleksrekommendationer för konfigurationsservern som stöds](https://aka.ms/asr-v2a-on-prem-components), kan verktyget rekommendera ytterligare servrar. Rekommendationen bygger på det största värdet för antingen dataomsättning per dag eller det största antal skyddade virtuella datorer (förutsatt i genomsnitt tre diskar per virtuell dator), beroende på vilken gräns som uppnås först på konfigurationsservern eller den kompletterande processervern. Du hittar detaljerad information om den totala dataomsättningen per dag och det totala antalet skyddade diskar i avsnittet ”Lokal sammanfattning”.

![Lokal infrastruktur som krävs för kapacitetsplaneraren](media/site-recovery-vmware-deployment-planner-analyze-report/required-on-premises-components-v2a.png)

### <a name="what-if-analysis"></a>Konsekvensanalys
I den här analysen beskrivs hur många överträdelser som kan inträffa under profileringsperioden när du tilldelar en lägre bandbredd för att önskat RPO-mål ska uppfyllas 90 procent av gångerna. En eller flera överträdelser av återställningspunktmålen kan inträffa på en viss dag. Grafen visar toppåterställningspunktmålet för dagen.
Utifrån den här analysen kan du avgöra om du kan godta antalet RPO-överträdelser och dagens största RPO-överträdelse sett till den lägre bandbredden. Om värdena är godtagbara kan du allokera den lägre bandbredden för replikering. Annars allokerar du den högre bandbredd som rekommenderas för att du ska uppnå önskat RPO-mål 100 % av gångerna.

![Konsekvensanalys i kapacitetsplaneraren](media/site-recovery-vmware-deployment-planner-analyze-report/what-if-analysis-v2a.png)

### <a name="recommended-vm-batch-size-for-initial-replication"></a>Recommended VM batch size for initial replication (Rekommenderad VM-batchstorlek för den initiala replikeringen)
I det här avsnittet rekommenderar vi det antal virtuella datorer som kan skyddas parallellt för att slutföra den inledande replikeringen inom 72 timmar med den föreslagna bandbredden för att uppfylla önskade återställningspunktmål 100 procent av den tid som anges. Det här värdet är ett konfigurerbart värde. Du kan ändra värdet då rapporten skapas med parametern *GoalToCompleteIR*.

I diagrammet här visas olika bandbreddsvärden och beräknad batchstorlek för virtuella datorer där den initiala replikeringen kan slutföras inom 72 timmar baserat på den genomsnittliga identifierade storleken för de virtuella datorerna bland alla kompatibla virtuella datorer.

I den offentliga förhandsutgåvan anger inte rapporten vilka virtuella datorer som ska ingå i en batch. Du kan använda diskstorleken som visas i avsnittet Compatible VMs (Kompatibla virtuella datorer) för att se de virtuella datorernas storlek och välja virtuella datorer till en batch, eller så kan du välja virtuella datorer sett till kända arbetsbelastningsegenskaper. Tidsåtgången för den inledande replikeringen förändras proportionellt med avseende på faktisk diskstorlek på den virtuella datorn, förbrukat diskutrymme och tillgängligt dataflöde i nätverket.

![Rekommenderad batchstorlek för virtuella datorer](media/site-recovery-vmware-deployment-planner-analyze-report/ir-batching-v2a.png)

### <a name="cost-estimation"></a>Kostnadsuppskattning
I diagrammet visas en sammanfattning av den uppskattade totala kostnaden för haveriberedskap (DR) till Azure i din valda målregion och i den valuta du har angett för rapporten.

![Sammanfattning av kostnadsuppskattning](media/site-recovery-vmware-deployment-planner-analyze-report/cost-estimation-summary-v2a.png)

Sammanfattningen hjälper dig att förstå den kostnad som du behöver betala för lagring, beräkning, nätverk och licenser när du skyddar alla dina kompatibla virtuella datorer till Azure med Azure Site Recovery. Kostnaden beräknas för kompatibla virtuella datorer och inte för alla profilerade virtuella datorer.  

Du kan visa kostnaden per månad eller per år. Läs mer om [målregioner som stöds](./site-recovery-vmware-deployment-planner-cost-estimation.md#supported-target-regions) och [valutor som stöds](./site-recovery-vmware-deployment-planner-cost-estimation.md#supported-currencies).

**Komponentkostnader** den totala DR-kostnaden delas upp i fyra komponenter: Beräkning, lagring, nätverk och Azure Site Recovery-licenskostnad. Kostnaden beräknas baserat på förbrukningen som tillkommer under replikering och DR-testtiden för beräkning, lagring (premium och standard), ExpressRoute/VPN som har konfigurerats mellan den lokala platsen och Azure och Azure Site Recovery-licens.

**Cost by states** (Kostnad per tillstånd) Den totala kostnaden för haveriberedskap (DR) är kategorier baserat på två olika tillstånd – replikering och DR-test.

**Replikeringskostnad**:  Kostnaden som tillkommer under replikering. Det här täcker kostnaden för lagring, nätverk och Azure Site Recovery-licensen.

**DR-test**: Kostnaden som tillkommer under redundansväxlingstest. Azure Site Recovery startar virtuella datorer under redundanstest. DR-testkostnaden täcker beräkning och lagring för de virtuella datorer som körs.

**Azure storage cost per Month/Year** (Azure Storage-kostnad per månad/år) Det visar den totala lagringskostnad som tillkommer för premium- och standardlagring för replikering och DR-test.
Du kan visa en detaljerad kostnadsanalys per VM på arket [Cost Estimation](site-recovery-vmware-deployment-planner-cost-estimation.md) (Kostnadsuppskattning).

### <a name="growth-factor-and-percentile-values-used"></a>Tillväxtfaktor och percentilvärde som används
I det här avsnittet längst ned på bladet visas vilket percentilvärde som använts för prestandaräknarna för de profilerade virtuella datorerna (standardvärdet är den 95:e percentilen) och vilken tillväxtfaktor som använts i alla beräkningar (standardvärdet är 30 procent).

![Tillväxtfaktor och percentilvärde som används](media/site-recovery-vmware-deployment-planner-analyze-report/growth-factor-v2a.png)

## <a name="recommendations-with-available-bandwidth-as-input"></a>Rekommendationer med tillgänglig bandbredd som indata

![Rekommendationer med tillgänglig bandbredd som indata](media/site-recovery-vmware-deployment-planner-analyze-report/profiling-overview-bandwidth-input-v2a.png)

Du kan ha en situation där du vet att du inte kan ange en bandbredd på mer än x Mbit/s för Site Recovery-replikering. Du kan välja att ange tillgänglig bandbredd i verktyget (med parametern -Bandwidth när du genererar rapporten) och då få se vilket RPO-mål i minuter du kan uppnå. Utifrån det här möjliga RPO-värdet kan du avgöra om du måste konfigurera ytterligare bandbredd eller om du nöjer dig med en lösning för haveriberedskap med det aktuella RPO-värdet.

![Möjligt återställningspunktmål för 500 Mbit/s bandbredd](media/site-recovery-vmware-deployment-planner-analyze-report/achievable-rpo-v2a.png)

## <a name="vm-storage-placement"></a>Placering av VM-lagring

>[!Note]
>När du replikerar direkt till hanterade diskar, behöver du inte bekymra dig om antalet lagringskonton. Använd endast rekommendationen för lagring på lagringstyp (Standard eller Premium). Samma typ kan användas för hanterade diskar.

![Placering av VM-lagring](media/site-recovery-vmware-deployment-planner-analyze-report/vm-storage-placement-v2a.png)

**Storage-disktyp**: Antingen ett standard- eller premium storage-konto som används för att replikera alla av motsvarande virtuella datorer i den **virtuella datorer på plats som är** kolumn.

**Suggested Prefix**: Det föreslagna prefixet på tre tecken som kan användas för att namnge lagringskontot. Du kan använda ditt eget prefix, men verktygets förslag följer [namngivningskonventionen för partitioner av lagringskonton](https://aka.ms/storage-performance-checklist).

**Föreslaget kontonamn**: Lagringskontots namn när du inkluderar det föreslagna prefixet. Ersätt namnet inom hakparenteser (< och >) med egna indata.

**Log Storage Account**: Alla replikeringsloggar lagras i ett standardlagringskonto. För virtuella datorer som replikerar till ett Premium Storage-konto konfigurerar du ytterligare ett Standard Storage-konto för logglagringsutrymme. Flera lagringskonton för premiumreplikering kan använda samma standardkonto för logglagring. Virtuella datorer som replikeras till lagringskonton av standardtyp använder samma lagringskonto för loggarna.

**Suggested Log Account Name**: Loggen namnet på ditt lagringskonto när du inkluderar det föreslagna prefixet. Ersätt namnet inom hakparenteser (< och >) med egna indata.

**Placement Summary**: En sammanfattning av den totala virtuella belastningen på lagringskontot vid tidpunkten för replikering och vid redundanstest / redundansväxling. I översikten ingår det totala antalet virtuella datorer som har mappats till lagringskontot, totalt antal läs- och skrivåtgärder (IOPS) för de virtuella datorer som placerats på lagringskontot, totalt antal skrivoperationer (replikering), total etablerad storlek sett till alla diskar och det totala antalet diskar.

**Virtuella datorer på plats som är**: En lista över alla virtuella datorer som ska placeras på det angivna lagringskontot för optimala prestanda och användning.

## <a name="compatible-vms"></a>Compatible VMs (Kompatibla virtuella datorer)
![Excel-kalkylblad med kompatibla virtuella datorer](media/site-recovery-vmware-deployment-planner-analyze-report/compatible-vms-v2a.png)

**Namn på virtuell dator**: Den virtuella datorns namn eller IP-adress som används i VMListFile när en rapport skapas. I den här kolumnen visas även de diskar (VMDK:er) som är kopplade till de virtuella datorerna. För att skilja virtuella vCenter-datorer med samma namn eller IP-adresser åt innefattar namnen ESXi-värdnamnet. Den angivna ESXi-värden är den värd där den virtuella datorn har placerats när verktyget identifierades under profileringsperioden.

**VM-kompatibilitet**: Värden är **Ja** och **Ja**\*. **Ja** \* för instanser där den virtuella datorn är en anpassning för [premium SSD](../virtual-machines/windows/disks-types.md). Här passar den profilerade högomsättnings- eller IOPS-disken i kategorin P20 eller P30, men storleken på disken gör att den mappas ned till en P10 eller P20. Lagringskontot avgör vilken Premium Storage-disktyp som en disk ska mappas till, baserat på dess storlek. Exempel:
* < 128 GB är en P10.
* 128 GB till 256 GB är en P15
* 256 till 512 GB är en P20.
* 512 till 1 024 GB är en P30.
* 1 025 till 2 048 GB är en P40.
* 2 049 till 4 095 GB är en P50.

Om exempelvis arbetsbelastningsegenskaperna för en disk placerar den i kategorin P20 eller P30, men storleken mappar den till en lägre Premium Storage-disktyp, markerar verktyget den här virtuella datorn som **Ja**\*. Verktyget rekommenderar också att du antingen ändrar källdiskens storlek så att den passar den rekommenderade Premium Storage-disktypen eller ändrar måldisktypen efter redundansväxling.

**Lagringstyp**: Standard eller premium.

**Suggested Prefix**: Prefixet tre tecken för lagringskontot.

**Storage-konto**: Namnet som använder det föreslagna lagringskonto prefixet.

**Högsta R/W IOPS (med tillväxtfaktor)** : Den högsta IOPS-arbetsbelastningen läsning/skrivningen på disken (standardvärdet är 95: e percentilen) inklusive faktorn för framtida tillväxt (standardvärdet är 30 procent). Observera att det totala antalet läs/skriv-IOPS för en virtuell dator inte alltid är summan av de enskilda diskarnas läs/skriv-IOPS, eftersom den virtuella datorns högsta läs/skriv-IOPS är den högsta summan av de enskilda diskarnas läs/skriv-IOPS under varje minut av profileringsperioden.

**Högsta Dataomsättning i Mbit/s (med tillväxtfaktor)** : Högsta dataomsättningsfrekvensen på disken (standardvärdet är 95: e percentilen) inklusive faktorn för framtida tillväxt (standardvärdet är 30 procent). Observera att den totala dataomsättningen för den virtuella datorn inte alltid är summan av de enskilda diskarnas dataomsättning, eftersom den virtuella datorns högsta dataomsättning är den högsta summan av de enskilda diskarnas dataomsättning under varje minut av profileringsperioden.

**Azure VM-storlek**: Lämplig mappad Azure Cloud Services VM-storlek för den här lokala virtuella datorn. Mappningen baseras på det lokala virtuella datorminnet, antalet diskar/kärnor/nätverkskort och läs- och skrivåtgärder, IOPS. Rekommendationen är alltid den lägsta virtuella Azure-datorstorlek som matchar alla lokala virtuella datoregenskaper.

**Antalet diskar**: Det totala antalet virtuella datordiskar (vmdk: er) på den virtuella datorn.

**Diskstorlek (GB)** : Total installationsstorlek för alla diskar på den virtuella datorn. Storleken för de enskilda diskarna i den virtuella datorn visas också i verktyget.

**Kärnor**: Antal processorkärnor på den virtuella datorn.

**Minne (MB)** : RAM-minne på den virtuella datorn.

**Nätverkskort**: Antalet nätverkskort på den virtuella datorn.

**Starttyp**: Starttyp för den virtuella datorn. Den kan vara BIOS eller EFI.  För närvarande stöder Azure Site Recovery virtuella Windows Server EFI-datorer (Windows Server 2012, 2012 R2 och 2016) förutsatt att antalet partitioner i startdisken är under 4 och startsektorstorleken är 512 byte. För att skydda virtuella EFI-datorer måste mobilitetstjänstversionen för Azure Site Recovery vara 9.13 eller högre. Endast redundans stöds av virtuella EFI-datorer. Återställning efter fel stöds inte.  

**OS-typ**: Det är OS-typ för den virtuella datorn. Det kan vara antingen Windows eller Linux eller något annat som är baserat på den valda mallen från VMware vSphere när du skapar den virtuella datorn.  

## <a name="incompatible-vms"></a>Incompatible VMs (Inkompatibla virtuella datorer)

![Excel-ark med inkompatibla virtuella datorer
](media/site-recovery-vmware-deployment-planner-analyze-report/incompatible-vms-v2a.png)

**Namn på virtuell dator**: Den virtuella datorns namn eller IP-adress som används i VMListFile när en rapport skapas. I den här kolumnen visas även de diskar (VMDK:er) som är kopplade till de virtuella datorerna. För att skilja virtuella vCenter-datorer med samma namn eller IP-adresser åt innefattar namnen ESXi-värdnamnet. Den angivna ESXi-värden är den värd där den virtuella datorn har placerats när verktyget identifierades under profileringsperioden.

**VM-kompatibilitet**: Anger varför den här virtuella datorn inte kan skyddas med Site Recovery. Anledningarna beskrivs för varje inkompatibel disk av den virtuella datorn och kan, baserat på publicerade [lagringsgränser](https://aka.ms/azure-storage-scalbility-performance), vara något av följande:

* Diskstorleken är > 4 095 GB. Azure Storage har för närvarande inte stöd för diskar som är större än 4 095 GB.

* Operativsystemets disk är > 2 048 GB. Azure Storage har för närvarande inte stöd för operativsystemdiskar som är större än 2 048 GB.

* Total storlek för den virtuella datorn (replikering + TFO) överskrider den gräns för lagringskontostorlek som stöds (35 TB). Den här inkompatibiliteten uppstår vanligen när en enskild disk i den virtuella datorn har en prestandaegenskap som överskrider den maxgräns som stöds av Azure- eller Site Recovery-gränserna för standardlagring. Denna instans skickar den virtuella datorn till Premium Storage-zonen. Maxgränsen för ett lagringskonto av premiumtyp är däremot 35 TB, och det går inte att skydda en enda virtuell dator över flera lagringskonton. Tänk också på att när ett redundanstest körs på en skyddad virtuell dator körs det på samma lagringskonto där replikeringen körs. I den här instansen ställer du in 2 ggr storleken på disken för att replikeringen ska fortskrida samtidigt som redundanstestningen genomförs.

* Käll-IOPS överskrider IOPS-gränsen för lagring på 7500 per disk.

* Käll-IOPS överskrider IOPS-gränsen för lagring på 80 000 per virtuell dator.

* Den genomsnittliga dataomsättningen överskrider den dataomsättningsgräns som stöds av Site Recovery på 10 MB/s för den genomsnittliga I/O-storleken för disken.

* Den genomsnittliga dataomsättningen överskrider den dataomsättningsgräns som stöds av Site Recovery på 25 MB/s för den genomsnittliga I/O-storleken för den virtuella datorn (summan av alla diskars omsättning).

* Den högsta dataomsättningen för alla diskar i den virtuella datorn överskrider högsta gränsen i Site Recovery på 54 MB/s per virtuell dator.

* Genomsnittligt antal effektiva skrivåtgärder (IOPS) överskrider gränsen i Site Recovery på 840 per disk.

* Beräknat lagringsutrymme för ögonblicksbilder överskrider gränsen på 10 TB.

* Total dataomsättning per dag överskrider gränsen för omsättning som stöds per dag med 2 TB av en by a processerver.


**Högsta R/W IOPS (med tillväxtfaktor)** : Den högsta IOPS-arbetsbelastningen på disken (standardvärdet är 95: e percentilen) inklusive faktorn för framtida tillväxt (standardvärdet är 30 procent). Observera att det totala antalet läs/skriv-IOPS för den virtuella datorn inte alltid är summan av de enskilda diskarnas läs/skriv-IOPS, eftersom den virtuella datorns högsta läs/skriv-IOPS är den högsta summan av de enskilda diskarnas läs/skriv-IOPS under varje minut av profileringsperioden.

**Högsta Dataomsättning i Mbit/s (med tillväxtfaktor)** : Högsta dataomsättningsfrekvensen på disken (standardvärdet den 95: e percentilen) inklusive faktorn för framtida tillväxt (standardvärdet är 30 procent). Observera att den totala dataomsättningen för den virtuella datorn inte alltid är summan av de enskilda diskarnas dataomsättning, eftersom den virtuella datorns högsta dataomsättning är den högsta summan av de enskilda diskarnas dataomsättning under varje minut av profileringsperioden.

**Antalet diskar**: Det totala antalet vmdk: er på den virtuella datorn.

**Diskstorlek (GB)** : Total installationsstorlek för alla diskar på den virtuella datorn. Storleken för de enskilda diskarna i den virtuella datorn visas också i verktyget.

**Kärnor**: Antal processorkärnor på den virtuella datorn.

**Minne (MB)** : Mängden RAM-minne på den virtuella datorn.

**Nätverkskort**: Antalet nätverkskort på den virtuella datorn.

**Starttyp**: Starttyp för den virtuella datorn. Den kan vara BIOS eller EFI.  För närvarande stöder Azure Site Recovery virtuella Windows Server EFI-datorer (Windows Server 2012, 2012 R2 och 2016) förutsatt att antalet partitioner i startdisken är under 4 och startsektorstorleken är 512 byte. För att skydda virtuella EFI-datorer måste mobilitetstjänstversionen för Azure Site Recovery vara 9.13 eller högre. Endast redundans stöds av virtuella EFI-datorer. Återställning efter fel stöds inte.

**OS-typ**:  Det är OS-typ för den virtuella datorn. Det kan vara antingen Windows eller Linux eller något annat som är baserat på den valda mallen från VMware vSphere när du skapar den virtuella datorn.

## <a name="azure-site-recovery-limits"></a>Gränser för Azure Site Recovery
Följande tabell innehåller gränserna för Azure Site Recovery. Dessa gränser är baserade på våra tester, men de täcker inte alla möjliga kombinationer av program-I/O. De faktiska resultaten kan variera beroende på blandningen av I/O i ditt program. För bästa resultat även efter distributionsplaneringen rekommenderar vi alltid att du kör omfattande programtester med redundanstest för att få en bild av verklig prestanda för programmet.

**Replication Storage Target** (Lagringsmål för replikering) | **Average Source Disk I/O Size** (Genomsnittlig I/O-storlek för källdisk) |**Average Source Disk Data Churn** (Genomsnittlig dataomsättning för källdisk) | **Total Source Disk Data Churn Per Day** (Total dataomsättning per dag för källdisk)
---|---|---|---
Standard Storage | 8 kB | 2 MB/s | 168 GB per disk
Premium P10- eller P15-disk | 8 kB  | 2 MB/s | 168 GB per disk
Premium P10- eller P15-disk | 16 kB | 4 MB/s |  336 GB per disk
Premium P10- eller P15-disk | 32 kB eller mer | 8 MB/s | 672 GB per disk
Premium P20-, P30-, P40- eller P50-disk | 8 kB    | 5 MB/s | 421 GB per disk
Premium P20-, P30-, P40- eller P50-disk | minst 16 kB | 20 MB/s | 1684 GB per disk

**Källans dataomsättning** | **Övre gräns**
---|---
Genomsnittlig dataomsättning per virtuell dator| 25 MB/s
Högsta dataomsättning av alla diskar på en virtuella dator | 54 MB/s
Maximal dataomsättning per dag som stöds av en processerver | 2 TB

Det här är genomsnittliga värden baserade på en I/O-överlappning på 30 procent. Site Recovery kan hantera högre dataflöden med annan överlappning, större skrivningsstorlek och verkligt I/O-beteende under arbetsbelastningen. Föregående antal antar en typisk eftersläpning på cirka fem minuter. Det vill säga, när data har överförts bearbetas de och en återställningspunkt skapas inom fem minuter.


## <a name="cost-estimation"></a>Kostnadsuppskattning
Läs mer om [kostnadsuppskattning](site-recovery-vmware-deployment-planner-cost-estimation.md).


## <a name="next-steps"></a>Nästa steg
Läs mer om [kostnadsuppskattning](site-recovery-vmware-deployment-planner-cost-estimation.md).
