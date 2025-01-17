---
title: Web service - loggning i Azure Machine Learning Studio | Microsoft Docs
description: 'Lär dig hur du aktiverar loggning för Machine Learning Studio-webbtjänster. Loggning ger ytterligare information för felsökning av API: erna.'
services: machine-learning
documentationcenter: ''
author: xiaoharper
ms.custom: seodec18
ms.author: amlstudiodocs
editor: cgronlun
ms.assetid: c54d41e1-0300-46ef-bbfc-d6f7dca85086
ms.service: machine-learning
ms.subservice: studio
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/15/2017
ms.openlocfilehash: 727379edb60756ca8cb3e5ebdc29cd38858945e4
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60345659"
---
# <a name="enable-logging-for-azure-machine-learning-studio-web-services"></a>Aktivera loggning för Azure Machine Learning Studio-webbtjänster
Det här dokumentet innehåller information om funktionen loggning av Machine Learning Studio-webbtjänster. Loggning ger ytterligare information, utöver just en felkod och ett meddelande som kan hjälpa dig att felsöka dina anrop till API: er för Machine Learning Studio.  

## <a name="how-to-enable-logging-for-a-web-service"></a>Så här aktiverar du loggning för en webbtjänst

Du aktiverar loggning från den [Azure Machine Learning Studio-webbtjänster](https://services.azureml.net) portal. 

1. Logga in på portalen för Azure Machine Learning Studio-webbtjänster på [ https://services.azureml.net ](https://services.azureml.net). För en klassisk webbtjänst, du kan också få till portalen genom att klicka på **avanmäla för Web** på sidan Machine Learning Studio-webbtjänster i Machine Learning Studio.

   ![Nya tjänster webbupplevelse länk](./media/web-services-logging/new-web-services-experience-link.png)

2. På den övre menyraden klickar du på **webbtjänster** för en ny webbtjänst, eller klicka på **klassiska webbtjänster** för en klassisk webbtjänst.

   ![Välj ny eller klassiska webbtjänster](./media/web-services-logging/select-web-service.png)

3. Klicka på webbtjänstnamn för en ny webbtjänst. För en klassisk webbtjänst klickar du på webbtjänstnamn och sedan på nästa sida klickar du på lämplig slutpunkt.

4. På den övre menyraden klickar du på **konfigurera**.

5. Ange den **aktivera loggning** alternativet att *fel* (för att logga endast fel) eller *alla* (för fullständig loggning).

   ![Välj loggningsnivån](./media/web-services-logging/enable-logging.png)

6. Klicka på **Spara**.

7. Klassiska webbtjänster, skapa den **ml-diagnostics** behållare.

   Alla web service loggar sparas i en blobbehållare med namnet **ml-diagnostics** i lagringskontot som associerats med webbtjänsten. För nya webbtjänster skapas den här behållaren första gången du få åtkomst till webbtjänsten. För klassiska webbtjänster måste du skapa behållaren om den inte redan finns. 

   1. I den [Azure-portalen](https://portal.azure.com)går du till lagringskontot som associerats med webbtjänsten.

   2. Under **Blob Service** klickar du på **Containers**.

   3. Om behållaren **ml-diagnostics** inte finns, klickar du på **+ behållare**, ge den namnet ”ml-diagnostiken” för behållaren och välj den **åtkomsttyp** som ”Blob”. Klicka på **OK**.

      ![Skapa en ny behållare för att lagra dina diagnostikloggar](./media/web-services-logging/create-ml-diagnostics-container.png)

> [!TIP]
>
> Web Services-instrumentpanel i Machine Learning Studio har också en växel för att aktivera loggning för en klassisk webbtjänst. Men eftersom loggning hanteras nu via Web Services-portalen, måste du aktivera loggning via portalen, enligt beskrivningen i den här artikeln. Om du redan har aktiverat loggning i Studio, inaktivera loggning i webbportalen för tjänster och aktivera det igen.


## <a name="the-effects-of-enabling-logging"></a>Effekterna av att aktivera loggning
När loggning är aktiverat, diagnostik och fel från slutpunkten för webbtjänsten loggas i den **ml-diagnostics** blobbehållare i Azure Storage-konto som är kopplad till användarens arbetsytan. Den här behållaren innehåller alla diagnostikinformation för alla slutpunkterna för webbtjänster för alla arbetsytor som är associerade med det här lagringskontot.

Loggarna kan granskas med någon av flera verktyg som finns tillgängliga att utforska Azure Storage-kontot. Den enklaste kan vara att navigera till lagringskontot i Azure-portalen, klicka på **behållare**, och klicka sedan på behållaren **ml-diagnostics**.  

## <a name="log-blob-detail-information"></a>Detaljerad information från blob
Varje blobb i behållaren innehåller diagnostikinformationen för exakt ett av följande åtgärder:

* Körningen av Batch-Execution-metod  
* Körningen av metoden begäranden och svar  
* Initieringen av en behållare för begäranden och svar

Namnet på varje blob har ett prefix följande format: 


`{Workspace Id}-{Web service Id}-{Endpoint Id}/{Log type}`


Där _loggtyp_ är en av följande värden:  

* batch  
* poäng/begäranden  
* poäng/init  

