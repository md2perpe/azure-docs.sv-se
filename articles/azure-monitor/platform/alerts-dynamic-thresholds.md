---
title: Skapa aviseringar med dynamiska tröskelvärden i Azure Monitor
description: Skapa aviseringar med dynamiska tröskelvärden för maskininlärningsbaserade
author: yanivlavi
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: yalavi
ms.reviewer: mbullwin
ms.openlocfilehash: 0d6c578186dab9622ce650f535e11d505efcecb3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65067625"
---
# <a name="metric-alerts-with-dynamic-thresholds-in-azure-monitor"></a>Måttaviseringar med dynamiska tröskelvärden i Azure Monitor

Metrisk varning med dynamiska tröskelvärden identifiering utnyttjar avancerad maskininlärning (ML) om du vill veta mer mått historiska beteende, identifiera mönster och avvikelser som indikerar möjliga problem. Det finns stöd för både ett enkelt gränssnitt och åtgärder i stor skala genom att tillåta användare att konfigurera Varningsregler via API: et för Azure Resource Manager på ett helt automatiserat sätt.

När en varningsregel har skapats kan aktiveras den endast när det övervakade måtten inte fungerar som förväntat, baserat på dess skräddarsydda tröskelvärden.

Vi vill gärna höra dina synpunkter, hålla det kommer på <azurealertsfeedback@microsoft.com>.

## <a name="why-and-when-is-using-dynamic-condition-type-recommended"></a>När och varför använder dynamisk Villkorstyp rekommenderas?

1. **Skalbar aviseringar** – dynamiska tröskelvärden för aviseringar regler kan skapa anpassade tröskelvärden för hundratals mått serien i taget. Ger ännu är det lika enkelt att definiera en varningsregel för ett mått. Med hjälp av Användargränssnittet eller Azure Resource Manager API-resultat i färre Varningsregler för att hantera. Den skalbara metoden är särskilt användbart när du hanterar mått dimensioner eller när du tillämpar på flera resurser, t.ex. alla prenumerationsresurser. Vilket innebär att en betydande tid som sparas på hanterings- och skapande av regler för aviseringar. [Mer information om hur du konfigurerar aviseringar för mått med dynamiska tröskelvärden med hjälp av mallar](alerts-metric-create-templates.md).

1. **Smart mått mönsterigenkänning** – med hjälp av vår unika ML-teknik, kan vi automatiskt identifiera måttmönster och anpassa sig till mått ändringar över tid, som ofta kan innehålla säsongsberoende (per timme / dag / vecka). Anpassar sig till de mått beteende med tiden och avisering baserat på avvikelser från användningsmönster Frigör arbetet med vetskapen om att ”höger” tröskelvärdet för varje mått. ML-algoritmen som används i dynamiska tröskelvärden har utformats för att förhindra att bort störande (låg precision) eller tröskelvärden för hela (låg återkallande) som inte har ett förväntat mönster.

1. **Intuitiv Configuration** – dynamiska tröskelvärden att ställa in aviseringar för mått med hjälp av avancerade koncept lindra att behöva ha omfattande kunskap om måttet.

## <a name="how-to-configure-alerts-rules-with-dynamic-thresholds"></a>Konfigurera aviseringar regler med dynamiska tröskelvärden?

Aviseringar med dynamiska tröskelvärden kan konfigureras via aviseringar för mått i Azure Monitor. [Mer information om hur du konfigurerar aviseringar för mått](alerts-metric.md).

## <a name="how-are-the-thresholds-calculated"></a>Hur beräknas tröskelvärdena?

Dynamiska tröskelvärden kontinuerligt lär sig av data för mått-serien och försöker att modellera den med hjälp av en uppsättning algoritmer och metoder. Den identifierar mönster i data, till exempel säsongsvärdet (per timme / dag / vecka) och kan hantera bort störande mått (till exempel dator CPU eller minne) samt mått med låg spridning (till exempel tillgänglighet och fel-pris).

Tröskelvärdena som är markerade så att en avvikelse från de här tröskelvärdena visar avvikelser i måttbeteendet.

> [!NOTE]
> Identifiering av säsongsknutet mönster har angetts till en timme, dag eller vecka intervall. Det innebär att andra mönster som bihourly mönster eller semiweekly inte kan identifieras.

## <a name="what-does-sensitivity-setting-in-dynamic-thresholds-mean"></a>Vad kostar ' känslighetsinställningen ' i dynamiska tröskelvärden medelvärdet?

Tröskelvärde för känslighet är ett övergripande begrepp som styr hur lång avvikelse från måttbeteende som krävs för att utlösa en avisering.
Det här alternativet kräver inte kunskap om mått som statiska tröskelvärdet. De tillgängliga alternativen är:

- Hög – är tröskelvärdena tätt och nära mönstret mått serien. En varningsregel aktiveras på den lägsta avvikelsen, vilket resulterar i fler aviseringar.
- Medel – mindre strikta och bättre avvägd tröskelvärden, färre aviseringar än med hög känslighet (standard).
- Låg – tröskelvärdena blir formulärdata med mer avståndet från mått serien mönster. En varningsregel utlöser endast vid stora avvikelser, vilket resulterar i färre aviseringar.

