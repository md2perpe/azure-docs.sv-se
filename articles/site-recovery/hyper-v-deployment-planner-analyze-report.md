---
title: Analysera rapporten för Distributionshanteraren för Azure Site Recovery för haveriberedskap för Hyper-V-datorer till Azure | Microsoft Docs
description: Den här artikeln beskriver hur du analyserar en rapport som genereras av Distributionshanteraren för Azure Site Recovery för haveriberedskap för Hyper-V-datorer till Azure.
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 3/20/2019
ms.author: mayg
ms.openlocfilehash: 7bfe382ac1a175aafb4944dffa8d12a372f4fb70
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60772899"
---
# <a name="analyze-the-azure-site-recovery-deployment-planner-report"></a>Analysera rapporten för Distributionshanteraren för Azure Site Recovery
I den här artikeln diskuteras bladen i Excel-rapporterna som genereras av distributionshanteraren för Azure Site Recovery för scenariot Hyper-V till Azure.

## <a name="on-premises-summary"></a>Lokal sammanfattning
Bladet med lokal sammanfattning ger en översikt över den profilerade Hyper-V-miljön.

![Lokal sammanfattning](media/hyper-v-deployment-planner-analyze-report/on-premises-summary-h2a.png)

**Startdatum** och **slutdatum**: Start- och slutdatumen för de profileringsdata som ingår i rapportgenereringen. Som standard är startdatumet det datum då profileringen startades och slutdatumet är det datum när profileringen avslutades. Den här informationen kan vara värdena ”StartDate” och ”EndDate” om rapporten genererades med dessa parametrar.

**Totalt antal profilering dagar**: Det totala antalet dagar för profilering mellan start- och slutdatumen som rapporten genererats.

**Antal kompatibla virtuella datorer**: Det totala antalet kompatibla virtuella datorer som nödvändig nätverksbandbredd, nödvändigt antal lagringskonton och Azure-kärnor beräknas.

**Totala antalet diskar för samtliga kompatibla virtuella datorer**: Det totala antalet diskar för samtliga kompatibla virtuella datorer.

**Genomsnittligt antal diskar per kompatibel virtuell dator**: Det genomsnittliga antalet diskar för samtliga kompatibla virtuella datorer.

**Genomsnittlig diskstorlek (GB)** : Den genomsnittliga diskstorleken beräknad för samtliga kompatibla virtuella datorer.

**Desired RPO (minuter)** : Antingen standard rpo-mål eller det värde som angavs för parametern ”DesiredRPO” när rapporten genererades för att uppskatta nödvändig bandbredd.

**Önskad bandbredd (Mbit/s)** : Det värde som du angav för parametern ”bandbredd” vid tidpunkten för rapporten genererades för att beräkna realistiska mål för återställningspunkt (RPO).

**Observerad vanliga dataomsättning per dag (GB)** : Den genomsnittliga dataomsättning som observerats under alla profileringsdagar.

## <a name="recommendations"></a>Rekommendationer 
Rekommendationsbladet för Hyper-V till Azure-rapporten innehåller följande information enligt vald önskat RPO:

![Rekommendationer för Hyper-V till Azure-rapport](media/hyper-v-deployment-planner-analyze-report/Recommendations-h2a.png)

### <a name="profile-data"></a>Profildata
![Profildata](media/hyper-v-deployment-planner-analyze-report/profile-data-h2a.png)

**Profileringsdata period**: Den period då Profileringen kördes. Som standard innehåller verktyget alla profilerade data i beräkningen. Om du använde alternativet StartDate och EndDate i rapportgenereringen, genereras rapporten för den specifika perioden. 

**Antal profilerade Hyper-V-servrar**: Antalet Hyper-V-servrar vars VM-rapport genereras. Välj en siffra för att visa namnet på Hyper-V-servrarna. Detta öppnar bladet On-premises Storage Requirement (Krav för lokal lagring), där alla servrar finns angivna tillsammans med sina lagringskrav. 

**Desired RPO**: Återställningspunktmålet för din distribution. Som standard beräknas vilken nätverksbandbredd som krävs för RPO-värden på 15, 30 respektive 60 minuter. De aktuella värdena på bladet uppdateras baserat på vad du väljer. Om du använde parametern DesiredRPOinMin när du genererade rapporten så visas det här värdet i resultatet Desired RPO (Önskat RPO-mål).

### <a name="profiling-overview"></a>Profileringsöversikt
![Profileringsöversikt](media/hyper-v-deployment-planner-analyze-report/profiling-overview-h2a.png)

**Totalt antal profilerade virtuella datorer**: Det totala antalet virtuella datorer vars profilerade data är tillgängliga. Om VMListFile innehåller namn på virtuella datorer som inte har profilerats så beaktas inte dessa virtuella datorer i rapporten och de ingår inte i värdet för antalet virtuella datorer som har profilerats.

