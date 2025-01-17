---
title: Simulerad enhetsbeteende i fjärrövervakningslösningen – Azure | Microsoft Docs
description: Den här artikeln beskriver hur du kan använda JavaScript för att definiera beteendet för en simulerad enhet i fjärrövervakningslösningen.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/29/2018
ms.topic: conceptual
ms.openlocfilehash: 04d2ad2f0e86ee977600af86a2ffd1e9d7680375
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65823424"
---
# <a name="implement-the-device-model-behavior"></a>Implementera beteendet för enhetsmodellen

Artikeln [förstå schemat för enhetsmodellen](iot-accelerators-remote-monitoring-device-schema.md) beskrivs det schema som definierar en simulerad enhet-modell. Den här artikeln avses två typer av JavaScript-fil som implementerar beteendet för en simulerad enhet:

- **Tillstånd** JavaScript-filer som körs med jämna tidsintervall att uppdatera det interna tillståndet för enheten.
- **Metoden** JavaScript-filer som körs när lösningen anropar en metod på enheten.

> [!NOTE]
> Modellen enhetsbeteenden är endast för simulerade enheter i device simulering-tjänsten. Om du vill skapa en riktig enhet Se [ansluta enheten till lösningsacceleratorn för fjärrövervakning](iot-accelerators-connecting-devices.md).

I den här artikeln kan du se hur du:

>[!div class="checklist"]
> * Kontrollera tillståndet för en simulerad enhet
> * Definiera hur en simulerad enhet svarar på ett metodanrop från lösningen för fjärrövervakning
> * Felsök ditt skript

## <a name="state-behavior"></a>Statliga beteende

