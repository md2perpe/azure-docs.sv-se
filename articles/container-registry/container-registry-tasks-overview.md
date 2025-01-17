---
title: Automatisera bygge och korrigeringar behållaravbildningar med Azure Container Registry (aktiviteter ACR)
description: En introduktion till ACR-aktiviteter, en uppsättning funktioner i Azure Container Registry som ger säker, automatiserad behållare bild build, hantering och korrigeringar i molnet.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 06/12/2019
ms.author: danlep
ms.openlocfilehash: 5089650996693b81e548bba8d89c0de29a8afd93
ms.sourcegitcommit: 72f1d1210980d2f75e490f879521bc73d76a17e1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/14/2019
ms.locfileid: "67147987"
---
# <a name="automate-container-image-builds-and-maintenance-with-acr-tasks"></a>Automatisera behållare avbildningar och underhåll med ACR-uppgifter

Behållare för nya nivåer av virtualisering, isolera program- och developer-beroenden från infrastrukturen och operativa krav. Vad är kvar, men är behovet av att hantera hur den här programvirtualisering hanteras och uppdaterad under hela livscykeln för behållaren.

## <a name="what-is-acr-tasks"></a>Vad är ACR uppgifter?

**ACR uppgifter** är en uppsättning funktioner i Azure Container Registry. Det ger molnbaserade behållare bild byggnad för Linux, Windows och ARM och kan automatisera [framework uppdatering av operativsystem och](#automate-os-and-framework-patching) för Docker-behållare. ACR-uppgifter inte bara utökar din ”inre slingan” utvecklingscykeln till molnet med behållare på begäran-avbildningar, men kan också versionsautomatisering på källan kodgenomförande eller när en behållare basavbildningen uppdateras. Med basavbildningen update-utlösare, kan du automatisera ditt operativsystem och programramverk korrigeringar arbetsflöde, upprätthålla säkra miljöer under beaktande huvudkonton oföränderligt behållare.

Skapa och testa behållaravbildningar med ACR uppgifter på fyra sätt:

* [Snabb uppgift](#quick-task): Skapa och skicka container-avbildningar på begäran, i Azure, utan en lokal Docker-motorn-installation. Tror `docker build`, `docker push` i molnet. Skapa från lokala källkoden eller en Git-lagringsplats.
* [Skapa på källan kodgenomförande](#automatic-build-on-source-code-commit): Utlös en behållare bild version automatiskt när koden strävar efter att en Git-lagringsplats.
* [Bygg på grundläggande uppdateringar](#automate-os-and-framework-patching): Utlös en behållare bild version när den avbildningen basavbildning har uppdaterats.
* [Uppgifter i flera steg](#multi-step-tasks): Definiera flera steg uppgifter som att skapa avbildningar kör behållare som kommandon och push-överför avbildningar till ett register. Den här funktionen i ACR uppgifter stöder körning av aktiviteten på begäran och parallella bild för att skapa, testa och push-åtgärder.

## <a name="quick-task"></a>Snabb uppgift

Inre slingan utvecklingscykeln, iterativ process för att skriva kod, skapa och testa ditt program innan du arrangerar källkontroll, som definieras i början av Livscykelhantering för behållaren.

Innan du skickar din första raden i kod, ACR uppgifter [snabb uppgift](container-registry-tutorial-quick-task.md) funktionen kan tillhandahålla en integrerad utvecklingsupplevelse genom att avlasta behållaravbildningen versioner till Azure. Med Snabbuppgifter, kan du kontrollera dina automatiserade byggesdefinitioner och fånga upp potentiella problem innan du checka in din kod.

Använda vanlig `docker build` format, den [az acr build] [ az-acr-build] kommandot i Azure CLI stöder en *kontext* (uppsättningen filer för att skapa), skickar det ACR uppgifter och som standard skickar den skapade avbildningen till dess registret när åtgärden har slutförts.

En introduktion finns i Snabbstart för att [skapa och köra en behållaravbildning](container-registry-quickstart-task-cli.md) i Azure Container Registry.  

I följande tabell visas några exempel på platser som stöds kontext för ACR uppgifter:

| Kontext plats | Beskrivning | Exempel |
| ---------------- | ----------- | ------- |
| Lokala filsystem | Filer i en katalog i det lokala filsystemet. | `/home/user/projects/myapp` |
| GitHub huvudgrenen | Filer i huvudservern (eller andra standard)-grenen av en GitHub-lagringsplats.  | `https://github.com/gituser/myapp-repo.git` |
| GitHub-gren | Viss förgrening av en GitHub-lagringsplatsen.| `https://github.com/gituser/myapp-repo.git#mybranch` |
| GitHub-undermapp | Filer i en undermapp i en GitHub-lagringsplatsen. Exemplet visar en kombination av en gren och undermappen specifikation. | `https://github.com/gituser/myapp-repo.git#mybranch:myfolder` |
| Remote tarball | Filer i en komprimerad fil på en fjärransluten webbserver. | `http://remoteserver/myapp.tar.gz` |

ACR uppgifter är utformad som en primitiv behållare livscykel. Till exempel integrera ACR uppgifter i din CI/CD-lösning. Genom att köra [az-inloggning] [ az-login] med en [tjänstens huvudnamn][az-login-service-principal], CI/CD-lösning kan sedan utfärdar [az acr skapa] [ az-acr-build] kommandon för att sätta igång bild versioner.

Lär dig hur du använder Snabbuppgifter i den första kursen i ACR uppgifter [skapa behållaravbildningar i molnet med Azure Container Registry uppgifter](container-registry-tutorial-quick-task.md).

## <a name="automatic-build-on-source-code-commit"></a>Automatisk byggd på källan kodgenomförande

Använd ACR uppgifter att automatiskt utlösa en behållaravbildning build när koden strävar efter att en Git-lagringsplats. Build-uppgifter, kan konfigureras med Azure CLI-kommando [az acr uppgift][az-acr-task], kan du ange en Git-lagringsplats och du kan också en gren och Dockerfile. När ditt team incheckningar kod till lagringsplatsen, utlöser en ACR-uppgifter som skapats webhook en version av den behållaravbildning som definierats i lagringsplatsen.

> [!IMPORTANT]
> Om du tidigare skapade uppgifter i förhandsversionen med kommandot `az acr build-task` behöver de uppgifterna skapas på nytt med hjälp av kommandot [az acr task][az-acr-task].

Lär dig hur du utlöser versioner på källan kodgenomförande i den andra självstudien i ACR uppgifter [automatisera behållaravbildning bygger med Azure Container Registry uppgifter](container-registry-tutorial-build-task.md).

## <a name="automate-os-and-framework-patching"></a>Automatisera framework uppdatering av operativsystem och

Kraften i ACR uppgifter att verkligen förbättra container build arbetsflödet kommer från dess förmåga att identifiera en uppdatering av en basavbildning. När den uppdaterade basavbildningen skickas till ditt register, eller en basavbildning uppdateras i en offentlig repo som i Docker Hub, skapa ACR uppgifter automatiskt alla programavbildningar som baseras på den.

Behållaravbildningar i stora drag kan kategoriseras i *grundläggande* avbildningar och *program* bilder. Din Källavbildningen inkluderar vanligtvis operativsystem och programramverk som ditt program har skapats, tillsammans med andra anpassningar. Dessa Källavbildningen är vanligtvis baserade på offentliga överordnade avbildningar, till exempel: [Alpine Linux][base-alpine], [Windows][base-windows], [.NET][base-dotnet], eller [Node.js ][base-node]. Flera av dina programavbildningar kan dela en gemensam basavbildning.

När en OS- eller app framework avbildning uppdateras av den överordnade Underhållaren, måste till exempel med en kritisk OS-säkerhetskorrigering du även uppdatera din grundläggande avbildningar för att inkludera kritiska korrigeringen. Varje programavbildningen måste sedan också byggas om så för att inkludera dessa överordnade korrigeringar ingår nu i din basavbildningen.

Eftersom ACR uppgifter identifierar dynamiskt basavbildningen beroenden när den bygger en behållaravbildning, kan den identifiera när en programavbildning basavbildning har uppdaterats. Med en förkonfigurerad [Skapa uppgift](container-registry-tutorial-base-image-update.md#create-a-task), ACR aktiviteter sedan **återskapas automatiskt varje programavbildningen** åt dig. Med den här automatisk identifiering och återskapa, ACR uppgifter sparar du tid och ansträngning som krävs för att manuellt spåra och uppdatera varje program bild refererar till din uppdaterade basavbildningen.

Lär dig mer om framework uppdatering i den tredje självstudien i ACR uppgifter av operativsystem och [automatisera bild bygger på grundläggande uppdateringar med Azure Container Registry uppgifter](container-registry-tutorial-base-image-update.md).

> [!NOTE]
> För närvarande uppdaterar basavbildningen utlösaren versioner bara om både bas- och program-avbildningar finns i samma Azure container registry eller basen finns i en offentlig Docker Hub eller Microsoft Container Registry-lagringsplats.

## <a name="multi-step-tasks"></a>Uppgifter i flera steg

Uppgifter i flera steg ger steg baserade aktivitetsdefinition och körning för att skapa, testa och uppdatering behållaravbildningar i molnet. Uppgiftsstegen definierar enskilda behållaravbildningars bygg- och push-åtgärder. De kan också definiera körningen av en eller flera behållare så varje steg använder behållaren som sin körningsmiljö.

Du kan till exempel skapa en uppgift i flera steg som automatiserar följande:

1. Skapa en avbildning för web-program
1. Kör webbprogramsbehållaren
1. Skapa en avbildning för web application test
1. Kör test webbprogramsbehållaren som utför tester mot löpande programbehållare
1. Om testet lyckas, skapa ett Helm-diagram Arkiv-paket
1. Utföra en `helm upgrade` med hjälp av nya Helm-diagram Arkiv-paketet

Uppgifter i flera steg kan du dela upp att bygga, köra och testa i en bild i mer sammansättningsbar steg, med stöd för kommunikation mellan steg beroende. Med flera steg åtgärder i ACR uppgifter har du mer detaljerad kontroll över bild att bygga, testa, och OS och framework korrigeringar arbetsflöden.

Lär dig om uppgifter i flera steg i [köra flera steg skapa, testa och patch uppgifter i ACR uppgifter](container-registry-tasks-multi-step.md).

## <a name="view-task-logs"></a>Visa aktivitet loggar

Varje aktivitet kör genererar loggutdata som du kan kontrollera för att avgöra om uppgift har körts. Om du använder den [az acr build](/cli/azure/acr#az-acr-build), [az acr kör](/cli/azure/acr#az-acr-run), eller [az acr-uppgiftskörning](/cli/azure/acr/task#az-acr-task-run) kommandot för att utlösa aktiviteten, loggutdata för aktiviteten kör strömmas till konsolen och sparas till senare hämtning. Visa loggar för en aktivitet körs i Azure portal eller Använd den [az acr uppgift loggar](/cli/azure/acr/task#az-acr-task-logs) kommando.

Från och med juli 2019, kommer data och loggfiler för körs i ett register vara kvar som standard i 30 dagar och sedan rensas automatiskt. Om du vill arkivera data för en aktivitet som körs kan du aktivera arkivering med hjälp av den [az acr update-körda](/cli/azure/acr/task#az-acr-task-update-run) kommando. I följande exempel aktiveras arkiverar för aktiviteten kör *cf11* i registret *myregistry*.

```azurecli
az acr task update-run --registry myregistry --run-id cf11 --no-archive false
```

## <a name="next-steps"></a>Nästa steg

När du är redo att automatisera framework uppdatering genom att skapa dina behållaravbildningar i molnet av operativsystem och ta en titt i tre delar [ACR uppgifter självstudieserien](container-registry-tutorial-quick-task.md).

Du kan också installera den [Docker-tillägg för Visual Studio Code](https://code.visualstudio.com/docs/azure/docker) och [Azure-konto](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) tillägg att arbeta med din Azure-behållarregister. Hämta och push-överför avbildningar till ett Azure container registry eller köra ACR aktiviteter, allt inom Visual Studio Code.

<!-- LINKS - External -->
[base-alpine]: https://hub.docker.com/_/alpine/
[base-dotnet]: https://hub.docker.com/r/microsoft/dotnet/
[base-node]: https://hub.docker.com/_/node/
[base-windows]: https://hub.docker.com/r/microsoft/nanoserver/
[sample-archive]: https://github.com/Azure-Samples/acr-build-helloworld-node/archive/master.zip
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-acr-task]: /cli/azure/acr
[az-login]: /cli/azure/reference-index#az-login
[az-login-service-principal]: /cli/azure/authenticate-azure-cli

<!-- IMAGES -->
[quick-build-01-fork]: ./media/container-registry-tutorial-quick-build/quick-build-01-fork.png
[quick-build-02-browser]: ./media/container-registry-tutorial-quick-build/quick-build-02-browser.png
