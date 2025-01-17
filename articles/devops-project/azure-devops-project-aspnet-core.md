---
title: 'Snabbstart: Skapa en CI/CD-pipeline för .NET med Azure DevOps Projects'
description: Azure DevOps Projects gör det enkelt att komma igång med Azure. Det hjälper dig att starta en .NET-app på en Azure-tjänst med några enkla få steg.
ms.prod: devops
ms.technology: devops-cicd
services: azure-devops-project
documentationcenter: vs-devops-build
author: mlearned
manager: douge
editor: ''
ms.assetid: ''
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 07/09/2018
ms.author: mlearned
ms.custom: mvc
monikerRange: vsts
ms.openlocfilehash: 387801f2ecb2f5fa1639005726218efb54d75dc8
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/22/2019
ms.locfileid: "67331427"
---
# <a name="create-a-cicd-pipeline-for-net-with-azure-devops-projects"></a>Skapa en CI/CD-pipeline för .NET med Azure DevOps Projects

Konfigurera kontinuerlig integrering (CI) och kontinuerlig leverans (CD) för ditt .NET Core- eller ASP.NET-program med DevOps Projects. DevOps Projects förenklar den initiala konfigurationen av en bygg- och versionspipeline i Azure Pipelines.

Om du inte har en Azure-prenumeration kan du skaffa en kostnadsfritt via [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

DevOps Projects skapar en CI/CD-pipeline i Azure DevOps. Du kan skapa en ny Azure DevOps-organisation eller använda en befintlig organisation. DevOps Projects skapar även Azure-resurser i den Azure-prenumeration som du väljer.

1. Logga in på [Microsoft Azure Portal](https://portal.azure.com).

1. I den vänstra fönsterrutan väljer du ikonen **Skapa en resurs** i det vänstra navigeringsfältet och söker sedan efter **DevOps Projects**.  

3.  Välj **Skapa**.

    ![Startar kontinuerlig leverans](_img/azure-devops-project-aspnet-core/fullbrowser.png)

## <a name="select-a-sample-application-and-azure-service"></a>Välj ett exempelprogrammet och en Azure-tjänst

1. Välj **.NET**-exempelprogrammet. .NET-exempel innehåller antingen ASP.NET-ramverket med öppen källkod eller det plattformsoberoende .NET Core-ramverket.

   ![.NET-ramverk](_img/azure-devops-project-aspnet-core/chooselanguagedotnet.png)

2. Det här exemplet är ett ASP.NET Core MVC-program. Välj den **.NET Core** programramverk, välj sedan **nästa**.    
    
3. Välj **Windows Web App** som ett distribution-mål, Välj **nästa**. Du kan alternativt välja andra Azure-tjänster för din distribution. Application framework, som du valde tidigare avser vilken typ av Azure-tjänsten distribution mål tillgänglig här.

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Konfigurera Azure DevOps och en Azure-prenumeration 

1. Ange en **projektnamn**.

2. Skapa en ny kostnad **Azure DevOps-organisation** eller välj en befintlig organisation i listrutan.

3. Välj din **Azure-prenumeration**, ange ett namn för din **webbapp** eller ta standard och välj sedan **klar**. Efter ett par minuter visas distributionsöversikt för DevOps-projekt i Azure-portalen. 

4. Välj **gå till resurs** att visa instrumentpanelen för DevOps-projektet. I det övre högra hörnet, fästa den **projekt** på instrumentpanelen för snabb åtkomst. En exempelapp ställs in i en lagringsplats i din **Azure DevOps-organisation**. En version körs och din app distribueras till Azure.

5. Instrumentpanelen ger insyn i kodlagringsplatsen, CI/CD-pipelinen och appen i Azure. Till höger under Azure-resurser, Välj **Bläddra** att visa din app som körs.

   ![Instrumentpanelsvy](_img/azure-devops-project-aspnet-core/dashboardnopreview.png) 

## <a name="commit-code-changes-and-execute-cicd"></a>Genomför ändringar i koden och kör CI/CD

DevOps Projects skapade en Git-lagringsplats i Azure Repos eller GitHub. Visa lagringsplatsen och gör kodändringar i programmet genom att utföra följande:

1. Till vänster om DevOps Projects-instrumentpanelen väljer du länken för din **huvudgren**. Den här länken öppnar en vy till den nyligen skapade Git-lagringsplatsen.

2. I nästa steg du kan använda webbläsaren att göra och spara ändringar i koden direkt till den **master** gren. Du kan också klona Git-lagringsplatsen i din favorit-IDE genom att välja **klona** längst upp höger på sidan för databasen. 

3. Gå till vänster filstruktur program till **Application/aspnet-core-dotnet-core/Pages/Index.cshtml**.

4. Välj **Redigera** och gör en ändring i h2-rubriken. Du kan till exempel skriva **Komma igång direkt med Azure DevOps Projects** eller göra någon annan ändring.

      ![Ändringar i koden](_img/azure-devops-project-aspnet-core/codechange.png)

5. Välj **genomför**, lämna en kommentar och välj **genomför** igen.

6. I webbläsaren går du till instrumentpanelen för Azure DevOps-projektet.  Du bör nu se att en version håller på att skapas. De ändringar du utfört skapas och distribueras automatiskt via en CI/CD-pipeline.

## <a name="examine-the-cicd-pipeline"></a>Granska CI/CD-pipelinen

I föregående steg konfigurerade Azure DevOps Projects automatiskt en fullständig CI/CD-pipeline. Utforska och anpassa pipelinen efter behov. Bekanta dig med Azure DevOps-bygg- och versionspipelines genom att utföra följande steg.

1. Välj **Bygg-pipelines** längst upp på DevOps Projects-instrumentpanelen.  
Länken öppnar en webbläsarflik och Azure DevOps-bygg-pipelinen för det nya projektet.

1. Välj ellipsen (...).  Den här åtgärden öppnar en meny där du kan starta flera aktiviteter, till exempel lägga till en ny version i en kö, pausa en version och redigera bygg-pipelinen.

1. Välj **Redigera**.

    ![Bygg-pipeline](_img/azure-devops-project-aspnet-core/builddef.png)

1. I den här fönsterrutan kan du granska de olika uppgifterna för bygg-pipelinen.  
 Versionen utför olika uppgifter som att hämta källor från Git-lagringsplatsen, återställa beroenden och publicera utdata som används för distributioner.

1. Välj bygg-pipelinens namn längst upp i bygg-pipelinen.

1. Ändra på din bygg-pipeline till något mer beskrivande och välj alternativet för att **spara och placera i kö**. Välj sedan **Spara**.

1. Under ditt bygg-pipelinenamn väljer du **Historik**.   
I fönsterrutan **Historik** ser du en spårningslogg över de senaste ändringarna för versionen.  Azure Pipelines spårar alla ändringar som görs av bygg-pipelinen, vilket innebär att du kan jämföra versioner.

1. Välj **Utlösare**.  
DevOps Projects skapade automatiskt en CI-utlösare, och varje incheckning till lagringsplatsen startar en ny version.  Du kan välja att inkludera eller exkludera grenar från CI-processen.

1. Välj **Kvarhållning**.  
Beroende på ditt scenario kan du ange principer för att behålla eller ta bort ett visst antal versioner.

1. Välj **Byggen och versioner** och sedan **Versioner**.  
DevOps Projects skapar en versionspipeline för att hantera distributioner till Azure.

1.  Välj ellipsen (...) till vänster intill din versionspipeline och välj sedan **Redigera**.  
Versionspipelinen innehåller en pipeline som definierar versionsprocessen.  

1. Under **Artefakter** väljer du **Släpp**.  Den bygg-pipeline du undersökte i de föregående stegen skapar de utdata som används för artefakten. 

1. Intill ikonen **Släpp** väljer du **Utlösare av kontinuerlig distribution**.  
Den här versionspipelinen har en aktiverad CD-utlösare som kör en distribution varje gång en ny versionsartefakt är tillgänglig. Du kan även inaktivera utlösaren så att dina distributioner kräver manuell körning.  

1. Till vänster väljer du **Uppgifter**.   
Uppgifter är de aktiviteter som distributionsprocessen utför. I det här exemplet skapades en uppgift för att distribuera till Azure App-tjänsten.

1. Till höger väljer du alternativet för att **visa versioner**. Den här vyn visar en historik över versioner.

1. Välj ellipsen (...) bredvid en av versionerna och välj **Öppna**.  
Det finns flera menyer att utforska, till exempel en versionssammanfattning, tillhörande arbetsobjekt och tester.


1. Välj **Incheckningar**.   
Den här vyn visar kodincheckningar som är associerade med den specifika distributionen. 

1. Välj **Loggar**.  
Loggarna innehåller användbar information om distributionsprocessen. De kan visas både under och efter distributionerna.


## <a name="clean-up-resources"></a>Rensa resurser

Du kan ta bort Azure App Service och andra relaterade resurser som du skapade när de inte längre behövs. Använd funktionen **Ta bort** på DevOps Projects-instrumentpanelen.

## <a name="next-steps"></a>Nästa steg

Se den här självstudien för att lära dig mer om att ändra dessa bygg- och versionspipelines för att uppfylla behoven i ditt team:

> [!div class="nextstepaction"]
> [Anpassa CD-process](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)

## <a name="videos"></a>Videoklipp

> [!VIDEO https://www.youtube.com/embed/itwqMf9aR0w]
