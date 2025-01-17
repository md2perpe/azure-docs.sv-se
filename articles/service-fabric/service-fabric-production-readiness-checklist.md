---
title: Azure Service Fabric produktion beredskap Checklista | Microsoft Docs
description: Förbereda dina Service Fabric-program och klustret produktion av följande metoder.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chakdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 6/05/2019
ms.author: aljo
ms.openlocfilehash: a75b02b8173507a28204a3ec2030ce7ed9838495
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66729867"
---
# <a name="production-readiness-checklist"></a>Checklista för produktionsberedskap

Är din program- och redo att ta produktionstrafik? Köra och testa ditt program och klustret nödvändigtvis inte är den redo att gå till produktion. Se till att program och kluster som kör smidigt genom att gå igenom följande lista. Vi rekommenderar att alla dessa objekt som ska kontrolleras. Naturligtvis kan du välja att använda alternativa lösningar för en viss artikel (till exempel en egen ramverk för diagnostik).


## <a name="prerequisites-for-production"></a>Krav för produktion
1. Metodtips för Azure Service Fabric: [Programutformning](./service-fabric-best-practices-applications.md), [Security](./service-fabric-best-practices-security.md), [nätverk](./service-fabric-best-practices-networking.md), [kapacitetsplanering och skalning](./service-fabric-best-practices-capacity-scaling.md), [infrastruktur som kod](./service-fabric-best-practices-infrastructure-as-code.md), och [övervakning och diagnostik](./service-fabric-best-practices-monitoring.md). 
1. Implementera säkerhetskonfiguration Reliable Actors om använder programmeringsmodellen aktörer
1. Skapa en dedikerad primära nodtypen systemtjänsterna för kluster med fler än 20 kärnor eller 10 noder. Lägg till [placeringsbegränsningar](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md) att reservera den primära nodtypen för systemtjänster.
1. Använd en D2v2 eller högre SKU för den primära nodtypen. Vi rekommenderar att välja en SKU med minst 50 GB hårddiskkapacitet.
1. Produktionskluster måste vara [säker](service-fabric-cluster-security.md). Ett exempel på hur du konfigurerar ett säkert kluster finns i den här [kluster mallen](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/7-VM-Windows-3-NodeTypes-Secure-NSG). Använda vanliga namn för certifikat och undvika att använda självsignerat signerat certifikat.
1. Lägg till [resursbegränsningar på behållare och tjänster](service-fabric-resource-governance.md), så att de inte förbrukar mer än 75% av noden resurser. 
1. Förstå och ange den [hållbarhetsnivå](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster). Silver eller högre hållbarhetsnivå rekommenderas för nodtyper tillståndskänsliga arbetsbelastningar som körs. Den primära nodtypen ska ha en hållbarhetsnivå ange Silver eller högre.
1. Förstå och välj den [tillförlitlighetsnivån](service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster) av nodtyp. Silver eller högre tillförlitlighet rekommenderas.
1. Testa arbetsbelastningar att identifiera belastning och skala [kapacitetskrav](service-fabric-cluster-capacity.md) för klustret. 
1. Dina tjänster och program som övervakas och programloggar som ska skapas och lagras med aviseringar. Se exempelvis [Lägg till loggning i Service Fabric-programmet](service-fabric-how-to-diagnostics-log.md) och [övervaka behållare med Azure Monitor-loggar](service-fabric-diagnostics-oms-containers.md).
1. Klustret övervakas med aviseringar (till exempel med [Azure Monitor loggar](service-fabric-diagnostics-event-analysis-oms.md)). 
1. Den underliggande infrastrukturen för VM scale set övervakas med aviseringar (till exempel med [Azure Monitor loggar](service-fabric-diagnostics-oms-agent.md).
1. Klustret har [primära och sekundära certifikat](service-fabric-cluster-security-update-certs-azure.md) alltid (så att du inte blir utelåst).
1. Underhålla separata kluster för utveckling, mellanlagring och produktion. 
1. [Programuppgraderingar](service-fabric-application-upgrade.md) och [uppgraderingar av](service-fabric-tutorial-upgrade-cluster.md) testas i utveckling och mellanlagring kluster först. 
1. Inaktivera automatiska uppgraderingar i produktionskluster och aktivera den för utveckling och mellanlagring kluster (rollback efter behov). 
1. Upprätta ett mål för återställningspunkt (RPO) för din tjänst och ställa in en [återställningsprocessen](service-fabric-disaster-recovery.md) och testa den.
1. Planera för [skalning](service-fabric-cluster-scaling.md) klustret manuellt eller programmässigt.
1. Planera för [uppdatering](service-fabric-patch-orchestration-application.md) klusternoderna. 
1. Upprätta en CI/CD-pipeline, så att de senaste ändringarna testas kontinuerligt. Till exempel [Azure DevOps](service-fabric-tutorial-deploy-app-with-cicd-vsts.md) eller [Jenkins](service-fabric-cicd-your-linux-applications-with-jenkins.md)
1. Testa din utveckling och mellanlagring kluster under belastning med den [Fault Analysis Service](service-fabric-testability-overview.md) och orsaka kontrollerad [chaos](service-fabric-controlled-chaos.md). 
1. Planera för [skalning](service-fabric-concepts-scalability.md) dina program. 


Om du använder Service Fabric Reliable Services eller Reliable Actors programmeringsmiljö, måste följande objekt som ska kontrolleras:
1. Uppgradera program under lokal utveckling för att kontrollera att koden för tjänsten är respektera annullering token i den `RunAsync` metoden och stänga anpassade kommunikationslyssnarna.
1. Undvika [vanliga fallgropar](service-fabric-work-with-reliable-collections.md) när du använder tillförlitliga samlingar.
1. Övervaka .NET CLR-minnesprestanda räknare när du kör belastningstester och söka efter höga nivåer av skräpinsamling eller lång körningstid heap tillväxt.
1. Underhålla offlinesäkerhetskopiering av [Reliable Services och Reliable Actors](service-fabric-reliable-services-backup-restore.md) och testa återställningsprocessen.
1. Din primära NodeType virtuella datorn instansantal bör helst vara lika med minimala för ditt kluster tillförlitlighetsnivån; villkor när det är lämpligt att överskrida minst nivån omfattar: tillfälligt när lodrätt skalning av dina primära NodeTypes VM Scale SKU: N.

## <a name="optional-best-practices"></a>Valfritt Metodtips

Alla dessa listor är förutsättningar för att gå till produktion, bör följande också övervägas:
1. Anslut till den [hälsomodellen för Service Fabric](service-fabric-health-introduction.md) för att utöka inbyggda health-utvärdering och rapportering.
1. Distribuera en anpassad watchdog som övervakar dina program och rapporter [läsa in](service-fabric-cluster-resource-manager-metrics.md) för [resource belastningsutjämning](service-fabric-cluster-resource-manager-balancing.md). 


## <a name="next-steps"></a>Nästa steg
* [Distribuera ett Service Fabric Windows-kluster](service-fabric-tutorial-create-vnet-and-windows-cluster.md)
* [Distribuera ett Service Fabric Linux-kluster](service-fabric-tutorial-create-vnet-and-linux-cluster.md)
* Läs om Service Fabric-[applivscykeln](service-fabric-application-lifecycle.md).
