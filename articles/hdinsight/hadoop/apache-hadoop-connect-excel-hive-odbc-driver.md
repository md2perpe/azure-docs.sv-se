---
title: Ansluta Excel till Apache Hadoop med Hive ODBC-drivrutin - Azure HDInsight
description: Lär dig hur du konfigurerar och använder Microsoft Hive ODBC driver för Excel för att fråga efter data i HDInsight-kluster från Microsoft Excel.
keywords: hadoop excel, hive excel, hive odbc
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 05/28/2019
ms.author: hrasheed
ms.openlocfilehash: 01e7ca184f2be755f23cd402e0694ea282e3b85e
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67441385"
---
# <a name="connect-excel-to-apache-hadoop-in-azure-hdinsight-with-the-microsoft-hive-odbc-driver"></a>Ansluta Excel till Apache Hadoop i Azure HDInsight med Microsoft Hive ODBC-drivrutin

[!INCLUDE [ODBC-JDBC-selector](../../../includes/hdinsight-selector-odbc-jdbc.md)]

Microsofts Big Data-lösning integrerar Microsoft Business Intelligence (BI)-komponenter med Apache Hadoop-kluster som har distribuerats i Azure HDInsight. Ett exempel på den här integreringen är möjligheten att ansluta Excel till Hive-datalagret i ett Hadoop-kluster i HDInsight med Microsoft Hive ODBC Open Database Connectivity ()-drivrutinen.

Det är också möjligt att ansluta de data som hör till ett HDInsight-kluster och andra datakällor, inklusive andra Hadoop-kluster med (icke-HDInsight), från Excel med hjälp av Microsoft Power Query-tillägget för Excel. Information om att installera och använda Power Query finns i [Anslut Excel till HDInsight med Power Query](../hdinsight-connect-excel-power-query.md).

## <a name="prerequisites"></a>Förutsättningar

Innan du påbörjar den här artikeln måste du ha följande objekt:

* Ett HDInsight Hadoop-kluster. Om du vill skapa en [Kom igång med Azure HDInsight](apache-hadoop-linux-tutorial-get-started.md).
* En arbetsstation med Office Professional Plus 2010 eller senare, eller Excel 2010 eller senare.

