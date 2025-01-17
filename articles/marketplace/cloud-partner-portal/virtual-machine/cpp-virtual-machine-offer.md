---
title: Erbjudande för virtuell dator på Azure Marketplace
description: Översikt över processen för att publicera ett erbjudande för virtuell dator på Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal
author: v-miclar
ms.service: marketplace
ms.topic: article
ms.date: 12/04/2018
ms.author: pabutler
ms.openlocfilehash: fed0f47c963edf40883c432f5476bd7fe5720abb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64938047"
---
# <a name="virtual-machine-offer"></a>Erbjudande på virtuella datorer

|    |    |
|-----------------------------------------------------------------|------------------------------------------|
| Det här avsnittet beskrivs hur du publicerar ett nytt erbjudande för virtuell dator till den [Azure Marketplace](https://azuremarketplace.microsoft.com). Det finns stöd för både Windows- och Linux-baserade virtuella datorer, som innehåller ett operativsystem virtuell hårddisk (VHD) och noll eller flera data virtuella hårddiskar. | ![ikon för virtuell dator](./media/virtual-machine-icon.png)  |


## <a name="publishing-overview"></a>Översikt över publicering

På nedanstående video, [optimera dina Azure Marketplace-erbjudande](https://channel9.msdn.com/Events/Build/2017/P4026?ocid=player), visas en översikt av Azure Marketplace, inklusive hur du publicerar på den här marketplace (med en lösning för virtuell dator), hur du optimerar användarupplevelsen med din produktsidan och valfritt Test Drive-upplevelsen, hur användaren leder genereras och hur du kan använda dem och optimera Kundengagemang.

> [!VIDEO https://channel9.msdn.com/Events/Build/2017/P4026/player]


## <a name="vm-publishing-process-flow"></a>Processflöde för VM-publicering

Följande diagram illustrerar de övergripande stegen vid publicering av ett erbjudande för virtuell dator. 

![Publiceringsprocessen för virtuell dator](./media/publishvm_001.png)

1. Skapa erbjudande - All information och information om erbjudandet har konfigurerats, inklusive erbjudande-beskrivning, marknadsföring material, juridisk information, stöd för information och tillgången specifikationer.

2. Skapa affärs- och tekniska resurser – skapa företagstillgångar (juridiska dokument och marknadsföringsmaterial) och tekniska resurser för den associerade lösningen (här, virtuella datorer och anslutna diskar). 

3. Skapa SKU: N – skapa den associerade SKU: er som är associerade med erbjudandet och skicka dem.  En unik SKU måste anges för varje bild som du planerar att publicera. 
 
4. Certifiera och publicera erbjudande - när erbjudandet och tekniska resurser har slutförts, kan du skicka erbjudandet. Den här överföringen startar publiceringsprocessen där lösningen testas verifierats, certifierade, sedan ”lanseras” på marketplace.  

## <a name="next-steps"></a>Nästa steg

Innan du betraktar dessa steg måste du uppfylla de [tekniska krav och affärskrav](./cpp-prerequisites.md) för att publicera en virtuell dator till Microsoft Azure Marketplace. 
