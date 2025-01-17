---
title: Översikt över Azure Diagnostics-tillägget
description: Använd Azure diagnostics för felsökning, mäta prestanda, övervaka, analysera trafik i molntjänster, virtuella datorer och service fabric
author: rboucher
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 02/13/2019
ms.author: robb
ms.subservice: diagnostic-extension
ms.openlocfilehash: 8a287f118c126967d2cf8cad77a434cfecc098eb
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60236239"
---
# <a name="what-is-azure-diagnostics-extension"></a>Vad är Azure Diagnostics-tillägget
Azure Diagnostics-tillägget är en agent i Azure som aktiverar insamlingen av diagnostikdata på ett distribuerat program. Du kan använda diagnostiktillägget från ett antal olika källor. För närvarande är Azure-molntjänst (klassisk) rollerna Web och Worker, virtuella datorer, virtuella datorer skala uppsättningar och Service Fabric. Andra Azure-tjänster har olika diagnostik metoder. Se [översikt över övervakning i Azure](../../azure-monitor/overview.md).

## <a name="linux-agent"></a>Linux-agent
En [Linux-versionen av tillägget](../../virtual-machines/extensions/diagnostics-linux.md) är tillgänglig för virtuella datorer som kör Linux. Statistik som samlas in och beteende skilja sig från den Windows-versionen.

## <a name="data-you-can-collect"></a>Du kan samla in data
Azure Diagnostics-tillägget kan samla in följande typer av data:

| Datakälla | Beskrivning |
| --- | --- |
| Räknaren prestandamått |Operativsystem och anpassade prestandaräknare |
| Programloggar |Spåra meddelanden som skrivits av ditt program |
| Windows-händelseloggar |Informationen som skickas till Windows event loggning system |
| .NET EventSource loggar |Kod med hjälp av .NET-händelser skrivs [EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) klass |
| IIS-loggar |Information om IIS-webbplatser |
| [Manifestet baserat ETW-loggar](https://docs.microsoft.com/windows/desktop/etw/about-event-tracing) |Event Tracing för Windows händelser som genererats av en process. (1) |
| Kraschdumpar (loggar) |Information om tillståndet hos processen om ett program kraschar |
| Anpassa felloggar |Loggar som skapats av programmet eller tjänsten |
| Azure Diagnostic infrastructure-loggar |Information om Azure Diagnostics själva |

(1) för att få en lista över ETW-leverantörer, köra `c:\Windows\System32\logman.exe query providers` i ett konsolfönster på den dator som du vill samla in information från.

## <a name="data-storage"></a>Datalagring
Tillägget lagrar data i en [Azure Storage-konto](diagnostics-extension-to-storage.md) som du anger.

Du kan också skicka den till [Application Insights](../../azure-monitor/app/cloudservices.md). 

Ett annat alternativ är att strömma det till [Event Hub](../../event-hubs/event-hubs-about.md), som sedan kan du skicka den till Azure-övervakningstjänster.

Du har också valet av skickar du data till Azure Monitor metrics time series-databas. Den här mottagare är för tillfället endast gäller för prestandaräknare. Det kan du skicka prestandaräknare i som anpassade mått. Den här funktionen är i förhandsversion. Azure Monitor-mellanlagringsplatsen har stöd för:
* Hämtar alla prestandaräknare skickas till Azure Monitor via den [Azure Monitor metrics API: er.](https://docs.microsoft.com/rest/api/monitor/)
* Varna vid alla prestandaräknare skickas till Azure Monitor via den [måttaviseringar](../../azure-monitor/platform/alerts-overview.md) i Azure Monitor
* Behandla jokertecken operator i prestandaräknare som dimensionen ”instans” på din mått.  Till exempel om du samlat in den ”logisk disk (\*) / DiskWrites/sek” räknare du skulle kunna filtrera och dela upp på dimensionen ”instans” för diagram eller Varna vid den Diskskrivningar/sek för varje logisk Disk på den virtuella datorn (till exempel C:)

Mer information om hur du konfigurerar den här mottagare som avser den [Azure diagnostics schema-dokumentationen.](diagnostics-extension-schema-1dot3.md)

## <a name="costs"></a>Kostnader
Vart och ett av alternativen ovan kan tillkomma kostnader. Glöm inte att undersöka dem för att undvika oväntade fakturor.  Application Insights, Event hub och Azure Storage har olika kostnader i samband med datainmatning och den tid som lagras. I synnerhet innehåller Azure Storage några data alltid så kan du ta bort äldre data efter en viss tidsperiod att hålla kostnaderna nere.    

## <a name="versioning-and-configuration-schema"></a>Schema för versionshantering och konfiguration
Se [versionshistorik för Azure-diagnostik och Schema](diagnostics-extension-schema.md).


## <a name="next-steps"></a>Nästa steg
Välj vilken tjänst som du försöker samla in diagnostik på och Använd följande artiklar för att komma igång. Länkarna Allmänt Azure-diagnostik för referens för specifika uppgifter.

## <a name="cloud-services-using-azure-diagnostics"></a>Cloud Services med Azure Diagnostics
* Om du använder Visual Studio finns i [Använd Visual Studio för att spåra en Cloud Services-program](/visualstudio/azure/vs-azure-tools-debug-cloud-services-virtual-machines) att komma igång. Annars läser
* [Så här övervakar du Cloud services med Azure Diagnostics](../../cloud-services/cloud-services-how-to-monitor.md)
* [Konfigurera Azure Diagnostics i en Cloud Services-program](../../cloud-services/cloud-services-dotnet-diagnostics.md)

Läs mer avancerade ämnen

* [Med hjälp av Azure-diagnostik med Application Insights för Cloud Services](../../azure-monitor/app/cloudservices.md)
* [Spåra flödet för en Cloud Services-program med Azure Diagnostics](../../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md)
* [Använd PowerShell för att konfigurera diagnostik på molntjänster](../../virtual-machines/extensions/diagnostics-windows.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="virtual-machines"></a>Virtuella datorer
* Om du använder Visual Studio finns i [Använd Visual Studio för att spåra Azure Virtual Machines](/visualstudio/azure/vs-azure-tools-debug-cloud-services-virtual-machines) att komma igång. Annars läser
* [Konfigurera Azure Diagnostics på en Azure-dator](/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines)

Läs mer avancerade ämnen

* [Använd PowerShell för att konfigurera diagnostik på Azure Virtual Machines](../../virtual-machines/extensions/diagnostics-windows.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Skapa en virtuell Windows-dator med övervakning och diagnostik med Azure Resource Manager-mall](../../virtual-machines/extensions/diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="service-fabric"></a>Service Fabric
Kom igång på [övervaka ett Service Fabric-program](../../service-fabric/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md). Många andra Service Fabric diagnostik artiklar är tillgängliga i navigeringsträdet till vänster när du har hämtat den här artikeln.

## <a name="general-articles"></a>Allmänna artiklar
* Lär dig hur du [använda prestandaräknare i Azure Diagnostics](../../cloud-services/diagnostics-performance-counters.md).
* Om du har problem med att starta diagnostik eller söka efter data i Azure storage-tabeller finns i [felsöka Azure Diagnostics](diagnostics-extension-troubleshooting.md)

