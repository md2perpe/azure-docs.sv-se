---
title: Hantera externa händelser i varaktiga funktioner – Azure
description: Lär dig hur du hanterar externa händelser i tillägget varaktiga funktioner för Azure Functions.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: eb024e11b78d13d5ab4544c634acef2ade8141c2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60730794"
---
# <a name="handling-external-events-in-durable-functions-azure-functions"></a>Hantera externa händelser i varaktiga funktioner (Azure Functions)

Orchestrator-funktioner har möjlighet att vänta och lyssna efter externa händelser. Den här funktionen i [varaktiga funktioner](durable-functions-overview.md) kan ofta vara användbart för hantering av mänsklig interaktion eller andra externa utlösare.

> [!NOTE]
> Externa händelser är enkelriktade asynkrona åtgärder. De är inte lämplig för situationer där klienten skickar händelsen behöver en synkron svar från orchestrator-funktion.

## <a name="wait-for-events"></a>Vänta på händelser

Den [WaitForExternalEvent](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_WaitForExternalEvent_) metoden kan en orchestrator-funktion att vänta asynkront och lyssna efter en extern händelse. Funktionen lyssnande orchestrator deklarerar den *namn* händelsens och *formen* det förväntar sig att ta emot.

### <a name="c"></a>C#

```csharp
[FunctionName("BudgetApproval")]
public static async Task Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    bool approved = await context.WaitForExternalEvent<bool>("Approval");
    if (approved)
    {
        // approval granted - do the approved action
    }
    else
    {
        // approval denied - send a notification
    }
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (fungerar endast 2.x)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const approved = yield context.df.waitForExternalEvent("Approval");
    if (approved) {
        // approval granted - do the approved action
    } else {
        // approval denied - send a notification
    }
});
```

I föregående exempel lyssnar efter en viss enskild händelse och vidtar åtgärder när den tas emot.

Du kan lyssna efter flera händelser samtidigt, precis som i följande exempel, som väntar på en av tre möjliga händelsemeddelanden.

#### <a name="c"></a>C#

```csharp
[FunctionName("Select")]
public static async Task Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    var event1 = context.WaitForExternalEvent<float>("Event1");
    var event2 = context.WaitForExternalEvent<bool>("Event2");
    var event3 = context.WaitForExternalEvent<int>("Event3");

    var winner = await Task.WhenAny(event1, event2, event3);
    if (winner == event1)
    {
        // ...
    }
    else if (winner == event2)
    {
        // ...
    }
    else if (winner == event3)
    {
        // ...
    }
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript (fungerar endast 2.x)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const event1 = context.df.waitForExternalEvent("Event1");
    const event2 = context.df.waitForExternalEvent("Event2");
    const event3 = context.df.waitForExternalEvent("Event3");

    const winner = yield context.df.Task.any([event1, event2, event3]);
    if (winner === event1) {
        // ...
    } else if (winner === event2) {
        // ...
    } else if (winner === event3) {
        // ...
    }
});
```

Det tidigare exemplet lyssnar efter *alla* av flera händelser. Det är också möjligt att vänta tills *alla* händelser.

#### <a name="c"></a>C#

```csharp
[FunctionName("NewBuildingPermit")]
public static async Task Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    string applicationId = context.GetInput<string>();

    var gate1 = context.WaitForExternalEvent("CityPlanningApproval");
    var gate2 = context.WaitForExternalEvent("FireDeptApproval");
    var gate3 = context.WaitForExternalEvent("BuildingDeptApproval");

    // all three departments must grant approval before a permit can be issued
    await Task.WhenAll(gate1, gate2, gate3);

    await context.CallActivityAsync("IssueBuildingPermit", applicationId);
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript (fungerar endast 2.x)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const applicationId = context.df.getInput();

    const gate1 = context.df.waitForExternalEvent("CityPlanningApproval");
    const gate2 = context.df.waitForExternalEvent("FireDeptApproval");
    const gate3 = context.df.waitForExternalEvent("BuildingDeptApproval");

    // all three departments must grant approval before a permit can be issued
    yield context.df.Task.all([gate1, gate2, gate3]);

    yield context.df.callActivity("IssueBuildingPermit", applicationId);
});
```

[WaitForExternalEvent](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_WaitForExternalEvent_) väntar på obestämd tid tills några indata.  Funktionsappen kan tas bort från minnet väntan på ett säkert sätt. Om en händelse tas emot för den här orchestration-instansen, och när den aktiveras installationen automatiskt och omedelbart bearbetar händelsen.

> [!NOTE]
> Om din funktionsapp använder den Standardförbrukningsplanen kan inga fakturering avgifter tillkommer när en uppgift från väntar på en orchestrator-funktion `WaitForExternalEvent` (.NET) eller `waitForExternalEvent` (JavaScript), oavsett hur länge den ska vänta.

I .NET, om händelsenyttolast inte kan konverteras till den förväntade typen `T`, genereras ett undantagsfel.

## <a name="send-events"></a>Skicka händelser

Den [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_) -metoden för den [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) klass skickar händelser som `WaitForExternalEvent` (.NET) eller `waitForExternalEvent` (JavaScript) väntar.  Den `RaiseEventAsync` metoden tar *eventName* och *eventData* som parametrar. Informationen om händelsen måste vara JSON-serialiserbara.

Nedan visas ett exempel kö-utlöst funktion som skickar en ”godkännande”-händelse till en instans för orchestrator-funktion. Orchestration-instans-ID kommer från brödtexten i kömeddelandet.

### <a name="c"></a>C#

```csharp
[FunctionName("ApprovalQueueProcessor")]
public static async Task Run(
    [QueueTrigger("approval-queue")] string instanceId,
    [OrchestrationClient] DurableOrchestrationClient client)
{
    await client.RaiseEventAsync(instanceId, "Approval", true);
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (fungerar endast 2.x)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);
    await client.raiseEvent(instanceId, "Approval", true);
};
```

Internt `RaiseEventAsync` (.NET) eller `raiseEvent` (JavaScript) placerar det i kö ett meddelande som hämtar slutpunktsstatus uppfattas av orchestrator-funktion för att vänta. Om instansen inte väntar på den angivna *händelsenamn,* händelsemeddelandet läggs till i en kö i minnet. Om orchestration-instans senare börjar lyssna efter som *händelsenamn,* det kontrollerar kön för händelsemeddelanden.

> [!NOTE]
> Om det finns ingen orchestration-instans med det angivna *instans-ID*, händelsemeddelandet ignoreras. Mer information om det här problemet finns i den [GitHub-ärende](https://github.com/Azure/azure-functions-durable-extension/issues/29). 

> [!WARNING]
> När du utvecklar lokalt i JavaScript, behöver du ställa in miljövariabeln `WEBSITE_HOSTNAME` till `localhost:<port>`, t.ex. `localhost:7071` att använda metoder på `DurableOrchestrationClient`. Mer information om det här kravet finns i den [GitHub-ärende](https://github.com/Azure/azure-functions-durable-js/issues/28).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig hur du ställer in eternal-orkestreringar](durable-functions-eternal-orchestrations.md)

> [!div class="nextstepaction"]
> [Köra ett exempel som väntar på externa händelser](durable-functions-phone-verification.md)

> [!div class="nextstepaction"]
> [Köra ett exempel som väntar mänsklig interaktion](durable-functions-phone-verification.md)