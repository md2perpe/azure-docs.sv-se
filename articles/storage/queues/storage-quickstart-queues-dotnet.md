---
title: 'Snabbstart: Använda .NET för att skapa en kö i Azure Storage'
description: I den här snabbstarten lär du dig hur du använder Azure Storage-klientbiblioteket för .NET för att skapa en kö och lägga till meddelanden i den. Därefter får du lära dig hur du läser och bearbetar meddelanden från kön.
services: storage
author: mhopkins-msft
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 02/06/2018
ms.author: mhopkins
ms.reviewer: cbrooks
ms.openlocfilehash: c3743c62dcbdccc2a119cfec570df96c622390c7
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/03/2019
ms.locfileid: "67540323"
---
# <a name="quickstart-use-net-to-create-a-queue-in-azure-storage"></a>Snabbstart: Använda .NET för att skapa en kö i Azure Storage

I den här snabbstarten lär du dig hur du använder Azure Storage-klientbiblioteket för .NET för att skapa en kö och lägga till meddelanden i den. Därefter får du lära dig hur du läser och bearbetar meddelanden från kön. 

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

Härnäst laddar du ned och installerar .NET Core 2.0 för ditt operativsystem. Om du kör Windows kan du installera Visual Studio och använda .NET Framework om du så föredrar. Du kan också välja att installera en redigerare som ska användas med operativsystemet.

### <a name="windows"></a>Windows

