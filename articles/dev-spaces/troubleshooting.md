---
title: Felsökning
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 09/11/2018
ms.topic: conceptual
description: Snabb Kubernetes-utveckling med containrar och mikrotjänster i Azure
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, behållare, Helm, tjänsten nät, tjänsten nät routning, kubectl, k8s '
ms.openlocfilehash: 651ae9d9f9a622724e1ee606219ba940995aa555
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67441752"
---
# <a name="troubleshooting-guide"></a>Felsökningsguide

Den här guiden innehåller information om vanliga problem som du kan ha när du använder Azure Dev blanksteg.

Om du har problem när du använder Azure Dev blanksteg, skapar du en [problem i Azure Dev blanksteg GitHub-lagringsplatsen](https://github.com/Azure/dev-spaces/issues).

## <a name="enabling-detailed-logging"></a>Aktivera detaljerad loggning

Det kan hjälpa för att skapa mer detaljerade loggar för granskning för att felsöka problem med mer effektivt.

Visual Studio-tillägg, ange den `MS_VS_AZUREDEVSPACES_TOOLS_LOGGING_ENABLED` miljövariabeln till 1. Glöm inte att starta om Visual Studio för miljövariabeln ska börja gälla. När du har aktiverat, detaljerade loggarna skrivs till din `%TEMP%\Microsoft.VisualStudio.Azure.DevSpaces.Tools` directory.

I CLI, kan du mata ut mer information under Kommandokörning med hjälp av den `--verbose` växla. Du kan också gå mer detaljerade loggar i `%TEMP%\Azure Dev Spaces`. På en Mac till TEMP-katalogen kan hittas genom att köra `echo $TMPDIR` från ett terminalfönster. På en Linux-dator till TEMP-katalogen är vanligtvis `/tmp`.

## <a name="debugging-services-with-multiple-instances"></a>Felsökning av tjänster med flera instanser

För närvarande Azure Dev blanksteg fungerar bäst när du felsöker en enda instans, eller pod. Azds.yaml-filen innehåller en inställning *replicaCount*, innebär det att antalet poddar som Kubernetes som körs i din tjänst. Om du ändrar replicaCount för att konfigurera din app för att köra flera poddar för en viss tjänst, bifogar felsökningsprogrammet till den första pod när visas i alfabetisk ordning. Felsökningsprogrammet kopplar till en annan pod när den ursprungliga pod återanvänds till oväntade resultat.

## <a name="error-failed-to-create-azure-dev-spaces-controller"></a>Felet ”Det gick inte att skapa Azure Dev blanksteg controller'

### <a name="reason"></a>Reason
Det här felet kan uppstå när något går fel med skapandet av kontrollanten. Om det är ett tillfälligt fel, ta bort och återskapa styrenheten för att åtgärda det.

### <a name="try"></a>Testa

Ta bort kontrollanten:

```bash
azds remove -g <resource group name> -n <cluster name>
```

Du måste använda Azure Dev blanksteg CLI för att ta bort en domänkontrollant. Det går inte att ta bort en domänkontrollant från Visual Studio. Du kan även installera Azure Dev blanksteg CLI i Azure Cloud Shell så att du inte kan ta bort en domänkontrollant från Azure Cloud Shell.

Om du inte har installerat Azure Dev blanksteg CLI kan du först installera den med hjälp av följande kommando sedan ta bort din kontrollant:

```cmd
az aks use-dev-spaces -g <resource group name> -n <cluster name>
```

Återskapa kontrollanten kan du göra detta från CLI eller Visual Studio. Se den [Team utveckling](quickstart-team-development.md) eller [utveckla med .NET Core](quickstart-netcore-visualstudio.md) snabbstarter exempel.

## <a name="error-service-cannot-be-started"></a>Fel-tjänsten kan inte startas ”.

Det här felet kan uppstå när koden för tjänsten inte startar. Orsaken är ofta i användarkod. För att få diagnostisk information, göra följande ändringar i dina kommandon och inställningar:

### <a name="try"></a>Prova:

På kommandoraden:

När du använder _azds.exe_, använda--utförlig kommandoradsalternativet och använda kommandoradsalternativet--utdata att ange formatet för utdata.
 
```cmd
azds up --verbose --output json
```

I Visual Studio:

1. Öppna **Verktyg > alternativ** och under **produkter och lösningar**, Välj **skapa och köra**.
2. Ändra inställningarna för **MSBuild projekt build utdata utförlighet** till **detaljerat** eller **diagnostiska**.

    ![Skärmbild av alternativ för dialogrutan](media/common/VerbositySetting.PNG)
    
### <a name="multi-stage-dockerfiles"></a>Flera steg Dockerfiles:
Du får en *går inte att starta tjänsten* fel när du använder en flerstegs Dockerfile. I det här fallet innehåller den utförliga utdata följande text:

```cmd
$ azds up -v
Using dev space 'default' with target 'AksClusterName'
Synchronizing files...6s
Installing Helm chart...2s
Waiting for container image build...10s
Building container image...
Step 1/12 : FROM [imagename:tag] AS base
Error parsing reference: "[imagename:tag] AS base" is not a valid repository/tag: invalid reference format
Failed to build container image.
Service cannot be started.
```

Det här felet uppstår eftersom bygger AKS-noder som kör en äldre version av Docker som inte stöder flera steg. Skriv om din Dockerfile för att undvika flera steg versioner.

### <a name="rerunning-a-service-after-controller-re-creation"></a>Köra en tjänst efter skapandet av domänkontrollanten igen
Du får en *går inte att starta tjänsten* fel vid försök att köra en tjänst när du har tagit bort och återskapas kontrollanten Azure Dev blanksteg som är associerade med det här klustret. I det här fallet innehåller den utförliga utdata följande text:

```cmd
Installing Helm chart...
Release "azds-33d46b-default-webapp1" does not exist. Installing it now.
Error: release azds-33d46b-default-webapp1 failed: services "webapp1" already exists
Helm install failed with exit code '1': Release "azds-33d46b-default-webapp1" does not exist. Installing it now.
Error: release azds-33d46b-default-webapp1 failed: services "webapp1" already exists
```

Det här felet beror på att ta bort kontrollanten Dev blanksteg tas inte bort tidigare installerade av den controller. Återskapande av kontrollanten och sedan försöka att köra tjänster som använder den nya domänkontrollanten misslyckas eftersom de gamla tjänsterna fortfarande är kvar.

Du löser problemet genom att använda den `kubectl delete` kommando för att manuellt ta bort de gamla tjänsterna från ditt kluster och kör sedan Dev blanksteg för att installera de nya tjänsterna.

## <a name="dns-name-resolution-fails-for-a-public-url-associated-with-a-dev-spaces-service"></a>DNS-namnmatchningen misslyckas för en offentlig URL som är associerade med en tjänst för utveckling blanksteg

Du kan konfigurera en offentlig URL-slutpunkt för din tjänst genom att ange den `--public` växla till den `azds prep` kommandot, eller genom att välja den `Publicly Accessible` kryssrutan i Visual Studio. Det offentliga DNS-namnet registreras automatiskt när du kör din tjänst i Dev blanksteg. Om den här DNS-namn inte har registrerats, visas en *sidan kan inte visas* eller *plats går inte att nå* fel i webbläsaren när du ansluter till en offentlig URL.

### <a name="try"></a>Prova:

Du kan använda följande kommando för att lista ut alla URL: er som är associerade med dina Dev blanksteg tjänster:

```cmd
azds list-uris
```

Om en Webbadress finns i den *väntande* tillstånd, som innebär att Dev blanksteg väntar fortfarande för DNS-registrering att slutföra. Ibland kan tar det några minuter för registrering för att slutföra. Dev blanksteg öppnas även en localhost-tunnel för varje tjänst, som du kan använda under väntan på DNS-registrering.

Om en URL som ligger i den *väntande* tillstånd under mer än 5 minuter, det kan bero på problem med den externa DNS-pod som skapar den offentliga slutpunkten eller nginx ingående controller pod som hämtar den offentliga slutpunkten. Du kan använda följande kommandon för att ta bort dessa poddar. AKS återskapas automatiskt borttagna poddarna.

```cmd
kubectl delete pod -n kube-system -l app=addon-http-application-routing-external-dns
kubectl delete pod -n kube-system -l app=addon-http-application-routing-nginx-ingress
```

## <a name="error-required-tools-and-configurations-are-missing"></a>Fel krävs verktyg och konfigurationer som saknas

Det här felet kan uppstå när du startar VS Code ”: [Azure Dev blanksteg] nödvändiga verktyg och konfigurationer för att skapa och Felsök '[projekt namn]' saknas”.
Felet innebär att azds.exe inte är i miljövariabeln PATH som visas i VS Code.

### <a name="try"></a>Prova:

Starta VS Code från en kommandotolk där PATH-miljövariabeln är korrekt.

## <a name="error-required-tools-to-build-and-debug-projectname-are-out-of-date"></a>Fel ”nödvändiga verktyg för att skapa och Felsök 'projectname' är inaktuell”.

Du ser detta fel i Visual Studio Code om du har en nyare version av VS Code-tillägg för Azure Dev blanksteg, men en äldre version av Azure Dev blanksteg CLI.

### <a name="try"></a>Testa

Hämta och installera den senaste versionen av Azure Dev blanksteg CLI:

* [Windows](https://aka.ms/get-azds-windows)
* [Mac](https://aka.ms/get-azds-mac)
* [Linux](https://aka.ms/get-azds-linux)

## <a name="error-azds-is-not-recognized-as-an-internal-or-external-command-operable-program-or-batch-file"></a>Fel 'azds' identifieras inte som ett internt eller externt kommando, ett körbart program eller en batchfil
 
Du kan se det här felet om azds.exe inte är installerat eller korrekt konfigurerat.

### <a name="try"></a>Prova:

1. Kontrollera plats-%ProgramFiles%/Microsoft SDKs\Azure\Azure Dev blanksteg CLI för azds.exe. Om det är det lägger du till den platsen att miljövariabeln PATH.
2. Om azds.exe inte är installerad, kör du följande kommando:

    ```cmd
    az aks use-dev-spaces -n <cluster-name> -g <resource-group>
    ```

## <a name="warning-dockerfile-could-not-be-generated-due-to-unsupported-language"></a>Varning ”Dockerfile kunde inte genereras på grund av språket stöds inte.
Azure Dev blanksteg har inbyggt stöd för C# och Node.js. När du kör *azds prep* i en katalog som innehåller kod som skrivs i ett av dessa språk, Azure Dev blanksteg automatiskt skapar en lämplig Dockerfile åt dig.

Du kan fortfarande använda Azure Dev blanksteg med kod som skrivits på andra språk, men du måste manuellt skapa Dockerfile innan du kör *azds upp* för första gången.

### <a name="try"></a>Prova:
Om ditt program är skrivna på ett språk att Azure Dev blanksteg inte har inbyggt stöd, måste du ange en lämplig Dockerfile för att skapa en behållaravbildning som kör koden. Docker innehåller en [lista över bästa praxis för att skriva Dockerfiles](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/) och en [Dockerfile-referensen](https://docs.docker.com/engine/reference/builder/) som hjälper dig att skriva en Dockerfile som passar dina behov.

När du har en lämplig Dockerfile på plats kan du fortsätta med körs *azds upp* att köra ditt program i Azure Dev blanksteg.

## <a name="error-upstream-connect-error-or-disconnectreset-before-headers"></a>Fel ”uppströms anslutningsfel eller koppla från/reset innan rubriker”
Du kan se det här felet när du försöker komma åt tjänsten. Till exempel när du går till tjänstens URL i en webbläsare. 

### <a name="reason"></a>Orsak 
Behållare-port är inte tillgängligt. Det här problemet kan inträffa därför att: 
* Behållaren håller fortfarande som skapat och distribuerat. Det här problemet kan uppstå om du kör `azds up` eller startar felsökningsprogrammet och försök sedan att få åtkomst till behållaren innan den har distribuerats.
* Portkonfiguration är inte konsekventa i din _Dockerfile_, Helm-diagrammet och eventuella serverkod som öppnar en port.

### <a name="try"></a>Prova:
1. Om behållaren håller på att bygger/distribueras, kan du vänta 2-3 sekunder och testa åtkomst till tjänsten igen. 
1. Kontrollera i portkonfigurationen. De angivna portnummer måste vara **identiska** i alla följande resurser:
    * **Dockerfile:** Anges av den `EXPOSE` instruktion.
    * **[Helm-diagrammet](https://docs.helm.sh):** Anges av den `externalPort` och `internalPort` värden för en tjänst (ofta finns i en `values.yml` fil),
    * Alla portar som öppnas i programkoden, till exempel i Node.js: `var server = app.listen(80, function () {...}`


## <a name="config-file-not-found"></a>Det gick inte att hitta konfigurationsfilen
Du kör `azds up` och följande felmeddelande: `Config file not found: .../azds.yaml`

### <a name="reason"></a>Orsak
Du måste köra `azds up` från rotkatalogen för kod som du vill köra och måste du initiera mappen kod att köra Azure Dev blanksteg.

### <a name="try"></a>Prova:
1. Ändra katalogen till rotmappen som innehåller koden för tjänsten. 
1. Om du inte har en _azds.yaml_ filen i mappen kod kör `azds prep` att generera Docker, Kubernetes och Azure Dev blanksteg tillgångar.

## <a name="error-the-pipe-program-azds-exited-unexpectedly-with-code-126"></a>Fel: 'Pipe programmet 'azds' avslutades oväntat med koden 126 ”.
Starta VS Code-felsökare kan ibland resultera i det här felet.

### <a name="try"></a>Prova:
1. Stäng och öppna VS Code.
2. Tryck på F5 igen.

## <a name="debugging-error-failed-to-find-debugger-extension-for-typecoreclr"></a>Felmeddelandet ”Det gick inte att hitta felsökare tillägg för typen: coreclr'-felsökning
Köra felsökaren för VS Code rapporterar felet: `Failed to find debugger extension for type:coreclr.`

### <a name="reason"></a>Orsak
Du har inte det VS Code-tillägget för C#-installerat på utvecklingsdatorn. Den C# tillägget innehåller felsökning för .NET Core (CoreCLR).

### <a name="try"></a>Prova:
Installera den [VS Code-tillägg för C#](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).

## <a name="debugging-error-configured-debug-type-coreclr-is-not-supported"></a>Felsöka fel 'konfigurerad debug typen 'coreclr' stöds inte ”
Köra felsökaren för VS Code rapporterar felet: `Configured debug type 'coreclr' is not supported.`

### <a name="reason"></a>Orsak
Du har inte det VS Code-tillägget för Azure Dev blanksteg är installerat på utvecklingsdatorn.

### <a name="try"></a>Prova:
Installera den [VS Code-tillägg för Azure Dev blanksteg](get-started-netcore.md).

## <a name="debugging-error-invalid-cwd-value-src-the-system-cannot-find-the-file-specified-or-launch-program-srcpath-to-project-binary-does-not-exist"></a>Felsöka fel ”ogiltig cwd värdet ' / src'. Det går inte att hitta den angivna filen ”. eller ”starta: programmet '/ src / [sökväg till projektet binär]' finns inte”
Köra felsökaren för VS Code rapporterar felet `Invalid 'cwd' value '/src'. The system cannot find the file specified.` och/eller `launch: program '/src/[path to project executable]' does not exist`

### <a name="reason"></a>Orsak
Som standard använder VS Code-tillägg `src` som arbetskatalog för projektet för behållaren. Om du har uppdaterat din `Dockerfile` för att ange en annan arbetskatalog, kanske du ser det här felet.

### <a name="try"></a>Prova:
Uppdatera den `launch.json` filen under den `.vscode` underkatalog i projektmappen. Ändra den `configurations->cwd` direktiv så att den pekar till samma katalog som den `WORKDIR` definieras i projektets `Dockerfile`. Du kan också behöva uppdatera den `configurations->program` direktiv samt.

## <a name="the-type-or-namespace-name-mylibrary-could-not-be-found"></a>Det gick inte att hitta typen eller namnrymd namnet ”MyLibrary”

### <a name="reason"></a>Reason 
Build-kontexten är på projekt/tjänstnivå som standard, kan därför ett biblioteksprojekt som du använder inte hittas.

### <a name="try"></a>Prova:
Vad som behöver göras:
1. Ändra den _azds.yaml_ filen för att ange build-kontext till lösningen nivå.
2. Ändra den _Dockerfile_ och _Dockerfile.develop_ filer att referera till projektet ( _.csproj_) filer på rätt sätt, i förhållande till den nya skapa kontexten.
3. Plats en _.dockerignore_ filen bredvid SLN-filen och ändra vid behov.

Du kan se ett exempel på https://github.com/sgreenmsft/buildcontextsample

## <a name="microsoftdevspacesregisteraction-authorization-error"></a>'Microsoft.DevSpaces/register/action' auktoriseringsfel
Du behöver *ägare* eller *deltagare* åtkomst i Azure-prenumerationen du hanterar Azure Dev blanksteg. Du kan se det här felet om du försöker hantera Dev blanksteg och du inte har *ägare* eller *deltagare* åtkomst till den associera Azure-prenumerationen.
`The client '<User email/Id>' with object id '<Guid>' does not have authorization to perform action 'Microsoft.DevSpaces/register/action' over scope '/subscriptions/<Subscription Id>'.`

### <a name="reason"></a>Reason
Den valda Azure-prenumerationen har inte registrerats i `Microsoft.DevSpaces` namnområde.

### <a name="try"></a>Prova:
Någon ägare eller deltagare åtkomst till Azure-prenumeration kan köra följande Azure CLI-kommando för att manuellt registrera den `Microsoft.DevSpaces` namnområde:

```cmd
az provider register --namespace Microsoft.DevSpaces
```

## <a name="dev-spaces-times-out-at-waiting-for-container-image-build-step-with-aks-virtual-nodes"></a>Dev blanksteg tidsgränsen uppnås vid *väntar på behållaren bild build...*  steg med AKS virtuella noder

### <a name="reason"></a>Reason
Den här timeout uppstår när du försöker använda Dev blanksteg för att köra en tjänst som är konfigurerad för att köras en [AKS virtuell nod](https://docs.microsoft.com/azure/aks/virtual-nodes-portal). Dev blanksteg stöder för närvarande inte att skapa eller felsöker tjänster i virtuella noder.

Om du kör `azds up` med den `--verbose` växel eller aktivera utförlig loggning i Visual Studio visas ytterligare information:

```cmd
$ azds up --verbose

Installed chart in 2s
Waiting for container image build...
pods/mywebapi-76cf5f69bb-lgprv: Scheduled: Successfully assigned default/mywebapi-76cf5f69bb-lgprv to virtual-node-aci-linux
Streaming build container logs for service 'mywebapi' failed with: Timed out after 601.3037572 seconds trying to start build logs streaming operation. 10m 1s
Container image build failed
```

Ovanstående kommando visar att tjänstens pod har tilldelats till *virtuella-nod-aci-linux*, vilket är en virtuell nod.

### <a name="try"></a>Prova:
Uppdatera Helm-diagram för tjänsten att ta bort *nodeSelector* och/eller *tolerations* värden som gör att tjänsten körs på en virtuell nod. Dessa värden definieras vanligen i diagrammets `values.yaml` fil.

Du kan fortfarande använda ett AKS-kluster som har virtuella noder funktionen är aktiverad, om du vill skapa/debug via Dev blanksteg tjänsten körs på en VM-nod. Det här är standardkonfigurationen.

## <a name="error-could-not-find-a-ready-tiller-pod-when-launching-dev-spaces"></a>”Fel: kunde inte hitta en klar tiller pod” när du startar Dev blanksteg

### <a name="reason"></a>Orsak
Det här felet uppstår om Helm-klienten kan inte längre kommunicera med din Tiller pod körs i klustret.

### <a name="try"></a>Prova:
Starta om agentnoder i klustret vanligtvis löser problemet.

## <a name="error-release-azds-identifier-spacename-servicename-failed-services-servicename-already-exists-or-pull-access-denied-for-servicename-repository-does-not-exist-or-may-require-docker-login"></a>”Fel: versionen azds -\<identifierare\>-\<spacename\>-\<servicename\> misslyckades: tjänsters\<servicename\>' finns redan ”eller” hämta åtkomst nekades för \<servicename\>, databasen finns inte eller kan kräva ”docker-inloggning” ”

### <a name="reason"></a>Reason
Dessa fel kan inträffa om du blandar direkt Helm-kommandon (till exempel `helm install`, `helm upgrade`, eller `helm delete`) med utveckling blanksteg kommandon (som `azds up` och `azds down`) i samma dev adressutrymme. De inträffar eftersom Dev blanksteg har en egen Tiller-instans som står i konflikt med din egen Tiller-instans som körs i samma dev adressutrymme.

### <a name="try"></a>Prova:
Det går bra att använda både Helm kommandon och Dev blanksteg kommandon mot samma AKS-kluster, men varje Dev blanksteg-aktiverat namnområde bör använda antingen den ena eller den andra.

Anta exempelvis att du använder ett Helm-kommando för att köra hela programmet i ett utrymme för utveckling av överordnad. Du kan skapa underordnade dev blanksteg av det överordnat, Använd Dev blanksteg för att köra enskilda tjänster i underordnat dev blanksteg och testa tjänsterna tillsammans. När du är redo att checka in ändringarna, kan du använda ett Helm-kommando för att distribuera den uppdaterade koden till det överordnade dev-utrymmet. Använd inte `azds up` att köra tjänsten uppdaterade i överordnat dev utrymme eftersom den är i konflikt med först köra med Helm-tjänsten.

## <a name="azure-dev-spaces-proxy-can-interfere-with-other-pods-running-in-a-dev-space"></a>Azure Dev blanksteg proxy kan störa andra poddar som körs i en dev-utrymme

### <a name="reason"></a>Orsak
När du aktiverar Dev blanksteg för ett namnområde i AKS-kluster kan ytterligare en behållare kallas _mindaro proxy_ är installerat i var och en av poddarna som körs i det här namnområdet. Den här behållaren spärras anrop till tjänster i en pod, som är väsentlig Dev blanksteg team utvecklingsmöjligheter; Det kan dock påverka vissa tjänster som körs i de poddarna. Det är känt att störa poddar som kör Azure Cache för Redis, orsaka anslutningsfel och fel i primär/sekundär kommunikation.

### <a name="try"></a>Prova:
Du kan flytta berörda poddarna i ett namnområde i det kluster som har _inte_ har Dev blanksteg aktiverat. Resten av ditt program kan fortsätta att köras i en Dev blanksteg-aktiverat namnområde. Dev blanksteg kan inte installeras på _mindaro proxy_ behållare i Dev blanksteg aktiverade namnområden.

## <a name="azure-dev-spaces-doesnt-seem-to-use-my-existing-dockerfile-to-build-a-container"></a>Azure Dev blanksteg verkar inte använda min befintliga Dockerfile för att skapa en behållare

### <a name="reason"></a>Orsak
Azure Dev blanksteg kan konfigureras för att peka på en specifik _Dockerfile_ i projektet. Om den visas Azure Dev blanksteg inte använder den _Dockerfile_ du förväntar dig att bygga dina behållare kan du behöva uttryckligen begära Azure Dev blanksteg vilka Dockerfile du använder. 

### <a name="try"></a>Prova:
Öppna den _azds.yaml_ filen som Azure Dev blanksteg som genererats i projektet. Använd den *konfigurationer -> utveckla -> build -> dockerfile* direktiv så att den pekar till Dockerfile som du vill använda:

```
...
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
```

## <a name="error-internal-watch-failed-watch-enospc-when-attaching-debugging-to-a-nodejs-application"></a>Fel ”intern watch misslyckades: Se ENOSPC” när du ansluter till ett Node.js-program-felsökning

### <a name="reason"></a>Reason

Noden som kör en pod med Node.js-program som du försöker ansluta till med en felsökare har överskridit den *fs.inotify.max_user_watches* värde. I vissa fall kan [standardvärdet *fs.inotify.max_user_watches* kanske för liten för att hantera koppla en felsökare direkt till en pod](https://github.com/Azure/AKS/issues/772).

### <a name="try"></a>Testa
En tillfällig lösning för det här problemet är att öka värdet för *fs.inotify.max_user_watches* på varje nod i klustret och starta om noden för att ändringarna ska börja gälla.

## <a name="new-pods-are-not-starting"></a>Nya poddarna startar inte

### <a name="reason"></a>Reason

Kubernetes-initieraren kan inte använda PodSpec för nya poddarna på grund av ändringar av RBAC-behörighet till den *kluster admin* roll i klustret. De nya pod även har en ogiltig PodSpec, till exempel kontot som är associerade med en pod finns inte längre. Se poddarna som finns i en *väntande* tillstånd på grund av initieraren problemet, Använd den `kubectl get pods` kommando:

```bash
kubectl get pods --all-namespaces --include-uninitialized
```

Det här problemet kan påverka poddar i *alla namnområden* i klustret, inklusive där Azure Dev blanksteg inte är aktiverade namnområden.

### <a name="try"></a>Testa

[Uppdaterar Dev blanksteg CLI till den senaste versionen](./how-to/upgrade-tools.md#update-the-dev-spaces-cli-extension-and-command-line-tools) och sedan ta bort den *azds InitializerConfiguration* från kontrollanten Azure Dev blanksteg:

```bash
az aks get-credentials --resource-group <resource group name> --name <cluster name>
kubectl delete InitializerConfiguration azds
```

När du har tagit bort den *azds InitializerConfiguration* från kontrollanten Azure Dev blanksteg använder `kubectl delete` att ta bort alla poddar i en *väntande* tillstånd. När alla väntande poddar har tagits bort, distribuera om poddarna.

Om nya poddarna fortfarande har fastnat i en *väntande* tillstånd efter en omdistribution, Använd `kubectl delete` att ta bort alla poddar i en *väntande* tillstånd. När alla väntande poddar har tagits bort, ta bort kontrollanten från klustret och installera det på nytt:

```bash
azds remove -g <resource group name> -n <cluster name>
azds controller create --name <cluster name> -g <resource group name> -tn <cluster name>
```

När din kontrollant har installerats om måste du distribuera om poddarna.

## <a name="incorrect-rbac-permissions-for-calling-dev-spaces-controller-and-apis"></a>Felaktig RBAC-behörigheter för att anropa Dev blanksteg styrenhet och API: er

### <a name="reason"></a>Reason
Användaren kommer åt Azure Dev blanksteg kontrollanten måste ha åtkomst till Läs administratören *kubeconfig* på AKS-klustret. Exempelvis kan den här behörigheten finns i den [inbyggda Azure Kubernetes Service-kluster-administratörsrollen](../aks/control-kubeconfig-access.md#available-cluster-roles-permissions). Användaren kommer åt Azure Dev blanksteg kontrollanten måste också ha den *deltagare* eller *ägare* RBAC-roll för styrenheten.

### <a name="try"></a>Testa
Mer information om hur du uppdaterar en användares behörigheter för ett AKS-kluster finns [här](../aks/control-kubeconfig-access.md#assign-role-permissions-to-a-user-or-group).

Så här uppdaterar användarens RBAC-roll för styrenheten:

1. Logga in på Azure Portal på https://portal.azure.com.
1. Navigera till resursgruppen som innehåller den domänkontrollant som vanligtvis är samma som ditt AKS-kluster.
1. Aktivera den *Visa dolda typer* kryssrutan.
1. Klicka på kontrollanten.
1. Öppna den *åtkomstkontroll (IAM)* fönstret.
1. Klicka på den *rolltilldelningar* fliken.
1. Klicka på *Lägg till* sedan *Lägg till rolltilldelning*.
    * För *rollen* väljer du antingen *deltagare* eller *ägare*.
    * För *tilldela åtkomst till* Välj *Azure AD-användare, grupp eller tjänstens huvudnamn*.
    * För *Välj* söker efter användaren som du vill ge behörighet.
1. Klicka på *Spara*.

## <a name="controller-create-failing-due-to-controller-name-length"></a>Skapa domänkontrollanten misslyckas på grund av domänkontrollant namnlängd

### <a name="reason"></a>Reason
Ett Azure Dev blanksteg domänkontrollantens namn får inte vara längre än 31 tecken. Om din Kontrollnamn överskrider 31 tecken när du aktiverar Dev blanksteg på ett AKS-kluster eller skapar en domänkontrollant, får du felet:

*Kunde inte skapa en Dev blanksteg styrenhet för klustret 'a-controller-name-that-is-way-too-long-aks-east-us': Azure Dev blanksteg Kontrollnamn 'a-controller-name-that-is-way-too-long-aks-east-us' är ogiltig. Villkor som bröt mot: Azure Dev blanksteg Controller namn får bara vara högst 31 tecken*

### <a name="try"></a>Testa

Skapa en domänkontrollant med ett alternativt namn:

```cmd
azds controller create --name my-controller --target-name MyAKS --resource-group MyResourceGroup
```

## <a name="enabling-dev-spaces-failing-when-windows-node-pools-are-added-to-an-aks-cluster"></a>Aktivera Dev blanksteg misslyckas när Windows nodpooler läggs till i ett AKS-kluster

### <a name="reason"></a>Reason
För närvarande är Azure Dev blanksteg avsedd att köras på Linux-poddar och endast noder. När du har ett AKS-kluster med en pool för Windows-nod, måste du kontrollera att Azure Dev blanksteg poddar endast är schemalagda på Linux-noder. Om en Azure Dev blanksteg pod är schemalagd att köras på en Windows-nod, kommer inte att starta den pod och aktiverar Dev blanksteg misslyckas.

### <a name="try"></a>Testa
[Lägg till en färg](../aks/operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations) till AKS-klustret för att se till att Linux poddar inte är schemalagda att köras på en Windows-nod.

## <a name="error-found-no-untainted-linux-nodes-in-ready-state-on-the-cluster-there-needs-to-be-at-least-one-untainted-linux-node-in-ready-state-to-deploy-pods-in-azds-namespace"></a>Fel som ”finns inga untainted Linux-noder i tillståndet klar på klustret. Det måste finnas minst en untainted Linux-noden redo att distribuera poddar i namnrymden 'azds ””.

### <a name="reason"></a>Reason

Azure Dev blanksteg kunde inte skapa en domänkontrollant på AKS-klustret eftersom det inte gick att hitta en untainted nod i ett *redo* tillstånd för att schemalägga poddar på. Azure Dev blanksteg kräver minst en Linux-nod i ett *redo* tillstånd som tillåter för att schemalägga poddar utan att ange tolerations.

### <a name="try"></a>Testa
[Uppdatera din färg konfiguration](../aks/operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations) på AKS-kluster för att se till att minst en Linux noden kan schemaläggas poddar utan att ange tolerations. Kontrollera också att minst en Linux-nod som tillåter schemaläggning av poddar utan att ange tolerations finns i den *redo* tillstånd. Om noden tar lång tid att nå den *redo* tillstånd, kan du starta om noden.

## <a name="error-azure-dev-spaces-cli-not-installed-properly-when-running-az-aks-use-dev-spaces"></a>Fel ”Azure Dev blanksteg CLI som inte har installerats korrekt” när du kör `az aks use-dev-spaces`

### <a name="reason"></a>Reason
En uppdatering av Azure Dev blanksteg CLI ändras dess installationssökvägen. Om du använder en version av Azure CLI tidigare än 2.0.63, kan du se det här felet. Använd för att visa din version av Azure CLI, `az --version`.

```bash
$ az --version
azure-cli                         2.0.60 *
...
```

Trots ett felmeddelande när du kör `az aks use-dev-spaces` med en version av Azure CLI innan 2.0.63 installationen lyckas. Du kan fortsätta att använda `azds` utan problem.

### <a name="try"></a>Testa
Uppdatera din installation av den [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) till 2.0.63 eller senare. Detta löser det felmeddelande som visas när du kör `az aks use-dev-spaces`. Du kan också fortsätta att använda din nuvarande version av Azure CLI och Azure Dev blanksteg CLI.
