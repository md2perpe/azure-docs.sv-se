---
title: Hantera åtkomst till Azure-resurser med RBAC och REST-API – Azure | Microsoft Docs
description: Lär dig mer om att hantera åtkomst till Azure-resurser för användare, grupper och program med hjälp av rollbaserad åtkomstkontroll (RBAC) och REST API. Detta innefattar hur du listar åtkomst, ger åtkomst och tar bort åtkomst.
services: active-directory
documentationcenter: na
author: rolyon
manager: mtillman
editor: ''
ms.assetid: 1f90228a-7aac-4ea7-ad82-b57d222ab128
ms.service: role-based-access-control
ms.workload: multiple
ms.tgt_pltfrm: rest-api
ms.devlang: na
ms.topic: conceptual
ms.date: 05/28/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 3602e4ca83e828270ebef56c688670b896ca58a4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66472743"
---
# <a name="manage-access-to-azure-resources-using-rbac-and-the-rest-api"></a>Hantera åtkomst till Azure-resurser med RBAC och REST API

[Rollbaserad åtkomstkontroll (RBAC)](overview.md) är metoden som du använder när du hanterar åtkomst till Azure-resurser. Den här artikeln beskriver hur du hanterar åtkomst för användare, grupper och program med RBAC och REST API.

## <a name="list-access"></a>Visar åtkomst

I RBAC lista för att lista åtkomstförsök kommer du rolltilldelningar. Vill se rolltilldelningar kan du använda en av de [rolltilldelningar – lista](/rest/api/authorization/roleassignments/list) REST API: er. För att förfina dina resultat, anger du ett scope och eventuellt ett filter.

1. Börja med följande begäran:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments?api-version=2015-07-01&$filter={filter}
    ```

1. I URI: N, Ersätt *{omfång}* med den omfattning som du vill visa en lista över rolltilldelningar.

    | Scope | Typ |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Prenumeration |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Resursgrupp |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Resource |
    
       
     > [!NOTE]
     > I exemplet ovan Microsoft.web är resursprovidern används som refererar till App service-instansen. På samma sätt kan du använda någon annan leverantör av resursen och skapa Scope-URI. För att förstå mer finns [Azure resursproviders och resurstyper](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services) stöds [Azure RM åtgärder för resursprovider](https://docs.microsoft.com/azure/role-based-access-control/resource-provider-operations).  
     
1. Ersätt *{filter}* med villkor som du vill använda för att filtrera rolltilldelningslista.

    | Filter | Beskrivning |
    | --- | --- |
    | `$filter=atScope()` | Visar endast den angivna omfattningen får inte inklusive rolltilldelningar i subscopes-rolltilldelningar. |
    | `$filter=principalId%20eq%20'{objectId}'` | Visar rolltilldelningar för en angiven användare, grupp eller tjänstens huvudnamn. |
    | `$filter=assignedTo('{objectId}')` | Visar en lista över rolltilldelningar för en angiven användare eller tjänstens huvudnamn. Om användaren är medlem i en grupp som har en rolltilldelning, visas också den rolltilldelningen. Det här filtret är transitiva för grupper, vilket innebär att om användaren är medlem i en grupp och den är medlem i en annan grupp som har en rolltilldelning, den rolltilldelningen visas också. Det här filtret accepterar endast objekt-id för en användare eller ett huvudnamn för tjänsten. Du kan inte skicka ett objekt-id för en grupp. |

## <a name="grant-access"></a>Bevilja åtkomst

För att skapa åtkomst i RBAC skapar du rolltilldelningar. Du kan skapa en rolltilldelning med den [rolltilldelningar – skapa](/rest/api/authorization/roleassignments/create) REST API och ange säkerhetsobjekt, rolldefinitionen och omfång. För att anropa detta API, måste du ha åtkomst till den `Microsoft.Authorization/roleAssignments/write` igen. Av de inbyggda rollerna, endast [ägare](built-in-roles.md#owner) och [administratör för användaråtkomst](built-in-roles.md#user-access-administrator) beviljas åtkomst till den här åtgärden.

1. Använd den [rolldefinitioner – lista](/rest/api/authorization/roledefinitions/list) REST API eller se [inbyggda roller](built-in-roles.md) att hämta identifieraren för den rolldefinition som du vill tilldela.

1. Använd ett GUID-verktyg för att generera en unik identifierare som används för tilldelning rollidentifieraren. Identifieraren har formatet: `00000000-0000-0000-0000-000000000000`

1. Börja med följande begäran och brödtext:

    ```http
    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentName}?api-version=2015-07-01
    ```

    ```json
    {
      "properties": {
        "roleDefinitionId": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}",
        "principalId": "{principalId}"
      }
    }
    ```
    
1. I URI: N, Ersätt *{omfång}* med omfånget för rolltilldelningen.

    | Scope | Typ |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Prenumeration |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Resursgrupp |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Resource |

1. Ersätt *{roleAssignmentName}* med GUID-identifierare rolltilldelningen.

1. I begärandetexten, Ersätt *{subscriptionId}* med ditt prenumerations-ID.

1. Ersätt *{roleDefinitionId}* med rollen definition identifierare.

1. Ersätt *{principalId}* med objekt-ID för användaren, gruppen eller tjänstens huvudnamn som ska tilldelas rollen.

## <a name="remove-access"></a>Tar bort åtkomst

I RBAC kan du ta bort en rolltilldelning för att ta bort åtkomst. Ta bort en rolltilldelning med den [rolltilldelningar – ta bort](/rest/api/authorization/roleassignments/delete) REST API. För att anropa detta API, måste du ha åtkomst till den `Microsoft.Authorization/roleAssignments/delete` igen. Av de inbyggda rollerna, endast [ägare](built-in-roles.md#owner) och [administratör för användaråtkomst](built-in-roles.md#user-access-administrator) beviljas åtkomst till den här åtgärden.

1. Hämta rollen tilldelning identifierare (GUID). Den här identifieraren returneras när du först skapa rolltilldelningen eller hämta dem genom att ange rolltilldelningar.

1. Börja med följande begäran:

    ```http
    DELETE https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentName}?api-version=2015-07-01
    ```

1. I URI: N, Ersätt *{omfång}* med omfattning för att ta bort rolltilldelningen.

    | Scope | Typ |
    | --- | --- |
    | `subscriptions/{subscriptionId}` | Prenumeration |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1` | Resursgrupp |
    | `subscriptions/{subscriptionId}/resourceGroups/myresourcegroup1/ providers/Microsoft.Web/sites/mysite1` | Resource |

1. Ersätt *{roleAssignmentName}* med GUID-identifierare rolltilldelningen.

## <a name="next-steps"></a>Nästa steg

- [Distribuera resurser med Resource Manager-mallar och Resource Manager REST API](../azure-resource-manager/resource-group-template-deploy-rest.md)
- [Azure REST API-referens](/rest/api/azure/)
- [Skapa anpassade roller för Azure-resurser med hjälp av REST-API](custom-roles-rest.md)
