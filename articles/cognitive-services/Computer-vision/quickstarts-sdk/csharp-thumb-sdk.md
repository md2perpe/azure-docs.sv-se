---
title: 'Snabbstart: Skapa miniatyrbilder – SDK, C#'
titleSuffix: Azure Cognitive Services
description: I den här snabbstarten ska du skapa en miniatyrbild från en bild med hjälp av Windows C#-klientbiblioteket för Visuellt innehåll.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 07/03/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 78ffd9628c7a65ae60d457bbff3631ea261649d5
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/05/2019
ms.locfileid: "67603443"
---
# <a name="quickstart-generate-a-thumbnail-using-the-computer-vision-sdk-and-c"></a>Snabbstart: Generera en miniatyrbild med SDK för Visuellt innehåll och C#

I den här snabbstarten genererar du en miniatyrbild med smart beskärning från en bild med hjälp av SDK för visuellt innehåll för C#. Om du vill kan du ladda ned koden i den här guiden som en komplett exempelapp från lagringsplatsen [Cognitive Services Csharp Vision](https://github.com/Azure-Samples/cognitive-services-vision-csharp-sdk-quickstarts/tree/master/ComputerVision) på GitHub.

## <a name="prerequisites"></a>Förutsättningar

* En prenumerationsnyckel för visuellt innehåll. Du kan få en kostnadsfri utvärderingsversion nyckel från [prova Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision). Eller följ instruktionerna i [skapa ett Cognitive Services-konto](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) att prenumerera på visuellt innehåll och få din nyckel.
* Valfri version av [Visual Studio 2015 eller 2017](https://www.visualstudio.com/downloads/).
* NuGET-paketet för [Microsoft.Azure.CognitiveServices.Vision.ComputerVision](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision)-klientbiblioteket. Du behöver inte ladda ned paketet. Installationsinstruktioner finns nedan.

## <a name="generatethumbnailasync-method"></a>Metoden GenerateThumbnailAsync

 Du kan använda dessa metoder för att skapa en miniatyrbild för en bild. Du anger höjden och bredden, som kan skilja sig från den ursprungliga bildens proportioner. Visuellt innehåll använder smart beskärning för att identifiera det område som är intressant och generera koordinater för beskärning baserat på det området.

För att köra exemplet följer du dessa steg:

1. Skapa en ny Visual C#-konsolapp i Visual Studio.
1. Installera NuGet-paketet för klientbiblioteket för Visuellt innehåll.
    1. Klicka på **Verktyg** på menyn, välj **NuGet Package Manager** (NuGet-pakethanteraren) och välj sedan **Manage NuGet Packages for Solution** (Hantera NuGet-paket för lösning).
    1. Klicka på fliken **Bläddra** och skriv ”Microsoft.Azure.CognitiveServices.Vision.ComputerVision” i rutan **Sök**.
    1. Välj **Microsoft.Azure.CognitiveServices.Vision.ComputerVision** när det visas. Klicka på kryssrutan bredvid namnet på ditt projekt och sedan på **Installera**.
1. Ersätt `Program.cs` med följande kod. Metoderna `GenerateThumbnailAsync` och `GenerateThumbnailInStreamAsync` omsluter [API:et för hämtning av miniatyrbild](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb) för fjärrbilder respektive lokala bilder. 

    ```csharp
    using Microsoft.Azure.CognitiveServices.Vision.ComputerVision;

    using System;
    using System.IO;
    using System.Threading.Tasks;

    namespace ImageThumbnail
    {
        class Program
        {
            private const bool writeThumbnailToDisk = false;

            // subscriptionKey = "0123456789abcdef0123456789ABCDEF"
            private const string subscriptionKey = "<SubscriptionKey>";

            // localImagePath = @"C:\Documents\LocalImage.jpg"
            private const string localImagePath = @"<LocalImage>";

            private const string remoteImageUrl =
                "https://upload.wikimedia.org/wikipedia/commons/9/94/Bloodhound_Puppy.jpg";

            private const int thumbnailWidth = 100;
            private const int thumbnailHeight = 100;

            static void Main(string[] args)
            {
                ComputerVisionClient computerVision = new ComputerVisionClient(
                    new ApiKeyServiceClientCredentials(subscriptionKey),
                    new System.Net.Http.DelegatingHandler[] { });

                // You must use the same region as you used to get your subscription
                // keys. For example, if you got your subscription keys from westus,
                // replace "westcentralus" with "westus".
                //
                // Free trial subscription keys are generated in the "westus"
                // region. If you use a free trial subscription key, you shouldn't
                // need to change the region.

                // Specify the Azure region
                computerVision.Endpoint = "https://westcentralus.api.cognitive.microsoft.com";

                Console.WriteLine("Images being analyzed ...\n");
                var t1 = GetRemoteThumbnailAsync(computerVision, remoteImageUrl);
                var t2 = GetLocalThumbnailAsnc(computerVision, localImagePath);

                Task.WhenAll(t1, t2).Wait(5000);
                Console.WriteLine("Press ENTER to exit");
                Console.ReadLine();
            }

            // Create a thumbnail from a remote image
            private static async Task GetRemoteThumbnailAsync(
                ComputerVisionClient computerVision, string imageUrl)
            {
                if (!Uri.IsWellFormedUriString(imageUrl, UriKind.Absolute))
                {
                    Console.WriteLine(
                        "\nInvalid remoteImageUrl:\n{0} \n", imageUrl);
                    return;
                }

                Stream thumbnail = await computerVision.GenerateThumbnailAsync(
                    thumbnailWidth, thumbnailHeight, imageUrl, true);

                string path = Environment.CurrentDirectory;
                string imageName = imageUrl.Substring(imageUrl.LastIndexOf('/') + 1);
                string thumbnailFilePath =
                    path + "\\" + imageName.Insert(imageName.Length - 4, "_thumb");

                // Save the thumbnail to the current working directory,
                // using the original name with the suffix "_thumb".
                SaveThumbnail(thumbnail, thumbnailFilePath);
            }

            // Create a thumbnail from a local image
            private static async Task GetLocalThumbnailAsnc(
                ComputerVisionClient computerVision, string imagePath)
            {
                if (!File.Exists(imagePath))
                {
                    Console.WriteLine(
                        "\nUnable to open or read localImagePath:\n{0} \n", imagePath);
                    return;
                }

                using (Stream imageStream = File.OpenRead(imagePath))
                {
                    Stream thumbnail = await computerVision.GenerateThumbnailInStreamAsync(
                        thumbnailWidth, thumbnailHeight, imageStream, true);

                    string thumbnailFilePath =
                        localImagePath.Insert(localImagePath.Length - 4, "_thumb");

                    // Save the thumbnail to the same folder as the original image,
                    // using the original name with the suffix "_thumb".
                    SaveThumbnail(thumbnail, thumbnailFilePath);
                }
            }

            // Save the thumbnail locally.
            // NOTE: This will overwrite an existing file of the same name.
            private static void SaveThumbnail(Stream thumbnail, string thumbnailFilePath)
            {
                if (writeThumbnailToDisk)
                {
                    using (Stream file = File.Create(thumbnailFilePath))
                    {
                        thumbnail.CopyTo(file);
                    }
                }
                Console.WriteLine("Thumbnail {0} written to: {1}\n",
                    writeThumbnailToDisk ? "" : "NOT", thumbnailFilePath);
            }
        }
    }
    ```

1. Ersätt `<Subscription Key>` med en giltig prenumerationsnyckel.
1. Om det behövs ändrar du `computerVision.Endpoint` till den Azure-region som är associerad med dina prenumerationsnycklar.
1. Om du vill kan du ersätta `<LocalImage>` med sökvägen och filnamnet för en lokal bild (ignoreras om det inte anges).
1. Du kan också ange `remoteImageUrl` till en annan bild om du vill.
1. Du kan även ange `writeThumbnailToDisk` till `true` för att spara miniatyrbilden på disk.
1. Kör programmet.


## <a name="examine-the-response"></a>Granska svaret

Ett lyckat svar sparar miniatyrbilden för varje bild lokalt och visar miniatyrbildens plats, till exempel:

```console
Thumbnail written to: C:\Documents\LocalImage_thumb.jpg

Thumbnail written to: ...\bin\Debug\Bloodhound_Puppy_thumb.jpg
```

## <a name="next-steps"></a>Nästa steg

Utforska API:erna för visuellt innehåll som används för att analysera en bild, identifiera kändisar och landmärken, skapa en miniatyrbild och extrahera tryckt och handskriven text.

> [!div class="nextstepaction"]
> [Utforska API:er för visuellt innehåll](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
