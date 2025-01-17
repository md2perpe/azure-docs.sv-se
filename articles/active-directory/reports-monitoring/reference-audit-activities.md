---
title: Referens för granskningsaktiviteter i Azure Active Directory (Azure AD) | Microsoft Docs
description: Få en översikt över vilka granskningsaktiviteter som kan loggas in dina granskningsloggar i Azure Active Directory (AD Azure).
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: a1f93126-77d1-4345-ab7d-561066041161
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 01/24/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 66dd017e8f78f1e93c96262b42dc084c165cdef7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60285489"
---
# <a name="azure-ad-audit-activity-reference"></a>Referens över granskningsaktiviteter i Azure AD

Med Azure Active Directory (Azure AD) rapporter får du den information du behöver för att fastställa hur din miljö.

Rapporteringsarkitekturen i Azure AD består av följande komponenter:

- **Aktivitetsrapporter** 
    - [Inloggningar](concept-sign-ins.md) – innehåller information om användningen av hanterade program och användare inloggningsaktiviteter
    - [Granskningsloggar](concept-audit-logs.md) – Ger spårbarhet via loggar för alla ändringar som gjorts via olika funktioner i Azure AD. 
    
- **Säkerhetsrapporter** 
    - [Riskfyllda inloggningar](concept-risky-sign-ins.md) – En riskfylld inloggning indikerar ett potentiellt inloggningsförsök av någon annan än användarkontots ägare. 
    - [Användare som har flaggats för risk](concept-user-at-risk.md) – En användare som har flaggats för risk indikerar att ett användarkonto kan ha komprometterats. 

I den här artikeln står vilka granskningsaktiviteter som kan loggas in dina granskningsloggar.

## <a name="access-reviews"></a>Åtkomstgranskningar

|Granskningskategori|Aktivitet|
|---|---|
|Åtkomstgranskningar|Åtkomstgranskning avslutades|
|Åtkomstgranskningar|Lägg till godkännare för begäran|
|Åtkomstgranskningar|Lägg till granskare i åtkomstgranskning|
|Åtkomstgranskningar|Tillämpa åtkomstgranskning|
|Åtkomstgranskningar|Skapa åtkomstgranskning|
|Åtkomstgranskningar|Skapa program|
|Åtkomstgranskningar|Skapa godkännande av begäran|
|Åtkomstgranskningar|Ta bort åtkomstgranskning|
|Åtkomstgranskningar|Ta bort program|
|Åtkomstgranskningar|Länka programkontroll|
|Åtkomstgranskningar|Publicera till Azure AD-åtkomstgranskningar|
|Åtkomstgranskningar|Ta bort granskare från åtkomstgranskning|
|Åtkomstgranskningar|Begär granskningsstopp|
|Åtkomstgranskningar|Begär tillämpning av granskningsresultat|
|Åtkomstgranskningar|Granska medlemskap i Rbac-roller|
|Åtkomstgranskningar|Granska apptilldelning|
|Åtkomstgranskningar|Granska gruppmedlemskap|
|Åtkomstgranskningar|Granska begäran om godkännande|
|Åtkomstgranskningar|Ta bort länk till programkontroll|
|Åtkomstgranskningar|Uppdatera åtkomstgranskning|
|Åtkomstgranskningar|Uppdatera Azure AD Access granskningar Publiceringsstatus|
|Åtkomstgranskningar|Uppdatera inställningar för e-postavisering om åtkomstgranskning|
|Åtkomstgranskningar|Uppdatera åtkomst granska antalet upprepningar för inställningen|
|Åtkomstgranskningar|Uppdatera upprepning åtkomstgranskningens varaktighet i dagar inställning|
|Åtkomstgranskningar|Uppdatera åtkomst granska upprepning end-typen inställning|
|Åtkomstgranskningar|Uppdatera åtkomst granska intervalltyp inställning|
|Åtkomstgranskningar|Uppdatera inställningar för påminnelse om åtkomstgranskning|
|Åtkomstgranskningar|Uppdatera program|
|Åtkomstgranskningar|Uppdatera godkännande av begäran|
|Åtkomstgranskningar|Användare som har inaktiverats|

