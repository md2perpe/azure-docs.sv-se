---
title: Skapa en funktion i Azure som utlöses av kömeddelanden | Microsoft Docs
description: Använd Azure Functions för att skapa en funktion utan server som startas av meddelanden som skickas till en Azure Storage-kö.
services: azure-functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
ms.assetid: 361da2a4-15d1-4903-bdc4-cc4b27fc3ff4
ms.service: azure-functions
ms.devlang: multiple
ms.topic: quickstart
ms.date: 10/01/2018
ms.author: glenga
ms.custom: mvc, cc996988-fb4f-47
ms.openlocfilehash: 44d6311246ab303966b7cfd8bee854b1c017f85d
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62107133"
---
# <a name="create-a-function-triggered-by-azure-queue-storage"></a>Skapa en funktion som utlöses av Azure Queue Storage

Läs hur du skapar en funktion som utlöses när meddelanden skickas till en Azure Storage-kö.

![Visa meddelande i loggarna.](./media/functions-create-storage-queue-triggered-function/function-app-in-portal-editor.png)

## <a name="prerequisites"></a>Nödvändiga komponenter

- Hämta och installera [Microsoft Azure Storage Explorer](https://storageexplorer.com/).

- En Azure-prenumeration. Om du inte har ett konto kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="create-an-azure-function-app"></a>Skapa en Azure Functions-app

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

![Funktionsappen skapades.](./media/functions-create-first-azure-function/function-app-create-success.png)

Därefter skapar du en funktion i den nya funktionsappen.

<a name="create-function"></a>

## <a name="create-a-queue-triggered-function"></a>Skapa en funktion som utlöses av en kö

1. Expandera funktionsappen och klicka på knappen **+** bredvid **Funktioner**. Om det här är den första funktionen i din funktionsapp väljer du **I portalen** och sedan **Fortsätt**. Annars går du till steg tre.

   ![Sidan snabbstart för funktioner i Azure Portal](./media/functions-create-storage-queue-triggered-function/function-app-quickstart-choose-portal.png)

1. Välj **Fler mallar** och sedan **Slutför och visa mallar**.

    ![Functions-snabbstart, välj fler mallar](./media/functions-create-storage-queue-triggered-function/add-first-function.png)

1. Skriv `queue` i sökfältet och välj sedan mallen **Köutlösare**.

1. Om du får uppmaningen väljer du **Installera** för att installera Azure Storage-tillägget för eventuella beroenden i funktionsappen. När installationen är klar väljer du **Fortsätt**.

    ![Installera bindningstillägg](./media/functions-create-storage-queue-triggered-function/functions-create-queue-storage-trigger-portal.png)

1. Använd inställningarna som anges i tabellen nedanför bilden.

    ![Konfigurera lagringsköfunktionen som utlöses.](./media/functions-create-storage-queue-triggered-function/functions-create-queue-storage-trigger-portal-2.png)

    | Inställning | Föreslaget värde | Beskrivning |
    |---|---|---|
    | **Namn** | Ett unikt namn i funktionsappen | Namnge funktionen som utlöses av kön. |
    | **Könamn**   | myqueue-items    | Namnet på den kö som ska anslutas till i ditt Storage-konto. |
    | **Lagringskontoanslutning** | AzureWebJobStorage | Du kan antingen använda den lagringskontoanslutning som redan används i funktionsappen eller skapa en ny.  |    

1. Klicka på **Skapa** för att skapa den nya funktionen.

Sedan ansluter du till ditt Azure Storage-konto och skapar lagringskön **myqueue-items**.

## <a name="create-the-queue"></a>Skapa kön

1. Klicka på **Integrera** i din funktion, expandera **Dokumentation** och kopiera både **kontonamnet** och **kontonyckeln**. Du använder dessa autentiseringsuppgifter för att ansluta till lagringskontot i Azure Storage Explorer. Om du redan har anslutit ditt lagringskonto går du vidare till steg 4.

    ![Hämta autentiseringsuppgifterna för att ansluta till lagringskontot.](./media/functions-create-storage-queue-triggered-function/functions-storage-account-connection.png)

1. Kör verktyget [Microsoft Azure Storage Explorer](https://storageexplorer.com/), klicka på anslutningsikonen till vänster, välj **Use a storage account name and key** (Använd ett kontonamn och en kontonyckel för lagringskontot) och klicka på **Nästa**.

    ![Kör verktyget Storage Account Explorer.](./media/functions-create-storage-queue-triggered-function/functions-storage-manager-connect-1.png)

1. Ange **kontonamnet** och **kontonyckeln** från steg 1, klicka på **Nästa** och sedan på **Anslut**.

    ![Ange autentiseringsuppgifter för lagringskontot och anslut.](./media/functions-create-storage-queue-triggered-function/functions-storage-manager-connect-2.png)

1. Expandera det anslutna lagringskontot, högerklicka på **Köer**, klicka på **Skapa kö**, skriv `myqueue-items` och tryck sedan på Retur.

    ![Skapa en lagringskö.](./media/functions-create-storage-queue-triggered-function/functions-storage-manager-create-queue.png)

Nu när du har en lagringskö kan du testa funktionen genom att lägga till ett meddelande i kön.

## <a name="test-the-function"></a>Testa funktionen

1. Gå till Azure Portal igen, bläddra till din funktion, expandera **Loggar** längst ned på sidan och se till att loggströmningen inte är pausad.

1. I Lagringsutforskaren expanderar du ditt lagringskonto, **Köer** och **myqueue-items**. Klicka sedan på **Lägg till meddelande**.

    ![Lägg till ett meddelande i kön.](./media/functions-create-storage-queue-triggered-function/functions-storage-manager-add-message.png)

1. Skriv ditt "Hello World!"- meddelande i **Meddelandetext** och klicka på **OK**.

1. Vänta några sekunder och gå sedan tillbaka till dina funktionsloggar och kontrollera att det nya meddelandet har lästs från kön.

    ![Visa meddelande i loggarna.](./media/functions-create-storage-queue-triggered-function/functions-queue-storage-trigger-view-logs.png)

1. Gå tillbaka till Lagringsutforskaren, klicka på **Uppdatera** och kontrollera att meddelandet har bearbetats och inte längre finns i kön.

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Nästa steg

Du har skapat en funktion som körs när ett meddelande läggs till i en lagringskö. Mer information om Queue Storage-utlösare finns i [Azure Functions Storage queue bindings](functions-bindings-storage-queue.md) (Azure Functions-lagringsköbindningar).

Nu när du har skapat din första funktion ska vi lägga till en utdatabindning till funktionen som skriver ett meddelande till en annan kö.

> [!div class="nextstepaction"]
> [Lägga till meddelanden i en Azure Storage-kö med Functions](functions-integrate-storage-queue-output-binding.md)
