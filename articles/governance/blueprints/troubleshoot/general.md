---
title: Felsöka vanliga fel
description: Lär dig hur du felsöker problem skapa, tilldela och ta bort skisser.
author: DCtheGeek
ms.author: dacoulte
ms.date: 12/11/2018
ms.topic: troubleshooting
ms.service: blueprints
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: 42fdd6645a7a0e7cd9a2f0a7bc969e8eee62758c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60874968"
---
# <a name="troubleshoot-errors-using-azure-blueprints"></a>Felsöka med hjälp av Azure skisser

Du kanske stöter på fel när du skapar eller tilldelar skisser. Den här artikeln beskrivs olika fel som kan uppstå och hur du löser dem.

## <a name="finding-error-details"></a>Hitta information om fel

Många fel blir resultatet av att tilldela en skiss till ett omfång. När en uppgift misslyckas, innehåller information om den misslyckade distributionen i skissen. Den här informationen anger problemet så att det kan åtgärdas och nästa distributionen är klar.

1. Välj **Alla tjänster** i den vänstra rutan. Sök efter och välj **Skisser**.

1. Välj **tilldelade skisser** från sidan till vänster och Använd sökningen för att filtrera skissen tilldelningar för att hitta den misslyckade tilldelningen. Du kan också sortera tabellen med tilldelningar efter den **Etableringsstatus** kolumn att se alla misslyckade tilldelningar grupperas tillsammans.

1. Vänsterklicka på skissen med den _misslyckades_ status eller högerklicka och välj **visa tilldelningsinformation**.

1. En röd banderoll varning att tilldelningen misslyckades är högst upp på sidan för tilldelning av skissen. Klicka någonstans på banderollen för att få mer information.

Det är vanligt att felet kan orsakas av en artefakt och inte skissen som helhet. Om en artefakt skapar ett Key Vault och Azure Policy förhindrar skapande av Key Vault, misslyckas hela tilldelningen.

## <a name="general-errors"></a>Allmänt fel

### <a name="policy-violation"></a>Scenario: Principöverträdelse

#### <a name="issue"></a>Problem

Malldistributionen misslyckades på grund av principöverträdelse.

#### <a name="cause"></a>Orsak

En princip orsaka en konflikt med distributionen för en rad orsaker:

- Resursen håller på att skapas är begränsad av principen (vanligtvis SKU eller plats-begränsningar)
- Distributionen kan du ställa fält som konfigurerats av principen (vanliga med taggar)

#### <a name="resolution"></a>Lösning

Ändra skissen så att det inte står i konflikt med principerna i felinformationen. Om den här ändringen inte är möjligt, är ett alternativ att ha omfånget för principtilldelningen ändras så skissen inte längre är i konflikt med principen.

### <a name="escape-function-parameter"></a>Scenario: Skissparametern är en funktion

#### <a name="issue"></a>Problem

Skissparametrar som är funktioner bearbetas innan de skickas till artefakter.

#### <a name="cause"></a>Orsak

Skicka en skissparametern som använder en funktion som `[resourceGroup().tags.myTag]`, i en artefakt resulterar i bearbetade resultatet av funktionen som anges på artefakten i stället för funktionen dynamiskt.

#### <a name="resolution"></a>Lösning

Om du vill skicka en funktion via som en parameter, escape-hela strängen med `[` så att skissparametern ser ut som `[[resourceGroup().tags.myTag]`. Escape-tecknet orsakar skisser värdet ska behandlas som en sträng vid bearbetning av skissen. Skisser placerar funktionen på den artefakt så att den kan vara dynamiska som förväntat. Mer information finns i [mall filstruktur - syntax](../../../azure-resource-manager/resource-group-authoring-templates.md#syntax).

## <a name="next-steps"></a>Nästa steg

Om du inte ser ditt problem eller inte kan lösa problemet besöker du någon av följande kanaler för ytterligare support:

- Få svar från Azure-experter via [Azure-forumen](https://azure.microsoft.com/support/forums/).
- Anslut till [@AzureSupport](https://twitter.com/azuresupport) – det officiella Microsoft Azure-kontot för att förbättra kundtjänstupplevelsen genom att ansluta Azure-communityn till rätt resurser: svar, support och experter.
- Om du behöver mer hjälp kan öppna du en incident i Azure-supporten. Gå till den [Azure supportwebbplats](https://azure.microsoft.com/support/options/) och välj **hämta stöder**.