---
title: Konfigurera en mall för enheten i ett program med Azure IoT Central | Microsoft Docs
description: Lär dig hur du ställer in en mall för enheten med mått, inställningar, egenskaper, regler och en instrumentpanel.
author: viv-liu
ms.author: viviali
ms.date: 06/19/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 7fb0fba519a7833ac318c713dc9eb3c6ac7f8b5b
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2019
ms.locfileid: "67509546"
---
# <a name="set-up-a-device-template"></a>Konfigurera en enhetsmall

En mall för enheten är en skiss som definierar egenskaper och beteenden för en typ av enhet som ansluter till ett Azure IoT Central program.

Hjälpverktyg kan till exempel skapa en mall för enheten för en ansluten fläkt som har följande egenskaper:

- Temperaturen som telemetri mätning
- Mätning av plats
- Fläkt meddelar fel händelse mätning
- Fläkt fungerar tillstånd mätning
- Fläkt hastighet
- Regler som skickar aviseringar
- Instrumentpanel som ger dig en övergripande vy av enheten

Från den här enheten mallen en operatör kan skapa och ansluta riktiga fläkt enheter med namn som **fläkt-1** och **fläkt 2**. Alla dessa fans ha mått, inställningar, egenskaper, regler och en instrumentpanel som användare av ditt program kan övervaka och hantera.

> [!NOTE]
> Endast builders och administratörer kan skapa, redigera och ta bort enheten mallar. Alla användare kan skapa enheter på den **Device Explorer** sida från befintliga mallar för enheten.

## <a name="create-a-device-template"></a>Skapa en mall för enhet

1. Navigera till den **enheten mallar** sidan.

2. Om du vill skapa en mall, starta genom att välja **+ ny**.

3. Kom igång snabbt genom att välja från befintliga färdiga mallar. Annars väljer **anpassade**, ange ett namn och klicka på **skapa** att skapa en egen mall från början.

   ![Mallbibliotek för enhet](./media/howto-set-up-template/newtemplate.png)

4. När du skapar en anpassad mall kan du se den **enhetsinformation** för den nya mallen för enheten. IoT Central skapar automatiskt en simulerad enhet när du skapar en mall för enheten. En simulerad enhet kan du testa hur ditt program innan du ansluter en riktig enhet.

I följande avsnitt beskrivs var och en av flikarna på den **enheten mallen** sidan.

## <a name="measurements"></a>Mått

Mätning av faktisk användning är de data som kommer från din enhet. Du kan lägga till flera mått till mallen för enheten att matcha funktionerna i din enhet.

- **Telemetri** måtten är numeriska datapunkter som din enhet som samlar in över tid. De är representeras som en löpande ström. Ett exempel är temperatur.
- **Händelsen** måtten är point-in-time-data som representerar något signifikanta på enheten. En allvarlighetsgrad som representerar vikten av en händelse. Ett exempel är ett meddelar fläkt-fel.
- **Tillstånd** mått representerar tillståndet för enheten eller dess komponenter under en viss tidsperiod. Till exempel ett fläkt-läge kan definieras som att ha **Operating** och **stoppad** som två möjliga tillstånd.
- **Plats** måtten är longitud och latitud koordinaterna för enheten under en viss tidsperiod i. Exempelvis kan en fläkt flyttas från en plats till en annan.

### <a name="create-a-telemetry-measurement"></a>Skapa ett telemetri-mått

Om du vill lägga till ett nytt mått i telemetri, Välj **+ ny mätning**, Välj **telemetri** som mätningen skriver och ange information i formuläret.

> [!NOTE]
> Fältnamn i mallen enheten måste matcha egenskapsnamnen i motsvarande enhet koden för telemetri-mått som ska visas i programmet när en riktig enhet är ansluten. Göra detsamma när du konfigurerar inställningar och egenskaper för enhet kommandon när du fortsätter att definiera enhet-mall i följande avsnitt.
.PNG exempel: du kan lägga till ett nytt temperatur telemetri mått:

| Visningsnamn        | Fältnamn    |  Enheter    | Min   |Max|
| --------------------| ------------- |-----------|-------|---|
| Temperatur         | temp          |  degC     |  0    |100|

![”Skapa telemetri” formuläret med information om temperatur mätning](./media/howto-set-up-template/measurementsform.png)

När du har valt **spara**, **temperatur** mått visas i listan över mått. I en liten stund visas visualisering av temperaturdata från den simulerade enheten.

När du visar telemetri som du kan välja bland följande alternativ för aggregeringen: Medelvärde, lägsta, högsta, Sum och Count. **Genomsnittlig** väljs som standard aggregering i diagrammet.

> [!NOTE]
> Datatypen för måttet telemetri är en flytande peka tal.

### <a name="create-an-event-measurement"></a>Skapa en händelse

