---
title: Förstå Azure Security Center för IoT-säkerhetsmodul för IoT Edge | Microsoft Docs
description: Förstå arkitekturen och funktionerna i Azure Security Center för IoT-säkerhetsmodul för IoT Edge.
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: e78523ae-d70a-456a-818d-f8b1b025d7cb
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/25/2019
ms.author: mlottner
ms.openlocfilehash: 847d6238bd42dc7da723dcc9acd47ed09c16dbf9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65200588"
---
# <a name="azure-iot-edge-security-module"></a>Azure IoT Edge-säkerhetsmodul

> [!IMPORTANT]
> Azure Security Center för IoT är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktion worklo§1ads. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/) ger kraftfulla funktioner för hantering och utföra affärs-arbetsflöden på gränsen.
Den viktiga delen som IoT Edge spelar i IoT-miljöer gör det mycket attraktivt för skadliga aktörer.

Azure Security Center (ASC) för IoT-säkerhetsmodul tillhandahåller en omfattande lösning för din IoT-Edge enheter.
ASC för IoT-modulen samlar in, aggregerar och analyserar raw säkerhetsdata från ditt operativsystem och behållarsystem i handlingsbara rekommendationer och aviseringar.

Liknar ASC för IoT security agenter för IoT-enheter, ASC för IoT Edge-modul är mycket anpassningsbara via dess modultvilling.
Se [konfigurera din agent](how-to-agent-configuration.md) vill veta mer.

ASC för IoT-säkerhetsmodul för IoT Edge erbjuder följande funktioner:

- Samlar in råa säkerhetshändelser från det underliggande operativsystemet (Linux) och behållare för IoT Edge-system.
  
  Se [ASC för IoT-agentkonfiguration](how-to-agent-configuration.md) mer information om tillgängliga säkerhetsuppdateringar datainsamlare.

- Analys av IoT Edge-distribution manifest.

- Aggregerar rådata säkerhetshändelser i meddelanden som skickas via [IoT Edge Hub](https://docs.microsoft.com/azure/iot-edge/iot-edge-runtime#iot-edge-hub).

- Ta bort konfigurationen genom användning av modultvilling säkerhet.

  Se [konfigurera en ASC för IoT-agenten](how-to-agent-configuration.md) vill veta mer.

ASC för IoT-säkerhetsmodul för IoT Edge körs i ett privilegierat läge under IoT Edge.
Privilegierat läge krävs för att tillåta modulen för att övervaka operativsystemet och andra IoT Edge-moduler.

## <a name="agent-supported-platforms"></a>Agent som stöds plattformar

ASC för IoT-säkerhetsmodul för IoT Edge finns för närvarande endast för Linux.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig om arkitekturen och funktionerna i ASC för IoT-säkerhetsmodul för IoT Edge.

Om du vill fortsätta att komma igång med ASC för IoT-distributionen, Använd följande artiklar:

- Distribuera [säkerhetsmodul för IoT Edge](how-to-deploy-edge.md)
- Lär dig hur du [konfigurera security-modulen](how-to-agent-configuration.md)
- Granska ASC för IoT [tjänsten krav](service-prerequisites.md)
- Lär dig hur du [aktivera ASC för IoT-tjänsten i din IoT-hubb](quickstart-onboard-iot-hub.md)
- Mer information om tjänsten från den [ASC för IoT vanliga frågor och svar](resources-frequently-asked-questions.md)