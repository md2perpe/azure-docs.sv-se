---
title: 'Snabbstart: Köra ett R-skript på ett kluster som ML-tjänster i Azure HDInsight med hjälp av R-konsolen'
description: I Snabbstart kan köra du ett R-skript i ett kluster för ML-tjänster i Azure HDInsight med hjälp av R-konsolen.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.date: 06/19/2019
ms.author: hrasheed
ms.custom: mvc
ms.openlocfilehash: 682ee4f44dcdd2619668645fa7a8aa22cb645273
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67450928"
---
# <a name="quickstart-execute-an-r-script-on-an-ml-services-cluster-in-azure-hdinsight-using-r-console"></a>Snabbstart: Köra ett R-skript på ett kluster som ML-tjänster i Azure HDInsight med hjälp av R-konsolen

ML-tjänster på Azure HDInsight kan R-skript för att köra distribuerade beräkningar med Apache Spark och Apache Hadoop MapReduce. ML-tjänster styr hur anropen genom att ställa in beräkningskontexten. Edge-nod i ett kluster är ett bra ställe att ansluta till klustret och köra R-skript. Med en kantnod har du möjlighet att köra parallelliserad distribuerade functions av RevoScaleR över kärnor i noden gränsservern. Du kan också köra dem mellan noderna i klustret med hjälp av Revoscaler's Hadoop Map Reduce eller Apache Spark compute-sammanhang.

I den här snabbstarten får du lära dig hur du kör ett R-skript med R-konsolen som visar hur du använder Spark för distribuerade R-beräkningar. Du definierar en beräkningskontext för att utföra beräkningar lokalt på en kantnod och igen distribueras mellan noderna i HDInsight-klustret.

## <a name="prerequisites"></a>Förutsättningar

* En ML-Services-kluster i HDInsight. Se [skapa Apache Hadoop-kluster med Azure portal](../hdinsight-hadoop-create-linux-clusters-portal.md) och välj **ML tjänster** för **Klustertyp**.

* En SSH-klient. Mer information finns i [Ansluta till HDInsight (Apache Hadoop) med hjälp av SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).


## <a name="connect-to-r-console"></a>Ansluta till R-konsolen

1. Ansluta till gränsnoden för ett ML tjänster HDInsight-kluster med SSH. Redigera kommandot nedan genom att ersätta `CLUSTERNAME` med namnet på klustret, och ange sedan kommandot:

    ```cmd
    ssh sshuser@CLUSTERNAME-ed-ssh.azurehdinsight.net
    ```

1. Starta R-konsolen från SSH-sessionen med följande kommando:

    ```
    R
    ```

    Du bör se utdata med versionen av ML Server samt annan information.


## <a name="use-a-compute-context"></a>Använda en beräkningskontext

1. Från frågan `>` kan du ange R-kod. Använd följande kod för att läsa in exempeldata till standardlagringen för HDInsight:

    ```R
    # Set the HDFS (WASB) location of example data
     bigDataDirRoot <- "/example/data"
    
     # create a local folder for storing data temporarily
     source <- "/tmp/AirOnTimeCSV2012"
     dir.create(source)
    
     # Download data to the tmp folder
     remoteDir <- "https://packages.revolutionanalytics.com/datasets/AirOnTimeCSV2012"
     download.file(file.path(remoteDir, "airOT201201.csv"), file.path(source, "airOT201201.csv"))
     download.file(file.path(remoteDir, "airOT201202.csv"), file.path(source, "airOT201202.csv"))
     download.file(file.path(remoteDir, "airOT201203.csv"), file.path(source, "airOT201203.csv"))
     download.file(file.path(remoteDir, "airOT201204.csv"), file.path(source, "airOT201204.csv"))
     download.file(file.path(remoteDir, "airOT201205.csv"), file.path(source, "airOT201205.csv"))
     download.file(file.path(remoteDir, "airOT201206.csv"), file.path(source, "airOT201206.csv"))
     download.file(file.path(remoteDir, "airOT201207.csv"), file.path(source, "airOT201207.csv"))
     download.file(file.path(remoteDir, "airOT201208.csv"), file.path(source, "airOT201208.csv"))
     download.file(file.path(remoteDir, "airOT201209.csv"), file.path(source, "airOT201209.csv"))
     download.file(file.path(remoteDir, "airOT201210.csv"), file.path(source, "airOT201210.csv"))
     download.file(file.path(remoteDir, "airOT201211.csv"), file.path(source, "airOT201211.csv"))
     download.file(file.path(remoteDir, "airOT201212.csv"), file.path(source, "airOT201212.csv"))
    
     # Set directory in bigDataDirRoot to load the data into
     inputDir <- file.path(bigDataDirRoot,"AirOnTimeCSV2012")
    
     # Make the directory
     rxHadoopMakeDir(inputDir)
    
     # Copy the data from source to input
     rxHadoopCopyFromLocal(source, bigDataDirRoot)
    ```

    Det här steget kan ta upp till 10 minuter för att slutföra.

