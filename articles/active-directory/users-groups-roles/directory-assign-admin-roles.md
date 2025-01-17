---
title: Administratören rollbeskrivningar och behörigheter – Azure Active Directory | Microsoft Docs
description: En administratörsroll kan lägga till användare, tilldela administrativa roller, återställa användarlösenord, hantera användarlicenser eller hantera domäner.
services: active-directory
author: curtand
manager: mtillman
search.appverid: MET150
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 06/04/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 73ca740a4dcca3bdbb1951e55df4061364a5c646
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67083926"
---
# <a name="administrator-role-permissions-in-azure-active-directory"></a>Behörigheter för administratör i Azure Active Directory

Med Azure Active Directory (Azure AD) kan ange du begränsade administratörer att hantera identitet uppgifter i mindre privilegierade roller. Administratörer kan tilldelas för sådant som att lägga till eller ändra användare, tilldela administrativa roller, återställa användarlösenord, hantera användarlicenser och hantera domännamn. Standard användarbehörigheter kan ändras endast i användarinställningarna i Azure AD.

Globala administratörer har åtkomst till alla administrativa funktioner. Som standard tilldelas den person som registrerar sig för en Azure-prenumeration rollen som Global administratör för katalogen. Endast globala administratörer och Privilegierade Rolladministratörer kan delegera administratörsroller. För att minska risken för företaget rekommenderar vi att du tilldela den här rollen till endast ett fåtal personer i företaget.


## <a name="assign-or-remove-administrator-roles"></a>Tilldela eller ta bort administratörsroller

Om du vill lära dig mer om att tilldela administrativa roller till en användare i Azure Active Directory, se [visa och tilldela administratörsroller i Azure Active Directory](directory-manage-roles-portal.md).

## <a name="available-roles"></a>Tillgängliga roller

Följande administratörsroller är tillgängliga:

