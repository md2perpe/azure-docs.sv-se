---
title: Azure MFA Server med AD FS i Windows Server – Azure Active Directory
description: Den här artikeln beskriver hur du kommer igång med Azure Multi-Factor Authentication och AD FS i Windows Server 2012 R2 och 2016.
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
ms.openlocfilehash: 4b38918dc6b80539ef8852aa408cda501958c9b1
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67057440"
---
# <a name="configure-azure-multi-factor-authentication-server-to-work-with-ad-fs-in-windows-server"></a>Konfigurera Azure Multi-Factor Authentication Server så att den fungerar med AD FS i Windows Server

Om du använder Active Directory Federation Services (AD FS) och vill skydda molnresurser eller lokala resurser kan du konfigurera Azure Multi-Factor Authentication Server att fungera med AD FS. Den här konfigurationen utlöser tvåstegsverifiering för slutpunkter med högt värde.

I den här artikeln diskuterar vi hur du använder Azure Multi-Factor Authentication Server med AD FS i Windows Server 2012 R2 eller Windows Server 2016. Mer information finns i [Skydda molnresurser och lokala resurser med hjälp av Azure Multi-Factor Authentication Server med AD FS 2.0](howto-mfaserver-adfs-2.md).

> [!IMPORTANT]
> Från och med den 1 juli 2019 erbjuder Microsoft inte längre MFA Server för nya distributioner. Nya kunder som vill kräva multifaktorautentisering från sina användare ska använda molnbaserade Azure Multi-Factor Authentication. Befintliga kunder som har aktiverat MFA Server före 1 juli kommer att kunna hämta den senaste versionen, framtida uppdateringar och skapa autentiseringsuppgifter för aktivering som vanligt.

## <a name="secure-windows-server-ad-fs-with-azure-multi-factor-authentication-server"></a>Skydda Windows Server AD FS med Azure Multi-Factor Authentication Server

När du installerar Azure Multi-Factor Authentication Server kan du välja mellan följande alternativ:

* Installera Azure Multi-Factor Authentication Server lokalt på samma server som AD FS.
* Installera Azure Multi-Factor Authentication-adaptern lokalt på AD FS-servern och installera sedan Multi-Factor Authentication-servern på en annan dator.

Innan du börjar bör du vara medveten om följande:

* Du måste inte installera Azure Multi-Factor Authentication-servern på AD FS-servern. Dock måste du installera Multi-Factor Authentication-adaptern för AD FS på en Windows Server 2012 R2 eller Windows Server 2016 som kör AD FS. Du kan installera servern på en annan dator om du installerar AD FS-adaptern separat på AD FS-federationsservern. Se följande information om hur du installerar adaptern separat.
* Om din organisation använder verifieringsmetoder med SMS eller mobilappar innehåller strängarna som definierats i företagsinställningarna platshållaren <$*application_name*$>. Du kan ange namnet på ett program som ersätter platshållaren i MFA Server v7.1. Den här platshållaren ersätts inte automatiskt när du använder AD FS-adaptern i v 7.0 eller senare. För äldre versioner tar du bort platshållaren från relevanta strängar när du skyddar AD FS.
* Det konto som du använder för att logga in måste ha användarbehörighet att skapa säkerhetsgrupper i Active Directory-tjänsten.
* Installationsguiden för Multi-Factor Authentication AD FS-adapter skapar en säkerhetsgrupp med namnet PhoneFactor Admins i din instans av Active Directory. AD FS-tjänstkontot för federationstjänsten läggs sedan till i den här gruppen. Verifiera att gruppen PhoneFactor Admins verkligen har skapats i domänkontrollanten och att AD FS-tjänstkontot är medlem av den här gruppen. Om det behövs lägger du till AD FS-tjänstkontot i gruppen PhoneFactor Admins på domänkontrollanten manuellt.
* Information om hur du installerar webbtjänst-SDK med användarportalen finns i [Distribuera användarportalen för Azure Multi-Factor Authentication Server.](howto-mfaserver-deploy-userportal.md)

### <a name="install-azure-multi-factor-authentication-server-locally-on-the-ad-fs-server"></a>Installera Azure Multi-Factor Authentication Server lokalt på AD FS-servern

