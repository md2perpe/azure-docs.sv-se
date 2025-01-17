---
title: Vad är tjänstens beroenden i Azure Active Directory villkorlig åtkomst? | Microsoft Docs
description: Lär dig hur villkor används i Azure Active Directory villkorlig åtkomst för att utlösa en princip.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 03/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: b9aca2e4ea5e107358ff72e83562057830ece2cc
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2019
ms.locfileid: "67509349"
---
# <a name="what-are-service-dependencies-in-azure-active-directory-conditional-access"></a>Vad är tjänstens beroenden i Azure Active Directory villkorlig åtkomst? 

Du kan ange krav för åtkomst till webbplatser och tjänster med principer för villkorlig åtkomst. Dina krav för åtkomst kan exempelvis ange att kräva multifaktorautentisering (MFA) eller [hanterade enheter](require-managed-devices.md). 

När du har åtkomst till en webbplats eller tjänst direkt, är effekten av en princip för relaterade vanligtvis lätt att utvärdera. Till exempel om du har en princip som kräver MFA för SharePoint Online konfigurerad tillämpas MFA för varje inloggning till SharePoint-webbportalen. Det är dock inte alltid schemabaserad att utvärdera effekten av en princip, eftersom det finns molnappar med beroenden till andra molnappar. Microsoft Teams kan till exempel ge åtkomst till resurser i SharePoint Online. Så när du använder Microsoft Teams i våra aktuella scenario kan omfattas du också SharePoint MFA-principen.   

## <a name="policy-enforcement"></a>Policyframtvingande 

Om du har ett tjänsteberoende som konfigurerats får principen tillämpas med hjälp av tidigt bundna eller sent bundna tvingande. 

- **Tvingande principer tidigt bundna** innebär att en användare måste uppfylla principen beroende tjänst innan du använder appen anropa. Exempelvis kan måste en användare uppfylla SharePoint-princip innan du loggar in på MS Teams. 
- **Tvingande principer sent bundna** inträffar när användaren loggar in på anropande appen. Tvingande uppskjutna till när du anropar appförfrågningar, en token för den underordnade tjänsten. Exempel innefattar MS Teams åtkomst till Planner och Office.com åtkomst till SharePoint. 

Diagrammet nedan illustrerar MS Teams tjänstens beroenden. Solid pilarna anger tidigt bundna tvingande streckad pil för Planner anger sent bundna tvingande. 

![MS Teams tjänstens beroenden](./media/service-dependencies/01.png)

Som bästa praxis bör du ange vanliga principer över relaterade appar och tjänster när det är möjligt. Med en konsekvent säkerhetsposition ger dig den bästa användarupplevelsen. Till exempel minskar ange en princip för vanliga i Exchange Online, SharePoint Online, Microsoft Teams och Skype för företag avsevärt oväntade uppmaningar som kan uppstå i olika principer tillämpas nedströms tjänster. 

I tabellen nedan visar en lista över ytterligare tjänstens beroenden, där klientappar som måste uppfylla  

| Klientappar         | Underordnad tjänst                          | Tvingande |
| :--                 | :--                                         | ---         | 
| Azure Data Lake     | Microsoft Azure Management (portal och API) | Tidigt bundna |
| Microsoft Classroom | Exchange                                    | Tidigt bundna |
|                     | SharePoint                                  | Tidigt bundna  |
| Microsoft Teams     | Exchange                                    | Tidigt bundna |
|                     | MS Planner                                  | Sent bundna  |
|                     | SharePoint                                  | Tidigt bundna |
|                     | Skype för företag – Online                   | Tidigt bundna |
| Office-portalen       | Exchange                                    | Sent bundna  |
|                     | SharePoint                                  | Sent bundna  |
| Outlook-grupper      | Exchange                                    | Tidigt bundna |
|                     | SharePoint                                  | Tidigt bundna |
| PowerApps           | Microsoft Azure Management (portal och API) | Tidigt bundna |
|                     | Windows Azure Active Directory              | Tidigt bundna |
| Project             | Dynamics CRM                                | Tidigt bundna |
| Skype för företag  | Exchange                                    | Tidigt bundna |
| Visual Studio       | Microsoft Azure Management (portal och API) | Tidigt bundna |

## <a name="next-steps"></a>Nästa steg

Om du vill lära dig mer om att implementera villkorlig åtkomst i din miljö, se [planera distributionen av villkorlig åtkomst i Azure Active Directory](plan-conditional-access.md).
