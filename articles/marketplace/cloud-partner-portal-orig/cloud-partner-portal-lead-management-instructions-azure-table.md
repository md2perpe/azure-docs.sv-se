---
title: Azure-tabell | Azure Marketplace
description: Konfigurera lead-hantering för Azure Table.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: pabutler
ms.openlocfilehash: 08f9d794822dfd7879efc7c4813ecc46f92f6a45
ms.sourcegitcommit: 72f1d1210980d2f75e490f879521bc73d76a17e1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/14/2019
ms.locfileid: "67147901"
---
# <a name="lead-management-instructions-for-azure-table"></a>Leda management instruktioner för Azure Table

Den här artikeln beskriver hur du konfigurerar Azure-tabell för att lagra säljleads. Azure-tabell kan du lagra och anpassa kundinformation.


## <a name="how-to-configure-azure-table"></a>Så här konfigurerar du Azure-tabell

1. Om du inte har ett Azure-konto, kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/).
2. När ditt Azure-konto är aktiv, logga in i den [Azure-portalen](https://portal.azure.com).
3. Skapa ett lagringskonto med följande procedur i Azure-portalen.  
    1. Välj **+ skapa en resurs** i den vänstra menyraden.  Den **New** fönstret (bladet) visas till höger.
    2. Välj **Storage** i den **New** fönstret.  En **aktuella** visas till höger.
    3. Välj **Lagringskonto** att börja skapa kontot.  Följ instruktionerna i artikeln [skapa ett lagringskonto](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal).

    ![Steg för att skapa ett Azure storage-konto](./media/cloud-partner-portal-lead-management-instructions-azure-table/azurestoragecreate.png)

    För mer information om storage-konton väljer [snabbsjälvstudien](https://docs.microsoft.com/azure/storage/).  Mer information om priser för storage finns i [priser för storage](https://azure.microsoft.com/pricing/details/storage/).

4. Vänta tills lagringskontot etablerats, en process som tar vanligtvis några minuter.  Åtkomst till ditt lagringskonto från den **Start** i Azure-portalen genom att välja **finns i alla dina resurser** eller genom att välja **alla resurser** i det vänstra navigeringsfönstret menyraden i Azure Portal.

    ![Få åtkomst till ditt Azure storage-konto](./media/cloud-partner-portal-lead-management-instructions-azure-table/azure-storage-access.png)

5. Kopiera anslutningssträngen för lagringskonto för nyckeln från din storage-konto-fönstret, och klistra in den i den **Lagringskontots anslutningssträng** på partnerportalen i molnet. Ett exempel på en anslutning förekomster av textsträngen är:

```sql
DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net
```

  ![Azure-lagringsnyckel](./media/cloud-partner-portal-lead-management-instructions-azure-table/azurestoragekeys.png)

Du kan använda [Azure Lagringsutforskaren](https://azurestorageexplorer.codeplex.com/) eller andra liknande verktyg för att se data i ditt storage-tabell. Du kan också exportera data från Azure-tabeller.


## <a name="use-microsoft-flow-with-an-azure-table-optional"></a>Använda Microsoft Flow med en Azure-tabell (*valfritt*) 

Du kan använda [Microsoft Flow](https://docs.microsoft.com/flow/) att automatisera meddelanden varje gång ett lead har lagts till i Azure-tabell. Om du inte har ett konto, kan du [registrera dig för ett kostnadsfritt konto](https://flow.microsoft.com/).


### <a name="lead-notification-example"></a>Leda meddelandeexemplet

Använd det här exemplet som en vägledning för att skapa ett enkelt flöde som automatiskt skickar ett e-postmeddelande när ett nytt lead läggs till i en Azure-tabell. Det här exemplet ställer in en upprepning att skicka lead-information varje timme om tabellagring är uppdaterad.

1. Logga in på ditt Microsoft Flow-konto.
2. I det vänstra navigeringsfältet väljer **Mina flöden**.
3. I det övre navigeringsfältet väljer **+ ny**.  
4. På den nedrullningsbara listan väljer **+ skapa från tom**
5. Skapa ett flöde från början, markera **skapa från tom**.

   ![Skapa ett nytt flöde från början](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-create-from-blank.png)

6. Kopplingar och utlösare söksidan, Välj **utlösare**.
7. Under **utlösare**väljer **upprepning**.
8. I den **upprepning** fönstret behålla standardinställningen på 1 för **intervall**. Från den **frekvens** listrutan, väljer **timme**.

   >[!NOTE] 
   >Även om det här exemplet används en 1-timmesintervall, kan du välja intervall och frekvens som passar bäst för dina affärsbehov.

   ![Ange frekvensen för 1 timme för upprepning](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-recurrence-dropdown.png)

9. Välj **+ nytt steg**.
10. Sök efter ”Get senaste gången” och välj sedan **hämta tidigare tid** under åtgärder. 

    ![Markera komma förbi Tidsåtgärd](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-search-getpasttime.png)

11. I den **hämta tidigare tid** ställer den **intervall** till 1.  Från den **tidsenhet** listrutan, väljer **timme**.
    >[!IMPORTANT] 
    >Se till att det här intervallet och en tidsenhet matchar intervall och frekvens som du har konfigurerat för upprepningen.

    ![Ange get tidigare tidsintervall](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-getpast-time.png)

    >[!TIP] 
    >Du kan kontrollera ditt flöde när som helst för att verifiera varje steg är rätt konfigurerad. Om du vill kontrollera ditt flöde, väljer **Flow förutsättningar** menyraden flöde.

I nästa uppsättning steg, ansluter du till din Azure-tabell och konfigurera standardbearbetningslogiken att hantera nya leads.

1. När du har Get tidigare tidssteg väljer **+ nytt steg**, och söker sedan efter ”Get-entiteter”.
2. Under **åtgärder**väljer **hämta entiteter**, och välj sedan **visa avancerade alternativ**.
3. I den **hämta entiteter** fönstret, ange information för följande fält:

   - **Tabellen** – ange namnet på ditt Azure Table Storage. Nästa skärmdump visar Kommandotolken när ”MarketPlaceLeads” anges i det här exemplet. 

     ![Välj ett anpassat värde för Azure-tabellnamn](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-getentities-table-name.png)

   - **Filterfråga** – Klicka på fältet och **hämta tidigare tid** ikon visas i ett popup-fönster. Välj **tidigare tid** att detta tidsstämpeln ska filtrera frågan. Du kan också klistra in följande funktion i fältet: CreatedTime `Timestamp gt datetime'@{body('Get_past_time')}'` 

     ![Konfigurera filterfunktion för fråga](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-getentities-filterquery.png)

4. Välj **nytt steg** att lägga till ett villkor så att Azure-tabell för nya leads.

   ![Använd nytt steg för att lägga till ett villkor så att Azure-tabell](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-add-filterquery-new-step.png)

5. I den **Välj en åtgärd** väljer **åtgärder**, och välj sedan den **villkor** kontroll.

     ![Lägg till Villkorskontroll](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-action-condition-control.png)

6. I den **villkor** väljer den **Välj ett värde** och välj sedan **uttryck** i popup-fönstret.
7. Klistra in `length(body('Get_entities')?['value'])` till den ***fx*** fält. Välj **OK** att lägga till den här funktionen. Att konfigurera villkoret:

   - Välj ”är större än” i listrutan.
   - Ange 0 som värde 

     ![Lägga till en funktion till villkoret](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-condition-fx0.png)

8. Konfigurera åtgärden ska utföras baserat på resultatet av villkoret.

     ![Konfigurera åtgärd baserat på villkoret resultat](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-condition-pick-action.png)

9. Om villkoret är **om ingen**, gör ingenting. 
10. Om villkoret är **om Ja**, utlösa en åtgärd som ansluter din Office 365-konto för att skicka ett e-postmeddelande. Välj **Lägg till en åtgärd**.
11. Välj **skicka ett e-postmeddelande**. 
12. I den **skicka ett e-postmeddelande** fönstret, ange information för följande fält:

    - **Att** -ange en e-postadress för alla användare som får det här meddelandet.
    - **Ämne** – ange ett ämne för e-postmeddelandet. Exempel: Nya leads!
    - **Body**:   Lägg till text som du vill inkludera i varje e-post (valfritt) och klistra sedan in brödtext `body('Get_entities')?['value']` som en funktion för att infoga lead-information.

      >[!NOTE] 
      >Du kan infoga ytterligare statisk eller dynamisk datapunkter och i brödtexten för e-postmeddelandet.

      ![Konfigurera e-post för lead-meddelande](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-emailbody-fx.png)

13. Välj **spara** att spara flödet. Microsoft Flow kommer automatiskt att testa flödet för fel. Om det inte finns några fel, startar ditt flöde körs när det sparats.

Nästa skärmdump visar ett exempel på hur den slutliga flow ska se.

[![Sista flödessekvens](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-end-to-end-thmb.png)](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-end-to-end.png)

(*Klickar du på bilden som förstora.* )


### <a name="manage-your-flow"></a>Hantera ditt flöde

Det är enkelt att hantera ditt flöde när den körs.  Du har fullständig kontroll över ditt flöde. Du kan exempelvis stoppa den, redigera den, finns i en körningshistorik och få analysfunktioner. Nästa skärmdump visar de alternativ som är tillgängliga för att hantera ett flöde. 

 ![Hantera ett flöde](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-manage-completed.png)

Flödet körs tills du stoppar den med hjälp av den **inaktivera flödet** alternativet.

Om du inte får någon lead e-postmeddelanden, innebär det att nya leads som inte har lagts till Azure-tabellen. Om det finns några fel i flow, får du ett e-postmeddelande som exemplet i nästa skärmdumpen.

 ![Flow felet e-postmeddelande](./media/cloud-partner-portal-lead-management-instructions-azure-table/msflow-failure-note.png)


## <a name="next-steps"></a>Nästa steg

[Konfigurera kundleads](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-get-customer-leads)
