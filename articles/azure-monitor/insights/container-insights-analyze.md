---
title: Övervaka prestanda för AKS-kluster med Azure Monitor för behållare | Microsoft Docs
description: Den här artikeln beskrivs hur du kan visa och analysera prestanda och loggar data med Azure Monitor för behållare.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/12/2019
ms.author: magoedte
ms.openlocfilehash: a370dcb349b61f3dda544d9c5a2030b6789e34c4
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67075402"
---
# <a name="understand-aks-cluster-performance-with-azure-monitor-for-containers"></a>Förstå prestanda för AKS-kluster med Azure Monitor för behållare 
Med Azure Monitor för behållare kan använda du prestandadiagram och hälsostatus för att övervaka arbetsbelastningen för dina Azure Kubernetes Service (AKS) kluster ur två perspektiv, direkt från ett AKS-kluster eller alla AKS-kluster i en prenumeration från Azure Övervaka. Visa Azure Container Instances (ACI) är också möjligt när du övervakar ett specifikt AKS-kluster.

Den här artikeln hjälper dig att förstå upplevelse mellan två perspektiv och hur den hjälper dig att snabbt utvärdera, undersöka och lösa problem som identifieras.

Information om hur du aktiverar Azure Monitor för behållare finns i [publicera Azure Monitor för behållare](container-insights-onboard.md).

Azure Monitor innehåller en vy för flera kluster som visar hälsotillståndet för alla övervakade AKS-kluster som kör Linux och Windows Server 2019 distribueras mellan resursgrupper i dina prenumerationer.  Den visar AKS-kluster identifieras som inte övervakas av lösningen. Du kan omedelbart förstår klusterhälsa och härifrån kan du öka detaljnivån till sidan noden och controller prestanda eller navigera för att se prestandadiagram för klustret.  AKS-kluster identifieras och identifieras som oövervakade kan aktivera du övervakning för klustret när som helst.  

De viktigaste skillnaderna övervaka en Windows Server-kluster med Azure Monitor för behållare jämfört med ett Linux-kluster är följande:

- Minne RSS-mått är inte tillgänglig för Windows-nod och behållare 
- Information om disken lagringskapacitet är inte tillgänglig för Windows-noder
- Live loggar support är tillgänglig med undantag för Windows-behållarloggarna.
- Endast pod miljöer övervakas, inte Docker-miljöer.
- Med förhandsversionen kan högst 30 Windows Server-behållare stöds. Den här begränsningen gäller inte för Linux-behållare.  

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal
Logga in på [Azure Portal](https://portal.azure.com). 

## <a name="multi-cluster-view-from-azure-monitor"></a>Visa flera kluster från Azure Monitor 
Om du vill visa alla AKS-kluster som distribueras hälsostatus, Välj **övervakaren** från det vänstra fönstret i Azure-portalen.  Under den **Insights** väljer **behållare**.  

![Exempel på Azure Monitor flera klusterinstrumentpanel](./media/container-insights-analyze/azmon-containers-multiview.png)

På den **övervakas kluster** fliken du kan lära dig följande:

1. Hur många kluster är i ett kritiskt eller dåligt tillstånd, jämfört med hur många är felfri eller inte reporting (kallas ett okänt tillstånd)?
2. Är alla mina [Azure Kubernetes-motor (AKS-motor)](https://github.com/Azure/aks-engine) distributioner felfria?
3. Hur många noder, användare och system poddar distribueras per kluster?
4. Hur mycket diskutrymme som är tillgängligt och finns det ett problem med kapacitet?

Den hälsotillstånd statusen som ingår är: 

* **Felfria** – inga problem har identifierats för den virtuella datorn och den fungerar som krävs. 
* **Kritiska** – en eller flera kritiska problem har identifierats som behöver åtgärdas om du vill återställa normal Användningsstatus som förväntat.
* **Varning** – ett eller flera problem har identifierats som behöver åtgärdas eller hälsostatus kan bli kritiska.
* **Okänd** – om tjänsten inte kunde upprätta en anslutning med noden eller pod, att status ändras till ett okänt tillstånd.
* **Det gick inte att hitta** – antingen arbetsytan, resursgruppen eller prenumerationen som innehåller arbetsytan för den här lösningen har tagits bort.
* **Obehörig** -användaren har inte behörighet att läsa data i arbetsytan.
* **Fel** -fel uppstod vid försök att läsa data från arbetsytan.
* **Felkonfigurerad** – Azure Monitor för behållare har inte konfigurerats korrekt i den angivna arbetsytan.
* **Inga data** -Data inte har rapporterat till arbetsytan under de senaste 30 minuterna.

Hälsotillståndet beräknar övergripande klusterstatus som *beräkning av sämsta* de tre tillstånd med ett undantag – om något av tre tillstånd är *okänd*, visar övergripande klustertillstånd **okänd**.  

Följande tabell innehåller en detaljerad analys av beräkningen Kontrollera hälsotillstånd för ett övervakade kluster för flera kluster-vyn.

| |Status |Tillgänglighet |  
|-------|-------|-----------------|  
|**Användaren Pod**| | |  
| |Felfri |100% |  
| |Varning |90 - 99% |  
| |Kritisk |< 90% |  
| |Okänt |Om du inte har rapporterat under senaste 30 minuterna |  
|**System Pod**| | |  
| |Felfri |100% |
| |Varning |Gäller inte |
| |Kritisk |< 100% |
| |Okänt |Om du inte har rapporterat under senaste 30 minuterna |
|**Node** | | |
| |Felfri |> 85% |
| |Varning |60 - 84% |
| |Kritisk |< 60% |
| |Okänt |Om du inte har rapporterat under senaste 30 minuterna |

Från listan över kluster, du kan öka detaljnivån ned till den **kluster** sidan genom att klicka på namnet på klustret, den **noder** prestanda sidan genom att klicka på samlade av noder i den **noder** kolumnen för den specifika kluster, eller öka detaljrikedomen för att den **domänkontrollanter** prestanda sidan genom att klicka på samlade av **användaren poddar** eller **System poddar**kolumn.   

## <a name="view-performance-directly-from-an-aks-cluster"></a>Visa prestanda direkt från ett AKS-kluster
Åtkomst till Azure Monitor för behållare som är tillgängliga direkt från ett AKS-kluster genom att välja **Insights** från det vänstra fönstret. Visa information om AKS-klustret är uppdelad i fyra perspektiv:

- Kluster
- Noder 
- Kontrollanter  
- Containrar

Standardsidan öppnas när du klickar på **Insights** är **kluster**, och innehåller fyra prestanda linjediagram visar viktiga prestandamått för ditt kluster. 

![Prestandadiagram för exempel på fliken kluster](./media/container-insights-analyze/containers-cluster-perfview.png)

Prestandadiagrammet visar fyra prestandamått:

- **Nodanvändningen CPU&nbsp;%** : En sammansatt perspektiv CPU-belastningen för hela klustret. Du kan filtrera resultaten för tidsintervallet genom att välja **genomsnittlig**, **Min**, **Max**, **50**, **90**, och **95: e** i percentiler Väljaren ovanför diagrammet, antingen separat eller kombinerat. 
- **Minnesanvändning för noden&nbsp;%** : En sammansatt perspektiv av minnesanvändningen för hela klustret. Du kan filtrera resultaten för tidsintervallet genom att välja **genomsnittlig**, **Min**, **Max**, **50**, **90**, och **95: e** i percentiler Väljaren ovanför diagrammet, antingen separat eller kombinerat. 
- **Antal noder**: Ett antal noder och status från Kubernetes. Status för noderna i klustret som representeras är *alla*, *redo*, och *inte klara* och kan filtreras individuellt eller kombineras i Väljaren ovanför diagrammet. 
- **Antal för aktiviteter pod**: Antal poddar och status från Kubernetes. Statusen för poddarna representeras är *alla*, *väntande*, *kör*, och *okänd* och kan filtreras individuellt eller kombineras i den väljare ovanför diagrammet. 

Du kan använda åt vänster och höger piltangenterna för att växla mellan varje datapunkt i diagrammet och upp/ned-nycklar för att gå igenom raderna: e percentilen. När du klickar på ikonen PIN-kod i det övre högra hörnet av något av diagrammen fäster det markerade diagrammet till senaste Azure-instrumentpanelen du visade senast. Du kan ändra storlek på och flytta diagrammet på instrumentpanelen. Markerat diagrammet från instrumentpanelen omdirigera dig till Azure Monitor för behållare och läsa in rätt omfattning och visa.

Azure Monitor för behållare stöder även Azure Monitor [måttutforskaren](../platform/metrics-getting-started.md), där du kan skapa egna diagram diagram, korrelera och undersöka trender och fästa på instrumentpaneler. Från metrics explorer kan du också använda de kriterier som du har angett för att visualisera dina mått som bas för en [mått baserade varningsregel](../platform/alerts-metric.md).  

## <a name="view-container-metrics-in-metrics-explorer"></a>Visa behållaren mått i metrics explorer
I metrics explorer, kan du visa aggregerade noden och pod mätvärden för resursutnyttjande från Azure Monitor för behållare. I följande tabell sammanfattas information som hjälper dig att använda diagram med mätvärden för att visualisera behållarmätvärden.

|Namnrymd | Mått |
|----------|--------|
| Insights.container/nodes | |
| | cpuUsageMillicores |
| | cpuUsagePercentage |
| | memoryRssBytes |
| | memoryRssPercentage |
| | memoryWorkingSetBytes |
| | memoryWorkingSetPercentage |
| | nodesCount |
| Insights.container/pods | |
| | PodCount |

Du kan använda [uppdelningen](../platform/metrics-charts.md#apply-splitting-to-a-chart) för ett mått att visa per dimension och visualisera hur olika delar av den jämfört med varandra. För en nod kan du segmentera diagrammet efter den *värden* dimension, och från en pod kan du segmentera det efter följande dimensioner:

* domänkontrollant
* Kubernetes-namnområde
* Nod
* Fas

## <a name="analyze-nodes-controllers-and-container-health"></a>Analysera noder, domänkontrollanter och hälsotillstånd för behållare

När du växlar till **noder**, **domänkontrollanter**, och **behållare** fliken automatiskt visas till höger på sidan är egenskapsrutan. Den visar egenskaperna för det objekt-markerats, inklusive etiketter som du definierar för att organisera Kubernetes-objekten. När en Linux-nod väljs det visar även under avsnittet **lokala diskkapacitet** tillgängligt diskutrymme och procent som används för varje disk som presenteras för noden. Klicka på den **>>** länkar i fönstret till view\hide fönstret. 

![Egenskapsrutan för exempel Kubernetes perspektiv](./media/container-insights-analyze/perspectives-preview-pane-01.png)

När du expanderar objekt inom hierarkin egenskaper fönstret uppdateringar baserat på de objekt som valts. Fönstret kan du också visa Kubernetes-händelser med fördefinierade loggsökningar genom att klicka på den **visa Kubernetes-händelseloggar** länken längst upp i fönstret. Mer information om hur du visar loggdata för Kubernetes finns i [söka loggarna för att analysera data](container-insights-log-search.md). Du kan se behållarloggarna och händelser i realtid när du granskar klusterresurser. Läs mer om den här funktionen och den konfiguration som krävs för att bevilja och styra åtkomsten [visa loggar realtid med Azure Monitor för behållare](container-insights-live-logs.md). 

Använd den **+ Lägg till Filter** alternativet högst upp på sidan om du vill filtrera resultatet för vyn av **Service**, **nod**, **Namespace**, eller  **Nodpool** och när du har valt vilka filter du sedan välja en av de värden som visas i den **Välj värde(n)** fält.  När filtret har konfigurerats kan tillämpas globalt medan du visar alla perspektiv AKS-klustret.  Formeln har endast stöd för likhetstecknet.  Du kan lägga till ytterligare filter på den första mallen för att ytterligare begränsa dina resultat.  Om du har angett ett filter genom att exempelvis **nod**, andra filtret skulle bara kan du välja **Service** eller **Namespace**.  

![Exempel med filtret för att begränsa resultaten](./media/container-insights-analyze/add-filter-option-01.png)

Ange ett filter i en flik fortsätter att tillämpas när du väljer en annan utan tas bort när du klickar på den **x** bredvid det angivna filtret.   

Växla till den **noder** fliken och raden hierarkin följer objektmodellen Kubernetes börjar med en nod i klustret. Expandera noden och du kan visa en eller flera poddar som körs på noden. Om flera behållare är grupperat till en pod, visas de som den sista raden i hierarkin. Du kan också visa hur många icke-pod relaterade arbetsbelastningar körs på värden om värden har processor eller minne.

![Exempel Kubernetes Node-hierarkin i prestandavyn](./media/container-insights-analyze/containers-nodes-view.png)

Windows Server-behållare som kör Windows Server 2019 OS visas efter alla Linux-baserade noder i listan. När du expanderar en Windows Server-nod kan du visa en eller flera poddar och behållare som körs på noden. När en nod är vald visar egenskapsrutan versionsinformation, exklusive agentinformation eftersom Windows Server-noder inte har någon agent installerad.  

![Exempel-Node-hierarkin med Windows Server-noder i listan](./media/container-insights-analyze/nodes-view-windows.png) 

Azure Container Instances virtuella noder kör Linux-operativsystem visas efter den sista noden i AKS klustret i listan.  När du har expanderat en virtuell ACI-nod kan visa du en eller flera poddar i ACI och behållare som körs på noden.  Mått är inte samlas in och rapporteras för noderna, endast poddar.

![Exempel-Node-hierarkin med Container Instances visas](./media/container-insights-analyze/nodes-view-aci.png)

Från en utökad noden kan granska du nedåt från pod eller behållare som körs på noden till den kontrollenheten för att visa prestandadata som visar den styrenheten. Klicka på värde under den **Controller** kolumnen för den specifika noden.   
![Exemplet nedåt från nod till controller i prestandavyn](./media/container-insights-analyze/drill-down-node-controller.png)

Du kan välja domänkontrollanter eller behållare överst på sidan och granska status och Resursanvändning för dessa objekt.  Om istället du vill granska minnesanvändningen, i den **mått** listrutan, väljer **minne RSS** eller **arbetsminne**. **Minne RSS** stöds endast för Kubernetes version 1.8 och senare. Annars kan du visa värden för **Min&nbsp; %**  som *NaN&nbsp;%* , vilket är ett värde av numeriska data som representerar en odefinierad eller inte går att representera värde. 

![Behållaren noder prestandavy](./media/container-insights-analyze/containers-node-metric-dropdown.png)

Som standard prestandadata baseras på de senaste sex timmarna, men du kan ändra i fönstret med hjälp av den **TimeRange** alternativet i det övre vänstra hörnet. Du kan också filtrera resultaten inom tidsintervallet genom att välja **genomsnittlig**, **Min**, **Max**, **50**, **90**, och **95: e** i Väljaren: e percentilen. 

![: E percentilen val för filtrering av data](./media/container-insights-analyze/containers-metric-percentile-filter.png)

När du för muspekaren över stapeldiagrammet under den **Trend** kolumnen visar varje fältet CPU eller minne användning, beroende på vilken enskilt mått har valts, inom 15 minuter exemplet. När du har valt trenddiagram via ett tangentbord, kan du använda tangenterna Alt + Page Up eller Alt + PgDn för att gå igenom varje stapel individuellt och få samma information som du skulle göra förklaring.

![Trend stapeldiagram hovra över exempel](./media/container-insights-analyze/containers-metric-trend-bar-01.png)    

I nästa exempel Observera för första i listan - nod *aks-nodepool1 -* , värdet för **behållare** är 9, vilket är en sammanslagning av det totala antalet behållare som distribueras.

![Sammanslagning av behållare per nod-exempel](./media/container-insights-analyze/containers-nodes-containerstotal.png)

Det kan du snabbt kan identifiera om du har en rätt balans mellan behållare mellan noderna i klustret. 

I följande tabell beskrivs den information som visas när du visar noder:

| Kolumn | Beskrivning | 
|--------|-------------|
| Namn | Namnet på värden. |
| Status | Kubernetes vy över nodstatusen. |
| Genomsnittlig&nbsp;%, Min&nbsp;%, Max&nbsp;%, 50&nbsp;%, 90&nbsp;% | Genomsnittlig nod procent baserat på: e percentilen under den valda perioden. |
| Genomsn, Min, Max, 50, 90 | Genomsnittlig noder faktiskt värde baserat på: e percentilen under den tidslängd som valts. Medelvärdet mäts från processor/minne gränsen för en nod. Det är genomsn värdet som rapporteras av värden för poddar och behållare. |
| Containrar | Antal behållare. |
| Drifttid | Representerar tid eftersom en nod startas eller startades om. |
| Kontrollanter | Endast för behållare och poddar. Den visar vilken domänkontrollant som den är bosatt i. Inte alla poddar är i en kontrollant så vissa kan visa **ej tillämpligt**. | 
| Trend genomsnittlig&nbsp;%, Min&nbsp;%, Max&nbsp;%, 50&nbsp;%, 90&nbsp;% | Stapeldiagram trend representerar genomsnittliga: e percentilen mått procentandelen kontrollanten. |

Välj i Väljaren, **styrenheter**.

![Välj domänkontrollanter vy](./media/container-insights-analyze/containers-controllers-tab.png)

Här kan du visa hälsotillståndet för prestanda för dina domänkontrollanter och ACI virtuell nod domänkontrollanter eller virtuell nod poddar som inte är anslutna till en domänkontrollant.

![< namn > domänkontrollanter prestandavy](./media/container-insights-analyze/containers-controllers-view.png)

Hierarki för raden som börjar med en domänkontrollant och när du har expanderat en domänkontrollant kan du visa en eller flera poddar.  Expandera pod och den sista raden visar behållaren grupperade poden. Från en utökad kontrollant du ökar detaljnivån till den noden som den körs på att visa prestandadata som filtreras för noden. ACI-poddar som inte är ansluten till en styrenhet visas senast i listan.

![Exempel-styrenheter hierarki med Container Instances poddar i listan](./media/container-insights-analyze/controllers-view-aci.png)

Klicka på värde under den **noden** kolumnen för den specifika styrenheten.   

![Exempel granska nedåt från nod till styrenhet i prestandavyn](./media/container-insights-analyze/drill-down-controller-node.png)

Informationen som visas när du visar domänkontrollanter beskrivs i följande tabell:

| Kolumn | Beskrivning | 
|--------|-------------|
| Namn | Namnet på kontrollanten.|
| Status | Insamling av status för behållarna när den har slutförts kör med status, till exempel *OK*, *Uppsagd*, *misslyckades* *stoppad*, eller *Pausats*. Om behållaren körs, men status har inte korrekt visas eller hämtades inte av agenten och har inte svarat mer än 30 minuter, statusen är *okänd*. Ytterligare information om statusikonen finns i tabellen nedan.|
| Genomsnittlig&nbsp;%, Min&nbsp;%, Max&nbsp;%, 50&nbsp;%, 90&nbsp;% | Rulla upp medelvärdet för den genomsnittliga procentandelen av varje entitet för valda mått- och: e percentilen. |
| Genomsn, Min, Max, 50, 90  | Samlar in den genomsnittliga CPU millicore eller minne över prestanda behållaren för den valda: e percentilen. Medelvärdet mäts från processor/minne gränsen för en pod. |
| Containrar | Totalt antal behållare för domänkontrollant eller pod. |
| Startar om | Summera för omstart räkningen från behållare. |
| Drifttid | Representerar tid efter att en behållare startades. |
| Node | Endast för behållare och poddar. Den visar vilken domänkontrollant som den är bosatt. | 
| Trend genomsnittlig&nbsp;%, Min&nbsp;%, Max&nbsp;%, 50&nbsp;%, 90&nbsp;%| Stapeldiagram trend representerar genomsnittliga: e percentilen mått för kontrollenheten. |

Ikonerna i statusfältet onlinestatus av behållarna:
 
| Ikon | Status | 
|--------|-------------|
| ![Klar körs statusikon](./media/container-insights-analyze/containers-ready-icon.png) | Kör (klar)|
| ![Väntar på eller pausat statusikon](./media/container-insights-analyze/containers-waiting-icon.png) | Väntar på eller pausats|
| ![Senast rapporterat kör statusikon](./media/container-insights-analyze/containers-grey-icon.png) | Senast rapporterat körs men har inte svarat mer än 30 minuter|
| ![Lyckad statusikon](./media/container-insights-analyze/containers-green-icon.png) | Har stoppats eller gick inte att stoppa|

Statusikonen visar ett antal baserat på din pod tillhandahåller. Den visar sämsta två tillstånd och när du hovrar över statusen visar statusen samlad från alla poddar i behållaren. Om det inte finns ett färdigt tillstånd, statusvärdet visar **(0)** . 

Välj i Väljaren, **behållare**.

![Välj behållare vy](./media/container-insights-analyze/containers-containers-tab.png)

Här kan du visa hälsotillståndet för prestanda för dina Azure Kubernetes och Azure Container Instances-behållare.  

![< namn > domänkontrollanter prestandavy](./media/container-insights-analyze/containers-containers-view.png)

Från en behållare kan du öka detaljnivån till en pod eller noden för att visa prestandadata som filtreras för objektet. Klicka på värde under den **Pod** eller **noden** kolumnen för den specifika behållaren.   

![Exempel granska nedåt från nod till styrenhet i prestandavyn](./media/container-insights-analyze/drill-down-controller-node.png)

I följande tabell beskrivs den information som visas när du visar behållare:

| Kolumn | Beskrivning | 
|--------|-------------|
| Namn | Namnet på kontrollanten.|
| Status | Status för behållare, om sådana. Ytterligare information om statusikonen finns i nästa tabell.|
| Genomsnittlig&nbsp;%, Min&nbsp;%, Max&nbsp;%, 50&nbsp;%, 90&nbsp;% | Insamling av den genomsnittliga procentandelen av varje entitet för valda mått- och: e percentilen. |
| Genomsn, Min, Max, 50, 90  | Insamling av Genomsnittlig CPU millicore eller minne prestanda för behållaren för den valda: e percentilen. Medelvärdet mäts från processor/minne gränsen för en pod. |
| Pod | Behållaren där poden finns.| 
| Node |  Noden där behållaren finns. | 
| Startar om | Representerar tid efter att en behållare startades. |
| Drifttid | Representerar tid eftersom en behållare startades eller startas om. |
| Trend genomsnittlig&nbsp;%, Min&nbsp;%, Max&nbsp;%, 50&nbsp;%, 90&nbsp;% | Stapeldiagram trend representerar genomsnittliga: e percentilen mått procentandelen av behållaren. |

Ikonen i statusfältet anger online status för poddar, enligt beskrivningen i följande tabell:
 
| Ikon | Status |  
|--------|-------------|  
| ![Klar körs statusikon](./media/container-insights-analyze/containers-ready-icon.png) | Kör (klar)|  
| ![Väntar på eller pausat statusikon](./media/container-insights-analyze/containers-waiting-icon.png) | Väntar på eller pausats|  
| ![Senast rapporterat kör statusikon](./media/container-insights-analyze/containers-grey-icon.png) | Senast rapporterat körs men har inte svarat under mer än 30 minuterna|  
| ![Avslutade statusikon](./media/container-insights-analyze/containers-terminated-icon.png) | Har stoppats eller gick inte att stoppa|  
| ![Misslyckade statusikon](./media/container-insights-analyze/containers-failed-icon.png) | Felaktigt tillstånd |  

## <a name="disk-capacity-workbook"></a>Disk kapacitet arbetsboken
Arbetsböcker kombinera text, [logga frågor](../log-query/query-language.md), [mått](../platform/data-platform-metrics.md), och parametrar i interaktiva rapporter. Arbetsböcker kan redigeras av andra gruppmedlemmar som har åtkomst till samma Azure-resurser.

Azure Monitor för behållare som innehåller en arbetsbok för att komma igång, **Disk kapacitet**.  Den här arbetsboken visas interaktiva disk användning diagram för varje disk som presenteras till noden i en behållare av följande perspektiv:

- % Diskanvändning för alla diskar
- Ledigt diskutrymme för alla diskar
- En tabell som visar för varje disk noder, dess % använt utrymme, används trenden för % utrymme, ledigt diskutrymme (GiB) och trend ledigt diskutrymme (GiB). När du väljer en rad i tabellen % använt utrymme och ledigt diskutrymme (GiB) som anges nedan 

Du har åtkomst till den här arbetsboken genom att välja **Disk kapacitet** från den **visa arbetsböcker** listrutan.  

![Visa arbetsböcker nedrullningsbar listruta](./media/container-insights-analyze/view-workbooks-dropdown-list.png)


## <a name="next-steps"></a>Nästa steg
- Granska den [skapa aviseringar med Azure Monitor för behållare](container-insights-alerts.md) och lär dig att skapa aviseringar för hög användning av processor och minne för dina DevOps eller operativa processer och procedurer. 
- Visa [logga fråga exempel](container-insights-log-search.md#search-logs-to-analyze-data) att se fördefinierade frågor och exempel för att utvärdera eller anpassa för aviseringar, visualisera och analysera dina kluster.