**Kompatibla virtuella datorer**: Hur många virtuella datorer som kan skyddas till Azure med hjälp av Azure Site Recovery. Det här är det totala antalet kompatibla virtuella datorer som nödvändig nätverksbandbredd, antal lagringskonton och antal Azure-kärnor beräknas för. Information om varje kompatibel virtuell dator finns i avsnittet ”Kompatibla virtuella datorer”.

**Inkompatibla virtuella datorer**: Antalet profilerade virtuella datorer som inte kan skyddas med Site Recovery. Orsaken till inkompatibiliteten beskrivs i avsnittet Inkompatibla virtuella datorer. Om VMListFile innehåller namnen på virtuella datorer som inte har profilerats undantas dessa virtuella datorer från antalet inkompatibla virtuella datorer. Dessa virtuella datorer visas under Data not found (Inga data hittades) i slutet av avsnittet Incompatible VMs (Inkompatibla virtuella datorer).

**Desired RPO**: Din önskade återställningspunkt mål i minuter. Rapporten genereras för tre RPO-värden: 15 (standard), 30 och 60 minuter. Rekommendationen angående bandbredd i rapporten förändras baserat på vad du väljer i listrutan **Desired RPO** (Önskat RPO-mål) uppe till höger på bladet. Om du genererade rapporten med ett anpassat värde för parametern -DesiredRPO visas det här anpassade värdet som standardvärde i listrutan **Desired RPO** (Önskat återställningspunktmål).

### <a name="required-network-bandwidth-mbps"></a>Required Network Bandwidth (Mbps) (Nödvändig nätverksbandbredd (Mbit/s))
![Nödvändig nätverksbandbredd](media/hyper-v-deployment-planner-analyze-report/required-network-bandwidth-h2a.png)

**Att uppfylla rpo-MÅLET 100% av tiden**: Den rekommenderade bandbredden i Mbit/s som du bör allokera om rpo-MÅLET 100 procent av tiden. Den här mängden bandbredd måste vara reserverad för stabil deltareplikering av samtliga kompatibla virtuella datorer om du helt ska undvika överträdelser av RPO-målet.

**Att uppfylla RPO 90% av tiden**: Kanske på grund av bandbreddspriserna eller en annan orsak kan du ange den bandbredd som krävs för att uppnå rpo-MÅLET 100 procent av tiden. I det här fallet kan du välja att tilldela en lägre bandbredd som gör att du uppnår önskat RPO 90 % av gångerna. I rapporten ges även en ”tänk om”-analys av hur många RPO-överträdelser du kan förvänta dig och deras varaktighet, så att du bättre ska förstå vad som kan hända om du tilldelar den här lägre bandbredden.

**Achieved Throughput**: Dataflödet från servern där du kör kommandot GetThroughput till Azure-region där lagringskontot finns. Dataflödesvärdet är en uppskattning av den nivå du kan uppnå när du skyddar de kompatibla virtuella datorerna med Site Recovery. Hyper-V-serverlagring och nätverksegenskaperna måste vara samma som på den server du kör verktyget från.