## <a name="account-provisioning"></a>Kontoetablering

|Granskningskategori|Aktivitet|
|---|---|
|Programhantering|Hämta beviljanden av V2-programbehörighet|
|Programhantering|Hämta tjänstens huvudnamn för V2-program i aktuell klient|
|Programhantering|Uppdatera V1-program|
|Programhantering|Uppdatera V2-program|
|Programhantering|Uppdatera behörighetsbeviljande för V2-program|
|Programhantering|Lägg till OAuth2PermissionGrant|
|Programhantering|Lägg till approlltilldelning till tjänstens huvudnamn|

## <a name="application-proxy"></a>Programproxy

|Granskningskategori|Aktivitet|
|---|---|
|Programhantering|Lägga till ett program|
|Programhantering|Lägg till ägare till program|
|Programhantering|Lägg till ägare till tjänstens huvudnamn|
|Programhantering|Lägg till princip till tjänstens huvudnamn|
|Kataloghantering|Lägg till tjänstens huvudnamn|
|Kataloghantering|Lägg till autentiseringsuppgifter för tjänstens huvudnamn|
|Kataloghantering|Samtyck till program|
|Kataloghantering|Ta bort program|
|Kataloghantering|Ta bort program permanent|
|Kataloghantering|Ta bort OAuth2PermissionGrant|
|Kataloghantering|Ta bort approlltilldelning från tjänstens huvudnamn|
|Kataloghantering|Ta bort ägare från program|
|Resource|Ta bort ägare från tjänstens huvudnamn|
|Resource|Ta bort princip från tjänstens huvudnamn|
|Resource|Ta bort tjänstens huvudnamn|


## <a name="automated-password-rollover"></a>Automatiserad lösenordsförnyelse

|Granskningskategori|Aktivitet|
|---|---|
|Programhantering|Ta bort autentiseringsuppgifter för tjänstens huvudnamn|


## <a name="b2c"></a>B2C

