---
title: Översikt över Azure Cost Management | Microsoft Docs
description: Azure Cost Management är en kostnadshanteringslösning som hjälper till att övervaka och kontrollera Azure-utgifter och optimera resursanvändning.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 05/14/2019
ms.topic: overview
ms.service: cost-management
manager: benshy
ms.custom: ''
ms.openlocfilehash: 6615c6d8912195dbecda727bbdb31dcf04898ea2
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65792814"
---
# <a name="what-is-azure-cost-management"></a>Vad är Azure Cost Management?

Kostnadshantering är processen att effektivt planera och styra kostnader för ditt företag. Kostnadshanteringsuppgifter utförs normalt av ekonomi-, hanterings- och appteam. Azure Cost Management hjälper organisationer att planera kostnad i åtanke. Det hjälper också till att analysera kostnaderna effektivt och vidta åtgärder för att optimera utgifter. Om du vill veta mer om metoder för kostnadshantering för organisationer kan du läsa artikeln om [metodtips för Azure Cost Management](cost-mgt-best-practices.md).

Titta på den [Azure Cost Management översiktsvideo](https://www.youtube.com/watch?v=el4yN5cHsJ0) för en kort översikt över hur Azure Cost Management kan hjälpa dig att spara pengar i Azure.

>[!VIDEO https://www.youtube.com/embed/el4yN5cHsJ0]

Fakturering är relaterat till kostnadshantering men inbegriper skillnader. I faktureringsprocessen ingår att fakturera kunder för varor eller tjänster och att hantera den affärsrelationen.  Inköps- och ekonomiteamen genomför vanligtvis faktureringsuppgifter.

Kostnadshantering visar organisationens kostnader och användningsmönster med hjälp av avancerade analyser. Rapporter i Cost Management visar användningsbaserad kostnader som används av Azure-tjänsterna och Marketplace-erbjudanden från tredje part. Kostnaderna baseras på förhandlade priser och ta reservation och Azure Hybrid-förmånen rabatter. Sammantaget visar rapporterna dina interna och externa kostnader för användning och Azure Marketplace-debiteringar. Andra debiteringar, till exempel köp av reservation, support och skatter visas ännu inte i rapporter. Rapporterna hjälper dig att förstå dina utgifter och resursanvändningen och kan hjälpa dig hitta utgiftsavvikelser. Förutsägelseanalys är också tillgängligt. Kostnadshantering använder Azure-hanteringsgrupper, budgetar och rekommendationer för att tydligt visa hur dina utgifter organiseras och hur du kan minska kostnaderna.

Du kan använda Azure-portalen eller olika API:er för exportautomatisering för att integrera kostnadsdata med externa system och processer. Automatiserad export av faktureringsdata och schemalagda rapporter är också tillgängligt.

## <a name="plan-and-control-expenses"></a>Planering och kontrollera utgifter

Med Cost Management kan du planera för och styra dina kostnader på följande sätt: Kostnadsanalys, budget, rekommendationer och export av kostnadshanteringsdata.

Du använder kostnadsanalys för att utforska och analysera dina organisationskostnader. Du kan visa aggregerade kostnader efter organisation för att se var kostnader ackumuleras och för att identifiera utgiftstrender. Du kan även se ackumulerade kostnader över tid för att beräkna månatliga, kvartalsvisa eller årliga kostnadstrender mot en budget.

Budgetar hjälper dig att planera och uppfylla finansiellt ansvar i din organisation. De bidrar till att förhindra att kostnadströsklar eller -gränser överskrids. Budgetar kan även hjälpa dig att informera andra om deras utgifter för att proaktivt hantera kostnader. De gör också att du kan se hur utgifterna utvecklas över tid.

Rekommendationer visar hur du kan optimera och förbättra effektiviteten genom att identifiera inaktiva och underutnyttjade resurser. Eller så kan de visa billigare resursalternativ. När du agerar utifrån rekommendationer ändrar sättet som du använder resurser för att spara pengar. För att tittar du först på rekommendationer för kostnadsoptimering för att hitta potentiell ineffektivitet i användningen. Därefter agerar du utefter en rekommendation för att ändra din Azure-resursanvändning till ett mer kostnadseffektivt alternativ. Sedan kontrollerar du åtgärden för att se till att den ändring du gör lyckas.

Om du använder externa system för att komma åt eller granska kostnadshanteringsdata kan du enkelt exportera data från Azure. Och du kan ange en daglig schemalagd export i CSV-format och lagra datafilerna i Azure Storage. Du kan sedan komma åt data från ditt externa system.

## <a name="consider-cloudyn"></a>Tjänsten Cloudyn

[Cloudyn](overview.md) är en Azure-tjänst som är relaterad till Cost Management. Med Cloudyn kan du spåra molnanvändning och utgifter för dina Azure-resurser. Det stöder även andra molnleverantörer, inklusive AWS och Google. Instrumentpanelsrapporter som är lätta att förstå hjälper dig med kostnadsallokering och kostnadsrapporter/återbetalningar. För närvarande har Cost Management inte stöd för kostnadsfördelning/återbetalning eller andra molntjänstleverantörer. Cloudyn är dock ett alternativ som _har_ stöd för dem. För närvarande kostnadshantering stöder inte Microsoft Cloud Service Provider (CSP)-konton, men Cloudyn gör. Om du har CSP-konton eller om du vill använda kostnadsrapporter/återbetalning, kan du använda Cloudyn för att hantera dina kostnader.

Titta på den [Azure Cost Management och Cloudyn video](https://www.youtube.com/watch?v=PmwFWwSluh8) att se rekommendationer när du ska använda Azure Cost Management eller Cloudyn, baserat på dina affärsbehov.

>[!VIDEO https://www.youtube.com/embed/PmwFWwSluh8]

## <a name="additional-azure-tools"></a>Ytterligare Azure-verktyg

Azure har andra verktyg som inte är en del av Azure Cost Management-funktionsuppsättningen. De spelar dock en viktig roll i kostnadshanteringsprocessen. Se följande länkar om du vill veta mer om dessa verktyg.

- [Azure Pricing Calculator](https://azure.microsoft.com/pricing/calculator/) – använd det här verktyget för att beräkna dina startkostnader för moln.
- [Azure Migrate](../migrate/migrate-overview.md) – utvärdera din nuvarande datacenterarbetsbelastning för att få insikter om vad som behövs från en Azure-ersättningslösning.
- [Azure Advisor](../advisor/advisor-overview.md) – identifiera oanvända virtuella datorer och få rekommendationer om köp av reserverade Azure-instanser.
- [Azure Hybrid-förmån](https://azure.microsoft.com/pricing/hybrid-benefit/) – använd dina aktuella lokala Windows Server- eller SQL Server-licenser för virtuella datorer i Azure för att spara.


## <a name="next-steps"></a>Nästa steg

Nu när du är bekant med Cost Management är nästa steg att börja använda tjänsten.

- Börja använda Azure Cost Management för att [analysera kostnader](quick-acm-cost-analysis.md).
- Du kan även läsa mer om [metodtips för Azure Cost Management](cost-mgt-best-practices.md).
