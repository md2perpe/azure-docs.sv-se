---
title: 'Snabbstart: Kör Apache Hive-frågor i Azure HDInsight - Apache Zeppelin'
description: I den här snabbstarten lär du dig för att köra Apache Hive-frågor med Apache Zeppelin.
keywords: hdinsight, hadoop, hive, LLAP interaktiv fråga
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: quickstart
ms.date: 05/06/2019
ms.author: hrasheed
ms.openlocfilehash: 1642c64b0b14c2e290aad689399b59d896660a28
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67056692"
---
# <a name="quickstart-execute-apache-hive-queries-in-azure-hdinsight-with-apache-zeppelin"></a>Snabbstart: Kör Apache Hive-frågor i Azure HDInsight med Apache Zeppelin

I den här snabbstarten får du lära dig hur du använder Apache Zeppelin för att köra [Apache Hive](https://hive.apache.org/) frågor i Azure HDInsight. Interaktiv HDInsight-fråga innehåller [Apache Zeppelin](https://zeppelin.apache.org/) anteckningsböcker som du kan använda för att köra interaktiva Hive-frågor.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

En interaktiv fråga i HDInsight-kluster. Se [Skapa kluster](../hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster) att skapa ett HDInsight-kluster.  Se till att välja den **interaktiv fråga** typ av kluster.

## <a name="create-an-apache-zeppelin-note"></a>Skapa ett Apache Zeppelin-anteckning

1. Ersätt `CLUSTERNAME` med namnet på klustret i följande URL `https://CLUSTERNAME.azurehdinsight.net/zeppelin`. Ange URL: en i en webbläsare.

2. Ange ditt kluster användarnamn och lösenord. Zeppelin-sidan kan du skapa en ny anteckning eller öppna befintliga anteckningar. **HiveSample** innehåller vissa Hive-exempelfrågor.  

    ![Interaktiv HDInsight-fråga zeppelin](./media/hdinsight-connect-hive-zeppelin/hdinsight-hive-zeppelin.png)

3. Välj **Skapa ny anteckning**.

4. Från den **Skapa ny anteckning** dialogrutan, Skriv in eller Välj följande värden:

    - Obs namn: Ange ett namn för kommentaren.
    - Standard-tolk: Välj **jdbc** från den nedrullningsbara listan.

5. Välj **skapa anteckning**.

6. Ange följande Hive-fråga i kodavsnittet och tryck sedan på **SKIFT + RETUR**:

    ```hive
    %jdbc(hive)
    show tables
    ```

    ![Interaktiv HDInsight-fråga zeppelin Kör fråga](./media/hdinsight-connect-hive-zeppelin/hdinsight-hive-zeppelin-query.png)

    Den **%jdbc(hive)** instruktion i den första raden anger anteckningsboken för att använda Hive JDBC-tolk.

    Frågan bör returnera en Hive-tabell som kallas **hivesampletable**.

    Följande är två ytterligare Hive-frågor som du kan köra mot **hivesampletable**:

    ```hive
    %jdbc(hive)
    select * from hivesampletable limit 10

    %jdbc(hive)
    select ${group_name}, count(*) as total_count
    from hivesampletable
    group by ${group_name=market,market|deviceplatform|devicemake}
    limit ${total_count=10}
    ```

    Jämföra med traditionella Hive, kommer resultatet av frågan tillbaka måste snabbare.

## <a name="clean-up-resources"></a>Rensa resurser

När du har slutfört snabbstarten kan du ta bort klustret. Med HDInsight lagras dina data i Azure Storage så att du på ett säkert sätt kan ta bort ett kluster när det inte används. Du debiteras också för ett HDInsight-kluster, även när det inte används. Eftersom avgifterna för klustret är flera gånger större än avgifterna för lagring är det ekonomiskt sett bra att ta bort kluster när de inte används.

Om du vill ta bort ett kluster, se [ta bort ett HDInsight-kluster med din webbläsare, PowerShell eller Azure CLI](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du lärt dig hur du använder Apache Zeppelin för att köra Apache Hive-frågor i Azure HDInsight. Mer information om Hive-frågor, visas nästa artikel hur du kör frågor med Visual Studio.

> [!div class="nextstepaction"]
> [Ansluta till Azure HDInsight och kör Apache Hive-frågor med Data Lake Tools för Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)
