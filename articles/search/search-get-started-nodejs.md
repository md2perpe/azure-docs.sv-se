---
title: 'Snabbstart för node.js: Skapa, läsa in och fråga sedan index med hjälp av Azure Search REST API: er – Azure Search'
description: 'Beskriver hur du skapar ett index, läsa in data och kör frågor med hjälp av Node.js och Azure Search REST-API: er.'
author: jj09
manager: jlembicz
services: search
ms.service: search
ms.topic: conceptual
ms.date: 04/26/2017
ms.author: jjed
ms.custom: seodec2018
ms.openlocfilehash: 44b7f1f49d6764418dcc0e72cb667e17a2b920c6
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67450030"
---
# <a name="quickstart-create-an-azure-search-index-in-nodejs"></a>Snabbstart: Skapa ett Azure Search-index i Node.js
> [!div class="op_single_selector"]
> * [Portal](search-get-started-portal.md)
> * [NET](search-howto-dotnet-sdk.md)
> 
> 

Lär dig hur du skapar ett anpassat Node.js-sökprogram som använder Azure Search som sökmiljö. I den här självstudiekursen används [REST-API:et för tjänsten Azure Search](https://msdn.microsoft.com/library/dn798935.aspx) för att skapa de objekt och åtgärder som används i den här övningen.

Vi använde [Node.js](https://Nodejs.org) och NPM, [Sublime Text 3](https://www.sublimetext.com/3) och Windows PowerShell i Windows 8.1 när vi utvecklade och testade den här koden.

Om du vill köra det här exemplet måste du ha en Azure Search-tjänst, som du kan registrera dig för på [Azure Portal](https://portal.azure.com). Stegvisa instruktioner finns i [Skapa en Azure Search-tjänst på portalen](search-create-service-portal.md).

## <a name="about-the-data"></a>Om de data som används
Det här exempelprogrammet använder data från [United States Geological Services (USGS)](https://geonames.usgs.gov/domestic/download_data.htm), som har filtrerats på delstaten Rhode Island för att minska datauppsättningens storlek. Vi ska använda dessa data för att skapa ett sökprogram som returnerar viktiga byggnader som sjukhus och skolor, samt geologiska element som vattendrag, sjöar och bergstoppar.

I det här programmet i **DataIndexer** programmet bygger och läser in indexet med hjälp av en [indexeraren](https://msdn.microsoft.com/library/azure/dn798918.aspx) konstruktion, hämtar den filtrerade USGS-datauppsättningen från en Azure SQL Database. Autentiseringsuppgifter och anslutningsinformation för onlinedatakällan finns i programkoden. Ingen ytterligare konfiguration krävs.

> [!NOTE]
> Vi har använt ett filter för den här datauppsättningen för att hålla oss under gränsen på 10 000 dokument för den kostnadsfria prisnivån. Den här begränsningen gäller inte om du använder standardnivån. Mer information om kapaciteten för varje prisnivå finns i [Tjänstbegränsningar för Search](search-limits-quotas-capacity.md).
> 
> 

<a id="sub-2"></a>

## <a name="find-the-service-name-and-api-key-of-your-azure-search-service"></a>Leta upp tjänstnamnet och API-nyckeln för Azure Search-tjänsten
När du har skapat tjänsten går du tillbaka till portalen för att hämta URL:en eller `api-key`. Anslutningar till Search-tjänsten kräver att du har båda URL:en och en `api-key` för att autentisera anropet.

1. Logga in på [Azure Portal](https://portal.azure.com).
2. I snabbåtkomstfältet klickar du på **Söktjänst** för att visa en lista över Azure Search-tjänsterna som har etablerats för din prenumeration.
3. Markera den tjänst som du vill använda.
4. På instrumentpanelen för tjänsten ser du paneler för viktig information som nyckelikonen för att komma åt administatörsnycklarna.
5. Kopiera tjänstens URL, en administratörsnyckel och en frågenyckel. Du behöver alla tre senare när du lägger till dem i filen config.js.

## <a name="download-the-sample-files"></a>Ladda ned exempelfilerna
Använd någon av följande metoder för att hämta exemplet.

1. Gå till [search-node-indexer-demo](https://github.com/Azure-Samples/search-node-indexer-demo).
2. Klicka på **Ladda ned ZIP**, spara ZIP-filen och extrahera sedan alla filer som den innehåller.

Alla efterföljande filändringar och körningsinstruktioner görs mot filer i den här mappen.

## <a name="update-the-configjs-with-your-search-service-url-and-api-key"></a>Uppdatera config.js. med Search-tjänstens URL och API-nyckel
Använd URL:en och API-nyckeln som du kopierade tidigare, ange URL:en, administratörsnyckeln och frågenyckeln i konfigurationsfilen.

Administratörsnycklar beviljar fullständig kontroll över tjänståtgärder, inklusive att skapa eller ta bort ett index och inläsning av dokument. Frågenycklar är däremot avsedda för skrivskyddade åtgärder, som vanligtvis används av klientprogram som ansluter till Azure Search.

I det här exemplet använder vi frågenyckeln för att uppfylla bästa praxis som rekommenderar att frågenyckeln används i klientprogram.

Följande skärmbild visar **config.js** i en textredigerare, med relevanta poster markerade så att du ser var du ska uppdatera filen med värdena för din söktjänst.

![][5]

## <a name="host-a-runtime-environment-for-the-sample"></a>Värd för en körningsmiljö för exemplet
Exempelfilen kräver en HTTP-server, som du kan installera globalt med npm.

Använd ett PowerShell-fönster för följande kommandon.

1. Navigera till mappen som innehåller filen **package.json**.
2. Skriv `npm install`.
3. Skriv `npm install -g http-server`.

## <a name="build-the-index-and-run-the-application"></a>Skapa indexet och kör programmet
1. Skriv `npm run indexDocuments`.
2. Skriv `npm run build`.
3. Skriv `npm run start_server`.
4. Dirigera webbläsaren till `http://localhost:8080/index.html`

## <a name="search-on-usgs-data"></a>Söka i USGS-data
USGS-datauppsättningen innehåller poster som är relevanta för delstaten Rhode Island. Om du klickar på **Search** i en tom sökruta returneras 50 poster, vilket är standard.

Om du skriver en sökterm ger du sökmotorn något att gå på. Prova att skriva namnet på någon från regionen. ”Roger Williams” var Rhode Islands första guvernör. Många parker, byggnader och skolor bär hans namn.

![][9]

Du kan också prova någon av dessa söktermer:

* Pawtucket
* Pembroke
* goose +cape

## <a name="next-steps"></a>Nästa steg
Det här är den första Azure Search-självstudiekursen som baseras på Node.js och USGS-datauppsättningen. Med tiden kommer vi att utöka den här självstudiekursen och demonstrera ytterligare sökfunktioner som du kanske vill använda i dina anpassade lösningar.

Om du redan har viss erfarenhet av Azure Search kan du använda det här exemplet som utgångspunkt för att prova förslagsställare (frågeifyllningsförslag eller Komplettera automatiskt), filter och aspektbaserad navigering. Du kan även förbättra sidan med sökresultat genom att lägga till antal och batchbearbeta dokument så att användarna kan bläddra igenom resultaten.

Har du inte provat Azure Search än? Vi rekommenderar att du går andra självstudiekurser så att du ser vad du kan skapa. Vår [dokumentationssida](https://azure.microsoft.com/documentation/services/search/) innehåller fler resurser. 

<!--Image references-->
[1]: ./media/search-get-started-Nodejs/create-search-portal-1.PNG
[2]: ./media/search-get-started-Nodejs/create-search-portal-2.PNG
[3]: ./media/search-get-started-Nodejs/create-search-portal-3.PNG
[5]: ./media/search-get-started-Nodejs/AzSearch-Nodejs-configjs.png
[9]: ./media/search-get-started-Nodejs/rogerwilliamsschool.png