1. Skapa lite datainfo och definiera två datakällor. Ange följande kod i R-konsolen:

    ```R
    # Define the HDFS (WASB) file system
     hdfsFS <- RxHdfsFileSystem()
    
     # Create info list for the airline data
     airlineColInfo <- list(
          DAY_OF_WEEK = list(type = "factor"),
          ORIGIN = list(type = "factor"),
          DEST = list(type = "factor"),
          DEP_TIME = list(type = "integer"),
          ARR_DEL15 = list(type = "logical"))
    
     # get all the column names
     varNames <- names(airlineColInfo)
    
     # Define the text data source in hdfs
     airOnTimeData <- RxTextData(inputDir, colInfo = airlineColInfo, varsToKeep = varNames, fileSystem = hdfsFS)
    
     # Define the text data source in local system
     airOnTimeDataLocal <- RxTextData(source, colInfo = airlineColInfo, varsToKeep = varNames)
    
     # formula to use
     formula = "ARR_DEL15 ~ ORIGIN + DAY_OF_WEEK + DEP_TIME + DEST"
    ```

1. Kör en logistisk regression över data med den **lokala** beräkningskontexten. Ange följande kod i R-konsolen:

    ```R
    # Set a local compute context
     rxSetComputeContext("local")
    
     # Run a logistic regression
     system.time(
        modelLocal <- rxLogit(formula, data = airOnTimeDataLocal)
     )
    
     # Display a summary
     summary(modelLocal)
    ```

    De nödvändiga beräkningarna ska slutföras inom cirka 7 minuter. Du bör se utdata som slutar med rader som liknar följande kodavsnitt:

    ```output
    Data: airOnTimeDataLocal (RxTextData Data Source)
     File name: /tmp/AirOnTimeCSV2012
     Dependent variable(s): ARR_DEL15
     Total independent variables: 634 (Including number dropped: 3)
     Number of valid observations: 6005381
     Number of missing observations: 91381
     -2*LogLikelihood: 5143814.1504 (Residual deviance on 6004750 degrees of freedom)
    
     Coefficients:
                      Estimate Std. Error z value Pr(>|z|)
      (Intercept)   -3.370e+00  1.051e+00  -3.208  0.00134 **
      ORIGIN=JFK     4.549e-01  7.915e-01   0.575  0.56548
      ORIGIN=LAX     5.265e-01  7.915e-01   0.665  0.50590
      ......
      DEST=SHD       5.975e-01  9.371e-01   0.638  0.52377
      DEST=TTN       4.563e-01  9.520e-01   0.479  0.63172
      DEST=LAR      -1.270e+00  7.575e-01  -1.676  0.09364 .
      DEST=BPT         Dropped    Dropped Dropped  Dropped
    
      ---
    
      Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1
    
      Condition number of final variance-covariance matrix: 11904202
      Number of iterations: 7
    ```

1. Kör samma logistiska regression med den **Spark** kontext. Spark-kontexten distribuerar bearbetningen mellan alla arbetsnoder i HDInsight-klustret. Ange följande kod i R-konsolen:

    ```R
    # Define the Spark compute context
     mySparkCluster <- RxSpark()
    
     # Set the compute context
     rxSetComputeContext(mySparkCluster)
    
     # Run a logistic regression
     system.time(  
        modelSpark <- rxLogit(formula, data = airOnTimeData)
     )
    
     # Display a summary
     summary(modelSpark)
    ```

    De nödvändiga beräkningarna ska slutföras inom cirka 5 minuter.

1. Om du vill avsluta R-konsolen, använder du följande kommando:

    ```R
    quit()
    ```

## <a name="clean-up-resources"></a>Rensa resurser

När du har slutfört snabbstarten kan du ta bort klustret. Med HDInsight lagras dina data i Azure Storage så att du på ett säkert sätt kan ta bort ett kluster när det inte används. Du debiteras också för ett HDInsight-kluster, även när det inte används. Eftersom avgifterna för klustret är flera gånger större än avgifterna för lagring är det ekonomiskt sett bra att ta bort kluster när de inte används.

Om du vill ta bort ett kluster, se [ta bort ett HDInsight-kluster med din webbläsare, PowerShell eller Azure CLI](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du lärt dig hur du kör ett R-skript med R-konsolen som visas med Spark för distribuerade R-beräkningar.  Gå vidare till nästa artikel om du vill lära dig de alternativ som är tillgängliga för att ange om och hur körning parallelliseras över kärnor för kantnoden eller HDInsight-kluster.

> [!div class="nextstepaction"]
>[Alternativ för beräkningskontexter för ML-tjänster på HDInsight](./r-server-compute-contexts.md)