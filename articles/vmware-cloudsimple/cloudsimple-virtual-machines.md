---
title: VMware-lösning genom CloudSimple - översikt över Azure virtuella datorer
description: Läs mer om CloudSimple virtuella datorer och deras fördelar.
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 77a2901ae2d81f42780110b1576c1f32c7ff397b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65209494"
---
# <a name="cloudsimple-virtual-machines-overview"></a>Översikt över CloudSimple virtuella datorer

CloudSimple kan du hantera virtuella VMware-datorer från Azure-portalen.  Ett kluster eller en resurspool från vSphere-kluster hanteras via Azure genom att mappa den till din prenumeration.  CloudSimple virtuell dator tar självbetjäningshantering av virtuella VMware-datorer från Azure-portalen.  

Om du vill skapa en CloudSimple VM från Azure, måste en mall för virtuell dator finnas på privat moln-vCenter.  Mallen används för att anpassa de operativsystem och program.  VM-mallen kan vara strikt läge för att uppfylla företagets säkerhetsprinciper.  Du kan använda mallen för att skapa virtuella datorer och använder dem från Azure-portalen med en självbetjäningsmodell.

## <a name="benefits"></a>Fördelar

CloudSimple-datorer från Azure-portalen tillhandahåller en mekanism för självbetjäning för användare att skapa och hantera virtuella VMware-datorer.

* Skapa en CloudSimple VM på privat moln-vCenter
* Hantera VM-egenskaper
  * Lägg till/ta bort diskar
  * Lägg till/ta bort nätverkskort
* Power driften av din CloudSimple VM
  * Slå på eller av
  * Reset VM
* Ta bort virtuell dator

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [förbrukar virtuella VMware-datorer på Azure](quickstart-create-vmware-virtual-machine.md)
* Lär dig hur du [mappa din Azure-prenumeration](https://docs.azure.cloudsimple.com/azure-subscription-mapping/)