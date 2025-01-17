---
title: Tjänst-till-tjänst-autentisering till Azure Key Vault med hjälp av .NET
description: Använd Microsoft.Azure.Services.AppAuthentication-biblioteket för att autentisera till Azure Key Vault med hjälp av .NET.
keywords: Azure key vault-lokala autentiseringsuppgifter
author: msmbaldwin
manager: barbkess
services: key-vault
ms.author: mbaldwin
ms.date: 07/06/2019
ms.topic: conceptual
ms.service: key-vault
ms.openlocfilehash: 79d4254de40ef787b30eb4f483c86383a928ee1f
ms.sourcegitcommit: d2785f020e134c3680ca1c8500aa2c0211aa1e24
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/04/2019
ms.locfileid: "67566229"
---
# <a name="service-to-service-authentication-to-azure-key-vault-using-net"></a>Tjänst-till-tjänst-autentisering till Azure Key Vault med hjälp av .NET

För att autentisera till Azure Key Vault behöver du en autentiseringsuppgift för Azure Active Directory (AD), en delad hemlighet eller ett certifikat. 

Hantera dessa autentiseringsuppgifter kan vara svårt och det är lockande att bifoga autentiseringsuppgifter i en app genom att inkludera dem i käll- eller configuration-filer.  Den `Microsoft.Azure.Services.AppAuthentication` för .NET-bibliotek förenklar det här problemet. Utvecklarens autentiseringsuppgifter används för att autentisera under lokal utveckling. När lösningen distribueras senare till Azure, växlar biblioteket automatiskt till programmet autentiseringsuppgifter.    Med developer autentiseringsuppgifter under lokal utveckling är säkrare eftersom du inte behöver skapa autentiseringsuppgifter för Azure AD eller dela autentiseringsuppgifter mellan utvecklare.

Den `Microsoft.Azure.Services.AppAuthentication` biblioteket hanterar autentisering automatiskt, vilket i sin tur gör att du kan fokusera på din lösning i stället för dina autentiseringsuppgifter.  Det har stöd för lokal utveckling med Microsoft Visual Studio, Azure CLI eller Azure AD-integrerad autentisering. När de distribueras till en Azure-resurs som har stöd för en hanterad identitet, använder biblioteket automatiskt [hanterade identiteter för Azure-resurser](../active-directory/msi-overview.md). Ingen kod eller konfigurationsändringar krävs. Biblioteket stöder även direkt användning av Azure AD [klientautentiseringsuppgifter](../azure-resource-manager/resource-group-authenticate-service-principal.md) när en hanterad identitet inte är tillgänglig, eller när utvecklarens säkerhetskontext som inte kan fastställas under lokal utveckling.

## <a name="using-the-library"></a>Med hjälp av klientbiblioteket

För .NET-program, är det enklaste sättet att arbeta med en hanterad identitet via den `Microsoft.Azure.Services.AppAuthentication` paketet. Här är hur du kommer igång:

1. Lägg till referenser till den [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) och [Microsoft.Azure.KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault) NuGet-paket till ditt program. 

2. Lägg till följande kod:

    ``` csharp
    using Microsoft.Azure.Services.AppAuthentication;
    using Microsoft.Azure.KeyVault;

    // Instantiate a new KeyVaultClient object, with an access token to Key Vault
    var azureServiceTokenProvider1 = new AzureServiceTokenProvider();
    var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider1.KeyVaultTokenCallback));

    // Optional: Request an access token to other Azure services
    var azureServiceTokenProvider2 = new AzureServiceTokenProvider();
    string accessToken = await azureServiceTokenProvider2.GetAccessTokenAsync("https://management.azure.com/").ConfigureAwait(false);
    ```

Den `AzureServiceTokenProvider` klass cachelagrar token i minnet och hämtar från Azure AD precis före förfallodatum. Därför behöver du inte längre kontrollera förfallodatum innan du anropar den `GetAccessTokenAsync` metoden. Bara anropa metoden när du vill använda token. 

