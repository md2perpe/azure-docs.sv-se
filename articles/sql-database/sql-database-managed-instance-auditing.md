---
title: Azure SQL Database managed instance granskning | Microsoft Docs
description: Lär dig hur du kommer igång med Azure SQL Database managed instance granskning med T-SQL
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
f1_keywords:
- mi.azure.sqlaudit.general.f1
author: vainolo
ms.author: arib
ms.reviewer: vanto
manager: craigg
ms.date: 04/08/2019
ms.openlocfilehash: 6ada2a5e505bfe37f4f9a956570d8b6f38f55e55
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60702936"
---
# <a name="get-started-with-azure-sql-database-managed-instance-auditing"></a>Kom igång med Azure SQL Database managed instance granskning

[Hanterad instans](sql-database-managed-instance.md) granskning spårar databashändelser och skriver dem till en granskningslogg i ditt Azure storage-konto. Granskning också:

- Hjälper dig att upprätthålla regelefterlevnad, Förstå Databasaktivitet och få insyn i avvikelser och fel som kan tyda på affärsproblem eller potentiella säkerhetsöverträdelser.
- Aktiverar och underlättar infört efterlevnadsstandarder, även om det inte garanterar efterlevnad. Mer information om Azure program som stöd för överensstämmelse med standarder, finns i den [Azure Trust Center](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) där du hittar den senaste listan med SQL Database-kompatibilitetscertifieringar.

## <a name="set-up-auditing-for-your-server-to-azure-storage"></a>Konfigurera granskning för din server till Azure storage

I följande avsnitt beskrivs konfigurationen av granskning på din hanterade instans.

