---
title: Vanliga security attribut för Azure Resource Manager
description: En lista med vanliga security attribut för att utvärdera Azure Resource Manager
services: azure-resource-manager
author: msmbaldwin
manager: barbkess
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: mbaldwin
ms.openlocfilehash: a771d4c2ae22b7bf149c13c80fe5286ef52a4545
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66002265"
---
# <a name="security-attributes-for-azure-resource-manager"></a>Security attribut för Azure Resource Manager

Den här artikeln dokumenteras security-attribut som skapats i Azure Resource Manager.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Förebyggande

| Säkerhetsattributet | Ja/nej | Anteckningar |
|---|---|--|
| Vilande kryptering:<ul><li>Kryptering på serversidan</li><li>Kryptering på serversidan med Kundhanterade nycklar</li><li>Andra krypteringsfunktioner (t.ex på klientsidan, alltid krypterad, osv.)</ul>| Ja |  |
| Kryptering under överföring:<ul><li>Express route-kryptering</li><li>Virtuellt nätverk med kryptering</li><li>VNet-VNet-kryptering</ul>| Ja | HTTPS/TLS. |
| Kryptering viktiga hantering (CMK, BYOK osv.)| Gäller inte | Azure Resource Manager lagrar endast kontrolldata inget kund-innehåll. |
| Kolumnen filnivåkryptering (Azure-datatjänster)| Ja | |
| API-anrop som är krypterad| Ja | |

## <a name="network-segmentation"></a>Nätverkssegmentering

| Säkerhetsattributet | Ja/nej | Anteckningar |
|---|---|--|
| Stöd för Service-slutpunkt| Nej | |
| VNet-stöd för inmatning| Ja | |
| Isolering av nätverk och brandväggsfunktioner support| Nej |  |
| Tvingad tunneltrafik support| Nej |  |

## <a name="detection"></a>Detection (Identifiering)

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Azure övervakningsstöd (Log analytics, appinsikter osv.)| Nej | |

## <a name="identity-and-access-management"></a>Identitets- och åtkomsthantering

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Autentisering| Ja | [Azure Active Directory](/azure/active-directory) baserat.|
| Auktorisering| Ja | |


## <a name="audit-trail"></a>Granskningslogg

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Kontroll och hantering av plan loggning och granskning| Ja | Aktivitetsloggar exponerar alla skrivåtgärder (PUT, POST, ta bort) utförs på dina resurser. Se [visa aktivitetsloggar till granska åtgärder på resurser](resource-group-audit.md). |
| Data plan loggning och granskning| Gäller inte | |

## <a name="configuration-management"></a>Konfigurationshantering

| Säkerhetsattributet | Ja/nej | Anteckningar|
|---|---|--|
| Support för Configuration (versionshanteringen för konfiguration, osv.)| Ja |  |