1. Hämta och installera Azure Multi-Factor Authentication Server på AD FS-servern. Mer information om installationen finns i [Komma igång med Azure Multi-Factor Authentication Server](howto-mfaserver-deploy.md).
2. Klicka på **AD FS**-ikonen i Azure Multi-Factor Authentication Server-hanteringskonsolen. Markera alternativen **Tillåt användarregistrering** och **Tillåt användare att välja metod**.
3. Välj ytterligare alternativ som du vill ange för din organisation.
4. Klicka på **Installera AD FS-adapter**.

   ![Installera ADFS-adaptern från konsolen MFA Server](./media/howto-mfaserver-adfs-2012/server.png)

5. Om Active Directory-fönstret visas, betyder det två saker. Datorn är ansluten till en domän och Active Directory-konfigurationen för att skydda kommunikationen mellan AD FS-adaptern och Multi-Factor Authentication-tjänsten är ofullständig. Klicka på **Nästa** om du vill slutföra konfigurationen automatiskt, eller markera kryssrutan **Hoppa över automatisk konfigurering av Active Directory och konfigurera inställningarna manuellt**. Klicka på **Nästa**.
6. Om fönstret Lokal grupp visas, betyder det två saker. Datorn är inte ansluten till en domän och konfigurationen för lokal grupp för att skydda kommunikationen mellan AD FS-adaptern och Multi-Factor Authentication-tjänsten är ofullständig. Klicka på **Nästa** för att slutföra konfigurationen automatiskt, eller markera kryssrutan **Hoppa över automatisk konfigurering av lokal grupp och konfigurera inställningarna manuellt**. Klicka på **Nästa**.
7. Klicka på **Nästa** i installationsguiden. Azure Multi-Factor Authentication Server skapar gruppen PhoneFactor Admins och lägger till AD FS-tjänstkontot i PhoneFactor Admins-gruppen.
8. Klicka på **Nästa** på sidan **Installationsprogram**.
9. Klicka på **Nästa** i installationsprogrammet för Multi-Factor Authentication AD FS-adaptern.
10. Klicka på **Stäng** när installationen är klar.
11. När adaptern har installerats måste du registrera den med AD FS. Öppna Windows PowerShell och kör följande kommando:

    `C:\Program Files\Multi-Factor Authentication Server\Register-MultiFactorAuthenticationAdfsAdapter.ps1`

12. För att använda den nyligen registrerade adaptern, redigera den globala autentiseringsprincipen i AD FS. I AD FS-hanteringskonsolen går du till noden **Autentiseringsprinciper**. I avsnittet **Multi-Factor Authentication** klickar du på länken **Redigera** bredvid avsnittet **Globala inställningar**. I fönstret **Redigera global autentiseringsprincip** väljer du **Multi-Factor Authentication** som ytterligare en autentiseringsmetod och klickar på **OK**. Adaptern registreras som WindowsAzureMultiFactorAuthentication. Starta om AD FS-tjänsten för att registreringen ska börja gälla.

![Redigera global autentiseringsprincip](./media/howto-mfaserver-adfs-2012/global.png)

Nu är Multi-Factor Authentication-servern konfigurerad som ytterligare en autentiseringsprovider för användning med AD FS.

## <a name="install-a-standalone-instance-of-the-ad-fs-adapter-by-using-the-web-service-sdk"></a>Installera en fristående instans av AD FS-adaptern med webbtjänst-SDK

1. Installera webbtjänst-SDK på servern som kör Multi-Factor Authentication Server.
2. Kopiera följande filer från katalogen \Program\Multi-Factor Authentication Server till den server där du planerar att installera AD FS-adaptern:
   * MultiFactorAuthenticationAdfsAdapterSetup64.msi
   * Register-MultiFactorAuthenticationAdfsAdapter.ps1
   * Unregister-MultiFactorAuthenticationAdfsAdapter.ps1
   * MultiFactorAuthenticationAdfsAdapter.config
3. Kör installationsfilen MultiFactorAuthenticationAdfsAdapterSetup64.msi.
4. Starta installationen genom att klicka på **Nästa** i installationsprogrammet för Multi-Factor Authentication AD FS-adaptern.
5. Klicka på **Stäng** när installationen är klar.

## <a name="edit-the-multifactorauthenticationadfsadapterconfig-file"></a>Redigera filen MultiFactorAuthenticationAdfsAdapter.config

