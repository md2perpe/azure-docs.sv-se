---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: ce64047fd7490106790ea8bb1ad7963d82a87c24
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67187720"
---
| Resource | Lediga | Delad | Basic | Standard | Premium (v2) | Isolerad </th> |
| --- | --- | --- | --- | --- | --- | --- |
| [Webb-, Mobil- eller API apps](https://azure.microsoft.com/services/app-service/) per [Azure App Service-plan](../articles/app-service/overview-hosting-plans.md)<sup>1</sup> |10 |100 |Obegränsade<sup>2</sup> |Obegränsade<sup>2</sup> |Obegränsade<sup>2</sup> |Obegränsade<sup>2</sup>|
| [App Service-plan](../articles/app-service/overview-hosting-plans.md) |10 per region |10 per resursgrupp |100 per resursgrupp |100 per resursgrupp |100 per resursgrupp |100 per resursgrupp|
| Beräkning instanstyp |Delad |Delad |Dedikerad<sup>3</sup> |Dedikerad<sup>3</sup> |Dedikerad<sup>3</sup></p> |Dedikerad<sup>3</sup>|
| [Skala ut](../articles/app-service/web-sites-scale.md) (maximalt antal instanser) |1 delad |1 delad |3 dedikerade<sup>3</sup> |10 dedikerade<sup>3</sup> |20 dedikerade<sup>3</sup>|100 dedikerade<sup>4</sup>|
| Storage<sup>5</sup> |1 GB<sup>5</sup> |1 GB<sup>5</sup> |10 GB<sup>5</sup> |50 GB<sup>5</sup> |250 GB<sup>5</sup></p> |1 TB<sup>5</sup>|
| CPU-tid (5 minuter)<sup>6</sup> |3 minuter |3 minuter |Obegränsade, betala enligt standardpriserna [priser](https://azure.microsoft.com/pricing/details/app-service/)</a> |Obegränsade, betala enligt standardpriserna [priser](https://azure.microsoft.com/pricing/details/app-service/)</a> |Obegränsade, betala enligt standardpriserna [priser](https://azure.microsoft.com/pricing/details/app-service/)</a> |Obegränsade, betala enligt standardpriserna [priser](https://azure.microsoft.com/pricing/details/app-service/)</a>|
| Processortid (dag)<sup>6</sup> |60 minuter |240 minuter |Obegränsade, betala enligt standardpriserna [priser](https://azure.microsoft.com/pricing/details/app-service/)</a> |Obegränsade, betala enligt standardpriserna [priser](https://azure.microsoft.com/pricing/details/app-service/)</a> |Obegränsade, betala enligt standardpriserna [priser](https://azure.microsoft.com/pricing/details/app-service/)</a> |Obegränsade, betala enligt standardpriserna [priser](https://azure.microsoft.com/pricing/details/app-service/)</a> |
| Minne (1 timme) |1 024 MB per App Service-plan |1 024 MB per app |Gäller inte |Saknas |Saknas |Gäller inte |
| Bandbredd |165 MB |Obegränsad och [dataöverföring](https://azure.microsoft.com/pricing/details/data-transfers/) gäller |Obegränsad och [dataöverföring](https://azure.microsoft.com/pricing/details/data-transfers/) gäller |Obegränsad och [dataöverföring](https://azure.microsoft.com/pricing/details/data-transfers/) gäller |Obegränsad och [dataöverföring](https://azure.microsoft.com/pricing/details/data-transfers/) gäller |Obegränsad och [dataöverföring](https://azure.microsoft.com/pricing/details/data-transfers/) gäller |
| Programarkitektur |32-bitars |32-bitars |32-bitars/64-bitars |32-bitars/64-bitars |32-bitars/64-bitars |32-bitars/64-bitars |
| Web sockets per instans<sup>7</sup> |5 |35 |350 |Obegränsat |Obegränsat |Obegränsat |
| Samtidiga [felsökning av anslutningar](../articles/app-service/troubleshoot-dotnet-visual-studio.md) per program |1 |1 |1 |5 |5 |5 |
| App Service Certificate per prenumeration<sup>10</sup>| Stöds inte | Stöds inte |10 |10 |10 |10 |
| Anpassade domäner per app</a> |0 (endast azurewebsites.net underdomän)|500 |500 |500 |500 |500 |
| Anpassad domän [SSL-stöd](../articles/app-service/app-service-web-tutorial-custom-ssl.md) |Stöds, inte jokerteckencertifikat för *. azurewebsites.net som är tillgängliga som standard|Stöds, inte jokerteckencertifikat för *. azurewebsites.net som är tillgängliga som standard|Obegränsade SNI SSL-anslutningar |Obegränsade SNI SSL och 1 IP SSL-anslutningar ingår |Obegränsade SNI SSL och 1 IP SSL-anslutningar ingår | Obegränsade SNI SSL och 1 IP SSL-anslutningar ingår|
| Integrerad belastningsutjämnare | |X |X |X |X |X<sup>9</sup> |
| [Alltid på](../articles/app-service/configure-common.md) | | |X |X |X |X |
| [Schemalagda säkerhetskopieringar](../articles/app-service/manage-backup.md) | | | | Schemalagda säkerhetskopieringar varannan timme, upp till 12 säkerhetskopieringar per dag (manuell + schemalagd) | Schemalagda säkerhetskopieringar varje timme, högst 50 säkerhetskopieringar per dag (manuell + schemalagd) | Schemalagda säkerhetskopieringar varje timme, högst 50 säkerhetskopieringar per dag (manuell + schemalagd) |
| [Automatisk skalning](../articles/app-service/web-sites-scale.md) | | | |X |X |X |
| [WebJobs](../articles/app-service/webjobs-create.md)<sup>8</sup> |X |X |X |X |X |X |
| [Azure Scheduler](https://azure.microsoft.com/services/scheduler/) stöd | |X |X |X |X |X |
| [Slutpunktsövervakning](../articles/app-service/web-sites-monitor.md) | | |X |X |X |X |
| [mellanlagringsplatser](../articles/app-service/deploy-staging-slots.md) | | | |5 |20 |20 |
| SLA | |  |99,9 % |99,95 %|99,95 %|99,95 %|  

<sup>1</sup>appar och lagringskvoter är per App Service-plan om inget annat anges.  
<sup>2</sup>det faktiska antalet appar som du kan ha på de här datorerna beror på aktiviteten för appar, storleken på instanserna av datorer och motsvarande resursutnyttjandet.  
<sup>3</sup>dedikerade instanser kan vara av olika storlekar. Mer information finns i [prissättning för App Service](https://azure.microsoft.com/pricing/details/app-service/).  
<sup>4</sup>mer tillåts på begäran.  
<sup>5</sup>lagringsgränsen är den totala innehållsstorleken över alla appar i samma App Service-planen.  
<sup>6</sup>dessa resurser är begränsade av fysiska resurser på de dedikerade instanserna (instansstorlek och antalet instanser).  
<sup>7</sup>om du skalar en app i Basic-nivån till två instanser du har 350 samtidiga anslutningar för var och en av de två instanserna.  
<sup>8</sup>kör anpassade körningsbara filer och/eller skript på begäran, enligt ett schema eller kontinuerligt som bakgrundsjobb i din App Service. ”Alltid på” krävs för kontinuerlig körning av WebJobs. Azure Scheduler Kostnadsfri eller Standard krävs för schemalagda WebJobs. Det finns ingen fördefinierade gräns för hur många av WebJobs som kan köras i en App Service-instans. Det finns praktisk gräns som beror på vad programkoden försöker utföra.  
<sup>9</sup>app Service isolerade SKU: er har möjlighet att vara internt läsa in belastningsutjämnade (ILB) med Azure Load Balancer så att det finns inga offentliga anslutningsmöjligheter från internet. Därför måste vissa funktioner för en ILB-isolerad App Service användas från datorer som har direkt åtkomst till ILB-nätverksslutpunkten.  
<sup>10</sup>kan du öka kvotgränsen för den App Service Certificate per prenumeration via en supportbegäran om att en maxgräns på 200.  