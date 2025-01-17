---
title: Azure Data Lake Storage Gen2 Storm prestandajustering riktlinjer | Microsoft Docs
description: Azure Data Lake Storage Gen2 Storm prestandajustering riktlinjer
services: storage
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: aa3c942448be6444044981eacc2bbc3214b9c1b4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64939395"
---
# <a name="performance-tuning-guidance-for-storm-on-hdinsight-and-azure-data-lake-storage-gen2"></a>Prestandajusteringsvägledning för Storm på HDInsight och Azure Data Lake Storage Gen2

Förstå de faktorer som ska beaktas när du finjustera prestanda för en Azure Storm-topologi. Till exempel är det viktigt att förstå egenskaperna för arbetet som utförs av kanaler och bultar (om arbetet är i/o eller minnesintensiva). Den här artikeln innehåller en mängd prestandajustering riktlinjer, inklusive felsöka vanliga problem.

## <a name="prerequisites"></a>Nödvändiga komponenter

* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Ett konto med Azure Data Lake Storage Gen2**. Anvisningar för hur du skapar ett finns i [snabbstarten: Skapa en lagrings-konto för analytiska](data-lake-storage-quickstart-create-account.md).
* **Azure HDInsight-kluster** med åtkomst till ett Data Lake Storage Gen2-konto. Se [Använda Azure Data Lake Storage Gen2 med Azure HDInsight-kluster](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2). Kontrollera att du aktivera Fjärrskrivbord för klustret.
* **Köra ett Storm-kluster på Data Lake Storage Gen2**. Mer information finns i [Storm på HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-storm-overview).
* **Riktlinjer för Data Lake Storage Gen2 för prestandajustering**.  Allmänna prestanda begrepp, se [Data Lake Storage Gen2 justering Prestandavägledning](data-lake-storage-performance-tuning-guidance.md).   

## <a name="tune-the-parallelism-of-the-topology"></a>Finjustera topologins parallellitet

Du kanske kan förbättra prestanda genom att öka samtidighet för läsning/skrivning till och från Data Lake Storage Gen2. En Storm-topologi har en uppsättning av konfigurationer som bestämmer parallellitet:
* Antal arbetsprocesser (”arbetarna” är jämnt fördelade på de virtuella datorerna).
* Antalet spout executor instanser.
* Antalet bult executor instanser.
* Antal spout uppgifter.
* Antal bult uppgifter.

Till exempel på ett kluster med 4 virtuella datorer och 4 arbetsprocesser, 32 spout executors och 32 spout aktiviteter och 256 bult executors och 512 bult uppgifter, Tänk på följande:

Varje övervakaren, vilket är en underordnad nod har en enskild worker Java virtual machine (JVM)-processen. Den här JVM-processen hanterar 4 spout-trådar och 64 bult trådar. Inom varje tråd körs uppgifter sekventiellt. Varje kanal tråd har 1 aktivitet med ovanstående konfiguration, och varje bult tråd har 2 aktiviteter.

Här är de olika komponenterna som ingår i Storm, och hur de påverkar graden av parallellitet som du har:
* Klustrets huvudnod (kallas Nimbus i Storm) används för att skicka och hantera jobb. Dessa noder har ingen inverkan på graden av parallellitet.
* Överordnade noder. HDInsight motsvarar en arbetsnod virtuell Azure-dator.
* Uppgifter för informationsanställda är Storm processer som körs på virtuella datorer. Varje worker-aktivitet motsvarar en JVM-instans. Storm distribuerar antal arbetsprocesser som du anger till arbetsnoderna så jämnt som möjligt.
* Kanalen och bultar executor instanser. Varje instans av executor motsvarar en tråd som körs i arbetare (JVMs).
* Storm-uppgifter. Detta är logiska aktiviteter som var och en av dessa trådar kör. Detta ändrar inte nivån av parallellitet, så att du ska utvärdera om du behöver flera aktiviteter per executor eller inte.

### <a name="get-the-best-performance-from-data-lake-storage-gen2"></a>Få bästa möjliga prestanda från Data Lake Storage Gen2

