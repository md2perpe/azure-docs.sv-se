---
title: Dataflöde för Azure Data Factory mappning optimera fliken
description: Optimera Azure Data Factory mappning Data flödar med hjälp av fliken optimera med partitionsinställningar
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/31/2019
ms.openlocfilehash: 40023931b2a0b3788a583a5b5240e7916b187e34
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67190649"
---
# <a name="mapping-data-flow-transformation-optimize-tab"></a>Mappa data flow omvandling optimera fliken

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Varje dataflöde transformering har en ””-optimeringsfliken. Fliken optimize innehåller valfria inställningar om du vill konfigurera partitioneringsscheman för dataflöden.

<img src="media/data-flow/opt001.png" width="800">

Standardinställningen är ”Använd aktuella partitionering”. Aktuella partitionering instruerar Azure Data Factory ska kunna använda partitioneringsschemat inbyggd Data flödar som körs på Spark i Azure Databricks. I allmänhet är det här den rekommenderade metoden.

Det finns dock instanser där kan du justera partitionering. Exempelvis om du vill spara dina transformationer till en enskild fil i sjön väljer ”enskild partition” på fliken Optimera för partitionering i transformeringen mottagare.

Ett annat fall där du kanske vill kontroll över de partitioneringsscheman som används för din Datatransformationer är i termer av prestanda. Justera partitionering av data ger en nivå av kontroll över distributionen av dina data över compute-noder och data ort optimeringar som kan såväl positivt som negativt prestanda påverkas av din övergripande flöde.

Om du vill ändra partitionering på en transformering, klicka på fliken Optimize och markerar du knappen ”Ställ in partitionering”. Sedan visas med ett antal alternativ för partitionering. Den bästa metoden för partitionering för att implementera varierar beroende på datavolymer, kandidatnycklar, null-värden och kardinalitet. Det är bra att börja med standard partitionering och försök sedan de olika alternativen för partitionering. Du kan testa med de kör i Pipeline och sedan visa den tid som krävs i varje transformering gruppering samt partition användning från vyn övervakning.

<img src="media/data-flow/opt002.png" width="600">

### <a name="round-robin"></a>Resursallokering

Resursallokering är enkel partition som automatiskt distribuerar data jämnt över partitioner. Använd resursallokering när du inte har bra viktiga kandidater för att implementera en stabil, smart partitioneringsstrategi. Du kan ange antalet fysiska partitioner.

### <a name="hash"></a>Hash

Azure Data Factory skapas en hash av kolumner för att skapa enhetliga partitioner så att rader med liknande värden ska ligga i samma partition. När du använder Hash-alternativet testa för möjliga partition skeva. Du kan ange antalet fysiska partitioner.

### <a name="dynamic-range"></a>Dynamiskt intervall

Dynamiskt intervall använder Spark dynamisk adressintervallen baserat på kolumner eller uttryck som du anger. Du kan ange antalet fysiska partitioner. 

### <a name="fixed-range"></a>Fast intervall

Du måste skapa ett uttryck som ger ett fast intervall för värden i dina partitionerade data-kolumner. Du bör ha en god förståelse av dina data innan du använder det här alternativet för att undvika partition skeva. De värden som du anger för att uttrycket ska användas som en del av en partitionsfunktion. Du kan ange antalet fysiska partitioner.

### <a name="key"></a>Nyckel

Om du har en god förståelse av dina data kardinalitet kan viktiga partitionering vara en bra partition-strategi. Viktiga partitionering skapar partitioner för varje unikt värde i kolumnen. Du kan inte ange antalet partitioner eftersom antalet baseras på unika värden i data.

## <a name="next-steps"></a>Nästa steg

[Mappning av dataflöde prestandaguide](concepts-data-flow-performance.md)
[dataflöde övervakning](concepts-data-flow-monitoring.md)
