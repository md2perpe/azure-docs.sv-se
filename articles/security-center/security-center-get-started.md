---
title: Azure Security Center-snabbstart – Publicera din Azure-prenumeration till Security Center Standard | Microsoft Docs
description: I den här snabbstarten får du lära dig att uppgradera till Security Centers Standard-prisnivå för ytterligare säkerhet.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security-center
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/3/2018
ms.author: rkarlin
ms.openlocfilehash: 4464796e655cefd28f858cb732ff3d5721a7aa47
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67206819"
---
# <a name="quickstart-onboard-your-azure-subscription-to-security-center-standard"></a>Snabbstart: Publicera din Azure-prenumeration till Security Center Standard
Azure Security Center erbjuder enhetlig säkerhetshantering och skydd mot hot i olika hybridmolnarbetsbelastningar. På den kostnadsfria nivån erbjuds endast begränsad säkerhet för dina Azure-resurser, medan Standard-nivån utökar funktionerna till lokala resurser och andra moln. Med Security Center Standard kan du hitta och åtgärda säkerhetsproblem, tillämpa åtkomst- och programkontroller för att blockera skadlig aktivitet, upptäcka hot med analys och intelligens och svara snabbt under attacker. Du kan prova Security Center Standard utan kostnad. Mer information finns på [prissidan](https://azure.microsoft.com/pricing/details/security-center/).

I den här artikeln uppgraderar du till Standard-nivån för utökad säkerhet och installerar Microsoft Monitoring Agent på dina virtuella datorer för att övervaka säkerhetsproblem och hot.

## <a name="prerequisites"></a>Nödvändiga komponenter
Du måste ha en prenumeration på Microsoft Azure för att komma igång med Security Center. Om du inte har någon prenumeration kan du registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/).

Om du vill uppgradera en prenumeration till standardnivån måste du vara tilldelad rollen som prenumerationsägare, prenumerationsdeltagare eller säkerhetsadministratör.

## <a name="enable-your-azure-subscription"></a>Aktivera din Azure-prenumeration

