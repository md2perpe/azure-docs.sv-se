---
title: Ensidesapplikation (appens kod konfiguration) - Microsoft identity-plattformen
description: Lär dig hur du skapar ett enkelsidigt program (konfiguration av appens kod)
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: nacanuma
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: b71454fc553a0f81c26426a6a9588f15d5311e38
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65406421"
---
# <a name="single-page-application---code-configuration"></a>Ensidesprogram - kod-konfiguration

Lär dig hur du konfigurerar koden för din ensidesapplikation (SPA).

## <a name="msal-libraries-supporting-implicit-flow"></a>MSAL bibliotek stöder implicit flöde

Microsoft identity-plattformen ger MSAL.js-biblioteket för att stödja det implicita flödet med hjälp av branschen rekommenderade metoder för säker.  

Bibliotek som har stöd för implicit flöde är:

| MSAL bibliotek | Beskrivning |
|--------------|--------------|
| ![MSAL.js](media/sample-v2-code/logo_js.png) <br/> [MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)  | Vanlig JavaScript-bibliotek för användning i alla klienten sida-webbappar som skapats med hjälp av JavaScript- eller SPA ramverk som Angular, Vue.js, React.js osv. |
| ![MSAL Angular](media/sample-v2-code/logo_angular.png) <br/> [MSAL Angular](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/README.md) | Omslutning för MSAL.js Kärnbibliotek för att förenkla användning i en sida-appar som skapats med Angular-ramverket. Det här biblioteket är en förhandsversion och har [kända problem](https://github.com/AzureAD/microsoft-authentication-library-for-js/issues?q=is%3Aopen+is%3Aissue+label%3Aangular) med vissa Angular versioner och webbläsare. |

## <a name="application-code-configuration"></a>Kod för programkonfiguration

I MSAL biblioteket skickas registreringsinformation för programmet som konfigurationen under initieringen biblioteket.

### <a name="javascript"></a>JavaScript

```javascript
// Configuration object constructed.
const config = {
    auth: {
        clientId: 'your_app_id',
        redirectUri: "your_app_redirect_uri" //defaults to application start page
    }
}

// create UserAgentApplication instance
const userAgentApplication = new UserAgentApplication(config);
```
Mer information om de konfigurerbara alternativ som är tillgängliga finns i [initiering av program med MSAL.js](msal-js-initializing-client-applications.md).

### <a name="angular"></a>Angular

```javascript
//In app.module.ts
@NgModule({
  imports: [ MsalModule.forRoot({
                clientId: 'your_app_id'
            })]
         })

  export class AppModule { }
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Inloggning och utloggning](scenario-spa-sign-in.md)
