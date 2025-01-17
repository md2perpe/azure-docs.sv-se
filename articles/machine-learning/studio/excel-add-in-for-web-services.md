---
title: Excel-tillägg för webbtjänster
titleSuffix: Azure Machine Learning Studio
description: Hur du använder Azure Machine Learning Web services direkt i Excel utan att behöva skriva någon kod.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 02/01/2018
ms.openlocfilehash: 9e801e0d7a26cd4d6c43118959aee1dec7216b1c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60750243"
---
# <a name="excel-add-in-for-azure-machine-learning-studio-web-services"></a>Excel-tillägget för Azure Machine Learning Studio-webbtjänster
Excel gör det enkelt att anropa webbtjänster direkt utan att behöva skriva någon kod.

## <a name="steps-to-use-an-existing-web-service-in-the-workbook"></a>Steg för att använda en befintlig webbtjänst i arbetsboken

1. Öppna den [Excel-exempelfilen](https://aka.ms/amlexcel-sample-2), som innehåller Excel-tillägg och data om passagerare på den på Titanic. 
 
    > [!NOTE]
    > Du kan se listan över de webbtjänster relaterade till filen och längst ned på sidan en kryssruta för ”automatisk förutsäga”. Om du aktiverar automatisk-förutsäga förutsägelser för **alla** dina tjänster kommer att uppdateras varje gång en ändring på indata. Om alternativet är avmarkerat måste du klicka på ”förutse alla” för uppdatering. För att aktivera automatisk-förutsäga på en tjänst på Gå till steg 6.

2. Välj webbtjänsten genom att klicka på den-”Titanic efterlevande ge säkrare prognoser (Excel-tillägget prov) [poäng]” i det här exemplet.
   
    ![Välj webbtjänst](./media/excel-add-in-for-web-services/image1.png)
3. Då kommer du till den **Predict** avsnittet.  Den här arbetsboken innehåller redan exempeldata, men för en tom arbetsbok kan du markera en cell i Excel och klicka på **använder exempeldata**.
4. Välj data med rubriker och klicka på ikonen indata intervall.  Kontrollera att rutan ”data innehåller rubriker” är markerad.
5. Under **utdata**, ange antalet celler där du vill att resultatet ska vara, till exempel ”H1” här.
6. Klicka på **förutsäga**. Om du markerar kryssrutan ”Automatisk predict” ska eventuella ändringar i de markerade områdena (de som anges som indata) utlösa en begäran och en uppdatering av utdata cellerna utan att behöva du ska trycka på knappen predict.
   
    ![Förutsäga avsnittet](./media/excel-add-in-for-web-services/image1.png)

Distribuera en webbtjänst eller Använd en befintlig webbtjänst. Mer information om hur du distribuerar en webbtjänst finns i [självstudie 3: Distribuera kredit risk modell](tutorial-part3-credit-risk-deploy.md).

Hämta API-nyckel för webbtjänsten. Om du utför beror denna åtgärd på om du har publicerat en klassiska Machine Learning-webbtjänst för en ny Machine Learning-webbtjänst.

**Använda en klassisk webbtjänst** 

1. I Machine Learning Studio, klickar du på den **WEBBTJÄNSTER** avsnittet i den vänstra rutan och välj sedan webbtjänsten.
   
    ![Studio väljer en webbtjänst](./media/excel-add-in-for-web-services/image4.png)
2. Kopiera API-nyckel för webbtjänsten.
   
    ![Studio API-nyckel](./media/excel-add-in-for-web-services/image5.png)
3. På den **INSTRUMENTPANELEN** för webbtjänsten klickar du på den **begäran/svar** länk.
4. Leta efter den **begär URI** avsnittet.  Kopiera och spara URL: en.

> [!NOTE]
> Nu är det möjligt att logga in på den [Azure Machine Learning Web Services](https://services.azureml.net) portalen för att hämta API-nyckel för en klassisk Machine Learning-webbtjänst.
> 
> 

**Använd en ny webbtjänst**

1. I den [Azure Machine Learning Web Services](https://services.azureml.net) klickar du på **webbtjänster**, välj sedan din webbtjänst. 
2. Klicka på **förbrukar**.
3. Leta efter den **grundläggande förbrukning info** avsnittet. Kopiera och spara den **primärnyckel** och **Request-Response** URL: en.

## <a name="steps-to-add-a-new-web-service"></a>Hur du lägger till en ny webbtjänst

1. Distribuera en webbtjänst eller Använd en befintlig webbtjänst. Mer information om hur du distribuerar en webbtjänst finns i [självstudie 3: Distribuera kredit risk modell](tutorial-part3-credit-risk-deploy.md).
2. Klicka på **förbrukar**.
3. Leta efter den **grundläggande förbrukning info** avsnittet. Kopiera och spara den **primärnyckel** och **Request-Response** URL: en.
4. I Excel, går du till den **webbtjänster** avsnittet (om du är i den **Predict** klickar du på bakåtpilen för att gå till listan för web services).
   
    ![Gå till Web service val](./media/excel-add-in-for-web-services/image3.png)
5. Klicka på **Lägg till webbtjänst**.
6. Klistra in Webbadressen till Excel-tillägget textrutan med etiketten **URL**.
7. Klistra in den API eller den primära nyckeln i textrutan **API-nyckel**.
8. Klicka på **Lägg till**.
   
    ![URL: en och API-nyckeln för en klassiska webbtjänst.](./media/excel-add-in-for-web-services/image6.png)
9. Om du vill använda webbtjänsten, följer du föregående anvisningarna och ”steg för att använda en befintlig web Service”.

## <a name="sharing-your-workbook"></a>Dela din arbetsbok
Om du har sparat din arbetsbok kan sparas även API eller den primära nyckeln för webbtjänster som du har lagt till. Det innebär att du bör bara dela arbetsboken med personer som du litar på.

Frågor i avsnittet nedan kommentar eller på vår [forum](https://go.microsoft.com/fwlink/?LinkID=403669&clcid=0x409).
