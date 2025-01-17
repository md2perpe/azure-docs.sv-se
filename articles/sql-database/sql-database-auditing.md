---
title: Kom igång med Azure SQL database-granskning | Microsoft Docs
description: Använda Azure SQL database-granskning för att spåra databashändelser till en granskningslogg.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: vainolo
ms.author: arib
ms.reviewer: vanto
manager: craigg
ms.date: 04/16/2019
ms.openlocfilehash: 1b3a6a18d10b9d9f6ab6456ae2911e54f5c56a71
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/03/2019
ms.locfileid: "67544091"
---
# <a name="get-started-with-sql-database-auditing"></a>Kom igång med SQL-databasgranskning

Granskning för Azure [SQL Database](sql-database-technical-overview.md) och [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) spårar databasen händelser och skriver dem till en granskningslogg i ditt Azure storage-konto, OMS-arbetsyta eller Event Hubs. Granskning också:

- Hjälper dig att upprätthålla regelefterlevnad, Förstå Databasaktivitet och få insyn i avvikelser och fel som kan tyda på affärsproblem eller potentiella säkerhetsöverträdelser.

- Aktiverar och underlättar infört efterlevnadsstandarder, även om det inte garanterar efterlevnad. Mer information om Azure program som stöd för överensstämmelse med standarder, finns i den [Azure Trust Center](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) där du hittar den senaste listan med SQL Database-kompatibilitetscertifieringar.


> [!NOTE] 
> Det här avsnittet gäller för Azure SQL-servern, och för både SQL Database- och SQL Data Warehouse-databaser som skapas på Azure SQL-servern. För enkelhetens skull används SQL Database när det gäller både SQL Database och SQL Data Warehouse.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]


## <a id="subheading-1"></a>Azure SQL database auditing översikt

Du kan använda SQL database auditing till att:

- **Behåll** granskningsspårning av valda händelser. Du kan definiera kategorier av databasåtgärder som ska granskas.
- **Rapporten** på Databasaktivitet. Du kan använda förkonfigurerade rapporter och en instrumentpanel för att komma igång snabbt med aktiviteten och rapportera händelser.
- **Analysera** rapporter. Du hittar misstänkta händelser, ovanliga aktiviteter och trender.

> [!IMPORTANT]
> Granskningsloggarna skrivs till **Tilläggsblobbar** i Azure Blob storage på din Azure-prenumeration.
>
> - Alla storage-typer (v1, v2, blob) stöds.
> - Alla lagringskonfigurationer replikering stöds.
> - **Premiumlagring** är för närvarande **stöds inte**.
> - **Storage i det virtuella nätverket** är för närvarande **stöds inte**.
> - **Storage bakom en brandvägg** är för närvarande **stöds inte**

## <a id="subheading-8"></a>Definiera på servernivå och databasnivå granskningsprincip

En granskningsprincip kan definieras för en specifik databas eller som en standardprincip för servern:

- En Serverprincipen gäller för alla befintliga och nya databaser på servern.

- Om *blobgranskning är aktiverat*, den *alltid gäller för databasen*. Databasen granskas oavsett databasen granskningsinställningar.

- Att aktivera blobbgranskning på databasen eller datalagret, samt för att aktivera den på servern har *inte* åsidosätta eller ändra några av inställningarna för den blobgranskning. Båda granskningar kommer att finnas sida vid sida. Med andra ord granskas databasen två gånger i parallella; en gång av princip för server och en gång av princip för databasen.

   > [!NOTE]
   > Du bör undvika att aktivera både blobgranskning och databasen blobbgranskning tillsammans, såvida inte:
    > - Du vill använda en annan *lagringskonto* eller *kvarhållningsperioden* för en viss databas.
    > - Du vill granska händelsetyper eller kategorier för en viss databas som skiljer sig från resten av databaser på servern. Du kan till exempel ha tabellen infogningar som behöver granskas endast för en viss databas.
   >
   > I annat fall rekommenderar vi att du aktiverar bara servernivå blobbgranskning och lämna granskning på databasnivå inaktiverad för alla databaser.

