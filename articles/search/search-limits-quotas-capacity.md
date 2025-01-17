---
title: 'Tjänstbegränsningar för nivåer och SKU: er – Azure Search'
description: Tjänstbegränsningar som används för kapacitetsplanering och övre gräns för begäranden och svar för Azure Search.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: b6940be7f64aa9ae16258fa936d197e2715235ab
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/01/2019
ms.locfileid: "67485403"
---
# <a name="service-limits-in-azure-search"></a>Tjänstbegränsningar i Azure Search
Maximalt begränsar på lagring, arbetsbelastningar och kvantiteter av index, dokument, och andra objekt är beroende av om du [etablera Azure Search](search-create-service-portal.md) på **kostnadsfri**, **grundläggande**,  **Standard**, eller **Lagringsoptimerade** prisnivåer.

+ **Kostnadsfria** är en delad tjänst för flera innehavare som medföljer din Azure-prenumeration.

+ **Grundläggande** tillhandahåller dedikerade resurser för produktionsarbetsbelastningar i mindre skala.

+ **Standard** körs på dedikerade virtuella datorer med mer lagring och bearbetning av kapacitet på alla nivåer. Standard finns i fyra nivåer: S1, S2, S3 och S3 HD.

+ **Optimerad lagring** körs på dedikerade virtuella datorer med mer totalt lagringsutrymme, lagring, bandbredd och minne än **Standard**. Optimerad lagring finns i två nivåer: L1 och L2

