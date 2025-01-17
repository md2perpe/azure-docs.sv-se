---
title: Användare som flaggats i säkerhetsrapporten i Azure Active Directory | Microsoft Docs
description: Lär dig mer om användare som flaggats i säkerhetsrapporten i Azure Active Directory-portalen
services: active-directory
author: MarkusVi
manager: daveba
ms.assetid: addd60fe-d5ac-4b8b-983c-0736c80ace02
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 01/17/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 463f5c2d03cd96089342aa9b22ef85ebc05aa909
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60438145"
---
# <a name="users-flagged-for-risk-report-in-the-azure-portal"></a>Rapport över användare som har flaggats för risk i Azure-portalen

Azure Active Directory (Azure AD) identifierar misstänkta åtgärder relaterade till dina användarkonton. För varje identifierad åtgärd skapas en post med namnet [riskhändelse](concept-risk-events.md).

Du kan komma åt säkerhetsrapporterna via [Azure-portalen](https://portal.azure.com) genom att välja bladet **Azure Active Directory** och sedan navigera till avsnittet **Säkerhet**. 

De identifierade riskhändelserna används för att beräkna:

- **Riskfyllda inloggningar** – En riskfylld inloggning indikerar ett potentiellt inloggningsförsök av någon annan än användarkontots ägare. 

- **Användare som har flaggats för risk** – En användare som har flaggats för risk indikerar att ett användarkonto kan ha komprometterats. 

Information om hur du konfigurerar principer som utlöser dessa riskhändelser finns i [Konfigurera riskprincipen för användare](../identity-protection/howto-user-risk-policy.md). 

![Riskfyllda inloggningar](./media/concept-user-at-risk/10.png)


## <a name="what-azure-ad-license-do-you-need-to-access-the-users-at-risk-report"></a>Vilken Azure AD-licens behöver du för åtkomst till rapporten över användare i farozonen?  

Alla utgåvor av Azure Active Directory ger rapporter över användare som har flaggats för risk. Nivån av rapportens detaljrikedom varierar dock mellan versionerna: 

- I **versionerna Azure Active Directory Free och Basic** finns en lista över användare som har flaggats för risk. 

- Dessutom kan du med utgåvan **Azure Active Directory Premium 1** utforska några av de underliggande riskhändelser som har identifierats för varje rapport. 

- Utgåvan **Azure Active Directory Premium 2** ger den mest detaljerade informationen om alla underliggande riskhändelser och du kan konfigurera säkerhetsprinciper som automatiskt svarar på konfigurerade risknivåer.


## <a name="users-at-risk-report-for-azure-ad-free-and-basic-editions"></a>Rapport över användare i farozonen för Azure AD Free och Basic

Rapporten om användare som flaggats för risk i den kostnadsfria och grundläggande versionen av Azure AD tillhandahåller en lista över användarkonton som kan ha komprometterats. 

![Riskfyllda inloggningar](./media/concept-user-at-risk/03.png)

När en användare väljs visas autentiseringsuppgifter. För användare i farozonen går du igenom användarens inloggningshistorik och återställer lösenordet om det behövs.

Dialogrutan tillhandahåller ett alternativ för att:

- Ladda ned rapport
- Söka efter användare

    ![Riskfyllda inloggningar](./media/concept-user-at-risk/16.png)

För att få mer detaljerad information behöver du en premiumlicens.

## <a name="users-at-risk-report-for-azure-ad-premium-editions"></a>Rapport över användare i farozonen för Azure AD Premium-versioner

Rapporten om användare som flaggats för risk i Azure AD Premium-versionerna innehåller följande:

- En lista över användarkonton som kan ha drabbats 

- Sammanställd information om de [riskhändelsetyper](concept-risk-events.md) som har identifierats

- Ett alternativ för att ladda ned rapporten

- Ett alternativ för att konfigurera en [princip för att åtgärda användarrisker](../identity-protection/howto-user-risk-policy.md)  

![Riskfyllda inloggningar](./media/concept-user-at-risk/71.png)

När du väljer en användare får du en detaljerad rapportvy för den här användaren som du kan använda för att göra följande:

- Öppna vyn Alla inloggningar

- Återställ användarens lösenord

- Ignorera alla händelser

- Undersök rapporterade riskhändelser för användaren. 

![Riskfyllda inloggningar](./media/concept-user-at-risk/324.png)

Om du vill undersöka en riskhändelse, markerar du en på listan för att öppna bladet med **Information** om den riskhändelsen. På bladet **Information** har du möjlighet att antingen stänga en riskhändelse manuellt eller återaktivera en manuellt stängd riskhändelse. 

![Riskfyllda inloggningar](./media/concept-user-at-risk/325.png)


## <a name="next-steps"></a>Nästa steg

- [Konfigurera riskprincipen för användare](../identity-protection/howto-user-risk-policy.md)
- [Konfigurera riskåtgärdsprincipen](../identity-protection/howto-user-risk-policy.md)
- [Identitetsskydd för Azure Active Directory](../active-directory-identityprotection.md)

