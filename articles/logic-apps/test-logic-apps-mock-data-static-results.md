---
title: Testa logic apps med fingerade data – Azure Logic Apps
description: Konfigurera statiska resultat för att testa logic apps med fingerade data utan att påverka produktionsmiljöer
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, LADocs
ms.topic: article
ms.date: 05/13/2019
ms.openlocfilehash: 45eeb20e5c572ddd98244b2e751322fcce1d4b76
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65597208"
---
# <a name="test-logic-apps-with-mock-data-by-setting-up-static-results"></a>Testa logic apps med fingerade data genom att ställa in statisk resultat

När du testar dina logic apps kan kanske du inte redo att faktiskt anropa eller få åtkomst till appar, tjänster och system för olika anledningar. Vanligtvis i dessa scenarier kan behöva du köra olika villkor sökvägar, tvinga fel, tillhandahålla specifikt meddelande svar organ eller ens försöker hoppa över några steg. Genom att ställa in statisk resultat för en åtgärd i din logikapp, kan du testa utdata från åtgärden. Aktivera statisk resultat på en åtgärd köra inte instruktionen, men returnerar fingerade data i stället.

Till exempel om du ställer in statisk resultat för Outlook 365 skicka e-post-åtgärd, Logic Apps-motorn returnerar bara fingerade data som du angav som statiska resultat, i stället för att anropa Outlook och skicka ett e-postmeddelande.

## <a name="prerequisites"></a>Nödvändiga komponenter

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du <a href="https://azure.microsoft.com/free/" target="_blank">registrera ett kostnadsfritt Azure-konto</a>.