För alla företagsdistributioner av Site Recovery bör du använda [ExpressRoute](https://aka.ms/expressroute).

### <a name="required-storage-accounts"></a>Nödvändiga lagringskonton
I följande diagram visas hur många lagringskonton (standard och premium) som behövs för att skydda alla kompatibla virtuella datorer. Om du vill veta vilket lagringskonto som ska användas för varje virtuell dator kan du läsa avsnittet ”Placering av VM-lagring”.

![Nödvändiga Azure Storage-konton](media/hyper-v-deployment-planner-analyze-report/required-storage-accounts-h2a.png)

### <a name="required-number-of-azure-cores"></a>Nödvändigt antal Azure-kärnor
Resultatet är det totala antalet kärnor som ska konfigureras före redundansväxling eller redundanstest av alla kompatibla virtuella datorer. Om det inte finns tillräckligt många kärnor tillgängliga i prenumerationen kan inte Site Recovery skapa virtuella datorer vid redundanstestet eller redundansväxlingen.

![Nödvändigt antal Azure-kärnor](media/hyper-v-deployment-planner-analyze-report/required-number-of-azure-cores-h2a.png)


### <a name="additional-on-premises-storage-requirement"></a>Ytterligare krav för lokal lagring

Det totala lediga lagringsutrymmet som krävs för Hyper-V-servrar för en lyckad initial replikering och deltareplikering för att garantera att VM-replikeringen inte orsakar några oönskade driftstopp för dina produktionsprogram. Mer information om varje volymkrav finns i [krav för lokal lagring](#on-premises-storage-requirement). 

Läs mer om varför det krävs ledigt utrymme för replikeringen i avsnittet [Krav för lokal lagring](#why-do-i-need-free-space-on-the-hyper-v-server-for-the-replication).

![Krav för lokal lagring och kopieringsfrekvens](media/hyper-v-deployment-planner-analyze-report/on-premises-storage-and-copy-frequency-h2a.png)

### <a name="maximum-copy-frequency"></a>Maximal kopieringsfrekvens
Den rekommenderade maximala kopieringsfrekvensen måste anges för replikeringen för att önskad RPO ska uppnås. Standardvärdet är 5 minuter. Du kan ställa in kopieringsfrekvensen till 30 sekunder för att få bättre RPO (mål för återställningspunkt).

### <a name="what-if-analysis"></a>Konsekvensanalys
![Konsekvensanalys](media/hyper-v-deployment-planner-analyze-report/what-if-analysis-h2a.png) I den här analysen beskrivs hur många överträdelser som kan inträffa under profileringsperioden när du tilldelar en lägre bandbredd för att önskat RPO-mål ska uppfyllas bara 90 % av gångerna. En eller flera överträdelser av återställningspunktmålen kan inträffa på en viss dag. Grafen visar toppåterställningspunktmålet för dagen. Utifrån den här analysen kan du avgöra om du kan godta antalet RPO-överträdelser och dagens största RPO-överträdelse sett till den lägre bandbredden. Du kan allokera den lägre bandbredden för replikering, om det är godtagbart. Om det inte är godtagbart, måste du allokera högre bandbredd enligt förslaget för att uppfylla önskad RPO 100 % av tiden. 

### <a name="recommendation-for-successful-initial-replication"></a>Rekommendation för lyckad initial replikering
I det här avsnittet diskuterar vi antalet batchar i vilka de virtuella datorerna ska skyddas och den minsta bandbredd som krävs för att slutföra den initiala replikeringen (IR). 

![IR-batchbearbetning](media/hyper-v-deployment-planner-analyze-report/ir-batching-h2a.png)

Virtuella datorer måste skyddas i angiven batchordning. Varje batch har en specifik lista över virtuella datorer. Virtuella datorer i batch 1 måste skyddas före batch 2. Virtuella datorer i batch 2 måste skyddas före batch 3 och så vidare. När den inledande replikeringen av de virtuella datorerna i batch 1 är klar kan du aktivera replikering för virtuella datorer i batch 2. På samma sätt kan du när den inledande replikeringen av de virtuella datorerna i batch 2 är klar aktivera replikering för virtuella datorer i batch 3, och så vidare. 

Om batchordningen inte följs kanske det inte finns tillräckligt med bandbredd för inledande replikering för virtuella datorer som ska skyddas senare. Det leder antingen till att de virtuella datorerna aldrig slutför den inledande replikeringen eller till att några få skyddade virtuella datorer försätts i läget för omsynkronisering. IR-batchbearbetning för det valda RPO-arket innehåller detaljerad information om vilka virtuella datorer som ska ingå i varje batch.

Diagrammet här visar bandbreddsfördelningen för inledande replikering och deltareplikering i batchar i den angivna batchordningen. När du skyddar virtuella datorer i den första batchen är fullständig bandbredd tillgänglig för inledande replikering. När den inledande replikeringen är slutförd för den första batchen krävs en del av bandbredden för deltareplikering. Återstående bandbredd kommer att vara tillgänglig för inledande replikering för virtuella datorer i den andra batchen. 

Fältet för batch 2 visar den bandbredd som krävs för deltareplikering för virtuella datorer i batch 1 och den tillgängliga bandbredden för inledande replikering av virtuella datorer i batch 2. På samma sätt visar fältet för batch 3 den bandbredd som krävs för deltareplikering av föregående batchar (virtuella datorer i batch 1 och 2) och tillgänglig bandbredd för inledande replikering för batch 3 och så vidare. När inledande replikering av alla batchar är klar visar det senaste fältet bandbredden som krävs för deltareplikering för alla skyddade virtuella datorer.

**Varför behöver jag inledande batchbearbetning av replikering?**
Tidsåtgången för den inledande replikeringen med avseende på den virtuella datorns diskstorlek, förbrukat diskutrymme och tillgängligt dataflöde i nätverket. Informationen finns i IR-batchbearbetning för ett valt RPO-ark.

### <a name="cost-estimation"></a>Kostnadsuppskattning
I diagrammet visas en sammanfattning av den uppskattade totala kostnaden för haveriberedskap (DR) till Azure för din valda målregion och i den valuta du har angett för rapporten.

![Sammanfattning av kostnadsuppskattning](media/hyper-v-deployment-planner-analyze-report/cost-estimation-summary-h2a.png)

Sammanfattningen hjälper dig att förstå den kostnad du behöver betala för lagring, beräkning, nätverk och licenser när du skyddar alla dina kompatibla virtuella datorer till Azure med Site Recovery. Kostnaden beräknas för kompatibla virtuella datorer och inte för alla profilerade virtuella datorer. 
 
Du kan visa kostnaden per månad eller per år. Läs mer om [målregioner som stöds](./hyper-v-deployment-planner-cost-estimation.md#supported-target-regions) och [valutor som stöds](./hyper-v-deployment-planner-cost-estimation.md#supported-currencies).

**Komponentkostnader**: Den totala DR-kostnaden delas upp i fyra komponenter: beräkning, lagring, nätverk och Site Recovery-licenskostnad. Kostnaden beräknas baserat på förbrukningen som uppstår vid replikering och DR-testtiden. Beräkning, lagring (premium och standard), ExpressRoute/VPN som konfigurerats mellan lokal plats och Azure samt Site Recovery-licenser används för beräkningar.

**Kostnad per tillstånd**: Den totala kostnaden för haveriberedskap kategoriseras utifrån två olika tillstånd: replikering och DR-test. 

**Replikeringskostnad**: Kostnaden som tillkommer under replikering. Det här täcker kostnaden för lagring, nätverk och Site Recovery-licensen. 

**DR-test**: Kostnaden som tillkommer under redundansväxlingstest. Site Recovery startar virtuella datorer under redundansväxlingstest. DR-testkostnaden täcker kostnaden för beräkning och lagring för de virtuella datorer som körs. 

**Azure Storage-kostnad per månad/år**: Stapeldiagrammet visar den totala lagringskostnad som tillkommer för premium- och standardlagring för replikering och DR-test. Du kan visa en detaljerad kostnadsanalys per VM på arket [Cost Estimation](hyper-v-deployment-planner-cost-estimation.md) (Kostnadsuppskattning).

### <a name="growth-factor-and-percentile-values-used"></a>Tillväxtfaktor och percentilvärde som används
I det här avsnittet, längst ned på bladet, visas vilket percentilvärde som använts för prestandaräknarna för de profilerade virtuella datorerna (standardvärdet är den 95:e percentilen). Det visar även tillväxtfaktorn (standardvärdet är 30 %) som används i alla beräkningar.

![Tillväxtfaktor och percentilvärde som används](media/hyper-v-deployment-planner-run/growth-factor-max-percentile-value.png)

## <a name="recommendations-with-available-bandwidth-as-input"></a>Rekommendationer med tillgänglig bandbredd som indata
![Profileringsöversikt med bandbreddsindata](media/hyper-v-deployment-planner-analyze-report/profiling-overview-bandwidth-input-h2a.png)

Du kan ha en situation där du vet att du inte kan ange en bandbredd på mer än x Mbit/s för Site Recovery-replikering. Du kan använda verktyget för att ange tillgänglig bandbredd (med parametern -Bandbredd när du genererar rapporten) och få det värde för RPO-målet i minuter du kan uppnå. Utifrån det här möjliga RPO-värdet kan du avgöra om du måste distribuera ytterligare bandbredd eller om du nöjer dig med en lösning för haveriberedskap med det aktuella RPO-värdet.

![Möjligt återställningspunktmål (RPO)](media/hyper-v-deployment-planner-analyze-report/achivable-rpo-h2a.PNG)

## <a name="vm-storage-placement-recommendation"></a>Rekommendation för placering av VM-lagring 
![Placering av VM-lagring](media/hyper-v-deployment-planner-analyze-report/vm-storage-placement-h2a.png)

**Storage-disktyp**: Antingen ett standard- eller premium storage-konto som används för att replikera alla av motsvarande virtuella datorer i den **virtuella datorer på plats som är** kolumn.

**Suggested Prefix**: Det föreslagna prefixet på tre tecken som kan användas för att namnge lagringskontot. Du kan använda ditt eget prefix, men verktygets förslag följer [namngivningskonventionen för partitioner av lagringskonton](https://aka.ms/storage-performance-checklist).

**Föreslaget kontonamn**: Lagringskontots namn när du inkluderar det föreslagna prefixet. Ersätt namnet inom hakparenteser (< och >) med egna indata.

**Log Storage Account**: Alla replikeringsloggar lagras i ett standardlagringskonto. För virtuella datorer som replikerar till ett Premium Storage-konto konfigurerar du ytterligare ett Standard Storage-konto för logglagringsutrymme. Flera lagringskonton för premiumreplikering kan använda samma standardkonto för logglagring. Virtuella datorer som replikeras till lagringskonton av standardtyp använder samma lagringskonto för loggarna.

**Suggested Log Account Name**: Loggen namnet på ditt lagringskonto när du inkluderar det föreslagna prefixet. Ersätt namnet inom hakparenteser (< och >) med egna indata.

**Placement Summary**: En sammanfattning av den totala virtuella belastningen på lagringskontot vid tidpunkten för replikering och vid redundanstest / redundansväxling. Sammanfattningen innehåller:

* Totalt antal virtuella datorer som är mappade till lagringskontot. 
* Totalt antal läs- och skrivåtgärder (IOPS) för de virtuella datorer som placeras på lagringskontot.
* Totalt antal skrivåtgärder (replikering) IOPS.
* Total installationsstorlek över alla diskar.
* Totalt antal diskar.

**Virtuella datorer på plats som är**: En lista över alla virtuella datorer som ska placeras på det angivna lagringskontot för optimala prestanda och användning.

## <a name="compatible-vms"></a>Compatible VMs (Kompatibla virtuella datorer)
Excel-rapporten som genereras av distributionshanteraren för Site Recovery innehåller information om alla kompatibla virtuella datorer på bladet ”Compatible VMs” (Kompatibla virtuella datorer).

![Compatible VMs (Kompatibla virtuella datorer)](media/hyper-v-deployment-planner-analyze-report/compatible-vms-h2a.png)

**Namn på virtuell dator**: VM-namnet som används i VMListFile när en rapport skapas. I den här kolumnen visas även de diskar (VHD:er) som är kopplade till de virtuella datorerna. Namnen inkluderar de Hyper-V-värdnamn där de virtuella datorerna placerades när verktyget upptäckte de under profileringsperioden.

**VM-kompatibilitet**: Värden är **Ja** och **Ja**\*. **Ja** \* för instanser där den virtuella datorn är en anpassning för [premium SSD](../virtual-machines/windows/disks-types.md). Här ryms den profilerade höga omsättningen eller IOPS-disken i en högre premiumdiskstorlek än storleken som är mappad till disken. Lagringskontot avgör vilken Premium Storage-disktyp som en disk ska mappas till, baserat på dess storlek: 
* < 128 GB är en P10.
* 128 GB till 256 GB är en P15.
* 256 till 512 GB är en P20.
* 512 till 1024 GB är en P30.
* 1025 till 2048 GB är en P40.
* 2049 till 4095 GB är en P50.

Om exempelvis arbetsbelastningsegenskaperna för en disk placerar den i kategorin P20 eller P30, men storleken mappar den till en lägre Premium Storage-disktyp, markerar verktyget den här virtuella datorn som **Ja**\*. Verktyget rekommenderar också att du antingen ändrar källdiskens storlek så att den passar den rekommenderade Premium Storage-disktypen eller ändrar måldisktypen efter redundansväxling.

**Lagringstyp**: Standard eller premium.

**Suggested Prefix**: Prefixet tre tecken för lagringskontot.

**Storage-konto**: Namnet som använder det föreslagna lagringskonto prefixet.

**Högsta R/W IOPS (med tillväxtfaktor)** : Den högsta IOPS-arbetsbelastningen läsning/skrivningen på disken (standardvärdet är 95: e percentilen) tillsammans med faktorn för framtida tillväxt (standardvärdet är 30 procent). Det totala antalet läs- och skrivåtgärder (IOPS) för en virtuell dator är inte alltid summan av den virtuella datorns individuella läs- och skrivåtgärder (IOPS). Den högsta läs/skriv-IOPS för den virtuella datorn är den högsta summan av de enskilda diskarnas läs/skriv-IOPS under varje minut av profileringsperioden.

**Högsta Dataomsättning i MB/s (med tillväxtfaktor)** : Högsta dataomsättningsfrekvensen på disken (standardvärdet är 95: e percentilen) tillsammans med faktorn för framtida tillväxt (standardvärdet är 30 procent). Den totala dataomsättningen för den virtuella datorn är inte alltid summan av den virtuella datorns individuella dataomsättning. Den högsta dataomsättningen för den virtuella datorn är den högsta summan av de enskilda diskarnas dataomsättning under varje minut av profileringsperioden.

**Azure VM-storlek**: Lämplig mappad storlek för Azure Cloud Services för den här lokala virtuella datorn. Mappningen baseras på det lokala virtuella datorminnet, antalet diskar/kärnor/nätverkskort och läs- och skrivåtgärder (IOPS). Rekommendationen är alltid den lägsta virtuella Azure-datorstorlek som matchar alla lokala virtuella datoregenskaper.

**Antalet diskar**: Det totala antalet virtuella datordiskar (VHD) på den virtuella datorn.

**Diskstorlek (GB)** : Den totala storleken för alla diskar på den virtuella datorn. Storleken för de enskilda diskarna i den virtuella datorn visas också i verktyget.

**Kärnor**: Antal processorkärnor på den virtuella datorn.

**Minne (MB)** : RAM-minne på den virtuella datorn.

**Nätverkskort**: Antalet nätverkskort på den virtuella datorn.

**Starttyp**: Starttyp för den virtuella datorn. Den kan vara BIOS eller EFI.

## <a name="incompatible-vms"></a>Incompatible VMs (Inkompatibla virtuella datorer)
Excel-rapporten som genereras av distributionshanteraren för Site Recovery innehåller information om alla inkompatibla virtuella datorer i bladet ”Incompatible VMs” (Inkompatibla virtuella datorer).

![Incompatible VMs (Inkompatibla virtuella datorer)](media/hyper-v-deployment-planner-analyze-report/incompatible-vms-h2a.png)

**Namn på virtuell dator**: VM-namnet som används i VMListFile när en rapport skapas. I den här kolumnen visas även de diskar (VHD:er) som är kopplade till de virtuella datorerna. Namnen inkluderar de Hyper-V-värdnamn där de virtuella datorerna placerades när verktyget upptäckte de under profileringsperioden.

**VM-kompatibilitet**: Anger varför den här virtuella datorn inte kan skyddas med Site Recovery. Anledningarna beskrivs för varje inkompatibel disk av den virtuella datorn och kan, baserat på publicerade [lagringsgränser](https://aka.ms/azure-storage-scalbility-performance), vara något av följande:

* Disken är större än 4095 GB. Azure Storage har för närvarande inte stöd för diskar som är större än 4095 GB.

* OS-disken är större än 2047 GB för virtuella datorer i generation 1 (BIOS-starttyp). Site Recovery stöder inte OS-diskstorlekar på över 2047 GB för virtuella datorer i generation 1.

* OS-disken är större än 300 GB för virtuella datorer i generation 2 (EFI-starttyp). Site Recovery stöder inte OS-diskstorlekar på över 300 GB för virtuella datorer i generation 2.

* Inget stöd för virtuella datorer med namn som innehåller något av följande tecken: “” [] `. Verktyget kan inte hämta profilerade data för virtuella datorer som har sådana tecken i sina namn. 

* VHD delas av minst två virtuella datorer. Azure stöder inte virtuella datorer med delad VHD.

* Virtuella datorer med Virtual Fiber Channel stöds inte. Site Recovery stöder inte virtuella datorer med Virtual Fiber Channel.

* Hyper-V-kluster innehåller inte en koordinatortjänst för replikering. Site Recovery stöder inte virtuella datorer i ett Hyper-V-kluster om koordinatortjänsten för Hyper-V inte är konfigurerad för klustret.

* Den virtuella datorn har inte hög tillgänglighet. Site Recovery stöder inte en virtuell dator om Hyper-V-klusternodens VHD är lagrade på den lokala disken istället för på klusterdisken. 

* Total storlek för den virtuella datorn (replikering + redundanstest) överskrider den gräns för den storlek på premiumlagringskonto som stöds (35 TB). Den här inkompatibiliteten uppstår vanligen när en enskild disk i den virtuella datorn har en prestandaegenskap som överskrider den maxgräns som stöds av Azure- eller Site Recovery-gränserna för standardlagring. Denna instans skickar den virtuella datorn till Premium Storage-zonen. Den maximala storleken som stöds för ett premiumlagringskonto är dock 35 TB. En enda skyddad virtuell dator kan inte skyddas över flera lagringskonton. 

    När ett redundanstest körs på en skyddad virtuell dator och om den ohanterade disken är konfigurerad för att köra redundanstest, körs det på samma lagringskonto där replikeringen körs. I den här instansen krävs ytterligare samma mängd lagringsutrymme som för replikeringen. Den garanterar att replikeringen ska gå vidare och att redundanstest ska lyckas parallellt. När hanterade diskar konfigureras för testredundans behövs inget ytterligare utrymme redovisas för VM-testredundans.

* Käll-IOPS överskrider IOPS-gränsen för lagring på 7500 per disk.

* Käll-IOPS överskrider IOPS-gränsen för lagring på 80 000 per virtuell dator.

* Den genomsnittliga dataomsättningen för virtuella källdatorer överskrider den dataomsättningsgräns som stöds av Site Recovery på 10 MB/s för den genomsnittliga I/O-storleken.

* Genomsnittligt antal effektiva skrivåtgärder (IOPS) för den virtuella källdatorn överskrider gränsen i Site Recovery på 840.

* Beräknat lagringsutrymme för ögonblicksbilder överskrider gränsen på 10 TB.

**Högsta R/W IOPS (med tillväxtfaktor)** : Den högsta IOPS-arbetsbelastningen på disken (standardvärdet är 95: e percentilen) tillsammans med faktorn för framtida tillväxt (standardvärdet är 30 procent). Det totala antalet läs- och skrivåtgärder (IOPS) för en virtuell dator är inte alltid summan av den virtuella datorns individuella läs- och skrivåtgärder (IOPS). Den högsta antalet läs-och skrivåtgärder (IOPS) för den virtuella datorn är den högsta summan av de enskilda diskarnas läs-och skrivåtgärder (IOPS) under varje minut av profileringsperioden.

**Högsta Dataomsättning (MB/s) (med tillväxtfaktor)** : Högsta dataomsättningsfrekvensen på disken (standardvärdet är 95: e percentilen) tillsammans med faktorn för framtida tillväxt (standardvärdet är 30 procent). Observera att den totala dataomsättningen för den virtuella datorn inte alltid är summan av den virtuella datorns individuella dataomsättning. Den högsta dataomsättningen för den virtuella datorn är den högsta summan av de enskilda diskarnas dataomsättning under varje minut av profileringsperioden.

**Antalet diskar**: Det totala antalet virtuella hårddiskar på den virtuella datorn.

**Diskstorlek (GB)** : Total installationsstorlek för alla diskar på den virtuella datorn. Storleken för de enskilda diskarna i den virtuella datorn visas också i verktyget.

**Kärnor**: Antal processorkärnor på den virtuella datorn.

**Minne (MB)** : Mängden RAM-minne på den virtuella datorn.

**Nätverkskort**: Antalet nätverkskort på den virtuella datorn.

**Starttyp**: Starttyp för den virtuella datorn. Den kan vara BIOS eller EFI.

## <a name="azure-site-recovery-limits"></a>Gränser för Azure Site Recovery
Följande tabell innehåller gränserna för Site Recovery. Dessa gränser är baserade på tester, men de täcker inte alla möjliga kombinationer av I/O i programmet. De faktiska resultaten kan variera beroende på blandningen av I/O i ditt program. För bästa resultat även efter distributionsplaneringen bör du köra omfattande programtester med redundanstest för att få en bild av verklig prestanda för programmet.
 
**Replication Storage Target** (Lagringsmål för replikering) | **Genomsnittlig I/O-storlek för virtuell källdator** |**Genomsnittlig dataomsättning för virtuell källdator** | **Total dataomsättning per dag för virtuell källdisk**
---|---|---|---
Standard Storage | 8 kB | 2 MB/s per virtuell dator | 168 GB per virtuell dator
Premium Storage | 8 kB  | 5 MB/s per virtuell dator | 421 GB per virtuell dator
Premium Storage | 16 kB eller mer| 20 MB/s per VM | 1684 GB per VM

Gränserna är genomsnittliga värden baserade på en I/O-överlappning på 30 procent. Site Recovery kan hantera högre dataflöden med annan överlappning, större skrivningsstorlek och verkligt I/O-beteende under arbetsbelastningen. Föregående antal antar en typisk eftersläpning på cirka fem minuter. Det vill säga, när data har överförts bearbetas de och en återställningspunkt skapas inom fem minuter.

## <a name="on-premises-storage-requirement"></a>Krav för lokal lagring

Kalkylbladet tillhandahåller kravet på totalt ledigt diskutrymme för varje volym på Hyper-V-servrarna (där VHD:er finns) för lyckad inledande replikering och deltareplikering. Innan du aktiverar replikering lägger du till det lagringsutrymme som krävs för volymerna för att se till att replikeringen inte orsakar några oönskade driftstopp för dina produktionsprogram. 

  Distributionshanteraren för Site Recovery identifierar kravet på optimalt lagringsutrymme baserat på VHD-storleken och nätverksbandbredden som används för replikeringen.

![Krav för lokal lagring](media/hyper-v-deployment-planner-analyze-report/on-premises-storage-requirement-h2a.png)

### <a name="why-do-i-need-free-space-on-the-hyper-v-server-for-the-replication"></a>Varför behöver jag ledigt utrymme på Hyper-V-servern för replikeringen?
* När du aktiverar replikering av en virtuell dator tar Site Recovery en ögonblicksbild av varje VHD för den virtuella datorn för inledande replikering. När den inledande replikeringen pågår skrivs nya ändringar till diskar i programmet. Site Recovery spårar dessa deltaändringar i loggfiler, som kräver ytterligare lagringsutrymme. Tills den inledande replikeringen är klar lagras loggfilerna lokalt. 

    Om det inte finns tillräckligt med utrymme för loggfilerna och ögonblicksbilden (AVHDX) hamnar replikeringen i läget för omsynkronisering och replikeringen slutförs aldrig. I värsta fall behöver du 100 % ytterligare ledigt utrymme av samma storlek som VHD:n för inledande replikering.
* När den inledande replikeringen är klar startar deltareplikeringen. Site Recovery spårar dessa deltaändringar i loggfilerna som lagras på volymen där den virtuella datorns virtuella hårddiskar (VHD) finns. Dessa loggfiler replikeras till Azure vid en konfigurerad koperingsfrekvens. Det kan ta lite tid att replikera loggfilerna till Azure, baserat på den tillgängliga nätverksbandbredden. 

    Om det inte finns tillräckligt med ledigt utrymme för att lagra loggfilerna pausas replikeringen. Sedan går replikeringsstatusen för den virtuella datorn till läget ”omsynkronisering krävs”.
* Om nätverksbandbredden inte är tillräcklig för att skicka loggfilerna till Azure staplas loggfilerna på volymen. När en loggfils storlek ökar till 50 % av VHD-storleken försätts replikeringen av den virtuella datorn i värsta fall i läget ”omsynkronisering krävs”. I värsta fall behöver du 50 % ytterligare ledigt utrymme av samma storlek som VHD:n för deltareplikering.

**Hyper-V-värd**: Listan över profilerade Hyper-V-servrar. Om en server är en del av ett Hyper-V-kluster grupperas alla klusternoder ihop.

**Volym (VHD-sökväg)** : Varje volym på en Hyper-V-värd där VHD: er/vhdx: er finns. 

**Ledigt utrymme (GB)** : Det lediga tillgängliga utrymmet på volymen.

**Totalt lagringsutrymme som krävs på volymen (GB)** : Det totala lediga utrymmet krävs på volymen för lyckad inledande replikering och deltareplikering. 

**Total mängd ytterligare lagring som ska etableras på volymen för lyckad replikering (GB)** : Den rekommenderar det totala ytterligare utrymmet som måste etableras på volymen för lyckad inledande replikering och deltareplikering.

## <a name="initial-replication-batching"></a>Inledande batchbearbetning av replikering 

### <a name="why-do-i-need-initial-replication-batching"></a>Varför behöver jag inledande batchbearbetning av replikering?
Om de virtuella datorerna skyddas på samma gång, blir kravet på ledigt utrymme mycket högre. Om det inte finns tillräckligt med lagringsutrymme övergår replikeringen av de virtuella datorerna till läget för omsynkronisering. Kravet på nätverksbandbredd skulle också bli mycket högre för att slutföra inledande replikering av alla virtuella datorer samtidigt. 

### <a name="initial-replication-batching-for-a-selected-rpo"></a>Inledande batchbearbetning av replikering för en vald RPO
Det här kalkylbladet innehåller en detaljerad vy för varje batch för inledande IR. För varje RPO skapas ett separat IR-batchbearbetningsark. 

När du har följt rekommendationerna för lokal lagring för varje volym finns den viktigaste informationen du behöver för att replikera i listan över virtuella datorer som kan skyddas parallellt. Dessa virtuella datorer är grupperade i en batch och det kan finnas flera batchar. Skydda virtuella datorer i den angivna batchordningen. Skydda först virtuella datorer i batch 1. När den inledande replikeringen är klar skyddar du virtuella datorer i batch 2 och så vidare. Du kan hämta listan över batchar och motsvarande virtuella datorer från det här bladet. 

![Information om IR-batchbearbetning](media/hyper-v-deployment-planner-analyze-report/ir-batching-for-rpo-h2a.png)

![Ytterligare information om IR-batchbearbetning](media/hyper-v-deployment-planner-analyze-report/ir-batching-for-rpo2-h2a.png)

### <a name="each-batch-provides-the-following-information"></a>Varje batch innehåller följande information 
**Hyper-V-värd**: Hyper-V-värd för den virtuella datorn som ska skyddas.

**Virtuell dator**: Den virtuella datorn som ska skyddas. 

**Kommentarer**: Om det krävs någon åtgärd för en specifik volym på en virtuell dator, anges kommentaren här. Om det exempelvis inte finns tillräckligt med ledigt utrymme på en volym visas kommentaren ”Add additional storage to protect this VM” (Lägg till ytterligare lagringsutrymme för att skydda den här virtuella datorn).

**Volym (VHD-sökväg)** : Volymens namn där den Virtuella datorns virtuella hårddiskar finns. 

**Ledigt utrymme på volymen (GB)** : Det ledigt diskutrymmet på volymen för den virtuella datorn. Vid beräkningen av ledigt utrymme på volymerna övervägs det använda diskutrymmet för deltareplikering av de virtuella datorerna för föregående batchar vars VHD:er finns på samma volym. 

Exempelvis kan VM1, VM2 och VM3 finnas på volymen E:\VHDpath. Ledigt utrymme på volymen är 500 GB före replikering. VM1 är en del av batch 1, VM2 är en del av batch 2 och VM3 är en del av batch3. För VM1 är det lediga tillgängliga utrymmet 500 GB. För VM2 blir det lediga tillgängliga utrymmet 500 – diskutrymmet som krävs för deltareplikering för VM1. Om vi säger att VM1 kräver 300 GB utrymme för deltareplikering blir det lediga tillgängliga utrymmet för VM2 500 GB – 300 GB = 200 GB. På samma sätt kräver VM2 300 GB för deltareplikering. Det lediga tillgängliga utrymmet för VM3 skulle bli 200 GB - 300 GB = -100 GB.

**Lagring som krävs på volymen för inledande replikering (GB)** : Det lediga utrymmet som krävs på volymen för den virtuella datorn för inledande replikering.

**Lagring som krävs på volymen för deltareplikering (GB)** : Det lediga utrymmet som krävs på volymen för den virtuella datorn för deltareplikering.

**Ytterligare lagring som krävs baserat på brist för att undvika replikeringsfel (GB)** : Ytterligare lagringsutrymme som krävs på volymen för den virtuella datorn. Det är maximum för den inledande replikeringens och deltareplikeringens utrymmeskrav minus det fria utrymmet på volymen.

**Minsta bandbredd som krävs för inledande replikering (Mbit/s)** : Den minsta bandbredd som krävs för inledande replikering för den virtuella datorn.

**Minsta bandbredd som krävs för deltareplikering (Mbit/s)** : Den minsta bandbredd som krävs för deltareplikering för den virtuella datorn.

### <a name="network-utilization-details-for-each-batch"></a>Information om nätverksanvändning för varje batch 
Varje batchtabell innehåller en sammanfattning av nätverksanvändningen för varje batch.

**Tillgänglig bandbredd för batch**: Den tillgängliga bandbredden för batchen efter att hänsyn tagits till den föregående batchens deltareplikeringsbandbredd.

**Uppskattad tillgänglig bandbredd för inledande replikering av batch**: Den tillgängliga bandbredden för inledande replikering av batchens virtuella datorer. 

**Ungefärlig förbrukad bandbredd för deltareplikering av batch**: Den bandbredd som krävs för deltareplikering av batchens virtuella datorer. 

**Uppskattad inledande replikeringstid för batch (hh: mm)** : Uppskattad inledande replikeringstid i timmar: minuter.



## <a name="next-steps"></a>Nästa steg
Läs mer om [kostnadsuppskattning](hyper-v-deployment-planner-cost-estimation.md).
