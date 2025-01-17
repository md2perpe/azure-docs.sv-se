---
title: Azure Traffic Manager | Microsoft Docs
description: Den här artikeln innehåller en översikt över Azure Traffic Manager. Se om det är rätt belastningsutjämningslösning för dig.
services: traffic-manager
author: asudbring
manager: twooley
ms.service: traffic-manager
customer intent: As an IT admin, I want to learn about Traffic Manager and what I can use it for.
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/05/2019
ms.author: allensu
ms.openlocfilehash: 95ef493ed44da8fd8372c11f15c3ab712961783e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67071010"
---
# <a name="what-is-traffic-manager"></a>Vad är Traffic Manager?
Azure Traffic Manager är en belastningsutjämnare för DNS-baserad trafik som hjälper dig att distribuera trafiken optimalt till tjänster i globala Azure-regioner, med hög tillgänglighet och korta svarstider.

Traffic Manager använder DNS för att dirigera klientbegäranden till den lämpligaste tjänstslutpunkten baserat på en trafikdirigeringsmetod och slutpunkternas hälsostatus. En slutpunkt är en Internetansluten tjänst i eller utanför Azure. Traffic Manager tillhandahåller en uppsättning [trafikdirigeringsmetoder](traffic-manager-routing-methods.md) och [alternativ för slutpunktsövervakning](traffic-manager-monitoring.md) som passar olika programbehov och modeller för automatisk redundansväxling. Traffic Manager har bra återhämtningsförmåga i händelse av fel, inklusive fel som påverkar en hel Azure-region.

>[!NOTE]
> Med Azure har du tillgång till en uppsättning fullständigt hanterade belastningsutjämningslösningar för dina scenarier. Om du är intresserad av TLS-avslut (Transport Layer Security) (”SSL-avlastning”) eller bearbetning på programnivå för enskilda HTTP/HTTPS-begäranden läser du avsnittet om [Application Gateway](../application-gateway/application-gateway-introduction.md). Om du är intresserad av regional belastningsutjämning läser du avsnittet om [Load Balancer](../load-balancer/load-balancer-overview.md). Du kan med fördel kombinera dessa lösningar efter behov för dina slutpunkts-till-slutpunkts-scenarier.

Traffic Manager ger följande funktioner:

## <a name="increase-application-availability"></a>Öka tillgängligheten för program

Traffic Manager tillhandahåller hög tillgänglighet för dina verksamhetskritiska program med slutpunktsövervakning och automatisk redundansväxling om en slutpunkt kraschar.
    
## <a name="improve-application-performance"></a>Förbättra programprestanda

Med Azure kan du köra molntjänster eller webbplatser på datacenter över hela världen. Traffic Manager förbättrar programmens svarstider genom att dirigera trafik till slutpunkten med den minsta nätverksfördröjningen för klienten.

## <a name="perform-service-maintenance-without-downtime"></a>Utför tjänstunderhåll utan avbrott

Du kan utföra planerat underhåll för dina program utan avbrottstid. Traffic Manager kan dirigera trafiken till alternativa slutpunkter under underhållsperioden.

## <a name="combine-hybrid-applications"></a>Kombinera hybridprogram

Traffic Manager stöder externa slutpunkter utanför Azure, vilket gör att lösningen kan användas i distributioner med hybridmoln och med lokala distributioner, inklusive ”[burst-to-cloud](https://azure.microsoft.com/overview/what-is-cloud-bursting/)”-, ”migrate-to-cloud”- och ”failover-to-cloud”-scenarier.

## <a name="distribute-traffic-for-complex-deployments"></a>Distribuera trafik för komplexa distributioner

Genom att använda [kapslade Traffic Manager-profiler](traffic-manager-nested-profiles.md) kan du kombinera flera trafikdirigeringsmetoder för att skapa avancerade och flexibla regler som uppfyller behoven i större och mer komplexa distributioner.

## <a name="pricing"></a>Prissättning

Prisinformation finns på sidan med [prisinformation för Traffic Manager](https://azure.microsoft.com/pricing/details/traffic-manager/).


## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [skapar en Traffic Manager-profil](traffic-manager-create-profile.md).
- Lär dig [hur Traffic Manager fungerar](traffic-manager-how-it-works.md).
- Läs [vanliga frågor och svar](traffic-manager-FAQs.md) om Traffic Manager.




