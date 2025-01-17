---
title: Säkerhetsrekommendationer i Azure Security Center | Microsoft Docs
description: Det här dokumentet vägleder dig genom hur rekommendationerna i Azure Security Center hjälpa dig att skydda dina Azure-resurser och uppfyller säkerhetsprinciper.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: 86c50c9f-eb6b-4d97-acb3-6d599c06133e
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/13/2019
ms.author: v-mohabe
ms.openlocfilehash: fe1d4bf27f3c4bb1f70c1c1fa9767c27f8767998
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67064250"
---
# <a name="security-recommendations-in-azure-security-center"></a>Säkerhetsrekommendationer i Azure Security Center 
Det här avsnittet förklarar hur du visar och förstå rekommendationerna i Azure Security Center för att hjälpa dig att skydda dina Azure-resurser.

> [!NOTE]
> I det här dokumentet beskrivs tjänsten genom en exempeldistribution.  Det här dokumentet är inte en stegvis guide.
>

## <a name="what-are-security-recommendations"></a>Vad är säkerhetsrekommendationer?
Security Center analyserar regelbundet säkerhetstillståndet hos dina Azure-resurser. När Security Center identifierar potentiella säkerhetsproblem skapas rekommendationer. Via rekommendationerna får du hjälp att ställa in de kontrollfunktioner som behövs.

## <a name="implementing-security-recommendations"></a>Implementera säkerhetsrekommendationer
### <a name="set-recommendations"></a>Set-rekommendationer
I [ange säkerhetsprinciper i Azure Security Center](tutorial-security-policy.md), lär du dig att:

* Konfigurera säkerhetsprinciper.
* Aktivera insamling av data.
* Välj vilka rekommendationer för att se som en del av din säkerhetsprincip.

Aktuella rekommendationer principcentret runt systemuppdateringar, baslinjeregler, program, [nätverkssäkerhetsgrupper](../virtual-network/security-overview.md) på undernät och nätverksgränssnitt, SQL database-granskning, SQL database transparent datakryptering, och brandväggar för webbaserade program.  [Ange säkerhetsprinciper](tutorial-security-policy.md) innehåller en beskrivning av varje rekommendation alternativ.

### <a name="monitor-recommendations"></a>Övervakare för rekommendationer
När du har angett en säkerhetsprincip analyseras säkerhetstillståndet för resurserna i Azure i Security Center för upptäckt av eventuella säkerhetsrisker. Den **rekommendationer** panelen **översikt** visar det totala antalet rekommendationer identifierades av Security Center.

![Översikten över Security center](./media/security-center-recommendations/asc-overview.png)

1. Välj den **rekommendationer panelen** under **översikt**. Den **rekommendationer** öppnas.
    
      ![Visa rekommendationer](./media/security-center-recommendations/view-recommendations.png)

    Du kan filtrera rekommendationer. Om du vill filtrera rekommendationer, Välj **Filter** på den **rekommendationer** bladet. Den **Filter** bladet öppnas och du kan välja allvarlighetsgrad och status du vill se.

   * **REKOMMENDATIONER**: Rekommendationen.
   * **SKYDDAR SCORE INVERKAN**: Ett resultat som genereras av Security Center med hjälp av din säkerhetsrekommendationer och tillämpa avancerade algoritmer för att avgöra hur viktigt är varje rekommendation. Mer information finns i [skydda poängberäkningen](security-center-secure-score.md#secure-score-calculation).
   * **RESOURCE**: Visar en lista över de resurser som den här rekommendationen gäller.
   * **STATUSFÄLT**:  Visas hur viktig rekommendationen:
       * **Hög (röd)** : En sårbarhet i finns en viktig resurs (till exempel ett program, en virtuell dator eller en nätverkssäkerhetsgrupp) som måste åtgärdas.
       * **Medel (Orange)** : Det finns ett säkerhetsproblem och icke-kritiska eller ytterligare åtgärder krävs att eliminera den eller för att slutföra en process.
       * **Låg (blå)** : Det finns en säkerhetsrisk som bör åtgärdas, men inte kräver omedelbar åtgärd. (Låg rekommendationer är inte visas som standard, men du kan filtrera fram om du vill se dem.) 
       * **Felfritt (grönt)** :
       * **Inte tillgänglig (grå)** :

1. Du kan visa information om varje rekommendation genom att klicka på rekommendationen.

    ![Information om rekommendation](./media/security-center-recommendations/recommendation-details.png)

>[!NOTE] 
> Se [klassiska och Resource Manager-distributionsmodeller](../azure-classic-rm.md) för Azure-resurser.
  
 ### <a name="apply-recommendations"></a>Tillämpa rekommendationer
> Bestäm vilken jag ska använda först när du har granskat alla rekommendationer. Vi rekommenderar att du använder den säkra bedöma effekten att utvärdera vilka rekommendationer ska tillämpas först.

1. Klicka på rekommendationen i listan.
1. Följ instruktionerna i den *åtgärdssteg* avsnittet.

## <a name="next-steps"></a>Nästa steg
I det här dokumentet berättade säkerhetsrekommendationer i Security Center. Om du vill veta mer om Security Center finns i följande avsnitt:

* [Ange säkerhetsprinciper i Azure Security Center](tutorial-security-policy.md) – Lär dig hur du ställer in säkerhetsprinciper för dina Azure-prenumerationer och resursgrupper.
* [Övervakning av säkerhetshälsa i Azure Security Center](security-center-monitoring.md) – Lär dig hur du övervakar Azure-resursernas hälsa.
* [Hantera och åtgärda säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md) – Lär dig hur du hanterar och åtgärdar säkerhetsaviseringar.
* [Övervaka partnerlösningar med Azure Security Center](security-center-partner-solutions.md) – Lär dig hur du övervakar dina partnerlösningars hälsostatus.
* [Vanliga frågor och svar om Azure Security Center](security-center-faq.md) – Här hittar du vanliga frågor och svar om tjänsten.
* [Azures säkerhetsblogg](https://blogs.msdn.com/b/azuresecurity/) – Här hittar du blogginlägg om säkerhet och regelefterlevnad i Azure.
