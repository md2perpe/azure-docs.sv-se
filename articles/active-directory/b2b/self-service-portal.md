---
title: Registreringsportalen för självbetjäning för B2B-samarbete – Azure Active Directory | Microsoft Docs
description: Azure Active Directory B2B-samarbete ger stöd för dina företagsomfattande relationer genom att tilldela affärspartner selektiv åtkomst till dina affärsprogram
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: sample
ms.date: 05/08/2018
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 28db43a345fb29a7529136ddfb929f5a48ccb1f8
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65785437"
---
# <a name="self-service-portal-for-azure-ad-b2b-collaboration-sign-up"></a>Registrering i självbetjäningsportalen för Azure AD B2B-samarbete

Kunder kan göra mycket med de inbyggda funktionerna som finns tillgängliga via [Azure-portalen](https://portal.azure.com) och [Programåtkomstpanelen](https://myapps.microsoft.com) för slutanvändare. Du kan dock behöva anpassa arbetsflödet för registrering för B2B-användare så att de passar behoven i din organisation. Du kan göra det med [inbjudan API](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation).

Som inbjudande organisation så kan du inte veta i förväg vilka de enskilda externa medarbetarna är som behöver åtkomst till dina resurser. Du behöver ett sätt för användare från partnerföretag att registrera sig själva med en uppsättning principer som du som inbjudande organisation styr. Det här scenariot är möjligt tack vare API:erna. Det finns ett [exempelprojekt på GitHub](https://github.com/Azure/active-directory-dotnet-graphapi-b2bportal-web) som gör just det.

Den här GitHub-projektet visar hur organisationer kan använda API:erna för att tillhandahålla en principbaserad registreringsfunktion med självbetjäning för dina betrodda partners med regler som fastställer vilka appar som de kommer åt. Partneranvändare kan få åtkomst till resurser när det behöver dem. De kan göra detta på ett säkert sätt, utan att den inbjudande organisationen manuellt registrerar dem. Du kan enkelt distribuera projektet till den Azure-prenumeration du väljer.

## <a name="as-is-code"></a>Kod i befintligt skick

Den här koden görs tillgänglig som ett exempel för att demonstrera användning av Azure Active Directory B2B-inbjudans-API. Den bör anpassas av ditt utvecklingsteam eller en partner och bör granskas innan du distribuerar den i ett produktionsscenario.

## <a name="next-steps"></a>Nästa steg

* [Vad är Azure AD B2B-samarbete?](what-is-b2b.md)
* [Azure AD B2B-samarbetslicensiering](licensing-guidance.md)
* [Vanliga frågor och svar om Azure Active Directory B2B-samarbete](faq.md)
