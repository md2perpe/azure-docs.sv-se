---
title: Felsöka Azure Stream Analytics med hjälp av diagnostikloggar
description: Den här artikeln beskriver hur du analyserar diagnostikloggar i Azure Stream Analytics.
services: stream-analytics
author: jseb225
ms.author: jeanb
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: a41c3f60d4b949f78c0755f97c9ef7e6302d78d8
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/22/2019
ms.locfileid: "67330005"
---
# <a name="troubleshoot-azure-stream-analytics-by-using-diagnostics-logs"></a>Felsöka Azure Stream Analytics med hjälp av diagnostikloggar

Ibland kan slutar Azure Stream Analytics-jobb oväntat bearbetning. Det är viktigt för att kunna felsöka den här typen av händelse. Fel kan inträffa på grund av ett oväntat frågeresultat, enhetsanslutningar eller ett oväntat tjänstavbrott. Diagnostikloggar i Stream Analytics kan du identifiera orsaken till problem när de inträffar och minska tiden för återställning.

Vi rekommenderar starkt att aktivera diagnostikloggar för alla produktionsjobb.

## <a name="log-types"></a>Loggtyper

Stream Analytics erbjuder två typer av loggar:

* [Aktivitetsloggar](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) (alltid på), som ger insikter om åtgärder som utförs på jobb.

* [Diagnostikloggar](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) (kan konfigureras), vilket ger bättre insyn i allt som händer med ett jobb. Diagnostik loggar start när jobbet skapas- och när jobbet har tagits bort. De täcker händelser när jobbet har uppdaterats och när den körs.

> [!NOTE]
> Du kan använda tjänster som Azure Storage, Azure Event Hubs och Azure Monitor loggar för att analysera avvikande data. Du debiteras enligt priserna för dessa tjänster.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="debugging-using-activity-logs"></a>Felsöka med hjälp av aktiviteten loggar

Aktivitetsloggar är aktiverade som standard och ge övergripande insikter om åtgärder som utförs av ditt Stream Analytics-jobb. Informationen i aktivitetsloggarna kan hjälpa dig att hitta orsaken till problem som påverkar ditt jobb. Gör följande om du vill använda aktivitetsloggar i Stream Analytics:

1. Logga in på Azure portal och väljer **aktivitetsloggen** under **översikt**.

   ![Stream Analytics aktivitetsloggen](./media/stream-analytics-job-diagnostic-logs/stream-analytics-menu.png)

2. Du kan se en lista över åtgärder som har utförts. Alla åtgärder som orsakade jobbet misslyckas har en röd info bubbla.

3. Klicka på en åtgärd för att se dess sammanfattningsvyn. Här är ofta begränsad. Om du vill veta mer om igen, klickar du på **JSON**.

   ![Stream Analytics-aktivitet log-åtgärdens sammanfattning](./media/stream-analytics-job-diagnostic-logs/operation-summary.png)

