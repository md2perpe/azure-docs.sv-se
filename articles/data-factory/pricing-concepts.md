---
title: Förstå Azure Data Factory priser med exemplen | Microsoft Docs
description: Den här artikeln förklarar och visar Azure Data Factory prissättningsmodellen med detaljerade exempel
documentationcenter: ''
author: shlo
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/25/2018
ms.author: shlo
ms.openlocfilehash: a825982532047f6e311c5508394df243310f02ab
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65233921"
---
# <a name="understanding-data-factory-pricing-through-examples"></a>Förstå Data Factory priser med exemplen

Den här artikeln förklarar och visar Azure Data Factory prissättningsmodellen med detaljerade exempel.

> [!NOTE]
> De priser som används i exemplen nedan är hypotetiska och är inte avsedda för en faktiska priser.

## <a name="copy-data-from-aws-s3-to-azure-blob-storage-hourly"></a>Kopiera data från AWS S3 till Azure Blob storage per timme

I det här scenariot som du vill kopiera data från AWS S3 till Azure Blob storage på ett timschema.

För att åstadkomma scenariot måste du skapa en pipeline med följande objekt:

1. En Kopieringsaktivitet med en indatauppsättning för data som ska kopieras från AWS S3.

2. Utdatauppsättning för data i Azure Storage.

3. En schemautlösare för att köra pipelinen varje timme.

   ![Scenario1](media/pricing-concepts/scenario1.png)

| **Åtgärder** | **Typer och enheter** |
| --- | --- |
| Skapa länkad tjänst | 2 Läs/Skriv-entitet  |
| Skapa datauppsättningar | 4 Läs/Skriv-entiteter (2 för att skapa en datauppsättning, 2 för den länkade tjänsten refererar till) |
| Skapa Pipeline | 3 Läs/Skriv-entiteter (1 för att skapa en pipeline, 2 för datauppsättningen referenser) |
| Get Pipeline | 1 Läs/Skriv-entitet |
| Kör Pipeline | 2 aktivitetskörningar (1 för utlösare som körs, 1 för aktivitetskörningar) |
| Kopiera Data antagandet: körningstid = 10: e minut | 10 \* 4 azure Integration Runtime (standardinställning DIU = 4) finns mer information om integrering enheter och optimera kopieringen bättre prestanda i [i den här artikeln](copy-activity-performance.md) |
| Övervaka Pipeline antagandet: Endast 1 kör uppstod | 2 övervakning kör poster igen (1 för pipelinekörning, 1 för aktivitetskörning) |

**Totalt antal scenariot priser: $0.16811**

- Data Factory Operations = **$0,0001**
  - Läs/Skriv = 10\*00001 = $0,0001 [1 R/W = $ 0,50 USD/50000 = 0,00001]
  - Övervaka = 2\*000005 = $0,00001 [1 övervakning = $0.25/50000 = 0.000005]
- Pipeline-dirigering &amp; körning = **$0.168**
  - Aktivitetskörningar = 001\*2 = 0,002 [1 kör = $1/1 000 = 0,001]
  - Dataförflyttningsaktiviteter = $0.166 (linjärt i 10 minuter körningstid. $0.25/timme på Azure Integration Runtime)

## <a name="copy-data-and-transform-with-azure-databricks-hourly"></a>Kopiera data och transformera med Azure Databricks per timme

I det här scenariot som du vill kopiera data från AWS S3 till Azure Blob storage och transformera data med Azure Databricks på ett timschema.

För att åstadkomma scenariot måste du skapa en pipeline med följande objekt:

1. En Kopieringsaktivitet med en indatauppsättning för data som ska kopieras från AWS S3 och en utdatauppsättning för data i Azure storage.
2. En Azure Databricks-aktivitet för transformering av data.
3. En schemautlösare för att köra pipelinen varje timme.

![Scenario2](media/pricing-concepts/scenario2.png)

