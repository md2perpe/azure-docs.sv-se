---
title: 'Självstudie – Azure Toolkit för IntelliJ: Skapa Spark-program för ett HDInsight-kluster'
description: Självstudie – Använd Azure Toolkit för IntelliJ för att utveckla Spark-program som skrivits i Scala och skicka dem till ett HDInsight Spark-kluster.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 06/26/2019
ms.author: hrasheed
ms.openlocfilehash: 0a434246791e73e24af1ffe7abd722f5265ca5b6
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67462412"
---
# <a name="tutorial-use-azure-toolkit-for-intellij-to-create-apache-spark-applications-for-an-hdinsight-cluster"></a>Självstudier: Använd Azure Toolkit för IntelliJ för att skapa Apache Spark-program för ett HDInsight-kluster

Den här kursen visar hur du använder Azure Toolkit för IntelliJ plugin-programmet för att utveckla Apache Spark-program som skrivits i [Scala](https://www.scala-lang.org/), och sedan skicka dem till ett HDInsight Spark-kluster direkt från den integrerade IntelliJ utvecklingsmiljö (IDE). Du kan använda plugin-programmet på flera sätt:

* Utveckla och skicka in ett Scala Spark-program på ett HDInsight Spark-kluster.
* Åtkomst till dina Azure HDInsight Spark-klusterresurser.
* Utveckla och kör ett Scala Spark-program lokalt.

I den här guiden får du lära dig att:
> [!div class="checklist"]
> * Använd Azure Toolkit för IntelliJ plugin-programmet
> * Utveckla Apache Spark-program
> * Skicka program till Azure HDInsight-kluster

## <a name="prerequisites"></a>Förutsättningar

* Ett Apache Spark-kluster i HDInsight. Anvisningar finns i [Skapa Apache Spark-kluster i Azure HDInsight](apache-spark-jupyter-spark-sql.md).

* [Oracle Java Development Kit](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).  Den här kursen använder Java version 8.0.202.

* IntelliJ IDEA. Den här artikeln använder [IntelliJ IDEA communityversion  2018.3.4](https://www.jetbrains.com/idea/download/).

* Azure Toolkit for IntelliJ.  Se [Installera Azure Toolkit för IntelliJ](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij-installation?view=azure-java-stable).

* WINUTILS. EXE.  Se [problem att köra Hadoop på Windows](https://wiki.apache.org/hadoop/WindowsProblems).

## <a name="install-scala-plugin-for-intellij-idea"></a>Installera plugin-programmet Scala för IntelliJ IDEA

Installera Scala-plugin-programmet via följande steg:

1. Öppna IntelliJ IDEA.

2. På välkomstskärmen går du till **Konfigurera** > **Plugin-program** för att öppna fönstret **Plugin-program**.
   
    ![Aktivera plugin-programmet Scala](./media/apache-spark-intellij-tool-plugin/enable-scala-plugin.png)

3. Välj **Installera** för det Scala-plugin-program som visas i det nya fönstret.  

    ![Installera plugin-programmet Scala](./media/apache-spark-intellij-tool-plugin/install-scala-plugin.png)

4. Du måste starta om IDE när plugin-programmet har installerats.

## <a name="create-a-spark-scala-application-for-an-hdinsight-spark-cluster"></a>Skapa ett Scala Spark-program för ett HDInsight Spark-kluster

1. Starta IntelliJ IDEA och välj **Skapa nytt projekt** för att öppna fönstret **Nytt projekt**.

2. Välj **Azure Spark/HDInsight** i den vänstra rutan.

3. Välj **Spark-projekt (Scala)** i huvudfönstret.

4. Från listrutan **Byggverktyg** väljer du något av följande:
   * **Maven** för guidestöd när du skapar Scala-projekt.
   * **SBT** för att hantera beroenden när du skapar Scala-projektet.

     ![Dialogrutan Nytt projekt](./media/apache-spark-intellij-tool-plugin/create-hdi-scala-app.png)

5. Välj **Nästa**.

6. I fönstret **Nytt projekt** anger du följande information:  

    |  Egenskap   | Beskrivning   |  
    | ----- | ----- |  
    |Projektnamn| Ange ett namn.  I den här självstudien används `myApp`.|  
    |Projektplats| Ange önskad plats för att spara projektet.|
    |Projekt-SDK| Det kan vara tomma på första gången du använder idé.  Välj **Nytt...**  och navigera till din JDK.|
    |Spark-version|Skapandeguiden integrerar rätt version för Spark SDK och Scala SDK. Om Sparks klusterversion är äldre än 2.0 väljer du **Spark 1.x**. Annars väljer du **Spark 2.x**. I det här exemplet används **Spark 2.3.0 (Scala 2.11.8)** .|

    ![Välj Spark SDK](./media/apache-spark-intellij-tool-plugin/hdi-new-project.png)

7. Välj **Slutför**.  Det kan ta några minuter innan projektet blir tillgänglig.

8. Spark-projekt skapar automatiskt en artefakt. Om du vill visa artefakten, gör du följande:

   a. Från menyraden navigerar du till **filen** > **projektstruktur...** .

   b. Från den **projektstruktur** väljer **artefakter**.  

   c. Välj **Avbryt** när du visar artefakten.

      ![Artefakten information i dialogrutan](./media/apache-spark-intellij-tool-plugin/default-artifact.png)

9. Lägg till din programkällkod genom att göra följande:

    a. Från projektet, navigerar du till **myApp** > **src** > **huvudsakliga** > **scala**.  

    b. Högerklicka på **scala**, och gå sedan till **New** > **Scala klass**.

   ![Kommandon för att skapa en Scala-klass från projektet](./media/apache-spark-intellij-tool-plugin/hdi-spark-scala-code.png)

   c. I den **Skapa ny klass med Scala** dialogrutan rutan, ange ett namn, Välj **objekt** i den **typ** listrutan och välj sedan **OK**.

     ![Skapa ny klass med Scala-dialogrutan](./media/apache-spark-intellij-tool-plugin/hdi-spark-scala-code-object.png)

   d. Den **myApp.scala** sedan öppnas i vyn huvudsakliga filen. Ersätt standardkoden med koden finns nedan:  

        import org.apache.spark.SparkConf
        import org.apache.spark.SparkContext
    
        object myApp{
            def main (arg: Array[String]): Unit = {
            val conf = new SparkConf().setAppName("myApp")
            val sc = new SparkContext(conf)
    
            val rdd = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
    
            //find the rows that have only one digit in the seventh column in the CSV file
            val rdd1 =  rdd.filter(s => s.split(",")(6).length() == 1)
    
            rdd1.saveAsTextFile("wasbs:///HVACOut")
            }
    
        }

    Koden läser data från HVAC.csv (tillgängligt i alla HDInsight Spark-kluster), hämtar de rader som innehåller endast en siffra i den sjunde kolumnen i CSV-filen och skriver utdata till `/HVACOut` under standardbehållare för lagring för klustret.

## <a name="connect-to-your-hdinsight-cluster"></a>Ansluta till ditt HDInsight-kluster
Användaren kan antingen [logga in på Azure-prenumeration](#sign-in-to-your-azure-subscription), eller [länka ett HDInsight-kluster](#link-a-cluster) med Ambari användarnamn/lösenord eller domänanslutna autentiseringsuppgift som ska ansluta till ditt HDInsight-kluster.

### <a name="sign-in-to-your-azure-subscription"></a>Logga in till din Azure-prenumeration

1. Från menyraden navigerar du till **visa** > **verktyget Windows** > **Azure Explorer**.
       
   ![Azure Explorer-länk](./media/apache-spark-intellij-tool-plugin/show-azure-explorer.png)

2. Azure Explorer högerklickar du på den **Azure** noden och välj sedan **logga In**.
   
   ![Azure Explorer-länk](./media/apache-spark-intellij-tool-plugin/explorer-rightclick-azure.png)

3. I den **Azure-inloggning** dialogrutan väljer du **inloggning på enhet**, och välj sedan **logga in**.

    ![Dialogrutan Azure-inloggning](./media/apache-spark-intellij-tool-plugin/view-explorer-2.png)

4. I den **Azure inloggning på enhet** dialogrutan klickar du på **Kopiera & Öppna**.
   
   ![Dialogrutan Azure-inloggning](./media/apache-spark-intellij-tool-plugin/view-explorer-5.png)

5. I Webbläsargränssnittet, klistra in koden och klicka sedan på **nästa**.
   
   ![Dialogrutan Azure-inloggning](./media/apache-spark-intellij-tool-plugin/view-explorer-6.png)

6. Ange dina autentiseringsuppgifter för Azure och stäng sedan webbläsaren.
   
   ![Dialogrutan Azure-inloggning](./media/apache-spark-intellij-tool-plugin/view-explorer-7.png)

7. När du har loggat in, den **Välj prenumerationer** dialogrutan visar en lista över alla Azure-prenumerationer som är associerade med autentiseringsuppgifterna. Välj din prenumeration och välj sedan den **Välj** knappen.

    ![Dialogrutan Välj prenumerationer](./media/apache-spark-intellij-tool-plugin/Select-Subscriptions.png)

8. Från **Azure Explorer**, expandera **HDInsight** visa HDInsight Spark-kluster som finns i dina prenumerationer.

    ![HDInsight Spark-kluster i Azure Explorer](./media/apache-spark-intellij-tool-plugin/view-explorer-3.png)

9.  Du kan ytterligare expandera en nod för klustrets namn om du vill visa de resurser (till exempel lagringskonton) som är associerade med klustret.

    ![En expanderad klusternamnet nod](./media/apache-spark-intellij-tool-plugin/view-explorer-4.png)

### <a name="link-a-cluster"></a>Länka ett kluster

Du kan länka ett HDInsight-kluster med hjälp av Apache Ambari hanteras användarnamnet. På samma sätt för ett domänanslutet HDInsight-kluster du kan länka med hjälp av domänen och användarnamnet, till exempel user1@contoso.com. Du kan också länka Livy Service-kluster.

1. Från menyraden navigerar du till **visa** > **verktyget Windows** > **Azure Explorer**.

2. Azure Explorer högerklickar du på den **HDInsight** noden och välj sedan **Link A kluster**.

   ![länken snabbmenyn för kluster](./media/apache-spark-intellij-tool-plugin/link-a-cluster-context-menu.png)

3. De tillgängliga alternativen i den **Link A kluster** fönstret varierar beroende på vilket värde som du väljer från den **Länkresurstyp** listrutan.  Ange värdena och välj sedan **OK**.

    * **HDInsight-kluster**  
  
        |Egenskap |Värde |
        |----|----|
        |Länkresurstyp|Välj **HDInsight-kluster** från den nedrullningsbara listan.|
        |Klustrets namn/URL| Ange klusternamnet.|
        |Autentiseringstyp| Lämna som **grundläggande autentisering**|
        |Användarnamn| Ange klusteranvändarnamn, standardvärdet är administratör.|
        |Lösenord| Ange lösenordet för användarnamnet.|
    
        ![länka dialogrutan för HDInsight-kluster](./media/apache-spark-intellij-tool-plugin/link-hdinsight-cluster-dialog.png)

    * **Livy-tjänsten**  
  
        |Egenskap |Värde |
        |----|----|
        |Länkresurstyp|Välj **Livy Service** från den nedrullningsbara listan.|
        |Livy-slutpunkt| Ange Livy-slutpunkt|
        |Klusternamn| Ange klusternamnet.|
        |Yarn-slutpunkt|Valfri.|
        |Autentiseringstyp| Lämna som **grundläggande autentisering**|
        |Användarnamn| Ange klusteranvändarnamn, standardvärdet är administratör.|
        |Lösenord| Ange lösenordet för användarnamnet.|

        ![länka Livy kluster dialogrutan](./media/apache-spark-intellij-tool-plugin/link-livy-cluster-dialog.png)

1. Du kan se din länkade kluster från den **HDInsight** noden.

   ![länkade kluster](./media/apache-spark-intellij-tool-plugin/linked-cluster.png)

2. Du kan också Avlänka ett kluster från **Azure Explorer**.

   ![ta bort kopplingen kluster](./media/apache-spark-intellij-tool-plugin/unlink.png)

## <a name="run-a-spark-scala-application-on-an-hdinsight-spark-cluster"></a>Kör ett Scala Spark-program på ett HDInsight Spark-kluster

När du har skapat ett Scala-program, kan du skicka den till klustret.

1. Från projektet, navigerar du till **myApp** > **src** > **huvudsakliga** > **scala**  >  **myApp**.  Högerklicka på **myApp**, och välj **skicka Spark-programmet** (den sannolikt är placerad längst ned i listan).
    
      ![Spark-programmet skicka till HDInsight-kommando](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-1.png)

2. I den **skicka Spark-programmet** dialogruta, väljer **1. Spark i HDInsight**.

3. I den **Upravit konfiguraci** fönstret anger du följande värden och välj sedan **OK**:

    |Egenskap |Värde |
    |----|----|
    |Spark-kluster (endast Linux)|Välj HDInsight Spark-klustret som du vill köra ditt program.|
    |Välj en artefakt att skicka|Lämna standardinställningen.|
    |Namn på main-klass|Standardvärdet är huvudklass från den valda filen. Du kan ändra klassen genom att välja ellipsen ( **...** ) och välja en annan klass.|
    |Jobbkonfigurationer|Du kan ändra standardnycklar och/eller värden. Mer information finns i [Apache Livy REST API](https://livy.incubator.apache.org./docs/latest/rest-api.html).|
    |Kommandoradsargument|Du kan ange argument avgränsade med blanksteg för huvudklass om det behövs.|
    |Refererade JAR-filer och refererade filer|Du kan ange sökvägarna för den refererade JAR-filer och filer eventuellt. Du kan också bläddra bland filer i Azure virtuellt filsystem, som för närvarande endast stöder ADLS Gen 2-kluster. Mer information: [Apache Spark-konfigurationen](https://spark.apache.org/docs/latest/configuration.html#runtime-environment).  Se även [ladda upp resurser ska ingå i klustret](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-storage-explorer).|
    |Ladda upp Jobblagring|Expandera för att visa ytterligare alternativ.|
    |Lagringstyp|Välj **använda Azure Blob för att ladda upp** från den nedrullningsbara listan.|
    |Lagringskonto|Ange ditt storage-konto.|
    |Lagringsnyckel|Ange din lagringsnyckeln.|
    |Storage-behållare|Välj din lagringsbehållare från den nedrullningsbara listan när **Lagringskonto** och **Lagringsnyckeln** har angetts.|

    ![Dialogrutan Skicka Spark](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-02.png)

4. Välj **SparkJobRun** att skicka ditt projekt till det markerade klustret. Den **Remote Spark-jobb i kluster** fliken visas jobbkörningens förlopp längst ned på sidan. Du kan stoppa programmet genom att klicka på den röda knappen. Läs hur du kommer åt jobbutdata i den ”åtkomst och hantera HDInsight Spark-kluster med hjälp av Azure Toolkit för IntelliJ” senare i den här artikeln.  
      
    ![Fönstret Spark bidrag](./media/apache-spark-intellij-tool-plugin/hdi-spark-app-result.png)

## <a name="debug-apache-spark-applications-locally-or-remotely-on-an-hdinsight-cluster"></a>Felsöka Apache Spark-program lokalt eller via fjärranslutning på ett HDInsight-kluster 

Vi rekommenderar också ett annat sätt för att skicka in Spark-programmet till klustret. Du kan göra det genom att ange parametrarna i den **kör/Debug konfigurationer** IDE. Mer information finns i [felsöka Apache Spark-program lokalt eller via fjärranslutning på ett HDInsight-kluster med Azure Toolkit för IntelliJ genom SSH](apache-spark-intellij-tool-debug-remotely-through-ssh.md).

## <a name="access-and-manage-hdinsight-spark-clusters-by-using-azure-toolkit-for-intellij"></a>Komma åt och hantera HDInsight Spark-kluster med hjälp av Azure Toolkit för IntelliJ

Du kan utföra olika åtgärder med hjälp av Azure Toolkit för IntelliJ.  De flesta åtgärder initieras från **Azure Explorer**.  Från menyraden navigerar du till **visa** > **verktyget Windows** > **Azure Explorer**.

### <a name="access-the-job-view"></a>Få åtkomst till jobbvyn

1. Från Azure Explorer navigerar du till **HDInsight** > \<Your kluster >> **jobb**.

    ![Jobbet visa nod](./media/apache-spark-intellij-tool-plugin/job-view-node.png)

2. I den högra rutan i **Spark Jobbvy** fliken visar alla program som körs i klustret. Välj namnet på programmet som du vill se mer information.

    ![Programinformation](./media/apache-spark-intellij-tool-plugin/view-job-logs.png)

3. Hovra över jobbdiagram om du vill visa grundläggande information om som körs. Välj en nod på jobbdiagram om du vill visa faser graph och information som alla jobb genererar.

    ![Information om steg](./media/apache-spark-intellij-tool-plugin/Job-graph-stage-info.png)

4. Visa ofta används loggfiler, till exempel *drivrutinen Stderr*, *drivrutinen Stdout*, och *Directory information*väljer den **Log** fliken.

    ![Logginformation](./media/apache-spark-intellij-tool-plugin/Job-log-info.png)

5. Du kan också visa historik för Spark Användargränssnittet och YARN-Användargränssnittet (på programnivå) genom att välja en länk längst ned i fönstret.

### <a name="access-the-spark-history-server"></a>Access Spark-historikserver

1. Från Azure Explorer, expandera **HDInsight**, högerklickar du på klusternamnet Spark och välj sedan **öppna Användargränssnittet för Spark-historik**.  
2. När du uppmanas ange administratörsautentiseringsuppgifter för klustrets och som du angav när du ställer in i klustret.

3. Du kan använda namnet på programmet för att söka efter programmet att du bara körts på instrumentpanelen för Spark historik server. I föregående kod, ange namnet på programmet med hjälp av `val conf = new SparkConf().setAppName("myApp")`. Programnamnet Spark är därför **myApp**.

### <a name="start-the-ambari-portal"></a>Starta Ambari-portalen

1. Från Azure Explorer, expandera **HDInsight**, högerklickar du på klusternamnet Spark och välj sedan **öppna klustret Management Portal(Ambari)** .  

2. När du uppmanas, ange administratörsautentiseringsuppgifterna för klustret. Du har angett autentiseringsuppgifterna under klusterkonfigurationen.

### <a name="manage-azure-subscriptions"></a>Hantera Azure-prenumerationer

Som standard visar Azure Toolkit för IntelliJ Spark-kluster från alla dina Azure-prenumerationer. Om det behövs kan du ange de prenumerationer som du vill komma åt.  

1. Azure Explorer högerklickar du på den **Azure** root node och välj sedan **Välj prenumerationer**.  

2. Från den **Välj prenumerationer** fönstret avmarkerar kryssrutorna bredvid prenumerationerna som du inte vill få åtkomst till och välj sedan **Stäng**.

## <a name="spark-console"></a>Spark-konsolen

Du kan köra Spark lokala Console(Scala) eller köra Spark Livy interaktiva sessionen Console(Scala).

### <a name="spark-local-consolescala"></a>Spark lokala Console(Scala)

Se till att du har uppfyllt WINUTILS. EXE-krav.

1. Från menyraden navigerar du till **kör** > **redigera konfigurationer...** .

2. Från den **kör/Debug konfigurationer** i den vänstra rutan i fönstret går du till **Apache Spark i HDInsight** >  **[Spark på HDInsight] myApp**.

3. I huvudfönstret, Välj den **lokalt kör** fliken.

4. Ange följande värden och välj sedan **OK**:

    |Egenskap |Värde |
    |----|----|
    |Jobbet main-klass|Standardvärdet är huvudklass från den valda filen. Du kan ändra klassen genom att välja ellipsen ( **...** ) och välja en annan klass.|
    |Miljövariabler|Kontrollera värdet för HADOOP_HOME är korrekt.|
    |WINUTILS.exe plats|Se till att sökvägen är korrekt.|

    ![Konfiguration av lokal konsol](./media/apache-spark-intellij-tool-plugin/console-set-configuration.png)

5. Från projektet, navigerar du till **myApp** > **src** > **huvudsakliga** > **scala**  >  **myApp**.  

6. Från menyraden navigerar du till **verktyg** > **Spark konsolen** > **kör Spark lokala Console(Scala)** .

7. Sedan ska två dialogrutor visas om du vill ställa du om du vill att automatiskt åtgärda beroenden. I så, fall Välj **automatiskt åtgärda**.

    ![Spark automatisk Fix1](./media/apache-spark-intellij-tool-plugin/console-auto-fix1.png)

    ![Spark automatisk Fix2](./media/apache-spark-intellij-tool-plugin/console-auto-fix2.png)

8. Konsolen ska se ut som i bilden nedan. I konsolen fönstret typen `sc.appName`, och tryck på ctrl + RETUR.  Resultatet visas. Du kan säga upp den lokala konsolen genom att klicka på röd knapp.

    ![Lokala konsolen resultat](./media/apache-spark-intellij-tool-plugin/local-console-result.png)

### <a name="spark-livy-interactive-session-consolescala"></a>Spark-Livy interaktiva sessionen Console(Scala)

Den stöds bara på IntelliJ 2018.2 och 2018.3.

1. Från menyraden navigerar du till **kör** > **redigera konfigurationer...** .

2. Från den **kör/Debug konfigurationer** i den vänstra rutan i fönstret går du till **Apache Spark i HDInsight** >  **[Spark på HDInsight] myApp**.

3. I huvudfönstret, Välj den **fjärrköra i kluster** fliken.

4. Ange följande värden och välj sedan **OK**:

    |Egenskap |Värde |
    |----|----|
    |Spark-kluster (endast Linux)|Välj HDInsight Spark-klustret som du vill köra ditt program.|
    |Namn på main-klass|Standardvärdet är huvudklass från den valda filen. Du kan ändra klassen genom att välja ellipsen ( **...** ) och välja en annan klass.|

    ![Konfiguration av interaktiv konsol](./media/apache-spark-intellij-tool-plugin/interactive-console-configuration.png)

5. Från projektet, navigerar du till **myApp** > **src** > **huvudsakliga** > **scala**  >  **myApp**.  

6. Från menyraden navigerar du till **verktyg** > **Spark konsolen** > **kör Spark Livy interaktiva sessionen Console(Scala)** .

7. Konsolen ska se ut som i bilden nedan. I konsolen fönstret typen `sc.appName`, och tryck på ctrl + RETUR.  Resultatet visas. Du kan säga upp den lokala konsolen genom att klicka på röd knapp.

    ![Interaktiv konsol resultat](./media/apache-spark-intellij-tool-plugin/interactive-console-result.png)

### <a name="send-selection-to-spark-console"></a>Skicka markeringen till Spark-konsolen

Det är bra att förutse resultatet skriptet genom att skicka kod till den lokala konsolen eller Livy interaktiva sessionen Console(Scala). Du kan markera kod i filen Scala och sedan högerklickar du på **skicka markeringen till Spark konsolen**. Den markerade koden kommer att skickas till konsolen och utföras. Resultatet visas efter den i konsolen. Konsolen ska kontrollera felen om befintlig.  

   ![Skicka markeringen till Spark-konsolen](./media/apache-spark-intellij-tool-plugin/send-selection-to-console.png)

## <a name="reader-only-role"></a>Endast läsare roll

När användarna skicka jobbet till ett kluster med endast läsare rollbehörighet, krävs autentiseringsuppgifter för Ambari.

### <a name="link-cluster-from-context-menu"></a>Länk-kluster från snabbmenyn

1. Logga in med endast läsare-rollen.
       
2. Från **Azure Explorer**, expandera **HDInsight** visa HDInsight-kluster som finns i din prenumeration. Klustren markerats **”roll: läsare”** bara har endast läsare rollbehörighet.

    ![HDInsight Spark-kluster i Azure Explorer](./media/apache-spark-intellij-tool-plugin/view-explorer-15.png)

3. Högerklicka på klustret med endast läsare rollbehörighet. Välj **länka det här klustret** från snabbmenyn för att länka klustret. Ange Ambari användarnamn och lösenord.

  
    ![HDInsight Spark-kluster i Azure Explorer](./media/apache-spark-intellij-tool-plugin/view-explorer-11.png)

4. Om klustret är länkat uppdateras HDInsight.
   Steget i klustret kommer att bli länkas.
  
    ![HDInsight Spark-kluster i Azure Explorer](./media/apache-spark-intellij-tool-plugin/view-explorer-8.png)

### <a name="link-cluster-by-expanding-jobs-node"></a>Länk-kluster genom att expandera noden för jobb

1. Klicka på **jobb** noden **kluster jobb åtkomst nekad** fönstret som öppnas.
   
2. Klicka på **länka det här klustret** länka kluster.
   
    ![HDInsight Spark-kluster i Azure Explorer](./media/apache-spark-intellij-tool-plugin/view-explorer-9.png)

### <a name="link-cluster-from-rundebug-configurations-window"></a>Länk-kluster från fönstret kör/Debug-konfigurationer

1. Skapa ett HDInsight-konfigurationen. Välj sedan **fjärrköra i kluster**.
   
2. Välj ett kluster som har läsare-endast rollbehörighet för **Spark-kluster (endast Linux)** . Varningsmeddelande visar ut. Du kan klicka på **länka det här klustret** länka kluster.
   
   ![HDInsight Spark-kluster i Azure Explorer](./media/apache-spark-intellij-tool-plugin/create-config-1.png)
   
### <a name="view-storage-accounts"></a>Visa Storage-konton

* För kluster med endast läsare rollbehörighet klickar du på **Lagringskonton** noden **Storage åtkomst nekad** fönstret som öppnas. Du kan klicka på **öppna Azure Storage Explorer** att öppna Storage Explorer.
     
   ![HDInsight Spark-kluster i Azure Explorer](./media/apache-spark-intellij-tool-plugin/view-explorer-14.png)

   ![HDInsight Spark-kluster i Azure Explorer](./media/apache-spark-intellij-tool-plugin/view-explorer-10.png)

* För länkade kluster, klickar du på **Lagringskonton** noden **Storage åtkomst nekad** fönstret som öppnas. Du kan klicka på **öppna Azure Storage** att öppna Storage Explorer.
     
   ![HDInsight Spark-kluster i Azure Explorer](./media/apache-spark-intellij-tool-plugin/view-explorer-13.png)

   ![HDInsight Spark-kluster i Azure Explorer](./media/apache-spark-intellij-tool-plugin/view-explorer-12.png)

## <a name="convert-existing-intellij-idea-applications-to-use-azure-toolkit-for-intellij"></a>Konvertera befintliga IntelliJ IDEA-programmen kan använda Azure Toolkit för IntelliJ

Du kan konvertera befintliga Spark Scala program som du skapade i IntelliJ IDEA till att vara kompatibel med Azure Toolkit för IntelliJ. Du kan sedan använda plugin-programmet för att skicka program till ett HDInsight Spark-kluster.

1. Öppna filen associerade .iml för ett befintligt Spark Scala-program som har skapats via IntelliJ IDEA.

2. I roten nivån är en **modulen** elementet enligt följande:
   
        <module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4">

   Redigera elementet så att lägga till `UniqueKey="HDInsightTool"` så att den **modulen** ser ut så här:
   
        <module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4" UniqueKey="HDInsightTool">

3. Spara ändringarna. Ditt program bör nu vara kompatibla med Azure Toolkit för IntelliJ. Du kan testa den genom att högerklicka på projektnamnet i projektet. Snabbmenyn har nu alternativet **skicka Spark-program till HDInsight**.

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte planerar att fortsätta använda det här programmet, tar du bort klustret som du skapade med följande steg:

1. Logga in på [Azure Portal](https://portal.azure.com/).

1. I rutan **Sök** längst upp skriver du **HDInsight**.

1. Välj **HDInsight-kluster** under **Tjänster**.

1. I listan över HDInsight-kluster som visas, väljer du den **...**  bredvid det kluster som du skapade för den här kursen.

1. Välj **Ta bort**. Välj **Ja**.

![Ta bort ett HDInsight-kluster](./media/apache-spark-intellij-tool-plugin/hdinsight-azure-portal-delete-cluster.png "Ta bort HDInsight-kluster")

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig hur du använder Azure Toolkit för IntelliJ plugin-programmet för att utveckla Apache Spark-program som skrivits i [Scala](https://www.scala-lang.org/), och skickat dem till ett HDInsight Spark-kluster direkt från den integrerade IntelliJ utvecklingsmiljö (IDE). Gå vidare till nästa artikel för att se hur de data som du har registrerat i Apache Spark kan hämtas till ett BI-analysverktyg såsom Power BI.

> [!div class="nextstepaction"]
> [Analysera data med BI-verktyg](apache-spark-use-bi-tools.md)
