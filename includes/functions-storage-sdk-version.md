---
title: ta med fil
description: ta med fil
services: functions
author: tdykstra
manager: cfowler
ms.service: functions
ms.topic: include
ms.date: 05/17/2018
ms.author: tdykstra
ms.custom: include file
ms.openlocfilehash: f8733ef907b8f31ace7ea72f705ba1b37d1adece
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67187133"
---
### <a name="azure-storage-sdk-version-in-functions-1x"></a>Azure Storage SDK-versionen i Functions 1.x

I funktioner 1.x, Storage-utlösare och bindningar använda version 7.2.1 av Azure Storage SDK ([WindowsAzure.Storage](https://www.nuget.org/packages/WindowsAzure.Storage/7.2.1) NuGet-paketet). Om du refererar till en annan version av Storage SDK, och du binder till en typ av Storage SDK i din funktionssignatur, kan Functions-körning rapporterar att den inte kan bindas till den typen. Lösningen är att se till att projektreferenserna [WindowsAzure.Storage 7.2.1](https://www.nuget.org/packages/WindowsAzure.Storage/7.2.1).
