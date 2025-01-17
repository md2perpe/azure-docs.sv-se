---
title: Använda Azure MFA Server med AD FS 2.0 - Azure Active Directory
description: Det här är sidan om Azure Multi-Factor Authentication som beskriver hur du kommer igång med Azure MFA och AD FS 2.0.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 231375c94163f098cce86bdbbf285a58957a9927
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67056108"
---
# <a name="configure-azure-multi-factor-authentication-server-to-work-with-ad-fs-20"></a>Konfigurera Azure Multi-Factor Authentication Server så att den fungerar med AD FS 2.0

Den här artikeln är för organisationer som federerade med Azure Active Directory och som vill skydda resurser som finns lokalt eller i molnet. Skydda dina resurser genom att använda Azure Multi-Factor Authentication-servern och konfigurera den för att fungera med AD FS så att tvåstegsverifiering utlöses för slutpunkter med högt värde.

Den här dokumentationen beskriver hur du använder Azure Multi-Factor Authentication Server med AD FS 2.0. Mer information om AD FS finns i [Skydda molnresurser och lokala resurser med Azure Multi-Factor Authentication Server med Windows Server 2012 R2 AD FS](howto-mfaserver-adfs-2012.md).

> [!IMPORTANT]
> Från och med den 1 juli 2019 erbjuder Microsoft inte längre MFA Server för nya distributioner. Nya kunder som vill kräva multifaktorautentisering från sina användare ska använda molnbaserade Azure Multi-Factor Authentication. Befintliga kunder som har aktiverat MFA Server före 1 juli kommer att kunna hämta den senaste versionen, framtida uppdateringar och skapa autentiseringsuppgifter för aktivering som vanligt.

## <a name="secure-ad-fs-20-with-a-proxy"></a>Skydda AD FS 2.0 med en proxy

Om du vill skydda AD FS 2.0 med en proxy installerar du Azure Multi-Factor Authentication Server på ADFS-proxyservern.

### <a name="configure-iis-authentication"></a>Konfigurera IIS-autentisering

1. Klicka på ikonen för **IIS-autentisering** på den vänstra menyn i Azure Multi-Factor Authentication Server.
2. Klicka på fliken **Formulärbaserad**.
3. Klicka på **Lägg till**.

   ![IIS-autentisering för MFA Server-fönstret](./media/howto-mfaserver-adfs-2/setup1.png)