| **Åtgärder** | **Typer och enheter** |
| --- | --- |
| Skapa länkad tjänst | 3 Läs/Skriv-entitet  |
| Skapa datauppsättningar | 4 Läs/Skriv-entiteter (2 för att skapa en datauppsättning, 2 för den länkade tjänsten refererar till) |
| Skapa Pipeline | 3 Läs/Skriv-entiteter (1 för att skapa en pipeline, 2 för datauppsättningen referenser) |
| Get Pipeline | 1 Läs/Skriv-entitet |
| Kör Pipeline | 3 aktivitetskörningar (1 för utlösarkörningen, 2 för aktivitetskörningar) |
| Kopiera Data antagandet: körningstid = 10: e minut | 10 \* 4 azure Integration Runtime (standardinställning DIU = 4) finns mer information om integrering enheter och optimera kopieringen bättre prestanda i [i den här artikeln](copy-activity-performance.md) |
| Övervaka Pipeline antagandet: Endast 1 kör uppstod | 3 övervakning kör poster igen (1 för pipelinekörning, 2 för aktivitetskörning) |
| Kör Databricks-aktivitet antagandet: körningstid = 10: e minut | 10: e minut externa aktivitet Pipelinekörning |

**Total Scenario pricing: $0.16916**

- Data Factory Operations = **$0.00012**
  - Läs/Skriv = 11\*00001 = $0.00011 [1 R/W = $ 0,50 USD/50000 = 0,00001]
  - Övervaka = 3\*000005 = $0,00001 [1 övervakning = $0.25/50000 = 0.000005]
- Pipeline-dirigering &amp; körning = **$0.16904**
  - Aktivitetskörningar = 001\*3 = 0,003 [1 kör = $1/1 000 = 0,001]
  - Dataförflyttningsaktiviteter = $0.166 (linjärt i 10 minuter körningstid. $0.25/timme på Azure Integration Runtime)
  - Externa Pipelineaktivitet = $0.000041 (linjärt i 10 minuter körningstid. $0.00025/timme på Azure Integration Runtime)

## <a name="copy-data-and-transform-with-dynamic-parameters-hourly"></a>Kopiera data och transformera med dynamiska parametrar per timme

I det här scenariot kan du kopiera data från AWS S3 till Azure Blob storage och transformering med Azure Databricks (med dynamiska parametrar i skriptet) på ett timschema.

För att åstadkomma scenariot måste du skapa en pipeline med följande objekt:

1. En Kopieringsaktivitet med en indatauppsättning för data som ska kopieras från AWS S3, en utdatauppsättning för data i Azure storage.
2. En Lookup-aktiviteten för att skicka parametrar dynamiskt till skriptet omvandling.
3. En Azure Databricks-aktivitet för transformering av data.
4. En schemautlösare för att köra pipelinen varje timme.

![Scenario3](media/pricing-concepts/scenario3.png)

| **Åtgärder** | **Typer och enheter** |
| --- | --- |
| Skapa länkad tjänst | 3 Läs/Skriv-entitet  |
| Skapa datauppsättningar | 4 Läs/Skriv-entiteter (2 för att skapa en datauppsättning, 2 för den länkade tjänsten refererar till) |
| Skapa Pipeline | 3 Läs/Skriv-entiteter (1 för att skapa en pipeline, 2 för datauppsättningen referenser) |
| Get Pipeline | 1 Läs/Skriv-entitet |
| Kör Pipeline | 4 aktivitetskörningar (1 för utlösare som körs, 3 för aktivitetskörningar) |
| Kopiera Data antagandet: körningstid = 10: e minut | 10 \* 4 azure Integration Runtime (standardinställning DIU = 4) finns mer information om integrering enheter och optimera kopieringen bättre prestanda i [i den här artikeln](copy-activity-performance.md) |
| Övervaka Pipeline antagandet: Endast 1 kör uppstod | 4 övervakning kör poster igen (1 för pipelinekörning, 3 för aktivitetskörning) |
| Köra Lookup-aktiviteten antagandet: körningstid = 1 min | 1 min Pipelineaktivitet körning |
| Kör Databricks-aktivitet antagandet: körningstid = 10: e minut | 10: e minut externa Pipelineaktivitet körning |

**Totalt antal scenariot priser: $0.17020**

