---
title: Ansluta till Azure blob storage - Azure Logic Apps
description: Skapa och hantera blobar i Azure storage med Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 06/20/2019
tags: connectors
ms.openlocfilehash: d9c29837e99d327112e6a9d648a5c56cc35e8555
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2019
ms.locfileid: "67296603"
---
# <a name="create-and-manage-blobs-in-azure-blob-storage-with-azure-logic-apps"></a>Skapa och hantera blobbar i Azure blob storage med Azure Logic Apps

Den här artikeln visar hur du kan komma åt och hantera filer som lagras som blobar i Azure storage-kontot från inuti en logikapp med Azure Blob Storage connector. På så sätt kan du skapa logikappar som automatiserar uppgifter och arbetsflöden för att hantera dina filer. Du kan till exempel skapa logikappar som att skapa, hämta, uppdatera och ta bort filer i ditt storage-konto.

Anta att du har ett verktyg som uppdateras på en Azure-webbplats. den fungerar som en utlösare för din logikapp. Om den här händelsen inträffar kan ha du logikappen vissa filen i din blob storage-behållare, vilket är en åtgärd i din logikapp.

> [!NOTE]
> Logic Apps stöder inte att ansluta direkt till Azure storage-konton genom brandväggar. För att få åtkomst till dessa konton måste du använda något av alternativen här:
>
> * Skapa en [integreringstjänstmiljön](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), som kan ansluta till resurser i Azure-nätverk.
>
> * Om du redan använder API Management kan du använda den här tjänsten för det här scenariot. Mer information finns i [enkel integrering företagsarkitektur](https://aka.ms/aisarch).

Om du är nybörjare till logic apps, granska [vad är Azure Logic Apps](../logic-apps/logic-apps-overview.md) och [snabbstarten: Skapa din första logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md). Specifika teknisk information finns i den [Azure Blob Storage-anslutning för referens](/connectors/azureblobconnector/).

## <a name="limits"></a>Limits

* Som standard Azure Blob Storage-åtgärder kan läsa eller skriva filer som är *50 MB eller mindre*. För att hantera filer som är större än 50 MB men upp till 1 024 MB, Azure Blob Storage-åtgärder stöder [meddelande storlekar](../logic-apps/logic-apps-handle-large-messages.md). Den **Get blobinnehåll** åtgärden använder implicit storlekar.

* Azure Blob Storage-utlösare stöder inte storlekar. När du begär innehåll, utlösare Markera bara de filer som är 50 MB eller mindre. Om du vill hämta filer större än 50 MB, så det här mönstret:

  * Använda en Azure Blob Storage-utlösare som returnerar filegenskaper, till exempel **när en blob läggs till eller ändras (enbart egenskaper)** .

  * Följ utlösare med Azure Blob Storage **Get blobinnehåll** åtgärd, som läser den fullständiga filen och implicit använder storlekar.

## <a name="prerequisites"></a>Nödvändiga komponenter

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/).

* En [Azure storage-konto och storage-behållare](../storage/blobs/storage-quickstart-blobs-portal.md)