- Installera [.NET Core för Windows](https://www.microsoft.com/net/download/windows) eller [.NET Framework](https://www.microsoft.com/net/download/windows) (ingår i Visual Studio för Windows)
- Installera [Visual Studio för Windows](https://www.visualstudio.com/). Om du använder .NET Core så är det valfritt för dig att installera Visual Studio.  

Information om hur du väljer mellan .NET Core och .NET Framework finns i [Välj mellan .NET Core och .NET Framework för serverappar](https://docs.microsoft.com/dotnet/standard/choosing-core-framework-server).

### <a name="linux"></a>Linux

- Installera [.NET Core för Linux](https://www.microsoft.com/net/download/linux)
- Om du vill kan du installera [Visual Studio Code](https://www.visualstudio.com/) och [C#-tillägget](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp&dotnetid=963890049.1518206068)

### <a name="macos"></a>macOS

- Installera [.NET Core för macOS](https://www.microsoft.com/net/download/macos).
- Du kan också installera [Visual Studio för Mac](https://www.visualstudio.com/vs/visual-studio-mac/)

## <a name="download-the-sample-application"></a>Hämta exempelprogrammet

Exempelprogrammet som används i den här snabbstarten är ett grundläggande konsolprogram. Du kan utforska exempelprogrammet på [GitHub](https://github.com/Azure-Samples/storage-queues-dotnet-quickstart).

Använd [git](https://git-scm.com/) för att ladda ned en kopia av programmet till utvecklingsmiljön. 

```bash
git clone https://github.com/Azure-Samples/storage-queues-dotnet-quickstart.git
```

Det här kommandot klonar lagret till den lokala git-mappen. Öppna Visual Studio-lösningen genom att leta reda på mappen *storage-queues-dotnet-quickstart*, öppna den och dubbelklicka på *storage-queues-dotnet-quickstart.sln*. 

[!INCLUDE [storage-copy-connection-string-portal](../../../includes/storage-copy-connection-string-portal.md)]

## <a name="configure-your-storage-connection-string"></a>Konfigurera anslutningssträngen för lagring

För att kunna köra programmet måste du ange anslutningssträngen för ditt lagringskonto. Exempelprogrammet läser anslutningssträngen från en miljövariabel och använder den till att autentisera dina begäranden till Azure Storage.

När du har kopierat anslutningssträngen ska du skriva den till en ny miljövariabel på den lokala dator där programmet körs. Konfigurera miljövariabeln genom att öppna ett konsolfönster och följa anvisningarna för ditt operativsystem. Ersätt `<yourconnectionstring>` med den faktiska anslutningssträngen:

### <a name="windows"></a>Windows

```cmd
setx storageconnectionstring "<yourconnectionstring>"
```

När du har lagt till miljövariabeln kan du behöva starta om alla program som körs och som behöver läsa in miljövariabeln, däribland konsolfönstret. Om du t.ex. använder Visual Studio som redigeringsprogram, så starta om Visual Studio innan du kör exemplet. 

### <a name="linux"></a>Linux

```bash
export storageconnectionstring=<yourconnectionstring>
```

När du har lagt till miljövariabeln så kör `source ~/.bashrc` från konsolfönstret så att ändringarna träder i kraft.

### <a name="macos"></a>macOS

Redigera din .bash_profile och lägg till miljövariabeln:

```bash
export STORAGE_CONNECTION_STRING=<yourconnectionstring>
```

När du har lagt till miljövariabeln så kör `source .bash_profile` från konsolfönstret så att ändringarna träder i kraft.

## <a name="run-the-sample"></a>Kör exemplet

Exempelprogrammet skapar en kö och lägger till ett meddelande i den. Programmet tittar först på meddelandet utan att ta bort det från kön och hämtar sedan meddelandet och tar bort det från kön.

### <a name="windows"></a>Windows

Om du använder Visual Studio som redigeringsprogram kan du köra genom att trycka på **F5**. 

I annat fall går du till programkatalogen och kör programmet med kommandot `dotnet run`.

```
dotnet run
```

### <a name="linux"></a>Linux

Gå till programkatalogen och kör programmet med kommandot `dotnet run`.

```
dotnet run
```

### <a name="macos"></a>macOS

Gå till programkatalogen och kör programmet med kommandot `dotnet run`.

```
dotnet run
```

Exempelprogrammets utdata ser ut ungefär som i följande exempel:

```
Azure Queues - .NET Quickstart sample

Created queue 'quickstartqueues-3136fe9a-fa52-4b19-a447-8999a847da52'

Added message 'aa8fa95f-07ea-4df7-bf86-82b3f7debfb7' to queue 'quickstartqueues-3136fe9a-fa52-4b19-a447-8999a847da52'
Message insertion time: 2/7/2019 4:30:46 AM +00:00
Message expiration time: 2/14/2019 4:30:46 AM +00:00

Contents of peeked message 'aa8fa95f-07ea-4df7-bf86-82b3f7debfb7': Hello, World

Message 'aa8fa95f-07ea-4df7-bf86-82b3f7debfb7' becomes visible again at 2/7/2019 4:31:16 AM +00:00

Processed and deleted message 'aa8fa95f-07ea-4df7-bf86-82b3f7debfb7'

Press any key to delete the sample queue.
```

## <a name="understand-the-sample-code"></a>Förstå exempelkoden

Härnäst får du utforska exempelkoden så att du förstår hur den fungerar.

### <a name="try-parsing-the-connection-string"></a>Försök att parsa anslutningssträngen

Exemplet kontrollera först att miljövariabeln innehåller en anslutningssträng som kan parsas för att skapa ett [CloudStorageAccount](/dotnet/api/microsoft.azure.cosmos.table.cloudstorageaccount)-objekt som pekar på lagringskontot. För att kontrollera att anslutningssträngen är giltig använder exemplet metoden [TryParse](/dotnet/api/microsoft.azure.cosmos.table.cloudstorageaccount.tryparse). Om **TryParse** lyckas, initieras variabeln *storageAccount* och **SANT** returneras.

```csharp
// Retrieve the connection string for use with the application. The storage connection string is stored
// in an environment variable called storageconnectionstring, on the machine where the application is running.
// If the environment variable is created after the application is launched in a console or with Visual
// Studio, the shell needs to be closed and reloaded to take the environment variable into account.
string storageConnectionString = Environment.GetEnvironmentVariable("storageconnectionstring");

// Check whether the connection string can be parsed.
if (CloudStorageAccount.TryParse(storageConnectionString, out storageAccount))
{
    // If the connection string is valid, proceed with calls to Azure Queues here.
    ...    
}
else
{
    Console.WriteLine(
        "A connection string has not been defined in the system environment variables. " +
        "Add an environment variable named 'storageconnectionstring' with your storage " +
        "connection string as a value.");
}
```

### <a name="create-the-queue"></a>Skapa kön

Först skapar exemplet en kö och lägger till ett meddelande i den. 

```csharp
// Create a queue called 'quickstartqueues' and append a GUID value so that the queue name 
// is unique in your storage account. 
queue = cloudQueueClient.GetQueueReference("quickstartqueues-" + Guid.NewGuid().ToString());
await queue.CreateAsync();

Console.WriteLine("Created queue '{0}'", queue.Name);
Console.WriteLine();
```

### <a name="add-a-message"></a>Lägg till ett meddelande

Därefter lägger exemplet till ett meddelande sist i kön. 

Ett meddelande måste har ett format som stöds av en XML-begäran med UTF-8-kodning, och kan vara upp till 64 kB stort. Om ett meddelande som innehåller binära data, rekommenderar vi att du Base64-koda meddelandet.

Som standard är den högsta livslängden för ett meddelande inställt på 7 dagar. Du kan ange valfri positivt tal för meddelandet time to live.

```csharp
// Create a message and add it to the queue. Set expiration time to 14 days.
CloudQueueMessage message = new CloudQueueMessage("Hello, World");
await queue.AddMessageAsync(message, new TimeSpan(14,0,0,0), null, null, null);
Console.WriteLine("Added message '{0}' to queue '{1}'", message.Id, queue.Name);
Console.WriteLine("Message insertion time: {0}", message.InsertionTime.ToString());
Console.WriteLine("Message expiration time: {0}", message.ExpirationTime.ToString());
Console.WriteLine();
```

Om du vill lägga till ett meddelande som inte upphör att gälla, använda `Timespan.FromSeconds(-1)` i anrop till [AddMessageAsync](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.addmessageasync).

```csharp
await queue.AddMessageAsync(message, TimeSpan.FromSeconds(-1), null, null, null);
```

### <a name="peek-a-message-from-the-queue"></a>Granska ett meddelande i kön

Exemplet visar hur du kan granska ett meddelande i en kö. När du granskar ett meddelande kan du läsa innehållet i meddelandet. Meddelandet förblir synligt för andra klienter, så att en annan klient sedan kan hämta och bearbeta meddelandet.

```csharp
// Peek at the message at the front of the queue. Peeking does not alter the message's 
// visibility, so that another client can still retrieve and process it. 
CloudQueueMessage peekedMessage = await queue.PeekMessageAsync();

// Display the ID and contents of the peeked message.
Console.WriteLine("Contents of peeked message '{0}': {1}", peekedMessage.Id, peekedMessage.AsString);
Console.WriteLine();
```

### <a name="dequeue-a-message"></a>Ta bort ett meddelande från en kö

Exemplet visar också hur du tar bort ett meddelande från kön. När du tar bort ett meddelande från kön hämtar du meddelandet från början av kön och gör det tillfälligt osynligt för andra klienter. Som standard förblir ett meddelande osynligt i 30 sekunder. Under den tiden kan din kod bearbeta meddelandet. För att slutföra borttagningen av meddelandet i kön tar du bort meddelandet omedelbart efter bearbetningen, så att en annan klient inte tar bort samma meddelande från kön.

Om din kod inte kan bearbeta ett meddelande på grund av ett maskin- eller programvarufel, blir meddelandet synligt igen efter osynlighetsperioden. En annan klient kan hämta samma meddelande och försöka igen.

```csharp
// Retrieve the message at the front of the queue. The message becomes invisible for 
// a specified interval, during which the client attempts to process it.
CloudQueueMessage retrievedMessage = await queue.GetMessageAsync();

// Display the time at which the message will become visible again if it is not deleted.
Console.WriteLine("Message '{0}' becomes visible again at {1}", retrievedMessage.Id, retrievedMessage.NextVisibleTime);
Console.WriteLine();

//Process and delete the message within the period of invisibility.
await queue.DeleteMessageAsync(retrievedMessage);
Console.WriteLine("Processed and deleted message '{0}'", retrievedMessage.Id);
Console.WriteLine();
```

### <a name="clean-up-resources"></a>Rensa resurser

Exemplet rensar upp de resurser som det skapade genom att ta bort kön. När kön tas bort tas även eventuella meddelanden i kön bort.

```csharp
Console.WriteLine("Press any key to delete the sample queue.");
Console.ReadLine();
Console.WriteLine("Deleting the queue and any messages it contains...");
Console.WriteLine();
if (queue != null)
{
    await queue.DeleteIfExistsAsync();
}
```

## <a name="resources-for-developing-net-applications-with-queues"></a>Resurser för utveckling av .NET-program med köer

Se dessa ytterligare resurser för .NET-utveckling med Azure Queues:

### <a name="binaries-and-source-code"></a>Binärfiler och källkod

- Ladda ned NuGet-paket för den senaste versionen av den [Azure Storage-klientbiblioteket för .NET](/dotnet/api/overview/azure/storage/client)
    - [Common](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common/)
    - [köer](https://www.nuget.org/packages/Azure.Storage.Queues/)
- Visa [källkoden för .NET-klientbiblioteket](https://github.com/Azure/azure-storage-net) på GitHub.

### <a name="client-library-reference-and-samples"></a>Referens och exempel för klientbiblioteket

- Mer information om klientbiblioteket för .NET finns i [API-referens för .NET](https://docs.microsoft.com/dotnet/api/overview/azure/storage).
- Utforska [Queue Storage-exempel](https://azure.microsoft.com/resources/samples/?sort=0&service=storage&platform=dotnet&term=queues) som skrivits med .NET-klientbiblioteket.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten lärde du dig hur du lägger till meddelanden i en kö, granskar meddelanden från en kö och bearbetar och tar bort meddelanden från en kö med hjälp av .NET. 

> [!div class="nextstepaction"]
> [Kommunicera mellan program med Azure Queue Storage](https://docs.microsoft.com/learn/modules/communicate-between-apps-with-azure-queue-storage/index)

- Mer information om .NET Core finns i [Kom igång med .NET på 10 minuter](https://www.microsoft.com/net/learn/get-started/).
