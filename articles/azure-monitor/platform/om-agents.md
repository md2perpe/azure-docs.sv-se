---
title: Ansluta Operations Manager till Azure Monitor | Microsoft Docs
description: För att underhålla din befintliga investering i System Center Operations Manager och använda utökade funktioner med Log Analytics, kan du integrera Operations Manager med ditt arbetsområde.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: 245ef71e-15a2-4be8-81a1-60101ee2f6e6
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/22/2019
ms.author: magoedte
ms.openlocfilehash: 336a9d9c76114920e92de2000152e500f7dce46f
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67445307"
---
# <a name="connect-operations-manager-to-azure-monitor"></a>Ansluta Operations Manager till Azure Monitor

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

Att underhålla din investering i [System Center Operations Manager](https://docs.microsoft.com/system-center/scom/key-concepts?view=sc-om-1807) och använder utökade funktioner med Azure Monitor, du kan integrera Operations Manager med Log Analytics-arbetsytan. På så sätt kan du utnyttja möjligheter i loggarna i Azure Monitor medan du använder Operations Manager för att:

* Övervaka hälsotillståndet för dina IT-tjänster med Operations Manager
* Underhålla integrationen med dina ITSM-lösningar med stöd för hantering av incidenter och problem
* Hantera livscykeln för agenter som distribuerats lokalt och till virtuella IaaS-datorer i det offentliga molnet som du övervakar med Operations Manager

Integrera med System Center Operations Manager lägger till värde till din strategi för åtgärder av tjänsten med hjälp av den hastighet och effektivitet för Azure Monitor i samla in, lagra och analysera loggdata från Operations Manager. Azure Monitor log frågar hjälp med att korrelera och arbete för att identifiera fel på problem och visa upprepningar som stöd för din befintliga problem-hanteringen. Flexibiliteten i motorn för frågor kan du undersöka prestanda, händelser och avisering data med omfattande instrumentpaneler och rapporteringsfunktioner att exponera dessa data på ett meningsfullt sätt visar styrkan Azure Monitor ger i complimenting Operations Manager.

Agenter som rapporterar till Operations Manager-hanteringsgrupp samla in data från dina servrar baserat på den [Log Analytics-datakällor](agent-data-sources.md) och lösningar som du har aktiverat i din arbetsyta. Beroende på de lösningar som har aktiverat sina data antingen skickas direkt från en Operations Manager-hanteringsserver till tjänsten eller på grund av mängden data som samlas in av systemet hanteras med agent, skickas direkt från agenten till Log Analytics-arbetsytan. Hanteringsservern vidarebefordrar data direkt till tjänsten, de skrivs aldrig till den operativa databasen eller informationslagerdatabasen. När en hanteringsserver förlorar anslutningen till Azure Monitor, cachelagrar den data lokalt tills kommunikation nytt har upprättats. Om hanteringsservern är offline på grund av planerat underhåll eller oplanerade avbrott, fortsätter anslutningen med Azure Monitor med en annan hanteringsserver i hanteringsgruppen.  

I följande diagram visas anslutningen mellan hanteringsservrarna och agenterna i en hanteringsgrupp för System Center Operations Manager och Azure Monitor, inklusive riktning och portar.   

![oms-operations-manager-integration-diagram](./media/om-agents/oms-operations-manager-connection.png)

Om din IT-säkerhetsprinciper inte tillåter att datorer i nätverket för att ansluta till Internet, kan hanteringsservrar konfigureras för att ansluta till Log Analytics-gatewayen till mottagning av konfigurationsinformation och skicka insamlade data, beroende på lösningarna aktiverad. Mer information och anvisningar om hur du konfigurerar din Operations Manager-hanteringsgrupp att kommunicera via en gateway för Log Analytics till Azure Monitor kan du se [ansluta datorer till Azure Monitor med Log Analytics-gateway](../../azure-monitor/platform/gateway.md).  

## <a name="prerequisites"></a>Förutsättningar

Granska följande krav innan du börjar.

* Azure Monitor har endast stöd för System Center Operations Manager 2016 eller senare, Operations Manager 2012 SP1 UR6 eller senare, och Operations Manager 2012 R2 UR2 eller senare. Stöd för proxy har lagts till i Operations Manager 2012 SP1 UR7 och Operations Manager 2012 R2 UR3.
* Integrera System Center Operations Manager 2016 med molnet för amerikanska myndigheter kräver ett uppdaterat Advisor management pack som ingår med Samlad uppdatering 2 eller senare. System Center Operations Manager 2012 R2 kräver ett uppdaterat Advisor management pack som ingår med Samlad uppdatering 3 eller senare.
* Alla Operations Manager-agenter måste uppfylla minimikraven. Kontrollera att agenterna är på den lägsta uppdateringen, annars Windows agentkommunikationen kan misslyckas och genererar fel i händelseloggen för Operations Manager.
* En Log Analytics-arbetsyta. Mer information, [Log Analytics arbetsyta-översikt](../../azure-monitor/platform/manage-access.md?toc=/azure/azure-monitor/toc.json).   
* Du kan autentisera till Azure med ett konto som är medlem i den [Log Analytics Contributor rollen](../../azure-monitor/platform/manage-access.md#manage-accounts-and-users).

* Regioner som stöds – endast följande Azure-regioner stöds av System Center Operations Manager för att ansluta till en Log Analytics-arbetsyta:
    - Västra centrala USA
    - Sydöstra Australien
    - Västra Europa
    - East US
    - Sydostasien
    - Östra Japan
    - Storbritannien, södra
    - Indien, centrala
    - Centrala Kanada
    - Västra USA 2

>[!NOTE]
>Ändringar i Azure API: er kommer att kunder inte kan konfigurera integrering mellan sina hanteringsgrupp och Azure Monitor för första gången. För kunder som redan har integrerat deras hanteringsgruppen med tjänsten kan påverkas du inte om du inte behöver konfigurera om din befintliga anslutning.  
>Ett nytt management pack har släppts för följande versioner av Operations Manager:
>  
>* Ladda ned hanteringspaketet från för System Center Operations Manager 1801 [här](https://www.microsoft.com/download/details.aspx?id=57173)  
>* System Center 2016 – Operations Manager kan du hämta hanteringspaketet från [här](https://www.microsoft.com/download/details.aspx?id=57172)  
>* För System Center Operations Manager 2012 R2 kan du ladda ned hanteringspaketet från [här](https://www.microsoft.com/download/details.aspx?id=57171)  
>
>Den här uppdateringen för management pack gäller inte för System Center Operations Manager 1807, vilket är en uppdaterad version från version 1801 och inte en fullständig version av produkten.   

### <a name="network"></a>Nätverk

Informationen nedan proxy och brandvägg konfigurationsuppgifter som efterfrågas för Operations Manager-agenten, hanteringsservrar och Operations-konsolen att kommunicera med Azure Monitor. Trafik från varje komponent är utgående trafik från ditt nätverk till Azure Monitor.   

|Resurs | Portnummer| Kringgå HTTPS-kontroll|  
|---------|------|-----------------------|  
|**Agent**|||  
|\*.ods.opinsights.azure.com| 443 |Ja|  
|\*.oms.opinsights.azure.com| 443|Ja|  
|\*.blob.core.windows.net| 443|Ja|  
|\*.azure-automation.net| 443|Ja|  
|**Hanteringsserver**|||  
|\*.service.opinsights.azure.com| 443||  
|\*.blob.core.windows.net| 443| Ja|  
|\*.ods.opinsights.azure.com| 443| Ja|  
|*.azure-automation.net | 443| Ja|  
|**Operations Manager-konsolen till Azure Monitor**|||  
|service.systemcenteradvisor.com| 443||  
|\*.service.opinsights.azure.com| 443||  
|\*.live.com| 80 och 443||  
|\*.microsoft.com| 80 och 443||  
|\*.microsoftonline.com| 80 och 443||  
|\*.mms.microsoft.com| 80 och 443||  
|login.windows.net| 80 och 443||  
|portal.loganalytics.io| 80 och 443||
|api.loganalytics.io| 80 och 443||
|docs.loganalytics.io| 80 och 443||  

### <a name="tls-12-protocol"></a>TLS 1.2-protokollet

Om du vill se till att skydda data under överföring till Azure Monitor, rekommenderar vi starkt att du kan konfigurera gruppen agent och en hanteringsserver för att kunna använda minst Transport Layer Security (TLS) 1.2. Äldre versioner av TLS/Secure Sockets Layer (SSL) har påträffats sårbara och de fungerar fortfarande för närvarande för att tillåta bakåtkompatibilitet kompatibilitet, de arbetar **rekommenderas inte**. Mer information [skickar data på ett säkert sätt med hjälp av TLS 1.2](../../azure-monitor/platform/data-security.md#sending-data-securely-using-tls-12).

## <a name="connecting-operations-manager-to-azure-monitor"></a>Ansluta Operations Manager till Azure Monitor

Utför följande steg för att konfigurera hanteringsgruppen för Operations Manager för att ansluta till en av dina Log Analytics-arbetsytor.

Vid första registreringen av din Operations Manager-hanteringsgrupp med en arbetsyta för Log Analytics finns inte alternativet för att ange proxykonfigurationen för hanteringsgruppen i driftkonsolen.  Hanteringsgruppen måste registrerats med tjänsten innan det här alternativet är tillgängligt.  För att kringgå detta kan behöva du uppdatera proxykonfigurationen system med hjälp av Netsh i systemet kör driftskonsolen från att konfigurera integration och alla hanteringsservrar i hanteringsgruppen.  

1. Öppna en upphöjd kommandotolk.
   a. Gå till **starta** och skriv **cmd**.
   b. Högerklicka på **kommandotolk** och välj Kör som administratör **.
1. Ange följande kommando och tryck på **Enter**:

    `netsh winhttp set proxy <proxy>:<port>`

När du har slutfört följande steg för att integrera med Azure Monitor kan du ta bort konfigurationen genom att köra `netsh winhttp reset proxy` och sedan använda den **konfigurera proxyservern** alternativet i Operations-konsolen för att ange proxy- eller Log Analytics gateway-servern.

1. Välj arbetsytan **Administration** i Operations Manager-konsolen.
1. Expandera noden Operations Management Suite och klicka på **Anslutning**.
1. Klicka på länken för att **registrera till Operations Management Suite**.
1. På den **guiden Operations Management Suite Onboarding: Autentisering** sidan, anger du e-postadress eller telefonnummer och lösenordet för administratörskontot som är associerad med OMS-prenumerationen och på **logga in**.

   >[!NOTE]
   >Operations Management Suite-namnet har tagits bort.

1. När du har autentiserats, på den **guiden Operations Management Suite Onboarding: Välj arbetsyta** sidan, uppmanas du för att välja din Azure-klient, prenumeration och Log Analytics-arbetsyta. Om du har mer än en arbetsyta väljer du arbetsytan som du vill registrera med Operations Manager-hanteringsgruppen från listrutan och klickar på **Nästa**.

   > [!NOTE]
   > Operations Manager stöder bara en Log Analytics-arbetsyta i taget. Anslutningen och de datorer som har registrerats i Azure Monitor med den föregående arbetsytan tas bort från Azure Monitor.
   >
   >
1. På den **guiden Operations Management Suite Onboarding: Sammanfattning** , bekräfta dina inställningar och om de är korrekta, klickar du på **skapa**.
1. På den **guiden Operations Management Suite Onboarding: Slutför** klickar du på **Stäng**.

### <a name="add-agent-managed-computers"></a>Lägg till datorer som hanteras med agent

När du har konfigurerat integrering med Log Analytics-arbetsytan, den bara upprättar en anslutning till tjänsten, inga data har samlats in från agenter som rapporterar till hanteringsgruppen. Detta sker inte fram när du har konfigurerat vilka specifika agenthanterade datorer samla in loggdata för Azure Monitor. Du kan antingen välja datorobjekt individuellt eller välja en grupp som innehåller Windows-datorobjekt. Du kan inte välja en grupp som innehåller instanser av en annan klass, till exempel logiska diskar eller SQL-databaser.

1. Öppna Operations Manager-konsolen och välj arbetsytan **Administration**.
1. Expandera noden Operations Management Suite och klicka på **Anslutning**.
1. Klicka på länken **Lägg till en dator/grupp** under rubriken Åtgärder på höger sida av fönstret.
1. I dialogrutan **Datorsökning** kan du söka efter datorer eller grupper som övervakas av Operations Manager. Välj datorer eller grupper för att publicera till Azure Monitor, klicka på **Lägg till**, och klicka sedan på **OK**.

Du kan visa datorer och grupper som konfigurerats för att samla in data från noden för hanterade datorer under Operations Management Suite i arbetsytan **Administration** i Operations-konsolen. Härifrån kan du lägga till eller ta bort datorer och grupper efter behov.

### <a name="configure-proxy-settings-in-the-operations-console"></a>Konfigurera proxyinställningar i Operations-konsolen

Utför följande steg om en intern proxyserver mellan hanteringsgruppen och Azure Monitor. De här inställningarna hanteras från hanteringsgruppen centralt och distribueras till agenthanterade system som ingår i omfånget att samla in loggdata för Azure Monitor.  Detta är fördelaktigt när vissa lösningar förbigår hanteringsservern och skickar data direkt till tjänsten.

1. Öppna Operations Manager-konsolen och välj arbetsytan **Administration**.
1. Expandera Operations Management Suite och klicka sedan på **Anslutningar**.
1. I vyn OMS-anslutning klickar du på **Konfigurera proxyserver**.
1. På **guiden Operations Management Suite: Proxyserver** väljer **använder en proxyserver för att få åtkomst till Operations Management Suite**, och ange sedan Webbadressen med portnumret, till exempel http://corpproxy:80 och klicka sedan på **Slutför** .

Om proxyservern kräver autentisering, utför du följande steg för att konfigurera autentiseringsuppgifter och inställningar som måste spridas till hanterade datorer som rapporterar till Azure Monitor i hanteringsgruppen.

1. Öppna Operations Manager-konsolen och välj arbetsytan **Administration**.
1. Under **Kör som-konfiguration** väljer du **Profiler**.
1. Öppna profilen **Kör som-profilproxy för System Center Advisor**.
1. I guiden Kör som-profil klickar du på Lägg till för att använda ett Kör som-konto. Du kan skapa ett [Kör som-konto](https://technet.microsoft.com/library/hh321655.aspx) eller använda ett befintligt konto. Det här kontot måste ha tillräcklig behörighet för att kunna passera genom proxyservern.
1. Ange vilket konto som ska hanteras genom att välja **En vald klass, grupp eller objekt**, klicka på **Välj...** och sedan klicka på **Grupp...** för att öppna **Gruppsökning**.
1. Sök efter och välj **Övervakning av servergrupp i Microsoft System Center Advisor**. Klicka på **OK** när du har valt grupp för att stänga rutan **Gruppsökning**.
1. Klicka på **OK** för att stänga rutan **Lägg till ett Kör som-konto**.
1. Klicka på **Spara** för att slutföra guiden och spara dina ändringar.

När anslutningen har skapats och du konfigurerar vilka agenter att samla in och rapportera loggdata till Azure Monitor, tillämpas följande konfiguration i hanteringsgruppen, inte nödvändigtvis i ordning:

* Kör som-kontot **Microsoft.SystemCenter.Advisor.RunAsAccount.Certificate** skapas. Det är associerat med Kör som-profilen **Microsoft System Center Advisor Run As Profile Blob** (Microsoft System Center Advisor Kör som-profilblob) och har två klasser som mål – **insamlingsserver** och **Operations Manager-hanteringsgrupp** .
* Två anslutningsprogram skapas.  Först heter **Microsoft.SystemCenter.Advisor.DataConnector** och konfigureras automatiskt med en prenumeration som vidarebefordrar alla varningar som genereras av instanser av alla klasser i hanteringsgruppen till Azure Monitor. Den andra kopplingen är **Advisor Connector**, som ansvarar för att kommunicera med Azure Monitor och dela data.
* Agenter och grupper som du har valt för att samla in data i hanteringsgruppen läggs till i **Övervakning av servergrupp i Microsoft System Center Advisor**.

## <a name="management-pack-updates"></a>Uppdateringar av hanteringspaket

När konfigurationen är klar upprättar en anslutning med Azure Monitor i Operations Manager-hanteringsgrupp. Hanteringsservern synkroniserar med webbtjänsten och tar emot uppdaterad konfigurationsinformation i form av hanteringspaket för de lösningar du har aktiverat som integreras med Operations Manager. Operations Manager söker efter uppdateringar för dessa hanteringspaket och automatiskt hämta och importerar dem när de är tillgängliga. Det är särskilt två regler som styr detta:

* **Microsoft.SystemCenter.Advisor.MPUpdate** -uppdaterar de grundläggande Azure Monitor-hanteringspaket. Kör som standard var 12:e timme.
* **Microsoft.SystemCenter.Advisor.Core.GetIntelligencePacksRule** – uppdaterar hanteringspaketen för lösningen som aktiverats i din arbetsyta. Körs som standard var 5:e minut.

Du kan åsidosätta dessa två regler för att antingen förhindra automatisk hämtning genom att inaktivera dem eller ändra frekvensen för hur ofta hanteringsservern synkroniseras med Azure Monitorto avgöra om ett nytt hanteringspaket är tillgängliga och ska laddas ned. Följ [de här stegen för att åsidosätta en regel eller övervakare](https://technet.microsoft.com/library/hh212869.aspx) för att ändra parametern **Frekvens** (med ett värde i sekunder) för att justera synkroniseringsschemat eller ändra parametern **Aktiverad** för att avaktivera reglerna. Rikta åsidosättningarna mot alla objekt i klassen Operations Manager Management Group.

Om du vill fortsätta efter din befintliga Ändra kontroll process för att styra management pack-versioner i hanteringsgruppen för produktion, kan du inaktivera reglerna och aktivera dem vid specifika tidpunkter när uppdateringar är tillåtna. Om du har en utvecklings- eller kvalitetskontrollshanteringsgrupp i din miljö och den är ansluten till Internet, kan du konfigurera hanteringsgruppen med Log Analytics-arbetsytan för att stödja det här scenariot. På så sätt kan du granska och utvärdera iterativ versioner av Azure Monitor-hanteringspaket innan du publicerar dem till hanteringsgruppen för produktion.

## <a name="switch-an-operations-manager-group-to-a-new-log-analytics-workspace"></a>Växla en Operations Manager-grupp till en ny Log Analytics-arbetsyta

1. Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).
1. I Azure Portal klickar du på knappen **Fler tjänster** längst upp till vänster. I listan över resurser skriver du **Log Analytics**. När du börjar skriva filtreras listan baserat på det du skriver. Välj **Log Analytics** och skapa en arbetsyta.  
1. Öppna Operations Manager-konsolen med ett konto med rollen Operations Manager-administratör och välj arbetsytan **Administration**.
1. Expandera Log Analytics och markera **anslutningar**.
1. Välj länken för att **konfigurera om Operation Management Suite** i mitten av fönstret.
1. Följ den **guiden för Log Analytics Onboarding** och ange den e-postadress eller telefonnummer tal och lösenordet för administratörskontot som är associerad med den nya Log Analytics-arbetsytan.

   > [!NOTE]
   > Den **guiden Operations Management Suite Onboarding: Välj arbetsyta** visar den befintliga arbetsytan som används.
   >
   >

## <a name="validate-operations-manager-integration-with-azure-monitor"></a>Verifiera Operations Manager-integrering med Azure Monitor

Det finns några olika sätt som du kan kontrollera att Azure Monitor för att integrering av Operations Manager har lyckats.

### <a name="to-confirm-integration-from-the-azure-portal"></a>Bekräfta integrationen från Azure Portal

1. I Azure Portal klickar du på knappen **Fler tjänster** längst upp till vänster. I listan över resurser skriver du **Log Analytics**. När du börjar skriva filtreras listan baserat på det du skriver.
1. Välj arbetsyta i listan över Log Analytics-arbetsytor.  
1. Välj **Avancerade inställningar**, **Anslutna källor**, och sedan **System Center**.
1. Du bör se namnet på hanteringsgruppen tillsammans med antalet agenter och status när data togs emot senast i tabellen i avsnittet System Center Operations Manager.

   ![oms-settings-connectedsources](./media/om-agents/oms-settings-connectedsources.png)

### <a name="to-confirm-integration-from-the-operations-console"></a>Bekräfta integrationen från Operations-konsolen

1. Öppna Operations Manager-konsolen och välj arbetsytan **Administration**.
1. Välj **Hanteringspaket** och i textrutan för att **söka** skriver du **Advisor** eller **Intelligence**.
1. Beroende på vilka lösningar du har aktiverat kan du se motsvarande hanteringspaket i sökresultaten.  Om du har aktiverat lösningen för aviseringshantering visas hanteringspaket Microsoft System Center Advisor aviseringshantering i listan.
1. Från vyn **Övervakning** navigerar du till vyn **Operations Management Suite\Hälsotillstånd**.  Välj en hanteringsserver under fönstret för **hanteringsservertillstånd** och i fönstret **Detaljvy** kontrollerar du att värdet för egenskapen **Authentication service URI** (URI för autentiseringstjänst) matchar Log Analytics-arbetsytans ID.

   ![oms-opsmgr-mg-authsvcuri-property-ms](./media/om-agents/oms-opsmgr-mg-authsvcuri-property-ms.png)

## <a name="remove-integration-with-azure-monitor"></a>Ta bort integrering med Azure Monitor

Flera steg måste utföras för att ta bort anslutningen och konfigurationen korrekt i hanteringsgruppen när du inte längre behöver integreringen mellan din Operations Manager-hanteringsgrupp och Log Analytics-arbetsytan. Följande procedur har du uppdatera Log Analytics-arbetsytan genom att ta bort referensen till din hanteringsgrupp, ta bort Azure Monitor-kopplingar och sedan ta bort hanteringspaket som stöder integration med tjänsten.  

Hanteringspaketen för lösningarna du har aktiverat som integrerar med Operations Manager och de hanteringspaket som krävs för att stödja integrering med Azure Monitor kan inte enkelt tas bort från hanteringsgruppen. Det beror på att en del av Azure Monitor-hanteringspaket har beroenden på andra relaterade hanteringspaket. För att ta bort hanteringspaket med beroenden i andra hanteringspaket laddar du ned skriptet [ för att ta bort hanteringspaket med ett beroende](https://gallery.technet.microsoft.com/scriptcenter/Script-to-remove-a-84f6873e) från TechNet Script Center.  

1. Öppna Operations Manager kommandotolken med ett konto som är medlem i rollen Operations Manager-administratörer.

    > [!WARNING]
    > Kontrollera att du inte har några anpassade hanteringspaket som innehåller ordet Advisor eller IntelligencePack i namnet innan du fortsätter. I annat fall kan du ta bort dem från hanteringsgruppen.
    >

1. Från kommandotolken skriver du: `Get-SCOMManagementPack -name "*Advisor*" | Remove-SCOMManagementPack -ErrorAction SilentlyContinue`
1. Skriv sedan `Get-SCOMManagementPack -name “*IntelligencePack*” | Remove-SCOMManagementPack -ErrorAction SilentlyContinue`
1. För att ta bort återstående hanteringspaket med beroenden till andra System Center Advisor-hanteringspaket använder du skriptet *RecursiveRemove.ps1* som du laddade ned innan från TechNet Script Center.  

    > [!NOTE]
    > Steg för att ta bort Advisor-hanteringspaketen med PowerShell tas inte bort Microsoft System Center Advisor eller Microsoft System Center Advisor internt hanteringspaket automatiskt.  Försök inte att ta bort dem.  
    >  

1. Öppna Operations-konsolen i Operations Manager med ett konto som har rollen Operations Manager-administratör.
1. Under **Administration** väljer du noden **Hanteringspaket** och i rutan för att **söka** skriver du **Advisor** och kontrollerar att följande hanteringspaket fortfarande importeras i hanteringsgruppen:

   * Microsoft System Center Advisor
   * Microsoft System Center Advisor Internal

1. I Azure-portalen klickar du på den **inställningar** panelen.
1. Välj **anslutna källor**.
1. Du bör se namnet på hanteringsgruppen som du vill ta bort från arbetsytan i tabellen i System Center Operations Manager-avsnittet. Under kolumnen **Senaste data** klickar du på **Ta bort**.  

    > [!NOTE]
    > Länken **Ta bort** blir inte tillgänglig förrän efter att den anslutna hanteringsgruppen känner av 14 dagar utan aktivitet.  
    >

1. Ett fönster visas där du uppmanas att bekräfta att du vill ta bort volymen.  Klicka på **Ja** för att gå vidare.

För att radera de två anslutningsprogrammen – Microsoft.SystemCenter.Advisor.DataConnector och Advisor Connector – sparar du PowerShell-skriptet nedan på datorn och kör det med följande exempel:

```
    .\OM2012_DeleteConnectors.ps1 “Advisor Connector” <ManagementServerName>
    .\OM2012_DeleteConnectors.ps1 “Microsoft.SystemCenter.Advisor.DataConnector” <ManagementServerName>
```

> [!NOTE]
> Den dator som du kör skriptet från (om det inte är en hanteringsserver) bör ha Operations Manager-kommandogränssnittet installerat, beroende på version av hanteringsgruppen.
>
>

```powershell
    param(
    [String] $connectorName,
    [String] $msName="localhost"
    )
    $mg = new-object Microsoft.EnterpriseManagement.ManagementGroup $msName
    $admin = $mg.GetConnectorFrameworkAdministration()
    ##########################################################################################
    # Configures a connector with the specified name.
    ##########################################################################################
    function New-Connector([String] $name)
    {
         $connectorForTest = $null;
         foreach($connector in $admin.GetMonitoringConnectors())
    {
    if($connectorName.Name -eq ${name})
    {
         $connectorForTest = Get-SCOMConnector -id $connector.id
    }
    }
    if ($connectorForTest -eq $null)
    {
         $testConnector = New-Object Microsoft.EnterpriseManagement.ConnectorFramework.ConnectorInfo
         $testConnector.Name = $name
         $testConnector.Description = "${name} Description"
         $testConnector.DiscoveryDataIsManaged = $false
         $connectorForTest = $admin.Setup($testConnector)
         $connectorForTest.Initialize();
    }
    return $connectorForTest
    }
    ##########################################################################################
    # Removes a connector with the specified name.
    ##########################################################################################
    function Remove-Connector([String] $name)
    {
        $testConnector = $null
        foreach($connector in $admin.GetMonitoringConnectors())
       {
        if($connector.Name -eq ${name})
       {
         $testConnector = Get-SCOMConnector -id $connector.id
       }
      }
     if ($testConnector -ne $null)
     {
        if($testConnector.Initialized)
     {
     foreach($alert in $testConnector.GetMonitoringAlerts())
     {
       $alert.ConnectorId = $null;
       $alert.Update("Delete Connector");
     }
     $testConnector.Uninitialize()
     }
     $connectorIdForTest = $admin.Cleanup($testConnector)
     }
    }
    ##########################################################################################
    # Delete a connector's Subscription
    ##########################################################################################
    function Delete-Subscription([String] $name)
    {
      foreach($testconnector in $admin.GetMonitoringConnectors())
      {
      if($testconnector.Name -eq $name)
      {
        $connector = Get-SCOMConnector -id $testconnector.id
      }
    }
    $subs = $admin.GetConnectorSubscriptions()
    foreach($sub in $subs)
    {
      if($sub.MonitoringConnectorId -eq $connector.id)
      {
        $admin.DeleteConnectorSubscription($admin.GetConnectorSubscription($sub.Id))
      }
     }
    }
    #New-Connector $connectorName
    write-host "Delete-Subscription"
    Delete-Subscription $connectorName
    write-host "Remove-Connector"
    Remove-Connector $connectorName
```

I framtiden om du planerar återansluter hanteringsgruppen till Log Analytics-arbetsytan, måste du återimportera den `Microsoft.SystemCenter.Advisor.Resources.\<Language>\.mpb` hanteringspaketfil. Beroende på vilken version av System Center Operations Manager som har distribuerats i din miljö kan du hitta filen här:

* På källmediet under mappen `\ManagementPacks` för System Center 2016 – Operations Manager och högre.
* Från den senaste uppdateringen som tillämpats på din hanteringsgrupp. För Operations Manager 2012, källmappen är `%ProgramFiles%\Microsoft System Center 2012\Operations Manager\Server\Management Packs for Update Rollups` och för 2012 R2, finns den i `System Center 2012 R2\Operations Manager\Server\Management Packs for Update Rollups`.

## <a name="next-steps"></a>Nästa steg

Om du vill lägga till funktioner och samla in data, se [lägga till Azure Monitor-lösningar från lösningsgalleriet](../../azure-monitor/insights/solutions.md).
