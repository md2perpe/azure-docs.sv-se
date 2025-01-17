---
title: Blockera äldre autentisering till Azure Active Directory (Azure AD) med villkorlig åtkomst | Microsoft Docs
description: Lär dig hur du kan förbättra din säkerhetsposition genom att blockera äldre autentisering med hjälp av Azure AD villkorlig åtkomst.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 06/17/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 60fc168a6c15e7cb8d6e4271154d99fb4136226e
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2019
ms.locfileid: "67509037"
---
# <a name="how-to-block-legacy-authentication-to-azure-ad-with-conditional-access"></a>Anvisningar: Blockera äldre autentisering till Azure AD med villkorlig åtkomst   

Om du vill ge dina användare enkel åtkomst till dina appar i molnet, stöder Azure Active Directory (Azure AD) ett stort antal autentiseringsprotokoll, inklusive äldre autentisering. Äldre protokoll stöder dock inte multifaktorautentisering (MFA). MFA är ett vanligt krav adress identitetsstöld i många miljöer. 

Om din miljö är redo att blockera äldre autentisering för att förbättra din klient protection, kan du göra det här målet med villkorlig åtkomst. Den här artikeln förklarar hur du kan konfigurera principer för villkorlig åtkomst som blockerar äldre autentisering för din klient.

## <a name="prerequisites"></a>Förutsättningar

Den här artikeln förutsätter att du är bekant med: 

- Den [grundbegreppen](overview.md) av Azure AD villkorlig åtkomst 
- Den [bästa praxis](best-practices.md) för att konfigurera principer för villkorlig åtkomst i Azure portal

## <a name="scenario-description"></a>Scenariobeskrivning

Azure AD stöder flera av de mest använda protokoll för autentisering och auktorisering, inklusive äldre autentisering. Äldre refererar till protokoll som använder grundläggande autentisering. Normalt kan inte dessa protokoll tvinga alla typer av tvåfaktorsautentisering. Exempel för appar som baseras på äldre autentisering är:

- Äldre Microsoft Office-program
- Appar med hjälp av e-postprotokoll som POP, IMAP och SMTP

En faktor autentisering (till exempel användarnamn och lösenord) är inte tillräckligt med dessa dagar. Lösenord är felaktigt eftersom de är lätta att gissa och vi (mänsklig) är felaktig på välja bra lösenord. Lösenord är också sårbara för attacker som nätfiske och lösenord besprutningsmedel olika. En av de enklaste saker som du kan göra för att skydda mot hot för lösenord är att implementera MFA. Med MFA, även om en angripare får tillgång till en användares lösenord räcker lösenordet enbart inte att autentisera och komma åt data.

Hur kan du förhindra att appar som använder äldre autentisering från att komma åt resurser i din klient Rekommendationen är att bara blockera dem med en princip för villkorlig åtkomst. Om det behövs kan tillåta du enbart vissa användare och specifika nätverksplatser du använder appar som baseras på äldre autentisering.

Principer för villkorlig åtkomst tillämpas efter den första-factor-autentiseringen har slutförts. Därför villkorlig åtkomst är inte avsedd som en första rad ansvarsfrihetsgrund för scenarier som attacker för denial of service (DoS), men kan använda signaler från dessa händelser (till exempel risknivå för inloggning, platsen för begäran och så vidare) för att fastställa åtkomst.

## <a name="implementation"></a>Implementering

Det här avsnittet beskrivs hur du konfigurerar principer för villkorlig åtkomst blockera äldre autentisering. 

### <a name="identify-legacy-authentication-use"></a>Identifiera äldre användning

Innan du kan blockera äldre autentisering i din katalog, måste du först förstå om användarna har appar som använder äldre autentisering och hur den påverkar din övergripande katalog. Azure AD-inloggningen loggar kan användas för att förstå om du använder äldre autentisering.

1. Navigera till den **Azure-portalen** > **Azure Active Directory** > **inloggningar**.
1. Lägga till kolumnen Klientappen om den inte visas genom att klicka på **kolumner** > **Klientappen**.
1. Filtrera efter **Klientappen** > **andra klienter** och klicka på **tillämpa**.

Filtrering kommer bara visa du loggar in försök som har gjorts av äldre autentiseringsprotokoll. När du klickar på varje enskild inloggningsförsök visas ytterligare information. Den **Klientappen** fältet under den **grundläggande information** fliken visar vilket äldre autentiseringsprotokoll användes.

