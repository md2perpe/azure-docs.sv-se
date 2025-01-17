---
title: Självstudie – Distribuera en app från ett geo-replikerat Docker-register i Azure
description: Distribuera en Linux-baserad webbapp till två olika Azure-regioner med hjälp av en containeravbildning från ett geo-replikerat Azure-containerregister. Del två av en serie i tre delar.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: tutorial
ms.date: 08/20/2018
ms.author: danlep
ms.custom: seodec18, mvc
ms.openlocfilehash: e5a38e2b6550d763f30c2462944b154f76bbe92c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60508258"
---
# <a name="tutorial-deploy-a-web-app-from-a-geo-replicated-azure-container-registry"></a>Självstudie: Distribuera en webbapp från ett georeplikerat Azure-containerregister

Det här är del två i en serie självstudier i tre delar. I [del ett](container-registry-tutorial-prepare-registry.md) skapade vi ett privat geo-replikerat containerregister, och en containeravbildning skapades från källan och pushades till registret. I den här artikeln drar du nytta av den nätverksnära aspekten av det geo-replikerade registret genom att distribuera containern till två Web App-instanser i två olika Azure-regioner. Varje instans hämtar sedan containeravbildningen från det närmaste registret.

Den här självstudien är del två i serien:

> [!div class="checklist"]
> * Distribuera en containeravbildning av till två instanser av *Web Apps för Containers*
> * Verifiera det distribuerade programmet

Om du inte har skapat något geo-replikerat register och pushat avbildningen av containerexempelprogrammet till registret, så gå tillbaka till föregående självstudiekurs i serien: [Förbered ett geo-replikerat Azure-containerregister](container-registry-tutorial-prepare-registry.md).

I nästa artikel i serien uppdaterar du programmet och push-överför sedan den uppdaterade containeravbildningen till registret. Sedan kan du bläddra till varje Web App-instans som körs och se hur ändringen automatiskt återspeglas i dem genom att visa geo-replikering av Azure Container Registry och aktiva webhookar.

## <a name="automatic-deployment-to-web-apps-for-containers"></a>Automatisk distribution till Web Apps för Containers

Azure Container Registry ger stöd för distribution av program i behållare direkt till [Web Apps för behållare](../app-service/containers/index.yml). I den här självstudiekursen använder du Azure Portal för att distribuera den containeravbildning som du skapade i den föregående kursen till två webbappsplaner i olika Azure-regioner.

När du distribuerar en webbapp från en behållaravbildning i registret, och har ett geo-replikerat register i samma region, så skapar Azure Container Registry en [webhook](container-registry-webhook.md) för avbildningsdistribution för dig. När du push-överför en ny avbildning till containerlagringsplatsen så hämtar webhooken ändringen och distribuerar automatiskt den nya containeravbildningen till din webbapp.

## <a name="deploy-a-web-app-for-containers-instance"></a>Distribuera en instans av Web App for Containers

Nu är det dags att skapa en instans av Web App for Containers i regionen *USA, västra*.

