---
title: Azure MFA Server webbtjänsten Mobile App - Azure Active Directory
description: Konfigurera MFA-servern till att skicka push-meddelanden till användare med Microsoft Authenticator-appen.
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
ms.openlocfilehash: 2cbe10eb88550f04ead22a64fbcc2f17548af02d
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67057381"
---
# <a name="enable-mobile-app-authentication-with-azure-multi-factor-authentication-server"></a>Aktivera mobilappautentisering och Azure Multi-Factor Authentication Server

Microsoft Authenticator-appen erbjuder ytterligare ett verifieringsalternativ utanför IP-nätverket. I stället för att ringa ett automatiserat telefonsamtal eller skicka ett SMS till användaren vid inloggningen skickar Azure Multi-Factor Authentication ett meddelande till Microsoft Authentication-appen på användarens smartphone eller surfplatta. Användaren trycker bara på **Autentisera** (eller anger en PIN-kod och trycker på ”Autentisera”) i appen för att slutföra deras inloggning.

Att använda en mobilapp för tvåstegsverifiering rekommenderas när mobilmottagningen är opålitlig. Om du använder appen som en OATH-tokengenerator behövs ingen nätverks- eller internetanslutning.

> [!IMPORTANT]
> Från och med den 1 juli 2019 erbjuder Microsoft inte längre MFA Server för nya distributioner. Nya kunder som vill kräva multifaktorautentisering från sina användare ska använda molnbaserade Azure Multi-Factor Authentication. Befintliga kunder som har aktiverat MFA Server före 1 juli kommer att kunna hämta den senaste versionen, framtida uppdateringar och skapa autentiseringsuppgifter för aktivering som vanligt.

> [!IMPORTANT]
> Om du har installerat Azure Multi-Factor Authentication Server v8.x eller senare krävs inte de flesta av de nedanstående stegen. Mobilappsautentisering kan konfigureras med anvisningarna under [Konfigurera mobilappen](#configure-the-mobile-app-settings-in-the-azure-multi-factor-authentication-server).

## <a name="requirements"></a>Krav

För att använda Microsoft Authenticator-appen måste du köra Multi-Factor Authentication Server v8.x eller senare

## <a name="configure-the-mobile-app-settings-in-the-azure-multi-factor-authentication-server"></a>Konfigurera Mobile App-inställningarna i Azure Multi-Factor Authentication Server

1. I Multi-Factor Authentication-servern klickar du på ikonen Användarportal. Om användarna ska kunna styra sina autentiseringsmetoder markerar du **Mobilapp** under **Tillåt användare att välja metod** på fliken Inställningar. Om den här funktionen inte är aktiverad måste slutanvändarna kontakta din supportavdelning för att slutföra aktiveringen av mobilappen.
2. Markera kryssrutan **Tillåt användare att aktivera mobilapp**.
3. Markera kryssrutan **Tillåt användarregistrering**.
4. Klicka på ikonen för **Mobilapp**.
5. Fyll i fältet **Kontonamn** med företagets eller organisationens namn som ska visas i kontots mobilapp.
   ![MFA-serverkonfiguration för mobilappinställningar](./media/howto-mfaserver-deploy-mobileapp/mobile.png)

## <a name="next-steps"></a>Nästa steg

- [Avancerade scenarier med Azure Multi-Factor Authentication och virtuella privata nätverk från tredje part](howto-mfaserver-nps-vpn.md).
