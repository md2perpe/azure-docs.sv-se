---
title: Uppdatera Azure Backup centrala reporting Innehållspaketet
description: Information om uppdateringar av innehållet i Azure Backup-paket i Power BI
services: backup
author: kasinh
manager: vvithal
ms.service: backup
ms.topic: conceptual
ms.date: 01/30/2019
ms.author: kasinh
ms.openlocfilehash: 87bc7ed3bb59266484858f534080fa87a7230981
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60641965"
---
# <a name="update-the-azure-backup-central-reporting-content-pack"></a>Uppdatera Azure Backup centrala reporting Innehållspaketet 

Den [Azure Backup-Innehållspaketet](https://docs.microsoft.com/azure/backup/backup-azure-configure-reports#view-reports-in-power-bi) kan användas för att visa rapporter om central säkerhetskopiering. Innehållspaketet uppdateras regelbundet för att lägga till fler funktioner och åtgärda buggar. Den här artikeln visar hur du uppdaterar Innehållspaketet. Du får också information du hur du skjuta upp uppdateringen och se uppdateringar som görs över tid.

## <a name="get-updates-to-the-content-pack"></a>Hämta uppdateringar till Innehållspaketet

### <a name="get-the-updated-content-pack"></a>Hämta det uppdaterade Innehållspaketet
Om du inte har gjort några ändringar i din kopia av Innehållspaketet, uppdateras automatiskt. När Innehållspaketet ändras får du ett meddelande i Power BI och ett e-postmeddelande. Du kan välja att få det uppdaterade Innehållspaketet vid behov. 

### <a name="postpone-the-update"></a>Skjut upp uppdateringen
Det bästa sättet är att importera Innehållspaketet till en [anpassade arbetsytan](https://youtu.be/26zyOtyHPJM?t=1m57s). Du kan nu redigera rapporterna.
Som tidigare nämnts, om Innehållspaketet ändras kan se du ett meddelande i Power BI. Du kan välja att hämta Innehållspaketet senare. 

## <a name="coming-soon"></a>Kommer snart
   
Azure Backup-innehållspaket har uppdaterats för att stödja flera arbetsbelastningar. Arbetsbelastningar är Azure SQL Database för IaaS VM-säkerhetskopiering och System Center Data Protection Manager. Det här stödet lägger till det aktuella stödet för Azure backup och Virtuella Azure-säkerhetskopieringar. Det här stödet innebär att du kan visa och analysera dina säkerhetskopierade data på en central plats. [Rapporterna kan också kan anpassas](https://youtu.be/26zyOtyHPJM) som passar organisationens behov.

De förkonfigurerade rapporter som levereras med Azure Backup-Innehållspaketet ändras. Ändringarna gör rapporter mer meningsfulla i arbetsbelastningar. En förhandstitt på kommande uppsättning rapporter finns här.

### <a name="summary"></a>Sammanfattning
   
![Sammanfattning](./media/backup-azure-central-reporting/AzBackup-Central-Reporting-Summary.png)

### <a name="billing"></a>Fakturering

![Fakturering](./media/backup-azure-central-reporting/AzBackup-Central-Reporting-Billing.png)

### <a name="compliance"></a>Efterlevnad

![Efterlevnad](./media/backup-azure-central-reporting/AzBackup-Central-Reporting-Compliance.png)

### <a name="storage"></a>Storage

![Storage](./media/backup-azure-central-reporting/AzBackup-Central-Reporting-Storage.png)

### <a name="backup-items"></a>Säkerhetskopieringsobjekt
![Säkerhetskopieringsobjekt](./media/backup-azure-central-reporting/AzBackup-Central-Reporting-BackupItem.png)

### <a name="alerts"></a>Aviseringar

![Aviseringar](./media/backup-azure-central-reporting/AzBackup-Central-Reporting-Alerts.png)

### <a name="jobs"></a>Jobb

![Jobb](./media/backup-azure-central-reporting/AzBackup-Central-Reporting-Jobs.png)
    

## <a name="next-steps"></a>Nästa steg

* [Dela rapporter i hela organisationen](https://youtu.be/26zyOtyHPJM)
* [Azure Backup vanliga frågor och svar](backup-azure-backup-faq.md)
