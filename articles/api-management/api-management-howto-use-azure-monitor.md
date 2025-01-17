---
title: Övervaka publicerade API:er i Azure API Management | Microsoft Docs
description: Följ stegen i den här kursen lär dig hur du övervakar din API i Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 06/15/2018
ms.author: apimpm
ms.openlocfilehash: c3148adc42cb4f899a87d894909eedff4c798575
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62127437"
---
# <a name="monitor-published-apis"></a>Övervaka publicerade API:er

Med Azure Monitor kan du visualisera, fråga, vidarebefordra, aktivera och vidta åtgärder för mått eller loggar från resurser i Azure.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Visa aktivitetsloggar
> * Visa diagnostikloggar
> * Visa mått för din API 
> * Konfigurera en varningsregel när din API hämtar obehöriga anrop

Följande video visar hur du övervakar API Management med Azure Monitor. 

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Monitor-API-Management-with-Azure-Monitor/player]

## <a name="prerequisites"></a>Förutsättningar

+ Lär dig [Azure API Management-terminologin](api-management-terminology.md).
+ Slutför följande snabbstart: [Skapa en Azure API Management-instans](get-started-create-service-instance.md).
+ Slutför även följande självstudie: [Importera och publicera ditt första API](import-and-publish.md).

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="view-metrics-of-your-apis"></a>Visa mått för dina API:er

API Management sänder ut mätvärden varje minut, vilket ger dig en insyn i realtid i API:ernas tillstånd och hälsa. Nedan följer en sammanfattning av några tillgängliga mått:

* Kapacitet (förhandsversion):  hjälper dig att fatta beslut om att uppgradera/nedgradera din APIM-tjänster. Måttet genereras per minut och återspeglar gatewaykapaciteten vid tidpunkten för rapporten. Måtten sträcker sig från 0 till 100 och beräknas utifrån gatewayens resurser som CPU och minnesanvändning.
* Totalt antal gatewaybegäranden: antalet API-begäranden under perioden. 
* Slutförda gatewaybegäranden: antalet API-begäranden som tog emot HTTP-svarskoder inklusive 304, 307 och under 301 (till exempel 200).
* Misslyckade gatewaybegäranden: antalet API-begäranden som tog emot felaktiga HTTP-svarskoder inklusive 400 och över 500.
* Ej auktoriserad begäran: antalet API-begäranden som tog emot HTTP-svarskoder som 401, 403 och 429.
* Övriga gatewaybegäranden: antalet API-begäranden som tog emot HTTP-svarskoder som inte tillhör någon av ovanstående kategorier (til exempel 418).

![måttdiagram](./media/api-management-azure-monitor/apim-monitor-metrics.png)

Så här får du åtkomst till mått:

1. Välj **Mått** på menyn långt ned på sidan.

    ![metrics](./media/api-management-azure-monitor/api-management-metrics-blade.png)

2. Från listrutan väljer du mått som du är intresserad av. Till exempel **Slutförda gatewaybegäranden**. Du kan också lägga till fler mått i diagrammet.
3. Diagrammet visar det totala antalet genomförda API-anrop.

## <a name="set-up-an-alert-rule-for-unauthorized-request"></a>Konfigurera en aviseringsregel för obehörig begäran

Du kan konfigurera för att ta emot varningar baserat på mått och aktivitetsloggar. Med Azure Monitor kan du konfigurera att en avisering ska göra följande när den utlöses:

* Skicka ett e-postmeddelande
* Anropa en webbhook
* Anropa en Azure Logic App

Så här konfigurerar du varningar:

1. Välj **aviseringar** på menyraden långt ned på sidan.

    ![aviseringar](./media/api-management-azure-monitor/alert-menu-item.png)

2. Klicka på en **ny aviseringsregel** för den här aviseringen.
3. Klicka på **Lägg till villkor**.
4. Välj **mått** i signaltyp nedrullningsbar listruta.
5. Välj **obehörig Gateway begära** signal att övervaka.

    ![aviseringar](./media/api-management-azure-monitor/signal-type.png)

