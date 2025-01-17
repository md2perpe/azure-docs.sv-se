---
title: Så här skapar du en blob i Azure Storage med hjälp av klientbiblioteket för Java v7 | Microsoft Docs
description: Skapa ett lagringskonto och en container i objektlagring (Blob). Sedan använda Azure Storage-klientbibliotek för Java v7 och ladda upp en blob till Azure Storage, laddar ned en blob och listar blobarna i en behållare.
services: storage
author: mhopkins-msft
ms.custom: mvc
ms.service: storage
ms.topic: conceptual
ms.date: 02/04/2019
ms.author: mhopkins
ms.reviewer: seguler
ms.openlocfilehash: f7cae5b3c7b0a7da6420674635ff9c3420a6436a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65154421"
---
# <a name="how-to-upload-download-and-list-blobs-using-the-client-library-for-java-v7"></a>Hur du laddar upp, ladda ned och lista blobar med hjälp av klientbiblioteket för Java v7

I den här guiden att lära dig hur du använder klientbiblioteket för Java v7 att ladda upp, ladda ned och lista blockblobar i en behållare i Azure Blob storage.

> [!TIP]
> Den senaste versionen av Azure Storage-klientbibliotek för Java är v10. Microsoft rekommenderar att du använder den senaste versionen av klientbiblioteket när det är möjligt. Kom igång med v10, se [snabbstarten: Ladda upp, ladda ned och lista blobar med hjälp av Java Storage SDK V10](storage-quickstart-blobs-java-v10.md).

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

