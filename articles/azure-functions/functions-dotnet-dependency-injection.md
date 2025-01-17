---
title: Använd beroendeinmatning i .NET Azure Functions
description: Lär dig hur du använder beroendeinmatning för att registrera och använda tjänster i .NET-funktioner
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
keywords: Azure functions, funktioner, serverlös arkitektur
ms.service: azure-functions
ms.devlang: dotnet
ms.topic: reference
ms.date: 05/28/2019
ms.author: jehollan, cshoe
ms.openlocfilehash: 781bcdc158cb362b7c46e1ba9771b6a92ebc56a8
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/01/2019
ms.locfileid: "67479624"
---
# <a name="use-dependency-injection-in-net-azure-functions"></a>Använd beroendeinmatning i .NET Azure Functions

Azure Functions har stöd för beroende inmatning (DI) programvara designmönstret, vilket är en teknik för att uppnå [invertering-kontroll (IoC)](https://docs.microsoft.com/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) mellan klasser och deras beroenden.

Azure Functions bygger på Beroendeinmatning för ASP.NET Core-funktioner. Är medvetna om olika tjänster, livslängd och designmönster för [ASP.NET Core beroendeinmatning](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection) innan du använder DI funktioner i en Azure-funktioner appen rekommenderas.

Stöd för beroendeinmatning börjar med Azure Functions 2.x.

## <a name="prerequisites"></a>Förutsättningar

Innan du kan använda beroendeinmatning, måste du installera följande NuGet-paket:

- [Microsoft.Azure.Functions.Extensions](https://www.nuget.org/packages/Microsoft.Azure.Functions.Extensions/)

- [Microsoft.NET.Sdk.Functions-paketet](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions/) version 1.0.28 eller senare

- Valfritt: [Microsoft.Extensions.Http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) krävs endast för att registrera HttpClient vid start

## <a name="register-services"></a>Registrera tjänster

Du kan skapa en metod för att konfigurera och lägga till komponenter för att registrera tjänster genom en `IFunctionsHostBuilder` instans.  Värden för Azure Functions skapar en instans av `IFunctionsHostBuilder` och skickar dem direkt i din metod.

Om du vill registrera metoden lägger du till den `FunctionsStartup` sammansättningen attribut som anger namnet på används under starten. Även kod refererar till en förhandsversion av [Microsoft.Azure.Cosmos](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/) på Nuget.

```csharp
using System;
using Microsoft.Azure.Functions.Extensions.DependencyInjection;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Http;
using Microsoft.Extensions.Logging;
using Microsoft.Azure.Cosmos;

[assembly: FunctionsStartup(typeof(MyNamespace.Startup))]

namespace MyNamespace
{
    public class Startup : FunctionsStartup
    {
        public override void Configure(IFunctionsHostBuilder builder)
        {
            builder.Services.AddHttpClient();
            builder.Services.AddSingleton((s) => {
                return new CosmosClient(Environment.GetEnvironmentVariable("COSMOSDB_CONNECTIONSTRING"));
            });
            builder.Services.AddSingleton<ILoggerProvider, MyLoggerProvider>();
        }
    }
}
```

## <a name="use-injected-dependencies"></a>Använda inmatade beroenden

ASP.NET Core använder konstruktorn inmatning för att tillgängliggöra dina beroenden till din funktion. I följande exempel visas hur `IMyService` och `HttpClient` beroenden är införs i en HTTP-utlöst funktion.

```csharp
using System;
using System.IO;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;
using Newtonsoft.Json;

namespace MyNamespace
{
    public class HttpTrigger
    {
        private readonly IMyService _service;
        private readonly HttpClient _client;

        public HttpTrigger(IMyService service, IHttpClientFactory httpClientFactory)
        {
            _service = service;
            _client = httpClientFactory.CreateClient();;
        }

        [FunctionName("GetPosts")]
        public async Task<IActionResult> Get(
            [HttpTrigger(AuthorizationLevel.Function, "get", Route = "posts")] HttpRequest req,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");
            var res = await _client.GetAsync("https://microsoft.com");
            await _service.AddResponse(res);

            return new OkResult();
        }
    }
}
```

Användning av konstruktor inmatning innebär att du inte ska använda statiska funktioner om du vill dra nytta av beroendeinmatning.

## <a name="service-lifetimes"></a>Tjänstens livslängd

Azure Functions-appar tillhandahåller samma service-livslängd som [ASP.NET Beroendeinmatning](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection#service-lifetimes): tillfälliga, begränsade, och singleton.

I functions-app matchar en livslängd för begränsade tjänsten en livslängd för körning av funktionen. Begränsade tjänster skapas en gång per körning. Senare begäranden för tjänsten under körningen återanvända den befintliga tjänstinstansen. En singleton-tjänsten livslängd matchar värden livslängd och återanvändas i funktionskörningar på instansen.

Singleton-tjänsterna livslängd rekommenderas för anslutningar och klienter, till exempel `SqlConnection`, `CloudBlobClient`, eller `HttpClient` instanser.

Visa eller ladda ned en [prov på olika service livslängd](https://aka.ms/functions/di-sample) på GitHub.

## <a name="logging-services"></a>Loggningstjänster

Om du behöver en egen loggningsprovider det rekommenderade sättet är att registrera en `ILoggerProvider` instans. Application Insights läggs till automatiskt av Azure Functions.

> [!WARNING]
> Lägg inte till `AddApplicationInsightsTelemetry()` till samlingen tjänster som det registrerar tjänster som konflikt med tjänster som tillhandahålls av miljön.

## <a name="function-app-provided-services"></a>Funktionen tillhandahålls apptjänster

Funktionen värden registrerar många tjänster. Följande tjänster är säkert att ta som ett beroende i ditt program:

|Typ av tjänst|Livslängd|Beskrivning|
|--|--|--|
|`Microsoft.Extensions.Configuration.IConfiguration`|Singleton-instans|Runtime-konfiguration|
|`Microsoft.Azure.WebJobs.Host.Executors.IHostIdProvider`|Singleton-instans|Ansvarar för att tillhandahålla ID för värdinstans|

Om det finns andra tjänster som du vill skapa ett beroende på, [skapa ett ärende och föreslå dem på GitHub](https://github.com/azure/azure-functions-host).

### <a name="overriding-host-services"></a>Åsidosätta tjänster

Åsidosätta tjänster som tillhandahålls av värden stöds för närvarande inte.  Om det finns tjänster som du vill åsidosätta, [skapa ett ärende och föreslå dem på GitHub](https://github.com/azure/azure-functions-host).

## <a name="next-steps"></a>Nästa steg

Mer information finns i följande resurser:

- [Så här övervakar du din funktionsapp](functions-monitoring.md)
- [Metodtips för functions](functions-best-practices.md)
