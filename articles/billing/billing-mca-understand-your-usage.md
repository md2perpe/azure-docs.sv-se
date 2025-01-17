---
title: Villkoren i filen Azure användning och avgifter för en Microsoft-kundavtal
description: Lär dig mer om att läsa och förstå avsnitt av Azure-användning och kostnader CSV för fakturering profilen.
author: bandersmsft
manager: jureid
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: d11e31366ea5aa15cf7a790eaee800fa2ea6dabe
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/01/2019
ms.locfileid: "67490632"
---
# <a name="terms-in-the-azure-usage-and-charges-file-for-a-microsoft-customer-agreement"></a>Villkoren i filen Azure användning och avgifter för en Microsoft-kundavtal

Den här artikeln gäller för något faktureringskonto för en Microsoft-kundavtal. [Kontrollera om du har åtkomst till en Microsoft-kundavtal](#check-access-to-a-microsoft-customer-agreement).

Azure användning och avgifter CSV-filen innehåller mätaren servernivå och dagliga användningsdebitering för den aktuella faktureringsperioden.

För att få din Azure-användning och avgifter kan se [visa och ladda ned Azure-användning och kostnader för ditt Microsoft-kundavtal](billing-download-azure-daily-usage.md). Det är tillgängligt i en fil med kommaavgränsade värden (.csv)-format som kan öppnas i ett kalkylbladsprogram.

Användningsdebitering är det totala antalet **månatliga** debiteringar i en prenumeration. Avgifter för användning beakta inte en kredit eller rabatt.

## <a name="changes-from-azure-ea-usage-and-charges"></a>Ändringar från Azure EA-användning och avgifter

Om du har ett EA-kund, märker du att villkoren i Azure fakturering profil användning CSV-filen skiljer sig från villkoren i Azure EA användning CSV-filen. Här är en mappning av villkoren för EA-användning för perioder Användarvillkor profil:

| Användningen av Azure EA CSV | Microsoft Customer Agreement Azure-användning och avgifter CSV |
| --- | --- |
| Date | date |
| Månad| date |
| Dag | date |
| År | date |
| Product | product |
| MeterId | meterID |
| MeterCategory | meterCategory |
| MeterSubCategory | meterSubCategory |
| MeterRegion | meterRegion |
| meterName | meterName |
| ConsumedQuantity | kvantitet |
| ResourceRate | effectivePrice |
| ExtendedCost | cost |
| resourceLocation | resourceLocation |
| ConsumedService | consumedService |
| InstanceId | instanceId |
| ServiceInfo1 | ServiceInfo1 |
| ServiceInfo2 | serviceInfo2 |
| AdditionalInfo | AdditionalInfo |
| Tags | taggar |
| StoreServiceIdentifier | Gäller inte |
| Avdelningsnamn | invoiceSection |
| Kostnadsställe | costCenter |
| unitOfMeasure | unitofMeasure |
| ResourceGroup | resourceGroup |
| ChargesBilledSeparately | isAzureCreditEligible |

## <a name="detailed-terms-and-descriptions"></a>Detaljerade villkor och beskrivningar

Följande villkor visas i Azure-användning och avgifter.

Term | Beskrivning
--- | ---
invoiceId | Det unika dokument-ID som visas på fakturan PDF
previousInvoiceId | Referens till en ursprungliga faktura om den här radartikeln är en återbetalning
billingAccountName | Namnet på kontot fakturering
billingAccountId | Unik identifierare för roten fakturering konto
billingProfileId | Namnet på den fakturering profil som betalar avgifter som faktureras
billingProfileName | Unik identifierare för fakturering profilen som betalar avgifter som faktureras
invoiceSectionId | Unik identifierare för fakturan
invoiceSectionName | Namnet på avsnittet faktura
costCenter | Det kostnadsställe som definierats i prenumerationen för att spåra kostnader (endast tillgängligt i öppen faktureringsperioder)
billingPeriodStartDate | Startdatumet för den faktureringsperioden som fakturan skapas
billingPeriodEndDate | Slutdatum för den faktureringsperioden som fakturan skapas
servicePeriodStartDate | Startdatumet för den klassificering perioden som har definierat och låst priser för förbrukade eller köpta-tjänsten
servicePeriodEndDate | Slutdatum för klassificering perioden som har definierat och låst priser för förbrukade eller köpta-tjänsten
date | För Azure Marketplace användningsbaserade avgifter och är detta datumet för klassificering. Detta är inköpsdatum för enstaka inköp (reservationer, Marketplace) eller fasta återkommande avgifter (support erbjuder).
serviceFamily | Service-familj som tillhör tjänsten
productOrderId | Unik identifierare för ordningen som produkt
productOrderName | Unikt namn för ordningen som produkt
consumedService | Namnet på förbrukad tjänst
meterId | Den unika identifieraren för mätaren
meterName | Namnet på mätaren
meterCategory | Namnet på klassificeringskategori för mätaren. Till exempel *molntjänster*, *nätverk*osv.
meterSubCategory | Namnet på underkategori för mätning klassificering
meterRegion | Namnet på den region där mätaren för tjänsten är tillgänglig. Identifierar platsen för datacenter för vissa tjänster som prissätts baserat på datacenterplats.
erbjudande | Namnet på erbjudandet har köpt
productId | Unik identifierare för den produkt som betalar avgifterna
product | Namnet på den produkt som betalar avgifterna
prenumerations-ID | Unik identifierare för den prenumeration betalar avgifterna
subscriptionName | Namnet på den prenumeration betalar avgifterna
reservationId | Unik identifierare för den köpta reservation-instansen
reservationName | Namnet på den köpta reservation-instansen
publisherType | Typ av utgivare (värden: firstParty, thirdPartyReseller, thirdPartyAgency)
PublisherName | Utgivare för Marketplace-tjänster
resourceGroupId | Unik identifierare för resursgruppen som hör till resursen
resourceGroupName | Namnet på resursgruppen som hör till resursen
resourceId | Unik identifierare för resursinstansen
resourceType | Typ av resursinstans
resourceLocation | Identifierar platsen för det datacenter där resursen körs.
location | Normaliserade platsen för resursen om olika platser är konfigurerade för samma regioner
kvantitet | Antalet enheter som har köpt eller förbrukat
unitOfMeasure | Måttenhet för fakturering för tjänsten. Beräkningstjänster, till exempel faktureras per timme.
chargeType | Typ av avgift. Värden: <ul><li>AsCharged-Usage: Avgifter som ackumuleras baserat på användning av en Azure-tjänst. Detta inkluderar användning mot virtuella datorer som inte debiteras på grund av reserverade instanser.</li><li>AsCharged-PurchaseMarketplace: Enstaka eller fasta återkommande debiteringar från Marketplace-köp</li><li>AsCharged-UsageMarketplace: Avgifter för Marketplace-tjänster som debiteras baserat på enheter med användning</li></ul>
isAzureCreditEligible | Flagga som anger om tillägget mot tjänsten är berättigade att betalas för användning av Azure-krediter (värden: True, False)
ServiceInfo1 | Tjänstspecifik metadata
serviceInfo2 | Äldre fält som samlar in valfria tjänstspecifika metadata
AdditionalInfo | Ytterligare tjänstspecifika metadata.
taggar | Taggar som du tilldelar till resursen

### <a name="make-sure-that-charges-are-correct"></a>Se till att kostnader är korrekta

Om du vill se till att avgifter i dina detaljerad användningsfil är korrekta, kan du kontrollera dem. Se [förstå debiteringar på din faktureringsinformation profil faktura](billing-mca-understand-your-bill.md)

## <a name="check-access-to-a-microsoft-customer-agreement"></a>Kontrollera åtkomst till en Microsoft-kundavtal
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Behöver du hjälp? Kontakta oss.

Om du har frågor eller behöver hjälp, [skapa en supportbegäran](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nästa steg

- [Visa och ladda ned Microsoft Azure-faktura](billing-download-azure-invoice.md)
- [Visa och hämta dina Microsoft Azure-användning och kostnader](billing-download-azure-daily-usage.md)