Redigera filen MultiFactorAuthenticationAdfsAdapter.config genom att följa stegen nedan:

1. Ange noden **UseWebServiceSdk** till **sant**.  
2. Ange värdet för **WebServiceSdkUrl** till URL:en för webbtjänst-SDK för Multi-Factor Authentication. Till exempel: *https:\/\/contoso.com/\<certificatename > /MultiFactorAuthWebServiceSdk/PfWsSdk.asmx*, där  *\<certificatename >* är namnet på certifikatet.  
3. Redigera skriptet Register-MultiFactorAuthenticationAdfsAdapter.ps1 genom att lägga till `-ConfigurationFilePath &lt;path&gt;` i slutet av kommandot `Register-AdfsAuthenticationProvider`, där *&lt;sökvägen&gt;* är den fullständiga sökvägen till filen MultiFactorAuthenticationAdfsAdapter.config.

### <a name="configure-the-web-service-sdk-with-a-username-and-password"></a>Konfigurera webbtjänst-SDK med ett användarnamn och lösenord

Det finns två alternativ för att konfigurera webbtjänst-SDK. Det första alternativet är att använda ett användarnamn och lösenord, det andra är att använda ett klientcertifikat. Följ dessa steg för det första alternativet eller hoppa framåt till det andra.  

1. Ange värdet för **WebServiceSdkUsername** till ett konto som är medlem i säkerhetsgruppen PhoneFactor Admins. Använd formatet &lt;domän&gt;&#92;&lt;användarnamn&gt;.  
2. Ange värdet för **WebServiceSdkPassword** till lämpligt kontolösenord.

### <a name="configure-the-web-service-sdk-with-a-client-certificate"></a>Konfigurera webbtjänst-SDK med ett klientcertifikat

Följ dessa steg om du vill konfigurera webbtjänst-SDK med ett klientcertifikat om du inte vill använda ett användarnamn och lösenord.

