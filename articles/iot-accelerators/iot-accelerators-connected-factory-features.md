---
title: Ansluten fabrik-lösning funktioner – Azure | Microsoft Docs
description: En översikt över funktioner i lösningen ansluten fabrik förkonfigurerade.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: dobett
ms.openlocfilehash: 2a11640959a8c7fdd0d238aba92698eb47934969
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67080453"
---
# <a name="what-is-connected-factory-iot-solution-accelerator"></a>Vad är ansluten fabrik IoT lösningsaccelerator?

Ansluten fabrik är en implementering av Microsofts Azure industriella IoT-Referensarkitektur, paketeras som på öppen källkod. Du kan använda den som en startpunkt för en kommersiell produkt. Du kan distribuera en version som är inbyggda i lösningen ansluten fabrik i Azure-prenumerationen från [Azure IoT-Lösningsacceleratorer](https://www.azureiotsolutions.com/#solutions/types/CF).

![Instrumentpanelen för lösningen för ansluten fabrik](./media/iot-accelerators-connected-factory-features/dashboard.png)

Ansluten fabrik lösningsaccelerator [koden finns på GitHub](https://github.com/Azure/azure-iot-connected-factory).

Ansluten fabrik innehåller följande funktioner:

## <a name="industrial-device-interoperability"></a>Interoperabilitet för industriella enheter

- Anslut till industriella tillgångar med OPC UA-gränssnitt.
- Använda simulerade produktionsrader (köra OPC UA-servrar i Docker-behållare) för att se live telemetri från dem.
- Bläddra OPC UA-informationsmodellen av OPC UA-servrar från en instrumentpanel i molnet.

## <a name="remote-management"></a>Fjärrhantering

- Konfigurera OPC UA-tillgångar från molninstrumentpanelen (anropa metoder, läsa och skriva data).
- Publicera och avpublicera telemetridata från dina OPC UA-tillgångar från en molninstrumentpanel.

## <a name="cloud-dashboard"></a>Molninstrumentpanel

- Visa telemetri förhandsversioner direkt i en instrumentpanel i molnet.
- Visa trender i telemetridata och skapa korrelationer med hjälp av Time Series Insights Explorer-instrumentpanelen.
- Se beräknade övergripande utrustning effektivitet (OEE) och (Key Performance Indicator) från en instrumentpanel i molnet.
- Visa industriella tillgångar hierarkier i en topologi i trädet och på en interaktiv karta.
- Visa, är medveten om och stänga aviseringar från en instrumentpanel i molnet.

## <a name="azure-time-series-insights"></a>Azure Time Series Insights

- [Azure Time Series Insights](../time-series-insights/time-series-insights-overview.md) har utformats för att lagra, visualisera och fråga stora mängder time series-data. Ansluten fabrik använder den här tjänsten.
- Ansluten fabrik kan integreras med den här tjänsten gör att du utför djup i realtid analys av dina enhetsdata.

## <a name="rules-and-alerts"></a>Regler och aviseringar

[Konfigurera tröskelbaserade regler för aviseringar](iot-accelerators-connected-factory-configure.md).

## <a name="end-to-end-security"></a>Säkerhet slutpunkt till slutpunkt

- Konfigurera säkerhetsbehörigheter för användare med hjälp av rollbaserad åtkomstkontroll (RBAC).
- Slutpunkt till slutpunkt kryptering implementeras med hjälp av OPC UA-autentisering (med X.509-certifikat) samt säkerhetstoken.

## <a name="customizability"></a>Anpassningsbarhet

- Anpassa lösningen för att uppfylla specifika affärsbehov.
- Hela lösningen-källkoden finns på GitHub. Se den [förinställda lösningen ansluten fabrik](https://github.com/Azure/azure-iot-connected-factory) lagringsplats.

## <a name="next-steps"></a>Nästa steg

Läs mer om lösningsaccelerator ansluten fabrik i snabbstarten [prova en molnbaserad lösning för att hantera mina industriella IoT-enheter](quickstart-connected-factory-deploy.md).
