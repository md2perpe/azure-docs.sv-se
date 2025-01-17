---
title: Teknisk information till en Power BI-appen | Azure Marketplace
description: Konfigurera teknisk information fält till en Power BI-appen för Microsoft AppSource Marketplace.
services: Azure, AppSource, Marketplace, Cloud Partner Portal, Power BI
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: pabutler
ms.openlocfilehash: 15f4e2a76724a70c15411dea767cc9bc433e4d4a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64943223"
---
# <a name="power-bi-apps-technical-info-tab"></a>Power BI-appar teknisk informationsfliken

På den **nytt erbjudande** kan du använda den **teknisk information** flik för att ge Power BI-installationsprogrammet paketet URL och annan information som du behöver att verifiera det nya erbjudandet.  För denna första version är alla Power BI-appar är kostnadsfria och är tillgängliga för nedladdning från AppSource. Därför kan du definiera lagerhållningsenheter (SKU: er) för den här erbjudandetypen.

![Fliken teknisk information](./media/technical-info-tab.png)


## <a name="technical-info-fields"></a>Teknisk information om fält 

På den **teknisk information** fliken, fyller du i fälten som beskrivs i följande tabell. En asterisk (*) i slutet av en fältetikett innebär att fältet är obligatoriskt.

|        Fält          |  Beskrivning                                                                 |
|    ---------------    |  ----------------------------------------------------------------------------|
| **Installationsprogrammet URL\***     | Powerbi genererar denna URL när du publicerar appen och skicka det till produktion.  Mer information finns i [publicera appar med instrumentpaneler och rapporter i Power BI](https://docs.microsoft.com/power-bi/service-create-distribute-apps).  |
|  **Anvisningar för verifiering**  |  Om du vill kan du lägga till instruktioner (upp till 3 000 tecken) för att hjälpa Microsoft verifiering gruppen konfigurerar, ansluter och testa din app. Inkludera vanliga inställningar, konton, parametrar eller annan information som kan användas för att testa alternativet Anslut Data. Den här informationen är synliga enbart för verifiering team och den används endast för verifiering.  |
| **Den här appen har skapats som ett innehållspaket för Power BI?** | Det här fältet används för närvarande endast internt. Lämna standardinställningen för **nr**. Om du ändrar inställningen för att **Ja**, du kan stoppa publiceringsprocessen.  |  
|  |  |


## <a name="next-steps"></a>Nästa steg

På den [Storefront information](./cpp-storefront-details-tab.md) fliken, ange marknadsföring och juridisk information för din app.

