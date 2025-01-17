---
title: Kör din första fråga med Azure PowerShell
description: Den här artikeln vägleder dig igenom stegen för att aktivera Resource Graph-modulen för Azure PowerShell och köra din första fråga.
author: DCtheGeek
ms.author: dacoulte
ms.date: 01/23/2019
ms.topic: quickstart
ms.service: resource-graph
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: 76771a7dfcc323cca6ea52366195c895ee510701
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/17/2019
ms.locfileid: "67165603"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-azure-powershell"></a>Snabbstart: Köra din första Resource Graph-fråga med hjälp av Azure PowerShell

Det första steget till att använda Azure Resource Graph är att kontrollera att modulen för Azure PowerShell är installerad. Denna snabbstart vägleder dig genom processen för att lägga till modulen i Azure PowerShell-installationen.

I slutet av den här processen kommer du att ha lagt till modulen till valfri Azure PowerShell-installation och kört din första Resource Graph-fråga.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="add-the-resource-graph-module"></a>Lägga till Resource Graph-modulen

Om du vill aktivera Azure PowerShell för att skicka frågor till Azure Resource Graph, måste du lägga till modulen. Den här modulen kan användas med lokalt installerat PowerShell, med [Azure Cloud Shell](https://shell.azure.com) eller med [Azure PowerShell Docker-avbildningen](https://hub.docker.com/r/azuresdk/azure-powershell/).

### <a name="base-requirements"></a>Grundläggande krav

Azure Resource Graph-modulen måste ha följande programvara:

- Azure PowerShell 1.0.0 eller senare. Om den ännu inte är installerad följer du [de här instruktionerna](/powershell/azure/install-az-ps).

- PowerShellGet 2.0.1 eller högre. Om den inte är installerad eller uppdaterad följer du [de här instruktionerna](/powershell/gallery/installing-psget).

### <a name="install-the-module"></a>Installera modulen

Resource Graph-modulen för PowerShell är **Az.ResourceGraph**.

1. Från en **administrativ** PowerShell-prompt kör du följande kommando:

   ```azurepowershell-interactive
   # Install the Resource Graph module from PowerShell Gallery
   Install-Module -Name Az.ResourceGraph
   ```

1. Kontrollera att modulen har importerats och har rätt version (0.7.1):

   ```azurepowershell-interactive
   # Get a list of commands for the imported Az.ResourceGraph module
   Get-Command -Module 'Az.ResourceGraph' -CommandType 'Cmdlet'
   ```

## <a name="run-your-first-resource-graph-query"></a>Köra din första Resource Graph-fråga

Nu när Azure PowerShell-modulen har lagts till i din valda miljö är det dags att testa en enkel Resource Graph-fråga. Frågan returnerar de första fem Azure-resurserna med **namn** och **resurstyp** för varje resurs.

1. Kör din första Azure Resource Graph-fråga med hjälp av cmdlet:et `Search-AzGraph`:

   ```azurepowershell-interactive
   # Login first with Connect-AzAccount if not using Cloud Shell

   # Run Azure Resource Graph query
   Search-AzGraph -Query 'project name, type | limit 5'
   ```

   > [!NOTE]
   > Då det här frågeexemplet inte tillhandahåller någon sorteringsmodifierare som `order by`, kommer flera körningar av frågan troligen att resultera i olika uppsättningar resurser per begäran.

1. Uppdatera frågan till `order by` egenskapen **namn**:

   ```azurepowershell-interactive
   # Run Azure Resource Graph query with 'order by'
   Search-AzGraph -Query 'project name, type | limit 5 | order by name asc'
   ```

   > [!NOTE]
   > Om du kör den här frågan flera kommer den, precis som den första frågan, sannolikt att resultera i olika resurser vid varje begäran. Ordningen på frågekommandona är viktig. I det här exemplet kommer `order by` efter `limit`. Det begränsar först frågeresultaten och sorterar sedan dem.

1. Uppdatera frågan till att först `order by` **Namn**-egenskapen och sedan sätta en `limit` för de fem främsta resultaten:

   ```azurepowershell-interactive
   # Run Azure Resource Graph query with `order by` first, then with `limit`
   Search-AzGraph -Query 'project name, type | order by name asc | limit 5'
   ```

När den sista frågan har körts flera gånger, och förutsatt att ingenting i din miljö ändras, kommer resultaten som returneras bli konsekventa och som förväntade – sorterade efter **Namn**-egenskapen men fortfarande begränsade till de fem främsta resultaten.

> [!NOTE]
> Om frågan inte returnerar resultat från en prenumeration du redan har åtkomst till, Observera att `Search-AzGraph` cmdlet standard till prenumerationer i standardkontexten. För att se en lista över prenumeration-ID: n som är en del av standardkontexten som kör det här `(Get-AzContext).Account.ExtendedProperties.Subscriptions` om du vill söka i alla prenumerationer som du har åtkomst till kan ange en PSDefaultParameterValues för `Search-AzGraph` cmdlet genom att köra `$PSDefaultParameterValues=@{"Search-AzGraph:Subscription"= $(Get-AzSubscription).ID }`
   
## <a name="clean-up-resources"></a>Rensa resurser

Om du vill ta bort Resource Graph-modulen från din Azure PowerShell-miljö, kan du göra det med hjälp av följande kommando:

```azurepowershell-interactive
# Remove the Resource Graph module from the Azure PowerShell environment
Remove-Module -Name 'Az.ResourceGraph'
```

> [!NOTE]
> Detta tar inte bort modulfilen som laddades ned tidigare. Det tar bara bort den från den körda PowerShell-sessionen.

## <a name="next-steps"></a>Nästa steg

- Få mer information om [frågespråket](./concepts/query-language.md)
- Lär dig att [utforska resurser](./concepts/explore-resources.md)
- Kör din första fråga med [Azure CLI](first-query-azurecli.md)
- Se exempel på [startfrågor](./samples/starter.md)
- Se exempel på [avancerade frågor](./samples/advanced.md)
- Ge feedback på [UserVoice](https://feedback.azure.com/forums/915958-azure-governance)
