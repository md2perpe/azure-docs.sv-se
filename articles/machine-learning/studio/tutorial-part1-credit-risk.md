---
title: 'Självstudie 1: Förutsäga kreditrisk'
titleSuffix: Azure Machine Learning Studio
description: En detaljerad självstudie om hur du skapar en förutsägelseanalys för kreditriskbedömning i Azure Machine Learning Studio. Den här självstudien är del ett i en självstudieserie i tre delar.  Den visar hur du skapar en arbetsyta, laddar upp data och skapar ett experiment.
keywords: kreditrisk, lösning för förutsägelseanalys, riskbedömning
author: sdgilley
ms.author: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: tutorial
ms.date: 02/11/2019
ms.openlocfilehash: f9746dae4cdf10a10922be41602f4ecd7f032f5b
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65949791"
---
# <a name="tutorial-1-predict-credit-risk---azure-machine-learning-studio"></a>Självstudie 1: Förutsäga kreditrisk – Azure Machine Learning Studio

I den här självstudien tittar vi närmare på hur du utvecklar en lösning för förutsägelseanalys. Du ska utveckla en enkel modell i Machine Learning Studio.  Därefter distribuerar du modellen som en Azure Machine Learning-webbtjänst.  Den här distribuerade modellen kan göra förutsägelser med nya data. Den här självstudien är **del ett i en självstudieserie i tre delar**.

Anta att du behöver förutsäga kreditrisken för en person baserat på den information som han eller hon fyller i på en kreditansökan.  

Kreditriskbedömning är ett komplext problem, men den här självstudien kommer att förenkla processen. Du använder det som ett exempel på hur du kan skapa en lösning för förutsägelseanalys med hjälp av Microsoft Azure Machine Learning Studio. Du använder Azure Machine Learning Studio och en Machine Learning-webbtjänst i den här lösningen.  

I den här självstudien i tre delar börjar du med offentligt tillgängliga kreditriskdata.  Därefter utvecklar du och tränar en förutsägelsemodell.  Slutligen distribuerar du modellen som en webbtjänst.

I den här delen av självstudien ska du: 
 
> [!div class="checklist"]
> * Skapa en Machine Learning Studio-arbetsyta
> * Överför befintliga data
> * Skapa ett experiment

Du kan sedan använda det här experimentet till att [träna modeller i del 2](tutorial-part2-credit-risk-train.md) och sedan [distribuera dem i del 3](tutorial-part3-credit-risk-deploy.md).

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]


## <a name="prerequisites"></a>Nödvändiga komponenter

I den här självstudien förutsätter vi att du har använt Machine Learning Studio åtminstone någon gång och att du är någorlunda insatt i vad maskininlärning är. Men vi förväntar oss inte att du är en expert.

Om du aldrig har använt **Azure Machine Learning Studio** förut vill du kanske börja med snabbstarten, [Skapa ditt första dataexperiment i Azure Machine Learning Studio](create-experiment.md). Snabbstarten vägleder dig genom första användningen av Machine Learning Studio. Vi går vi igenom grunderna och förklarar hur du drar och släpper moduler i ett experiment, hur du kopplar ihop dem och hur du kör experimentet. Vi avslutar med att titta på resultatet.


