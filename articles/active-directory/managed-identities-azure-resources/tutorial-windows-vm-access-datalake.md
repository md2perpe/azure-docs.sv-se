---
title: Så använder du en systemtilldelad hanterad identitet för en virtuell Windows-dator för åtkomst till Azure Data Lake Store
description: En självstudiekurs som visar hur du använder en systemtilldelad hanterad identitet för virtuell Windows-dator för åtkomst till Azure Data Lake Store.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/13/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6cd03ccdab6a7120dc59e42ab62c1d3b2d1e5bc3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60304901"
---
# <a name="tutorial-use-a-windows-vm-system-assigned-managed-identity-to-access-azure-data-lake-store"></a>Självstudier: Använda en systemtilldelad hanterad identitet för en virtuell Windows-dator för åtkomst till Azure Data Lake Store

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

I den här självstudien lär du dig att komma åt en Azure Data Lake Store med en systemtilldelad hanterad identitet för en virtuell Windows-dator. Hanterade tjänstidentiteter hanteras automatiskt av Azure och gör att du kan autentisera mot tjänster som stöder Azure AD-autentisering, utan att du behöver skriva in autentiseringsuppgifter i koden. Lär dig att:

> [!div class="checklist"]
> * Bevilja din virtuella dator åtkomst till en Azure Data Lake Store
> * Hämta en åtkomsttoken med hjälp av en identitet för en virtuell dator och använd den för att få åtkomst till en Azure Data Lake Store

## <a name="prerequisites"></a>Nödvändiga komponenter

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="grant-your-vm-access-to-azure-data-lake-store"></a>Bevilja din virtuella dator åtkomst till Azure Data Lake Store

Nu kan du ge din VM-åtkomst till filer och mappar i en Azure Data Lake Store.  Du kan använda en befintlig Data Lake Store eller skapa en ny för det här steget.  För att skapa en ny Data Lake Store med hjälp av Azure-portalen följer du den här [snabbstarten för Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal). Det finns även snabbstarter som använder Azure CLI och Azure PowerShell i [dokumentationen om Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview).

I din Data Lake Store skapar du en ny mapp och ger den virtuella datorns systemtilldelade identitet behörighet att läsa, skriva och köra filer i mappen:

1. I Azure-portalen klickar du på **Data Lake Store** i det vänstra navigeringsfältet.
2. Klicka på den Data Lake Store som du vill använda för den här självstudien.
3. Klicka på **Datautforskaren** i kommandofältet.
4. Rotmappen för Data Lake Store har valts.  Klicka på **Åtkomst** i kommandofältet.
5. Klicka på **Lägg till**.  I fältet **Välj** anger du namnet på den virtuella datorn, till exempel **DevTestVM**.  Klicka för att välja den virtuella datorn från sökresultatet och klicka sedan på **Välj**.
6. Klicka på **Välj behörigheter**.  Välj **Läs** och **Kör**, lägg till **den här mappen** och lägg till den med **endast åtkomstbehörighet**.  Klicka på **OK**.  Behörigheten bör nu har lagts till.
7. Stäng bladet **Åtkomst**.
8. För den här självstudien skapar du en ny mapp.  Klicka på **Ny mapp** i kommandofältet och ge den nya mappen ett namn, till exempel **TestFolder**.  Klicka på **OK**.
9. Klicka på den mapp du skapade och klicka sedan på **Åtkomst** i kommandofältet.
10. Som i steg 5 klickar du på **Lägg till**. I fältet **Välj** anger du namnet på den virtuella datorn. Markera den och klicka på **Välj**.
11. Som i steg 6 klickar du på **Välj behörigheter**. Välj **Läs**, **Skriv** och **Kör**, lägg till i **den här mappen** och lägg till som **en åtkomstbehörighetspost och en standardbehörighetspost**.  Klicka på **OK**.  Behörigheten bör nu har lagts till.

Den virtuella datorns systemtilldelade hanterade identitet kan nu utföra alla åtgärder på filer i den mapp som du skapade.  Mer information om hur du hanterar åtkomst till Data Lake Store finns i den här artikeln om [Åtkomstkontroll i Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-access-control).

