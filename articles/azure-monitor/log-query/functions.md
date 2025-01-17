---
title: Funktioner i Azure Monitor loggfrågor | Microsoft Docs
description: Den här artikeln beskriver hur du använder functions för att anropa en fråga från en annan loggfråga i Azure Monitor.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/15/2018
ms.author: bwren
ms.openlocfilehash: 4b3116230a085bfbb9a6139fbada4179d802bf5e
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2019
ms.locfileid: "67296075"
---
# <a name="using-functions-in-azure-monitor-log-queries"></a>Med hjälp av funktioner i Azure Monitor log-frågor

Om du vill använda en loggfråga med en annan fråga kan du spara den som en funktion. Det gör att du kan förenkla komplexa frågor genom att dela upp dem i delar och att du kan återanvända gemensam kod med flera frågor.

## <a name="create-a-function"></a>Skapa en funktion

Skapa en funktion med Log Analytics i Azure portal genom att klicka på **spara** och sedan tillhandahålla informationen i följande tabell.

| Inställning | Beskrivning |
|:---|:---|
| Namn           | Namn för frågan i **frågeutforskaren**. |
| Spara som        | Funktion |
| Funktionsalias | Kort namn att använda funktionen i andra frågor. Får inte innehålla blanksteg och måste vara unika. |
| Category       | En kategori för att organisera sparade frågor och funktioner i **frågeutforskaren**. |

> [!NOTE]
> En funktion i Azure Monitor får inte innehålla en annan funktion.

> [!NOTE]
> Det är möjligt i Azure Monitor log-frågor, men för närvarande inte för Application Insights frågor att spara en funktion.



## <a name="use-a-function"></a>Använda en funktion
Funktionen genom att inkludera dess alias i en annan fråga. Det kan användas som andra tabeller.

## <a name="example"></a>Exempel
Följande exempelfråga returnerar alla saknade säkerhetsuppdateringar som rapporteras i den sista dagen. Spara den här frågan som en funktion med alias _security_updates_last_day_. 

```Kusto
Update
| where TimeGenerated > ago(1d) 
| where Classification == "Security Updates" 
| where UpdateState == "Needed"
```

Skapa en annan fråga och referens i _security_updates_last_day_ att söka efter för SQL-relaterade nödvändiga säkerhetsuppdateringar.

```Kusto
security_updates_last_day | where Title contains "SQL"
```

## <a name="next-steps"></a>Nästa steg
Se andra lektioner för att skriva Azure Monitor log-frågor:

- [Strängåtgärder](string-operations.md)
- [Åtgärder för datum och tid](datetime-operations.md)
- [Aggregeringsfunktioner](aggregations.md)
- [Avancerade aggregeringar](advanced-aggregations.md)
- [JSON och datastrukturer](json-data-structures.md)
- [Kopplingar](joins.md)
- [Diagram](charts.md)
