---
title: Uppdatera Azure-moduler i Azure Automation
description: Den här artikeln beskrivs hur du kan nu uppdatera vanliga Azure PowerShell-moduler som tillhandahålls som standard i Azure Automation.
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 06/14/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: a42fae4e7ff9ba9edc29c64480983987e41cf9c1
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2019
ms.locfileid: "67476788"
---
# <a name="how-to-update-azure-powershell-modules-in-azure-automation"></a>Så här uppdaterar du Azure PowerShell-moduler i Azure Automation

Uppdatera Azure moduler i ditt Automation-konto måste du använda den [uppdatera Azure-moduler runbook](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update), som är tillgängligt som öppen källkod. Att börja använda den **uppdatering AutomationAzureModulesForAccount** runbook för att uppdatera din Azure-moduler, ladda ned det från den [uppdatera Azure-moduler runbook databasen](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) på GitHub. Du kan sedan importera den till ditt Automation-konto eller köra den som ett skript. Läs hur du importerar en runbook i ditt Automation-konto i [importerar en runbook](manage-runbooks.md#import-a-runbook).

De vanligaste AzureRM PowerShell-modulerna som tillhandahålls som standard i varje Automation-konto. Azure-teamet uppdaterar Azure-moduler regelbundet, därför för att hålla dig uppdaterad du vill använda den [uppdatering AutomationAzureModulesForAccount](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) runbook för att uppdatera moduler i ditt Automation-konton.

Eftersom modulerna uppdateras regelbundet av produktgruppen och ändringar som kan uppstå med cmdletarna som ingår. Den här åtgärden kan negativt påverka dina runbooks beroende på vilken typ av ändring, till exempel byta namn på en parameter eller avvecklar en cmdlet helt och hållet.

För att undvika att påverka dina runbooks och de processer som de automatisera, testa och verifiera innan du fortsätter. Om du inte har ett särskilt Automation-konto som är avsedd för detta ändamål kan du skapa en så att du kan testa många olika scenarier under utvecklingen av dina runbooks. Detta test bör innehålla iterativ ändringar, till exempel uppdaterar PowerShell-moduler.

Om du utvecklar dina skript lokalt, rekommenderar vi för att ha samma modulversioner lokalt som du har i ditt Automation-konto när testning för att se till att du får samma resultat. När resultaten verifieras och du har använt alla ändringar som krävs, kan du flytta ändringarna till produktion.

> [!NOTE]
> Ett nytt Automation-konto kan inte innehålla de senaste modulerna.

## <a name="considerations"></a>Överväganden

Här följer några saker att beakta när du använder den här processen för att uppdatera din Azure-moduler:

* Denna runbook har stöd för uppdatering av den **Azure** och **AzureRm** moduler som standard. Denna runbook har stöd för uppdatering av den **Az** samt moduler. Granska den [uppdatera Azure-moduler runbook README](https://github.com/microsoft/AzureAutomation-Account-Modules-Update/blob/master/README.md) för mer information om hur du uppdaterar `Az` moduler med denna runbook. Det finns ytterligare viktiga faktorer som du behöver ta hänsyn till när du använder den `Az` moduler i ditt Automation-konto, mer information, se [med hjälp av Az-moduler i ditt Automation-konto](az-modules.md).

* Innan du startar den här runbooken ska du kontrollera ditt Automation-konto har en [Azure kör som-kontouppgiften](manage-runas-account.md) skapas.

* Du kan använda den här koden som en vanlig PowerShell-skript i stället för en runbook: bara logga in på Azure med hjälp av den [Connect-AzureRmAccount](/powershell/module/azurerm.profile/connect-azurermaccount) kommandot först och sedan skicka `-Login $false` till skriptet.

* Om du vill använda denna runbook på suveräna moln, använder de `AzureRmEnvironment` parameter för att skicka rätt miljö till runbooken.  Godkända värden är **AzureCloud**, **AzureChinaCloud**, **AzureGermanCloud**, och **azureusgovernment eller**. Dessa värden kan hämtas från att använda `Get-AzureRmEnvironment | select Name`. Om du inte skickar ett värde för den här parametern, runbooken som standard i Azures offentliga moln **AzureCloud**

* Om du vill använda en specifik version av Azure PowerShell-modulen i stället för den senaste tillgängliga på PowerShell-galleriet, skicka dessa versioner till den valfria `ModuleVersionOverrides` -parametern för den **Update-AutomationAzureModulesForAccount**runbook. Exempel finns i den [uppdatering AutomationAzureModulesForAccount.ps1](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update/blob/master/Update-AutomationAzureModulesForAccount.ps1
) runbook. Azure PowerShell-moduler som inte nämns i den `ModuleVersionOverrides` parametern uppdateras med de senaste modulversionerna på PowerShell-galleriet. Om du skickar något till den `ModuleVersionOverrides` parameter, alla moduler som uppdateras med de senaste modulversionerna på PowerShell-galleriet. Det här beteendet är samma som den **uppdatera Azure-moduler** knappen.

## <a name="known-issues"></a>Kända problem

Det finns ett känt problem med uppdatering AzureRM-moduler i ett Automation-konto som tillhör en resursgrupp med det numeriska namn som börjar med 0. Om du vill uppdatera din Azure-moduler i ditt Automation-konto, måste den vara i en resursgrupp som har ett alfanumeriskt namn. Resursgrupper med numeriska namn som börjar med 0 kan inte uppdatera AzureRM-moduler just nu.

## <a name="next-steps"></a>Nästa steg

Gå till öppen källkod [uppdatera Azure-moduler runbook](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) mer information om den.