Också skapa ett Azure storage-konto i den [Azure-portalen](https://portal.azure.com/#create/Microsoft.StorageAccount-ARM). Hjälp med att skapa kontot finns i [Skapa ett lagringskonto](../common/storage-quickstart-create-account.md).

Kontrollera att du har följande krav:

* Installera en IDE som har Maven-integrering.

* Du kan även installera och konfigurera Maven så att det fungerar från kommandoraden.

Den här guiden används [Eclipse](https://www.eclipse.org/downloads/) med konfigurationen ”Eclipse IDE for Java Developers”.

## <a name="download-the-sample-application"></a>Hämta exempelprogrammet

Den [exempelprogrammet](https://github.com/Azure-Samples/storage-blobs-java-quickstart) är ett grundläggande konsolprogram.  

Använd [git](https://git-scm.com/) för att ladda ned en kopia av programmet till utvecklingsmiljön. 

```bash
git clone https://github.com/Azure-Samples/storage-blobs-java-quickstart.git
```

Det här kommandot klonar lagret till den lokala git-mappen. Öppna projektet genom att starta Eclipse och stänga välkomstskärmen. Välj **Arkiv** och sedan **Öppna projekt, under Filsystem**. Kontrollera att alternativet för att **identifiera och konfigurera projektegenskaper** har markerats. Välj **Katalog** och navigera till den plats där sparade den klonade lagringsplatsen. På den klonade lagringsplatsen väljer du mappen **blobAzureApp**. Kontrollera att projektet **blobAzureApp** visas som ett Eclipse-projekt och välj sedan **Slutför**.

När projektet har importerats öppnar du **AzureApp.java** (finns i **blobQuickstart.blobAzureApp** inuti **src/main/java**) och ersätter `accountname` och `accountkey` inuti strängen `storageConnectionString`. Kör sedan programmet. Specifika anvisningar för att slutföra dessa uppgifter beskrivs i följande avsnitt.

[!INCLUDE [storage-copy-connection-string-portal](../../../includes/storage-copy-connection-string-portal.md)]    

## <a name="configure-your-storage-connection-string"></a>Konfigurera anslutningssträngen för lagring
    
Du måste ange anslutningssträngen för ditt lagringskonto i programmet. Öppna filen **AzureApp.Java**. Hitta variabeln `storageConnectionString` och klistra in anslutningssträngens värde som du kopierade i föregående avsnitt. Variabeln `storageConnectionString` bör se ut ungefär som följande kodexempel:

```java
public static final String storageConnectionString =
"DefaultEndpointsProtocol=https;" +
"AccountName=<account-name>;" +
"AccountKey=<account-key>";
```

## <a name="run-the-sample"></a>Kör exemplet

Det här exempelprogrammet skapar en testfil i din standardkatalog (*C:\Användare\<användare>\AppData\Local\Temp* för Windows-användare), laddar upp den till bloblagringen, listar blobarna i containern och laddar sedan ned filen med ett nytt namn så att du kan jämföra den gamla och den nya filen. 

Kör exemplet med hjälp av Maven på kommandoraden. Öppna ett gränssnitt och gå till **blobAzureApp** i den klonade katalogen. Ange sedan `mvn compile exec:java`. 

Följande exempel visar utdata om du kör programmet i Windows.

```
Azure Blob storage quick start sample
Creating container: quickstartcontainer
Creating a sample file at: C:\Users\<user>\AppData\Local\Temp\sampleFile514658495642546986.txt
Uploading the sample file 
URI of blob is: https://myexamplesacct.blob.core.windows.net/quickstartcontainer/sampleFile514658495642546986.txt
The program has completed successfully.
Press the 'Enter' key while in the console to delete the sample files, example container, and exit the application.

Deleting the container
Deleting the source, and downloaded files
```

Kontrollera att exempelfilen finns i standardkatalogen (*C:\Användare\<användare>\AppData\Local\Temp* för Windows-användare) innan du fortsätter. Kopiera URL:en för bloben från konsolfönstret och klistra in den i en webbläsare om du vill se innehållet i filen i bloblagringen. Om du jämför exempelfilen i katalogen med det innehåll som lagras i bloblagringen märker du att de är likadana. 

  >[!NOTE]
  >Du kan också använda ett verktyg som [Azure Storage Explorer](https://storageexplorer.com/?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) för att visa filerna i Blob Storage. Azure Storage Explorer är ett kostnadsfritt verktyg för flera plattformar som gör det möjligt att komma åt information på lagringskontot.

När du har kontrollerat filerna trycker du på **Retur**-tangenten för att avsluta demonstrationen och ta bort testfilerna. Nu när du vet vad exemplet gör kan du öppna filen **AzureApp.java** och titta på koden. 

## <a name="understand-the-sample-code"></a>Förstå exempelkoden

Härnäst går vi igenom exempelkoden så att du kan förstå hur den fungerar.

### <a name="get-references-to-the-storage-objects"></a>Hämta referenser till lagringsobjekten

Det första du ska göra är att skapa referenser till objekten som används för att komma åt och hantera Blob Storage. De här objekten bygger på varandra – vart och ett används av nästa i listan.

* Skapa en instans av objektet [CloudStorageAccount](/java/api/com.microsoft.azure.management.storage.storageaccount) som pekar mot lagringskontot.

    Objektet **CloudStorageAccount** är en representation av lagringskontot och gör det möjligt att konfigurera och komma åt egenskaper för lagringskontot programmässigt. Med hjälp av objektet **CloudStorageAccount** kan du skapa en instans av klienten **CloudBlobClient**, som krävs för att komma åt blobbtjänsten.

* Skapa en instans av objektet **CloudBlobClient**, som pekar mot [Blob Service](/java/api/com.microsoft.azure.storage.blob._cloud_blob_client) i lagringskontot.

    **CloudBlobClient** ger dig en åtkomstpunkt till blobtjänsten så att du kan konfigurera och komma åt egenskaper för bloblagring programmatiskt. Med hjälp av **CloudBlobClient** kan du skapa en instans av objektet **CloudBlobContainer**, som krävs för att skapa behållare.

* Skapa en instans av objektet [CloudBlobContainer](/java/api/com.microsoft.azure.storage.blob._cloud_blob_container), som representerar den behållare du får åtkomst till. Använd containrar för att organisera blobar på samma sätt som du använder mappar på datorn för att organisera filer.    

    När du har **CloudBlobContainer** kan du skapa en instans av objektet [CloudBlockBlob](/java/api/com.microsoft.azure.storage.blob._cloud_block_blob) som pekar mot den specifika blob du är intresserad av, och utföra en åtgärd för att bland annat ladda upp, ladda ned, kopiera.

> [!IMPORTANT]
> Containernamn måste använda gemener. Mer information om containrar finns i [Namnge och referera till containrar, blobar och metadata](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

### <a name="create-a-container"></a>Skapa en container

I det här avsnittet skapar du en instans av objekt, skapar en ny container och anger sedan behörigheter för containern så att blobarna är offentliga och går att komma åt med bara en URL. Containern heter **quickstartcontainer**. 

Det här exemplet använder [CreateIfNotExists](/java/api/com.microsoft.azure.storage.blob._cloud_blob_container.createifnotexists) eftersom vi vill skapa en ny container varje gång exemplet körs. I en produktionsmiljö där du använder samma container i ett helt program är det bättre att bara anropa **CreateIfNotExists** en gång. Du kan också skapa containern i förväg så att du inte behöver skapa den i koden.

```java
// Parse the connection string and create a blob client to interact with Blob storage
storageAccount = CloudStorageAccount.parse(storageConnectionString);
blobClient = storageAccount.createCloudBlobClient();
container = blobClient.getContainerReference("quickstartcontainer");

// Create the container if it does not exist with public access.
System.out.println("Creating container: " + container.getName());
container.createIfNotExists(BlobContainerPublicAccessType.CONTAINER, new BlobRequestOptions(), new OperationContext());
```

### <a name="upload-blobs-to-the-container"></a>Ladda upp blobar i containern

Hämta en referens till bloben i Målbehållaren om du vill överföra en fil till en blockblob. När du har blobbreferensen kan du ladda upp data till den med hjälp av [CloudBlockBlob.Upload](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._cloud_block_blob.upload). Den här åtgärden skapar bloben om den inte redan finns, eller skriver över bloben om den redan finns.

Exempelkoden skapar en lokal fil som ska användas för uppladdning och nedladdning, och lagrar den fil som ska laddas upp som **source** (källa) och namnet på bloben i **blob**. I följande exempel laddas filen upp till containern med namnet **quickstartcontainer**.

```java
//Creating a sample file
sourceFile = File.createTempFile("sampleFile", ".txt");
System.out.println("Creating a sample file at: " + sourceFile.toString());
Writer output = new BufferedWriter(new FileWriter(sourceFile));
output.write("Hello Azure!");
output.close();

//Getting a blob reference
CloudBlockBlob blob = container.getBlockBlobReference(sourceFile.getName());

//Creating blob and uploading file to it
System.out.println("Uploading the sample file ");
blob.uploadFromFile(sourceFile.getAbsolutePath());
```

Det finns flera metoder för `upload`, exempelvis [upload](/java/api/com.microsoft.azure.storage.blob._cloud_block_blob.upload), [uploadBlock](/java/api/com.microsoft.azure.storage.blob._cloud_block_blob.uploadblock), [uploadFullBlob](/java/api/com.microsoft.azure.storage.blob._cloud_block_blob.uploadfullblob), [uploadStandardBlobTier](/java/api/com.microsoft.azure.storage.blob._cloud_block_blob.uploadstandardblobtier) och [uploadText](/java/api/com.microsoft.azure.storage.blob._cloud_block_blob.uploadtext), som du kan använda med Blob Storage. Om du till exempel har en sträng kan du använda metoden `UploadText` i stället för metoden `Upload`. 

Blockblobar kan vara valfri texttyp eller binär fil. Sidblobar används främst för VHD-filer som backar IaaS-datorer. Använd tilläggsblobar för loggning, till exempel när du vill skriva till en fil och sedan fortsätta att lägga till mer information. De flesta objekt som lagras i Blob Storage är blockblobar.

### <a name="list-the-blobs-in-a-container"></a>Visa en lista över blobarna i en container

Du kan hämta en lista över filer i behållaren med hjälp av [CloudBlobContainer.ListBlobs](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._cloud_blob_container.listblobs). Följande kod hämtar listan över blobbar och går sedan igenom dem och visar URI för de blobbar som har påträffats. Du kan kopiera URI från kommandofönstret och klistra in den i en webbläsare om du vill visa filen.

```java
//Listing contents of container
for (ListBlobItem blobItem : container.listBlobs()) {
    System.out.println("URI of blob is: " + blobItem.getUri());
}
```

### <a name="download-blobs"></a>Ladda ned blobbar

Ladda ned blobbar till den lokala disken med hjälp av [CloudBlob.DownloadToFile](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._cloud_blob.downloadtofile).

Följande kod laddar ned bloben som överfördes i föregående avsnitt och lägger till ett suffix med "_DOWNLOADED" i blobnamnet så att du kan se båda filerna på den lokala disken. 

```java
// Download blob. In most cases, you would have to retrieve the reference
// to cloudBlockBlob here. However, we created that reference earlier, and 
// haven't changed the blob we're interested in, so we can reuse it. 
// Here we are creating a new file to download to. Alternatively you can also pass in the path as a string into downloadToFile method: blob.downloadToFile("/path/to/new/file").
downloadedFile = new File(sourceFile.getParentFile(), "downloadedFile.txt");
blob.downloadToFile(downloadedFile.getAbsolutePath());
```

### <a name="clean-up-resources"></a>Rensa resurser

Om du inte längre behöver blobarna som du har överfört kan du ta bort hela behållaren med [CloudBlobContainer.DeleteIfExists](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._cloud_blob_container.deleteifexists). Denna metod tar även bort filerna i containern.

```java
try {
if(container != null)
    container.deleteIfExists();
} catch (StorageException ex) {
System.out.println(String.format("Service error. Http code: %d and error code: %s", ex.getHttpStatusCode(), ex.getErrorCode()));
}

System.out.println("Deleting the source, and downloaded files");

if(downloadedFile != null)
downloadedFile.deleteOnExit();
        
if(sourceFile != null)
sourceFile.deleteOnExit();
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln beskrivs hur du överför filer mellan en lokal disk och Azure Blob storage med Java. För att få mer information om hur du arbetar med Java fortsätter du till vår lagringsplats för GitHub-källkod.

> [!div class="nextstepaction"]
> [Microsoft Azure Storage SDK v10 för Java](https://github.com/azure/azure-storage-java) 
> [Java API-referens](https://docs.microsoft.com/java/azure/)
> [kodexempel för Java](../common/storage-samples-java.md)