> [!NOTE]
> Alla nivåer är allmänt tillgängliga, inklusive Lagringsoptimerade-nivå från och med den 1 juli. Alla priser finns på den [prisinformation om](https://azure.microsoft.com/pricing/details/search/) sidan.

  S3 High Density (S3 HD) är utformat för specifika arbetsbelastningar: [multitenans](search-modeling-multitenant-saas-applications.md) och stora mängder små index (en miljon dokument per index, tre tusen index per tjänst). Den här nivån ger inte den [indexeraren funktionen](search-indexer-overview.md). På S3 HD måste datainmatning använda push-metoden, med hjälp av API-anrop till skicka data från källa till index. 

> [!NOTE]
> En tjänst har etablerats på en specifik nivå. Hoppa nivåer för att få kapacitet innebär att etablera en ny tjänst (det finns ingen uppgradering på plats). Mer information finns i [väljer en SKU eller nivå](search-sku-tier.md). Mer information om hur du justerar kapacitet inom en tjänst som du redan har etablerat finns [skala resursnivåer för fråge- och indexeringsarbetsbelastningar](search-capacity-planning.md).
>

## <a name="subscription-limits"></a>Prenumerationsgränser
[!INCLUDE [azure-search-limits-per-subscription](../../includes/azure-search-limits-per-subscription.md)]

## <a name="storage-limits"></a>Lagringsgränser
[!INCLUDE [azure-search-limits-per-service](../../includes/azure-search-limits-per-service.md)]

<a name="index-limits"></a>

## <a name="index-limits"></a>Index gränser

| Resource | Lediga | Basic&nbsp;<sup>1</sup>  | S1 | S2 | S3 | S3&nbsp;HD | L1 | L2 |
| -------- | ---- | ------------------- | --- | --- | --- | --- | --- | --- |
| Maximalt antal index |3 |5 eller 15 |50 |200 |200 |1 000 per partition eller 3 000 per tjänst |10 |10 |
| Maximal enkel fält per index |1000 |100 |1000 |1000 |1000 |1000 |1000 |1000 |
| Maximal komplex samling fält per index |40 |40 |40 |40 |40 |40 |40 |40 |
| Maximal element för alla komplexa samlingar per dokument |3000 |3000 |3000 |3000 |3000 |3000 |3000 |3000 |
| Maximalt djup för komplexa fält |10 |10 |10 |10 |10 |10 |10 |10 |
| Maximal [förslagsställare](https://docs.microsoft.com/rest/api/searchservice/suggesters) per index |1 |1 |1 |1 |1 |1 |1 |1 |
| Maximal [poängprofiler](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index) per index |100 |100 |100 |100 |100 |100 |100 |100 |
| Maximal funktioner per profil |8 |8 |8 |8 |8 |8 |8 |8 |

<sup>1</sup> bastjänster som skapats före December 2017 har lägre gränser (5 i stället för 15) på index. Nivån Basic är endast SKU: N med en lägre gräns på 100 fält per index.

<a name="document-limits"></a>

## <a name="document-limits"></a>Dokumentet gränser 

Från och med oktober 2018 finns det inte längre några begränsningar för dokument på alla nya tjänster som skapats på alla faktureringsbara nivå (Basic, S1, S2, S3, S3 HD) i alla regioner. Även om de flesta regioner har haft ett obegränsat antal dokumentantal sedan November/December 2017, fanns det fem regioner som har fortsatt att införa begränsningar för dokumentet. Beroende på när och var du skapat en söktjänst, kan du köra en tjänst som kan fortfarande komma att dokumentet gränser.

Kontrollera panelen Usage i översiktssidan för din tjänst för att avgöra om tjänsten har gränser för dokumentet. Dokumentera antal är antingen Obegränsat eller omfattas av en gräns baserat på nivå.

  ![Användningsikonen](media/search-limits-quotas-capacity/portal-usage-tile.png)

### <a name="regions-previously-having-document-limits"></a>Regioner som tidigare har dokumentet gränser

Om portalen meddelar en dokumentgräns, din tjänst skapades antingen före sent 2017, eller den skapades på ett datacenter med lägre kapacitet kluster som värd för Azure Search-tjänsterna:

+ Östra Australien
+ Östasien
+ Indien, centrala
+ Västra Japan
+ Västra centrala USA

Följande maximala begränsningar gäller för tjänster som omfattas av dokumentet begränsningar:

|  Lediga | Basic | S1 | S2 | S3 | S3&nbsp;HD |
|-------|-------|----|----|----|-------|
|  10 000 |1&nbsp;miljoner |15 miljoner per partition eller 180 miljoner per tjänst |60 miljoner per partition eller 720 miljoner per tjänst |120 miljoner per partition eller 1,4 miljarder per tjänst |1 miljon per index eller 200 miljoner per partition |

Om din tjänst har begränsningar som blockerar du kan skapa en ny tjänst och sedan publicera allt innehåll till den tjänsten. Det finns ingen mekanism för sömlöst reprovisioning din tjänst till ny maskinvara i bakgrunden.

> [!Note] 
> 200 miljoner dokument per partition har tagits bort men 1 miljon dokument per index gränsen förblir för S3 High Density-tjänster som har skapats efter sent 2017.


### <a name="document-size-limits-per-api-call"></a>Dokumentera storleksgränser per API-anrop

Den maximala dokumentstorleken när du anropar en API-Index är cirka 16 megabyte.

Dokumentet är faktiskt en gräns för storleken på indexet API-begärandetexten. Eftersom du kan skicka en batch med flera dokument i indexet API: et på samma gång, beror normalt storleksgränsen på hur många dokument är i batchen. För en grupp med ett enda dokument är den maximala dokumentstorleken 16 MB JSON.

Kom ihåg att undanta inte kan frågas data från begäran för att hålla dokumentstorlek nere. Bilder och andra binära data är inte direkt frågningsbart och bör inte lagras i indexet. Om du vill integrera inte kan frågas data i sökresultat, definierar du en icke-sökbart fält som lagrar en URL: en referens till resursen.

## <a name="indexer-limits"></a>Indexeraren gränser

Maximalt tillåtna antalet gånger som körs finns för att ge belastningsutjämning och stabilitet till tjänsten som helhet, men större datauppsättningar kan behöva mer indexering tid än maximalt tillåter. Om ett indexeringsjobb inte kan slutföras inom den maximala tiden som tillåts, kan du försöka köra det enligt ett schema. Scheduler håller reda på status för indexering. Om en schemalagd indexeringsjobb avbryts av någon anledning, kan indexeraren ta vid där det senast slutade vid nästa schemalagda körning.


| Resource | Kostnadsfria&nbsp;<sup>1</sup> | Basic&nbsp;<sup>2</sup>| S1 | S2 | S3 | S3&nbsp;HD&nbsp;<sup>3</sup>|L1 |L2 |
| -------- | ----------------- | ----------------- | --- | --- | --- | --- | --- | --- |
| Maximalt antal indexerare |3 |5 eller 15|50 |200 |200 |Gäller inte |10 |10 |
| Maximalt antal datakällor |3 |5 eller 15 |50 |200 |200 |Gäller inte |10 |10 |
| Maximal kompetens <sup>4</sup> |3 |5 eller 15 |50 |200 |200 |Gäller inte |10 |10 |
| Maximala indexering belastningen per anrop |10 000 dokument |Begränsas bara av maximalt antal dokument |Begränsas bara av maximalt antal dokument |Begränsas bara av maximalt antal dokument |Begränsas bara av maximalt antal dokument |Gäller inte |Obegränsad |Obegränsad |
| Minsta schema | 5 minuter |5 minuter |5 minuter |5 minuter |5 minuter |5 minuter |5 minuter | 5 minuter |
| Maximal körtid <sup>5</sup> | 1 – 3 minuter |24 timmar |24 timmar |24 timmar |24 timmar |Gäller inte  |24 timmar |24 timmar |
| Maximal körtid för kognitiv sökning kompetens eller blob-indexering med bildanalys <sup>5</sup> | 3 – 10 minuter |2 timmar |2 timmar |2 timmar |2 timmar |Gäller inte  |2 timmar |2 timmar |
| BLOB-indexeraren: storlek för maximal blob, MB |16 |16 |128 |256 |256 |Gäller inte  |256 |256 |
| BLOB-indexeraren: maximala antalet tecken innehåll extraheras från en blob |32,000 |64,000 |4&nbsp;miljoner |4&nbsp;miljoner |4&nbsp;miljoner |Gäller inte |4&nbsp;miljoner |4&nbsp;miljoner |

<sup>1</sup> kostnadsfria tjänster har indexeraren maximal körningstid för 3 minuter för blob-källor och 1 minut för alla andra datakällor. För AI indexering att anrop till Cognitive Services, är kostnadsfria tjänster begränsad till 20 kostnadsfria transaktioner per dag, där en transaktion har definierats som ett dokument som har skickas via pipelinen berikande.

<sup>2</sup> bastjänster som skapats före December 2017 har lägre gränser (5 i stället för 15) på indexerare och datakällor kompetens.

<sup>3</sup> S3 HD omfattar inte stöd för indexerare.

<sup>4</sup> högst 30 färdigheter per kompetens.

<sup>5</sup> kognitiv sökning arbetsbelastningar och bildanalys i Azure blob-indexering har kortare körs gånger än vanlig textindexering. Bildanalys och bearbetning av naturligt språk är intensiv och förbruka oproportionerligt mängder tillgängliga processorkraft. Körtid har minskats till att ge möjlighet att köra ge andra jobb i kön.  

## <a name="queries-per-second-qps"></a>Frågor per sekund (QPS)

Frågor per sekund uppskattningar måste ha utvecklats oberoende av alla kunder. Indexstorlek och komplexitet, fråga storleken och komplexiteten och mängden trafik är primära faktorerna för frågor per sekund. Det går inte att erbjuda meningsfulla beräkningar när sådana faktorer är okänd.

Uppskattningar är mer förutsägbar i tjänster som körs på dedikerade resurser (nivåerna Basic och Standard). Du kan beräkna Indexlagring mer noggrant eftersom du har kontroll över flera parametrar. Anvisningar om hur du metoden uppskattning finns [Azure Search-prestanda och optimering](search-performance-optimization.md).

För Lagringsoptimerade-nivåer, bör du förväntar dig ett lägre frågedataflöde och högre svarstid än Standard-nivåerna.  Metoder för att uppskatta den frågeprestanda du får är samma som Standard-nivåerna.

## <a name="data-limits-cognitive-search"></a>Databegränsningar (kognitiv sökning)

En [kognitiv sökning pipeline](cognitive-search-concept-intro.md) som anropar en Text Analytics-resurs för [entitetsidentifiering](cognitive-search-skill-entity-recognition.md), [nyckeln diskussionsämne](cognitive-search-skill-keyphrases.md), [attitydanalys ](cognitive-search-skill-sentiment.md), och [språkidentifiering](cognitive-search-skill-language-detection.md) lyder under databegränsningar. Den maximala storleken för en post ska vara 50 000 tecken enligt `String.Length`. Om du vill dela upp dina data innan de skickas till sentiment analyzer, Använd den [Text dela färdighet](cognitive-search-skill-textsplit.md).

## <a name="api-request-limits"></a>API-gränser för begäran
* Högst 16 MB per begäran <sup>1</sup>
* Maximal URL-längd 8 KB
* Maximalt antal 1000 dokument per batch med index överför, sammanfogar eller tar bort
* Maximalt 32 fält i $orderby-satsen
* Maximal search termen storleken är 32 766 byte (32 KB minus 2 byte) med UTF-8-kodat text

<sup>1</sup> i Azure Search brödtexten i en begäran är föremål för en övre gräns på 16 MB, aktiviteternas en praktisk gräns på innehållet i enskilda fält eller samlingar som inte annars är begränsade av teoretisk gränser (se [data som stöds typer](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) för mer information om fältet sammansättning och begränsningar).

## <a name="api-response-limits"></a>API-svar-gränser
* Maximalt 1 000 dokument returneras per sida för sökresultat
* Maximalt 100 förslag som returneras per föreslår API-begäran

## <a name="api-key-limits"></a>Viktiga API-gränser
API-nycklar används för tjänst-autentisering. Det finns två typer. Admin-nycklar har angetts i rubriken och bevilja fullständig skrivskyddad åtkomst till tjänsten. Frågenycklar är skrivskyddad, anges på URL: en och distribueras vanligen till klientprogram.

* Högst 2 administratörsnycklar per tjänst
* Högst 50 frågenycklar per tjänst
