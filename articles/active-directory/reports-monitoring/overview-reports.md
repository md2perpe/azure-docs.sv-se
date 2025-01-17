---
title: Vad är rapporter i Azure Active Directory? | Microsoft Docs
description: En allmän översikt över Azure Active Directory-rapportering.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 6141a333-38db-478a-927e-526f1e7614f4
ms.service: active-directory
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 66ccf8014938e813676e9338e9bfc4eaa18f63f9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60286503"
---
# <a name="what-are-azure-active-directory-reports"></a>Vad är rapporter i Azure Active Directory?

Azure AD-rapporter (Azure Active Directory) tillhandahåller en omfattande översikt över aktivitet i din miljö. Med dessa data kan du:

- Avgör hur din app och dina tjänster används av dina användare
- Identifiera potentiella risker som påverkar hälsotillståndet för din miljö
- Felsöka problem som hindrar användare från att uträtta sitt arbete  

Rapportarkitekturen förlitar sig på två huvudsakliga pelare:

- [Säkerhetsrapporter](#security-reports)
- [Aktivitetsrapporter](#activity-reports)

![Rapportering](./media/overview-reports/01.png)


## <a name="security-reports"></a>Säkerhetsrapporter

Säkerhetsrapporter hjälper dig att skydda din organisations identiteter. Det finns två typer av säkerhetsrapporter:

- **Användare som har flaggats för risk** - Från [användare som har flaggats för risksäkerhetsrapporten](concept-user-at-risk.md) får du en översikt över användarkonton som kan ha drabbats.

- **Riskfyllda inloggningar** – med [ säkerhetsrapporten för riskfyllda inloggningar](concept-risky-sign-ins.md) får du en indikator för inloggningsförsök som kan ha utförts av någon som inte är tillförlitligt ägare för ett användarkonto. 

### <a name="what-azure-ad-license-do-you-need-to-access-a-security-report"></a>Vilken Azure AD-licens behöver du för att komma åt en säkerhetsrapport?  

Alla utgåvor av Azure AD ger rapporter över användare som har flaggats för risk och riskfyllda inloggningar. Nivån av rapportens detaljrikedom varierar dock mellan versionerna: 

- I **versionerna Azure Active Directory Free och Basic** har du en lista över användare som har flaggats för risk och riskfyllda inloggningar. 

- Utgåvan **Azure Active Directory Premium 1** har en utökad modell där du även kan utforska några av de underliggande riskhändelser som har identifierats för varje rapport. 

- Utgåvan **Azure Active Directory Premium 2** ger den mest detaljerade informationen om de underliggande riskhändelser och du kan konfigurera säkerhetsprinciper som automatiskt svarar på konfigurerade risknivåer.


## <a name="activity-reports"></a>Aktivitetsrapporter

Aktivitetsrapporter hjälper dig att förstå beteendet för användare i din organisation. Det finns två typer av aktivitetsrapporter i Azure AD:

- **Granskningsloggar** – [aktivitetsrapport för granskningsloggar ](concept-audit-logs.md) ger dig tillgång till historiken för varje aktivitet i din klient.

- **Inloggningar** – med [aktivitetsrapport för inloggningar ](concept-sign-ins.md) kan du bestämma vem som har utfört de uppgifter som rapporteras i granskningsloggarna.


### <a name="audit-logs-report"></a>Granskningsloggar 

[Granskningsloggarna](concept-audit-logs.md) ger dokumentation över systemaktiviteter för kontroll av överensstämmelse. Dessa data gör att det möjligt att hantera vanliga scenarier, till exempel:

- Någon i min klient har fått tillgång till en administratörsgrupp. Vem som gav användaren åtkomst? 

- Jag vill ha en lista över användare som loggar in på en viss app eftersom jag nyss publicerade appen och vill veta om den fungerar bra

- Jag vill veta hur många lösenordsåterställningar som sker i min klient


#### <a name="what-azure-ad-license-do-you-need-to-access-the-audit-logs-report"></a>Vilken Azure AD-licens behöver du för att komma åt granskningsloggar?  

Granskningsloggarna är tillgängliga för funktioner som du har licenser för. Om du har en licens för en specifik funktion har du också åtkomst till dess granskningslogg. Mer information finns i avsnittet om [Azure Active Directory-funktioner](https://www.microsoft.com/cloud-platform/azure-active-directory-features).   

### <a name="sign-ins-report"></a>Rapport över inloggningar

[Rapport för inloggningar ](concept-sign-ins.md) gör det möjligt för att få svar på frågor som:

- Vilket inloggningsmönster har en användare?
- Hur många användare har en användare loggat in under en vecka?
- Vad är status för dessa inloggningar?

#### <a name="what-azure-ad-license-do-you-need-to-access-the-sign-ins-activity-report"></a>Vilken Azure AD-licens behöver du för att komma åt inloggningsaktiviteter?  

Din klient måste ha en associerad Azure AD Premium-licens för att det ska gå att se alla rapporter om inloggningsaktiviteter.

## <a name="programmatic-access"></a>Programmässig åtkomst

Utöver användargränssnittet ger Azure AD också [programmässig åtkomst](concept-reporting-api.md) till rapporteringsdata via en uppsättning REST-baserade API:er. Du kan anropa API: erna från en mängd olika programmeringsspråk och verktyg. 

## <a name="next-steps"></a>Nästa steg

- [Rapport över riskfyllda inloggningar](concept-risky-sign-ins.md)
- [Granskningsloggar](concept-audit-logs.md)
- [Inloggningsrapport](concept-sign-ins.md)