De här loggarna visar vilka användare fortfarande är beroende av äldre och vilka program som använder äldre protokoll att autentiseringsbegäranden. Implementera en princip för villkorlig åtkomst för dessa användare endast för användare som inte finns med i loggarna och bekräftas som inte använder äldre autentisering.

### <a name="block-legacy-authentication"></a>Blockera äldre autentisering 

Du kan ange ett villkor som är kopplad till klientappar som används för att komma åt resurser i en princip för villkorlig åtkomst. Klient apps villkor kan du begränsa omfånget till appar som använder äldre autentisering genom att välja **andra klienter** för **mobilappar och skrivbordsklienter**.

![Övriga klienter](./media/block-legacy-authentication/01.png)

Om du vill blockera åtkomst för dessa appar måste du markera **blockera åtkomst**.

![Blockera åtkomst](./media/block-legacy-authentication/02.png)

### <a name="select-users-and-cloud-apps"></a>Välj användare och appar i molnet

Om du vill blockera äldre autentisering för din organisation kan tror du förmodligen att du kan göra detta genom att välja:

- Alla användare
- Alla molnappar
- Blockera åtkomst

![Tilldelningar](./media/block-legacy-authentication/03.png)

Azure har en säkerhetsfunktion som hindrar dig från att skapa en princip så här eftersom den här konfigurationen bryter mot den [bästa praxis](best-practices.md) för principer för villkorlig åtkomst.
 
![Principkonfigurationen stöds inte](./media/block-legacy-authentication/04.png)

Funktionen säkerhet är nödvändigt eftersom *blockerar alla användare och alla molnappar* finns risken för att blockera hela din organisation från att logga in på din klient. Du inkludera inte minst en användare för att uppfylla kravet på minimal bästa praxis. Du kan också utesluta en katalogroll.

![Principkonfigurationen stöds inte](./media/block-legacy-authentication/05.png)

Du kan uppfylla det här säkerhetsfunktion genom att exkludera en användare från din princip. Vi rekommenderar att du bör definiera några [nödfall-åtkomst till administratörskonton i Azure AD](../users-groups-roles/directory-emergency-access.md) och exkluderar dem från din princip.

## <a name="policy-deployment"></a>Distribution av princip

Innan du placerar principen till produktion måste ta hand om:
 
- **Tjänstkonton** – identifiera användarkonton som används som tjänstkonton eller av enheter, till exempel konferens rummet telefoner. Kontrollera att dessa konton har starka lösenord och lägga till dem i en undantagen grupp.
- **Logga in rapporter** – granska inloggning rapporten och leta efter **andra klienter** trafik. Identifiera övre användning och undersöka varför den inte används. Trafiken genereras vanligtvis av äldre Office-klienter som inte använder modern autentisering eller vissa tredjeparts-e-postappar. Gör en plan för att flytta användning från de här apparna, eller om effekten är låg, meddela användarna att de inte kan använda de här apparna längre.
 
Mer information finns i [hur ska du distribuera en ny princip?](best-practices.md#how-should-you-deploy-a-new-policy).

## <a name="what-you-should-know"></a>Det här bör du känna till

Blockerar åtkomst med hjälp av **andra klienter** blockerar också Exchange Online PowerShell med hjälp av grundläggande autentisering.

Konfigurera en princip för **andra klienter** blockerar för hela organisationen från vissa klienter som SPConnect. Det här blocket inträffar eftersom äldre klienter autentiseras på oväntade sätt. Problemet gäller inte för viktiga Office-program som de äldre Office-klienterna.

Det kan ta upp till 24 timmar innan principen ska träda i kraft.

Du kan välja alla tillgängliga bevilja kontroller för den **andra klienter** villkoret, men slutanvändarens upplevelse är alltid samma - blockerat åtkomsten.

Om du blockerar äldre autentisering med den **andra klienter** villkor, du kan också ange villkor för enhetens plattform och plats. Till exempel om du bara vill att blockera äldre autentisering för mobila enheter, ställa in den **enhetsplattformar** villkor genom att välja:

- Android
- iOS
- Windows Phone

![Principkonfigurationen stöds inte](./media/block-legacy-authentication/06.png)

## <a name="next-steps"></a>Nästa steg

- Om du inte är bekant med att konfigurera principer för villkorlig åtkomst än [kräver MFA för specifika appar med Azure Active Directory villkorsstyrd åtkomst](app-based-mfa.md) ett exempel.
- Mer information om stöd för modern autentisering finns i [hur modern autentisering fungerar för Office 2013 och Office 2016 klientappar](https://docs.microsoft.com/office365/enterprise/modern-auth-for-office-2013-and-2016) 
