---
title: Felsöka Azure Active Directory-aktivitet loggarna fel i innehållspaket | Microsoft Docs
description: Ger dig en lista över felmeddelanden av Innehållspaketet för Azure Active Directory-aktivitet och steg för att åtgärda felen.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: ffce7eb1-99da-4ea7-9c4d-2322b755c8ce
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 06/07/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6ee49ae56122fe596a4490914677d91d2f0348f6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66807521"
---
# <a name="troubleshooting-azure-active-directory-activity-logs-content-pack-errors"></a>Felsöka Azure Active Directory-aktivitet loggarna fel i innehållspaket 

|  |
|--|
|För närvarande använder Azure AD Power BI-innehållspaketet Azure AD Graph-API:er för att hämta data från din Azure AD-klientorganisation. Därför kan det hända att du ser vissa skillnader mellan data som är tillgängliga i innehållspaketet och de data som hämtas med hjälp av [Microsoft Graph-API:er för rapportering](concept-reporting-api.md). |
|  |

När du arbetar med Power BI-Innehållspaketet för Azure Active Directory (AD Azure), är det möjligt att du får följande fel: 

- [Uppdateringen misslyckades](troubleshoot-content-pack.md#refresh-failed) 
- [Det gick inte att uppdatera datakällans autentiseringsuppgifter](troubleshoot-content-pack.md#failed-to-update-data-source-credentials) 
- [Import av data tar lång tid](#data-import-is-too-slow) 

Den här artikeln finns information om möjliga orsaker och hur du åtgärdar de här felen.
 
## <a name="refresh-failed"></a>Uppdateringen misslyckades 
 
**Hur det här felet visas**: E-post från Power BI- eller felstatus i historiken för uppdatering. 


| Orsak | Hur du åtgärdar |
| ---   | ---        |
| Uppdatera fel kan inträffa fel när autentiseringsuppgifterna för de användare som ansluter till Innehållspaketet har återställa men inte uppdateras i anslutningsinställningarna för Innehållspaketet. | Leta upp datauppsättningen för instrumentpanelen för aktivitetsloggar i Azure AD i Power BI (**Azure Active Directory-aktivitetsloggar**), välj Uppdatera schema och ange dina autentiseringsuppgifter för Azure AD. |
| En uppdatering kan misslyckas på grund av stora datauppsättningar. | Azure AD Innehållspaketet med Power BI kan för närvarande stöder endast små datauppsättningar (färre än 500,00 rader) på grund av vissa begränsningar i tidsgränser i Power BI-tjänsten. Om du stöter på begränsningsfel eller om uppdateringen misslyckas på grund av timeout-problem, kan det bero på att du försöker hämta en stor datauppsättning. Minska hur lång tid i frågan och försök igen.|
 
 
## <a name="failed-to-update-data-source-credentials"></a>Det gick inte att uppdatera datakällans autentiseringsuppgifter 
 
**Hur det här felet visas**: I Power BI, när du ansluter till Azure AD-aktivitet loggar Innehållspaketet. 

| Orsak | Hur du åtgärdar |
| ---   | ---        |
| Den anslutande användaren är inte en global administratör eller en säkerhetsläsare eller en säkerhetsadministratör. | Använd ett konto som är antingen en global administratör eller en säkerhetsläsare eller en administratör för att få åtkomst till innehållspaket. |
| Din klient är inte en Premium-klient eller har inte minst en användare med Premium-licens fil. | [Öppna ett supportärende](../fundamentals/active-directory-troubleshooting-support-howto.md).|
 


## <a name="data-import-is-too-slow"></a>Importera data är för långsam 
 
**Hur det här felet visas**: I Power BI när du har anslutit ditt innehållspaket dataimporten börjar att förbereda din instrumentpanel för Azure AD activity logs. Det här meddelandet visas: **Importerar data...**  utan någon ytterligare pågår.  

| Orsak | Hur du åtgärdar |
| ---   | ---        |
| Beroende på storleken på din klient kan kan det här steget ta allt från några minuter och 30 minuter. | Om meddelandet inte ändras till som visar instrumentpanelen inom en timme [öppna ett supportärende](../fundamentals/active-directory-troubleshooting-support-howto.md).|

## <a name="next-steps"></a>Nästa steg

* [Installera Power BI-Innehållspaketet för Azure AD-rapporter](quickstart-install-power-bi-content-pack.md).
* [Använda Power BI content pack för Azure AD-rapporter för att visualisera dina data](howto-power-bi-content-pack.md)
* [Få support för Azure Active Directory](../fundamentals/active-directory-troubleshooting-support-howto.md)
