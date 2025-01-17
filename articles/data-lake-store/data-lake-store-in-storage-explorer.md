---
title: Hantera Azure Data Lake Storage Gen1 resurser i Azure Storage Explorer
description: Lär dig att komma åt och hantera dina Azure Data Lake Storage Gen1 data och resurser i Azure Storage Explorer
Keywords: Azure Data Lake Store, Azure Storage Explorer
services: Data Lake Store
documentationcenter: ''
author: jejiang
manager: DJ
editor: Jenny Jiang
ms.assetid: ''
ms.service: data-lake-store
ms.custom: Azure Data Lake Store
ms.devlang: dotnet
ms.topic: article
ms.date: 02/05/2018
ms.author: jejiang
ms.openlocfilehash: 18ef1f182611a9c8fbf24cd08026633f5449bbe9
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60613619"
---
# <a name="manage-azure-data-lake-storage-gen1-resources-by-using-storage-explorer"></a>Hantera Azure Data Lake Storage Gen1 resurser med Storage Explorer

[Azure Data Lake Storage Gen1](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview) är en tjänst för att lagra stora mängder Ostrukturerade data, exempelvis text eller binära data. Du kan komma åt data var du än är via HTTP eller HTTPS. Data Lake Storage Gen1 i Azure Storage Explorer kan du komma åt och hantera Data Lake Storage Gen1 data och resurser, tillsammans med andra Azure-entiteter som blobar och köer. Nu kan du använda samma verktyg för att hantera olika Azure-entiteter på ett och samma ställe.

En annan fördel är att du inte behöver ha Prenumerationsbehörighet för att hantera Data Lake Storage Gen1 data. I Storage Explorer kan du koppla Data Lake Storage Gen1 sökvägen till den **lokala och anslutna** noden så länge någon beviljar behörigheten.

## <a name="prerequisites"></a>Nödvändiga komponenter
Du behöver följande för att slutföra stegen i den här artikeln:

*   En Azure-prenumeration. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial).
*   Ett Data Lake Storage Gen1-konto. Anvisningar för hur du skapar ett finns i [Kom igång med Azure Data Lake Storage Gen1](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal).

## <a name="install-storage-explorer"></a>Installera Storage Explorer