## <a id="subheading-2"></a>Konfigurera granskning för din databas

I följande avsnitt beskrivs konfigurationen av granskning med Azure portal.

1. Gå till [Azure-portalen](https://portal.azure.com).
2. Gå till **granskning** under rubriken säkerheten i din SQL database-server-fönstret.

    <a id="auditing-screenshot"></a>![Navigeringsfönstret][1]

3. Om du vill ställa in en granskningsprincip för server, kan du välja den **visa serverinställningar** länk på granskning database-sidan. Du kan sedan visa eller ändra serverns granskningsinställningar. Server granska principer gäller för alla befintliga och nya databaser på den här servern.

    ![Navigeringsfönstret][2]

4. Om du vill aktivera granskning på databasnivå kan växla **granskning** till **på**.

    Om servergranskning är aktiverat, kommer att finnas sida vid sida med server audit databasens konfigurerade granskning.

    ![Navigeringsfönstret][3]

5. **Nya** – nu har du flera alternativ för att konfigurera där granskningsloggar ska skrivas. Du kan skriva loggar till ett Azure storage-konto, till en Log Analytics-arbetsyta för användning av Azure Monitor-loggar eller till event hub för förbrukning med händelsehubben. Du kan konfigurera en kombination av dessa alternativ och granskningsloggar skrivs till var och en.

   > [!WARNING]
   > Aktivera granskning till Log Analytics kommer resultera i kostnader beroende på enligt datainmatningsfrekvensen. Tänk på den associerade kostnaden med detta [alternativet](https://azure.microsoft.com/pricing/details/monitor/), eller överväga att lagra granskningen loggar i ett Azure storage-konto.

    ![lagringsalternativ](./media/sql-database-auditing-get-started/auditing-select-destination.png)

6. Konfigurera granskning för skrivning loggar till ett lagringskonto, väljer **Storage** och öppna **lagringsinformation**. Välj Azure-lagringskontot där loggarna sparas och välj sedan kvarhållningsperioden. De gamla loggarna tas bort. Klicka sedan på **OK**.

    ![storage account](./media/sql-database-auditing-get-started/auditing_select_storage.png)

7. Konfigurera granskning för skrivning loggar på en Log Analytics-arbetsyta väljer **Log Analytics (förhandsversion)** och öppna **Log Analytics information**. Välj eller skapa Log Analytics-arbetsyta där loggarna sparas, och klicka sedan på **OK**.

    ![Log Analytics-arbetsyta](./media/sql-database-auditing-get-started/auditing_select_oms.png)

8. Konfigurera granskning för skrivning loggar till en händelsehubb, Välj **Event Hub (förhandsversion)** och öppna **information om Händelsehubb**. Välj händelsehubb där loggarna sparas, och klicka sedan på **OK**. Var noga med att event hub finns i samma region som din databas och server.

    ![Händelsehubb](./media/sql-database-auditing-get-started/auditing_select_event_hub.png)

9. Klicka på **Spara**.
10. Om du vill anpassa de granskade händelserna, kan du göra detta via [PowerShell-cmdletar](#subheading-7) eller [REST API](#subheading-9).
11. När du har konfigurerat inställningarna för granskning kan du aktivera funktionen för identifiering av nya hot och konfigurera e-postmeddelanden om du vill få säkerhetsaviseringar. När du använder hotidentifiering kan få du proaktiva varningar på avvikande databasaktiviteter som kan innebära potentiella säkerhetshot. Mer information finns i [komma igång med hotidentifiering](sql-database-threat-detection-get-started.md).

> [!IMPORTANT]
> Det går inte att aktivera granskning på en pausad Azure SQL Data Warehouse. Om du vill aktivera den pausning avregistrera datalagret.

> [!WARNING]
> Aktivera granskning på en server som har en Azure SQL Data Warehouse på den **resulterar i datalagret som återupptas och nytt pausas igen** som kan tillkomma i faktureringstjänstens avgifter.

## <a id="subheading-3"></a>Analysera granskningsloggar och rapporter

Om du har valt att skriva granskningsloggar till Azure Monitor-loggar:

- Använd den [Azure-portalen](https://portal.azure.com).  Öppna den aktuella databasen. Överst på databasens **granskning** klickar du på **visa granskningsloggar**.

    ![Visa granskningsloggar](./media/sql-database-auditing-get-started/7_auditing_get_started_blob_view_audit_logs.png)

- Klicka på **öppna i OMS** överst i den **granskningsposter** vyn loggar öppnas i Log Analytics, där du kan anpassa tidsintervallet och sökfrågan.

    ![Öppna i Log Analytics](./media/sql-database-auditing-get-started/auditing_open_in_oms.png)

- Du kan också du kan också komma åt granskningsloggarna från Log Analytics-bladet. Öppna Log Analytics-arbetsytan och under **Allmänt** klickar du på **loggar**. Du kan börja med en enkel fråga, till exempel: *Sök ”SQLSecurityAuditEvents”* att visa granskningen loggar.
    Härifrån kan du också använda [Azure Monitor loggar](../log-analytics/log-analytics-log-search.md) att köra avancerade sökningar på din granskningsloggdata. Azure Monitor-loggar ger dig operational realtidsinsikter med integrerad sökning och anpassade instrumentpaneler för snabb analys av miljontals poster över alla dina arbetsbelastningar och servrar. Ytterligare användbar information om Azure Monitor-loggar frågespråket och kommandon finns i [Azure Monitor loggar sökreferens](../log-analytics/log-analytics-log-search.md).

Om du har valt att skriva granskningsloggar till Event Hub:

- Om du vill använda granskning loggar data från Event Hub, behöver du ställer in en dataström som förbrukar händelser och skriva dem till ett mål. Mer information finns i [dokumentation om Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/).
- Granskningsloggar i Event Hub samlas i brödtexten i [Apache Avro](https://avro.apache.org/) händelser och lagras med JSON formatering med UTF-8-kodning. Du kan använda för att läsa granskningsloggarna [Avro verktyg](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview#use-avro-tools) eller liknande verktyg som bearbetar det här formatet.

Om du väljer att skriva granskningsloggar till ett Azure storage-konto, finns det flera metoder som du kan använda för att visa loggfilerna:

- Granskningsloggar räknas samman på kontot du valde i installationsprogrammet. Du kan utforska granskningsloggar genom att använda ett verktyg som [Azure Storage Explorer](https://storageexplorer.com/). I Azure storage granskningsloggarna sparas som en samling av blobfiler i en behållare med namnet **sqldbauditlogs**. Mer information om hierarkin för mappen storage namngivningskonventioner och loggformat, finns det [loggformat för SQL Database-granskning](https://go.microsoft.com/fwlink/?linkid=829599).

- Använd den [Azure-portalen](https://portal.azure.com).  Öppna den aktuella databasen. Överst på databasens **granskning** klickar du på **visa granskningsloggar**.

    ![Navigeringsfönstret][7]

    **Granskningsposter** öppnas, där du kommer att kunna visa loggarna.

  - Du kan visa specifika datum genom att klicka på **Filter** överst i den **granskningsposter** sidan.
  - Du kan växla mellan granskningsposter som har skapats av den *server granskningsprincip* och *databasen granskningsprincip* genom att ändra **granska källa**.
  - Du kan visa endast SQL-inmatning relaterade granskningsposter genom att kontrollera **visa enbart granskningsposter för SQL-inmatningar** kryssrutan.

       ![Navigeringsfönstret][8]

- Använd systemfunktionen **sys.fn_get_audit_file** (T-SQL) att returnera granskningsloggdata i tabellformat. Mer information om hur du använder den här funktionen finns i [sys.fn_get_audit_file](https://docs.microsoft.com/sql/relational-databases/system-functions/sys-fn-get-audit-file-transact-sql).

- Använd **sammanfoga granskningsfilerna** i SQL Server Management Studio (startar med SSMS 17):
    1. SSMS-menyn väljer **filen** > **öppna** > **sammanfoga granskningsfilerna**.

        ![Navigeringsfönstret][9]
    2. Den **lägga till granskningsfilerna** öppnas dialogrutan. Välj en av de **Lägg till** alternativ att välja om du vill slå samman granskningsfilerna från en lokal disk eller importera dem från Azure Storage. Du måste ange din information för Azure Storage och kontonyckel.

    3. När alla filer att slå samman har lagts till, klickar du på **OK** att slutföra merge-operation.

    4. Sammanfogade filen öppnas i SSMS, där du kan visa och analysera dem, samt exportera den till en XEL eller CSV-fil, eller till en tabell.

- Använda Powerbi. Du kan visa och analysera granskningsloggdata i Power BI. Mer information och för att komma åt en nedladdningsbar mall finns i [analysera granskningsloggdata i Power BI](https://blogs.msdn.microsoft.com/azuresqldbsupport/20../../sql-azure-blob-auditing-basic-power-bi-dashboard/).
- Ladda ned loggfiler från dina Azure Storage blob-behållare via portalen eller genom att använda ett verktyg som [Azure Storage Explorer](https://storageexplorer.com/).
  - När du har hämtat en loggfil lokalt, dubbelklicka på filen för att öppna, visa och analysera loggar i SSMS.
  - Du kan också hämta flera filer samtidigt via Azure Storage Explorer. Du gör detta genom att högerklicka på en viss undermapp och välj **Spara som** att spara i en lokal mapp.

- Ytterligare metoder:

  - När du hämtat flera filer eller en undermapp som innehåller loggfiler, kan du slå samman dem lokalt enligt beskrivningen i SSMS Merge granskningsfilerna anvisningarna som beskrivs ovan.
  - Visa blobbgranskning loggar programmässigt:

    - [Frågefiler utökade händelser](https://sqlscope.wordpress.com/20../../reading-extended-event-files-using-client-side-tools-only/) med hjälp av PowerShell.

## <a id="subheading-5"></a>Produktionsmetoder

<!--The description in this section refers to preceding screen captures.-->

### <a id="subheading-6">Granskning geo-replikerade databaser</a>

Med geo-replikerade databaser när du aktiverar granskning på den primära databasen har den sekundära databasen en identisk granskningsprincip. Det är också möjligt att ställa in granskning på den sekundära databasen genom att aktivera granskning på **sekundär server**, oberoende av den primära databasen.

- På servernivå (**rekommenderas**): Aktivera granskning på både den **primärservern** samt de **sekundär server** -primära och sekundära databaserna var granskas oberoende baserat på deras respektive princip på servernivå.
- På databasnivå: Granskning databasnivå för sekundära databaser kan bara konfigureras från den primära databasen granskningsinställningar.
  - Granskning måste vara aktiverat på den *primära databasen själva*, inte på servern.
  - När granskning har aktiverats på den primära databasen kan aktiveras det också på den sekundära databasen.

    >[!IMPORTANT]
    >Med granskning på databasnivå, ska lagringsinställningarna för den sekundära databasen vara identiskt för den primära databasen orsakar tvärregional trafik. Vi rekommenderar att du aktiverar endast serverniv och lämna granskning på databasnivå inaktiverad för alla databaser.
    > [!WARNING]
    > Med hjälp av event hub eller Azure Monitor-loggar som mål för granskningsloggar på servernivå stöds för närvarande inte för sekundär geo-replikerade databaser.

### <a id="subheading-6">Storage åtkomstnyckeln återskapades</a>

I produktion förmodligen du uppdatera dina storage-nycklar med jämna mellanrum. När du skriver granskningsloggar till Azure storage, måste du spara om granskningsprincipen när du uppdaterar dina nycklar. Processen är följande:

1. Öppna **lagringsinformation**. I den **Lagringsåtkomstnyckel** väljer **sekundära**, och klicka på **OK**. Klicka sedan på **spara** överst på konfigurationssidan för granskning.

    ![Navigeringsfönstret][5]
2. Gå till konfigurationssidan för lagring och återskapa den primära åtkomstnyckeln.

    ![Navigeringsfönstret][6]
3. Gå tillbaka till konfigurationssidan granskning växla lagringsåtkomstnyckel från sekundär till primär och klicka sedan på **OK**. Klicka sedan på **spara** överst på konfigurationssidan för granskning.
4. Gå tillbaka till konfigurationssidan för lagring och återskapa den sekundära åtkomstnyckeln (som förberedelse inför nästa tangent uppdateringscykeln).

## <a name="additional-information"></a>Ytterligare information

- Mer information om loggen formatera hierarki på lagringsmappen och namngivningskonventioner, finns i den [referens till Blob granskningslogg Format](https://go.microsoft.com/fwlink/?linkid=829599).

    > [!IMPORTANT]
    > Azure SQL-databasen och Audit lagrar 4 000 tecken av data för Teckenfält i en granskningspost. När den **instruktionen** eller **data_sensitivity_information** värden som returneras från en granskningsbar åtgärd innehålla fler än 4 000 tecken kommer alla data utöver de första 4 000 tecken att  **förkortas och inte granskas**.

- Granskningsloggarna skrivs till **Tilläggsblobbar** i Azure Blob storage på din Azure-prenumeration:
  - **Premium Storage** är för närvarande **stöds inte** av Tilläggsblobbar.
  - **Storage i det virtuella nätverket** är för närvarande **stöds inte**.

- Granskning standardprincipen innehåller följande uppsättning åtgärdsgrupper som granskar alla frågor och lagrade procedurer som körs mot databasen, samt lyckade och misslyckade inloggningar och alla åtgärder:

    BATCH_COMPLETED_GROUP<br>
    SUCCESSFUL_DATABASE_AUTHENTICATION_GROUP<br>
    FAILED_DATABASE_AUTHENTICATION_GROUP

    Du kan konfigurera granskning för olika typer av åtgärder och åtgärdsgrupper med hjälp av PowerShell, enligt beskrivningen i den [hantera SQL database-granskning med Azure PowerShell](#subheading-7) avsnittet.

- När du använder AAD-autentisering misslyckades inloggningar poster kommer *inte* visas i SQL-granskningsloggen. Om du vill visa granskningsposter för Misslyckad inloggning, måste du gå till den [Azure Active Directory-portalen]( ../active-directory/reports-monitoring/reference-sign-ins-error-codes.md), som loggar information om dessa händelser.


## <a id="subheading-7"></a>Hantera SQL database-granskning med Azure PowerShell

**PowerShell-cmdletar (inklusive WHERE-satsen stöd för ytterligare filtrering)** :

- [Skapa eller uppdatera databasen granskningsprincip (Set-AzSqlDatabaseAudit)](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabaseaudit)
- [Skapa eller uppdatera princip för Server granskning (Set-AzSqlServerAudit)](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlserveraudit)
- [Hämta databas granskningsprincip (Get-AzSqlDatabaseAudit)](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabaseaudit)
- [Hämta Server granskningsprincip (Get-AzSqlServerAudit)](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlserveraudit)
- [Ta bort databasen granskningsprincip (Remove-AzSqlDatabaseAudit)](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqldatabaseaudit)
- [Ta bort princip för Server granskning (Remove-AzSqlServerAudit)](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlserveraudit)

Ett skript-exempel finns i [konfigurera granskning och hotidentifiering med hjälp av PowerShell](scripts/sql-database-auditing-and-threat-detection-powershell.md).

## <a id="subheading-9"></a>Hantera SQL database-granskning med REST API

**REST-API**:

- [Skapa eller uppdatera databasen granskningsprincip](https://docs.microsoft.com/rest/api/sql/database%20auditing%20settings/createorupdate)
- [Skapa eller uppdatera Server granskningsprincip](https://docs.microsoft.com/rest/api/sql/server%20auditing%20settings/createorupdate)
- [Hämta databas granskningsprincip](https://docs.microsoft.com/rest/api/sql/database%20auditing%20settings/get)
- [Hämta Server granskningsprincip](https://docs.microsoft.com/rest/api/sql/server%20auditing%20settings/get)

Utökade princip med där satsen stöd för ytterligare filtrering:

- [Skapa eller uppdatera databasen *utökade* granskningsprincip](https://docs.microsoft.com/rest/api/sql/database%20extended%20auditing%20settings/createorupdate)
- [Skapa eller uppdatera Server *utökade* granskningsprincip](https://docs.microsoft.com/rest/api/sql/server%20auditing%20settings/createorupdate)
- [Hämta databas *utökade* granskningsprincip](https://docs.microsoft.com/rest/api/sql/database%20extended%20auditing%20settings/get)
- [Hämta Server *utökade* granskningsprincip](https://docs.microsoft.com/rest/api/sql/server%20auditing%20settings/get)

## <a id="subheading-10"></a>Hantera SQL database-granskning med hjälp av ARM-mallar

Du kan hantera Azure SQL database-granskning med hjälp av [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) mallar, som visas i följande exempel:

- [Distribuera en Azure SQL-Server med granskning aktiverat för att skriva granskningsloggar till Azure Blob storage-konto](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-blob-storage)
- [Distribuera en Azure SQL-Server med granskning aktiverat för att skriva granskningsloggar till Log Analytics](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-oms)
- [Distribuera en Azure SQL-Server med granskning aktiverat för att skriva granskningsloggar till Event Hubs](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-eventhub)

> [!NOTE]
> Länkade exemplen finns på en extern offentlig lagringsplats och tillhandahålls ”som är” utan garanti, och stöds inte under några Microsoft-supporten program/tjänst.

<!--Anchors-->
[Azure SQL Database Auditing overview]: #subheading-1
[Set up auditing for your database]: #subheading-2
[Analyze audit logs and reports]: #subheading-3
[Practices for usage in production]: #subheading-5
[Storage Key Regeneration]: #subheading-6
[Manage SQL database auditing using Azure PowerShell]: #subheading-7
[Blob/Table differences in Server auditing policy inheritance]: (#subheading-8)
[Manage SQL database auditing using REST API]: #subheading-9
[Manage SQL database auditing using ARM templates]: #subheading-10

<!--Image references-->
[1]: ./media/sql-database-auditing-get-started/1_auditing_get_started_settings.png
[2]: ./media/sql-database-auditing-get-started/2_auditing_get_started_server_inherit.png
[3]: ./media/sql-database-auditing-get-started/3_auditing_get_started_turn_on.png
[4]: ./media/sql-database-auditing-get-started/4_auditing_get_started_storage_details.png
[5]: ./media/sql-database-auditing-get-started/5_auditing_get_started_storage_key_regeneration.png
[6]: ./media/sql-database-auditing-get-started/6_auditing_get_started_regenerate_key.png
[7]: ./media/sql-database-auditing-get-started/7_auditing_get_started_blob_view_audit_logs.png
[8]: ./media/sql-database-auditing-get-started/8_auditing_get_started_blob_audit_records.png
[9]: ./media/sql-database-auditing-get-started/9_auditing_get_started_ssms_1.png
[10]: ./media/sql-database-auditing-get-started/10_auditing_get_started_ssms_2.png
