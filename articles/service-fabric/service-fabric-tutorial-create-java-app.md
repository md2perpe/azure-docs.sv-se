---
title: Skapa en Java-app för Service Fabric i Azure | Microsoft Docs
description: I den här självstudien lär du dig att skapa en pålitlig Java-programtjänst med en klientdel och en tillförlitlig serverdel för tillståndskänsliga tjänster, samt hur du distribuerar programmet till ett kluster.
services: service-fabric
documentationcenter: java
author: suhuruli
manager: mfussell
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: java
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/01/2018
ms.author: suhuruli
ms.custom: mvc
ms.openlocfilehash: 559c02e74e97093a15b1d768eb5a3b32502db64e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60865157"
---
# <a name="tutorial-create-an-application-with-a-java-web-api-front-end-service-and-a-stateful-back-end-service-on-service-fabric"></a>Självstudier: Skapa ett program med en Java Web API-klientdelstjänst och en tillståndskänslig serverdelstjänst i Service Fabric

Den här självstudien ingår i en serie. När du är klar har du ett röstningsprogram med en Java-webbklientdel som sparar röstningsresultaten i en tillståndskänslig serverdelstjänst i klustret. För den här självstudien måste du ha en fungerande dator för Mac OSX eller Linux-utvecklare. Om du inte vill skapa röstningsprogrammet manuellt kan du [ladda ned källkoden för det färdiga programmet](https://github.com/Azure-Samples/service-fabric-java-quickstart) och gå vidare till [Gå igenom exempelprogrammet för röstning](service-fabric-tutorial-create-java-app.md#walk-through-the-voting-sample-application). Överväg även att följa [snabbstarten för Java-tillförlitliga tjänster.](service-fabric-quickstart-java-reliable-services.md)


![Lokal röstningsapp](./media/service-fabric-tutorial-create-java-app/votingjavalocal.png)

I den här självstudieserien får du lära du dig att:
> [!div class="checklist"]
> * skapa ett Java-program för Service Fabric Reliable Services
> * [distribuera och felsöka programmet på ett lokalt kluster](service-fabric-tutorial-debug-log-local-cluster.md)
> * [distribuera programmet till ett Azure-kluster](service-fabric-tutorial-java-deploy-azure.md)
> * [konfigurera övervakning och diagnostik för programmet](service-fabric-tutorial-java-elk.md)
> * [konfigurera CI/CD](service-fabric-tutorial-java-jenkins.md)


I del ett i den här serien lärde du dig att:

> [!div class="checklist"]
> * skapa en tillståndskänslig och tillförlitlig Java-tjänst
> * skapa en tillståndslös Java-webbprogramtjänst
> * fjärrkommunicera med den tillståndskänsliga tjänsten
> * distribuera program till ett lokalt Service Fabric-kluster.

## <a name="prerequisites"></a>Nödvändiga komponenter

Innan du börjar den här självstudien:

* Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Konfigurera din utvecklingsmiljö för [Mac](service-fabric-get-started-mac.md) eller [Linux](service-fabric-get-started-linux.md). Följ anvisningarna för att installera plugin-programmet Eclipse, Gradle, Service Fabric-SDK och Service Fabric-CLI (sfctl).

## <a name="create-the-front-end-java-stateless-service"></a>Skapa en tillståndslös Java-tjänst för klientdelen

Skapa först webbklientdelen för röstningsprogrammet. Ett webbgränssnitt som drivs av AngularJS skickar begäranden till den tillståndslösa Java-tjänsten, som kör en förenklad HTTP-server. Den här tjänsten bearbetar varje begäran och skickar ett fjärrproceduranrop till den tillståndskänsliga tjänsten för att lagra rösterna. 

1. Starta Eclipse.

2. Skapa ett projekt med **Arkiv**->**Nytt**->**Övrigt**->**Service Fabric** -> **Service Fabric-projekt**.

    ![Dialogrutan Nytt projekt i Eclipse](./media/service-fabric-tutorial-create-java-app/create-sf-proj-wizard.png)

3. I dialogrutan med **projektguiden för Service Fabric** ger du projektet namnet **Voting** och trycker på **Nästa**.

    ![Välja den tillståndslösa Java-tjänsten i dialogrutan Ny tjänst](./media/service-fabric-tutorial-create-java-app/name-sf-proj-wizard.png) 

4. På sidan **Lägg till tjänst** väljer du **Tillståndslös tjänst** och ger din tjänst namnet **VotingWeb**. Klicka på **Slutför** för att skapa projektet.

    ![Skapa en tillståndslös tjänst]( ./media/service-fabric-tutorial-create-java-app/createvotingweb.png)

    Eclipse skapar ett program- och ett tjänstprojekt, samt visar dem i Package Explorer.

    ![Package Explorer i Eclipse efter skapandet av programmet]( ./media/service-fabric-tutorial-create-java-app/eclipse-package-explorer.png)

Tabellen innehåller en kort beskrivning av varje objekt i Package Explorer från föregående skärmbild. 

| **Package Explorer-objekt** | **Beskrivning** |
| --- | --- |
| PublishProfiles | Innehåller JSON-filer som beskriver profilinformation för lokala kluster och Azure Service Fabric-kluster. Innehållet i de här filerna används av plugin-programmet när du distribuerar programmet. |
| Skript | Innehåller hjälpkomponentskript som kan användas från kommandoraden för att snabbt kunna hantera programmet med ett kluster. |
| VotingApplication | Innehåller Service Fabric-programmet som skickas till Service Fabric-klustret. |
| VotingWeb | Innehåller källfiler för den tillståndslösa klientdelstjänsten samt den relaterade Gradle.build-filen. |
| build.gradle | Gradle-fil som används för att hantera projektet. |
| settings.gradle | Innehåller namnen på de Gradle-projekt som finns i mappen. |

### <a name="add-html-and-javascript-to-the-votingweb-service"></a>Lägga till HTML och Javascript i VotingWeb-tjänsten

För att lägga till ett gränssnitt som kan renderas av den tillståndslösa tjänsten lägger du till en HTML-fil. HTML-filen renderas sedan av den förenklade HTTP-server som är inbäddad i den tillståndslösa Java-tjänsten.

1. Expandera katalogen *VotingApplication* för att gå till katalogen *VotingApplication/VotingWebPkg/Code*.

2. Högerklicka på katalogen *Kod* och klicka sedan på **Nytt**->**Mapp**.

3. Ge mappen namnet *wwwroot* och klicka på **Slutför**.

    ![Eclipse skapar wwwroot-mappen](./media/service-fabric-tutorial-create-java-app/create-wwwroot-folder.png)

4. Lägg till en fil i **wwwroot** med namnet **index.html** och klistra in följande innehåll i filen.

```html
<!DOCTYPE html>
<html>
<script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.6.4/angular.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/angular-ui-bootstrap/0.13.4/ui-bootstrap-tpls.min.js"></script>
<link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css">
<body>

<script>
var app = angular.module('VotingApp', ['ui.bootstrap']);
app.controller("VotingAppController", ['$rootScope', '$scope', '$http', '$timeout', function ($rootScope, $scope, $http, $timeout) {
    $scope.votes = [];
    
    $scope.refresh = function () {
        $http.get('getStatelessList')
            .then(function successCallback(response) {
        $scope.votes = Object.assign(
            {},
            ...Object.keys(response.data) .
            map(key => ({[decodeURI(key)]: response.data[key]}))
        )
        },
        function errorCallback(response) {
            alert(response);
        });
    };

    $scope.remove = function (item) {
       $http.get("removeItem", {params: { item: encodeURI(item) }})
            .then(function successCallback(response) {
                $scope.refresh();
            },
            function errorCallback(response) {
                alert(response);
            });
    };

    $scope.add = function (item) {
        if (!item) {return;}
        $http.get("addItem", {params: { item: encodeURI(item) }})
            .then(function successCallback(response) {
                $scope.refresh();
            },
            function errorCallback(response) {
                alert(response);
            });
    };
}]);
</script>

<div ng-app="VotingApp" ng-controller="VotingAppController" ng-init="refresh()">
    <div class="container-fluid">
        <div class="row">
            <div class="col-xs-8 col-xs-offset-2 text-center">
                <h2>Service Fabric Voting Sample</h2>
            </div>
        </div>

        <div class="row">
            <div class="col-xs-offset-2">
                <form style="width:50% ! important;" class="center-block">
                    <div class="col-xs-6 form-group">
                        <input id="txtAdd" type="text" class="form-control" placeholder="Add voting option" ng-model="item" />
                    </div>
                    <button id="btnAdd" class="btn btn-default" ng-click="add(item)">
                        <span class="glyphicon glyphicon-plus" aria-hidden="true"></span>
                        Add
                    </button>
                </form>
            </div>
        </div>

        <hr />

        <div class="row">
            <div class="col-xs-8 col-xs-offset-2">
                <div class="row">
                    <div class="col-xs-4">
                        Click to vote
                    </div>
                </div>
                <div class="row top-buffer" ng-repeat="(key, value)  in votes">
                    <div class="col-xs-8">
                        <button class="btn btn-success text-left btn-block" ng-click="add(key)">
                            <span class="pull-left">
                                {{key}}
                            </span>
                            <span class="badge pull-right">
                                {{value}} Votes
                            </span>
                        </button>
                    </div>
                    <div class="col-xs-4">
                        <button class="btn btn-danger pull-right btn-block" ng-click="remove(key)">
                            <span class="glyphicon glyphicon-remove" aria-hidden="true"></span>
                            Remove
                        </button>
                    </div>
                </div>
            </div>
        </div>
    </div>
</div>

</body>
</html>
```

### <a name="update-the-votingwebjava-file"></a>Uppdatera filen VotingWeb.java

I underprojektet **VotingWeb** öppnar du filen *VotingWeb/src/statelessservice/VotingWeb.java*. Tjänsten **VotingWeb** är en gateway till den tillståndslösa tjänsten och ansvarar för att konfigurera kommunikationslyssnaren för klientdelens API.

Ersätt den befintliga metoden **createServiceInstanceListeners** i filen med följande och spara ändringarna.

```java
@Override
protected List<ServiceInstanceListener> createServiceInstanceListeners() {

    EndpointResourceDescription endpoint = this.getServiceContext().getCodePackageActivationContext().getEndpoint(webEndpointName);
    int port = endpoint.getPort();

    List<ServiceInstanceListener> listeners = new ArrayList<ServiceInstanceListener>();
    listeners.add(new ServiceInstanceListener((context) -> new HttpCommunicationListener(context, port)));
    return listeners;
}
```

### <a name="add-the-httpcommunicationlistenerjava-file"></a>Lägg till filen HTTPCommunicationListener.java

HTTP-kommunikationslyssnaren fungerar som en kontrollant som konfigurerar HTTP-servern och gör de API:er som definierar röstningsåtgärder tillgängliga. Högerklicka på paketet *statelessservice* i mappen *VotingWeb/src/statelessservice* och välj sedan **Nytt->Fil**.  Ge filen namnet *HttpCommunicationListener.java* och klicka på **Slutför**.

Byt ut filens innehåll mot följande och spara sedan ändringarna.  Längre fram ändras den här filen till att rendera, läsa och skriva röstningsdata från serverdelstjänsten i Uppdatera filen HttpCommunicationListener.java.  För närvarande returnerar lyssnaren endast statisk HTML för röstningsappen.

```java
// ------------------------------------------------------------
//  Copyright (c) Microsoft Corporation.  All rights reserved.
//  Licensed under the MIT License (MIT). See License.txt in the repo root for license information.
// ------------------------------------------------------------

package statelessservice;

import com.google.gson.Gson;
import com.sun.net.httpserver.HttpExchange;
import com.sun.net.httpserver.HttpHandler;
import com.sun.net.httpserver.Headers;

import java.io.File;
import java.io.OutputStream;
import java.io.FileInputStream;

import java.net.InetSocketAddress;
import java.net.URI;

import java.util.HashMap;
import java.util.Map;
import java.util.concurrent.CompletableFuture;
import java.util.logging.Level;
import java.util.logging.Logger;

import microsoft.servicefabric.services.communication.runtime.CommunicationListener;
import microsoft.servicefabric.services.runtime.StatelessServiceContext;
import microsoft.servicefabric.services.client.ServicePartitionKey;
import microsoft.servicefabric.services.remoting.client.ServiceProxyBase;
import microsoft.servicefabric.services.communication.client.TargetReplicaSelector;
import system.fabric.CancellationToken;

public class HttpCommunicationListener implements CommunicationListener {

    private static final Logger logger = Logger.getLogger(HttpCommunicationListener.class.getName());

    private static final String HEADER_CONTENT_TYPE = "Content-Type";
    private static final int STATUS_OK = 200;
    private static final int STATUS_NOT_FOUND = 404;
    private static final int STATUS_ERROR = 500;
    private static final String RESPONSE_NOT_FOUND = "404 (Not Found) \n";
    private static final String MIME = "text/html";
    private static final String ENCODING = "UTF-8";

    private static final String ROOT = "wwwroot/";
    private static final String FILE_NAME = "index.html";
    private StatelessServiceContext context;
    private com.sun.net.httpserver.HttpServer server;
    private ServicePartitionKey partitionKey;
    private final int port;

    public HttpCommunicationListener(StatelessServiceContext context, int port) {
        this.partitionKey = new ServicePartitionKey(0);
        this.context = context;
        this.port = port;
    }

    // Called by openAsync when the class is instantiated
    public void start() {
        try {
            logger.log(Level.INFO, "Starting Server");
            server = com.sun.net.httpserver.HttpServer.create(new InetSocketAddress(this.port), 0);
        } catch (Exception ex) {
            logger.log(Level.SEVERE, null, ex);
            throw new RuntimeException(ex);
        }

        // Responsible for rendering the HTML layout described in the previous step
        server.createContext("/", new HttpHandler() {
            @Override
            public void handle(HttpExchange t) {
                try {
                    File file = new File(ROOT + FILE_NAME).getCanonicalFile();

                    if (!file.isFile()) {
                      // Object does not exist or is not a file: reject with 404 error.
                      t.sendResponseHeaders(STATUS_NOT_FOUND, RESPONSE_NOT_FOUND.length());
                      OutputStream os = t.getResponseBody();
                      os.write(RESPONSE_NOT_FOUND.getBytes());
                      os.close();
                    } else {
                      Headers h = t.getResponseHeaders();
                      h.set(HEADER_CONTENT_TYPE, MIME);
                      t.sendResponseHeaders(STATUS_OK, 0);
    
                      OutputStream os = t.getResponseBody();
                      FileInputStream fs = new FileInputStream(file);
                      final byte[] buffer = new byte[0x10000];
                      int count = 0;
                      while ((count = fs.read(buffer)) >= 0) {
                        os.write(buffer,0,count);
                      }

                      fs.close();
                      os.close();
                    }
                } catch (Exception e) {
                    logger.log(Level.WARNING, null, e);
                }
            }
        });

        /*
        [Replace this entire comment block in the 'Connect the services' section]
        */

        server.setExecutor(null);
        server.start();
    }

    //Helper method to parse raw HTTP requests
    private Map<String, String> queryToMap(String query){
        Map<String, String> result = new HashMap<String, String>();
        for (String param : query.split("&")) {
            String pair[] = param.split("=");
            if (pair.length>1) {
                result.put(pair[0], pair[1]);
            }else{
                result.put(pair[0], "");
            }
        }
        return result;
    }

    //Called closeAsync when the service is shut down
    private void stop() {
        if (null != server)
            server.stop(0);
    }

    //Called by the Service Fabric runtime when this service is created on a node
    @Override
    public CompletableFuture<String> openAsync(CancellationToken cancellationToken) {
        this.start();
                    logger.log(Level.INFO, "Opened Server");
        String publishUri = String.format("http://%s:%d/", this.context.getNodeContext().getIpAddressOrFQDN(), port);
        return CompletableFuture.completedFuture(publishUri);
    }

    //Called by the Service Fabric runtime when the service is shut down
    @Override
    public CompletableFuture<?> closeAsync(CancellationToken cancellationToken) {
        this.stop();
        return CompletableFuture.completedFuture(true);
    }

    //Called by the Service Fabric runtime to forcibly shut this listener down
    @Override
    public void abort() {
        this.stop();
    }
}
```

### <a name="configure-the-listening-port"></a>Konfigurera lyssningsporten

När klientdelstjänsten VotingWeb skapas, väljer Service Fabric en port som tjänsten ska lyssna på.  Tjänsten VotingWeb fungerar som klientdel för det här programmet och godkänner extern trafik, så vi kopplar den tjänsten till en fast och välkänd port. I Package Explorer öppnar du *VotingApplication/VotingWebPkg/ServiceManifest.xml*.  Hitta resursen **Slutpunkt** i avsnittet **Resurser** och ändra värdet för **Port** till 8080 (vi fortsätter att använda den här porten i självstudien). Om du vill distribuera och köra programmet lokalt måste programmets lyssningsport vara öppen och tillgänglig på datorn. Klistra in följande kodavsnitt i elementet **ServiceManifest** (dvs. direkt under elementet ```<DataPackage>```).

```xml
<Resources>
    <Endpoints>
        <!-- This endpoint is used by the communication listener to obtain the port on which to
            listen. Please note that if your service is partitioned, this port is shared with
            replicas of different partitions that are placed in your code. -->
        <Endpoint Name="WebEndpoint" Protocol="http" Port="8080" />
    </Endpoints>
  </Resources>
```

## <a name="add-a-stateful-back-end-service-to-your-application"></a>Lägg till en tillståndskänslig serverdelstjänst i ditt program

Nu när stommen i Java Web API-tjänsten har slutförts, ska vi fortsätta med att slutföra den tillståndskänsliga serverdelstjänsten.

I Service Fabric kan du konsekvent och tillförlitligt lagra dina data direkt i tjänsten med hjälp av tillförlitliga samlingar. Tillförlitliga samlingar är en uppsättning samlingsklasser för hög tillgänglighet och tillförlitlighet. Användningen av dessa klasser är välkänd för alla som har använt Java-samlingar.

1. I Package Explorer högerklickar du på **Voting** i programprojektet och väljer **Service Fabric > Lägg till Service Fabric-tjänst**.

2. I dialogrutan **Lägg till tjänst** väljer du **Tillståndskänslig tjänst**, ger tjänsten namnet **VotingDataService** och klickar på **Lägg till tjänst**.

    När tjänstprojektet har skapats har du två tjänster i ditt program. Du kan lägga till fler tjänster på samma sätt allt eftersom du fortsätter att bygga på ditt program. Tjänsterna kan ha olika versionsnummer och uppgraderas fristående.

3. Eclipse skapar ett tjänstprojekt som visas i Package Explorer.

    ![Solution Explorer](./media/service-fabric-tutorial-create-java-app/packageexplorercompletejava.png)

### <a name="add-the-votingdataservicejava-file"></a>Lägga till filen VotingDataService.java

Filen *VotingDataService.java* innehåller metoder med logik för att hämta, lägga till och ta bort röster från tillförlitliga samlingar. Lägg till följande klassmetoder för **VotingDataService** i filen *VotingDataService/src/statefulservice/VotingDataService.java*.

```java
package statefulservice;

import java.util.HashMap;
import java.util.ArrayList;
import java.util.List;
import java.util.concurrent.CompletableFuture;
import java.util.concurrent.atomic.AtomicInteger;

import microsoft.servicefabric.services.communication.runtime.ServiceReplicaListener;

import microsoft.servicefabric.services.runtime.StatefulService;

import microsoft.servicefabric.services.remoting.fabrictransport.runtime.FabricTransportServiceRemotingListener;

import microsoft.servicefabric.data.ReliableStateManager;
import microsoft.servicefabric.data.Transaction;
import microsoft.servicefabric.data.collections.ReliableHashMap;
import microsoft.servicefabric.data.utilities.AsyncEnumeration;
import microsoft.servicefabric.data.utilities.KeyValuePair;

import system.fabric.StatefulServiceContext;

import rpcmethods.VotingRPC;

class VotingDataService extends StatefulService implements VotingRPC {
    private static final String MAP_NAME = "votesMap";
    private ReliableStateManager stateManager;

    protected VotingDataService (StatefulServiceContext statefulServiceContext) {
        super (statefulServiceContext);
    }

    @Override
    protected List<ServiceReplicaListener> createServiceReplicaListeners() {
        this.stateManager = this.getReliableStateManager();
        ArrayList<ServiceReplicaListener> listeners = new ArrayList<>();

        listeners.add(new ServiceReplicaListener((context) -> {
            return new FabricTransportServiceRemotingListener(context,this);
        }));

        return listeners;
    }

    // Method that will be invoked via RPC from the front end to retrieve the complete set of votes in the map
    public CompletableFuture<HashMap<String,String>> getList() {
        HashMap<String, String> tempMap = new HashMap<String, String>();

        try {

            ReliableHashMap<String, String> votesMap = stateManager
                    .<String, String> getOrAddReliableHashMapAsync(MAP_NAME).get();

            Transaction tx = stateManager.createTransaction();
            AsyncEnumeration<KeyValuePair<String, String>> kv = votesMap.keyValuesAsync(tx).get();
            while (kv.hasMoreElementsAsync().get()) {
                KeyValuePair<String, String> k = kv.nextElementAsync().get();
                tempMap.put(k.getKey(), k.getValue());
            }

            tx.close();


        } catch (Exception e) {
            e.printStackTrace();
        }

        return CompletableFuture.completedFuture(tempMap);
    }

    // Method that will be invoked via RPC from the front end to add an item to the votes list or to increase the
    // vote count for a particular item
    public CompletableFuture<Integer> addItem(String itemToAdd) {
        AtomicInteger status = new AtomicInteger(-1);

        try {

            ReliableHashMap<String, String> votesMap = stateManager
                    .<String, String> getOrAddReliableHashMapAsync(MAP_NAME).get();

            Transaction tx = stateManager.createTransaction();
            votesMap.computeAsync(tx, itemToAdd, (k, v) -> {
                if (v == null) {
                    return "1";
                }
                else {
                    int numVotes = Integer.parseInt(v);
                    numVotes = numVotes + 1;
                    return Integer.toString(numVotes);
                }
            }).get();

            tx.commitAsync().get();
            tx.close();

            status.set(1);
        } catch (Exception e) {
            e.printStackTrace();
        }

        return CompletableFuture.completedFuture(new Integer(status.get()));
    }

    // Method that will be invoked via RPC from the front end to remove an item
    public CompletableFuture<Integer> removeItem(String itemToRemove) {
        AtomicInteger status = new AtomicInteger(-1);
        try {
            ReliableHashMap<String, String> votesMap = stateManager
                    .<String, String> getOrAddReliableHashMapAsync(MAP_NAME).get();

            Transaction tx = stateManager.createTransaction();
            votesMap.removeAsync(tx, itemToRemove).get();
            tx.commitAsync().get();
            tx.close();

            status.set(1);
        } catch (Exception e) {
            e.printStackTrace();
        }

        return CompletableFuture.completedFuture(new Integer(status.get()));
    }

}
```

Stommen för den tillståndslösa tjänsten i klientdelen och serverdelstjänsten har nu skapats.

## <a name="create-the-communication-interface-to-your-application"></a>Skapa kommunikationsgränssnittet till ditt program

 Nästa steg är att ansluta den tillståndslösa tjänsten i klientdelen och serverdelstjänsten. Både tjänsterna använder ett gränssnitt som kallas VotingRPC, som definierar åtgärder för röstningsprogrammet. Det här gränssnittet implementeras av både klientdels- och serverdelstjänsterna för att aktivera RPC (Remote Procedure Calls) mellan de två tjänsterna. Tyvärr har Eclipse inte stöd för tillägg av Gradle-delprojekt, så det paket som innehåller gränssnittet måste läggas till manuellt.

1. Högerklicka på projektet **Voting** i Package Explorer och klicka på **Ny -> Mapp**. Ge mappen namnet **VotingRPC/src/rpcmethods**.

    ![Skapa VotingRPC-paket](./media/service-fabric-tutorial-create-java-app/createvotingrpcpackage.png)

3. Skapa en fil under *Voting/VotingRPC/src/rpcmethods* med namnet *VotingRPC.java* och klistra in följande i **VotingRPC.java**-filen. 

    ```java
    package rpcmethods;
    
    import java.util.ArrayList;
    import java.util.concurrent.CompletableFuture;
    import java.util.List;
    import java.util.HashMap;
    
    import microsoft.servicefabric.services.remoting.Service;
    
    public interface VotingRPC extends Service {
        CompletableFuture<HashMap<String, String>> getList();
    
        CompletableFuture<Integer> addItem(String itemToAdd);
    
        CompletableFuture<Integer> removeItem(String itemToRemove);
    }
    ```

4. Skapa en tom fil med namnet *build.gradle* i katalogen *Voting/VotingRPC* och klistra in följande i den. Gradle-filen används för att bygga och skapa den jar-fil som importeras av andra tjänster. 

    ```gradle
    apply plugin: 'java'
    apply plugin: 'eclipse'
    
    sourceSets {
      main {
         java.srcDirs = ['src']
         output.classesDir = 'out/classes'
          resources {
           srcDirs = ['src']
         }
       }
    }
    
    clean.doFirst {
        delete "${projectDir}/out"
        delete "${projectDir}/VotingRPC.jar"
    }
    
    repositories {
        mavenCentral()
    }
    
    dependencies {
        compile ('com.microsoft.servicefabric:sf-actors:1.0.0')
    }
    
    jar {
        from configurations.compile.collect {
            (it.isDirectory() && !it.getName().contains("native")) ? it : zipTree(it)}

        manifest {
                attributes(
                'Main-Class': 'rpcmethods.VotingRPC')
            baseName "VotingRPC"
            destinationDir = file('./')
        }
    
        exclude 'META-INF/*.RSA', 'META-INF/*.SF','META-INF/*.DSA'
    }

    defaultTasks 'clean', 'jar'
    ```

5. I filen *Voting/settings.gradle* lägger du till en rad för att inkludera det nya projektet i versionen. 

    ```gradle
    include ':VotingRPC'
    ```

6. I filen *Voting/VotingWeb/src/statelessservice/HttpCommunicationListener.java* ersätter du kommentarsblocket med följande.  

    ```java
    server.createContext("/getStatelessList", new HttpHandler() {
        @Override
        public void handle(HttpExchange t) {
            try {
                t.sendResponseHeaders(STATUS_OK,0);
                OutputStream os = t.getResponseBody();
    
                HashMap<String,String> list = ServiceProxyBase.create(VotingRPC.class, new URI("fabric:/VotingApplication/VotingDataService"), partitionKey, TargetReplicaSelector.DEFAULT, "").getList().get();
                String json = new Gson().toJson(list);
                os.write(json.getBytes(ENCODING));
                os.close();
            } catch (Exception e) {
                logger.log(Level.WARNING, null, e);
            }
        }
    });
    
    server.createContext("/removeItem", new HttpHandler() {
        @Override
        public void handle(HttpExchange t) {
            try {
                OutputStream os = t.getResponseBody();
                URI r = t.getRequestURI();
    
                Map<String, String> params = queryToMap(r.getQuery());
                String itemToRemove = params.get("item");

                Integer num = ServiceProxyBase.create(VotingRPC.class, new URI("fabric:/VotingApplication/VotingDataService"), partitionKey, TargetReplicaSelector.DEFAULT, "").removeItem(itemToRemove).get();
    
                if (num != 1)
                {
                    t.sendResponseHeaders(STATUS_ERROR, 0);
                } else {
                    t.sendResponseHeaders(STATUS_OK,0);
                }
    
                String json = new Gson().toJson(num);
                os.write(json.getBytes(ENCODING));
                os.close();
            } catch (Exception e) {
                logger.log(Level.WARNING, null, e);
            }
    
        }
    });
    
    server.createContext("/addItem", new HttpHandler() {
        @Override
        public void handle(HttpExchange t) {
            try {
                URI r = t.getRequestURI();
                Map<String, String> params = queryToMap(r.getQuery());
                String itemToAdd = params.get("item");

                OutputStream os = t.getResponseBody();
                Integer num = ServiceProxyBase.create(VotingRPC.class, new URI("fabric:/VotingApplication/VotingDataService"), partitionKey, TargetReplicaSelector.DEFAULT, "").addItem(itemToAdd).get();
                if (num != 1)
                {
                    t.sendResponseHeaders(STATUS_ERROR, 0);
                } else {
                    t.sendResponseHeaders(STATUS_OK,0);
                }
    
                String json = new Gson().toJson(num);
                os.write(json.getBytes(ENCODING));
                os.close();
            } catch (Exception e) {
                logger.log(Level.WARNING, null, e);
            }
        }
    });
    ```
7. Lägg till lämplig importinstruktion överst i filen *Voting/VotingWeb/src/statelessservice/HttpCommunicationListener.java*. 

    ```java
    import rpcmethods.VotingRPC; 
    ```

I det här skedet är funktionerna för klientdel, serverdel och RPC-gränssnitt slutförda. Nästa steg är att konfigurera Gradle-skripten på lämpligt sätt innan du distribuerar till ett Service Fabric-kluster. 

## <a name="walk-through-the-voting-sample-application"></a>Gå igenom exempelprogrammet för röstning
Röstningsprogrammet består av två tjänster:
- Webbtjänst för klientdelen (VotingWeb) – En Java-webbtjänst för webbsidans klientdel som gör API:er tillgängliga för kommunikation med serverdelstjänsten.
- Serverdelstjänst (VotingDataService) – En Java-webbtjänst som definierar de metoder som anropas via RPC (Remote Procedure Calls) för att spara rösterna.

![Diagram över programmet](./media/service-fabric-tutorial-create-java-app/walkthroughjavavoting.png)

När du utför en åtgärd i programmet (lägger till objekt, röstar, tar bort objekt) händer följande:
1. Ett JavaScript skickar en begäran till webb-API:n i webbtjänsten för klientdelen som en HTTP-begäran.

2. Webbtjänsten för klientdelen använder den inbyggda tjänsten för fjärrkommunikation i Service Fabric till att leta upp och vidarebefordra begärandet till serverdelstjänsten. 

3. Serverdelstjänsten definierar metoder som uppdaterar resultatet i en tillförlitlig ordlista. Innehållet i den här tillförlitliga uppslagslistan replikeras till flera noder i klustret och sparas på disken. Alla programdata lagras i klustret. 

## <a name="configure-gradle-scripts"></a>Konfigurera Gradle-skript 

I det här avsnittet konfigureras Gradle-skripten för projektet. 

1. Ersätt innehållet i filen *Voting/build.grade* med följande.

    ```gradle
    apply plugin: 'java'
    apply plugin: 'eclipse'
    
    subprojects {
        apply plugin: 'java'
    }
    
    defaultTasks 'clean', 'jar', 'copyDeps'
    ```

2. Ersätt innehållet i filen *Voting/VotingWeb/build.gradle* med följande.

    ```gradle
    apply plugin: 'java'
    apply plugin: 'eclipse'
    
    sourceSets {
      main {
         java.srcDirs = ['src']
         output.classesDir = 'out/classes'
          resources {
           srcDirs = ['src']
         }
       }
    }
    
    clean.doFirst {
        delete "${projectDir}/../lib"
        delete "${projectDir}/out"
        delete "${projectDir}/../VotingApplication/VotingWebPkg/Code/lib"
        delete "${projectDir}/../VotingApplication/VotingWebPkg/Code/VotingWeb.jar"
    }
    
    repositories {
        mavenCentral()
    }
    
    dependencies {
        compile ('com.microsoft.servicefabric:sf-actors:1.0.0-preview1')
        compile project(':VotingRPC')
    }
    
    task explodeDeps(type: Copy, dependsOn:configurations.compile) { task ->
        configurations.compile.filter {!it.toString().contains("native")}.each{
            from it
        }
    
        configurations.compile.filter {it.toString().contains("native")}.each{
            from zipTree(it)
        }
        into "../lib/"
        include "lib*.so", "*.jar"
    }
    
    task copyDeps<< {
        copy {
            from("../lib/")
            into("../VotingApplication/VotingWebPkg/Code/lib")
            include('lib*.so')
        }
    }
    
    compileJava.dependsOn(explodeDeps)
    
    jar {
        from configurations.compile.collect {(it.isDirectory() && !it.getName().contains("native")) ? it : zipTree(it)}
    
        manifest {
            attributes(
                'Main-Class': 'statelessservice.VotingWebServiceHost')
            baseName "VotingWeb"
            destinationDir = file('../VotingApplication/VotingWebPkg/Code/')
        }
    
        exclude 'META-INF/*.RSA', 'META-INF/*.SF','META-INF/*.DSA'
    }
    
    defaultTasks 'clean', 'jar', 'copyDeps'
    ```

3. Ersätt innehållet i filen *Voting/VotingDataService/build.gradle*. 

    ```gradle
    apply plugin: 'java'
    apply plugin: 'eclipse'
    
    sourceSets {
      main {
         java.srcDirs = ['src']
         output.classesDir = 'out/classes'
          resources {
           srcDirs = ['src']
         }
       }
    }
    
    clean.doFirst {
        delete "${projectDir}/../lib"
        delete "${projectDir}/out"
        delete "${projectDir}/../VotingApplication/VotingDataServicePkg/Code/lib"
        delete "${projectDir}/../VotingApplication/VotingDataServicePkg/Code/VotingDataService.jar"
    }
    
    repositories {
        mavenCentral()
    }
    
    dependencies {
        compile ('com.microsoft.servicefabric:sf-actors:1.0.0-preview1')
        compile project(':VotingRPC')
    }
    
    task explodeDeps(type: Copy, dependsOn:configurations.compile) { task ->
        configurations.compile.filter {!it.toString().contains("native")}.each{
            from it
        }
    
        configurations.compile.filter {it.toString().contains("native")}.each{
            from zipTree(it)
        }
        into "../lib/"
        include "lib*.so", "*.jar"
    }
    
    compileJava.dependsOn(explodeDeps)
    
    task copyDeps<< {
        copy {
            from("../lib/")
            into("../VotingApplication/VotingDataServicePkg/Code/lib")
            include('lib*.so')
        }
    }
    
    jar {
        from configurations.compile.collect {
            (it.isDirectory() && !it.getName().contains("native")) ? it : zipTree(it)}
    
        manifest {
            attributes('Main-Class': 'statefulservice.VotingDataServiceHost')
    
            baseName "VotingDataService"
            destinationDir = file('../VotingApplication/VotingDataServicePkg/Code/')
        }
    
        exclude 'META-INF/*.RSA', 'META-INF/*.SF','META-INF/*.DSA'
    }
    
    defaultTasks 'clean', 'jar', 'copyDeps'
    ```

## <a name="deploy-application-to-local-cluster"></a>Distribuera ett program till det lokala klustret

Programmet är nu redo att distribueras till ett lokalt Service Fabric-kluster.

1. Högerklicka på projektet **Voting** i Package Explorer och klicka på **Service Fabric -> Build Application** (Skapa program) för att skapa ditt program.

2. Kör det lokala Service Fabric-klustret. Det här steget beror på vilken utvecklingsmiljö du har (Mac eller Linux).

    Om du använder en Mac-dator kan du köra det lokala klustret med följande kommando: Ersätt kommandot som skickades till parametern **-v** med sökvägen till din egen arbetsyta.

    ```bash
    docker run -itd -p 19080:19080 -p 8080:8080 -p --name sfonebox servicefabricoss/service-fabric-onebox
    ```
    Mer detaljerad information finns i [installationsguiden för OS X.](service-fabric-get-started-mac.md)

    Om du använder en Linux-dator startar du det lokala klustret med följande kommando: 

    ```bash 
    sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
    ```
    Mer detaljerad information finns i [installationsguiden för Linux.](service-fabric-get-started-linux.md)

4. Högerklicka på projektet **Voting** i Package Explorer för Eclipse och klicka på **Service Fabric -> Publicera program ...** 
5. I fönstret **Publish Application** (Publicera program) väljer du **Local.json** i listrutan och klickar på **Publish** (Publicera).
6. Gå till din webbserver webbläsare och åtkomst http:\//localhost:8080 att visa programmet som körs i det lokala Service Fabric-klustret. 

## <a name="next-steps"></a>Nästa steg

I den här självstudiedelen lärde du dig att:

> [!div class="checklist"]
> * Skapa en Java-tjänst som en tillståndskänslig och tillförlitlig tjänst
> * Skapa en Java-tjänst som en tillståndslös webbtjänst
> * Lägga till ett Java-gränssnitt som hanterar RPC (Remote Procedure Calls) mellan dina tjänster
> * Konfigurera dina Gradle-skript
> * Skapa och distribuera programmet till ett lokalt Service Fabric-kluster

Gå vidare till nästa kurs:
> [!div class="nextstepaction"]
> [Felsöka och logga program på ett lokalt kluster](service-fabric-tutorial-debug-log-local-cluster.md)