1. Skaffa ett klientcertifikat från en certifikatutfärdare för servern som kör webbtjänst-SDK. Lär dig hur du [skaffar klientcertifikat](https://technet.microsoft.com/library/cc770328.aspx).  
2. Importera klientcertifikatet till den lokala datorns personliga certifikatarkiv på servern som kör webbtjänst-SDK. Kontrollera att certifikatutfärdarens offentliga certifikat finns i certifikatarkivet för betrodda rotcertifikat.  
3. Exportera de offentliga och privata nycklarna för klientcertifikatet till en PFX-fil.  
4. Exportera den offentliga nyckeln i Base64-format till en CER-fil.  
5. I Serverhanteraren kontrollerar du att funktionen Web Server (IIS)\Web Server\Security\IIS Client Certificate Mapping Authentication är installerad. Om den inte är installerad väljer du **Lägg till roller och funktioner** för att lägga till funktionen.  
6. I IIS-hanteraren dubbelklickar du på **Konfigurationsredigeraren** i webbplatsen som innehåller den virtuella katalogen för webbtjänst-SDK. Det är viktigt att du markerar webbplatsen och inte den virtuella katalogen.  
7. Gå till avsnittet **system.webServer/security/authentication/iisClientCertificateMappingAuthentication**.  
8. Ange aktiverat till **sant**.  
9. Ange oneToOneCertificateMappingsEnabled till **sant**.  
10. Klicka på knappen **...** bredvid oneToOneMappings och klicka sedan på länken **Lägg till**.  
11. Öppna CER-filen med Base64-format som du exporterade tidigare. Ta bort *-----BEGIN CERTIFICATE-----* , *-----END CERTIFICATE-----* och eventuella radbrytningar. Kopiera den resulterande strängen.  
12. Ange certificate till strängen som du kopierade i föregående steg.  
13. Ange aktiverat till **sant**.  
14. Ange userName till ett konto som är medlem i säkerhetsgruppen PhoneFactor Admins. Använd formatet &lt;domän&gt;&#92;&lt;användarnamn&gt;.  
15. Ange lösenordet till lämpligt kontolösenord och stäng Konfigurationsredigeraren.  
16. Klicka på länken **Använd**.  
17. I den virtuella katalogen för webbtjänst-SDK dubbelklickar du på **Autentisering**.  
18. Kontrollera att ASP.NET-personifiering och Grundläggande autentisering är **aktiverat** och att alla andra objekt är **inaktiverade**.  
19. Dubbelklicka på **SSL-inställningar** i den virtuella katalogen för webbtjänst-SDK.  
20. Ange Klientcertifikat till **Acceptera** och klicka på **Använd**.  
21. Kopiera PFX-filen som du exporterade tidigare till servern som kör AD FS-adaptern.  
22. Importera PFX-filen till den lokala datorns personliga certifikatarkiv.  
23. Högerklicka och välj **Hantera privata nycklar** och ge sedan läsbehörighet till kontot som du använde för att logga in till AD FS-tjänsten.  
24. Öppna klientcertifikatet och kopiera tumavtrycket från fliken **Information**.  
25. I filen MultiFactorAuthenticationAdfsAdapter.config anger du **WebServiceSdkCertificateThumbprint** till strängen som du kopierade i föregående steg.  

Registrera adaptern genom att köra skriptet \Program Files\Multi-Factor Authentication Server\Register-MultiFactorAuthenticationAdfsAdapter.ps1 i PowerShell. Adaptern registreras som WindowsAzureMultiFactorAuthentication. Starta om AD FS-tjänsten för att registreringen ska börja gälla.

## <a name="secure-azure-ad-resources-using-ad-fs"></a>Skydda Azure AD-resurser med hjälp av AD FS

Ställ in en anspråksregel så att Active Directory Federation Services genererar multipleauthn-kravet när en användare utför tvåstegsverifiering om du vill skydda din molnresurs. Det här anspråket överförs till Azure AD. Följ dessa steg:

1. Öppna AD FS-hantering.
2. Välj **Förlitande partsförtroenden** till vänster.
3. Högerklicka på **Microsoft Office 365 Identity Platform** och välj **Redigera anspråksregler...**

   ![Redigera anspråksregler i AD FS-konsolen](./media/howto-mfaserver-adfs-2012/trustedip1.png)

4. För Utfärdande av transformeringsregler klickar du på **Lägg till regel.**

   ![Redigera regler i AD FS-konsolen](./media/howto-mfaserver-adfs-2012/trustedip2.png)

5. I guiden Lägg till anspråksregel för transformering väljer du **Släpp igenom eller Filtrera ett inkommande anspråk** i listrutan och klickar sedan på **Nästa**.

   ![Transformera anspråk regeln guiden Lägg till](./media/howto-mfaserver-adfs-2012/trustedip3.png)

6. Namnge din regel.
7. Välj **Autentiseringsmetodreferenser** som den inkommande anspråkstypen.
8. Välj **Släpp igenom alla anspråksvärden**.

    ![Transformera anspråk regeln guiden Lägg till](./media/howto-mfaserver-adfs-2012/configurewizard.png)

9. Klicka på **Slutför**. Stäng AD FS-hanteringskonsolen.

## <a name="troubleshooting-logs"></a>Felsökningsloggar

För att du ska få hjälp med felsökningsproblem med MFA-serverns AD FS-adapter följer du stegen nedan för att aktivera ytterligare loggning.

1. I MFA-serverns gränssnitt öppnar du AD FS-avsnittet och markerar kryssrutan **Aktivera loggning**.
2. På varje AD FS-server använder du **regedit.exe** för att skapa en registernyckel för strängvärdet `Computer\HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Positive Networks\PhoneFactor\InstallPath` med värdet `C:\Program Files\Multi-Factor Authentication Server\` (eller någon annan katalog som du väljer).  **Tänk på att ett avslutande snedstreck är viktigt.**
3. Skapa `C:\Program Files\Multi-Factor Authentication Server\Logs`-katalog (eller någon annan katalog som anges i **Steg 2**).
4. Bevilja ändringsåtkomst till Logs-katalogen till AD FS-tjänstkontot.
5. Starta om AD FS-tjänsten.
6. Kontrollera att `MultiFactorAuthAdfsAdapter.log`-filen skapades i Logs-katalogen.

## <a name="related-topics"></a>Relaterade ämnen

Hjälp med felsökning finns i [Azure Multi-Factor Authentication FAQs (Vanliga frågor och svar om Azure Multi-Factor Authentication)](multi-factor-authentication-faq.md)
