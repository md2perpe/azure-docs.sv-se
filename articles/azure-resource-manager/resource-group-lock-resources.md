---
title: Låsa Azure-resurser ska kunna ändras | Microsoft Docs
description: Hindra användare från uppdatering eller radering viktiga Azure-resurser genom att använda ett lås för alla användare och roller.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 05/14/2019
ms.author: tomfitz
ms.openlocfilehash: 31d77b4ea6e7594cd3ed4dba264f9ea6db4ca290
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/17/2019
ms.locfileid: "67155211"
---
# <a name="lock-resources-to-prevent-unexpected-changes"></a>Låsresurser för att förhindra oväntade ändringar 

Som administratör kan behöva du låsa en prenumeration, resursgrupp eller resurs för att förhindra att andra användare i din organisation av misstag tar bort eller ändrar viktiga resurser. Du kan ange låsnivån till **CanNotDelete** eller **ReadOnly**. I portalen låsen kallas **ta bort** och **skrivskyddad** respektive.

* **CanNotDelete** innebär att behöriga användare kan fortfarande läsa och ändra en resurs, men de kan inte ta bort resursen. 
* **Skrivskyddad** innebär att behöriga användare kan läsa en resurs, men de kan inte ta bort eller uppdatera resursen. Tillämpa den här Lås liknar att begränsa alla behöriga användare till de behörigheter som beviljas genom den **läsare** roll. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="how-locks-are-applied"></a>Hur Lås tillämpas

När du använder ett lås på en överordnad omfattning, ärver alla resurser i detta omfång samma låset. Även resurser som du lägger till senare ärver låset från överordnat. Den mest restriktiva Lås i arvet företräde.

Till skillnad från rollbaserad åtkomstkontroll använder du hanteringslås för att tillämpa en begränsning för alla användare och roller. Läs om att ange behörigheter för användare och roller i [Azure rollbaserad åtkomstkontroll](../role-based-access-control/role-assignments-portal.md).

Resource Manager-Lås gäller endast för åtgärder som sker i Hanteringsplanet, som består av åtgärder som skickas till `https://management.azure.com`. Låsen begränsa inte hur resurser utföra egna funktioner. Resursändringar är begränsade men resursåtgärder är inte begränsade. Till exempel gör ett ReadOnly-lås på en SQL Database att du inte tar bort eller ändrar databasen. Den hindra inte dig från att skapa, uppdatera eller ta bort data i databasen. Datatransaktioner tillåts eftersom dessa åtgärder inte skickas till `https://management.azure.com`.

Tillämpa **ReadOnly** kan leda till oväntade resultat eftersom vissa åtgärder som inte verkar ändra resursen faktiskt kräver åtgärder som blockerats av låset. Den **ReadOnly** Lås kan användas till resursen eller resursgruppen som innehåller resursen. Några vanliga exempel på åtgärder som blockeras av en **ReadOnly** Lås är:

* En **ReadOnly** låset på ett lagringskonto som förhindrar att alla användare lista nycklarna. Listan med nycklar åtgärden hanteras via en POST-begäran eftersom de returnerade nycklarna är tillgängliga för skrivåtgärder.

* En **ReadOnly** lås på en App Service-resurs som förhindrar att Visual Studio Server Explorer visar filer för resursen eftersom den interaktionen kräver skrivbehörighet.

* En **ReadOnly** låset på en resursgrupp som innehåller en virtuell dator förhindrar alla användare från att starta eller starta om den virtuella datorn. Dessa åtgärder kräver en POST-begäran.

## <a name="who-can-create-or-delete-locks"></a>Vem som kan skapa eller ta bort lås
För att skapa eller ta bort hanteringslås, måste du ha åtkomst till `Microsoft.Authorization/*` eller `Microsoft.Authorization/locks/*` åtgärder. Av de inbyggda rollerna har endast **Ägare** och **Administratör för användaråtkomst** åtkomst till dessa åtgärder.

## <a name="managed-applications-and-locks"></a>Hanterade program och lås

