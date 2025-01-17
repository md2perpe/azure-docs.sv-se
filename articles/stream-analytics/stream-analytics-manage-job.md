---
title: 'Självstudie: Skapa och hantera ett Stream Analytics-jobb med hjälp av Azure-portalen'
description: I den här självstudien finns en omfattande beskrivning av hur du använder Azure Stream Analytics för att analysera bedrägliga samtal i en telefonsamtalsström.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.workload: data-services
ms.topic: tutorial
ms.custom: mvc
ms.date: 06/03/2019
ms.openlocfilehash: d09ed0585250d078f728aa4e7272cca147a40c38
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/07/2019
ms.locfileid: "67612366"
---
# <a name="analyze-phone-call-data-with-stream-analytics-and-visualize-results-in-power-bi-dashboard"></a>Analysera telefonsamtalsdata med Stream Analytics och visualisera resultat på en Power BI-instrumentpanel

I den här självstudien lär du dig att analysera telefonsamtalsdata med hjälp av Azure Stream Analytics. Telefonsamtal-data som genereras av ett klientprogram innehåller några bedrägliga samtal som kommer att filtreras av Stream Analytics-jobb.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Generera exempel på telefonsamtalsdata och skicka dem till Azure Event Hubs
> * Skapa ett Stream Analytics-jobb
> * Konfigurera jobbindata och -utdata
> * Definiera en fråga för att filtrera bedrägliga samtal
> * Testa och starta jobbet
> * Visualisera resultat i Power BI

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar måste du göra följande:

* Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/).
* Logga in på [Azure Portal](https://portal.azure.com/).
* Ladda ned telefonsamtalets händelsegeneratorapp [TelcoGenerator.zip](https://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip) från Microsoft Download Center eller hämta källkoden från [GitHub](https://aka.ms/azure-stream-analytics-telcogenerator).
* Du behöver Power BI-konto.

## <a name="create-an-azure-event-hub"></a>Skapa en Azure-händelsehubb

Innan Stream Analytics kan analysera dataströmmen med bedrägliga samtal behöver data skickas till Azure. I den här självstudien skickar du data till Azure med  [Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/event-hubs-what-is-event-hubs).

Använd följande steg för att skapa en händelsehubb och skickar samtalsdata till den händelsehubben:

1. Logga in på [Azure Portal](https://portal.azure.com/).
2. Välj **Skapa en resurs** > **Sakernas Internet** > **Event Hubs**.

   ![Skapa en Azure-händelsehubb på portalen](media/stream-analytics-manage-job/find-event-hub-resource.png)
3. Fyll i fönsterrutan **Skapa namnrymd** med följande värden:

   |**Inställning**  |**Föreslaget värde** |**Beskrivning**  |
   |---------|---------|---------|
   |Namn     | myEventHubsNS        |  Ett unikt namn för att identifiera händelsehubbens namnområde.       |
   |Prenumeration     |   \<Din prenumeration\>      |   Välj en Azure-prenumeration där du vill skapa händelsehubben.      |
   |Resursgrupp     |   MyASADemoRG      |  Välj **Skapa ny** och ange ett nytt resursgruppsnamn för ditt konto.       |
   |Location     |   USA, västra 2      |    Plats där händelsehubbens namnområde kan distribueras.     |

4. Använd standardalternativ för de återstående inställningarna och välj **Skapa**.

   ![Skapa en namnrymd för en händelsehubb på Azure-portalen](media/stream-analytics-manage-job/create-event-hub-namespace.png)

5. När namnrymden har distribuerats klart går du till **Alla resurser** och letar upp *myEventHubsNS* i listan över Azure-resurser. Välj *myEventHubsNS* för att öppna den.
6. Välj sedan **+ Händelsehubb** och ange **Namn** som *MyEventHub* eller ett annat valfritt namn. Använd standardalternativen för de återstående inställningarna och välj **Skapa**. Vänta sedan tills distributionen är klar.

   ![Konfiguration av händelsehubb på Azure-portalen](media/stream-analytics-manage-job/create-event-hub-portal.png)

### <a name="grant-access-to-the-event-hub-and-get-a-connection-string"></a>Bevilja åtkomst till händelsehubben och få en anslutningssträng

Innan ett program kan skicka data till Azure Event Hubs måste händelsen ha en princip som tillåter lämplig åtkomst. Åtkomstprincipen producerar en anslutningssträng som inkluderar auktoriseringsinformation.

1. Gå till händelsehubben som du skapade i föregående steg, MyEventHub *. Välj **Principer för delad åtkomst** under **Inställningar** och välj sedan **+ Lägg till**.

2. Ge principen namnet **MyPolicy** och se till att **Hantera** är markerat. Välj sedan **Skapa**.

   ![Skapa händelsehubb med delade åtkomstprinciper](media/stream-analytics-manage-job/create-event-hub-access-policy.png)

3. När principen har skapats väljer du att öppna principen och hitta **Anslutningssträng – primär nyckel**. Välj den blå **kopieringsknappen** intill anslutningssträngen.

   ![Spara anslutningssträngen för princip för delad åtkomst](media/stream-analytics-manage-job/save-connection-string.png)

4. Klistra in anslutningssträngen i en textredigerare. Du behöver den här anslutningssträngen i nästa avsnitt.

   Anslutningssträngen ser ut så här:

   `Endpoint=sb://<Your event hub namespace>.servicebus.windows.net/;SharedAccessKeyName=<Your shared access policy name>;SharedAccessKey=<generated key>;EntityPath=<Your event hub name>`

   Lägg märke till att anslutningssträngen innehåller flera nyckel/värde-par som är separerade med semikolon: **Endpoint**, **SharedAccessKeyName**, **SharedAccessKey** och **EntityPath**.

## <a name="start-the-event-generator-application"></a>Starta händelsegeneratorprogrammet

Innan du startar appen TelcoGenerator ska du konfigurera den så att den skickar data till de Azure Event Hubs du skapade tidigare.

1. Extrahera innehållet i filen [TelcoGenerator.zip](https://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip).
2. Öppna filen `TelcoGenerator\TelcoGenerator\telcodatagen.exe.config` i en valfri textredigerare (Det finns mer än en .config-fil, så se till att öppna rätt fil.)

3. Uppdatera elementet `<appSettings>` i config-filen med följande information:

   * Ange värdet för nyckeln *EventHubName* till värdet för EntityPath i anslutningssträngen.
   * Ange värdet för den *Microsoft.ServiceBus.ConnectionString* nyckel till anslutningssträngen utan värdet EntityPath (Glöm inte att ta bort semikolonet som föregår det).

4. Spara filen.
5. Öppna sedan ett kommandofönster och växla till den mapp där du packade upp programmet TelcoGenerator. Ange sedan följande kommando:

   ```cmd
   telcodatagen.exe 1000 0.2 2
   ```

   Kommandot stöder följande parametrar:
   * Antal samtalsdataposter per timme.
   * Procentandel för sannolikhet för bedrägeri, vilket är hur ofta appen ska simulera ett bedrägligt samtal. Värdet 0.2 betyder att cirka 20 % av anropsposterna ser bedrägliga ut.
   * Längd i timmar, vilket är det antal timmar som appen ska köras. Du kan också stoppa appen när som helst genom att avsluta processen (**Ctrl + C**) på kommandoraden.

   Efter några sekunder börjar appen visa telefonsamtalsposter på skärmen och skickar dem till en händelsehubb. Telefonsamtalsdata innehåller följande fält:

   |**Spela in**  |**Definition**  |
   |---------|---------|
   |CallrecTime    |  Tidsstämpeln för samtalets starttid.       |
   |SwitchNum     |  Telefonväxeln används för att ansluta samtalet. I det här exemplet är växlarna strängar som representerar land/region för ursprung (USA, Kina, Storbritannien, Tyskland eller Australien).       |
   |CallingNum     |  Uppringarens telefonnummer.       |
   |CallingIMSI     |  International Mobile Subscriber Identity (IMSI). Det är en unik identifierare för uppringaren.       |
   |CalledNum     |   Telefonnumret till mottagaren.      |
   |CalledIMSI     |  International Mobile Subscriber Identity (IMSI). Det är en unik identifierare för mottagaren.       |

## <a name="create-a-stream-analytics-job"></a>Skapa ett Stream Analytics-jobb

Nu nr du har en ström av anropshändelser kan du skapa ett Stream Analytics-jobb som läser data från händelsehubben.

1. Du skapar du ett Stream Analytics-jobb genom att gå till [Azure-portalen](https://portal.azure.com/).

2. Välj **Skapa en resurs** > **Sakernas Internet** > **Stream Analytics-jobb**.

3. Fyll i bladet **Nytt Stream Analytics-jobb** med följande värden:

   |**Inställning**  |**Föreslaget värde**  |**Beskrivning**  |
   |---------|---------|---------|
   |Jobbnamn     |  ASATutorial       |   Ett unikt namn för att identifiera händelsehubbens namnområde.      |
   |Prenumeration    |  \<Din prenumeration\>   |   Välj en Azure-prenumeration där du vill skapa jobbet.       |
   |Resursgrupp   |   MyASADemoRG      |   Välj **Använd befintlig** och ange ett nytt resursgruppsnamn för ditt konto.      |
   |Location   |    USA, västra 2     |      Plats där jobbet kan distribueras. Vi rekommenderar att du placerar jobbet och händelsehubben i samma region för bästa prestanda och så att du inte ska betala för att överföra data mellan regioner.      |
   |Värdmiljö    | Molnet        |     Stream Analytics-jobb kan distribueras till molnet eller edge. Molnet kan du distribuera till Azure-molnet och Edge kan du distribuera till en IoT Edge-enhet.    |
   |Strömningsenheter     |    1       |      Strömningsenheter representerar de bearbetningsresurser som krävs för att köra ett jobb. Standardvärdet är inställt på 1. Mer information om skalning av strömningsenheter finns i artikeln om att [förstå och justera strömningsenheter](stream-analytics-streaming-unit-consumption.md).      |

4. Använd standardalternativen för återstående inställningar, Välj **skapa**, och vänta på att distributionen ska lyckas.

   ![Skapa ett Azure Stream Analytics-jobb](media/stream-analytics-manage-job/create-stream-analytics-job.png)

## <a name="configure-job-input"></a>Konfigurera jobbindata

Nästa steg är att definiera en indatakälla som jobbet använder för att läsa data med hjälp av den händelsehubb som du skapade i föregående avsnitt.

1. Från Azure-portalen öppnar du fönsterrutan **Alla resurser** och letar reda på Stream Analytics-jobbet *ASATutorial*.

2. I avsnittet **Jobbtopologi** i Stream Analytics-jobbfönstret väljer du alternativet **Indata**.

3. Välj **+ Lägg till strömindata** och **Händelsehubb**. Fyll i fönsterrutan med följande värden:

   |**Inställning**  |**Föreslaget värde**  |**Beskrivning**  |
   |---------|---------|---------|
   |Inmatat alias     |  CallStream       |  Ange ett eget namn som identifierar din inmatning. Indataaliaset får enbart innehålla alfanumeriska tecken, bindestreck och understreck och måste vara mellan 3-63 tecken långt.       |
   |Prenumeration    |   \<Din prenumeration\>      |   Välj en Azure-prenumeration där du skapade händelsehubben. Händelsehubben kan finnas i samma eller en annan prenumeration som Stream Analytics-jobbet.       |
   |Namnområde för händelsehubb    |  myEventHubsNS       |  Välj namnområdet för händelsehubben som du skapade i föregående avsnitt. Alla händelsehubbnamnområden som finns i din aktuella prenumeration visas i listrutan.       |
   |Namn på händelsehubb    |   MyEventHub      |  Välj händelsehubben som du skapade i föregående avsnitt. Alla händelsehubbar som finns i din aktuella prenumeration visas i listrutan.       |
   |Principnamn för Event Hub   |  MyPolicy       |  Välj händelsehubbens delade princip som du skapade i föregående avsnitt. Alla händelsehubbprinciper som finns i din aktuella prenumeration visas i listrutan.       |

4. Använd standardalternativ för de återstående inställningarna och välj **Spara**.

   ![Konfigurera Azure Stream Analytics-indata](media/stream-analytics-manage-job/configure-stream-analytics-input.png)

## <a name="configure-job-output"></a>Konfigurera jobbutdata

Det sista steget är att definiera en utdatamottagare för jobbet där det kan skriva transformerade data. I den här självstudien matar du ut och visualiserar data med Power BI.

1. Från Azure-portalen öppnar du fönsterrutan **Alla resurser** och Stream Analytics-jobbet *ASATutorial*.

2. I avsnittet **Jobbtopologi** i Stream Analytics-jobbfönstret väljer du alternativet **Utdata**.

3. Välj **+ Lägg till** > **Power BI**. Fyll sedan i formuläret med följande information och välj **Auktorisera**:

   |**Inställning**  |**Föreslaget värde**  |
   |---------|---------|
   |Utdataalias  |  MyPBIoutput  |
   |Namn på datauppsättning  |   ASAdataset  |
   |Tabellnamn |  ASATable  |

   ![Konfigurera Stream Analytics-utdata](media/stream-analytics-manage-job/configure-stream-analytics-output.png)

4. När du har valt **Auktorisera** visas ett popup-fönster och du ombeds ange autentiseringsuppgifter för att autentisera ditt Power BI-konto. När auktoriseringen är klar ska du **Spara** inställningarna.

## <a name="define-a-query-to-analyze-input-data"></a>Definiera en fråga för att analysera indata

Nästa steg är att skapa en transformering som analyserar data i realtid. Du definierar transformationsfrågan med [Stream Analytics-frågespråket](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference). Den fråga som används i den här självstudien identifierar bedrägliga samtal från telefondata.

I det här exemplet görs bedrägliga samtal från samma användare inom fem sekunder, men på olika platser. Samma användare kan till exempel inte legitimt ringa ett samtal från USA och Australien samtidigt. Så här definierar du transformationsfrågan för ditt Stream Analytics-jobb:

1. Från Azure-portalen, öppnar den **alla resurser** fönstret och navigera till den **ASATutorial** Stream Analytics-jobb som du skapade tidigare.

2. I avsnittet **Jobbtopologi** i Stream Analytics-jobbfönstret väljer du alternativet **Fråga**. I frågefönstret visas indata och utdata som är konfigurerade för jobbet, och du kan skapa en fråga för att transformera indataströmmen.

3. Ersätt den befintliga frågan i redigeraren med följande fråga, som utför en självkoppling med ett 5-sekundersintervall med samtalsdata:

   ```sql
   SELECT System.Timestamp AS WindowEnd, COUNT(*) AS FraudulentCalls
   INTO "MyPBIoutput"
   FROM "CallStream" CS1 TIMESTAMP BY CallRecTime
   JOIN "CallStream" CS2 TIMESTAMP BY CallRecTime
   ON CS1.CallingIMSI = CS2.CallingIMSI
   AND DATEDIFF(ss, CS1, CS2) BETWEEN 1 AND 5
   WHERE CS1.SwitchNum != CS2.SwitchNum
   GROUP BY TumblingWindow(Duration(second, 1))
   ```

   Om du vill söka efter bedrägliga samtal kan du upprätta en självkoppling för strömmande data baserat på värdet `CallRecTime`. Du kan sedan leta efter poster där den `CallingIMSI` värdet (det ursprungliga numret) är detsamma, men `SwitchNum` värdet (land/region för ursprung) är olika. När du använder en JOIN-åtgärd med strömmande data måste kopplingen tillhandahålla samma begränsningar för hur långt matchningsraderna kan delas upp i tid. Eftersom strömmande data är oändliga anges tidsgränserna för relationen inom **ON**-satsen för kopplingen med hjälp av funktionen [DATEDIFF](https://docs.microsoft.com/stream-analytics-query/datediff-azure-stream-analytics).

   Frågan är som en vanlig SQL-koppling förutom funktionen **DATEDIFF**. Den **DATEDIFF**-funktion som används i den här frågan är specifik för Streaming Analytics och måste visas i `ON...BETWEEN`-satsen.

4. **Spara** frågan.

   ![Definiera Stream Analytics-fråga i portalen](media/stream-analytics-manage-job/define-stream-analytics-query.png)

## <a name="test-your-query"></a>Testa frågan

Du kan testa en fråga från frågeredigeraren med hjälp av exempeldata. Kör följande steg för att testa frågan:

1. Kontrollera att TelcoGenerator-appen körs och producerar telefonsamtalsposter.

2. I fönsterrutan **Fråga** väljer du punkterna bredvid *CallStream*-indata och väljer sedan **Exempeldata från indata**.

3. Ställ in **Minuter** på 3 och välj **OK**. Tre minuters data samplas från indataströmmen, och du meddelas när exempeldata är klara. Du kan visa status för exemplet från meddelandefältet.

   Exempeldata lagras tillfälligt och är tillgängliga medan du har frågefönstret öppet. Om du stänger frågefönstret ignoreras exempeldata och du måste skapa en ny uppsättning exempeldata om du vill testa. Alternativt kan du använda en JSON-fil med exempeldata från [GitHub](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/telco.json) och sedan ladda upp den .JSON-filen och använda den som exempeldata för *CallStream*-indata.

   ![Illustration av hur indata samplas för Stream Analytics](media/stream-analytics-manage-job/sample-input-data-asa.png)

4. Välj **Testa** för att testa frågan. Du bör se följande resultat:

   ![Utdata från Stream Analytics frågetest](media/stream-analytics-manage-job/sample-test-output-restuls.png)

## <a name="start-the-job-and-visualize-output"></a>Starta jobbet och visualisera utdata

1. För att starta jobbet går du till fönsterrutan **Översikt** i jobbet och väljer **Starta**.

2. Välj **Nu** som starttid för jobbutdata och välj **Start**. Du kan se dess status i meddelandefältet.

3. När jobbet är klart går du till [Power BI](https://powerbi.com/) och loggar in med ditt arbets- eller skolkonto. Om Stream Analytics-jobbfrågan genererar resultat finns den *ASAdataset*-datamängd som du skapade under fliken **Datamängder**.

4. Från din Power BI-arbetsyta väljer du **+ Skapa** för att skapa en new instrumentpanel med namnet *Fraudulent Calls* (Bedrägliga samtal).

5. Välj **Lägg till panel** högst upp i fönstret. Välj sedan **Anpassade strömmande data** och **Nästa**. Välj **ASAdataset** under **Dina datamängder**. Välj **kort** från den **visualiseringstyp** listrutan och Lägg till **bedrägliga samtal** till **fält**. Välj **Nästa** för att ange ett namn på panelen och välj sedan **Applicera** för att skapa panelen.

   ![Skapa paneler på Power BI-instrumentpanelen](media/stream-analytics-manage-job/create-power-bi-dashboard-tiles.png)

6. Följ steg 5, med följande alternativ:
   * När du kommer till Visualiseringstyp väljer du Linjediagram.
   * Lägg till en axel och välj **windowend**.
   * Lägg till ett värde och välj **fraudulentcalls**.
   * För **Tidsfönster att visa** väljer du de senaste 10 minuterna.

7. Din instrumentpanel bör se ut som i exemplet nedan när båda panelerna har lagts till. Observera att om din händelsehubbs avsändarprogram och Streaming Analytics-programmet körs, Power BI-instrumentpanel regelbundet uppdateras i takt kommer nya data.

   ![Visa resultat på Power BI-instrumentpanelen](media/stream-analytics-manage-job/power-bi-results-dashboard.png)

## <a name="embedding-your-power-bi-dashboard-in-a-web-application"></a>Bädda in Power BI-instrumentpanel i ett webbprogram

I den här delen av självstudiekursen ska du använda ett exempel [ASP.NET](https://asp.net/) exempelwebbprogram som skapats av Power BI-teamet att bädda in instrumentpanelen. Mer information om att bädda in instrumentpaneler finns i artikeln [inbäddning med Power BI](https://docs.microsoft.com/power-bi/developer/embedding).

För att konfigurera programmet går du till GitHub-lagringsplatsen [PowerBI-Developer-Samples](https://github.com/Microsoft/PowerBI-Developer-Samples) och följer instruktionerna under avsnittet om **användarägda data** (använd URL:erna för omdirigering och startsida i underavsnittet **integrate-dashboard-web-app**). Eftersom vi använder exemplet med instrumentpanelen ska du använda exempelkoden **integrate-dashboard-web-app** som finns på [GitHub-lagringsplatsen](https://github.com/Microsoft/PowerBI-Developer-Samples/tree/master/User%20Owns%20Data/integrate-dashboard-web-app).
När du har fått igång programmet i webbläsaren följer du anvisningarna för att bädda in instrumentpanelen du skapade tidigare på webbsidan:

1. Välj **logga in på Power BI**, vilket ger programmet åtkomst till instrumentpaneler i Power BI-kontot.

2. Välj knappen **Hämta instrumentpaneler** som visar kontots instrumentpaneler i en tabell. Leta reda på namnet på instrumentpanelen du skapade tidigare **powerbi-embedded-dashboard** och kopiera motsvarande **EmbedUrl**.

3. Slutligen ska du klistra in **EmbedUrl** i motsvarande textfält och välja **Bädda in instrumentpanel**. Nu kan du visa samma instrumentpanel inbäddad i ett webbprogram.

## <a name="next-steps"></a>Nästa steg

I den här självstudien skapade du ett enkelt Stream Analytics-jobb, analyserade inkommande data och presenterade resultatet på en Power BI-instrumentpanel. Om du vill veta mer om Stream Analytics-jobb kan du fortsätta till nästa självstudie:

> [!div class="nextstepaction"]
> [Köra Azure Functions från Azure Stream Analytics-jobb](stream-analytics-with-azure-functions.md)
