---
title: Använd ett Azure SDK för att konfigurera en virtuell dator med hanterade identiteter för Azure-resurser
description: Steg för steg-instruktioner för att konfigurera och använda hanterade identiteter för Azure-resurser på en Azure-dator med hjälp av en Azure-SDK.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/28/2017
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 29446f6f0a86a00cc92fe46211caddd22aaa4859
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66112556"
---
# <a name="configure-a-vm-with-managed-identities-for-azure-resources-using-an-azure-sdk"></a>Konfigurera en virtuell dator med hanterade identiteter för Azure-resurser med hjälp av en Azure-SDK

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Hanterade identiteter för Azure-resurser tillhandahåller Azure-tjänster med en automatiskt hanterad identitet i Azure Active Directory (AD). Du kan använda den här identiteten för att autentisera till en tjänst som stöder Azure AD-autentisering utan autentiseringsuppgifter i din kod. 

I den här artikeln får du lära dig hur du aktiverar och ta bort hanterade identiteter för Azure-resurser för en Azure-dator med hjälp av en Azure-SDK.

## <a name="prerequisites"></a>Nödvändiga komponenter

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

## <a name="azure-sdks-with-managed-identities-for-azure-resources-support"></a>Stöd för Azure SDK: er med hanterade identiteter för Azure-resurser 

Azure har stöd för flera programmeringsspråk plattformar via ett antal [Azure SDK: er](https://azure.microsoft.com/downloads). Flera av dem har uppdaterats för att stödja hanterade identiteter för Azure-resurser och tillhandahålla motsvarande exempel för att demonstrera användning. Den här listan uppdateras när ytterligare stöd har lagts till:

| SDK | Exempel |
| --- | ------ | 
| .NET   | [Hantera resurs från en virtuell dator aktiveras med hanterade identiteter för Azure-resurser aktiverat](https://azure.microsoft.com/resources/samples/aad-dotnet-manage-resources-from-vm-with-msi/) |
| Java   | [Hantera lagring på en virtuell dator aktiveras med hanterade identiteter för Azure-resurser](https://azure.microsoft.com/resources/samples/compute-java-manage-resources-from-vm-with-msi-in-aad-group/)|
| Node.js| [Skapa en virtuell dator med systemtilldelade hanterad identitet aktiverat](https://azure.microsoft.com/resources/samples/compute-node-msi-vm/) |
| Python | [Skapa en virtuell dator med systemtilldelade hanterad identitet aktiverat](https://azure.microsoft.com/resources/samples/compute-python-msi-vm/) |
| Ruby   | [Skapa virtuell Azure-dator med en automatiskt genererad identitet som aktiverats](https://azure.microsoft.com/resources/samples/compute-ruby-msi-vm/) |

## <a name="next-steps"></a>Nästa steg

- Se relaterade artiklar under **konfigurera identitet för en Azure VM**du vill veta hur du kan också använda Azure portal, PowerShell, CLI och resource-mallarna.
