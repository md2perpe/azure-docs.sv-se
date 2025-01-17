---
title: Arkitektoniska begrepp i Azure IoT Central | Microsoft Docs
description: Den här artikeln beskriver viktiga begrepp gällande arkitekturen i Azure IoT Central
author: dominicbetts
ms.author: dobett
ms.date: 05/31/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 4bc9a79576c3165585a4a2c897bd41bfb77c080c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66693130"
---
# <a name="azure-iot-central-architecture"></a>Azure IoT Central-arkitektur

Den här artikeln innehåller en översikt över Microsoft Azure IoT Central-arkitektur.

![Översta arkitektur](media/concepts-architecture/architecture.png)

## <a name="devices"></a>Enheter

Enheter utbyta data med Azure IoT Central programmet. En enhet kan:

- Skicka mätning av faktisk användning, till exempel telemetri.
- Synkronisera inställningar med ditt program.

De data som en enhet kan utbyta med ditt program har angetts i en mall för enheten i Azure IoT Central. Mer information om enheten mallar finns i [Metadata management](#metadata-management).

Läs mer om hur enheterna ska ansluta till Azure IoT Central programmet i [enhetsanslutning](concepts-connectivity.md).

## <a name="cloud-gateway"></a>Molngatewayen

Azure IoT Central använder Azure IoT Hub som en molngateway som möjliggör anslutningar på enheten. IoT Hub kan:

- Datainmatning i stor skala i molnet.
- Hantering av enheter.
- Skydda enhetsanslutning.

Läs mer om IoT Hub i [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/).

Mer information om enhetsanslutning i Azure IoT Central finns [enhetsanslutning](concepts-connectivity.md).

## <a name="data-stores"></a>Datalager

Azure IoT Central lagrar programdata i molnet. Programdata lagras innehåller:

- Mallar för enheten.
- Enhetsidentiteter.
- Enhetens metadata.
- Data för användaren och rollen.

Azure IoT Central använder en time series lagra för mätdata som skickas från dina enheter. Time series-data från enheter som används av analytics-tjänsten.

## <a name="analytics"></a>Analytics

Analytics-tjänsten ansvarar för att skapa anpassade reporting data som visas i programmet. En operatör kan [anpassa analyser](howto-create-analytics.md) visas i programmet. Analytics-tjänsten är byggt ovanpå [Azure Time Series Insights](https://azure.microsoft.com/services/time-series-insights/) och bearbetar mätdata som skickas från dina enheter.

## <a name="rules-and-actions"></a>Regler och åtgärder

[Regler och åtgärder](howto-create-telemetry-rules.md) samverkar att automatisera uppgifter i programmet. Hjälpverktyg kan definiera regler baserat på enhetstelemetri, till exempel temperaturen som överskrider ett angivet tröskelvärde. Azure IoT Central använder en stream-processor för att avgöra när villkor är uppfyllda. När en regelvillkor är uppfyllt, utlöser en åtgärd som definieras av tillverkaren. En åtgärd kan exempelvis skicka ett e-postmeddelande för att meddela en tekniker att temperaturen i en enhet är för högt.

## <a name="metadata-management"></a>Hantering av metadata

I ett Azure IoT Central program definierar enheten mallar beteende och möjligheterna för typer av enheter. Exempelvis anger en kylskåp enheten mall telemetri kylskåp skickar till ditt program.

![Mall-arkitektur](media/concepts-architecture/template_architecture.png)

I en mall för enheten:

- **Mätning av faktisk användning** ange telemetri som enheten skickar till programmet.
- **Inställningar för** anger de konfigurationer som en operatör kan ställa in.
- **Egenskaper för** ange metadata som en operatör kan ställa in.
- **Regler** automatisera beteende i appen baserat på data som skickas från en enhet.
- **Instrumentpaneler** är anpassningsbara vyer för en enhet i programmet.

Ett program kan ha en eller flera simulerade och verkliga enheter baserat på varje enhet-mall.

## <a name="data-export"></a>Export av data

I ett Azure IoT Central program, kan du [kontinuerligt exportera dina data](howto-export-data-event-hubs-service-bus.md) din egen Azure Event Hubs och Azure Service Bus-instanser. Du kan också regelbundet exportera dina data till Azure Blob storage-kontot. IoT Central kan exportera mått, enheter och mallar för enheten.

## <a name="batch-device-updates"></a>Batch-enhetsuppdateringar

I ett Azure IoT Central program, kan du [skapa och köra jobb](howto-run-a-job.md) att hantera anslutna enheter. Dessa jobb låter dig göra massuppdateringar enhetsegenskaper eller inställningar eller kör kommandon. Du kan till exempel skapa ett jobb för att öka hastigheten fläkt för flera kylda varuautomater.

## <a name="role-based-access-control-rbac"></a>Rollbaserad åtkomstkontroll (RBAC)

En [administratören kan definiera åtkomstregler](howto-administer.md) för ett Azure IoT Central-program med hjälp av de fördefinierade rollerna. En administratör kan tilldela användare till roller som bestämmer vilka delar av programmet användaren har åtkomst till.

## <a name="security"></a>Säkerhet

Säkerhetsfunktionerna i Azure IoT Central omfattar:

- Data krypteras vid överföring och i vila.
- Autentisering tillhandahålls genom Azure Active Directory eller Account. Tvåfaktorsautentisering stöds.
- Fullständig klientisolering.
- Enheten på radnivå.

## <a name="ui-shell"></a>UI-gränssnittet

UI-gränssnittet är en modern, dynamiskt, HTML5 webbläsarbaserade program.
En administratör kan anpassa Användargränssnittet för programmet genom att använda anpassade teman och ändra hjälplänkar så att den pekar till dina egna anpassade hjälpresurser. Läs mer om anpassning av Användargränssnittet i [anpassa Azure IoT Central Användargränssnittet](howto-customize-ui.md) artikeln.

En operatör kan skapa instrumentpaneler för anpassade program. Du kan ha flera instrumentpaneler som visar olika data och växla mellan dem.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig om arkitekturen för Azure IoT Central, föreslagna nästa steg är att lära dig om [enhetsanslutning](concepts-connectivity.md) i Azure IoT Central.