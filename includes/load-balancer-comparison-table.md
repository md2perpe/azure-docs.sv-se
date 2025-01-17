---
title: ta med fil
description: ta med fil
services: load balancer
author: KumudD
ms.service: load-balancer
ms.topic: include
ms.date: 02/08/2018
ms.author: kumud
ms.custom: include file
ms.openlocfilehash: 1d3ce900f7354b31e999c12b8e1eb0e23d391fcb
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67187764"
---
| | Standard-SKU | Grundläggande SKU |
| --- | --- | --- |
| Storlek på serverdelspool | Stöder upp till 1000 instanser. | Stöder upp till 100 instanser. |
| Slutpunkter för serverdelspool | Alla virtuella datorer i ett enskilt virtuellt nätverk, inklusive blandning av virtuella datorer, tillgänglighetsuppsättningar, VM-skalningsuppsättningar. | Virtuella datorer i en enskild tillgänglighetsuppsättning eller en VM-skalningsuppsättning. |
| [Hälsoavsökningar](../articles/load-balancer/load-balancer-custom-probe-overview.md#types) | TCP, HTTP, HTTPS | TCP, HTTP |
| [Beteende hos hälsoavsökning ur funktion](../articles/load-balancer/load-balancer-custom-probe-overview.md#probedown) | TCP-anslutningarna förblir aktiva när instansavsökningen är ur funktion __och__ på alla avsökningar är ur funktion. | TCP-anslutningarna förblir aktiva när instansavsökningen är ur funktion. Alla TCP-anslutningar avslutas på alla avsökningar ur funktion. |
| Tillgänglighetszoner | I standard-SKU:er, zonredundanta och zonindelade klienter för inkommande och utgående överlever utgående flödesmappningar zonfel och belastningsutjämning mellan zoner. | Inte tillgängligt. |
| Diagnostik | Azure Monitor, flerdimensionella mått inklusive byte- och paketräknare, hälsoavsökningsstatus, anslutningsförsök (TCP SYN), utgående anslutningshälsa (lyckade och misslyckade SNAT-flöden), aktiva dataplansmått | Azure Log Analytics för offentlig lastbalanserare, SNAT-överbelastningsavisering, räknare för serverdelspoolshälsa. |
| HA-portar | Intern lastbalanserare | Inte tillgängligt. |
| Säker som standard | Såvida detta inte godkänts av en nätverkssäkerhetsgrupp så är inkommande inte tillåtet för offentliga IP- och lastbalanserarslutpunkter (offentliga och interna). | Öppen som standard, nätverkssäkerhetsgrupp valfri. |
| [Utgående anslutningar](../articles/load-balancer/load-balancer-outbound-connections.md) | Du kan uttryckligen ange poolbaserad utgående NAT med [utgående regler](../articles/load-balancer/load-balancer-outbound-rules-overview.md). Du kan använda flera klientdelar med avanmälan per lastbalanserarregel. Ett utgående scenario _måste_ uttryckligen skapas för att den virtuella datorn, tillgänglighetsuppsättningen och VM-skalningsuppsättningen ska kunna använda utgående anslutning.  Du kan nå tjänstslutpunkter för virtuellt nätverk utan att definiera utgående anslutning, och de räknas inte mot bearbetade data.  Alla offentliga IP-adresser, inklusive Azure PaaS-tjänster som är inte tillgängliga som VNet-tjänstslutpunkter, måste nås via utgående anslutning och räknas mot bearbetade data. När bara en intern lastbalanserare fungerar som värd för en virtuell dator, tillgänglighetsuppsättning eller skalningsuppsättning för virtuell dator är utgående anslutningar via standard-SNAT inte tillgängliga. Använd [utgående regler](../articles/load-balancer/load-balancer-outbound-rules-overview.md) i stället. Utgående SNAT-programmering är transportprotokollsspecifik och baseras på den inkommande lastbalanserarregelns protokoll. | En enskild klientdel som valts slumpmässigt när det finns flera klientdelar.  När endast den interna lastbalanseraren fungerar som värd för en virtuell dator, tillgänglighetsuppsättning eller VM-skalningsuppsättningar används standard-SNAT. |
| [Regel för utgående trafik](../articles/load-balancer/load-balancer-outbound-rules-overview.md) | Deklarativ utgående NAT-konfiguration som använder offentliga IP-adresser eller offentliga IP-prefix eller bådadera, konfigurerbar utgående timeout för inaktivitet (4-120 minuter), anpassad SNAT-porttilldelning | Inte tillgängligt. |
|  [TCP-återställning vid inaktivitet](../articles/load-balancer/load-balancer-tcp-reset.md) | Aktivera TCP-återställning (TCP RST) för timeout vid inaktivitet för alla regler | Inte tillgängligt |
| [Flera klienter](../articles/load-balancer/load-balancer-multivip-overview.md) | Inkommande och [utgående](../articles/load-balancer/load-balancer-outbound-connections.md) | Endast inkommande |
| Hanteringsåtgärder | De flesta åtgärder < 30 sekunder | 60 -90+ sekunder är standard. |
| SLA | 99,99 % för datasökväg med två felfria virtuella datorer. | Inte tillämpligt. | 
| Prissättning | Debiteras baserat på antal regler och bearbetade inkommande och utgående data associerade med resursen.  | Ingen avgift. |
|  |  |  |