* Logikappen där du behöver åtkomst till Azure blob storage-kontot. Om du vill starta logikappen med en Azure Blob Storage-utlösare, som du behöver en [tom logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="add-trigger"></a>

## <a name="add-blob-storage-trigger"></a>Lägg till blob storage-utlösare

I Azure Logic Apps varje logikapp måste börja med en [utlösaren](../logic-apps/logic-apps-overview.md#logic-app-concepts), som utlöses när en specifik händelse sker eller när ett specifikt villkor uppfylls. Varje gång utlösaren Logic Apps-motorn skapar en logikappinstans och börjar köras appens arbetsflöde.

Det här exemplet visar hur du kan starta en logikapparbetsflöde med den **när en blob läggs till eller ändras (enbart egenskaper)** utlösare när egenskaper för en blob hämtar läggs till eller uppdateras i lagringsbehållaren.

1. I den [Azure-portalen](https://portal.azure.com) eller Visual Studio, skapa en tom logikapp som öppnas Logic App Designer. Det här exemplet används Azure-portalen.

2. I sökrutan anger du ”azure blob” som filter. Välj utlösaren som du vill använda från listan över utlösare.

   Det här exemplet använder den här utlösaren: **När en blob läggs till eller ändras (enbart egenskaper)**

   ![Välj utlösare](./media/connectors-create-api-azureblobstorage/azure-blob-trigger.png)

3. Om du uppmanas anslutningsinformation [skapar din blob storage-anslutning nu](#create-connection). Eller om anslutningen redan finns, ange nödvändig information för utlösaren.

   För det här exemplet väljer du behållaren och mappen som du vill övervaka.

   1. I den **behållare** väljer du mappikonen.

   2. I listan väljer du vinkelparentesen ( **>** ), och sedan bläddra tills du hittar och välj den mapp som du vill.

      ![Välj mapp](./media/connectors-create-api-azureblobstorage/trigger-select-folder.png)

   3. Välj intervall och frekvens för hur ofta du vill att utlösaren ska kontrollera mappen för ändringar.

4. När du är klar på verktygsfältet för appdesignern väljer **spara**.

5. Nu ska du fortsätta att lägga till en eller flera åtgärder i din logikapp för uppgifter som du vill utföra med utlösare resultaten.

<a name="add-action"></a>

## <a name="add-blob-storage-action"></a>Lägg till blob storage-åtgärd

I Azure Logic Apps, en [åtgärd](../logic-apps/logic-apps-overview.md#logic-app-concepts) är ett steg i arbetsflödet som följer en utlösare eller en annan åtgärd. I det här exemplet logikappen som börjar med den [upprepningsutlösare](../connectors/connectors-native-recurrence.md).

1. I den [Azure-portalen](https://portal.azure.com) eller Visual Studio, öppna logikappen i Logic App Designer. Det här exemplet används Azure-portalen.

2. I Logic App Designer, utlösaren eller åtgärden, väljer **nytt steg**.

   ![Lägga till en åtgärd](./media/connectors-create-api-azureblobstorage/add-action.png) 

   Flytta musen över den anslutande pilen för att lägga till en åtgärd mellan befintliga steg. Välj plustecknet ( **+** ) som visas och välj **Lägg till en åtgärd**.

3. I sökrutan anger du ”azure blob” som filter. Välj vilken åtgärd du önska från åtgärdslistan över.

   Det här exemplet använder den här åtgärden: **Hämta blobbinnehåll**

   ![Välj åtgärd](./media/connectors-create-api-azureblobstorage/azure-blob-action.png)

4. Om du uppmanas anslutningsinformation [skapa anslutningen Azure Blob Storage nu](#create-connection).
Eller om anslutningen redan finns, ange informationen som krävs för åtgärden.

   För det här exemplet väljer du den fil du vill.

   1. Från den **Blob** väljer du mappikonen.
  
      ![Välj mapp](./media/connectors-create-api-azureblobstorage/action-select-folder.png)

   2. Hitta och välj den fil du vill utifrån blobens **Id** tal. Du hittar den **Id** numret i blobbmetadata som returneras av den tidigare beskrivna blob storage-utlösaren.

5. När du är klar på verktygsfältet för appdesignern väljer **spara**.
Testa din logikapp genom att se till att den valda mappen innehåller en blob.

Det här exemplet hämtar endast innehållet för en blob. Lägg till en annan åtgärd som skapar en fil med blobben med hjälp av en annan koppling för att visa innehållet. Lägg exempelvis till en OneDrive-åtgärd som skapar en fil baserat på blob-innehållet.

<a name="create-connection"></a>

## <a name="connect-to-storage-account"></a>Ansluta till lagringskonto

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

[!INCLUDE [Create a connection to Azure blob storage](../../includes/connectors-create-api-azureblobstorage.md)]

## <a name="connector-reference"></a>Referens för anslutningsapp

Teknisk information, till exempel utlösare och åtgärder gränser, enligt beskrivningen av kopplingens öppna API: et (tidigare Swagger) fil, finns i den [anslutningsappens-referenssida](/connectors/azureblobconnector/).

## <a name="next-steps"></a>Nästa steg

* Läs mer om andra [Logic Apps-anslutningsprogram](../connectors/apis-list.md)
