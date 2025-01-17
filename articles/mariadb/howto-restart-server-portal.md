---
title: Starta om Azure Database for MariaDB-server med Azure-portalen
description: Den här artikeln beskrivs hur du kan starta om en Azure Database for MariaDB-server med hjälp av Azure-portalen.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 2/7/2019
ms.openlocfilehash: 232037562c4a84ee9217e2e89a0da2ffdc37d560
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60745798"
---
# <a name="restart-azure-database-for-mariadb-server-using-azure-portal"></a>Starta om Azure Database for MariaDB-server med Azure-portalen
Det här avsnittet beskrivs hur du kan starta om en Azure Database for MariaDB-server. Du kan behöva starta om servern för underhåll orsaker, vilket medför ett kort avbrott som servern utför åtgärden.

Starta om servern kommer att blockeras om tjänsten är upptagen. Tjänsten kan till exempel behandlar tidigare åtgärden, till exempel skala virtuella kärnor.

Den tid som krävs för att slutföra en omstart är beroende av MariaDB återställningsprocessen. Om du vill minska tiden för omstart, rekommenderar vi du minska mängden aktiviteten på server före omstarten.

## <a name="prerequisites"></a>Nödvändiga komponenter
Du behöver följande för att slutföra den här guiden:
- En [Azure Database for MariaDB-server](./quickstart-create-mariadb-server-database-using-azure-portal.md)

## <a name="perform-server-restart"></a>Utföra omstart av servern

Starta följande steg om MariaDB-server:

1. Välj din Azure Database for MariaDB-server i Azure-portalen.

2. I verktygsfältet på serverns **översikt** klickar du på **starta om**.

   ![Azure Database for MariaDB - översikt – starta](./media/howto-restart-server-portal/2-server.png)

3. Klicka på **Ja** att bekräfta att starta om servern.

   ![Azure Database for MariaDB - omstart bekräfta](./media/howto-restart-server-portal/3-restart-confirm.png)

4. Observera att Serverstatusen ändras till ”startar om”.

   ![Azure Database for MariaDB - status för omstart](./media/howto-restart-server-portal/4-restarting-status.png)

5. Bekräfta omstart av servern är klar.

   ![Azure Database for MariaDB - omstart lyckades](./media/howto-restart-server-portal/5-restart-success.png)

## <a name="next-steps"></a>Nästa steg

[Snabbstart: Skapa Azure Database for MariaDB-server med Azure-portalen](./quickstart-create-mariadb-server-database-using-azure-portal.md)