1. Gå till [Azure-portalen](https://portal.azure.com).
1. Skapa ett Azure Storage **behållare** där granskningsloggar lagras.

   1. Gå till Azure Storage där du vill lagra dina granskningsloggar.

      > [!IMPORTANT]
      > Använd ett lagringskonto i samma region som den hanterade instansen om du vill undvika läsningar/skrivningar över flera regioner.

   1. I storage-konto går du till **översikt** och klicka på **Blobar**.

      ![Azure Blob-widget](./media/sql-managed-instance-auditing/1_blobs_widget.png)

   1. I den översta menyn klickar du på **+ behållare** att skapa en ny behållare.

      ![Skapa blob-behållare ikon](./media/sql-managed-instance-auditing/2_create_container_button.png)

   1. Ange en behållare **namn**, anger offentlig åtkomst till **privata**, och klicka sedan på **OK**.

      ![Skapa konfigurationen för blob-behållaren](./media/sql-managed-instance-auditing/3_create_container_config.png)

1. När du har skapat behållaren för granskningen loggar det finns två sätt att konfigurera det som mål för granskningsloggarna: [med T-SQL](#blobtsql) eller [med hjälp av SQL Server Management Studio (SSMS) Användargränssnittet](#blobssms):

   - <a id="blobtsql"></a>Konfigurera BLOB-lagring för granskningsloggar med T-SQL:

     1. Klicka på den nyligen skapade behållaren i listan med behållare och klicka sedan på **behållaregenskaperna**.

        ![Egenskapsknappen för BLOB-behållare](./media/sql-managed-instance-auditing/4_container_properties_button.png)

     1. Kopiera URL: en för behållaren genom att klicka på ikonen för kopiera och spara URL: en (till exempel i Anteckningar) för framtida användning. URL-format för behållaren ska vara `https://<StorageName>.blob.core.windows.net/<ContainerName>`

        ![BLOB-behållare Kopiera URL](./media/sql-managed-instance-auditing/5_container_copy_name.png)

     1. Skapa ett Azure Storage **SAS-Token** att ge hanterad instans granskning åtkomsträttigheter till lagringskontot:

        - Gå till Azure Storage-konto där du skapade behållaren i föregående steg.

        - Klicka på **signatur för delad åtkomst** på menyn Inställningar.

          ![Delad åtkomst signatur-ikonen i inställningsmenyn för lagring](./media/sql-managed-instance-auditing/6_storage_settings_menu.png)

        - Konfigurera SAS enligt följande:

          - **Tillåtna tjänster**: Blob

          - **Startdatum**: för att undvika tidszon-relaterade problem rekommenderar vi att du använder gårdagens datum

          - **Slutdatum**: Välj det datum då den här SAS-Token upphör att gälla

            > [!NOTE]
            > Förnya token vid utgången att undvika fel för granskning.

          - Klicka sedan på **Generera signatur för delad åtkomst (SAS)** .
            
            ![SAS-konfiguration](./media/sql-managed-instance-auditing/7_sas_configure.png)

        - När du klickar på Generera SAS, längst SAS-Token ned. Kopiera token genom att klicka på ikonen för kopiera och spara den (till exempel i Anteckningar) för framtida användning.

          ![Kopiera SAS-token](./media/sql-managed-instance-auditing/8_sas_copy.png)

          > [!IMPORTANT]
          > Ta bort frågetecknet (””?) tecken från början av token.

     1. Anslut till din hanterade instans via SQL Server Management Studio (SSMS) eller något annat verktyg som stöds.

     1. Kör följande T-SQL-instruktion till **skapar du en ny autentiseringsuppgift** med hjälp av behållarens Webbadress och SAS-Token som du skapade i föregående steg:

        ```SQL
        CREATE CREDENTIAL [<container_url>]
        WITH IDENTITY='SHARED ACCESS SIGNATURE',
        SECRET = '<SAS KEY>'
        GO
        ```

     1. Kör följande T-SQL-instruktion för att skapa en ny Server Audit (Välj granskningsnamnet på din egen, använda behållarens Webbadress som du skapade i föregående steg). Om inte anges `RETENTION_DAYS` standardvärdet är 0 (obegränsad kvarhållning):

        ```SQL
        CREATE SERVER AUDIT [<your_audit_name>]
        TO URL ( PATH ='<container_url>' [, RETENTION_DAYS =  integer ])
        GO
        ```

        1. Fortsätt genom [skapar en Server Audit Specification eller Databasgranskningsspecifikation](#createspec)

   - <a id="blobssms"></a>Konfigurera blob-lagring för granskningsloggar med hjälp av SQL Server Management Studio (SSMS) 18 (förhandsversion):

     1. Anslut till den hantera instansen med SQL Server Management Studio (SSMS) Användargränssnittet.

     1. Expandera rot anteckna Object Explorer.

     1. Expandera den **Security** noden, högerklickar du på den **granskningar** noden och klicka på ”ny granskning”:

        ![Expandera säkerhet och granska nod](./media/sql-managed-instance-auditing/10_mi_SSMS_new_audit.png)

     1. Se till att ”URL” är markerad i **Audit mål** och klicka på **Bläddra**:

        ![Bläddra i Azure-lagring](./media/sql-managed-instance-auditing/11_mi_SSMS_audit_browse.png)

     1. (Valfritt) Logga in på ditt Azure-konto:

        ![Logga in på Azure](./media/sql-managed-instance-auditing/12_mi_SSMS_sign_in_to_azure.png)

     1. Välj en prenumeration, lagringskontot och Blob-behållare från listrutorna eller skapa en egen behållare genom att klicka på **skapa**. När du är klar klickar du på **OK**:

        ![Välj Azure-prenumeration, lagringskontot och blob-behållare](./media/sql-managed-instance-auditing/13_mi_SSMS_select_subscription_account_container.png)

     1. Klicka på **OK** i dialogrutan ”Skapa granskning”.

1. <a id="createspec"></a>När du har konfigurerat skapa Blob-behållaren som mål för granskningsloggar, en Server granska specifikationen eller databasen granska specifikationen precis som för SQL Server:

   - [Skapa Server audit specification T-SQL-guide](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-specification-transact-sql)
   - [Skapa databas audit specification T-SQL-guide](https://docs.microsoft.com/sql/t-sql/statements/create-database-audit-specification-transact-sql)

1. Aktivera servergranskning som du skapade i steg 6:

    ```SQL
    ALTER SERVER AUDIT [<your_audit_name>]
    WITH (STATE=ON);
    GO
    ```

Ytterligare information:

- [Granskning skillnaderna mellan enskilda databaser, elastisk pool, s och hanterade instanser i Azure SQL Database och databaser i SQL Server](#auditing-differences-between-databases-in-azure-sql-database-and-databases-in-sql-server)
- [SKAPA SERVERGRANSKNING](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-transact-sql)
- [ALTER SERVER AUDIT](https://docs.microsoft.com/sql/t-sql/statements/alter-server-audit-transact-sql)

## <a name="set-up-auditing-for-your-server-to-event-hub-or-azure-monitor-logs"></a>Konfigurera granskning för din server till Event Hub eller Azure Monitor-loggar

Granskningsloggar från en hanterad instans kan skickas till och med Hubs eller Azure Monitor-loggar. Det här avsnittet beskrivs hur du konfigurerar detta:

1. Navigera i den [Azure-portalen](https://portal.azure.com/) till den hanterade instansen.

2. Klicka på **diagnostikinställningar**.

3. Klicka på **slå på diagnostik**. Om diagnostik är redan aktiverad i *+ Lägg till diagnostikinställning* visas i stället.

4. Välj **SQLSecurityAuditEvents** i listan över loggar.

5. Välj ett mål för granskningshändelser - Event Hub, Azure Monitor-loggar eller båda. Konfigurera för varje mål de obligatoriska parametrarna (t.ex. Log Analytics-arbetsytan).

6. Klicka på **Spara**.

    ![Konfigurera diagnostikinställningar](./media/sql-managed-instance-auditing/9_mi_configure_diagnostics.png)

7. Ansluta till den hanterade instansen med hjälp av **SQL Server Management Studio (SSMS)** eller någon annan klient som stöds.

8. Kör följande T-SQL-instruktion för att skapa en servergranskning:

    ```SQL
    CREATE SERVER AUDIT [<your_audit_name>] TO EXTERNAL_MONITOR;
    GO
    ```

9. Skapa en granskningsspecifikation för servern eller databasgranskningsspecifikation precis som för SQL Server:

   - [Skapa Server audit specification T-SQL-guide](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-specification-transact-sql)
   - [Skapa databas audit specification T-SQL-guide](https://docs.microsoft.com/sql/t-sql/statements/create-database-audit-specification-transact-sql)

10. Aktivera servergranskning som skapades i steg 8:
 
    ```SQL
    ALTER SERVER AUDIT [<your_audit_name>] WITH (STATE=ON);
    GO
    ```

## <a name="consume-audit-logs"></a>Använd granskningsloggar

### <a name="consume-logs-stored-in-azure-storage"></a>Använda loggar som lagras i Azure Storage

Det finns flera metoder som du kan använda för att visa blob granskningsloggar.

- Använd systemfunktionen `sys.fn_get_audit_file` (T-SQL) att returnera granskningsloggdata i tabellformat. Mer information om hur du använder den här funktionen finns i den [sys.fn_get_audit_file dokumentation](https://docs.microsoft.com/sql/relational-databases/system-functions/sys-fn-get-audit-file-transact-sql).

- Du kan utforska granskningsloggar genom att använda ett verktyg som [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/). Granskningsloggarna sparas i Azure storage som en samling blobfiler i en behållare som har definierats för att lagra granskningsloggar. Ytterligare information om hierarkin för mappen storage namngivningskonventioner och loggformat, finns det [referens till Blob granskningslogg Format](https://go.microsoft.com/fwlink/?linkid=829599).

- En fullständig lista över audit log förbrukningsmetoder finns i den [Kom igång med SQL-databasgranskning](sql-database-auditing.md).

### <a name="consume-logs-stored-in-event-hub"></a>Använda loggar som lagras i Event Hub

Om du vill använda granskning loggar data från Event Hub, behöver du ställer in en dataström som förbrukar händelser och skriva dem till ett mål. Mer information finns i dokumentation om Azure Event Hubs.

### <a name="consume-and-analyze-logs-stored-in-azure-monitor-logs"></a>Använda och analysera loggar som lagras i Azure Monitor-loggar

Om granskningsloggarna skrivs till Azure Monitor-loggar, är de tillgängliga i Log Analytics-arbetsyta där du kan köra avancerade sökningar på granskningsdata. Som en startpunkt, navigera till Log Analytics-arbetsytan och under *Allmänt* avsnittet klickar du på *loggar* och ange en enkel fråga, till exempel: `search "SQLSecurityAuditEvents"` att visa granskningen loggar.  

Azure Monitor-loggar ger dig operational realtidsinsikter med integrerad sökning och anpassade instrumentpaneler för snabb analys av miljontals poster över alla dina arbetsbelastningar och servrar. Ytterligare användbar information om Azure Monitor-loggar frågespråket och kommandon finns i [Azure Monitor loggar sökreferens](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview).

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="auditing-differences-between-databases-in-azure-sql-database-and-databases-in-sql-server"></a>Granskning skillnaderna mellan databaser i Azure SQL Database och databaser i SQL Server

De viktigaste skillnaderna mellan granskning i databaser i Azure SQL Database och databaser i SQL Server är:

- Med alternativet för hanterad instans-distribution i Azure SQL Database-granskning fungerar på servernivå och lagrar `.xel` loggfiler i Azure Blob storage.
- Med enkel databas och elastisk pool distributionsalternativ i Azure SQL Database-granskning fungerar på databasnivå.
- I SQL Server lokalt per virtuell datorer, granska fungerar på servern nivå, men lagrar händelser på filer system/windows-händelseloggar.

XEvent granskning i hanterad instans stöder prestandamål i Azure Blob storage. Fil- och windows-loggar är **stöds inte**.

Nyckeln skillnader i den `CREATE AUDIT` syntax för granskning till Azure Blob storage är:

- En ny syntax `TO URL` tillhandahålls och kan du ange URL: en för Azure blob Storage-behållare där de `.xel` filerna är placerade.
- En ny syntax `TO EXTERNAL MONITOR` är tillgänglig för att även Hub och Azure Monitor-loggar mål.
- Syntaxen `TO FILE` är **stöds inte** eftersom SQL-databas inte kan komma åt Windows-filresurser.
- Stäng av alternativet är **stöds inte**.
- `queue_delay` 0 är **stöds inte**.

## <a name="next-steps"></a>Nästa steg

- En fullständig lista över audit log förbrukningsmetoder finns i den [Kom igång med SQL-databasgranskning](sql-database-auditing.md).
- Mer information om Azure program som stöd för överensstämmelse med standarder, finns i den [Azure Trust Center](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) där du hittar den senaste listan med SQL Database-kompatibilitetscertifieringar.

<!--Image references-->