* **[Programadministratör](#application-administrator)** : Användare i den här rollen kan skapa och hantera alla aspekter av företagsprogram och programregistreringar proxyinställningarna för programmet. Den här rollen ger även möjlighet att godkänna delegerade behörigheter och programbehörigheter exklusive Microsoft Graph och Azure AD Graph. Användare som tilldelats den här rollen har inte lagts som ägare när du skapar nya programregistreringar eller företagsprogram.

  <b>Viktiga</b>: Den här rollen ger möjlighet att hantera programmet autentiseringsuppgifter. Användare som är tilldelade den här rollen kan lägga till autentiseringsuppgifter i ett program och använda dessa autentiseringsuppgifter för att personifiera programmets identitet. Om programmets identitet har beviljats åtkomst till Azure Active Directory, till exempel möjligheten att skapa eller uppdatera användare eller andra objekt, kan sedan en användare som tilldelats den här rollen utföra dessa åtgärder medan personifieras programmet. Den här möjligheten att personifiera programmets identitet kan vara en höjning av privilegier över vad användaren kan göra via sin rolltilldelningar i Azure AD. Det är viktigt att förstå att tilldela en användare till rollen programadministratör ger dem möjlighet att personifiera identitet för ett program.

* **[Programutvecklare](#application-developer)** : Användare i den här rollen kan skapa programregistreringar när den ”användare kan registrera program” är inställt på Nej. Den här rollen ger även behörighet att ge samtycke åt det egna när de ”användare kan godkänna att appar får åtkomst till företagets data å deras vägnar” är inställt på Nej. Användare som tilldelats den här rollen har lagts till som ägare när du skapar nya programregistreringar eller företagsprogram.

* **[Administratören Authentication](#authentication-administrator)** : Användare med den här rollen kan ange eller återställa autentiseringsuppgifter för icke-password. Autentisering-administratörer kan kräva att användare kan Omregistrera mot befintliga autentiseringsuppgifter för icke-lösenord (till exempel MFA eller FIDO) och återkalla **MFA sparas på enheten**, som uppmanar för MFA på den nästa inloggningen av användare icke-administratörer eller som har tilldelats följande roller:
  * Autentisering-administratör
  * Katalogläsare
  * Gäst bjuder in
  * Meddelandecenterläsare
  * Rapportläsare

  En administratör för autentisering är för närvarande i offentlig förhandsversion. Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

  <b>Viktiga</b>: Användare med den här rollen kan ändra autentiseringsuppgifter för personer som kan ha åtkomst till känslig eller privat information eller kritiska konfiguration i och utanför Azure Active Directory. Ändra autentiseringsuppgifterna för en användare kan det innebära att möjlighet att anta användarens identitet och behörigheter. Exempel:

  * Programregistrering och företagsprogram ägare, som kan hantera autentiseringsuppgifterna för appar som de äger. Apparna kan ha privilegierad behörigheter i Azure AD och någon annanstans inte beviljas till administratörer för autentisering. Den här sökvägen som administratör autentisering kanske kan anta identiteten av ett programmets ägare och sedan ytterligare anta identiteten av ett privilegierat program genom att uppdatera autentiseringsuppgifterna för programmet.
  * Azure-prenumerationsägare, vilka kan ha åtkomst till känslig eller privat information eller kritiska konfiguration i Azure.
  * Säkerhetsgrupper och Office 365-gruppen ägare, vilka kan hantera gruppmedlemskap. Dessa grupper kan bevilja åtkomst till känslig eller privat information eller kritiska konfiguration i Azure AD och andra platser.
  * Administratörer i andra tjänster utanför Azure AD som Exchange Online, Office-säkerhet och efterlevnad Center och personal system.
  * Icke-administratörer som chefer, juridiska ombud och Personal anställda som kan ha åtkomst till känslig eller privat information.

* **[Azure Information Protection-administratör](#azure-information-protection-administrator)** : Användare med den här rollen har alla behörigheter i Azure Information Protection-tjänsten. Den här rollen kan konfigurera etiketter för Azure Information Protection-principen, hantera skyddsmallar och aktivera skydd. Den här rollen ger inte några behörigheter i Identity Protection Center, Privileged Identity Management, övervaka Office 365 Service Health eller Office 365 säkerhets- och Efterlevnadscenter.

* **[Flow-administratör för B2C-användare](#b2c-user-flow-administrator)** : Användare med den här rollen kan skapa och hantera B2C Användarflöden (även kallat ”inbyggd” principer) i Azure Portal. Genom att skapa eller redigera användarflöden kan dessa användare ändra html/CSS/javascript-innehållet i användarens upplevelse, ändra MFA-kraven per användarflöde, ändra anspråken i token och justera sessionsinställningar för alla principer i klienten. Å andra sidan, den här rollen inkluderar möjligheten att granska användardata, eller göra ändringar i attribut som ingår i klient-schemat. Ändringar i Identitetsramverk (även kallat anpassat) principer är också utanför omfånget för den här rollen.

* **[B2C Användaradministratör Flow attributet](#b2c-user-flow-attribute-administrator)** : Användare med rollen Lägg till eller ta bort anpassade attribut som är tillgängliga för alla användarflöden som i klienten. Därför måste kan användare med den här rollen ändra eller lägga till nya element i schemat för slutanvändaren och påverka beteendet för alla användarflöden som och indirekt innebär ändringar vilka data som kan vara och svar för slutanvändare och skickas slutligen som anspråk till program. Den här rollen kan inte redigera användarflöden.

* **[Administratör för B2C IEF Keyset](#b2c-ief-keyset-administrator)** :    Användare kan skapa och hantera principnycklar och hemligheter för tokenkryptering, token signaturer och anspråk kryptering/dekryptering. Genom att lägga till nya nycklar befintliga nyckelbehållare, kan den här begränsad administratör förnya hemligheter efter behov utan att påverka befintliga program. Den här användaren kan se det fullständiga innehållet på dessa hemligheter och deras utgångsdatum även när de har skapats.
    
  <b>Viktigt:</b> det här är en känsliga roll. Administratörsrollen keyset bör granskas noggrant och tilldelas med försiktighet under Förproduktion och produktion.

* **[Administratör för B2C IEF princip](#b2c-ief-policy-administrator)** : Användare i den här rollen har möjlighet att skapa, läsa, uppdatera och ta bort alla anpassade principer i Azure AD B2C och därför har fullständig kontroll över den Identitetsramverk i relevanta Azure AD B2C-klienten. Genom att redigera principer för kan den här användaren upprätta direkt federation med externa indentitetsprovidrar, ändra katalogschemat, ändra alla användarinriktade innehåll (HTML, CSS, JavaScript), ändra kraven för att slutföra en autentisering, skapa nya användare, skicka användardata till externa system, inklusive fullständig migrering och redigera information om alla användare, inklusive tidskritiska fält som lösenord och telefonnummer. Omvänt kan kan inte den här rollen ändra krypteringsnycklarna eller redigera hemligheter som används för federation i klienten.

  <b>Viktigt:</b> B2 IEF princip administratör är en mycket känslig roll som ska tilldelas en mycket begränsad för klienter i produktion. Aktiviteter med hjälp av dessa användare bör granskas noggrant, särskilt för klienter i produktion.

* **[Faktureringsadministratör](#billing-administrator)** : Gör inköp, hanterar prenumerationer, hanterar supportärenden och övervakar tjänstehälsa.

* **[Molnprogramadministratör](#cloud-application-administrator)** : Användare i den här rollen har samma behörigheter som rollen programadministratör exklusive möjligheten att hantera programproxy. Den här rollen ger möjlighet att skapa och hantera alla aspekter av företagsprogram och programregistreringar. Den här rollen ger även möjlighet att godkänna delegerade behörigheter och programbehörigheter exklusive Microsoft Graph och Azure AD Graph. Användare som tilldelats den här rollen har inte lagts som ägare när du skapar nya programregistreringar eller företagsprogram.

  <b>Viktiga</b>: Den här rollen ger möjlighet att hantera programmet autentiseringsuppgifter. Användare som är tilldelade den här rollen kan lägga till autentiseringsuppgifter i ett program och använda dessa autentiseringsuppgifter för att personifiera programmets identitet. Om programmets identitet har beviljats åtkomst till Azure Active Directory, till exempel möjligheten att skapa eller uppdatera användare eller andra objekt, kan sedan en användare som tilldelats den här rollen utföra dessa åtgärder medan personifieras programmet. Den här möjligheten att personifiera programmets identitet kan vara en höjning av privilegier över vad användaren kan göra via sin rolltilldelningar i Azure AD. Det är viktigt att förstå att tilldela en användare till rollen Molnprogramadministratör ger dem möjlighet att personifiera identitet för ett program.

* **[Molnenhetsadministratör](#cloud-device-administrator)** : Användare i den här rollen kan aktivera, inaktivera, och ta bort enheter i Azure AD och Läs Windows 10 BitLocker-nycklar (om sådan finns) i Azure-portalen. Rollen ger inte behörighet att hantera andra egenskaper på enheten.

* **[Efterlevnadsadministratör](#compliance-administrator)** : Användare med den här rollen har behörighet att hantera efterlevnad-relaterade funktioner i Microsoft 365 efterlevnadscenter, Administrationscenter för Microsoft 365, Azure, och Office 365-säkerhet och Efterlevnadscenter. Användare kan också hantera alla funktioner i administrationscentret för Exchange, Teams och Skype för företag-administrationscentret och skapa supportbegäranden för Azure och Microsoft 365. Mer information finns på [om Office 365-administratörsroller](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

  I | Kan göra
  ----- | ----------
  [Microsoft 365 efterlevnadscenter](https://protection.office.com) | Skydda och hantera din organisations data över Microsoft 365-tjänster<br>Hantera aviseringar för efterlevnad
  [Compliance Manager](https://docs.microsoft.com/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud) | Spåra, tilldela och verifiera din organisations regelefterlevnad aktiviteter
  [Och Efterlevnadscenter för Office 365-säkerhet](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Hantera datastyrning<br>Utför juridisk information och data undersökning<br>Hantera begäran om ämne
  [Intune](https://docs.microsoft.com/intune/role-based-access-control) | Visa alla Intune-granskningsdata
  [Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | Har skrivskyddad behörighet och kan hantera aviseringar<br>Kan skapa och ändra principer för filer och Tillåt styrningsåtgärder<br> Kan visa de inbyggda rapporterna under datahantering

* **[Data Efterlevnadsadministratör](#compliance-data-administrator)** : Användare med den här rollen har behörighet att skydda och spåra data i efterlevnadscentret för Microsoft 365, Microsoft 365 Administrationscenter och Azure. Användare kan också hantera alla funktioner i administrationscentret för Exchange, Compliance Manager, Teams och Skype för företag-administrationscentret och skapa supportbegäranden för Azure och Microsoft 365.

  I | Kan göra
  ----- | ----------
  [Microsoft 365 efterlevnadscenter](https://protection.office.com) | Övervaka efterlevnad-relaterade principer i Microsoft 365-tjänster<br>Hantera aviseringar för efterlevnad
  [Compliance Manager](https://docs.microsoft.com/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud) | Spåra, tilldela och verifiera din organisations regelefterlevnad aktiviteter
  [Och Efterlevnadscenter för Office 365-säkerhet](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Hantera datastyrning<br>Utför juridisk information och data undersökning<br>Hantera begäran om ämne
  [Intune](https://docs.microsoft.com/intune/role-based-access-control) | Visa alla Intune-granskningsdata
  [Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | Har skrivskyddad behörighet och kan hantera aviseringar<br>Kan skapa och ändra principer för filer och Tillåt styrningsåtgärder<br> Kan visa de inbyggda rapporterna under datahantering

* **[Administratör för villkorsstyrd åtkomst](#conditional-access-administrator)** : Användare med den här rollen har behörighet att hantera inställningar för Azure Active Directory villkorlig åtkomst.
  > [!NOTE]
  > Om du vill distribuera principen för villkorlig åtkomst för Exchange ActiveSync i Azure, måste användaren också vara en Global administratör.
  
* **[Customer Lockbox åtkomst godkännaren](#customer-lockbox-access-approver)** : Hanterar [Customer Lockbox begär](https://docs.microsoft.com/office365/admin/manage/customer-lockbox-requests) i din organisation. De kan får e-postmeddelanden för Customer Lockbox begäranden och godkänna och Neka förfrågningar från Administrationscenter för Microsoft 365. De kan också aktivera funktionen Customer Lockbox eller inaktivera. Endast globala administratörer kan återställa lösenorden för personer som har tilldelats den här rollen.
  <!--  This was announced in August of 2018. https://techcommunity.microsoft.com/t5/Security-Privacy-and-Compliance/Customer-Lockbox-Approver-Role-Now-Available/ba-p/223393-->

* **[Desktop Analytics-administratör](#desktop-analytics-administrator)** : Användare i den här rollen kan hantera Desktop Analytics och Office-anpassning & princip för tjänster. Detta inkluderar möjligheten att visa tillgångar, skapa planer för distribution, visa status för distribution och hälsa för Desktop analys. För Office-anpassning & princip för tjänsten kan den här rollen du hantera principer för Office.

* **[Enhetsadministratör](#device-administrators)** : Den här rollen är tilldelas endast som en ytterligare lokal administratör i [Enhetsinställningar](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/DeviceSettings/menuId/). Användare med den här rollen blir lokala administratörer på alla Windows 10-enheter som är anslutna till Azure Active Directory. De har inte behörighet att hantera enheters objekt i Azure Active Directory. 

* **[Katalogläsare](#directory-readers)** : Det här är en roll som ska tilldelas endast till äldre program som inte stöder den [godkänna Framework](../develop/quickstart-v1-integrate-apps-with-azure-ad.md). Inte tilldela den till användare.

* **[Konton för katalogsynkronisering](#directory-synchronization-accounts)** : Använd inte. Den här rollen är tilldelas automatiskt till Azure AD Connect-tjänsten och inte avsedd eller stöd för annan användning.

* **[Katalogskrivare](#directory-writers)** : Det här är en äldre roll som ska tilldelas till program som inte stöder den [godkänna Framework](../develop/quickstart-v1-integrate-apps-with-azure-ad.md). Det ska inte tilldelas alla användare.

* **[Dynamics 365-administratör / CRM-administratören](#crm-service-administrator)** : Användare med den här rollen har globala behörigheter inom Microsoft Dynamics 365 Online när tjänsten finns närvarande, liksom möjlighet att hantera supportbegäranden och kontrollera tjänstens hälsotillstånd. Mer information på [använda administratörsrollen för att hantera din klient](https://docs.microsoft.com/dynamics365/customer-engagement/admin/use-service-admin-role-manage-tenant).
  > [!NOTE] 
  > I Microsoft Graph API, Azure AD Graph API och Azure AD PowerShell identifieras rollen som ”tjänstadministratör för Dynamics 365”. Det är ”Dynamics 365-administratör” i den [Azure-portalen](https://portal.azure.com).

* **[Exchange-administratören](#exchange-service-administrator)** : Användare med den här rollen har globala behörigheter inom Microsoft Exchange Online när tjänsten finns. Har också möjlighet att skapa och hantera alla Office 365-grupper, hantera supportbegäranden och kontrollera tjänstens hälsotillstånd. Mer information på [om Office 365-administratörsroller](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).
  > [!NOTE]
  > I Microsoft Graph API, Azure AD Graph API och Azure AD PowerShell identifieras rollen som ”Exchange Service-administratör”. Det är ”Exchange-administratör” i den [Azure-portalen](https://portal.azure.com). Det är ”Exchange Online administratör i fältet” i den [administrationscentret för Exchange](https://go.microsoft.com/fwlink/p/?LinkID=529144). 

* **[Externa identitet tjänstproviderns administratör](#external-identity-provider-administrator)** : Den här administratören hanterar federation mellan Azure Active Directory-klienter och externa indentitetsprovidrar. Med den här rollen kan användarna lägga till nya Identitetsproviders och konfigurera alla tillgängliga inställningar (t.ex. autentisering sökväg, tjänst-id tilldelade nyckelbehållare). Den här användaren kan aktivera klienten ska lita på autentiseringar från externa indentitetsprovidrar. Resulterande påverkan på slutanvändarupplevelse beror på vilken typ av klient:
  * Azure Active Directory-klienter för anställda och partners: Tillägg av en federation (t.ex. med Gmail) påverkar omedelbart alla gäst inbjudningar som ännu inte har lösts in. Se [att lägga till Google som en identitetsleverantör för B2B-gästanvändare](https://docs.microsoft.com/azure/active-directory/b2b/google-federation).
  * Azure Active Directory B2C-klienter: Tillägg av en federation (t.ex. Facebook eller med en annan Azure Active Directory) påverkar direkt inte slutanvändaren flöden förrän identitetsprovidern har lagts till som ett alternativ i ett användarflöde (även kallat inbyggd princip). Se [konfigurera ett Microsoft-konto som identitetsprovider](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-msa-app) ett exempel. Om du vill ändra användarflöden, krävs rollen begränsad av ”B2C Flow Användaradministratör”.

* **[Global administratör / företaget administratör](#company-administrator)** : Användare med den här rollen har åtkomst till alla administrativa funktioner i Azure Active Directory, samt tjänster som använder Azure Active Directory-identiteter som Microsoft 365 security center Microsoft 365 efterlevnadscenter Exchange Online, SharePoint Online och Skype för företag Online. Den person som registrerar sig för Azure Active Directory-klient blir global administratör. Endast globala administratörer kan tilldela andra administratörsroller. Det kan finnas mer än en global administratör i företaget. Globala administratörer kan återställa lösenordet för alla användare och alla andra administratörer.

  > [!NOTE]
  > I Microsoft Graph API, Azure AD Graph API och Azure AD PowerShell identifieras rollen som ”företagets administratör”. Det är ”Global administratör” i den [Azure-portalen](https://portal.azure.com).
  >
  >

* **[Gäst bjuder in](#guest-inviter)** : Användare i den här rollen kan hantera inbjudningar för Azure Active Directory B2B gäst när den **medlemmar kan bjuda in** användaren är inställt på Nej. Mer information om B2B-samarbete i [om Azure AD B2B-samarbete](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b). Den omfattar inte några andra behörigheter.

* **[Intune-administratör](#intune-service-administrator)** : Användare med den här rollen har globala behörigheter inom Microsoft Intune Online när tjänsten finns. Dessutom innehåller den här rollen möjlighet att hantera användare och enheter för att associera principer, samt skapa och hantera grupper. Mer information på [rollbaserad administrationskontroll (RBAC) med Microsoft Intune](https://docs.microsoft.com/intune/role-based-access-control)
  > [!NOTE]
  > I Microsoft Graph API, Azure AD Graph API och Azure AD PowerShell identifieras rollen som ”Intune-tjänstadministratör”. Det är ”Intune-administratör” i den [Azure-portalen](https://portal.azure.com).
  
 * **[Kaizala Administrator](#kaizala-administrator)** : Användare med den här rollen har globala behörigheter att hantera inställningar i Microsoft Kaizala när tjänsten finns närvarande, liksom möjlighet att hantera supportbegäranden och kontrollera tjänstens hälsotillstånd.
Dessutom kan användaren komma åt rapporter som rör införande och användning av Kaizala av organisationsmedlemmar och business-rapporter som skapas med hjälp av Kaizala-åtgärder. 

* **[Licensadministratör](#license-administrator)** : Användare i den här rollen kan lägga till, ta bort och uppdatera licensiera tilldelningar för användare, grupper (med gruppbaserad licensiering) och hantera användningsplats på användare. Rollen ger inte möjlighet att köpa eller hantera prenumerationer, skapa eller hantera grupper, eller skapa eller hantera användarna, förutom användningsplatsen. Den här rollen har ingen behörighet att visa, skapa eller hantera supportärenden.

* **[Sekretess Meddelandecenterläsare](#message-center-privacy-reader)** : Användare i den här rollen kan övervaka alla meddelanden i Meddelandecentret, inklusive data sekretess meddelanden. Meddelandecenter sekretess läsarna få e-postmeddelanden, inklusive de som är relaterade till datasekretess och de kan säga upp prenumerationen med hjälp av Meddelandecenterinställningar. Endast den globala administratören och Message Center sekretess läsaren kan läsa data sekretess meddelanden. Dessutom innehåller den här rollen möjligheten att visa grupper, domäner och prenumerationer. Den här rollen har ingen behörighet att visa, skapa eller hantera begäranden.

* **[Meddelandecenterläsare](#message-center-reader)** : Användare i den här rollen kan övervaka meddelanden och rådgivande hälsotillstånd uppdateringar i [Office 365-meddelandecentret](https://support.office.com/article/Message-center-in-Office-365-38FB3333-BFCC-4340-A37B-DEDA509C2093) för organisationen på konfigurerade tjänster som Exchange, Intune och Microsoft Teams. Meddelandecenter läsarna få veckovis e-sammandrag av inlägg, uppdateringar, och kan dela center meddelandeposter i Office 365. I Azure AD har användare som tilldelats den här rollen endast läsåtkomst på Azure AD-tjänster, till exempel användare och grupper. Den här rollen har ingen behörighet att visa, skapa eller hantera supportärenden.

* **[Partner nivå 1-Support](#partner-tier1-support)** : Använd inte. Den här rollen har gjorts inaktuell och kommer att tas bort från Azure AD i framtiden. Den här rollen är avsedd att användas av ett litet antal Microsoft återförsäljning-partner och är inte avsedd för allmänt bruk.

* **[Partner – nivå 2-stöd](#partner-tier2-support)** : Använd inte. Den här rollen har gjorts inaktuell och kommer att tas bort från Azure AD i framtiden. Den här rollen är avsedd att användas av ett litet antal Microsoft återförsäljning-partner och är inte avsedd för allmänt bruk.

* **[Supportavdelningsadministratören (lösenord)](#helpdesk-administrator)** : Användare med den här rollen kan ändra lösenord, ogiltigförklara uppdateringstoken, hanterar tjänstbegäranden och kontrollera tjänstens hälsotillstånd. Ogiltigförklara en uppdateringstoken Tvingar användaren att logga in igen. Helpdeks-administratörer kan återställa lösenord och ogiltigförklara uppdateringstoken för andra användare som är icke-administratörer eller tilldelats följande roller:
  * Katalogläsare
  * Gäst bjuder in
  * Supportavdelningsadministratör
  * Meddelandecenterläsare
  * Rapportläsare
  
  <b>Viktiga</b>: Användare med den här rollen kan ändra lösenord för personer som kan ha åtkomst till känslig eller privat information eller kritiska konfiguration i och utanför Azure Active Directory. Ändra lösenordet för en användare kan det innebära att möjlighet att anta användarens identitet och behörigheter. Exempel:
  * Programregistrering och företagsprogram ägare, som kan hantera autentiseringsuppgifterna för appar som de äger. Apparna kan ha privilegierad behörigheter i Azure AD och någon annanstans inte beviljas till Supportadministratörer. Den här sökvägen som en Supportavdelningsadministratör kanske kan anta identiteten av ett programmets ägare och sedan ytterligare anta identiteten av ett privilegierat program genom att uppdatera autentiseringsuppgifterna för programmet.
  * Azure-prenumerationsägare, vilka kan ha åtkomst till känslig eller privat information eller kritiska konfiguration i Azure.
  * Säkerhetsgrupper och Office 365-gruppen ägare, vilka kan hantera gruppmedlemskap. Dessa grupper kan bevilja åtkomst till känslig eller privat information eller kritiska konfiguration i Azure AD och andra platser.
  * Administratörer i andra tjänster utanför Azure AD som Exchange Online, Office-säkerhet och efterlevnad Center och personal system.
  * Icke-administratörer som chefer, juridiska ombud och Personal anställda som kan ha åtkomst till känslig eller privat information.


  > [!NOTE]
  > Delegera administrativa behörigheter över delmängder av användare och tillämpa principer på en delmängd användare kan göra med [administrativa enheter (förhandsversion)](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-administrative-units).


  > [!NOTE]
  > Den här rollen kallades tidigare ”Lösenordsadministratör” [Azure-portalen](https://portal.azure.com/). Vi ändrar dess namn till ”Supportavdelningsadministratör” som matchar dess namn i Azure AD PowerShell, Azure AD Graph API och Microsoft Graph API. För en kort stund kommer vi ändra namnet till ”(lösenord) Supportavdelningsadministratör” i Azure-portalen innan ändringen ”Supportavdelningsadministratör”.


* **[Power BI-administratör](#power-bi-service-administrator)** : Användare med den här rollen har globala behörigheter inom Microsoft Power BI när tjänsten finns närvarande, liksom möjlighet att hantera supportbegäranden och kontrollera tjänstens hälsotillstånd. Mer information på [förstå administratörsrollen för Power BI](https://docs.microsoft.com/power-bi/service-admin-role).
  > [!NOTE]
  > I Microsoft Graph API, Azure AD Graph API och Azure AD PowerShell identifieras rollen som ”Power BI-tjänstadministratör”. Det är ”Power BI-administratör” i den [Azure-portalen](https://portal.azure.com).

* **[Privilegierad administratören Authentication](#privileged-authentication-administrator)** : Användare med den här rollen kan ange eller återställa icke-password autentiseringsuppgifter för alla användare, inklusive globala administratörer. Privilegierade autentisering-administratörer kan tvinga användare att registrera mot befintliga icke-password autentiseringsuppgifter (t.ex. MFA, FIDO) och återkalla ”Kom ihåg att MFA på enheten' prompt för MFA vid nästa inloggning för alla användare. Privilegierade autentisering-administratörer kan:
  * Tvinga användare att registrera mot befintliga icke-password autentiseringsuppgifter (t.ex. MFA, FIDO)
  * Återkalla ”Kom ihåg att MFA på enheten' prompt för MFA vid nästa inloggning

* **[Privilegierad Rolladministratör](#privileged-role-administrator)** : Användare med den här rollen kan hantera rolltilldelningar i Azure Active Directory, liksom i Azure AD Privileged Identity Management. Den här rollen kan dessutom hantera alla aspekter av Privileged Identity Management och administrativa enheter.

  <b>Viktiga</b>: Den här rollen ger dig möjlighet att hantera tilldelningar för alla Azure AD-roller, inklusive den globala administratörsrollen. Den här rollen omfattar inte andra Privilegierade funktioner i Azure AD, som skapar eller uppdaterar användare. Dock kan användare som tilldelats den här rollen bevilja sig själva eller andra ytterligare behörighet genom att tilldela ytterligare roller.

* **[Rapporterar läsare](#reports-reader)** : Användare med den här rollen kan visa användning rapporteringsdata och instrumentpanelen rapporter i Microsoft 365 Administrationscenter och införande kontexten pack i Power BI. Dessutom rollen ger åtkomst till loggar in rapporter och aktivitet i Azure AD och data som returneras av Microsoft Graph rapporterings-API. En användare som tilldelats rollen rapportläsare kan komma åt endast relevanta användnings- och användningsstatistik. De har inte någon administratörsbehörighet för att konfigurera inställningar eller åtkomst till produktspecifika Administrationscenter som Exchange. Den här rollen har ingen behörighet att visa, skapa eller hantera supportärenden.

* **[Sök administratör](#search-administrator)** : Användare i den här rollen har fullständig åtkomst till alla Microsoft Search-hanteringsfunktioner i Microsoft 365 Administrationscenter. Sök administratörer kan delegera Sök-administratörer och Sök redigeraren roller till användare, och skapa och hantera innehåll som bokmärken Q & som, och platser. Dessa användare kan dessutom visa meddelandecenter, kontrollera tjänstens hälsotillstånd och skapar tjänstbegäranden.

* **[Sök redigeraren](#search-editor)** : Användare i den här rollen kan skapa, hantera och ta bort innehåll för Microsoft Search i Microsoft 365 Administrationscenter, inklusive bokmärken Q & som och platser.

* **[Säkerhetsadministratör](#security-administrator)** : Användare med den här rollen har behörighet att hantera säkerhetsrelaterade funktioner i Microsoft 365 Säkerhetscenter, Azure Active Directory Identity Protection, Azure Information Protection och Office 365-säkerhet och Efterlevnadscenter. Mer information om behörigheter för Office 365 finns på [behörigheter i Office 365 säkerhets- och Efterlevnadscenter](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1).
  
  I | Kan göra
  --- | ---
  [Microsoft 365 Säkerhetscenter](https://protection.office.com) | Övervaka säkerhetsrelaterade principer i Microsoft 365-tjänster<br>Hantera hot och aviseringar<br>Visa rapporter
  Identity Protection Center | Alla behörigheter för rollen Säkerhetsläsare<br>Dessutom kan utföra alla åtgärder för Identity Protection Center förutom för återställning av lösenord
  [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) | Alla behörigheter för rollen Säkerhetsläsare<br>**Det går inte att** hantera rolltilldelningar i Azure AD eller inställningar
  [Och Efterlevnadscenter för Office 365-säkerhet](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Hantera säkerhetsprinciper<br>Visa, undersöka och svara på säkerhetshot<br>Visa rapporter
  Azure Advanced Threat Protection | Övervaka och svara på misstänkta aktiviteter
  Windows Defender ATP och EDR | Tilldela roller<br>Hantera datorgrupper<br>Konfigurera slutpunkt för identifiering av hot och automatiska reparationer<br>Visa, undersöka och vidta åtgärder vid aviseringar
  [Intune](https://docs.microsoft.com/intune/role-based-access-control) | Vyer användare, enhet, registrering, konfiguration och programinformation<br>Det går inte att göra ändringar i Intune
  [Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | Lägga till administratörer, lägga till principer och inställningar, ladda upp loggar och utföra styrningsåtgärder
  [Azure Security Center](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) | Kan visa säkerhetsprinciper, security tillstånd, redigera säkerhetsprinciper, Visa aviseringar och rekommendationer, avvisa aviseringar och rekommendationer
  [Tjänstehälsa för Office 365](https://docs.microsoft.com/office365/enterprise/view-service-health) | Visa hälsotillståndet för Office 365-tjänster

* **[Säkerhetsansvarig](#security-operator)** : Användare med den här rollen kan hantera aviseringar och har global skrivskyddad åtkomst på säkerhetsrelaterade funktioner, inklusive all information i Microsoft 365 Säkerhetscenter, Azure Active Directory, Identity Protection, Privileged Identity Management och Office 365 Säkerhets- och Efterlevnadscenter. Mer information om behörigheter för Office 365 finns på [behörigheter i Office 365 säkerhets- och Efterlevnadscenter](https://docs.microsoft.com/office365/securitycompliance/permissions-in-the-security-and-compliance-center).

  I | Kan göra
  --- | ---
  [Microsoft 365 Säkerhetscenter](https://protection.office.com) | Alla behörigheter för rollen Säkerhetsläsare<br>Visa, undersöka och åtgärda säkerhetsaviseringar för hot
  Identity Protection Center | Alla behörigheter för rollen Säkerhetsläsare<br>Dessutom kan utföra alla åtgärder för Identity Protection Center förutom för återställning av lösenord
  [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) | Alla behörigheter för rollen Säkerhetsläsare
  [Och Efterlevnadscenter för Office 365-säkerhet](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Alla behörigheter för rollen Säkerhetsläsare<br>Visa, undersöka och åtgärda säkerhetsaviseringar
  Windows Defender ATP och EDR | Alla behörigheter för rollen Säkerhetsläsare<br>Visa, undersöka och åtgärda säkerhetsaviseringar
  [Intune](https://docs.microsoft.com/intune/role-based-access-control) | Alla behörigheter för rollen Säkerhetsläsare
  [Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | Alla behörigheter för rollen Säkerhetsläsare
  [Tjänstehälsa för Office 365](https://docs.microsoft.com/office365/enterprise/view-service-health) | Visa hälsotillståndet för Office 365-tjänster
<!--* **[Security Operator](#security-operator)**: Users with this role can manage alerts and have global read-only access on security-related feature, including all information in Microsoft 365 security center, Azure Active Directory, Identity Protection, Privileged Identity Management.-->

* **[Security Reader](#security-reader)** : Användare med den här rollen har global skrivskyddad åtkomst på säkerhetsrelaterade funktioner, inklusive all information i Microsoft 365 Säkerhetscenter, Azure Active Directory, Identity Protection, Privileged Identity Management, samt möjlighet att läsa Azure Active Directory-inloggningsrapporter och granskningsloggar, och i Office 365 säkerhets- och Efterlevnadscenter. Mer information om behörigheter för Office 365 finns på [behörigheter i Office 365 säkerhets- och Efterlevnadscenter](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1).

  I | Kan göra
  --- | ---
  [Microsoft 365 Säkerhetscenter](https://protection.office.com) | Visa säkerhetsrelaterade principer för Microsoft 365-tjänster<br>Visa säkerhetshot och aviseringar<br>Visa rapporter
  Identity Protection Center | Läsa alla säkerhetsrapporter och inställningsinformation för säkerhetsfunktioner<br><ul><li>Skydd mot skräppost<li>Kryptering<li>Skydd mot dataförlust<li>Mot skadlig kod<li>Advanced Threat Protection<li>Mot nätfiske<li>Mailflow regler
  [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) | Har skrivskyddad åtkomst till all information som visas i Azure AD PIM: Principer och rapporter för Azure AD-rolltilldelningar security granskar och i framtiden skrivskyddad åtkomst till principdata och rapporter för scenarier utöver Azure AD-rolltilldelning.<br>**Det går inte att** registrera dig för Azure AD PIM eller göra några ändringar. PIM-portalen eller via PowerShell kan någon i den här rollen aktivera ytterligare roller (till exempel Global administratör eller privilegierad Rolladministratör), om användaren är berättigad till dem.
  [Och Efterlevnadscenter för Office 365-säkerhet](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Visa säkerhetsprinciper<br>Visa och undersöka hot<br>Visa rapporter
  Windows Defender ATP och EDR | Visa och undersöka aviseringar
  [Intune](https://docs.microsoft.com/intune/role-based-access-control) | Vyer användare, enhet, registrering, konfiguration och information om programmet. Det går inte att göra ändringar i Intune.
  [Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | Har skrivskyddad behörighet och kan hantera aviseringar
  [Azure Security Center](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) | Visa rekommendationer och aviseringar, visa IPSec-principer security tillstånd, men det går inte att göra ändringar
  [Tjänstehälsa för Office 365](https://docs.microsoft.com/office365/enterprise/view-service-health) | Visa hälsotillståndet för Office 365-tjänster

* **[Tjänstsupportadministratör](#service-support-administrator)** : Användare med den här rollen kan öppna supportbegäranden med Microsoft för Azure och Office 365-tjänster och visa tjänstinstrumentpanelen och meddelandecenter i datacentret i den [Azure-portalen](https://portal.azure.com) och [Microsoft 365 Administrationscenter](https://admin.microsoft.com). Mer information på [om Office 365-administratörsroller](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).
  > [!NOTE]
  > I Microsoft Graph API, Azure AD Graph API och Azure AD PowerShell identifieras rollen som ”Tjänstsupportadministratör”. Det är ”tjänstadministratör” i den [Azure-portalen](https://portal.azure.com), [Microsoft 365 Administrationscenter](https://admin.microsoft.com), och Intune-portalen.

* **[SharePoint Administrator](#sharepoint-service-administrator)** : Användare med den här rollen har globala behörigheter inom Microsoft SharePoint Online när tjänsten finns närvarande, liksom möjligheten att skapa och hantera alla Office 365-grupper, hantera supportbegäranden och kontrollera tjänstens hälsotillstånd. Mer information på [om Office 365-administratörsroller](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).
  > [!NOTE]
  > I Microsoft Graph API, Azure AD Graph API och Azure AD PowerShell identifieras rollen som ”SharePoint-tjänstadministratör”. Det är ”SharePoint-administratör” i den [Azure-portalen](https://portal.azure.com).

* **[Skype för företag – / Lync administratör](#lync-service-administrator)** : Användare med den här rollen har globala behörigheter inom Microsoft Skype för företag när tjänsten finns närvarande, samt hantera Skype-specifika användarattribut i Azure Active Directory. Den här rollen ger dessutom möjlighet att hantera supportbegäranden och kontrollera tjänstens hälsotillstånd och komma åt Teams och Skype för Business Admin Center. Kontot måste också ha licens för team eller teamen PowerShell-cmdlet: ar kan köras. Mer information på [om the Skype för företag-administratörsroll](https://support.office.com/article/about-the-skype-for-business-admin-role-aeb35bda-93fc-49b1-ac2c-c74fbeb737b5) och team licensinformation på [Skype för företag och Microsoft Teams-tillägg-licensiering](https://docs.microsoft.com/skypeforbusiness/skype-for-business-and-microsoft-teams-add-on-licensing/skype-for-business-and-microsoft-teams-add-on-licensing)

  > [!NOTE]
  > I Microsoft Graph API, Azure AD Graph API och Azure AD PowerShell identifieras rollen som ”Lync-tjänstadministratör”. Det är ”Skype för Business Administrator” i den [Azure-portalen](https://portal.azure.com/).

* **[Team administratör](#teams-service-administrator)** : Användare i den här rollen kan hantera alla aspekter av Microsoft Teams-arbetsbelastning via Microsoft Teams och Skype för företag-administrationscentret och respektive PowerShell-moduler. Detta inkluderar bland andra delar alla hanteringsverktyg som är relaterade till telefoni, meddelanden, möten och team själva. Den här rollen ger dessutom möjlighet att skapa och hantera alla Office 365-grupper, hantera supportbegäranden och kontrollera tjänstens hälsotillstånd.
  > [!NOTE]
  > I Microsoft Graph API, Azure AD Graph API och Azure AD PowerShell identifieras rollen som ”team Service-administratör”. Det är ”team administratör” i den [Azure-portalen](https://portal.azure.com).

* **[Teams kommunikation administratör](#teams-communications-administrator)** : Användare i den här rollen kan hantera aspekter av arbetsbelastningen Microsoft Teams rör röst- och telefoni. Detta inkluderar hanteringsverktygen för tilldelning av telefon, röst- och möte principer och fullständig åtkomst till anropet analytics verktygsuppsättningen.

* **[Teams kommunikation supporttekniker](#teams-communications-support-engineer)** : Användare i den här rollen kan felsöka kommunikationsproblem i Microsoft Teams och Skype för företag med hjälp av användaren anrop felsökningsverktygen i Microsoft Teams och Skype för företag-administrationscentret. Användare i den här rollen kan visa fullständiga anropet registrera information för alla deltagare som ingår. Den här rollen har ingen behörighet att visa, skapa eller hantera supportärenden.

* **[Teams kommunikation Support Specialist](#teams-communications-support-specialist)** : Användare i den här rollen kan felsöka kommunikationsproblem i Microsoft Teams och Skype för företag med hjälp av användaren anrop felsökningsverktygen i Microsoft Teams och Skype för företag-administrationscentret. Användare i den här rollen kan bara se användarinformation i anropet för den specifika användaren har sökt. Den här rollen har ingen behörighet att visa, skapa eller hantera supportärenden.

* **[Användaradministratör](#user-administrator)** : Användare med den här rollen kan skapa användare, och hantera alla aspekter av användare med vissa begränsningar (se nedan) och kan uppdatera principerna för Lösenordets giltighetstid. Dessutom kan kan användare med den här rollen skapa och hantera alla grupper. Den här rollen inkluderar även möjligheten att skapa och hantera användarvyer, hantera supportbegäranden och kontrollera tjänstens hälsotillstånd.

  | | |
  | --- | --- |
  |Allmänna behörigheter|<p>Skapa användare och grupper</p><p>Skapa och hantera användarvyer</p><p>Hantera supportärenden för Office<p>Uppdatera lösenordets upphörande|
  |<p>På alla användare, inklusive alla administratörer</p>|<p>Hantera licenser</p><p>Hantera alla användaregenskaper utom User Principal Name</p>
  |Endast på användare som är icke-administratörer eller begränsade administrativa roller i något av följande:<ul><li>Katalogläsare<li>Gäst bjuder in<li>Supportavdelningsadministratör<li>Meddelandecenterläsare<li>Rapportläsare<li>Användaradministratör|<p>Ta bort och återställning</p><p>Inaktivera och aktivera</p><p>Ogiltigförklara uppdatera token</p><p>Hantera egenskaper för alla användare, inklusive användarens huvudnamn</p><p>Återställ lösenord</p><p>Uppdatera (FIDO) enhetsnycklar</p>
  
  <b>Viktiga</b>: Användare med den här rollen kan ändra lösenord för personer som kan ha åtkomst till känslig eller privat information eller kritiska konfiguration i och utanför Azure Active Directory. Ändra lösenordet för en användare kan det innebära att möjlighet att anta användarens identitet och behörigheter. Exempel:
  * Programregistrering och företagsprogram ägare, som kan hantera autentiseringsuppgifterna för appar som de äger. Apparna kan ha privilegierad behörigheter i Azure AD och någon annanstans inte beviljas till administratörer. Den här sökvägen som en användare med rollen kanske kan anta identiteten av ett programmets ägare och sedan ytterligare anta identiteten av ett privilegierat program genom att uppdatera autentiseringsuppgifterna för programmet.
  * Azure-prenumerationsägare, vilka kan ha åtkomst till känslig eller privat information eller kritiska konfiguration i Azure.
  * Säkerhetsgrupper och Office 365-gruppen ägare, vilka kan hantera gruppmedlemskap. Dessa grupper kan bevilja åtkomst till känslig eller privat information eller kritiska konfiguration i Azure AD och andra platser.
  * Administratörer i andra tjänster utanför Azure AD som Exchange Online, Office-säkerhet och efterlevnad Center och personal system.
  * Icke-administratörer som chefer, juridiska ombud och Personal anställda som kan ha åtkomst till känslig eller privat information.

## <a name="role-permissions"></a>Rollbehörigheter
I följande tabeller beskrivs de särskilda behörigheterna i Azure Active Directory för varje roll. Vissa roller kan ha ytterligare behörigheter i Microsoft-tjänster utanför Azure Active Directory.

### <a name="application-administrator"></a>Programadministratör
Kan skapa och hantera alla aspekter av appregistreringar och enterprise-appar.

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| Microsoft.aad.Directory/Applications/Audience/Update | Uppdatera applications.audience-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/applications/authentication/update | Uppdatera applications.authentication-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/applications/basic/update | Uppdatera grundläggande egenskaper för program i Azure Active Directory. |
| Microsoft.aad.Directory/Applications/Create | Skapa program i Azure Active Directory. |
| microsoft.aad.directory/applications/credentials/update | Uppdatera applications.credentials-egenskapen i Azure Active Directory. |
| Microsoft.aad.Directory/Applications/delete | Ta bort program i Azure Active Directory. |
| Microsoft.aad.Directory/Applications/owners/Update | Uppdatera applications.owners-egenskapen i Azure Active Directory. |
| Microsoft.aad.Directory/Applications/permissions/Update | Uppdatera applications.permissions-egenskapen i Azure Active Directory. |
| Microsoft.aad.Directory/Applications/policies/Update | Uppdatera applications.policies-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/create | Skapa appRoleAssignments i Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/read | Läsa appRoleAssignments i Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/update | Uppdatera appRoleAssignments i Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/delete | Ta bort appRoleAssignments i Azure Active Directory. |
| microsoft.aad.directory/auditLogs/allProperties/read | Läsa alla egenskaper (inklusive Privilegierade egenskaper) för auditLogs i Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/basic/read | Läsa policies.applicationConfiguration-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/basic/update | Uppdatera policies.applicationConfiguration-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/create | Skapa principer i Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/delete | Ta bort principer i Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/owners/read | Läsa policies.applicationConfiguration-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/owners/update | Uppdatera policies.applicationConfiguration-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/policyAppliedTo/read | Läsa policies.applicationConfiguration-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/update | Uppdatera servicePrincipals.appRoleAssignedTo-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/update | Uppdatera servicePrincipals.appRoleAssignments-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/audience/update | Uppdatera servicePrincipals.audience-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/authentication/update | Uppdatera servicePrincipals.authentication-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/basic/update | Uppdatera grundläggande egenskaper för servicePrincipals i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/create | Skapa servicePrincipals i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/credentials/update | Uppdatera servicePrincipals.credentials-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/delete | Ta bort servicePrincipals i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/owners/update | Uppdatera servicePrincipals.owners-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/permissions/update | Uppdatera servicePrincipals.permissions-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/policies/update | Uppdatera servicePrincipals.policies-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/signInReports/allProperties/read | Läsa alla egenskaper (inklusive Privilegierade egenskaper) för signInReports i Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Läsa och konfigurera Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Skapa och hantera Azure supportärenden. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Läsa och konfigurera Office 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Skapa och hantera Office 365-supportbegäranden. |

### <a name="application-developer"></a>Programutvecklare
Kan skapa programregistreringar oberoende av den ”användare kan registrera program' inställningen.

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.aad.directory/applications/createAsOwner | Skapa program i Azure Active Directory. Skaparen läggs till som första ägare och det skapade objektet räknas mot Skaparens kvot på 250 skapade objekt. |
| microsoft.aad.directory/appRoleAssignments/createAsOwner | Skapa appRoleAssignments i Azure Active Directory. Skaparen läggs till som första ägare och det skapade objektet räknas mot Skaparens kvot på 250 skapade objekt. |
| microsoft.aad.directory/oAuth2PermissionGrants/createAsOwner | Skapa oAuth2PermissionGrants i Azure Active Directory. Skaparen läggs till som första ägare och det skapade objektet räknas mot Skaparens kvot på 250 skapade objekt. |
| microsoft.aad.directory/servicePrincipals/createAsOwner | Skapa servicePrincipals i Azure Active Directory. Skaparen läggs till som första ägare och det skapade objektet räknas mot Skaparens kvot på 250 skapade objekt. |

### <a name="authentication-administrator"></a>Autentisering-administratör
Kan visa, ange och återställa information om autentisering för alla icke-administratörsanvändare.

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Ogiltigförklaras alla tokens för användaruppdatering i Azure Active Directory. |
| microsoft.aad.directory/users/strongAuthentication/update | Egenskaper för stark autentisering som autentiseringsuppgifter för MFA. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Läsa och konfigurera Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Skapa och hantera Azure supportärenden. |
| microsoft.office365.webPortal/allEntities/basic/read | Läsa grundläggande egenskaper för alla resurser i microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Läsa och konfigurera Office 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Skapa och hantera Office 365-supportbegäranden. |

### <a name="azure-information-protection-administrator"></a>Azure Information Protection-administratör
Kan hantera alla aspekter av Azure Information Protection-tjänsten.

  > [!NOTE]
  > Den här rollen har ytterligare behörighet utanför Azure Active Directory. Mer information finns i rollen beskrivningen ovan.
  >
  >

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.azure.informationProtection/allEntities/allTasks | Hantera alla aspekter av Azure Information Protection. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Läsa och konfigurera Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Skapa och hantera Azure supportärenden. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Läsa och konfigurera Office 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Skapa och hantera Office 365-supportbegäranden. |

### <a name="b2c-user-flow-administrator"></a>B2C User Flow Administrator
Skapa och hantera alla aspekter av användarflöden.

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.aad.b2c/userFlows/allTasks | Läsa och konfigurera användarflöden i Azure Active Directory B2C. |

### <a name="b2c-user-flow-attribute-administrator"></a>B2C User Flow Attribute Administrator
Skapa och hantera tillgängliga för alla användarflöden som attributschemat.

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.aad.b2c/userAttributes/allTasks | Läsa och konfigurera användarattribut i Azure Active Directory B2C. |

### <a name="b2c-ief-keyset-administrator"></a>B2C IEF Keyset Administrator
Hantera hemligheter för federation och kryptering i den Identitetsramverk.

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.aad.b2c/trustFramework/keySets/allTasks | Läsa och konfigurera viktiga uppsättningar i Azure Active Directory B2C. |

### <a name="b2c-ief-policy-administrator"></a>B2C IEF Policy Administrator
Skapa och hantera framework förtroendeprinciper i den Identitetsramverk.

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.aad.b2c/trustFramework/policies/allTasks | Läsa och konfigurera anpassade principer i Azure Active Directory B2C. |

### <a name="billing-administrator"></a>Faktureringsadministratör
Kan utföra vanliga faktureringsrelaterade uppgifter som uppdatering av betalningsinformation.

  > [!NOTE]
  > Den här rollen har ytterligare behörighet utanför Azure Active Directory. Mer information finns i rollen beskrivningen ovan.
  >
  >

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.aad.directory/organization/basic/update | Uppdatera grundläggande egenskaper på organisation i Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Läsa och konfigurera Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Skapa och hantera Azure supportärenden. |
| microsoft.commerce.billing/allEntities/allTasks | Hantera alla aspekter av Office 365-fakturering. |
| microsoft.office365.webPortal/allEntities/basic/read | Läsa grundläggande egenskaper för alla resurser i microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Läsa och konfigurera Office 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Skapa och hantera Office 365-supportbegäranden. |


### <a name="cloud-application-administrator"></a>Molnprogramadministratör
Kan skapa och hantera alla aspekter av appregistreringar och enterprise-appar förutom App Proxy.

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| Microsoft.aad.Directory/Applications/Audience/Update | Uppdatera applications.audience-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/applications/authentication/update | Uppdatera applications.authentication-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/applications/basic/update | Uppdatera grundläggande egenskaper för program i Azure Active Directory. |
| Microsoft.aad.Directory/Applications/Create | Skapa program i Azure Active Directory. |
| microsoft.aad.directory/applications/credentials/update | Uppdatera applications.credentials-egenskapen i Azure Active Directory. |
| Microsoft.aad.Directory/Applications/delete | Ta bort program i Azure Active Directory. |
| Microsoft.aad.Directory/Applications/owners/Update | Uppdatera applications.owners-egenskapen i Azure Active Directory. |
| Microsoft.aad.Directory/Applications/permissions/Update | Uppdatera applications.permissions-egenskapen i Azure Active Directory. |
| Microsoft.aad.Directory/Applications/policies/Update | Uppdatera applications.policies-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/create | Skapa appRoleAssignments i Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/update | Uppdatera appRoleAssignments i Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/delete | Ta bort appRoleAssignments i Azure Active Directory. |
| microsoft.aad.directory/auditLogs/allProperties/read | Läsa alla egenskaper (inklusive Privilegierade egenskaper) för auditLogs i Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/create | Skapa principer i Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/basic/read | Läsa policies.applicationConfiguration-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/basic/update | Uppdatera policies.applicationConfiguration-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/delete | Ta bort principer i Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/owners/read | Läsa policies.applicationConfiguration-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/owners/update | Uppdatera policies.applicationConfiguration-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/policyAppliedTo/read | Läsa policies.applicationConfiguration-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/update | Uppdatera servicePrincipals.appRoleAssignedTo-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/update | Uppdatera servicePrincipals.appRoleAssignments-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/audience/update | Uppdatera servicePrincipals.audience-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/authentication/update | Uppdatera servicePrincipals.authentication-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/basic/update | Uppdatera grundläggande egenskaper för servicePrincipals i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/create | Skapa servicePrincipals i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/credentials/update | Uppdatera servicePrincipals.credentials-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/delete | Ta bort servicePrincipals i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/owners/update | Uppdatera servicePrincipals.owners-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/permissions/update | Uppdatera servicePrincipals.permissions-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/policies/update | Uppdatera servicePrincipals.policies-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/signInReports/allProperties/read | Läsa alla egenskaper (inklusive Privilegierade egenskaper) för signInReports i Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Läsa och konfigurera Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Skapa och hantera Azure supportärenden. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Läsa och konfigurera Office 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Skapa och hantera Office 365-supportbegäranden. |

### <a name="cloud-device-administrator"></a>Molnenhetsadministratör
Full behörighet att hantera enheter i Azure AD.

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.aad.directory/auditLogs/allProperties/read | Läsa alla egenskaper (inklusive Privilegierade egenskaper) för auditLogs i Azure Active Directory. |
| microsoft.aad.directory/devices/bitLockerRecoveryKeys/read | Läsa devices.bitLockerRecoveryKeys-egenskapen i Azure Active Directory. |
| Microsoft.aad.Directory/Devices/delete | Ta bort enheter i Azure Active Directory. |
| Microsoft.aad.Directory/Devices/disable | Inaktivera enheter i Azure Active Directory. |
| microsoft.aad.directory/devices/enable | Konfigurera enheter i Azure Active Directory. |
| microsoft.aad.directory/signInReports/allProperties/read | Läsa alla egenskaper (inklusive Privilegierade egenskaper) för signInReports i Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Läsa och konfigurera Azure Service Health. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Läsa och konfigurera Office 365 Service Health. |

### <a name="company-administrator"></a>Företagsadministratör
Kan hantera alla aspekter av Azure AD och Microsoft-tjänster att använda Azure AD-identiteter.

  > [!NOTE]
  > Den här rollen har ytterligare behörighet utanför Azure Active Directory. Mer information finns i rollen beskrivningen ovan.
  >
  >

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.aad.cloudAppSecurity/allEntities/allTasks | Skapa och ta bort alla resurser och läsa och uppdatera standardegenskaper i microsoft.aad.cloudAppSecurity. |
| microsoft.aad.directory/administrativeUnits/allProperties/allTasks | Skapa och ta bort administrativeUnits och läsa och uppdatera alla egenskaper i Azure Active Directory. |
| microsoft.aad.directory/applications/allProperties/allTasks | Skapa och ta bort program, och läsa och uppdatera alla egenskaper i Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/allProperties/allTasks | Skapa och ta bort appRoleAssignments och läsa och uppdatera alla egenskaper i Azure Active Directory. |
| microsoft.aad.directory/auditLogs/allProperties/read | Läsa alla egenskaper (inklusive Privilegierade egenskaper) för auditLogs i Azure Active Directory. |
| microsoft.aad.directory/contacts/allProperties/allTasks | Skapa och ta bort kontakter och läsa och uppdatera alla egenskaper i Azure Active Directory. |
| microsoft.aad.directory/contracts/allProperties/allTasks | Skapa och ta bort kontrakt, och läsa och uppdatera alla egenskaper i Azure Active Directory. |
| microsoft.aad.directory/devices/allProperties/allTasks | Skapa och ta bort enheter och läsa och uppdatera alla egenskaper i Azure Active Directory. |
| microsoft.aad.directory/directoryRoles/allProperties/allTasks | Skapa och ta bort directoryRoles, och läsa och uppdatera alla egenskaper i Azure Active Directory. |
| microsoft.aad.directory/directoryRoleTemplates/allProperties/allTasks | Skapa och ta bort directoryRoleTemplates och läsa och uppdatera alla egenskaper i Azure Active Directory. |
| microsoft.aad.directory/domains/allProperties/allTasks | Skapa och ta bort domäner och läsa och uppdatera alla egenskaper i Azure Active Directory. |
| microsoft.aad.directory/groups/allProperties/allTasks | Skapa och ta bort grupper och läsa och uppdatera alla egenskaper i Azure Active Directory. |
| microsoft.aad.directory/groupSettings/allProperties/allTasks | Skapa och ta bort groupSettings, och läsa och uppdatera alla egenskaper i Azure Active Directory. |
| microsoft.aad.directory/groupSettingTemplates/allProperties/allTasks | Skapa och ta bort groupSettingTemplates, och läsa och uppdatera alla egenskaper i Azure Active Directory. |
| microsoft.aad.directory/loginTenantBranding/allProperties/allTasks | Skapa och ta bort loginTenantBranding, och läsa och uppdatera alla egenskaper i Azure Active Directory. |
| microsoft.aad.directory/oAuth2PermissionGrants/allProperties/allTasks | Skapa och ta bort oAuth2PermissionGrants, och läsa och uppdatera alla egenskaper i Azure Active Directory. |
| microsoft.aad.directory/organization/allProperties/allTasks | Skapa och ta bort organisation, och läsa och uppdatera alla egenskaper i Azure Active Directory. |
| microsoft.aad.directory/policies/allProperties/allTasks | Skapa och ta bort principer och läsa och uppdatera alla egenskaper i Azure Active Directory. |
| microsoft.aad.directory/roleAssignments/allProperties/allTasks | Skapa och ta bort roleAssignments och läsa och uppdatera alla egenskaper i Azure Active Directory. |
| microsoft.aad.directory/roleDefinitions/allProperties/allTasks | Skapa och ta bort roleDefinitions och läsa och uppdatera alla egenskaper i Azure Active Directory. |
| microsoft.aad.directory/scopedRoleMemberships/allProperties/allTasks | Skapa och ta bort scopedRoleMemberships och läsa och uppdatera alla egenskaper i Azure Active Directory. |
| microsoft.aad.directory/serviceAction/activateService | Kan utföra åtgärden Activateservice service i Azure Active Directory |
| microsoft.aad.directory/serviceAction/disableDirectoryFeature | Kan utföra åtgärden Disabledirectoryfeature service i Azure Active Directory |
| microsoft.aad.directory/serviceAction/enableDirectoryFeature | Kan utföra åtgärden Enabledirectoryfeature service i Azure Active Directory |
| microsoft.aad.directory/serviceAction/getAvailableExtentionProperties | Kan utföra åtgärden Getavailableextentionproperties service i Azure Active Directory |
| microsoft.aad.directory/servicePrincipals/allProperties/allTasks | Skapa och ta bort servicePrincipals och läsa och uppdatera alla egenskaper i Azure Active Directory. |
| microsoft.aad.directory/signInReports/allProperties/read | Läsa alla egenskaper (inklusive Privilegierade egenskaper) för signInReports i Azure Active Directory. |
| microsoft.aad.directory/subscribedSkus/allProperties/allTasks | Skapa och ta bort subscribedSkus och läsa och uppdatera alla egenskaper i Azure Active Directory. |
| microsoft.aad.directory/users/allProperties/allTasks | Skapa och ta bort användare, och läsa och uppdatera alla egenskaper i Azure Active Directory. |
| microsoft.aad.directorySync/allEntities/allTasks | Utföra alla åtgärder i Azure AD Connect. |
| microsoft.aad.identityProtection/allEntities/allTasks | Skapa och ta bort alla resurser och läsa och uppdatera standardegenskaper i microsoft.aad.identityProtection. |
| microsoft.aad.privilegedIdentityManagement/allEntities/read | Läsa alla resurser i microsoft.aad.privilegedIdentityManagement. |
| microsoft.azure.advancedThreatProtection/allEntities/read | Läsa alla resurser i microsoft.azure.advancedThreatProtection. |
| microsoft.azure.informationProtection/allEntities/allTasks | Hantera alla aspekter av Azure Information Protection. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Läsa och konfigurera Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Skapa och hantera Azure supportärenden. |
| microsoft.commerce.billing/allEntities/allTasks | Hantera alla aspekter av Office 365-fakturering. |
| microsoft.intune/allEntities/allTasks | Hantera alla aspekter av Intune. |
| microsoft.office365.complianceManager/allEntities/allTasks | Hantera alla aspekter av Office 365-efterlevnad Manager |
| microsoft.office365.desktopAnalytics/allEntities/allTasks | Hantera alla aspekter av Desktop Analytics. |
| microsoft.office365.exchange/allEntities/allTasks | Hantera alla aspekter av Exchange Online. |
| microsoft.office365.lockbox/allEntities/allTasks | Hantera alla aspekter av Office 365 Customer Lockbox |
| microsoft.office365.messageCenter/messages/read | Läsa meddelanden i microsoft.office365.messageCenter. |
| microsoft.office365.messageCenter/securityMessages/read | Läsa securityMessages i microsoft.office365.messageCenter. |
| microsoft.office365.protectionCenter/allEntities/allTasks | Hantera alla aspekter av Office 365-Säkerhetscenter. |
| microsoft.office365.securityComplianceCenter/allEntities/allTasks | Skapa och ta bort alla resurser och läsa och uppdatera standardegenskaper i microsoft.office365.securityComplianceCenter. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Läsa och konfigurera Office 365 Service Health. |
| microsoft.office365.sharepoint/allEntities/allTasks | Skapa och ta bort alla resurser och läsa och uppdatera standardegenskaper i microsoft.office365.sharepoint. |
| microsoft.office365.skypeForBusiness/allEntities/allTasks | Hantera alla aspekter av Skype för företag Online. |
| microsoft.office365.supportTickets/allEntities/allTasks | Skapa och hantera Office 365-supportbegäranden. |
| microsoft.office365.usageReports/allEntities/read | Läs Office 365 användningsrapporter. |
| microsoft.office365.webPortal/allEntities/basic/read | Läsa grundläggande egenskaper för alla resurser i microsoft.office365.webPortal. |
| microsoft.powerApps.dynamics365/allEntities/allTasks | Hantera alla aspekter av Dynamics 365. |
| microsoft.powerApps.powerBI/allEntities/allTasks | Hantera alla aspekter av Power BI. |
| microsoft.windows.defenderAdvancedThreatProtection/allEntities/read | Läsa alla resurser i microsoft.windows.defenderAdvancedThreatProtection. |

### <a name="compliance-administrator"></a>Efterlevnadsadministratör
Kan läsa och hantera efterlevnadskonfiguration och rapporter i Azure AD och Office 365.

  > [!NOTE]
  > Den här rollen har ytterligare behörighet utanför Azure Active Directory. Mer information finns i rollen beskrivningen ovan.
  >
  >

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Läsa och konfigurera Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Skapa och hantera Azure supportärenden. |
| microsoft.office365.webPortal/allEntities/basic/read | Läsa grundläggande egenskaper för alla resurser i microsoft.office365.webPortal. |
| microsoft.office365.complianceManager/allEntities/allTasks | Hantera alla aspekter av Office 365-efterlevnad Manager |
| microsoft.office365.exchange/allEntities/allTasks | Hantera alla aspekter av Exchange Online. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Läsa och konfigurera Office 365 Service Health. |
| microsoft.office365.sharepoint/allEntities/allTasks | Skapa och ta bort alla resurser och läsa och uppdatera standardegenskaper i microsoft.office365.sharepoint. |
| microsoft.office365.skypeForBusiness/allEntities/allTasks | Hantera alla aspekter av Skype för företag Online. |
| microsoft.office365.supportTickets/allEntities/allTasks | Skapa och hantera Office 365-supportbegäranden. |

### <a name="compliance-data-administrator"></a>Efterlevnadsadministratör för Data
Skapar och hanterar efterlevnad innehåll.

  > [!NOTE]
  > Den här rollen har ytterligare behörighet utanför Azure Active Directory. Mer information finns i rollen beskrivningen ovan.
  >
  >

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.aad.cloudAppSecurity/allEntities/allTasks | Läsa och konfigurera Microsoft Cloud App Security. |
| microsoft.azure.informationProtection/allEntities/allTasks | Hantera alla aspekter av Azure Information Protection. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Läsa och konfigurera Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Skapa och hantera Azure supportärenden. |
| microsoft.office365.webPortal/allEntities/basic/read | Läsa grundläggande egenskaper för alla resurser i microsoft.office365.webPortal. |
| microsoft.office365.complianceManager/allEntities/allTasks | Hantera alla aspekter av Office 365-efterlevnad Manager |
| microsoft.office365.exchange/allEntities/allTasks | Hantera alla aspekter av Exchange Online. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Läsa och konfigurera Office 365 Service Health. |
| microsoft.office365.sharepoint/allEntities/allTasks | Skapa och ta bort alla resurser och läsa och uppdatera standardegenskaper i microsoft.office365.sharepoint. |
| microsoft.office365.skypeForBusiness/allEntities/allTasks | Hantera alla aspekter av Skype för företag Online. |
| microsoft.office365.supportTickets/allEntities/allTasks | Skapa och hantera Office 365-supportbegäranden. |

### <a name="conditional-access-administrator"></a>Administratör för villkorsstyrd åtkomst
Kan hantera funktioner för villkorlig åtkomst.

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.aad.directory/policies/conditionalAccess/basic/read | Läsa policies.conditionalAccess-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/policies/conditionalAccess/basic/update | Uppdatera policies.conditionalAccess-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/policies/conditionalAccess/create | Skapa principer i Azure Active Directory. |
| microsoft.aad.directory/policies/conditionalAccess/delete | Ta bort principer i Azure Active Directory. |
| microsoft.aad.directory/policies/conditionalAccess/owners/read | Läsa policies.conditionalAccess-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/policies/conditionalAccess/owners/update | Uppdatera policies.conditionalAccess-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/policies/conditionalAccess/policiesAppliedTo/read | Läsa policies.conditionalAccess-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/policies/conditionalAccess/tenantDefault/update | Uppdatera policies.conditionalAccess-egenskapen i Azure Active Directory. |

### <a name="crm-service-administrator"></a>CRM-tjänstadministratör
Kan hantera alla aspekter av Dynamics 365-produkten.

  > [!NOTE]
  > Den här rollen har ytterligare behörighet utanför Azure Active Directory. Mer information finns i rollen beskrivningen ovan.
  >
  >

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Läsa och konfigurera Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Skapa och hantera Azure supportärenden. |
| microsoft.powerApps.dynamics365/allEntities/allTasks | Hantera alla aspekter av Dynamics 365. |
| microsoft.office365.webPortal/allEntities/basic/read | Läsa grundläggande egenskaper för alla resurser i microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Läsa och konfigurera Office 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Skapa och hantera Office 365-supportbegäranden. |

### <a name="customer-lockbox-access-approver"></a>Customer LockBox åtkomst godkännaren
Kan godkänna förfrågningar till åtkomst till kundens organisationsdata Microsoft support.

  > [!NOTE]
  > Den här rollen har ytterligare behörighet utanför Azure Active Directory. Mer information finns i rollen beskrivningen ovan.
  >
  >

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.office365.webPortal/allEntities/basic/read | Läsa grundläggande egenskaper för alla resurser i microsoft.office365.webPortal. |
| microsoft.office365.lockbox/allEntities/allTasks | Hantera alla aspekter av Office 365 Customer Lockbox |

### <a name="desktop-analytics-administrator"></a>Desktop Analytics-administratör
Kan hantera Desktop Analytics och Office-anpassning & princip för tjänster. Detta inkluderar möjligheten att visa tillgångar, skapa planer för distribution, visa status för distribution och hälsa för Desktop analys. För Office-anpassning & princip för tjänsten kan den här rollen du hantera principer för Office.

  > [!NOTE]
  > Den här rollen har ytterligare behörighet utanför Azure Active Directory. Mer information finns i rollen beskrivningen ovan.
  >
  >

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Läsa och konfigurera Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Skapa och hantera Azure supportärenden. |
| microsoft.office365.webPortal/allEntities/basic/read | Läsa grundläggande egenskaper för alla resurser i microsoft.office365.webPortal. |
| microsoft.office365.desktopAnalytics/allEntities/allTasks | Hantera alla aspekter av Desktop Analytics. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Läsa och konfigurera Office 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Skapa och hantera Office 365-supportbegäranden. |

### <a name="device-administrators"></a>Enhetsadministratörer
Användare som tilldelats den här rollen har lagts till i gruppen lokala administratörer på Azure AD-anslutna enheter.

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.aad.directory/groupSettings/basic/read | Läsa grundläggande egenskaper för groupSettings i Azure Active Directory. |
| microsoft.aad.directory/groupSettingTemplates/basic/read | Läsa grundläggande egenskaper för groupSettingTemplates i Azure Active Directory. |

### <a name="directory-readers"></a>Katalogläsare
Kan läsa grundläggande kataloginformation. För att bevilja åtkomst till program inte avsedd för användare.

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.aad.directory/administrativeUnits/basic/read | Läs grundläggande egenskaper på administrativeUnits i Azure Active Directory. |
| microsoft.aad.directory/administrativeUnits/members/read | Läs administrativeUnits.members-egenskapen i Azure Active Directory. |
| Microsoft.aad.Directory/Applications/Basic/Read | Läsa grundläggande egenskaper för program i Azure Active Directory. |
| Microsoft.aad.Directory/Applications/owners/Read | Läsa applications.owners-egenskapen i Azure Active Directory. |
| Microsoft.aad.Directory/Applications/policies/Read | Läsa applications.policies-egenskapen i Azure Active Directory. |
| Microsoft.aad.Directory/Contacts/Basic/Read | Läs grundläggande egenskaper för kontakter i Azure Active Directory. |
| microsoft.aad.directory/contacts/memberOf/read | Läsa contacts.memberOf-egenskapen i Azure Active Directory. |
| Microsoft.aad.Directory/contracts/Basic/Read | Läsa grundläggande egenskaper för kontrakt i Azure Active Directory. |
| Microsoft.aad.Directory/Devices/Basic/Read | Läs grundläggande egenskaper för enheter i Azure Active Directory. |
| microsoft.aad.directory/devices/memberOf/read | Läsa devices.memberOf-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/devices/registeredOwners/read | Läsa devices.registeredOwners-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/devices/registeredUsers/read | Läsa devices.registeredUsers-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/directoryRoles/basic/read | Läsa grundläggande egenskaper för directoryRoles i Azure Active Directory. |
| microsoft.aad.directory/directoryRoles/eligibleMembers/read | Läsa directoryRoles.eligibleMembers-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/directoryRoles/members/read | Läsa directoryRoles.members-egenskapen i Azure Active Directory. |
| Microsoft.aad.Directory/Domains/Basic/Read | Läs grundläggande egenskaper för domäner i Azure Active Directory. |
| microsoft.aad.directory/groups/appRoleAssignments/read | Läsa groups.appRoleAssignments-egenskapen i Azure Active Directory. |
| Microsoft.aad.Directory/groups/Basic/Read | Läsa grundläggande egenskaper för grupper i Azure Active Directory. |
| microsoft.aad.directory/groups/memberOf/read | Läsa groups.memberOf-egenskapen i Azure Active Directory. |
| Microsoft.aad.Directory/groups/members/Read | Läsa groups.members-egenskapen i Azure Active Directory. |
| Microsoft.aad.Directory/groups/owners/Read | Läsa groups.owners-egenskapen i Azure Active Directory. |
| Microsoft.aad.Directory/groups/Settings/Read | Läsa groups.settings-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/groupSettings/basic/read | Läsa grundläggande egenskaper för groupSettings i Azure Active Directory. |
| microsoft.aad.directory/groupSettingTemplates/basic/read | Läsa grundläggande egenskaper för groupSettingTemplates i Azure Active Directory. |
| microsoft.aad.directory/oAuth2PermissionGrants/basic/read | Läsa grundläggande egenskaper för oAuth2PermissionGrants i Azure Active Directory. |
| Microsoft.aad.Directory/Organization/Basic/Read | Läsa grundläggande egenskaper för organisation i Azure Active Directory. |
| microsoft.aad.directory/organization/trustedCAsForPasswordlessAuth/read | Läsa organization.trustedCAsForPasswordlessAuth-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/roleAssignments/basic/read | Läsa grundläggande egenskaper för rolltilldelningar i Azure Active Directory. |
| microsoft.aad.directory/roleDefinitions/basic/read | Läsa grundläggande egenskaper för roleDefinitions i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/read | Läsa servicePrincipals.appRoleAssignedTo-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/read | Läsa servicePrincipals.appRoleAssignments-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/basic/read | Läsa grundläggande egenskaper för servicePrincipals i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/memberOf/read | Läsa servicePrincipals.memberOf-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/oAuth2PermissionGrants/basic/read | Läsa servicePrincipals.oAuth2PermissionGrants-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/ownedObjects/read | Läsa servicePrincipals.ownedObjects-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/owners/read | Läsa servicePrincipals.owners-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/policies/read | Läsa servicePrincipals.policies-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/subscribedSkus/basic/read | Läsa grundläggande egenskaper för subscribedSkus i Azure Active Directory. |
| microsoft.aad.directory/users/appRoleAssignments/read | Läsa users.appRoleAssignments-egenskapen i Azure Active Directory. |
| Microsoft.aad.Directory/Users/Basic/Read | Läsa grundläggande egenskaper för användare i Azure Active Directory. |
| microsoft.aad.directory/users/directReports/read | Läsa users.directReports-egenskapen i Azure Active Directory. |
| Microsoft.aad.Directory/Users/Manager/Read | Läsa users.manager-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/users/memberOf/read | Läsa users.memberOf-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/users/oAuth2PermissionGrants/basic/read | Läsa users.oAuth2PermissionGrants-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/users/ownedDevices/read | Läsa users.ownedDevices-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/users/ownedObjects/read | Läsa users.ownedObjects-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/users/registeredDevices/read | Läsa users.registeredDevices-egenskapen i Azure Active Directory. |

### <a name="directory-synchronization-accounts"></a>Konton för katalogen katalogsynkronisering
Endast används av Azure AD Connect-tjänsten.

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.aad.directory/organization/dirSync/update | Uppdatera organization.dirSync-egenskapen i Azure Active Directory. |
| Microsoft.aad.Directory/policies/Create | Skapa principer i Azure Active Directory. |
| Microsoft.aad.Directory/policies/delete | Ta bort principer i Azure Active Directory. |
| Microsoft.aad.Directory/policies/Basic/Read | Läs grundläggande egenskaper för principer i Azure Active Directory. |
| Microsoft.aad.Directory/policies/Basic/Update | Uppdatera grundläggande egenskaper för principer i Azure Active Directory. |
| Microsoft.aad.Directory/policies/owners/Read | Läsa policies.owners-egenskapen i Azure Active Directory. |
| Microsoft.aad.Directory/policies/owners/Update | Uppdatera policies.owners-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/policies/policiesAppliedTo/read | Läsa policies.policiesAppliedTo-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/policies/tenantDefault/update | Uppdatera policies.tenantDefault-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/read | Läsa servicePrincipals.appRoleAssignedTo-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/update | Uppdatera servicePrincipals.appRoleAssignedTo-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/read | Läsa servicePrincipals.appRoleAssignments-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/update | Uppdatera servicePrincipals.appRoleAssignments-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/audience/update | Uppdatera servicePrincipals.audience-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/authentication/update | Uppdatera servicePrincipals.authentication-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/basic/read | Läsa grundläggande egenskaper för servicePrincipals i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/basic/update | Uppdatera grundläggande egenskaper för servicePrincipals i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/create | Skapa servicePrincipals i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/credentials/update | Uppdatera servicePrincipals.credentials-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/memberOf/read | Läsa servicePrincipals.memberOf-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/oAuth2PermissionGrants/basic/read | Läsa servicePrincipals.oAuth2PermissionGrants-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/owners/read | Läsa servicePrincipals.owners-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/owners/update | Uppdatera servicePrincipals.owners-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/ownedObjects/read | Läsa servicePrincipals.ownedObjects-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/permissions/update | Uppdatera servicePrincipals.permissions-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/policies/read | Läsa servicePrincipals.policies-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/policies/update | Uppdatera servicePrincipals.policies-egenskapen i Azure Active Directory. |
| microsoft.aad.directorySync/allEntities/allTasks | Utföra alla åtgärder i Azure AD Connect. |

### <a name="directory-writers"></a>Katalogskrivare
Kan läsa och skriva grundläggande kataloginformation. För att bevilja åtkomst till program inte avsedd för användare.

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| Microsoft.aad.Directory/groups/Create | Skapa grupper i Azure Active Directory. |
| microsoft.aad.directory/groups/createAsOwner | Skapa grupper i Azure Active Directory. Skaparen läggs till som första ägare och det skapade objektet räknas mot Skaparens kvot på 250 skapade objekt. |
| microsoft.aad.directory/groups/appRoleAssignments/update | Uppdatera groups.appRoleAssignments-egenskapen i Azure Active Directory. |
| Microsoft.aad.Directory/groups/Basic/Update | Uppdatera grundläggande egenskaper för grupper i Azure Active Directory. |
| Microsoft.aad.Directory/groups/members/Update | Uppdatera groups.members-egenskapen i Azure Active Directory. |
| Microsoft.aad.Directory/groups/owners/Update | Uppdatera groups.owners-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/groups/settings/update | Uppdatera groups.settings-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/groupSettings/basic/update | Uppdatera grundläggande egenskaper för groupSettings i Azure Active Directory. |
| microsoft.aad.directory/groupSettings/create | Skapa groupSettings i Azure Active Directory. |
| microsoft.aad.directory/groupSettings/delete | Ta bort groupSettings i Azure Active Directory. |
| microsoft.aad.directory/users/appRoleAssignments/update | Uppdatera users.appRoleAssignments-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/users/assignLicense | Hantera licenser för användare i Azure Active Directory. |
| microsoft.aad.directory/users/basic/update | Uppdatera grundläggande egenskaper för användare i Azure Active Directory. |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Ogiltigförklaras alla tokens för användaruppdatering i Azure Active Directory. |
| Microsoft.aad.Directory/Users/Manager/Update | Uppdatera users.manager-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/users/userPrincipalName/update | Uppdatera users.userPrincipalName-egenskapen i Azure Active Directory. |

### <a name="exchange-service-administrator"></a>Exchange Service-administratör
Kan hantera alla aspekter av Exchange-produkten.

  > [!NOTE]
  > Den här rollen har ytterligare behörighet utanför Azure Active Directory. Mer information finns i rollen beskrivningen ovan.
  >
  >

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.aad.directory/groups/unified/appRoleAssignments/update | Uppdatera groups.unified-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/groups/unified/basic/update | Uppdatera grundläggande egenskaper för Office 365-grupper. |
| microsoft.aad.directory/groups/unified/create | Skapa Office 365-grupper. |
| microsoft.aad.directory/groups/unified/delete | Ta bort Office 365-grupper. |
| microsoft.aad.directory/groups/unified/members/update | Uppdatera medlemskap i Office 365-grupper. |
| microsoft.aad.directory/groups/unified/owners/update | Uppdatera ägarskapet för Office 365-grupper. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Läsa och konfigurera Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Skapa och hantera Azure supportärenden. |
| microsoft.office365.webPortal/allEntities/basic/read | Läsa grundläggande egenskaper för alla resurser i microsoft.office365.webPortal. |
| microsoft.office365.exchange/allEntities/allTasks | Hantera alla aspekter av Exchange Online. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Läsa och konfigurera Office 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Skapa och hantera Office 365-supportbegäranden. |

### <a name="external-identity-provider-administrator"></a>Externa identitet tjänstproviderns administratör
Konfigurera identitetsleverantörer för användning i direkt federation.

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.aad.b2c/identityProviders/allTasks | Läsa och konfigurera Identitetsproviders i Azure Active Directory B2C. |

### <a name="guest-inviter"></a>Gäst bjuder in
Kan bjuda in gästanvändare oberoende av den ”medlemmar kan bjuda in gäster” inställningen.

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.aad.directory/users/appRoleAssignments/read | Läsa users.appRoleAssignments-egenskapen i Azure Active Directory. |
| Microsoft.aad.Directory/Users/Basic/Read | Läsa grundläggande egenskaper för användare i Azure Active Directory. |
| microsoft.aad.directory/users/directReports/read | Läsa users.directReports-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/users/inviteGuest | Bjud in gästanvändare i Azure Active Directory. |
| Microsoft.aad.Directory/Users/Manager/Read | Läsa users.manager-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/users/memberOf/read | Läsa users.memberOf-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/users/oAuth2PermissionGrants/basic/read | Läsa users.oAuth2PermissionGrants-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/users/ownedDevices/read | Läsa users.ownedDevices-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/users/ownedObjects/read | Läsa users.ownedObjects-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/users/registeredDevices/read | Läsa users.registeredDevices-egenskapen i Azure Active Directory. |

### <a name="helpdesk-administrator"></a>Supportavdelningsadministratör
Kan återställa lösenord för icke-administratörer och Helpdeks-administratörer.

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.aad.directory/devices/bitLockerRecoveryKeys/read | Läsa devices.bitLockerRecoveryKeys-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Ogiltigförklaras alla tokens för användaruppdatering i Azure Active Directory. |
| Microsoft.aad.Directory/Users/Password/Update | Uppdatera lösenord för alla användare i Azure Active Directory. Se onlinedokumentationen för mer information. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Läsa och konfigurera Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Skapa och hantera Azure supportärenden. |
| microsoft.office365.webPortal/allEntities/basic/read | Läsa grundläggande egenskaper för alla resurser i microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Läsa och konfigurera Office 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Skapa och hantera Office 365-supportbegäranden. |

### <a name="intune-service-administrator"></a>Administratör för Intune-tjänsten
Kan hantera alla aspekter av Intune-produkten.

  > [!NOTE]
  > Den här rollen har ytterligare behörighet utanför Azure Active Directory. Mer information finns i rollen beskrivningen ovan.
  >
  >

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| Microsoft.aad.Directory/Contacts/Basic/Update | Uppdatera grundläggande egenskaper för kontakter i Azure Active Directory. |
| Microsoft.aad.Directory/Contacts/Create | Skapa kontakter i Azure Active Directory. |
| Microsoft.aad.Directory/Contacts/delete | Ta bort kontakter i Azure Active Directory. |
| Microsoft.aad.Directory/Devices/Basic/Update | Uppdatera grundläggande egenskaper för enheter i Azure Active Directory. |
| microsoft.aad.directory/devices/bitLockerRecoveryKeys/read | Läsa devices.bitLockerRecoveryKeys-egenskapen i Azure Active Directory. |
| Microsoft.aad.Directory/Devices/Create | Skapa enheter i Azure Active Directory. |
| Microsoft.aad.Directory/Devices/delete | Ta bort enheter i Azure Active Directory. |
| microsoft.aad.directory/devices/registeredOwners/update | Uppdatera devices.registeredOwners-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/devices/registeredUsers/update | Uppdatera devices.registeredUsers-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/groups/appRoleAssignments/update | Uppdatera groups.appRoleAssignments-egenskapen i Azure Active Directory. |
| Microsoft.aad.Directory/groups/Basic/Update | Uppdatera grundläggande egenskaper för grupper i Azure Active Directory. |
| Microsoft.aad.Directory/groups/Create | Skapa grupper i Azure Active Directory. |
| microsoft.aad.directory/groups/createAsOwner | Skapa grupper i Azure Active Directory. Skaparen läggs till som första ägare och det skapade objektet räknas mot Skaparens kvot på 250 skapade objekt. |
| Microsoft.aad.Directory/groups/delete | Ta bort grupper i Azure Active Directory. |
| microsoft.aad.directory/groups/hiddenMembers/read | Läsa groups.hiddenMembers-egenskapen i Azure Active Directory. |
| Microsoft.aad.Directory/groups/members/Update | Uppdatera groups.members-egenskapen i Azure Active Directory. |
| Microsoft.aad.Directory/groups/owners/Update | Uppdatera groups.owners-egenskapen i Azure Active Directory. |
| Microsoft.aad.Directory/groups/Restore | Återställa grupper i Azure Active Directory. |
| microsoft.aad.directory/groups/settings/update | Uppdatera groups.settings-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/users/appRoleAssignments/update | Uppdatera users.appRoleAssignments-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/users/basic/update | Uppdatera grundläggande egenskaper för användare i Azure Active Directory. |
| Microsoft.aad.Directory/Users/Manager/Update | Uppdatera users.manager-egenskapen i Azure Active Directory. |
| microsoft.azure.supportTickets/allEntities/allTasks | Skapa och hantera Azure supportärenden. |
| microsoft.intune/allEntities/allTasks | Hantera alla aspekter av Intune. |
| microsoft.office365.supportTickets/allEntities/allTasks | Skapa och hantera Office 365-supportbegäranden. |
| microsoft.office365.webPortal/allEntities/basic/read | Läsa grundläggande egenskaper för alla resurser i microsoft.office365.webPortal. |

### <a name="kaizala-administrator"></a>Kaizala Administrator
Kan hantera inställningar för Microsoft Kaizala.  

  > [!NOTE]
  > Den här rollen har ytterligare behörigheter utanför Azure Active Directory. Mer information finns i rollen beskrivningen ovan.
  >
  >  
  
| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.office365.serviceHealth/allEntities/allTasks | Läsa och konfigurera Office 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Skapa och hantera Office 365-supportbegäranden. |
| microsoft.office365.webPortal/allEntities/basic/read | Läs Office 365 Administrationscenter. |

### <a name="license-administrator"></a>Licensadministratör
Kan hantera produktlicenser på användare och grupper.

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.aad.directory/users/assignLicense | Hantera licenser för användare i Azure Active Directory. |
| microsoft.aad.directory/users/usageLocation/update | Uppdatera users.usageLocation-egenskapen i Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Läsa och konfigurera Azure Service Health. |
| microsoft.office365.webPortal/allEntities/basic/read | Läsa grundläggande egenskaper för alla resurser i microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Läsa och konfigurera Office 365 Service Health. |

### <a name="lync-service-administrator"></a>Lync-tjänstadministratör
Kan hantera alla aspekter av Skype för Business-produkten.

  > [!NOTE]
  > Den här rollen har ytterligare behörighet utanför Azure Active Directory. Mer information finns i rollen beskrivningen ovan.
  >
  >

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Läsa och konfigurera Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Skapa och hantera Azure supportärenden. |
| microsoft.office365.webPortal/allEntities/basic/read | Läsa grundläggande egenskaper för alla resurser i microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Läsa och konfigurera Office 365 Service Health. |
| microsoft.office365.skypeForBusiness/allEntities/allTasks | Hantera alla aspekter av Skype för företag Online. |
| microsoft.office365.supportTickets/allEntities/allTasks | Skapa och hantera Office 365-supportbegäranden. |

### <a name="message-center-privacy-reader"></a>Sekretess Meddelandecenterläsare
Kan läsa meddelandecenter inlägg, datameddelanden för sekretess, grupper, domäner och prenumerationer.

  > [!NOTE]
  > Den här rollen har ytterligare behörigheter utanför Azure Active Directory. Mer information finns i rollen beskrivningen ovan.
  >
  >

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.office365.webPortal/allEntities/basic/read | Läsa grundläggande egenskaper för alla resurser i microsoft.office365.webPortal. |
| microsoft.office365.messageCenter/messages/read | Läsa meddelanden i microsoft.office365.messageCenter. |
| microsoft.office365.messageCenter/securityMessages/read | Läsa securityMessages i microsoft.office365.messageCenter. |

### <a name="message-center-reader"></a>Meddelandecenterläsare
Kan läsa meddelanden och uppdateringar avseende sin organisation i meddelandecenter för Office 365. 

  > [!NOTE]
  > Den här rollen har ytterligare behörighet utanför Azure Active Directory. Mer information finns i rollen beskrivningen ovan.
  >
  >

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.office365.webPortal/allEntities/basic/read | Läsa grundläggande egenskaper för alla resurser i microsoft.office365.webPortal. |
| microsoft.office365.messageCenter/messages/read | Läsa meddelanden i microsoft.office365.messageCenter. |

### <a name="partner-tier1-support"></a>Partnersupport, nivå 1
Använd inte – inte avsett för allmänt bruk.

  > [!NOTE]
  > Den här rollen har ytterligare behörighet utanför Azure Active Directory. Mer information finns i rollen beskrivningen ovan.
  >
  >

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| Microsoft.aad.Directory/Contacts/Basic/Update | Uppdatera grundläggande egenskaper för kontakter i Azure Active Directory. |
| Microsoft.aad.Directory/Contacts/Create | Skapa kontakter i Azure Active Directory. |
| Microsoft.aad.Directory/Contacts/delete | Ta bort kontakter i Azure Active Directory. |
| Microsoft.aad.Directory/groups/Create | Skapa grupper i Azure Active Directory. |
| microsoft.aad.directory/groups/createAsOwner | Skapa grupper i Azure Active Directory. Skaparen läggs till som första ägare och det skapade objektet räknas mot Skaparens kvot på 250 skapade objekt. |
| Microsoft.aad.Directory/groups/members/Update | Uppdatera groups.members-egenskapen i Azure Active Directory. |
| Microsoft.aad.Directory/groups/owners/Update | Uppdatera groups.owners-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/users/appRoleAssignments/update | Uppdatera users.appRoleAssignments-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/users/assignLicense | Hantera licenser för användare i Azure Active Directory. |
| microsoft.aad.directory/users/basic/update | Uppdatera grundläggande egenskaper för användare i Azure Active Directory. |
| Microsoft.aad.Directory/Users/delete | Ta bort användare i Azure Active Directory. |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Ogiltigförklaras alla tokens för användaruppdatering i Azure Active Directory. |
| Microsoft.aad.Directory/Users/Manager/Update | Uppdatera users.manager-egenskapen i Azure Active Directory. |
| Microsoft.aad.Directory/Users/Password/Update | Uppdatera lösenord för alla användare i Azure Active Directory. Se onlinedokumentationen för mer information. |
| Microsoft.aad.Directory/Users/Restore | Återställa borttagna användare i Azure Active Directory. |
| microsoft.aad.directory/users/userPrincipalName/update | Uppdatera users.userPrincipalName-egenskapen i Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Läsa och konfigurera Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Skapa och hantera Azure supportärenden. |
| microsoft.office365.webPortal/allEntities/basic/read | Läsa grundläggande egenskaper för alla resurser i microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Läsa och konfigurera Office 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Skapa och hantera Office 365-supportbegäranden. |

### <a name="partner-tier2-support"></a>Partnersupport, nivå 2
Använd inte – inte avsett för allmänt bruk.

  > [!NOTE]
  > Den här rollen har ytterligare behörighet utanför Azure Active Directory. Mer information finns i rollen beskrivningen ovan.
  >
  >

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| Microsoft.aad.Directory/Contacts/Basic/Update | Uppdatera grundläggande egenskaper för kontakter i Azure Active Directory. |
| Microsoft.aad.Directory/Contacts/Create | Skapa kontakter i Azure Active Directory. |
| Microsoft.aad.Directory/Contacts/delete | Ta bort kontakter i Azure Active Directory. |
| microsoft.aad.directory/domains/allTasks | Skapa och ta bort domäner och läsa och uppdatera standardegenskaper i Azure Active Directory. |
| Microsoft.aad.Directory/groups/Create | Skapa grupper i Azure Active Directory. |
| Microsoft.aad.Directory/groups/delete | Ta bort grupper i Azure Active Directory. |
| Microsoft.aad.Directory/groups/members/Update | Uppdatera groups.members-egenskapen i Azure Active Directory. |
| Microsoft.aad.Directory/groups/Restore | Återställa grupper i Azure Active Directory. |
| microsoft.aad.directory/organization/basic/update | Uppdatera grundläggande egenskaper på organisation i Azure Active Directory. |
| microsoft.aad.directory/users/appRoleAssignments/update | Uppdatera users.appRoleAssignments-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/users/assignLicense | Hantera licenser för användare i Azure Active Directory. |
| microsoft.aad.directory/users/basic/update | Uppdatera grundläggande egenskaper för användare i Azure Active Directory. |
| Microsoft.aad.Directory/Users/delete | Ta bort användare i Azure Active Directory. |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Ogiltigförklaras alla tokens för användaruppdatering i Azure Active Directory. |
| Microsoft.aad.Directory/Users/Manager/Update | Uppdatera users.manager-egenskapen i Azure Active Directory. |
| Microsoft.aad.Directory/Users/Password/Update | Uppdatera lösenord för alla användare i Azure Active Directory. Se onlinedokumentationen för mer information. |
| Microsoft.aad.Directory/Users/Restore | Återställa borttagna användare i Azure Active Directory. |
| microsoft.aad.directory/users/userPrincipalName/update | Uppdatera users.userPrincipalName-egenskapen i Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Läsa och konfigurera Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Skapa och hantera Azure supportärenden. |
| microsoft.office365.webPortal/allEntities/basic/read | Läsa grundläggande egenskaper för alla resurser i microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Läsa och konfigurera Office 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Skapa och hantera Office 365-supportbegäranden. |

### <a name="power-bi-service-administrator"></a>Power BI-tjänstadministratör
Kan hantera alla aspekter av Power BI-produkten.

  > [!NOTE]
  > Den här rollen har ytterligare behörighet utanför Azure Active Directory. Mer information finns i rollen beskrivningen ovan.
  >
  >

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Läsa och konfigurera Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Skapa och hantera Azure supportärenden. |
| microsoft.powerApps.powerBI/allEntities/allTasks | Hantera alla aspekter av Power BI. |
| microsoft.office365.webPortal/allEntities/basic/read | Läsa grundläggande egenskaper för alla resurser i microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Läsa och konfigurera Office 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Skapa och hantera Office 365-supportbegäranden. |

### <a name="privileged-authentication-administrator"></a>Administratören för privilegierade Authentication
Behörighet att visa, ange och återställa information om autentisering för alla användare (admin eller icke-administratörer).

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Ogiltigförklaras alla tokens för användaruppdatering i Azure Active Directory. |
| microsoft.aad.directory/users/strongAuthentication/update | Egenskaper för stark autentisering som autentiseringsuppgifter för MFA. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Läsa och konfigurera Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Skapa och hantera Azure supportärenden. |
| microsoft.office365.webPortal/allEntities/basic/read | Läsa grundläggande egenskaper för alla resurser i microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Läsa och konfigurera Office 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Skapa och hantera Office 365-supportbegäranden. |

### <a name="privileged-role-administrator"></a>Privilegierad Rolladministratör
Kan hantera rolltilldelningar i Azure AD och alla aspekter av Privileged Identity Management.

  > [!NOTE]
  > Den här rollen har ytterligare behörighet utanför Azure Active Directory. Mer information finns i rollen beskrivningen ovan.
  >
  >

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.aad.privilegedIdentityManagement/allEntities/allTasks | Skapa och ta bort alla resurser och läsa och uppdatera standardegenskaper i microsoft.aad.privilegedIdentityManagement. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/allTasks | Läsa och konfigurera servicePrincipals.appRoleAssignedTo-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/oAuth2PermissionGrants/allTasks | Läsa och konfigurera servicePrincipals.oAuth2PermissionGrants-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/administrativeUnits/allProperties/allTasks | Skapa och hantera administrativa enheter (inklusive medlemmar) |
| microsoft.aad.directory/roleAssignments/allProperties/allTasks | Skapa och hantera rolltilldelningar. |
| microsoft.aad.directory/roleDefinitions/allProperties/allTasks | Skapa och hantera rolldefinitioner. |

### <a name="reports-reader"></a>Rapportläsare
Kan läsa inloggnings- och granskningsrapporter.

  > [!NOTE]
  > Den här rollen har ytterligare behörighet utanför Azure Active Directory. Mer information finns i rollen beskrivningen ovan.
  >
  >

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.aad.directory/auditLogs/allProperties/read | Läsa alla egenskaper (inklusive Privilegierade egenskaper) för auditLogs i Azure Active Directory. |
| microsoft.aad.directory/signInReports/allProperties/read | Läsa alla egenskaper (inklusive Privilegierade egenskaper) för signInReports i Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Läsa och konfigurera Azure Service Health. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Läsa och konfigurera Office 365 Service Health. |
| microsoft.office365.usageReports/allEntities/read | Läs Office 365 användningsrapporter. |

### <a name="search-administrator"></a>Sök-administratör
Kan skapa och hantera alla aspekter av Microsoft Search inställningar.

  > [!NOTE]
  > Den här rollen har ytterligare behörigheter utanför Azure Active Directory. Mer information finns i rollen beskrivningen ovan.
  >
  >

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.office365.messageCenter/messages/read | Läsa meddelanden i microsoft.office365.messageCenter. |
| microsoft.office365.search/allEntities/allProperties/allTasks | Skapa och ta bort alla resurser och läsa och uppdatera alla egenskaper i microsoft.office365.search. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Läsa och konfigurera Office 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Skapa och hantera Office 365-supportbegäranden. |
| microsoft.office365.usageReports/allEntities/read | Läs Office 365 användningsrapporter. |
| microsoft.office365.webPortal/allEntities/basic/read | Läsa grundläggande egenskaper för alla resurser i microsoft.office365.webPortal. |

### <a name="search-editor"></a>Sök-redigeraren
Kan skapa och hantera tryckt innehåll, till exempel bokmärken, frågor och och som, platser, floorplan.

  > [!NOTE]
  > Den här rollen har ytterligare behörigheter utanför Azure Active Directory. Mer information finns i rollen beskrivningen ovan.
  >
  >

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.office365.messageCenter/messages/read | Läsa meddelanden i microsoft.office365.messageCenter. |
| microsoft.office365.search/content/allProperties/allTasks | Skapa och ta bort innehåll, och läsa och uppdatera alla egenskaper i microsoft.office365.search. |
| microsoft.office365.usageReports/allEntities/read | Läs Office 365 användningsrapporter. |

### <a name="security-administrator"></a>Säkerhetsadministratör
Kan läsa säkerhetsinformation och rapporter och hantera konfigurationen i Azure AD och Office 365.

  > [!NOTE]
  > Den här rollen har ytterligare behörighet utanför Azure Active Directory. Mer information finns i rollen beskrivningen ovan.
  >
  >

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| Microsoft.aad.Directory/Applications/policies/Update | Uppdatera applications.policies-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/auditLogs/allProperties/read | Läsa alla egenskaper (inklusive Privilegierade egenskaper) för auditLogs i Azure Active Directory. |
| microsoft.aad.directory/devices/bitLockerRecoveryKeys/read | Läsa devices.bitLockerRecoveryKeys-egenskapen i Azure Active Directory. |
| Microsoft.aad.Directory/policies/Basic/Update | Uppdatera grundläggande egenskaper för principer i Azure Active Directory. |
| Microsoft.aad.Directory/policies/Create | Skapa principer i Azure Active Directory. |
| Microsoft.aad.Directory/policies/delete | Ta bort principer i Azure Active Directory. |
| Microsoft.aad.Directory/policies/owners/Update | Uppdatera policies.owners-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/policies/tenantDefault/update | Uppdatera policies.tenantDefault-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/policies/update | Uppdatera servicePrincipals.policies-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/signInReports/allProperties/read | Läsa alla egenskaper (inklusive Privilegierade egenskaper) för signInReports i Azure Active Directory. |
| microsoft.aad.identityProtection/allEntities/read | Läsa alla resurser i microsoft.aad.identityProtection. |
| microsoft.aad.identityProtection/allEntities/update | Uppdatera alla resurser i microsoft.aad.identityProtection. |
| microsoft.aad.privilegedIdentityManagement/allEntities/read | Läsa alla resurser i microsoft.aad.privilegedIdentityManagement. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Läsa och konfigurera Azure Service Health. |
| microsoft.office365.webPortal/allEntities/basic/read | Läsa grundläggande egenskaper för alla resurser i microsoft.office365.webPortal. |
| microsoft.office365.protectionCenter/allEntities/read | Läsa alla aspekter av Office 365-Säkerhetscenter. |
| microsoft.office365.protectionCenter/allEntities/update | Uppdatera alla resurser i microsoft.office365.protectionCenter. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Läsa och konfigurera Office 365 Service Health. |

### <a name="security-operator"></a>Säkerhetsansvarig
Skapar och hanterar säkerhetshändelser.

  > [!NOTE]
  > Den här rollen har ytterligare behörigheter utanför Azure Active Directory. Mer information finns i rollen beskrivningen ovan.
  >
  >

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.aad.cloudAppSecurity/allEntities/allTasks | Läsa och konfigurera Microsoft Cloud App Security. |
| microsoft.aad.identityProtection/allEntities/read | Läsa alla resurser i microsoft.aad.identityProtection. |
| microsoft.aad.privilegedIdentityManagement/allEntities/read | Läsa alla resurser i microsoft.aad.privilegedIdentityManagement. |
| microsoft.azure.advancedThreatProtection/allEntities/read | Läsa och konfigurera Azure AD Advanced Threat Protection. |
| microsoft.intune/allEntities/allTasks | Hantera alla aspekter av Intune. |
| microsoft.office365.securityComplianceCenter/allEntities/allTasks | Läsa och konfigurera säkerhets- och Efterlevnadscenter. |
| microsoft.office365.usageReports/allEntities/read | Läs Office 365 användningsrapporter. |
| microsoft.windows.defenderAdvancedThreatProtection/allEntities/read | Läsa och konfigurera Windows Defender Avancerat skydd. |

### <a name="security-reader"></a>Säkerhetsläsare
Kan läsa säkerhetsinformation och rapporter i Azure AD och Office 365.

  > [!NOTE]
  > Den här rollen har ytterligare behörighet utanför Azure Active Directory. Mer information finns i rollen beskrivningen ovan.
  >
  >

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.aad.directory/auditLogs/allProperties/read | Läsa alla egenskaper (inklusive Privilegierade egenskaper) för auditLogs i Azure Active Directory. |
| microsoft.aad.directory/devices/bitLockerRecoveryKeys/read | Läsa devices.bitLockerRecoveryKeys-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/signInReports/allProperties/read | Läsa alla egenskaper (inklusive Privilegierade egenskaper) för signInReports i Azure Active Directory. |
| microsoft.aad.identityProtection/allEntities/read | Läsa alla resurser i microsoft.aad.identityProtection. |
| microsoft.aad.privilegedIdentityManagement/allEntities/read | Läsa alla resurser i microsoft.aad.privilegedIdentityManagement. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Läsa och konfigurera Azure Service Health. |
| microsoft.office365.webPortal/allEntities/basic/read | Läsa grundläggande egenskaper för alla resurser i microsoft.office365.webPortal. |
| microsoft.office365.protectionCenter/allEntities/read | Läsa alla aspekter av Office 365-Säkerhetscenter. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Läsa och konfigurera Office 365 Service Health. |

### <a name="service-support-administrator"></a>Tjänstsupportadministratör
Kan läsa information om service health och hantera supportärenden.

  > [!NOTE]
  > Den här rollen har ytterligare behörighet utanför Azure Active Directory. Mer information finns i rollen beskrivningen ovan.
  >
  >

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Läsa och konfigurera Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Skapa och hantera Azure supportärenden. |
| microsoft.office365.webPortal/allEntities/basic/read | Läsa grundläggande egenskaper för alla resurser i microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Läsa och konfigurera Office 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Skapa och hantera Office 365-supportbegäranden. |

### <a name="sharepoint-service-administrator"></a>SharePoint Service Administrator
Kan hantera alla aspekter av SharePoint-tjänsten.

  > [!NOTE]
  > Den här rollen har ytterligare behörighet utanför Azure Active Directory. Mer information finns i rollen beskrivningen ovan.
  >
  >

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.aad.directory/groups/unified/appRoleAssignments/update | Uppdatera groups.unified-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/groups/unified/basic/update | Uppdatera grundläggande egenskaper för Office 365-grupper. |
| microsoft.aad.directory/groups/unified/create | Skapa Office 365-grupper. |
| microsoft.aad.directory/groups/unified/delete | Ta bort Office 365-grupper. |
| microsoft.aad.directory/groups/unified/members/update | Uppdatera medlemskap i Office 365-grupper. |
| microsoft.aad.directory/groups/unified/owners/update | Uppdatera ägarskapet för Office 365-grupper. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Läsa och konfigurera Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Skapa och hantera Azure supportärenden. |
| microsoft.office365.webPortal/allEntities/basic/read | Läsa grundläggande egenskaper för alla resurser i microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Läsa och konfigurera Office 365 Service Health. |
| microsoft.office365.sharepoint/allEntities/allTasks | Skapa och ta bort alla resurser och läsa och uppdatera standardegenskaper i microsoft.office365.sharepoint. |
| microsoft.office365.supportTickets/allEntities/allTasks | Skapa och hantera Office 365-supportbegäranden. |

### <a name="teams-communications-administrator"></a>Teams kommunikation administratör
Kan hantera anropa och möten funktioner i Microsoft Teams-tjänst.

  > [!NOTE]
  > Den här rollen har ytterligare behörighet utanför Azure Active Directory. Mer information finns i rollen beskrivningen ovan.
  >
  >

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Läsa och konfigurera Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Skapa och hantera Azure supportärenden. |
| microsoft.office365.webPortal/allEntities/basic/read | Läsa grundläggande egenskaper för alla resurser i microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Läsa och konfigurera Office 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Skapa och hantera Office 365-supportbegäranden. |
| microsoft.office365.usageReports/allEntities/read | Läs Office 365 användningsrapporter. |

### <a name="teams-communications-support-engineer"></a>Teams kommunikation supporttekniker
Kan felsöka problem med kommunikation i grupper med hjälp av avancerade verktyg.

  > [!NOTE]
  > Den här rollen har ytterligare behörighet utanför Azure Active Directory. Mer information finns i rollen beskrivningen ovan.
  >
  >

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Läsa och konfigurera Azure Service Health. |
| microsoft.office365.webPortal/allEntities/basic/read | Läsa grundläggande egenskaper för alla resurser i microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Läsa och konfigurera Office 365 Service Health. |

### <a name="teams-communications-support-specialist"></a>Teams kommunikation Support Specialist
Kan felsöka problem med kommunikation i Teams med hjälp av grundläggande verktyg.

  > [!NOTE]
  > Den här rollen har ytterligare behörighet utanför Azure Active Directory. Mer information finns i rollen beskrivningen ovan.
  >
  >

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Läsa och konfigurera Azure Service Health. |
| microsoft.office365.webPortal/allEntities/basic/read | Läsa grundläggande egenskaper för alla resurser i microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Läsa och konfigurera Office 365 Service Health. |

### <a name="teams-service-administrator"></a>Team-tjänstadministratör
Hantera Microsoft Teams-tjänsten.

  > [!NOTE]
  > Den här rollen har ytterligare behörighet utanför Azure Active Directory. Mer information finns i rollen beskrivningen ovan.
  >
  >

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.aad.directory/groups/hiddenMembers/read | Läsa groups.hiddenMembers-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/groups/unified/appRoleAssignments/update | Uppdatera groups.unified-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/groups/unified/basic/update | Uppdatera grundläggande egenskaper för Office 365-grupper. |
| microsoft.aad.directory/groups/unified/create | Skapa Office 365-grupper. |
| microsoft.aad.directory/groups/unified/delete | Ta bort Office 365-grupper. |
| microsoft.aad.directory/groups/unified/members/update | Uppdatera medlemskap i Office 365-grupper. |
| microsoft.aad.directory/groups/unified/owners/update | Uppdatera ägarskapet för Office 365-grupper. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Läsa och konfigurera Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Skapa och hantera Azure supportärenden. |
| microsoft.office365.webPortal/allEntities/basic/read | Läsa grundläggande egenskaper för alla resurser i microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Läsa och konfigurera Office 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Skapa och hantera Office 365-supportbegäranden. |
| microsoft.office365.usageReports/allEntities/read | Läs Office 365 användningsrapporter. |

### <a name="user-administrator"></a>Användaradministratör
Kan hantera alla aspekter av användare och grupper, inklusive återställning av lösenord för begränsade administratörer.

| **Åtgärder** | **Beskrivning** |
| --- | --- |
| microsoft.aad.directory/appRoleAssignments/create | Skapa appRoleAssignments i Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/delete | Ta bort appRoleAssignments i Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/update | Uppdatera appRoleAssignments i Azure Active Directory. |
| Microsoft.aad.Directory/Contacts/Basic/Update | Uppdatera grundläggande egenskaper för kontakter i Azure Active Directory. |
| Microsoft.aad.Directory/Contacts/Create | Skapa kontakter i Azure Active Directory. |
| Microsoft.aad.Directory/Contacts/delete | Ta bort kontakter i Azure Active Directory. |
| microsoft.aad.directory/groups/appRoleAssignments/update | Uppdatera groups.appRoleAssignments-egenskapen i Azure Active Directory. |
| Microsoft.aad.Directory/groups/Basic/Update | Uppdatera grundläggande egenskaper för grupper i Azure Active Directory. |
| Microsoft.aad.Directory/groups/Create | Skapa grupper i Azure Active Directory. |
| microsoft.aad.directory/groups/createAsOwner | Skapa grupper i Azure Active Directory. Skaparen läggs till som första ägare och det skapade objektet räknas mot Skaparens kvot på 250 skapade objekt. |
| Microsoft.aad.Directory/groups/delete | Ta bort grupper i Azure Active Directory. |
| microsoft.aad.directory/groups/hiddenMembers/read | Läsa groups.hiddenMembers-egenskapen i Azure Active Directory. |
| Microsoft.aad.Directory/groups/members/Update | Uppdatera groups.members-egenskapen i Azure Active Directory. |
| Microsoft.aad.Directory/groups/owners/Update | Uppdatera groups.owners-egenskapen i Azure Active Directory. |
| Microsoft.aad.Directory/groups/Restore | Återställa grupper i Azure Active Directory. |
| microsoft.aad.directory/groups/settings/update | Uppdatera groups.settings-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/users/appRoleAssignments/update | Uppdatera users.appRoleAssignments-egenskapen i Azure Active Directory. |
| microsoft.aad.directory/users/assignLicense | Hantera licenser för användare i Azure Active Directory. |
| microsoft.aad.directory/users/basic/update | Uppdatera grundläggande egenskaper för användare i Azure Active Directory. |
| Microsoft.aad.Directory/Users/Create | Skapa användare i Azure Active Directory. |
| Microsoft.aad.Directory/Users/delete | Ta bort användare i Azure Active Directory. |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Ogiltigförklaras alla tokens för användaruppdatering i Azure Active Directory. |
| Microsoft.aad.Directory/Users/Manager/Update | Uppdatera users.manager-egenskapen i Azure Active Directory. |
| Microsoft.aad.Directory/Users/Password/Update | Uppdatera lösenord för alla användare i Azure Active Directory. Se onlinedokumentationen för mer information. |
| Microsoft.aad.Directory/Users/Restore | Återställa borttagna användare i Azure Active Directory. |
| microsoft.aad.directory/users/userPrincipalName/update | Uppdatera users.userPrincipalName-egenskapen i Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Läsa och konfigurera Azure Service Health. |
| microsoft.azure.supportTickets/allEntities/allTasks | Skapa och hantera Azure supportärenden. |
| microsoft.office365.webPortal/allEntities/basic/read | Läsa grundläggande egenskaper för alla resurser i microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Läsa och konfigurera Office 365 Service Health. |
| microsoft.office365.supportTickets/allEntities/allTasks | Skapa och hantera Office 365-supportbegäranden. |

## <a name="role-template-ids"></a>Mallen för ID: N

Mallen för ID: N används främst av Graph API eller PowerShell-användare.

Graph displayName | Visningsnamn för Azure portal | directoryRoleTemplateId
----------------- | ------------------------- | -------------------------
Programadministratör | Programadministratör | 9B895D92-2CD3-44C7-9D02-A6AC2D5EA5C3
Programutvecklare | Programutvecklare | CF1C38E5-3621-4004-A7CB-879624DCED7C
Autentisering-administratör | Autentisering-administratör | c4e39bd9-1100-46d3-8c65-fb160da0071f
Azure Information Protection-administratör | Azure Information Protection-administratör | 7495fdc4-34c4-4d15-a289-98788ce399fd
B2C användarflödet administratör | B2C användarflödet administratör | 6e591065-9bad-43ed-90f3-e9424366d2f0
B2C User Flow Attribute Administrator | B2C User Flow Attribute Administrator | 0f971eea-41eb-4569-a71e-57bb8a3eff1e
B2C IEF Keyset Administrator | B2C IEF Keyset Administrator | aaf43236-0c0d-4d5f-883a-6955382ac081
B2C IEF Policy Administrator | B2C IEF Policy Administrator | 3edaf663-341e-4475-9f94-5c398ef6c070
Faktureringsadministratör | Faktureringsadministratör | b0f54661-2d74-4c50-afa3-1ec803f12efe
Molnprogramadministratör | Molnprogramadministratör | 158c047a-c907-4556-b7ef-446551a6b5f7
Molnenhetsadministratör | Molnenhetsadministratör | 7698a772-787b-4ac8-901f-60d6b08affd2
Företagsadministratör | Global administratör | 62e90394-69f5-4237-9190-012177145e10
Efterlevnadsadministratör | Efterlevnadsadministratör | 17315797-102d-40b4-93e0-432062caca18
Efterlevnadsadministratör för Data | Efterlevnadsadministratör för data | e6d1a23a-da11-4be4-9570-befc86d067a7
Administratör för villkorsstyrd åtkomst | Administratör för villkorlig åtkomst | b1be1c3e-b65d-4f19-8427-f6fa0d97feb9
CRM-tjänstadministratör | Dynamics 365-administratör | 44367163-eba1-44c3-98af-f5787879f96a
Customer LockBox åtkomst godkännaren | Customer Lockbox åtkomst godkännaren | 5c4f9dcd-47dc-4cf7-8c9a-9e4207cbfc91
Desktop Analytics-administratör | Desktop Analytics-administratör | 38a96431-2bdf-4b4c-8b6e-5d3d8abac1a4
Enhetsadministratörer | Enhetsadministratörer | 9f06204d-73c1-4d4c-880a-6edb90606fd8
Enhetskoppling | Enhetskoppling | 9c094953-4995-41c8-84c8-3ebb9b32c93f
Enhetshanterare | Enhetshanterare | 2b499bcd-da44-4968-8aec-78e1674fa64d
Användare av enheter | Användare av enheter | d405c6df-0af8-4e3b-95e4-4d06e542189e
Katalogläsare | Katalogläsare | 88d8e3e3-8f55-4a1e-953a-9b9898b8876b
Konton för katalogen katalogsynkronisering | Konton för katalogsynkronisering | d29b2b05-8046-44ba-8758-1e26182fcf32
Katalogskrivare | Katalogskrivare | 9360feb5-f418-4baa-8175-e2a00bac4301
Exchange Service-administratör | Exchange-administratör | 29232cdf-9323-42fd-ade2-1d097af3e4de
Externa identitet tjänstproviderns administratör | Externa identitet tjänstproviderns administratör | be2f45a1-457d-42af-a067-6ec1fa63bc45
Gäst bjuder in | Gäst bjuder in | 95e79109-95c0-4d8e-aee3-d01accf2d47b
Supportavdelningsadministratör | Lösenordsadministratör | 729827e3-9c14-49f7-bb1b-9608f156bbb8
Administratör för Intune-tjänsten | Intune-administratör | 3a2c62db-5318-420d-8d74-23affee5d9d5
Kaizala Administrator | Kaizala-administratör | 74ef975b-6605-40af-a5d2-b9539d836353
Licensadministratör | Licensadministratör | 4d6ac14f-3453-41d0-bef9-a3e0c569773a
Lync-tjänstadministratör | Skype för företag-administratör | 75941009-915a-4869-abe7-691bff18279e
Sekretess Meddelandecenterläsare | Message center sekretess läsare | ac16e43d-7b2d-40e0-ac05-243ff356ab5b
Meddelandecenterläsare | Message center läsare | 790c1fb9-7f7d-4f88-86a1-ef1f95c05c1b
Partnersupport, nivå 1 | Nivå 1-support för partner | 4ba39ca4-527c-499a-b93d-d9b492c50246
Partnersupport, nivå 2 | Partnersupport, nivå 2 | e00e864a-17c5-4a4b-9c06-f5b95a8d5bd8
Power BI-tjänstadministratör | Power BI-administratör | a9ea8996-122f-4c74-9520-8edcd192826c
Administratören för privilegierade Authentication | Administratören Privilegierade authentication | 7be44c8a-adaf-4e2a-84d6-ab2649e08a13
Privilegierad Rolladministratör | Privilegierad rolladministratör | e8611ab8-c189-46e8-94e1-60213ab1f814
Rapportläsare | Rapportläsare | 4a5d8f65-41da-4de4-8968-e035b65339cf
Sök-administratör | Sökadministratör | 0964bb5e-9bdb-4d7b-ac29-58e794862a40
Sök-redigeraren | Sök-redigeraren | 8835291a-918c-4fd7-a9ce-faa49f0cf7d9
Säkerhetsadministratör | Säkerhetsadministratör | 194ae4cb-b126-40b2-bd5b-6091b380977d
Säkerhetsansvarig | Säkerhetsansvarig | 5f2222b1-57c3-48ba-8ad5-d4759f1fde6f
Säkerhetsläsare | Säkerhetsläsare | 5d6b6bb7-de71-4623-b4af-96380a352509
Tjänstsupportadministratör | Tjänstadministratör | f023fd81-a637-4b56-95fd-791ac0226033
SharePoint Service Administrator | SharePoint-administratör | f28a1f50-f6e7-4571-818b-6a12f2af6b6c
Teams kommunikation administratör | Teams kommunikation administratör | baf37b3a-610e-45da-9e62-d9d1e5e8914b
Teams kommunikation supporttekniker | Teams kommunikation supporttekniker | f70938a0-fc10-4177-9e90-2178f8765737
Teams kommunikation Support Specialist | Teams kommunikation Support Specialist | fcf91098-03e3-41a9-b5ba-6f0ec8188a12
Team-tjänstadministratör | Team-tjänstadministratör | 69091246-20e8-4a56-aa4d-066075b2a7a8
Användare | Användare | a0b1b346-4d3e-4e8b-98f8-753987be4970
Användarkonto-administratör | Användaradministratör | fe930be7-5e62-47db-91af-98c3a49a38b1
Arbetsplatsenhetskoppling | Arbetsplatsenhetskoppling | c34f683f-4d5a-4403-affd-6615e00e3a7f


## <a name="deprecated-roles"></a>Föråldrad roller

Följande roller ska inte användas. De har blivit inaktuella och tas bort från Azure AD i framtiden.

* Licensadministratör
* Enhetskoppling
* Enhetshanterare
* Användare av enheter
* E-Postverifierad Användarskapare
* Postlådeadministratör
* Arbetsplatsenhetskoppling

## <a name="next-steps"></a>Nästa steg

* Mer information om hur du tilldelar en användare som administratör för en Azure-prenumeration finns [hantera åtkomst med RBAC och Azure portal](../../role-based-access-control/role-assignments-portal.md)
* Mer information om hur resursåtkomsten hanteras i Microsoft Azure finns i [Förstå resursåtkomst i Azure](../../role-based-access-control/rbac-and-directory-admin-roles.md)
* Mer information om hur Azure Active Directory relaterar till din Azure-prenumeration finns i [Hur Azure-prenumerationer är associerade med Azure Active Directory](../fundamentals/active-directory-how-subscriptions-associated-directory.md)
