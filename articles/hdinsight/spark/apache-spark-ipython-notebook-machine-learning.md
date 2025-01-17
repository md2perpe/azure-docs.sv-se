---
title: 'Självstudier: Skapa ett Spark-maskininlärningsprogram i Azure HDInsight'
description: Självstudie – stegvisa instruktioner om hur du skapar Apache Spark machine learning-programmet i HDInsight Spark-kluster med Jupyter-anteckningsboken.
ms.service: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.custom: hdinsightactive,mvc
ms.topic: tutorial
ms.date: 06/26/2019
ms.author: hrasheed
ms.openlocfilehash: e1a52072ab3309454742d2d3e8582b58a33666e3
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67448712"
---
# <a name="tutorial-build-an-apache-spark-machine-learning-application-in-azure-hdinsight"></a>Självstudier: Skapa ett Apache Spark-machine learning-program i Azure HDInsight 

I den här självstudien lär du dig att använda [Jupyter Notebook](https://jupyter.org/) för att skapa ett [Apache Spark](https://spark.apache.org/)-maskininlärningsprogram för Azure HDInsight.

[MLib](https://spark.apache.org/docs/latest/ml-guide.html) är Sparks skalbara Machine Learning-bibliotek som består av vanliga algoritmer och verktyg, inklusive klassificering, regression, klustring, samarbetsfilter, dimensionsminskning samt underliggande optimeringsprimitiver.

I den här guiden får du lära dig att:
> [!div class="checklist"]
> * Utveckla ett Apache Spark-maskininlärningsprogram

## <a name="prerequisites"></a>Förutsättningar

* Ett Apache Spark-kluster i HDInsight. Se [skapar ett Apache Spark-kluster](./apache-spark-jupyter-spark-sql-use-portal.md).

* Kunskaper om Jupyter Notebooks med Spark på HDInsight. Mer information finns i [läsa in data och köra frågor med Apache Spark i HDInsight](./apache-spark-load-data-run-query.md).

## <a name="understand-the-data-set"></a>Förstå datauppsättningen

Programmet använder exemplet **HVAC.csv** data som är tillgängliga på alla kluster som standard. Filen finns på `\HdiSamples\HdiSamples\SensorSampleData\hvac`. Data visar måltemperaturen och den faktiska temperaturen för några byggnader som har installerade HVAC-system. Kolumnen **System** representerar system-ID:t, och kolumnen **SystemAge** representerar antalet år som HVAC-systemet har funnits i byggnaden. Med dessa data kan du förutsäga om en byggnad kommer att bli varmare eller kallare baserat på måltemperaturen baserat på ett system-ID och systemets ålder.

![Ögonblicksbild av data som används för Spark machine learning-exempel](./media/apache-spark-ipython-notebook-machine-learning/spark-machine-learning-understand-data.png "Ögonblicksbild av data som används för Spark machine learning-exempel")

## <a name="develop-a-spark-machine-learning-application-using-spark-mllib"></a>Utveckla ett Spark-maskininlärningsprogram med Spark MLlib

I det här programmet använder du en Spark [ML-pipeline](https://spark.apache.org/docs/2.2.0/ml-pipeline.html) för att utföra en dokumentklassificering. ML-pipelines tillhandahåller en enhetlig uppsättning högnivå-API:er som har skapats på DataFrames som hjälper användare att skapa och justera praktiska machine learning-pipelines. I pipelinen delar du upp dokumentet i ord, konverterar orden till en numerisk funktionsvektor och slutligen skapa en förutsägelsemodell med funktionsvektorer och etiketter. Skapa programmet genom att utföra stegen nedan.

1. Skapa en Jupyter-anteckningsbok med PySpark-kerneln. Instruktioner finns i [Skapa en Jupyter-anteckningsbok](./apache-spark-jupyter-spark-sql.md#create-a-jupyter-notebook).

1. Importera de typer som krävs för det här scenariot. Klistra in följande kodfragment i en tom cell och tryck sedan på **SKIFT+RETUR**. 

    ```PySpark
    from pyspark.ml import Pipeline
    from pyspark.ml.classification import LogisticRegression
    from pyspark.ml.feature import HashingTF, Tokenizer
    from pyspark.sql import Row

    import os
    import sys
    from pyspark.sql.types import *

    from pyspark.mllib.classification import LogisticRegressionWithSGD
    from pyspark.mllib.regression import LabeledPoint
    from numpy import array
    ```

3. Läs in data (hvac.csv), parsa dem och använd dem för att träna modellen. 

    ```PySpark
    # Define a type called LabelDocument
    LabeledDocument = Row("BuildingID", "SystemInfo", "label")

    # Define a function that parses the raw CSV file and returns an object of type LabeledDocument
    def parseDocument(line):
        values = [str(x) for x in line.split(',')]
        if (values[3] > values[2]):
            hot = 1.0
        else:
            hot = 0.0        

        textValue = str(values[4]) + " " + str(values[5])

        return LabeledDocument((values[6]), textValue, hot)

    # Load the raw HVAC.csv file, parse it using the function
    data = sc.textFile("/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

    documents = data.filter(lambda s: "Date" not in s).map(parseDocument)
    training = documents.toDF()
    ```

    I kodfragmentet definierar du en funktion som jämför den faktiska temperaturen med måltemperaturen. Om den faktiska temperaturen är högre är byggnaden varm, vilket markeras med värdet **1.0**. Annars är byggnaden kall, vilket visas av värdet **0.0**.

4. Konfigurera Spark machine learning-pipelinen som består av tre steg: tokenizer, hashingTF och lr.

    ```PySpark
    tokenizer = Tokenizer(inputCol="SystemInfo", outputCol="words")
    hashingTF = HashingTF(inputCol=tokenizer.getOutputCol(), outputCol="features")
    lr = LogisticRegression(maxIter=10, regParam=0.01)
    pipeline = Pipeline(stages=[tokenizer, hashingTF, lr])
    ```

    Mer information om pipeline och hur det fungerar finns i avsnittet om [Apache Spark-maskininlärnings-pipeline](https://spark.apache.org/docs/latest/ml-pipeline.html).

5. Anpassa pipelinen till utbildningsdokumentet.

    ```PySpark
    model = pipeline.fit(training)
    ```

6. Verifiera utbildningsdokumentet för att kontrollera din utveckling med programmet.

    ```PySpark
    training.show()
    ```
   
    Utdatan liknar följande:

    ```output
    +----------+----------+-----+
    |BuildingID|SystemInfo|label|
    +----------+----------+-----+
    |         4|     13 20|  0.0|
    |        17|      3 20|  0.0|
    |        18|     17 20|  1.0|
    |        15|      2 23|  0.0|
    |         3|      16 9|  1.0|
    |         4|     13 28|  0.0|
    |         2|     12 24|  0.0|
    |        16|     20 26|  1.0|
    |         9|      16 9|  1.0|
    |        12|       6 5|  0.0|
    |        15|     10 17|  1.0|
    |         7|      2 11|  0.0|
    |        15|      14 2|  1.0|
    |         6|       3 2|  0.0|
    |        20|     19 22|  0.0|
    |         8|     19 11|  0.0|
    |         6|      15 7|  0.0|
    |        13|      12 5|  0.0|
    |         4|      8 22|  0.0|
    |         7|      17 5|  0.0|
    +----------+----------+-----+
    ```

    Jämföra utdata med CSV-råfilen. Den första raden i CSV-filen har dessa data till exempel:

    ![Ögonblicksbild av utdata för Spark machine learning-exempel](./media/apache-spark-ipython-notebook-machine-learning/spark-machine-learning-output-data.png "Ögonblicksbild av utdata för Spark machine learning-exempel")

    Lägg märke till att den faktiska temperaturen är lägre än måltemperaturen, vilket indikerar att byggnaden är kall. I utbildningsutdata är därför värdet för **etikett** i den första raden **0.0**, vilket betyder att byggnaden inte är varm.

7. Förbered en datauppsättning att köra träningsmodellen mot. För att göra det skickar du ett system-ID och en systemålder (betecknas som **SystemInfo** i utbildningsutdata). Modellen förutsäger om byggnaden med det system-ID:t och den systemåldern kommer att bli varmare (betecknas av 1.0) eller kallare (betecknas med 0.0).

    ```PySpark
    # SystemInfo here is a combination of system ID followed by system age
    Document = Row("id", "SystemInfo")
    test = sc.parallelize([(1L, "20 25"),
                    (2L, "4 15"),
                    (3L, "16 9"),
                    (4L, "9 22"),
                    (5L, "17 10"),
                    (6L, "7 22")]) \
        .map(lambda x: Document(*x)).toDF()
    ```

8. Gör slutligen förutsägelser på testdata.

    ```PySpark
    # Make predictions on test documents and print columns of interest
    prediction = model.transform(test)
    selected = prediction.select("SystemInfo", "prediction", "probability")
    for row in selected.collect():
        print row
    ```

    Utdatan liknar följande:

    ```output  
    Row(SystemInfo=u'20 25', prediction=1.0, probability=DenseVector([0.4999, 0.5001]))
    Row(SystemInfo=u'4 15', prediction=0.0, probability=DenseVector([0.5016, 0.4984]))
    Row(SystemInfo=u'16 9', prediction=1.0, probability=DenseVector([0.4785, 0.5215]))
    Row(SystemInfo=u'9 22', prediction=1.0, probability=DenseVector([0.4549, 0.5451]))
    Row(SystemInfo=u'17 10', prediction=1.0, probability=DenseVector([0.4925, 0.5075]))
    Row(SystemInfo=u'7 22', prediction=0.0, probability=DenseVector([0.5015, 0.4985]))
    ```

   Från den första raden i förutsägelsen kan du se att för ett HVAC-system med ID 20 och en systemålder på 25 år är byggnaden varm (**förutsägelse=1.0**). Det första värdet för DenseVector (0,49999) motsvarar förutsägelsen 0.0 och det andra värdet (0,5001) motsvarar förutsägelsen 1.0. I utdata, trots att det andra värdet bara är marginellt högre, visar modellen **förutsägelse=1.0**.

10. Stäng anteckningsboken för att frigöra resurser. Du gör det genom att välja **Stäng och stoppa** i anteckningsbokens **Fil**-meny. Åtgärden stänger anteckningsboken.

## <a name="use-anaconda-scikit-learn-library-for-spark-machine-learning"></a>Använda Anaconda scikit-bibliotek för Spark machine learning
Apache Spark-kluster i HDInsight innehåller Anaconda-bibliotek. Det innehåller också **scikit-learn**-bibliotek för machine learning. Biblioteket innehåller också olika datauppsättningar som du kan använda för att skapa exempelprogram direkt från en Jupyter Notebook. För exempel på hur du använder scikit-learn-biblioteket, se [https://scikit-learn.org/stable/auto_examples/index.html](https://scikit-learn.org/stable/auto_examples/index.html).

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte planerar att fortsätta använda det här programmet, tar du bort klustret som du skapade med följande steg:

1. Logga in på [Azure Portal](https://portal.azure.com/).

1. I rutan **Sök** längst upp skriver du **HDInsight**.

1. Välj **HDInsight-kluster** under **Tjänster**.

1. I listan över HDInsight-kluster som visas, väljer du den **...**  bredvid det kluster som du skapade för den här kursen.

1. Välj **Ta bort**. Välj **Ja**.

![Ta bort ett HDInsight-kluster](./media/apache-spark-ipython-notebook-machine-learning/hdinsight-azure-portal-delete-cluster.png "Ta bort HDInsight-kluster")

## <a name="next-steps"></a>Nästa steg

I den här självstudien lärde du dig att använda Jupyter-anteckningsboken för att skapa ett Apache Spark machine learning-program för Azure HDInsight. Gå vidare till nästa självstudie om du vill lära dig hur du använder IntelliJ IDEA för Spark-jobb.

> [!div class="nextstepaction"]
> [Skapa ett Scala Maven-program med IntelliJ](./apache-spark-create-standalone-application.md)