---
title: Hur du arbetar med sökresultat – Azure Search
description: Strukturera och sortera sökresultat, hämta ett dokumentantal och lägga till innehållsnavigering sökresultat i Azure Search.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.devlang: ''
ms.topic: conceptual
ms.date: 06/13/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 73f0dc98d7d2c3e7aa77f6414cbd58e58599eae7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67068834"
---
# <a name="how-to-work-with-search-results-in-azure-search"></a>Hur du arbetar med sökning resulterar i Azure Search
Den här artikeln innehåller råd om hur du implementerar standardelement på en sökresultatsida, till exempel totala antalet, hämta dokument, sorteringsordningar och navigering. Sidan-relaterade alternativ som bidrar data eller information till dina sökresultat anges via den [Dokumentsökningsoperationer](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) begäranden som skickas till din Azure Search-tjänst. 

Förfrågningar innehålla en GET-command, sökväg, och frågeparametrar som informerar tjänsten vad som efterfrågas och hur du formulera svaret i REST-API. I .NET-SDK motsvarande API: et är [DocumentSearchResult klass](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.documentsearchresult-1).

Kodexempel är ett webbgränssnitt för klientdelen som du hittar här: [New York City jobb demoappen](https://azjobsdemo.azurewebsites.net/) och [CognitiveSearchFrontEnd](https://github.com/LuisCabrer/CognitiveSearchFrontEnd).

> [!NOTE]
> En giltig begäran innehåller ett antal element, till exempel en tjänstens URL och en sökväg, HTTP-verb `api-version`och så vidare. Av utrymmesskäl trimmas vi exemplen för att fokusera på just den syntax som är relevant för sidbrytning. Mer information om syntax som begäran finns i [Azure Search Service REST](https://docs.microsoft.com/rest/api/searchservice).
>

## <a name="total-hits-and-page-counts"></a>Totalt antal träffar och Sidräkningar

Visar det totala antalet resultat som returnerats från en fråga och returnerar sedan dessa resultat i mindre segment är grundläggande för i stort sett alla söksidor.

![][1]

I Azure Search kan du använda den `$count`, `$top`, och `$skip` parametrar för att returnera dessa värden. I följande exempel visas en exempelförfrågan för totalt antal träffar i ett index som kallas ”-onlinekatalog”, returneras som `@odata.count`:

    GET /indexes/online-catalog/docs?$count=true

Hämta dokument i grupper med 15, och även visar totalt antal träffar, med början vid den första sidan:

    GET /indexes/online-catalog/docs?search=*$top=15&$skip=0&$count=true

Sidbrytning resultat kräver både `$top` och `$skip`, där `$top` anger hur många objekt för att returnera i batchen, och `$skip` anger hur många objekt att hoppa över. I följande exempel visar bredvid 15 objekt, var och en indikeras av inkrementell hoppar i den `$skip` parametern.

    GET /indexes/online-catalog/docs?search=*$top=15&$skip=0&$count=true

    GET /indexes/online-catalog/docs?search=*$top=15&$skip=15&$count=true

    GET /indexes/online-catalog/docs?search=*$top=15&$skip=30&$count=true

## <a name="layout"></a>Layout

På en sökresultatsida kanske du vill visa en miniatyrbild, en delmängd av fält och en länk till en fullständig produktsidan.

 ![][2]

I Azure Search kan du använda `$select` och en [Search API-begäran](https://docs.microsoft.com/rest/api/searchservice/search-documents) att implementera den här upplevelsen.

Att returnera en delmängd av fält för en sida vid sida layout:

    GET /indexes/online-catalog/docs?search=*&$select=productName,imageFile,description,price,rating

Bilder och mediefiler är inte direkt sökbart och ska lagras i en annan plattform för lagring, till exempel Azure Blob storage, för att minska kostnaderna. Definiera ett fält som lagrar den URL-adressen för externt innehåll i index och dokument. Du kan sedan använda fältet som ett referensen till avbildningen. URL-Adressen till avbildningen måste vara i dokumentet.

Att hämta en beskrivning av produktsidan för en **onClick** händelse, Använd [Lookup dokumentet](https://docs.microsoft.com/rest/api/searchservice/Lookup-Document) att skicka in nyckeln för dokumentet som ska hämtas. Datatypen för nyckeln är `Edm.String`. I det här exemplet är det *246810*.

    GET /indexes/online-catalog/docs/246810

## <a name="sort-by-relevance-rating-or-price"></a>Sortera efter relevans, klassificering eller pris

Sorteringsordningar ofta som standard relevans, men det är vanligt att göra alternativa sortera order lättillgängliga så att kunder kan snabbt blandar befintliga resultaten till en annan rangordnas ordning.

 ![][3]

I Azure Search sortering baseras på den `$orderby` uttryck för alla fält som indexeras som `"Sortable": true.` en `$orderby` -satsen är en OData-uttrycket. Information om syntaxen finns i [OData-uttryckssyntax för filter och order by-satser](query-odata-filter-orderby-syntax.md).

Relevans är associerad med poängprofiler. Du kan använda standard bedömning som förlitar sig på textanalys av och statistik för att rangordnas ordning alla resultat med högre poäng gå till dokument med mer eller starkare matchningar på en sökterm.

Alternativa sorteringsordningar är vanligtvis hör ihop med **onClick** händelser som anropa en metod som bygger sorteringsordning. Till exempel få det här elementet på sidan:

 ![][4]

Skapar du en metod som accepterar det valda sorteringsalternativet som indata och returnerar en sorterad lista för de kriterier som är associerade med det alternativet.

 ![][5]

> [!NOTE]
> Även standard poängsättningen räcker för många scenarier, rekommenderar vi basera relevans på en anpassad bedömningsprofilen i stället. En anpassad bedömningsprofilen ger dig ett sätt att öka objekt som är bättre för din verksamhet. Se [lägga till bedömningsprofiler](index-add-scoring-profiles.md) för mer information.
>

## <a name="faceted-navigation"></a>Aspektbaserad navigering

Söknavigering är vanligt på en resultatsida som ofta finns på sidan eller längst upp på en sida. I Azure Search ger aspektbaserad navigering självriktad sökning baserat på fördefinierade filter. Se [Aspektbaserad navigering i Azure Search](search-faceted-navigation.md) mer information.

## <a name="filters-at-the-page-level"></a>Filter på sidnivå

Om en lösningsdesign ingår dedicerade söksidor för specifika typer av innehåll (till exempel ett onlinebutiker program som har avdelningar som visas överst på sidan), kan du infoga ett [filteruttryck](search-filters.md) tillsammans med en **onClick** händelse att öppna en sida i förväg filtrerade tillstånd.

Du kan skicka ett filter med eller utan ett sökuttryck. Följande begäran kommer till exempel filtrera på varumärke, returnerar endast de dokument som matchar den.

    GET /indexes/online-catalog/docs?$filter=brandname eq 'Microsoft' and category eq 'Games'

Se [söka efter dokument (Azure Search-API)](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) för mer information om `$filter` uttryck.

## <a name="see-also"></a>Se även

- [Azure Search Service REST API](https://docs.microsoft.com/rest/api/searchservice)
- [Index-åtgärder](https://docs.microsoft.com/rest/api/searchservice/Index-operations)
- [Dokumentåtgärder](https://docs.microsoft.com/rest/api/searchservice/Document-operations)
- [Aspektbaserad navigering i Azure Search](search-faceted-navigation.md)

<!--Image references-->
[1]: ./media/search-pagination-page-layout/Pages-1-Viewing1ofNResults.PNG
[2]: ./media/search-pagination-page-layout/Pages-2-Tiled.PNG
[3]: ./media/search-pagination-page-layout/Pages-3-SortBy.png
[4]: ./media/search-pagination-page-layout/Pages-4-SortbyRelevance.png
[5]: ./media/search-pagination-page-layout/Pages-5-BuildSort.png
