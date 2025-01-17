---
title: 'Azure Cosmos DB: SQL .NET Core API, SDK och resurser'
description: Lär dig allt om SQL .NET Core API och SDK, inklusive frisläppningsdatum, dras tillbaka datum och ändringar som gjorts mellan varje version av Azure Cosmos DB .NET Core SDK.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 03/22/2018
ms.author: sngun
ms.openlocfilehash: 14350125f88c315a813eb64e3ce2b88dbec2aa24
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/24/2019
ms.locfileid: "67340202"
---
# <a name="azure-cosmos-db-net-core-sdk-for-sql-api-release-notes-and-resources"></a>Azure Cosmos DB .NET Core SDK för SQL-API: Viktig information och resurser
> [!div class="op_single_selector"]
> * [NET](sql-api-sdk-dotnet.md)
> * [.NET-Ändringsfeed](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Async Java](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [REST-resursprovider](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [BulkExecutor – .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [BulkExecutor – Java](sql-api-sdk-bulk-executor-java.md)

| |  |
|---|---|
|**Hämta SDK**| [NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core/)|
|**API-dokumentation**|[.NET API-referensdokumentation](/dotnet/api/overview/azure/cosmosdb?view=azure-dotnet)|
|**Exempel**|[.NET-kodexempel](sql-api-dotnet-samples.md)|
|**Kom igång**|[Kom igång med Azure Cosmos DB .NET Core SDK](sql-api-dotnet-core-get-started-preview.md)|
|**Självstudier om webbappen**|[Utveckling av webbappar med Azure Cosmos DB](sql-api-dotnet-application.md)|
|**Aktuella framework som stöds**|[.NET standard 1.6 och .NET Standard 1.5](https://www.nuget.org/packages/NETStandard.Library)|

## <a name="release-notes"></a>Viktig information

Azure Cosmos DB .NET Core SDK har funktionsparitet med den senaste versionen av den [Azure Cosmos DB .NET SDK](sql-api-sdk-dotnet.md).

### <a name="a-name3001-preview3001-preview"></a><a name="3.0.0.1-preview"/>3.0.0.1-Preview
* Förhandsversion 1 av [Version 3.0.0](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/) av .NET SDK för en förhandsversion.
* Target .NET Standard som har stöd för .NET framework 4.6.1+ och .NET Core 2.0 +
* Ny objektmodell med översta CosmosClient och metoder dela upp på relevanta CosmosDatabases, CosmosContainers och CosmosItems klasser.
* Stöd för strömmar.
* Uppdatera CosmosResponseMessage från servern för att returnera statuskod och utlöser undantag endast när inget svar returneras.

### <a name="a-name241241"></a><a name="2.4.1"/>2.4.1

* Korrigeringar för spårning av konkurrenstillstånd för frågor som gjorde att tomma sidor

### <a name="a-name240240"></a><a name="2.4.0"/>2.4.0

* SDK: er System.Net.Http versionen matchar det som definieras i NuGet-paketet
* Ökat decimalprecision storleken för LINQ-frågor.
* Har lagts till nya klasser CompositePath, CompositePathSortOrder, SpatialSpec, SpatialType och PartitionKeyDefinitionVersion
* Har lagts till TimeToLivePropertyPath till dokumentsamling
* Har lagts till CompositeIndexes och SpatialIndexes till IndexPolicy
* Har lagts till versionen till PartitionKeyDefinition
* Lagt till ingen till PartitionKey

### <a name="a-name230230"></a><a name="2.3.0"/>2.3.0

 * Har lagts till IdleTcpConnectionTimeout, OpenTcpConnectionTimeout, MaxRequestsPerTcpConnection och MaxTcpConnectionsPerEndpoint till ConnectionPolicy.
 
### <a name="a-name223223"></a><a name="2.2.3"/>2.2.3

* Diagnostikförbättringar

### <a name="a-name222222"></a><a name="2.2.2"/>2.2.2

* Har lagts till variabeln miljöinställning ”POCOSerializationOnly”.

* Ta bort DocumentDB.Spatial.Sql.dll och ingår nu i Microsoft.Azure.Documents.ServiceInterop.dll

### <a name="a-name221221"></a><a name="2.2.1"/>2.2.1

* Förbättringar i logik för omprövning under en redundansväxling för StoredProcedure utföra anrop.

* Gjort DocumentClientEventSource singleton. 

* Åtgärda GatewayAddressCache tidsgräns som inte respekterar ConnectionPolicy RequestTimeout.

### <a name="a-name220220"></a><a name="2.2.0"/>2.2.0

* Lagt till TransportException, en typ av interna undantag av SDK direkt/TCP-transport diagnostik. När det finns i undantag meddelanden, skriver den här typen ytterligare information för felsökning av anslutningsproblem för klienten.

* Har lagts till nya konstruktorn överlagring som tar en HttpMessageHandler, en HTTP-hanteraren stack ska användas för att skicka HttpClient-förfrågningar (t.ex. HttpClientHandler).

* Åtgärda fel där huvud med null-värden har inte hanteras korrekt.

* Förbättrad samling cache-verifiering.

### <a name="a-name213213"></a><a name="2.1.3"/>2.1.3

* Uppdaterade System.Net.Security till 4.3.2.

### <a name="a-name212212"></a><a name="2.1.2"/>2.1.2

* Diagnostikspårning förbättringar.

### <a name="a-name211211"></a><a name="2.1.1"/>2.1.1

* Lägga till mer flexibilitet vid tillfälliga fel i flera regioner begäran.

### <a name="a-name210210"></a><a name="2.1.0"/>2.1.0

* Att skriva stöd har lagts till flera regioner.
* Mellan partition frågeprestandaförbättringar med ÖVERKANT och MaxBufferedItemCount.

### <a name="a-name200200"></a><a name="2.0.0"/>2.0.0

* Har lagts till behöver support för avbrott.
* Har lagts till SetCurrentLocation till ConnectionPolicy som automatiskt fyller de önskade platser baserat på regionen.
* En bugg har åtgärdats i mellan Partition frågor med Min/Max- och ett filter som matchar inga dokument på en enskild partition.
* DocumentClient metoderna har nu paritet med IDocumentClient.
* Uppdaterade direkt TCP transport stackutrymme för att minska antalet anslutningar som upprättats.
* Tillagt stöd för direkt TCP för läge för icke-Windows-klienter.

### <a name="a-name200-preview2200-preview2"></a><a name="2.0.0-preview2"/>2.0.0-preview2

* Har lagts till behöver support för avbrott.
* Har lagts till SetCurrentLocation till ConnectionPolicy som automatiskt fyller de önskade platser baserat på regionen.
* En bugg har åtgärdats i mellan Partition frågor med Min/Max- och ett filter som matchar inga dokument på en enskild partition.

### <a name="a-name200-preview200-preview"></a><a name="2.0.0-preview"/>2.0.0-Preview

* DocumentClient metoderna har nu paritet med IDocumentClient.
* Uppdaterade direkt TCP transport stackutrymme för att minska antalet anslutningar som upprättats.
* Tillagt stöd för direkt TCP för läge för icke-Windows-klienter.

### <a name="a-name11001100"></a><a name="1.10.0"/>1.10.0

* Egenskapen har lagts ConsistencyLevel till FeedOptions.
* Har lagts till JsonSerializerSettings RequestOptions och FeedOptions.
* Har lagts till EnableReadRequestsFallback till ConnectionPolicy.

### <a name="a-name191191"></a><a name="1.9.1"/>1.9.1

* Fast KeyNotFoundException för mellan partition sortering av frågor i specialfall.
* En bugg har åtgärdats där JsonProperty attribut i select-satsen för LINQ-frågor inte som lösts in.

### <a name="a-name182182"></a><a name="1.8.2"/>1.8.2

* En bugg har åtgärdats som uppnås under vissa konkurrenstillstånd som resulterar i återkommande ”Microsoft.Azure.Documents.NotFoundException: Läs sessionen är inte tillgänglig för inkommande sessionstoken ”fel när du använder Session konsekvensnivå.

### <a name="a-name181181"></a><a name="1.8.1"/>1.8.1

* Fast regression där FeedOptions.MaxItemCount = -1 utlöste ett System.ArithmeticException: sidstorlek är negativt.
* Lägga till en ny toString ()-funktion till QueryMetrics.
* Exponerade partitionsstatistik vid läsning av samlingar.
* Tillagda PartitionKey-egenskapen till ChangeFeedOptions.
* Mindre felkorrigeringar.

### <a name="a-name171171"></a><a name="1.7.1"/>1.7.1
 
 * Lägger till möjligheten att ange unika index för dokument med hjälp av UniqueKeyPolicy egenskap på dokumentsamling.
 * Ett fel där de anpassade inställningarna för JsonSerializer inte har att användas för vissa frågor och lagrade procedurkörning har åtgärdats.

### <a name="a-name170170"></a><a name="1.7.0"/>1.7.0
 
 * Anpassning av ändra från Azure DocumentDB till Azure Cosmos DB i API-referensen dokumentation, metadatainformation i sammansättningar och NuGet-paketet.
 * Visa diagnostikinformation och svarstid från svaret antalet begäranden som skickas med direktanslutning läge. Egenskapsnamnen är RequestDiagnosticsString och RequestLatency ResourceResponse klassen.
 * Den SDK-versionen kräver att den senaste versionen av Azure Cosmos DB-emulatorn tillgänglig för nedladdning från https://aka.ms/cosmosdb-emulator.
 
### <a name="a-name160160"></a><a name="1.6.0"/>1.6.0

* Lägga till flera tillförlitlighet korrigeringar och förbättringar.

### <a name="a-name151151"></a><a name="1.5.1"/>1.5.1 

* Interna ändringar med stöd för [Microsoft.Azure.Graphs](https://docs.microsoft.com/azure/cosmos-db/graph-sdk-dotnet)

### <a name="a-name150150"></a><a name="1.5.0"/>1.5.0 

* Stöd har lagts till för PartitionKeyRangeId som en FeedOption för att samla frågeresultaten till en specifik partition nyckelintervall värde.
* Stöd har lagts till för StartTime som en ChangeFeedOption att börja titta för att ändringarna efter den tidpunkten.

### <a name="a-name141141"></a><a name="1.4.1"/>1.4.1

*   Ett problem har åtgärdats i klassen JsonSerializable som kan orsaka ett dataspillsundantag.

### <a name="a-name140140"></a><a name="1.4.0"/>1.4.0

*   Lagt till stöd för att ange anpassade JsonSerializerSettings vid instansiering en [DocumentClient](/dotnet/api/microsoft.azure.documents.client.documentclient?view=azure-dotnet) instans.

### <a name="a-name132132"></a><a name="1.3.2"/>1.3.2

*   Stöd för .NET Standard 1.5 som en mål-ramverk.

### <a name="a-name131131"></a><a name="1.3.1"/>1.3.1

*   Ett problem som påverkade x64 har åtgärdats datorer som inte stöder SSE4 instruktion och utlöser SEHException när du kör Azure Cosmos DB-frågor.

### <a name="a-name130130"></a><a name="1.3.0"/>1.3.0

*   Stöd har lagts till för en ny konsekvensnivå kallas ConsistentPrefix.
*   Stöd har lagts till för frågan mått för enskilda partitioner.
*   Stöd har lagts till för att begränsa storleken på fortsättningstoken för frågor.
*   Tillagt stöd för mer detaljerad spårning för misslyckade förfrågningar.
*   Gjort några prestandaförbättringar i SDK.

### <a name="a-name122122"></a><a name="1.2.2"/>1.2.2

* Ett problem som ignoreras PartitionKey-värde som angavs i FeedOptions för mängdfrågor har åtgärdats.
* Ett problem har åtgärdats i transparent hantering av partitionen management under mitten flygning flera partitioner Order By frågekörning.

### <a name="a-name121121"></a><a name="1.2.1"/>1.2.1

* Ett problem som orsakade låsningar i några av async API: er när de används i kontexten för ASP.NET har åtgärdats.

### <a name="a-name120120"></a><a name="1.2.0"/>1.2.0

* Åtgärdar att göra SDK mer robust för automatisk redundans under vissa omständigheter.

### <a name="a-name112112"></a><a name="1.1.2"/>1.1.2

* Korrigering för ett problem som ibland medför en WebException: Fjärrnamnet kunde inte lösas.
* Lagt till stöd för att läsa in ett skrivet dokument genom att lägga till nya överlagringar till ReadDocumentAsync API direkt.

### <a name="a-name111111"></a><a name="1.1.1"/>1.1.1

* LINQ-stöd för mängdfrågor (COUNT, MIN, MAX, SUM och Genomsnittlig) har lagts till.
* Korrigering för problem med en minnesläcka för objektet ConnectionPolicy beror på användning av händelsehanterare.
* Åtgärda ett problem där UpsertAttachmentAsync inte fungerar när en ETag har använts.
* Åtgärda ett problem där mellan partition ordning av frågan fortsättning inte fungerar vid sortering på strängfält.

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0

* Stöd har lagts till för mängdfrågor (COUNT, MIN, MAX, SUM och Genomsnittlig). Se [aggregering support](sql-query-aggregates.md).
* Sänkte lägsta dataflöde på partitionerade samlingar från 10,100 RU/s till 2500 RU/s.

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0

Azure Cosmos DB .NET Core SDK gör det möjligt att bygga snabba, plattformsoberoende [ASP.NET Core](https://www.asp.net/core) och [.NET Core](https://www.microsoft.com/net/core#windows) appar för att köras på Windows, Mac och Linux. Den senaste versionen av Azure Cosmos DB .NET Core SDK är fullständigt [Xamarin](https://www.xamarin.com) kompatibel och används för att bygga appar för iOS, Android och Mono (Linux).  

### <a name="a-name010-preview010-preview"></a><a name="0.1.0-preview"/>0.1.0-Preview

Förhandsversion av Azure Cosmos DB .NET Core SDK gör det möjligt att bygga snabba, plattformsoberoende [ASP.NET Core](https://www.asp.net/core) och [.NET Core](https://www.microsoft.com/net/core#windows) appar för att köras på Windows, Mac och Linux.

Förhandsversion av Azure Cosmos DB .NET Core SDK har funktionsparitet med den senaste versionen av den [Azure Cosmos DB .NET SDK](sql-api-sdk-dotnet.md) och har stöd för följande:
* Alla [anslutningsläge](performance-tips.md#networking): Gateway-läge, direkt TCP och Direct HTTPs.
* Alla [konsekvensnivåer](consistency-levels.md): Begränsad föråldring, stark, Session och slutlig.
* [Partitionerade samlingar](partition-data.md).
* [Databaskonton och geo-replikering](distribute-data-globally.md).

Om du har frågor som rör detta SDK kan skicka till [StackOverflow](https://stackoverflow.com/questions/tagged/azure-documentdb), eller rapportera problemet i den [GitHub-lagringsplatsen](https://github.com/Azure/azure-documentdb-dotnet/issues).

## <a name="release--retirement-dates"></a>Fri & Släpp dras tillbaka datum

| Version | Utgivningsdatum | Slutdatum |
| --- | --- | --- |
| [2.4.1](#2.4.1) |Den 20 juni 2019 |--- |
| [2.4.0](#2.4.0) |05 maj 2019 |--- |
| [2.3.0](#2.3.0) |04 april 2019 |--- |
| [2.2.3](#2.2.3) |11 mars 2019 |--- |
| [2.2.2](#2.2.2) |06 februari 2019 |--- |
| [2.2.1](#2.2.1) |24 december 2018 |--- |
| [2.2.0](#2.2.0) |07 december 2018 |--- |
| [2.1.3](#2.1.3) |15 oktober 2018 |--- |
| [2.1.2](#2.1.2) |04 oktober 2018 |--- |
| [2.1.1](#2.1.1) |27 september 2018 |--- |
| [2.1.0](#2.1.0) |21 september 2018 |--- |
| [2.0.0](#2.0.0) |07 september 2018 |--- |
| [1.9.1](#1.9.1) |09 mars 2018 |--- |
| [1.8.2](#1.8.2) |21 februari 2018 |--- |
| [1.8.1](#1.8.1) |05 februari 2018 |--- |
| [1.7.1](#1.7.1) |16 november 2017 |--- |
| [1.7.0](#1.7.0) |Den 10 november 2017 |--- |
| [1.6.0](#1.6.0) |Den 17 oktober 2017 |--- |
| [1.5.1](#1.5.1) |02 oktober 2017 |--- |
| [1.5.0](#1.5.0) |10 augusti 2017 |--- | 
| [1.4.1](#1.4.1) |07 augusti 2017 |--- |
| [1.4.0](#1.4.0) |02 augusti 2017 |--- |
| [1.3.2](#1.3.2) |12 juni 2017 |--- |
| [1.3.1](#1.3.1) |Den 23 maj 2017 |--- |
| [1.3.0](#1.3.0) |10 maj 2017 |--- |
| [1.2.2](#1.2.2) |19 april 2017 |--- |
| [1.2.1](#1.2.1) |Den 29 mars 2017 |--- |
| [1.2.0](#1.2.0) |25 mars 2017 |--- |
| [1.1.2](#1.1.2) |20 mars 2017 |--- |
| [1.1.1](#1.1.1) |14 mars 2017 |--- |
| [1.1.0](#1.1.0) |16 februari 2017 |--- |
| [1.0.0](#1.0.0) |Den 21 december 2016 |--- |
| [0.1.0-Preview](#0.1.0-preview) |Den 15 november 2016 |Den 31 december 2016 |

## <a name="see-also"></a>Se även
Mer information om Cosmos DB finns [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) service-sidan.