6. I den **konfigurera signallogiken** visa, ange ett tröskelvärde efter som aviseringen ska aktiveras och klicka på **klar**.

    ![aviseringar](./media/api-management-azure-monitor/threshold.png)

7. Välj en befintlig åtgärdsgrupp eller skapa en ny. I exemplet nedan visas skickas ett e-postmeddelande till administratörerna. 

    ![aviseringar](./media/api-management-azure-monitor/action-details.png)

8. Ange ett namn, beskrivning av regeln och välj allvarlighetsgraden. 
9. Tryck på **skapa varningsregel**.
10. Prova att anropa konferens-API utan API-nyckel. Aviseringen utlöses och e-postmeddelandet skickas till administratörerna. 

## <a name="activity-logs"></a>Aktivitetsloggar

Aktivitetsloggar ger insikt i de åtgärder som vidtogs för dina API Management-tjänster. Med aktivitetsloggar kan du bestämma ”vad, vem och när” för skrivåtgärder (PUT, POST, DELETE) som ska vidtas för dina API Management-tjänster.

> [!NOTE]
> Aktivitetsloggar inkluderar inte läsåtgärder (GET), åtgärder som utförs i Azure-portalen eller via ursprungliga hanterings-API:er.

Du kan få åtkomst till aktivitetsloggar i API Management-tjänsten eller få åtkomst till loggar för alla dina Azure-resurser i Azure Monitor. 

![aktivitetsloggar](./media/api-management-azure-monitor/apim-monitor-activity-logs.png)

Så här visar du aktivitetsloggar:

1. Välj din APIM-tjänstinstans.
2. Klicka på **Aktivitetslogg**.

    ![aktivitetslogg](./media/api-management-azure-monitor/api-management-activity-logs-blade.png)

3. Välj önskat filtreringsomfång och klicka på **Använd**.

## <a name="diagnostic-logs"></a>Diagnostikloggar

Diagnostikloggar innehåller omfattande information om åtgärder och fel som är viktiga för granskning, samt i felsökningssyfte. Diagnostikloggar skiljer sig från aktivitetsloggar. Aktivitetsloggar ger insikt i de åtgärder som vidtogs för dina Azure-resurser. Diagnostikloggar ger information om åtgärder som din resurs har vidtagit.

Så här konfigurerar du diagnostikloggar:

1. Välj din APIM-tjänstinstans.
2. Klicka på **Diagnostikinställningar**.

    ![diagnostikloggar](./media/api-management-azure-monitor/api-management-diagnostic-logs-blade.png)

3. Klicka på **Slå på diagnostik**. Du kan arkivera diagnostikloggar tillsammans med mått i ett lagringskonto, strömma dem till en händelsehubb eller skicka dem till Azure Monitor-loggar. 

API Management tillhandahåller för närvarande diagnostikloggar (i batchar varje timme) om enskilda API-begäran där varje inmatning har följande schema:

```json
{  
    "isRequestSuccess" : "",
    "time": "",
    "operationName": "",
    "category": "",
    "durationMs": ,
    "callerIpAddress": "",
    "correlationId": "",
    "location": "",
    "httpStatusCodeCategory": "",
    "resourceId": "",
    "properties": {   
        "method": "", 
        "url": "", 
        "clientProtocol": "", 
        "responseCode": , 
        "backendMethod": "", 
        "backendUrl": "", 
        "backendResponseCode": ,
        "backendProtocol": "",  
        "requestSize": , 
        "responseSize": , 
        "cache": "", 
        "cacheTime": "", 
        "backendTime": , 
        "clientTime": , 
        "apiId": "",
        "operationId": "", 
        "productId": "", 
        "userId": "", 
        "apimSubscriptionId": "", 
        "backendId": "",
        "lastError": { 
            "elapsed" : "", 
            "source" : "", 
            "scope" : "", 
            "section" : "" ,
            "reason" : "", 
            "message" : ""
        } 
    }      
}  
```

