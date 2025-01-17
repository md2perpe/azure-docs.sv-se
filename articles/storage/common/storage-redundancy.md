---
title: Dataredundans i Azure Storage | Microsoft Docs
description: Data i ditt Microsoft Azure Storage-konto replikeras för hållbarhet och hög tillgänglighet. Alternativ för dataredundans är lokalt redundant lagring (LRS), zonredundant lagring (ZRS), geo-redundant lagring (GRS) och read-access geo-redundant lagring (RA-GRS).
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 01/18/2019
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: 078c62913b903eafe9e0fcfcef4189f5ca735d0f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66002825"
---
# <a name="azure-storage-redundancy"></a>Redundans i Azure Storage

Data i ditt Microsoft Azure-lagringskonto replikeras alltid för att säkerställa hållbarhet och hög tillgänglighet. Azure Storage kopierar dina data så att den är skyddad från planerade och oplanerade händelser, inklusive tillfälliga maskinvarufel, nätverket och strömavbrott av massiv naturkatastrofer. Du kan välja att replikera dina data inom samma Datacenter på zonindelad datacenter inom samma region eller flera geografiskt åtskilda regioner.

Replikeringen garanterar att ditt lagringskonto uppfyller [Servicenivåavtal (SLA) för lagring](https://azure.microsoft.com/support/legal/sla/storage/) även vid fel. Visa SLA för information om Azure Storage-garantier för hållbarhet och tillgänglighet.

Azure Storage kontrollerar regelbundet integriteten för data som lagras med redundanskontroller (CRC). Den har reparerats med hjälp av redundanta data om skadade data har identifierats. Azure-lagring beräknas också kontrollsummor på all nätverkstrafik för att identifiera skadade data paket när du lagrar eller hämtning av data.

## <a name="choosing-a-redundancy-option"></a>Välja ett alternativ för dataredundans

När du skapar ett lagringskonto kan välja du något av följande redundansalternativ för:

* [Lokalt redundant lagring (LRS)](storage-redundancy-lrs.md)
* [Zonredundant lagring (ZRS)](storage-redundancy-zrs.md)
* [Geo-redundant lagring (GRS)](storage-redundancy-grs.md)
* [Geo-redundant lagring med läsbehörighet (RA-GRS)](storage-redundancy-grs.md#read-access-geo-redundant-storage)

I följande tabell ger en snabb överblick över omfånget för hållbarhet och tillgänglighet som varje replikeringsstrategi ger dig för en viss typ av händelse (eller händelse liknande effektnivå).

| Scenario                                                                                                 | LRS                             | ZRS                              | GRS                                  | RA-GRS                               |
| :------------------------------------------------------------------------------------------------------- | :------------------------------ | :------------------------------- | :----------------------------------- | :----------------------------------- |
| Noden otillgänglighet inom ett datacenter                                                                 | Ja                             | Ja                              | Ja                                  | Ja                                  |
| Ett helt datacenter (zonindelad eller icke-zonindelad) blir otillgänglig                                           | Nej                              | Ja                              | Ja                                  | Ja                                  |
| Ett avbrott på regionomfattande                                                                                     | Nej                              | Nej                               | Ja                                  | Ja                                  |
| Läsåtkomst till dina data (på en fjärransluten, geo-replikerad region) i händelse av hela otillgänglighet | Nej                              | Nej                               | Nej                                   | Ja                                  |
| Utformad för att ge \_ \_ objektshållbarhet under ett givet år                                          | minst 99,999999999% (11 9) | minst 99,9999999999% (12 9) | minst 99,99999999999999% (16 9) | minst 99,99999999999999% (16 9) |
| Stöds storage-kontotyper                                                                   | GPv2, GPv1, Blob                | GPv2                             | GPv2, GPv1, Blob                     | GPv2, GPv1, Blob                     |
| Tillgänglighet för läsbegäranden | Minst 99,9% (99% för lågfrekvent åtkomstnivå) | Minst 99,9% (99% för lågfrekvent åtkomstnivå) | Minst 99,9% (99% för lågfrekvent åtkomstnivå) | Minst 99,99% (99,9% för kalla åtkomstnivåer) |
| Tillgänglighet för skrivbegäranden | Minst 99,9% (99% för lågfrekvent åtkomstnivå) | Minst 99,9% (99% för lågfrekvent åtkomstnivå) | Minst 99,9% (99% för lågfrekvent åtkomstnivå) | Minst 99,9% (99% för lågfrekvent åtkomstnivå) |

Information om priser för varje alternativ för dataredundans, finns i [priser för Azure Storage](https://azure.microsoft.com/pricing/details/storage/). 

Information om Azure Storage-garantier för hållbarhet och tillgänglighet, finns i den [serviceavtal för Azure Storage](https://azure.microsoft.com/support/legal/sla/storage/).

> [!NOTE]
> Premium Storage stöder endast lokalt redundant lagring (LRS).

## <a name="changing-replication-strategy"></a>Ändra replikeringsstrategi
Du kan ändra replikeringsstrategi i ditt storage-konto med hjälp av den [Azure-portalen](https://portal.azure.com/), [Azure Powershell](storage-powershell-guide-full.md), [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest), eller någon av de [Azure-klient bibliotek](https://docs.microsoft.com/azure/index#pivot=sdkstools). Ändra typen för replikering av ditt lagringskonto resulterar inte i driftstopp.

   > [!NOTE]
   > För närvarande kan använda du inte portalen eller API för att omvandla ditt konto till ZRS. Om du vill konvertera ditt kontos replikering till ZRS [zonen-redundant lagring (ZRS)](storage-redundancy-zrs.md) mer information.
    
### <a name="are-there-any-costs-to-changing-my-accounts-replication-strategy"></a>Finns det några kostnader för att ändra replikeringsstrategi för mitt konto?
Det beror på din sökväg till konvertering. Sorteringen från billigaste på dyraste redundans-erbjudandet och besvarar LRS, ZRS, GRS och RA-GRS. Till exempel kommer *från* LRS till något tillkommer ytterligare kostnader eftersom du kommer att en mer avancerad redundansnivå. Gå *till* GRS eller RA-GRS påförs en avgift för utgående bandbredd eftersom dina data (på din primära region) replikeras till din fjärranslutna sekundär region. Detta är en enstaka dig vid installationen. När data kopieras, finns det inga ytterligare avgifter för konvertering. Du debiteras bara för att replikera alla nya eller uppdateringar av befintliga data. Mer information om kostnader för bandbredd finns [prissättning för Azure Storage](https://azure.microsoft.com/pricing/details/storage/blobs/).

Om du konverterar ditt lagringskonto från GRS till LRS ingår utan extra kostnad, men din replikerade data tas bort från den sekundära platsen.

Om du konverterar ditt storage-konto från RA-GRS till GRS eller LRS debiteras det kontot som RA-GRS för ytterligare 30 dagar efter det datum som den har konverterats.

## <a name="see-also"></a>Se också

- [Lokalt redundant lagring (LRS): Dataredundans med låg kostnad för Azure Storage](storage-redundancy-lrs.md)
- [Zonredundant lagring (ZRS): Azure Storage-program med hög tillgänglighet](storage-redundancy-zrs.md)
- [GEO-redundant lagring (GRS): Tvärregional replikering för Azure Storage](storage-redundancy-grs.md)
- [Skalbarhets- och prestandamål i Azure Storage](storage-scalability-targets.md)
- [Utforma högtillgängliga program med hjälp av RA-GRS-lagring](../storage-designing-ha-apps-with-ragrs.md)
- [Microsoft Azure Storage redundans alternativ och läsåtkomst geografiskt redundant lagring](https://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/11/introducing-read-access-geo-replicated-storage-ra-grs-for-windows-azure-storage.aspx)
- [SOSP Paper - Azure Storage: En högtillgänglig molnlagringstjänst med stark konsekvens](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx)
