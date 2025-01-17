---
title: Installera Azure IoT Edge på Linux | Microsoft Docs
description: Installationsinstruktioner för Azure IoT Edge på Linux AMD64-enheter som kör Ubuntu
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 06/27/2019
ms.author: kgremban
ms.custom: seodec18
ms.openlocfilehash: bbab0d8d0947c18cf8e6c178d12fdbd7b335d2b6
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/01/2019
ms.locfileid: "67485903"
---
# <a name="install-the-azure-iot-edge-runtime-on-linux-x64"></a>Installera Azure IoT Edge-körningen på Linux (x64)

Azure IoT Edge-körningen är vad omvandlar en enhet till en IoT Edge-enhet. Körningen kan distribueras på enheter som är så litet som en Raspberry Pi eller stora som industriella-server. När en enhet konfigureras med IoT Edge-körningen, kan du börja distribuera affärslogik till den från molnet.

Mer information finns i [förstå Azure IoT Edge-körningen och dess arkitektur](iot-edge-runtime.md).

Den här artikeln visar hur du installerar Azure IoT Edge-körningen på din Ubuntu Linux x64 (Intel/AMD) IoT Edge-enhet. Referera till [Azure IoT Edge stöds system](support.md#operating-systems) en lista över operativsystem som stöds AMD64.

> [!NOTE]
> Paket i databaser för Linux-programvara är gäller under licensvillkor som finns i varje paket (/ usr/dela/docs/*paketnamn*). Läs licensvillkoren innan du börjar använda paketet. Din installation och användning av paketet kräver att du accepterar dessa villkor. Om du inte samtycker till licensvillkoren, Använd inte paketet.

## <a name="install-the-latest-version"></a>Installera den senaste versionen

Använd följande avsnitt för att installera den senaste versionen av tjänsten Azure IoT Edge på dina enheter. 

### <a name="register-microsoft-key-and-software-repository-feed"></a>Registrera Microsoft nyckeln och program lagringsplats feed

Förbered enheten för IoT Edge runtime-installation.


Installera konfiguration av databaser. Välj antingen den **16.04** eller **18.04** kodfragment som passar din version av Ubuntu:

> [!NOTE]
> Kontrollera att du väljer kodfragmentet rätt kod som visas för din version av Ubuntu.

* För **Ubuntu 16.04**:
   ```bash
   curl https://packages.microsoft.com/config/ubuntu/16.04/prod.list > ./microsoft-prod.list
   ```

* För **Ubuntu 18.04**:
   ```bash
   curl https://packages.microsoft.com/config/ubuntu/18.04/prod.list > ./microsoft-prod.list
   ```
   
Kopiera Skapad lista.

   ```bash
   sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
   ```

Installera Microsoft GPG offentlig nyckel

   ```bash
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
   ```

### <a name="install-the-container-runtime"></a>Installera runtime behållare

Azure IoT Edge förlitar sig på en [OCI-kompatibla](https://www.opencontainers.org/) behållare runtime. I produktionsscenarier rekommenderar vi att du använder den [Moby-baserade](https://mobyproject.org/) motor som anges nedan. Det är den enda behållare motorn officiellt stöds med Azure IoT Edge. Docker CE/EE-behållaravbildningar är kompatibla med Moby runtime.

Utföra apt uppdatering.

   ```bash
   sudo apt-get update
   ```

Installera Moby-motorn.

   ```bash
   sudo apt-get install moby-engine
   ```

Installera Moby kommandoradsgränssnittet (CLI). CLI är användbart för utveckling men valfria för Produktionsdistribution.

   ```bash
   sudo apt-get install moby-cli
   ```

#### <a name="verify-your-linux-kernel-for-moby-compatibility"></a>Kontrollera din Linux-kernel för Moby kompatibilitet

Många tillverkare av inbäddade enheter skicka enhet-avbildningar som innehåller anpassade Linux-kernel som saknar funktioner som krävs för kompatibilitet för körning av behållare. Om det uppstår problem när du installerar den rekommenderade [Moby](https://github.com/moby/moby) körning av behållare, kanske du felsöker din Linux kernel konfiguration med hjälp av den [Kontrollera-config](https://raw.githubusercontent.com/moby/moby/master/contrib/check-config.sh) skriptet som anges i den officiella [Moby Github-lagringsplatsen](https://github.com/moby/moby) genom att köra följande kommandon på enheten.

   ```bash
   curl -sSL https://raw.githubusercontent.com/moby/moby/master/contrib/check-config.sh -o check-config.sh
   chmod +x check-config.sh
   ./check-config.sh
   ```

Detta ger ett detaljerat resultat som innehåller statusen för kernelfunktioner som används av Moby-körningen. Du måste kontrollera att alla objekt `Generally Necessary` och `Network Drivers` är aktiverade för att säkerställa att din kernel är helt kompatibelt med Moby runtime.  Om du har identifierat några saknas funktioner, kan du aktivera dem genom att återskapa din kernel från källan och välja de associerade modulerna ska ingå i lämplig kernel-.config.  Om du använder en kernel-konfiguration-generator som defconfig eller menuconfig behöver du på samma sätt att hitta och aktivera respektive funktioner och därefter återskapa din kernel.  När du har distribuerat din nyligen ändrade kernel, kör du skriptet Kontrollera-config igen för att kontrollera att dina identifierade funktioner har aktiverats.

### <a name="install-the-azure-iot-edge-security-daemon"></a>Installera Daemon för Azure IoT Edge-säkerhet

Den **IoT Edge security daemon** tillhandahåller och underhåller säkerhetsstandarder på IoT Edge-enhet. Daemonen startar vid varje start och startar enheten genom att starta resten av IoT Edge-körningen.

Installationskommandot installerar också standardversionen av den **libiothsm** om det inte redan finns.

Utföra apt uppdatering.

   ```bash
   sudo apt-get update
   ```

Installera daemonen säkerhet. Paketet har installerats på `/etc/iotedge/`.

   ```bash
   sudo apt-get install iotedge
   ```

När IoT Edge har installerats uppmanas du att uppdatera konfigurationsfilen i utdata. Följ stegen i den [konfigurera Azure IoT Edge security daemon](#configure-the-azure-iot-edge-security-daemon) avsnitt för att avsluta etablering av enheten. 

## <a name="install-a-specific-version"></a>Installera en specifik version

Om du vill installera en specifik version av Azure IoT Edge kan du rikta komponentfiler direkt från IoT Edge GitHub-lagringsplatsen. Använd följande steg för att få alla IoT Edge-komponenterna på din enhet: Moby-motorn och CLI, libiothsm och slutligen daemonen IoT Edge-säkerhet.

1. Navigera till den [Azure IoT Edge släpper](https://github.com/Azure/azure-iotedge/releases), och hitta den utgivna versionen som du vill använda. 

2. Expandera den **tillgångar** för den här versionen.

3. Det kanske eller kanske inte uppdateringar av Moby-motor i en viss version. Om du ser filer som börjar med **moby-engine** och **moby cli**, Använd följande kommandon för att uppdatera dessa komponenter. Om du inte ser några Moby filer och du inte redan har Moby installerad på din enhet, gå tillbaka till de äldre versionen tillgångarna tills du hittar dem. 

   1. Hitta den **moby-engine** fil som matchar arkitekturen för din IoT Edge-enhet. Högerklicka på länken filen och Kopiera länkadress.

   2. Använder du kopierade länk i följande kommando för att installera den versionen av Moby-motor: 

      ```bash
      curl -L <moby-engine link> -o moby_engine.deb && sudo dpkg -i ./moby_engine.deb
      ```

   3. Hitta den **moby cli** fil som matchar arkitekturen för din IoT Edge-enhet. Moby CLI är en valfri komponent, men kan vara till hjälp under utveckling. Högerklicka på länken filen och Kopiera länkadress. 

   4. Använd länken kopierade i följande kommando för att installera den versionen av Moby CLI: 

      ```bash
      curl -L <moby-cli link> -o moby_cli.deb && sudo dpkg -i ./moby_cli.deb
      ```

4. Varje version bör ha nya filer för IoT Edge security-daemon och hsmlib. Använd följande kommandon för att uppdatera dessa komponenter. 

   1. Hitta den **libiothsm-std** fil som matchar arkitekturen för din IoT Edge-enhet. Högerklicka på länken filen och Kopiera länkadress. 

   2. Använd den kopierade länken i följande kommando för att installera den versionen av hsmlib:

      ```bash
      curl -L <libiothsm-std link> -o libiothsm-std.deb && sudo dpkg -i ./libiothsm-std.deb
      ```
   
   3. Hitta den **iotedge** fil som matchar arkitekturen för din IoT Edge-enhet. Högerklicka på länken filen och Kopiera länkadress. 

   4. Använd den kopierade länken i följande kommando för att installera den versionen av IoT Edge security daemon. 

      ```bash
      curl -L <iotedge link> -o iotedge.deb && sudo dpkg -i ./iotedge.deb
      ```

När IoT Edge har installerats uppmanas du att uppdatera konfigurationsfilen i utdata. Följ stegen i nästa avsnitt för att avsluta etablering av enheten. 

## <a name="configure-the-azure-iot-edge-security-daemon"></a>Konfigurera Azure IoT Edge security daemon

Konfigurera IoT Edge-körningen för att länka den fysiska enheten med en enhetsidentitet som finns i Azure IoT hub.

Daemonen kan konfigureras med hjälp av konfigurationsfilen på `/etc/iotedge/config.yaml`. Filen är skrivskyddad som standard måste du kanske förhöjd behörighet att redigera den.

En enda IoT Edge-enhet kan etableras manuellt med hjälp av en sträng för anslutningar av enhet som tillhandahålls av IoT Hub. Eller så kan du använda Device Provisioning-tjänsten att automatiskt etablera enheter, vilket är användbart när du har många enheter för att etablera. Beroende på föredrar etablering, väljer du lämplig installationsskriptet.

### <a name="option-1-manual-provisioning"></a>Alternativ 1: Manuell etablering

Om du vill etablera en enhet manuellt, måste du ange den med en [enhetsanslutningssträngen](how-to-register-device-portal.md) att du kan skapa genom att registrera en ny enhet i IoT hub.

Öppna konfigurationsfilen.

```bash
sudo nano /etc/iotedge/config.yaml
```

Gå till avsnittet etablering av filen. Ta bort kommentarerna i **manuell** Etableringsläge och kontrollera att dps Etableringsläge har kommenterats bort. Uppdatera värdet för **device_connection_string** med anslutningssträngen från din IoT Edge-enhet.

   ```yaml
   provisioning:
     source: "manual"
     device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
  
   # provisioning:
   #   source: "dps"
   #   global_endpoint: "https://global.azure-devices-provisioning.net"
   #   scope_id: "{scope_id}"
   #   registration_id: "{registration_id}"
   ```

Spara och stäng filen.

   `CTRL + X`, `Y`, `Enter`

Starta om daemon när du har angett etableringsinformationen i konfigurationsfilen:

```bash
sudo systemctl restart iotedge
```

### <a name="option-2-automatic-provisioning"></a>Alternativ 2: Automatisk etablering

Att automatiskt etablera en enhet [konfigurera Device Provisioning-tjänsten och hämta din enhet registrerings-ID](how-to-auto-provision-simulated-device-linux.md). Automatisk etablering fungerar bara med enheter som har ett chip för Trusted Platform Module (TPM). Till exempel levereras Raspberry Pi enheter inte med TPM som standard.

Öppna konfigurationsfilen.

```bash
sudo nano /etc/iotedge/config.yaml
```

Gå till avsnittet etablering av filen. Ta bort kommentarerna i **dps** etablering läge och kommentera ut avsnittet Manuell. Uppdaterar du värdet för **scope_id** och **registration_id** med värden från din IoT Hub Device Provisioning-tjänsten och IoT Edge-enhet med TPM.

   ```yaml
   # provisioning:
   #   source: "manual"
   #   device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
  
   provisioning:
     source: "dps"
     global_endpoint: "https://global.azure-devices-provisioning.net"
     scope_id: "{scope_id}"
     registration_id: "{registration_id}"
   ```

Spara och stäng filen.

   `CTRL + X`, `Y`, `Enter`

Starta om daemon när du har angett etableringsinformationen i konfigurationsfilen:

```bash
sudo systemctl restart iotedge
```

## <a name="verify-successful-installation"></a>Verifiera installationen

Om du har använt den **manuell konfiguration** stegen i föregående avsnitt, IoT Edge-körningen ska vara har etablerad och körs på din enhet. Om du har använt den **automatisk konfiguration** stegen, måste du utföra några ytterligare steg så att körningen kan registrera din enhet med IoT-hubben för din räkning. Nästa steg, se [skapa och etablera en simulerad TPM IoT Edge-enhet på en Linux-dator](how-to-auto-provision-simulated-device-linux.md#give-iot-edge-access-to-the-tpm).

Du kan kontrollera status för en IoT Edge-Daemon med hjälp av:

```bash
systemctl status iotedge
```

Kontrollera daemon loggar med:

```bash
journalctl -u iotedge --no-pager --no-full
```

Och listan körs moduler med:

```bash
sudo iotedge list
```

## <a name="tips-and-suggestions"></a>Användbara tips och råd

Förhöjd behörighet krävs för att köra `iotedge`-kommandon. När du har installerat en runtime, logga ut från datorn och logga in för att uppdatera dina behörigheter automatiskt. Tills dess kan du använda **sudo** framför alla `iotedge` kommandona.

På resursen begränsad enheter, vi rekommenderar starkt att du ställer in den *OptimizeForPerformance* miljövariabeln till *FALSKT* enligt anvisningarna i den [felsökningsguide ](troubleshoot.md).

Om ditt nätverk som har en proxyserver, följer du stegen i [konfigurerar IoT Edge-enheten att kommunicera via en proxyserver](how-to-configure-proxy-support.md).

## <a name="uninstall-iot-edge"></a>Avinstallera IoT Edge

Om du vill ta bort IoT Edge-installationen från din Linux-enhet kan använda följande kommandon från kommandoraden.

Ta bort IoT Edge-körningen.

```bash
sudo apt-get remove --purge iotedge
```

När IoT Edge-körningen tas bort, stoppas den behållare som det skapats men finns kvar på enheten. Visa alla behållare för att se vilka som finns kvar.

```bash
sudo docker ps -a
```

Ta bort behållarna från enheten, till exempel två körningsbehållarna.

```bash
sudo docker rm -f <container name>
```

Ta bort behållaren runtime slutligen från din enhet.

```bash
sudo apt-get remove --purge moby-cli
sudo apt-get remove --purge moby-engine
```

## <a name="next-steps"></a>Nästa steg

Nu när du har en IoT Edge-enhet med den som är installerad kan du [distribuera IoT Edge-moduler](how-to-deploy-modules-portal.md).

Om du har problem med IoT Edge-körningen installeras korrekt kan du kolla den [felsökning](troubleshoot.md) sidan.

Om du vill uppdatera en befintlig installation till den senaste versionen av IoT Edge, se [uppdatera IoT Edge security daemon och runtime](how-to-update-iot-edge.md).
