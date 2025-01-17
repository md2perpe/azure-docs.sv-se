---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 10/26/2018
ms.author: raynew
ms.openlocfilehash: 9919521c8cb77f23f50a8097c4e630b4467dc725
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67187052"
---
### <a name="installation-failures"></a>Installationsfel
| **Exempel på felmeddelande** | **Rekommenderad åtgärd** |
|--------------------------|------------------------|
|ERROR   Failed to load Accounts. Fel: System.IO.IOException: Det går inte att läsa data från transportanslutningen när du installerar och registrerar CS-servern.| Kontrollera att TLS 1.0 är aktiverad på datorn. |

### <a name="registration-failures"></a>Registreringsfel
Du kan felsöka registreringsfel genom att granska loggarna i mappen **%ProgramData%\ASRLogs**.

| **Exempel på felmeddelande** | **Rekommenderad åtgärd** |
|--------------------------|------------------------|
|**09:20:06**:InnerException.Type: SrsRestApiClientLib.AcsException,InnerException.<br>meddelande: ACS50008: SAML-token är ogiltig.<br>Spårnings-ID: 1921ea5b-4723-4be7-8087-a75d3f9e1072<br>Korrelations-ID: 62fea7e6-2197-4be4-a2c0-71ceb7aa2d97><br>Timestamp: **2016-12-12 14:50:08Z<br>** | Kontrollera att tiden på din systemklocka inte skiljer sig mer än 15 minuter jämfört med din lokala tid. Kör installationsprogrammet igen för att slutföra registreringen.|
|**09:35:27** : DRRegistrationException vid försök att hämta alla disaster recovery-valv för det valda certifikatet:: Threw Exception.Type:Microsoft.DisasterRecovery.Registration.DRRegistrationException, Exception.Message: ACS50008: SAML-token är ogiltig.<br>Spårnings-ID: e5ad1af1-2d39-4970-8eef-096e325c9950<br>Korrelations-ID: abe9deb8-3e64-464d-8375-36db9816427a<br>Timestamp: **2016-05-19 01:35:39Z**<br> | Kontrollera att tiden på din systemklocka inte skiljer sig mer än 15 minuter jämfört med din lokala tid. Kör installationsprogrammet igen för att slutföra registreringen.|
|06:28:45: Det gick inte att skapa certifikatet<br>06:28:45: Installationen måste avbrytas. Det går inte att skapa ett certifikat som krävs för att autentisera till Site Recovery. Kör installationen igen | Se till att du kör installationen som lokal administratör. |
