---
title: Ansluta underordnade enheter – Azure IoT Edge | Microsoft Docs
description: Så här konfigurerar du nedströms eller lövenheter att ansluta till Azure IoT Edge gateway-enheter.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/07/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 7a66355ca1a0c9c2c144f04cd944efe22467d3ae
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67058506"
---
# <a name="connect-a-downstream-device-to-an-azure-iot-edge-gateway"></a>En underordnad ansluts till en Azure IoT Edge-gateway

Den här artikeln innehåller anvisningar för att upprätta en betrodd anslutning mellan efterföljande enheter och transparent IoT Edge-gateways. En eller flera enheter kan skicka sina meddelanden via en enda gateway-enhet som hanterar anslutningen till IoT Hub i ett scenario med transparent gateway. En underordnad enhet kan vara valfritt program eller en plattform som har en identitet som skapats med den [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub) molntjänst. I många fall kan dessa program använda den [Azure IoT-enhetens SDK](../iot-hub/iot-hub-devguide-sdks.md). En underordnad enhet kan även vara ett program som körs på IoT Edge-gatewayenhet själva. 

Det finns tre allmänna steg för att skapa en lyckad transparent gatewayanslutning. Den här artikeln beskriver det tredje steget:

1. Gateway-enheten behöver på ett säkert sätt ansluta till underordnade enheter, ta emot kommunikation från underordnade enheter och dirigera meddelanden till rätt destination. Mer information finns i [konfigurera en IoT Edge-enhet kan fungera som en transparent gateway](how-to-create-transparent-gateway.md).
2. Underordnad enhet måste en enhetsidentitet för att kunna autentisera med IoT Hub och veta för att kommunicera via dess gateway-enhet. Mer information finns i [autentisera en underordnad enhet på Azure IoT Hub](how-to-authenticate-downstream-device.md).
3. **Underordnade enheten måste vara på ett säkert sätt ansluta till dess gateway-enhet.**

Den här artikeln identifierar vanliga problem med underordnade enhetsanslutningar och hjälper dig att konfigurera dina underordnade enheter genom att: 

* Förklarar transport layer security (TLS) och certifikatet grunderna. 
* Förklarar hur TLS-biblioteken fungerar i olika operativsystem och hur varje operativsystem behandlar certifikat.
* Gå igenom Azure IoT-exempel i flera språk för att snabbt komma igång. 

I den här artikeln villkoren *gateway* och *IoT Edge-gateway* referera till en IoT Edge-enhet som har konfigurerats som en transparent gateway. 

## <a name="prepare-a-downstream-device"></a>Förbereda en underordnad enhet

En underordnad enhet kan vara valfritt program eller en plattform som har en identitet som skapats med den [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub) molntjänst. I många fall kan dessa program använda den [Azure IoT-enhetens SDK](../iot-hub/iot-hub-devguide-sdks.md). En underordnad enhet kan även vara ett program som körs på IoT Edge-gatewayenhet själva. 

Om du vill ansluta en underordnad enhet till en IoT Edge-gateway, behöver du två saker:

* En enhet eller ett program som är konfigurerad med en IoT Hub-enhetsanslutningssträngen läggas till med information för att ansluta till gatewayen. 

    Det här steget förklaras i [autentisera en underordnad enhet på Azure IoT Hub](how-to-authenticate-downstream-device.md).

* Enhet eller program som har att lita på gatewayens **rotcertifikatutfärdare** certifikat för att validera TLS-anslutningar till gateway-enheten. 

    Det här steget förklaras i detalj i resten av den här artikeln. Det här steget kan vara utförs en av två sätt: genom att installera CA-certifikat i operativsystemets certifikatarkivet eller (för vissa språk) genom att referera till certifikatet i program med hjälp av Azure IoT SDK: er.

## <a name="tls-and-certificate-fundamentals"></a>TLS och certifikat

