---
title: ta med fil
description: ta med fil
services: networking
author: anavinahar
ms.service: networking
ms.topic: include
ms.date: 06/25/2019
ms.author: anavin
ms.custom: include file
ms.openlocfilehash: 3e66bf61caf786473d89eab9a6567bb05aff0d19
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67457559"
---
<a name="azure-resource-manager-virtual-networking-limits"></a>Nätverksgränser – Azure Resource Manager följande begränsningar gäller endast för nätverksresurser som hanteras via **Azure Resource Manager** per region per prenumeration. Lär dig hur du [visa din aktuella användning mot din prenumerationsbegränsningar](../articles/networking/check-usage-against-limits.md).

> [!NOTE]
> Vi har nyligen ökat alla standardgränser till deras högsta gränser. Om det finns ingen övre gräns-kolumn, har resursen inte justerbara gränser. Om du hade gränserna ökat med stöd för tidigare och inte ser uppdaterade gränser i tabellerna nedan [öppna en kundsupportärende utan kostnad](../articles/azure-resource-manager/resource-manager-quota-errors.md)

| Resource | Standard-/ maxgräns | 
| --- | --- |
| Virtuella nätverk |1,000 |
| Undernät per virtuellt nätverk |3,000 |
| Vnet-peering per virtuellt nätverk |500 |
| DNS-servrar per virtuellt nätverk |20 |
| Privata IP-adresser per virtuellt nätverk |65,536 |
| Privata IP-adresser per nätverksgränssnitt |256 |
| Privata IP-adresser per virtuell dator |256 |
| Samtidiga TCP eller UDP flöden per nätverkskort för en virtuell dator eller rollinstans |500,000 |
| Nätverkskort |65,536 |
| Nätverkssäkerhetsgrupper |5,000 |
| NSG-regler per NSG |1,000 |
| IP-adresser och intervall som angetts för källa eller mål i en säkerhetsgrupp |4,000 |
| Programsäkerhetsgrupper |3,000 |
| Programsäkerhetsgrupper per IP-konfiguration, per nätverkskort |20 |
| IP-konfigurationer per programsäkerhetsgruppen |4,000 |
| Programsäkerhetsgrupper som kan anges i alla säkerhetsregler för en grupp |100 |
| Användardefinierade vägtabeller |200 |
| Användardefinierade vägar vägtabell |400 |
| Punkt-till-plats-rotcertifikat per Azure VPN Gateway |20 |
| Trycker på virtuellt nätverk |100 |
| Tryck på nätverksgränssnittskonfigurationer per virtuellt nätverks-TAP |100 |

#### <a name="publicip-address"></a>Offentliga IP-adress gränser
| Resource | Standardgräns | Övre gräns |
| --- | --- | --- |
| Offentliga IP-adresser – dynamisk | 1 000 för Basic. |Kontakta supporten. |
| Offentliga IP-adresser – statiska | 1 000 för Basic. |Kontakta supporten. |
| Offentliga IP-adresser – statiska | 200 för Standard.|Kontakta supporten. |
| Längd för offentlig IP-prefix | /28 | Kontakta supporten. |

#### <a name="load-balancer"></a>Load balancer gränser
Följande begränsningar gäller endast för nätverksresurser som hanteras via Azure Resource Manager per region per prenumeration. Lär dig hur du [visa din aktuella användning mot din prenumerationsbegränsningar](../articles/networking/check-usage-against-limits.md).

| Resource | Standard-/ maxgräns |
| --- | --- |
| Lastbalanserare | 1,000 | 
| Regler per resurs, Basic | 250 |
| Regler per resurs, Standard | 1,500 | 
| Regler per IP-konfiguration | 299 |
| Regler per nätverkskort | 300 |
| Frontend IP-konfigurationer, Basic | 200 |
| Frontend IP-konfigurationer, Standard | 600 |
| Backend-poolen, Basic | 100, enskild tillgänglighetsuppsättning |
| Backend-poolen, Standard | 1 000, enda virtuellt nätverk |
| Backend-resurser per belastningsutjämnare, Standard<sup>1</sup> | 150 |
| Portar med hög tillgänglighet, Standard | 1 per interna frontend |

<sup>1</sup>gränsen är upp till 150 resurser i valfri kombination av fristående virtuella datorresurser, tillgänglighetsuppsättning resurser och resurser för VM-skalningsuppsättning.

#### <a name="virtual-networking-limits-classic"></a>Följande begränsningar gäller endast för nätverksresurser som hanteras via den **klassiska** distributionsmodellen per prenumeration. Lär dig hur du [visa din aktuella användning mot din prenumerationsbegränsningar](../articles/networking/check-usage-against-limits.md).

| Resource | Standardgräns | Övre gräns |
| --- | --- | --- |
| Virtuella nätverk |100 |100 |
| Lokala nätverksplatser |20 |Kontakta supporten. |
| DNS-servrar per virtuellt nätverk |20 |20 |
| Privata IP-adresser per virtuellt nätverk |4,096 |4,096 |
| Samtidiga TCP eller UDP flöden per nätverkskort för en virtuell dator eller rollinstans |500 000 upp till 1 000 000 för två eller flera nätverkskort. |500 000 upp till 1 000 000 för två eller flera nätverkskort. |
| Nätverkssäkerhetsgrupper (NSG) |200 |200 |
| NSG-regler per NSG |1,000 |1,000 |
| Användardefinierade vägtabeller |200 |200 |
| Användardefinierade vägar vägtabell |400 |400 |
| Offentliga IP-adresser (dynamiska) |500 |500 |
| Reserverade offentliga IP-adresser |500 |500 |
| Offentlig VIP per distribution |5 |Kontakta supporten |
| Privat VIP (intern belastningsutjämning) per distribution |1 |1 |
| Slutpunkten åtkomstkontrollistor (ACL) |50 |50 |
