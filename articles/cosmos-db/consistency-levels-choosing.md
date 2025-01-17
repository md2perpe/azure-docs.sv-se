---
title: Välja rätt konsekvensnivå för ditt program som använder Azure Cosmos DB
description: Välja rätt konsekvensnivå för ditt program i Azure Cosmos DB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/22/2019
ms.reviewer: sngun
ms.openlocfilehash: b08f9a85b8c9f52724e2cd08eaf13eb1faae0977
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66243566"
---
# <a name="choose-the-right-consistency-level"></a>Välja rätt konsekvensnivå 

Distribuerade databaser som förlitar sig på replikering för hög tillgänglighet, svarstider, eller båda, se grundläggande förhållandet mellan läsningskontinuitet jämfört med tillgänglighet, svarstid och dataflöde. De flesta kommersiellt distribuerade databaser Ställ utvecklare kan välja mellan två extrem konsekvensmodeller: *stark* konsekvens och *slutlig* konsekvens. Azure Cosmos DB kan utvecklare välja mellan fem väldefinierade konsekvensmodeller: *stark*, *begränsad föråldring*, *session*, *konsekvent prefixet* och *eventuell*. Var och en av dessa konsekvensmodeller är väldefinierade, intuitivt och kan användas för den specifika verkliga scenarier. Var och en av de fem konsekvensmodeller ge exakta [tillgänglighet och prestanda kompromisser](consistency-levels-tradeoffs.md) och backas upp av omfattande serviceavtal. Följande enkla överväganden hjälper dig att fatta rätt beslut i många vanliga scenarier.

## <a name="sql-api-and-table-api"></a>SQL-API och tabell-API

Tänk på följande om ditt program har skapats med SQL API eller tabell-API:

- Sessionskonsekvens är den optimala för många verkliga scenarier, och det är det rekommendera alternativet. Mer information finns i, [anvisningar: hantera sessionstoken för ditt program](how-to-manage-consistency.md#utilize-session-tokens).

- Om programmet kräver stark konsekvens, rekommenderas att du använder begränsad föråldring konsekvensnivå.

- Om du behöver striktare garanterar konsekvens än som anges av sessionskonsekvens och enskild ensiffriga fördröjning för skrivningar, vi rekommenderar att du använder bunden föråldring konsekvensnivå.  

- Om programmet kräver konsekvens, rekommenderas att du använder konsekvent prefix konsekvensnivå.

- Om du behöver mindre strikta konsekvensgarantier än de som tillhandahålls av sessionskonsekvens rekommenderas att du använder konsekvent prefix konsekvensnivå.

- Om du behöver högsta möjliga tillgänglighet och lägsta svarstid kan sedan använda slutlig konsekvensnivå.

- Om du behöver ännu högre tillförlitlighet utan att offra prestanda kan skapa du en anpassad konsekvensnivå på programnivå. Mer information finns i [anvisningar implementera anpassade synkronisering i dina program](how-to-custom-synchronization.md).

## <a name="cassandra-mongodb-and-gremlin-apis"></a>Cassandra, MongoDB och Gremlin-API: er

- Mer information om mappning mellan ”Läs konsekvensnivå” erbjuds i Apache Cassandra och Cosmos DB-konsekvensnivåer, finns [konsekvensnivåer och Cosmos DB API: er](consistency-levels-across-apis.md#cassandra-mapping).

- Mer information om mappning mellan ”Läs problem” för MongoDB och Azure Cosmos DB-konsekvensnivåer finns [konsekvensnivåer och Cosmos DB API: er](consistency-levels-across-apis.md#mongo-mapping).

## <a name="consistency-guarantees-in-practice"></a>Konsekvensgarantier i praktiken

I praktiken kan du ofta få starkare konsekvensgarantier. Konsekvensgarantier för en Läsåtgärd motsvarar färskhet och skrivordning av databastillståndet som du begär. Läs konsekvens är kopplad till beställning och spridningen av skrivning/uppdateringsåtgärder.  

* När konsekvensnivån är inställd på **begränsad föråldring**, garanterar Cosmos DB att klienterna alltid läsa värdet för föregående skrivåtgärder, med en fördröjning som avgränsas av fönstret föråldring.

* När konsekvensnivån är inställd på **stark**fönstret föråldring motsvarar noll och klienterna garanteras att läsa det senaste allokerade värdet för Skrivåtgärden.

* Fönstret föråldring är i hög grad beroende på din arbetsbelastning för de återstående tre konsekvensnivåerna. Exempel: om det finns inga skrivåtgärder på databasen, en Läsåtgärd med **slutlig**, **session**, eller **konsekvent prefix** konsekvensnivåer troligen kommer att ge samma resultat som en Läsåtgärd med stark konsekvensnivå.

Om ditt Azure Cosmos-konto har konfigurerats med en konsekvensnivå än stark konsekvens, du kan ta reda på sannolikheten att dina klienter kan få stark och konsekventa läsningar för dina arbetsbelastningar genom att titta på den *Probabilistically Begränsad föråldring* (PBS) mått. Det här måttet visas i Azure-portalen, mer information finns i [övervakaren Probabilistically bunden föråldring (PBS) mått](how-to-manage-consistency.md#monitor-probabilistically-bounded-staleness-pbs-metric).

Avsnittet om sannolikhetsbunden begränsad föråldring visar hur eventuell är din slutlig konsekvens. Det här måttet ger en överblick över hur ofta du kan få en starkare konsekvens än konsekvensnivå som du för närvarande har konfigurerat för ditt Azure Cosmos-konto. Med andra ord kan du se sannolikheten (mätt i millisekunder) för att få starkt konsekventa läsningar för en kombination av Skriv- och läsregioner.

## <a name="next-steps"></a>Nästa steg

Läs mer om konsekvensnivåer i följande artiklar:

* [Konsekvens mappning på i Cosmos DB API: er](consistency-levels-across-apis.md)
* [Tillgänglighet och prestanda kompromisser för olika konsekvensnivåer](consistency-levels-tradeoffs.md)
* [Så här hanterar du sessionstoken för ditt program](how-to-manage-consistency.md#utilize-session-tokens)
* [Övervaka Probabilistically bunden föråldring (PBS) mått](how-to-manage-consistency.md#monitor-probabilistically-bounded-staleness-pbs-metric)
