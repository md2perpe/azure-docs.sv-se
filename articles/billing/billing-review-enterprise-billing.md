---
title: Granska Azure enterprise-registrering faktureringsdata med REST API | Microsoft Docs
description: 'Lär dig hur du använder Azure REST API: er för att granska faktureringsinformation för enterprise-registrering.'
services: billing
documentationcenter: na
author: lleonard-msft
manager: ''
editor: ''
ms.assetid: 82D50B98-40F2-44B1-A445-4391EA9EBBAA
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/06/2018
ms.author: erikre
ms.openlocfilehash: 9a0b536426ab024d5af7b257e44a2d5e20f14def
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60371044"
---
# <a name="review-enterprise-enrollment-billing-using-rest-apis"></a>Granska enterprise enrollment fakturering med hjälp av REST API: er

Azure Reporting API: er hjälper dig att granska och hantera dina Azure-kostnader.

I den här artikeln får du lära dig att hämta debiteringsinformation som som är associerade med fakturering konton, avdelning eller registreringskonton för enterprise agreement (EA) med hjälp av REST-API: er för Azure. 

## <a name="individual-account-billing"></a>Fakturering för enskilda

Hämta information för konton i en avdelning:

```http
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/providers/Microsoft.Consumption/usageDetails?api-version=2018-06-30
Content-Type: application/json   
Authorization: Bearer
```

Den `{billingAccountId}` parametern krävs och bör innehålla ID för kontot.

Följande huvuden krävs: 

|Begärandehuvud|Beskrivning|  
|--------------------|-----------------|  
|*Content-Type:*|Krävs. Ange `application/json`.|  
|*Authorization:*|Krävs. Ange att ett giltigt `Bearer` [API-nyckel](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based). |  

Det här exemplet visar en synkron anrop som returnerar information för den aktuella faktureringsperioden. Av prestandaskäl returnera synkrona anrop information för den senaste månaden.  Du kan också anropa den [API asynkront](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based) att returnera data för 36 månader.


## <a name="response"></a>Svar  

Statuskod 200 returneras (OK) för ett lyckat svar som innehåller en lista med detaljerade kostnader för kontot.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/BillingAccounts/1234/providers/Microsoft.Billing/billingPeriods/201702/providers/Microsoft.Consumption/usageDetails/usageDetailsId1",
      "name": "usageDetailsId1",
      "type": "Microsoft.Consumption/usageDetails",
      "properties": {
        ...
        "usageStart": "2017-02-13T00:00:00Z",
        "usageEnd": "2017-02-13T23:59:59Z",
        "instanceName": "shared1",
        "instanceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Default-Web-eastasia/providers/Microsoft.Web/sites/shared1",
        "currency": "USD",
        "usageQuantity": 0.00328,
        "billableQuantity": 0.00328,
        "pretaxCost": 0.67,
        "isEstimated": false,
        ...
      }
    }
  ]
}
```  

Det här exemplet är förkortas; Se [hämta kostnadsinformation för något faktureringskonto](/rest/api/consumption/usagedetails/list#billingaccountusagedetailslist) för en fullständig beskrivning av varje svarsfält och felhantering.

## <a name="department-billing"></a>Avdelning fakturering 

Få användningsinformation som sammanställs för alla konton i en avdelning. 

```http
GET https://management.azure.com/providers/Microsoft.Billing/departments/{departmentId}/providers/Microsoft.Consumption/usageDetails?api-version=2018-06-30
Content-Type: application/json   
Authorization: Bearer
```

Den `{departmentId}` parametern krävs och bör innehålla ID för avdelningen registreringskontot.

Följande huvuden krävs: 

|Begärandehuvud|Beskrivning|  
|--------------------|-----------------|  
|*Content-Type:*|Krävs. Ange `application/json`.|  
|*Authorization:*|Krävs. Ange att ett giltigt `Bearer` [API-nyckel](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based). |  

Det här exemplet visar en synkron anrop som returnerar information för den aktuella faktureringsperioden. Av prestandaskäl returnera synkrona anrop information för den senaste månaden.  Du kan också anropa den [API asynkront](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based) att returnera data för 36 månader.

### <a name="response"></a>Svar  

Statuskod 200 returneras (OK) för ett lyckat svar som innehåller en lista över detaljerad användningsinformation och kostnader för en viss faktureringsperiod period och faktura-ID för avdelningen.


I följande exempel visas resultatet av REST-API för avdelning `1234`.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/Departments/1234/providers/Microsoft.Billing/billingPeriods/201702/providers/Microsoft.Consumption/usageDetails/usageDetailsId1",
      "name": "usageDetailsId1",
      "type": "Microsoft.Consumption/usageDetails",
      "properties": {
        "billingPeriodId": "/providers/Microsoft.Billing/Departments/1234/providers/Microsoft.Billing/billingPeriods/201702",
        "invoiceId": "/providers/Microsoft.Billing/Departments/1234/providers/Microsoft.Billing/invoices/201703-123456789",
        "usageStart": "2017-02-13T00:00:00Z",
        "usageEnd": "2017-02-13T23:59:59Z",
        "instanceName": "shared1",
        "instanceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Default-Web-eastasia/providers/Microsoft.Web/sites/shared1",
        "instanceLocation": "eastasia",
        "currency": "USD",
        "usageQuantity": 0.00328,
        "billableQuantity": 0.00328,
        "pretaxCost": 0.67,
        ...
      }
    }
  ]
}
```  