Den [simulering](../../articles/iot-accelerators/iot-accelerators-remote-monitoring-device-schema.md#simulation) delen av schemat för enhetsmodellen definierar det interna tillståndet för en simulerad enhet:

- `InitialState` definierar ursprungliga värden för alla egenskaper för enhetsobjekt för tillstånd.
- `Script` identifierar en JavaScript-fil som körs på ett schema för att uppdatera enhetens tillstånd.

Exemplet nedan visar definitionen av enhetsobjekt för tillstånd för en simulerad kylaggregat enhet:

```json
"Simulation": {
  "InitialState": {
    "online": true,
    "temperature": 75.0,
    "temperature_unit": "F",
    "humidity": 70.0,
    "humidity_unit": "%",
    "pressure": 150.0,
    "pressure_unit": "psig",
    "simulation_state": "normal_pressure"
  },
  "Interval": "00:00:05",
  "Scripts": {
    "Type": "javascript",
    "Path": "chiller-01-state.js"
  }
}
```

Tillståndet för den simulerade enheten, enligt definitionen i den `InitialState` avsnittet, lagras i minnet av tjänsten simulering. Tillståndsinformationen skickas som indata till den `main` funktion som definierats i **kylaggregat-01-state.js**. I det här exemplet tjänsten simuleringen körs den **kylaggregat-01-state.js** fil var femte sekund. Skriptet kan ändra tillståndet för den simulerade enheten.

Följande visar konturerna för en typisk `main` funktionen:

```javascript
function main(context, previousState, previousProperties) {

  // Use the previous device state to
  // generate the new device state
  // returned by the function.

  return state;
}
```

Den `context` parametern har följande egenskaper:

- `currentTime` som en sträng med formatet `yyyy-MM-dd'T'HH:mm:sszzz`
- `deviceId`, till exempel `Simulated.Chiller.123`
- `deviceModel`, till exempel `Chiller`

Den `state` parametern innehåller tillståndet för enheten som hanteras av tjänsten enhet simulering. Det här värdet är den `state` objektet som returneras av det föregående anropet till `main`.

I följande exempel visas en typisk implementering av den `main` metod för att hantera enhetens tillstånd som underhålls av tjänsten simulering:

```javascript
// Default state
var state = {
  online: true,
  temperature: 75.0,
  temperature_unit: "F",
  humidity: 70.0,
  humidity_unit: "%",
  pressure: 150.0,
  pressure_unit: "psig",
  simulation_state: "normal_pressure"
};

function restoreState(previousState) {
  // If the previous state is null, force a default state
  if (previousState !== undefined && previousState !== null) {
      state = previousState;
  } else {
      log("Using default state");
  }
}

function main(context, previousState, previousProperties) {

  restoreState(previousState);

  // Update state

  return state;
}
```

I följande exempel visas hur `main` metoden kan simulera telemetrivärden som varierar över tid:

```javascript
/**
 * Simple formula generating a random value
 * around the average and between min and max
 */
function vary(avg, percentage, min, max) {
  var value = avg * (1 + ((percentage / 100) * (2 * Math.random() - 1)));
  value = Math.max(value, min);
  value = Math.min(value, max);
  return value;
}


function main(context, previousState, previousProperties) {

    restoreState(previousState);

    // 75F +/- 5%,  Min 25F, Max 100F
    state.temperature = vary(75, 5, 25, 100);

    // 70% +/- 5%,  Min 2%, Max 99%
    state.humidity = vary(70, 5, 2, 99);

    log("Simulation state: " + state.simulation_state);
    if (state.simulation_state === "high_pressure") {
        // 250 psig +/- 25%,  Min 50 psig, Max 300 psig
        state.pressure = vary(250, 25, 50, 300);
    } else {
        // 150 psig +/- 10%,  Min 50 psig, Max 300 psig
        state.pressure = vary(150, 10, 50, 300);
    }

    return state;
}
```

Du kan visa den fullständiga [kylaggregat-01-state.js](https://github.com/Azure/device-simulation-dotnet/blob/master/Services/data/devicemodels/scripts/chiller-01-state.js) på GitHub.

## <a name="method-behavior"></a>Metoden beteende

Den [CloudToDeviceMethods](../../articles/iot-accelerators/iot-accelerators-remote-monitoring-device-schema.md#cloudtodevicemethods) delen av schemat för enhetsmodellen definierar de metoder som en simulerad enhet svarar på.

I följande exempel visas listan över metoder som stöds av en simulerad kylaggregat enhet:

```json
"CloudToDeviceMethods": {
  "Reboot": {
    "Type": "javascript",
    "Path": "Reboot-method.js"
  },
  "FirmwareUpdate": {
    "Type": "javascript",
    "Path": "FirmwareUpdate-method.js"
  },
  "EmergencyValveRelease": {
    "Type": "javascript",
    "Path": "EmergencyValveRelease-method.js"
  },
  "IncreasePressure": {
    "Type": "javascript",
    "Path": "IncreasePressure-method.js"
  }
}
```

Varje metod har en associerad JavaScript-fil som implementerar metodens beteende.

Tillståndet för den simulerade enheten, enligt definitionen i den `InitialState` avsnittet av schema, hålls kvar i minnet av tjänsten simulering. Tillståndsinformationen skickas som indata till den `main` funktion som definierats i JavaScript-fil när metoden anropas. Skriptet kan ändra tillståndet för den simulerade enheten.

Följande visar konturerna för en typisk `main` funktionen:

```javascript
function main(context, previousState, previousProperties) {

}
```

Den `context` parametern har följande egenskaper:

- `currentTime` som en sträng med formatet `yyyy-MM-dd'T'HH:mm:sszzz`
- `deviceId`, till exempel `Simulated.Chiller.123`
- `deviceModel`, till exempel `Chiller`

Den `state` parametern innehåller tillståndet för enheten som hanteras av tjänsten enhet simulering.

Den `properties` parametern innehåller egenskaperna för enheten som har skrivits som rapporterade egenskaper till enhetstvillingen i IoT Hub.

Det finns tre globala funktioner som du kan använda för att implementera metodens beteende:

- `updateState` att uppdatera tjänsten simulering tillstånd.
- `updateProperty` att uppdatera en enskild enhetsegenskap.
- `sleep` att pausa körning för att simulera en tidskrävande uppgift.

I följande exempel visas en förkortad version av den **IncreasePressure method.js** skript som används av de simulerade kylaggregat-enheterna:

```javascript
function main(context, previousState, previousProperties) {

    log("Starting 'Increase Pressure' method simulation (5 seconds)");

    // Pause the simulation and change the simulation mode so that the
    // temperature will fluctuate at ~250 when it resumes
    var state = {
        simulation_state: "high_pressure",
        CalculateRandomizedTelemetry: false
    };
    updateState(state);

    // Increase
    state.pressure = 210;
    updateState(state);
    log("Pressure increased to " + state.pressure);
    sleep(1000);

    // Increase
    state.pressure = 250;
    updateState(state);
    log("Pressure increased to " + state.pressure);
    sleep(1000);

    // Resume the simulation
    state.CalculateRandomizedTelemetry = true;
    updateState(state);

    log("'Increase Pressure' method simulation completed");
}
```

## <a name="debugging-script-files"></a>Felsökning skriptfiler

Det går inte att koppla en felsökare till Javascript-tolk som används av enheten simulering tjänsten kör tillstånd och metoden skript. Du kan dock logga information i loggen för tjänsten. Inbyggt `log()` funktionen kan du spara information för att spåra och felsöka körning av funktion.

Om det finns ett syntaxfel vilken tolk som misslyckas och skriver en `Jint.Runtime.JavaScriptException` post in i tjänsten.

Den [som kör tjänsten lokalt](https://github.com/Azure/device-simulation-dotnet#running-the-service-locally-eg-for-development-tasks) artikeln på GitHub visar hur du kör tjänsten enheten simuleringen lokalt. Kör tjänsten lokalt gör det enklare att felsöka dina simulerade enheter innan du distribuerar dem till molnet.

## <a name="next-steps"></a>Nästa steg

Den här artikeln beskrivs hur du definierar beteendet för dina egna anpassade simulerade enhetsmodell. Den här artikeln visar dig hur du:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Kontrollera tillståndet för en simulerad enhet
> * Definiera hur en simulerad enhet svarar på ett metodanrop från lösningen för fjärrövervakning
> * Felsök ditt skript

Nu när du har lärt dig hur du anger beteendet för en simulerad enhet, föreslagna nästa steg är att lära dig hur du [skapar en simulerad enhet](iot-accelerators-remote-monitoring-create-simulated-device.md).

Mer information för utvecklare om lösningen för fjärrövervakning, finns:

* [Referensguide för utvecklare](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
* [Felsökningsguide för utvecklare](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide)

<!-- Next tutorials in the sequence -->
