---
title: Skriptexempel för Azure PowerShell – Skapa en testmiljö för Azure Firewall
description: Skriptexempel för Azure PowerShell – Skapa en testmiljö för Azure Firewall.
services: virtual-network
author: vhorne
ms.service: firewall
ms.devlang: powershell
ms.topic: sample
ms.date: 8/13/2018
ms.author: victorh
ms.openlocfilehash: 6e85bd6ec51cff27fed6d0b2d9e73f94325e4d4f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60194192"
---
# <a name="create-an-azure-firewall-test-environment"></a>Skapa en testmiljö för Azure Firewall

Det här Skriptexemplet skapar en brandvägg och en testmiljö för nätverket. Nätverket innehåller ett virtuellt nätverk med tre undernät: *AzureFirewallSubnet*, *ServersSubnet* och *JumpboxSubnet*. ServersSubnet och JumpboxSubnet har var sin Windows Server med två kärnor.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Brandväggen ligger i AzureFirewallSubnet och är konfigurerad med en programregelsamling som har en enda regel för att tillåta åtkomst till www.microsoft.com.

Dessutom skapas en användardefinierad väg som skickar nätverkstrafik från ServersSubnet via brandväggen där brandväggsreglerna tillämpas.

Du kan köra skriptet från Azure [Cloud Shell](https://shell.azure.com/powershell) eller från en lokal PowerShell-installation. 

Om du kör PowerShell lokalt kräver det här skriptet Azure PowerShell. Kör `Get-Module -ListAvailable Az` för att hitta den installerade versionen. 

Du kan använda `PowerShellGet` om du behöver uppgradera, och det är inbyggt i Windows 10 och Windows Server 2016.

> [!NOTE]
>I andra Windows-versionen måste du installera `PowerShellGet` innan du kan använda det. Du kan köra `Get-Module -Name PowerShellGet -ListAvailable | Select-Object -Property Name,Version,Path` för att avgöra om det är installerat på datorn. Om du inte ser några utdata måste du installera den senaste versionen av [Windows Management-ramverket](https://www.microsoft.com/download/details.aspx?id=54616).

Mer information finns i [installera Azure PowerShell](/powershell/azure/install-Az-ps)

Befintliga Azure PowerShell-installationer som gjorts med installationsprogrammet för Web Platform kommer att så i konflikt med installationen av PowerShellGet och måste tas bort.

Om du kör PowerShell lokalt måste du även komma ihåg att köra `Connect-AzAccount` för att skapa en anslutning till Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exempelskript


[!code-azurepowershell-interactive[main](../../../powershell_scripts/firewall/create-fw-test.ps1  "Create a firewall test environment")]

## <a name="clean-up-deployment"></a>Rensa distribution 

Kör följande kommando för att ta bort resursgruppen, den virtuella datorn och alla relaterade resurser:

```powershell
Remove-AzResourceGroup -Name AzfwSampleScriptEastUS -Force
```

## <a name="script-explanation"></a>Förklaring av skript

I det här skriptet används följande kommandon för att skapa en resursgrupp, ett virtuellt nätverk och nätverkssäkerhetsgrupper. Varje kommando i följande tabell länkar till kommandospecifik dokumentation:

| Kommando | Anteckningar |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Skapar en resursgrupp där alla resurser lagras. |
| [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) | Skapar ett konfigurationsobjekt för undernät. |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Skapar ett virtuellt Azure-nätverk och klientdelsundernät. |
| [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) | Skapar säkerhetsregler som ska tilldelas till en nätverkssäkerhetsgrupp. |
| [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) |Skapar NSG-regler som tillåter eller blockerar specifika portar till specifika undernät. |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) | Associerar NSG:er med undernät. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Skapar en offentlig IP-adress för åtkomst till den virtuella datorn från Internet. |
| [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) | Skapar virtuella nätverksgränssnitt och kopplar dem till det virtuella nätverkets klient- och serverdelsundernät. |
| [New-AzVMConfig](/powershell/module/az.compute/new-azvmconfig) | Skapar en virtuell datorkonfiguration. Den här konfigurationen omfattar information som virtuellt datornamn, operativsystem och administrativa autentiseringsuppgifter. Konfigurationen används vid skapande av virtuell dator. |
| [New-AzVM](/powershell/module/az.compute/new-azvm) | Skapa en virtuell dator. |
|[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Tar bort en resursgrupp och alla resurser som ingår i gruppen. |
|[New-AzFirewall](https://docs.microsoft.com/powershell/module/az.network/new-azfirewall)| Skapar en ny Azure Firewall-brandvägg.|
|[Get-AzFirewall](https://docs.microsoft.com/powershell/module/az.network/get-azfirewall)|Hämtar ett Azure Firewall-objekt.|
|[New-AzFirewallApplicationRule](https://docs.microsoft.com/powershell/module/az.network/new-azfirewallapplicationrule)|Skapar en ny Azure Firewall-programregel.|
|[Set-AzFirewall](https://docs.microsoft.com/powershell/module/az.network/set-azfirewall)|Sparar ändringarna i Azure Firewall-objektet.|

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell finns i [Azure PowerShell-dokumentationen](/powershell/azure/overview).

