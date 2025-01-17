---
title: Vad är Azure service health meddelanden?
description: Meddelanden om hälsostatus för tjänsten kan du visa service health meddelanden som publiceras av Microsoft Azure.
author: dkamstra
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 4/12/2018
ms.author: dukek
ms.subservice: logs
ms.openlocfilehash: 87efa7442b0c67f2ee5f83b6b3e8ac8530ce5285
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079553"
---
# <a name="view-service-health-notifications-by-using-the-azure-portal"></a>Visa aviseringar om hälsotillståndet för tjänsten med hjälp av Azure-portalen

Meddelanden om hälsostatus för tjänsten har publicerats av Azure och innehåller information om resurser i din prenumeration. Dessa aviseringar är en underklass till aktiviteten logghändelser och finns också i aktivitetsloggen. Meddelanden om hälsostatus för tjänsten kan vara information eller användbara, beroende på klassen.

Det finns olika typer av meddelanden om hälsostatus för tjänsten:  

- **Åtgärd krävs:** Azure märker något annorlunda sker på ditt konto och arbeta med dig för att åtgärda detta. Azure skickar ett meddelande, antingen med information om de åtgärder som du behöver ta eller kontakta Azure-tekniker eller support.  
- **Assisterad återställning:** En händelse har inträffat och tekniker har bekräftat att du fortfarande har påverkan. Azure engineering behöver arbeta med dig direkt för att återställa dina tjänster till full hälsa.  
- **Incident:** En händelse som påverkar tjänsten för närvarande påverkar en eller flera resurser i din prenumeration.  
- **Underhåll:** Aktiviteten planerat underhåll som kan påverka en eller flera av resurserna i din prenumeration.  
- **Information:** Använd potentiella optimeringar som kan hjälpa dig att förbättra din resurs. 
- **Säkerhet:** Brådskande säkerhetsrelaterad information om dina lösningar som körs på Azure.

Varje avisering om tjänstens hälsa innehåller information om omfattning och påverkan till dina resurser. Informationen innehåller:

Egenskapsnamn | Beskrivning
-------- | -----------
kanaler | En av följande värden: **Administratören** eller **åtgärden**.
correlationId | Vanligtvis ett GUID i formatet för strängen. Händelser som hör till samma åtgärd vanligtvis dela samma correlationId.
eventDataId | Den unika identifieraren för en händelse.
EventName | Rubriken på en händelse.
nivå | Nivån för en händelse
resourceProviderName | Namnet på resursprovidern för resursen som påverkas.
ResourceType| Typ av resurs för resursen som påverkas.
subStatus | Vanligtvis HTTP-statuskod för motsvarande RESTEN anropa, men kan även innehålla andra strängar som beskriver en understatus. Exempel: OK (HTTP-statuskod: 200) skapade (HTTP-statuskod: 201), godkänt (HTTP-statuskod: 202), inget innehåll (HTTP-statuskod: 204), felaktig begäran (HTTP-statuskod: 400) hittades inte (HTTP-statuskod: 404) konflikt (HTTP-statuskod: 409), interna serverfel (HTTP-statuskod: 500), tjänsten är inte tillgänglig (HTTP-statuskod: 503) och Gateway-Timeout (HTTP-statuskod: 504).
eventTimestamp | Tidsstämpel när händelsen skapades av tjänsten Azure behandlingen av begäran som motsvarar händelsen.
submissionTimestamp | Tidsstämpel när händelsen blev tillgängliga för frågor.
subscriptionId | Azure-prenumerationen där den här händelsen loggades.
status | Sträng som anger status för åtgärden. Vissa vanliga värden är: **Igång**, **pågår**, **lyckades**, **misslyckades**, **Active**, och **löst**.
operationName | Namnet på åtgärden.
category | Den här egenskapen är alltid **ServiceHealth**.
resourceId | Resurs-ID för resursen som påverkas.
Properties.title | Lokaliserade rubriken för den här kommunikationen. Engelska är standard.
Properties.communication | Lokaliserad information om kommunikationen med HTML-kod. Engelska är standard.
Properties.incidentType | En av följande värden: **Åtgärd krävs**, **endast i informationssyfte**, **Incident**, **Underhåll**, eller **Security**.
Properties.trackingId | Incidenten som den här händelsen är associerad. Används för att korrelera händelser relaterade till en incident.
Properties.impactedServices | En kommenterad JSON-blob som beskriver de tjänster och regioner som påverkas av incidenten. Egenskapen innehåller en lista över tjänster, som har en **ServiceName**, och en lista över berörda regioner som har en **RegionName**.
Properties.defaultLanguageTitle | Kommunikation på engelska.
Properties.defaultLanguageContent | Kommunikation på engelska som oformaterad text eller HTML-kod.
Properties.Stage | Möjliga värden för **Incident**, och **Security** är **aktiv,** **löst** eller **RCA**. För **åtgärd krävs** eller **information** är det enda värdet **Active.** För **Underhåll** de är: **Aktiva**, **planerat**, **InProgress**, **har avbrutits**, **schemaläggas**, **löst**, eller **fullständig**.
Properties.communicationId | Kommunikation som är associerad till den här händelsen.

### <a name="details-on-service-health-level-information"></a>Information om service health nivåinformation

**Åtgärd krävs** (properties.incidentType == åtgärd krävs)
- Information - administratör åtgärd som krävs för att förhindra påverkan på befintliga tjänster
    
**Underhåll** (properties.incidentType == underhåll)
- Varning - nödfall Underhåll
- Information - standard planerat underhåll

**Information** (properties.incidentType == Information)
- Information - administratör kan krävas för att förhindra påverkan på befintliga tjänster

**Security** (properties.incidentType == säkerhet)
- Fel - utökas problem med att komma åt flera tjänster över flera regioner som påverkar en rad olika kunder.
- Varning - problem med åtkomst till specifika tjänster och/eller specifika regioner som påverkar en delmängd av kunderna.
- Information - problem som påverkar hanteringsåtgärder och/eller svarstid, inte påverkar tjänstens tillgänglighet.

**Tjänsten problem** (properties.incidentType == Incident)
- Fel - utökas problem med att komma åt flera tjänster över flera regioner som påverkar en rad olika kunder.
- Varning - problem med åtkomst till specifika tjänster och/eller specifika regioner som påverkar en delmängd av kunderna.
- Information - problem som påverkar hanteringsåtgärder och/eller svarstid, inte påverkar tjänstens tillgänglighet.