Den stora utmaningen med på ett säkert sätt ansluta underordnade enheter till IoT Edge är precis som andra säker klient/server-kommunikation som sker via internet. En klient och en server på ett säkert sätt kommunicerar via internet med [Transport layer security (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security). TLS byggs med standard [offentlig nyckelinfrastruktur (PKI)](https://en.wikipedia.org/wiki/Public_key_infrastructure) konstruktioner kallas certifikat. TLS är en ganska ingår specifikation och bemöta en stor mängd information som rör skydda två slutpunkter. Det här avsnittet sammanfattas begrepp som är relevant för dig att på ett säkert sätt ansluta enheter till en IoT Edge-gateway.

När en klient ansluter till en server, servern visar en kedja av certifikat, kallas de *servercertifikatkedjan*. En certifikatkedja består vanligtvis av ett rotcertifikat certificate authority (CA), en eller flera mellanliggande CA-certifikat och slutligen servercertifikat själva. En klient upprättar förtroende med en server genom att kryptografiskt Verifiera certifikatkedja för hela servern. Den här klienten verifiering av servercertifikatkedjan kallas *serververifiering av certifikatkedjan*. Klienten anropar kryptografiskt tjänsten för att bevisa tillgång till privata nyckeln som är associerade med servercertifikatet i en process som kallas *funktionstest av tillgång*. Kombinationen av kedjan serververifiering och bevis på tillgång kallas *serverautentisering*. För att verifiera en servercertifikatkedjan kan behöver en klient en kopia av certifikatet för rotcertifikatutfärdaren som används för att skapa (eller utfärda) serverns certifikat. Normalt när du ansluter till webbplatser, är en webbläsare förkonfigurerad med vanliga CA-certifikat så att klienten har en sömlös process. 

När en enhet ansluter till Azure IoT Hub, enheten är klienten och tjänsten IoT Hub cloud är servern. Tjänsten IoT Hub cloud backas upp av ett rot-CA-certifikat som kallas **Baltimore CyberTrust Root**, som är allmänt tillgängliga och vanligt. Eftersom IoT Hub CA-certifikatet är redan installerad på de flesta enheter kan använda många olika implementeringar TLS (OpenSSL, Schannel, LibreSSL) det automatiskt under verifieringen av servercertifikatet. En enhet som kan ansluta till IoT Hub kan ha problem med försöker ansluta till en IoT Edge-gateway.

När en enhet ansluter till en IoT Edge-gateway, underordnade enheten är klienten och gateway-enheten är servern. Azure IoT Edge kan operatörer (eller användare) att skapa gateway-certifikatkedjor men passar behoven. Operatören kan välja att använda en offentlig CA-certifikat som Baltimore, eller använda ett självsignerat (eller interna) rot CA-certifikat. Offentlig CA-certifikat har en associerad kostnad till dem ofta, så används vanligtvis i produktionsscenarier. Självsignerade certifikat är prioriterade för utveckling och testning. Transparent gateway installationsprogrammet artiklar som anges i inledningen använda självsignerade rotcertifikat CA: N. 

När du använder ett självsignerat rotcertifikat för en IoT Edge-gateway måste vara installerad på eller tillhandahålls till alla underordnade enheter försöker ansluta till gatewayen. 

![Installationsprogram för gateway-certifikatet](./media/how-to-create-transparent-gateway/gateway-setup.png)

Läs mer om IoT Edge-certifikat och vissa konsekvenser för produktion i [användningsinformation för IoT Edge certifikat](iot-edge-certs.md).

## <a name="provide-the-root-ca-certificate"></a>Ange rot-CA-certifikat

Om du vill verifiera certifikat för gateway-enheten, måste underordnade enheter ha sin egen kopia av certifikatet från rotcertifikatutfärdaren. Om du använde skripten på IoT Edge git-lagringsplatsen för att skapa testcertifikat och sedan certifikatet för rotcertifikatutfärdaren kallas **azure-iot-test-only.root.ca.cert.pem**. Om du inte redan gjort som en del av de andra underordnade enheten förberedelser, flytta den här certifikatfilen till valfri katalog på en underordnad enhet. Du kan använda en tjänst som [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) eller en funktion som [säker kopia protokollet](https://www.ssh.com/ssh/scp/) att flytta certifikatfilen.

## <a name="install-certificates-in-the-os"></a>Installera certifikat i Operativsystemet

Installera rotcertifikatutfärdarens certifikat i certifikatarkivet för operativsystemets Allmänt gör de flesta programmen kan använda certifikatet från rotcertifikatutfärdaren. Det finns vissa undantag, precis som NodeJS-program som inte använder OS-certifikatet lagra men föredrar att använda interna certifikatarkiv för nod-runtime. Om du inte kan installera certifikatet på nivån operativsystemet ska gå vidare till [använda certifikat med Azure IoT SDK: er](#use-certificates-with-azure-iot-sdks). 

### <a name="ubuntu"></a>Ubuntu

Följande kommandon är ett exempel på hur du installerar ett CA-certifikat på en Ubuntu-värd. Det här exemplet förutsätts att du använder den **azure-iot-test-only.root.ca.cert.pem** certifikatet från krav artiklar och att du har kopierat certifikatet till en plats på den underordnade enheten.

```bash
sudo cp <path>/azure-iot-test-only.root.ca.cert.pem /usr/local/share/ca-certificates/azure-iot-test-only.root.ca.cert.pem.crt
sudo update-ca-certificates
```

Du bör se ett meddelande med texten ”uppdaterar certifikat i /etc/ssl/certs... 1 lade till 0 bort; gjorts ”.

### <a name="windows"></a>Windows

Följande är ett exempel på hur du installerar ett CA-certifikat på en Windows-värd. Det här exemplet förutsätts att du använder den **azure-iot-test-only.root.ca.cert.pem** certifikatet från krav artiklar och att du har kopierat certifikatet till en plats på den underordnade enheten.

1. I Start-menyn, Sök efter och välj **hantera datorcertifikat**. Ett verktyg som kallas **certlm** öppnas.
2. Gå till **certifikat – lokal dator** > **betrodda rotcertifikatutfärdare**.
3. Högerklicka på **certifikat** och välj **alla uppgifter** > **Import**. Guiden Importera certifikat ska starta. 
4. Följ stegen enligt anvisningarna och importera certifikatfil `<path>/azure-iot-test-only.root.ca.cert.pem`. När du är klar visas meddelandet ”importerades”. 

Du kan också installera certifikat via programmering med hjälp av .NET-API: er, som visas i .NET-exempel senare i den här artikeln. 

Program använder vanligtvis Windows tillhandahållna TLS stack kallas [Schannel](https://docs.microsoft.com/windows/desktop/com/schannel) att på ett säkert sätt ansluta via TLS. Schannel *kräver* att alla certifikat installeras i certifikatarkivet Windows innan du försöker upprätta en anslutning med TLS.

## <a name="use-certificates-with-azure-iot-sdks"></a>Använda certifikat med Azure IoT SDK: er

Det här avsnittet beskrivs hur Azure IoT SDK ansluter till en IoT Edge-enhet med hjälp av enkla exempelprogram. Målet med alla exempel är att ansluta enhetsklienten och skicka telemetrimeddelanden till gatewayen, och sedan stänga anslutningen och avsluta. 

Har två saker som är redo innan du använder programnivå exemplen:

* Underordnade enhetens IoT Hub-anslutningssträngen ändras så att den pekar till gateway-enheten och eventuella certifikat som krävs för att autentisera din underordnad enhet till IoT Hub. Mer information finns i [autentisera en underordnad enhet på Azure IoT Hub](how-to-authenticate-downstream-device.md).

* Den fullständiga sökvägen till rot-CA-certifikatet som du kopierade och sparat någonstans på underordnade enheten.

    Till exempel `<path>/azure-iot-test-only.root.ca.cert.pem`. 

### <a name="nodejs"></a>NodeJS

Det här avsnittet innehåller ett exempelprogram för att ansluta en Azure IoT NodeJS enhetsklienten till en IoT Edge-gateway. För NodeJS-program, måste du installera certifikatet från rotcertifikatutfärdaren på programnivå som visas här. NodeJS-program använda inte datorns certifikatarkiv. 

1. Hämta det här exemplet för **edge_downstream_device.js** från den [lagringsplatsen för Azure IoT-enhetens SDK för Node.js-exempel](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples). 
2. Se till att du har alla förutsättningar för att köra exemplet genom att granska den **readme.md** fil. 
3. I filen edge_downstream_device.js uppdatera den **connectionString** och **edge_ca_cert_path** variabler. 
4. I SDK-dokumentationen för instruktioner om hur du kör exemplet på din enhet. 

För att förstå exemplet som du kör, är ett kodfragment hur klient-SDK läser certifikatfilen och används för att upprätta en säker TLS-anslutning: 

```javascript
// Provide the Azure IoT device client via setOptions with the X509
// Edge root CA certificate that was used to setup the Edge runtime
var options = {
    ca : fs.readFileSync(edge_ca_cert_path, 'utf-8'),
};
```

### <a name="net"></a>.NET

Det här avsnittet introducerar ett exempelprogram för att ansluta en klient för Azure IoT .NET-enhet till en IoT Edge-gateway. .NET-program är dock automatiskt kan använda alla installerade certifikat i datorns certifikatarkiv på både Linux och Windows-värdar.

1. Hämta det här exemplet för **EdgeDownstreamDevice** från den [IoT Edge .NET exempelmappen](https://github.com/Azure/iotedge/tree/master/samples/dotnet/EdgeDownstreamDevice). 
2. Se till att du har alla förutsättningar för att köra exemplet genom att granska den **readme.md** fil. 
3. I den **egenskaper / launchSettings.json** uppdaterar den **DEVICE_CONNECTION_STRING** och **CA_CERTIFICATE_PATH** variabler. Om du vill använda certifikatet som installerades i det betrodda certifikatarkivet på värdsystemet du inget i den här variabeln. 
4. I SDK-dokumentationen för instruktioner om hur du kör exemplet på din enhet. 

Om du vill installera programmässigt ett betrott certifikat i certifikatarkivet via ett .NET-program, referera till den **InstallCACert()** fungera i den **EdgeDownstreamDevice / Program.cs** fil. Den här åtgärden är idempotenta, så kan köras flera gånger med samma värden utan ytterligare påverkas. 

### <a name="c"></a>C

Det här avsnittet introducerar ett exempelprogram för att ansluta en klient för Azure IoT C-enheten till en IoT Edge-gateway. C SDK kan arbeta med många TLS-bibliotek, inklusive OpenSSL, WolfSSL och Schannel. Mer information finns i den [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c). 

1. Hämta den **iotedge_downstream_device_sample** programmet från den [Azure IoT-enhetens SDK för C-exempel](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples). 
2. Se till att du har alla förutsättningar för att köra exemplet genom att granska den **readme.md** fil. 
3. I filen iotedge_downstream_device_sample.c uppdatera den **connectionString** och **edge_ca_cert_path** variabler. 
4. I SDK-dokumentationen för instruktioner om hur du kör exemplet på din enhet. 

Azure IoT-enhetens SDK för C ger ett alternativ för att registrera ett CA-certifikat när du konfigurerar klienten. Den här åtgärden installera inte certifikatet var som helst, men i stället använder en strängformat för certifikatet i minnet. Sparade certifikatet har angetts för den underliggande TLS-stacken när en anslutning upprättas. 

```C
(void)IoTHubDeviceClient_SetOption(device_handle, OPTION_TRUSTED_CERT, cert_string);
```

På Windows-värdar, om du inte använder OpenSSL eller en annan TLS-biblioteket, SDK: N som standard använder Schannel. För Schannel ska fungera, IoT Edge rot-CA-certifikatet bör installeras i certifikatarkivet Windows inte har angetts med hjälp av den `IoTHubDeviceClient_SetOption` igen. 

### <a name="java"></a>Java

Det här avsnittet introducerar ett exempelprogram som du ansluter en enhet Azure IoT-Java-klient till en IoT Edge-gateway. 

1. Hämta det här exemplet för **överföringshändelse** från den [Azure IoT-enhetens SDK för Java-exempel](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples). 
2. Se till att du har alla förutsättningar för att köra exemplet genom att granska den **readme.md** fil. 
3. I SDK-dokumentationen för instruktioner om hur du kör exemplet på din enhet.

### <a name="python"></a>Python

Det här avsnittet introducerar ett exempelprogram för att ansluta en Azure IoT Python-klienten för enheter till en IoT Edge-gateway. 

1. Hämta det här exemplet för **edge_downstream_client** från den [Azure IoT-enhetens SDK för Python-exempel](https://github.com/Azure/azure-iot-sdk-python/tree/master/device/samples). 
2. Se till att du har alla förutsättningar för att köra exemplet genom att granska den **readme.md** fil. 
3. I filen edge_downstream_client.py uppdatera den **anslutningssträng** och **TRUSTED_ROOT_CA_CERTIFICATE_PATH** variabler. 
4. I SDK-dokumentationen för instruktioner om hur du kör exemplet på din enhet. 


## <a name="test-the-gateway-connection"></a>Testa gateway-anslutning

Det här är ett exempel på kommando som testar att allt har konfigurerats korrekt. Du bör se ett meddelande om ”verifieras OK”.

```cmd/sh
openssl s_client -connect mygateway.contoso.com:8883 -CAfile <CERTDIR>/certs/azure-iot-test-only.root.ca.cert.pem -showcerts
```

## <a name="troubleshoot-the-gateway-connection"></a>Felsöka gateway-anslutning

Om enheten löv har tillfällig anslutning till dess gateway-enhet kan du prova följande steg för matchning. 

1. Är gateway-värdnamnet i anslutningssträngen samma som värde för värdnamn i filen IoT Edge config.yaml på gateway-enheten?
2. Är gateway-värdnamn som matchas till en IP-adress? Du kan lösa tillfälliga anslutningar med hjälp av DNS eller genom att lägga till en host-filpost på löv-enheten.
3. Är kommunikationsportar öppen i brandväggen? Kommunikationen baserat på det protokoll som används (MQTTS:8883 / AMQPS:5671 / HTTPS:433) måste vara mellan underordnad enhet och transparent IoT Edge.

## <a name="next-steps"></a>Nästa steg

Lär dig hur IoT Edge kan utöka [offlinefunktionerna](offline-capabilities.md) till underordnade enheter. 
