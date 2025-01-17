---
title: Skapa en app för flera containrar i Web App for Containers – Azure App Service
description: Lär dig hur du använder flera behållare på Azure med Docker Compose, WordPress och MySQL.
keywords: Azure apptjänst, webbapp, linux, docker compose, multicontainer, flera behållare, webbapp för behållare, flera behållare, behållare, wordpress, azure db för mysql, produktionsdatabasen med behållare
services: app-service
documentationcenter: ''
author: msangapu
manager: jeconnoc
editor: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/29/2019
ms.author: msangapu
ms.openlocfilehash: d1176d2d70dcd4de3e4287a1973de027c061f291
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67055549"
---
# <a name="tutorial-create-a-multi-container-preview-app-in-web-app-for-containers"></a>Självstudier: Skapa en app med flera containrar (förhandsversion) med Web App for Containers

Med [Web App for Containers](app-service-linux-intro.md) får du ett flexibelt sätt att använda Docker-avbildningar. I den här självstudien lär du dig hur du skapar en app med flera containrar med hjälp av WordPress och MySQL. Du genomför den här självstudiekursen i Cloud Shell men du kan även köra dessa kommandon lokalt med kommandoradsgränssnittet [Azure CLI](/cli/azure/install-azure-cli) (2.0.32 eller senare).

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Konvertera en Docker Compose-konfiguration så att den fungerar med Web App for Containers
> * Distribuera en app med flera containrar till Azure
> * Lägga till appinställningar
> * Använda beständig lagring för containrarna
> * Ansluta till Azure Database for MySQL
> * Felsöka fel

