---
title: Loggaviseringar i Azure Monitor
description: 'Utlösa e-postmeddelanden, meddelanden, anropa webbplatser URL: er (webhooks) eller automation när angivna analytisk fråga villkor är uppfyllda för Azure-aviseringar.'
author: msvijayn
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 5/31/2019
ms.author: vinagara
ms.subservice: alerts
ms.openlocfilehash: ae35c735cffeb8cd85af1f32bb2d14ede6dc6b69
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66427412"
---
# <a name="log-alerts-in-azure-monitor"></a>Loggaviseringar i Azure Monitor

Den här artikeln innehåller information om aviseringar är en av typerna av aviseringar som stöds i den [Azure Alerts](../../azure-monitor/platform/alerts-overview.md) och Tillåt användare att använda Azures analysplattform som bas för aviseringar.

Log aviseringen består av Loggsökning regler som har skapats för [Azure Monitor-loggar](../../azure-monitor/learn/tutorial-viewdata.md) eller [Application Insights](../../azure-monitor/app/cloudservices.md#view-azure-diagnostics-events). Läs mer om användningen i [skapar loggaviseringar i Azure](../../azure-monitor/platform/alerts-log.md)

> [!NOTE]
> Populära loggdata från [Azure Monitor-loggar](../../azure-monitor/learn/tutorial-viewdata.md) finns nu även på plattformen mått i Azure Monitor. För information om vy [mått aviseringar för loggar](../../azure-monitor/platform/alerts-metric-logs.md)


## <a name="log-search-alert-rule---definition-and-types"></a>Sök loggvarningsregel - definitions- och typer

Loggsökningsregler skapas av Azure-aviseringar för att automatiskt köra angivna loggfrågor med jämna mellanrum.  Om resultatet av loggfrågan matchar särskilda villkor skapas en aviseringspost. Regeln kan sedan automatiskt köra en eller flera åtgärder med hjälp av [Åtgärdsgrupper](../../azure-monitor/platform/action-groups.md). [Azure Monitoring deltagare](../../azure-monitor/platform/roles-permissions-security.md) roll för att skapa, ändra och uppdatera loggvarningar kan krävas; tillsammans med åtkomst- och fråga körningsbehörigheter för analytics-mål i varningsregeln eller aviseringsfråga. Om användaren skapar inte har åtkomst till alla analytics mål i varningsregeln eller aviseringsfråga - regeln skapades kan misslyckas eller loggvarningsregel körs med ofullständiga resultat.

Regler för log search definieras av följande information:

- **Loggar frågor**.  Den fråga som körs varje gång regeln utlöses.  Poster som returneras av den här frågan används för att avgöra om en avisering utlöses. Analytics-fråga kan vara för en specifik Log Analytics-arbetsyta eller Application Insights-app och även omfatta flera [flera Log Analytics och Application Insights-resurser](../../azure-monitor/log-query/cross-workspace-query.md#querying-across-log-analytics-workspaces-and-from-application-insights) förutsatt att användaren har åtkomst samt fråga rättigheter till alla resurser. 
    > [!IMPORTANT]
    > [fråga mellan resurser](../../azure-monitor/log-query/cross-workspace-query.md#querying-across-log-analytics-workspaces-and-from-application-insights) stöd i loggaviseringar för Application Insights och log aviseringar för [Log Analytics som konfigurerats med scheduledQueryRules API](../../azure-monitor/platform/alerts-log-api-switch.md) endast.

    Vissa analytiska kommandon och kombinationer är inkompatibelt med användning i loggvarningar; för mer information se [logga aviseringsfrågor i Azure Monitor](../../azure-monitor/platform/alerts-log-query.md).

- **Tidsperiod**.  Anger tidsintervallet för frågan. Frågan returnerar bara de poster som har skapats i det här intervallet för den aktuella tiden. Tidsperiod begränsar de data som hämtats för loggfråga att förhindra missbruk och kringgår alla tid-kommandon (t.ex. sedan) används i loggen frågan. <br>*Till exempel om hur lång tid har angetts till 60 minuter och frågan körs klockan 13:15, returneras endast de poster som har skapats mellan 12:15 PM och 1:15 log frågan. Om log frågan använder tid kommandot som sedan nu 7d, log frågan skulle köras endast för data mellan 12:15 PM och 1:15 – som om det finns data för endast de senaste 60 minuterna. Och inte för sju dagarnas data som anges i loggfråga.*

- **Frekvens**.  Anger hur ofta frågan ska köras. Kan vara ett värde mellan 5 minuter och 24 timmar. Ska vara lika med eller mindre än tidsperioden.  Om värdet är större än hur lång tid, riskerar du poster som saknas.<br>*Anta exempelvis att en tidsperiod på 30 minuter och en frekvens på 60 minuter.  Om frågan körs i 1:00, returnerar poster mellan 12:30 och 1:00.  Nästa gång frågan körs är 2:00 när den skulle returnera poster mellan 1:30 och 2:00.  Alla poster som skapats mellan 01:00 och 1:30 skulle aldrig ska utvärderas.*

- **Tröskelvärde för**.  Resultatet av loggsökningen utvärderas för att avgöra om en avisering ska skapas.  Tröskelvärdet är olika för olika typer av Varningsregler i log search.

Log search regler oavsett om det för [Azure Monitor-loggar](../../azure-monitor/learn/tutorial-viewdata.md) eller [Application Insights](../../azure-monitor/app/cloudservices.md#view-azure-diagnostics-events), kan vara av två typer. De olika typerna beskrivs i detalj i avsnitten som följer.

- **[Antal resultat](#number-of-results-alert-rules)** . Avisering skapas när antalet poster som returneras av loggsökningen överskrider ett angivet tal.
- **[Metrisk måttenhet](#metric-measurement-alert-rules)** .  Varning skapad för varje objekt i resultatet av loggsökningen med värden som överskrider angivet tröskelvärde.

Skillnaderna mellan varningsregel typerna är som följer.

- *Antal resultat* Varningsregler skapar alltid en enda avisering, stund *metriska måttenheter* varningsregel skapar en avisering för varje objekt som är över tröskeln.
- *Antal resultat* Varningsregler skapar en avisering när tröskelvärdet överskrids en gång. *Metrisk måttenhet* Varningsregler kan skapa en avisering när tröskelvärdet överskrids ett visst antal gånger under ett visst tidsintervall.

### <a name="number-of-results-alert-rules"></a>Antal resultat Varningsregler

**Antal resultat** Varningsregler skapa en avisering när antalet poster som returneras av sökfrågan överskrider det angivna tröskelvärdet. Den här typen av varningsregel är perfekt för att arbeta med händelser, t.ex Windows-händelseloggar, Syslog, WebApp svar och anpassade loggar.  Du kanske vill skapa en avisering när en viss felhändelse skapas eller när flera felhändelser skapas inom en viss tidsperiod.

**Tröskelvärde**: Tröskelvärdet för ett antal resultat Varningsregler är större än eller mindre än ett visst värde.  Om antalet poster som returneras av loggsökningen matchar det här villkoret, skapas en avisering.

För att skicka en enskild händelse, ange hur många resultat till större än 0 och Sök efter förekomsten av en enskild händelse som har skapats sedan den senaste gången frågan kördes. Vissa program kan logga in med ett enstaka fel som inte nödvändigtvis genererar en avisering.  Programmet kan till exempel försök processen som skapade felhändelsen och lyckas nästa gång.  I det här fallet kan du inte vill skapa aviseringen om flera händelser skapas inom en viss tidsperiod.  

I vissa fall kan du skapa en avisering om en händelse.  En process kan exempelvis logga regelbundna händelser som indikerar att den fungerar korrekt.  Om det inte logga en av dessa händelser inom en viss tidsperiod, ska en avisering skapas.  I det här fallet kan du ange tröskelvärde för **mindre än 1**.

#### <a name="example-of-number-of-records-type-log-alert"></a>Exempel på antal poster typ log avisering

Tänk dig ett scenario där du vill veta när en webbaserad App får ett svar till användare med kod 500 (det vill säga) internt serverfel. Du skapar en aviseringsregel med följande uppgifter:  

- **Fråga:** begäranden | där Resultatkod == ”500”<br>
- **Tidsperiod:** 30 minuter<br>
- **Aviseringsfrekvens:** fem minuter<br>
- **Tröskelvärdet:** Större än 0<br>

Sedan skulle aviseringen köra frågan var femte minut, med 30 minuters data – att söka efter en post där Resultatkod var 500. Om med ett enda posten hittas, utlöses aviseringen och utlöser en åtgärd som har konfigurerats.

### <a name="metric-measurement-alert-rules"></a>Varningsregler för metriska måttenheter

**Metrisk måttenhet** Varningsregler skapar en avisering för varje objekt i en fråga med ett värde som överskrider ett angivet tröskelvärde och anges villkor för utlösare. Till skillnad från **antal resultat** aviseringsregler, **metriska måttenheter** Varningsregler fungerar när analytics resultatet innehåller en tidsserie. De har följande tydliga skillnader från **antal resultat** aviseringsregler.

- **Mängdfunktion**: Anger beräkningen som utförs och eventuellt ett numeriskt fält ska aggregeras.  Till exempel **antal()** returnerar antalet poster i frågan, **avg(CounterValue)** Returnerar medelvärdet för fältet CounterValue under period. Mängdfunktion i fråga måste vara med namnet/kallas: AggregatedValue och ange ett numeriskt värde. 

- **Gruppera fältet**: En post med ett aggregerat värde skapas för varje instans av det här fältet och en avisering genereras för varje.  Till exempel om du vill generera en avisering för varje dator använder du **per dator**. Om, det finns flera gruppfälten som anges i aviseringen fråga kan användaren kan ange vilka fält som används för att sortera sökresultat med den **sammanställda på** (metricColumn)-parameter

    > [!NOTE]
    > *Sammanställd på* (metricColumn) är tillgängligt för måttet mätning typ aviseringar för Application Insights och log aviseringar för [Log Analytics som konfigurerats med scheduledQueryRules API](../../azure-monitor/platform/alerts-log-api-switch.md) endast.

- **Intervall**:  Definierar det tidsintervall som aggregeras dina data.  Exempel: Om du har angett **fem minuter**, skapas en post för varje instans av gruppfältet samman med 5 minuters mellanrum under den tidsperiod som angetts för aviseringen.

    > [!NOTE]
    > Bin-funktionen måste användas i frågan för att ange intervall. Eftersom bin() kan leda till olika tidsintervall - omvandlas avisering automatiskt bin-kommando till bin_at kommandot med lämplig tidpunkt vid körning, för att säkerställa att resultaten med en fast tidpunkt. Metrisk måttenhet typen av avisering för log har utformats för att arbeta med frågor som har upp till tre instanser av bin() kommando
    
- **Tröskelvärde**: Tröskelvärdet för metriska måttenheter Varningsregler definieras av ett samlat värde och ett antal intrång.  Om en datapunkt i loggsökningen överskrider detta värde, den betraktas som ett intrång.  Om antalet överträdelser i för alla objekt i resultatet överskrider det angivna värdet, skapas en avisering för objektet.

Felaktig konfiguration av den *sammanställda på* eller *metricColumn* alternativet kan orsaka Varningsregler till detektering feltändning. Mer information finns i [felsökning när varningsregel för metriska måttenheter är felaktig](alert-log-troubleshoot.md#metric-measurement-alert-rule-is-incorrect).

#### <a name="example-of-metric-measurement-type-log-alert"></a>Exempel på mått mätning typ log avisering

Tänk dig ett scenario där du vill ha en avisering om en dator har överskridits processoranvändning 90% tre gånger under 30 minuter.  Du skapar en aviseringsregel med följande uppgifter:  

- **Fråga:** Perf | där ObjectName == ”Processor” och CounterName == ”% processortid” | Sammanfatta AggregatedValue = avg(CounterValue) efter bin (TimeGenerated, 5m), dator<br>
- **Tidsperiod:** 30 minuter<br>
- **Aviseringsfrekvens:** fem minuter<br>
- **Alert Logic - villkoret & tröskel:** Större än 90<br>
- **Gruppfältet (aggregering-on):** Computer
- **Utlös aviseringen baserat på:** Totalt antal dataintrång är större än 2<br>

Frågan skapar ett genomsnittligt värde för varje dator med 5 minuters mellanrum.  Den här frågan skulle köras var femte minut för data som samlas in under de föregående 30 minuterna. Eftersom gruppfältet (aggregering-on) valt är kolumner ”dator” - AggregatedValue delas för olika värden för ”dator” och genomsnittliga processoranvändningen för varje dator bestäms efter en tid lagerplats på 5 minuter.  Exemplet frågeresultat för (säga) tre datorer blir enligt nedan.


|TimeGenerated [UTC] |Computer  |AggregatedValue  |
|---------|---------|---------|
|20xx-xx-xxT01:00:00Z     |   srv01.contoso.com      |    72     |
|20xx-xx-xxT01:00:00Z     |   srv02.contoso.com      |    91     |
|20xx-xx-xxT01:00:00Z     |   srv03.contoso.com      |    83     |
|...     |   ...      |    ...     |
|20xx-xx-xxT01:30:00Z     |   srv01.contoso.com      |    88     |
|20xx-xx-xxT01:30:00Z     |   srv02.contoso.com      |    84     |
|20xx-xx-xxT01:30:00Z     |   srv03.contoso.com      |    92     |

Om det var frågeresultatet ska ritas, skulle den visas som.

![Exemplet frågeresultat](media/alerts-unified-log/metrics-measurement-sample-graph.png)

I det här exemplet vi kan se i lagerplatser av 5 minuter för var och en av de tre datorerna - genomsnittlig processoranvändning som beräknats för 5 minuter. Tröskelvärdet på 90 överträds av srv01 bara en gång på 1:25 bin. Däremot srv02 överskrider tröskelvärdet 90 vid 1:10, 1:15 och 1:25 lagerplatser; medan srv03 överskrider tröskelvärdet 90 vid 1:10, 1:15, 1:20 och 1:30.
Eftersom aviseringen är konfigurerad för att utlösaren baserat på totalt antal överträdelser är större än två, ser vi att srv02 och srv03 endast uppfyller kriterierna. Därför skapas separata aviseringar för srv02 och srv03 eftersom de brott mot tröskelvärdet 90% två gånger över flera tid lagerplatser.  Om den *Utlös aviseringen baserat på:* parametern konfigurerades i stället för *kontinuerlig överträdelser* alternativ, och sedan skulle att utlösa en avisering **endast** för srv03 eftersom den har brutit mot den tröskelvärdet för tre på varandra följande lagerplatser från 1:10 för 1:20. Och **inte** för srv02, eftersom den intrång tröskelvärdet för två på varandra följande lagerplatser från 1:10 för 1:15.

## <a name="log-search-alert-rule---firing-and-state"></a>Sök loggvarningsregel - den utlösts och tillstånd

Sök aviseringsregeln fungerar på logiken vinstmöjligheter av användaren enligt konfiguration och anpassade analytics-fråga som används. Sedan övervakningslogiken kapslas inklusive exakta villkoret eller orsak varför varningsregeln ska utlösas i en analytics-fråga – som kan skilja sig i varje loggvarningsregel. Azure-aviseringar har begränsade information för specifika underliggande Rotorsaksanalys (eller) scenariot som utvärderas när villkoret tröskelvärdet för log search varningsregel uppnåtts eller överskridits. Därför kallas loggvarningar som tillstånd utan. Och loggaviseringsregler ska behålla, så länge som aviseringstillståndet uppfylls av resultatet av anpassade analytics-fråga som tillhandahålls. Utan aviseringen var komma löst, som maskeras logiken för den exakta orsaken till övervakning av fel i analytics-frågan som anges av användaren. Det för närvarande finns ingen mekanism för Azure Monitor-aviseringar definitivt härleda grundorsaken som löst.

Låt oss se desamma, med ett praktiska exempel. Anta att vi har en loggvarningsregel som kallas *Contoso-Log-avisering*, enligt konfigurationen i den [exempel som angetts för många resultat typ log avisering](#example-of-number-of-records-type-log-alert) – där anpassade aviseringsfrågan är utformad för att leta efter 500 Resultatkod i loggarna.

- Klockan 13:05 när Contoso-Log-avisering utfördes av Azure-aviseringar, gav resultatet från noll poster med Resultatkod med 500. Eftersom noll är under tröskeln och aviseringen utlöses inte.
- I nästa iteration klockan 13:10 när Contoso-Log-avisering utfördes av Azure-aviseringar angetts resultatet som för fem posterna med Resultatkod 500. Eftersom fem överskrider tröskelvärdet och aviseringen utlöses med tillhörande åtgärder hämta utlöses.
- Resultatet visas två poster med 500 Resultatkod klockan 13:15 när Contoso-Log-avisering utfördes av Azure-aviseringar. Eftersom två överskrider tröskelvärdet och aviseringen utlöses med tillhörande åtgärder hämta utlöses.
- I nästa iteration klockan 13:20 när Contoso-Log-aviseringen har utförts genom Azure avisering får nu resultatet igen noll poster med 500 Resultatkod. Eftersom noll är under tröskeln och aviseringen utlöses inte.

Men ovan listade om klockan 13:15 - Azure-aviseringar kan inte fastställa att de underliggande problem som kan uppstå vid 1:10 kvarstår och om det finns net nya fel. När frågan som användaren kan hänsyn till tidigare poster – kan Azure-aviseringar vara säker på att. Eftersom logiken för aviseringen är inkapslade i aviseringsfrågan - så två poster med 500 Resultatkod sett klockan 13:15 kanske eller kanske inte redan visas klockan 13:10. Därför för att ta till en viss försiktighet när Contoso-Log-avisering körs klockan 13:15, utlöses konfigurerade åtgärden igen. Nu klockan 13:20 när inga poster visas med 500 Resultatkod - Azure-aviseringar inte får vara säker på att orsaken till 500 Resultatkod visas vid 1:10 PM och 1:15 löses nu och Azure Monitor-aviseringar tryggt kan härleda kan 500 fel problem inte utföras av samma skäl s igen. Contoso-Log-aviseringen ska därför inte ändras till löst i aviseringen om Azure-instrumentpanel och/eller meddelanden som skickas ut om upplösning av avisering. I stället den användare som förstår exakta villkoret eller orsaken till den logik som är inbäddad i analytics-fråga kan [markerar du aviseringen som stängts](alerts-managing-alert-states.md) efter behov.

## <a name="pricing-and-billing-of-log-alerts"></a>Priser och fakturering av aviseringar

Priser för loggvarningar anges på den [priser för Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/) sidan. I Azure-fakturor loggvarningar representeras som typ `microsoft.insights/scheduledqueryrules` med:

- Aviseringar i Application Insights visas med exakt aviseringsnamn tillsammans med resursgruppen och egenskaper för aviseringen
- Aviseringar i Log Analytics som visas med exakt aviseringsnamn tillsammans med resursgruppen och Aviseringsegenskaper; När du skapade med hjälp av [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules)

Den [äldre Log Analytics API](../../azure-monitor/platform/api-alerts.md) har aviseringsåtgärder och scheman som en del av Log Analytics sparade sökningen och inte rätt [Azure-resurser](../../azure-resource-manager/resource-group-overview.md). Därför möjliggjort fakturering för sådana äldre loggaviseringar som skapats för Log Analytics med hjälp av Azure Portal **utan** [byter till nya API: et](../../azure-monitor/platform/alerts-log-api-switch.md) eller via [äldre Log Analytics API](../../azure-monitor/platform/api-alerts.md) - dolda pseudo Varningsregler skapas på `microsoft.insights/scheduledqueryrules` för fakturering i Azure. Dolda pseudo-aviseringsregler skapade för fakturering på `microsoft.insights/scheduledqueryrules` enligt som `<WorkspaceName>|<savedSearchId>|<scheduleId>|<ActionId>` tillsammans med resursgruppen och egenskaper för aviseringen.

> [!NOTE]
> Om ogiltiga tecken som `<, >, %, &, \, ?, /` finns, kommer att ersättas med `_` i dolda pseudo varningsregeln namn och därför även i Azure fakturerar.

Ta bort dolda scheduleQueryRules resurserna som du skapade för fakturering av Varningsregler med [äldre Log Analytics API](api-alerts.md), användare kan göra något av följande:

- Antingen en användare kan [växla API till inställningar för Varningsregler i Log Analytics-arbetsytan](../../azure-monitor/platform/alerts-log-api-switch.md) och utan dataförlust sina Varningsregler eller övervakning flytten till Azure Resource Manager kompatibla [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules). Därigenom eliminera behovet av att göra pseudo dolda Varningsregler för fakturering.
- Eller om användaren inte vill att växla till API-inställningar, användaren måste **ta bort** ursprungliga schema och Aviseringsåtgärd med [äldre Log Analytics API](api-alerts.md) eller ta bort i [Azure-portalen i ursprungliga loggvarningsregel](../../azure-monitor/platform/alerts-log.md#view--manage-log-alerts-in-azure-portal)

Dessutom för dolda scheduleQueryRules resurserna som du skapade för fakturering av Varningsregler med [äldre Log Analytics API](api-alerts.md), ändring av åtgärder som PUT misslyckas. Som den `microsoft.insights/scheduledqueryrules` typ pseudo reglerna avsedda för syftet med fakturering Varningsregler som skapats med hjälp av [äldre Log Analytics API](api-alerts.md). Ändringar varningsregeln ska göras med hjälp av [äldre Log Analytics API](api-alerts.md) (eller) kan användaren [växla API till inställningar för varningsreglerna](../../azure-monitor/platform/alerts-log-api-switch.md) att använda [scheduledQueryRules API](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) i stället.

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [skapa i loggaviseringar i Azure](../../azure-monitor/platform/alerts-log.md).
* Förstå [webhooks i loggaviseringar i Azure](alerts-log-webhook.md).
* Lär dig mer om [Azure-aviseringar](../../azure-monitor/platform/alerts-overview.md).
* Läs mer om [Application Insights](../../azure-monitor/app/analytics.md).
* Läs mer om [Log Analytics](../../azure-monitor/log-query/log-query-overview.md).