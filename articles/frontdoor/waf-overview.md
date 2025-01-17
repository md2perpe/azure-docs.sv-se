---
title: Vad är Azure waf för Azure-åtkomsten? (Förhandsversion)
description: Lär dig hur Azure Brandvägg för webbaserade program för Azure ytterdörren tjänsten skyddar dina webbprogram mot skadliga attacker.
services: frontdoor
documentationcenter: ''
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/31/2019
ms.author: kumud;tyao
ms.openlocfilehash: 122e9687ee313edff34e5a4fd9a44b1026a63811
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66478775"
---
# <a name="what-is-azure-web-application-firewall-for-azure-front-door"></a>Vad är Azure waf för Azure-åtkomsten?

Med Azures brandvägg för webbaserade program (WAF) får du ett centraliserat skydd för dina webbprogram som levereras globalt med Azure Front Door. Den är utformad och drivs för att skydda dina webbtjänster mot vanliga kryphål och säkerhetsproblem och se till att din tjänst har hög tillgänglighet för dina användare förutom att den hjälper dig att uppfylla efterlevnadskrav.


Webbprogram är allt skadliga attacker, till exempel datorn för tjänsten översvämningar, SQL-inmatningsattacker och cross-site skriptattacker mål. Dessa skadliga attacker kan orsaka service avbrott och dataförlust, utgöra en stor risk för web application ägare.

Det kan vara svårt att förhindra sådana attacker i programkoden och kräver ofta omfattande underhåll, korrigeringar och övervakning av flera skikt i programtopologin. Med en centraliserad brandvägg för webbaserade program blir det enklare att hantera säkerheten och programadministratörer får bättre möjligheter skydda mot intrång. Dessutom kan kan en WAF-lösning reagera på ett säkerhetshot snabbare genom att åtgärda en känd svaghet på en central plats i stället för att utföras i varje enskilt webbprogram.

WAF för ytterdörren är en global och centraliserad lösning. Den har distribuerats på Azure edge nätverksplatser i hela världen och varje inkommande begäran för ett WAF aktiverat webbprogram som levereras av åtkomsten kontrolleras på nätverksgränsen. Detta tillåter WAF för att förhindra skadliga attacker nära attack-källor, innan de kommer in ditt virtuella nätverk och erbjuder globala skydd i stor skala utan att offra prestanda. En WAF-princip som enkelt kan länkas till en profil som ytterdörren i din prenumeration och nya regler kan distribueras på några minuter, så att du kan snabbt svara på förändrade threat mönster.

![Brandvägg för Azure webbaserade program](./media/waf-overview/web-application-firewall-overview2.png)

Azure WAF kan också aktiveras med Application Gateway. Mer information finns i [Brandvägg för webbaserade program](../application-gateway/waf-overview.md).

## <a name="waf-policy-and-rules"></a>WAF-policy och regler

Du kan konfigurera en princip för WAF och associerar principen till en eller flera ytterdörren klientdelar för skydd. En WAF-princip består av två typer av säkerhetsregler:

- anpassade regler som skapats av kunden.

- hanterade regeluppsättningar som är en samling Azure-hanterade förkonfigurerade uppsättning regler.

När båda förekommer bearbetas anpassade regler före bearbetar regler i en hanterad regeluppsättning. En regel består av ett matchningsvillkor, en prioritet och en åtgärd. Åtgärdstyper som stöds är: TILLÅT BLOCK, LOG och OMDIRIGERING. Du kan skapa en helt anpassad princip som uppfyller dina specifika programkrav skydd genom att kombinera hanterade och anpassade regler.

Regler i en princip bearbetas i prioriterad ordning där prioritet är ett unikt heltal som definierar ordningen för regler som bearbetas. Mindre heltalsvärde som anger en högre prioritet och de utvärderas före regler med en högre heltal. När en regel har kopplats kan tillämpas på motsvarande åtgärd som har definierats i regeln på begäran. När en sådan matchning har bearbetats kan bearbetas regler med lägre prioritet inte ytterligare.

