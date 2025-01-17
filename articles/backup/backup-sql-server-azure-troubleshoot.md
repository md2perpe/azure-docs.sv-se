---
title: Felsöka säkerhetskopiering av SQL Server-databas med Azure Backup | Microsoft Docs
description: Felsökningsinformation för att säkerhetskopiera SQL Server-databaser som körs på virtuella Azure-datorer med Azure Backup.
services: backup
author: anuragm
manager: shivamg
ms.service: backup
ms.topic: article
ms.date: 06/18/2019
ms.author: anuragm
ms.openlocfilehash: 9ed30a35f30d1b6b9fdcd43110ed93618a10dbc3
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67204198"
---
# <a name="troubleshoot-back-up-sql-server-on-azure"></a>Felsöka säkerhetskopiering av SQL Server på Azure

Den här artikeln innehåller felsökningsinformation för att skydda SQL Server-datorer på Azure.

## <a name="feature-consideration-and-limitations"></a>Funktionen överväganden och begränsningar

Om du vill visa funktionen överväganden finns i artikeln [om SQL Server-säkerhetskopiering i Azure virtuella datorer](backup-azure-sql-database.md#feature-consideration-and-limitations).

## <a name="sql-server-permissions"></a>SQL Server-behörigheter

Konfigurera skydd för en SQL Server-databas på en virtuell dator i **AzureBackupWindowsWorkload** tillägget måste vara installerad på den virtuella datorn. Om du får felet, **UserErrorSQLNoSysadminMembership**, betyder det SQL-instansen inte har behörighet för säkerhetskopiering. Om du vill åtgärda det här felet följer du stegen i [ange behörigheter för virtuella datorer inte finns i marketplace med SQL](backup-azure-sql-database.md#set-vm-permissions).

## <a name="backup-type-unsupported"></a>Typ av säkerhetskopiering stöds inte

| Severity | Beskrivning | Möjliga orsaker | Rekommenderad åtgärd |
|---|---|---|---|
| Varning | Aktuella inställningar för den här databasen stöder inte vissa typer av säkerhetskopieringstyper som finns i den associerade principen. | <li>**Master DB**: Endast en fullständig säkerhetskopiering databasåtgärd kan utföras i master-databasen; varken **differentiell** säkerhetskopiering eller transaktion **loggar** säkerhetskopiering stöds. </li> <li>Alla databaser i **enkla återställningsmodellen** tillåter inte transaktionen **loggar** säkerhetskopia som ska vidtas.</li> | Ändra databasinställningarna så att alla säkerhetskopieringstyper i principen stöds. Du kan också ändra den aktuella principen om du vill inkludera endast säkerhetskopiering typer som stöds. I annat fall stöds inte säkerhetskopieringstyper kommer att åsidosättas vid schemalagd säkerhetskopiering eller säkerhetskopieringen misslyckas för ad hoc-säkerhetskopiering.


## <a name="usererrorsqlpodoesnotsupportbackuptype"></a>UserErrorSQLPODoesNotSupportBackupType

| Felmeddelande | Möjliga orsaker | Rekommenderad åtgärd |
|---|---|---|
| Den här SQL database stöder inte den begärda säkerhetskopieringstypen. | Inträffar när databasens Återställningsmodell inte tillåter den begärda säkerhetskopieringstypen. Felet kan inträffa i följande situationer: <br/><ul><li>En databas med en enkel återställningsprocess tillåter inte loggsäkerhetskopiering.</li><li>Differentiella och loggbaserade säkerhetskopieringar tillåts inte för en databas.</li></ul>Mer information finns i den [återställningsmodeller för SQL](https://docs.microsoft.com/sql/relational-databases/backup-restore/recovery-models-sql-server) dokumentation. | Om loggsäkerhetskopieringen misslyckas av DB i enkelt återställningsläge, prova något av följande alternativ:<ul><li>Om databasen är i enkelt återställningsläge, inaktivera loggsäkerhetskopior.</li><li>Använd den [dokumentation om SQL](https://docs.microsoft.com/sql/relational-databases/backup-restore/view-or-change-the-recovery-model-of-a-database-sql-server) ändra databasens Återställningsmodell till Full eller Bulk Logged. </li><li> Om du har en standard för att säkerhetskopiera flera databaser som inte kan ändras om du inte vill ändra återställningsmodellen och ignorera felet. Din fullständiga och differentiella säkerhetskopieringar fungerar per schema. Säkerhetskopior av kommer att hoppas över, som förväntas i det här fallet.</li></ul>Om det är en Master-databasen och du har konfigurerat differentiell eller log säkerhetskopiering, Använd någon av följande steg:<ul><li>Använd portalen för att ändra säkerhetskopieringsprincipen schemat för huvudservern databasen till Full.</li><li>Om du har en standard för att säkerhetskopiera flera databaser som inte kan ändras kan du ignorera felet. Fullständiga säkerhetskopieringen fungerar per schema. Differentiell eller log säkerhetskopieringar göras inte som förväntas i det här fallet.</li></ul> |
| Åtgärden avbröts eftersom en motstridig åtgärd redan körs på samma databas. | Se den [blogginlägg om hur du säkerhetskopierar och återställer begränsningar](https://blogs.msdn.microsoft.com/arvindsh/2008/12/30/concurrency-of-full-differential-and-log-backups-on-the-same-database) som körs samtidigt.| [Använda SQL Server Management Studio (SSMS) för att övervaka säkerhetskopieringsjobb.](manage-monitor-sql-database-backup.md) När den hindrande åtgärden misslyckas, startar du om åtgärden.|

## <a name="usererrorsqlpodoesnotexist"></a>UserErrorSQLPODoesNotExist

| Felmeddelande | Möjliga orsaker | Rekommenderad åtgärd |
|---|---|---|
| SQL-databasen finns inte. | Databasen har antingen tagits bort eller bytt namn. | Kontrollera om databasen av misstag har tagits bort eller bytt namn.<br/><br/> Om databasen tagits bort av misstag, om du vill fortsätta säkerhetskopiering, återställa databasen till den ursprungliga platsen.<br/><br/> Om du tog bort den och inte behöver framtida säkerhetskopior i Recovery Services-valv på [Avbryt säkerhetskopiering med ”ta bort/avinstallationsalternativ”](manage-monitor-sql-database-backup.md).

## <a name="usererrorsqllsnvalidationfailure"></a>UserErrorSQLLSNValidationFailure

| Felmeddelande | Möjliga orsaker | Rekommenderad åtgärd |
|---|---|---|
| Loggkedjan är bruten. | Databasen eller den virtuella datorn säkerhetskopieras med hjälp av en annan säkerhetskopieringslösning som trunkerar loggkedjan.|<ul><li>Kontrollera om en annan lösning för säkerhetskopiering eller skript används. I så fall kan du stoppa den andra lösningen för säkerhetskopiering. </li><li>Om säkerhetskopieringen har en ad hoc-loggsäkerhetskopiering, utlösa en fullständig säkerhetskopiering för att starta en ny loggkedjan. Ingen åtgärd krävs för schemalagda säkerhetskopieringar, eftersom Azure Backup-tjänsten kommer automatiskt att aktivera en fullständig säkerhetskopiering för att åtgärda problemet.</li>|

## <a name="usererroropeningsqlconnection"></a>UserErrorOpeningSQLConnection

| Felmeddelande | Möjliga orsaker | Rekommenderad åtgärd |
|---|---|---|
| Azure Backup är inte kan ansluta till SQL-instansen. | Azure Backup kan inte ansluta till SQL-instansen. | Använd ytterligare information på menyn Azure portal fel för att begränsa den bakomliggande orsaken. Referera till [SQL felsöka säkerhetskopiering av](https://docs.microsoft.com/sql/database-engine/configure-windows/troubleshoot-connecting-to-the-sql-server-database-engine) åtgärda felet.<br/><ul><li>Om standardinställningarna för SQL inte tillåter fjärranslutningar, ändra inställningarna. Se följande artiklar för information om hur du ändrar inställningarna.<ul><li>[MSSQLSERVER_-1](/previous-versions/sql/sql-server-2016/bb326495(v=sql.130))</li><li>[MSSQLSERVER_2](/sql/relational-databases/errors-events/mssqlserver-2-database-engine-error)</li><li>[MSSQLSERVER_53](/sql/relational-databases/errors-events/mssqlserver-53-database-engine-error)</li></ul></li></ul><ul><li>Om det finns problem med inloggningen, referera till den via länkarna kan åtgärdas nedan:<ul><li>[MSSQLSERVER_18456](/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error)</li><li>[MSSQLSERVER_18452](/sql/relational-databases/errors-events/mssqlserver-18452-database-engine-error)</li></ul></li></ul> |

## <a name="usererrorparentfullbackupmissing"></a>UserErrorParentFullBackupMissing

| Felmeddelande | Möjliga orsaker | Rekommenderad åtgärd |
|---|---|---|
| Första fullständig säkerhetskopiering saknas för den här datakällan. | Fullständig säkerhetskopiering saknas för databasen. Logg- och differentiella säkerhetskopieringar överordnad till en fullständig säkerhetskopia, så att fullständiga säkerhetskopior måste du vidta innan du aktiverar RDC eller säkerhetskopior. | Utlös en ad hoc-fullständig säkerhetskopiering.   |

## <a name="usererrorbackupfailedastransactionlogisfull"></a>UserErrorBackupFailedAsTransactionLogIsFull

| Felmeddelande | Möjliga orsaker | Rekommenderad åtgärd |
|---|---|---|
| Det går inte att säkerhetskopiera eftersom transaktionsloggen för datakällan är full. | Transaktionell loggutrymmet för databasen är full. | Om du vill åtgärda problemet genom att referera till den [dokumentation om SQL](https://docs.microsoft.com/sql/relational-databases/errors-events/mssqlserver-9002-database-engine-error). |

## <a name="usererrorcannotrestoreexistingdbwithoutforceoverwrite"></a>UserErrorCannotRestoreExistingDBWithoutForceOverwrite

| Felmeddelande | Möjliga orsaker | Rekommenderad åtgärd |
|---|---|---|
| Databasen med samma namn finns redan på målplatsen | Återställningsmål mål har redan en databas med samma namn.  | <ul><li>Ändra namnet på mål-databasen</li><li>Du kan också använda alternativet force överskrivning på sidan för återställning</li> |

## <a name="usererrorrestorefaileddatabasecannotbeofflined"></a>UserErrorRestoreFailedDatabaseCannotBeOfflined

| Felmeddelande | Möjliga orsaker | Rekommenderad åtgärd |
|---|---|---|
| Det gick inte att återställa eftersom databasen inte kunde försättas offline. | När du gör en återställning måldatabasen som behöver tas offline. Azure Backup kan inte ta med dessa data offline. | Använd ytterligare information på menyn Azure portal fel för att begränsa den bakomliggande orsaken. Mer information finns i den [dokumentation om SQL](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-a-database-backup-using-ssms). |

##  <a name="usererrorcannotfindservercertificatewiththumbprint"></a>UserErrorCannotFindServerCertificateWithThumbprint

| Felmeddelande | Möjliga orsaker | Rekommenderad åtgärd |
|---|---|---|
| Det går inte att hitta servercertifikat med tumavtryck på målet. | Huvudservern databasen på mål-instansen har inte ett giltigt tumavtryck. | Importera giltigt certifikat-tumavtrycket används på målinstansen på målinstansen. |

## <a name="usererrorrestorenotpossiblebecauselogbackupcontainsbulkloggedchanges"></a>UserErrorRestoreNotPossibleBecauseLogBackupContainsBulkLoggedChanges

| Felmeddelande | Möjliga orsaker | Rekommenderad åtgärd |
|---|---|---|
| Loggsäkerhetskopian för återställning innehåller massloggade ändringar. De kan inte användas för att stoppa vid en godtycklig tidpunkt enligt SQL-riktlinjerna. | När en databas är i bulk loggade återställningsläge, kan data mellan en massloggade transaktion och nästa log-transaktionen inte återställas. | Välj en annan tidpunkt för återställning. [Läs mer](https://docs.microsoft.com/previous-versions/sql/sql-server-2008-r2/ms186229(v=sql.105))


## <a name="fabricsvcbackuppreferencecheckfailedusererror"></a>FabricSvcBackupPreferenceCheckFailedUserError

| Felmeddelande | Möjliga orsaker | Rekommenderad åtgärd |
|---|---|---|
| Säkerhetskopieringsinställningen för SQL AlwaysOn-tillgänglighetsgruppen uppfylls inte eftersom vissa noder i tillgänglighetsgruppen inte är registrerade. | Noder som krävs för att utföra säkerhetskopieringar har registrerats inte eller inte kan nås. | <ul><li>Kontrollera att alla noder som krävs för att utföra säkerhetskopieringar av den här databasen är registrerade och felfria och försök sedan igen.</li><li>Ändra SQL Always On Availability Group inställning för säkerhetskopiering.</li></ul> |

## <a name="vmnotinrunningstateusererror"></a>VMNotInRunningStateUserError

| Felmeddelande | Möjliga orsaker | Rekommenderad åtgärd |
|---|---|---|
| SQLServer-dator är antingen avstängd eller inte tillgänglig för Azure Backup-tjänsten. | Virtuella datorn stängs av | Se till att SQLServer körs. |

## <a name="guestagentstatusunavailableusererror"></a>GuestAgentStatusUnavailableUserError

| Felmeddelande | Möjliga orsaker | Rekommenderad åtgärd |
|---|---|---|
| Azure Backup-tjänsten använder Virtuella Azure-gästagenten för säkerhetskopiering men gästagenten är inte tillgängliga på målservern. | Gästagenten är inte aktiverat eller är skadad | [Installera virtuella datorns gästagent](../virtual-machines/extensions/agent-windows.md) manuellt. |

## <a name="autoprotectioncancelledornotvalid"></a>AutoProtectionCancelledOrNotValid

| Felmeddelande | Möjliga orsaker | Rekommenderad åtgärd |
|---|---|---|
| Automatiskt skydd avsett har antingen tagits bort eller är inte längre giltig. | När du aktiverar automatiskt skydd på en SQL-instans **Konfigurera säkerhetskopiering** uppgifter har körts för alla databaser i instansen. Om du inaktiverar automatiskt skydd medan jobben körs, kommer **pågår** jobb avbryts med den här felkoden. | Aktivera automatiskt skydd igen att skydda alla återstående databaser. |

## <a name="re-registration-failures"></a>Omregistrering fel

Sök efter en eller flera av de [symptom](#symptoms) innan du utlöser åtgärden registrera igen.

### <a name="symptoms"></a>Symtom

* Alla åtgärder som säkerhetskopiering, återställning och konfigurera säkerhetskopiering misslyckas på den virtuella datorn med någon av följande felkoder: **WorkloadExtensionNotReachable**, **UserErrorWorkloadExtensionNotInstalled**, **WorkloadExtensionNotPresent**, **WorkloadExtensionDidntDequeueMsg**
* Den **Status för säkerhetskopiering** för säkerhetskopiering objekt som visar **kan inte nås**. Även om du måste försäkra dig om att alla andra orsaker som kan också leda till samma status:

  * Bristande behörighet att utföra relaterade säkerhetskopieringsåtgärder på den virtuella datorn  
  * Virtuell dator har stängts av på grund av som säkerhetskopieringar inte kan äga rum
  * Nätverksproblem  

    ![Registrera virtuell dator](./media/backup-azure-sql-database/re-register-vm.png)

* När det gäller alltid på tillgänglighetsgruppen igång säkerhetskopieringarna misslyckas när du har ändrat inställning för säkerhetskopiering eller när det uppstod ett fel

### <a name="causes"></a>Orsaker
Dessa problem kan uppstå på grund av en eller flera av följande orsaker:

  * Tillägget har tagits bort eller avinstallerats från portalen 
  * Tillägget har avinstallerats från den **Kontrollpanelen** för den virtuella datorn under **avinstallera eller ändra ett Program** UI
  * Virtuell dator har återställts bakåt i tiden med återställning av diskar som är på plats
  * Virtuella datorer stängdes av under en längre period på grund av som tilläggskonfigurationen på den upphört att gälla
  * Virtuell dator har tagits bort och en annan virtuell dator har skapats med samma namn, i samma resursgrupp som den borttagna virtuella datorn
  * En av noderna AG fick inte konfigurationen för fullständig säkerhetskopiering, detta kan inträffa antingen vid tidpunkten för tillgänglighet av gruppregistrering till valvet eller när en ny nod läggs  <br>
    I situationerna ovan rekommenderas att utlösa Omregistrera åtgärden på den virtuella datorn. Det här alternativet är bara tillgänglig via PowerShell och kommer snart att vara tillgängliga på Azure Portal.

## <a name="files-size-limit-beyond-which-restore-happens-to-default-path"></a>Filens storleksgräns utöver vilka återställning sker till standardsökvägen

Den totala strängstorleken på filerna beror inte bara på hur många filer utan även på deras namn och sökvägar. Hämta logiskt filnamn och fysiska sökvägen för var och en av databasfilerna.
Du kan använda SQL-frågan som anges nedan:

  ```
  SELECT mf.name AS LogicalName, Physical_Name AS Location FROM sys.master_files mf
                 INNER JOIN sys.databases db ON db.database_id = mf.database_id
                 WHERE db.name = N'<Database Name>'"
 ```

Nu ordna dem i det format som anges nedan:

  ```[{"path":"<Location>","logicalName":"<LogicalName>","isDir":false},{"path":"<Location>","logicalName":"<LogicalName>","isDir":false}]}
  ```

Exempel:

  ```[{"path":"F:\\Data\\TestDB12.mdf","logicalName":"TestDB12","isDir":false},{"path":"F:\\Log\\TestDB12_log.ldf","logicalName":"TestDB12_log","isDir":false}]}
  ```

Om strängstorleken på det innehåll som anges ovan överstiger 20 000 byte, sedan databasfilerna lagras på olika sätt och under återställningen kommer du inte att kunna ange sökvägen till målfilen för återställning. Filerna kommer att återställas till standard SQL-sökvägen som tillhandahålls av SQL Server.

### <a name="override-the-default-target-restore-file-path"></a>Åsidosätt standardsökvägen till målfilen återställning

Du kan åsidosätta sökvägen till målfilen återställning under återställningen genom att placera en JSON-fil som innehåller mappningen av databasfilen målsökväg för återställning. Skapa en fil för den här `database_name.json` och placera den på plats *c:\Program\Microsoft Files\Azure arbetsbelastning Backup\bin\plugins\SQL*.

Innehållet i filen bör vara i formatet nedan:
  ```[
    {
      "Path": "<Restore_Path>",
      "LogicalName": "<LogicalName>",
      "IsDir": "false"
    },
    {
      "Path": "<Restore_Path>",
      "LogicalName": "LogicalName",
      "IsDir": "false"
    },  
  ]
  ```

Exempel:

  ```[
      {
        "Path": "F:\\Data\\testdb2_1546408741449456.mdf",
        "LogicalName": "testdb7",
       "IsDir": "false"
      },
      {
        "Path": "F:\\Log\\testdb2_log_1546408741449456.ldf",
        "LogicalName": "testdb7_log",
        "IsDir": "false"
      },  
    ]
  ```

 
Du kan få det logiska namnet för databasfilen med hjälp av SQL-frågan som anges nedan i innehållet ovan:

```
SELECT mf.name AS LogicalName FROM sys.master_files mf
                INNER JOIN sys.databases db ON db.database_id = mf.database_id
                WHERE db.name = N'<Database Name>'"
  ```


Den här filen ska placeras innan du utlöser återställningen.

## <a name="next-steps"></a>Nästa steg

Läs mer om Azure Backup för SQL Server-datorer (förhandsversion), [Azure Backup för SQL-datorer (förhandsversion)](../virtual-machines/windows/sql/virtual-machines-windows-sql-backup-recovery.md#azbackup).