Logga in på [Azure Portal](https://portal.azure.com) och navigera till den registernyckel som du skapade i den föregående självstudiekursen.

Välj **Databaser** > **acr helloworld**, högerklicka på taggen **v1** under **Taggar** och välj **Distribuera till webbapp**:

![Distribuera till App Service i Azure Portal][deploy-app-portal-01]

Om ”Distribuera till webbapp” är inaktiverat kan det bero på att du inte har aktiverat administratörsanvändaren för registret enligt anvisningarna i [Create a container registry](container-registry-tutorial-prepare-registry.md#create-a-container-registry) (Skapa ett containerregister) i den första självstudiekursen. Du kan aktivera administratörsanvändaren i **Inställningar** > **Åtkomstnycklar** på Azure Portal.

Ange följande värden för varje inställning under **Web App for Containers**, som visas när du har valt ”Distribuera till webbapp”:

| Inställning | Värde |
|---|---|
| **Webbplatsnamn** | Ett globalt unikt namn för webbappen. I det här exemplet använder vi formatet `<acrName>-westus` för att lätt kunna identifiera det register och den region som webbappen distribueras från. |
| **Resursgrupp** | **Använd befintlig** > `myResourceGroup` |
| **App Service-plan/plats** | Skapa ett nytt schema med namnet `plan-westus` i regionen **USA, västra**. |
| **Avbildning** | `acr-helloworld:v1`

Etablera webbappen i regionen *USA, västra* genom att välja **Skapa**.

![Webbappen i Linux-konfiguration i Azure Portal][deploy-app-portal-02]

## <a name="view-the-deployed-web-app"></a>Visa den distribuerade webbappen

När distributionen är klar kan du visa programmet som körs genom att gå till URL:en i webbläsaren.

Välj **App Services** i portalen, och sedan den webbapp som du etablerade i föregående steg. I det här exemplet får webbappen namnet *uniqueregistryname-westus*.

Markera webbappens hyperlänks-URL högst upp till höger i **App Service**-översikten, så att du kan se programmet som körs i din webbläsare.

![Webbappen i Linux-konfiguration i Azure Portal][deploy-app-portal-04]

När Docker-avbildningen har distribuerats från din geo-replikerade container, så visar webbplatsen en avbildning som representerar den Azure-regionen som är värd för containerregistret.

![Distribuerad webbapp som visas i en webbläsare][deployed-app-westus]

## <a name="deploy-second-web-app-for-containers-instance"></a>Distribuera en andra instans av Web App for Containers

Distribuera en andra webbapp till regionen *USA, östra* med den procedur som beskrivs i föregående avsnitt. Ange följande värden under **Web App for Containers**:

| Inställning | Värde |
|---|---|
| **Webbplatsnamn** | Ett globalt unikt namn för webbappen. I det här exemplet använder vi formatet `<acrName>-eastus` för att lätt kunna identifiera det register och den region som webbappen distribueras från. |
| **Resursgrupp** | **Använd befintlig** > `myResourceGroup` |
| **App Service-plan/plats** | Skapa ett nytt schema med namnet `plan-eastus` i regionen **USA, östra**. |
| **Avbildning** | `acr-helloworld:v1`

Etablera webbappen i regionen *USA, östra* genom att välja **Skapa**.

![Webbappen i Linux-konfiguration i Azure Portal][deploy-app-portal-06]

## <a name="view-the-deployed-web-app"></a>Visa den distribuerade webbappen

Liksom tidigare kan du visa programmet som körs genom att gå till URL:en i webbläsaren.

Välj **App Services** i portalen, och sedan den webbapp som du etablerade i föregående steg. I det här exemplet får webbappen namnet *uniqueregistryname-eastus*.

Markera webbappens hyperlänks-URL högst upp till höger i **App Service**-översikten, så att du kan se programmet som körs i din webbläsare.

![Webbappen i Linux-konfiguration i Azure Portal][deploy-app-portal-07]

När Docker-avbildningen har distribuerats från din geo-replikerade container, så visar webbplatsen en avbildning som representerar den Azure-regionen som är värd för containerregistret.

![Distribuerad webbapp som visas i en webbläsare][deployed-app-eastus]

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen har du distribuerat två instanser av Web App for Containers från ett geo-replikerat Azure-behållarregister.

Gå vidare till nästa självstudie där du lär dig att uppdatera och sedan distribuera en ny containeravbildning till containerregistret, och sedan kontrollera att de webbappar som körs i båda regionerna uppdaterades automatiskt.

> [!div class="nextstepaction"]
> [Distribuera en uppdatering till en georeplikerad containeravbildning](./container-registry-tutorial-deploy-update.md)

<!-- IMAGES -->
[deploy-app-portal-01]: ./media/container-registry-tutorial-deploy-app/deploy-app-portal-01.png
[deploy-app-portal-02]: ./media/container-registry-tutorial-deploy-app/deploy-app-portal-02.png
[deploy-app-portal-03]: ./media/container-registry-tutorial-deploy-app/deploy-app-portal-03.png
[deploy-app-portal-04]: ./media/container-registry-tutorial-deploy-app/deploy-app-portal-04.png
[deploy-app-portal-05]: ./media/container-registry-tutorial-deploy-app/deploy-app-portal-05.png
[deploy-app-portal-06]: ./media/container-registry-tutorial-deploy-app/deploy-app-portal-06.png
[deploy-app-portal-07]: ./media/container-registry-tutorial-deploy-app/deploy-app-portal-07.png
[deployed-app-westus]: ./media/container-registry-tutorial-deploy-app/deployed-app-westus.png
[deployed-app-eastus]: ./media/container-registry-tutorial-deploy-app/deployed-app-eastus.png