Vissa Azure-tjänster, till exempel Azure Databricks, använda [hanterade program](../managed-applications/overview.md) du implementerar tjänsten. I så fall kan skapar tjänsten två resursgrupper. En resursgrupp som innehåller en översikt över tjänsten och inte är låst. Andra resursgruppen innehåller infrastrukturen för tjänsten och är låst.

Om du försöker ta bort resursgruppen infrastruktur, får du ett felmeddelande om att resursgruppen är låst. Om du försöker ta bort låset för resursgruppen infrastruktur, får du ett felmeddelande om att låset inte kan tas bort eftersom den ägs av ett systemprogram för.

Ta bort tjänsten, vilket även tar bort resursgruppen infrastruktur i stället.

Välj den tjänst som du har distribuerat för hanterade program.

![Välj tjänst](./media/resource-group-lock-resources/select-service.png)

Observera tjänsten innehåller en länk för en **hanterad resursgrupp**. Resursgruppen innehåller infrastrukturen och är låst. Det går inte att direkt ta bort.

![Visa grupp](./media/resource-group-lock-resources/show-managed-group.png)

Om du vill ta bort allt innehåll för tjänsten, inklusive den låsta infrastrukturresursgrupp Välj **ta bort** för tjänsten.

![Ta bort tjänsten](./media/resource-group-lock-resources/delete-service.png)

## <a name="portal"></a>Portalen
[!INCLUDE [resource-manager-lock-resources](../../includes/resource-manager-lock-resources.md)]

## <a name="template"></a>Mall

När du använder Resource Manager-mall för att distribuera ett lås kan använda du olika värden för namn och typ beroende på omfattningen av låset.

När du använder ett lås till en **resource**, Använd följande format:

* namn – `{resourceName}/Microsoft.Authorization/{lockName}`
* typ- `{resourceProviderNamespace}/{resourceType}/providers/locks`

När du använder ett lås till en **resursgrupp** eller **prenumeration**, Använd följande format:

* namn – `{lockName}`
* typ- `Microsoft.Authorization/locks`

