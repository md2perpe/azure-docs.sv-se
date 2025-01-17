---
title: Kända problem och felsökning
titleSuffix: Azure Machine Learning service
description: Hämta en lista över kända problem, lösningar, och felsökning för Azure Machine Learning-tjänsten.
services: machine-learning
author: j-martens
ms.author: jmartens
ms.reviewer: mldocs
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 04/30/2019
ms.custom: seodec18
ms.openlocfilehash: 80bb7af0f7ed20336ab08d4f3ca9639057b9c67f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65149762"
---
# <a name="known-issues-and-troubleshooting-azure-machine-learning-service"></a>Kända problem och felsökning Azure Machine Learning-tjänsten

Den här artikeln hjälper dig att hitta och korrigera fel eller fel som uppstod när du använder Azure Machine Learning-tjänsten.

## <a name="visual-interface-issues"></a>Visuella gränssnittet problem

Visuella gränssnittet för machine learning-tjänstproblem.

### <a name="long-compute-preparation-time"></a>Länge beräkningsnoderna förberedelsetid

Skapa ny beräkning eller använda lämnar beräkningstid tar kan vara några minuter eller ännu längre. Den här gruppen arbetar för optimering.


### <a name="cannot-run-an-experiment-only-contains-dataset"></a>Köra ett experiment det går inte att endast innehåller datauppsättningen 

Du kan vilja köra ett experiment som endast innehåller datauppsättning för att visualisera datauppsättningen. Men det går inte för att köra ett experiment endast innehåller datauppsättningen idag. Vi åtgärdar aktivt problemet.
 
Innan åtgärden, kan du ansluta datauppsättningen till alla data på en omvandling-moduler (Välj kolumner i datauppsättning, redigera Metadata, dela Data osv) och kör experimentet. Sedan kan du visualisera datauppsättningen. 

Nedan bild visar hur: ![visulize-data](./media/resource-known-issues/aml-visualize-data.png)

## <a name="sdk-installation-issues"></a>SDK-installationsproblem

**Felmeddelande: Det går inte att avinstallera 'PyYAML'**

Azure Machine Learning-SDK för Python: PyYAML är ett distutils installerade projekt. Vi kan inte därför korrekt fastställa vilka filer som hör till det om det finns en partiell avinstallation. Om du vill fortsätta installerar denna SDK när du ignorera det här felet, använder du:

```Python
pip install --upgrade azureml-sdk[notebooks,automl] --ignore-installed PyYAML
```

## <a name="trouble-creating-azure-machine-learning-compute"></a>Problem med att skapa beräkning av Azure Machine Learning

Det finns en ovanligt risk att vissa användare som skapade sin Azure Machine Learning-arbetsyta från Azure-portalen innan GA-versionen kan inte skapa beräkning av Azure Machine Learning på arbetsytan. Du kan generera en supportförfrågan mot tjänsten, eller så kan du skapa en ny arbetsyta via portalen eller SDK, för att avblockera själv omedelbart.

## <a name="image-building-failure"></a>Bild byggnad fel

Bild för att skapa fel när du distribuerar webbtjänsten. Lösningen är att lägga till ”pynacl == 1.2.1” som ett pip beroende till Conda-fil för konfiguration av avbildningen.

## <a name="deployment-failure"></a>Distributionsfel

Om du observerar `['DaskOnBatch:context_managers.DaskOnBatch', 'setup.py']' died with <Signals.SIGKILL: 9>`, ändra SKU: N för virtuella datorer som används i distributionen till ett som har mer minne.

## <a name="fpgas"></a>FPGA

Du kommer inte att kunna distribuera modeller på FPGA förrän du har begärt och godkänts för FPGA kvot. För att begära åtkomst, fyller du i formuläret för begäran av kvot: https://aka.ms/aml-real-time-ai

## <a name="automated-machine-learning"></a>Automatiserad maskininlärning

Tensor Flow automatiserad maskininlärning stöder för närvarande inte tensor flow version 1.13. Installera den här versionen kommer paketberoenden slutar att fungera. Vi arbetar för att åtgärda problemet i en framtida version. 

### <a name="experiment-charts"></a>Experiment diagram

Binär klassificering diagram (precisionsåterkallningsdiagram, ROC, få kurvan osv) visas i iterationer av automatiserade ML-experiment är inte rendering corectly i användargränssnittet eftersom 4/12. Diagrammet diagrammen finns för närvarande som visar inverterade resultat, där modeller för bättre prestanda visas med lägre resultat. En lösning är under undersökningen.

