---
title: Anslut en MongoDB-program till Azure Cosmos DB
description: Lär dig hur du ansluter din MongoDB-app till Azure Cosmos DB.
author: rimman
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: dabce19d60a380b47d3583dedb5c11303f416ce7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65978707"
---
# <a name="connect-a-mongodb-application-to-azure-cosmos-db"></a>Anslut en MongoDB-program till Azure Cosmos DB
Lär dig hur du ansluter din MongoDB-app till en Azure Cosmos DB med hjälp av en anslutningssträng för MongoDB. Du kan sedan använda ett Azure Cosmos-databasen som data store för din MongoDB-app. 

Den här självstudien ger dig två sätt att hämta information om anslutningssträngen:

- [Metoden Snabbstart](#QuickstartConnection), för användning med .NET, Node.js, MongoDB-gränssnittet, Java och Python-drivrutiner
- [Anpassad sträng anslutningsmetoden](#GetCustomConnection), för användning med andra drivrutiner

## <a name="prerequisites"></a>Nödvändiga komponenter

- Ett Azure-konto. Om du inte har ett Azure-konto kan du skapa en [kostnadsfritt Azure-konto](https://azure.microsoft.com/free/) nu. 
- Ett Cosmos-konto. Anvisningar finns i [bygga en webbapp med Azure Cosmos DB API för MongoDB och .NET SDK](create-mongodb-dotnet.md).

## <a id="QuickstartConnection"></a>Hämta anslutningssträngen för MongoDB med hjälp av snabb start
1. I en webbläsare, loggar du in den [Azure-portalen](https://portal.azure.com).
2. I den **Azure Cosmos DB** bladet välj API: et. 
3. I rutan till vänster på kontobladet klickar du på **Snabbstart**. 
4. Välj din plattform ( **.NET**, **Node.js**, **MongoDB-gränssnittet**, **Java**, **Python**). Om du inte ser din drivrutin eller verktyg i listan, oroa inte dig – dokumenterar vi kontinuerligt fler kodfragment. Kommentera nedan på vad du vill se. Om du vill lära dig mer om att skapa din egen anslutning, läsa [hämta information om kontots anslutningssträng](#GetCustomConnection).
5. Kopiera och klistra in kodfragmentet i MongoDB-app.

    ![Snabbstart-bladet](./media/connect-mongodb-account/QuickStartBlade.png)

## <a id="GetCustomConnection"></a> Hämta anslutningssträngen för MongoDB för att anpassa
1. I en webbläsare, loggar du in den [Azure-portalen](https://portal.azure.com).
2. I den **Azure Cosmos DB** bladet välj API: et. 
3. I rutan till vänster på kontobladet klickar du på **Connection String**. 
4. Den **Connection String** blad öppnas. Den har all information som behövs för att ansluta till kontot med hjälp av en drivrutin för MongoDB, inklusive en preconstructed anslutningssträng.

    ![Bladet Anslutningssträng](./media/connect-mongodb-account/ConnectionStringBlade.png)

## <a name="connection-string-requirements"></a>Sträng anslutningskraven
> [!Important]
> Azure Cosmos DB har stränga säkerhetskrav och säkerhetsstandarder. Azure Cosmos DB-konton kräver autentisering och säker kommunikation via *SSL*. 
>
>

Azure Cosmos DB stöder standard MongoDB URI formatet för anslutningssträngen, med några särskilda krav: Azure Cosmos DB-konton kräver autentisering och säker kommunikation via SSL. Därför är formatet för anslutningssträngen:

    mongodb://username:password@host:port/[database]?ssl=true

Värdena för den här strängen är tillgängliga i den **Connection String** bladet som visades tidigare:

* Användarnamn (krävs): Cosmos-kontonamn.
* Lösenord (krävs): Cosmos-kontolösenord.
* Värd (krävs): FQDN för Cosmos-konto.
* Port (krävs): 10255.
* Databas (valfritt): Den databas som anslutningen använder. Om ingen databas har angetts och är standarddatabasen ”test”.
* SSL = true (krävs)

Anta exempelvis att kontot som visas på den **Connection String** bladet. En giltig anslutningssträng är:

    mongodb://contoso123:0Fc3IolnL12312asdfawejunASDF@asdfYXX2t8a97kghVcUzcDv98hawelufhawefafnoQRGwNj2nMPL1Y9qsIr9Srdw==@contoso123.documents.azure.com:10255/mydatabase?ssl=true

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [använder Studio 3T](mongodb-mongochef.md) med Azure Cosmos DB:s API för MongoDB.
- Lär dig hur du [använder Robo 3T](mongodb-robomongo.md) med Azure Cosmos DB:s API för MongoDB.
- Utforska MongoDB-[exempel](mongodb-samples.md) med Azure Cosmos DB:s API för MongoDB.
