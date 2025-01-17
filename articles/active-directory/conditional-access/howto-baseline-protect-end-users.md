---
title: Baslinjeprincip slutanvändaren protection (förhandsgranskningsversion) – Azure Active Directory
description: Princip för villkorlig åtkomst att kräva multifaktorautentisering för användare
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 05/16/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: f2644e0e35139ac470b89f6af1b95cf510f60a0a
ms.sourcegitcommit: d3b1f89edceb9bff1870f562bc2c2fd52636fc21
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/04/2019
ms.locfileid: "67561006"
---
# <a name="baseline-policy-end-user-protection-preview"></a>Baslinjeprincip: Slutanvändaren protection (förhandsgranskningsversion)

Brukar vi tror att administratörskonton är de enda kontona som behöver skydd med multifaktorautentisering (MFA). Administratörer har bred åtkomst till känslig information och kan göra ändringar i inställningar för hela prenumerationen. Dock brukar illvilliga aktörer target slutanvändare. När du har fått åtkomst, kan dessa illvilliga aktörer begär åtkomst till hemlig information åt den ursprungliga kontoinnehavaren eller hämta hela katalogen för att utföra en nätfiske attack på hela organisationen. En vanlig metod för att förbättra skyddet för alla användare är att kräva en starkare form av verifiering för kontot, till exempel multifaktorautentisering (MFA).

Användare bör inte uppmanas varje gång de loggar in för att uppnå en rimlig balans mellan säkerhet och användbarhet. Autentiseringsförfrågningar som återspeglar normal användare fungerar, till exempel loggar in från samma enhet från samma plats, har en låg risken för angrepp. Endast inloggningar som bedöms vara riskfyllda och visa egenskaperna för någon obehörig ska ange MFA utmaningar.

Slutanvändaren protection är en riskbaserad MFA [baslinjeprincip](concept-baseline-protection.md) som skyddar alla användare i en katalog, inklusive alla administratörsroller. När den här principen kräver att alla användare att registrera för MFA med Autentiseringsappen. Användare kan ignorera meddelandet för MFA-registrering i 14 dagar, varefter de kommer att blockeras från att logga in tills de registrera för MFA. När du har registrerats för MFA, uppmanas användare för MFA endast under riskfyllda inloggningsförsök. Komprometterade användarkonton blockeras tills deras lösenord återställs och riskhändelser har avslagits.

> [!NOTE]
> Den här principen gäller för alla användare, inklusive gästkonton och kommer att utvärderas vid inloggning till alla program.

## <a name="recovering-compromised-accounts"></a>Återställa komprometterade konton

För att skydda våra kunder, hittas Microsofts läckta autentiseringsuppgifter offentligt tillgängliga användarnamn/lösenord par. Om de matchar en av våra användare skyddar vi kontot omedelbart. Användare med läckta autentiseringsuppgifter bekräftas äventyras. Dessa användare kommer att blockeras från att logga in förrän deras lösenord har återställts.

Användare som har tilldelats en licens för Azure AD Premium kan återställa åtkomst via lösenordsåterställning via självbetjäning (SSPR) om funktionen har aktiverats i sin katalog. Användare utan en premiumlicens som blir blockerad måste kontakta en administratör för att utföra en manuell lösenordsåterställning och Stäng riskhändelse flaggad användare.

### <a name="steps-to-unblock-a-user"></a>Steg för att avblockera en användare

Bekräfta att användaren har blockerats av principen genom att undersöka användarens inloggning loggar.

1. En administratör behöver logga in på den **Azure-portalen** och gå till **Azure Active Directory** > **användare** > Klicka på namnet på användaren och gå att inloggningar.
1. Om du vill initiera lösenordsåterställning på en blockerad användare, en administratör behöver navigera till **Azure Active Directory** > **användare som har flaggats för risk**
1. Klicka på den användare vars konto har blockerats för att visa information om användarens senaste inloggning aktivitet.
1. Klicka på Återställ lösenord om du vill tilldela ett tillfälligt lösenord som måste ändras vid nästa inloggning.
1. Klicka på Stäng alla händelser för att återställa användarens riskpoäng.

Användaren kan nu logga in, återställa sina lösenord och få åtkomst till programmet.

## <a name="deployment-considerations"></a>Distributionsöverväganden

Eftersom den **slutanvändaren protection** principen gäller för alla användare i din katalog, flera saker som behöver göras för att säkerställa en smidig distribution. Dessa överväganden omfattar identifiera användare och tjänsten principer i Azure AD som inte kan eller inte utför MFA, samt program och klienter som används av din organisation och som inte stöder modern autentisering.

### <a name="legacy-protocols"></a>Äldre protokoll

Äldre autentiseringsprotokoll (IMAP, SMTP-, POP3 osv.) som används av e-postklienter för autentisering. Dessa protokoll har inte stöd för MFA.  De flesta av de konto kompromisser som setts av Microsoft orsakas av illvilliga aktörer utför attacker mot äldre protokoll försök att kringgå MFA. För att säkerställa att MFA krävs vid inloggning till ett konto och obehöriga inte kringgå MFA, blockerar den här principen alla autentiseringsbegäranden administratörskonton från äldre protokoll.

> [!WARNING]
> Innan du aktiverar den här principen måste du kontrollera att användarna inte använder äldre autentiseringsprotokoll. Finns i artikeln [så här: Blockera äldre autentisering till Azure AD med villkorlig åtkomst](howto-baseline-protect-legacy-auth.md#identify-legacy-authentication-use) för mer information.

## <a name="enable-the-baseline-policy"></a>Aktivera baslinjeprincip för

Principen **baslinjeprincip: Slutanvändaren protection (förhandsgranskningsversion)** är förkonfigurerad och visas högst upp när du navigerar till bladet för villkorlig åtkomst i Azure-portalen.

Aktivera den här principen och skydda dina användare:

1. Logga in på den **Azure-portalen** som global administratör, säkerhetsadministratör eller administratör för villkorsstyrd åtkomst.
1. Bläddra till **Azure Active Directory** > **villkorlig åtkomst**.
1. Välj i listan med principer, **baslinjeprincip: Slutanvändaren protection (förhandsgranskningsversion)** .
1. Ange **aktiverar principen** till **Använd principen omedelbart**.
1. Klicka på **spara**.

## <a name="next-steps"></a>Nästa steg

Mer information finns i:

* [Protection principer för villkorlig åtkomst baslinje](concept-baseline-protection.md)
* [Fem steg för att skydda din infrastruktur för Identitetshantering](../../security/azure-ad-secure-steps.md)
* [Vad är villkorlig åtkomst i Azure Active Directory?](overview.md)
