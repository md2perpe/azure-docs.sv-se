---
title: Distribuera lösningen för fjärrövervakning lokalt (Visual Studio Code) – Azure | Microsoft Docs
description: Den här guiden visar hur du distribuerar den lösningsacceleratorn för fjärrövervakningen till den lokala datorn med hjälp av Visual Studio Code för utveckling och testning.
author: avneet723
manager: hegate
ms.author: avneets
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/17/2019
ms.topic: conceptual
ms.openlocfilehash: ed3301eb0e723e05e2a642ffea2f1609032553b4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66730182"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-locally---visual-studio-code"></a>Distribuera lösningsacceleratorn för fjärrövervakning lokalt – Visual Studio Code

[!INCLUDE [iot-accelerators-selector-local](../../includes/iot-accelerators-selector-local.md)]

Den här artikeln visar hur du distribuerar lösningsacceleratorn för fjärrövervakning till din lokala dator för utveckling och testning. Du lär dig hur du kör mikrotjänster i Visual Studio Code. En lokal mikrotjänster distribution använder följande molntjänster: IoT-hubb, Cosmos DB, Azure Strömningsanalys och Azure Time Series Insights.

## <a name="prerequisites"></a>Nödvändiga komponenter

För att distribuera Azure-tjänsterna används av lösningsacceleratorn för fjärrövervakning, behöver du en aktiv Azure-prenumeration.

Om du inte har något konto kan du skapa ett kostnadsfritt utvärderingskonto på bara några minuter. Mer information om den [kostnadsfria utvärderingsversionen av Azure](https://azure.microsoft.com/pricing/free-trial/).

### <a name="machine-setup"></a>Dator-installationen

För att slutföra lokal distribution, behöver du följande verktygen som installeras på din lokala utvecklingsdator:

* [Git](https://git-scm.com/)
* [.NET Core](https://dotnet.microsoft.com/download)
* [Docker](https://www.docker.com)
* [Nginx](https://nginx.org/en/download.html)
* [Visual Studio Code](https://code.visualstudio.com/)
* [VS Code C# tillägg](https://code.visualstudio.com/docs/languages/csharp)
* [Node.js v8](https://nodejs.org/) -programvaran är en förutsättning för PCS CLI som skript som använder för att skapa Azure-resurser. Använd inte v10 för Node.js

> [!NOTE]
> Visual Studio Code är tillgängligt för Windows, Mac och Ubuntu.

[!INCLUDE [iot-accelerators-local-setup](../../includes/iot-accelerators-local-setup.md)]

## <a name="run-the-microservices"></a>Kör mikrotjänster

I det här avsnittet ska köra du mikrotjänster för fjärrövervakning. Du kör webbgränssnittet internt, tjänsten Enhetssimulering i Docker och mikrotjänster i Visual Studio Code.

### <a name="build-the-code"></a>Skapa koden

Navigera till azure-iot-pcs-remote-monitoring-dotnet\services i Kommandotolken och kör följande kommandon för att skapa koden.

```cmd
dotnet restore
dotnet build -c Release
```

### <a name="deploy-all-other-microservices-on-local-machine"></a>Distribuera alla mikrotjänster på den lokala datorn

Följande steg visar hur du kör fjärrövervakning-mikrotjänster i Visual Studio Code:

1. Starta Visual Studio Code.
1. I VS Code, öppna den **azure-iot-pcs-remote-monitoring-dotnet** mapp.
1. Skapa en ny mapp med namnet **.vscode** i den **azure-iot-pcs-remote-monitoring-dotnet** mapp.
1. Kopiera filerna **launch.json** och **tasks.json** från services\scripts\local\launch\idesettings\vscode till den **.vscode** mapp som du nyss skapade.
1. Öppna den **Debug panelen** i VS Code och kör den **kör alla mikrotjänster** konfiguration. Den här konfigurationen körs enheten simulering mikrotjänster i Docker och kör andra mikrotjänster i felsökningsprogrammet.

Utdata från att köras **kör alla microsoervices** på Felsökningskonsolen ser ut som följande:

[![Deploy-Local-Microservices](./media/deploy-locally-vscode/auth-debug-results-inline.png)](./media/deploy-locally-vscode/auth-debug-results-expanded.png#lightbox)

### <a name="run-the-web-ui"></a>Kör webbgränssnittet

I det här steget ska starta du webbgränssnittet. Gå till **azure-iot-pcs-remote-monitoring-dotnet\webui** mapp i din lokala kopiera och kör följande kommandon:

```cmd
npm install
npm start
```

När början är klar visas sidan i webbläsaren **http:\//localhost:3000 / instrumentpanel**. Fel på den här sidan förväntas. Följ anvisningarna nedan om du vill visa programmet utan fel.

### <a name="configure-and-run-nginx"></a>Konfigurera och köra NGINX

Ställa in en omvänd proxy-server för att länka webbprogram och mikrotjänster som körs på den lokala datorn:

* Kopiera den **nginx.conf** fil från den **webui\scripts\localhost** mappen till den **nginx\conf** installationskatalog.
* Kör **nginx**.

Mer information om att köra **nginx**, se [nginx för Windows](https://nginx.org/en/docs/windows.html).

### <a name="connect-to-the-dashboard"></a>Ansluta till instrumentpanelen

Om du vill komma åt instrumentpanelen för fjärrövervakning lösningen måste gå till http:\//localhost:9000 i webbläsaren.

## <a name="clean-up"></a>Rensa

Ta bort molntjänsterna från din Azure-prenumeration för att undvika onödiga avgifter när du är klar med testet. Om du vill ta bort tjänsterna, navigera till den [Azure-portalen](https://ms.portal.azure.com) och ta bort resursen som den **start.cmd** skriptet som du skapade.

Du kan också ta bort den lokala kopian av databasen fjärrövervakning skapas när du har klonat källkoden från GitHub.

## <a name="next-steps"></a>Nästa steg

Nu när du har distribuerat av lösningen för fjärrövervakning, nästa steg är att [utforska funktionerna i lösningens instrumentpanel](quickstart-remote-monitoring-deploy.md).