|Granskningskategori|Aktivitet|
|---|---|
|Programhantering|Återställ program|
|Programhantering|Återkalla medgivande|
|Programhantering|Uppdatera programmet|
|Programhantering|Uppdatera externa hemligheter|
|Programhantering|Uppdatera tjänstens huvudnamn|
|Programhantering|Utfärda ett åtkomsttoken till programmet|
|Programhantering|Utfärda en auktoriseringskod till programmet|
|Programhantering|Utfärda ett id_token för programmet|
|Programhantering|Verifiera autentiseringsuppgifterna för lokalt konto|
|Programhantering|Verifiera användarautentisering|
|Programhantering|Lägga till V2-programbehörigheter|
|Programhantering|Lägga till en nyckel som baseras på en ASCII-hemlighet till en CPIM-nyckelcontainer|
|Programhantering|Lägg till en nyckel till en CPIM-nyckelcontainer|
|Programhantering|AdminPolicyDatas-SetResources|
|Programhantering|AdminUserJourneys-GetResources|
|Programhantering|AdminUserJourneys-RemoveResources|
|Autentisering|AdminUserJourneys-SetResources|
|Autentisering|Skapa IdentityProvider|
|Autentisering|Skapa V1-program|
|Autentisering|Skapa V2-program|
|Autentisering|Skapa en anpassad domän i klienten|
|Auktorisering|Skapa ny AdminUserJourney|
|Auktorisering|Skapa json för lokaliserad resurs|
|Auktorisering|Skapa ny anpassad IDP|
|Auktorisering|Skapa ny IDP|
|Auktorisering|Skapa eller uppdatera en B2C-katalogresurs|
|Auktorisering|Skapa princip|
|Auktorisering|Skapa trustFramework-princip|
|Auktorisering|Skapa trustFramework-princip med konfigurerbart prefix|
|Auktorisering|Skapa användarattribut|
|Auktorisering|CreateTrustFrameworkPolicy|
|Auktorisering|Skapar eller uppdaterar en ny AdminUserJourney|
|Auktorisering|Ta bort IDP|
|Auktorisering|Ta bort IdentityProvider|
|Auktorisering|Ta bort V1-program|
|Auktorisering|Ta bort V2-program|
|Auktorisering|Ta bort behörighetsbeviljande för V2-program|
|Auktorisering|Ta bort en B2C-katalogresurs|
|Auktorisering|Ta bort en CPIM-nyckelcontainer|
|Auktorisering|Ta bort trustFramework-princip|
|Auktorisering|Ta bort användarattribut|
|Auktorisering|Aktivera B2C-funktion|
|Auktorisering|Hämta B2C-katalogresurser i en prenumeration|
|Auktorisering|Hämta anpassad IDP|
|Auktorisering|Hämta IDP|
|Auktorisering|Hämta V1- och V2-program|
|Auktorisering|Hämta V1-program|
|Auktorisering|Hämta V1-program|
|Auktorisering|Hämta V2-program|
|Auktorisering|Hämta V2-program|
|Auktorisering|Hämta en B2C-katalogresurs|
|Auktorisering|Skapa en lista över anpassade domäner i klienten|
|Auktorisering|Hämta en användarresa|
|Auktorisering|Hämta tillåtna programanspråk för användarresa|
|Auktorisering|Hämta tillåtna självkontrollerade anspråk för användarresa|
|Auktorisering|Hämta tillåtna självkontrollerade anspråk för princip|
|Auktorisering|Hämta tillgänglig lista över utdataanspråk|
|Auktorisering|Hämta innehållsdefinitioner för användarresa|
|Auktorisering|Hämta idp:er för ett specifikt administratörsflöde|
|Auktorisering|Hämta nyckelcontainer för metadata för aktiv nyckel i JWK|
|Auktorisering|Hämta lista över alla administratörsflöden|
|Auktorisering|Hämta lista över taggar för alla administratörsflöden för alla användare|
|Auktorisering|Hämta lista över klienter för en användare|
|Auktorisering|Hämta självkontrollerade anspråk för lokala konton|
|Auktorisering|Hämta json för lokaliserad resurs|
|Auktorisering|Hämta åtgärder för resursprovidern Microsoft.AzureActiveDirectory|
|Auktorisering|Hämta principer|
|Auktorisering|Hämta princip|
|Auktorisering|Hämta resursegenskaper för en klient|
|Auktorisering|Hämta IDP-lista som stöds|
|Auktorisering|Hämta IDP-lista som stöds för användarresa|
|Auktorisering|Hämta klientinfo|
|Auktorisering|Hämta klienttillåtna funktioner|
|Auktorisering|Hämta klientdefinierad anpassad IDP-lista|
|Auktorisering|Hämta klientdefinierad IDP-lista|
|Auktorisering|Hämta klientdefinierad lokal IDP-lista|
|Auktorisering|Hämta klientinformation för en användare för att skapa en resurs|
|Auktorisering|Hämta klientlista|
|Auktorisering|Hämta tenantDomains|
|Auktorisering|Hämta standardkultur som stöds för CPIM|
|Auktorisering|Hämta information om ett administratörsflöde|
|Auktorisering|Hämta listan över UserJourneys för den här klienten|
|Auktorisering|Hämta uppsättningen med tillgängliga kulturer som stöds för CPIM|
|Auktorisering|Hämta trustFramework-princip|
|Auktorisering|Hämta trustFramework-princip som xml|
|Auktorisering|Hämta användarattribut|
|Auktorisering|Hämta användarattribut|
|Auktorisering|Hämta lista för användarresa|
|Auktorisering|GetIEFPolicies|
|Auktorisering|GetIdentityProviders|
|Auktorisering|GetTrustFrameworkPolicy|
|Auktorisering|Hämtar en CPIM-nyckelcontainer i jwk-format|
|Auktorisering|Hämtar lista över nyckelcontainrar i klienten|
|Auktorisering|Hämtar typ av klient|
|Auktorisering|MigrateTenantMetadata|
|Auktorisering|Korrigera IdentityProvider|
|Auktorisering|PutTrustFrameworkPolicy|
|Auktorisering|PutTrustFrameworkpolicy|
|Auktorisering|Ta bort en användarresa|
|Auktorisering|Återställa en säkerhetskopia av CPIM-nyckelcontainer|
|Auktorisering|Hämta beviljanden av V2-programbehörighet|
|Auktorisering|Hämta tjänstens huvudnamn för V2-program i aktuell klient|
|Auktorisering|Uppdatera anpassad IDP|
|Auktorisering|Uppdatera IDP|
|Auktorisering|Uppdatera den lokala IdP:n|
|Auktorisering|Uppdatera V1-program|
|Auktorisering|Uppdatera V2-program|
|Auktorisering|Uppdatera behörighetsbeviljande för V2-program|
|Auktorisering|Uppdatera princip|
|Auktorisering|Uppdatera användarattribut|
|Auktorisering|Ladda upp en CPIM-krypterad nyckel|
|Auktorisering|Användarauktorisering: API är inaktiverat för klient-featureset|
|Auktorisering|Användarauktorisering: Användaren beviljas åtkomst som ”Innehavaradministratör”|
|Auktorisering|Användarauktorisering: En användare har beviljats behörighet för autentiserade användare|
|Auktorisering|Kontrollera om B2C-funktionen är aktiverad|
|Auktorisering|Kontrollera om funktionen är aktiverad|
|Auktorisering|Skapa program|
|Auktorisering|Ta bort program|
|Auktorisering|Länka programkontroll|
|Auktorisering|Publicera till Azure AD-åtkomstgranskningar|
|Auktorisering|Ta bort länk till programkontroll|
|Auktorisering|Uppdatera program|
|Auktorisering|Inaktivera enkel inloggning för skrivbord|
|Auktorisering|Inaktivera skrivbords-Sso för en specifik domän|
|Auktorisering|Inaktivera programproxy|
|Auktorisering|Inaktivera direktautentisering|
|Auktorisering|Aktivera enkel inloggning för skrivbord|
|Kataloghantering|Aktivera skrivbords-Sso för en specifik domän|
|Kataloghantering|Aktivera Application Proxy|
|Kataloghantering|Aktivera direktautentisering|
|Kataloghantering|Skapa en anpassad domän i klienten|
|Kataloghantering|Aktivera B2C-funktion|
|Kataloghantering|Skapa en lista över anpassade domäner i klienten|
|Kataloghantering|Hämta resursegenskaper för en klient|
|Kataloghantering|Hämta klientinfo|
|Kataloghantering|Hämta klienttillåtna funktioner|
|Kataloghantering|Hämta tenantDomains|
|Nyckel|Hämtar typ av klient|
|Nyckel|Kontrollera om B2C-funktionen är aktiverad|
|Nyckel|Kontrollera om funktionen är aktiverad|
|Nyckel|Lägg till partner till företag|
|Nyckel|Lägg till overifierad domän|
|Nyckel|Lägg till verifierad domän|
|Nyckel|Skapa företag|
|Nyckel|Skapa företagsinställningar|
|Nyckel|Ta bort företagsinställningar|
|Nyckel|Flytta ned partner|
|Nyckel|Katalog borttagen|
|Annat|Katalog borttagen permanent|
|Annat|Katalog schemalagd för borttagning|
|Resource|Flytta upp företag till partner|
|Resource|Rensa egenskaper för Rights Management|
|Resource|Ta bort partner från företag|
|Resource|Ta bort overifierad domän|
|Resource|Ta bort verifierad domän|
|Resource|Ange företagsinformation|
|Resource|Ange Dirsync-funktion|
|Resource|Ange flaggan Dirsyncenabled|
|Resource|Ange partnerskap|
|Resource|Ange tröskelvärde för oavsiktlig borttagning|
|Resource|Ange företagstillåten dataplats|
|Resource|Ange företagsfunktionen Multinationell som aktiverad|
|Resource|Ange katalogfunktion för klientorganisation|
|Resource|Ange domänautentisering|
|Resource|Ange federationsinställningar för domän|
|Resource|Ange lösenordsprincip|
|Resource|Ange egenskaper för Rights Management|
|Resource|Uppdatera företag|
|Resource|Uppdatera företagsinställningar|
|Resource|Uppdatera domän|
|Resource|Verifiera domän|
|Resource|Verifiera e-postverifierad domän|
|Resource|Publicering|
|Resource|Uppdatera aviseringsinställningar|
|Resource|Uppdatera inställningar för sammandrag av veckan|
|Resource|Inaktivera lösenordstillbakaskrivning för katalog|
|Resource|Aktivera lösenordstillbakaskrivning för katalog|
|Resource|Lägg till approlltilldelning till grupp|
|Resource|Lägg till grupp|
|Resource|Lägg till medlem i grupp|
|Resource|Lägg till ägare till grupp|
|Resource|Skapa gruppinställningar|
|Resource|Ta bort grupp|
|Resource|Ta bort gruppinställningar|
|Resource|Slutför tillämpning av gruppbaserad licens för användare|
|Resource|Ta bort grupp permanent|
|Resource|Ta bort approlltilldelning från grupp|
|Resource|Ta bort medlem från grupp|
|Resource|Ta bort ägare från grupp|
|Resource|Återställ grupp|
|Resource|Ange grupplicens|
|Resource|Ange grupp som ska hanteras av användare|
|Resource|Börja tillämpa gruppbaserad licens för användare|
|Resource|Utlös omberäkning för grupplicens|
|Resource|Uppdatera grupp|
|Resource|Uppdatera gruppinställningar|
|Resource|Lägg till medlem|
|Resource|Skapa grupp|
|Resource|ta bort grupp|
|Resource|Ta bort medlem|
|Resource|Uppdatera grupp|
|Resource|Godkänn en väntande begäran om att gå med i en grupp|
|Resource|Avbryt en väntande begäran om att gå med i en grupp|
|Resource|Skapa princip för livscykelhantering|
|Resource|Ta bort en väntande begäran om att gå med i en grupp|
|Resource|Avvisa en väntande begäran om att gå med i en grupp|
|Resource|Förnya grupp|
|Resource|Begäran om att gå med i en grupp|
|Resource|Ange dynamiska gruppegenskaper|
|Resource|Uppdatera princip för livscykelhantering|
|Resource|Lägga till en nyckel som baseras på en ASCII-hemlighet till en CPIM-nyckelcontainer|
|Resource|Lägg till en nyckel till en CPIM-nyckelcontainer|
|Resource|Ta bort en CPIM-nyckelcontainer|
|Resource|Ta bort nyckelcontainer|
|Resource|Hämta nyckelcontainer för metadata för aktiv nyckel i JWK|
|Resource|Hämta metadata för nyckelcontainer|
|Resource|Hämtar en CPIM-nyckelcontainer i jwk-format|
|Resource|Hämtar lista över nyckelcontainrar i klienten|
|Resource|Återställa en säkerhetskopia av CPIM-nyckelcontainer|
|Resource|Spara nyckelcontainer|
|Resource|Ladda upp en CPIM-krypterad nyckel|
|Resource|Utfärda en auktoriseringskod till programmet|
|Resource|Utfärda ett id_token för programmet|


