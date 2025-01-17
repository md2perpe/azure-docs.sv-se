---
title: JavaScript-utvecklare för Azure Functions | Microsoft Docs
description: Förstå hur du utvecklar funktioner med hjälp av JavaScript.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: azure-funktioner, funktioner, händelsebearbetning, webhooks, dynamisk beräkning, serverlös arkitektur
ms.assetid: 45dedd78-3ff9-411f-bb4b-16d29a11384c
ms.service: azure-functions
ms.devlang: nodejs
ms.topic: reference
ms.date: 02/24/2019
ms.author: glenga
ms.openlocfilehash: 9a7c186f7c5fb46078eaa5729e79fdcc256ecc6d
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67460203"
---
# <a name="azure-functions-javascript-developer-guide"></a>Utvecklarguide för Azure Functions JavaScript

Den här guiden innehåller information om krångla skriva Azure Functions med JavaScript.

En JavaScript-funktion är en exporterad `function` som körs när den utlöses ([utlösare har konfigurerats i function.json](functions-triggers-bindings.md)). Det första argumentet som skickas till alla funktioner är en `context` objektet som används för mottagning och skicka bindningsdata, loggning och kommunicera med körningen.

Den här artikeln förutsätter att du redan har läst den [Azure Functions för utvecklare](functions-reference.md). Slutför Functions-Snabbstart för att skapa din första funktion med hjälp av [Visual Studio Code](functions-create-first-function-vs-code.md) eller [i portalen](functions-create-first-azure-function.md).