4. Om du vill identifiera användarnamn, lösenord och domänvariabler automatiskt anger du inloggnings-URL:en (som https://sso.contoso.com/adfs/ls) i dialogrutan Konfigurera formulärbaserad webbplats automatiskt och klickar på **OK**.
5. Markera rutan **Kräv Azure Multi-Factor Authentication-användarmatchning** om alla användare har importerats eller ska importeras till servern och använda tvåstegsverifiering. Om ett stort antal användare inte har importerats till servern än, eller om de ska undantas från tvåstegsverifiering, lämnar du rutan avmarkerad.
6. Om sidvariablerna inte kan identifieras automatiskt klickar du på knappen **Ange manuellt** i dialogrutan Konfigurera formulärbaserad webbplats automatiskt.
7. I dialogrutan Lägg till formulärbaserad webbplats anger du URL:en till inloggningssidan för AD FS i fältet Skicka URL (till exempel https://sso.contoso.com/adfs/ls) och anger ett programnamn (valfritt). Programnamnet visas i Azure Multi-Factor Authentication-rapporter och kan visas i autentiseringsmeddelanden i SMS- eller mobilappar.
8. Ange formatet för begäran till **POST eller GET**.
9. Ange användarnamnsvariabeln (ctl00$ContentPlaceHolder1$UsernameTextBox) och lösenordsvariabeln (ctl00$ContentPlaceHolder1$PasswordTextBox). Om en textruta för domänen visas på den formulärbaserade inloggningssidan anger du även domänvariabeln. Du kan behöva gå till inloggningssidan i en webbläsare, högerklicka på sidan och välja **Visa källa** för att hitta namnen på inmatningsrutorna på inloggningssidan.
10. Markera rutan **Kräv Azure Multi-Factor Authentication-användarmatchning** om alla användare har importerats eller ska importeras till servern och använda tvåstegsverifiering. Om ett stort antal användare inte har importerats till servern än, eller om de ska undantas från tvåstegsverifiering, lämnar du rutan avmarkerad.

    ![Lägg till formulärbaserad webbplats till MFA-servern](./media/howto-mfaserver-adfs-2/manual.png)

11. Klicka på **Avancerat...** och granska de avancerade inställningarna. Bland de inställningar som du kan konfigurera finns följande:

    - Välj en anpassad nekandeväxlingsfil
    - Cachelagra lyckade autentiseringar till webbplatsen med hjälp av cookies
    - Välj hur du vill autentisera primära autentiseringsuppgifter

12. Eftersom AD FS-proxyservern troligen inte ska anslutas till domänen kan du använda LDAP för att ansluta till domänkontrollanten för användarimport och förautentisering. Klicka på fliken **Primär autentisering** i dialogrutan Avancerad formulärbaserad webbplats och välj **LDAP-bindning** för autentiseringstypen Förautentisering.
13. När du är klar klickar du på **OK** så visas dialogrutan Lägg till formulärbaserad webbplats igen.
14. Stäng dialogrutan genom att klicka på **OK**.
15. När URL- och sidvariablerna har identifierats eller angetts visas webbplatsdata på panelen Formulärbaserad.
16. Klicka på fliken **Ursprunglig modul** och välj servern, webbplatsen som AD FS-proxyn körs under (t.ex. ”Standardwebbplats”) eller AD FS-proxyprogrammet (t.ex. ”Is” under ”adfs”) för att aktivera IIS-pluginprogrammet på önskad nivå.
17. Klicka på rutan **Aktivera IIS-autentisering** överst på skärmen.

Nu är IIS-autentisering aktiverat.

### <a name="configure-directory-integration"></a>Konfigurera katalogintegrering

Du har aktiverat IIS-autentisering, men för att kunna utföra förautentisering till din Active Directory (AD) via LDAP måste du konfigurera LDAP-anslutningen till domänkontrollanten.

1. Det gör du genom att klicka på ikonen **Katalogintegrering**.
2. På fliken Inställningar väljer du alternativknappen **Använd specifik LDAP-konfiguration**.

   ![Konfigurera LDAP-inställningar för specifika LDAP-inställningar](./media/howto-mfaserver-adfs-2/ldap1.png)

3. Klicka på **Redigera**.
4. I dialogrutan Redigera LDAP-konfiguration fyller du i fälten med den information som krävs för att ansluta till AD-domänkontrollanten. Fältbeskrivningar finns inkluderade i hjälpfilen för Azure Multi-Factor Authentication-server.
5. Testa LDAP-anslutningen genom att klicka på knappen **Testa**.

   ![Testa LDAP-konfiguration i MFA Server](./media/howto-mfaserver-adfs-2/ldap2.png)

6. Klicka på **OK** om LDAP-anslutningstestet lyckas.

### <a name="configure-company-settings"></a>Konfigurera företagsinställningar

1. Klicka därefter på ikonen **Företagsinställningar** och välj fliken **Matcha användarnamn**.
2. Välj alternativknappen **Matcha användarnamn med unikt identifierarattribut för LDAP**.
3. Om användare anger sina användarnamn i ”domän\användarnamn”-format måste servern kunna extrahera domänen från användarnamnet när LDAP-frågan skapas. Detta kan göras via en registerinställning.
4. Öppna Registereditorn och gå till HKEY_LOCAL_MACHINE/SOFTWARE/Wow6432Node/Positive Networks/PhoneFactor på en 64-bitarsserver. Om du arbetar på en 32-bitarsserver utelämnar du ”Wow6432Node” i sökvägen. Skapa en DWORD-registernyckel med namnet ”UsernameCxz_stripPrefixDomain” och ange värdet till 1. Nu skyddas AD FS-proxyn av Azure Multi-Factor Authentication.

Se till att användarna har importerats från Active Directory till servern. Se den [tillförlitliga IP-adresser](#trusted-ips) om du vill tillåta interna IP-adresser så att tvåstegsverifiering inte krävs vid inloggning till webbplatsen från dessa platser.

![Registereditorn för att konfigurera Företagsinställningar](./media/howto-mfaserver-adfs-2/reg.png)

## <a name="ad-fs-20-direct-without-a-proxy"></a>AD FS 2.0 direkt utan någon proxy

Du kan skydda AD FS när AD FS-proxy inte används. Installera Azure Multi-Factor Authentication Server på AD FS-servern och konfigurera servern genom att följa stegen nedan:

1. Klicka på ikonen för **IIS-autentisering** på den vänstra menyn i Azure Multi-Factor Authentication Server.
2. Klicka på fliken **HTTP**.
3. Klicka på **Lägg till**.
4. I dialogrutan Lägg till grundläggande webbadress anger du URL:en för AD FS-webbplatsen där HTTP-autentiseringen utförs (till exempel https://sso.domain.com/adfs/ls/auth/integrated) i fältet Bas-URL. Ange sedan ett programnamn (valfritt). Programnamnet visas i Azure Multi-Factor Authentication-rapporter och kan visas i autentiseringsmeddelanden i SMS- eller mobilappar.
5. Om du vill justerar du värdena för timeout vid inaktivitet och längsta sessionstid.
6. Markera rutan **Kräv Azure Multi-Factor Authentication-användarmatchning** om alla användare har importerats eller ska importeras till servern och använda tvåstegsverifiering. Om ett stort antal användare inte har importerats till servern än, eller om de ska undantas från tvåstegsverifiering, lämnar du rutan avmarkerad.
7. Markera kryssrutan för cachelagring av cookies om du vill.

   ![AD FS 2.0 direkt utan någon proxy](./media/howto-mfaserver-adfs-2/noproxy.png)

8. Klicka på **OK**.
9. Klicka på fliken **Ursprunglig modul** och välj servern, webbplatsen (t.ex. ”Standardwebbplats”) eller ADFS-programmet (t.ex. ”Is” under ”adfs”) för att aktivera IIS-pluginprogrammet på önskad nivå.
10. Klicka på rutan **Aktivera IIS-autentisering** överst på skärmen.

Nu skyddas AD FS av Azure Multi-Factor Authentication.

Se till att användarna har importerats från Active Directory till servern. Se avsnittet tillförlitliga IP-adresser om du vill tillåta interna IP-adresser så att tvåstegsverifiering inte krävs vid inloggning till webbplatsen från dessa platser.

## <a name="trusted-ips"></a>Tillförlitliga IP-adresser

Tillförlitliga IP-adresser låter användarna kringgå Azure Multi-Factor Authentication för webbplatsförfrågningar som kommer från specifika IP-adresser eller undernät. Du vill kanske till exempel undanta användare från tvåstegsverifiering när de loggar in från kontoret. För att göra det anger du kontorets undernät som en tillförlitlig IP-adress.

### <a name="to-configure-trusted-ips"></a>Så här konfigurerar du tillförlitliga IP-adresser

1. Klicka på fliken **Tillförlitliga IP-adresser** i avsnittet IIS-autentisering.
2. Klicka på **Lägg …** till.
3. När dialogrutan Lägg till tillförlitlig IP-adress visas väljer du en av alternativknapparna **Enkel IP**, **IP-intervall** eller **Undernät**.
4. Ange IP-adressen, IP-adressintervall eller undernät som ska tillåtas. Om du anger ett undernät väljer du lämplig nätmask och klickar på **OK**.

![Konfigurera tillförlitliga IP-adresser till MFA-servern](./media/howto-mfaserver-adfs-2/trusted.png)
