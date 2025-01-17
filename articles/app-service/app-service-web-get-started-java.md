---
title: Skapa Java-webbapp på Windows - Azure App Service
description: I den här snabbstarten får distribuera du din första Java Hello World i Azure App Service i Windows på bara några minuter.
keywords: azure, app service, web app, windows, java, maven, quickstart
services: app-service\web
documentationcenter: ''
author: msangapu-msft
manager: jeconnoc
editor: ''
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: quickstart
ms.date: 05/29/2019
ms.author: jasonfreeberg
ms.custom: mvc
ms.openlocfilehash: bd11b5334fbffc28aa6869c9f37b2b9909692d56
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/17/2019
ms.locfileid: "67154798"
---
# <a name="quickstart-create-a-java-app-in-app-service"></a>Snabbstart: Skapa en Java-app i App Service

> [!NOTE]
> I den här artikeln distribueras en app till App Service i Windows. Distribuera till App Service på _Linux_, se [skapa Java-webbapp i Linux](./containers/quickstart-java.md).
>

Med [Azure App Service](overview.md) får du en automatiskt uppdaterad webbvärdtjänst med hög skalbarhet.  Den här snabbstarten visar hur du använder den [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) med den [Maven-pluginprogrammet för Azure App Service](https://github.com/Microsoft/azure-maven-plugins/tree/develop/azure-webapp-maven-plugin) ska distribuera en Java web Arkiv (WAR)-fil.

> [!NOTE]
> Kan du också göra samma sak med hjälp av populära IDEs som IntelliJ och Eclipse. Kolla in våra liknande dokument på [Azure Toolkit för IntelliJ Quickstart](/java/azure/intellij/azure-toolkit-for-intellij-create-hello-world-web-app) eller [Azure Toolkit för Eclipse Snabbstart](/java/azure/eclipse/azure-toolkit-for-eclipse-create-hello-world-web-app).
>
![Exempelapp som körs i Azure](./media/app-service-web-get-started-java/java-hello-world-in-browser.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-java-app"></a>Skapa en Java-app

Kör följande Maven-kommando i Cloud Shell-prompten för att skapa en ny app med namnet `helloworld`:

```bash
mvn archetype:generate -DgroupId=example.demo -DartifactId=helloworld -DarchetypeArtifactId=maven-archetype-webapp
```

## <a name="configure-the-maven-plugin"></a>Konfigurera Maven-plugin-programmet

Distribuera från Maven genom att använda kodredigeraren i Cloud Shell för att öppna projektets `pom.xml`-fil i katalogen `helloworld`. 

```bash
code pom.xml
```

Lägg sedan till följande plugin-definition i `<build>`-elementet i filen `pom.xml`.

```xml
<plugins>
    <!--*************************************************-->
    <!-- Deploy to Tomcat in App Service Windows         -->
    <!--*************************************************-->
    <plugin>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-webapp-maven-plugin</artifactId>
        <version>1.6.0</version>
        <configuration>
            <!-- Specify v2 schema -->
            <schemaVersion>v2</schemaVersion>
            <!-- App information -->
            <subscriptionId>${SUBSCRIPTION_ID}</subscriptionId>
            <resourceGroup>${RESOURCEGROUP_NAME}</resourceGroup>
            <appName>${WEBAPP_NAME}</appName>
            <region>${REGION}</region>
            <!-- Java Runtime Stack for App Service on Windows-->
            <runtime>
                <os>windows</os>
                <javaVersion>1.8</javaVersion>
                <webContainer>tomcat 9.0</webContainer>
            </runtime>
            <deployment>
                <resources>
                    <resource>
                        <directory>${project.basedir}/target</directory>
                        <includes>
                            <include>*.war</include>
                        </includes>
                    </resource>
                </resources>
            </deployment>
        </configuration>
    </plugin>
</plugins>
```

> [!NOTE]
> I den här artikeln arbetar vi endast med Java-appar som paketerats i WAR-filer. Plugin-programmet stöder också JAR-webbprogram. Läs informationen om att [distribuera en Java SE JAR-fil till App Service på Linux](https://docs.microsoft.com/java/azure/spring-framework/deploy-spring-boot-java-app-with-maven-plugin?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) om du vill testa det.


Uppdatera följande platshållare i konfigurationen av plugin-program:

| Platshållare | Beskrivning |
| ----------- | ----------- |
| `SUBSCRIPTION_ID` | Unikt ID för prenumerationen som du vill distribuera din app. Standard prenumerations-ID finns från Cloud Shell eller CLI med den `az account show` kommando. Använd för alla tillgängliga prenumerationer den `az account list` kommando.|
| `RESOURCEGROUP_NAME` | Namnet på den nya resursgrupp där du vill skapa din app. Genom att lägga alla resurser för en app i en grupp, kan du hantera dem tillsammans. Genom att till exempel ta bort resursgruppen skulle du ta bort alla resurser som är associerade med appen. Uppdatera det här värdet med en unik ny resursgruppens namn, till exempel *myResourceGroup*. Du använder den här resursgruppens namn för att rensa alla Azure-resurser i ett senare avsnitt. |
| `WEBAPP_NAME` | Appnamnet som ska ingå i värdnamnet för appen när de distribueras till Azure (WEBAPP_NAME.azurewebsites.net). Uppdatera det här värdet med ett unikt namn för den nya App Service-appen, som blir värd för din Java-app, till exempel *contoso*. |
| `REGION` | En Azure-region där appen finns till exempel *westus2*. Du kan hämta en lista över regioner från Cloud Shell eller CLI med kommandot `az account list-locations`. |

## <a name="deploy-the-app"></a>Distribuera appen

Distribuera din Java-app till Azure med följande kommando:

```bash
mvn package azure-webapp:deploy
```

När distributionen är klar bläddrar du till den distribuerade tillämpningen med hjälp av följande webbadress i webbläsaren, till exempel `http://<webapp>.azurewebsites.net/`.

![Exempelapp som körs i Azure](./media/app-service-web-get-started-java/java-hello-world-in-browser.png)

**Grattis!** Du har distribuerat din första Java-app till App Service i Windows.

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Azure för Java-utvecklare resurser](/java/azure/)

> [!div class="nextstepaction"]
> [Mappa anpassad domän](app-service-web-tutorial-custom-domain.md)