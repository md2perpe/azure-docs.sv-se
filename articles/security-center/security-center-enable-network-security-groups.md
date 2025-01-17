---
title: Aktivera Nätverkssäkerhetsgrupper i Azure Security Center | Microsoft Docs
description: Det här dokumentet visar hur du implementerar Azure Security Center-rekommendationen **aktivera Nätverkssäkerhetsgrupper**.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: f53ed853-ffaf-4530-a019-1906ba6f341b
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: rkarlin
ms.openlocfilehash: 14b7cc8f8162574380ca21ac515af8b7d3d5ded9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60911470"
---
# <a name="enable-network-security-groups-in-azure-security-center"></a>Aktivera Nätverkssäkerhetsgrupper i Azure Security Center
Azure Security Center rekommenderar att du aktiverar en nätverkssäkerhetsgrupp (NSG) om något inte redan är aktiverad. Nätverkssäkerhetsgrupper innehåller en lista över regler för åtkomstkontrollistan (ACL) som tillåter eller nekar nätverkstrafik till dina VM-instanser i ett virtuellt nätverk. NSG:er kan antingen associeras med undernät eller individuella VM-instanser inom det undernätet. När en NSG är associerad med ett undernät, tillämpas ACL-reglerna på alla VM-instanser i det undernätet. Dessutom kan trafik till en enskild VM begränsas ytterligare genom att koppla en NSG direkt till den virtuella datorn. Läs mer i [vad är en Nätverkssäkerhetsgrupp (NSG)?](../virtual-network/security-overview.md)

Om du inte har Nätverkssäkerhetsgrupper aktiverade visar två rekommendationer i Security Center till dig: Aktivera Nätverkssäkerhetsgrupper på undernät och aktivera Nätverkssäkerhetsgrupper på virtuella datorer. Du kan välja vilken nivå, undernät eller virtuella datorn att tillämpa NSG: er.

> [!NOTE]
> I det här dokumentet beskrivs tjänsten genom en exempeldistribution.  Det är alltså inte en steg-för-steg-guide.
>
>

## <a name="implement-the-recommendation"></a>Implementera rekommendationen
1. I den **rekommendationer** bladet väljer **aktivera Nätverkssäkerhetsgrupper** på undernät eller virtuella datorer.
   ![Aktivera nätverkssäkerhetsgrupper][1]
2. Då öppnas bladet **konfigurera saknade Nätverkssäkerhetsgrupper** för undernät eller virtuella datorer, beroende på rekommendationen som du har valt. Välj ett undernät eller en virtuell dator för att konfigurera en NSG på.

   ![Konfigurera NSG för undernätet][2]

   ![Konfigurera NSG för VM][3]
3. På den **Välj nätverkssäkerhetsgrupp** bladet väljer du en befintlig NSG eller **Skapa nytt** att skapa en NSG.

   ![Välj Nätverkssäkerhetsgrupp][4]

Om du skapar en NSG, följer du stegen i [hantera en nätverkssäkerhetsgrupp](../virtual-network/manage-network-security-group.md) att skapa en Nätverkssäkerhetsgrupp och säkerhetsregler.

## <a name="see-also"></a>Se också
Den här artikeln visade hur du implementerar Security Center-rekommendationen ”aktivera Nätverkssäkerhetsgrupper” för undernät eller virtuella datorer. Mer information om hur du aktiverar NSG: er finns följande:

* [Vad är en nätverkssäkerhetsgrupp (NSG)?](../virtual-network/security-overview.md)
* [Hantera en grupp](../virtual-network/manage-network-security-group.md)

I följande avsnitt kan du lära dig mer om Security Center:

* [Ange säkerhetsprinciper i Azure Security Center](tutorial-security-policy.md) – Här får du lära dig hur du ställer in säkerhetsprinciper för prenumerationer och resursgrupper i Azure.
* [Hantera säkerhetsrekommendationer i Azure Security Center](security-center-recommendations.md) – Lär dig hur rekommendationer hjälper dig att skydda dina Azure-resurser.
* [Övervakning av säkerhetshälsa i Azure Security Center](security-center-monitoring.md) – Lär dig att övervaka hälsotillståndet hos dina Azure-resurser.
* [Hantera och åtgärda säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md) – Här får du lära dig hur du hanterar och åtgärdar säkerhetsaviseringar.
* [Övervaka partnerlösningar med Azure Security Center](security-center-partner-solutions.md): Här får du lära dig hur du övervakar dina partnerlösningars hälsostatus.
* [Vanliga frågor och svar om Azure Security Center](security-center-faq.md) – Här hittar du vanliga frågor och svar om tjänsten.
* [Azures säkerhetsblogg](https://blogs.msdn.com/b/azuresecurity/) – senaste nytt i Azure-säkerhet och information.

<!--Image references-->
[1]: ./media/security-center-enable-nsg/enable-nsg.png
[2]:./media/security-center-enable-nsg/configure-nsg-for-subnet.png
[3]: ./media/security-center-enable-nsg/configure-nsg-for-vm.png
[4]: ./media/security-center-enable-nsg/choose-nsg.png