I följande exempel visas en mall som skapar en app service-plan, en webbplats och ett lås på webbplatsen. Resurstypen för låset är resurstypen för resurs att låsa och **/providers/ Lås**. Namnet på låset har skapats genom att sammanfoga resursnamnet med **/Microsoft.Authorization/** och namnet på låset.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "hostingPlanName": {
            "type": "string"
        }
    },
    "variables": {
        "siteName": "[concat('ExampleSite', uniqueString(resourceGroup().id))]"
    },
    "resources": [
        {
            "apiVersion": "2016-09-01",
            "type": "Microsoft.Web/serverfarms",
            "name": "[parameters('hostingPlanName')]",
            "location": "[resourceGroup().location]",
            "sku": {
                "tier": "Free",
                "name": "f1",
                "capacity": 0
            },
            "properties": {
                "targetWorkerCount": 1
            }
        },
        {
            "apiVersion": "2016-08-01",
            "name": "[variables('siteName')]",
            "type": "Microsoft.Web/sites",
            "location": "[resourceGroup().location]",
            "dependsOn": [
                "[resourceId('Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
            ],
            "properties": {
                "serverFarmId": "[parameters('hostingPlanName')]"
            }
        },
        {
            "type": "Microsoft.Web/sites/providers/locks",
            "apiVersion": "2016-09-01",
            "name": "[concat(variables('siteName'), '/Microsoft.Authorization/siteLock')]",
            "dependsOn": [
                "[resourceId('Microsoft.Web/sites', variables('siteName'))]"
            ],
            "properties": {
                "level": "CanNotDelete",
                "notes": "Site should not be deleted."
            }
        }
    ]
}
```

Ett exempel på hur ett lås på en resursgrupp, se [skapa en resursgrupp och låsa det](https://github.com/Azure/azure-quickstart-templates/tree/master/subscription-level-deployments/create-rg-lock-role-assignment).

## <a name="powershell"></a>PowerShell
Du Lås distribuerade resurser med Azure PowerShell med hjälp av den [New AzResourceLock](/powershell/module/az.resources/new-azresourcelock) kommando.

Ange namnet på resursen och dess resurstyp dess resursgruppens namn om du vill låsa en resurs.

```azurepowershell-interactive
New-AzResourceLock -LockLevel CanNotDelete -LockName LockSite -ResourceName examplesite -ResourceType Microsoft.Web/sites -ResourceGroupName exampleresourcegroup
```

Om du vill låsa en resursgrupp, ange namnet på resursgruppen.

```azurepowershell-interactive
New-AzResourceLock -LockName LockGroup -LockLevel CanNotDelete -ResourceGroupName exampleresourcegroup
```

Hämta information om ett lås [Get-AzResourceLock](/powershell/module/az.resources/get-azresourcelock). Hämta alla Lås i din prenumeration med:

```azurepowershell-interactive
Get-AzResourceLock
```

Hämta alla Lås för en resurs med:

```azurepowershell-interactive
Get-AzResourceLock -ResourceName examplesite -ResourceType Microsoft.Web/sites -ResourceGroupName exampleresourcegroup
```

Hämta alla Lås för en resursgrupp med:

```azurepowershell-interactive
Get-AzResourceLock -ResourceGroupName exampleresourcegroup
```

Om du vill ta bort ett lås, använder du:

```azurepowershell-interactive
$lockId = (Get-AzResourceLock -ResourceGroupName exampleresourcegroup -ResourceName examplesite -ResourceType Microsoft.Web/sites).LockId
Remove-AzResourceLock -LockId $lockId
```

## <a name="azure-cli"></a>Azure CLI

Du Lås distribuerade resurser med Azure CLI med hjälp av den [az lock skapa](/cli/azure/lock#az-lock-create) kommando.

Ange namnet på resursen och dess resurstyp dess resursgruppens namn om du vill låsa en resurs.

```azurecli
az lock create --name LockSite --lock-type CanNotDelete --resource-group exampleresourcegroup --resource-name examplesite --resource-type Microsoft.Web/sites
```

Om du vill låsa en resursgrupp, ange namnet på resursgruppen.

```azurecli
az lock create --name LockGroup --lock-type CanNotDelete --resource-group exampleresourcegroup
```

Hämta information om ett lås [az lock list](/cli/azure/lock#az-lock-list). Hämta alla Lås i din prenumeration med:

```azurecli
az lock list
```

Hämta alla Lås för en resurs med:

```azurecli
az lock list --resource-group exampleresourcegroup --resource-name examplesite --namespace Microsoft.Web --resource-type sites --parent ""
```

Hämta alla Lås för en resursgrupp med:

```azurecli
az lock list --resource-group exampleresourcegroup
```

Om du vill ta bort ett lås, använder du:

```azurecli
lockid=$(az lock show --name LockSite --resource-group exampleresourcegroup --resource-type Microsoft.Web/sites --resource-name examplesite --output tsv --query id)
az lock delete --ids $lockid
```

## <a name="rest-api"></a>REST-API
Du kan låsa distribuerade resurser med den [REST API för hanteringslås](https://docs.microsoft.com/rest/api/resources/managementlocks). REST API kan du skapa och ta bort lås och hämta information om befintliga Lås.

Om du vill skapa ett lås, kör du:

    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/locks/{lock-name}?api-version={api-version}

Omfånget kan vara en prenumeration, resursgrupp eller resurs. Lås-namnet är vad du vill anropa låset. Api-versionen, använda **2016-09-01**.

På begäran, innehåller ett JSON-objekt som anger egenskaperna för låset.

    {
      "properties": {
        "level": "CanNotDelete",
        "notes": "Optional text notes."
      }
    } 

## <a name="next-steps"></a>Nästa steg
* Läs om hur du ordnar dina resurser i [med taggar för att organisera dina resurser](resource-group-using-tags.md)
* Du kan använda begränsningar och konventioner i din prenumeration med anpassade principer. Mer information finns i [Vad är Azure Policy?](../governance/policy/overview.md).
* Vägledning för hur företag kan använda resurshanteraren för att effektivt hantera prenumerationer finns i [Azure enterprise scaffold - förebyggande prenumerationsåtgärder](/azure/architecture/cloud-adoption-guide/subscription-governance).