Om du vill lägga till en ny händelse mätning, Välj **+ ny mätning** och välj **händelse** som måttenhet. Ange information den **Skapa händelse** formuläret.

Ange den **visningsnamn**, **fältnamn**, och **allvarlighetsgrad** information om händelsen. Du kan välja mellan tre tillgängliga nivåer av allvarlighetsgrad: **Fel**, **varning**, och **Information**.

Exempel: du kan lägga till en ny **fläkt meddelar fel** händelse.

| Visningsnamn        | Fältnamn    |  Standard-allvarlighetsgrad |
| --------------------| ------------- |-----------|
| Fel på fläktmotor     | fanmotorerror |  Fel    |

![”Skapa händelse” formuläret med information om en fläkt meddelar händelse](./media/howto-set-up-template/eventmeasurementsform.png)

När du har valt **spara**, **fläkt meddelar fel** mått visas i listan över mått. I en liten stund visas visualiseringen av händelsedata från den simulerade enheten.

Välj ikonen händelse i diagrammet om du vill visa mer information om en händelse:

![Information om händelsen ”fläkt meddelar fel”](./media/howto-set-up-template/eventmeasurementsdetail.png)

> [!NOTE]
> Datatypen för måttet händelse är sträng.

### <a name="create-a-state-measurement"></a>Skapa ett mått för tillstånd

Om du vill lägga till ett nytt tillstånd mått, Välj den **+ ny mätning** och välj **tillstånd** som måttenhet. Ange information den **skapa tillstånd** formuläret.

Ange information för **visningsnamn**, **fältnamn**, och **värden** av tillstånd. Varje värde kan också ha ett namn som ska användas när värdet visas i diagram och tabeller.

Exempel: du kan lägga till en ny **fläkt läge** tillstånd som har två möjliga värden som enheten kan skicka **Operating** och **stoppad**.

| Visningsnamn | Fältnamn    |  Värdet 1   | Visningsnamn | Värdet 2    |Visningsnamn  | 
| -------------| ------------- |----------- | -------------| -----------| -------------|
| Fläktläge     | fläktläge       |  1         | Drift    |     0      | Stoppad      |

![”Redigeringsläge” formuläret med information om fläkt läge](./media/howto-set-up-template/statemeasurementsform.png)

När du har valt **spara**, **fläkt läge** tillstånd mått visas i listan över mått. En kort stund har du se visualisering av systemtillstånd från den simulerade enheten.

Om enheten skickar för många datapunkter i en liten varaktighet, visas mätningen tillstånd med ett annat visuellt objekt. Välj diagrammet för att visa alla datapunkter inom den tidsperioden i kronologisk ordning. Du kan också begränsa tidsintervall för mätningen ritas i diagrammet.

> [!NOTE]
> Datatypen för måttet tillstånd är sträng.

### <a name="create-a-location-measurement"></a>Skapa en plats

Om du vill lägga till en ny plats mätning, Välj **+ ny mätning**, Välj **plats** som värdet skriver och ange information den **skapa mått** formuläret.

Du kan till exempel lägga till en ny plats telemetri mätning:

| Visningsnamn        | Fältnamn    |
| --------------------| ------------- |
| Tillgången plats      |  assetloc     |

![”Skapa plats” formuläret med information om plats mätning](./media/howto-set-up-template/locationmeasurementsform.png)

När du har valt **spara**, **plats** mått visas i listan över mått. I en liten stund visas visualisering av platsdata från den simulerade enheten.

Vid visning av plats måste du kan välja mellan följande alternativ: senaste plats och platshistorik. **Platshistorik** tillämpas endast via det valda tidsintervallet.

Datatypen för måttet plats är ett objekt som innehåller longitud, latitud och en valfri höjd. Följande kodfragment visar JavaScript-struktur:

```javascript
assetloc: {
  lon: floating point number,
  lat: floating point number,
  alt?: floating point number
}
```

## <a name="settings"></a>Inställningar

Inställningar styr en enhet. De operatörer kan ange indata till enheten. Du kan lägga till flera inställningar till mallen för enheten som visas som paneler på den **inställningar** fliken för operatörer att använda. Du kan lägga till många typer av inställningar: tal, text, datum, växla, plocklista och områdesetikett.

Inställningarna kan vara i något av tre tillstånd. Enheten rapporterar tillståndet.

- **Synkroniserad**: Enheten har ändrats för att återspegla värdet.

- **Väntande**: Enheten för närvarande ändrar till inställningens värde.

- **Fel**: Enheten har returnerat ett fel.

Du kan till exempel lägga till en ny fläkt hastighet inställning genom att välja **inställningar** och ange i den nya **nummer** inställningen:

| Visningsnamn  | Fältnamn    |  Enheter  | Decimals |Inledande|
| --------------| ------------- |---------| ---------|---- |
| Fläkthastighet     | fanSpeed      | RPM     | 2        | 0   |