- Data Factory Operations = **$0.00013**
  - Läs/Skriv = 11\*00001 = $0.00011 [1 R/W = $ 0,50 USD/50000 = 0,00001]
  - Övervaka = 4\*000005 = $0.00002 [1 övervakning = $0.25/50000 = 0.000005]
- Pipeline-dirigering &amp; körning = **$0.17007**
  - Aktivitetskörningar = 001\*4 = 0.004 [1 kör = $1/1 000 = 0,001]
  - Dataförflyttningsaktiviteter = $0.166 (linjärt i 10 minuter körningstid. $0.25/timme på Azure Integration Runtime)
  - Pipeline-aktiviteten = $0.00003 (linjärt för 1 minut körningstid. $ 0,002/timme på Azure Integration Runtime)
  - Externa Pipelineaktivitet = $0.000041 (linjärt i 10 minuter körningstid. $0.00025/timme på Azure Integration Runtime)

## <a name="using-mapping-data-flow-debug-for-a-normal-workday-preview-pricing"></a>Om du använder mappning data flödet för en normal arbetsdag (pris för förhandsversion)

Du är ansvarig för att utforma, bygga och testa mappning Data flödar varje dag som en Data-tekniker. Du kan logga in på den ADF UI på morgonen och aktivera felsökningsläget för Data flödar. Standard-TTL för Debug sessioner är 60 minuter. Du arbeta under dagen i 10 timmar, så din felsökningssessionen upphör aldrig att gälla. Därför blir din kostnad för dagen:

**10 (timmar) x 8 (kärnor) x $0.112 = $8.96**

## <a name="transform-data-in-blob-store-with-mapping-data-flows-preview-pricing"></a>Transformera data i blob store med att mappa dataflöden (pris för förhandsversion)

I det här scenariot som du vill omvandla data i Blob Store visuellt i ADF mappning Data flödar på ett timschema.

För att åstadkomma scenariot måste du skapa en pipeline med följande objekt:

1. Aktiviteten dataflöde med omvandling logiken.

2. Indatauppsättning för data i Azure Storage.

3. Utdatauppsättning för data i Azure Storage.

4. En schemautlösare för att köra pipelinen varje timme.

| **Åtgärder** | **Typer och enheter** |
| --- | --- |
| Skapa länkad tjänst | 2 Läs/Skriv-entitet  |
| Skapa datauppsättningar | 4 Läs/Skriv-entiteter (2 för att skapa en datauppsättning, 2 för den länkade tjänsten refererar till) |
| Skapa Pipeline | 3 Läs/Skriv-entiteter (1 för att skapa en pipeline, 2 för datauppsättningen referenser) |
| Get Pipeline | 1 Läs/Skriv-entitet |
| Kör Pipeline | 2 aktivitetskörningar (1 för utlösare som körs, 1 för aktivitetskörningar) |
| Körningstid för data Flow antaganden: = 10 min + 10: e minut TTL | 10 \* 8 kärnor för allmän beräkning med TTL-värde på 10 |
| Övervaka Pipeline antagandet: Endast 1 kör uppstod | 2 övervakning kör poster igen (1 för pipelinekörning, 1 för aktivitetskörning) |

**Totalt antal scenariot priser: $0.3011**

- Data Factory Operations = **$0,0001**
  - Läs/Skriv = 10\*00001 = $0,0001 [1 R/W = $ 0,50 USD/50000 = 0,00001]
  - Övervaka = 2\*000005 = $0,00001 [1 övervakning = $0.25/50000 = 0.000005]
- Pipeline-dirigering &amp; körning = **$0.301**
  - Aktivitetskörningar = 001\*2 = 0,002 [1 kör = $1/1 000 = 0,001]
  - Data flödar aktiviteter = $0.299 linjärt för 20 minuter (10 minuter körningstid + 10 minuter TTL). $0.112/ timme på Azure Integration Runtime med 8 kärnor general compute

## <a name="next-steps"></a>Nästa steg

Nu när du förstår priser för Azure Data Factory kan du börja!

- [Skapa en datafabrik med hjälp av Azure Data Factory-användargränssnitt](quickstart-create-data-factory-portal.md)

- [Introduktion till Azure Data Factory](introduction.md)

- [Visuell redigering i Azure Data Factory](author-visually.md)
