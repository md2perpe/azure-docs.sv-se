---
title: 'Självstudier: Använda anpassad logotypdetektor för att känna igen Azure-tjänster – Custom Vision'
titlesuffix: Azure Cognitive Services
description: I de här självstudierna ska du gå igenom en exempelapp som använder Azure Custom Vision som en del av ett scenario för identifiering av logotyper. Lär dig hur Custom Vision ska användas med andra komponenter för att leverera ett program för slutpunkt till slutpunkt.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: tutorial
ms.date: 07/03/2019
ms.author: pafarley
ms.openlocfilehash: b4b10591069b71a4e70769f5bdcd6149768c5007
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/05/2019
ms.locfileid: "67604026"
---
# <a name="tutorial-recognize-azure-service-logos-in-camera-pictures"></a>Självstudier: Identifiera Azure-tjänstens logotyper i bilder med kameran

I de här självstudierna utforskar en exempelapp som använder Azure Custom Vision som en del av ett större scenario. Appen AI Visual Provision, en Xamarin.Forms-app för mobila plattformar, analyserar kamerabilder av Azure-tjänstens logotyper och distribuerar sedan de faktiska tjänsterna till användarens Azure-konto. Här lär du dig hur det använder Custom Vision tillsammans med andra komponenter för att leverera ett användbart slutpunkt till slutpunkt-program. Du kan köra hela appen scenariot för dig själv eller du kan slutföra Custom Vision del av installationen och utforska hur appen används.

De här självstudierna visar hur du:

> [!div class="checklist"]
> - Skapa en anpassad objektdetektor för att identifiera Azure-tjänstens logotyper.
> - Anslut appen till Azure-tjänsten Visuellt innehåll och Custom Vision.
> - Skapa ett konto för Azure-tjänstens huvudnamn för att distribuera Azure-tjänster från appen.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar. 

## <a name="prerequisites"></a>Förutsättningar

