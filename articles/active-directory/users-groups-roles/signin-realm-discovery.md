---
title: Användarnamn lookup under autentiseringen inloggning – Azure Active Directory | Microsoft Docs
description: Hur anvisningarna på skärmen meddelanden visar användarnamn lookup under inloggning
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 04/15/2019
ms.author: curtand
ms.reviewer: kexia
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: db627359b75aa0ea19e30a8d22bcacaa3409cb4a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66418188"
---
# <a name="home-realm-discovery-for-azure-active-directory-sign-in-pages"></a>Identifiering av startsfär för Azure Active Directory-inloggningssidor

Vi ändrar inloggningen i Azure Active Directory (Azure AD) för att göra rum för nya autentiseringsmetoder och förbättra användningen. Under inloggningen avgör Azure AD var en användare ska autentiseras. Azure AD fattar intelligenta beslut genom att läsa av organisations- och användarinställningar för det användarnamn som angavs på inloggningssidan. Det här är ett steg mot en lösenordsfri framtid som öppnar för andra autentiseringsuppgifter som FIDO 2.0.

## <a name="home-realm-discovery-behavior"></a>Beteende för identifiering av startsfär

Historiskt sett har identifiering av startsfär regleras av den domän som har angetts vid inloggning eller en princip för Home identifieringen av Startsfären för vissa äldre program. Exempelvis kan skriver sitt användarnamn en Azure Active Directory-användare i vår identifiering beteende men skulle fortfarande når sin organisations autentiseringsuppgifter samling skärmen. Detta inträffar när användaren ger korrekt organisationens domän namnet ”contoso.com”. Det här beteendet tillåter inte detaljnivån att anpassa upplevelsen för enskilda användare.

För att stödja ett bredare spektrum av autentiseringsuppgifter och öka användbarheten, uppdateras nu Azure Active Directorys användarnamn lookup beteende under inloggningen. Det nya beteendet gör smarta beslut genom att läsa klient och användare på inställningar baserat på användarnamnet som angetts på sidan logga in. Om du vill göra detta möjligt, ska Azure Active Directory kontrollera om det användarnamn som anges på sidan logga in finns i deras angivna domänen eller omdirigerar användaren för att ange sina autentiseringsuppgifter.

En annan fördel av detta arbete är förbättrad fel meddelanden. Här följer några exempel på förbättrade felmeddelanden när du loggar in till ett program som stöder Azure Active Directory-användare.

- Användarnamnet är felstavad eller användarnamnet har ännu inte synkroniserats till Azure AD:
  
    ![användarnamnet är felskrivet eller hittades inte](./media/signin-realm-discovery/typo-username.png)
  
- Domännamnet är felstavad:
  
    ![domännamnet är felskrivet eller hittades inte](./media/signin-realm-discovery/typo-domain.png)
  
- Användaren försöker att logga in med en känd konsumentdomän:
  
    ![logga in med en känd konsumentdomän](./media/signin-realm-discovery/consumer-domain.png)
  
- Lösenordet är felstavad men användarnamnet stämmer:  
  
    ![lösenordet är fel med bra användarnamn](./media/signin-realm-discovery/incorrect-password.png)
  
> [!IMPORTANT]
> Den här funktionen kan påverka på federerade domäner som förlitar sig på gamla domännivå identifiering av startsfär att tvinga federation. Uppdateringarna på när federerad domänstöd kommer att läggas till, finns [startsfär identifiering under inloggning för Microsoft 365-tjänster](https://azure.microsoft.com/updates/signin-hrd/). Under tiden kan har vissa organisationer tränats sina anställda att logga in med ett användarnamn som inte finns i Azure Active Directory men innehåller rätt domännamn, eftersom domännamnen dirigerar användare för närvarande till organisationens domän slutpunkt. Det nya beteendet inloggning tillåter inte detta. Användaren meddelas att rätta användarnamnet och de tillåts inte att logga in med ett användarnamn som inte finns i Azure Active Directory.
>
> Om du eller din organisation har metoder som är beroende av den gamla funktionen, är det viktigt för organisationens administratörer uppdatera dokumentationen för inloggning och autentisering av medarbetare och träna anställda använder sina Azure Active Directory-användarnamn för inloggning.
  
Om du har problem med det nya beteendet kan lämna dina kommentarer i den **Feedback** i den här artikeln.  

## <a name="next-steps"></a>Nästa steg

[Anpassa din inloggning profilering](../fundamentals/add-custom-domain.md)
