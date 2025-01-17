---
title: 'Snabbstart: Skapa och övervaka Apache Storm-topologi i Azure HDInsight'
description: I den här snabbstarten lär du dig hur du skapar och övervakar en Apache Storm-topologi i Azure HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.date: 06/14/2019
ms.author: hrasheed
ms.custom: mvc
ms.openlocfilehash: 12001aef970d3b465a7f5c8e0c7af072b8f4ec80
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67428447"
---
# <a name="quickstart-create-and-monitor-an-apache-storm-topology-in-azure-hdinsight"></a>Snabbstart: Skapa och övervaka en Apache Storm-topologi i Azure HDInsight

Apache Storm är ett skalbart, feltolerant och distribuerat system för beräkningar i realtid för bearbetning av dataströmmar. Du kan skapa ett molnbaserat Storm-kluster som utför analyser av stordata i realtid med Storm på Azure HDInsight.

I den här snabbstarten använder du ett exempel från Apache [storm starter-](https://github.com/apache/storm/tree/v2.0.0/examples/storm-starter) projektet för att skapa och övervaka en Apache Storm-topologi i ett befintligt Apache Storm-kluster.

## <a name="prerequisites"></a>Förutsättningar

* Ett Apache Storm-kluster på HDInsight. Se [skapa Apache Hadoop-kluster med Azure portal](../hdinsight-hadoop-create-linux-clusters-portal.md) och välj **Storm** för **Klustertyp**.

* En SSH-klient. Mer information finns i [Ansluta till HDInsight (Apache Hadoop) med hjälp av SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="create-the-topology"></a>Skapa topologin

1. Anslut till ditt Storm-kluster. Redigera kommandot nedan genom att ersätta `CLUSTERNAME` med namnet på ditt Storm-kluster och ange sedan kommandot:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Den **WordCount** exempel ingår i ditt HDInsight-kluster på `/usr/hdp/current/storm-client/contrib/storm-starter/`. Topologin genererar meningar slumpmässigt och räknar hur många gånger ord används. Använd följande kommando för att starta den **wordcount** topologi i klustret:

    ```bash
    storm jar /usr/hdp/current/storm-client/contrib/storm-starter/storm-starter-topologies-*.jar org.apache.storm.starter.WordCountTopology wordcount
    ```

## <a name="monitor-the-topology"></a>Övervaka topologin

Storm innehåller ett webbgränssnitt för att arbeta med topologier som körs och ingår i ditt HDInsight-kluster.

Genomför följande för att övervaka topologin med hjälp av Storm-användargränssnittet:

1. Visa Storm-användargränssnittet genom att öppna en webbläsare på `https://CLUSTERNAME.azurehdinsight.net/stormui`. Ersätt `CLUSTERNAME` med namnet på klustret.

2. Under **Topology Summary**väljer den **wordcount** post i den **namn** kolumn. Detta visar information om topologin.

    ![Storm Dashboard med storm-starter, topologisk information om WordCount.](./media/apache-storm-quickstart/topology-summary.png)

    Den nya sidan innehåller följande information:

    |Egenskap | Beskrivning |
    |---|---|
    |Topology stats|Grundläggande information om topologins prestanda ordnad i tidsfönster. När du markerar ett specifikt tidsfönster ändras tidsfönstret för information som visas i andra avsnitt på sidan.|
    |Kanaler|Grundläggande information om kanaler, inklusive det senaste felet som returnerades av varje kanal.|
    |Bultar|Grundläggande information om bultar.|
    |Topologikonfiguration|Detaljerad information om topologikonfiguration.|
    |Aktivera|Återupptar bearbetningen av en inaktiverad topologi.|
    |inaktivera|Pausar en topologi som körs.|
    |Balansera om|Justerar topologins parallellitet. Du bör balansera om topologier som körs när du har ändrat antalet noder i klustret. Ombalansering justerar parallelliteten och kompenserar för det ökade/minskade antalet noder i klustret. Mer information finns i [förstå parallellitet i en Apache Storm-topologi](https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html).|
    |Avsluta|Avslutar en Storm-topologi efter en angiven tidsgräns.|

3. På den här sidan väljer du en post från avsnittet **Spouts** (Kanaler) eller **Bolts** (Bultar). Detta visar information om den valda komponenten.

    ![Storm-instrumentpanelen med information om valda komponenter.](./media/apache-storm-quickstart/component-summary.png)

    Den nya sidan visar följande information:

    |Egenskap | Beskrivning |
    |---|---|
    |Spout/bult stats|Grundläggande information om komponentens prestanda, ordnad i tidsfönster. När du markerar ett specifikt tidsfönster ändras tidsfönstret för information som visas i andra avsnitt på sidan.|
    |Input stats (endast bult)|Information om komponenter som producerar de data som används av bulten.|
    |Output stats|Information om hur data som sänds av bulten.|
    |Executors|Information om komponentens instanser.|
    |Fel|Fel som genereras av den här komponenten.|

4. När du visar information om en kanal eller en bult väljer du en post i kolumnen **Port** i avsnittet **Executors** (Utförare) för att visa information för en viss komponentinstans.

        2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: split default ["with"]
        2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: split default ["nature"]
        2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [snow]
        2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [snow, 747293]
        2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [white]
        2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [white, 747293]
        2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [seven]
        2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [seven, 1493957]

    I det här exemplet förekommer ordet **seven** (sju) 1 493 957 gånger. Det är antalet gånger ordet har påträffats sedan topologin startades.

## <a name="stop-the-topology"></a>Stoppa topologin

Gå tillbaka till sidan **Topology summary** (Topologiöversikt) för ordräkningstopologin och välj knappen **Kill** (Avsluta) i avsnittet **Topology actions** (Topologiåtgärder). När du uppmanas ange antal sekunder innan topologin stoppas, anger du 10. När tidsgränsen uppnåtts visas topologin inte längre när du går in på avsnittet **Storm UI** (Storm-användargränssnitt) på instrumentpanelen.

## <a name="clean-up-resources"></a>Rensa resurser

När du har slutfört snabbstarten kan du ta bort klustret. Med HDInsight lagras dina data i Azure Storage så att du på ett säkert sätt kan ta bort ett kluster när det inte används. Du debiteras också för ett HDInsight-kluster, även när det inte används. Eftersom avgifterna för klustret är flera gånger större än avgifterna för lagring är det ekonomiskt sett bra att ta bort kluster när de inte används.

Om du vill ta bort ett kluster, se [ta bort ett HDInsight-kluster med din webbläsare, PowerShell eller Azure CLI](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten använde du ett exempel från Apache [storm starter-](https://github.com/apache/storm/tree/v2.0.0/examples/storm-starter) projektet för att skapa och övervaka en Apache Storm-topologi i ett befintligt Apache Storm-kluster. Gå vidare till nästa artikel om du vill lära dig grunderna för hantering och övervakning av Apache Storm-topologier.

> [!div class="nextstepaction"]
>[Distribuera och hantera Apache Storm-topologier i Azure HDInsight](./apache-storm-deploy-monitor-topology-linux.md)