När du arbetar med Data Lake Storage Gen2 kan få du bästa möjliga prestanda om du gör följande:
* Slå samman ett litet lägger till i större storlekar.
* Göra så många samtidiga begäranden som du kan. Eftersom varje bult tråd gör blockerar läsningar, som du vill ha någonstans i intervallet för 8 – 12 trådar per kärna. Detta ser till att nätverkskortet och den CPU som också används. En större virtuell dator kan flera samtidiga begäranden.  

### <a name="example-topology"></a>Exempeltopologi

Vi antar att du har en 8 worker nodkluster med en D13v2 Azure virtuell dator. Den här virtuella datorn har 8 kärnor, så mellan 8 arbetsnoderna du ha 64 Totalt antal kärnor.

Anta att vi gör 8 bult trådar per kärna. Angivna 64 kärnor, det innebär att vi vill 512 totala bult executor instanser (d.v.s. trådar). I det här fallet vi antar att vi börja med en JVM per virtuell dator och använda främst tråd samtidighet i JVM för att uppnå samtidighet. Det innebär att vi behöver 8 worker-uppgifter (en per virtuell dator i Azure) och 512 bult executors. Med den här konfigurationen Storm försöker distribuera arbetare jämnt över arbetsnoder (kallas även överordnade noder), vilket ger varje arbetsnod 1 JVM. Nu inom Övervakare, försöker Storm distribuera executors jämnt mellan Övervakare, trådar vilket ger varje övervakaren (det vill säga JVM) 8 var och en.

## <a name="tune-additional-parameters"></a>Justera ytterligare parametrar
När du har grundläggande topologi kan överväga du om du vill ändra någon av parametrarna:
* **Antal JVMs per arbetsnod.** Om du har en stor datastruktur (till exempel en uppslagstabell) kräver du värden i minnet och varje JVM en separat kopia. Du kan också använda datastrukturen i många trådar om du har färre JVMs. För den bult i/o gör antalet JVMs inte så mycket som antalet trådar som har lagts till i dessa JVMs skillnad. För enkelhetens skull är det en bra idé att ha en JVM per person. Beroende på vad din bult gör eller vilka program som bearbetar du Kräv, men du kan behöva ändra det här värdet.
* **Antal spout executors.** Eftersom föregående exempel använder bultar för att skriva till Data Lake Storage Gen2, gäller antal kanaler inte direkt till bulten prestanda. Beroende på mängden bearbetning eller i/o som händer i kanal, är det dock en bra idé att finjustera kanaler för bästa prestanda. Kontrollera att du har tillräckligt med kanaler för att kunna hålla bultarna upptagen. Utdata-priser för kanaler ska matcha dataflödet för bultarna. Den faktiska konfigurationen är beroende av kanal.
* **Antal uppgifter.** Varje bult körs som en tråd. Ytterligare uppgifter per bult tillhandahåller inte någon ytterligare samtidighet. Den enda gången som de är förmånen är om din processen att kanske uttrycka tuppeln tar en stor del av din bult körningstid. Det är en bra idé att gruppen många tupplar i en större lägga till innan du skickar en bekräftelse från bulten. Så i de flesta fall ger inga ytterligare fördelar i flera aktiviteter.
* **Lokal eller shuffle gruppering.** När den här inställningen är aktiverad, skickas tupplar till bultar i samma arbetsprocessen. Detta minskar kommunikation mellan processer anrop för kommunikation och nätverk. Detta rekommenderas för de flesta topologier.

Det här grundläggande scenariot är en bra utgångspunkt. Testa med dina egna data att justera parametrarna föregående för att uppnå optimala prestanda.

## <a name="tune-the-spout"></a>Finjustera kanal

Du kan ändra följande inställningar för att justera kanal.

- **Tuppel timeout: topology.message.timeout.secs**. Den här inställningen avgör hur lång tid ett meddelande tar att slutföra och få godkännande, innan den anses misslyckades.

- **Maximalt minne per arbetsprocessen: worker.childopts**. Den här inställningen kan du ange ytterligare kommandoradsparametrar som Java-arbetare. Den vanligaste inställningen här är XmX som anger maximalt minne som allokerats till en JVM heap.

