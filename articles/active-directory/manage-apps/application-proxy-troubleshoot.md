---
title: Felsöka Application Proxy | Microsoft Docs
description: Beskriver hur du felsöker fel i Azure AD-programproxy.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/24/2019
ms.author: mimart
ms.reviewer: japere
ms.custom: H1Hack27Feb2017; it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2cac7e3ba458caad9c373160be1b66e2a665088a
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67440460"
---
# <a name="troubleshoot-application-proxy-problems-and-error-messages"></a>Felsöka problem med Application Proxy och felmeddelanden
Om fel uppstår i komma åt ett publicerat program eller publicera program kontrollerar du följande alternativ för att se om Microsoft Azure AD-programproxyn fungerar korrekt:

* Öppna konsolen Windows-tjänster och kontrollera att den **Microsoft AAD Application Proxy Connector** tjänsten är aktiverade och körs. Du kan också vill titta på egenskapssidan för Application Proxy-tjänsten enligt följande bild:  
  ![Egenskaper för Microsoft AAD Application Proxy Connector skärmbild](./media/application-proxy-troubleshoot/connectorproperties.png)
* Öppna Loggboken och leta efter Application Proxy connector händelser i **applikationer och tjänsteloggar** > **Microsoft** > **AadApplicationProxy**  >  **Connector** > **Admin**.
* Om det behövs mer detaljerade loggar är tillgängliga som [aktivera Application Proxy connector-sessionsloggar](application-proxy-connectors.md#under-the-hood).

När du felsöker problem med Application Proxy kan vi rekommenderar att du börjar med att felsöka flödet, [felsöka Application Proxy Connector utfärdar](application-proxy-debug-connectors.md)att avgöra om programproxyanslutningar är korrekt konfigurerade. Om du fortfarande har problem med att ansluta till programmet, följer du felsökning flödet i [felsöka programproxyn programproblem](application-proxy-debug-apps.md).

## <a name="the-page-is-not-rendered-correctly"></a>Sidan renderas inte korrekt
Du kan ha problem med programmet återgivning eller felaktigt fungerar utan att du får felmeddelanden. Detta kan inträffa om du har publicerat den artikel sökvägen, men programmet kräver innehåll som finns utanför den sökvägen.

Exempel: Om du publicerar sökvägen `https://yourapp/app` men programmet anropar bilder `https://yourapp/media`, de återges inte. Se till att du publicerar program med den högsta nivån sökvägen måste du inkludera alla relevant innehåll. I det här exemplet skulle det vara `http://yourapp/`.

Om du ändrar sökvägen för att inkludera refererat innehåll, men fortfarande behöver användare alltid landar på en djupare länk i sökvägen finns i bloggposten [inställningen länken rätt for Application Proxy-program i Azure AD åtkomst till panelen och Office 365-appstartaren](https://blogs.technet.microsoft.com/applicationproxyblog/2016/04/06/setting-the-right-link-for-application-proxy-applications-in-the-azure-ad-access-panel-and-office-365-app-launcher/).

## <a name="connector-errors"></a>Felmeddelanden

Om registreringen misslyckas under guiden kopplingsinstallationen, finns det två sätt att se orsaken till felet. Antingen titta i Loggboken under **program och tjänster Logs\Microsoft\AadApplicationProxy\Connector\Admin**, eller kör följande Windows PowerShell-kommando:

    Get-EventLog application –source "Microsoft AAD Application Proxy Connector" –EntryType "Error" –Newest 1

När du har hittat Connector-fel från händelseloggen, kan du använda den här tabellen över vanliga fel för att lösa problemet:

| Fel | Rekommenderade åtgärder |
| ----- | ----------------- |
| Det gick inte att registreringen av anslutningsappen: Kontrollera att du har aktiverat Application Proxy i Azure-hanteringsportalen och att du angett ditt Active Directory-användarnamn och lösenord korrekt. Fel: ”En eller flera fel inträffade”. | Om du har stängt fönstret registreringen utan att logga in till Azure AD, kör guiden koppling igen och registrera anslutningsverktyget. <br><br> Om fönstret registrering öppnar och stänger sedan omedelbart utan så att du kan logga in kan får du förmodligen det här felet. Det här felet uppstår när det finns ett nätverksfel på datorn. Se till att det är möjligt att ansluta från en webbläsare till en offentlig webbplats och att portarna är öppna som anges i [Application Proxy krav](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment). |
| Rensa fel visas i fönstret registrering. Det går inte att fortsätta | Om du ser detta fel och sedan fönstret stängs, du har angett felaktigt användarnamn eller lösenord. Försök igen. |
| Det gick inte att registreringen av anslutningsappen: Kontrollera att du har aktiverat Application Proxy i Azure-hanteringsportalen och att du angett ditt Active Directory-användarnamn och lösenord korrekt. Fel: 'AADSTS50059: Ingen information om identifiering av klient finns i antingen begäran eller underförstådda på valfri angivna autentiseringsuppgifter och Sök efter service principal URI har misslyckats. | Du försöker logga in med ett Account och inte en domän som är en del av organisations-ID för den katalog som du försöker komma åt. Se till att administratören är en del av samma domännamn som klient-domänen, till exempel om Azure AD-domänen är contoso.com ska administratören vara admin@contoso.com. |
| Det gick inte att hämta aktuella körningsprincipen för att köra PowerShell-skript. | Om anslutningen misslyckas, kontrollera att PowerShell-körningsprincipen inte är inaktiverat. <br><br>1. Öppna redigeraren.<br>2. Gå till **Datorkonfiguration** > **Administrationsmallar** > **Windows-komponenter**  >   **Windows PowerShell** och dubbelklicka på **aktivera körning av skript**.<br>3. Körningsprincipen kan vara inställd på antingen **inte konfigurerad** eller **aktiverad**. Om inställd **aktiverad**, se till att under Alternativ, körningsprincipen är inställd på antingen **Tillåt lokala skript och remote signerade skript** eller **Tillåt alla skript**. |
| Det gick inte att hämta konfigurationen av anslutningen. | Kopplingens klientcertifikat som används för autentisering, upphört att gälla. Detta kan också inträffa om du har anslutningstjänsten installerad bakom en proxyserver. I det här fallet anslutningstjänsten inte kan komma åt Internet och kan inte tillhandahålla program till fjärranslutna användare. Förnya manuellt med hjälp av den `Register-AppProxyConnector` cmdlet i Windows PowerShell. Om din Anslutningsapp finns bakom en proxyserver, är det nödvändigt att bevilja Internetåtkomst till Connector konton ”nätverkstjänster” och ”lokalt system”. Detta kan åstadkommas genom att ge dem åtkomst till proxyservern eller genom att ange att kringgå proxyn. |
| Det gick inte att registreringen av anslutningsappen: Kontrollera att du är administratör program för din Active Directory för att registrera anslutningsverktyget. Fel: ”Registreringsbegäran nekades”. | Det alias som du försöker logga in med är inte en administratör på den här domänen. Din Anslutningsapp är alltid installerad för den katalog som äger användarens domän. Se till att det administratörskonto som du försöker logga in med har administratörsbehörighet för minst program till Azure AD-klient. |
| Anslutningen kunde inte ansluta till tjänsten på grund av nätverk problem. Anslutningen försökte komma åt följande URL. | Anslutningen kan inte ansluta till Molntjänsten Application Proxy. Detta kan inträffa om du har en brandväggsregel som blockerar anslutningen. Se till att du har åtkomst till rätt portar och URL: er som listas i [Application Proxy krav](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment). |

## <a name="kerberos-errors"></a>Kerberos-fel

Den här tabellen beskriver mer vanliga fel som kommer från Kerberos-installationen och konfigurationen tillsammans med förslag för matchning av.

| Fel | Rekommenderade åtgärder |
| ----- | ----------------- |
| Det gick inte att hämta aktuella körningsprincipen för att köra PowerShell-skript. | Om anslutningen misslyckas, kontrollera att PowerShell-körningsprincipen inte är inaktiverat.<br><br>1. Öppna redigeraren.<br>2. Gå till **Datorkonfiguration** > **Administrationsmallar** > **Windows-komponenter**  >   **Windows PowerShell** och dubbelklicka på **aktivera körning av skript**.<br>3. Körningsprincipen kan vara inställd på antingen **inte konfigurerad** eller **aktiverad**. Om inställd **aktiverad**, se till att under Alternativ, körningsprincipen är inställd på antingen **Tillåt lokala skript och remote signerade skript** eller **Tillåt alla skript**. |
| 12008 – azure AD överskred det maximala antalet tillåtna Kerberos-autentiseringsförsök till backend-servern. | Detta fel kan bero på felaktig konfiguration mellan Azure AD och backend-servern för programmet, eller ett problem i tid och datum-konfigurationen på båda datorerna. Backend-servern avvisade Kerberos-biljetten som skapats av Azure AD. Verifiera Azure AD och programmet backend-servern är rätt konfigurerade. Se till att tid och datum-konfigurationen på Azure AD och backend-programserver ska synkroniseras. |
| 13016 – azure AD kan inte hämta en Kerberos-biljett för användarens räkning eftersom det finns inget UPN i kant-token eller i åtkomst-cookie. | Det finns ett problem med STS-konfigurationen. Åtgärda Anspråkskonfiguration UPN i STS. |
| 13019 – azure AD kan inte hämta en Kerberos-biljett för användarens räkning på grund av följande allmänna API-fel. | Den här händelsen kan tyda på felaktig konfiguration mellan Azure AD och Domänkontrollantservern eller ett problem i tid och datum-konfigurationen på båda datorerna. Domänkontrollanten avvisade Kerberos-biljetten som skapats av Azure AD. Verifiera Azure AD och backend-programserver har konfigurerats korrekt, särskilt SPN-konfigurationen. Kontrollera att Azure AD är domänansluten till samma domän som domänkontrollanten för att se till att domänkontrollanten upprättar förtroende med Azure AD. Se till att tid och datum-konfigurationen på Azure AD och domänkontrollanten ska synkroniseras. |
| 13020 – azure AD kan inte hämta en Kerberos-biljett för användarens räkning eftersom SPN för backend-servern inte har definierats. | Den här händelsen kan tyda på felaktig konfiguration mellan Azure AD och Domänkontrollantservern eller ett problem i tid och datum-konfigurationen på båda datorerna. Domänkontrollanten avvisade Kerberos-biljetten som skapats av Azure AD. Verifiera Azure AD och backend-programserver har konfigurerats korrekt, särskilt SPN-konfigurationen. Kontrollera att Azure AD är domänansluten till samma domän som domänkontrollanten för att se till att domänkontrollanten upprättar förtroende med Azure AD. Se till att tid och datum-konfigurationen på Azure AD och domänkontrollanten ska synkroniseras. |
| 13022 – azure AD autentisera inte användaren eftersom backend-servern svarar på Kerberos-autentiseringsförsök med ett HTTP 401-fel. | Den här händelsen kan tyda på felaktig konfiguration mellan Azure AD och backend-servern för programmet, eller ett problem i tid och datum-konfigurationen på båda datorerna. Backend-servern avvisade Kerberos-biljetten som skapats av Azure AD. Verifiera Azure AD och programmet backend-servern är rätt konfigurerade. Se till att tid och datum-konfigurationen på Azure AD och backend-programserver ska synkroniseras. Mer information finns i [felsöka Kerberos-begränsad delegering konfigurationer för Application Proxy](application-proxy-back-end-kerberos-constrained-delegation-how-to.md).  |

## <a name="end-user-errors"></a>Slutanvändaren fel

Den här listan innehåller fel som slutanvändarna kan stöta på när de försöker få åtkomst till appen och misslyckas. 

| Fel | Rekommenderade åtgärder |
| ----- | ----------------- |
| Webbplatsen kan inte visa sidan. | Användaren kan få det här felet när du försöker få åtkomst till appen som du har publicerat om programmet är ett IWA-program. Definierade SPN-namnet för det här programmet kan vara felaktig. Kontrollera att tjänstens Huvudnamn som konfigurerats för det här programmet är korrekt för IWA appar. |
| Webbplatsen kan inte visa sidan. | Användaren kan få det här felet när du försöker få åtkomst till appen som du har publicerat om programmet är ett program för OWA. Detta kan bero på något av följande:<br><li>Definierade SPN-namnet för det här programmet är felaktigt. Kontrollera att tjänstens Huvudnamn som konfigurerats för det här programmet är korrekt.</li><li>Den användare som försökte komma åt programmet använder ett Microsoft-konto i stället för på rätt företagskontot för att logga in eller användaren är en gästanvändare. Kontrollera att användaren loggar in med sitt företagskonto som matchar domänen för det publicerade programmet. Account användare och gäst kan inte komma åt IWA program.</li><li>Den användare som försökte komma åt programmet har inte definierats korrekt för det här programmet på sida på plats. Se till att den här användaren har rätt behörigheter som har definierats för det här backend-programmet på plats på datorn. |
| Den här företagets appen kan inte nås. Du har inte behörighet att komma åt det här programmet. Auktoriseringen misslyckades. Se till att tilldela användare med åtkomst till det här programmet. | Användaren kan få det här felet när du försöker få åtkomst till appen som du har publicerat om de använder Microsoft-konton i stället för sitt företagskonto för att logga in. Gästanvändare kan också få det här felet. Account användare och gäster kan inte komma åt IWA program. Kontrollera att användaren loggar in med sitt företagskonto som matchar domänen för det publicerade programmet.<br><br>Du har inte tilldelat användaren för det här programmet. Gå till den **programmet** fliken och under **användare och grupper**, tilldela den här användaren eller användargruppen till det här programmet. |
| Den här företagets appen är inte tillgänglig just nu. Försök igen senare... Tidsgränsen för anslutningen har uppnåtts. | Användaren kan få det här felet när du försöker få åtkomst till appen som du har publicerat om de inte är korrekt definierade för det här programmet på den lokala sidan. Se till att användarna har rätt behörigheter som har definierats för det här backend-programmet på plats på datorn. |
| Den här företagets appen kan inte nås. Du har inte behörighet att komma åt det här programmet. Auktoriseringen misslyckades. Kontrollera att användaren har en licens för Azure Active Directory Premium eller Basic. | Användaren kan få det här felet när du försöker få åtkomst till appen som du har publicerat om de inte uttryckligen tilldelade med en Premium/Basic-licens av prenumerantens administratör. Gå till Active Directory för prenumerantens **licenser** fliken och se till att den här användaren eller användargruppen tilldelas en Premium eller Basic-licens. |
| Det gick inte att hitta en server med det angivna värdnamnet. | Användaren kan få det här felet när du försöker få åtkomst till appen som du har publicerat om programmets anpassad domän inte har konfigurerats korrekt. Kontrollera att du har laddat upp ett certifikat för domänen och korrekt konfigurerad DNS-post genom att följa stegen i [arbeta med anpassade domäner i Azure AD Application Proxy](application-proxy-configure-custom-domain.md) |

## <a name="my-error-wasnt-listed-here"></a>Mina fel visas inte här

Om det uppstår ett fel eller problem med Azure AD Application Proxy som inte visas i den här felsökningsguiden kan vill vi få information om den. Skicka ett e-postmeddelande till vår [feedbackteamet](mailto:aadapfeedback@microsoft.com) med information om fel som inträffat.

## <a name="see-also"></a>Se också
* [Aktivera programproxyn för Azure Active Directory](application-proxy-add-on-premises-application.md)
* [Publicera program med Application Proxy](application-proxy-add-on-premises-application.md)
* [Aktivera enkel inloggning](application-proxy-configure-single-sign-on-with-kcd.md)
* [Aktivera villkorlig åtkomst](application-proxy-integrate-with-sharepoint-server.md)


<!--Image references-->
[1]: ./media/application-proxy-troubleshoot/connectorproperties.png
[2]: ./media/active-directory-application-proxy-troubleshoot/sessionlog.png