## <a name="core-directory"></a>Grundläggande katalog

|Granskningskategori|Aktivitet|
|---|---|
|Hantering av administrativ enhet|Ladda ned en enskild riskhändelsetyp|
|Hantering av administrativ enhet|Ladda ned administratörer och status för sammandrag av veckan|
|Hantering av administrativ enhet|Ladda ned alla riskhändelsetyper|
|Hantering av administrativ enhet|Ladda ned kostnadsfria användarriskhändelser|
|Hantering av administrativ enhet|Ladda ned användare som har flaggats för risk|
|Programhantering|Bearbetade batchinbjudningar|
|Programhantering|Överförda batchinbjudningar|
|Programhantering|Lägg till ägare till princip|
|Programhantering|Lägg till princip|
|Programhantering|Ta bort princip|
|Programhantering|Ta bort autentiseringsuppgifter för princip|
|Programhantering|Uppdatera princip|
|Programhantering|Ställ in MFA-registreingsprincip|
|Programhantering|Ange princip för inloggningsrisk|
|Programhantering|Ange princip för användarrisk|
|Programhantering|Godkänn användningsvillkoren|
|Programhantering|Skapa användningsvillkor|
|Programhantering|Ta bort användningsvillkor|
|Programhantering|Ta bort användningsvillkor|
|Programhantering|Redigera användningsvillkoren|
|Programhantering|Publicera användningsvillkoren|
|Programhantering|Avpublicera användningsvillkoren|
|Programhantering|Lägg till SSL-certifikat för program|
|Programhantering|Ta bort SSL-bindning|
|Programhantering|Registrera anslutningsapp|
|Programhantering|AdminPolicyDatas-RemoveResources|
|Programhantering|AdminPolicyDatas-SetResources|
|Programhantering|AdminUserJourneys-GetResources|
|Kataloghantering|AdminUserJourneys-RemoveResources|
|Kataloghantering|AdminUserJourneys-SetResources|
|Kataloghantering|Skapa IdentityProvider|
|Kataloghantering|Skapa ny AdminUserJourney|
|Kataloghantering|Skapa json för lokaliserad resurs|
|Kataloghantering|Skapa ny anpassad IDP|
|Kataloghantering|Skapa ny IDP|
|Kataloghantering|Skapa eller uppdatera en B2C-katalogresurs|
|Kataloghantering|Skapa princip|
|Kataloghantering|Skapa trustFramework-princip|
|Kataloghantering|Skapa trustFramework-princip med konfigurerbart prefix|
|Kataloghantering|Skapa användarattribut|
|Kataloghantering|CreateTrustFrameworkPolicy|
|Kataloghantering|Ta bort IDP|
|Kataloghantering|Ta bort IdentityProvider|
|Kataloghantering|Ta bort en B2C-katalogresurs|
|Kataloghantering|Ta bort trustFramework-princip|
|Kataloghantering|Ta bort användarattribut|
|Kataloghantering|Hämta B2C-katalogresurser i en resursgrupp|
|Kataloghantering|Hämta B2C-katalogresurser i en prenumeration|
|Kataloghantering|Hämta anpassad IDP|
|Kataloghantering|Hämta IDP|
|Kataloghantering|Hämta en B2C-katalogresurs|
|Kataloghantering|Hämta en användarresa|
|Kataloghantering|Hämta tillåtna programanspråk för användarresa|
|Kataloghantering|Hämta tillåtna självkontrollerade anspråk för användarresa|
|Kataloghantering|Hämta tillåtna självkontrollerade anspråk för princip|
|Kataloghantering|Hämta tillgänglig lista över utdataanspråk|
|Kataloghantering|Hämta innehållsdefinitioner för användarresa|
|Kataloghantering|Hämta idp:er för ett specifikt administratörsflöde|
|Kataloghantering|Hämta lista över alla administratörsflöden|
|Kataloghantering|Hämta lista över taggar för alla administratörsflöden för alla användare|
|Grupphantering|Hämta lista över klienter för en användare|
|Grupphantering|Hämta självkontrollerade anspråk för lokala konton|
|Grupphantering|Hämta json för lokaliserad resurs|
|Grupphantering|Hämta åtgärder för resursprovidern Microsoft.AzureActiveDirectory|
|Grupphantering|Hämta principer|
|Grupphantering|Hämta princip|
|Grupphantering|Hämta IDP-lista som stöds|
|Grupphantering|Hämta IDP-lista som stöds för användarresa|
|Grupphantering|Hämta klientdefinierad anpassad IDP-lista|
|Grupphantering|Hämta klientdefinierad IDP-lista|
|Grupphantering|Hämta klientdefinierad lokal IDP-lista|
|Grupphantering|Hämta klientinformation för en användare för att skapa en resurs|
|Grupphantering|Hämta standardkultur som stöds för CPIM|
|Grupphantering|Hämta information om ett administratörsflöde|
|Grupphantering|Hämta listan över UserJourneys för den här klienten|
|Grupphantering|Hämta uppsättningen med tillgängliga kulturer som stöds för CPIM|
|Grupphantering|Hämta trustFramework-princip|
|Grupphantering|Hämta trustFramework-princip som xml|
|Grupphantering|Hämta användarattribut|
|Principhantering|Hämta användarattribut|
|Principhantering|Hämta lista för användarresa|
|Principhantering|GetIEFPolicies|
|Principhantering|GetIdentityProviders|
|Principhantering|GetTrustFrameworkPolicy|
|Resource|MigrateTenantMetadata|
|Resource|Flytta resurser|
|Resource|Korrigera IdentityProvider|
|Resource|PutTrustFrameworkPolicy|
|Resource|PutTrustFrameworkpolicy|
|Resource|Ta bort en användarresa|
|Resource|Uppdatera anpassad IDP|
|Resource|Uppdatera IDP|
|Resource|Uppdatera den lokala IdP:n|
|Resource|Uppdatera en B2C-katalogresurs|
|Resource|Uppdatera princip|
|Resource|Uppdatera prenumerationsstatus|
|Rollhantering|Uppdatera användarattribut|
|Rollhantering|Verifiera flytta resurser|
|Rollhantering|Lägg till enhet|
|Rollhantering|Lägg till enhetskonfiguration|
|Rollhantering|Lägg till registrerad ägare till enhet|
|Rollhantering|Lägg till registrerade användare till enhet|
|Rollhantering|Ta bort enhet|
|Rollhantering|Ta bort enhetskonfiguration|
|Rollhantering|Enheten är inte längre kompatibel|
|Rollhantering|Enheten hanteras inte längre|
|Användarhantering|Ta bort registrerad ägare från enhet|
|Användarhantering|Ta bort registrerade användare från enhet|
|Användarhantering|Uppdatera enhet|
|Användarhantering|Uppdatera enhetskonfiguration|
|Användarhantering|Lägg till berättigad medlem i rollen|
|Användarhantering|Lägg till medlem till roll|
|Användarhantering|Lägg till rolltilldelning till rolldefinition|
|Användarhantering|Lägg till roll från mall|
|Användarhantering|Lägg till begränsad medlem till roll|
|Användarhantering|Ta bort behörig medlem från roll|
|Användarhantering|Ta bort medlem från roll|
|Användarhantering|Ta bort rolltilldelning från rolldefinition|
|Användarhantering|Ta bort begränsad medlem från roll|
|Användarhantering|Uppdatera roll|
|Användarhantering|AccessReview_Review|
|Användarhantering|AccessReview_Update|
|Användarhantering|ActivationAborted|
|Användarhantering|ActivationAborted|
|Användarhantering|ActivationAborted|
|Användarhantering|ActivationRequested|
|Användarhantering|Tillagt|
|Användarhantering|Tilldela|