Ett webbprogram som levereras av ytterdörren kan ha endast en WAF-princip som är kopplade till den i taget. Du kan dock ha en ytterdörren konfiguration utan några WAF-principer som är associerade med den. Om det finns en WAF-princip, replikeras den till alla våra edge-platser för att garantera konsekvens i säkerhetsprinciper i hela världen.

## <a name="waf-modes"></a>WAF-lägen

WAF-princip kan konfigureras för att köras i följande två lägen:

- **Identifieringsläge:** När du kör i identifieringsläge WAF vidta inte några andra åtgärder än Övervakare och loggar begäran och dess matchade WAF-regel till WAF-loggar. Du kan aktivera loggningsdiagnostik för ytterdörren (när du använder portalen detta kan uppnås genom att gå till den **diagnostik** avsnitt i Azure-portalen).

- **Förhindringsläge:** När konfigurerad för att köras i förhindringsläge, tar WAF den angivna åtgärden om en begäran matchar en regel och om en matchning påträffas utvärderas inga fler regler med lägre prioritet. Alla matchade förfrågningar loggas också i WAF-loggar.

## <a name="waf-actions"></a>WAF-åtgärder

WAF-kunder kan välja att köra från någon av åtgärder när en begäran matchar ett villkor:

- **Tillåt:**  Begäran skickas via WAF och vidarebefordras till backend-server. Inga fler kan regler med lägre prioritet blockera den här begäran.
- **Blockera:** Begäran blockeras och WAF skickar ett svar till klienten utan att vidarebefordra begäran till backend-server.
- **LOG:**  Begäran loggas i WAF-loggar och WAF fortsätter utvärdering av regler med lägre prioritet.
- **Omdirigera:** WAF omdirigerar begäran till den angivna URI: N. Den URI som angetts är en nivå principinställning. När tjänsten har konfigurerats, alla begäranden som matchar den **omdirigera** åtgärden kommer att skickas till denna URI.

## <a name="waf-rules"></a>WAF-regler

En WAF-princip kan bestå av två typer av regler för anslutningssäkerhet - anpassade regler med kunden och hanterade regeluppsättningar Azure-hanterade förkonfigurerade uppsättning regler.

### <a name="custom-authored-rules"></a>Anpassade skapade regler

Du kan konfigurera anpassade regler WAF på följande sätt:

- **IP Tillåt lista och Blockeringslista:** Du kan konfigurera anpassade regler för att styra åtkomsten till ditt webbprogram utifrån en lista med klientens IP-adresser eller IP-adressintervall. Både IPv4 och IPv6-adresstyper stöds. Den här listan kan konfigureras för att blockera eller tillåta dessa begäranden där käll-IP matchar en IP-adress i listan.

- **Geografisk åtkomstkontroll:** Du kan konfigurera anpassade regler för att styra åtkomsten till ditt webbprogram utifrån landskoden som är associerade med en klients IP-adress.

- **HTTP-parametrarna-baserad åtkomstkontroll:** Du kan konfigurera anpassade regler baserat på sträng som matchar HTTP/HTTPS-begäranparametrar, t.ex frågesträngar, POST argument, begärande-URI, huvudet i begäran och Begärandetexten.

- **Begär metoden-baserad åtkomstkontroll:** Du kan konfigurera anpassade regler baserat på HTTP-frågemetoden för till exempel GET, PUT eller HEAD-begäran.

- **Begränsning för storlek:** Du kan konfigurera anpassade regler baserat på längden på vissa delar av en begäran, till exempel frågesträng, Uri, eller brödtext i begäran.