> [!TIP] 
> Du hittar en fungerande kopia av det experiment som du skapar i den här självstudien i [Azure AI Gallery](https://gallery.azure.ai). Gå till **[Självstudie – Förutsäga kreditrisk](https://gallery.azure.ai/Experiment/Walkthrough-Credit-risk-prediction-1)** och klicka på **Öppna i Studio** för att ladda ned en kopia av experimentet till din Machine Learning Studio-arbetsyta.
> 


## <a name="create-a-machine-learning-studio-workspace"></a>Skapa en Machine Learning Studio-arbetsyta

För att kunna använda Machine Learning Studio behöver du ha en Microsoft Azure Machine Learning Studio-arbetsyta. Den här arbetsytan innehåller de verktyg du behöver för att skapa, hantera och publicera experiment.  

Information om hur du skapar en arbetsyta finns i [Skapa och dela en Azure Machine Learning Studio-arbetsyta](create-workspace.md).

När arbetsytan har skapats kan du öppna Machine Learning Studio ([https://studio.azureml.net/Home](https://studio.azureml.net/Home)). Om du har fler än en arbetsyta kan du välja arbetsytan i verktygsfältet i det övre högra hörnet i fönstret.

![Välja arbetsyta i Studio](./media/tutorial-part1-credit-risk/open-workspace.png)

> [!TIP]
> Om du är arbetsytans ägare kan du dela de experiment som du arbetar med genom att bjuda in andra till arbetsytan. Du kan göra detta i Machine Learning Studio på sidan **SETTINGS** (Inställningar). Du behöver bara det Microsoft-konto eller organisationskonto för varje användare.
> 
> På sidan **SETTINGS** (Inställningar) klickar du på **USERS** (Användare) och sedan på **INVITE MORE USERS** (Bjud in fler användare) längst ned i fönstret.
> 

## <a name="upload"></a>Ladda upp befintliga data

För att utveckla en förutsägande modell för kreditrisk, behöver du data som du kan använda för att träna och sedan testa modellen. I den här självstudien använder du ”UCI Statlog (German Credit Data) Data Set” (kreditdata från Tyskland) från UC Irvine Machine Learning-databasen. Den finns här:  
<a href="https://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)">https://archive.ics.uci.edu/ml/datasets/Statlog+(German+Credit+Data)</a>

Du använder filen med namnet **german.data**. Ladda ned den här filen till den lokala hårddisken.  

Datamängden **german.data** innehåller rader med 20 variabler för 1000 tidigare kreditsökanden. De här 20 variablerna representerar datamängdens uppsättning med funktioner (*funktionsvektorn*), som tillhandahåller identifieringsegenskaper för varje kreditsökande. Ytterligare en kolumn i varje rad representerar sökandens beräknade kreditrisk. 700 sökande har identifierats som en låg kreditrisk och 300 som en hög risk.

UCI-webbplatsen innehåller en beskrivning av attributen för funktionsvektor för dessa data. Dessa data innehåller ekonomisk information, kredithistorik, anställningsstatus och personlig information. Varje sökande har getts en binär klassificering som anger om sökanden utgör en låg eller hög kreditrisk. 

Du använder dessa data för att träna en modell för förutsägelseanalys. När du är klar, ska din modell kunna acceptera en funktionen vektor för en ny person och förutsäga om de är en låg eller hög kreditrisk.  

Här är en intressant problemvariant.

I beskrivningen av datamängden på UCI-webbplatsen står det hur mycket det kostar om du skapar en felaktig klassificering av kreditrisken för en person.
Om modellen förutsäger en hög kreditrisk för en person som faktiskt utgör en låg kreditrisk har modellen gjort en felaktig klassificering.

Den omvända felklassificeringen är dock fem gånger dyrare för finansinstitutet: om modellen förutsäger en låg kreditrisk för en person som faktiskt utgör en hög kreditrisk.

Därför ska du träna modellen så att kostnaden för den senare typen av felklassificering är fem gånger högre än den andra felklassificeringen.

Ett enkelt sätt att göra detta när du tränar modellen i experimentet är att duplicera (fem gånger) de poster som representerar någon med en hög kreditrisk. 

Om modellen sedan felklassificerar någon som en låg kreditrisk när den personen faktiskt utgör en hög risk genomför modellen samma felklassificering fem gånger, en gång för varje dubblett. Detta ökar kostnaden för det här felet i träningsresultatet.


### <a name="convert-the-dataset-format"></a>Konvertera datamängdens format

Den ursprungliga datamängden använder ett format med blankstegsavgränsning. Machine Learning Studio fungerar bättre med en kommaavgränsad fil (CSV). Därför konverterar du datamängden genom att ersätta blanksteg med kommatecken.  

Det finns många sätt att konvertera dessa data. Ett sätt är att använda följande Windows PowerShell-kommando:   

    cat german.data | %{$_ -replace " ",","} | sc german.csv  

Ett annat sätt är att använda Unix sed-kommandot:  

    sed 's/ /,/g' german.data > german.csv  

I båda fallen skapar du en kommaavgränsad version av data i en fil med namnet **german.csv** som du kan använda i experimentet.

### <a name="upload-the-dataset-to-machine-learning-studio"></a>Ladda upp datamängden till Machine Learning Studio

När data har konverterats till CSV-format behöver du ladda upp dem till Machine Learning Studio. 

1. Öppna startsidan för Machine Learning Studio ([https://studio.azureml.net](https://studio.azureml.net)). 

2. Klicka på menyn ![Meny](./media/tutorial-part1-credit-risk/menu.png) i det övre vänstra hörnet i fönstret, klicka på **Azure Machine Learning**, välj **Studio** och logga in.

3. Klicka på **+NEW** (Ny) längst ned i fönstret.

4. Välj **DATASET** (Datamängd).

5. Välj **FROM LOCAL FILE** (Från lokal fil).

    ![Lägga till en datamängd från en lokal fil](./media/tutorial-part1-credit-risk/add-dataset.png)

6. I dialogrutan **Upload a new dataset** (Ladda upp en ny datamängd) klickar du på Bläddra och letar upp filen **german.csv** som du skapade.

7. Ange ett namn för datamängden. I den här självstudien kallar du den ”UCI German Credit Card Data”.

8. För datatypen väljer du **Generic CSV File With no header (.nh.csv)** (Generisk CSV-fil utan rubrik).

9. Lägg till en beskrivning om du vill.

10. Klicka på kryssmarkeringen **OK**.  

    ![Ladda upp datamängden](./media/tutorial-part1-credit-risk/upload-dataset.png)

Detta laddar upp data till en datamängdsmodul som du kan använda i ett experiment.

Du kan hantera datamängder som du har laddat upp till Studio genom att klicka på fliken **DATASETS** (Datamängder) till vänster i Studio-fönstret.

![Hantera datamängder](./media/tutorial-part1-credit-risk/dataset-list.png)

Mer information om hur du importerar andra typer av data till ett experiment finns i avsnittet om att [importera dina träningsdata till Azure Machine Learning Studio](import-data.md).

## <a name="create-an-experiment"></a>Skapa ett experiment

Nästa steg i den här självstudien är att skapa ett experiment i Machine Learning Studio som använder den datamängd som du laddade upp.  

1. I Studio klickar du på **+NEW** (Ny) längst ned i fönstret.
1. Välj **EXPERIMENT** och sedan ”Blank Experiment” (Tomt experiment). 

    ![Skapa ett nytt experiment](./media/tutorial-part1-credit-risk/create-new-experiment.png)


1. Välj det fördefinierade experimentnamnet överst på arbetsytan och byt namn på de till ett mer beskrivande namn.

    ![Byta namn på experimentet](./media/tutorial-part1-credit-risk/rename-experiment.png)

   > [!TIP]
   > Det är bra att fylla i **Summary** (Sammanfattning) och **Description** (Beskrivning) för experimentet i fönstret **Properties** (Egenskaper). De här egenskaperna ger dig möjlighet att dokumentera experimentet så att alla som tittar på det senare kan förstå dina mål och metoder.
   > 
   > ![Egenskaper för experiment](./media/tutorial-part1-credit-risk/experiment-properties.png)
   > 

1. I modulpaletten till vänster om experimentets arbetsyta expanderar du **Saved Datasets** (Sparade datamängder).
1. Leta upp den datamängd som du skapade under **My Datasets** (Mina datamängder) och dra den till arbetsytan. Du kan även hitta datamängden genom att ange namnet i rutan **Search** (Sök) ovanför paletten.  

    ![Lägga till datamängden i experimentet](./media/tutorial-part1-credit-risk/add-dataset-to-experiment.png)


### <a name="prepare-the-data"></a>Förbereda data

Du kan visa de första 100 dataraderna och viss statistisk information för hela datamängden: Klicka på utdataporten för datamängden (den lilla cirkeln längst ned) och välj **Visualize** (Visualisera).  

Eftersom datafilen inte levererades med kolumnrubriker har Studio angett allmänna rubriker (Col1, Col2, *osv.*). Bra rubriker är inte nödvändiga för skapandet av en modell, men de gör det lättare att arbeta med data i experimentet. Och när du senare publicerar den här modellen i en webbtjänst hjälper rubrikerna till att identifiera kolumnerna för användare av tjänsten.  

Du kan lägga till kolumnrubriker med hjälp av modulen [Edit Metadata][edit-metadata] (Redigera metadata).

Du använder modulen [Edit Metadata][edit-metadata] (Redigera metadata) för att ändra metadata som associeras med en datamängd. I det här fallet använder du den för att ge kolumnrubrikerna mer användbara namn. 

Du använder [Edit Metadata][edit-metadata] (Redigera metadata) genom att först ange vilka kolumner som ska ändras (i det här fallet alla). Sedan anger du den åtgärd som ska utföras på de kolumnerna (i det här fallet ska kolumnrubrikerna ändras).

1. På modulpaletten skriver du ”metadata” i rutan **Search** (Sök). [Edit Metadata][edit-metadata] (Redigera metadata) visas i modullistan.

1. Klicka och dra modulen [Edit Metadata][edit-metadata] (Redigera metadata) till arbetsytan och släpp den nedanför den datamängd som du lade till tidigare.

1. Ansluta datamängden till [Edit Metadata][edit-metadata] (Redigera metadata): klicka på utdataporten för datamängden (den lilla cirkeln längst ned i datamängden), dra till indataporten för [Edit Metadata][edit-metadata] (Redigera metadata) (den lilla cirkeln längst upp i modulen) och släpp sedan musknappen. Datamängden och modulen förblir anslutna även om du flyttar runt endera av dem på arbetsytan.
 
    Nu bör experimentet se ut ungefär så här:  

    ![Lägga till metadata](./media/tutorial-part1-credit-risk/experiment-with-edit-metadata-module.png)

    Det röda utropstecknet anger att du inte har angett egenskaperna för den här modulen ännu. Det gör du i nästa steg.

    > [!TIP]
    > Du kan lägga till en kommentar till en modul genom att dubbelklicka på modulen och skriva text. På så sätt kan du snabbt se vad modulen gör i experimentet. I det här fallet dubbelklickar du på modulen [Edit Metadata][edit-metadata] (Redigera metadata) och skriver kommentaren ”Lägg till kolumnrubriker”. Klicka någon annanstans på arbetsytan för att stänga textrutan. Om du vill visa kommentaren klickar du på nedåtpilen i modulen.
    > 
    > ![Redigera metadatamodulen med tillagd kommentar](./media/tutorial-part1-credit-risk/edit-metadata-with-comment.png)
    > 

1. Välj [Edit Metadata][edit-metadata] (Redigera metadata). I fönstret **Properties** (Egenskaper) till höger om arbetsytan klickar du på **Launch column selector** (Starta kolumnväljaren).

1. I dialogrutan **Select columns** (Välj kolumner) väljer du alla rader i **Available Columns** (Tillgängliga kolumner) och klickar på > för att flytta dem till **Selected Columns** (Valda kolumner).
   Dialogrutan bör se ut så här:

   ![Kolumnväljaren med alla kolumner valda](./media/tutorial-part1-credit-risk/select-columns.png)


1. Klicka på kryssmarkeringen **OK**.

1. I fönstret **Properties** (Egenskaper) letar du upp parametern **New column names** (Nya kolumnnamn). I det här fältet anger du en lista med namn för de 21 kolumnerna i datamängden, avgränsade med kommatecken och i kolumnordning. Du kan hämta kolumnnamnen från datamängdens dokumentation på UCI-webbplatsen eller kopiera och klistra in följande lista:  

   ```   
   Status of checking account, Duration in months, Credit history, Purpose, Credit amount, Savings account/bond, Present employment since, Installment rate in percentage of disposable income, Personal status and sex, Other debtors, Present residence since, Property, Age in years, Other installment plans, Housing, Number of existing credits, Job, Number of people providing maintenance for, Telephone, Foreign worker, Credit risk  
   ```

   Fönstret Properties (Egenskaper) ser ut så här:

   ![Egenskaper för Redigera metadata](./media/tutorial-part1-credit-risk/edit-metadata-properties.png)

   > [!TIP]
   > Om du vill kontrollera kolumnrubrikerna kör du experimentet (klicka på **RUN** (Kör) nedanför arbetsytan för experimentet). När det har körts klart (en grön markering visas i [Edit Metadata][edit-metadata] (Redigera metadata)) klickar du på utdataporten för modulen [Edit Metadata][edit-metadata] (Redigera metadata) och väljer **Visualize** (Visualisera). Du kan visa utdata från valfri modul på samma sätt för att visa förloppet för data genom experimentet.
   > 
   > 

### <a name="create-training-and-test-datasets"></a>Skapa datamängder för träning och testning

Du behöver en del data för att träna modellen och en del för att testa den.
I nästa steg i experimentet delar du därför in datamängden i två separata datamängder: en för att träna modellen och en för att testa den.

Det gör du med hjälp av modulen [Split Data][split] (Dela data).  

1. Leta upp modulen [Split Data][split] (Dela data), dra den till arbetsytan och anslut den till modulen [Edit Metadata][edit-metadata] (Redigera metadata).

1. Som standard är delningsförhållandet 0,5 och parametern **Randomized split** (Slumpmässig delning) anges. Det innebär att en slumpmässig halva av data matas ut via en port i modulen [Split Data][split] (Dela data) och halva via den andra. Du kan justera de här parametrarna samt parametern **Random seed** (Slumpmässigt frö) för att ändra delningen mellan träningsdata och testningsdata. I det här exemplet låter du dem vara som de är.
   
   > [!TIP]
   > Egenskapen **Fraction of rows in the first output dataset** (Andel rader i den första utdatamängden) avgör hur mycket av data som matas ut via den *vänstra* utdataporten. Om du till exempel anger förhållandet till 0,7 matas 70 % av data ut via den vänstra porten och 30 % via den högra porten.  
   > 
   > 

1. Dubbelklicka på modulen [Split Data][split] (Dela data) och ange kommentaren ”Tränings-/testningsdata delat 50 %”. 

Du kan använda utdata för modulen [Split Data][split] (Dela data) på det sätt du vill, men just nu väljer vi att använda vänster utdata som träningsdata och höger utdata som testningsdata.  

Som vi nämnde i det [föregående steget](tutorial-part1-credit-risk.md#upload) är kostnaden för felklassificering av en hög kreditrisk som låg fem gånger högre än kostnaden för felklassificering av en låg kreditrisk som hög. Du beaktar detta genom att generera en ny datamängd som speglar den här kostnadsfunktionen. I den nya datamängden replikeras varje högriskexempel fem gånger medan varje lågriskexempel inte replikeras.   

Du kan utföra replikeringen med hjälp av R-kod:  

1. Leta upp och dra modulen [Execute R Script][execute-r-script] (Kör R-skript) till experimentets arbetsyta. 

1. Anslut den vänstra utdataporten för modulen [Split Data][split] (Dela data) till den första indataporten (”Dataset1”) i modulen [Execute R Script][execute-r-script] (Kör R-skript).

1. Dubbelklicka på modulen [Execute R Script][execute-r-script] (Kör R-skript) och ange kommentaren ”Ange kostnadsjustering”.

1. I fönstret **Properties** (Egenskaper) tar du bort standardtexten i parametern **R Script** och anger följande skript:
   
       dataset1 <- maml.mapInputPort(1)
       data.set<-dataset1[dataset1[,21]==1,]
       pos<-dataset1[dataset1[,21]==2,]
       for (i in 1:5) data.set<-rbind(data.set,pos)
       maml.mapOutputPort("data.set")

    ![R-skript i modulen Execute R Script](./media/tutorial-part1-credit-risk/execute-r-script.png)

Du behöver utföra samma replikeringsåtgärden för varje utdata från modulen [Split Data][split] (Dela data) så att träningsdata och testningsdata inte har samma kostnadsjustering. Det enklaste sättet att göra detta är att duplicera den [Execute R Script][execute-r-script]-modul (Kör R-skript) som du just skapade och ansluta den till den andra utdataporten för modulen [Split Data][split] (Dela data).

1. Högerklicka på [Execute R Script][execute-r-script] (Kör R-skript) och välj **Copy** (Kopiera).

1. Högerklicka på experimentets arbetsyta och välj **Paste** (Klistra in).

1. Dra den nya modulen till rätt plats och anslut den högra utdataporten för modulen [Split Data][split] (Dela data) till den första indataporten för den nya [Execute R Script][execute-r-script]-modulen (Kör R-skript). 

1. Längst ned på arbetsytan klickar du på **Run** (Kör). 

> [!TIP]
> Kopian av Execute R Script-modulen (Kör R-skript) innehåller samma skript som den ursprungliga modulen. När du kopierar och klistrar in en modul på arbetsytan behåller kopian alla egenskaper från originalet.  
> 
>

Nu ser experimentet ut ungefär så här:

![Lägga till Split-modulen och R-skript](./media/tutorial-part1-credit-risk/experiment.png)

Mer information om hur du använder R-skript i experiment finns i [Utöka experimentet med R](extend-your-experiment-with-r.md).


## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [machine-learning-studio-clean-up](../../../includes/machine-learning-studio-clean-up.md)]

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du slutfört dessa steg: 
 
> [!div class="checklist"]
> * Skapa en Machine Learning Studio-arbetsyta
> * Ladda upp befintliga data till arbetsytan
> * Skapa ett experiment

Du är nu redo att träna och utvärdera modeller för dessa data.

> [!div class="nextstepaction"]
> [Självstudie 2 – Träna och utvärdera modeller](tutorial-part2-credit-risk-train.md)

<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[edit-metadata]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
