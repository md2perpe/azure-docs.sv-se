---
title: Skapa rekommendationer med hjälp av Apache Mahout och HDInsight (SSH) - Azure
description: Lär dig hur du använder Apache Mahout-machine learning-biblioteket för att skapa filmrekommendationer med HDInsight (Hadoop).
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/24/2019
ms.openlocfilehash: d566b57ae12520b9eee26334a67d2e10c05f8040
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64709078"
---
# <a name="generate-movie-recommendations-by-using-apache-mahout-with-linux-based-apache-hadoop-in-hdinsight-ssh"></a>Skapa filmrekommendationer med hjälp av Apache Mahout med Linux-baserade Apache Hadoop i HDInsight (SSH)

[!INCLUDE [mahout-selector](../../../includes/hdinsight-selector-mahout.md)]

Lär dig hur du använder den [Apache Mahout](https://mahout.apache.org) machine learning-biblioteket med Azure HDInsight för att generera filmrekommendationer.

Mahout är ett [maskininlärning](https://en.wikipedia.org/wiki/Machine_learning) -biblioteket för Apache Hadoop. Mahout innehåller algoritmer för databehandling, t.ex filtrering, klassificering och klustring. I den här artikeln använder du en rekommendationsmotor för att generera filmrekommendationer som baseras på dina vänner har sett filmer.

## <a name="prerequisites"></a>Nödvändiga komponenter

* Ett Apache Hadoop-kluster på HDInsight. Se [Kom igång med HDInsight på Linux](./apache-hadoop-linux-tutorial-get-started.md).

* En SSH-klient. Mer information finns i [Ansluta till HDInsight (Apache Hadoop) med hjälp av SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="apache-mahout-versioning"></a>Apache Mahout versionshantering

Mer information om versionen av Mahout på HDInsight finns i [HDInsight-versioner och Apache Hadoop-komponenter](../hdinsight-component-versioning.md).

## <a name="recommendations"></a>Förstå rekommendationer

En av de funktioner som tillhandahålls av Mahout är en rekommendationsmotor. Den här motorn accepterar data i formatet `userID`, `itemId`, och `prefValue` (preferens för objektet). Mahout utföra delad förekomsten analysen för att avgöra: *användare som har en inställning för ett objekt har också en inställning för dessa andra objekt*. Mahout avgör sedan användare med liknande objekt inställningar som kan användas för att göra rekommendationer.

Följande arbetsflöde är ett förenklat exempel som använder filmdata:

* **delad förekomsten**: Joe Alice och Bob alla gillade *Star Wars*, *The Empire under katastrofsituationer tillbaka*, och *avkastningen på Jedi*. Mahout anger att användare som gillar något av dessa filmer också som de andra två.

* **delad förekomsten**: Bob och Alice också tyckte *The Phantom hot*, *Attack av klonerna*, och *Revenge av Sith*. Mahout anger att användare som gillade föregående tre filmer också som dessa tre filmer.

* **Likhet rekommendation**: Eftersom Joe gillade tre första filmer, Mahout tittar på filmer den andra med liknande inställningar som gillade, men Josef har inte sett (gillade/klassificerad). I det här fallet Mahout rekommenderar *The Phantom hot*, *Attack av klonerna*, och *Revenge av Sith*.

### <a name="understanding-the-data"></a>Förstå data

Jag behöver [GroupLens Research](https://grouplens.org/datasets/movielens/) tillhandahåller data för klassificering för filmer i ett format som är kompatibel med Mahout. Informationen är tillgänglig på ditt kluster standardlagring på `/HdiSamples/HdiSamples/MahoutMovieData`.

Det finns två filer, `moviedb.txt` och `user-ratings.txt`. Den `user-ratings.txt` filen ska användas vid analys. Den `moviedb.txt` används för att ange användarvänliga textinformation när du visar resultaten.

Data i användaren ratings.txt har en struktur för `userID`, `movieID`, `userRating`, och `timestamp`, vilket anger hur varje användare väl en film. Här är ett exempel på data:

    196    242    3    881250949
    186    302    3    891717742
    22     377    1    878887116
    244    51     2    880606923
    166    346    1    886397596

## <a name="run-the-analysis"></a>Analys körs

Använd kommandot rekommendation jobbet ska köras från en SSH-anslutning till klustret:

```bash
mahout recommenditembased -s SIMILARITY_COOCCURRENCE -i /HdiSamples/HdiSamples/MahoutMovieData/user-ratings.txt -o /example/data/mahoutout --tempDir /temp/mahouttemp
```

> [!NOTE]  
> Jobbet kan ta flera minuter att slutföra, och kan köra flera MapReduce-jobb.

## <a name="view-the-output"></a>Visa utdata

1. När jobbet har slutförts, använder du följande kommando för att visa genererade utdata:

    ```bash
    hdfs dfs -text /example/data/mahoutout/part-r-00000
    ```

    Utdata visas på följande sätt:

        1    [234:5.0,347:5.0,237:5.0,47:5.0,282:5.0,275:5.0,88:5.0,515:5.0,514:5.0,121:5.0]
        2    [282:5.0,210:5.0,237:5.0,234:5.0,347:5.0,121:5.0,258:5.0,515:5.0,462:5.0,79:5.0]
        3    [284:5.0,285:4.828125,508:4.7543354,845:4.75,319:4.705128,124:4.7045455,150:4.6938777,311:4.6769233,248:4.65625,272:4.649266]
        4    [690:5.0,12:5.0,234:5.0,275:5.0,121:5.0,255:5.0,237:5.0,895:5.0,282:5.0,117:5.0]

    Den första kolumnen är den `userID`. De värden som finns i ' [' och ']' är `movieId`:`recommendationScore`.

2. Du kan använda utdata, tillsammans med moviedb.txt, för att ge mer information om rekommendationerna. Först kopiera filerna lokalt med hjälp av följande kommandon:

    ```bash
    hdfs dfs -get /example/data/mahoutout/part-r-00000 recommendations.txt
    hdfs dfs -get /HdiSamples/HdiSamples/MahoutMovieData/* .
    ```

    Det här kommandot kopierar utdata till en fil med namnet **recommendations.txt** i den aktuella katalogen, tillsammans med film datafiler.

3. Använd följande kommando för att skapa ett pythonskript som hämtar film namn för data i rekommendationer-utdata:

    ```bash
    nano show_recommendations.py
    ```

    När redigeringsprogrammet öppnas kan du använda följande text som innehållet i filen:

   ```python
   #!/usr/bin/env python

   import sys

   if len(sys.argv) != 5:
        print "Arguments: userId userDataFilename movieFilename recommendationFilename"
        sys.exit(1)

   userId, userDataFilename, movieFilename, recommendationFilename = sys.argv[1:]

   print "Reading Movies Descriptions"
   movieFile = open(movieFilename)
   movieById = {}
   for line in movieFile:
       tokens = line.split("|")
       movieById[tokens[0]] = tokens[1:]
   movieFile.close()

   print "Reading Rated Movies"
   userDataFile = open(userDataFilename)
   ratedMovieIds = []
   for line in userDataFile:
       tokens = line.split("\t")
       if tokens[0] == userId:
           ratedMovieIds.append((tokens[1],tokens[2]))
   userDataFile.close()

   print "Reading Recommendations"
   recommendationFile = open(recommendationFilename)
   recommendations = []
   for line in recommendationFile:
       tokens = line.split("\t")
       if tokens[0] == userId:
           movieIdAndScores = tokens[1].strip("[]\n").split(",")
           recommendations = [ movieIdAndScore.split(":") for movieIdAndScore in movieIdAndScores ]
           break
   recommendationFile.close()

   print "Rated Movies"
   print "------------------------"
   for movieId, rating in ratedMovieIds:
       print "%s, rating=%s" % (movieById[movieId][0], rating)
   print "------------------------"

   print "Recommended Movies"
   print "------------------------"
   for movieId, score in recommendations:
       print "%s, score=%s" % (movieById[movieId][0], score)
   print "------------------------"
   ```

    Tryck på **Ctrl + X**, **Y**, och slutligen **RETUR** att spara data.

4. Köra Python-skriptet. Följande kommando förutsätter att du är i katalogen där alla filer som hämtades:

    ```bash
    python show_recommendations.py 4 user-ratings.txt moviedb.txt recommendations.txt
    ```

    Det här kommandot tittar på de rekommendationer som genererats för användaren ID 4.

   * Den **användaren ratings.txt** används för att hämta filmer som har klassificerats.

   * Den **moviedb.txt** används för att hämta namnen på filmerna.

   * Den **recommendations.txt** används för att hämta filmrekommendationer för den här användaren.

     Utdata från det här kommandot liknar följande text:

       Sju år i Tibet (1997), bedöma = 5.0 Indiana Jones och den senaste Crusade (1989), bedöma = 5.0 Jaws (1975), poäng = 5.0 förnuft och känseln (1995) poäng = 5.0 oberoende dag (ID4) (1996), poäng = 5.0 mitt bästa vän Wedding (1997) poäng = 5.0 Jerry Maguire (1996) poäng = 5.0 Scream 2 (1997), poäng = 5.0 tid att Kill, (1996), poäng = 5.0

## <a name="delete-temporary-data"></a>Ta bort tillfälliga data

Mahout jobb ta inte bort tillfälliga data som skapas vid bearbetning av jobbet. Den `--tempDir` parameter har angetts i exempel-jobbet för att isolera de temporära filerna i en specifik sökväg för enkelt borttagning. Ta bort tillfälliga filer med följande kommando:

```bash
hdfs dfs -rm -f -r /temp/mahouttemp
```

> [!WARNING]  
> Om du vill köra kommandot igen måste du också ta bort katalogen. Använd följande för att ta bort den här katalogen:
>
> `hdfs dfs -rm -f -r /example/data/mahoutout`


## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du använder Mahout kan upptäcka andra sätt att arbeta med data i HDInsight:

* [Apache Hive med HDInsight](hdinsight-use-hive.md)
* [Apache Pig med HDInsight](hdinsight-use-pig.md)
* [MapReduce med HDInsight](hdinsight-use-mapreduce.md)