## <a name="get-an-access-token-using-the-vms-system-assigned-managed-identity-and-use-it-to-call-the-azure-data-lake-store-filesystem"></a>Hämta en åtkomsttoken med hjälp av den virtuella datorns systemtilldelade hanterade identitet och använd den för att anropa Azure Data Lake Store-filsystemet

Azure Data Lake Store har inbyggt stöd för Azure AD-autentisering, vilket gör att åtkomsttoken som hämtas med hjälp av hanterade identiteter för Azure-resurser.  För att autentisera till Data Lake Store-filsystemet skickar du en åtkomsttoken som utfärdats av Azure AD till slutpunkten för Data Lake Store-filsystemet i en auktoriseringsrubrik i formatet ”Bearer <ACCESS_TOKEN_VALUE>”.  Mer information om Data Lake Store-stöd för Azure AD-autentisering finns avsnittet om [autentisering med Data Lake Store med hjälp av Azure Active Directory](https://docs.microsoft.com/azure/data-lake-store/data-lakes-store-authentication-using-azure-active-directory)

> [!NOTE]
> Data Lake Store-filsystemets klient-SDK:er stöder inte ännu hanterade identiteter för Azure-resurser.  Den här självstudien kommer att uppdateras när stöd har lagts till i SDK.

I den här självstudien autentiserar du till Lake Store-filsystemets REST API med hjälp av PowerShell för att göra REST-begäranden. Om du vill använda den virtuella datorns systemtilldelade hanterade identitet för autentisering behöver du göra begäranden från den virtuella datorn.

1. I portalen går du till **Virtuella datorer** och sedan till den virtuella Windows-datorn. Under **Översikt** klickar du på **Anslut**.
2. Ange ditt **användarnamn** och **lösenord** som du lade till när du skapade den virtuella Windows-datorn. 
3. Nu när du har skapat en **anslutning till fjärrskrivbord** med den virtuella datorn öppnar du **PowerShell** i fjärrsessionen. 
4. Använd PowerShells `Invoke-WebRequest` och skicka en begäran till den lokala slutpunkten för hanterade identiteter för Azure-resurser för att hämta en åtkomsttoken för Azure Data Lake Store.  Resurs-ID: t för Data Lake Store är `https://datalake.azure.net/`.  Data Lake gör en exakt matchning på resurs-ID, och det avslutande snedstrecket är viktigt.

   ```powershell
   $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fdatalake.azure.net%2F' -Method GET -Headers @{Metadata="true"}
   ```
    
   Konvertera svaret från ett JSON-objekt till ett PowerShell-objekt. 
    
   ```powershell
   $content = $response.Content | ConvertFrom-Json
   ```

   Extrahera åtkomsttoken från svaret.
    
   ```powershell
   $AccessToken = $content.access_token
   ```

5. Med hjälp av PowerShells ”Invoke-WebRequest” skickar du en begäran till REST-slutpunkten för din Data Lake Store för att lista mappar i rotmappen.  Det här är ett enkelt sätt att kontrollera att allt är korrekt konfigurerat.  Det är viktigt att strängen ”Bearer” (ägartoken) i auktoriseringsrubriken har versalt ”B”.  Du hittar namnet på din Data Lake Store i **översikten** av Data Lake Store-bladet i Azure-portalen.

   ```powershell
   Invoke-WebRequest -Uri https://<YOUR_ADLS_NAME>.azuredatalakestore.net/webhdfs/v1/?op=LISTSTATUS -Headers @{Authorization="Bearer $AccessToken"}
   ```

   Ett lyckat svar ut så här:

   ```powershell
   StatusCode        : 200
   StatusDescription : OK
   Content           : {"FileStatuses":{"FileStatus":[{"length":0,"pathSuffix":"TestFolder","type":"DIRECTORY", "blockSize":0,"accessTime":1507934941392, "modificationTime":1507944835699,"replication":0, "permission":"770","ow..."
   RawContent        : HTTP/1.1 200 OK
                       Pragma: no-cache
                       x-ms-request-id: b4b31e16-e968-46a1-879a-3474aa7d4528
                       x-ms-webhdfs-version: 17.04.22.00
                       Status: 0x0
                       X-Content-Type-Options: nosniff
                       Strict-Transport-Security: ma...
   Forms             : {}
   Headers           : {[Pragma, no-cache], [x-ms-request-id, b4b31e16-e968-46a1-879a-3474aa7d4528],
                       [x-ms-webhdfs-version, 17.04.22.00], [Status, 0x0]...}
   Images            : {}
   InputFields       : {}
   Links             : {}
   ParsedHtml        : System.__ComObject
   RawContentLength  : 556
   ```

6. Nu kan du prova att ladda upp en fil till din Data Lake Store.  Skapa först en fil som ska laddas upp.

   ```powershell
   echo "Test file." > Test1.txt
   ```

7. Med hjälp av PowerShells `Invoke-WebRequest` skickar du en begäran till REST-slutpunkten för din Data Lake Store för att ladda upp filen till den mapp som du skapade förut.  Den här begäran kräver två steg.  I det första steget skapar du en begäran och får en omdirigering till det ställe där filen ska laddas upp.  I det andra steget utför du själva uppladdningen av filen.  Kom ihåg att ange namnet på mappen och filen på lämpligt sätt om du har använt andra värden än dem som förekommer i den här självstudien. 

   ```powershell
   $HdfsRedirectResponse = Invoke-WebRequest -Uri https://<YOUR_ADLS_NAME>.azuredatalakestore.net/webhdfs/v1/TestFolder/Test1.txt?op=CREATE -Method PUT -Headers @{Authorization="Bearer $AccessToken"} -Infile Test1.txt -MaximumRedirection 0
   ```

   Om du inspekterar värdet för `$HdfsRedirectResponse` bör det se ut som följande svar:

   ```powershell
   PS C:\> $HdfsRedirectResponse

   StatusCode        : 307
   StatusDescription : Temporary Redirect
   Content           : {}
   RawContent        : HTTP/1.1 307 Temporary Redirect
                       Pragma: no-cache
                       x-ms-request-id: b7ab492f-b514-4483-aada-4aa0611d12b3
                       ContentLength: 0
                       x-ms-webhdfs-version: 17.04.22.00
                       Status: 0x0
                       X-Content-Type-Options: nosn...
   Headers           : {[Pragma, no-cache], [x-ms-request-id, b7ab492f-b514-4483-aada-4aa0611d12b3], 
                       [ContentLength, 0], [x-ms-webhdfs-version, 17.04.22.00]...}
   RawContentLength  : 0
   ```

   Slutför uppladdningen genom att skicka en begäran till omdirigeringsslutpunkten:

   ```powershell
   Invoke-WebRequest -Uri $HdfsRedirectResponse.Headers.Location -Method PUT -Headers @{Authorization="Bearer $AccessToken"} -Infile Test1.txt -MaximumRedirection 0
   ```

   Ett lyckat svar ut så här:

   ```powershell
   StatusCode        : 201
   StatusDescription : Created
   Content           : {}
   RawContent        : HTTP/1.1 201 Created
                       Pragma: no-cache
                       x-ms-request-id: 1e70f36f-ead1-4566-acfa-d0c3ec1e2307
                       ContentLength: 0
                       x-ms-webhdfs-version: 17.04.22.00
                       Status: 0x0
                       X-Content-Type-Options: nosniff
                       Strict...
   Headers           : {[Pragma, no-cache], [x-ms-request-id, 1e70f36f-ead1-4566-acfa-d0c3ec1e2307],
                       [ContentLength, 0], [x-ms-webhdfs-version, 17.04.22.00]...}
   RawContentLength  : 0
   ```

Genom att använda andra Data Lake Store-API:er kan du lägga till i filer, ladda ned filer med mera.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig att använda en systemtilldelad hanterad identitet för en virtuell Windows-dator för att få åtkomst till Data Lake Store. Läs mer om Azure Data Lake Store:

> [!div class="nextstepaction"]
>[Azure Data Lake Store](/azure/data-lake-store/data-lake-store-overview)
