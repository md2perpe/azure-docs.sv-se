---
title: Multifaktorautentisering (MFA) och PIM - Azure Active Directory | Microsoft Docs
description: Lär dig hur Azure AD Privileged Identity Management (PIM) verifierar multifaktorautentisering (MFA).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 08/31/2018
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8831a838a403d58d8673c6400e0dda06c03dc69f
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60442683"
---
# <a name="multi-factor-authentication-mfa-and-pim"></a>Multifaktorautentisering (MFA) och PIM

Vi rekommenderar att du kräver multifaktorautentisering (MFA) för alla administratörer. Detta minskar risken för angrepp på grund av ett olämpligt lösenord.

Du kan kräva att användarna slutföra MFA-kontrollen när de loggar in. Du kan också kräva att användarna slutföra MFA-kontrollen när de aktiverar en roll i Azure Active Directory (Azure AD) Privileged Identity Management (PIM). Det här sättet om användaren inte slutföra MFA-kontrollen när de loggat in, uppmanas de att göra detta av PIM.

> [!IMPORTANT]
> Just nu, Azure MFA fungerar bara med arbets- eller skolkonton, inte Microsoft-konton (vanligtvis ett personligt konto som används för att logga in på Microsoft-tjänster som Skype, Xbox, Outlook.com, osv.). Därför kan alla som använder ett Microsoft-konto inte en berättigad administratör eftersom de inte kan använda MFA för att aktivera sina roller. Om dessa användare behöver att fortsätta att hantera arbetsbelastningar med ett Microsoft-konto, gör du dem till permanenta administratörer för tillfället.

## <a name="how-pim-validates-mfa"></a>Hur PIM verifierar MFA

Det finns två alternativ för att verifiera MFA när en användare aktiverar rollen.

Det enklaste alternativet är att förlita dig på Azure MFA för användare som aktiverar en privilegierad roll. Om du vill göra detta måste du först kontrollera att dessa användare licensieras, om det behövs och har registrerat dig för Azure MFA. Mer information om hur du distribuerar Azure MFA finns i [distribuera molnbaserade Azure Multi-Factor Authentication](../authentication/howto-mfa-getstarted.md). Det rekommenderas, men inte krävs att du konfigurerar Azure AD för att införa MFA för dessa användare när de loggar in. Det beror på att MFA-kontroller ska göras av PIM själva.

Du kan också ha din identitetsprovider som ansvarar för MFA genom att användare autentiseras lokalt. Om du har konfigurerat AD Federation Services för att kräva smartkortbaserad autentisering innan du använder Azure AD, till exempel [skydda molnresurser med Azure Multi-Factor Authentication och AD FS](../authentication/howto-mfa-adfs.md) innehåller anvisningar för att konfigurera AD FS för att skicka anspråk till Azure AD. När en användare försöker aktivera en roll, godtar PIM att MFA har redan verifierats för användaren när den får rätt anspråken.

## <a name="next-steps"></a>Nästa steg

- [Konfigurera inställningar för Azure AD-rollen i PIM](pim-how-to-change-default-settings.md)
- [Konfigurera Azure-resurs rollinställningar i PIM](pim-resource-roles-configure-role-settings.md)