![”Konfigurera tal” formuläret med information om inställningar för uppspelningshastighet](./media/howto-set-up-template/settingsform.png)

När du har valt **spara**, **fläkthastighet** inställningen visas som en panel. En operatör kan använda inställningen på den **Device Explorer** sidan för att ändra fläkt hastigheten på enheten.

## <a name="properties"></a>Egenskaper

Egenskaperna är metadata som associeras med enheten, till exempel en fast enhetsplats och serienummer. Lägga till flera egenskaper i mallen för enheten som visas som paneler på den **egenskaper** fliken. En egenskap har en typ som tal, text, datum, växla, enhetsegenskap, etikett eller en fast plats. En operator anger värden för egenskaper när de skapar en enhet och de kan redigera dessa värden när som helst. Enhetsegenskaper är skrivskyddade och skickas från enheten till programmet. En operatör kan inte ändra egenskaper för enhet. När en riktig enhet ansluter uppdaterar egenskapen enhetspanelen i programmet.

Det finns två kategorier med egenskaper:

- _Enhetsegenskaper_ som enheten rapporterar till programmet IoT Central. Enhetsegenskaper är skrivskyddade värdena som rapporteras av enheten och uppdateras i programmet när en riktig enhet är ansluten.
- _Programegenskaper_ som lagras i programmet och kan redigeras av operatorn. Programegenskaper lagras endast i programmet och ses aldrig av en enhet.

Du kan till exempel lägga till datum för senaste underhållna för enheten som en ny **datum** egenskapen (en programegenskap) på den **egenskaper** fliken:

| Visningsnamn  | Fältnamn | Initialt värde   |
| --------------| -----------|-----------------|
| Senaste service      | lastServiced        | 01/29/2019     |

![”Konfigurera senaste Serviced” formuläret på fliken ”Egenskaper”](./media/howto-set-up-template/propertiesform.png)

När du har valt **spara**, senaste servas datum för enheten visas som en panel.

När du har skapat panelen kan du ändra egenskapsvärdet för program i den **Device Explorer**.

### <a name="create-a-location-property"></a>Skapa en plats-egenskap

Du kan ge geografisk kontext till dina platsdata i Azure IoT Central och mappa alla latitud och longitud koordinater eller en gatuadress. Azure Maps gör det möjligt för den här funktionen i IoT Central.

Du kan lägga till två typer av platsegenskaper:

- **Plats som en programegenskap**, som lagras i programmet. Programegenskaper lagras endast i programmet och ses aldrig av en enhet.
- **Plats som en enhetsegenskap**, som enheten rapporterar till programmet. Den här typen av egenskapen passar bäst för en statisk plats.

> [!NOTE]
> En historik registreras inte i platsen som en egenskap. Om historik är det önskade kan du använda ett plats-mått.

#### <a name="add-location-as-an-application-property"></a>Lägg till-plats som en egenskap för programmet

Du kan skapa en plats-egenskap som en programegenskap med hjälp av Azure Maps i ditt IoT Central-program. Du kan exempelvis lägga till enhetsadress för installation:

1. Navigera till den **egenskaper** fliken.

2. Välj i biblioteket, **plats**.

3. Konfigurera **visningsnamn**, **fältnamn**, och (frivilligt) **ursprungsvärdet** för platsen.

    | Visningsnamn  | Fältnamn | Initialt värde |
    | --------------| -----------|---------|
    | Installationen adress | installAddress | Microsoft, 1 Microsoft Way, Redmond, WA 98052   |

   ![”Konfigurera platsen” formuläret med information om plats](./media/howto-set-up-template/locationcloudproperty2.png)

   Det finns två format som stöds för att lägga till en plats:
   - **Platsen som en adress**
   - **Platsen som koordinater**

4. Välj **Spara**. En operatör kan du uppdatera platsvärdet i den **Device Explorer**.

#### <a name="add-location-as-a-device-property"></a>Lägg till plats som en enhetsegenskap

Du kan skapa en plats-egenskap som en enhet som enheten rapporterar. Exempel: Om du vill spåra enhetens plats:

1. Navigera till den **egenskaper** fliken.

2. Välj **enhetsegenskap** från biblioteket.

3. Konfigurera visningsnamn och fältnamn och välj **plats** som datatyp:

    | Visningsnamn  | Fältnamn | Datatyp |
    | --------------| -----------|-----------|
    | Enhetsplats | deviceLocation | location  |

   > [!NOTE]
   > Fältnamn måste matcha egenskapsnamnen i motsvarande kod för enhet

   ![”Konfigurera enhetsegenskaper” formuläret med information om plats](./media/howto-set-up-template/locationdeviceproperty2.png)

