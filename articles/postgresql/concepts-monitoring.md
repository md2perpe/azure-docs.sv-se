---
title: Övervaka och finjustera i Azure Database för PostgreSQL – enskild Server
description: Den här artikeln beskriver övervakar och justerar funktioner i Azure Database för PostgreSQL – enskild Server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 06/19/2019
ms.openlocfilehash: c69ffb30a37de8e6dc3e15aa1f7dcd6a9311d614
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2019
ms.locfileid: "67274298"
---
# <a name="monitor-and-tune-azure-database-for-postgresql---single-server"></a>Övervaka och finjustera Azure Database för PostgreSQL – enskild Server
Övervakning av data om dina servrar kan du felsöka och optimera din arbetsbelastning. Azure Database för PostgreSQL innehåller olika övervakningsalternativ att ge insikter i beteendet för din server.

## <a name="metrics"></a>Mått
Azure Database för PostgreSQL innehåller olika mått som ger inblick i beteendet för de resurser som stödjer PostgreSQL-servern. Varje mått har genererats med en frekvens för en minut och har upp till 30 dagars historik. Du kan konfigurera aviseringar om måtten. Stegvisa anvisningar finns i [hur du konfigurerar aviseringar](howto-alert-on-metric.md). Andra uppgifter är att skapa automatiska åtgärder, utföra avancerade analyser och arkivering historik. Mer information finns i den [översikt över Azure-mått](../monitoring-and-diagnostics/monitoring-overview-metrics.md).

### <a name="list-of-metrics"></a>Lista över mått
De här måtten är tillgängliga för Azure Database för PostgreSQL:

|Mått|Metrisk visningsnamn|Enhet|Beskrivning|
|---|---|---|---|
|cpu_percent|CPU-procent|Procent|Procentandelen av Processorn som används.|
|memory_percent|Minne|Procent|Procentandelen minne som används.|
|io_consumption_percent|IO-procent|Procent|Procentandelen av i/o som används.|
|storage_percent|Lagringsprocent|Procent|Procentandelen av lagring som används av servern är maximalt.|
|storage_used|Använt lagringsutrymme|Byte|Mängden lagringsutrymme som används. Lagring som används av tjänsten kan omfatta databasfiler och transaktionsloggar serverloggarna.|
|storage_limit|Gränsen för lagring|Byte|Det maximala lagringsutrymmet för den här servern.|
|serverlog_storage_percent|Procent för lagring av Server-loggen|Procent|Procentandelen av server logglagring som används av serverns maximala server log lagring.|
|serverlog_storage_usage|Server logglagring som används|Byte|Mängden log-serverlagring används.|
|serverlog_storage_limit|Log storage gränsen|Byte|Det maximala server log lagringsutrymmet för den här servern.|
|active_connections|Aktiva anslutningar|Count|Antal aktiva anslutningar till servern.|
|connections_failed|Misslyckade anslutningar|Count|Antal misslyckade anslutningar till servern.|
|network_bytes_egress|Nätverk ut|Byte|Nätverk ut över aktiva anslutningar.|
|network_bytes_ingress|Nätverk in|Byte|Nätverk i över aktiva anslutningar.|
|backup_storage_used|Används för lagring av säkerhetskopior|Byte|Mängden lagring av säkerhetskopior används.|
|pg_replica_log_delay_in_bytes|Max Lag Across Replicas|Byte|Fördröjning i byte mellan huvudservern och de flesta släpar repliken. Detta mått är tillgängliga på den överordnade servern.|
|pg_replica_log_delay_in_seconds|Repliken fördröjning|Sekunder|Tid sedan den senaste återupprepas transaktion. Detta mått är tillgängliga för replikservrar.|

## <a name="server-logs"></a>Serverloggar
Du kan aktivera loggning på servern. Dessa loggar finns också tillgängliga via Azure-diagnostikloggar i [Azure Monitor loggar](../azure-monitor/log-query/log-query-overview.md), Händelsehubbar och Storage-konto. Mer information om loggning finns i [serverloggar](concepts-server-logs.md) sidan.

## <a name="query-store"></a>Query Store
[Query Store](concepts-query-store.md) håller reda på frågan prestanda över tid, inklusive fråga efter körningsstatistik och vänta händelser. Funktionen kvarstår fråga information om körningsprestanda i en databas med namnet **azure_sys** under query_store schemat. Du kan styra insamling och lagring av data via olika configuration rattar.

## <a name="query-performance-insight"></a>Query Performance Insight
[Query Performance Insight](concepts-query-performance-insight.md) fungerar tillsammans med Query Store att tillhandahålla visualiseringar som är tillgängliga från Azure-portalen. Dessa diagram kan du identifiera viktiga frågor som påverkas prestanda. Fråga efter prestanda Insightis kan nås från den **Support och felsökning** delen av din Azure Database for PostgreSQL-server portalsidan.

## <a name="performance-recommendations"></a>Prestandarekommendationer
Den [Prestandarekommendationer](concepts-performance-recommendations.md) funktionen identifierar möjligheter att förbättra prestanda för arbetsbelastningen. Prestandarekommendationer ger dig rekommendationer för att skapa nya index som kan förbättra prestandan för dina arbetsbelastningar. För att skapa indexrekommendationer beaktar funktionen olika egenskaper i databasen, inklusive dess schema och arbetsbelastningen som rapporterats av Query Store. När du implementerar en rekommendation för prestanda, bör kunderna testa prestanda för att utvärdera effekten av ändringarna. 

## <a name="next-steps"></a>Nästa steg
- Se [hur du konfigurerar aviseringar](howto-alert-on-metric.md) anvisningar om hur du skapar en avisering på ett mått.
- Mer information om hur du komma åt och exportera mått med hjälp av Azure portal, REST API eller CLI finns i den [översikt över Azure-mått](../monitoring-and-diagnostics/monitoring-overview-metrics.md).
- Läs vår blogg på [bästa praxis när du övervakar servern](https://azure.microsoft.com/blog/best-practices-for-alerting-on-metrics-with-azure-database-for-postgresql-monitoring/).