Det här exemplet är förkortas; Se [hämta kostnadsinformation för en avdelning](/rest/api/consumption/usagedetails/list#departmentusagedetailslist) för en fullständig beskrivning av varje svarsfält och felhantering.

## <a name="enrollment-account-billing"></a>Fakturering för registrering

Få användningsinformation som sammanställs för registreringskontot.

```http
GET GET https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts/{enrollmentAccountId}/providers/Microsoft.Consumption/usageDetails?api-version=2018-06-30
Content-Type: application/json   
Authorization: Bearer
```

Den `{enrollmentAccountId}` parametern krävs och bör innehålla ID för registreringskontot.

Följande huvuden krävs: 

|Begärandehuvud|Beskrivning|  
|--------------------|-----------------|  
|*Content-Type:*|Krävs. Ange `application/json`.|  
|*Authorization:*|Krävs. Ange att ett giltigt `Bearer` [API-nyckel](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based). |  

Det här exemplet visar en synkron anrop som returnerar information för den aktuella faktureringsperioden. Av prestandaskäl returnera synkrona anrop information för den senaste månaden.  Du kan också anropa den [API asynkront](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based) att returnera data för 36 månader.

### <a name="response"></a>Svar  

Statuskod 200 returneras (OK) för ett lyckat svar som innehåller en lista över detaljerad användningsinformation och kostnader för en viss faktureringsperiod period och faktura-ID för avdelningen.

I följande exempel visas resultatet av REST-API för företagsregistrering `1234`.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/EnrollmentAccounts/1234/providers/Microsoft.Billing/billingPeriods/201702/providers/Microsoft.Consumption/usageDetails/usageDetailsId1",
      "name": "usageDetailsId1",
      "type": "Microsoft.Consumption/usageDetails",
      "properties": {
        "billingPeriodId": "/providers/Microsoft.Billing/EnrollmentAccounts/1234/providers/Microsoft.Billing/billingPeriods/201702",
        "invoiceId": "/providers/Microsoft.Billing/EnrollmentAccounts/1234/providers/Microsoft.Billing/invoices/201703-123456789",
        "usageStart": "2017-02-13T00:00:00Z",
        "usageEnd": "2017-02-13T23:59:59Z",
        ....
        "currency": "USD",
        "usageQuantity": 0.00328,
        "billableQuantity": 0.00328,
        "pretaxCost": 0.67,
        ...
      }
    }
  ]
}
``` 

Det här exemplet är förkortas; Se [hämta kostnadsinformation för ett konto för enhetsregistreringshanterare](/rest/api/consumption/usagedetails/list#enrollmentaccountusagedetailslist) för en fullständig beskrivning av varje svarsfält och felhantering.

## <a name="next-steps"></a>Nästa steg 
- Granska [Enterprise rapporteringsöversikt](https://docs.microsoft.com/azure/billing/billing-enterprise-api)
- Undersöka [Enterprise fakturering REST-API](https://docs.microsoft.com/rest/api/billing/)   
- [Kom igång med Azure REST API](https://docs.microsoft.com/rest/api/azure/)   
