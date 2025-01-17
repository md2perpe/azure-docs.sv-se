---
title: 'PowerShell och CLI: Aktivera SQL TDE - med Azure Key Vault – ta med din egen nyckel – Azure SQL Database | Microsoft Docs'
description: Lär dig hur du konfigurerar en Azure SQL Database och Data Warehouse för att börja använda Transparent datakryptering (TDE) för kryptering i vila med PowerShell eller CLI.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aliceku
ms.author: aliceku
ms.reviewer: vanto
manager: craigg
ms.date: 03/12/2019
ms.openlocfilehash: c42c6175512105de38a29be260c370851e152137
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60330881"
---
# <a name="powershell-and-cli-enable-transparent-data-encryption-with-customer-managed-key-from-azure-key-vault"></a>PowerShell och CLI: Aktivera Transparent datakryptering med kundhanterad nyckel från Azure Key Vault

Den här artikeln beskriver hur du använder en nyckel från Azure Key Vault för Transparent datakryptering (TDE) på en SQL-databas eller datalager. Mer information om TDE med Azure Key Vault-integrering – ta med din egen nyckel (BYOK) Support på [TDE med Kundhanterade nycklar i Azure Key Vault](transparent-data-encryption-byok-azure-sql.md). 

## <a name="prerequisites-for-powershell"></a>Krav för PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Modulen PowerShell Azure Resource Manager är fortfarande stöds av Azure SQL Database, men alla framtida utveckling är för modulen Az.Sql. Dessa cmdlets finns i [i AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenten för kommandon i modulen Az och AzureRm-moduler är avsevärt identiska.

- Du måste ha en Azure-prenumeration och vara administratör på den aktuella prenumerationen.
- (Rekommenderas men valfria) Ha en maskinvarusäkerhetsmodul (HSM) eller lokal nyckel lagra för att skapa en lokal kopia av nyckelmaterial TDE-skydd.
- Du måste ha Azure PowerShell installerad och igång. 
- Skapa en Azure Key Vault och en nyckel som ska användas för TDE.
  - [PowerShell-instruktioner från Key Vault](../key-vault/key-vault-overview.md)
  - [Anvisningar för att använda en maskinvarusäkerhetsmodul (HSM) och Key Vault](../key-vault/key-vault-hsm-protected-keys.md)
    - Nyckelvalvet måste ha följande egenskap som ska användas för transparent Datakryptering:
  - [soft-delete](../key-vault/key-vault-ovw-soft-delete.md)
  - [Så här använder du Key Vault mjuk borttagning med PowerShell](../key-vault/key-vault-soft-delete-powershell.md) 
- Nyckeln måste ha följande attribut som ska användas för transparent Datakryptering:
   - Inga upphör att gälla
   - Inte inaktiverat
   - Kan utföra *hämta*, *omsluta nyckel*, *nyckelomslutning* åtgärder

## <a name="step-1-assign-an-azure-ad-identity-to-your-server"></a>Steg 1. Tilldela en Azure AD-identitet till din server 

Om du har en befintlig server kan du använda följande för att lägga till en Azure AD-identitet till din server:

   ```powershell
   $server = Set-AzSqlServer `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -AssignIdentity
   ```

Om du skapar en server, använder du den [New AzSqlServer](/powershell/module/az.sql/new-azsqlserver) cmdlet med taggen-identitet för att lägga till en Azure AD-identitet när servern skapas:

   ```powershell
   $server = New-AzSqlServer `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -Location <RegionName> `
   -ServerName <LogicalServerName> `
   -ServerVersion "12.0" `
   -SqlAdministratorCredentials <PSCredential> `
   -AssignIdentity 
   ```

## <a name="step-2-grant-key-vault-permissions-to-your-server"></a>Steg 2. Bevilja behörigheter för Key Vault till servern