## <a name="identity-protection"></a>Identitetsskydd

|Granskningskategori|Aktivitet|
|---|---|
|Kataloghantering|Upphöj|
|Kataloghantering|Borttaget|
|Kataloghantering|Ändringar av rollinställningar|
|Annat|ScanAlertsNow|
|Annat|Registrera|
|Annat|Ta bort behörighet|
|Annat|UpdateAlertSettings|
|Annat|UpdateCurrentState|
|Principhantering|Åtkomstgranskning avslutades|
|Principhantering|Lägg till godkännare för begäran|
|Principhantering|Lägg till granskare i åtkomstgranskning|
|Användarhantering|Tillämpa åtkomstgranskning|
|Användarhantering|Skapa åtkomstgranskning|


## <a name="invited-users"></a>Inbjudna användare

|Granskningskategori|Aktivitet|
|---|---|
|Annat|Skapa godkännande av begäran|
|Annat|Ta bort åtkomstgranskning|
|Användarhantering|Ta bort granskare från åtkomstgranskning|
|Användarhantering|Begär tillämpning av granskningsresultat|
|Användarhantering|Begär granskningsstopp|
|Användarhantering|Granska apptilldelning|
|Användarhantering|Granska gruppmedlemskap|
|Användarhantering|Granska medlemskap i Rbac-roller|