- **Betygsätt begränsande regler:** En regel för kontroll av priset är att begränsa onormalt hög trafik från klientens IP-adress. Du kan konfigurera ett tröskelvärde för antalet begäranden tillåts från klientens IP-adress under en tid för en minut. Detta skiljer sig från en IP-baserat på listan Tillåt/blockera anpassad regel som antingen kan alla eller blockerar alla begära från en klient-IP. Hastighetsbegränsning kan kombineras med ytterligare matchningsvillkor, till exempel HTTP (S)-parametrar som matchar för detaljerad kontroll.

### <a name="azure-managed-rule-sets"></a>Azure-hanterade regeluppsättningar

Azure-hanterade regeluppsättningar ger ett enkelt sätt att distribuera skydd mot en gemensam uppsättning säkerhetshot. Eftersom dessa regeluppsättningar hanteras av Azure, uppdateras reglerna som behövs för att skydda mot nya attacksignaturer. I den offentliga förhandsversionen innehåller Azure-hanterade standard regeluppsättning regler mot hot följande kategorier:

- Cross site scripting
- Java-attacker
- Lokala filinkludering
- PHP-inmatningsattacker.
- Fjärrkörning
- Införande av fjärrfil
- Sessionsfixering
- Skydd mot SQL-inmatning
- Protokollet angripare

Det lägre versionsnumret för standard regeluppsättning ökar när nya attacksignaturer läggs till i regeluppsättningen.
Regeln standarduppsättningen är aktiverat som standard i identifieringsläge i WAF-principer. Du kan inaktivera eller aktivera enskilda regler i standard regeluppsättning som uppfyller dina programkrav. Du kan också ange specifika åtgärder (TILLÅT/BLOCKERA/OMDIRIGERING/inloggning) per regel. Standardåtgärden är att BLOCKERA. Dessutom kan anpassade regler konfigureras i samma princip för WAF om du vill kringgå någon av de förkonfigurerade reglerna i standard regeluppsättning.
Anpassade regler tillämpas alltid innan regler i standard regeluppsättning. Om en begäran matchar en anpassad regel, motsvarande Regelåtgärd tillämpas och begäran blockeras eller skickas vidare till backend-server utan anrop av alla ytterligare anpassade regler eller reglerna i standard regeluppsättning. Dessutom har möjlighet att ta bort standard regeluppsättning från WAF-principer.


### <a name="bot-protection-rule-preview"></a>Regeln för skydd av bot (förhandsversion)

En hanterad regeluppsättning för Bot-skydd kan aktiveras för din WAF vidtar åtgärder för anpassade på begäranden från kända skadliga IP-adresser. IP-adresser kommer från Microsoft Hotinformationen feed. [Intelligent Security Graph](https://www.microsoft.com/security/operations/intelligence) Driver Microsofts hotintelligens och används av flera tjänster, inklusive Azure Security Center.

![Regeluppsättning för bot-skydd](./media/waf-front-door-configure-bot-protection/BotProtect2.png)

> [!IMPORTANT]
> Regeluppsättning för bot-skydd är för närvarande i offentlig förhandsversion och ingår ett serviceavtal för förhandsversionen. Vissa funktioner kanske inte stöds eller kan ha begränsad funktionalitet.  Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Om Bot skydd är aktiverat, loggas inkommande begäranden som matchar skadliga robotar klientens IP-adresser vid FrontdoorWebApplicationFirewallLog inloggning. Du kan komma åt WAF-loggar från storage-konto, event hub eller log analytics. 

## <a name="configuration"></a>Konfiguration

Konfigurera och distribuera alla WAF regeltyper stöds fullt ut med hjälp av Azure-portalen, REST API: er, Azure Resource Manager-mallar och Azure PowerShell.

## <a name="monitoring"></a>Övervakning

Övervakning av WAF vid ytterdörren är integrerat med Azure Monitor för att spåra aviseringar och enkelt övervaka trafik trender.

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [konfigurera en WAF-princip för åtkomsten med hjälp av Azure portal](waf-front-door-create-portal.md)