När den faktiska enheten är ansluten, platsen du lagt till som en enhetsegenskap uppdateras med värdet som skickas från enheten. När du har konfigurerat din Platsegenskapen, kan du [lägga till en karta för att visualisera plats i instrumentpanelen för enheten](#add-a-location-in-the-dashboard).

## <a name="commands"></a>Kommandon

Kommandon används för att hantera en enhet. De operatörer kan köra kommandon på enheten. Du kan lägga till flera kommandon till mallen för enheten som visas som paneler på den **kommandon** fliken för operatörer att använda. Som enhetens builder har du möjlighet att definiera kommandon efter behov.

Hur skiljer sig ett kommando från en inställning?

* **Ange**: En inställning är en konfiguration som du vill tillämpa på en enhet. Du vill att enheten ska spara konfigurationen tills du ändrar den. Till exempel vill du ange temperaturen för din frysfartyg och du vill att även om frysfartyg startas om.

* **Kommandot**: Du kan använda kommandon för att omedelbart köra ett kommando på enheten via en fjärranslutning från IoT Central. Om en enhet inte är ansluten kommandot tidsgränsen och misslyckas. Exempelvis kan du starta om en enhet.

Exempel: du kan lägga till en ny **Echo** kommandot genom att välja den **kommandon** och sedan välja **+ nytt kommando**, och ange information om nya kommandot:

| Visningsnamn  | Fältnamn | Standardvärde för tidsgräns | Datatyp |
| --------------| -----------|---------------- | --------- |
| Ekokommando  | eko       |  30             | text      |

![”Konfigurera kommandot” formuläret med information om echo](./media/howto-set-up-template/commandsecho1.png)

När du har valt **spara**, **Echo** kommandot visas som en panel och är redo att användas från den **Device Explorer** när verkliga enheten ansluts. Fältnamn för kommandot måste matcha egenskapsnamnen i motsvarande enhet koden för kommandon att kunna köras.

## <a name="rules"></a>Regler

Regler för operatörerna kan övervaka enheter i nära realtid. Regler för anropa automatiskt åtgärder som att skicka ett e-postmeddelande när regeln utlöses. En typ av regel är tillgängliga idag:

- **Telemetri regeln**, som utlöses när den valda enhetstelemetri överskrider ett angivet tröskelvärde. [Mer information om regler för telemetri](howto-create-telemetry-rules.md).

## <a name="dashboard"></a>Instrumentpanel

Instrumentpanelen är där en operatör går för att se information om en enhet. Som en builder du lägga till paneler till den här sidan för att hjälpa operatörer att förstå hur enheten fungerar. Du kan lägga till många typer av paneler som bild, linjediagram, stapeldiagram, nyckeltal (KPI), inställningar och egenskaper och etiketten.

Du kan till exempel lägga till en **inställningar och egenskaper** rutan för att visa ett urval av de aktuella värdena för inställningar och egenskaper genom att välja den **instrumentpanelen** fliken och panel från biblioteket:

![”Konfigurera enhetsinformation” formuläret med information om inställningar och egenskaper](./media/howto-set-up-template/dashboardsettingsandpropertiesform1.png)

Nu när en operatör visar instrumentpanelen på den **Device Explorer**, de kan se panelen.

### <a name="add-a-location-in-the-dashboard"></a>Lägg till en plats på instrumentpanelen

Om du har konfigurerat ett plats-mått, kan du visualisera plats med en karta i instrumentpanelen i enheten.

1. Navigera till den **instrumentpanelen** fliken.

1. På instrumentpanelen för enheten väljer **kartan** från biblioteket.

1. Ge kartan en rubrik. I följande exempel har rubriken **aktuella enhetsplats**. Välj plats-mått som du tidigare har konfigurerat på den **mätningar** fliken. I följande exempel visas den **tillgången plats** mätning:

   ![”Konfigurera kartan” formuläret med information om namn och egenskaper](./media/howto-set-up-template/locationcloudproperty5map.png)

1. Välj **Spara**. Panelen tjänstkarta visar nu den plats som du har valt.

Du kan ändra storlek på panelen tjänstkarta. När en operatör visar instrumentpanelen på den **Device Explorer**, paneler på instrumentpanelen att du har konfigurerat, inklusive en platskarta är synliga.

Mer information om hur du använder paneler i Azure IoT Central finns [använda instrumentpaneler](howto-use-tiles.md).

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du ställer in en mall för enheten i Azure IoT Central programmet, kan du:

> [!div class="nextstepaction"]
> [Skapa en ny enhet mallversion](howto-version-device-template.md)
> [ansluta en MXChip IoT DevKit enhet till Azure IoT Central programmet](howto-connect-devkit.md)
> [ansluta ett allmänt klientprogram till din Azure IoT Central-program (Node.js)](howto-connect-nodejs.md)