## <a name="install-microsoft-hive-odbc-driver"></a>Installera Microsoft Hive ODBC-drivrutin
Ladda ned och installera den [Microsoft Hive ODBC-drivrutin](https://go.microsoft.com/fwlink/?LinkID=286698) version som matchar versionen av programmet som du kommer att använda ODBC-drivrutinen.  I den här artikeln används drivrutinen för Office Excel.

## <a name="create-apache-hive-odbc-data-source"></a>Skapa Apache Hive ODBC-datakälla
Följande steg visar hur du skapar en Hive ODBC-datakälla.

1. Från Windows, navigerar du till Start > Windows Administrationsverktyg > ODBC-datakällor (32-bit)/(64-bit).  Då öppnas det **ODBC Data Source Administrator** fönster.

    ![Datakälla för OBDC](./media/apache-hadoop-connect-excel-hive-odbc-driver/HDI.SimbaHiveOdbc.DataSourceAdmin1.png "konfigurera en DNS med hjälp av ODBC-datakälla")

1. Från den **användar-DSN** fliken **Lägg till** att öppna den **Skapa ny datakälla** fönster.

1. Välj **Microsoft Hive ODBC-drivrutin**, och välj sedan **Slutför** att öppna den **Microsoft Hive ODBC-drivrutinen DSN för** fönster.

1. Ange eller välj följande värden:

   | Egenskap | Beskrivning |
   | --- | --- |
   |  Namn på datakälla |Namnge din datakälla |
   |  Värdar |Ange `HDInsightClusterName.azurehdinsight.net`. Till exempel, `myHDICluster.azurehdinsight.net` |
   |  Port |Använd **443**. (Den här porten har ändrats från 563 till 443.) |
   |  Databas |Använd **standard**. |
   |  Mekanism |Välj **Windows Azure HDInsight-tjänsten** |
   |  Användarnamn |Ange HDInsight-kluster HTTP användarens användarnamn. Standardanvändarnamnet är **admin**. |
   |  Lösenord |Ange användarlösenord för HDInsight-kluster. Markera kryssrutan **spara lösenord (krypterade)** .|

1. Valfritt: Välj **avancerade alternativ...**  

   | Parameter | Beskrivning |
   | --- | --- |
   |  Använda Internfråga |När det är valt försöker ODBC-drivrutinen inte att konvertera TSQL till HiveQL. Du bör använda den endast om du är 100% säker på att du skickar ren HiveQL-instruktioner. När du ansluter till SQL Server eller Azure SQL Database, bör du lämna det avmarkerat. |
   |  Rader hämtade per block |När du hämtar ett stort antal poster, kan justera den här parametern krävas att säkerställa optimala prestanda. |
   |  Standardlängden för sträng-kolumnen, binär Kolumnlängd, Decimal kolumnskalan |Datatypen längder och Precision-datorerna kan påverka hur data returneras. De kan orsaka felaktig information som ska returneras på grund av förlust av precision och/eller trunkering. |

    ![Avancerade alternativ](./media/apache-hadoop-connect-excel-hive-odbc-driver/HDI.HiveOdbc.DataSource.AdvancedOptions1.png "avancerade DSN konfigurationsalternativ")

1. Välj **testa** att testa datakällan. När datakällan är korrekt konfigurerad, visar testresultatet **lyckades!** .  

1. Välj **OK** att stänga testfönstret.  

1. Välj **OK** att Stäng den **Microsoft Hive ODBC-drivrutinen DSN för** fönster.  

1. Välj **OK** att Stäng den **ODBC Data Source Administrator** fönster.  

## <a name="import-data-into-excel-from-hdinsight"></a>Importera data till Excel från HDInsight

Följande steg beskriver hur du importerar data från en Hive-tabell till en Excel-arbetsbok med hjälp av ODBC-datakälla som du skapade i föregående avsnitt.

1. Öppna en ny eller befintlig arbetsbok i Excel.

2. Från den **Data** fliken, navigera till **hämta Data** > **från andra källor** > **från ODBC** att starta den **Från ODBC** fönster.

    ![Öppna dataanslutningsguiden](./media/apache-hadoop-connect-excel-hive-odbc-driver/HDI.SimbaHiveOdbc.Excel.DataConnection1.png "öppna dataanslutningsguiden")

3. Från listrutan, Välj namnet på datakällan som du skapade i det sista avsnittet och välj sedan **OK**.

4. För första användning, en **ODBC-drivrutinen** öppnas dialogrutan. Välj **Windows** menyn till vänster. Välj sedan **Connect** att öppna den **Navigator** fönster.

5. Från **Navigator**, gå till **HIVE** > **standard** > **hivesampletable**, och välj sedan  **Läs in**. Det tar en liten stund innan data har importerats till Excel.

    ![HDInsight Hive ODBC navigator](./media/apache-hadoop-connect-excel-hive-odbc-driver/hdinsight.hive.odbc.navigator.png "öppna dataanslutningsguiden")

## <a name="next-steps"></a>Nästa steg

I den här artikeln beskrivs hur du använder Microsoft Hive ODBC-drivrutinen för att hämta data från HDInsight Service till Excel. På samma sätt kan du hämta data från HDInsight Service till SQL Database. Det är också möjligt att överföra data till ett HDInsight Service. Du kan läsa mer här:

* [Visualisera Apache Hive-data med Microsoft Power BI i Azure HDInsight](apache-hadoop-connect-hive-power-bi.md).
* [Visualisera Interactive Query Hive-data med Power BI i Azure HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).
* [Använda Apache Zeppelin för att köra Apache Hive-frågor i Azure HDInsight](../interactive-query/hdinsight-connect-hive-zeppelin.md).
* [Ansluta Excel till Apache Hadoop med Power Query](apache-hadoop-connect-excel-power-query.md).
* [Ansluta till Azure HDInsight och kör Apache Hive-frågor med Data Lake Tools för Visual Studio](apache-hadoop-visual-studio-tools-get-started.md).
* [Använda Azure HDInsight-verktyg för Visual Studio Code](../hdinsight-for-vscode.md).
* [Ladda upp data till HDInsight](./../hdinsight-upload-data.md).