Använd den [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) cmdlet för att ge din serveråtkomst till nyckeln valvet innan du använder en nyckel från den för transparent Datakryptering.

   ```powershell
   Set-AzKeyVaultAccessPolicy  `
   -VaultName <KeyVaultName> `
   -ObjectId $server.Identity.PrincipalId `
   -PermissionsToKeys get, wrapKey, unwrapKey
   ```

## <a name="step-3-add-the-key-vault-key-to-the-server-and-set-the-tde-protector"></a>Steg 3. Lägg till Key Vault-nyckeln till servern och ange TDE-skydd

- Använd den [Lägg till AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey) cmdlet för att lägga till nyckeln från Key Vault på servern.
- Använd den [Set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) cmdlet för att ange nyckeln som TDE-skydd för alla serverresurser.
- Använd den [Get-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector) cmdlet för att bekräfta att TDE-skyddet har konfigurerats rätt.

> [!Note]
> Den kombinerade längden för key vault-namn och namn får inte överskrida 94 tecken.
> 

>[!Tip]
>Ett exempel KeyId från Key Vault: https://contosokeyvault.vault.azure.net/keys/Key1/1a1a2b2b3c3c4d4d5e5e6f6f7g7g8h8h
>

   ```powershell
   <# Add the key from Key Vault to the server #>
   Add-AzSqlServerKeyVaultKey `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -KeyId <KeyVaultKeyId>

   <# Set the key as the TDE protector for all resources under the server #>
   Set-AzSqlServerTransparentDataEncryptionProtector `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -Type AzureKeyVault `
   -KeyId <KeyVaultKeyId> 

   <# To confirm that the TDE protector was configured as intended: #>
   Get-AzSqlServerTransparentDataEncryptionProtector `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> 
   ```

## <a name="step-4-turn-on-tde"></a>Steg 4. Aktivera transparent Datakryptering 

Använd den [Set-AzSqlDatabaseTransparentDataEncryption](/powershell/module/az.sql/set-azsqldatabasetransparentdataencryption) cmdlet för att aktivera TDE.

   ```powershell
   Set-AzSqlDatabaseTransparentDataEncryption `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -DatabaseName <DatabaseName> `
   -State "Enabled"
   ```

Databasen eller datalagret har nu TDE aktiverat med en krypteringsnyckel i Key Vault.

## <a name="step-5-check-the-encryption-state-and-encryption-activity"></a>Steg 5. Kontrollera krypteringsstatus och kryptering aktivitet

Använd den [Get-AzSqlDatabaseTransparentDataEncryption](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryption) att hämta krypteringsstatus och [Get-AzSqlDatabaseTransparentDataEncryptionActivity](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryptionactivity) att kontrollera förloppet för kryptering för en databas eller data warehouse.

   ```powershell
   # Get the encryption state
   Get-AzSqlDatabaseTransparentDataEncryption `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -DatabaseName <DatabaseName> `

   <# Check the encryption progress for a database or data warehouse #>
   Get-AzSqlDatabaseTransparentDataEncryptionActivity `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -DatabaseName <DatabaseName>  
   ```

## <a name="other-useful-powershell-cmdlets"></a>Andra användbara PowerShell-cmdletar

- Använd den [Set-AzSqlDatabaseTransparentDataEncryption](/powershell/module/az.sql/set-azsqldatabasetransparentdataencryption) cmdlet för att inaktivera TDE.

   ```powershell
   Set-AzSqlDatabaseTransparentDataEncryption `
   -ServerName <LogicalServerName> `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -DatabaseName <DatabaseName> `
   -State "Disabled”
   ```
 
- Använd den [Get-AzSqlServerKeyVaultKey](/powershell/module/az.sql/get-azsqlserverkeyvaultkey) cmdleten returnerade listan med Key Vault-nycklar som läggs till på servern.

   ```powershell
   <# KeyId is an optional parameter, to return a specific key version #>
   Get-AzSqlServerKeyVaultKey `
   -ServerName <LogicalServerName> `
   -ResourceGroupName <SQLDatabaseResourceGroupName>
   ```
 
- Använd den [Remove-AzSqlServerKeyVaultKey](/powershell/module/az.sql/remove-azsqlserverkeyvaultkey) att ta bort en Key Vault-nyckeln från servern.

   ```powershell
   <# The key set as the TDE Protector cannot be removed. #>
   Remove-AzSqlServerKeyVaultKey `
   -KeyId <KeyVaultKeyId> `
   -ServerName <LogicalServerName> `
   -ResourceGroupName <SQLDatabaseResourceGroupName>   
   ```
 
## <a name="troubleshooting"></a>Felsökning

