---
title: Skapa Azure-program tekniska resurser | Azure Marketplace
description: Skapa tekniska resurser för ett erbjudande för Azure-program.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: article
ms.date: 12/13/2018
ms.author: pabutler
ms.openlocfilehash: cbe1b8c8f1159d90fbf97eeae272c1c50ec9b9bb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64942991"
---
# <a name="prepare-your-azure-application-technical-assets"></a>Förbereda dina tekniska resurser för Azure-program

Den här artikeln beskriver resurserna för att förbereda tekniska resurser för ditt erbjudande i Azure-program.

## <a name="before-you-begin"></a>Innan du börjar

Granska på nedanstående video, [mallar för att skapa lösningar och hanterade program för Azure Marketplace](https://channel9.msdn.com/Events/Build/2018/BRK3603), en översikt över hur du skapar en Azure Resource Manager-mall för att definiera en Azure lösning och sedan hur du därefter publicera apperbjudandet på Azure Marketplace.

>[!VIDEO https://channel9.msdn.com/Events/Build/2018/BRK3603/player]


Granska följande dokumentation från Azure-program, vilket ger Snabbstarter, självstudier och exempel.

- [Förstå Azure Resource Manager-mallar](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates)
- Snabbstarter:

  - [Azure Quickstart-mallar](https://azure.microsoft.com/documentation/templates/)
  - [GitHub Azure Quickstart-mallar](https://github.com/azure/azure-quickstart-templates)
  - [Publicera programdefinition](https://docs.microsoft.com/azure/managed-applications/publish-managed-app-definition-quickstart)
  - [Distribuera service catalog-app](https://docs.microsoft.com/azure/managed-applications/deploy-service-catalog-quickstart)

  
- Självstudier:

  - [Skapa definitionsfiler](https://docs.microsoft.com/azure/managed-applications/publish-service-catalog-app)
  - [Publicera marknadsplatsprogram](https://docs.microsoft.com/azure/managed-applications/publish-marketplace-app)

  - Exempel:

    - [Azure CLI](https://docs.microsoft.com/azure/managed-applications/cli-samples)
    - [Azure PowerShell](https://docs.microsoft.com/azure/managed-applications/powershell-samples)
    - [Hanterade programlösningar](https://docs.microsoft.com/azure/managed-applications/sample-projects)

## <a name="fundamental-technical-knowledge"></a>Grundläggande teknisk kunskap

Utforma, bygga och testa dessa tillgångar ta tid och kräver teknisk kunskap av både Azure-plattformen och de tekniker som används för att skapa erbjudandet.

Ingenjörsteamet bör ha kunskaper om följande Microsoft-tekniker:

- Grundläggande förståelse för [Azure-tjänster](https://azure.microsoft.com/services/)
- Så här [utforma och skapa Azure-program](https://azure.microsoft.com/solutions/architecture/)
- Kunskaper om [Azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/), [Azure Storage](https://azure.microsoft.com/services/?filter=storage), och [Azure Networking](https://azure.microsoft.com/services/?filter=networking)
- Kunskaper om [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)
- Kunskaper om [JSON](https://www.json.org/)

## <a name="suggested-tools"></a>Föreslaget verktyg

Välj en eller båda av följande skript miljöer för att hantera dina Azure-program:

- [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
- [Azure CLI](https://docs.microsoft.com/cli/azure)

Vi rekommenderar att lägga till följande verktyg i utvecklingsmiljön:

- [Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
- [Visual Studio Code](https://code.visualstudio.com/) med följande filtillägg:

  - Tillägg: [Verktyget Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
  - Tillägg: [Tjärgropar](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
  - Tillägg: [Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

Vi rekommenderar också granska de tillgängliga verktyg i den [Azure-utvecklarverktyg](https://azure.microsoft.com/tools/) sidan och, om du använder Visual Studio i [Visual Studio Marketplace](https://marketplace.visualstudio.com/).

## <a name="next-steps"></a>Nästa steg

[Skapa ett erbjudande för Azure-program](./cpp-create-offer.md)

