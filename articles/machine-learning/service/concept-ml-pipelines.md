---
title: 'Pipelines: Optimera machine learning-arbetsflöden'
titleSuffix: Azure Machine Learning service
description: Läs mer om de machine learning pipelines som du kan skapa med Azure Machine Learning-SDK för Python och fördelarna med att använda pipelines i den här artikeln. Machine learning (ML) pipelines som används av datatekniker att bygga, optimera och hantera sina arbetsflöden för maskininlärning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: sanpil
author: sanpil
ms.date: 05/14/2019
ms.custom: seodec18
ms.openlocfilehash: f49b384f6f943e8c6767a6133a835011bc1e6bac
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67059322"
---
# <a name="build-reusable-ml-pipelines-in-azure-machine-learning-service"></a>Skapa återanvändbara ML-pipeliner i Azure Machine Learning-tjänsten

Läs mer om de machine learning pipelines som du kan skapa med Azure Machine Learning-SDK för Python och fördelarna med att använda pipelines i den här artikeln.

## <a name="what-are-machine-learning-pipelines"></a>Vad är machine learning pipelines?

Med machine learning (ML) pipelines, dataexperter, datatekniker och IT-proffs kan samarbeta med stegen som ingår i:
+ Förberedelse av data, till exempel normalizations och omvandlingar
+ Modellträning
+ Modell-utvärdering
+ Distribution 

I följande diagram visas en exempel-pipeline:

![Machine learning-pipelines i Azure Machine Learning-tjänsten](./media/concept-ml-pipelines/pipelines.png)

<a name="compare"></a>
### <a name="which-azure-pipeline-technology-should-i-use"></a>Vilken Azure pipeline-teknik ska jag använda?

Azure-molnet tillhandahåller flera andra pipelines, var och en med ett annat syfte. I följande tabell visas olika pipelines och hur de används för:

| Pipeline | Vad läget gör | Canonical pipe |
| ---- | ---- | ---- |
| Azure Machine Learning pipelines | Definierar återanvändbara machine learning-arbetsflöden som kan användas som en mall för din machine learning-scenarier. | Data -> modell |
| [Azure Data Factory-pipelines](https://docs.microsoft.com/azure/data-factory/concepts-pipelines-activities) | Grupper dataförflyttning och transformation kontrollaktiviteter behövs för att utföra en uppgift.  | Data -> data |
| [Azure pipelines](https://azure.microsoft.com/services/devops/pipelines/) | Kontinuerlig integrering och leverans av ditt program till någon moln plattform för alla  | Kod -> appens/tjänstens |

## <a name="why-build-pipelines-with-azure-machine-learning"></a>Varför ska man skapa pipelines med Azure Machine Learning?

Du kan använda den [Azure Machine Learning-SDK för Python](#the-python-sdk-for-pipelines) att skapa ML pipelines, samt att skicka och spåra individuella pipelinekörningar.

Du kan optimera ditt arbetsflöde med enkelhet, hastighet, portabilitet och återanvändning med pipelines. När du skapar pipelines med Azure Machine Learning kan du fokusera på din expertis, maskininlärning, istället för på infrastruktur.

Med hjälp av steg gör det möjligt att köra bara de steg som du behöver, när du justera och testa ditt arbetsflöde. Ett steg är en beräkningsenhet i pipelinen. I det föregående diagrammet visas uppgiften att förbereda data kan omfatta många steg. De här stegen omfattar, men inte begränsat till, normalisering, transformation, verifiering och funktionalisering. Datakällor och mellanliggande data som återanvänds i pipeline, vilket sparar compute tid och resurser. 

När är utformat för pipelinen, är det ofta mer finjustera runt loopen utbildning för pipelinen. När du kör en pipeline, kör hoppar till de steg som måste köras igen, till exempel en uppdaterad inlärningsskript och hoppar över vad som inte har ändrats. Samma paradigmet gäller oförändrade skript som används för körningen av steget. 

Med Azure Machine Learning kan du använda olika verktyg och ramverk, till exempel PyTorch eller TensorFlow, för varje steg i din pipeline. Azure koordinater mellan de olika [beräkningsmål](concept-azure-machine-learning-architecture.md) du använder, så att dina mellanliggande data kan delas med de underordnade beräkningsmål enkelt. 

Du kan [spåra mått för din pipeline-experiment](https://docs.microsoft.com/azure/machine-learning/service/how-to-track-experiments) direkt i Azure-portalen. 

## <a name="key-advantages"></a>Viktiga fördelar

Viktiga fördelar för att skapa pipelines för din machine learning-arbetsflöden finns:

|Stor fördel|Beskrivning|
|:-------:|-----------|
|**Obevakad&nbsp;körs**|Schemalägga några steg för att köra parallellt eller i följd i en tillförlitlig och obevakat sätt. Eftersom dataförberedelser och modellering kan senaste dagar eller veckor, du kan nu fokusera på andra uppgifter medan din pipeline körs. |
|**Blandade och skilda beräkning**|Använda flera pipelines som på ett tillförlitligt sätt samordnas i heterogena och skalbar beräkningar och lagringsutrymmen. Du kan köra enskilda pipeline-stegen på olika beräkningsmål, till exempel HDInsight, GPU Data Science VM och Databricks. På så sätt blir effektiv användning av tillgängliga beräkningsalternativ.|
|**Återanvändningsmöjligheter**|Du kan mallanpassa pipelines för specifika scenarier, till exempel träna och batchbedömning. Aktivera dem från externa system via enkla REST-anrop.|
|**Spårning och versionshantering**|I stället för att manuellt spåra data och resultatet sökvägar som du iterera använda pipelines SDK att uttryckligen ge ett namn och version dina data datakällor, indata och utdata. Du kan också hantera skript och data separat för ökad produktivitet.|

## <a name="the-python-sdk-for-pipelines"></a>Python SDK för pipelines

Använda Python för att skapa ML-pipelines. SDK: N för Azure Machine Learning erbjuder tvingande konstruktioner för ordningsföljd och parallellisera stegen i dina pipelines när inget databeroende finns. Du kan interagera med den i Jupyter-anteckningsböcker eller i en annan önskad integrerad utvecklingsmiljö. 

Du kan med hjälp av deklarativa databeroenden, för att optimera dina uppgifter. SDK innehåller ett ramverk med färdiga moduler för vanliga åtgärder, till exempel överföring av replikeringsdata och publicering av modellen. Du kan utöka ramverket för att modellera egna konventioner genom att implementera anpassade steg som är återanvändbara för pipelines. Du kan också hantera beräkningsmål och lagringsresurser direkt från SDK.

Du kan spara pipelines som en mall och distribuera dem till en REST-slutpunkt så att du kan schemalägga jobb i batch-bedömning eller omtränings.

Information om hur du skapar dina egna, se den [Python SDK referensdokument för pipelines](https://docs.microsoft.com/python/api/azureml-pipeline-core/?view=azure-ml-py) och anteckningsboken i nästa avsnitt.

## <a name="example-notebooks"></a>Exempel-anteckningsböcker
 
Följande anteckningsböcker demonstrera pipelines med Azure Machine Learning: [how-to-use-azureml/machine-learning-pipelines](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines).
 
[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [skapa din första pipeline](how-to-create-your-first-pipeline.md).
