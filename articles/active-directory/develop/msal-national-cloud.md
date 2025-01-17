---
title: Använd Microsoft Authentication Library (MSAL) i nationella moln – Microsoft identity-plattformen
description: 'Microsoft Authentication Library (MSAL) gör det möjligt för apputvecklare att hämta token för att kunna anropa säkra webb-API: er. Dessa webb-API: er kan vara Microsoft Graph, andra Microsoft-APIs, partnerwebb-API: er eller dina egna webb-API. MSAL har stöd för flera arkitekturer för program och plattformar.'
services: active-directory
documentationcenter: dev-center-name
author: negoe
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: negoe
ms.reviewer: nacanuma
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: f9958356cae3c486ecf68e280f33d63c6a537b14
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66235267"
---
# <a name="use-msal-in-a-national-cloud-environment"></a>Om du Använd MSAL i en nationell molnmiljö

[Nationella moln](authentication-national-cloud.md) är fysiskt isolerat instanser av Azure. Dessa områden av Azure att säkerställa att dataplacering och landsbaserad placering efterlevnad datakrav respekteras inom geografiska gränser.

Förutom Microsoft worldwide cloud kan programutvecklare i nationella moln att hämta token för att autentisera och anropa säkra webb-API: er i Microsoft Authentication Library (MSAL). Dessa webb-API: er kan vara Microsoft Graph eller andra Microsoft-APIs.

Azure Active Directory (Azure AD) har distribuerats i följande nationella moln, inklusive det globala molnet:  

- Azure Government
- Azure Kina 21Vianet
- Azure Tyskland

