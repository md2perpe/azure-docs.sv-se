---
title: Vad är nytt i Azure Application Gateway
description: Lär dig vad är nytt med Azure Application Gateway som föråldrade funktioner för de senaste versionsanteckningarna, kända problem, felkorrigeringar och kommande ändringar.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: overview
ms.date: 4/30/2019
ms.author: victorh
ms.openlocfilehash: 2a494b924107baeabbcf412af7e1bbdb1db0f753
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/07/2019
ms.locfileid: "66752014"
---
# <a name="whats-new-in-azure-application-gateway"></a>Vad är nytt i Azure Application Gateway?

Azure Application Gateway uppdateras kontinuerligt. Om du vill hålla dig uppdaterad med den senaste utvecklingen, innehåller den här artikeln information om:

- Den senaste versionen
- Kända problem
- Felkorrigeringar
- Inaktuell funktion

## <a name="new-features"></a>Nya funktioner

|Funktion  |Beskrivning  |Tilläggsdatum  |
|---------|---------|---------|
|Anpassade WAF-regler |Applicaiton Gateway WAF_v2 stöder nu skapa anpassade regler. Se [anpassade regler för Application Gateway](custom-waf-rules-overview.md). |Juni 2019 |
|Automatisk skalning och redundans, statiska VIP stöd GA |Allmän tillgänglighet för v2-SKU som stöder automatisk skalning, redundans, förbättra prestanda, statiska virtuella IP-adresser, Key Vault omarbetning för rubriken. Se [dokumentationen till Programgatewayen autoskalning](application-gateway-autoscaling-zone-redundant.md). |April 2019 |
|Key Vault-integrering |Application Gateway stöder nu integrering med Key Vault (i allmänt tillgänglig förhandsversion) för servercertifikat som är kopplade till HTTPS-aktiverade lyssnare. Se [SSL-avslutning med Key Vault-certifikat](key-vault-certs.md). |April 2019 |
|Rubriken CRUD/omskrivningar     |Du kan nu arbeta om HTTP-huvuden. Se [självstudien: Skapa en Programgateway och skriv om HTTP-huvuden](tutorial-http-header-rewrite-powershell.md) för mer information.|December 2018|
|WAF-konfigurationen och undantag lista     |Vi har lagt till fler alternativ för att hjälpa dig att konfigurera din WAF och minska falska positiva identifieringar. Se [storleksgränser för Web application firewall begäran och undantagslistor](application-gateway-waf-configuration.md) för mer information.|December 2018|
|Automatisk skalning och redundans, statiska VIP-stöd      |Det finns många förbättringar, till exempel automatisk skalning och förbättrad prestanda med v2-SKU. Se [vad är Azure Application Gateway?](overview.md) för mer information.|September 2018|
|Anslutningstömning     |Anslutningstömning kan du ta bort medlemmar från backend-poolen ett smidigt sätt. Mer information finns i [Anslutningstömning](overview.md#connection-draining).|September 2018|
|Anpassade felsidor     |Du kan skapa en felsida i formatet för resten av dina webbplatser med anpassade felsidor. För att möjliggöra detta, se [skapa Programgateway anpassade felsidor](custom-error.md).|September 2018|
|Förbättringar av mått     |Du kan få en bättre överblick över tillståndet för din Programgateway med förbättrad mått. För att aktivera mått på Application Gateway, se [backend-hälsotillstånd, diagnostikloggar och mått för Application Gateway](application-gateway-diagnostics.md).|Juni 2018|

## <a name="next-steps"></a>Nästa steg

Läs mer om Azure Application Gateway, [vad är Azure Application Gateway?](overview.md)