* Grundläggande kunskaper om [hur du skapar logikappar](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Logikappen där du vill ställa in statisk resultat

<a name="set-up-static-results"></a>

## <a name="set-up-static-results"></a>Konfigurera statiska resultat

1. Om du inte redan har gjort i den [Azure-portalen](https://portal.azure.com), öppna logikappen i Logic Apps Designer.

1. På åtgärden där du vill ställa in statisk resultat, följer du dessa steg: 

   1. I åtgärdens övre högra hörnet väljer du ellipserna ( *...* ) och välj **statiska resultatet**, till exempel:

      ![Välj ”statiska resultat” > ”Aktivera statisk resultatet”](./media/test-logic-apps-mock-data-static-results/select-static-result.png)

   1. Välj **aktivera statisk resultatet**. Ange fingerad utdatavärden som du vill returnera åtgärdens svar för egenskaperna krävs (*).

      Här är till exempel de nödvändiga egenskaperna för HTTP-åtgärd:

      | Egenskap | Beskrivning |
      |----------|-------------|
      | **Status** | Åtgärdens status ska returneras |
      | **Statuskod** | Specifika statuskoden ska returneras |
      | **Headers** | Huvud-innehållet så att |
      |||

      ![Välj ”Aktivera statisk resultatet”](./media/test-logic-apps-mock-data-static-results/enable-static-result.png)

      Om du vill ange fingerade data i JavaScript Object Notation (JSON)-format, Välj **växla till JSON-läget** (![Välj ”växel till JSON-läget”](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button.png)).

   1. Valfria egenskaper, öppna den **Välj valfria fält** och välj de egenskaper som du vill testa.

      ![Välj valfri egenskaper](./media/test-logic-apps-mock-data-static-results/optional-properties.png)

1. När du är redo att spara väljer **klar**.

   I åtgärdens övre högra hörnet i namnlisten visas nu en bägare testikon (![ikonen för statiska resultat](./media/test-logic-apps-mock-data-static-results/static-results-test-beaker-icon.png)), vilket betyder att du har aktiverat statiska resultat.

   ![Ikon som visar aktiverat statiska resultat](./media/test-logic-apps-mock-data-static-results/static-results-enabled.png)

   Du hittar tidigare körningar som använder fingerade data [hitta körningar som använder statiska resultat](#find-runs-mock-data) senare i det här avsnittet.

<a name="reuse-sample-outputs"></a>

## <a name="reuse-previous-outputs"></a>Återanvända tidigare utdata

Om din logikapp har en tidigare kan kör med utdata som du kan återanvända som fingerad utdata du kopiera och klistra in utdata från som körs.

1. Om du inte redan har gjort i den [Azure-portalen](https://portal.azure.com), öppna logikappen i Logic Apps Designer.

1. På huvudmenyn för din logikapp, Välj **översikt**.

1. I den **Körningshistorik** sektionen, Välj den logikappskörningen om du vill.

1. Hitta och expandera den åtgärd som har de utdata som du vill ha i logikappens arbetsflöde.

1. Välj den **visa råutdata** länk.

1. Kopiera objektet fullständig JavaScript Object Notation (JSON) eller specifika avsnitt som du vill använda, till exempel, outputs-avsnittet eller bara avsnittet rubriker.

1. Följ stegen för att öppna den **Statiska resultatet** box för din åtgärd i [ställa in statisk resultat](#set-up-static-results).

1. Efter den **Statiska resultatet** öppnas, Välj antingen steg:

   * Om du vill klistra in ett fullständigt JSON-objekt, Välj **växla till JSON-läget** (![Välj ”växel till JSON-läget”](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button.png)):

     ![Välj ”växel till JSON-läget” för fullständigt objekt](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button-complete.png)

   * Om du vill klistra in bara ett JSON avsnitt, bredvid den områdesetikett väljer **växla till JSON-läget** för avsnittet, till exempel:

     ![Välj ”växel till JSON-läget” för utdata](./media/test-logic-apps-mock-data-static-results/switch-to-json-mode-button-outputs.png)

1. Klistra in din tidigare kopierade JSON i JSON-redigerare.

   ![JSON-läget](./media/test-logic-apps-mock-data-static-results/json-editing-mode.png)

1. När du är klar väljer du **Klar**. Eller, om du vill återgå till designern, välja **växel redigeringsläget** (![Välj ”växel redigeringsläget”](./media/test-logic-apps-mock-data-static-results/switch-editor-mode-button.png)).

<a name="find-runs-mock-data"></a>

## <a name="find-runs-that-use-static-results"></a>Hitta körningar som använder statiska resultat

Logikappens körnings identifierar körningar där åtgärderna som använder statiska resultat. Följ dessa steg för att hitta dessa körs:

1. På huvudmenyn för din logikapp, Välj **översikt**. 

1. I den högra rutan under **Körningshistorik**, hitta den **statiska resultat** kolumn. 

   Alla körningar som innehåller åtgärder med resultat har den **Statiska resultat** kolumnen **aktiverad**, till exempel:

   ![Körningshistorik - statisk resultat kolumn](./media/test-logic-apps-mock-data-static-results/run-history.png)

1. Om du vill visa åtgärder som använder statiska resultat, Välj det kör som du vill var den **Statiska resultat** kolumn har angetts till **aktiverad**.

   Åtgärder som använder statiska resultat visa test bägaren (![ikonen för statiska resultat](./media/test-logic-apps-mock-data-static-results/static-results-test-beaker-icon.png)) ikonen, till exempel:

   ![Körningshistorik - åtgärder som använder statiska resultat](./media/test-logic-apps-mock-data-static-results/static-results-enabled-run-details.png)

## <a name="disable-static-results"></a>Inaktivera statisk resultat

Om du inaktiverar statiska resultatet slänga inte värdena från din senaste konfiguration. Så när du aktiverar statisk resultat nästa gång kan du fortsätta använda dina tidigare värden.

1. Hitta åtgärden som du vill inaktivera statiska utdata. I åtgärdens övre högra hörnet väljer du ikonen test bägare (![ikonen för statiska resultat](./media/test-logic-apps-mock-data-static-results/static-results-test-beaker-icon.png)).

   ![Inaktivera statisk resultat](./media/test-logic-apps-mock-data-static-results/disable-static-results.png)

1. Välj **inaktivera statiska resultatet** > **klar**.

   ![Inaktivera statisk resultat](./media/test-logic-apps-mock-data-static-results/disable-static-results-button.png)

## <a name="reference"></a>Referens

Läs mer om den här inställningen i dina underliggande arbetsflödesdefinitioner [Statiska results - Schemareferens för Definitionsspråk för arbetsflödet](../logic-apps/logic-apps-workflow-definition-language.md#static-results) och [runtimeConfiguration.staticResult - Runtime konfigurationsinställningar](../logic-apps/logic-apps-workflow-actions-triggers.md#runtime-configuration-settings)

## <a name="next-steps"></a>Nästa steg

* Läs mer om [Azure Logic Apps](../logic-apps/logic-apps-overview.md)