Kontrollera följande om ett problem inträffar:
- Om det inte går att hitta nyckelvalvet, se till att du befinner dig i rätt prenumeration med hjälp av den [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription) cmdlet.

   ```powershell
   Get-AzSubscription `
   -SubscriptionId <SubscriptionId>
   ```

- Om den nya nyckeln inte kan läggas till servern eller den nya nyckeln kan inte uppdateras som TDE-skydd kan du kontrollera följande:
   - Nyckeln ska inte ha ett förfallodatum
   - Nyckeln måste ha den *hämta*, *omsluta nyckel*, och *nyckelomslutning* operations aktiverat.

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om att rotera TDE-skydd för en server för att uppfylla krav på säkerhet: [Rotera Transparent datakryptering skydd med hjälp av PowerShell](transparent-data-encryption-byok-azure-sql-key-rotation.md).
- Lär dig hur du tar bort ett potentiellt komprometterade TDE-skydd i händelse av en säkerhetsrisk: [Tar bort en potentiellt komprometterade nyckel](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md). 

## <a name="prerequisites-for-cli"></a>Krav för CLI

- Du måste ha en Azure-prenumeration och vara administratör på den aktuella prenumerationen.
- (Rekommenderas men valfria) Ha en maskinvarusäkerhetsmodul (HSM) eller lokal nyckel lagra för att skapa en lokal kopia av nyckelmaterial TDE-skydd.
- Kommandoradsgränssnitt 2.0 eller senare. Om du vill installera den senaste versionen och ansluter till din Azure-prenumeration, se [installerar och konfigurerar Azure plattformsoberoende kommandoradsgränssnitt 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli). 
- Skapa en Azure Key Vault och en nyckel som ska användas för TDE.
  - [Hantera Nyckelvalv med hjälp av CLI 2.0](../key-vault/key-vault-manage-with-cli2.md)
  - [Anvisningar för att använda en maskinvarusäkerhetsmodul (HSM) och Key Vault](../key-vault/key-vault-hsm-protected-keys.md)
    - Nyckelvalvet måste ha följande egenskap som ska användas för transparent Datakryptering:
  - [soft-delete](../key-vault/key-vault-ovw-soft-delete.md)
  - [Så här använder du Key Vault mjuk borttagning med CLI](../key-vault/key-vault-soft-delete-cli.md) 
- Nyckeln måste ha följande attribut som ska användas för transparent Datakryptering:
   - Inga upphör att gälla
   - Inte inaktiverat
   - Kan utföra *hämta*, *omsluta nyckel*, *nyckelomslutning* åtgärder
   
## <a name="step-1-create-a-server-with-an-azure-ad-identity"></a>Steg 1. Skapa en server med en Azure AD-identitet
      cli
      # create server (with identity) and database
      az sql server create --name <servername> --resource-group <rgname>  --location <location> --admin-user <user> --admin-password <password> --assign-identity
      az sql db create --name <dbname> --server <servername> --resource-group <rgname>  
 
 
>[!Tip]
>Förhindra ”principalID” skapar servern, är det objekt-id som används för att tilldela behörigheter för key vault i nästa steg
>
 
## <a name="step-2-grant-key-vault-permissions-to-the-logical-sql-server"></a>Steg 2. Bevilja behörigheter för Key Vault till den logiska sql-servern
      cli
      # create key vault, key and grant permission
       az keyvault create --name <kvname> --resource-group <rgname> --location <location> --enable-soft-delete true
       az keyvault key create --name <keyname> --vault-name <kvname> --protection software
       az keyvault set-policy --name <kvname>  --object-id <objectid> --resource-group <rgname> --key-permissions wrapKey unwrapKey get 


>[!Tip]
>Skydda viktiga URI eller keyID för den nya nyckeln för nästa steg, till exempel: https://contosokeyvault.vault.azure.net/keys/Key1/1a1a2b2b3c3c4d4d5e5e6f6f7g7g8h8h
>
 
       
## <a name="step-3-add-the-key-vault-key-to-the-server-and-set-the-tde-protector"></a>Steg 3. Lägg till Key Vault-nyckeln till servern och ange TDE-skydd
  
     cli
     # add server key and update encryption protector
     az sql server key create --server <servername> --resource-group <rgname> --kid <keyID>
     az sql server tde-key set --server <servername> --server-key-type AzureKeyVault  --resource-group <rgname> --kid <keyID>

        
  > [!Note]
> Den kombinerade längden för key vault-namn och namn får inte överskrida 94 tecken.
> 

  
## <a name="step-4-turn-on-tde"></a>Steg 4. Aktivera transparent Datakryptering 
      cli
      # enable encryption
      az sql db tde set --database <dbname> --server <servername> --resource-group <rgname> --status Enabled 
      

Databasen eller datalagret har nu TDE aktiverat med en kundhanterad krypteringsnyckel i Azure Key Vault.

## <a name="step-5-check-the-encryption-state-and-encryption-activity"></a>Steg 5. Kontrollera krypteringsstatus och kryptering aktivitet

     cli
      # get encryption scan progress
      az sql db tde list-activity --database <dbname> --server <servername> --resource-group <rgname>  

      # get whether encryption is on or off
      az sql db tde show --database <dbname> --server <servername> --resource-group <rgname> 

## <a name="sql-cli-references"></a>SQL-CLI-referenser

https://docs.microsoft.com/cli/azure/sql 

https://docs.microsoft.com/cli/azure/sql/server/key 

https://docs.microsoft.com/cli/azure/sql/server/tde-key 

https://docs.microsoft.com/cli/azure/sql/db/tde 