[!INCLUDE [Free trial note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Nödvändiga komponenter

Den här kursen behöver du erfarenhet av [Docker Compose](https://docs.docker.com/compose/).

## <a name="download-the-sample"></a>Hämta exemplet

I den här självstudien använder du Compose-filen från [Docker](https://docs.docker.com/compose/wordpress/#define-the-project), men du ändrar den för att ta med Azure Database for MySQL, beständig lagring och Redis. Konfigurationsfilen finns på [Azure Samples](https://github.com/Azure-Samples/multicontainerwordpress) (Azure-exempel).

[!code-yml[Main](../../../azure-app-service-multi-container/docker-compose-wordpress.yml)]

Alternativ för konfigurationer som stöds finns i [Docker Compose alternativ](configure-custom-container.md#docker-compose-options).

Skapa en självstudiekatalog i Cloud Shell och byt sedan till den.

```bash
mkdir tutorial

cd tutorial
```

Kör sedan följande kommando för att klona lagringsplatsen för exempelprogrammet till din självstudiekatalog. Ändra sedan till katalogen `multicontainerwordpress`.

```bash
git clone https://github.com/Azure-Samples/multicontainerwordpress

cd multicontainerwordpress
```

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

[!INCLUDE [resource group intro text](../../../includes/resource-group.md)]

Skapa i Cloud Shell en resursgrupp med kommandot [`az group create`](/cli/azure/group?view=azure-cli-latest#az-group-create). I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på platsen *South Central US* (Södra centrala USA). Om du vill se alla platser som stöds för App Service på Linux på **Standard**-nivån kör du kommandot [`az appservice list-locations --sku S1 --linux-workers-enabled`](/cli/azure/appservice?view=azure-cli-latest#az-appservice-list-locations).

```azurecli-interactive
az group create --name myResourceGroup --location "South Central US"
```

Du skapar vanligtvis din resursgrupp och resurserna i en region nära dig.

När kommandot har slutförts visar JSON-utdata resursgruppens egenskaper.

## <a name="create-an-azure-app-service-plan"></a>Skapa en Azure App Service-plan

Skapa i Cloud Shell en App Service-plan i resursgruppen med kommandot [`az appservice plan create`](/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create).

<!-- [!INCLUDE [app-service-plan](app-service-plan-linux.md)] -->

I följande exempel skapas en App Service-plan med namnet `myAppServicePlan` i prisnivån **Standard** (`--sku S1`) och i en Linux-containrar (`--is-linux`).

```azurecli-interactive
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku S1 --is-linux
```

När App Service-planen har skapats visas information av Cloud Shell. Informationen ser ut ungefär som i följande exempel:

```json
{
  "adminSiteName": null,
  "appServicePlanName": "myAppServicePlan",
  "geoRegion": "South Central US",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan",
  "kind": "linux",
  "location": "South Central US",
  "maximumNumberOfWorkers": 1,
  "name": "myAppServicePlan",
  < JSON data removed for brevity. >
  "targetWorkerSizeId": 0,
  "type": "Microsoft.Web/serverfarms",
  "workerTierName": null
}
```

### <a name="docker-compose-with-wordpress-and-mysql-containers"></a>Docker Compose med WordPress- och MySQL-containrar

## <a name="create-a-docker-compose-app"></a>Skapa en Docker Compose-app

I Cloud Shell skapar du en [webbapp](app-service-linux-intro.md) med flera containrar i `myAppServicePlan` App Service-planen med kommandot [az webapp create](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create). Glöm inte att ersätta  _\<appens namn->_ med ett unikt appnamn.

```azurecli-interactive
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app-name> --multicontainer-config-type compose --multicontainer-config-file docker-compose-wordpress.yml
```

När webbappen har skapats visar en Cloud Shell utdata liknande den i följande exempel:

```json
{
  "additionalProperties": {},
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app-name>.azurewebsites.net",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

### <a name="browse-to-the-app"></a>Bläddra till appen

Bläddra till den distribuerade appen på (`http://<app-name>.azurewebsites.net`). Det kan ta några minuter att läsa in appen. Om du får ett fel väntar du ytterligare ett par minuter och uppdaterar sedan webbläsaren. Om du har problem och vill felsöka kan du granska [containerloggarna](#find-docker-container-logs).

![Exempelapp med flera containrar i Web App for Containers][1]

**Grattis!** Du har skapat en app med flera containrar i Web App for Containers. Därefter konfigurerar du appen för att använda Azure Database for MySQL. Installera inte WordPress just nu.

## <a name="connect-to-production-database"></a>Ansluta till produktionsdatabasen

Det är inte rekommenderat att använda databascontainrar i en produktionsmiljö. Lokala containrar är inte skalbara. I stället använder du Azure Database for MySQL som kan skalas.

### <a name="create-an-azure-database-for-mysql-server"></a>Skapa en Azure Database for MySQL-server

Skapa en Azure Database for MySQL-server med kommandot [`az mysql server create`](/cli/azure/mysql/server?view=azure-cli-latest#az-mysql-server-create).

I följande kommando ersätter du ditt MySQL-servernamn där du ser den  _&lt;mysql-servernamn >_ platshållaren (giltiga tecken är `a-z`, `0-9`, och `-`). Det här namnet är en del av MySQL-serverns värdnamn (`<mysql-server-name>.database.windows.net`) och den måste vara globalt unik.

```azurecli-interactive
az mysql server create --resource-group myResourceGroup --name <mysql-server-name>  --location "South Central US" --admin-user adminuser --admin-password My5up3rStr0ngPaSw0rd! --sku-name B_Gen4_1 --version 5.7
```

Det kan ta några minuter att skapa servern. När MySQL-servern skapas visar Cloud Shell information som ser ut ungefär så här:

```json
{
  "administratorLogin": "adminuser",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "<mysql-server-name>.database.windows.net",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforMySQL/servers/<mysql-server-name>",
  "location": "southcentralus",
  "name": "<mysql-server-name>",
  "resourceGroup": "myResourceGroup",
  ...
}
```

### <a name="configure-server-firewall"></a>Konfigurera serverbrandväggen

Skapa en brandväggsregel för MySQL-servern för att tillåta klientanslutningar med hjälp av kommandot [`az mysql server firewall-rule create`](/cli/azure/mysql/server/firewall-rule?view=azure-cli-latest#az-mysql-server-firewall-rule-create). När både start-IP och slut-IP har angetts till 0.0.0.0 öppnas brandväggen endast för andra Azure-resurser.

```azurecli-interactive
az mysql server firewall-rule create --name allAzureIPs --server <mysql-server-name> --resource-group myResourceGroup --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!TIP]
> Du kan begränsa brandväggsregeln ännu mer genom att [endast använda de utgående IP-adresser som används av din app](../overview-inbound-outbound-ips.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#find-outbound-ips).
>

### <a name="create-the-wordpress-database"></a>Skapa WordPress-databasen

```azurecli-interactive
az mysql db create --resource-group myResourceGroup --server-name <mysql-server-name> --name wordpress
```

När databasen skapas visar Cloud Shell information som ser ut ungefär så här:

```json
{
  "additionalProperties": {},
  "charset": "latin1",
  "collation": "latin1_swedish_ci",
  "id": "/subscriptions/12db1644-4b12-4cab-ba54-8ba2f2822c1f/resourceGroups/myResourceGroup/providers/Microsoft.DBforMySQL/servers/<mysql-server-name>/databases/wordpress",
  "name": "wordpress",
  "resourceGroup": "myResourceGroup",
  "type": "Microsoft.DBforMySQL/servers/databases"
}
```

### <a name="configure-database-variables-in-wordpress"></a>Konfigurera databasvariabler i WordPress

För att ansluta WordPress-appen till den nya MySQL-servern konfigurerar du ett par WordPress-specifika miljövariabler, inklusive sökvägen för certifikatutfärdare för SSL som definieras av `MYSQL_SSL_CA`. [Baltimore CyberTrust Root](https://www.digicert.com/digicert-root-certificates.htm) från [DigiCert](https://www.digicert.com/) tillhandahålls i den [anpassade avbildningen](https://docs.microsoft.com/azure/app-service/containers/tutorial-multi-container-app#use-a-custom-image-for-mysql-ssl-and-other-configurations) nedan.

Du kan göra dessa ändringar med hjälp av kommandot [az webapp config appsettings set](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) i Cloud Shell. Appinställningar är skifteslägeskänsliga och avgränsas med blanksteg.

```azurecli-interactive
az webapp config appsettings set --resource-group myResourceGroup --name <app-name> --settings WORDPRESS_DB_HOST="<mysql-server-name>.mysql.database.azure.com" WORDPRESS_DB_USER="adminuser@<mysql-server-name>" WORDPRESS_DB_PASSWORD="My5up3rStr0ngPaSw0rd!" WORDPRESS_DB_NAME="wordpress" MYSQL_SSL_CA="BaltimoreCyberTrustroot.crt.pem"
```

När appinställningen har skapats visar Cloud Shell information som ser ut ungefär som i följande exempel:

```json
[
  {
    "name": "WORDPRESS_DB_HOST",
    "slotSetting": false,
    "value": "<mysql-server-name>.mysql.database.azure.com"
  },
  {
    "name": "WORDPRESS_DB_USER",
    "slotSetting": false,
    "value": "adminuser@<mysql-server-name>"
  },
  {
    "name": "WORDPRESS_DB_NAME",
    "slotSetting": false,
    "value": "wordpress"
  },
  {
    "name": "WORDPRESS_DB_PASSWORD",
    "slotSetting": false,
    "value": "My5up3rStr0ngPaSw0rd!"
  },
  {
    "name": "MYSQL_SSL_CA",
    "slotSetting": false,
    "value": "BaltimoreCyberTrustroot.crt.pem"
  }
]
```

Mer information om miljövariabler finns [konfigurera miljövariabler](configure-custom-container.md#configure-environment-variables).

### <a name="use-a-custom-image-for-mysql-ssl-and-other-configurations"></a>Använda en anpassad avbildning för MySQL SSL och andra konfigurationer

SSL används som standard av Azure Database for MySQL. WordPress kräver ytterligare konfiguration för att använda SSL med MySQL. WordPress ”officiella image” ger inte ytterligare konfiguration, men en [anpassad avbildning](https://github.com/Azure-Samples/multicontainerwordpress) har förberetts för din bekvämlighet. I vanliga fall skulle du ha lagt till önskade ändringar i en egen avbildning.

Den anpassade avbildningen är baserad på den officiella avbildningen av [WordPress från Docker Hub](https://hub.docker.com/_/wordpress/). Följande ändringar har gjorts i den här anpassade avbildningen för Azure Database for MySQL:

* [Lägger till Baltimore Cyber Trust Root-certifikatfilen för SSL i MySQL.](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/docker-entrypoint.sh#L61)
* [Använder appinställning för MySQL SSL-certifikatutfärdarcertifikat i WordPress wp-config.php.](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/docker-entrypoint.sh#L163)
* [Lägger till WordPress-definition för MYSQL_CLIENT_FLAGS som behövs för MySQL SSL.](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/docker-entrypoint.sh#L164)

Följande ändringar har gjorts för Redis (används i ett senare avsnitt):

* [Lägger till PHP-tillägg för Redis v4.0.2.](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/Dockerfile#L35)
* [Lägger till uppackning som behövs för extrahering av filer.](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/docker-entrypoint.sh#L71)
* [Lägger till WordPress-pluginprogrammet Redis Object Cache 1.3.8.](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/docker-entrypoint.sh#L74)
* [Använder appinställningen för Redis-värdnamnet i WordPress wp-config.php.](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/docker-entrypoint.sh#L162)

Om du vill använda den anpassade avbildningen uppdaterar du filen docker-compose-wordpress.yml. I Cloud Shell skriver du `nano docker-compose-wordpress.yml` för att öppna nanotextredigerare. Ändra `image: wordpress` för att använda `image: microsoft/multicontainerwordpress`. Du behöver inte längre databascontainern. Ta bort avsnittet  `db`, `environment`, `depends_on` och `volumes` från konfigurationsfilen. Din fil ska se ut som följande kod:

```yaml
version: '3.3'

services:
   wordpress:
     image: microsoft/multicontainerwordpress
     ports:
       - "8000:80"
     restart: always
```

Spara dina ändringar och avsluta nano. Använd kommandot `^O` för att spara och `^X` för att avsluta.

### <a name="update-app-with-new-configuration"></a>Uppdatera appen med en ny konfiguration

I Cloud Shell konfigurerar du om [webappen](app-service-linux-intro.md) med flera containrar med kommandot [az webapp config container set](/cli/azure/webapp/config/container?view=azure-cli-latest#az-webapp-config-container-set). Glöm inte att ersätta  _\<appens namn->_ med namnet på webbappen du skapade tidigare.

```azurecli-interactive
az webapp config container set --resource-group myResourceGroup --name <app-name> --multicontainer-config-type compose --multicontainer-config-file docker-compose-wordpress.yml
```

När webbappen har konfigurerats om visar Cloud Shell information liknande den i följande exempel:

```json
[
  {
    "name": "DOCKER_CUSTOM_IMAGE_NAME",
    "value": "COMPOSE|dmVyc2lvbjogJzMuMycKCnNlcnZpY2VzOgogICB3b3JkcHJlc3M6CiAgICAgaW1hZ2U6IG1zYW5nYXB1L3dvcmRwcmVzcwogICAgIHBvcnRzOgogICAgICAgLSAiODAwMDo4MCIKICAgICByZXN0YXJ0OiBhbHdheXM="
  }
]
```

### <a name="browse-to-the-app"></a>Bläddra till appen

Bläddra till den distribuerade appen på (`http://<app-name>.azurewebsites.net`). Appen använder nu Azure Database for MySQL.

![Exempelapp med flera containrar i Web App for Containers][1]

## <a name="add-persistent-storage"></a>Lägga till beständig lagring

Din app med flera containrar körs nu i Web App for Containers. Men om du installerar WordPress nu och startar om appen senare märker du att WordPress-installationen är borta. Det beror på att Docker Compose-konfigurationen för närvarande pekar på en lagringsplats inne i containern. Filerna som installeras i containern bevaras inte när appen startas om. I det här avsnittet ska du [lägga till beständig lagring](configure-custom-container.md#use-persistent-shared-storage) till din WordPress-behållare.

### <a name="configure-environment-variables"></a>Konfigurera miljövariabler

Om du vill använda beständig lagring, ska du aktivera den här inställningen i App Service. Du kan göra denna ändring med hjälp av kommandot [az webapp config appsettings set](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) i Cloud Shell. Appinställningar är skifteslägeskänsliga och avgränsas med blanksteg.

```azurecli-interactive
az webapp config appsettings set --resource-group myResourceGroup --name <app-name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=TRUE
```

När appinställningen har skapats visar Cloud Shell information som ser ut ungefär som i följande exempel:

```json
[
  < JSON data removed for brevity. >
  {
    "name": "WORDPRESS_DB_NAME",
    "slotSetting": false,
    "value": "wordpress"
  },
  {
    "name": "WEBSITES_ENABLE_APP_SERVICE_STORAGE",
    "slotSetting": false,
    "value": "TRUE"
  }
]
```

### <a name="modify-configuration-file"></a>Ändra konfigurationsfilen

I Cloud Shell skriver du `nano docker-compose-wordpress.yml` för att öppna nanotextredigerare.

Alternativet `volumes` mappar filsystemet till en katalog i containern. `${WEBAPP_STORAGE_HOME}` är en miljövariabel i App Service som är mappad till beständig lagring för din app. Du använder miljövariabeln i volymalternativet så att WordPress-filerna installeras i den beständiga lagringen i stället för containern. Gör följande ändringar i filen:

I avsnittet `wordpress` lägger du till ett `volumes`-alternativ så att det ser ut som i följande kod:

```yaml
version: '3.3'

services:
   wordpress:
     image: microsoft/multicontainerwordpress
     volumes:
      - ${WEBAPP_STORAGE_HOME}/site/wwwroot:/var/www/html
     ports:
       - "8000:80"
     restart: always
```

### <a name="update-app-with-new-configuration"></a>Uppdatera appen med en ny konfiguration

I Cloud Shell konfigurerar du om [webappen](app-service-linux-intro.md) med flera containrar med kommandot [az webapp config container set](/cli/azure/webapp/config/container?view=azure-cli-latest#az-webapp-config-container-set). Glöm inte att ersätta  _\<appens namn->_ med ett unikt appnamn.

```azurecli-interactive
az webapp config container set --resource-group myResourceGroup --name <app-name> --multicontainer-config-type compose --multicontainer-config-file docker-compose-wordpress.yml
```

När kommandot körs visas utdata som ser ut ungefär som i följande exempel:

```json
[
  {
    "name": "WEBSITES_ENABLE_APP_SERVICE_STORAGE",
    "slotSetting": false,
    "value": "TRUE"
  },
  {
    "name": "DOCKER_CUSTOM_IMAGE_NAME",
    "value": "COMPOSE|dmVyc2lvbjogJzMuMycKCnNlcnZpY2VzOgogICBteXNxbDoKICAgICBpbWFnZTogbXlzcWw6NS43CiAgICAgdm9sdW1lczoKICAgICAgIC0gZGJfZGF0YTovdmFyL2xpYi9teXNxbAogICAgIHJlc3RhcnQ6IGFsd2F5cwogICAgIGVudmlyb25tZW50OgogICAgICAgTVlTUUxfUk9PVF9QQVNTV09SRDogZXhhbXBsZXBhc3MKCiAgIHdvcmRwcmVzczoKICAgICBkZXBlbmRzX29uOgogICAgICAgLSBteXNxbAogICAgIGltYWdlOiB3b3JkcHJlc3M6bGF0ZXN0CiAgICAgcG9ydHM6CiAgICAgICAtICI4MDAwOjgwIgogICAgIHJlc3RhcnQ6IGFsd2F5cwogICAgIGVudmlyb25tZW50OgogICAgICAgV09SRFBSRVNTX0RCX1BBU1NXT1JEOiBleGFtcGxlcGFzcwp2b2x1bWVzOgogICAgZGJfZGF0YTo="
  }
]
```

### <a name="browse-to-the-app"></a>Bläddra till appen

Bläddra till den distribuerade appen på (`http://<app-name>.azurewebsites.net`).

Nu används Azure Database for MySQL och beständig lagring i WordPress-containern.

## <a name="add-redis-container"></a>Lägga till Redis-container

 Den officiella avbildningen för WordPress innehåller inte beroenden för Redis. Dessa beroenden och ytterligare konfiguration som behövs för att använda Regis med WordPress har förberetts för dig i den här [anpassade avbildningen](https://github.com/Azure-Samples/multicontainerwordpress). I vanliga fall skulle du ha lagt till önskade ändringar i en egen avbildning.

Den anpassade avbildningen är baserad på den officiella avbildningen av [WordPress från Docker Hub](https://hub.docker.com/_/wordpress/). Följande ändringar har gjorts i den här anpassade avbildningen för Redis:

* [Lägger till PHP-tillägg för Redis v4.0.2.](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/Dockerfile#L35)
* [Lägger till uppackning som behövs för extrahering av filer.](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/docker-entrypoint.sh#L71)
* [Lägger till WordPress-pluginprogrammet Redis Object Cache 1.3.8.](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/docker-entrypoint.sh#L74)
* [Använder appinställningen för Redis-värdnamnet i WordPress wp-config.php.](https://github.com/Azure-Samples/multicontainerwordpress/blob/5669a89e0ee8599285f0e2e6f7e935c16e539b92/docker-entrypoint.sh#L162)

Lägg till Redis-containern i slutet av konfigurationsfilen som i följande exempel:

```yaml
version: '3.3'

services:
   wordpress:
     image: microsoft/multicontainerwordpress
     ports:
       - "8000:80"
     restart: always

   redis:
     image: redis:3-alpine
     restart: always
```

### <a name="configure-environment-variables"></a>Konfigurera miljövariabler

Aktivera inställningen `WP_REDIS_HOST` i App Service så att du kan använda Redis. Det här är en *obligatorisk inställning* för att WordPress ska kunna kommunicera med Redis-värden. Du kan göra denna ändring med hjälp av kommandot [az webapp config appsettings set](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) i Cloud Shell. Appinställningar är skifteslägeskänsliga och avgränsas med blanksteg.

```azurecli-interactive
az webapp config appsettings set --resource-group myResourceGroup --name <app-name> --settings WP_REDIS_HOST="redis"
```

När appinställningen har skapats visar Cloud Shell information som ser ut ungefär som i följande exempel:

```json
[
  < JSON data removed for brevity. >
  {
    "name": "WORDPRESS_DB_USER",
    "slotSetting": false,
    "value": "adminuser@<mysql-server-name>"
  },
  {
    "name": "WP_REDIS_HOST",
    "slotSetting": false,
    "value": "redis"
  }
]
```

### <a name="update-app-with-new-configuration"></a>Uppdatera appen med en ny konfiguration

I Cloud Shell konfigurerar du om [webappen](app-service-linux-intro.md) med flera containrar med kommandot [az webapp config container set](/cli/azure/webapp/config/container?view=azure-cli-latest#az-webapp-config-container-set). Glöm inte att ersätta  _\<appens namn->_ med ett unikt appnamn.

```azurecli-interactive
az webapp config container set --resource-group myResourceGroup --name <app-name> --multicontainer-config-type compose --multicontainer-config-file compose-wordpress.yml
```

När kommandot körs visas utdata som ser ut ungefär som i följande exempel:

```json
[
  {
    "name": "DOCKER_CUSTOM_IMAGE_NAME",
    "value": "COMPOSE|dmVyc2lvbjogJzMuMycKCnNlcnZpY2VzOgogICBteXNxbDoKICAgICBpbWFnZTogbXlzcWw6NS43CiAgICAgdm9sdW1lczoKICAgICAgIC0gZGJfZGF0YTovdmFyL2xpYi9teXNxbAogICAgIHJlc3RhcnQ6IGFsd2F5cwogICAgIGVudmlyb25tZW50OgogICAgICAgTVlTUUxfUk9PVF9QQVNTV09SRDogZXhhbXBsZXBhc3MKCiAgIHdvcmRwcmVzczoKICAgICBkZXBlbmRzX29uOgogICAgICAgLSBteXNxbAogICAgIGltYWdlOiB3b3JkcHJlc3M6bGF0ZXN0CiAgICAgcG9ydHM6CiAgICAgICAtICI4MDAwOjgwIgogICAgIHJlc3RhcnQ6IGFsd2F5cwogICAgIGVudmlyb25tZW50OgogICAgICAgV09SRFBSRVNTX0RCX1BBU1NXT1JEOiBleGFtcGxlcGFzcwp2b2x1bWVzOgogICAgZGJfZGF0YTo="
  }
]
```

### <a name="browse-to-the-app"></a>Bläddra till appen

Bläddra till den distribuerade appen på (`http://<app-name>.azurewebsites.net`).

Utför stegen och installera WordPress.

### <a name="connect-wordpress-to-redis"></a>Ansluta WordPress till Redis

Logga in till WordPress-administratören. I navigeringen till vänster väljer du **Plugins** (Plugin-program) och sedan **Installed Plugins** (Installerade plugin-program).

![Välj WordPress-plugin-program][2]

Visa alla plugin-program här

På sidan för plugin-program letar du rätt på **Redis Object Cache** och klickar på **Aktivera**.

![Aktivera Redis][3]

Klicka på **Inställningar**.

![Klicka på Inställningar][4]

Klicka på **Enable Object Cache** (Aktivera Object Cache).

![Klicka på Enable Object Cache (Aktivera Object Cache)][5]

WordPress ansluter till Redis-servern. Anslutningens **status** visas på samma sida.

![WordPress ansluter till Redis-servern. Anslutningens **status** visas på samma sida.][6]

**Gratulerar!** Du har anslutit WordPress till Redis. Den produktionsklara appen använder nu **Azure Database for MySQL, beständig lagring och Redis**. Nu kan du skala ut din App Service-plan till flera instanser.

## <a name="find-docker-container-logs"></a>Hitta loggar för Docker-containrar

Om du stöter på problem när du använder flera containrar kan du se loggar för containrarna genom att gå till: `https://<app-name>.scm.azurewebsites.net/api/logs/docker`.

Utdata ser ut ungefär som i följande exempel:

```json
[
   {
      "machineName":"RD00XYZYZE567A",
      "lastUpdated":"2018-05-10T04:11:45Z",
      "size":25125,
      "href":"https://<app-name>.scm.azurewebsites.net/api/vfs/LogFiles/2018_05_10_RD00XYZYZE567A_docker.log",
      "path":"/home/LogFiles/2018_05_10_RD00XYZYZE567A_docker.log"
   }
]
```

Du ser en logg för varje container och ytterligare en logg för den överordnade processen. Kopiera respektive `href`-värde till webbläsaren om du vill visa loggen.

[!INCLUDE [Clean-up section](../../../includes/cli-script-clean-up.md)]

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:
> [!div class="checklist"]
> * Konvertera en Docker Compose-konfiguration så att den fungerar med Web App for Containers
> * Distribuera en app med flera containrar till Azure
> * Lägga till appinställningar
> * Använda beständig lagring för containrarna
> * Ansluta till Azure Database for MySQL
> * Felsöka fel

Gå vidare till nästa självstudie för att läsa hur du mappar ett anpassat DNS-namn till din app.

> [!div class="nextstepaction"]
> [Självstudie: Mappa anpassad DNS-namn till din app](../app-service-web-tutorial-custom-domain.md)

Eller titta på andra resurser:

> [!div class="nextstepaction"]
> [Konfigurera anpassade behållaren](configure-custom-container.md)

<!--Image references-->
[1]: ./media/tutorial-multi-container-app/azure-multi-container-wordpress-install.png
[2]: ./media/tutorial-multi-container-app/wordpress-plugins.png
[3]: ./media/tutorial-multi-container-app/activate-redis.png
[4]: ./media/tutorial-multi-container-app/redis-settings.png
[5]: ./media/tutorial-multi-container-app/enable-object-cache.png
[6]: ./media/tutorial-multi-container-app/redis-connected.png
