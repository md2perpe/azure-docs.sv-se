---
title: Data-verktyg för utforskning och visualisering – Azure | Microsoft Docs
description: Utforskning och visualisering Dataverktyg för den virtuella datorn för datavetenskap.
keywords: data science tools, data science virtual machine, tools for data science, linux data science
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/16/2018
ms.author: gokuma
ms.openlocfilehash: 693be80e493a0ba259d147f432dc9d6c07ba876d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66427527"
---
# <a name="data-exploration-and-visualization-tools-on-the-data-science-virtual-machine"></a>Utforskning och visualisering Dataverktyg på den virtuella datorn för datavetenskap

Ett viktigt steg i datavetenskap är att förstå data. Visualisering och datautforskningsverktygen påskyndar förståelse av data. Här är några verktyg som tillhandahålls på DSVM som gör det här viktiga steget enklare. 

## <a name="apache-drill"></a>Apache Drill
|    |           |
| ------------- | ------------- |
| Vad är det?   | SQL-frågemotor för öppen källkod på stordata    |
| Stöds DSVM-versioner      | Windows, Linux  |
| Hur är det konfigurerade / installerad på DSVM?      |  Installerade i `/dsvm/tools/drill*` i inbäddade läge   |
| Vanliga användningsområden      |  I situ datagranskning utan ETL. Fråga olika datakällor och inklusive CSV, JSON, tabeller, Hadoop-format     |
| Hur du använder / köra den?      | Genväg på skrivbordet  <br/> [Kom igång med ökad detaljnivå på 10 minuter](https://drill.apache.org/docs/drill-in-10-minutes/)  |
| Relaterade verktyg på DSVM      |   Rattle, Weka, SQL Server Management Studio      |

## <a name="weka"></a>Weka
|    |           |
| ------------- | ------------- |
| Vad är det?   |  Weka är en samling av machine learning-algoritmer för datautvinning uppgifter som rör. Algoritmerna kan antingen används direkt i en datauppsättning eller anropas från din egen Java-kod. Weka innehåller verktyg för förbearbetning av data, klassificering, regression, klustring, association regler och visualisering. |
| Stöds DSVM-versioner     | Windows, Linux     |
| Vanliga användningsområden      | Allmänna ML-verktyget     |
| Hur du använder / köra den?      | På Windows, söka efter Weka på Start-menyn. Logga in med X2Go på Linux, och gå till program -> utveckling Weka ->. |
| Innehåller länkar till exempel      | [Weka-exempel](https://www.cs.waikato.ac.nz/ml/weka/documentation.html) |
| Relaterade verktyg på DSVM      |LightGBM spännen, Xgboost   |

## <a name="rattle"></a>Spännen
|    |           |
| ------------- | ------------- |
| Vad är det?   |   Ett grafiskt användargränssnitt för datautvinning med R   |
| Stöds DSVM-versioner     | Windows, Linux     |
| Vanliga användningsområden      | Allmänna UI datautvinning-verktyget för R    |
| Hur du använder / köra den?      | UI-verktyget. På Windows, starta en kommandotolk genom att köra R, sedan i R kör `rattle()`. På Linux, Anslut med X2Go, starta en terminal, kör R, sedan i R kör `rattle()`. |
| Innehåller länkar till exempel      | [Spännen](https://togaware.com/onepager/) |
| Relaterade verktyg på DSVM      |LightGBM, Weka, Xgboost   |

## <a name="power-bi-desktop"></a>Power BI Desktop 
|    |           |
| ------------- | ------------- |
| Vad är det?   | Interaktiv datavisualisering och BI-verktyg    |
| Stöds DSVM-versioner      | Windows  |
| Vanliga användningsområden      |  Datavisualisering och skapa instrumentpaneler   |
| Hur du använder / köra den?      | Genväg på skrivbordet (`C:\Program Files\Microsoft Power BI Desktop\bin\PBIDesktop.exe`)      |
| Relaterade verktyg på DSVM      |   Visual Studio 2019, Visual Studio Code, Juno      |

