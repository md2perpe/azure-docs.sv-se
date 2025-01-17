---
title: Hantera och övervaka SQL Server-databaser på en Azure virtuell dator som backas upp av Azure Backup | Microsoft Docs
description: Den här artikeln beskriver hur du hantera och övervaka SQL Server-databaser som körs på en virtuell Azure-dator.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 03/14/2018
ms.author: raynew
ms.openlocfilehash: d850cb222b0028f862cdba302140ce12af8576db
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66492730"
---
# <a name="manage-and-monitor-backed-up-sql-server-databases"></a>Hantera och övervaka säkerhetskopierade SQL Server-databaser

Den här artikeln beskrivs vanliga uppgifter för hantering och övervakning av SQL Server-databaser som körs på en Azure-dator (VM) och som ska säkerhetskopieras till en Azure Backup Recovery Services-valvet genom den [Azure Backup](backup-overview.md) service. Du lär dig att övervaka jobb och aviseringar, stoppa och återuppta databasskyddet, köra säkerhetskopieringsjobb och avregistrera en virtuell dator från säkerhetskopior.

Om du inte har konfigurerat säkerhetskopiering för SQL Server-databaser, se [säkerhetskopiera SQL Server-databaser på Azure Virtual Machines](backup-azure-sql-database.md)

## <a name="monitor-manual-backup-jobs-in-the-portal"></a>Övervaka manuell säkerhetskopieringsjobb i portalen

Azure Backup visar alla manuellt utlösta jobb i den **säkerhetskopieringsjobb** portal. De jobb som du ser i den här portalen inkludera databasidentifiering och registrera och säkerhetskopiering och återställning.

![Backup jobb-portalen](./media/backup-azure-sql-database/jobs-list.png)

> [!NOTE]
> Den **säkerhetskopieringsjobb** portalen inte visar schemalagda säkerhetskopieringsjobb. Använd SQL Server Management Studio för att övervaka schemalagda säkerhetskopieringsjobb, enligt beskrivningen i nästa avsnitt.
>

Mer information om övervakningsscenarier, går du till [övervakning i Azure portal](backup-azure-monitoring-built-in-monitor.md) och [övervakning med hjälp av Azure Monitor](backup-azure-monitoring-use-azuremonitor.md).  


## <a name="view-backup-alerts"></a>Visa säkerhetskopieringsaviseringar

Eftersom loggsäkerhetskopior sker var 15: e minut, kan det vara tråkigt att övervaka säkerhetskopieringsjobb. Azure Backup förenklar övervakning genom att skicka e-postaviseringar. E-postaviseringar finns:

- Utlöses för alla Säkerhetskopieringsfel.
- Konsoliderade på databasnivå av felkoder.
- Skickas endast för en databas första säkerhetskopieringen har misslyckats.

Övervaka aviseringar om säkerhetskopiering av databasen:

1. Logga in på [Azure Portal](https://portal.azure.com).

2. På instrumentpanelen för valvet väljer **aviseringar och händelser**.

   ![Välja aviseringar och händelser](./media/backup-azure-sql-database/vault-menu-alerts-events.png)

3. I **aviseringar och händelser**väljer **säkerhetskopiering aviseringar**.

   ![Välja säkerhetskopieringsaviseringar](./media/backup-azure-sql-database/backup-alerts-dashboard.png)

## <a name="stop-protection-for-a-sql-server-database"></a>Stoppa skydd för en SQL-serverdatabas

Du kan stoppa säkerhetskopiering av SQL Server-databasen i ett par olika sätt:

* Stoppa alla framtida säkerhetskopieringsjobb och ta bort alla återställningspunkter.
* Stoppa alla framtida säkerhetskopieringsjobb och lämna kvar återställningspunkterna.

Om du väljer att lämna återställningspunkterna, Tänk på följande information:

* Alla återställningspunkter förblir intakta alltid, alla rensning när stoppa skall stoppa skyddet med Behåll data.
* Du debiteras för den skyddade instansen och förbrukad lagring. Mer information finns i [priser för Azure Backup](https://azure.microsoft.com/pricing/details/backup/).
* Om du tar bort en datakälla utan att stoppa säkerhetskopiering misslyckas nya säkerhetskopior.

Så här stoppar du skydd för en databas:

1. På instrumentpanelen för valvet väljer **Säkerhetskopieringsobjekt**.

2. Under **typ av Säkerhetskopieringshantering**väljer **SQL i Azure VM**.

    ![Välja SQL på Azure VM](./media/backup-azure-sql-database/sql-restore-backup-items.png)

3. Välj den databas som du vill sluta skydda.

    ![Välja den databas som skydd ska stoppas för](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

4. Databas-menyn, Välj **stoppa säkerhetskopiering**.

    ![Välja Avbryt säkerhetskopiering](./media/backup-azure-sql-database/stop-db-button.png)


5. På den **stoppa säkerhetskopiering** menyn, Välj om du vill behålla eller ta bort data. Om du vill ange en orsak och en kommentar.

    ![Behålla eller ta bort data från menyn stoppa säkerhetskopiering](./media/backup-azure-sql-database/stop-backup-button.png)

6. Välj **stoppa säkerhetskopiering**.

>
> [!NOTE]
Finns det under vanliga frågor och svar för mer information om alternativet för att ta bort data: <br/>
* [Om jag tar bort en databas från en instans av autoprotected vad händer med säkerhetskopiorna?](faq-backup-sql-server.md#if-i-delete-a-database-from-an-autoprotected-instance-what-will-happen-to-the-backups)<br/>
* [Om jag förhindrar säkerhetskopieringen av en databas för autoprotected vad blir sitt beteende?](faq-backup-sql-server.md#if-i-change-the-name-of-the-database-after-it-has-been-protected-what-will-be-the-behavior)
>
>


## <a name="resume-protection-for-a-sql-database"></a>Återuppta skyddet för en SQL-databas

När du stoppar skydd för SQL-databasen, om du väljer den **Behåll säkerhetskopieringsdata** alternativet kan du senare kan återuppta skyddet. Om du inte behåller säkerhetskopierade data, kan inte du återuppta skyddet.

Att återuppta skyddet av en SQL-databas:

1. Öppna säkerhetskopieringsobjektet och välj **återuppta säkerhetskopiering**.

    ![Välj Resume backup (Återuppta säkerhetskopiering) för att återuppta databasskyddet](./media/backup-azure-sql-database/resume-backup-button.png)

2. På menyn **Säkerhetskopieringspolicy** väljer du en policy och sedan **Spara**.

## <a name="run-an-on-demand-backup"></a>Köra en säkerhetskopiering på begäran

Du kan köra olika typer av säkerhetskopieringar på begäran:

* Fullständig säkerhetskopia
* Fullständig säkerhetskopia med endast kopiering
* Differentiell säkerhetskopia
* Loggsäkerhetskopia

Även om du vill ange kvarhållningsvaraktighetens för fullständig säkerhetskopiering med endast kopiering kan anges automatiskt kvarhållningsintervallet för andra typer av säkerhetskopiering till 30 dagar från aktuell tid. <br/>
Mer information finns i [SQL Server-säkerhetskopieringstyper](backup-architecture.md#sql-server-backup-types).

## <a name="unregister-a-sql-server-instance"></a>Avregistrera en SQL-serverinstans

Avregistrera en SQL Server-instansen när du inaktiverar skyddet men innan du tar bort valvet:

1. På instrumentpanelen för valvet under **hantera**väljer **infrastruktur för säkerhetskopiering**.  

   ![Välja infrastruktur för säkerhetskopiering](./media/backup-azure-sql-database/backup-infrastructure-button.png)

2. Under **Hanteringsservrar** väljer du **Skyddade servrar**.

   ![Välja skyddade servrar](./media/backup-azure-sql-database/protected-servers.png)

3. I **skyddade servrar**, väljer du servern du avregistrera. Om du vill ta bort valvet måste du avregistrera alla servrar.

4. Högerklicka på den skyddade servern och välj **avregistrera**.

   ![Välja Ta bort](./media/backup-azure-sql-database/delete-protected-server.jpg)

## <a name="re-register-extension-on-the-sql-server-vm"></a>Registrera SQL Server-VM-tillägget

Ibland kan tillägget arbetsbelastningen på den virtuella datorn påverkas av en orsak eller den andra. I sådana fall kan börjar alla åtgärder som utlöses på den virtuella datorn misslyckas. Sedan kan du behöva registrera tillägget på den virtuella datorn. **Omregistrera** åtgärden installerar om arbetsbelastningen säkerhetskopieringstillägget på den virtuella datorn att fortsätta.  <br>

Det är bäst att använda det här alternativet med försiktighet; När det utlöses på en virtuell dator med filnamnstillägget redan felfri, kan den här åtgärden kommer tillägget att hämta startas om. Detta kan resultera i alla pågående jobb misslyckas. Se efter för en eller flera av de [symptom](backup-sql-server-azure-troubleshoot.md#symptoms) innan du utlöser åtgärden registrera igen.

## <a name="next-steps"></a>Nästa steg

Mer information finns i [felsöka säkerhetskopieringar på en SQL Server-databas](backup-sql-server-azure-troubleshoot.md).