Den `GetAccessTokenAsync` metoden kräver en resursidentifierare. Mer information finns i [vilka Azure-tjänster stöder hanterade identiteter för Azure-resurser](../active-directory/msi-overview.md).

## <a name="local-development-authentication"></a>Lokal utveckling autentisering

Det finns två scenarier för primär autentisering för lokal utveckling: [autentisera till Azure-tjänster](#authenticating-to-azure-services), och [autentisering till anpassade tjänster](#authenticating-to-custom-services).

### <a name="authenticating-to-azure-services"></a>Autentisera till Azure-tjänster

Lokala datorer stöder inte hanterade identiteter för Azure-resurser.  Därför kan den `Microsoft.Azure.Services.AppAuthentication` biblioteket använder dina utvecklaruppgifter för att köra i din lokala utvecklingsmiljö. När lösningen har distribuerats till Azure använder en hanterad identitet för att växla till en autentiseringsuppgift bevilja flöde för OAuth 2.0-klienten.  Det innebär att du kan testa samma kod lokalt och via en fjärranslutning utan besvär.

För lokal utveckling `AzureServiceTokenProvider` hämtar token med **Visual Studio**, **kommandoradsgränssnittet** (CLI), eller **Azure AD-integrerad autentisering**. Varje alternativ har försökt sekventiellt och använder det första alternativet som lyckas. Om inget alternativ fungerar, en `AzureServiceTokenProviderException` undantagsfel med detaljerad information.

### <a name="authenticating-with-visual-studio"></a>Autentisera med Visual Studio

Autentisering med Visual Studio har följande krav:

1. [Visual Studio 2017 v15.5](https://blogs.msdn.microsoft.com/visualstudio/2017/10/11/visual-studio-2017-version-15-5-preview/) eller senare.

2. Den [Appautentisering tillägget för Visual Studio](https://go.microsoft.com/fwlink/?linkid=862354), tillgängliga som en separat tillägg för Visual Studio 2017 uppdatering 5 och tillsammans med produkten i uppdatering 6 och senare. Med uppdatering 6 eller senare, kan du verifiera installationen av tillägget Appautentisering genom att välja Azure utvecklingsverktyg från i installationsprogrammet för Visual Studio.
 
Logga in till Visual Studio och använda **verktyg**&nbsp;>&nbsp;**alternativ**&nbsp;>&nbsp;**Azure Service Autentisering** att välja ett konto för lokal utveckling. 

Om du stöter på problem med att använda Visual Studio, till exempel fel om filen tokenleverantör noga igenom de här stegen. 

Det kan också vara nödvändigt att autentiseras på nytt din developer token. Du gör detta genom att gå till **verktyg**&nbsp;>&nbsp;**alternativ**>**Azure&nbsp;Service&nbsp;autentisering**  och leta efter en **återautentisera** länken under det valda kontot.  Välj den för att autentisera. 

### <a name="authenticating-with-azure-cli"></a>Autentisera med Azure CLI

Du använder Azure CLI för lokal utveckling:

1. Installera [Azure CLI v2.0.12](/cli/azure/install-azure-cli) eller senare. Uppgradera tidigare versioner. 

2. Använd **az-inloggning** att logga in på Azure.

Använd `az account get-access-token` att kontrollera åtkomst.  Om du får ett felmeddelande, kontrollerar du att steg 1 har slutförts. 

Om Azure CLI inte är installerat i standardkatalogen kan ett felmeddelande reporting som `AzureServiceTokenProvider` det går inte att hitta sökvägen för Azure CLI.  Använd den **AzureCLIPath** miljövariabeln definiera Azure CLI-installationsmappen. `AzureServiceTokenProvider` lägger till den katalog som anges i den **AzureCLIPath** miljövariabeln till den **sökväg** miljövariabeln vid behov.

Om du är inloggad på Azure CLI med hjälp av flera konton eller ditt konto har åtkomst till flera prenumerationer, måste du ange specifika prenumerationen som ska användas.  Om du vill göra det använder du:

```
az account set --subscription <subscription-id>
```

Det här kommandot genererar utdata endast vid fel.  Om du vill kontrollera inställningarna för aktuellt konto använder du:

```
az account list
```

### <a name="authenticating-with-azure-ad-authentication"></a>Autentisering med Azure AD-autentisering

Om du vill använda Azure AD-autentisering, kontrollerar du att:

- Din lokala active directory [synkroniseras till Azure AD](../active-directory/connect/active-directory-aadconnect.md).

- Din kod körs på en domänansluten dator.


### <a name="authenticating-to-custom-services"></a>Autentisering till anpassade tjänster

När en tjänst-anrop Azure-tjänster fungerar de föregående stegen eftersom Azure-tjänster tillåta åtkomst till både användare och program.  

När du skapar en tjänst som anropar en anpassad tjänst kan du använda autentiseringsuppgifter för Azure AD-klient för lokal utveckling autentisering.  Det finns två alternativ: 

1.  Använd ett huvudnamn för tjänsten för att logga in på Azure:

    1.  [Skapa ett huvudnamn för tjänsten](/cli/azure/create-an-azure-service-principal-azure-cli).

    2.  Använd Azure CLI för att logga in:

        ```azurecli
        az login --service-principal -u <principal-id> --password <password> --tenant <tenant-id> --allow-no-subscriptions
        ```

        Eftersom tjänstens huvudnamn inte kan ha åtkomst till en prenumeration, använda den `--allow-no-subscriptions` argumentet.

2.  Använd miljövariabler för att ange huvudnamn tjänstinformation.  Mer information finns i [som kör programmet med ett huvudnamn för tjänsten](#running-the-application-using-a-service-principal).

När du har loggat in till Azure, `AzureServiceTokenProvider` använder tjänstens huvudnamn för att hämta en token för lokal utveckling.

Detta gäller endast för lokal utveckling. När lösningen har distribuerats till Azure, växlar biblioteket till en hanterad identitet för autentisering.

## <a name="running-the-application-using-managed-identity-or-user-assigned-identity"></a>Kör programmet med hanterad identitet eller Användartilldelad identitet 

När du kör koden på en Azure App Service eller en Azure-dator med en hanterad identitet aktiverat använder biblioteket automatiskt den hanterade identitet. 

Du kan också autentisera med en Användartilldelad identitet. Läs mer på användartilldelade identiteter, [om hanteras av identiteter för Azure-resurser](../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work). Du måste ange klient-ID för Användartilldelad identitet i anslutningssträngen för att autentisera med en Användartilldelad identitet. Anslutningssträngen har angetts i den [sträng anslutningssupport](#connection-string-support) nedan.

## <a name="running-the-application-using-a-service-principal"></a>Kör programmet med ett huvudnamn för tjänsten 

Det kan vara nödvändigt att skapa en Azure AD-klienten autentiseringsuppgifter för att autentisera. Vanliga exempel:

- Koden körs på en lokal utvecklingsmiljö, men inte under utvecklarens identitet.  Service Fabric kan till exempel använder den [kontot NetworkService](../service-fabric/service-fabric-application-secret-management.md) för lokal utveckling.
 
- Din kod körs på en lokal utvecklingsmiljö och du autentiserar till en anpassad tjänst så att du inte kan använda din developer-identitet. 
 
- Din kod körs på en Azure-beräkningsresurs som inte har stöd för hanterade identiteter för Azure-resurser, till exempel Azure Batch.

Det finns tre huvudsakliga sätt för att använda ett huvudnamn för tjänsten för att köra programmet. Om du vill använda någon av dem måste du först [skapa ett huvudnamn för tjänsten](/cli/azure/create-an-azure-service-principal-azure-cli).

### <a name="use-a-certificate-in-local-keystore-to-sign-into-azure-ad"></a>Använda ett certifikat i lokal keystore att logga in på Azure AD

1. Skapa en tjänstobjektets certifikat med hjälp av Azure CLI [az ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) kommando. 

    ```azurecli
    az ad sp create-for-rbac --create-cert
    ```

    Detta skapar en .pem-fil (privata nyckeln) som lagras i din arbetskatalog. Distribuera det här certifikatet till antingen den *LocalMachine* eller *CurrentUser* lagra. 

    > [!Important]
    > CLI-kommando genererar en .pem-fil, men endast innehåller inbyggt stöd för PFX-certifikat i Windows. Om du vill generera ett PFX-certifikat i stället använder du PowerShell-kommandon som visas här: [Skapa tjänstens huvudnamn med självsignerat certifikat](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-self-signed-certificate). Dessa kommandon distribuera automatiskt certifikat samt.

1. Ange en miljövariabel som heter **AzureServicesAuthConnectionString** till:

    ```
    RunAs=App;AppId={AppId};TenantId={TenantId};CertificateThumbprint={Thumbprint};
          CertificateStoreLocation={CertificateStore}
    ```
 
    Ersätt *{AppId}* , *{TenantId}* , och *{Thumbprint}* med värden som skapades i steg 1. Ersätt *{CertificateStore}* med antingen `LocalMachine` eller `CurrentUser`, baserat på din distributionsplan.

1. Kör appen. 

### <a name="use-a-shared-secret-credential-to-sign-into-azure-ad"></a>Använda en delad hemlighet autentiseringsuppgift för att logga in på Azure AD

1. Skapa en tjänstobjektets certifikat med ett lösenord med [az ad sp create-for-rbac--lösenord](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac). 

2. Ange en miljövariabel som heter **AzureServicesAuthConnectionString** till:

    ```
    RunAs=App;AppId={AppId};TenantId={TenantId};AppKey={ClientSecret} 
    ```

    Ersätt _{AppId}_ , _{TenantId}_ , och _{ClientSecret}_ med värden som skapades i steg 1.

3. Kör appen. 

När allt har konfigurerats korrekt, utan ytterligare kodändringar krävs.  `AzureServiceTokenProvider` använder miljövariabeln och certifikatet för att autentisera till Azure AD. 

### <a name="use-a-certificate-in-key-vault-to-sign-into-azure-ad"></a>Använda ett certifikat i Key Vault för att logga in på Azure AD

Det här alternativet kan du lagra klientcertifikat för tjänstens huvudnamn i Key Vault och använda den för autentisering av tjänstens huvudnamn. Du kan använda detta för följande scenarier:

* Lokal autentisering, där du vill autentisera med hjälp av ett explicit tjänstobjekt och vill behålla tjänstens huvudnamn autentiseringsuppgifter på ett säkert sätt i ett nyckelvalv. Developer-konto måste ha åtkomst till nyckelvalvet. 
* Autentisering från Azure där du vill använda explicita autentiseringsuppgifter (t.ex. för scenarier med flera klienter) och vill behålla tjänstens huvudnamn autentiseringsuppgifter på ett säkert sätt i ett nyckelvalv. Hanterad identitet måste ha åtkomst till nyckelvalvet. 

Den hanterade identitet eller developer-identitet måste ha behörighet att hämta klientcertifikatet från Key Vault. AppAuthentication biblioteket använder hämtade certifikatet som autentiseringsuppgifter för tjänstens huvudnamn-klienten.

Att använda ett klientcertifikat för autentisering av tjänstens huvudnamn

1. Skapa en tjänstobjektets certifikat och lagra den automatiskt i din keyvault med Azure CLI [az ad sp create-for-rbac--keyvault <keyvaultname> --cert <certificatename> --skapa-cert--skip-tilldelning](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) kommando:

    ```azurecli
    az ad sp create-for-rbac --keyvault <keyvaultname> --cert <certificatename> --create-cert --skip-assignment
    ```
    
    Certifikat-ID ska vara en URL i formatet `https://<keyvaultname>.vault.azure.net/secrets/<certificatename>`

1. Ersätt `{KeyVaultCertificateSecretIdentifier}` i den här anslutningssträngen med certifikat-ID:

```
RunAs=App;AppId={TestAppId};KeyVaultCertificateSecretIdentifier={KeyVaultCertificateSecretIdentifier}
```

Om exempelvis ditt nyckelvalv anropades ”Mittkeyvault” och du har skapat ett certifikat som kallas ”myCert”, certifikat-ID blir `https://myKeyVault.vault.azure.net/secrets/myCert`, och anslutningssträngen skulle vara `RunAs=App;AppId={TestAppId};TenantId={TenantId};KeyVaultCertificateSecretIdentifier=https://myKeyVault.vault.azure.net/secrets/myCert`.

## <a name="connection-string-support"></a>Stödet för sträng

Som standard `AzureServiceTokenProvider` använder flera metoder för att hämta en token. 

Du kontrollerar processen genom att använda en anslutningssträng som skickas till den `AzureServiceTokenProvider` konstruktor eller har angetts i den *AzureServicesAuthConnectionString* miljövariabeln. 

Följande alternativ stöds:

| Alternativ för anslutningssträngen | Scenario | Kommentar|
|:--------------------------------|:------------------------|:----------------------------|
| `RunAs=Developer; DeveloperTool=AzureCli` | Lokal utveckling | AzureServiceTokenProvider använder AzureCli för att hämta token. |
| `RunAs=Developer; DeveloperTool=VisualStudio` | Lokal utveckling | AzureServiceTokenProvider använder Visual Studio för att hämta token. |
| `RunAs=CurrentUser` | Lokal utveckling | AzureServiceTokenProvider använder Azure AD-integrerad autentisering för att hämta token. |
| `RunAs=App` | [Hanterade identiteter för Azure-resurser](../active-directory/managed-identities-azure-resources/index.yml) | AzureServiceTokenProvider använder en hanterad identitet för att hämta token. |
| `RunAs=App;AppId={ClientId of user-assigned identity}` | [Användartilldelad identitet för Azure-resurser](../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work) | AzureServiceTokenProvider använder en Användartilldelad identitet för att hämta token. |
| `RunAs=App;AppId={TestAppId};KeyVaultCertificateSecretIdentifier={KeyVaultCertificateSecretIdentifier}` | Autentisering av anpassade tjänster | KeyVaultCertificateSecretIdentifier = certifikatets hemlig identifierare. |
| `RunAs=App;AppId={AppId};TenantId={TenantId};CertificateThumbprint={Thumbprint};CertificateStoreLocation={LocalMachine or CurrentUser}`| Tjänstens huvudnamn | `AzureServiceTokenProvider` använder certifikat för att hämta åtkomsttoken från Azure AD. |
| `RunAs=App;AppId={AppId};TenantId={TenantId};CertificateSubjectName={Subject};CertificateStoreLocation={LocalMachine or CurrentUser}` | Tjänstens huvudnamn | `AzureServiceTokenProvider` använder certifikat för att hämta åtkomsttoken från Azure AD|
| `RunAs=App;AppId={AppId};TenantId={TenantId};AppKey={ClientSecret}` | Tjänstens huvudnamn |`AzureServiceTokenProvider` använder hemlighet för att hämta åtkomsttoken från Azure AD. |

## <a name="samples"></a>Exempel

Se den `Microsoft.Azure.Services.AppAuthentication` bibliotek i åtgärden, finns i följande kodexempel.

1. [Använda en hanterad identitet för att hämta en hemlighet från Azure Key Vault vid körning](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet)

2. [Distribuera en Azure Resource Manager-mall från en Azure virtuell dator med en hanterad identitet programmässigt](https://github.com/Azure-Samples/windowsvm-msi-arm-dotnet).

3. [Använda .NET Core-exemplet och en hanterad identitet för att anropa Azure-tjänster från en virtuell Linux-dator](https://github.com/Azure-Samples/linuxvm-msi-keyvault-arm-dotnet/).

## <a name="next-steps"></a>Nästa steg

- Läs mer om [hanterade identiteter för Azure-resurser](../active-directory/managed-identities-azure-resources/index.yml).
- Läs mer om [autentiseringsscenarier för Azure AD](../active-directory/develop/active-directory-authentication-scenarios.md).
