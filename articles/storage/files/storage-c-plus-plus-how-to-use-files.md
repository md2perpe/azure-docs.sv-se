---
title: Utveckla för Azure Files med C++ | Microsoft Docs
description: Lär dig hur du utvecklar C++-program och tjänster som använder Azure-filer för att lagra fildata.
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 09/19/2017
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 424db4435c569475f9463894b5364fc22190689a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64717918"
---
# <a name="develop-for-azure-files-with-c"></a>Utveckla för Azure Files med C++

[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

[!INCLUDE [storage-try-azure-tools-files](../../../includes/storage-try-azure-tools-files.md)]

## <a name="about-this-tutorial"></a>Om den här självstudiekursen

I de här självstudierna lär du dig att utföra grundläggande åtgärder på Azure Files. Genom sampel som är skrivna i C++, lär du dig att skapa resurser och kataloger, ladda upp, lista och ta bort filer. Om du är nybörjare på Azure Files, kommer att gå igenom begreppen i avsnitten som följer förstå exemplen.

* Skapa och ta bort Azure-filresurser
* Skapa och ta bort kataloger
* Räkna upp filer och kataloger i en Azure-filresurs
* Ladda upp, hämta och ta bort en fil
* Ange kvot (största storleken) för en Azure-filresurs
* Skapa en signatur för delad åtkomst (SAS-nyckel) för en fil som använder en princip för delad åtkomst som definierats för resursen.

> [!Note]  
> Eftersom Azure Files kan nås över SMB, går det att skriva enkla program som har åtkomst till Azure-filresursen med standard C++-i/o-klasser och funktioner. Den här artikeln beskriver hur du skriver program som använder Azure Storage C++ SDK, som använder den [File REST API](https://docs.microsoft.com/rest/api/storageservices/file-service-rest-api) att kommunicera med Azure Files.

## <a name="create-a-c-application"></a>Skapa ett C++-program

Om du vill skapa exemplen behöver du installerar Azure Storage-klientbiblioteket 2.4.0 för C++. Du bör också skapat ett Azure storage-konto.

För att du har installerat Azure Storage-klienten 2.4.0 för C++ kan använda du någon av följande metoder:

* **Linux:** Följ instruktionerna den [Azure Storage-klientbiblioteket för C++ README](https://github.com/Azure/azure-storage-cpp/blob/master/README.md) sidan.
* **Windows:** I Visual Studio klickar du på **verktyg &gt; NuGet-Pakethanteraren &gt; Pakethanterarkonsolen**. Skriv följande kommando i den [NuGet Package Manager-konsolen](https://docs.nuget.org/docs/start-here/using-the-package-manager-console) och tryck på **RETUR**.
  

```powershell
Install-Package wastorage
```

## <a name="set-up-your-application-to-use-azure-files"></a>Konfigurera programmet att använda Azure Files

Lägg till följande ingår satser längst upp i källfilen C++ där du vill hantera Azure Files:

```cpp
#include <was/storage_account.h>
#include <was/file.h>
```

## <a name="set-up-an-azure-storage-connection-string"></a>Konfigurera en anslutningssträng för Azure storage

Om du vill använda fillagring, måste du ansluta till Azure storage-kontot. Det första steget är att konfigurera en anslutningssträng som vi använder för att ansluta till ditt lagringskonto. Nu ska vi definiera en statisk variabel för att göra detta.

```cpp
// Define the connection-string with your values.
const utility::string_t
storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=your_storage_account;AccountKey=your_storage_account_key"));
```

## <a name="connecting-to-an-azure-storage-account"></a>Ansluta till ett Azure storage-konto

Du kan använda den **cloud_storage_account** klass för att representera kontoinformationen för lagring. Du hämtar informationen om lagringskontot från Azure Storage-anslutningssträngen med hjälp av metoden **parse**.

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account =
  azure::storage::cloud_storage_account::parse(storage_connection_string);
```

## <a name="create-an-azure-file-share"></a>Skapa en Azure-filresurs

Alla filer och kataloger i en Azure-filresurs finns i en behållare som kallas en **dela**. Ditt storage-konto kan ha så många resurser som gör att din kapacitet. För att få åtkomst till en resurs och dess innehåll kan behöva du använda en Azure Files-klient.

```cpp
// Create the Azure Files client.
azure::storage::cloud_file_client file_client =
  storage_account.create_cloud_file_client();
```

Med hjälp av Azure Files-klienten, kan du sedan hämta en referens till en resurs.

```cpp
// Get a reference to the file share
azure::storage::cloud_file_share share =
  file_client.get_share_reference(_XPLATSTR("my-sample-share"));
```

Om du vill skapa resursen, Använd den **create_if_not_exists** -metoden för den **cloud_file_share** objekt.

```cpp
if (share.create_if_not_exists()) {
    std::wcout << U("New share created") << std::endl;
}
```

Nu **dela** innehåller en referens till en resurs med namnet **my-sample-share**.

## <a name="delete-an-azure-file-share"></a>Ta bort en Azure-filresurs

Tar bort en resurs gör du genom att anropa den **delete_if_exists** metod på ett cloud_file_share-objekt. Här är exempelkod som gör detta.

```cpp
// Get a reference to the share.
azure::storage::cloud_file_share share =
  file_client.get_share_reference(_XPLATSTR("my-sample-share"));

// delete the share if exists
share.delete_share_if_exists();
```

## <a name="create-a-directory"></a>Skapa en katalog

Du kan sortera lagring genom att placera filer i underkataloger i stället för att alla i rotkatalogen. Azure Files kan du skapa så många kataloger som ditt konto tillåter. Koden nedan skapar en katalog med namnet **my-sample-directory** under rotkatalogen samt en undermapp som heter **min exempel underkatalog**.

```cpp
// Retrieve a reference to a directory
azure::storage::cloud_file_directory directory = share.get_directory_reference(_XPLATSTR("my-sample-directory"));

// Return value is true if the share did not exist and was successfully created.
directory.create_if_not_exists();

// Create a subdirectory.
azure::storage::cloud_file_directory subdirectory =
  directory.get_subdirectory_reference(_XPLATSTR("my-sample-subdirectory"));
subdirectory.create_if_not_exists();
```

## <a name="delete-a-directory"></a>Ta bort en katalog

Ta bort en katalog är en enkel uppgift, men det bör noteras att du inte kan ta bort en katalog som fortfarande innehåller filer eller andra kataloger.

```cpp
// Get a reference to the share.
azure::storage::cloud_file_share share =
  file_client.get_share_reference(_XPLATSTR("my-sample-share"));

// Get a reference to the directory.
azure::storage::cloud_file_directory directory =
  share.get_directory_reference(_XPLATSTR("my-sample-directory"));

// Get a reference to the subdirectory you want to delete.
azure::storage::cloud_file_directory sub_directory =
  directory.get_subdirectory_reference(_XPLATSTR("my-sample-subdirectory"));

// Delete the subdirectory and the sample directory.
sub_directory.delete_directory_if_exists();

directory.delete_directory_if_exists();
```

## <a name="enumerate-files-and-directories-in-an-azure-file-share"></a>Räkna upp filer och kataloger i en Azure-filresurs

Hämtar en lista över filer och kataloger i en resurs görs enkelt genom att anropa **list_files_and_directories** på en **cloud_file_directory** referens. Komma åt den omfattande uppsättningen med egenskaper och metoder för en returnerad **list_file_and_directory_item**, måste du anropa den **list_file_and_directory_item.as_file** metod för att hämta en **cloud_file**  objekt, eller **list_file_and_directory_item.as_directory** metod för att hämta en **cloud_file_directory** objekt.

Följande kod visar hur du hämtar och returnerar URI: N för varje objekt i rotkatalogen i resursen.

```cpp
//Get a reference to the root directory for the share.
azure::storage::cloud_file_directory root_dir =
  share.get_root_directory_reference();

// Output URI of each item.
azure::storage::list_file_and_directory_result_iterator end_of_results;

for (auto it = directory.list_files_and_directories(); it != end_of_results; ++it)
{
    if(it->is_directory())
    {
        ucout << "Directory: " << it->as_directory().uri().primary_uri().to_string() << std::endl;
    }
    else if (it->is_file())
    {
        ucout << "File: " << it->as_file().uri().primary_uri().to_string() << std::endl;
    }
}
```

## <a name="upload-a-file"></a>Överför en fil

Åtminstone innehåller en rotkatalog där filer kan finnas i en Azure-filresurs. I det här avsnittet lär du dig att överföra en fil från lokal lagring till rotkatalogen för en resurs.

Det första steget i att ladda upp en fil är att hämta en referens till katalogen där det ska finnas. Du gör detta genom att anropa den **get_root_directory_reference** -metoden i resurs-objektet.

```cpp
//Get a reference to the root directory for the share.
azure::storage::cloud_file_directory root_dir = share.get_root_directory_reference();
```

Nu när du har en referens till rotkatalogen för resursen kan överföra du en fil till den. Det här exemplet Överför från en fil från text och från en dataström.

```cpp
// Upload a file from a stream.
concurrency::streams::istream input_stream =
  concurrency::streams::file_stream<uint8_t>::open_istream(_XPLATSTR("DataFile.txt")).get();

azure::storage::cloud_file file1 =
  root_dir.get_file_reference(_XPLATSTR("my-sample-file-1"));
file1.upload_from_stream(input_stream);

// Upload some files from text.
azure::storage::cloud_file file2 =
  root_dir.get_file_reference(_XPLATSTR("my-sample-file-2"));
file2.upload_text(_XPLATSTR("more text"));

// Upload a file from a file.
azure::storage::cloud_file file4 =
  root_dir.get_file_reference(_XPLATSTR("my-sample-file-3"));
file4.upload_from_file(_XPLATSTR("DataFile.txt"));
```

## <a name="download-a-file"></a>Hämta en fil

För att hämta filer, hämta en filreferens först och sedan anropa den **download_to_stream** metod för att överföra filens innehåll till ett dataströmsobjekt som du sedan kan spara till en lokal fil. Du kan också använda den **download_to_file** metod för att hämta innehållet i en fil till en lokal fil. Du kan använda den **download_text** metod för att hämta innehållet i en fil som en textsträng.

I följande exempel används den **download_to_stream** och **download_text** metoder för att demonstrera laddar ned filerna som skapades i föregående avsnitt.

```cpp
// Download as text
azure::storage::cloud_file text_file =
  root_dir.get_file_reference(_XPLATSTR("my-sample-file-2"));
utility::string_t text = text_file.download_text();
ucout << "File Text: " << text << std::endl;

// Download as a stream.
azure::storage::cloud_file stream_file =
  root_dir.get_file_reference(_XPLATSTR("my-sample-file-3"));

concurrency::streams::container_buffer<std::vector<uint8_t>> buffer;
concurrency::streams::ostream output_stream(buffer);
stream_file.download_to_stream(output_stream);
std::ofstream outfile("DownloadFile.txt", std::ofstream::binary);
std::vector<unsigned char>& data = buffer.collection();
outfile.write((char *)&data[0], buffer.size());
outfile.close();
```

## <a name="delete-a-file"></a>Ta bort en fil

En annan vanliga Azure Files-åtgärden kan ta bort filen. Följande kod tar du bort en fil med namnet my-sample-filen-3 lagras under rotkatalogen.

```cpp
// Get a reference to the root directory for the share.
azure::storage::cloud_file_share share =
  file_client.get_share_reference(_XPLATSTR("my-sample-share"));

azure::storage::cloud_file_directory root_dir =
  share.get_root_directory_reference();

azure::storage::cloud_file file =
  root_dir.get_file_reference(_XPLATSTR("my-sample-file-3"));

file.delete_file_if_exists();
```

## <a name="set-the-quota-maximum-size-for-an-azure-file-share"></a>Ange kvot (största storleken) för en Azure-filresurs

Du kan ange kvoten (eller maxstorleken) för en filresurs, i gigabyte. Du kan också kontrollera hur mycket data som lagras på resursen för närvarande.

Genom att ange kvoten för en resurs kan du begränsa den totala storleken på filerna som lagras på resursen. Om den totala storleken på filerna som lagras på resursen överskrider kvoten som angetts för resursen kan klienterna inte öka storleken på befintliga filer eller skapa nya filer såvida inte dessa filer är tomma.

Exemplet nedan visar hur du kontrollerar användningen av en resurs och hur du ställer in kvoten för resursen.

```cpp
// Parse the connection string for the storage account.
azure::storage::cloud_storage_account storage_account =
  azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the file client.
azure::storage::cloud_file_client file_client =
  storage_account.create_cloud_file_client();

// Get a reference to the share.
azure::storage::cloud_file_share share =
  file_client.get_share_reference(_XPLATSTR("my-sample-share"));
if (share.exists())
{
    std::cout << "Current share usage: " << share.download_share_usage() << "/" << share.properties().quota();

    //This line sets the quota to 2560GB
    share.resize(2560);

    std::cout << "Quota increased: " << share.download_share_usage() << "/" << share.properties().quota();

}
```

## <a name="generate-a-shared-access-signature-for-a-file-or-file-share"></a>Generera en signatur för delad åtkomst för en fil eller filresurs

Du kan generera en signatur för delad åtkomst (SAS) för en filresurs eller för en enskild fil. Du kan också skapa en princip för delad åtkomst på en filresurs för att hantera signaturer för delad åtkomst. Vi rekommenderar att du skapar en princip för delad åtkomst eftersom det ger dig möjlighet att återkalla signaturen för delad åtkomst om det behövs.

I följande exempel skapar vi en princip för delad åtkomst på en resurs och använder sedan principen för att ange begränsningarna för en signatur för delad åtkomst på en fil i resursen.

```cpp
// Parse the connection string for the storage account.
azure::storage::cloud_storage_account storage_account =
  azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the file client and get a reference to the share
azure::storage::cloud_file_client file_client =
  storage_account.create_cloud_file_client();

azure::storage::cloud_file_share share =
  file_client.get_share_reference(_XPLATSTR("my-sample-share"));

if (share.exists())
{
    // Create and assign a policy
    utility::string_t policy_name = _XPLATSTR("sampleSharePolicy");

    azure::storage::file_shared_access_policy sharedPolicy =
      azure::storage::file_shared_access_policy();

    //set permissions to expire in 90 minutes
    sharedPolicy.set_expiry(utility::datetime::utc_now() +
       utility::datetime::from_minutes(90));

    //give read and write permissions
    sharedPolicy.set_permissions(azure::storage::file_shared_access_policy::permissions::write | azure::storage::file_shared_access_policy::permissions::read);

    //set permissions for the share
    azure::storage::file_share_permissions permissions;

    //retrieve the current list of shared access policies
    azure::storage::shared_access_policies<azure::storage::file_shared_access_policy> policies;

    //add the new shared policy
    policies.insert(std::make_pair(policy_name, sharedPolicy));

    //save the updated policy list
    permissions.set_policies(policies);
    share.upload_permissions(permissions);

    //Retrieve the root directory and file references
    azure::storage::cloud_file_directory root_dir =
        share.get_root_directory_reference();
    azure::storage::cloud_file file =
      root_dir.get_file_reference(_XPLATSTR("my-sample-file-1"));

    // Generate a SAS for a file in the share
    //  and associate this access policy with it.
    utility::string_t sas_token = file.get_shared_access_signature(sharedPolicy);

    // Create a new CloudFile object from the SAS, and write some text to the file.
    azure::storage::cloud_file file_with_sas(azure::storage::storage_credentials(sas_token).transform_uri(file.uri().primary_uri()));
    utility::string_t text = _XPLATSTR("My sample content");
    file_with_sas.upload_text(text);

    //Download and print URL with SAS.
    utility::string_t downloaded_text = file_with_sas.download_text();
    ucout << downloaded_text << std::endl;
    ucout << azure::storage::storage_credentials(sas_token).transform_uri(file.uri().primary_uri()).to_string() << std::endl;

}
```

## <a name="next-steps"></a>Nästa steg

Utforska gärna dessa resurser om du vill veta mer om Azure Storage:

* [Storage-klientbibliotek för C++](https://github.com/Azure/azure-storage-cpp)
* [Azure Storage Service filexempel i C++](https://github.com/Azure-Samples/storage-file-cpp-getting-started)
* [Azure Storage Explorer](https://go.microsoft.com/fwlink/?LinkID=822673&clcid=0x409)
* [Azure Storage-dokumentation](https://azure.microsoft.com/documentation/services/storage/)