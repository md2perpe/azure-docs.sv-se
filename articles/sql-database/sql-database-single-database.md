---
title: Vad är en enkel databas i Azure SQL Database | Microsoft Docs
description: Lär dig mer om enkel databas i Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 04/08/2019
ms.openlocfilehash: 36b1fb96ac1dd89375588a65063ce729f6ac825d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65794396"
---
# <a name="what-is-a-single-database-in-azure-sql-database"></a>Vad är en enskild databas i Azure SQL Database

Alternativ för distribution av enskild databas skapar en databas i Azure SQL Database med en egen uppsättning resurser och hanteras via en SQL Database-server. Med en enkel databas, alla databaser vara isolerade från varandra och portabla, var och en med sin egen tjänstnivå inom den [DTU-baserade inköpsmodellen](sql-database-service-tiers-dtu.md) eller [vCore-baserade inköpsmodellen](sql-database-service-tiers-vcore.md) och en garanterad beräkna storleken.

> [!IMPORTANT]
> Enkel databas är en av tre distributionsalternativen för Azure SQL Database. De andra två är [elastiska pooler](sql-database-elastic-pool.md) och [hanterad instans](sql-database-managed-instance.md).
> [!NOTE]
> En ordlista i Azure SQL Database finns [SQL Database allmänna ordlista](sql-database-glossary-terms.md)

## <a name="dynamic-scalability"></a>Dynamisk skalbarhet

Du kan skapa din första app på en liten, enkel databas för låg kostnad i Beräkningsnivån utan server (förhandsversion) eller en liten storlek på den etablerade beräkning-nivån. Du ändrar den [beräkning eller service-nivån](sql-database-single-database-scale.md) manuellt eller programmässigt när som helst för att uppfylla behoven i din lösning. Du kan justera prestandan utan driftavbrott för din app eller dina kunder. Dynamisk skalbarhet gör att databasen transparent kan svara på snabbt förändrade resurskrav och gör det möjligt för dig att endast betala för de resurser som du behöver, när du behöver dem.

## <a name="single-databases-and-elastic-pools"></a>Enkla databaser och elastiska pooler

En enskild databas kan flyttas till eller från en [elastisk pool](sql-database-elastic-pool.md) för resursdelning. För många företag och appar räcker det att kunna skapa enkla databaser och reglera prestanda för fristående databaser upp eller ner efter behov, speciellt om användningsmönstren är relativt förutsägbara. Men om du har oförutsägbara användningsmönster, kan det vara svårt att hantera kostnader och din affärsmodell. Elastiska pooler är utformade för att lösa problemet. Konceptet är enkelt. Du allokerar prestandaresurser till en pool i stället för en individuell databas och betalar för de samlade prestandaresurserna för poolen istället för prestandan för enskilda databaser.

## <a name="monitoring-and-alerting"></a>Övervakning och avisering

Du använder inbyggt [prestandaövervakning](sql-database-performance.md) och [aviseringsverktygen](sql-database-insights-alerts-portal.md), kombinerat med prestandaklassificeringarna. Med dessa verktyg kan du snabbt utvärdera effekten av att skala upp eller ner baserat på dina aktuella eller projekterade prestandakrav. Dessutom kan SQL-databasen [skapa mått och diagnostikloggar](sql-database-metrics-diag-logging.md) för lättare övervakning.

## <a name="availability-capabilities"></a>Kapacitet för tillgänglighet

Enkla databaser, elastiska pooler och hanterade instanser ger många tillgänglighet egenskaper. Mer information finns i [tillgänglighet egenskaper](sql-database-technical-overview.md#availability-capabilities).

## <a name="transact-sql-differences"></a>Transact-SQL-skillnader

De flesta Transact-SQL-funktioner som använder program stöds helt i både Microsoft SQL Server och Azure SQL Database. Till exempel fungerar SQL-kärnkomponenter, till exempel datatyper, operatörer, sträng, aritmetiska, logiska och markören functions identiskt i SQL Server och SQL-databas. Det finns dock några T-SQL skillnader i DDL (data definition language) och DML (data manipulation language) element, vilket resulterar i T-SQL-uttryck och frågor som stöds endast delvis (vilket beskrivs senare i den här artikeln).
Dessutom finns vissa funktioner och syntax som inte stöds på alla eftersom Azure SQL Database har utformats för att isolera funktioner från beroenden på huvuddatabasen och operativsystemet. Därför är de flesta aktiviteter på servernivå olämpliga för SQL Database. T-SQL-uttryck och alternativ är inte tillgängliga om de konfigurerar servernivåalternativ, operativsystemets komponenter eller anger systemkonfigurationen för filen. När sådana funktioner krävs ett lämpligt alternativ ofta är tillgänglig på annat sätt från SQL Database eller från en annan Azure-funktion eller -tjänst.

Mer information finns i [lösa Transact-SQL-skillnader vid migrering till SQL Database](sql-database-transact-sql-information.md).

## <a name="security"></a>Säkerhet

SQL Database erbjuder en uppsättning [inbyggd säkerhet och efterlevnad](sql-database-security-overview.md) funktioner för att hjälpa programmet att uppfylla olika krav för säkerhet och efterlevnad.

> [!IMPORTANT]
> Azure SQL Database (alla distributionsalternativ), har certifierats mot ett antal efterlevnadsstandarder. Mer information finns i den [Microsoft Azure Trust Center](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) där du hittar den senaste listan med SQL Database-kompatibilitetscertifieringar.

## <a name="next-steps"></a>Nästa steg

- För att snabbt komma igång med en enkel databas, börja med den [enkel database Snabbstart guide.md](sql-database-single-database-quickstart-guide.md).
- Läs om hur du migrerar en SQL Server-databas till Azure i [migrera till Azure SQL Database](sql-database-single-database-migrate.md).
- Information om vilka funktioner som stöds finns i avsnittet [Funktioner](sql-database-features.md).