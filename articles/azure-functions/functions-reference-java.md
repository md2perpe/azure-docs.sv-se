---
title: Java-utvecklare för Azure Functions | Microsoft Docs
description: Förstå hur du utvecklar funktioner med Java.
services: functions
documentationcenter: na
author: rloutlaw
manager: justhe
keywords: Azure functions, funktioner, händelsebearbetning, webhooks, dynamisk beräkning, serverlös arkitektur och java
ms.service: azure-functions
ms.devlang: java
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: routlaw
ms.openlocfilehash: acd873cd19cafb785f968fd3d8671640bcfafed8
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/17/2019
ms.locfileid: "67163707"
---
# <a name="azure-functions-java-developer-guide"></a>Utvecklarguide för Azure Functions Java

Azure Functions-körningen stöder [Java SE 8 LTS (zulu8.31.0.2-jre8.0.181-win_x64)](https://repos.azul.com/azure-only/zulu/packages/zulu-8/8u181/). Den här guiden innehåller information om krångla skriva Azure Functions med Java.

En Java-funktionen är en `public` metoden dekorerad med anteckningen `@FunctionName`. Den här metoden definierar posten för en Java-funktion och måste vara unika i ett visst paket. 

Den här artikeln förutsätter att du redan har läst den [Azure Functions för utvecklare](functions-reference.md). Du bör också slutföras Functions-Snabbstart för att skapa din första funktion med [Visual Studio Code](functions-create-first-function-vs-code.md) eller [Maven](functions-create-first-java-maven.md).

## <a name="programming-model"></a>Programmeringsmodell 

Begreppet [utlösare och bindningar](functions-triggers-bindings.md) är grundläggande för Azure Functions. Utlösare starta körning av din kod. Bindningar ger dig ett sätt att skicka data till och returnera data från en funktion utan att behöva skriva kod för åtkomst av anpassade data.

## <a name="folder-structure"></a>mappstruktur

Här är mappstrukturen för ett projekt med Azure Functions Java:

```
FunctionsProject
 | - src
 | | - main
 | | | - java
 | | | | - FunctionApp
 | | | | | - MyFirstFunction.java
 | | | | | - MySecondFunction.java
 | - target
 | | - azure-functions
 | | | - FunctionApp
 | | | | - FunctionApp.jar
 | | | | - host.json
 | | | | - MyFirstFunction
 | | | | | - function.json
 | | | | - MySecondFunction
 | | | | | - function.json
 | | | | - bin
 | | | | - lib
 | - pom.xml
```

Du kan använda en delad [host.json](functions-host-json.md) filen för att konfigurera funktionsappen. Varje funktion har sina egna kodfilen (.java) och bindningen konfigurationsfil (function.json).

Du kan placera mer än en funktion i ett projekt. Undvik att placera dina funktioner i separata JAR-filer. Den `FunctionApp` i mål-katalogen är det distribueras till din funktionsapp i Azure.

## <a name="triggers-and-annotations"></a>Utlösare och anteckningar

 Funktioner anropas av en utlösare, till exempel en HTTP-begäran, en timer eller en uppdatering av data. Din funktion måste bearbeta som utlösare och andra indata att producera en eller flera utdata.

Använda Java-anteckningar som ingår i den [com.microsoft.azure.functions.annotation.*](/java/api/com.microsoft.azure.functions.annotation) paket att binda indata och utdata till din metoder. Mer information finns i den [Java referensdokument](/java/api/com.microsoft.azure.functions.annotation).

> [!IMPORTANT] 
> Du måste konfigurera ett Azure Storage-konto i din [local.settings.json](/azure/azure-functions/functions-run-local#local-settings-file) att köra Azure Blob storage, Azure Queue storage eller Azure Table storage-utlösare lokalt.

Exempel:

```java
public class Function {
    public String echo(@HttpTrigger(name = "req", 
      methods = {"post"},  authLevel = AuthorizationLevel.ANONYMOUS) 
        String req, ExecutionContext context) {
        return String.format(req);
    }
}
```

här är den genererade motsvarande `function.json` av den [azure-functions-maven-plugin-programmet](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-maven-plugin):

```json
{
  "scriptFile": "azure-functions-example.jar",
  "entryPoint": "com.example.Function.echo",
  "bindings": [
    {
      "type": "httpTrigger",
      "name": "req",
      "direction": "in",
      "authLevel": "anonymous",
      "methods": [ "post" ]
    },
    {
      "type": "http",
      "name": "$return",
      "direction": "out"
    }
  ]
}

```

## <a name="jdk-runtime-availability-and-support"></a>JDK runtime tillgänglighet och support 

Lokal utveckling av Java-funktionsappar, hämta och använda den [Azul Zulu Enterprise för Azure](https://assets.azul.com/files/Zulu-for-Azure-FAQ.pdf) Java 8 JDKs från [Azul Systems](https://www.azul.com/downloads/azure-only/zulu/). Azure Functions använder Azul Java 8 JDK-körningen när du distribuerar dina funktionsappar till molnet.

[Azure-supporten](https://azure.microsoft.com/support/) för problem med JDKs och funktionen appar är tillgängliga med en [kvalificerade supportavtal](https://azure.microsoft.com/support/plans/).

## <a name="customize-jvm"></a>Anpassa JVM

Functions kan du anpassa Java virtual machine (JVM) används för att köra Java-funktioner. Den [följande JVM-alternativ](https://github.com/Azure/azure-functions-java-worker/blob/master/worker.config.json#L7) används som standard:

* `-XX:+TieredCompilation`
* `-XX:TieredStopAtLevel=1`
* `-noverify` 
* `-Djava.net.preferIPv4Stack=true`
* `-jar`

Du kan ange ytterligare argument i en app som inställning med namnet `JAVA_OPTS`. Du kan lägga till inställningar för din funktionsapp som distribueras till Azure i Azure portal eller Azure CLI.

### <a name="azure-portal"></a>Azure Portal

I den [Azure-portalen](https://portal.azure.com), använda den [programinställningar fliken](functions-how-to-use-azure-function-app-settings.md#settings) att lägga till den `JAVA_OPTS` inställningen.

### <a name="azure-cli"></a>Azure CLI

Du kan använda den [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings) kommando för att ange `JAVA_OPTS`, som i följande exempel:

```azurecli-interactive
az functionapp config appsettings set --name <APP_NAME> \
--resource-group <RESOURCE_GROUP> \
--settings "JAVA_OPTS=-Djava.awt.headless=true"
```
Det här exemplet aktiverar fjärradministrerad läge. Ersätt `<APP_NAME>` med namnet på funktionsappen och `<RESOURCE_GROUP>` med resursgruppen.

> [!WARNING]  
> I den [förbrukningsplan](functions-scale.md#consumption-plan), måste du lägga till den `WEBSITE_USE_PLACEHOLDER` inställning med värdet `0`.  
Den här inställningen ökar kallstart tiderna för Java-funktioner.

## <a name="third-party-libraries"></a>Bibliotek från tredje part 

Azure Functions har stöd för användning av bibliotek från tredje part. Som standard alla beroenden som anges i ditt projekt `pom.xml` filen paketeras automatiskt under den [ `mvn package` ](https://github.com/Microsoft/azure-maven-plugins/blob/master/azure-functions-maven-plugin/README.md#azure-functionspackage) mål. För bibliotek som inte har angetts som beroenden i den `pom.xml` filen, placera dem i en `lib` katalogen i funktionens rotkatalog. Beroendena placeras i den `lib` directory läggs till i systemet klassinläsare vid körning.

Den `com.microsoft.azure.functions:azure-functions-java-library` beroende tillhandahålls på klassökvägen som standard och behöver inte inkluderas i den `lib` directory. Dessutom [azure-functions-java-worker](https://github.com/Azure/azure-functions-java-worker) lägger till beroenden som anges [här](https://github.com/Azure/azure-functions-java-worker/wiki/Azure-Java-Functions-Worker-Dependencies) i klassökvägen.

## <a name="data-type-support"></a>Stöd för datatypen

Du kan använda vanlig gamla Java objects (Pojo), typer som definieras i `azure-functions-java-library`, eller primitiva datatyper, till exempel sträng och heltal för att binda till indata eller utdatabindningar.

### <a name="pojos"></a>POJOs

För att konvertera indata till POJO, [azure-functions-java-worker](https://github.com/Azure/azure-functions-java-worker) använder den [gson](https://github.com/google/gson) biblioteket. POJO-typer som används som indata till funktioner ska vara `public`.

### <a name="binary-data"></a>Binära data

Binda binära indata eller utdata till `byte[]`, genom att ange den `dataType` i din function.json till `binary`:

```java
   @FunctionName("BlobTrigger")
    @StorageAccount("AzureWebJobsStorage")
     public void blobTrigger(
        @BlobTrigger(name = "content", path = "myblob/{fileName}", dataType = "binary") byte[] content,
        @BindingName("fileName") String fileName,
        final ExecutionContext context
    ) {
        context.getLogger().info("Java Blob trigger function processed a blob.\n Name: " + fileName + "\n Size: " + content.length + " Bytes");
    }
```

Om du räknar med null-värden, Använd `Optional<T>`.

## <a name="bindings"></a>Bindningar

Indata- och utdatabindningar en deklarativ metod för att ansluta till data från i din kod. En funktion kan ha flera indatafiler och utdatabindningar.

### <a name="input-binding-example"></a>Indatabindning exempel

```java
package com.example;

import com.microsoft.azure.functions.annotation.*;

public class Function {
    @FunctionName("echo")
    public static String echo(
        @HttpTrigger(name = "req", methods = { "put" }, authLevel = AuthorizationLevel.ANONYMOUS, route = "items/{id}") String inputReq,
        @TableInput(name = "item", tableName = "items", partitionKey = "Example", rowKey = "{id}", connection = "AzureWebJobsStorage") TestInputData inputData
        @TableOutput(name = "myOutputTable", tableName = "Person", connection = "AzureWebJobsStorage") OutputBinding<Person> testOutputData,
    ) {
        testOutputData.setValue(new Person(httpbody + "Partition", httpbody + "Row", httpbody + "Name"));
        return "Hello, " + inputReq + " and " + inputData.getKey() + ".";
    }

    public static class TestInputData {
        public String getKey() { return this.RowKey; }
        private String RowKey;
    }
    public static class Person {
        public String PartitionKey;
        public String RowKey;
        public String Name;

        public Person(String p, String r, String n) {
            this.PartitionKey = p;
            this.RowKey = r;
            this.Name = n;
        }
    }
}
```

Du anropa den här funktionen med en HTTP-begäran. 
- Nyttolasten för HTTP-begäran skickas som en `String` för argumentet `inputReq`.
- En post som hämtas från tabellagring och som skickas som `TestInputData` till argumentet `inputData`.

För att få en batch med indata, kan du binda till `String[]`, `POJO[]`, `List<String>`, eller `List<POJO>`.

```java
@FunctionName("ProcessIotMessages")
    public void processIotMessages(
        @EventHubTrigger(name = "message", eventHubName = "%AzureWebJobsEventHubPath%", connection = "AzureWebJobsEventHubSender", cardinality = Cardinality.MANY) List<TestEventData> messages,
        final ExecutionContext context)
    {
        context.getLogger().info("Java Event Hub trigger received messages. Batch size: " + messages.size());
    }
    
    public class TestEventData {
    public String id;
}

```

Den här funktionen aktiveras när det finns nya data i konfigurerade event hub. Eftersom den `cardinality` är inställd på `MANY`, funktionen tar emot en grupp med meddelanden från event hub. `EventData` från event hub konverteras de till `TestEventData` för funktion-körning.

### <a name="output-binding-example"></a>Exempel på utdata-bindning

Du kan binda en utdatabindning till det returnera värdet med hjälp av `$return`. 

```java
package com.example;

import com.microsoft.azure.functions.annotation.*;

public class Function {
    @FunctionName("copy")
    @StorageAccount("AzureWebJobsStorage")
    @BlobOutput(name = "$return", path = "samples-output-java/{name}")
    public static String copy(@BlobTrigger(name = "blob", path = "samples-input-java/{name}") String content) {
        return content;
    }
}
```

Om det finns flera utdatabindningar kan du använda det returnera värdet för endast en av dem.

Använda för att skicka flera utdatavärden, `OutputBinding<T>` definieras i den `azure-functions-java-library` paketet. 

```java
@FunctionName("QueueOutputPOJOList")
    public HttpResponseMessage QueueOutputPOJOList(@HttpTrigger(name = "req", methods = { HttpMethod.GET,
            HttpMethod.POST }, authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Optional<String>> request,
            @QueueOutput(name = "itemsOut", queueName = "test-output-java-pojo", connection = "AzureWebJobsStorage") OutputBinding<List<TestData>> itemsOut, 
            final ExecutionContext context) {
        context.getLogger().info("Java HTTP trigger processed a request.");
       
        String query = request.getQueryParameters().get("queueMessageId");
        String queueMessageId = request.getBody().orElse(query);
        itemsOut.setValue(new ArrayList<TestData>());
        if (queueMessageId != null) {
            TestData testData1 = new TestData();
            testData1.id = "msg1"+queueMessageId;
            TestData testData2 = new TestData();
            testData2.id = "msg2"+queueMessageId;

            itemsOut.getValue().add(testData1);
            itemsOut.getValue().add(testData2);

            return request.createResponseBuilder(HttpStatus.OK).body("Hello, " + queueMessageId).build();
        } else {
            return request.createResponseBuilder(HttpStatus.INTERNAL_SERVER_ERROR)
                    .body("Did not find expected items in CosmosDB input list").build();
        }
    }

     public static class TestData {
        public String id;
    }
```

Du anropa den här funktionen på en HttpRequest. Flera värden skriver till Queue storage.

## <a name="httprequestmessage-and-httpresponsemessage"></a>HttpRequestMessage och HttpResponseMessage

 Dessa definieras i `azure-functions-java-library`. De är helper typer du arbetar med HttpTrigger funktioner.

| Specialiserad typ      |       Mål        | Normal användning                  |
| --------------------- | :-----------------: | ------------------------------ |
| `HttpRequestMessage<T>`  |    HTTP-utlösare     | Hämtar metoden, sidhuvuden eller frågor |
| `HttpResponseMessage` | HTTP-Utdatabindning | Returnerar status än 200   |

## <a name="metadata"></a>Metadata

Få utlösare skicka [utlösa metadata](/azure/azure-functions/functions-triggers-bindings) tillsammans med indata. Du kan använda anteckningen `@BindingName` att binda till utlösa metadata.


```Java
package com.example;

import java.util.Optional;
import com.microsoft.azure.functions.annotation.*;


public class Function {
    @FunctionName("metadata")
    public static String metadata(
        @HttpTrigger(name = "req", methods = { "get", "post" }, authLevel = AuthorizationLevel.ANONYMOUS) Optional<String> body,
        @BindingName("name") String queryValue
    ) {
        return body.orElse(queryValue);
    }
}
```
I föregående exempel, den `queryValue` är bunden till frågesträngparametern `name` i http-begärans URL `http://{example.host}/api/metadata?name=test`. Ett annat exempel, som visar hur du binder till `Id` från kön utlösaren metadata.

```java
 @FunctionName("QueueTriggerMetadata")
    public void QueueTriggerMetadata(
        @QueueTrigger(name = "message", queueName = "test-input-java-metadata", connection = "AzureWebJobsStorage") String message,@BindingName("Id") String metadataId,
        @QueueOutput(name = "output", queueName = "test-output-java-metadata", connection = "AzureWebJobsStorage") OutputBinding<TestData> output,
        final ExecutionContext context
    ) {
        context.getLogger().info("Java Queue trigger function processed a message: " + message + " with metadaId:" + metadataId );
        TestData testData = new TestData();
        testData.id = metadataId;
        output.setValue(testData);
    }
```

> [!NOTE]
> Det namn som i anteckningen måste matcha metadata-egenskap.

## <a name="execution-context"></a>Körningskontext

`ExecutionContext`, definieras i den `azure-functions-java-library`, innehåller hjälpmetoder att kommunicera med functions-körning.

### <a name="logger"></a>loggaren

Använd `getLogger`har definierats i `ExecutionContext`, för att skriva loggar från funktionskoden.

Exempel:

```java

import com.microsoft.azure.functions.*;
import com.microsoft.azure.functions.annotation.*;

public class Function {
    public String echo(@HttpTrigger(name = "req", methods = {"post"}, authLevel = AuthorizationLevel.ANONYMOUS) String req, ExecutionContext context) {
        if (req.isEmpty()) {
            context.getLogger().warning("Empty request body received by function " + context.getFunctionName() + " with invocation " + context.getInvocationId());
        }
        return String.format(req);
    }
}
```

## <a name="view-logs-and-trace"></a>Visa loggar och spårning

Du kan använda Azure CLI för att stream Java stdout och stderr-loggning, samt andra programloggning. 

Här är hur du konfigurerar funktionsappen för att skriva programloggning med hjälp av Azure CLI:

```azurecli-interactive
az webapp log config --name functionname --resource-group myResourceGroup --application-logging true
```

Strömma loggning utdata för din funktionsapp med hjälp av Azure CLI, öppna en kommandotolk, Bash eller terminalsession och ange följande kommando:

```azurecli-interactive
az webapp log tail --name webappname --resource-group myResourceGroup
```
Den [az webapp log tail](/cli/azure/webapp/log) kommandot har alternativ för att filtrera utdata med hjälp av den `--provider` alternativet. 

Öppna en kommandotolk, Bash eller terminalsession för att hämta loggfilerna som en ZIP-fil med hjälp av Azure CLI, och ange följande kommando:

```azurecli-interactive
az webapp log download --resource-group resourcegroupname --name functionappname
```

Du måste ha aktiverat filsystem som loggar in på Azure portal eller Azure CLI innan du kör det här kommandot.

## <a name="environment-variables"></a>Miljövariabler

I funktioner, [appinställningar](https://docs.microsoft.com/azure/azure-functions/functions-app-settings), till exempel tjänstanslutning strängar, visas som miljövariabler under körning. Du kan komma åt de här inställningarna med hjälp av, `System.getenv("AzureWebJobsStorage")`.

Du kan till exempel lägga till [AppSetting](https://docs.microsoft.com/azure/azure-functions/functions-how-to-use-azure-function-app-settings), med namnet `testAppSetting` och värdet `testAppSettingValue`:

```java

public class Function {
    public String echo(@HttpTrigger(name = "req", methods = {"post"}, authLevel = AuthorizationLevel.ANONYMOUS) String req, ExecutionContext context) {
        context.getLogger().info("testAppSetting "+ System.getenv("testAppSettingValue"));
        return String.format(req);
    }
}

```

## <a name="next-steps"></a>Nästa steg

Mer information om utveckling av Azure Functions Java finns i följande resurser:

* [Metodtips för Azure Functions](functions-best-practices.md)
* [Azure Functions, info för utvecklare](functions-reference.md)
* [Azure Functions-utlösare och bindningar](functions-triggers-bindings.md)
* Lokal utveckling och felsökning med [Visual Studio Code](https://code.visualstudio.com/docs/java/java-azurefunctions), [IntelliJ](functions-create-maven-intellij.md), och [Eclipse](functions-create-maven-eclipse.md)
* [Fjärrfelsök Java Azure Functions med Visual Studio Code](https://code.visualstudio.com/docs/java/java-serverless#_remote-debug-functions-running-in-the-cloud)
* [Maven-pluginprogrammet för Azure Functions](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-functions-maven-plugin/README.md) 
* Effektivisera funktionsskapande via den `azure-functions:add` målet, och förbereda uppsamlingskatalogen för [ZIP-filen distribution](deployment-zip-push.md).