Installera den senaste versionen av Azure Storage Explorer på [webbsidan för produkten](https://azure.microsoft.com/features/storage-explorer/). Installationen stöder Windows-, Linux- och Mac-versioner.

## <a name="connect-to-an-azure-subscription"></a>Ansluta till en Azure-prenumeration

1. Välj ikonen för plugin-programmet till vänster i Storage Explorer.
       
   ![Ikon för plugin-program](./media/data-lake-store-in-storage-explorer/plug-in-icon.png)
 
2. Välj **Lägg till ett Azure-konto** och välj sedan **Logga in**.

   ![Dialogrutan Anslut till Azure Storage](./media/data-lake-store-in-storage-explorer/connect-to-azure-subscription.png)

2. I dialogrutan **Logga in på ditt konto** anger du dina autentiseringsuppgifter för Azure.

    ![Dialogrutan för inloggning i Azure](./media/data-lake-store-in-storage-explorer/sign-in.png)

3. Välj din prenumeration i listan och välj sedan **Använd**.

    ![Prenumerationsinformation och knappen Använd](./media/data-lake-store-in-storage-explorer/apply-subscription.png)

    **EXPLORER**-fönstret uppdateras och visar konton i den valda prenumerationen.

    ![Kontolista](./media/data-lake-store-in-storage-explorer/account-list.png)

Data Lake Storage Gen1 har anslutit till din Azure-prenumeration.

## <a name="connect-to-data-lake-storage-gen1"></a>Ansluta till Data Lake Storage Gen1
Du kan komma åt resurser som inte finns i din prenumeration om någon ger dig URI:n till dessa resurser. Du kan sedan ansluta till Data Lake Storage Gen1 med hjälp av URI: N när du loggar in.
1. Öppna Storage Explorer.
2. I det vänstra fönstret expanderar du **Lokala och ansluten**.
3. Högerklicka på **Data Lake Store** och välj **Anslut till Data Lake Store**.

      ![Anslut till Data Lake Store på snabbmenyn](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-uri-attach.png)

4. Ange URI:n. Verktyget går till platsen för den URL som du angav.

      ![Dialogrutan Anslut till Data Lake Store med textrutan för att ange URI:n](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-uri-attach-dialog.png)

      ![Resultatet av att ansluta till Data Lake Storage Gen1](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-attach-finish.png)

## <a name="view-a-data-lake-storage-gen1-accounts-contents"></a>Visa innehållet i ett Data Lake Storage Gen1-konto
Ett Data Lake Storage Gen1 kontos resurser innehåller mappar och filer.

Följande steg illustrerar hur du visar innehållet i ett Data Lake Storage Gen1 konto i Storage Explorer:

1. Öppna Storage Explorer.
2. I den vänstra rutan expanderar du den prenumeration som innehåller det Data Lake Storage Gen1-konto som du vill visa.
3. Expandera **Data Lake Store**.
4. Högerklicka på noden Gen1 för Data Lake Storage-konto som du vill visa och välj sedan **öppna**. Du kan också dubbelklicka på Data Lake Storage Gen1-konto för att öppna den. 
   
   Huvudfönstret visar Data Lake Storage Gen1-kontots innehåll.

   ![Huvudfönstret med en lista över mappar](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-toolbar-mainpane.png) 

## <a name="manage-resources-in-data-lake-storage-gen1"></a>Hantera resurser i Data Lake Storage Gen1

Du kan hantera Data Lake Storage Gen1 resurser med följande åtgärder:
*   Bläddra igenom Data Lake Storage Gen1 resurser över flera Gen1 för Data Lake Storage-konton.  
*   Använd en anslutningssträng för att ansluta till och hantera Data Lake Storage Gen1 direkt. 
*   Visa Data Lake Storage Gen1 resurser som delas av andra via en ACL under **lokala och anslutna**.
*   Utför CRUD-åtgärder på filer eller mappar: stöd för rekursiva mappar och val av flera filer. 
*   Dra, släpp och lägg till en mapp så att du snabbt kommer åt nyligen använda platser. Den här åtgärden speglar Utforskaren i skrivbordsmiljö. 
*   Kopiera och öppna Data Lake Storage Gen1 hyperlänk i Storage Explorer med ett klick. 
*   Visa aktivitetslogg i det undre högra fönstret för att hålla reda på aktivitetsstatusen.
*   Visa mappstatistik och filegenskaper.

## <a name="manage-resources-in-azure-storage-explorer"></a>Hantera resurser i Azure Storage Explorer
När du skapar ett Data Lake Storage Gen1-konto kan du:

* ladda upp mappar och filer, ladda ned mappar och filer och öppna resurser på din lokala dator
* fästa på **Snabbåtkomst**, skapa en ny mapp, kopiera en URL och markera alla
* kopiera och klistra in, byta namn, ta bort, få mappstatistik och uppdatera.

Följande anvisningar visar hur du hanterar resurser i ett Data Lake Storage Gen1-konto. Följ stegen för den uppgift som du vill utföra.

### <a name="upload-files"></a>Överföra filer

1. Gå till verktygsfältet i huvudfönstret, välj **Ladda upp** och välj sedan **Ladda upp filer** i den nedrullningsbara menyn.

   ![Menyalternativet Ladda upp filer](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-upload-files-menu.png) 

2. I dialogrutan **Välj filer att ladda upp** väljer du de filer som du vill ladda upp.

   ![Dialogrutan för att överföra filer](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-upload-files-dialog.png)

3. Välj **Öppna** för att påbörja uppladdningen.

### <a name="upload-a-folder"></a>Ladda upp en mapp

1. Gå till verktygsfältet i huvudfönstret, välj **Ladda upp** och välj sedan **Ladda upp mapp** i den nedrullningsbara menyn.

   ![Menyalternativet Ladda upp mapp](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-upload-folder-menu.png) 
     
2. I dialogrutan **Välj mapp att ladda upp** väljer du en mapp som du vill ladda upp. Klicka på **Välj mapp**.

   ![Dialogrutan för att ladda upp mappar](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-upload-folder-dialog.png)      

   Uppladdningen påbörjas.

   ![Dialogrutan med pågående uppladdning](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-upload-folder-drag.png) 

> [!NOTE]
> Du kan starta uppladdningen genom att dra filer och mappar direkt på en lokal dator. 
       
### <a name="download-folders-or-files-to-your-local-computer"></a>Ladda ned mappar eller filer till en lokal dator

1. Markera de mappar eller filer som du vill ladda ned.
2. Gå till verktygsfältet i huvudfönstret och klicka på **Hämta**.
3. Ange en plats och ett namn i dialogrutan **Välj mapp där nedladdade filer ska sparas**.
4. Välj **Spara**.

### <a name="open-a-folder-or-file-from-your-local-computer"></a>Öppna en mapp eller en fil från den lokala datorn

1. Markera den mapp eller fil som du vill öppna.
2. Gå till verktygsfältet i huvudfönstret och välj **Öppna**. Högerklicka på den markerade mappen eller filen och välj **Öppna** på snabbmenyn.

Filen laddas ned och öppnas med det program som är associerat med filens underliggande filtyp. Eller också öppnas mappen i huvudfönstret.

![Öppnad fil](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-open.png) 

### <a name="copy-folders-or-files-to-the-clipboard"></a>Kopiera mappar och filer till Urklipp

1. Markera de mappar eller filer som du vill kopiera.
2. Gå till verktygsfältet i huvudfönstret och klicka på **Kopiera**. Högerklicka på de markerade mapparna eller filerna och välj **Kopiera** på snabbmenyn.
3. Bläddra till ett annat Data Lake Storage Gen1 konto i den vänstra rutan och dubbelklicka för att visa den i huvudfönstret.
4. Gå till verktygsfältet i huvudfönstret och klicka på **Klistra in** för att skapa en kopia. Eller välj **Klistra in** på snabbmenyn för målet.

![Val för att kopiera en mapp](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-copy-paste.png)

> [!NOTE]
> Kopiera och klistra in mellan lagringstyper stöds inte. Du kan kopiera Data Lake Storage Gen1 mappar eller filer och klistra in dem i ett annat Data Lake Storage Gen1-konto. Men du *kan* kopiera Data Lake Storage Gen1 mappar och filer och klistra in dem till Azure Blob storage eller tvärtom.
> 
> Kopieringen och inklistringen fungerar genom att mapparna eller filerna laddas ned lokalt och sedan laddas upp till destinationen. Verktyget *utför inte* åtgärden på serversidan. Det är långsamt att kopiera och klistra in stora filer. Optimering av högpresterande kopiering och flytt av filer pågår.

### <a name="delete-folders-or-files"></a>Ta bort mappar eller filer

1. Markera de mappar eller filer som du vill ta bort.
2. Gå till verktygsfältet i huvudfönstret och klicka på **Ta bort**. Högerklicka på de markerade mapparna eller filerna och välj **Ta bort** på snabbmenyn.
3. Välj **Ja** i bekräftelsedialogrutan.

![Val för att ta bort en mapp](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-delete.png)

### <a name="pin-to-quick-access"></a>Fäst i Snabbåtkomst

1. Markera den mapp som du vill fästa.
2. Gå till verktygsfältet i huvudfönstret och välj **Fäst i Snabbåtkomst**.

   Den valda mappen läggs till i noden **Snabbåtkomst** i det vänstra fönstret.

   ![Val för att fästa en mapp i Snabbåtkomst](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-quick-access.png)

När du har fäst en mapp i noden **Snabbåtkomst** kan du enkelt komma åt resurserna.

### <a name="use-deep-links"></a>Använda djuplänkar
Om du har en URL kan du ange den i adressfältet i Utforskaren eller i en webbläsare. Sedan körs Storage Explorer.exe automatiskt för att gå till platsen för URL:en.

![Djuplänk i Utforskaren](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-deep-link.png)


## <a name="next-steps"></a>Nästa steg
* Visa [viktig information och videor för den senaste Storage Explorer-versionen](https://www.storageexplorer.com).
* Lär dig [hantera Azure Cosmos DB i Azure Storage Explorer](https://docs.microsoft.com/azure/cosmos-db/storage-explorer).
* [Kom igång med Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer).
* [Kom igång med Azure Data Lake Storage Gen1](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview).
* Titta på en [YouTube-video om hur du använder Azure Cosmos DB i Azure Storage Explorer](https://www.youtube.com/watch?v=iNIbg1DLgWo&feature=youtu.be).