1. Logga in på [Azure-portalen](https://azure.microsoft.com/features/azure-portal/).
2. På menyn **Microsoft Azure** väljer du **Security Center**. **Security Center – Översikt** öppnas.

   ![Översikt över Security Center][2]

**Security Center – Översikt** ger en enhetlig överblick över säkerhetstillståndet för dina hybridarbetsbelastningar, vilket gör det lättare att identifiera och utvärdera säkerheten för arbetsbelastningarna samt identifiera och minimera risker. Security Center aktiverar automatiskt Azure-prenumerationer som inte tidigare har registrerats av dig eller någon annan prenumerationsanvändare på den kostnadsfria nivån.

Du kan visa och filtrera listan över prenumerationer genom att klicka på menyalternativet **Prenumerationer**. Security Center börjar nu utvärdera säkerheten för prenumerationerna för att identifiera säkerhetsproblem. Om du vill anpassa typerna av utvärderingar kan du ändra säkerhetsprincipen. En säkerhetsprincip definierar den önskade konfigurationen för arbetsbelastningarna och hjälper till att säkerställa efterlevnaden av företagets eller bestämmelsemässiga säkerhetskrav.

Inom några minuter efter att du har startat Security Center för första gången kanske du ser:

- **Rekommendationer** för sätt att förbättra säkerheten för dina Azure-prenumerationer. Om du klickar på panelen **Rekommendationer** öppnas en rangordnad lista.
- En inventering av resurserna **beräkning och appar**, **nätverk**, **datasäkerhet** och **identitet och åtkomst** som nu utvärderas av Security Center tillsammans med säkerhetspositionen för var och en.

Om du vill dra full nytta av Security Center måste du slutföra stegen nedan för att uppgradera till standardnivån och installera Microsoft Monitoring Agent.

## <a name="upgrade-to-the-standard-tier"></a>Uppgradera till standardnivån
Du måste uppgradera till standardnivån för att kunna använda snabbstart och självstudier i Security Center. Det finns en kostnadsfri utvärderingsversion av Security Center Standard. Mer information finns på [prissidan](https://azure.microsoft.com/pricing/details/security-center/). 

1. På huvudmenyn i Security Center väljer du **Komma igång**.
 
   ![Kom igång][4]

2. Under **Uppgradera** listar Security Center prenumerationer och arbetsytor som är behöriga för registrering. 
   - Du kan klicka på den expanderbara texten **Använd din utvärderingsversion** för att se en lista över alla prenumerationer och arbetsytor med deras berättigandestatus för utvärderingsversion.
   -    Du kan uppgradera prenumerationer och arbetsytor som inte är berättigade till utvärderingsversionen.
   -    Du kan välja berättigade arbetsytor och prenumerationer för att påbörja din utvärderingsperiod.
3. Klicka på **Starta utvärdering** för att påbörja din utvärderingsperiod för de valda prenumerationerna.


  ![Säkerhetsaviseringar][9]

## <a name="automate-data-collection"></a>Automatisera datainsamling
Security Center samlar in data från dina virtuella Azure-datorer och icke-Azure-datorer för att övervaka säkerhetsproblem och hot. Data samlas in med Microsoft Monitoring Agent, som läser olika säkerhetsrelaterade konfigurationer och händelseloggar från datorn och kopierar data till din arbetsyta för analys. Som standard skapar Security Center en ny arbetsyta till dig.

När automatisk etablering är aktiverat installerar Security Center Microsoft Monitoring Agent på alla virtuella Azure-datorer som stöds och alla nya som skapas. Automatisk försörjning rekommenderas starkt.

Så här aktiverar du automatisk försörjning för Microsoft Monitoring Agent:

1. På huvudmenyn i Security Center väljer du **Säkerhetsprincip**.
2. Välj **Redigera inställningar>** på prenumerationens rad.
3. På fliken **Datainsamling** anger du **Automatisk etablering** till **På**.
4. Välj **Spara**.
---
  ![Aktivera automatisk försörjning][6]

Med de här nya kunskaperna om dina virtuella datorer i Azure kan Security Center ge dig ytterligare rekommendationer som relaterar till systemuppdateringsstatus, OS-säkerhetskonfigurationer, slutpunktsskydd samt generera fler säkerhetsaviseringar.

  ![Rekommendationer][8]

## <a name="clean-up-resources"></a>Rensa resurser
De andra snabbstarterna och självstudierna i den här samlingen bygger på den här snabbstarten. Om du tänker fortsätta med att arbeta med efterföljande snabbstarter och självstudier ska du fortsätta att köra Standard-nivån och ha automatisk etablering aktiverad. Om du inte tänker fortsätta eller vill återgå till den kostnadsfria nivån:

1. Återgå till huvudmenyn i Security Center och välj **Säkerhetsprincip**.
2. På raden för prenumerationen som du vill ska bli kostnadsfri igen väljer du **Redigera inställningar>** .
3. Välj **Prisnivå** och välj **Kostnadsfri** om du vill byta prenumeration från Standard-nivån till den kostnadsfria nivån.
5. Välj **Spara**.

Om du vill avaktivera automatisk etablering:

1. Återgå till huvudmenyn i Security Center och välj **Säkerhetsprincip**.
2. Välj **Redigera inställningar>** på raden för prenumerationen som du vill inaktivera automatisk etablering för.
3. På fliken **Datainsamling** anger du **Automatisk etablering** till **Av**.
4. Välj **Spara**.

>[!NOTE]
> Inaktivering av automatisk etablering tar inte bort Microsoft Monitoring Agent från virtuella Azure-datorer där agenten har etablerats. Inaktivering av automatisk etablering begränsar säkerhetsövervakningen för dina resurser.
>

## <a name="next-steps"></a>Nästa steg
I den här självstudien har du uppgraderat till standardnivån och etablerat Microsoft Monitoring Agent för enhetlig säkerhetshantering och skydd mot hot i dina hybridmolnarbetsbelastningar. Om du vill lära dig mer om att använda Security Center fortsätter du till snabbstarten för publicering av Windows-datorer som är lokala och i andra moln.

> [!div class="nextstepaction"]
> [Snabbstart: Publicera Windows-datorer till Azure Security Center](quick-onboard-windows-computer.md)

<!--Image references-->
[2]: ./media/security-center-get-started/overview.png
[4]: ./media/security-center-get-started/get-started.png
[5]: ./media/security-center-get-started/pricing.png
[6]: ./media/security-center-get-started/enable-automatic-provisioning.png
[7]: ./media/security-center-get-started/security-alerts.png
[8]: ./media/security-center-get-started/recommendations.png
[9]: ./media/security-center-get-started/select-subscription.png
