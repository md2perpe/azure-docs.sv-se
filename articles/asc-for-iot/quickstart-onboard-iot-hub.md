---
title: Använd Azure Security Center för IoT-tjänsten i förhandsversionen av IoT Hub | Microsoft Docs
description: Lär dig hur du aktiverar Azure Security Center för IoT-tjänsten i din IoT-hubb.
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 670e6d2b-e168-4b14-a9bf-51a33c2a9aad
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/16/2019
ms.author: mlottner
ms.openlocfilehash: 7030ae1c3a28cdd74671dc95dce59cf86cacf4c9
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65786166"
---
# <a name="quickstart-enable-service-in-iot-hub"></a>Snabbstart: Aktivera tjänsten i IoT Hub

> [!IMPORTANT]
> Azure Security Center för IoT är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Den här artikeln innehåller en förklaring på hur du aktiverar Azure Security Center (ASC) för IoT-förhandsversioner på IoT Hub.  

> [!NOTE]
> Azure Security Center för IoT för närvarande endast stöd för standardnivån för IoT-hubbar.
> Azure Security Center för IoT är en enda hubb-lösning. Om du behöver flera hubbar krävs flera lösningar. 

## <a name="prerequisites-for-enabling-the-service"></a>Förutsättningar för att aktivera tjänsten

- Log Analytics-arbetsyta
  - Två typer av information lagras som standard i Log Analytics-arbetsytan av ASC för IoT; **säkerhetsaviseringar** och **rekommendationer**. 
  - Du kan välja att lägga till lagring av en typ för ytterligare information, **råhändelser**. Observera att lagra **råhändelser** innebär ytterligare lagringskostnader i Log Analytics. 
- IoT Hub (standard-nivån)
- Uppfyller alla [tjänsten krav](service-prerequisites.md) 
- Stöds tjänstregioner
  - Centrala USA
  - Norra Europa
  - Sydostasien

## <a name="enable-asc-for-iot-on-your-iot-hub"></a>Aktivera ASC för IoT i din IoT-hubb 

Om du vill aktivera säkerheten på din IoT-hubb, gör du följande: 

1. Öppna din **IoT Hub** i Azure-portalen. 
2. Under den **Security** -menyn klickar du på **översikt**, klicka sedan på **Start förhandsversion**. 
3. Välj **aktivera IoT-säkerhet**. 
4. Ange information om din Log Analytics-arbetsytan. 
   - Välja att lagra **råhändelser** utöver typerna av information för lagring genom att låta den **raw händelse** växla **på**. 
   - Välja att aktivera **twin samling** genom att låta den **twin samling** växla **på**. 
5. Klicka på **Spara**. 

Gratulerar! Du har slutfört att aktivera ASC för IoT i din IoT-hubb. 

## <a name="next-steps"></a>Nästa steg

Gå vidare till nästa artikel om du vill lära dig hur du konfigurerar din lösning...

> [!div class="nextstepaction"]
> [Konfigurera din lösning](quickstart-configure-your-solution.md)
