---
title: Upptäck Azure SQL Database Managed Instance inbyggda brandväggen | Microsoft Docs
description: Lär dig hur du verifierar inbyggda brandväggsskydd i Azure SQL Database Managed Instance.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, carlrab
manager: craigg
ms.date: 12/04/2018
ms.openlocfilehash: 774455a2901782ef52b213c6a13c17636e28b1a4
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60699650"
---
# <a name="verifying-the-managed-instance-built-in-firewall"></a>Verifiera den inbyggda brandväggen för hanterad instans

Den hanterade instansen [obligatoriska inkommande säkerhetsregler](sql-database-managed-instance-connectivity-architecture.md#mandatory-inbound-security-rules) kräver hanteringsportar 9000, 9003, 1438, 1440, 1452 vara öppna från **vilken källa som helst** på den Nätverkssäkerhetsgrupp (NSG) som skyddar hanterat Instans. Även om dessa portar är öppna på NSG-nivå, är de skyddade på nätverksnivå med den inbyggda brandväggen.

## <a name="verify-firewall"></a>Kontrollera brandväggen

Använda någon skanner säkerhetsverktyget för att testa portarna för att kontrollera dessa portar. Följande skärmbild visar hur du använder något av dessa verktyg.

![Verifiera inbyggda brandväggen](./media/sql-database-managed-instance-management-endpoint/03_verify_firewall.png)

## <a name="next-steps"></a>Nästa steg

Mer information om hanterade instanser och anslutning finns i [Azure SQL Database hanterad instans Anslutningsarkitektur](sql-database-managed-instance-connectivity-architecture.md).