---
title: Azure Cosmos DB Table API .NET Standard SDK och resurser
description: Lär dig allt om Azure Cosmos DB Table API och .NET Standard SDK inklusive frisläppningsdatum, dras tillbaka datum och ändringar som gjorts mellan varje version.
author: wmengmsft
ms.author: wmeng
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: dotnet
ms.topic: reference
ms.date: 03/18/2019
ms.openlocfilehash: 1c9894772f440a568cbc08890feb961471d84137
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66480213"
---
# <a name="azure-cosmos-db-table-net-standard-api-download-and-release-notes"></a>Azure Cosmos DB-tabell .NET Standard-API: Ladda ned och viktig information
> [!div class="op_single_selector"]
> 
> * [NET](table-sdk-dotnet.md)
> * [.NET-standard](table-sdk-dotnet-standard.md)
> * [Java](table-sdk-java.md)
> * [Node.js](table-sdk-nodejs.md)
> * [Python](table-sdk-python.md)

|   |   |
|---|---|
|**Hämta SDK**|[NuGet](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table)|
|**Exempel**|[Cosmos DB Table API .NET-exempel](https://github.com/Azure-Samples/azure-cosmos-table-dotnet-core-getting-started)|
|**Snabbstart**|[Snabbstart](create-table-dotnet.md)|
|**Självstudie**|[Självstudie](tutorial-develop-table-dotnet.md)|
|**Aktuella framework som stöds**|[Microsoft .NET Standard 2.0](https://www.nuget.org/packages/NETStandard.Library)|

## <a name="release-notes"></a>Viktig information

### <a name="a-name101101"></a><a name="1.0.1"/>1.0.1
* Felkorrigeringar

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* Allmänt tillgänglig version

### <a name="a-name0110-preview0110-preview"></a><a name="0.11.0-preview"/>0.11.0-Preview
* Ändringar har gjorts till hur CloudTableClient kan konfigureras. Det tar nu en ett TableClientConfiguration objekt under konstruktionen. TableClientConfiguration innehåller olika egenskaper för att konfigurera klientens funktionssätt beroende på om mål-slutpunkten är Cosmos DB Table API eller tabell-API för Azure Storage.
* Stöd har lagts till för att TableQuery du returnerar resultat på sorteringsordningen på en anpassad kolumn. Den här funktionen stöds bara på Cosmos DB Table-slutpunkter.
* Stöd har lagts till för att exponera RequestCharges på olika resultattyper. Den här funktionen stöds bara på Cosmos DB Table-slutpunkter.

### <a name="a-name0101-preview0101-preview"></a><a name="0.10.1-preview"/>0.10.1-Preview
* Lägg till stöd för SAS-token, driften av TablePermissions och ServiceProperties ServiceStats mot Azure Storage-tabell slutpunkter. 
   > [!NOTE]
   > Vissa funktioner i föregående tabell SDK för Azure Storage stöds inte ännu, till exempel client side encryption.

### <a name="a-name0100-preview0100-preview"></a><a name="0.10.0-preview"/>0.10.0-Preview
* Lägg till stöd för grundläggande CRUD-, batch- och frågeåtgärder mot Azure Storage-tabell slutpunkter. 
   > [!NOTE]
   > Vissa funktioner i föregående tabell SDK för Azure Storage stöds inte ännu, till exempel client side encryption.

### <a name="a-name091-preview091-preview"></a><a name="0.9.1-preview"/>0.9.1-Preview
* Azure Cosmos DB tabell SDK för .NET Standard är ett plattformsoberoende .NET-bibliotek som tillhandahåller effektiv åtkomst till datamodellen tabell Cosmos DB. Den här första versionen stöder den fullständiga uppsättningen tabell och entiteten CRUD + fråga funktioner med liknande API: er som den [Cosmos DB tabell SDK för .NET Framework](table-sdk-dotnet.md). 
   > [!NOTE]
   >  Azure lagringstabell slutpunkter stöds inte ännu i 0.9.1-preview-versionen.

## <a name="release-and-retirement-dates"></a>Versionen och dras tillbaka datum
Microsoft meddelar minst **12 månader** förväg dra tillbaka en SDK för att utjämna övergången till en nyare/stöds version.

Den här plattformsoberoende .NET Standard-bibliotekets [Microsoft.Azure.Cosmos.Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) ersätter i .NET Framework-biblioteket [Microsoft.Azure.CosmosDB.Table](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table).

| Version | Utgivningsdatum | Slutdatum |
| --- | --- | --- |
| [1.0.1](#1.0.1) |Den 19 april 2019 |--- |
| [1.0.0](#1.0.0) |Den 13 mars 2019 |--- |
| [0.11.0-Preview](#0.11.0-preview) |5 mars 2019 |--- |
| [0.10.1-Preview](#0.10.1-preview) |Den 22 januari 2019 |--- |
| [0.10.0-Preview](#0.10.0-preview) |18 december 2018 |--- |
| [0.9.1-Preview](#0.9.1-preview) |Den 18 oktober 2018 |--- |


## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Se också
Läs mer om Azure Cosmos DB Table API i [introduktion till Azure Cosmos DB Table API](table-introduction.md). 