## <a name="databricks"></a>Databricks

Databricks och Azure Machine Learning-problem.

### <a name="failure-when-installing-packages"></a>Fel vid installation av paket

Azure Machine Learning SDK-installation misslyckas på Azure Databricks när flera paket installeras. Vissa paket, till exempel `psutil`, kan orsaka konflikter. Undvik installationsfel genom att installera paket genom att frysa version library. Det här problemet är relaterat till Databricks och inte till Azure Machine Learning-tjänst-SDK. Du kan uppleva problemet med andra bibliotek för. Exempel:

```python
psutil cryptography==1.5 pyopenssl==16.0.0 ipython==2.2.0
```

Du kan också använda init skript om du hålla får installera problem med med Python-biblioteken. Den här metoden stöds inte officiellt. Mer information finns i [kluster-omfattande init skript](https://docs.azuredatabricks.net/user-guide/clusters/init-scripts.html#cluster-scoped-init-scripts).

### <a name="cancel-an-automated-machine-learning-run"></a>Avbryt en automatiserad machine learning-körning

Starta om ditt Azure Databricks-kluster för att avbryta en körning och starta ett nytt experiment som körs, när du använder automatisk maskininlärningsfunktioner på Azure Databricks.

### <a name="10-iterations-for-automated-machine-learning"></a>> 10 iterationer för automatiserade machine learning

I automatiserade machine learning-inställningar, om du har fler än 10 iterationer, ange `show_output` till `False` när du skickar körningen.

### <a name="widget-for-the-azure-machine-learning-sdkautomated-machine-learning"></a>Widget för Azure Machine Learning SDK/automatiserad machine learning

Azure Machine Learning SDK-widgeten stöds inte i en Databricks notebook eftersom de bärbara datorerna inte kan tolka HTML widgetar. Du kan visa widgeten på portalen med hjälp av den här Python-koden i din Azure Databricks notebook-cellen:

```
displayHTML("<a href={} target='_blank'>Azure Portal: {}</a>".format(local_run.get_portal_url(), local_run.id))
```

### <a name="import-error-no-module-named-pandascoreindexes"></a>Fel vid import: Ingen modul med namnet ”pandas.core.indexes”

Om du ser det här felet automatisk när du använder maskininlärning:

1. Kör följande kommando för att installera två paket i Azure Databricks-klustret: 

   ```
   scikit-learn==0.19.1
   pandas==0.22.0
   ```

1. Koppla från och Återanslut klustret för att din bärbara dator. 

Om det inte går att lösa problemet, försök att starta om klustret.

## <a name="azure-portal"></a>Azure Portal

Om du går direkt för att visa din arbetsyta från en delningslänk från SDK: N eller portalen kan du inte visa normala översikt översiktssidan med prenumerationsinformation i tillägget. Du kommer inte heller att kunna växla till en annan arbetsyta. Om du vill visa en annan arbetsyta lösningen är att gå direkt till den [Azure-portalen](https://portal.azure.com) och Sök efter namnet på arbetsytan.

## <a name="diagnostic-logs"></a>Diagnostikloggar

Ibland kan det vara bra om du kan ange diagnostisk information när du frågar om du behöver hjälp. Vissa loggar finns [Azure-portalen](https://portal.azure.com) och gå till arbetsytan och välj **Arbetsyta > Experiment > Kör > loggar**.

## <a name="resource-quotas"></a>Resurskvoter

Lär dig mer om den [resurskvoter](how-to-manage-quotas.md) som kan uppstå när du arbetar med Azure Machine Learning.

## <a name="authentication-errors"></a>Autentiseringsfel

Om du gör en management-åtgärd i ett beräkningsmål från en fjärransluten jobb, visas något av följande fel:

```json
{"code":"Unauthorized","statusCode":401,"message":"Unauthorized","details":[{"code":"InvalidOrExpiredToken","message":"The request token was either invalid or expired. Please try again with a valid token."}]}
```

```json
{"error":{"code":"AuthenticationFailed","message":"Authentication failed."}}
```

Exempel: du får ett fel om du försöker skapa eller koppla en beräkningsmål från en ML-Pipeline som skickas för fjärrkörning.
