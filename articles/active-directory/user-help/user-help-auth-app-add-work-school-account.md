---
title: Lägg till ditt arbets- eller skolkonto till Microsoft Authenticator-appen – Azure Active Directory | Microsoft Docs
description: Hur du lägger till arbets-eller skolkonto för Microsoft Authenticator-appen för verifiering av två faktorer.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 01/24/2019
ms.author: lizross
ms.reviewer: olhaun
ms.collection: M365-identity-device-management
ms.openlocfilehash: 231f3c44344f4119bab6e1efb5546e6fded0c784
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60473879"
---
# <a name="add-your-work-or-school-account"></a>Lägg till ditt arbets- eller skolkonto
Om din organisation använder två faktorer verifiering, kan du ställa in ditt arbets- eller skolkonto konto att använda Microsoft Authenticator-appen som en av verifieringsmetoderna.

>[!Important]
>Innan du kan lägga till ditt konto, måste du hämta och installera Microsoft Authenticator-appen. Om du inte har gjort det ännu, följer du stegen i den [ladda ned och installera appen](user-help-auth-app-download-install.md) artikeln.

## <a name="add-your-work-or-school-account"></a>Lägg till ditt arbets- eller skolkonto

1. På din dator, går du till den [ytterligare säkerhetsverifiering](https://aka.ms/mfasetup) sidan.

    >[!Note]
    >Om du inte ser den **ytterligare säkerhetsverifiering** sidan, är det möjligt att din administratör har aktiverat säkerhetsupplevelse för info (förhandsversion). Om så är fallet ska du följa anvisningarna i den [konfigurera säkerhetsinformation för att använda en autentiseringsapp](security-info-setup-auth-app.md) avsnittet. Om detta inte är fallet behöver du kontakta organisationens supportavdelning om du behöver hjälp. Läs mer om säkerhetsinformation [info (förhandsversion) Säkerhetsöversikt](user-help-security-info-overview.md).

2. Markera kryssrutan bredvid **autentiseringsapp**, och välj sedan **konfigurera**.

    Den **konfigurera mobilappen** visas.
    
    ![Skärmen som tillhandahåller QR-kod](./media/user-help-auth-app-download-install/auth-app-barcode.png)

3. Öppna Microsoft Authenticator-appen, Välj **Lägg till konto** från den **anpassa och kontroll** ikonen i det övre högra hörnet, och välj sedan **arbets- eller skolkonto**.

4. Använd enhetens kamera för att skanna QR-kod från den **konfigurera mobilappen** skärmen på datorn och välj sedan **klar**.

    >[!Note]
    >Om din kamera inte att samla in QR-koden kan du manuellt lägga till din kontoinformation till Microsoft Authenticator-appen för verifiering av två faktorer. Mer information och hur du gör det, finns i [manuellt lägga till ditt konto](user-help-auth-app-add-account-manual.md).

5. Granska den **konton** skärmen i appen på enheten, för att se till att ditt konto är rätt och att det finns en tillhörande sexsiffrig Verifieringskod. För ytterligare säkerhet ändrar verifieringskoden med 30 sekunders mellanrum hindra någon från och med en kod flera gånger.

    ![skärmen konton](./media/user-help-auth-app-download-install/auth-app-accounts.png)

## <a name="next-steps"></a>Nästa steg

- När du har lagt till dina konton till appen, kan du logga in med Authenticator-appen på din enhet. Mer information finns i [logga in med appen](user-help-auth-app-sign-in.md).

- För enheter som kör iOS, du kan även säkerhetskopiera dina autentiseringsuppgifter och appinställningar, till exempel ordningen på dina konton, som rör molnet. Mer information finns i [säkerhetskopiering och återställning med Microsoft Authenticator-appen](user-help-auth-app-backup-recovery.md).