Den här artikeln stöder också [TypeScript apputveckling](#typescript).

## <a name="folder-structure"></a>mappstruktur

Det ser ut som följande nödvändiga mappstrukturen för en JavaScript-projektet. Du kan ändra denna standardinställning. Mer information finns i den [skriptfil](#using-scriptfile) nedan.

```
FunctionsProject
 | - MyFirstFunction
 | | - index.js
 | | - function.json
 | - MySecondFunction
 | | - index.js
 | | - function.json
 | - SharedCode
 | | - myFirstHelperFunction.js
 | | - mySecondHelperFunction.js
 | - node_modules
 | - host.json
 | - package.json
 | - extensions.csproj
```

I roten av projektet, det finns en delad [host.json](functions-host-json.md) -fil som kan användas för att konfigurera funktionsappen. Varje funktion har en mapp med en egen fil med kod (.js) och bindningen konfigurationsfil (function.json). Namnet på `function.json`'s överordnad katalog är alltid namnet på din funktion.

Bindningen-tillägg som krävs i [version 2.x](functions-versions.md) funktioner runtime definieras i den `extensions.csproj` -fil med faktiska library-filer i den `bin` mapp. När du utvecklar lokalt, måste du [registrera tillägg av bindning](./functions-bindings-register.md#extension-bundles). När du utvecklar funktioner i Azure-portalen görs denna registrering för dig.

## <a name="exporting-a-function"></a>Exportera en funktion

JavaScript-funktioner måste exporteras [ `module.exports` ](https://nodejs.org/api/modules.html#modules_module_exports) (eller [ `exports` ](https://nodejs.org/api/modules.html#modules_exports)). Din exporterade funktion ska vara en JavaScript-funktion som körs när den utlöses.

Som standard söker Functions-körning efter din funktion i `index.js`, där `index.js` delar samma överordnad katalog som dess motsvarande `function.json`. I standard-fall din exporterade funktion ska vara den enda exporten från dess fil eller export med namnet `run` eller `index`. Om du vill konfigurera filens plats och exportera namnet på din funktion, Läs om [konfigurerar din funktionsadressen](functions-reference-node.md#configure-function-entry-point) nedan.

Exporterade funktionen skickas ett antal argument på körning. Det första argumentet som det tar är alltid en `context` objekt. Om din funktion är synkron (inte returnerar ett löfte), måste du ange den `context` objekt anropar bara `context.done` krävs för korrekt användning.

```javascript
// You should include context, other arguments are optional
module.exports = function(context, myTrigger, myInput, myOtherInput) {
    // function logic goes here :)
    context.done();
};
```

### <a name="exporting-an-async-function"></a>Exportera en async-funktion
När du använder den [ `async function` ](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/async_function) deklarationen eller vanlig JavaScript [löften](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) i version 2.x av Functions-körning, du behöver inte explicit anropa den [ `context.done` ](#contextdone-method) återanrop för att signalera att funktionen har slutförts. Funktionen har slutförts när exporterade async-funktion/löftet har slutförts. För funktioner som riktar in sig på version 1.x-körningen, måste du fortfarande anropa [ `context.done` ](#contextdone-method) när koden är klar körs.

I följande exempel är en enkel funktion som loggar den utlöstes och omedelbart är slutfört.

```javascript
module.exports = async function (context) {
    context.log('JavaScript trigger function processed a request.');
};
```

När du exporterar en async-funktion kan du också konfigurera en utdatabindning för att ta den `return` värde. Detta rekommenderas om du bara har en utdatabindning.

Tilldela en utdata med hjälp av `return`, ändra den `name` egenskap `$return` i `function.json`.

```json
{
  "type": "http",
  "direction": "out",
  "name": "$return"
}
```

I det här fallet din funktion bör se ut som i följande exempel:

```javascript
module.exports = async function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');
    // You can call and await an async method here
    return {
        body: "Hello, world!"
    };
}
```

## <a name="bindings"></a>Bindningar 
I JavaScript, [bindningar](functions-triggers-bindings.md) konfigureras och definieras i en funktion function.json. Funktioner som interagerar med bindningar flera olika sätt.

### <a name="inputs"></a>Indata
Indata är indelade i två kategorier i Azure Functions: en är indata för arbetsflödesutlösaren och den andra är ytterligare indata. Utlösare och andra indata Bindningar (bindningarna för `direction === "in"`) kan läsas av en funktion på tre sätt:
 - ** _(Rekommenderas)_  Som parametrarna som skickades till funktionen.** De skickas till funktionen i samma ordning som de har definierats i *function.json*. Den `name` egenskapen som definierats i *function.json* behöver inte matcha namnet på parametern, även om den ska.
 
   ```javascript
   module.exports = async function(context, myTrigger, myInput, myOtherInput) { ... };
   ```
   
 - **Medlemmar i den [ `context.bindings` ](#contextbindings-property) objekt.** Varje medlem heter genom den `name` egenskapen som definierats i *function.json*.
 
   ```javascript
   module.exports = async function(context) { 
       context.log("This is myTrigger: " + context.bindings.myTrigger);
       context.log("This is myInput: " + context.bindings.myInput);
       context.log("This is myOtherInput: " + context.bindings.myOtherInput);
   };
   ```
   
 - **Som indata med hjälp av JavaScript [ `arguments` ](https://msdn.microsoft.com/library/87dw3w1k.aspx) objekt.** Detta är i princip detsamma som att skicka indata som parametrar, men kan du dynamiskt hantera indata.
 
   ```javascript
   module.exports = async function(context) { 
       context.log("This is myTrigger: " + arguments[1]);
       context.log("This is myInput: " + arguments[2]);
       context.log("This is myOtherInput: " + arguments[3]);
   };
   ```

### <a name="outputs"></a>outputs
Utdata (bindningarna för `direction === "out"`) kan skrivas till av en funktion på flera olika sätt. I samtliga fall den `name` egenskapen för bindningen som definierats i *function.json* motsvarar namnet på medlemmen objekt skrivs till i din funktion. 

Du kan tilldela data till utdatabindningar i något av följande sätt (inte kombinera dessa metoderna):

- ** _(Rekommenderas för flera utdata)_  Returnerar ett objekt.** Om du använder en asynkron/löftet som returnerar funktionen, kan du returnera ett objekt med tilldelade utdata. I exemplet nedan visas utdatabindningar namnges ”httpResponse” och ”queueOutput” i *function.json*.

  ```javascript
  module.exports = async function(context) {
      let retMsg = 'Hello, world!';
      return {
          httpResponse: {
              body: retMsg
          },
          queueOutput: retMsg
      };
  };
  ```

  Om du använder en synkron funktion kan du gå tillbaka det här objektet med [ `context.done` ](#contextdone-method) (se exemplet).
- ** _(Rekommenderas för enkel utdata)_  Returnera ett värde direkt och använda bindningsnamn $return.** Detta fungerar endast för asynkrona/löftet returnerar funktioner. Se exemplet i [exporterar en async-funktion](#exporting-an-async-function). 
- **Tilldela värden till `context.bindings`**  du kan tilldela värden direkt till context.bindings.

  ```javascript
  module.exports = async function(context) {
      let retMsg = 'Hello, world!';
      context.bindings.httpResponse = {
          body: retMsg
      };
      context.bindings.queueOutput = retMsg;
      return;
  };
  ```

### <a name="bindings-data-type"></a>Bindningar datatyp

För att definiera datatypen för en indatabindning använder den `dataType` -egenskapen i Bindningsdefinitionen för. Till exempel använda typen för att läsa innehållet i en HTTP-förfrågan i binärformat `binary`:

```json
{
    "type": "httpTrigger",
    "name": "req",
    "direction": "in",
    "dataType": "binary"
}
```

Alternativ för `dataType` är: `binary`, `stream`, och `string`.

## <a name="context-object"></a>Context-objektet
Körningen använder en `context` objekt att skicka data till och från din funktion och så att du kan kommunicera med körningen. Context-objektet kan användas för läsning och data från bindningar, skriva loggar och med den `context.done` motringning när din exporterade funktion är synkrona.

Den `context` objekt alltid är den första parametern för en funktion. Den bör inkluderas eftersom den har viktiga metoder som `context.done` och `context.log`. Du kan kalla objektet vad du vill ha (till exempel `ctx` eller `c`).

```javascript
// You must include a context, but other arguments are optional
module.exports = function(ctx) {
    // function logic goes here :)
    ctx.done();
};
```

### <a name="contextbindings-property"></a>Context.Bindings egenskap

```js
context.bindings
```

Returnerar en namngiven objekt som används för att läsa eller tilldela bindningsdata. Indata och utlösare binda data kan nås genom att läsa egenskaper på `context.bindings`. Bindning av utdata kan tilldelas genom att lägga till data till `context.bindings`

Till exempel följande bindningsdefinitionerna i din function.json kan du få åtkomst till innehållet i en kö från `context.bindings.myInput` och tilldela utdata till en kö med hjälp av `context.bindings.myOutput`.

```json
{
    "type":"queue",
    "direction":"in",
    "name":"myInput"
    ...
},
{
    "type":"queue",
    "direction":"out",
    "name":"myOutput"
    ...
}
```

```javascript
// myInput contains the input data, which may have properties such as "name"
var author = context.bindings.myInput.name;
// Similarly, you can set your output data
context.bindings.myOutput = { 
        some_text: 'hello world', 
        a_number: 1 };
```

Du kan välja att definiera utdata bindning data med den `context.done` metoden i stället för den `context.binding` objekt (se nedan).

### <a name="contextbindingdata-property"></a>context.bindingData egenskap

```js
context.bindingData
```

Returnerar en namngiven objekt som innehåller data för utlösaren metadata och funktionen anrop (`invocationId`, `sys.methodName`, `sys.utcNow`, `sys.randGuid`). Ett exempel på utlösaren metadata finns i den här [event hubs exempel](functions-bindings-event-hubs.md#trigger---javascript-example).

### <a name="contextdone-method"></a>Context.Done metod

```js
context.done([err],[propertyBag])
```

Gör att runtimes vet att din kod har slutförts. När funktionen använder den [ `async function` ](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/async_function) deklarationen du behöver inte använda `context.done()`. Den `context.done` anropas implicit återanrop. Async-funktioner är tillgängliga i noden 8 eller senare, vilket kräver version 2.x av Functions-körning.

Om funktionen inte är en async-funktion, **måste du anropa** `context.done` att informera körningen att funktionen har slutförts. Körningstider reda på om den saknas.

Den `context.done` metoden kan du ange både en användardefinierad fel att körningen och ett JSON-objekt som innehåller utdata-bindning. Egenskaper som skickas till `context.done` skriva över vad som helst på den `context.bindings` objekt.

```javascript
// Even though we set myOutput to have:
//  -> text: 'hello world', number: 123
context.bindings.myOutput = { text: 'hello world', number: 123 };
// If we pass an object to the done function...
context.done(null, { myOutput: { text: 'hello there, world', noNumber: true }});
// the done method overwrites the myOutput binding to be: 
//  -> text: 'hello there, world', noNumber: true
```

### <a name="contextlog-method"></a>Context.log metod  

```js
context.log(message)
```

Gör att du kan skriva till direktuppspelningsloggarna funktion på standardnivå för spårningen. På `context.log`, ytterligare loggning metoder är tillgängliga som gör att du sparar funktionsloggar på andra spårningsnivåer:


| Metod                 | Beskrivning                                |
| ---------------------- | ------------------------------------------ |
| **error(_message_)**   | Skriver till Felnivån loggningen eller lägre.   |
| **warn(_message_)**    | Skriver till varningsnivå loggningen eller lägre. |
| **info(_message_)**    | Skriver till info-nivån loggningen eller lägre.    |
| **verbose(_message_)** | Skriver till utförlig loggning för nivån.           |

I följande exempel skriver en logg vid spårningsnivån varning:

```javascript
context.log.warn("Something has happened."); 
```

Du kan [konfigurera spårningsnivå tröskelvärdet för loggning](#configure-the-trace-level-for-console-logging) i host.json-filen. Mer information om hur du skriver loggar finns i [skriva trace utdata](#writing-trace-output-to-the-console) nedan.

Läs [övervaka Azure Functions](functions-monitoring.md) vill veta mer om att visa och fråga funktionsloggar.

## <a name="writing-trace-output-to-the-console"></a>Skrivning spårningsutdata till konsolen 

I funktioner, använder du den `context.log` metoder för att skriva spårningsutdata till konsolen. I funktioner v2.x trace matar du ut med hjälp av `console.log` samlas på Funktionsapp-nivå. Det innebär att utdata från `console.log` inte är knutna till en specifik funktionsanrop och visas inte i en viss funktion loggar. De, men spridas till Application Insights. Du kan inte använda i Functions v1.x `console.log` att skriva till konsolen.

När du anropar `context.log()`, meddelandet skrivs till konsolen vid spårningsnivån standard, vilket är den _info_ spårningsnivå. Följande kod skriver till konsolen vid spårningsnivån info:

```javascript
context.log({hello: 'world'});  
```

Den här koden motsvarar koden ovan:

```javascript
context.log.info({hello: 'world'});  
```

Den här koden skriver till konsolen vid Felnivån:

```javascript
context.log.error("An error has occurred.");  
```

Eftersom _fel_ är högsta spårningen loggningsnivån genom den här skrivs spåret till utdata i alla spårningsnivåer som har aktiverats.

Alla `context.log` metoderna stöder samma parameter-format som stöds av Node.js [util.format metoden](https://nodejs.org/api/util.html#util_util_format_format). Titta på följande kod som skriver funktionsloggar med hjälp av standard spårningsnivån:

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=' + req.originalUrl);
context.log('Request Headers = ' + JSON.stringify(req.headers));
```

Du kan också skriva samma kod i följande format:

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=%s', req.originalUrl);
context.log('Request Headers = ', JSON.stringify(req.headers));
```

### <a name="configure-the-trace-level-for-console-logging"></a>Konfigurera Spårningsnivån för konsolen loggning

Functions 1.x kan du ange tröskelvärdet Spårningsnivån för skrivning till konsolen, vilket gör det enkelt att styra hur spårningar skrivs till konsolen från din funktion. Ange tröskelvärdet för alla spårningar som skrivs till konsolen och den `tracing.consoleLevel` egenskap i host.json-fil. Den här inställningen gäller för alla funktioner i din funktionsapp. I följande exempel anger tröskelvärdet spårning för att aktivera utförlig loggning:

```json
{
    "tracing": {
        "consoleLevel": "verbose"
    }
}  
```

Värden för **consoleLevel** motsvarar namnen på de `context.log` metoder. För att inaktivera alla spårningsloggning till konsolen, ange **consoleLevel** till _av_. Mer information finns i [referens för host.json](functions-host-json-v1.md).

## <a name="http-triggers-and-bindings"></a>HTTP-utlösare och bindningar

HTTP- och webhook-utlösare och HTTP-utdata bindningar använda begäranden och svar-objekt som representerar den HTTP-meddelanden.  

### <a name="request-object"></a>Objekt

Den `context.req` (begäran) objekt har följande egenskaper:

| Egenskap      | Beskrivning                                                    |
| ------------- | -------------------------------------------------------------- |
| _body_        | Ett objekt som innehåller brödtext för begäran.               |
| _headers_     | Ett objekt som innehåller de begärda rubrikerna.                   |
| _method_      | HTTP-metod för begäran.                                |
| _originalUrl_ | URL för begäran.                                        |
| _params_      | Ett objekt som innehåller parametrarna routning av begäran. |
| _query_       | Ett objekt som innehåller frågeparametrarna.                  |
| _rawBody_     | Brödtexten i meddelandet som en sträng.                           |


### <a name="response-object"></a>Svarsobjekt

Den `context.res` ()-svarsobjekt har följande egenskaper:

| Egenskap  | Beskrivning                                               |
| --------- | --------------------------------------------------------- |
| _body_    | Ett objekt som innehåller brödtexten i svaret.         |
| _headers_ | Ett objekt som innehåller svarshuvuden.             |
| _isRaw_   | Anger att formatering hoppas för svaret.    |
| _status_  | HTTP-statuskod i svaret.                     |

### <a name="accessing-the-request-and-response"></a>Åtkomst till begäranden och svar 

När du arbetar med HTTP-utlösare kan komma du åt HTTP-begäranden och svar-objekt på flera olika sätt:

+ **Från `req` och `res` egenskaper på den `context` objekt.** På så sätt kan du använda det vanliga mönstret att komma åt HTTP data från context-objektet i stället för att använda fullständiga `context.bindings.name` mönster. I följande exempel visas hur du kommer åt den `req` och `res` objekt på den `context`:

    ```javascript
    // You can access your http request off the context ...
    if(context.req.body.emoji === ':pizza:') context.log('Yay!');
    // and also set your http response
    context.res = { status: 202, body: 'You successfully ordered more coffee!' }; 
    ```

+ **Från namngivna indata och utdatabindningar.** På så sätt kan fungerar HTTP-utlösare och bindningar på samma som för andra bindningen. I följande exempel anges svarsobjekt med hjälp av en namngiven `response` bindning: 

    ```json
    {
        "type": "http",
        "direction": "out",
        "name": "response"
    }
    ```
    ```javascript
    context.bindings.response = { status: 201, body: "Insert succeeded." };
    ```
+ ** _[Endast svar]_  Genom att anropa `context.res.send(body?: any)`.** Ett HTTP-svar skapas med indata `body` som svarstexten. `context.done()` anropas implicit.

+ ** _[Endast svar]_  Genom att anropa `context.done()`.** En särskild typ av HTTP-bindning returnerar ett svar som skickas till den `context.done()` metoden. Följande HTTP-utdatabindning definierar en `$return` utdataparameter:

    ```json
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
    ``` 
    ```javascript
     // Define a valid response object.
    res = { status: 201, body: "Insert succeeded." };
    context.done(null, res);   
    ```  

## <a name="node-version"></a>Nodversionen

I följande tabell visas Node.js-version som används av varje huvudversion av Functions-körning:

| Functions-version | Node.js-version | 
|---|---|
| 1.x | 6.11.2 (låst av körningen) |
| 2.x  | _Aktiva LTS_ och _Underhåll LTS_ Node.js-versioner (8.11.1 och 10.14.1 rekommenderas). Ange version med hjälp av WEBSITE_NODE_DEFAULT_VERSION [appinställningen](functions-how-to-use-azure-function-app-settings.md#settings).|

Du kan se den aktuella versionen som körningen använder genom att kontrollera inställningarna ovan appen eller genom att skriva ut `process.version` från valfri funktion.

## <a name="dependency-management"></a>Beroendehantering
För att kunna använda community-biblioteken i JavaScript-koden som visas i det exemplet nedan måste du kontrollera att alla beroenden är installerade på din Funktionsapp i Azure.

```javascript
// Import the underscore.js library
var _ = require('underscore');
var version = process.version; // version === 'v6.5.0'

module.exports = function(context) {
    // Using our imported underscore.js library
    var matched_names = _
        .where(context.bindings.myInput.names, {first: 'Carla'});
```

> [!NOTE]
> Du bör definiera en `package.json` i roten på din Funktionsapp. Definiera filen kan alla funktioner i appen som delar samma cachelagrade paket, vilket ger bästa prestanda. Om det uppstår en versionskonflikt, du kan lösa problemet genom att lägga till en `package.json` filen i mappen för en specifik funktion.  

När du distribuerar Funktionsappar från källkontroll, alla `package.json` filen finns i ditt lager, utlöser en `npm install` i en mapp under distributionen. Men när du distribuerar via portalen eller CLI så du måste manuellt installera paket.

Det finns två sätt att installera paket på din Funktionsapp: 

### <a name="deploying-with-dependencies"></a>Distribuera med beroenden
1. Installera alla nödvändiga paket lokalt genom att köra `npm install`.

2. Distribuera din kod och se till att den `node_modules` mappen ingår i distributionen. 


### <a name="using-kudu"></a>Med Kudu
1. Gå till `https://<function_app_name>.scm.azurewebsites.net`.

2. Klicka på **felsöka konsolen** > **CMD**.

3. Gå till `D:\home\site\wwwroot`, och dra sedan package.json-fil till den **wwwroot** mapp på den övre delen av sidan.  
    Du kan också överföra filer till din funktionsapp på andra sätt. Mer information finns i [så här uppdaterar du funktionen appfiler](functions-reference.md#fileupdate). 

4. När package.json-fil har överförts, kör den `npm install` i den **Kudu-konsolen för fjärrkörning**.  
    Den här åtgärden hämtar de paket som anges i package.json-fil och startar om appen.

## <a name="environment-variables"></a>Miljövariabler

I funktioner, [appinställningar](functions-app-settings.md), till exempel tjänstanslutning strängar, visas som miljövariabler under körning. Du kan komma åt de här inställningarna med hjälp av `process.env`, som visas här i andra och tredje anrop till `context.log()` där vi loggar den `AzureWebJobsStorage` och `WEBSITE_SITE_NAME` miljövariabler:

```javascript
module.exports = async function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    context.log('Node.js timer trigger function ran!', timeStamp);
    context.log("AzureWebJobsStorage: " + process.env["AzureWebJobsStorage"]);
    context.log("WEBSITE_SITE_NAME: " + process.env["WEBSITE_SITE_NAME"]);
};
```

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

När du kör lokalt appinställningar läses från den [local.settings.json](functions-run-local.md#local-settings-file) projektfilen.

## <a name="configure-function-entry-point"></a>Konfigurera funktionens startadress

Den `function.json` egenskaper `scriptFile` och `entryPoint` kan användas för att konfigurera platsen och namnet på din exporterade funktion. De här egenskaperna kan vara viktigt när din JavaScript är transpiled.

### <a name="using-scriptfile"></a>Med hjälp av `scriptFile`

Som standard körs en JavaScript-funktion från `index.js`, en fil som delar samma överordnad katalog som dess motsvarande `function.json`.

`scriptFile` kan användas för att få en mappstruktur som ser ut som i följande exempel:

```
FunctionApp
 | - host.json
 | - myNodeFunction
 | | - function.json
 | - lib
 | | - sayHello.js
 | - node_modules
 | | - ... packages ...
 | - package.json
```

Den `function.json` för `myNodeFunction` bör innehålla en `scriptFile` egenskap som pekar på filen med exporterade funktionen ska köras.

```json
{
  "scriptFile": "../lib/sayHello.js",
  "bindings": [
    ...
  ]
}
```

### <a name="using-entrypoint"></a>Med hjälp av `entryPoint`

I `scriptFile` (eller `index.js`), en funktion måste exporteras med `module.exports` för att hitta och köra. Som standard är den funktion som körs när den utlöses endast export från filen, export med namnet `run`, eller export med namnet `index`.

Detta kan konfigureras med hjälp av `entryPoint` i `function.json`, som i följande exempel:

```json
{
  "entryPoint": "logFoo",
  "bindings": [
    ...
  ]
}
```

I funktioner v2.x som har stöd för den `this` parameter i användarfunktioner Funktionskoden kan sedan som i följande exempel:

```javascript
class MyObj {
    constructor() {
        this.foo = 1;
    };

    logFoo(context) { 
        context.log("Foo is " + this.foo); 
        context.done(); 
    }
}

const myObj = new MyObj();
module.exports = myObj;
```

I det här exemplet är det viktigt att Observera att även om ett objekt ska exporteras, det finns inga garantier om du vill bevara tillstånd mellan körningar.

## <a name="local-debugging"></a>Lokal felsökning

När startar med den `--inspect` parameter, en Node.js-process lyssnar efter en felsökning klient på den angivna porten. I Azure Functions 2.x kan du ange argument som ska skickas till Node.js-processen som kör koden genom att lägga till miljövariabeln eller Appinställningen `languageWorkers:node:arguments = <args>`. 

Om du vill felsöka lokalt, lägger du till `"languageWorkers:node:arguments": "--inspect=5858"` under `Values` i din [local.settings.json](https://docs.microsoft.com/azure/azure-functions/functions-run-local#local-settings-file) filen och koppla en felsökare till port 5858.

När du felsöker använder VS Code, den `--inspect` parameter läggs automatiskt med hjälp av den `port` värdet i projektets launch.json-filen.

I version 1.x, ställa in `languageWorkers:node:arguments` fungerar inte. Felsökningsport kan väljas med den [ `--nodeDebugPort` ](https://docs.microsoft.com/azure/azure-functions/functions-run-local#start) parametern på Azure Functions Core Tools.

## <a name="typescript"></a>TypeScript

När du använder version 2.x av Functions-körning både [Azure Functions för Visual Studio Code](functions-create-first-function-vs-code.md) och [Azure Functions Core Tools](functions-run-local.md) kan du skapa funktionsappar med hjälp av en mall som har stöd för TypeScript-funktion app-projekt. Mallen genererar `package.json` och `tsconfig.json` projektfiler som gör det enklare att transpile, köra och publicera JavaScript-funktioner från TypeScript-kod med dessa verktyg.

En generated `.funcignore` filen används för att ange vilka filer utesluts när ett projekt publiceras till Azure.  

TypeScript-filer (TS) är transpiled i JavaScript-filer (.js) i den `dist` utdatakatalog. TypeScript mallar använder den [ `scriptFile` parametern](#using-scriptfile) i `function.json` att ange platsen för motsvarande .js-fil i den `dist` mapp. Platsen har angetts i mallen med hjälp av `outDir` parametern i den `tsconfig.json` filen. Om du ändrar den här inställningen eller namnet på mappen kan körningen inte hitta koden som ska köras.

> [!NOTE]
> Experimentella stöd för TypeScript finns version 1.x av Functions-körning. Experimentella version transpiles TypeScript-filer i JavaScript-filer när funktionen anropas. I version 2.x kan det här experimentella stödet har ersatts av verktyget-drivna-metoden som gör transpilation innan värden har initierats och under distributionsprocessen.

Det sätt som du utvecklar lokalt och distribuera från en TypeScript-projektet beror på din utvecklingsverktyg.

### <a name="visual-studio-code"></a>Visual Studio-koden

Den [Azure Functions för Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) tillägg kan du utveckla dina funktioner med TypeScript. De viktigaste verktygen är ett krav för Azure Functions-tillägget.

Om du vill skapa en funktionsapp i TypeScript i Visual Studio Code, Välj `TypeScript` som ditt språk när du skapar en funktionsapp.

När du trycker på **F5** för att köra appen lokalt, transpilation görs innan värden (func.exe) har initierats. 

När du distribuerar appen till Azure med den **distribuera funktionsappen...**  knapp, tillägget Azure Functions skapar först en produktionsklar version av JavaScript-filer från TypeScript källfiler.

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Du måste ange alternativet TypeScript språk när du skapar funktionsappen för att skapa en TypeScript-funktionsappsprojekt med Core Tools. Du kan göra detta på något av följande sätt:

- Kör den `func init` kommando, Välj `node` som ditt språk stack och välj sedan `typescript`.

- Kör `func init --worker-runtime typescript`-kommandot.

Använd för att köra Funktionskoden appen lokalt med hjälp av Core Tools i `npm start` kommandot i stället för `func host start`. Den `npm start` kommando motsvarar följande kommandon:

- `npm run build`
- `func extensions install`
- `tsc`
- `func start`

Innan du använder den [ `func azure functionapp publish` ] kommandot för att distribuera till Azure, måste du först köra den `npm run build:production` kommando. Det här kommandot skapar en produktionsklar version av JavaScript-filer från TypeScript-källfiler som kan distribueras med hjälp av [ `func azure functionapp publish` ].

## <a name="considerations-for-javascript-functions"></a>Överväganden för JavaScript-funktioner

När du arbetar med JavaScript-funktioner måste du vara medveten om överväganden i följande avsnitt.

### <a name="choose-single-vcpu-app-service-plans"></a>Välj en vCPU App Service-planer

När du skapar en funktionsapp som använder App Service-planen, rekommenderar vi att du väljer en enda vCPU-plan i stället för en plan med flera virtuella processorer. Idag funktioner körs mer effektivt JavaScript-funktioner på enskild vCPU virtuella datorer och med större virtuella datorer genererar inte de förväntade prestandaförbättringarna. Om det behövs kan du manuellt skala ut genom att lägga till flera enskild vCPU VM-instanser eller du kan aktivera automatisk skalning. Mer information finns i [skala instansantalet manuellt eller automatiskt](../monitoring-and-diagnostics/insights-how-to-scale.md?toc=%2fazure%2fapp-service%2ftoc.json).

### <a name="cold-start"></a>Kallstart

När börjar utveckla Azure Functions i utan server som värd modellen kalla är verklighet. *Kallstart* refererar till faktumet att när appen startas för första gången efter en tids inaktivitet, det tar längre tid att starta. För JavaScript-funktioner med stora beroendeträd särskilt kan kallstart vara betydande. Påskynda processen kallstart [kör dina funktioner som en paketfil](run-functions-from-deployment-package.md) när det är möjligt. Många distributionsmetoder använder Kör från paketet modellen som standard, men om du har stora kallstarter och körs inte på så sätt kan den här ändringen kan erbjuda en viktig förbättringar.

### <a name="connection-limits"></a>Anslutningsgränser

När du använder en tjänstspecifika klient i ett program för Azure Functions kan inte skapa en ny klient med varje funktionsanrop. Skapa i stället en enda, statisk klient i det globala området. Mer information finns i [hantera anslutningar i Azure Functions](manage-connections.md).

### <a name="use-async-and-await"></a>Använd `async` och `await`

När du skriver Azure Functions i JavaScript, ska du skriva kod med hjälp av den `async` och `await` nyckelord. Skriva kod med `async` och `await` i stället för återanrop eller `.then` och `.catch` med löften hjälper till att undvika två vanliga problem:
 - Utlöser undantag utan felhantering som [krascha Node.js-processen](https://nodejs.org/api/process.html#process_warning_using_uncaughtexception_correctly), potentiellt påverkar för körningen av andra funktioner.
 - Oväntade resultat, till exempel saknas loggar från context.log, på grund av asynkrona anrop som korrekt inte har slutförts.

I exemplet nedan asynkron metod `fs.readFile` anropas med ett fel vid första återanropsfunktion som dess andra parameter. Den här koden gör båda av de problem som nämns ovan. Ett undantag som inte uttryckligen har fastnat i rätt omfattning kraschat hela processen (problemet #1). Anropa `context.done()` utanför omfattningen av återanrop funktionen innebär att funktionsanrop kan avslutas innan filen ska läsas (utfärda #2). I det här exemplet anropar `context.done()` för tidigt resulterar i saknas loggposter som börjar med `Data from file:`.

```javascript
// NOT RECOMMENDED PATTERN
const fs = require('fs');

module.exports = function (context) {
    fs.readFile('./hello.txt', (err, data) => {
        if (err) {
            context.log.error('ERROR', err);
            // BUG #1: This will result in an uncaught exception that crashes the entire process
            throw err;
        }
        context.log(`Data from file: ${data}`);
        // context.done() should be called here
    });
    // BUG #2: Data is not guaranteed to be read before the Azure Function's invocation ends
    context.done();
}
```

Med hjälp av den `async` och `await` nyckelord hjälper till att undvika båda dessa fel. Du bör använda Node.js verktygsfunktionen [ `util.promisify` ](https://nodejs.org/api/util.html#util_util_promisify_original) att fel första återanrop-style funktioner awaitable funktioner.

I exemplet nedan visas växla ett ohanterat undantag när funktionen körs bara enskilda anrop som utlöste ett undantag. Den `await` nyckelordet innebär att stegen följande `readFileAsync` endast körs efter `readFile` har slutförts. Med `async` och `await`, också behöver du inte anropa den `context.done()` återanrop.

```javascript
// Recommended pattern
const fs = require('fs');
const util = require('util');
const readFileAsync = util.promisify(fs.readFile);

module.exports = async function (context) {
    try {
        const data = await readFileAsync('./hello.txt');
    } catch (err) {
        context.log.error('ERROR', err);
        // This rethrown exception will be handled by the Functions Runtime and will only fail the individual invocation
        throw err;
    }
    context.log(`Data from file: ${data}`);
}
```

## <a name="next-steps"></a>Nästa steg

Mer information finns i följande resurser:

+ [Metodtips för Azure Functions](functions-best-practices.md)
+ [Azure Functions, info för utvecklare](functions-reference.md)
+ [Azure Functions-utlösare och bindningar](functions-triggers-bindings.md)

[funktionen azure functionapp publicera]: functions-run-local.md#project-file-deployment
