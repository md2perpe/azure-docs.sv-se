---
title: Ladda ned Azure-fakturering och daglig användningsdata | Microsoft Docs
description: Beskriver hur du ladda ned eller visa Azure fakturering och daglig användningsdata.
keywords: faktura, nedladdning av faktura, azure-faktura, azure-användning
services: billing
documentationcenter: ''
author: genlin
manager: adpick
editor: ''
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: banders
ms.openlocfilehash: 16b2eaef74a7aa0e3e28bfcbb6dbd9da568db6cf
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/01/2019
ms.locfileid: "67491410"
---
# <a name="download-or-view-your-azure-billing-invoice-and-daily-usage-data"></a>Ladda ned eller visa din faktura för Azure och uppgifter om daglig användning

För de flesta prenumerationer kan du ladda ned din faktura från den [Azure-portalen](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) eller så har den skickas via e-post. Om du är Azure-kund med ett Enterprise-avtal (EA-kund), kan du ladda ned fakturor för din organisation. Fakturor skickas till den har ställts in för att ta emot fakturor för registrering.

Om du är EA-kund eller har en [Microsoft kundavtal](#check-access-to-a-microsoft-customer-agreement), du kan ladda ned användning i den [Azure-portalen](https://portal.azure.com/). För andra prenumerationer, går du till den [Azure Kontocenter](https://account.azure.com/Subscriptions) att ladda ned användning.

Endast vissa roller har behörighet att hämta någon faktureringsinformation faktura och användning, till exempel kontoadministratör eller företagsadministratör. Läs mer om att få åtkomst till faktureringsinformation på sidan om att [hantera åtkomst till Azure-fakturering med hjälp av roller](billing-manage-access.md).

Om du har ett Microsoft-kundavtal måste vara en fakturering profil ägare, deltagare, läsare, eller fakturera manager för att visa information om fakturering och användning. Läs mer om fakturering administratörsroller för Microsoft Customer avtal i [fakturering profil roller och uppgifter](billing-understand-mca-roles.md#billing-profile-roles-and-tasks).

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="download-your-azure-invoices-pdf"></a>Hämta din Azure-fakturor (PDF)

Du kan hämta din faktura för de flesta prenumerationer från Azure-portalen. Om du har ett Microsoft-kundavtal finns i hämta fakturor för fakturerings-profilen.

### <a name="download-invoices-for-an-individual-subscription"></a>Ladda ner fakturor för en enskild prenumeration

1. Välj din prenumeration från den [prenumerationssidan](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) i Azure portal enligt [en användare med åtkomst till fakturor](billing-manage-access.md).

2. Välj **Fakturor**.

    ![Skärmbild som visar alternativet fakturering och användning](./media/billing-download-azure-invoice-daily-usage-date/billingandusage.png)

3. Klicka på **hämta faktura** vill se en kopia av PDF-fakturor. Om status är Stopped **inte tillgänglig**, se [Varför ser jag en faktura för den senaste faktureringsperioden?](#noinvoice)

    ![Skärmbild som visar faktureringsperioder, hämtningsalternativ och totala kostnaden för varje faktureringsperiod](./media/billing-download-azure-invoice-daily-usage-date/billing4.png)

4. Du kan också visa det dagliga arbetet genom att klicka på faktureringsperioden.

Läs mer om fakturan [förstå fakturan för Microsoft Azure](billing-understand-your-bill.md). Läs hur du hanterar dina kostnader, [att undvika oväntade kostnader med Azure-fakturering och kostnadshantering](billing-getting-started.md).

### <a name="download-invoices-for-a-microsoft-customer-agreement"></a>Ladda ned fakturor för en Microsoft-kundavtal

Fakturor genereras för varje [fakturering profil](billing-mca-overview.md#billing-profiles) i Microsoft kundavtal. Du måste vara en fakturering profil ägare, deltagare, läsare, eller fakturera manager för att ladda ner fakturor från Azure-portalen.

1. Sök efter **Cost Management + fakturering**.
2. Välj en profil för fakturering.
3. Välj **Fakturor**.
4. Hitta raden på fakturan som du vill hämta i rutnätet för fakturan.
5. Klicka på ellipsknappen (`...`) i slutet av raden.
6. I snabbmenyn download väljer **faktura**.

Om du inte ser en faktura för den senaste faktureringsperioden, se **ytterligare information**. <!-- Fix this -->
### <a name="noinvoice"></a> Varför ser jag en faktura för den senaste faktureringsperioden?

Det kan finnas flera orsaker till att den inte visas:

- Det är mindre än 30 dagar sedan du började prenumerera på Azure.

- Fakturan har inte genererats än. Vänta till slutet av faktureringsperioden.

- Du har inte behörighet att visa fakturor. Om du har ett Microsoft-kundavtal, måste du vara fakturering profilen ägare, deltagare, läsare, eller fakturera manager. För andra prenumerationer, kan du inte ser gamla fakturor om du inte är kontoadministratören. Läs mer om att få åtkomst till faktureringsinformation på sidan om att [hantera åtkomst till Azure-fakturering med hjälp av roller](billing-manage-access.md).

- Om du har en kostnadsfri utvärderingsversion eller en månatlig kredit med din prenumeration som du inte överskrider kan får du inte en faktura om du inte har ett Microsoft-kundavtal.

## <a name="get-your-invoice-in-email-pdf"></a>Hämta din faktura i e-post (PDF)

Du kan välja och konfigurera ytterligare mottagare för att ta emot din Azure-faktura i ett e-postmeddelande. Den här funktionen är kanske inte tillgänglig för vissa prenumerationer som stöd för erbjudanden, Enterprise-avtal eller Azure i Open. Om du har ett avtal för Microsoft Customer Se Get profilen fakturering fakturor via e-post.

### <a name="get-your-subscriptions-invoices-in-email"></a>Få din prenumeration fakturor via e-post

1. Välj din prenumeration från den [prenumerationssidan](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). Välja i varje prenumeration som du äger. Klicka på **fakturor** sedan **e min faktura**.

    ![Skärmbild som visar valet i flödet](./media/billing-download-azure-invoice-daily-usage-date/InvoicesDeepLink.PNG)

2. Klicka på **anmäla** och godkänner villkoren.

    ![Skärmbild som visar valbar flow steg 2](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep2.PNG)

3. När du har accepterat avtalet, kan du konfigurera ytterligare mottagare. När en mottagare har tagits bort, lagras inte längre den e-postadressen. Om du ändrar dig kan behöva du lägga till dem igen.

    ![Skärmbild som visar valbar flow steg 3](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep3.PNG)

Om du inte får ett e-postmeddelande när du har följt stegen, kontrollera din e-postadress är korrekt i den [kommunikationsinställningar i din profil](https://account.windowsazure.com/profile).

### <a name="opt-out-of-getting-your-subscriptions-invoices-in-email"></a>Avanmäla dig från att få din prenumeration fakturor via e-post

Du kan välja bort komma fakturan via e-post genom att följa stegen ovan och klicka på **avanmäla dig från e-postfakturor**. Det här alternativet tar bort alla e-postadresser som är inställda för att ta emot fakturor via e-post. Du kan konfigurera om mottagarna om du väljer i.

 ![Skärmbild som visar välja bort flödet](./media/billing-download-azure-invoice-daily-usage-date/InvoiceArticleStep4.PNG)

### <a name="get-your-microsoft-customer-agreement-invoices-in-email"></a>Hämta dina Microsoft kundavtal fakturor via e-post

Om du har ett Microsoft-kundavtal, kan du välja för att hämta din faktura i ett e-postmeddelande. Alla fakturering profil ägare, deltagare, läsare och faktura chefer får fakturan via e-post. Läsare kan inte uppdatera dina inställningar för e-faktura.

1. Sök efter **Cost Management + fakturering**.
1. Välj en profil för fakturering.
1. Under **inställningar**väljer **egenskaper**.
1. Under **e-Postfaktura**väljer **inställning för Update e-faktura**.
1. Välj **anmäla**.
1. Klicka på **Uppdatera**.

### <a name="opt-out-of-getting-your-billing-profile-invoices-in-email"></a>Välja bort komma fakturering profil fakturor via e-post

Du kan välja bort komma fakturan via e-post genom att följa stegen ovan och klicka på **avanmäla dig**. Alla ägare, deltagare, läsare och faktura chefer väljs bort blir fakturan via e-post för. Om du är en läsare kan ändra du inte inställningar för e-faktura.

## <a name="download-usage"></a>Ladda ned användning

 För de flesta prenumerationer hittar du din dagliga användningsfil i den [Azure Kontocenter](https://account.azure.com/Subscriptions). Om du är EA-kund eller har ett avtal för Microsoft-kund kan du ladda ned användning i den [Azure-portalen](https://portal.azure.com/). <!-- TO DO: update PayG experience to Ibiza once it ships-->

### <a name="download-usage-from-the-account-center-csv"></a>Ladda ned användning från Azure Kontocenter (.csv)

1. Logga in på den [Azure Kontocenter](https://account.windowsazure.com/subscriptions) som kontoadministratör.

2. Välj den prenumeration som du vill ha information om faktura och användning.

3. Välj **FAKTURERINGSHISTORIK**.

    ![Skärmbild som visar historik betalningsalternativ](./media/billing-download-azure-invoice-daily-usage-date/Billinghisotry.png)

4. Du kan se dina utdrag för de senaste sex faktureringsperioderna och den aktuella, ännu inte fakturerade perioden.

    ![Skärmbild som visar faktureringsperioder, alternativ för att ladda ned faktura och daglig användning och totala kostnaden för varje faktureringsperiod](./media/billing-download-azure-invoice-daily-usage-date/billingSum.png)

5. Välj **Visa aktuellt meddelande** om du vill se en uppskattning av dina avgifter, vid den tidpunkt då uppskattningen genererades. Den här informationen uppdateras varje dag och kanske inte omfattar all användning. Din månadsfaktura kan skilja sig från den här beräkningen.

    ![Skärmbild som visar alternativet Visa aktuellt meddelande](./media/billing-download-azure-invoice-daily-usage-date/billingSum2.png)

    ![Skärmbild som visar uppskattning av aktuella debiteringar](./media/billing-download-azure-invoice-daily-usage-date/billingSum3.png)

6. Välj **Ladda ned användning** om du vill ladda ned dina dagliga användningsdata som en CSV-fil. Om det finns två tillgängliga versioner laddar du ned version två.

    ![Skärmbild som visar alternativet ladda ned användning](./media/billing-download-azure-invoice-daily-usage-date/DLusage.png)

Är bara kontoadministratören kan komma åt Azure Kontocenter. Andra fakturering administratörer, till exempel en ägare kan få användning informationen med hjälp av den [fakturerings-API: er](billing-usage-rate-card-overview.md).

Läs mer om din dagliga användning i [Förstå fakturan för Microsoft Azure](billing-understand-your-bill.md). Läs hur du hanterar dina kostnader, [att undvika oväntade kostnader med Azure-fakturering och kostnadshantering](billing-getting-started.md).

### <a name="download-usage-for-ea-customers"></a>Ladda ned användningsdata för EA-kunder

Om du vill visa och ladda ned användningsdata som EA-kund, måste du vara en Enterprise-administratör, ägare, eller avdelning Admin när vyn debiterar principen är aktiverad.

1. Logga in på [Azure Portal](https://portal.azure.com).
1. Sök efter *Cost Management + fakturering*.

    ![Skärmbild som visar Azure portal-sökning](./media/billing-download-azure-invoice-daily-usage-date/portal-cm-billing-search.png)

1. Välj **användning och kostnader**.
1. För den månad som du vill ladda ned, väljer **hämta**.

### <a name="download-usage-for-your-microsoft-customer-agreement"></a>Ladda ned användningsdata för ditt Microsoft-kundavtal

Om du vill visa och hämta användningsdata för en profil för fakturering, måste du vara en fakturering profil ägare, deltagare, läsare, eller fakturera manager.

#### <a name="download-usage-for-billed-charges"></a>Ladda ned användningsdata för fakturerade kostnader

1. Sök efter **Cost Management + fakturering**.
2. Välj en profil för fakturering.
3. Välj **Fakturor**.
4. Hitta raden på fakturan som motsvarar den användning som du vill hämta i rutnätet för fakturan.
5. Klicka på ellipsknappen (`...`) i slutet av raden.
6. I snabbmenyn download väljer **Azure-användning och kostnader**.

#### <a name="download-usage-for-open-charges"></a>Ladda ned användningsdata för öppna avgifter

Du kan också hämta månad hittills användning för den aktuella faktureringsperioden, vilket innebär att kostnaderna som inte har fakturerats ännu.

1. Sök efter **Cost Management + fakturering**.
2. Välj en profil för fakturering.
3. I den **översikt** bladet klickar du på **ladda ned Azure-användning och avgifter**.

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Kontrollera åtkomst till en Microsoft-kundavtal
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss

Om du har frågor eller behöver hjälp, [skapa en supportbegäran](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nästa steg

Mer information om din faktura och avgifter finns:

- [Förstå fakturan för Microsoft Azure](billing-understand-your-bill.md)
- [Förstå villkoren på fakturan för Azure](billing-understand-your-invoice.md)
- [Förstå villkoren på Microsoft Azure detaljerad användning](billing-understand-your-usage.md)
- [Visa din organisations Azure priser](billing-ea-pricing.md)

Om du har ett Microsoft-kundavtal, se:

- [Förstå debiteringar på fakturan för din faktureringsinformation profil](billing-mca-understand-your-bill.md)
- [Förstå villkoren på fakturan för din faktureringsinformation profil](billing-mca-understand-your-invoice.md)
- [Förstå filen Azure användning och avgifter för din faktureringsinformation profil](billing-mca-understand-your-usage.md)
- [Visa och hämta skattedokument för din faktureringsinformation profil](billing-mca-download-tax-document.md)
- [Visa din organisations Azure priser](billing-ea-pricing.md)