## <a name="what-are-the-operator-setting-options-in-dynamic-thresholds"></a>Vad är 'Operator' inställningsalternativen i dynamiska tröskelvärden?

Dynamiska tröskelvärden för aviseringar regel kan skapa anpassade tröskelvärden baserat på måttbeteende för båda övre och nedre gränser med hjälp av samma varningsregeln.
Du kan välja aviseringen ska utlösas på någon av följande tre villkor:

- Större än det övre tröskelvärdet eller lägre än det nedre tröskelvärdet (standard)
- Större än det högsta tröskelvärdet
- Lägre än det nedre tröskelvärdet.

## <a name="what-do-the-advanced-settings-in-dynamic-thresholds-mean"></a>Vad gör de avancerade inställningarna i dynamiska tröskelvärden medelvärdet?

**Misslyckas perioder** -dynamiska tröskelvärden kan du konfigurera ”antal överträdelser för att utlösa aviseringen”, ett minsta antal avvikelser som krävs inom ett visst tidsintervall för systemet kan generera en avisering (standardvärdet för tidsperiod är fyra avvikelser i 20 minuter). Användaren kan konfigurera misslyckas punkter och välj vad du vill bli aviserad om genom att ändra misslyckas punkter och tidsperioden. Den här möjligheten minskar onödig avisering som genererats av övergående spikar. Exempel:

Om du vill utlösa en avisering när problemet är kontinuerligt efter 20 minuter, 4 på varandra följande gånger under en viss period gruppering på 5 minuter, använder du följande inställningar:

![Misslyckas perioder inställningar för kontinuerlig problemet 20 minuter 4 på varandra följande gånger under en viss period gruppering på 5 minuter](media/alerts-dynamic-thresholds/0008.png)

Om du vill utlösa en avisering när det har en överträdelse från en dynamiska tröskelvärden på 20 minuter från de senaste 30 minuterna med period på fem minuter, använder du följande inställningar:

![Misslyckas perioder inställningar för problemet på 20 minuter från de senaste 30 minuterna med period gruppering på 5 minuter](media/alerts-dynamic-thresholds/0009.png)

**Ignorera data före** -användare kan även definiera ett startdatum som systemet bör börja räkna tröskelvärden från. Ett vanligt användningsfall kan uppstå när en resurs har en körs i ett läge för testning och höjs nu för att leverera en produktionsarbetsbelastning och därför beteendet för vilka mått som helst under testfasen bör ignoreras.

## <a name="how-do-you-find-out-why-a-dynamic-thresholds-alert-was-triggered"></a>Hur tar du reda på varför en dynamiska tröskelvärden aviseringen utlöstes?

