---
title: Grupputveckling Azure Dev blanksteg med hjälp av .NET Core och VS Code
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 07/09/2018
ms.topic: tutorial
description: Snabb Kubernetes-utveckling med containrar och mikrotjänster i Azure
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, behållare, Helm, tjänsten nät, tjänsten nät routning, kubectl, k8s '
ms.openlocfilehash: 608f95234af68bad0b3c961baf61d3476d2126ea
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65765153"
---
[!INCLUDE [](../../includes/devspaces-team-development-1.md)]

### <a name="make-a-code-change"></a>Göra en kodändring
Gå till VS Code-fönstret för `mywebapi` och göra en kod som redigera den `string Get(int id)` -metod i `Controllers/ValuesController.cs`, till exempel:

```csharp
[HttpGet("{id}")]
public string Get(int id)
{
    return "mywebapi now says something new";
}
```

[!INCLUDE [](../../includes/devspaces-team-development-2.md)]
