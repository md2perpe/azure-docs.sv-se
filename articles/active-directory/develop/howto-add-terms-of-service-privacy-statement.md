---
title: Användningsvillkor och sekretesspolicy för appar | Azure
description: Lär dig hur du kan konfigurera användningsvillkor och sekretesspolicy för appar som har registrerats för användning av Azure AD.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/22/2019
ms.author: ryanwi
ms.reviwer: lenalepa, sureshja
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: b0a01b50573405964b09339d03e84c62dbdd8582
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/01/2019
ms.locfileid: "67482854"
---
# <a name="how-to-configure-terms-of-service-and-privacy-statement-for-an-app"></a>Anvisningar: Konfigurera användningsvillkor och sekretesspolicy för en app

Utvecklare som skapar och hanterar appar som integreras med Azure Active Directory (Azure AD) och Microsoft-konton bör innehålla länkar till appens användningsvillkor och sekretesspolicy. Användningsvillkor och sekretesspolicy exponeras för användare via medgivande användarupplevelsen. De hjälper dina användare veta att de kan lita på din app. Användningsvillkor och sekretesspolicy är särskilt viktigt på användarinriktade appar för flera appar – appar som används av flera kataloger eller som är tillgängliga för alla microsoftkonto.

Du ansvarar för att skapa användningsvillkor och sekretesspolicy instruktionen dokument för din app och för att tillhandahålla de URL: er till dessa dokument. För appar för flera klienter som inte innehåller dessa länkar, visar medgivande användarupplevelsen för din app en avisering som kan hindra användare från att principer för din app.

> [!NOTE]
> * Appar för en enskild klient visas inte en avisering.
> * Om det saknas en eller båda två länkar, visas en avisering i din app.

## <a name="user-consent-experience"></a>Användarupplevelsen för medgivande

I följande exempel visas att användaren samtycke upplevelsen när användningsvillkor och sekretesspolicy konfigureras och när dessa länkar inte har konfigurerats.

![Skärmbilder med och utan en sekretesspolicy och användningsvillkor som har angetts](./media/howto-add-terms-of-service-privacy-statement/user-consent-exp-privacy-statement-terms-service.png)

## <a name="formatting-links-to-the-terms-of-service-and-privacy-statement-documents"></a>Formatering länkar till tillhörande användningsvillkor och sekretesspolicy instruktionen dokument

Innan du lägger till länkar till appens användningsvillkor och sekretesspolicy instruktionen dokument kan du kontrollera att URL: er följer dessa riktlinjer.

| Riktlinjer     | Beskrivning                           |
|---------------|---------------------------------------|
| Format        | Giltig URL                             |
| Giltigt scheman | HTTP och HTTPS<br/>Vi rekommenderar att HTTPS |
| Maxlängd    | 2048 tecken                       |

Exempel: `https://myapp.com/terms-of-service` och `https://myapp.com/privacy-statement`

## <a name="adding-links-to-the-terms-of-service-and-privacy-statement"></a>Att lägga till användningsvillkor och sekretesspolicy

När användningsvillkor och sekretesspolicy är redo kan du lägga till länkar till dessa dokument i din app med någon av följande metoder:

* [Via Azure portal](#azure-portal)
* [Med det app-objektet JSON](#app-object-json)
* [Med hjälp av MSGraph beta REST API](#msgraph-beta-rest-api)

### <a name="azure-portal"></a>Med Azure portal
Följ dessa steg i Azure-portalen.

1. Logga in på [Azure Portal](https://portal.azure.com/).
2. Navigera till den **Appregistreringar** och väljer din app.
3. Öppna den **varumärkesriktlinjer** fönstret.
4. Fyll i **villkoren av tjänstens URL** och **URL till sekretesspolicy** fält.
5. Spara ändringarna.

    ![Egenskaper för innehåller användningsvillkor och sekretesspolicy instruktionen URL: er](./media/howto-add-terms-of-service-privacy-statement/azure-portal-terms-service-privacy-statement-urls.png)

### <a name="app-object-json"></a>Med det app-objektet JSON

Om du föredrar att ändra objektet app JSON direkt, kan du använda manifest-redigeraren i Azure portal eller Programregistreringsportalen för att innehålla länkar till appens användningsvillkor och sekretesspolicy.

```json
    "informationalUrls": { 
        "termsOfService": "<your_terms_of_service_url>", 
        "privacy": "<your_privacy_statement_url>" 
    }
```

### <a name="msgraph-beta-rest-api"></a>Med hjälp av MSGraph beta REST API

För att programmässigt uppdatera alla dina appar, kan du använda MSGraph beta REST API för att uppdatera alla dina appar för att innehålla länkar till tillhörande användningsvillkor och sekretesspolicy instruktionen dokument.

```
PATCH https://graph.microsoft.com/beta/applications/{application id}
{ 
    "appId": "{your application id}", 
    "info": { 
        "termsOfServiceUrl": "<your_terms_of_service_url>", 
        "supportUrl": null, 
        "privacyStatementUrl": "<your_privacy_statement_url>", 
        "marketingUrl": null, 
        "logoUrl": null 
    }
}
```

> [!NOTE]
> * Var noga med att inte skriva över alla befintliga värden som du har tilldelat till någon av dessa fält: `supportUrl`, `marketingUrl`, och `logoUrl`
> * MSGraph beta REST-API fungerar bara när du loggar in med ett Azure AD-konto. Personliga Microsoft-konton stöds inte.