Du kan utforska utlösta aviseringsinstanser i aviseringsvyn antingen genom att klicka på länken i e-post eller SMS: et eller webbläsaren så visas aviseringarna visa i Azure-portalen. [Mer information om varningar](alerts-overview.md#alerts-experience).

I aviseringsvyn visas:

- Måttinformation för tillfället dynamiska tröskelvärden-avisering utlöses.
- Ett diagram över den period där aviseringen utlöstes med dynamiska tröskelvärden som används i det här läget i tid.
- Möjlighet att ge feedback om dynamiska tröskelvärden aviseringen och aviseringarna visa upplevelse som kunde förbättra framtida identifieringar.

## <a name="will-slow-behavior-change-in-the-metric-trigger-an-alert"></a>Långsam beteendet ändras i måttutlösaren en avisering?

Förmodligen inte. Dynamiska tröskelvärden är bra för identifiering av betydande avvikelser i stället för att långsamt utvecklas problem.

## <a name="how-much-data-is-used-to-preview-and-then-calculate-thresholds"></a>Hur mycket data används för att förhandsgranska och sedan beräkna tröskelvärden?

De tröskelvärden som visas i diagrammet, innan en varningsregel skapas på måttet beräknas baserat på tillräckligt med historiska data för att beräkna timme eller varje dag säsongens mönster (10 dagar). När en varningsregel har skapats använder alla nödvändiga historiska data som är tillgänglig och kommer kontinuerligt Läs och anpassa baserat på nya data så att tröskelvärdena mer exakta med dynamiska tröskelvärden. Det innebär att diagrammet efter den här beräkningen också visar veckovisa mönster.

## <a name="how-much-data-is-needed-to-trigger-an-alert"></a>Hur mycket data krävs för att utlösa en avisering?

Om du har en ny resurs eller saknas måttdata utlöser dynamiska tröskelvärden inte aviseringar innan det finns tre dagar data att säkerställa att korrekt tröskelvärden.

## <a name="dynamic-thresholds-best-practices"></a>Metodtips för dynamiska tröskelvärden

Dynamiska tröskelvärden kan tillämpas på valfri plattform eller ett anpassat mått i Azure Monitor och den har också justerad för vanliga mått för program och infrastruktur.
Följande objekt är bästa metoder för hur du konfigurerar aviseringar på några av de här måtten med dynamiska tröskelvärden.

### <a name="dynamic-thresholds-on-virtual-machine-cpu-percentage-metrics"></a>Dynamiska tröskelvärden på mätvärden för virtuell dator CPU-procent

1. I [Azure-portalen](https://portal.azure.com), klicka på **övervakaren**. Övervakarevyn samlas alla dina övervakningsinställningar och -data i en vy.

2. Klicka på **aviseringar** klickar **+ ny aviseringsregel**.

    > [!TIP]
    > De flesta resursbladen har också **aviseringar** i sina resursmenyn under **övervakning**, du kan skapa aviseringar därifrån också.

3. Klicka på **Välj mål**, i fönstret kontext som läser in väljer du en målresurs som du vill att Avisera om. Använd **prenumeration** och **”virtuella datorer” resurstypen** listrutor för att hitta resursen som du vill övervaka. Du kan också använda sökfältet för att hitta din resurs.

4. När du har valt en målresurs, klickar du på **Lägg till villkor**.

5. Välj den **CPU-procent**.

6. Du kan också förfina måttet genom att justera **Period** och **aggregering**. Det rekommenderas inte för att använda, Maximum, sammansättningstyp för den här Måttyp eftersom det är mindre representativ för beteende. För ”högsta' aggregering typen statisk tröskelvärdet kanske är mer lämpliga.

7. Du ser ett diagram för måttet för de senaste 6 timmarna. Definiera parametrarna som aviseringen:
    1. **Ange ett villkor för typen** -väljer alternativet ”dynamiska”.
    1. **Känslighet** -Välj Medium/låg känslighet för att minska onödig avisering.
    1. **Operatorn** -väljer ”större än' om inte beteendet representerar programanvändningen.
    1. **Frekvens** -Överväg att sänka baserat på inverkan på företaget för aviseringen.
    1. **Misslyckas perioder** (avancerade alternativ) - fönstret titt backend ska vara minst 15 minuter. Till exempel om perioden är inställt på fem minuter, ska misslyckas perioder vara minst tre eller fler.

8. Metrisk diagrammet visar beräknade tröskelvärden baserat på senaste data.

9. Klicka på **Klar**.

10. Fyll i **aviseringsinformation** som **avisering Regelnamn**, **beskrivning**, och **allvarlighetsgrad**.

11. Lägg till en åtgärdsgrupp till aviseringen genom att välja en befintlig åtgärdsgrupp eller skapa en ny åtgärdsgrupp.

12. Klicka på **klar** att spara måttaviseringsregel.

> [!NOTE]
> Metrisk varning regler som skapats via portalen skapas i samma resursgrupp som målresursen.

### <a name="dynamic-thresholds-on-application-insights-http-request-execution-time"></a>Körningstid för dynamiska tröskelvärden på Application Insights HTTP-begäran

1. I [Azure-portalen](https://portal.azure.com), klicka på **övervakaren**. Övervakarevyn samlas alla dina övervakningsinställningar och -data i en vy.

2. Klicka på **aviseringar** klickar **+ ny aviseringsregel**.

    > [!TIP]
    > De flesta resursbladen har också **aviseringar** i sina resursmenyn under **övervakning**, du kan skapa aviseringar därifrån också.

3. Klicka på **Välj mål**, i fönstret kontext som läser in väljer du en målresurs som du vill att Avisera om. Använd **prenumeration** och **”Application Insights” resurstypen** listrutor för att hitta resursen som du vill övervaka. Du kan också använda sökfältet för att hitta din resurs.

4. När du har valt en målresurs, klickar du på **Lägg till villkor**.

5. Välj den **'Körningstid för HTTP-begäran ”** .

6. Du kan också förfina måttet genom att justera **Period** och **aggregering**. Det rekommenderas inte för att använda, Maximum, sammansättningstyp för den här Måttyp eftersom det är mindre representativ för beteende. För ”högsta' aggregering typen statisk tröskelvärdet kanske är mer lämpliga.

7. Du ser ett diagram för måttet för de senaste 6 timmarna. Definiera parametrarna som aviseringen:
    1. **Ange ett villkor för typen** -väljer alternativet ”dynamiska”.
    1. **Operatorn** -Välj ”större än' att minska aviseringar som utlösts på förbättring i varaktighet.
    1. **Frekvens** -Överväg att sänka baserat på inverkan på företaget för aviseringen.

8. Metrisk diagrammet visar beräknade tröskelvärden baserat på senaste data.

9. Klicka på **Klar**.

10. Fyll i **aviseringsinformation** som **avisering Regelnamn**, **beskrivning**, och **allvarlighetsgrad**.

11. Lägg till en åtgärdsgrupp till aviseringen genom att välja en befintlig åtgärdsgrupp eller skapa en ny åtgärdsgrupp.

12. Klicka på **klar** att spara måttaviseringsregel.

> [!NOTE]
> Metrisk varning regler som skapats via portalen skapas i samma resursgrupp som målresursen.