4. Rulla ned till den **egenskaper** avsnitt i JSON, vilket ger information om felet som orsakade den misslyckade åtgärden. I det här exemplet var felet på grund av ett körningsfel utanför bundna latitudvärden. Avvikelse i de data som bearbetas av ett Stream Analytics-jobb orsakar en datafel. Du kan lära dig om olika [inkommande och utgående datafel och varför de inträffar](https://docs.microsoft.com/azure/stream-analytics/data-errors).

   ![Information om JSON-fel](./media/stream-analytics-job-diagnostic-logs/error-details.png)

5. Du kan vidta åtgärder utifrån felmeddelandet i JSON. I det här exemplet kontrollerar latitudvärdet är mellan-90 grader och 90 grader måste läggas till i frågan.

6. Om ett felmeddelande i aktivitetsloggarna inte hjälpa dig att identifiera orsaken, aktivera diagnostikloggar och använda Azure Monitor-loggar.

## <a name="send-diagnostics-to-azure-monitor-logs"></a>Skicka diagnostik till Azure Monitor-loggar

Aktivera diagnostikloggar och skicka dem till Azure Monitor-loggar rekommenderas starkt. Diagnostikloggar är **av** som standard. Aktivera diagnostikloggar genom att utföra följande steg:

1.  Logga in på Azure Portal och gå till Stream Analytics-jobbet. Under **övervakning**väljer **diagnostikloggar**. Välj sedan **slå på diagnostik**.

    ![Bladnavigering till diagnostikloggar](./media/stream-analytics-job-diagnostic-logs/diagnostic-logs-monitoring.png)  

2.  Skapa en **namn** i **diagnostikinställningar** och markera kryssrutan bredvid **skicka till Log Analytics**. Lägg till en befintlig eller skapa en ny **Log analytics-arbetsytan**. Markera kryssrutorna för **körning** och **redigering** under **LOG**, och **AllMetrics** under **mått** . Klicka på **Spara**. Det rekommenderas att använda en Log Analytics-arbetsyta i samma Azure-region som ditt Stream Analytics-jobb för att förhindra ytterligare kostnader.

    ![Inställningarna för diagnostikloggar](./media/stream-analytics-job-diagnostic-logs/diagnostic-settings.png)

3. När ditt Stream Analytics-jobb startar dirigeras diagnostikloggar till Log Analytics-arbetsytan. Gå till Log Analytics-arbetsytan och välj **loggar** under den **Allmänt** avsnittet.

   ![Azure Monitor-loggar under avsnittet Allmänt](./media/stream-analytics-job-diagnostic-logs/log-analytics-logs.png)

4. Du kan [skriva en egen fråga](../azure-monitor/log-query/get-started-portal.md) om du vill söka efter termer, identifiera trender, analysera mönster och få insikter utifrån dina data. Exempel: du kan skriva en fråga att filtrera endast diagnostikloggar som har meddelandet ”det direktuppspelade jobbet misslyckades”. Diagnostikloggar från Azure Stream Analytics lagras i den **AzureDiagnostics** tabell.

   ![Diagnostik-fråga och resultat](./media/stream-analytics-job-diagnostic-logs/diagnostic-logs-query.png)

5. När du har en fråga som söker efter rätt loggarna kan du spara den genom att välja **spara** och ange ett namn och kategori. Du kan sedan skapa en avisering genom att välja **ny aviseringsregel**. Ange sedan aviseringstillståndet. Välj **villkor** och anger tröskelvärdet och frekvensen som den här anpassade loggsökning utvärderas.  

   ![Diagnostiklogg sökfråga](./media/stream-analytics-job-diagnostic-logs/search-query.png)

6. Välj åtgärdsgruppen och ange detaljerad information om aviseringen, som namn och beskrivning, innan du kan skapa varningsregeln. Du kan dirigera diagnostikloggar för olika jobb till samma Log Analytics-arbetsytan. På så sätt kan du ställa in aviseringar när det fungerar över alla jobb.  

## <a name="diagnostics-log-categories"></a>Diagnostiklogg för kategorier

Azure Stream Analytics samlar in två typer av diagnostikloggar:

* **Redigera**: Samlar in händelser som är relaterade till jobbet redigering åtgärder, till exempel jobbskapande, att lägga till och ta bort indata och utdata, att lägga till och uppdatera frågan, och startar eller stoppar jobbet.

* **Körning**: Samlar in händelser som inträffar under jobbkörningen.
    * Anslutningsfel
    * Bearbetning av fel, inklusive:
        * Händelser som inte överensstämmer med frågedefinitionen (Felmatchade fälttyper och värden, saknade fält och så vidare)
        * Fel för utvärdering av uttryck
    * Andra händelser och fel

## <a name="diagnostics-logs-schema"></a>Schema för Diagnostics-loggar

Alla loggar lagras i JSON-format. Varje post innehåller följande sträng fälten:

Namn | Beskrivning
------- | -------
time | Tidsstämpel (i UTC) i loggen.
resourceId | ID för den resurs som att åtgärden ägde rum, i versaler. Det omfattar prenumerations-ID, resursgruppen och namnet på jobb. Till exempel   **/SUBSCRIPTIONS/6503D296-DAC1-4449-9B03-609A1F4A1C87/RESOURCEGROUPS/MY-RESOURCE-GROUP/PROVIDERS/MICROSOFT. STREAMANALYTICS/STREAMINGJOBS/MYSTREAMINGJOB**.
category | Logga kategori, antingen **körning** eller **redigering**.
operationName | Namnet på åtgärden som loggas. Till exempel **skicka händelser: SQL-utdata skriva fel till mysqloutput**.
status | Status för åtgärden. Till exempel **misslyckades** eller **lyckades**.
nivå | Loggningsnivå. Till exempel **fel**, **varning**, eller **information**.
properties | Logga post-specifik information om serialiserad som en JSON-sträng. Mer information finns i följande avsnitt i den här artikeln.

### <a name="execution-log-properties-schema"></a>Schema för körning log-egenskaper

Loggarna för jobbkörning har information om händelser som inträffade under jobbkörningen för Stream Analytics. Schemat för egenskaper varierar beroende på om händelsen är ett datafel eller en allmän händelse.

### <a name="data-errors"></a>Datafel

Alla fel som uppstår medan jobbet bearbetar data är i den här kategorin loggar. De här loggarna skapas under data läses, serialisering, oftast och skrivåtgärder. Dessa loggar med inte anslutningsfel. Anslutningsfel behandlas som allmänna händelser. Du kan läsa mer om orsaken till olika olika [inkommande och utgående datafel](https://docs.microsoft.com/azure/stream-analytics/data-errors).

Namn | Beskrivning
------- | -------
Källa | Namnet på jobbet indata eller utdata där felet uppstod.
Meddelande | Meddelande som associerats med fel.
Typ | Typ av fel. Till exempel **DataConversionError**, **CsvParserError**, eller **ServiceBusPropertyColumnMissingError**.
Data | Innehåller data som är användbar korrekt hitta orsaken till felet. Omfattas av trunkering, beroende på storlek.

Beroende på den **operationName** värde, datafel har följande schema:

* **Serialisera händelser** ske under händelsen läsåtgärder. De inträffar när data på indata inte uppfyller fråga schemat för något av följande skäl:

   * *Typmatchningsfel när händelser (de) serialisera*: Identifierar det fält som orsakar felet.

   * *Det går inte att läsa en händelse, ogiltig serialisering*: Visar information om plats i indatan där felet uppstod. Innehåller blob-namnet för blob-indata, förskjutning och ett exempel på data.

* **Skicka händelser** uppstå under skrivåtgärder. De identifiera strömningshändelsen som orsakade felet.

### <a name="generic-events"></a>Allmänna händelser

Allmänna händelser täcker allt annat.

Namn | Beskrivning
-------- | --------
Fel | (valfritt) Information om fel. Detta är vanligtvis, undantagsinformation om den är tillgänglig.
Message| Loggmeddelande.
Typ | Typ av meddelande. Mappas till interna kategorisering av fel. Till exempel **JobValidationError** eller **BlobOutputAdapterInitializationFailure**.
Korrelations-ID | [GUID](https://en.wikipedia.org/wiki/Universally_unique_identifier) som unikt identifierar jobbkörningen. Alla körningsloggsposter från tidpunkten då jobbet startar tills jobbet stoppas har samma **Korrelations-ID** värde.

## <a name="next-steps"></a>Nästa steg

* [Introduktion till Stream Analytics](stream-analytics-introduction.md)
* [Kom igång med Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skala Stream Analytics-jobb](stream-analytics-scale-jobs.md)
* [Frågespråksreferens för Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Datafel för Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/data-errors)