## <a name="microsoft-identity-manager-mim"></a>Microsoft Identity Manager (MIM)

|Granskningskategori|Aktivitet|
|---|---|
|Grupphantering|Granska begäran om godkännande|
|Grupphantering|Uppdatera åtkomstgranskning|
|Grupphantering|Uppdatera inställningar för e-postavisering om åtkomstgranskning|
|Grupphantering|Uppdatera åtkomst granska antalet upprepningar för inställningen|
|Grupphantering|Uppdatera upprepning åtkomstgranskningens varaktighet i dagar inställning|
|Användarhantering|Uppdatera åtkomst granska upprepning end-typen inställning|
|Användarhantering|Uppdatera åtkomst granska intervalltyp inställning|



## <a name="privileged-identity-management"></a>Privileged Identity Management

|Granskningskategori|Aktivitet|
|---|---|
|PIM|ActivationAborted|
|PIM|ActivationAborted|
|PIM|ActivationAborted|
|PIM|ActivationDenied|
|PIM|ActivationRequested|
|PIM|Tillagt|
|PIM|AddedOutsidePIM|
|PIM|Tilldela|
|PIM|DismissAlert|
|PIM|Upphöj|
|PIM|ReactivateAlert|
|PIM|Borttaget|
|PIM|RemovedOutsidePIM|
|PIM|Begär granskningsstopp|
|PIM|Ändringar av rollinställningar|
|PIM|ScanAlertsNow|
|PIM|Registrera|
|PIM|Ta bort tilldelning|
|PIM|Ta bort behörighet|
|PIM|UpdateAlertSettings|
|PIM|UpdateCurrentState|


