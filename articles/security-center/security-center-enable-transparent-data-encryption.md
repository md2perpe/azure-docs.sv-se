---
title: Aktivera Transparent datakryptering i Azure Security Center | Microsoft Docs
description: Det här dokumentet visar hur du implementerar Azure Security Center-rekommendationen **aktivera Transparent datakryptering**.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: e4be8a0e-2118-4ee9-a266-69e52d9f7f8e
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: rkarlin
ms.openlocfilehash: d5ddec40a1b20e377ec18ce871018f674557e7b4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60704040"
---
# <a name="enable-transparent-data-encryption-in-azure-security-center"></a>Aktivera Transparent datakryptering i Azure Security Center
Azure Security Center rekommenderar att du aktiverar Transparent datakryptering (TDE) på SQL-databaser om TDE inte redan är aktiverad. TDE skyddar dina data och hjälper dig att uppfylla efterlevnadskrav genom att kryptera databasen, tillhörande säkerhetskopior och transaktionsloggfiler vilande, utan ändringar i ditt program. Läs mer i [Transparent datakryptering med Azure SQL Database](https://msdn.microsoft.com/library/dn948096).

Den här rekommendationen gäller för Azure SQL-tjänsten. omfattar inte SQL som körs på dina virtuella datorer.

> [!NOTE]
> I det här dokumentet beskrivs tjänsten genom en exempeldistribution.  Det är alltså inte en steg-för-steg-guide.
>
>

## <a name="implement-the-recommendation"></a>Implementera rekommendationen
1. I den **rekommendationer** bladet väljer **aktivera Transparent datakryptering**.
   ![Aktivera transparent datakryptering][1]
2. Då öppnas det **aktivera Transparent datakryptering på SQL-databaser** bladet. Välj en SQL-databas för att aktivera TDE på.
   ![Välj SQL DB för att aktivera TDE på][2]
3. På den **Transparent datakryptering** bladet väljer **på** under datakryptering och välj **spara** i menyfliksområdet längst upp på bladet.
   ![Aktivera transparent Datakryptering][3]

   När TDE är aktiverat på den valda SQL-databasen i **krypteringsstatus** ändras till **krypterad**.    

   ![Krypteringsstatus][4]

## <a name="see-also"></a>Se också
Den här artikeln visar dig hur du implementerar Security Center-rekommendationen ”aktivera Transparent datakryptering”. Mer information om SQL TDE finns i:

* [Transparent datakryptering med Azure SQL Database](https://msdn.microsoft.com/library/dn948096)
* [Kom igång med Transparent datakryptering (TDE)](../sql-data-warehouse/sql-data-warehouse-encryption-tde.md)

I följande avsnitt kan du lära dig mer om Security Center:

* [Ange säkerhetsprinciper i Azure Security Center](tutorial-security-policy.md) – Här får du lära dig hur du ställer in säkerhetsprinciper för prenumerationer och resursgrupper i Azure.
* [Hantera säkerhetsrekommendationer i Azure Security Center](security-center-recommendations.md) – Lär dig hur rekommendationer hjälper dig att skydda dina Azure-resurser.
* [Övervakning av säkerhetshälsa i Azure Security Center](security-center-monitoring.md) – Lär dig att övervaka hälsotillståndet hos dina Azure-resurser.
* [Hantera och åtgärda säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md) – Här får du lära dig hur du hanterar och åtgärdar säkerhetsaviseringar.
* [Övervaka partnerlösningar med Azure Security Center](security-center-partner-solutions.md): Här får du lära dig hur du övervakar dina partnerlösningars hälsostatus.
* [Vanliga frågor och svar om Azure Security Center](security-center-faq.md) – Här hittar du vanliga frågor och svar om tjänsten.
* [Azures säkerhetsblogg](https://blogs.msdn.com/b/azuresecurity/) – senaste nytt i Azure-säkerhet och information.

<!--Image references-->
[1]: ./media/security-center-enable-tde-on-sql-databases/enable-tde.png
[2]:./media/security-center-enable-tde-on-sql-databases/transparent-data-encryption-blade.png
[3]: ./media/security-center-enable-tde-on-sql-databases/turn-on-tde.png
[4]: ./media/security-center-enable-tde-on-sql-databases/encrypted.png
