---
title: Distribuera en virtuell dator från Azure Marketplace
description: Beskriver hur du distribuerar en virtuell dator från en förkonfigurerad virtuell dator i Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: article
ms.date: 11/29/2018
ms.author: pabutler
ms.openlocfilehash: 482840f5e611bc2d8092add2822a0ed5b2c8f883
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64938704"
---
# <a name="deploy-a-virtual-machine-from-the-azure-marketplace"></a>Distribuera en virtuell dator från Azure Marketplace

Den här artikeln förklarar hur du distribuerar en förkonfigurerad virtuell-dator (VM) från Azure Marketplace, med det medföljande Azure PowerShell-skriptet.  Det här skriptet visar också WinRM HTTP och HTTPS-slutpunkter på den virtuella datorn.  Skriptet kräver att du redan har ett certifikat som överförs till Azure Key Vault, enligt beskrivningen i [skapa certifikat för Azure Key Vault](./cpp-create-key-vault-cert.md). 

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="vm-deployment-template"></a>Mall för distribution av virtuell dator

Distributionsmall för Snabbstart för Azure VM är tillgänglig som filen online [azuredeploy.json](https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-vm-winrm-keyvault-windows/azuredeploy.json).  Det innehåller följande parametrar:

|  **Parametern**        |   **Beskrivning**                                 |
|  -------------        |   ---------------                                 |
| newStorageAccountName | Namnet på lagringskontot                       |
| dnsNameForPublicIP    | DNS-namnet för den offentliga IP-Adressen. Måste vara gemener.    |
| adminUserName         | Administratörens användarnamn                          |
| adminPassword         | Administratörslösenordet                          |
| imagePublisher        | Avbildningens utgivare                                   |
| imageOffer            | Erbjudande                                       |
| imageSKU              | Bild-SKU                                         |
| vmSize                | Storleken på den virtuella datorn                                    |
| vmName                | Namnet på den virtuella datorn                                    |
| vaultName             | Namnet på nyckelvalvet                             |
| vaultResourceGroup    | Resursgrupp för nyckelvalvet                   |
| certificateUrl        | URL: en för certifikatet med version i KeyVault, till exempel  `https://testault.vault.azure.net/secrets/testcert/b621es1db241e56a72d037479xab1r7` |
|  |  |


## <a name="deployment-script"></a>Distributionsskript

Redigera följande Azure PowerShell-skript och köra den för att distribuera den angivna Azure Marketplace-datorn.

```powershell

New-AzResourceGroupDeployment -Name "dplvm$postfix" -ResourceGroupName "$rgName" -TemplateUri "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-vm-winrm-keyvault-windows/azuredeploy.json" -newStorageAccountName "test$postfix" -dnsNameForPublicIP $vmName -adminUserName "isv" -adminPassword $pwd -vmSize "Standard_A2" -vmName $vmName -vaultName "$kvname" -vaultResourceGroup "$rgName" -certificateUrl $objAzureKeyVaultSecret.Id 

```


## <a name="next-steps"></a>Nästa steg

När du har distribuerat en förkonfigurerad virtuell dator kan du konfigurera och komma åt de lösningar och tjänster som den innehåller eller använder den för ytterligare utveckling. 