- **Max spout väntande: topology.max.spout.pending**. Den här inställningen anger antalet tupplar i kan vara flygning (har ännu inte bekräftas på alla noder i topologin) per kanal tråd när som helst.

  En bra beräkning göra är att beräkna storleken på var och en av dina tupplar. Ta sedan reda på hur mycket minne en kanal trådar har. Den totala mängden minne som allokerats till en tråd, dividerat med det här värdet bör ge dig den övre gränsen för högsta spout väntar på parametern.

Standard Data Lake Storage Gen2 Storm bult har en storlek synkronisering principparametern (fileBufferSize) som kan användas för att finjustera den här parametern.

I I/O-intensiva topologier är det en bra idé att har varje bult tråd till en egen fil och för att ange en princip för filen rotation (fileRotationSize). När filen når en viss storlek, dataströmmen rensas automatiskt och en ny fil skrivs till. Den rekommendera filstorleken för rotation är 1 GB.

## <a name="monitor-your-topology-in-storm"></a>Övervaka topologin i Storm  
När topologin körs, kan du övervaka den i användargränssnittet för Storm. Här är de viktigaste parametrarna och titta på:

* **Svarstid för körning av totalt antal processer.** Det här är den genomsnittliga tid med en tuppel tar som ska skickas från kanal, bearbetas av bulten och bekräftas.

* **Totalt antal bult processen svarstid.** Det här är den genomsnittliga tid som krävs tuppeln i bulten förrän den tagit emot en bekräftelse.

* **Totalt antal bult körningssvarstid.** Det här är den Genomsnittlig tid per bult i execute-metoden.

* **Antal fel.** Detta avser antalet tupplar som inte kunde bearbetas fullständigt innan de uppnåddes.

* **Kapacitet.** Det här är ett mått på hur upptagen systemet är. Det här värdet är 1, arbetar så fort de kan din bultar. Om det är mindre än 1, öka parallellitet. Om den är större än 1 kan du minska parallellitet.

## <a name="troubleshoot-common-problems"></a>Felsöka vanliga problem
Här följer några vanliga scenarier för felsökning.
* **Tidsgränsen nåddes för många tupplar.** Titta på varje nod i topologin att avgöra var flaskhalsen är. Den vanligaste orsaken till detta är att bultarna inte är kan hålla jämna steg med kanaler. Detta leder till tupplar belastning interna buffertar väntar på att bearbetas. Överväg att öka timeout-värdet eller minska max spout väntande.

* **Det finns en svarstid för körning av hög Totalt antal processer, men ett låga bult bearbetningssvarstid.** I det här fallet kan det är möjligt att tuppeln inte är som lyckad tillräckligt snabbt. Kontrollera att det finns ett tillräckligt antal acknowledgers. En annan möjlighet är att de väntar i kö för länge innan bultarna börjar bearbeta dem. Minska maximal spout väntande.

* **Det finns en hög bult körningssvarstid.** Det innebär att metoden execute() i din bult tar för lång. Optimera koden, eller titta på skrivningsstorlek och tömma beteende.

### <a name="data-lake-storage-gen2-throttling"></a>Data Lake Storage Gen2 begränsning
Om du stöter på gränserna för bandbredd som tillhandahålls av Data Lake Storage Gen2 kan du se Aktivitetsfel. Loggarna för en aktivitet för begränsningsfel. Du kan minska parallellitet genom att öka behållarens storlek.    

Du kan kontrollera om du hämtar begränsas genom att aktivera felsökningsloggning på klientsidan:

1. I **Ambari** > **Storm** > **Config** > **avancerade storm-worker-log4j**, ändra **&lt;rot nivå = ”info”&gt;** till  **&lt;rot nivå = ”felsöka”&gt;** . Starta om alla noder/tjänsten för att konfigurationen ska börja gälla.
2. Övervaka Storm-topologi som loggar in arbetsnoder (under /var/log/storm/worker-artifacts /&lt;TopologyName&gt;/&lt;port&gt;/worker.log) för Data Lake Storage Gen2 begränsning av undantag.

## <a name="next-steps"></a>Nästa steg
Ytterligare prestandajustering för Storm kan refereras i [den här bloggen](https://blogs.msdn.microsoft.com/shanyu/2015/05/14/performance-tuning-for-hdinsight-storm-and-microsoft-azure-eventhubs/).

Ett ytterligare exempel att köra finns i [aktiviteten på GitHub](https://github.com/hdinsight/storm-performance-automation).
