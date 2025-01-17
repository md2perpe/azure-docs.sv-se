---
title: Förstå Azure IoT Hub MQTT-support | Microsoft Docs
description: 'Utvecklarguide – stöd för enheter som ansluter till en IoT Hub device-slutpunkter med hjälp av MQTT-protokollet. Innehåller information om inbyggda MQTT stöd i SDK: er för Azure IoT-enheter.'
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/12/2018
ms.author: robinsh
ms.openlocfilehash: 238627f5cb57a614d5f82f0eeba1ea6b4e32f458
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67445484"
---
# <a name="communicate-with-your-iot-hub-using-the-mqtt-protocol"></a>Kommunicera med IoT-hubben med hjälp av MQTT-protokollet

IoT Hub kan enheter att kommunicera med IoT Hub device-slutpunkter med hjälp av:

* [MQTT v3.1.1](https://mqtt.org/) på port 8883
* MQTT v3.1.1 via WebSocket på port 443.

IoT Hub är inte en fullständig MQTT broker och stöder inte det beteende som angetts i MQTT v3.1.1-standard. Den här artikeln beskrivs hur enheter kan använda stöds MQTT beteenden för att kommunicera med IoT Hub.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

All kommunikation mellan enheten med IoT Hub måste skyddas med hjälp av TLS/SSL. Därför stöder IoT Hub inte ej säkra anslutningar via port 1883.

## <a name="connecting-to-iot-hub"></a>Ansluter till IoT Hub

En enhet kan använda MQTT-protokollet för att ansluta till en IoT-hubb med någon av följande alternativ.

* Bibliotek i den [Azure IoT SDK: er](https://github.com/Azure/azure-iot-sdks).
* Av MQTT-protokollet direkt.

## <a name="using-the-device-sdks"></a>Med enhets-SDK: er

[SDK: er för enhetens](https://github.com/Azure/azure-iot-sdks) som har stöd för MQTT-protokollet är tillgängliga för Java, Node.js, C, C#, och Python. SDK: er för enheter använda standard IoT Hub-anslutningssträngen för att upprätta en anslutning till en IoT-hubb. Om du vill använda MQTT-protokollet måste klienten protocol-parametern anges till **MQTT**. Som standard enheten SDK: er ansluta till en IoT-hubb med den **CleanSession** -flaggan inställd på **0** och använda **QoS 1** för utbyte av meddelanden med IoT hub.

När en enhet är ansluten till en IoT-hubb, SDK: er för enheter tillhandahåller metoder som att enheten kan utbyta meddelanden med en IoT-hubb.

I följande tabell innehåller länkar till kodexempel för varje språk som stöds och anger parametern för att använda för att upprätta en anslutning till IoT-hubben med hjälp av MQTT-protokollet.

| Språk | Parametern Protocol |
| --- | --- |
| [Node.js](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device.js) |azure-iot-device-mqtt |
| [Java](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-samples/send-receive-sample/src/main/java/samples/com/microsoft/azure/sdk/iot/SendReceive.java) |IotHubClientProtocol.MQTT |
| [C](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iothub_client_sample_mqtt_dm) |MQTT_Protocol |
| [C#](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/iothub/device/samples) |TransportType.Mqtt |
| [Python](https://github.com/Azure/azure-iot-sdk-python/tree/master/device/samples) |IoTHubTransportProvider.MQTT |

### <a name="migrating-a-device-app-from-amqp-to-mqtt"></a>Migrera en enhetsapp från AMQP till MQTT

Om du använder den [SDK: er enheter](https://github.com/Azure/azure-iot-sdks), växla från att använda AMQP till MQTT innebär att du ändrar protokoll-parametern i initieringen av klient som nämnts tidigare.

När du gör detta, se till att kontrollera följande:

* AMQP returnerar fel för många villkor, medan MQTT avslutar anslutningen. Din undantagshantering logic kan därför kräva några ändringar.

* MQTT stöder inte den *avvisa* åtgärder när du tar emot [meddelanden från moln till enhet](iot-hub-devguide-messaging.md). Om din backend-app behöver på ett svar från enhetsappen, bör du använda [direkta metoder](iot-hub-devguide-direct-methods.md).

## <a name="using-the-mqtt-protocol-directly-as-a-device"></a>Med hjälp av MQTT-protokollet direkt (som en enhet)

Om en enhet inte kan använda SDK: er för enheter, kan det fortfarande ansluta till slutpunkterna offentliga enheter med hjälp av MQTT-protokollet på port 8883. I den **CONNECT** paket, enheten ska använda följande värden:

* För den **ClientId** fältet, Använd den **deviceId**.

* För den **användarnamn** fältet, Använd `{iothubhostname}/{device_id}/?api-version=2018-06-30`, där `{iothubhostname}` är fullständig CName för IoT-hubben.

    Om namnet på IoT-hubben är till exempel **contoso.azure-devices.net** och om namnet på din enhet är **MyDevice01**, fullständiga **användarnamn** fältet ska innehålla:

    `contoso.azure-devices.net/MyDevice01/?api-version=2018-06-30`

* För den **lösenord** fältet, använda en SAS-token. Formatet för SAS-token är desamma som för HTTPS- och AMQP-protokoll:

  `SharedAccessSignature sig={signature-string}&se={expiry}&sr={URL-encoded-resourceURI}`

  > [!NOTE]
  > Om du använder X.509-certifikatautentisering, SAS-token lösenord krävs inte. Mer information finns i [Konfigurera X.509 säkerheten i Azure IoT Hub](iot-hub-security-x509-get-started.md)

  Mer information om hur du genererar SAS-token finns i avsnittet enheten i [med hjälp av IoT Hub säkerhetstoken](iot-hub-devguide-security.md#use-sas-tokens-in-a-device-app).

  När du testar kan du kan också använda plattformsoberoende [Azure IoT-verktyg för Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) eller [Device Explorer](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/tools/DeviceExplorer) verktyg för att snabbt skapa en SAS-token som du kan kopiera och klistra in i din egen kod:

För Azure IoT-verktyg:

1. Expandera den **AZURE IOT HUB-enheter** fliken i det nedre vänstra hörnet av Visual Studio Code.
  
2. Högerklicka på enheten och välj **generera SAS-Token för enheten**.
  
3. Ange **förfallotid** och tryck på ”RETUR”.
  
4. SAS-token skapas och kopieras till Urklipp.

För Device Explorer:

1. Gå till den **Management** fliken **Device Explorer**.

2. Klicka på **SAS-Token** (överst till höger).

3. På **SASTokenForm**, Välj din enhet i den **DeviceID** nedrullningsbar listruta. Ange din **TTL**.

4. Klicka på **generera** att skapa din token.

   SAS-token som genereras har följande struktur:

   `HostName={your hub name}.azure-devices.net;DeviceId=javadevice;SharedAccessSignature=SharedAccessSignature sr={your hub name}.azure-devices.net%2Fdevices%2FMyDevice01%2Fapi-version%3D2016-11-14&sig=vSgHBMUG.....Ntg%3d&se=1456481802`

   En del av denna token ska användas som den **lösenord** fält som du vill ansluta med hjälp av MQTT är:

   `SharedAccessSignature sr={your hub name}.azure-devices.net%2Fdevices%2FMyDevice01%2Fapi-version%3D2016-11-14&sig=vSgHBMUG.....Ntg%3d&se=1456481802`

MQTT ansluta och koppla från paket, IoT Hub skickar en händelse på den **Operations Monitoring** kanal. Den här händelsen har ytterligare information som kan hjälpa dig att felsöka anslutningsproblem.

App för enheter kan ange en **kommer** meddelande i den **CONNECT** paket. Enhetsappen ska använda `devices/{device_id}/messages/events/` eller `devices/{device_id}/messages/events/{property_bag}` som den **kommer** ämnesnamn definiera **kommer** meddelanden ska vidarebefordras som ett telemetri-meddelande. I det här fallet om nätverksanslutningen är stängd, men en **DISCONNECT** paket togs inte emot tidigare från enheten, IoT Hub och skickar sedan den **kommer** meddelandet som anges i den **CONNECT** paketet till telemetri-kanalen. Telemetri kanalen kan vara antingen standard **händelser** slutpunkt eller en anpassad slutpunkt som definierats av IoT Hub routning. Meddelandet har den **iothub-MessageType** egenskapen med värdet **kommer** tilldelade till den.

## <a name="using-the-mqtt-protocol-directly-as-a-module"></a>Med hjälp av MQTT-protokollet direkt (som en modul)

Ansluta till IoT Hub via MQTT med hjälp av en modul-identitet som genereras liknar enheten (beskrivs [ovan](#using-the-mqtt-protocol-directly-as-a-device)) men du måste använda följande:

* Ange klient-id `{device_id}/{module_id}`.

* Om autentisering med användarnamn och lösenord, anger användarnamnet `<hubname>.azure-devices.net/{device_id}/{module_id}/?api-version=2018-06-30` och använda SAS-token som är associerade med identiteten som modulen som lösenord.

* Använd `devices/{device_id}/modules/{module_id}/messages/events/` som ämnet för att publicera telemetri.

* Använd `devices/{device_id}/modules/{module_id}/messages/events/` som kommer ämnet.

* Twin GET och PATCH ämnen är identiska för moduler och enheter.

* Avsnittet twin status är identisk för moduler och enheter.

### <a name="tlsssl-configuration"></a>TLS/SSL-konfiguration

Du använder MQTT direkt, protokollet klienten *måste* ansluta via TLS/SSL. Försöker hoppa över det här steget misslyckas med fel.

För att upprätta en anslutning med TLS, kan du behöva ladda ned och referera till DigiCert Baltimore-rotcertifikat. Det här certifikatet är det som används i Azure för att säkra anslutningen. Du hittar det här certifikatet i den [Azure-iot-sdk-c](https://github.com/Azure/azure-iot-sdk-c/blob/master/certs/certs.c) lagringsplats. Mer information om dessa certifikat kan hittas på [Digicert webbplats](https://www.digicert.com/digicert-root-certificates.htm).

Ett exempel på hur du implementerar det här med hjälp av Python-versionen av den [Paho MQTT biblioteket](https://pypi.python.org/pypi/paho-mqtt) av Eclipse Foundation kan se ut så här.

Installera först Paho-biblioteket från miljön kommandoraden:

```cmd/sh
pip install paho-mqtt
```

Sedan implementera klienten i ett Python-skript. Ersätt platshållarna på följande sätt:

* `<local path to digicert.cer>` är sökvägen till en lokal fil som innehåller DigiCert Baltimore-rotcertifikat. Du kan skapa den här filen genom att kopiera certifikatinformationen från [certs.c](https://github.com/Azure/azure-iot-sdk-c/blob/master/certs/certs.c) i Azure IoT SDK för C. raderna `-----BEGIN CERTIFICATE-----` och `-----END CERTIFICATE-----`, ta bort den `"` som hämtas i början och slutet av varje rad och ta bort den `\r\n` tecken i slutet av varje rad.

* `<device id from device registry>` är ID för en enhet som du lade till din IoT-hubb.

* `<generated SAS token>` är en SAS-token för den enhet som skapats enligt beskrivningen tidigare i den här artikeln.

* `<iot hub name>` namnet på din IoT-hubb.

```python
from paho.mqtt import client as mqtt
import ssl

path_to_root_cert = "<local path to digicert.cer>"
device_id = "<device id from device registry>"
sas_token = "<generated SAS token>"
iot_hub_name = "<iot hub name>"

def on_connect(client, userdata, flags, rc):
  print ("Device connected with result code: " + str(rc))
def on_disconnect(client, userdata, rc):
  print ("Device disconnected with result code: " + str(rc))
def on_publish(client, userdata, mid):
  print ("Device sent message")

client = mqtt.Client(client_id=device_id, protocol=mqtt.MQTTv311)

client.on_connect = on_connect
client.on_disconnect = on_disconnect
client.on_publish = on_publish

client.username_pw_set(username=iot_hub_name+".azure-devices.net/" + device_id + "/?api-version=2018-06-30", password=sas_token)

client.tls_set(ca_certs=path_to_root_cert, certfile=None, keyfile=None, cert_reqs=ssl.CERT_REQUIRED, tls_version=ssl.PROTOCOL_TLSv1, ciphers=None)
client.tls_insecure_set(False)

client.connect(iot_hub_name+".azure-devices.net", port=8883)

client.publish("devices/" + device_id + "/messages/events/", "{id=123}", qos=1)
client.loop_forever()
```

### <a name="sending-device-to-cloud-messages"></a>Skicka meddelanden från enheten till molnet

När du har gjort en lyckad anslutning, en enhet kan skicka meddelanden till IoT Hub med hjälp av `devices/{device_id}/messages/events/` eller `devices/{device_id}/messages/events/{property_bag}` som en **ämnesnamn**. Den `{property_bag}` elementet gör att enheten skickar meddelanden med ytterligare egenskaper i en url-kodat format. Exempel:

```text
RFC 2396-encoded(<PropertyName1>)=RFC 2396-encoded(<PropertyValue1>)&RFC 2396-encoded(<PropertyName2>)=RFC 2396-encoded(<PropertyValue2>)…
```

> [!NOTE]
> Detta `{property_bag}` -elementet använder samma kodning som frågesträngar i HTTPS-protokollet.

Här följer en lista med IoT Hub implementeringsspecifikt beteenden:

* IoT Hub har inte stöd för QoS 2 meddelanden. Om en enhetsapp publicerar ett meddelande med **QoS 2**, IoT Hub stängs nätverksanslutningen.

* Behåll meddelanden sparas inte i IoT Hub. Om en enhet skickar ett meddelande med den **Behåll** -flaggan inställd på 1, IoT Hub lägger till den **x-opt-behålla** programegenskap i meddelandet. I det här fallet i stället för att spara Behåll meddelandet, skickar IoT Hub den till backend-app.

* IoT Hub stöder endast en aktiv MQTT-anslutning per enhet. Alla nya MQTT-anslutningar för samma enhets-ID gör IoT Hub för att ta bort den befintliga anslutningen.

Mer information finns i [meddelanden utvecklarhandboken](iot-hub-devguide-messaging.md).

### <a name="receiving-cloud-to-device-messages"></a>Ta emot meddelanden från moln till enhet

Om du vill ta emot meddelanden från IoT Hub, en enhet ska prenumerera med `devices/{device_id}/messages/devicebound/#` som en **avsnittet Filter**. Med flera nivåer jokertecknet `#` i avsnittet Filter används endast för att tillåta att enheten tar emot ytterligare egenskaper i ämnesnamnet. IoT Hub tillåter inte användningen av den `#` eller `?` jokertecken för filtrering av underavsnitt. Eftersom IoT Hub inte är en asynkron för allmänna pub-sub-meddelandetjänst, stöder endast dokumenterade avsnittsnamn och avsnittet filter.

Enheten får inte några meddelanden från IoT Hub, tills den har slutat prenumerera på dess enhetsspecifik slutpunkt, representeras av den `devices/{device_id}/messages/devicebound/#` avsnittet filter. När en prenumeration har upprättats kan enheten tar emot moln-till-enhet-meddelanden som skickades till den efter tiden för prenumerationen. Om enheten ansluter med **CleanSession** -flaggan inställd på **0**, prenumerationen bevaras mellan olika sessioner. I det här fallet nästa gång enheten ansluter med **CleanSession 0** den tar emot eventuella utestående meddelanden som skickas till den under frånkoppling. Om enheten använder **CleanSession** -flaggan inställd på **1** men det tar inte emot meddelanden från IoT Hub tills den prenumererar på dess enhetsslutpunkt.

IoT-hubb levererar meddelanden med den **ämnesnamn** `devices/{device_id}/messages/devicebound/`, eller `devices/{device_id}/messages/devicebound/{property_bag}` när det finns meddelandeegenskaper. `{property_bag}` innehåller url-kodade nyckel/värde-par för meddelandeegenskaper. Endast egenskaper för program och användare inställbar Systemegenskaper (till exempel **messageId** eller **correlationId**) som ingår i egenskapsuppsättningen. System egenskapsnamn har prefixet **$** , programegenskaper använder ursprungliga egenskapsnamnet med inget prefix.

När en app för enheter som prenumererar på ett ämne med **QoS 2**, IoT Hub ger högsta QoS nivå 1 i den **SUBACK** paket. Efter det levererar IoT-hubb meddelanden till enheten med hjälp av QoS-1.

### <a name="retrieving-a-device-twins-properties"></a>Hämtning av en enhetstvilling egenskaper

Först måste en enhet prenumererar på `$iothub/twin/res/#`, för att få åtgärdens svar. Sedan skickar den ett tomt meddelande till ämnet `$iothub/twin/GET/?$rid={request id}`, med ett värde som fylls i automatiskt för **fråge-ID**. Tjänsten skickar sedan ett svarsmeddelande som innehåller dubbla enhetsdata på avsnittet `$iothub/twin/res/{status}/?$rid={request id}`, med samma **fråge-ID** som begäran.

ID för förfrågan kan vara något giltigt värde för ett egenskapsvärde för meddelande enligt [IoT Hub messaging utvecklarhandboken](iot-hub-devguide-messaging.md), och status har verifierats som ett heltal.

Svarstexten innehåller egenskapsavsnittet i enhetstvillingen, som visas i exemplet nedan svaret:

```json
{
    "desired": {
        "telemetrySendFrequency": "5m",
        "$version": 12
    },
    "reported": {
        "telemetrySendFrequency": "5m",
        "batteryLevel": 55,
        "$version": 123
    }
}
```

De möjliga statuskoder är:

|Status | Beskrivning |
| ----- | ----------- |
| 204 | Lyckades (inget innehåll returneras) |
| 429 | För många begäranden (begränsad), enligt [IoT Hub-begränsning](iot-hub-devguide-quotas-throttling.md) |
| 5** | Serverfel |

Mer information finns i [utvecklarguide för Device twins](iot-hub-devguide-device-twins.md).

### <a name="update-device-twins-reported-properties"></a>Uppdatera enhetstvillingens rapporterade egenskaper

Om du vill uppdatera rapporterade egenskaper skickar enheten en begäran till IoT Hub via en publikation över ett avsedda MQTT-ämne. När begäran bearbetades, svarar IoT Hub lyckad eller misslyckad status för uppdateringsåtgärden via en publikation till ett annat avsnitt. Det här avsnittet går att prenumerera av enheten för att kunna meddela om resultatet av begäran om uppdatering av dess enhetstvilling. För att implementera den här typen av begäranden och svar i MQTT, vi utnyttja begreppet id för förfrågan (`$rid`) ingår från början i enheten i dess begäran om uppdatering. Den här begäran-id ingår också i svaret från IoT Hub för att tillåta att enheten att korrelera svaret till viss tidigare begäran.

Följande anvisningar beskriver hur en enhet uppdaterar rapporterade egenskaper i enhetstvillingen i IoT Hub:

1. En enhet måste först prenumerera på den `$iothub/twin/res/#` avsnittet om du vill ta emot åtgärdens svar från IoT Hub.

2. En enhet skickar ett meddelande som innehåller tvillinguppdatering enheten till den `$iothub/twin/PATCH/properties/reported/?$rid={request id}` avsnittet. Det här meddelandet innehåller en **fråge-ID** värde.

3. Tjänsten skickar sedan ett svarsmeddelande som innehåller det nya ETag-värdet för samlingen rapporterade egenskaper på avsnittet `$iothub/twin/res/{status}/?$rid={request id}`. Den här svarsmeddelande använder samma **fråge-ID** som begäran.

Meddelandetexten begäran innehåller ett JSON-dokument som innehåller nya värden för rapporterade egenskaper. Varje medlem i JSON-dokumentet uppdaterar eller lägga till motsvarande medlem i den enhetstvillingen dokumentet. En medlem som har angetts till `null`, tar bort medlemmen från objektet. Exempel:

```json
{
    "telemetrySendFrequency": "35m",
    "batteryLevel": 60
}
```

De möjliga statuskoder är:

|Status | Beskrivning |
| ----- | ----------- |
| 200 | Klart |
| 400 | Felaktig begäran. Felaktig JSON |
| 429 | För många begäranden (begränsad), enligt [IoT Hub-begränsning](iot-hub-devguide-quotas-throttling.md) |
| 5** | Serverfel |

Python kodfragmentet nedan visar läsningen rapporterade egenskaper uppdateringsprocessen över MQTT (med Paho MQTT-klienten):

```python
from paho.mqtt import client as mqtt

# authenticate the client with IoT Hub (not shown here)

client.subscribe("$iothub/twin/res/#")
rid = "1"
twin_reported_property_patch = "{\"firmware_version\": \"v1.1\"}"
client.publish("$iothub/twin/PATCH/properties/reported/?$rid=" + rid, twin_reported_property_patch, qos=0)
```

Vid en lyckad distribution för tvilling rapporterade egenskaper uppdateringsåtgärden ovan, publikationen meddelandet från IoT Hub har följande avsnitt: `$iothub/twin/res/204/?$rid=1&$version=6`, där `204` är statuskod som visar att det lyckades, `$rid=1` motsvarar begäran-ID tillhandahålls av enheten i koden, och `$version` motsvarar versionen av avsnittet rapporterade egenskaper för enhetstvillingar efter uppdateringen.

Mer information finns i [utvecklarguide för Device twins](iot-hub-devguide-device-twins.md).

### <a name="receiving-desired-properties-update-notifications"></a>Meddelanden om uppdateringar mottagande önskade egenskaper

När en enhet är ansluten, IoT Hub skickar meddelanden till ämnet `$iothub/twin/PATCH/properties/desired/?$version={new version}`, som innehåller innehållet i uppdateringen utförs av lösningens serverdel. Exempel:

```json
{
    "telemetrySendFrequency": "5m",
    "route": null,
    "$version": 8
}
```

För egenskapen uppdateringar `null` värden innebär att JSON-Objektmedlem tas bort. Observera också att `$version` anger den nya versionen av avsnittet önskade egenskaper i läsningen.

> [!IMPORTANT]
> IoT Hub genererar ändringsmeddelanden endast när enheter är anslutna. Se till att implementera den [enheten återanslutning flow](iot-hub-devguide-device-twins.md#device-reconnection-flow) att hålla önskade egenskaper synkroniseras mellan IoT Hub och app för enheter.

Mer information finns i [utvecklarguide för Device twins](iot-hub-devguide-device-twins.md).

### <a name="respond-to-a-direct-method"></a>Svara på en direkt metod

Först måste en enhet har prenumerera `$iothub/methods/POST/#`. IoT Hub skickar metodbegäranden till ämnet `$iothub/methods/POST/{method name}/?$rid={request id}`, med en giltig JSON eller en brödtext.

För att svara, enheten skickar ett meddelande med en giltig JSON eller brödtext till ämnet `$iothub/methods/res/{status}/?$rid={request id}`. I det här meddelandet i **fråge-ID** måste matcha det i meddelandet med begäran och **status** måste vara ett heltal.

Mer information finns i [direkta metoden utvecklarguide](iot-hub-devguide-direct-methods.md).

### <a name="additional-considerations"></a>Annat som är bra att tänka på

Som en sista överväganden om du vill anpassa beteendet MQTT-protokollet på sidan moln bör du granska den [Azure IoT-protokollgatewayen](iot-hub-protocol-gateway.md). Det här programmet kan du distribuera en gateway med höga prestanda anpassade protokoll som har ett gränssnitt direkt med IoT Hub. Azure IoT-protokollgatewayen kan du anpassa protokollet som enheten ska hantera brownfield MQTT distributioner eller andra anpassade protokoll. Den här metoden kräver dock att du kör och driva en anpassade protokoll-gateway.

## <a name="next-steps"></a>Nästa steg

Läs mer om MQTT-protokollet i den [MQTT dokumentation](https://mqtt.org/documentation).

Mer information om hur du planerar distributionen av IoT Hub finns:

* [Azure Certified for IoT-enhetskatalog](https://catalog.azureiotsolutions.com/)
* [Stöd för ytterligare protokoll](iot-hub-protocol-gateway.md)
* [Jämför med Event Hubs](iot-hub-compare-event-hubs.md)
* [Skalning, HA och DR](iot-hub-scaling.md)

Om du vill fortsätta för att utforska funktionerna för IoT Hub, se:

* [Utvecklarhandboken för IoT Hub](iot-hub-devguide.md)
* [Distribuera AI till gränsenheter med Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
