---
title: ta med fil
description: ta med fil
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/11/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 13e99f1dc84c5345ba04fcfc412b8ef4237cc581
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67187016"
---
## <a name="copy-your-credentials-from-the-azure-portal"></a>Kopiera dina autentiseringsuppgifter från Azure-portalen

Exempelprogrammet måste auktorisera åtkomsten till ditt lagringskonto. Tillhandahåll autentiseringsuppgifterna för ditt lagringskonto till programmet i form av en anslutningssträng. Visa autentiseringsuppgifterna för lagringskontot:

1. Navigera till [Azure-portalen](https://portal.azure.com).
2. Leta rätt på ditt lagringskonto.
3. Välj **Åtkomstnycklar** i avsnittet **Inställningar** i lagringskontoöversikten. Åtkomstnycklarna och anslutningssträngen för ditt konto visas.
4. Notera namnet på lagringskontot. Du behöver det för auktoriseringen.   
5. Sök efter värdet för **Key** (Nyckel) **key1** och klicka på knappen **Kopiera** för att kopiera kontonyckeln.

    ![Skärmbild som visar hur du kopierar kontonyckeln från Azure-portalen](media/storage-copy-account-key-portal/portal-account-key.png)