- [Visual Studio 2017 eller senare](https://www.visualstudio.com/downloads/)
- Xamarin-arbetsbelastningen för Visual Studio (se [Installera Xamarin](https://docs.microsoft.com/xamarin/cross-platform/get-started/installation/windows))
- En iOS- eller Android-emulator för Visual Studio
- [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli-windows?view=azure-cli-latest) (valfritt)

## <a name="get-the-source-code"></a>Hämta källkoden

Om du vill använda den webbapp som tillhandahålls kan du klona eller ladda ned appens källkod från lagringsplatsen [AI Visual Provision](https://github.com/Microsoft/AIVisualProvision) på GitHub. Öppna filen *Source/VisualProvision.sln* i Visual Studio. Senare redigerar du några av projektfilerna så att du kan köra appen.

## <a name="create-an-object-detector"></a>Skapa en objektidentifierare

Logga in på [Custom Visions webbplats](https://customvision.ai/) och skapa ett nytt projekt. Ange ett objektidentifieringsprojekt och använd logotyp-domänen. Detta gör att tjänsten kan använda en algoritm som är optimerad för identifiering av logotyp. 

![Nytt projektfönster på Custom Vision-webbplatsen i webbläsaren Chrome](media/azure-logo-tutorial/new-project.png)

## <a name="upload-and-tag-images"></a>Ladda upp och tagga bilder

I nästa steg tränar du algoritmen för logotypidentifiering genom att ladda upp bilder på Azure-tjänstens logotyper och tagga dem manuellt. AIVisualProvision-lagringsplatsen innehåller en uppsättning inlärningsbilder som du kan använda. På webbplatsen väljer du knappen **Lägg till bilder** på fliken **Training Images** (Träningsbilder). Gå till mappen **Documents/Images/Training_DataSet** för databasen. Du måste manuellt tagga logotyperna i varje bild, så om du bara testar det här projektet kan det vara bra att ladda upp endast en delmängd av bilderna. Ladda upp minst 15 instanser av varje tagg som du tänker använda.

När du har laddat upp träningsbilderna väljer du den första på skärmen. De taggning fönstret visas. Rita rutor och tilldela taggar för en logotyp i varje bild. 

![Logotyptaggning på Custom Vision-webbplatsen](media/azure-logo-tutorial/tag-logos.png)

Appen är konfigurerad för att fungera med specifika taggsträngar. Definitionerna finns i filen *Source\VisualProvision\Services\Recognition\RecognitionService.cs*:

[!code-csharp[Tag definitions](~/AIVisualProvision/Source/VisualProvision/Services/Recognition/RecognitionService.cs?range=18-33)]

När du har taggat en bild går du till höger för att tagga nästa. Stäng taggningsfönstret när du är klar.

## <a name="train-the-object-detector"></a>Träna objektidentifieraren

I den vänstra rutan ställer du in reglaget **Taggar** på **Tagged** (Taggad) för att visa bilderna. Välj sedan den gröna knappen överst på sidan för att träna modellen. Algoritmen för att träna att identifiera samma taggar i nya avbildningar. Det testar också modellen på några av dina befintliga bilder för att generera noggrannhetspoäng.

![Custom Vision-webbplatsen, på fliken Training Images (Träningsbilder). I den här skärmbilden är knappen Träna markerad](media/azure-logo-tutorial/train-model.png)

## <a name="get-the-prediction-url"></a>Hämta URL:en för förutsägelse

När din modell har tränats är du redo att integrera den med din app. Du behöver hämta slutpunkts-URL (adressen för din modell, som ska skicka frågor till appen) och nyckeln förutsägelse (för att ge appen åtkomst till förutsägelse begäranden). På fliken **Prestanda** väljer du knappen **Prediction URL** (Förutsägelse-URL) längst upp på sidan.

![Custom Vision-webbplatsen med en förutsägelse-API-skärm som visar en URL-adress och en API-nyckel](media/azure-logo-tutorial/cusvis-endpoint.png)

Kopiera bildfilens URL och värdet för **Prediction-Key** till lämpliga fält filen *Source\VisualProvision\AppSettings.cs*:

[!code-csharp[Custom Vision fields](~/AIVisualProvision/Source/VisualProvision/AppSettings.cs?range=22-26)]

## <a name="examine-custom-vision-usage"></a>Granska Custom Vision-användning

Öppna filen *Source/VisualProvision/Services/Recognition/CustomVisionService.cs* för att se hur appen använder din Custom Vision-nyckel och slutpunkts-URL. Metoden **PredictImageContentsAsync** tar en byteström av en bildfil tillsammans med en annuleringstoken (för hantering av asynkron aktivitet), anropar förutsägelse-API:t för Custom Vision och returnerar förutsägelseresultatet. 

[!code-csharp[Custom Vision fields](~/AIVisualProvision/Source/VisualProvision/Services/Recognition/CustomVisionService.cs?range=12-28)]

Resultatet får formen av en **PredictionResult**-instans, som innehåller en lista över **förutsägelseinstanser**. En **förutsägelse** innehåller en identifierad tagg och dess avgränsningsfältsplats i bilden.

[!code-csharp[Custom Vision fields](~/AIVisualProvision/Source/VisualProvision/Services/Recognition/Prediction.cs?range=3-12)]

Om du vill lära dig mer om hur appen hanterar dessa data börjar du med metoden **GetResourcesAsync**. Den här metoden definieras i filen *Source/VisualProvision/Services/Recognition/RecognitionService.cs*.  

## <a name="add-computer-vision"></a>Lägga till Visuellt innehåll

Custom Vision-delen av självstudien är klar. Om du vill att köra appen måste du integrera tjänsten Visuellt innehåll också. Appen använder textidentifieringsfunktionen i Visuellt text för att komplettera processen för logotypidentifiering. En Azure-logotyp kan identifieras utefter sitt utseende *eller* med den text som är skriven nära den. Till skillnad från Custom Vision-modeller är Visuellt innehåll tränat i förväg för att utföra vissa åtgärder på bilder eller videor.

Prenumerera på tjänsten Visuellt innehåll för att hämta en nyckel och slutpunkts-URL. Hjälp om det här steget finns i [Skaffa prenumerationsnycklar](https://docs.microsoft.com/azure/cognitive-services/computer-vision/vision-api-how-to-topics/howtosubscribe).

![Tjänsten för visuellt innehåll i Azure-portalen med Snabbstart-menyn som valts. En länk för nycklar samt slutpunkts-URL för API är markerade](media/azure-logo-tutorial/comvis-keys.png)

Nu öppnar du filen *Source\VisualProvision\AppSettings.cs* och fyller i variablerna `ComputerVisionEndpoint` och `ComputerVisionKey` med rätt värden.

[!code-csharp[Computer Vision fields](~/AIVisualProvision/Source/VisualProvision/AppSettings.cs?range=28-32)]

## <a name="create-a-service-principal"></a>Skapa ett huvudnamn för tjänsten

Appen kräver ett Azure-konto för tjänstens huvudnamn för att distribuera tjänster till din Azure-prenumeration. Med tjänstens huvudnamn kan du delegera specifika behörigheter för en app med rollbaserad åtkomstkontroll. Mer information finns i [guiden för tjänstens huvudnamn](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-create-service-principals).

Du kan skapa ett huvudnamn för tjänsten med hjälp av Azure Cloud Shell eller Azure CLI såsom det visas här. Börja med att logga in och välja den prenumeration som du vill använda.

```console
az login
az account list
az account set --subscription "<subscription name or subscription id>"
```

Skapa sedan tjänstens huvudnamn. (Den här processen kan ta lite tid att slutföra.)

```console
az ad sp create-for-rbac --name <servicePrincipalName> --password <yourSPStrongPassword>
```

Vid slutförande bör du se följande JSON-utdata, däribland nödvändiga autentiseringsuppgifter.

```json
{
  "clientId": "(...)",
  "clientSecret": "(...)",
  "subscriptionId": "(...)",
  "tenantId": "(...)",
  ...
}
```

Anteckna värdena `clientId` och `tenantId`. Lägg till dem i rätt fält i filen *Source\VisualProvision\AppSettings.cs*.

[!code-csharp[Computer Vision fields](~/AIVisualProvision/Source/VisualProvision/AppSettings.cs?range=8-16)]

## <a name="run-the-app"></a>Kör appen

Nu har du gett appen åtkomst till:

- En tränad Custom Vision-modell
- Tjänsten Visuellt innehåll
- Ett tjänsthuvudnamn

Följ dessa steg om du vill köra appen:

1. I Visual Studio Solution Explorer väljer du antingen projektet **VisualProvision.Android** eller projektet **VisualProvision.iOS**. Välj en motsvarande emulator eller ansluten mobil enhet från den nedrullningsbara menyn på huvudverktygsfältet. Kör sedan appen.

    > [!NOTE]
    > Du behöver en MacOS-enhet för att köra en iOS-emulator.

1. På den första skärmen anger du klient-ID för tjänstens huvudnamn, klientorganisations-ID och lösenord. Välj knappen **Logga in**.

    > [!NOTE]
    > På vissa emulatorer har knappen **Logga in** kanske inte aktiverats i det här steget. Om det här händer stoppar du appen, öppnar filen *Source/VisualProvision/Pages/LoginPage.xaml*, letar rätt på det `Button`-element som är märkt **LOGIN BUTTON** (Inloggningsknapp), tar bort följande rad och kör sedan appen igen.
    >  ```xaml
    >  IsEnabled="{Binding IsValid}"
    >  ```
    
    ![Appskärmen som visar fält för autentiseringsuppgifter för tjänstens huvudnamn](media/azure-logo-tutorial/app-credentials.png)

1. På nästa skärm väljer du din Azure-prenumeration i den nedrullningsbara menyn. (Den här menyn bör innehålla alla prenumerationer som ditt tjänsthuvudnamn har åtkomst till.) Välj knappen **Fortsätt**. I det här läget kan appen uppmana dig att ge åtkomst till enhetens kamera och fotolagring. Bevilja åtkomstbehörigheterna.

    ![Appskärmen som visar en listruta för mål-Azure-prenumeration](media/azure-logo-tutorial/app-az-subscription.png)


1. Kameran på enheten aktiveras. Ta ett foto av en av Azure-tjänstens logotyper som du har tränat. En distributionsfönster uppmanar dig att välja en region och en resursgrupp för de nya tjänsterna (som du skulle göra om du hade distribuerat dem från Azure-portalen). 

    ![En smartphonekameraskärm som fokuserar på två pappersurklipp med Azure-logotyper](media/azure-logo-tutorial/app-camera-capture.png)

    ![En appskärm som visar fält för distributionsregion och resursgrupp](media/azure-logo-tutorial/app-deployment-options.png)

## <a name="clean-up-resources"></a>Rensa resurser

Om du har följt alla steg i det här scenariot och använt appen för att distribuera Azure-tjänster till ditt konto går du till [Azure-portalen](https://ms.portal.azure.com/). Där avbryter du de tjänster som du inte vill använda.

Om du planerar att skapa egna objektidentifieringsprojekt med Custom Vision kan det vara bra att ta bort det logotypidentifieringsprojekt som du skapade i den här självstudien. I en kostnadsfri utvärderingsversion av Custom Vision går det bara att ha två projekt. För att ta bort logotypidentifieringsprojektet går du till [Custom Vision-webbplatsen](https://customvision.ai), öppnar **Projekt** och väljer papperskorgsikonen under **Mitt nya projekt**.

## <a name="next-steps"></a>Nästa steg

I den här självstudien konfigurerar du och utforskar en komplett Xamarin.Forms-app som använder Custom Vision-tjänsten för att identifiera logotyper i mobilkamerabilder. Nu kan du läsa om metodtips för att skapa en Custom Vision-modell, så att du kan göra den kraftfull och korrekt när du skapar en modell för din egen app.

> [!div class="nextstepaction"]
> [Förbättra din klassificerare](getting-started-improving-your-classifier.md)