## <a name="self-service-group-management"></a>Självbetjäning, grupphantering

|Granskningskategori|Aktivitet|
|---|---|
|Grupphantering|Återställ användarlösenord|
|Grupphantering|Återställ användare|
|Grupphantering|Genomför ändring av användarlösenord|
|Grupphantering|Ange användarhanterare|
|Grupphantering|Aktivera användares OATH-tokenmetadata|
|Grupphantering|Uppdatera StsRefreshTokenValidFrom-tidsstämpel|
|Grupphantering|Uppdatera externa hemligheter|
|Grupphantering|Uppdatera användare|
|Grupphantering|Administratören skapar ett tillfälligt lösenord|


## <a name="self-service-password-management"></a>Lösenordshantering via självbetjäning

|Granskningskategori|Aktivitet|
|---|---|
|Kataloghantering|Administratörer kräver att användaren återställer sitt lösenord|
|Kataloghantering|Tilldela extern användare till program|
|Användarhantering|E-postmeddelandet skickades inte, användaren har avbrutit prenumerationen|
|Användarhantering|Bjud in extern användare|
|Användarhantering|Lös in inbjudan för extern användare|
|Användarhantering|Generering av viral klientorganisation|
|Användarhantering|Generering av viral användare|
|Användarhantering|Användarlösenordsregistrering|
|Användarhantering|Återställning av användarlösenord|
|Användarhantering|Blockerad från lösenordsåterställning via självbetjäning|


## <a name="terms-of-use"></a>Användningsvillkor

|Granskningskategori|Aktivitet|
|---|---|
|Användningsvillkor|Godkänn användningsvillkoren|
|Användningsvillkor|Skapa användningsvillkor|
|Användningsvillkor|Ta bort användningsvillkor|
|Användningsvillkor|Ta bort ditt medgivande|
|Användningsvillkor|Ta bort användningsvillkor|
|Användningsvillkor|Redigera användningsvillkoren|
|Användningsvillkor|Upphör att gälla användningsvillkor|
|Användningsvillkor|Hårda ta bort användningsvillkor|
|Användningsvillkor|Publicera användningsvillkoren|
|Användningsvillkor|Avpublicera användningsvillkoren|


## <a name="next-steps"></a>Nästa steg

- [Azure AD-rapporter översikt](overview-reports.md).
- [Granskningsloggarna](concept-audit-logs.md). 
- [Programmatisk åtkomst till Azure AD-rapporter](concept-reporting-api.md)
