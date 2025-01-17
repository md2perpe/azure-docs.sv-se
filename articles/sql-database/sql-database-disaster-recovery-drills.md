---
title: SQL Database Programåterställningstest | Microsoft Docs
description: Lär dig vägledning och bästa praxis för att utföra tester av haveriberedskap med Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
manager: craigg
ms.date: 12/18/2018
ms.openlocfilehash: 5d754ae558d485036a9a55f573a3f40162ed9f84
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60725439"
---
# <a name="performing-disaster-recovery-drill"></a>Utföra Programåterställningstest

Du rekommenderas att verifieringen av programberedskap för återställningsarbetsflöde utförs med jämna mellanrum. Verifiera programmets beteende och konsekvenserna av förlust av data och/eller kan avbrott innebär att redundansväxlingen kan vara bra engineering. Det är också ett krav enligt de flesta branschstandarder som en del av business continuity certifiering.

Utföra ett programåterställningstest består av:

* Simulering nivån avbrott
* Återställa
* Verifiera programåterställning integritet inlägg

Beroende på hur du [utformats för ditt program för affärskontinuitet](sql-database-business-continuity.md), arbetsflödet att köra detaljerade kan variera. Den här artikeln beskriver Metodtips för att utföra ett programåterställningstest i samband med Azure SQL Database.

## <a name="geo-restore"></a>Geo-återställning

Utför test med en test-miljö genom att skapa en kopia av produktionsmiljön och använder det för att kontrollera programmets redundans arbetsflöde för att förhindra förlust av data när utföra ett programåterställningstest.

### <a name="outage-simulation"></a>Avbrott simulering

För att simulera driftstörningarna, kan du byta namn på källdatabasen. Namnändringen orsakar anslutningsfel för program.

### <a name="recovery"></a>Återställning

* Utföra geo-återställning av databasen till en annan server enligt [här](sql-database-disaster-recovery.md).
* Ändra programmets konfiguration för att ansluta till den återställda databasen och följ de [konfigurerar en databas efter återställningen](sql-database-disaster-recovery.md) guiden för att slutföra återställningen.

### <a name="validation"></a>Validering

Slutför detaljerade genom att verifiera programmet integritet efter återställningen (inklusive anslutningssträngar, inloggningar, grundläggande funktioner testning eller andra verifieringar-delen av standardprogram signeringar procedurer).

## <a name="failover-groups"></a>Redundansgrupper

För en databas som skyddas med redundansgrupper omfattar drill-Övning planerad redundansväxling till den sekundära servern. Den planerade redundansväxlingen säkerställer att primärt och sekundära databaser i redundansgruppen förblir synkroniserade när rollerna växlas. Till skillnad från den oplanerade redundansväxlingen leder åtgärden inte till förlust av data, så detaljerade kan utföras i produktionsmiljön.

### <a name="outage-simulation"></a>Avbrott simulering

Du kan inaktivera webbprogram eller virtuella datorn är ansluten till databasen för att simulera avbrottet. Avbrott simuleringen resulterar i fel vid lagringsanslutning för webbklienter.

### <a name="recovery"></a>Återställning

* Kontrollera programkonfigurationen i DR-regionen pekar på den sekundära, tidigare som blir den nya primärt nås.
* Initiera [planerad redundans](scripts/sql-database-setup-geodr-and-failover-database-powershell.md) i redundansgruppen från den sekundära servern.
* Följ den [konfigurerar en databas efter återställningen](sql-database-disaster-recovery.md) guiden för att slutföra återställningen.

### <a name="validation"></a>Validering

Slutför detaljerade genom att verifiera programmet integritet efter återställningen (inklusive anslutningar, grundläggande funktioner testning eller andra verifieringar som krävs för ökad detaljnivå signeringar).

## <a name="next-steps"></a>Nästa steg

* Läs mer om affärskontinuitet affärsscenarier, i [affärskontinuitet scenarier](sql-database-business-continuity.md).
* Vill veta mer om Azure SQL Database automatiska säkerhetskopior, se [automatiserad säkerhetskopiering för SQL-databas](sql-database-automated-backups.md)
* Läs om hur du använder automatiska säkerhetskopieringar för återställning i [återställa en databas från de tjänstinitierade säkerhetskopiorna](sql-database-recovery-using-backups.md).
* Läs om hur du snabbare återställningsalternativ i [aktiv geo-replikering](sql-database-active-geo-replication.md) och [automatisk redundans grupper](sql-database-auto-failover-group.md).