| Egenskap   | Typ | Beskrivning |
| ------------- | ------------- | ------------- |
| isRequestSuccess | boolesk | True om HTTP-begäran slutfördes med svarsstatuskod inom intervallet 2xx eller 3xx |
| time | date-time | Tidstämpel för mottagande av HTTP-begäran av gatewayen |
| operationName | sträng | Konstantvärde ”Microsoft.ApiManagement/GatewayLogs” |
| category | sträng | Konstantvärde ”GatewayLogs” |
| durationMs | heltal | Antalet millisekunder från att gatewayen fick begäran till då svaret har skickats fullständigt |
| callerIpAddress | sträng | IP-adress för anropare av omedelbar gateway (kan vara en mellanhand) |
| correlationId | sträng | Unik http-begäranidentifierare tilldelad av API Management |
| location | sträng | Namn på Azure-regionen där gatewayen som behandlade begäran hittades |
| httpStatusCodeCategory | sträng | Kategori för http-svarsstatuskod: Lyckades (301 eller lägre eller 304 eller 307), Ej auktoriserad (401, 403, 429), Felaktig (400, mellan 500 och 600), Annat |
| resourceId | sträng | ID för API Management-resursen /SUBSCRIPTIONS/\<prenumeration > /RESOURCEGROUPS/\<resursgrupp >/PROVIDERS/MICROSOFT. APIMANAGEMENT/SERVICE/\<namn > |
| properties | objekt | Egenskaper för aktuell begäran |
| metod | sträng | HTTP-metod för inkommande begäran |
| url | sträng | URL för inkommande begäran |
| clientProtocol | sträng | HTTP-protokollversion för inkommande begäran |
| responseCode | heltal | Statuskod för HTTP-svar som skickas till klient |
| backendMethod | sträng | HTTP-metod för begäran som skickats till en serverdel |
| backendUrl | sträng | URL för begäran som skickats till en serverdel |
| backendResponseCode | heltal | Kod för HTTP-svaret togs emot av en serverdel |
| backendProtocol | sträng | HTTP-protokollversion för begäran som skickats till en serverdel | 
| requestSize | heltal | Antalet byte som tagits emot från en klient under bearbetning av begäran | 
| responseSize | heltal | Antalet byte som har skickats till en klient under bearbetning av begäran | 
| cache | sträng | Status för API Management cachemedverkan i behandling av begäran (t.ex. träff, miss, ingen) | 
| cacheTime | heltal | Antalet millisekunder som ägnats åt övergripande API Management cache-IO (ansluta, skicka och ta emot byte) | 
| backendTime | heltal | Antalet millisekunder som ägnats åt övergripande serverdels-IO (ansluta, skicka och ta emot byte) | 
| clientTime | heltal | Antalet millisekunder som ägnats åt övergripande klient-IO (ansluta, skicka och ta emot byte) | 
| apiId | sträng | API-entitetsidentifierare för aktuell begäran | 
| operationId | sträng | Åtgärdsentitetsidentifierare för aktuell begäran | 
| productId | sträng | Produktentitetsidentifierare för aktuell begäran | 
| userId | sträng | Användarentitetsidentifierare för aktuell begäran | 
| apimSubscriptionId | sträng | Prenumerationsentitetsidentifierare för aktuell begäran | 
| backendId | sträng | Serverdelentitetsidentifierare för aktuell begäran | 
| LastError | objekt | Bearbetningsfel för senaste begäran | 
| elapsed | heltal | Antalet förflutna millisekunder sedan gatewayen tog emot begäran till den tidpunkt då felet inträffade | 
| source | sträng | Namn på principen eller behandling av intern hanterare som orsakade felet | 
| scope | sträng | Omfattningen för det dokument som innehåller principen som orsakade felet | 
| section | sträng | Avsnittet för det dokument som innehåller principen som orsakade felet | 
| reason | sträng | Felorsak | 
| message | sträng | Felmeddelande | 

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Visa aktivitetsloggar
> * Visa diagnostikloggar
> * Visa mått för din API
> * Konfigurera en varningsregel när din API hämtar obehöriga anrop

Gå vidare till nästa kurs:

> [!div class="nextstepaction"]
> [Spåra anrop](api-management-howto-api-inspector.md)