Den här guiden visar hur du loggar in att fungera och skolkonton, hämta en åtkomsttoken och anropa Microsoft Graph API i den [Azure Government-molnet](https://azure.microsoft.com/global-infrastructure/government/) miljö.

## <a name="prerequisites"></a>Nödvändiga komponenter

Innan du börjar bör du kontrollera att du uppfyller dessa krav.

### <a name="choose-the-appropriate-identities"></a>Välj lämplig identiteter

[Azure Government](https://docs.microsoft.com/azure/azure-government/) program kan använda Azure AD-Government-identiteter och offentlig Azure AD-identiteter för att autentisera användare. Eftersom du kan använda någon av dessa identiteter, måste du bestämma vilka utfärdarslutpunkten som du ska välja för ditt scenario:

- Azure AD Public: Vanligaste om din organisation redan har en offentlig Azure AD-klient till support för Office 365 (offentlig eller GCC) eller ett annat program.
- Azure AD Government: Vanligaste om din organisation redan har en Azure Government för AD-klient till support för Office 365 (GCC hög eller DoD) eller skapar en ny klient i Azure AD-Government.

När du har bestämt är ett särskilt beaktande där du genomför appregistreringen. Om du väljer offentlig Azure AD-identiteter för Azure Government-program, måste du registrera programmet i din offentlig Azure AD-klient.

### <a name="get-an-azure-government-subscription"></a>Skaffa en Azure Government-prenumeration

Om du vill skaffa en Azure Government-prenumeration, se [hantera och ansluta till din prenumeration i Azure Government](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-subscriptions).

Om du inte har en Azure Government-prenumeration kan du skapa en [kostnadsfritt konto](https://azure.microsoft.com/global-infrastructure/government/request/) innan du börjar.

## <a name="javascript"></a>JavaScript

### <a name="step-1-register-your-application"></a>Steg 1: Registrera ditt program

1. Logga in på [Azure Portal](https://portal.azure.us/).
    
   Azure portal-slutpunkter för andra nationella moln finns i [App registrering slutpunkter](authentication-national-cloud.md#app-registration-endpoints).

1. Om ditt konto får du tillgång till fler än en klient, Välj ditt konto i det övre högra hörnet och ange din portal-session med önskade Azure AD-klient.
1. Gå till den [appregistreringar](https://aka.ms/ra/ff) sidan på Microsoft identity-plattformen för utvecklare.
1. När sidan **Registrera ett program** visas anger du ett namn för programmet.
1. Under **stöds kontotyper**väljer **konton i alla organisationskatalog**.
1. I den **omdirigerings-URI** väljer den **Web** plattform och ange värdet till programmets URL baserat på din webbserver. Se nästa avsnitt för anvisningar om hur du anger och hämtar omdirigerings-URL i Visual Studio och noden.
1. Välj **Registrera**.
1. På appens sida **Översikt** antecknar du värdet för **Application (client) ID** (Program-ID (klient)).
1. Den här självstudien måste du aktivera den [implicit beviljande av flow](v2-oauth2-implicit-grant-flow.md). I den vänstra rutan i det registrerade programmet, väljer **autentisering**.
1. I **avancerade inställningar**under **Implicit beviljande**väljer den **ID-token** och **åtkomsttoken** kryssrutorna. ID-token och åtkomsttoken krävs eftersom den här appen behöver logga in användare och anropa ett API.
1. Välj **Spara**.

### <a name="step-2--set-up-your-web-server-or-project"></a>Steg 2:  Konfigurera din webbserver eller projekt

- [Hämta projektfilerna](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip) för en lokal webbserver, till exempel noden.

  eller

- [Ladda ned Visual Studio-projektet](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/vsquickstart.zip).

Gå sedan till [konfigurera din JavaScript SPA](#step-4-configure-your-javascript-spa) konfigurera kodexemplet innan den körs.

### <a name="step-3-use-the-microsoft-authentication-library-to-sign-in-the-user"></a>Steg 3: Använd Microsoft Authentication Library för att logga in användaren

Följ stegen i den [JavaScript självstudien](tutorial-v2-javascript-spa.md#create-your-project) att skapa projektet och integrera med MSAL för att logga in användaren.

### <a name="step-4-configure-your-javascript-spa"></a>Steg 4: Konfigurera din JavaScript SPA

I den `index.html` filen som skapades under projektkonfiguration, lägga till registreringsinformationen som programmet. Lägg till följande kod högst upp i den `<script></script>` taggar i brödtexten i din `index.html` fil:

```javascript
const msalConfig = {
    auth:{
        clientId: "Enter_the_Application_Id_here",
        authority: "https://login.microsoftonline.us/Enter_the_Tenant_Info_Here",
        }
}

const graphConfig = {
        graphEndpoint: "https://graph.microsoft.us",
        graphScopes: ["user.read"],
}

// create UserAgentApplication instance
const myMSALObj = new UserAgentApplication(msalConfig);
```

I koden:

- `Enter_the_Application_Id_here` är den **(klient)-ID: T** värdet för det program som du har registrerat.
- `Enter_the_Tenant_Info_Here` är inställd på något av följande alternativ:
    - Om ditt program stöder **konton i den här organisationskatalog**, Ersätt detta värde med klient-ID eller klientorganisationens namn (till exempel contoso.microsoft.com).
    - Om ditt program stöder **konton i alla organisationskatalog**, Ersätt detta värde med `organizations`.
    
    Autentiseringsslutpunkter för nationella moln finns i [autentiseringsslutpunkter för Azure AD](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud#azure-ad-authentication-endpoints).

    > [!NOTE]
    > Personliga Microsoft-konton stöds inte i nationella moln.
  
- `graphEndpoint` är Microsoft Graph-slutpunkt för Microsoft-molnet för amerikanska myndigheter.

   Du hittar Microsoft Graph-slutpunkter för nationella moln [Microsoft Graph-slutpunkter i nationella moln](https://docs.microsoft.com/graph/deployments#microsoft-graph-and-graph-explorer-service-root-endpoints).

## <a name="net"></a>.NET

Du kan använda MSAL.NET för att logga in användare, hämta token och anropa Microsoft Graph API i nationella moln.

Följande självstudier visar hur man skapar en .NET Core 2.2 MVC-webbapp. Appen använder OpenID Connect för att logga in användare med ett arbets- och skolkonton konto i en organisation som hör till ett nationella moln.

- Logga in användare och hämta token genom att följa [den här självstudien](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-4-Sovereign#build-an-aspnet-core-web-app-signing-in-users-in-sovereign-clouds-with-the-microsoft-identity-platform).
- För att anropa Microsoft Graph API, Följ [den här självstudien](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-4-Sovereign-Call-MSGraph#using-the-microsoft-identity-platform-to-call-the-microsoft-graph-api-from-an-an-aspnet-core-2x-web-app-on-behalf-of-a-user-signing-in-using-their-work-and-school-account-in-microsoft-national-cloud).

## <a name="next-steps"></a>Nästa steg

Läs mer om:

- [Azure Government](https://docs.microsoft.com/azure/azure-government/)
- [Azure China 21Vianet](https://docs.microsoft.com/azure/china/)
- [Azure Germany](https://docs.microsoft.com/azure/germany/)