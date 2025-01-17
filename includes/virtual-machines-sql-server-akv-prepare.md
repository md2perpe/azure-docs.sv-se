---
title: ta med fil
description: ta med fil
services: virtual-machines-windows
author: rothja
manager: craigg
tags: azure-service-management
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 04/30/2018
ms.author: jroth
ms.custom: include file
ms.openlocfilehash: 1e96431af575533566786341750e17e7a7f446e0
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67076027"
---
## <a name="prepare-for-akv-integration"></a>Förbered AKV-integreringen
Om du vill använda Azure Key Vault-integrering för att konfigurera SQL Server-dator, finns det flera förutsättningar: 

1. [Installera Azure Powershell](#install)
2. [Skapa en Azure Active Directory](#register)
3. [Skapa ett nyckelvalv](#createkeyvault)

I följande avsnitt beskrivs dessa krav och den information du behöver samla in för att senare Kör PowerShell-cmdletar.

[!INCLUDE [updated-for-az](./updated-for-az.md)]

### <a id="install"></a> Installera Azure PowerShell
Kontrollera att du har installerat den senaste Azure PowerShell-modulen. Mer information finns i [Installera och konfigurera Azure PowerShell](/powershell/azure/install-az-ps).

### <a id="register"></a> Registrera ett program i Azure Active Directory

Först behöver du ha en [Azure Active Directory](https://azure.microsoft.com/trial/get-started-active-directory/) (AAD) i din prenumeration. Bland många fördelar kan du ge behörighet till ditt nyckelvalv för vissa användare och program.

Därefter registrera ett program med AAD. Detta ger dig ett huvudkonto för tjänsten som har åtkomst till ditt nyckelvalv, som måste den virtuella datorn. I Azure Key Vault-artikeln hittar du de här stegen i den [registrera ett program med Azure Active Directory](../articles/key-vault/key-vault-manage-with-cli2.md#registering-an-application-with-azure-active-directory) avsnittet eller om du kan se steg med skärmbilder i den **hämta en identitet för programavsnittet**  av [det här blogginlägget](http://blogs.technet.com/b/kv/archive/2015/01/09/azure-key-vault-step-by-step.aspx). Innan du slutför stegen som du behöver samla in följande information under denna registrering som behövs senare när du aktiverar Azure Key Vault-integrering på din SQL-VM.

* När programmet har lagts till, hitta den **program-ID** på den **registrerad app** bladet.
    Program-ID tilldelas senare till den **$spName** (Service Principal name) parameter i PowerShell-skript för att aktivera Azure Key Vault-integrering.

   ![Program-ID:t](./media/virtual-machines-sql-server-akv-prepare/aad-application-id.png)

* Under de här stegen när du skapar din nyckel att kopiera hemligheten för din nyckel som du ser i följande skärmbild. Den här nyckeln hemlig tilldelas senare till den **$spSecret** (Service Principal hemlighet) parameter i PowerShell-skriptet.

   ![AAD-hemlighet](./media/virtual-machines-sql-server-akv-prepare/aad-sp-secret.png)

* Program-ID och hemligheten också används för att skapa en autentiseringsuppgift i SQL Server.

* Du måste auktorisera den här nya klient-ID som har följande åtkomstbehörighet: **hämta**, **wrapKey**, **unwrapKey**. Detta görs med de [Set-AzKeyVaultAccessPolicy](https://docs.microsoft.com/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) cmdlet. Mer information finns i [översikt över Azure Key Vault](../articles/key-vault/key-vault-overview.md).

### <a id="createkeyvault"></a> Skapa ett nyckelvalv
Du behöver åtkomst till ett nyckelvalv för att kunna använda Azure Key Vault kan du lagra nycklar som du ska använda för kryptering i den virtuella datorn. Om du inte redan har konfigurerat ditt nyckelvalv kan du skapa en genom att följa stegen i den [komma igång med Azure Key Vault](../articles/key-vault/key-vault-overview.md) artikeln. Innan du slutför stegen nedan finns information du behöver samla in under den här uppsättningen upp som behövs senare när du aktiverar Azure Key Vault-integrering på din SQL-VM.

    New-AzKeyVault -VaultName 'ContosoKeyVault' -ResourceGroupName 'ContosoResourceGroup' -Location 'East Asia'

När du kommer till att skapa ett nyckelvalv steg, notera den returnerade **vaultUri** egenskapen, som är key vault-URL. I det här exemplet i det steg som visas nedan, namn på key vault är ContosoKeyVault, nyckelvalvet URL skulle därför https://contosokeyvault.vault.azure.net/.

Key vault-Webbadress tilldelas senare till den **$akvURL** parameter i PowerShell-skript för att aktivera Azure Key Vault-integrering.

När nyckelvalvet har skapats, måste vi lägga till en nyckel till nyckelvalvet, som den här nyckeln anges när vi skapar skapa en asymmetrisk nyckel senare i SQL Server.
