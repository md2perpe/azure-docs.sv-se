---
title: Vad är hybrid-identitet med Azure Active Directory?
description: Hybrid-identitet som har en vanlig användaridentitet för autentisering och auktorisering både lokalt och i molnet.
keywords: introduktion till Azure AD Connect, översikt över Azure AD Connect, vad är Azure AD Connect, installera Active Directory
services: active-directory
author: billmath
manager: daveba
ms.assetid: 59bd209e-30d7-4a89-ae7a-e415969825ea
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 05/17/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9c43238d44b2309d105ef14e696a5a16848d0b58
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65896825"
---
# <a name="what-is-hybrid-identity-with-azure-active-directory"></a>Vad är hybrid-identitet med Azure Active Directory?

Företag och organisationer blir i dag allt mer en blandning av lokala och molnbaserade program.  Användare behöver åtkomst till dessa program både lokalt och i molnet. Hantera användare både lokalt och i molnet-utgör en utmaning scenarier. 

Microsofts identitetslösningar omfattar både lokala och molnbaserade funktioner.  Dessa lösningar skapar en gemensam användaridentitet för autentisering och auktorisering för alla resurser, oavsett plats. Vi kallar det för **hybrididentitet**.

Hybrididentitet till Azure AD och hybrid-identitet och hantering av dessa scenarier blir möjligt.

För att uppnå hybrididentitet med Azure AD, kan en av tre autentiseringsmetoder användas, beroende på dina scenarier.   De tre metoderna är: 

- **[Synkronisering av lösenordshash (PHS)](whatis-phs.md)**  
- **[Direktautentisering (PTA)](how-to-connect-pta.md)**  
- **[Federation (AD FS)](whatis-fed.md)** 

Dessa autentiseringsmetoder ger även funktioner för [enkel inloggning](how-to-connect-sso.md).  Enkel inloggning loggar automatiskt in användare när de är på sina företagsenheter som är anslutna till företagsnätverket.

Mer information finns i avsnittet om att [välja rätt autentiseringsmetod för din Azure Active Directory-hybrididentitetslösning](https://docs.microsoft.com/azure/security/azure-ad-choose-authn). 

## <a name="common-scenarios-and-recommendations"></a>Vanliga scenarier och rekommendationer 

Här följer några vanliga scenarier med hybrididentitet och åtkomsthantering med rekommendationer om vilket (eller vilka) hybrididentitetsalternativ som passar för respektive scenario. 

|Jag behöver:|PHS och SSO<sup>1</sup>| PTA och SSO<sup>2</sup> | AD FS<sup>3</sup>| 
|-----|-----|-----|-----| 
|Synkronisera nya användare, kontakter och gruppkonton som skapas i min lokala Active Directory till molnet automatiskt.|![Rekommenderas](./media/whatis-hybrid-identity/ic195031.png)| ![Rekommenderas](./media/whatis-hybrid-identity/ic195031.png) |![Rekommenderas](./media/whatis-hybrid-identity/ic195031.png)| 
|Konfigurera min klientorganisation för Office 365-hybridscenarier.|![Rekommenderas](./media/whatis-hybrid-identity/ic195031.png)| ![Rekommenderas](./media/whatis-hybrid-identity/ic195031.png) |![Rekommenderas](./media/whatis-hybrid-identity/ic195031.png)| 
|Göra det möjligt för mina användare att logga in och komma åt molntjänster med sina lokala lösenord.|![Rekommenderas](./media/whatis-hybrid-identity/ic195031.png)| ![Rekommenderas](./media/whatis-hybrid-identity/ic195031.png) |![Rekommenderas](./media/whatis-hybrid-identity/ic195031.png)| 
|Implementera enkel inloggning med hjälp av företagsautentiseringsuppgifter.|![Rekommenderas](./media/whatis-hybrid-identity/ic195031.png)| ![Rekommenderas](./media/whatis-hybrid-identity/ic195031.png) |![Rekommenderas](./media/whatis-hybrid-identity/ic195031.png)|  
|Se till att inga lösenordshashvärden lagras i molnet.| |![Rekommenderas](./media/whatis-hybrid-identity/ic195031.png)|![Rekommenderas](./media/whatis-hybrid-identity/ic195031.png)| 
|Aktivera molnbaserade lösningar för multifaktorautentisering.|![Rekommenderas](./media/whatis-hybrid-identity/ic195031.png)|![Rekommenderas](./media/whatis-hybrid-identity/ic195031.png)|![Rekommenderas](./media/whatis-hybrid-identity/ic195031.png)| 
|Stödja lokala lösningar för multifaktorautentisering.| | |![Rekommenderas](./media/whatis-hybrid-identity/ic195031.png)| 
|Stödja autentisering med smartkort för mina användare.<sup>4</sup>| | |![Rekommenderas](./media/whatis-hybrid-identity/ic195031.png)| 
|Visa meddelanden om lösenord som upphör att gälla i Office-portalen och i skrivbordsversionen av Windows 10.| | |![Rekommenderas](./media/whatis-hybrid-identity/ic195031.png)| 

> <sup>1</sup> Lösenordshash-synkronisering med enkel inloggning. 
> 
> <sup>2</sup> Direktautentisering och enkel inloggning.  
> 
> <sup>3</sup> federerad enkel inloggning med AD FS.  
>  
> <sup>4</sup> AD FS kan integreras med din företags-PKI för att tillåta inloggning med certifikat. Dessa certifikat kan vara mjuka certifikat som distribueras via betrodda etableringskanaler, till exempel MDM, GPO eller smartkortscertifikat (inklusive PIV/CAC-kort) eller Hello för företag (cert-förtroende). Mer information om stöd för smartkortsautentisering finns på [den här bloggen](https://blogs.msdn.microsoft.com/samueld/2016/07/19/adfs-certauth-aad-o365/). 
> 

## <a name="license-requirements-for-using-azure-ad-connect"></a>Licenskrav för att använda Azure AD Connect

[!INCLUDE [active-directory-free-license.md](../../../includes/active-directory-free-license.md)]

## <a name="next-steps"></a>Nästa steg 

- [Vad är Azure AD Connect och Connect Health?](whatis-azure-ad-connect.md) 
- [Vad är synkronisering av lösenordshash (PHS)?](whatis-phs.md) 
- [Vad är direktautentisering (PTA)?](how-to-connect-pta.md) 
- [Vad är federation?](whatis-fed.md) 
- [Vad är enkel inloggning?](how-to-connect-sso.md) 

