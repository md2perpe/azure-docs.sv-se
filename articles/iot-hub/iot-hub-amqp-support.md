---
title: Förstå Azure IoT Hub AMQP support | Microsoft Docs
description: 'Utvecklarguide – stöd för enheter som ansluter till IoT Hub enheten kund- och service-riktade slutpunkter med hjälp av AMQP-protokollet. Innehåller information om inbyggda stöd för AMQP i SDK: er för Azure IoT-enheter.'
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/30/2019
ms.author: robinsh
ms.openlocfilehash: e0c7b6aa9745beaf7a7d336e8308d12348bb274b
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67432615"
---
# <a name="communicate-with-your-iot-hub-by-using-the-amqp-protocol"></a>Kommunicera med IoT-hubben med hjälp av AMQP-protokollet

Stöds av Azure IoT Hub [OASIS avancerade AMQP Message Queuing Protocol () version 1.0](https://docs.oasis-open.org/amqp/core/v1.0/os/amqp-core-complete-v1.0-os.pdf) att leverera en mängd funktioner via enheten kund- och service-riktade slutpunkter. Det här dokumentet beskriver hur du använder AMQP-klienter att ansluta till en IoT-hubb använda IoT Hub-funktionen.

## <a name="service-client"></a>Klienten för tjänsten

### <a name="connect-and-authenticate-to-an-iot-hub-service-client"></a>Ansluta och autentisera till en IoT-hubb (service-klient)

Om du vill ansluta till en IoT hub med hjälp av AMQP, en klient kan använda den [anspråksbaserad säkerhet (CBS)](https://www.oasis-open.org/committees/download.php/60412/amqp-cbs-v1.0-wd03.doc) eller [enkel autentisering och säkerhet Layer (SASL) autentisering](https://en.wikipedia.org/wiki/Simple_Authentication_and_Security_Layer).

Följande krävs för tjänstklienten:

| Information | Värde |
|-------------|--------------|
| IoT hub-värdnamn | `<iot-hub-name>.azure-devices.net` |
| Nyckelnamn | `service` |
| Åtkomstnyckel | En primär eller sekundär nyckel som är associerad med tjänsten |
| Signatur för delad åtkomst | En tillfällig delad åtkomstsignatur i följande format: `SharedAccessSignature sig={signature-string}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`. Om du vill hämta koden för att generera den här signaturen, se [styra åtkomsten till IoT Hub](./iot-hub-devguide-security.md#security-token-structure).

I följande kod kodfragment används den [uAMQP biblioteket i Python](https://github.com/Azure/azure-uamqp-python) att ansluta till en IoT hub via en avsändare-länk.

```python
import uamqp
import urllib
import time

# Use generate_sas_token implementation available here: 
# https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security#security-token-structure
from helper import generate_sas_token

iot_hub_name = '<iot-hub-name>'
hostname = '{iot_hub_name}.azure-devices.net'.format(iot_hub_name=iot_hub_name)
policy_name = 'service'
access_key = '<primary-or-secondary-key>'
operation = '<operation-link-name>' # example: '/messages/devicebound'

username = '{policy_name}@sas.root.{iot_hub_name}'.format(iot_hub_name=iot_hub_name, policy_name=policy_name)
sas_token = generate_sas_token(hostname, access_key, policy_name)
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username), urllib.quote_plus(sas_token), hostname, operation)

# Create a send or receive client
send_client = uamqp.SendClient(uri, debug=True)
receive_client = uamqp.ReceiveClient(uri, debug=True)
```

### <a name="invoke-cloud-to-device-messages-service-client"></a>Anropa meddelanden från molnet till enheten (service-klient)

Mer information om moln till enhet-meddelande exchange mellan tjänsten och IoT-hubben och mellan enheten och IoT hub, se [skicka meddelanden från moln till enhet från IoT hub](iot-hub-devguide-messages-c2d.md). Tjänsten klienten använder två länkar att skicka meddelanden och få feedback om tidigare skickade meddelanden från enheter, enligt beskrivningen i följande tabell:

| Skapat av | Länktyp | Länk | Beskrivning |
|------------|-----------|-----------|-------------|
| Tjänst | Avsändaren länk | `/messages/devicebound` | Meddelanden från molnet till enheten som är avsedda för enheter som skickas till den här länken av tjänsten. Meddelanden som skickas via den här länken har sina `To` -egenskapen angetts till den målenheten mottagare länksökvägen `/devices/<deviceID>/messages/devicebound`. |
| Tjänst | Mottagare länk | `/messages/serviceBound/feedback` | Slutförande, avvisande och överlåtelse feedback-meddelanden som kommer från enheter som tas emot på den här länken av tjänsten. Mer information om meddelanden finns i [skicka meddelanden från moln till enhet från en IoT-hubb](./iot-hub-devguide-messages-c2d.md#message-feedback). |

Följande kodfragment visar hur du skapar ett moln-till-enhet-meddelande och skicka den till en enhet med hjälp av den [uAMQP biblioteket i Python](https://github.com/Azure/azure-uamqp-python).

```python
import uuid
# Create a message and set message property 'To' to the device-bound link on device
msg_id = str(uuid.uuid4())
msg_content = b"Message content goes here!"
device_id = '<device-id>'
to = '/devices/{device_id}/messages/devicebound'.format(device_id=device_id)
ack = 'full' # Alternative values are 'positive', 'negative', and 'none'
app_props = { 'iothub-ack': ack }
msg_props = uamqp.message.MessageProperties(message_id=msg_id, to=to)
msg = uamqp.Message(msg_content, properties=msg_props, application_properties=app_props)

# Send the message by using the send client that you created and connected to the IoT hub earlier
send_client.queue_message(msg)
results = send_client.send_all_messages()

# Close the client if it's not needed
send_client.close()
```

För att få feedback, skapar tjänstklienten du en mottagare-länk. Följande kodfragment visar hur du skapar en länk med hjälp av den [uAMQP biblioteket i Python](https://github.com/Azure/azure-uamqp-python):

```python
import json

operation = '/messages/serviceBound/feedback'

# ...
# Re-create the URI by using the preceding feedback path and authenticate it
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username), urllib.quote_plus(sas_token), hostname, operation)

receive_client = uamqp.ReceiveClient(uri, debug=True)
batch = receive_client.receive_message_batch(max_batch_size=10)
for msg in batch:
  print('received a message')
  # Check content_type in message property to identify feedback messages coming from device
  if msg.properties.content_type == 'application/vnd.microsoft.iothub.feedback.json':
    msg_body_raw = msg.get_data()
    msg_body_str = ''.join(msg_body_raw)
    msg_body = json.loads(msg_body_str)
    print(json.dumps(msg_body, indent=2))
    print('******************')
    for feedback in msg_body:
      print('feedback received')
      print('\tstatusCode: ' + str(feedback['statusCode']))
      print('\toriginalMessageId: ' + str(feedback['originalMessageId']))
      print('\tdeviceId: ' + str(feedback['deviceId']))
      print
  else:
    print('unknown message:', msg.properties.content_type)
```

I föregående kod visas ett meddelande om moln till enhet feedback har innehållstypen för *application/vnd.microsoft.iothub.feedback.json*. Du kan använda egenskaperna i meddelandetexten JSON som härleder leveransstatus av det ursprungliga meddelandet:

* Nyckeln `statusCode` i feedbacken har något av följande värden: *Lyckade*, *har upphört att gälla*, *DeliveryCountExceeded*, *avvisade*, eller *rensas*.

* Nyckeln `deviceId` i feedbacken har ID för målenheten.

* Nyckeln `originalMessageId` i feedbacken har ID för moln-till-enhet-meddelandet som skickades av tjänsten. Du kan använda den här leveransstatus för att korrelera feedback till meddelanden från molnet till enheten.

### <a name="receive-telemetry-messages-service-client"></a>Ta emot telemetrimeddelanden (service-klient)

Som standard lagrar IoT-hubben inmatade enheten telemetrimeddelanden i en inbyggd händelsehubb. Service-klienten kan använda AMQP-protokollet för att ta emot de lagrade händelserna.

För detta ändamål måste tjänstklienten först ansluta till IoT hub-slutpunkten och ta emot en adress för omdirigering till de inbyggda händelsehubbar. Service-klienten använder sedan den angivna adressen för att ansluta till inbyggda event hub.

I varje steg måste klienten finns i följande uppgifter:

* Giltigt service autentiseringsuppgifter (signaturtoken för tjänsten delad åtkomst).

* En välutformat sökväg till den grupp konsument-partition som den har för avsikt att hämta meddelanden från. För en viss konsument grupp och partitions-ID, sökvägen har följande format: `/messages/events/ConsumerGroups/<consumer_group>/Partitions/<partition_id>` (förinställd konsumentgrupp är `$Default`).

* Ett valfritt filtrering predikat att utse en startpunkt i partitionen. Det här predikatet kan vara i form av en sekvens nummer, offset eller köas tidsstämpel.

I följande kod kodfragment används den [uAMQP biblioteket i Python](https://github.com/Azure/azure-uamqp-python) att demonstrera föregående steg:

```python
import json
import uamqp
import urllib
import time

# Use the generate_sas_token implementation that's available here: https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security#security-token-structure
from helper import generate_sas_token

iot_hub_name = '<iot-hub-name>'
hostname = '{iot_hub_name}.azure-devices.net'.format(iot_hub_name=iot_hub_name)
policy_name = 'service'
access_key = '<primary-or-secondary-key>'
operation = '/messages/events/ConsumerGroups/{consumer_group}/Partitions/{p_id}'.format(consumer_group='$Default', p_id=0)

username = '{policy_name}@sas.root.{iot_hub_name}'.format(policy_name=policy_name, iot_hub_name=iot_hub_name)
sas_token = generate_sas_token(hostname, access_key, policy_name)
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username), urllib.quote_plus(sas_token), hostname, operation)

# Optional filtering predicates can be specified by using endpoint_filter
# Valid predicates include:
# - amqp.annotation.x-opt-sequence-number
# - amqp.annotation.x-opt-offset
# - amqp.annotation.x-opt-enqueued-time
# Set endpoint_filter variable to None if no filter is needed
endpoint_filter = b'amqp.annotation.x-opt-sequence-number > 2995'

# Helper function to set the filtering predicate on the source URI
def set_endpoint_filter(uri, endpoint_filter=''):
  source_uri = uamqp.address.Source(uri)
  source_uri.set_filter(endpoint_filter)
  return source_uri

receive_client = uamqp.ReceiveClient(set_endpoint_filter(uri, endpoint_filter), debug=True)
try:
  batch = receive_client.receive_message_batch(max_batch_size=5)
except uamqp.errors.LinkRedirect as redirect:
  # Once a redirect error is received, close the original client and recreate a new one to the re-directed address
  receive_client.close()

  sas_auth = uamqp.authentication.SASTokenAuth.from_shared_access_key(redirect.address, policy_name, access_key)
  receive_client = uamqp.ReceiveClient(set_endpoint_filter(redirect.address, endpoint_filter), auth=sas_auth, debug=True)

# Start receiving messages in batches
batch = receive_client.receive_message_batch(max_batch_size=5)
for msg in batch:
  print('*** received a message ***')
  print(''.join(msg.get_data()))
  print('\t: ' + str(msg.annotations['x-opt-sequence-number']))
  print('\t: ' + str(msg.annotations['x-opt-offset']))
  print('\t: ' + str(msg.annotations['x-opt-enqueued-time']))
```

För en viss enhets-ID använder IoT-hubben en hash av enhets-ID för att avgöra vilken partition som ska lagra meddelanden i. I föregående kodfragment visar hur händelser tas emot från en enda sådana partition. Observera dock att ett typiskt program ofta behöver hämta händelser som lagras i alla händelsenavspartitioner.

## <a name="device-client"></a>Enhetsklient

### <a name="connect-and-authenticate-to-an-iot-hub-device-client"></a>Ansluta och autentisera till en IoT-hubb (enhetsklienten)

En enhet kan använda för att ansluta till en IoT hub med hjälp av AMQP [anspråksbaserad säkerhet (CBS)](https://www.oasis-open.org/committees/download.php/60412/amqp-cbs-v1.0-wd03.doc) eller [enkel autentisering och säkerhet Layer (SASL)](https://en.wikipedia.org/wiki/Simple_Authentication_and_Security_Layer) autentisering.

Följande krävs för enhetsklienten:

| Information | Värde |
|-------------|--------------|
| IoT hub-värdnamn | `<iot-hub-name>.azure-devices.net` |
| Åtkomstnyckel | En primär eller sekundär nyckel som är associerat med enheten |
| Signatur för delad åtkomst | En tillfällig delad åtkomstsignatur i följande format: `SharedAccessSignature sig={signature-string}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`. Om du vill hämta koden för att generera den här signaturen, se [styra åtkomsten till IoT Hub](./iot-hub-devguide-security.md#security-token-structure).

I följande kod kodfragment används den [uAMQP biblioteket i Python](https://github.com/Azure/azure-uamqp-python) att ansluta till en IoT hub via en avsändare-länk.

```python
import uamqp
import urllib
import uuid

# Use generate_sas_token implementation available here: 
# https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security#security-token-structure
from helper import generate_sas_token

iot_hub_name = '<iot-hub-name>'
hostname = '{iot_hub_name}.azure-devices.net'.format(iot_hub_name=iot_hub_name)
device_id = '<device-id>'
access_key = '<primary-or-secondary-key>'
username = '{device_id}@sas.{iot_hub_name}'.format(device_id=device_id, iot_hub_name=iot_hub_name)
sas_token = generate_sas_token('{hostname}/devices/{device_id}'.format(hostname=hostname, device_id=device_id), access_key, None)

operation = '<operation-link-name>' # e.g., '/devices/{device_id}/messages/devicebound'
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username), urllib.quote_plus(sas_token), hostname, operation)

receive_client = uamqp.ReceiveClient(uri, debug=True)
send_client = uamqp.SendClient(uri, debug=True)
```

Följande länksökvägar stöds som åtgärder:

| Skapat av | Länktyp | Länk | Beskrivning |
|------------|-----------|-----------|-------------|
| Enheter | Mottagare länk | `/devices/<deviceID>/messages/devicebound` | Meddelanden från molnet till enheten som är avsedda för enheter som tas emot på den här länken av varje målenhet. |
| Enheter | Avsändaren länk | `/devices/<deviceID>messages/events` | Enhet-till-moln-meddelanden som skickas från en enhet skickas via den här länken. |
| Enheter | Avsändaren länk | `/messages/serviceBound/feedback` | Feedback för moln till enhet-meddelande skickas till tjänsten via den här länken av enheter. |

### <a name="receive-cloud-to-device-commands-device-client"></a>Tar emot kommandon moln till enhet (enhetsklienten)

Moln-till-enhet-kommandon som skickas till enheter som tas emot på en `/devices/<deviceID>/messages/devicebound` länk. Enheter kan ta emot dessa meddelanden i batchar och använda data meddelandenyttolast, meddelandeegenskaper, kommentarer eller programegenskaper i meddelandet vid behov.

I följande kod kodfragment används den [uAMQP biblioteket i Python](https://github.com/Azure/azure-uamqp-python)) att ta emot meddelanden från moln till enhet av en enhet.

```python
# ...
# Create a receive client for the cloud-to-device receive link on the device
operation = '/devices/{device_id}/messages/devicebound'.format(device_id=device_id)
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username), urllib.quote_plus(sas_token), hostname, operation)

receive_client = uamqp.ReceiveClient(uri, debug=True)
while True:
  batch = receive_client.receive_message_batch(max_batch_size=5)
  for msg in batch:
    print('*** received a message ***')
    print(''.join(msg.get_data()))

    # Property 'to' is set to: '/devices/device1/messages/devicebound',
    print('\tto:                     ' + str(msg.properties.to))

    # Property 'message_id' is set to value provided by the service
    print('\tmessage_id:             ' + str(msg.properties.message_id))

    # Other properties are present if they were provided by the service
    print('\tcreation_time:          ' + str(msg.properties.creation_time))
    print('\tcorrelation_id:         ' + str(msg.properties.correlation_id))
    print('\tcontent_type:           ' + str(msg.properties.content_type))
    print('\treply_to_group_id:      ' + str(msg.properties.reply_to_group_id))
    print('\tsubject:                ' + str(msg.properties.subject))
    print('\tuser_id:                ' + str(msg.properties.user_id))
    print('\tgroup_sequence:         ' + str(msg.properties.group_sequence))
    print('\tcontent_encoding:       ' + str(msg.properties.content_encoding))
    print('\treply_to:               ' + str(msg.properties.reply_to))
    print('\tabsolute_expiry_time:   ' + str(msg.properties.absolute_expiry_time))
    print('\tgroup_id:               ' + str(msg.properties.group_id))

    # Message sequence number in the built-in event hub
    print('\tx-opt-sequence-number:  ' + str(msg.annotations['x-opt-sequence-number']))
```

### <a name="send-telemetry-messages-device-client"></a>Skicka telemetrimeddelanden (enhetsklienten)

Du kan också skicka telemetrimeddelanden från en enhet med hjälp av AMQP. Enheten kan du ge en ordlista med egenskaper för program eller skicka meddelanden till olika egenskaper, till exempel meddelande-ID.

I följande kod kodfragment används den [uAMQP biblioteket i Python](https://github.com/Azure/azure-uamqp-python) att skicka meddelanden från enhet till moln från en enhet.

```python
# ...
# Create a send client for the device-to-cloud send link on the device
operation = '/devices/{device_id}/messages/events'.format(device_id=device_id)
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username), urllib.quote_plus(sas_token), hostname, operation)

send_client = uamqp.SendClient(uri, debug=True)

# Set any of the applicable message properties
msg_props = uamqp.message.MessageProperties()
msg_props.message_id = str(uuid.uuid4())
msg_props.creation_time = None
msg_props.correlation_id = None
msg_props.content_type = None
msg_props.reply_to_group_id = None
msg_props.subject = None
msg_props.user_id = None
msg_props.group_sequence = None
msg_props.to = None
msg_props.content_encoding = None
msg_props.reply_to = None
msg_props.absolute_expiry_time = None
msg_props.group_id = None

# Application properties in the message (if any)
application_properties = { "app_property_key": "app_property_value" }

# Create message
msg_data = b"Your message payload goes here"
message = uamqp.Message(msg_data, properties=msg_props, application_properties=application_properties)

send_client.queue_message(message)
results = send_client.send_all_messages()

for result in results:
    if result == uamqp.constants.MessageState.SendFailed:
        print result
```

## <a name="additional-notes"></a>Ytterligare information

* AMQP-anslutningar kan avbrytas på grund av ett nätverk glapp för eller förnyandet av autentiseringen token (genereras i koden). Tjänstklienten måste hantera dessa fall och återupprätta anslutningen och länkar, om det behövs. Om en autentiseringstoken upphör att gälla undvika klienten en listmeny anslutningen genom att proaktivt förnya token innan det upphör att gälla.

* Klienten måste ibland att kunna hantera länken omdirigeringar korrekt. Information om en sådan åtgärd finns i dokumentationen för din AMQP-klient.

## <a name="next-steps"></a>Nästa steg

Läs mer om AMQP-protokollet i den [AMQP v1.0 specifikationen](https://www.amqp.org/sites/amqp.org/files/amqp.pdf).

Mer information om IoT Hub-meddelanden finns:

* [Meddelanden från moln till enhet](./iot-hub-devguide-messages-c2d.md)
* [Stöd för ytterligare protokoll](iot-hub-protocol-gateway.md)
* [Stöd för Message Queuing telemetri Transport (MQTT) protokollet](./iot-hub-mqtt-support.md)