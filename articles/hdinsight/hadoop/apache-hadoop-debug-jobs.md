---
title: 'Debug Apache Hadoop: Visa loggar och tolka felmeddelanden - Azure HDInsight'
description: Läs mer om de felmeddelanden som du kan få när du administrerar HDInsight med hjälp av PowerShell och vad du kan göra för att återställa.
ms.reviewer: jasonh
author: ashishthaps
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/14/2017
ms.author: ashishth
ms.openlocfilehash: f96171e1c75676a185edf4a1901ef65b7181135a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64721006"
---
# <a name="analyze-apache-hadoop-logs"></a>Analysera Apache Hadoop-loggar

Varje Apache Hadoop-kluster i Azure HDInsight har ett Azure storage-konto som används som standardfilsystem. Lagringskontot kallas standardkontot för lagring. Klustret använder Azure Table storage och Blob-lagringen på standardkontot för lagring för att lagra loggar.  Om du vill ta reda på standardkontot för lagring för klustret kan se [hantera Apache Hadoop-kluster i HDInsight](../hdinsight-administer-use-portal-linux.md#find-the-storage-accounts). Loggarna behåller i Storage-kontot även när klustret tas bort.

## <a name="logs-written-to-azure-tables"></a>Loggar som skrivs till Azure-tabeller

Loggarna skrivs till Azure Tables ger en inblick i vad som händer med ett HDInsight-kluster.

När du skapar ett HDInsight-kluster skapas automatiskt sex tabeller för Linux-baserade kluster i standard-tabellagring:

* hdinsightagentlog
* syslog
* daemonlog
* hadoopservicelog
* ambariserverlog
* ambariagentlog

Tabellen filnamnen är **u\<klusternamn > DDMonYYYYatHHMMSSsss\<tabellnamn >** .

Dessa tabeller innehåller följande fält:

* ClusterDnsName
* ComponentName
* EventTimestamp
* Värd
* MALoggingHash
* Message
* N
* PreciseTimeStamp
* Roll
* RowIndex
* Klientorganisation
* TIDSSTÄMPEL
* TraceLevel

### <a name="tools-for-accessing-the-logs"></a>Verktyg för att komma åt loggarna
Det finns många verktyg för att komma åt data i dessa tabeller:

* Visual Studio
* Azure Lagringsutforskaren
* Power Query för Excel

#### <a name="use-power-query-for-excel"></a>Använd Power Query för Excel
Power Query kan installeras från [Microsoft Power Query för Excel](https://www.microsoft.com/en-us/download/details.aspx?id=39379). Läs mer på hämtningssidan för systemkraven.

**Använda Power Query för att öppna och analysera loggen för tjänsten**

1. Öppna **Microsoft Excel**.
2. Från den **Power Query** -menyn klickar du på **från Azure**, och klicka sedan på **från Microsoft Azure-tabellagring**.
   
    ![HDInsight Hadoop Excel PowerQuery öppna Azure Table storage](./media/apache-hadoop-debug-jobs/hdinsight-hadoop-analyze-logs-using-excel-power-query-open.png)
3. Ange lagringskontonamn, det korta namnet eller det fullständiga Domännamnet.
4. Ange lagringskontonyckeln. Du bör se en lista över tabeller:
   
    ![HDInsight Hadoop-loggar som lagras i Azure Table storage](./media/apache-hadoop-debug-jobs/hdinsight-hadoop-analyze-logs-table-names.png)
5. Högerklicka på tabellen hadoopservicelog i den **Navigator** rutan och välj **redigera**. Du bör se fyra kolumner. Du kan också ta bort den **partitionsnyckel**, **Radnyckel**, och **tidsstämpel** kolumner genom att markera dem och sedan på **ta bort kolumner** från alternativ i menyfliksområdet.
6. Klicka på ikonen för innehåll kolumnen för välja kolumner som du vill importera till Excel-kalkylblad. För det här exemplet har jag valt TraceLevel och ComponentName: Det kan ge mig grundläggande information som komponenter hade problem.
   
    ![HDInsight Hadoop-loggar Välj kolumner](./media/apache-hadoop-debug-jobs/hdinsight-hadoop-analyze-logs-using-excel-power-query-filter.png)
7. Klicka på **OK** för import av data.
8. Välj den **TraceLevel**, roll, och **ComponentName** kolumner och klicka sedan på **Group By** kontroll i menyfliksområdet.
9. Klicka på **OK** i dialogrutan Gruppera efter
10. Klicka på ** gäller & Stäng **.

Du kan nu använda Excel för att filtrera och sortera efter behov. Du kanske vill inkludera andra kolumner (till exempel felmeddelande) för att kunna granska nedåt i problem när de inträffar, men att markera och gruppera de kolumner som beskrivs ovan ger en vettigt bild av vad som händer med Hadoop-tjänster. Samma tanken kan tillämpas på tabellerna setuplog och hadoopinstalllog.

#### <a name="use-visual-studio"></a>Använd Visual Studio
**Använda Visual Studio**

1. Öppna Visual Studio.
2. Från den **visa** -menyn klickar du på **Cloud Explorer**. Eller klicka bara på **CTRL +\, CTRL + X**.
3. Från **Cloud Explorer**väljer **resurstyper**.  Ett annat alternativ är **resursgrupper**.
4. Expandera **Lagringskonton**, standardkontot för lagring för klustret, och sedan **tabeller**.
5. Dubbelklicka på **hadoopservicelog**.
6. Lägg till ett filter. Exempel:
   
        TraceLevel eq 'ERROR'
   
    ![HDInsight Hadoop-loggar Välj kolumner](./media/apache-hadoop-debug-jobs/hdinsight-hadoop-analyze-logs-visual-studio-filter.png)
   
    Mer information om hur du skapar filter finns i [filtret konstruerar strängar för tabelldesign](../../vs-azure-tools-table-designer-construct-filter-strings.md).

## <a name="logs-written-to-azure-blob-storage"></a>Loggar som skrivs till Azure Blob Storage
Loggarna skrivs till Azure Tables ger en inblick i vad som händer med ett HDInsight-kluster. Dessa tabeller ger dock inte på aktivitetsnivå loggarna, vilket kan göra det enklare att gå djupare in problem när de uppstår. HDInsight-kluster är konfigurerade för att skriva uppgift loggar till ditt Blob Storage-konto för alla jobb som skickas via Templeton för att tillhandahålla den här nästa detaljnivå. Det innebär praktiskt taget, jobb som skickas med hjälp av Microsoft Azure PowerShell-cmdlets eller .NET jobbet skickas API: erna, inte jobb som skickas via RDP/kommandoradsbaserad-Command-Line åtkomst till klustret. 

Om du vill visa loggfilerna kan se [åtkomst Apache Hadoop YARN-programloggar på Linux-baserade HDInsight](../hdinsight-hadoop-access-yarn-app-logs-linux.md).


Läs mer om Programloggar, [förenkla användarloggar hantering och åtkomst i Apache Hadoop YARN](https://hortonworks.com/blog/simplifying-user-logs-management-and-access-in-yarn/).


## <a name="view-cluster-health-and-job-logs"></a>Visa hälso- och loggar för kluster
### <a name="access-the-ambari-ui"></a>Komma åt Ambari UI
Klicka på ett HDInsight-klusternamnet öppna fönstret kluster från Azure-portalen. Kluster-fönstret klickar du på **instrumentpanelen**.

![Starta instrumentpanelen för klustret](./media/apache-hadoop-debug-jobs/hdi-debug-launch-dashboard.png)


### <a name="access-the-yarn-ui"></a>Få åtkomst till Yarn-Användargränssnittet
Klicka på ett HDInsight-klusternamnet öppna fönstret kluster från Azure-portalen. Kluster-fönstret klickar du på **instrumentpanelen**. När du uppmanas, anger du autentiseringsuppgifter för kluster-administratör. Välj i Ambari, **YARN** från listan över tjänster till vänster. På sidan som visas, väljer **snabblänkar**, välj sedan den aktiva huvudnoden posten och Resource Manager UI.

Du kan använda YARN-Användargränssnittet för att göra följande:

* **Hämta klusterstatus**. I den vänstra rutan expanderar **kluster**, och klicka på **om**. Den här present klustret statusinformation som totala allokerade minne, kärnor som används, tillståndet för klusterresurshanteraren, klusterversion och så vidare.
  
    ![Starta instrumentpanelen för klustret](./media/apache-hadoop-debug-jobs/hdi-debug-yarn-cluster-state.png)
* **Hämta status för noden**. I den vänstra rutan expanderar **kluster**, och klicka på **noder**. Här visas alla noder i klustret, HTTP-adressen för varje nod, resurser som är allokerade till varje nod osv.
* **Övervaka jobbstatusen**. I den vänstra rutan expanderar **kluster**, och klicka sedan på **program** att lista alla jobb i klustret. Om du vill titta på jobb i ett visst tillstånd (till exempel nya, har skickats, körs, osv.), klickar du på länken under **program**. Du kan ytterligare Klicka på jobbnamnet för att få mer information om jobbet sådana inklusive utdata, loggar osv.

### <a name="access-the-hbase-ui"></a>Få åtkomst till HBase-Gränssnittet
Klicka på en HDInsight HBase-klusternamnet för att öppna fönstret kluster från Azure-portalen. Kluster-fönstret klickar du på **instrumentpanelen**. När du uppmanas, anger du autentiseringsuppgifter för kluster-administratör. Välj HBase i Ambari, från listan över tjänster. Välj **snabblänkar** överst på sidan, peka på den aktiva Zookeeper-nodlänken och klicka sedan på HBase Master UI.

## <a name="hdinsight-error-codes"></a>Felkoder för HDInsight
Felmeddelanden uppdelat i det här avsnittet anges att hjälpa användare med Hadoop i Azure HDInsight förstå möjliga feltillstånd som de kan stöta på när du administrerar tjänsten med hjälp av Azure PowerShell och att meddela dem om de steg som kan utföras återställa från felet.

Några av följande felmeddelanden kan också ses i Azure-portalen när den används för att hantera HDInsight-kluster. Men andra felmeddelanden som du kan stöta på det finns mindre detaljerad på grund av begränsningar i korrigerande åtgärder som är möjliga i den här kontexten. Andra felmeddelanden tillhandahålls i sammanhang där minskningen är uppenbart. 

### <a id="AtLeastOneSqlMetastoreMustBeProvided"></a>AtLeastOneSqlMetastoreMustBeProvided
* **Beskrivning**: Ange Azure SQL databasinformation för minst en komponent för att kunna använda anpassade inställningar för metastores för Hive och Oozie.
* **Minskning**: Användaren måste ange ett giltigt SQL Azure-metaarkiv och försöka.  

### <a id="AzureRegionNotSupported"></a>AzureRegionNotSupported
* **Beskrivning**: Det gick inte att skapa kluster i regionen *nameOfYourRegion*. Använder ett giltigt område i HDInsight och försök igen med begäran.
* **Minskning**: Kunden bör skapa regionen klustret som för närvarande stöder dem: Sydostasien, Västeuropa, Nordeuropa, USA, östra eller USA, västra.  

### <a id="ClusterContainerRecordNotFound"></a>ClusterContainerRecordNotFound
* **Beskrivning**: Servern kunde inte hitta den begärda kluster-posten.  
* **Minskning**: Försök igen.

### <a id="ClusterDnsNameInvalidReservedWord"></a>ClusterDnsNameInvalidReservedWord
* **Beskrivning**: DNS-klusternamnet *yourDnsName* är ogiltig. Kontrollera att namnet börjar och slutar med alfanumeriska och kan bara innehålla '-' specialtecken  
* **Minskning**: Se till att du har använt ett giltigt DNS-namn för ditt kluster som startar och slutar med alfanumeriska och innehåller ingen särskild andra tecken än ett streck '-' och försök sedan igen.

### <a id="ClusterNameUnavailable"></a>ClusterNameUnavailable
* **Beskrivning**: Klusternamnet *yourClusterName* är inte tillgänglig. Välj ett annat namn.  
* **Minskning**: Användaren måste ange ett klusternamn som är unikt och ännu inte finns och försök igen. Om du använder portalen, meddelar Användargränssnittet dem om ett klusternamn används redan under Skapa stegen.

### <a id="ClusterPasswordInvalid"></a>ClusterPasswordInvalid
* **Beskrivning**: Klusterlösenordet är ogiltigt. Lösenordet måste vara minst 10 tecken långt och måste innehålla minst en siffra, en versal bokstav, gemen bokstav och specialtecken utan blanksteg och får inte innehålla användarnamnet som en del av den.  
* **Minskning**: Ange ett giltigt kluster-lösenord och försök igen.

### <a id="ClusterUserNameInvalid"></a>ClusterUserNameInvalid
* **Beskrivning**: Kluster-användarnamnet är ogiltigt. Kontrollera att användarnamnet inte innehålla specialtecken eller blanksteg.  
* **Minskning**: Ange ett giltigt klusternamn och försök igen.

### <a id="ClusterUserNameInvalidReservedWord"></a>ClusterUserNameInvalidReservedWord
* **Beskrivning**: DNS-klusternamnet *yourDnsClusterName* är ogiltig. Kontrollera att namnet börjar och slutar med alfanumeriska och kan bara innehålla '-' specialtecken  
* **Minskning**: Ange ett giltigt DNS-klusternamn och försök igen.

### <a id="ContainerNameMisMatchWithDnsName"></a>ContainerNameMisMatchWithDnsName
* **Beskrivning**: Behållarens namn i URI: N *yourcontainerURI* och DNS-namnet *yourDnsName* i begäran innehållet måste vara samma.  
* **Minskning**: Se till att din behållare namn och din DNS-namn är samma och försök igen.

### <a id="DataNodeDefinitionNotFound"></a>DataNodeDefinitionNotFound
* **Beskrivning**: Ogiltig klusterkonfiguration. Det går inte att hitta någon nod datadefinitioner i nodstorlek.  
* **Minskning**: Försök igen.

### <a id="DeploymentDeletionFailure"></a>DeploymentDeletionFailure
* **Beskrivning**: Borttagningen av distributionen misslyckades för klustret  
* **Minskning**: Försöka göra om borttagningen.

### <a id="DnsMappingNotFound"></a>DnsMappingNotFound
* **Beskrivning**: Konfigurationsfel för tjänsten. Nödvändiga DNS-mappningsinformation hittades inte.  
* **Minskning**: Ta bort klustret och skapa ett nytt kluster.

### <a id="DuplicateClusterContainerRequest"></a>DuplicateClusterContainerRequest
* **Beskrivning**: Duplicera kluster behållare skapas försök. Det finns en post för *nameOfYourContainer* men Etags matchar inte.
* **Minskning**: Ange ett unikt namn för behållaren och försök skapa igen.

### <a id="DuplicateClusterInHostedService"></a>DuplicateClusterInHostedService
* **Beskrivning**: Den värdbaserade tjänsten *nameOfYourHostedService* innehåller redan ett kluster. En värdbaserad tjänst får inte innehålla flera kluster  
* **Minskning**: Vara värd för klustret i en annan värdbaserad tjänst.

### <a id="FailureToUpdateDeploymentStatus"></a>FailureToUpdateDeploymentStatus
* **Beskrivning**: Servern kunde inte uppdatera tillståndet för klusterdistributionen.  
* **Minskning**: Försök igen. Om det händer flera gånger, kontakta CSS.

### <a id="HdiRestoreClusterAltered"></a>HdiRestoreClusterAltered
* **Beskrivning**: Klustret *yourClusterName* har tagits bort som en del av underhåll. . Återskapa klustret.
* **Minskning**: Återskapa klustret.

### <a id="HeadNodeConfigNotFound"></a>HeadNodeConfigNotFound
* **Beskrivning**: Ogiltig klusterkonfiguration. Nödvändiga huvudnoden konfigurationen hittades inte i nodstorlekar.
* **Minskning**: Försök igen.

### <a id="HostedServiceCreationFailure"></a>HostedServiceCreationFailure
* **Beskrivning**: Det går inte att skapa värdbaserade tjänsten *nameOfYourHostedService*. Gör om begäran.  
* **Minskning**: Gör om begäran.

### <a id="HostedServiceHasProductionDeployment"></a>HostedServiceHasProductionDeployment
* **Beskrivning**: Den värdbaserade tjänsten *nameOfYourHostedService* har redan en Produktionsdistribution. En värdbaserad tjänst får inte innehålla flera Produktionsdistribution. Försök igen med ett annat klusternamn.
* **Minskning**: Använd ett annat klusternamn och försök begäran.

### <a id="HostedServiceNotFound"></a>HostedServiceNotFound
* **Beskrivning**: Den värdbaserade tjänsten *nameOfYourHostedService* för klustret inte kunde hittas.  
* **Minskning**: Om klustret är i feltillstånd, ta bort den och försök sedan igen.

### <a id="HostedServiceWithNoDeployment"></a>HostedServiceWithNoDeployment
* **Beskrivning**: Den värdbaserade tjänsten *nameOfYourHostedService* har ingen associerad distribution.  
* **Minskning**: Om klustret är i feltillstånd, ta bort den och försök sedan igen.

### <a id="InsufficientResourcesCores"></a>InsufficientResourcesCores
* **Beskrivning**: SubscriptionId *yourSubscriptionId* har inte kärnor kvar för att skapa kluster *yourClusterName*. Krävs: *resourcesRequired*, tillgängliga: *resourcesAvailable*.  
* **Minskning**: Frigör resurser i din prenumeration eller öka resurserna som är tillgängliga i prenumerationen och försök att skapa klustret igen.

### <a id="InsufficientResourcesHostedServices"></a>InsufficientResourcesHostedServices
* **Beskrivning**: Prenumerations-ID *yourSubscriptionId* saknar kvoten för en ny HostedService att skapa kluster *yourClusterName*.  
* **Minskning**: Frigör resurser i din prenumeration eller öka resurserna som är tillgängliga i prenumerationen och försök att skapa klustret igen.

### <a id="InternalErrorRetryRequest"></a>InternalErrorRetryRequest
* **Beskrivning**: Ett internt fel inträffade på servern. Gör om begäran.  
* **Minskning**: Gör om begäran.

### <a id="InvalidAzureStorageLocation"></a>InvalidAzureStorageLocation
* **Beskrivning**: Azure Storage-plats *dataRegionName* är inte en giltig plats. Kontrollera att regionen är korrekt och försök igen med begäran.
* **Minskning**: Välj en lagringsplats som har stöd för HDInsight, kontrollera att klustret är samplacerade och försök igen.

### <a id="InvalidNodeSizeForDataNode"></a>InvalidNodeSizeForDataNode
* **Beskrivning**: Ogiltig VM-storlek för datanoder. Endast ”stora” storleken stöds för alla datanoder.  
* **Minskning**: Ange nodstorleken stöds för datanoden och försök igen.

### <a id="InvalidNodeSizeForHeadNode"></a>InvalidNodeSizeForHeadNode
* **Beskrivning**: Ogiltig VM-storlek för huvudnoden. Endast 'Extrastora VM-storleken stöds för huvudnoden.  
* **Minskning**: Ange nodstorleken stöds för huvudnoden och försök igen

### <a id="InvalidRightsForDeploymentDeletion"></a>InvalidRightsForDeploymentDeletion
* **Beskrivning**: Prenumerations-ID *yourSubscriptionId* som används har inte tillräcklig behörighet för att köra åtgärden ta bort för klustret *yourClusterName*.  
* **Minskning**: Om klustret är i feltillstånd, släpp den och försök sedan igen.  

### <a id="InvalidStorageAccountBlobContainerName"></a>InvalidStorageAccountBlobContainerName
* **Beskrivning**: Externa blobnamn på lagringskontobehållaren *yourContainerName* är ogiltig. Kontrollera att namnet börjar med en bokstav och innehåller bara gemena bokstäver, siffror och bindestreck.  
* **Minskning**: Ange ett giltigt kontonamnet och försök igen.

### <a id="InvalidStorageAccountConfigurationSecretKey"></a>InvalidStorageAccountConfigurationSecretKey
* **Beskrivning**: Konfiguration för extern lagringskontot *yourStorageAccountName* måste ha information om hemlig nyckel anges.  
* **Minskning**: Ange en giltig hemlig nyckel för lagringskontot och försök igen.

### <a id="InvalidVersionHeaderFormat"></a>InvalidVersionHeaderFormat
* **Beskrivning**: Versionshuvudet *yourVersionHeader* är inte giltigt format för åååå-mm-dd.  
* **Minskning**: Ange ett giltigt format för versionshuvudet- och försök begäran.

### <a id="MoreThanOneHeadNode"></a>MoreThanOneHeadNode
* **Beskrivning**: Ogiltig klusterkonfiguration. Hitta mer än en huvudnod konfiguration.  
* **Minskning**: Redigera konfigurationen så att den bara en huvudnoden har angetts.

### <a id="OperationTimedOutRetryRequest"></a>OperationTimedOutRetryRequest
* **Beskrivning**: Åtgärden kunde inte slutföras inom den tilldelade tiden eller den max. antal omförsök möjligt. Gör om begäran.  
* **Minskning**: Gör om begäran.

### <a id="ParameterNullOrEmpty"></a>ParameterNullOrEmpty
* **Beskrivning**: Parametern *yourParameterName* får inte vara null eller tomt.  
* **Minskning**: Ange ett giltigt värde för parametern.

### <a id="PreClusterCreationValidationFailure"></a>PreClusterCreationValidationFailure
* **Beskrivning**: En eller flera av kluster skapa begäran om indata är inte giltig. Kontrollera att indatavärdena är korrekta och försök igen med begäran.  
* **Minskning**: Kontrollera att indatavärdena är korrekta och försök igen med begäran.

### <a id="RegionCapabilityNotAvailable"></a>RegionCapabilityNotAvailable
* **Beskrivning**: Region-funktioner som är inte tillgängliga för region *yourRegionName* och prenumerations-ID *yourSubscriptionId*.  
* **Minskning**: Ange en region som har stöd för HDInsight-kluster. Regionerna i offentligt stöds är: Sydostasien, Västeuropa, Nordeuropa, USA, östra eller USA, västra.

### <a id="StorageAccountNotColocated"></a>StorageAccountNotColocated
* **Beskrivning**: Storage-konto *yourStorageAccountName* är i regionen *currentRegionName*. Det bör vara samma som regionen som klustret *yourClusterRegionName*.  
* **Minskning**: Ange ett lagringskonto i samma region som klustret är i eller om dina data finns redan i storage-konto, skapa ett nytt kluster i samma region som det befintliga lagringskontot. Om du använder portalen meddelar Användargränssnittet dem om det här problemet i förväg.

### <a id="SubscriptionIdNotActive"></a>SubscriptionIdNotActive
* **Beskrivning**: Angivna prenumerations-ID *yourSubscriptionId* är inte aktiv.  
* **Minskning**: Återaktivera din prenumeration eller skaffa en giltig prenumeration.

### <a id="SubscriptionIdNotFound"></a>SubscriptionIdNotFound
* **Beskrivning**: Prenumerations-ID *yourSubscriptionId* kunde inte hittas.  
* **Minskning**: Kontrollera att ditt prenumerations-ID är giltig och försök igen.

### <a id="UnableToResolveDNS"></a>UnableToResolveDNS
* **Beskrivning**: Det gick inte att matcha DNS *yourDnsUrl*. Kontrollera att den fullständiga URL: en för blob-slutpunkten har angetts.  
* **Minskning**: Ange en giltig URL. Webbadressen måste vara helt giltiga, inklusive från och med *http://* och slutar på *.com*.

### <a id="UnableToVerifyLocationOfResource"></a>UnableToVerifyLocationOfResource
* **Beskrivning**: Det går inte att kontrollera platsen för resurs *yourDnsUrl*. Kontrollera att den fullständiga URL: en för blob-slutpunkten har angetts.  
* **Minskning**: Ange en giltig URL. Webbadressen måste vara helt giltiga, inklusive från och med *http://* och slutar på *.com*.

### <a id="VersionCapabilityNotAvailable"></a>VersionCapabilityNotAvailable
* **Beskrivning**: Version-funktioner som är inte tillgängliga för version *specifiedVersion* och prenumerations-ID *yourSubscriptionId*.  
* **Minskning**: Välj en version som är tillgänglig och försök igen.

### <a id="VersionNotSupported"></a>VersionNotSupported
* **Beskrivning**: Version *specifiedVersion* stöds inte.
* **Minskning**: Välj en version som stöds och försök igen.

### <a id="VersionNotSupportedInRegion"></a>VersionNotSupportedInRegion
* **Beskrivning**: Version *specifiedVersion* är inte tillgänglig i Azure-region *specifiedRegion*.  
* **Minskning**: Välj en version som stöds i den region som har angetts och försök igen.

### <a id="WasbAccountConfigNotFound"></a>WasbAccountConfigNotFound
* **Beskrivning**: Ogiltig klusterkonfiguration. Nödvändiga WASB-kontokonfigurationen hittades inte i externa konton.  
* **Minskning**: Kontrollera att kontot finns och är korrekt angavs i konfigurationen och försök igen.

## <a name="next-steps"></a>Nästa steg

* [Aktivera heap dumps för Apache Hadoop-tjänster på Linux-baserat HDInsight](../hdinsight-hadoop-collect-debug-heap-dump-linux.md)
* [Hantera HDInsight-kluster med hjälp av Apache Ambari-webbgränssnittet](../hdinsight-hadoop-manage-ambari.md)
