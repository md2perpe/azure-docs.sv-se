---
title: Skriptexempel för Azure CLI – Återställa en webbapp från en säkerhetskopia | Microsoft Docs
description: Skriptexempel för Azure CLI – Återställa en webbapp från en säkerhetskopia
services: app-service\web
documentationcenter: ''
author: msangapu
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.service: app-service-web
ms.workload: web
ms.devlang: na
ms.topic: sample
ms.date: 12/07/2017
ms.author: msangapu
ms.reviewer: cephalin
ms.custom: seodec18
ms.openlocfilehash: 9c0306f9b9d89962a4dd3cb05c168db3ec1ba4c1
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/07/2019
ms.locfileid: "67618839"
---
# <a name="restore-a-web-app-from-a-backup-using-cli"></a>Återställa en webbapp från en säkerhetskopia med CLI

Det här exempelskriptet skapar en webbapp i App Service med dess relaterade resurser, och skapar sedan en tidsbegränsad säkerhetskopia för webbappen. 

Om du vill köra skriptet behöver du en befintlig säkerhetskopia för en webbapp. Information om hur du skapar en sådan finns i [Säkerhetskopiera en webbapp](cli-backup-onetime.md) eller [Skapa en schemalagd säkerhetskopiering för en webbapp](cli-backup-scheduled.md).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI-gränssnittet lokalt måste du ha Azure CLI version 2.0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI]( /cli/azure/install-azure-cli). 

## <a name="sample-script"></a>Exempelskript

[!code-azurecli-interactive[main](../../../cli_scripts/app-service/backup-restore/backup-restore.sh?highlight=3-4,9 "Restore a web app from a backup")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [`az webapp config backup list`](/cli/azure/webapp/config/backup?view=azure-cli-latest#az-webapp-config-backup-list) | Hämtar en lista över säkerhetskopior för en webbapp. |
| [`az webapp config backup restore`](/cli/azure/webapp/config/backup?view=azure-cli-latest#az-webapp-config-backup-restore) | Återställer en webbapp från en säkerhetskopia. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](https://docs.microsoft.com/cli/azure).

Fler App Service CLI-skriptexempel finns i [dokumentationen för Azure App Service](../samples-cli.md).
