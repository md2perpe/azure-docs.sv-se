---
title: Anslut Fortinet data till Azure Sentinel-Preview | Microsoft Docs
description: Lär dig hur du ansluter Fortinet data till Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: add92907-0d7c-42b8-a773-f570f2d705ff
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/17/2019
ms.author: rkarlin
ms.openlocfilehash: 6f061a99a526b3865081a7d19b1eecbdc158d6a1
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67442648"
---
# <a name="connect-your-fortinet-appliance"></a>Ansluta din Fortinet-installation

> [!IMPORTANT]
> Azure Sentinel är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal. Det rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [kompletterande användningsvillkor för förhandsversioner av Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Du kan ansluta Azure Sentinel till alla Fortinet-installation för genom att spara loggfilerna som Syslog Common Event Format (CEF). Med integrering med Azure Sentinel, kan du enkelt köra analyser och frågor över loggfilsdata från Fortinet. Mer information om hur Azure Sentinel matar in data som CEF Se [ansluta CEF installationer](connect-common-event-format.md).

> [!NOTE]
> Data lagras i den geografiska platsen för arbetsytan där du kör Azure Sentinel.

## <a name="step-1-connect-your-fortinet-appliance-by-using-an-agent"></a>Steg 1: Ansluta din Fortinet-installation med hjälp av en agent

Distribuera en agent på en dedikerad virtuell dator eller lokal dator för att stödja kommunikation mellan enheten och den Sentinel-Azure för att ansluta din Fortinet installation till Azure Sentinel. Du kan distribuera agenten automatiskt eller manuellt. Automatisk distribution är endast tillgänglig om den dedikerade datorn är en ny virtuell dator som du skapar i Azure.

Du kan också distribuera agenten manuellt på en befintlig Azure-dator på en virtuell dator i ett annat moln eller på en lokal dator.

Ett nätverksdiagram för båda alternativen finns i [ansluta datakällor](connect-data-sources.md#agent-options).

### <a name="deploy-the-agent-in-azure"></a>Distribuera agenten i Azure

1. I Sentinel-Azure-portalen väljer **datakopplingar** och väljer du typen av installation.

1. Under **Linux Syslog-agentkonfiguration**:
   - Välj **automatisk distribution** om du vill skapa en ny dator som är förinstallerade med agenten Sentinel-Azure och innehåller alla den konfiguration som krävs, enligt beskrivningen ovan. Välj **automatisk distribution** > **agenten för automatisk distribution**. Sidan för en dedikerad virtuell dator som automatiskt ansluter till din arbetsyta visas. Den virtuella datorn är en **standard D2s v3 (2 virtuella processorer, 8 GB minne)** och har en offentlig IP-adress.
      1. På den **anpassad distribution** anger din information, ange ett användarnamn och ett lösenord, och om du samtycker till villkoren kan du köpa den virtuella datorn.
      1. Konfigurera installationen för att skicka loggar med hjälp av inställningarna som visas på sidan. Använd de här inställningarna för allmän Common Event Format-anslutningen:
         - Protocol = UDP
         - Port = 514
         - Anläggning = lokal 4
         - Format = CEF
   - Välj **manuell distribution** om du vill använda en befintlig virtuell dator som den dedikerade Linux-datorn där Sentinel-Azure-agenten är installerad. 
      1. Under **ladda ned och installera agenten Syslog**väljer **virtuella Linux-datorer**. 
      1. På den **virtuella datorer** skärmen, väljer du den dator som du vill använda och välj **Connect**.
      1. På skärmen för anslutningen under **konfigurera och vidarebefordra Syslog**, Ställ in om din Syslog-daemon är **rsyslog.d** eller **syslog-ng**. 
      1. Kopiera dessa kommandon och köra dem på din installation:
          - Om du har valt rsyslog.d:
              
            1. Berätta för Syslog-daemon lyssna på anläggningen local_4 och skicka Syslog-meddelanden till Sentinel-Azure-agenten med hjälp av port 25226. Använd följande kommando: `sudo bash -c "printf 'local4.debug  @127.0.0.1:25226\n\n:msg, contains, \"Fortinet\"  @127.0.0.1:25226' > /etc/rsyslog.d/security-config-omsagent.conf"`
            1. Ladda ned och installera den [security_events konfigurationsfilen](https://aka.ms/asi-syslog-config-file-linux) som konfigurerar Syslog-agenten att lyssna på port 25226. Använd det här kommandot: `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` där {0} ska ersättas med din arbetsyta GUID.
            1. Starta om syslog-daemon med hjälp av det här kommandot: `sudo service rsyslog restart`
             
          - Om du har valt syslog-ng:

              1. Berätta för Syslog-daemon lyssna på anläggningen local_4 och skicka Syslog-meddelanden till Sentinel-Azure-agenten med hjälp av port 25226. Använd följande kommando: `sudo bash -c "printf 'filter f_local4_oms { facility(local4); };\n  destination security_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_local4_oms); destination(security_oms); };\n\nfilter f_msg_oms { match(\"Fortinet\" value(\"MESSAGE\")); };\n  destination security_msg_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_msg_oms); destination(security_msg_oms); };' > /etc/syslog-ng/security-config-omsagent.conf"`
              1. Ladda ned och installera den [security_events konfigurationsfilen](https://aka.ms/asi-syslog-config-file-linux) som konfigurerar Syslog-agenten att lyssna på port 25226. Använd det här kommandot: `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` där {0} ska ersättas med din arbetsyta GUID.
              1. Starta om syslog-daemon med hjälp av det här kommandot: `sudo service syslog-ng restart`
      1. Starta om Syslog-agenten med hjälp av det här kommandot: `sudo /opt/microsoft/omsagent/bin/service_control restart [{workspace GUID}]`
      1. Bekräfta att det inte finns några fel i agentloggen genom att köra det här kommandot: `tail /var/opt/microsoft/omsagent/log/omsagent.log`

### <a name="deploy-the-agent-on-an-on-premises-linux-server"></a>Distribuera agenten på en lokal server för Linux

Om du inte använder Azure, distribuera manuellt Azure Sentinel-agenten ska köras på en dedikerad server för Linux.

1. I Sentinel-Azure-portalen väljer **datakopplingar** och väljer du typen av installation.
1. Skapa en dedikerad Linux VM under **Linux Syslog-agentkonfiguration** Välj **manuell distribution**.

    1. Under **ladda ned och installera agenten Syslog**väljer **icke-Azure Linux-dator**.
    1. I den **direktagent** skärm som öppnas väljer du **agenten för Linux** att ladda ned agenten eller kör detta kommando för att hämta den på din Linux-dator: `wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w {workspace GUID} -s gehIk/GvZHJmqlgewMsIcth8H6VqXLM9YXEpu0BymnZEJb6mEjZzCHhZgCx5jrMB1pVjRCMhn+XTQgDTU3DVtQ== -d opinsights.azure.com`

       1. På skärmen anslutningen under **konfigurera och vidarebefordra Syslog**, Ställ in om din Syslog-daemon är **rsyslog.d** eller **syslog-ng**.
       1. Kopiera dessa kommandon och köra dem på din installation:

          - Om du har valt rsyslog:

            1. Berätta för Syslog-daemon lyssna på anläggningen local_4 och skicka Syslog-meddelanden till Sentinel-Azure-agenten använder port 25226. Använd följande kommando: `sudo bash -c "printf 'local4.debug  @127.0.0.1:25226\n\n:msg, contains, \"Fortinet\"  @127.0.0.1:25226' > /etc/rsyslog.d/security-config-omsagent.conf"`
            1. Ladda ned och installera den [security_events konfigurationsfilen](https://aka.ms/asi-syslog-config-file-linux) som konfigurerar Syslog-agenten att lyssna på port 25226. Använd det här kommandot: `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` där {0} ska ersättas med din arbetsyta GUID.
            1. Starta om syslog-daemon med hjälp av det här kommandot: `sudo service rsyslog restart`

          - Om du har valt syslog-ng:

            1. Berätta för Syslog-daemon lyssna på anläggningen local_4 och skicka Syslog-meddelanden till Sentinel-Azure-agenten med hjälp av port 25226. Använd följande kommando: `sudo bash -c "printf 'filter f_local4_oms { facility(local4); };\n  destination security_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_local4_oms); destination(security_oms); };\n\nfilter f_msg_oms { match(\"Fortinet\" value(\"MESSAGE\")); };\n  destination security_msg_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_msg_oms); destination(security_msg_oms); };' > /etc/syslog-ng/security-config-omsagent.conf"`
            1. Ladda ned och installera den [security_events konfigurationsfilen](https://aka.ms/asi-syslog-config-file-linux) som konfigurerar Syslog-agenten att lyssna på port 25226. Använd det här kommandot: `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` där {0} ska ersättas med din arbetsyta GUID.
            1. Starta om syslog-daemon med hjälp av det här kommandot: `sudo service syslog-ng restart`

      1. Starta om Syslog-agenten med hjälp av det här kommandot: `sudo /opt/microsoft/omsagent/bin/service_control restart [{workspace GUID}]`
      1. Bekräfta att det inte finns några fel i agentloggen genom att köra det här kommandot: `tail /var/opt/microsoft/omsagent/log/omsagent.log`
 
## <a name="step-2-forward-fortinet-logs-to-the-syslog-agent"></a>Steg 2: Vidarebefordra Fortinet loggar till Syslog-agent

Konfigurera Fortinet för att vidarebefordra Syslog-meddelanden i CEF-format till Azure-arbetsytan via Syslog-agenten.

1. Öppna CLI på Fortinet-installationen och kör följande kommandon:

        config log syslogd setting
        set format cef
        set facility <facility_name>
        set port 514
        set reliable disable
        set server <ip_address_of_Receiver>
        set status enable
        end

    - Byt ut servern **ip-adress** med IP-adressen för agenten.
    - Ange **facility_name** att använda funktionen som du konfigurerade i agenten. Som standard anger detta till local4 i agenten.
    - Ange den **syslog-port** till **514** eller den port som angetts på agenten.
    - Om du vill aktivera CEF-format i tidiga FortiOS versioner, kan du behöva kör kommandot **csv inaktivera**.
 
   > [!NOTE] 
   > Mer information går du till den [Fortinet dokumentbibliotek](https://aka.ms/asi-syslog-fortinet-fortinetdocumentlibrary). Välj din version och använda den **handboken** och **referens: loggar meddelandet**.

 Om du vill använda relevanta schemat i Azure Monitor Log Analytics för Fortinet händelser, söka efter `CommonSecurityLog`.


## <a name="step-3-validate-connectivity"></a>Steg 3: Verifiera anslutningen

Det kan ta upp till 20 minuter tills loggarna börjar visas i Log Analytics. 

1. Kontrollera att du använder rätt anläggningen. Funktionen måste vara samma i din installation och Sentinel-Azure. Du kan kontrollera vilken anläggning-fil som du använder i Azure Sentinel och göra ändringar i filen `security-config-omsagent.conf`. 

2. Se till att dina loggar får till rätt port i Syslog-agenten. Kör det här kommandot på agentdatorn Syslog: `tcpdump -A -ni any  port 514 -vv`. Det här kommandot visar de loggar som strömma från enheten till den Syslog-datorn. Kontrollera att loggarna tas emot från käll-installationen på rätt port och rätt anläggningen.

3. Se till att loggarna du skickar följer [RFC 5424](https://tools.ietf.org/html/rfc542).

4. På datorn som kör Syslog-agenten Kontrollera att portarna 514 och 25226 är öppen och lyssnar med hjälp av kommandot `netstat -a -n:`. Mer information om hur du använder det här kommandot finns i [netstat(8) - Linux man sidan](https://linux.die.net/man/8/netstat). Om den lyssnar på rätt sätt, visas:

   ![Azure Sentinel-portar](./media/connect-cef/ports.png) 

5. Kontrollera daemon anges att lyssna på port 514, där du skickar loggarna.
    - För rsyslog:<br>Kontrollera att filen `/etc/rsyslog.conf` innehåller den här konfigurationen:

           # provides UDP syslog reception
           module(load="imudp")
           input(type="imudp" port="514")
        
           # provides TCP syslog reception
           module(load="imtcp")
           input(type="imtcp" port="514")

      Mer information finns i [imudp: UDP inkommande syslogmodulen](https://www.rsyslog.com/doc/v8-stable/configuration/modules/imudp.html#imudp-udp-syslog-input-module) och [imtcp: Inkommande TCP Syslog-modulen](https://www.rsyslog.com/doc/v8-stable/configuration/modules/imtcp.html#imtcp-tcp-syslog-input-module).

   - För syslog-ng:<br>Kontrollera att filen `/etc/syslog-ng/syslog-ng.conf` innehåller den här konfigurationen:

           # source s_network {
            network( transport(UDP) port(514));
             };
     Mer information finns i [imudp: UDP inkommande syslogmodulen](https://rsyslog.readthedocs.io/en/latest/configuration/modules/imudp.html) och [syslog-ng öppen källkod edition 3,16 - Administrationsguide](https://www.syslog-ng.com/technical-documents/doc/syslog-ng-open-source-edition/3.16/administration-guide/19#TOPIC-956455).

1. Kontrollera att det finns kommunikation mellan Syslog-daemon och agenten. Kör det här kommandot på agentdatorn Syslog: `tcpdump -A -ni any  port 25226 -vv`. Det här kommandot visar de loggar som strömma från enheten till den Syslog-datorn. Kontrollera att loggarna också tas emot på agenten.

6. Om båda dessa kommandon angetts lyckade resultat och kontrollera Log Analytics för att se om dina loggar inkommer. Alla händelser som strömmas direkt från dessa enheter visas i obearbetat format i Log Analytics under `CommonSecurityLog` typen.

7. Kontrollera om det finns fel eller om loggarna som inte kommer se i `tail /var/opt/microsoft/omsagent/<workspace id>/log/omsagent.log`. Om det står att det finns fel i formatet matchningsfel, går du till `/etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` och titta på filen `security_events.conf`. Kontrollera att dina loggar matchar regex-format som du ser i den här filen.

8. Se till att din standardstorlek för Syslog-meddelande är begränsat till 2 048 byte (2 KB). Om loggar är för lång, uppdatera security_events.conf med hjälp av det här kommandot: `message_length_limit 4096`

10. Om dina Fortinet loggar inte tas emot av agenten, kör du följande kommando, beroende på vilken typ av Syslog-daemon som du använder, ange funktionen och ange loggarna för att söka efter ord Fortinet i loggarna:
       - rsyslog.d: `sudo bash -c "printf 'local4.debug  @127.0.0.1:25226\n\n:msg, contains, \"Fortinet\"  @127.0.0.1:25226' > /etc/rsyslog.d/security-config-omsagent.conf"`

     Starta om Syslog-daemon med hjälp av det här kommandot: `sudo service rsyslog restart`
       - syslog-ng: `sudo bash -c "printf 'filter f_local4_oms { facility(local4); };\n  destination security_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_local4_oms); destination(security_oms); };\n\nfilter f_msg_oms { match(\"Fortinet\" value(\"MESSAGE\")); };\n  destination security_msg_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_msg_oms); destination(security_msg_oms); };' > /etc/syslog-ng/security-config-omsagent.conf"`
      
     Starta om Syslog-daemon med hjälp av det här kommandot: `sudo service syslog-ng restart`

## <a name="next-steps"></a>Nästa steg
I den här artikeln beskrivs hur du ansluter Fortinet installationer till Sentinel-Azure. Mer information om Azure Sentinel finns i följande artiklar:
- Lär dig hur du [få insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [upptäcka hot med Azure Sentinel](tutorial-detect-threats.md).

