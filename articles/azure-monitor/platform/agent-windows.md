---
title: Ansluta Windows-datorer till Azure Monitor | Microsoft Docs
description: Den här artikeln beskriver hur du ansluter Windows-datorer i andra moln eller en lokal plats till Azure Monitor med Log Analytics-agenten för Windows.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/14/2019
ms.author: magoedte
ms.openlocfilehash: 7f562959ac6022539ccf7137f352a2e9507758dc
ms.sourcegitcommit: 72f1d1210980d2f75e490f879521bc73d76a17e1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/14/2019
ms.locfileid: "67146357"
---
# <a name="connect-windows-computers-to-azure-monitor"></a>Ansluta Windows-datorer till Azure Monitor

För att övervaka och hantera virtuella datorer eller fysiska datorer i ditt lokala datacenter eller andra moln med Azure Monitor kan behöva du distribuera Log Analytics-agenten (kallas även som Microsoft Monitoring Agent (MMA)) och konfigurera den att rapportera till en eller flera Log Analytics-arbetsytor. Agenten stöder också Hybrid Runbook Worker-rollen för Azure Automation.  

På en övervakad Windows-dator visas agenten som Microsoft Monitoring Agent-tjänsten. Tjänsten Microsoft Monitoring Agent samlar in händelser från loggfiler och Windows-händelseloggen, prestandadata och annan telemetri. Även om agenten är inte kommunicera med Azure Monitor den rapporterar till kan agenten fortsätter att köras och placerar insamlade data på disken för den övervakade datorn. När anslutningen återupprättas skickar tjänsten Microsoft Monitoring Agent insamlade data till tjänsten.

Agenten kan installeras med någon av följande metoder. De flesta installationer använder en kombination av dessa metoder för att installera olika uppsättningar datorer efter behov.  Information om hur du använder varje metod finns senare i artikeln.

* Manuell installation. Installationen körs manuellt på datorn med hjälp av guiden Konfigurera från kommandoraden eller distribueras med ett befintligt programdistributionsverktyg.
* Azure Automation Desired State Configuration (DSC). Använda DSC i Azure Automation med ett skript för Windows-datorer som redan har distribuerats i din miljö.  
* PowerShell-skript.
* Resource Manager-mall för virtuella datorer som kör Windows lokalt i Azure Stack. 

>[!NOTE]
>Azure Security Center (ASC) ska är beroende av Microsoft Monitoring Agent (kallas även för Log Analytics-Windows-agent) och installera och konfigurera den för att rapportera till en Log Analytics-arbetsyta som en del av distributionen. ASC innehåller ett alternativ för automatisk etablering som aktiverar automatisk installation av Windows för Log Analytics-agenten på alla virtuella datorer i din prenumeration och konfigurerar den för att rapportera till en viss arbetsyta. Mer information om det här alternativet finns i [aktivera automatisk etablering av Log Analytics-agenten](../../security-center/security-center-enable-data-collection.md#enable-automatic-provisioning-of-microsoft-monitoring-agent-).
>

Om du vill konfigurera agenten för att rapportera till fler än en arbetsyta, detta kan inte utföras under den ursprungliga installationen, endast efteråt genom att uppdatera inställningarna från Kontrollpanelen eller PowerShell enligt beskrivningen i [när du lägger till eller ta bort en arbetsyta](agent-manage.md#adding-or-removing-a-workspace).  

Om du vill förstå konfigurationen som stöds, så granska [de Windows-operativsystem som stöds](log-analytics-agent.md#supported-windows-operating-systems) och [nätverkets brandväggskonfiguration](log-analytics-agent.md#network-firewall-requirements).

## <a name="obtain-workspace-id-and-key"></a>Hämta arbetsytans ID och nyckel
Innan du installerar Log Analytics-agenten för Windows, behöver du arbetsytans ID och nyckel för Log Analytics-arbetsytan.  Den här informationen krävs under installationen från olika installationsmetoderna korrekt konfigurera agenten och se till att den kan kommunicera med Azure Monitor i kommersiella Azure och molnet för amerikanska myndigheter. 

1. Klicka på **Alla tjänster** på Azure Portal. I listan över resurser skriver du **Log Analytics**. När du börjar skriva filtreras listan baserat på det du skriver. Välj **Log Analytics**.
2. Välj den arbetsyta som du tänker om hur du konfigurerar agenten rapporterar till i din lista över Log Analytics-arbetsytor.
3. Välj **Avancerade inställningar**.<br><br> ![Avancerade inställningar i Log Analytics](media/agent-windows/log-analytics-advanced-settings-01.png)<br><br>  
4. Välj **Anslutna källor** och välj sedan **Windows-servrar**.   
5. Kopiera och klistra in i din favoritredigerare, den **arbetsyte-ID** och **primärnyckel**.    
   
## <a name="configure-agent-to-use-tls-12"></a>Konfigurera agenten för att använda TLS 1.2
Konfigurera användning av den [TLS 1.2](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings#tls-12) protokoll för kommunikation mellan Windows-agenten och tjänsten Log Analytics kan du följa stegen nedan för att aktivera innan agenten har installerats på den virtuella datorn eller efteråt.   

1. Leta upp följande registerundernyckel: **HKEY_LOCAL_MACHINE\System\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols**
2. Skapa en undernyckel under **protokoll** för TLS 1.2 **HKLM\System\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2**
3. Skapa en **klienten** undernycklar under undernyckeln TLS 1.2-protokollet version du skapade tidigare. Till exempel **HKLM\System\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client**.
4. Skapa följande DWORD-värden under **HKLM\System\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client**:

    * **Aktiverad** [värde = 1]
    * **DisabledByDefault** [värde = 0]  

Konfigurera .NET Framework 4.6 eller senare för att stödja säker kryptering, som standard den är inaktiverad. Den [stark kryptografi](https://docs.microsoft.com/dotnet/framework/network-programming/tls#schusestrongcrypto) använder säkrare nätverksprotokoll som TLS 1.2 och blockerar protokoll som inte är säker. 

1. Leta upp följande registerundernyckel: **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\\.NETFramework\v4.0.30319**.  
2. Skapa DWORD-värde **SchUseStrongCrypto** under den här undernyckeln med värdet **1**.  
3. Leta upp följande registerundernyckel: **HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\\.NETFramework\v4.0.30319**.  
4. Skapa DWORD-värde **SchUseStrongCrypto** under den här undernyckeln med värdet **1**. 
5. Starta om systemet så att inställningarna ska börja gälla. 

## <a name="install-the-agent-using-setup-wizard"></a>Installera agenten med installationsguiden
Följande steg installerar och konfigurerar Log Analytics-agenten i Azure och Azure Government-molnet med hjälp av installationsguiden för agenten på datorn. Om du vill lära dig hur du konfigurerar att också rapportera till en hanteringsgrupp för System Center Operations Manager-agenten finns i [distribuera Operations Manager-agenten med Agentinstallationsguiden](https://docs.microsoft.com/system-center/scom/manage-deploy-windows-agent-manually#to-deploy-the-operations-manager-agent-with-the-agent-setup-wizard).

1. I din Log Analytics-arbetsyta från den **Windows-servrar** sidan du navigerat för att tidigare, väljer du lämplig **ladda ned Windows Agent** versionen för att ladda ned beroende på Processorarkitekturen för Windows-operativsystem.   
2. Kör installationsprogrammet för att installera agenten på datorn.
2. På sidan **Välkommen** klickar du på **Nästa**.
3. På sidan **Licensvillkor** läser du licensen och klickar sedan på **Jag accepterar**.
4. På sidan **Målmapp** ändrar du eller behåller standardinstallationsmappen och klickar sedan på **Nästa**.
5. På sidan **Installationsalternativ för agent** väljer du att ansluta agenten till Azure Log Analytics och klickar sedan på **Nästa**.   
6. På sidan **Azure Log Analytics** gör du följande:
   1. Klistra in **Id för arbetsyta** och **Arbetsytenyckel (primär nyckel)** som du kopierade tidigare.  Om datorn ska rapportera till en Log Analytics-arbetsyta i Azure Government-molnet väljer du **Azure US Government** i listrutan **Azure Cloud**.  
   2. Om datorn behöver kommunicera via en proxyserver till Log Analytics-tjänsten klickar du på **Avancerat** och anger URL och portnummer för proxyservern.  Om proxyservern kräver autentisering anger du användarnamn och lösenord för att autentisera hos proxyservern och klickar sedan på **Nästa**.  
7. Klicka på **Nästa** när du har gjort de konfigurationsinställningar som krävs.<br><br> ![klistra in ID för arbetsyta och primär nyckel](media/agent-windows/log-analytics-mma-setup-laworkspace.png)<br><br>
8. På sidan **Klar att installera** kontrollerar du valen och klickar sedan på **Installera**.
9. På sidan **Konfigurationen har slutförts** klickar du på **Slutför**.

När du är klar visas **Microsoft Monitoring Agent** i **Kontrollpanelen**. För att bekräfta att den rapporterar till Log Analytics, granska [verifiera agentanslutning till Log Analytics](#verify-agent-connectivity-to-log-analytics). 

## <a name="install-the-agent-using-the-command-line"></a>Installera agenten från kommandoraden
Den hämta filen för agenten är ett fristående installationsprogram.  Installationsprogrammet för agenten och filerna som ingår i paketet och måste extraheras för att kunna installera med hjälp av kommandoraden som visas i följande exempel.    

>[!NOTE]
>Om du vill uppgradera en agent kan behöva du använda Log Analytics skript-API. Finns i avsnittet [hantera och underhålla Log Analytics-agenten för Windows och Linux](agent-manage.md) för ytterligare information.

Följande tabell visar de specifika parametrarna som stöds av installationsprogrammet för agenten, inklusive när de distribueras med hjälp av Automation DSC.

|MMA-specifika alternativ                   |Anteckningar         |
|---------------------------------------|--------------|
| NOAPM=1                               | Valfri parameter. Installerar agent utan .NET Application Performance Monitoring.|   
|ADD_OPINSIGHTS_WORKSPACE               | 1 = konfigurera att agenten ska rapportera till en arbetsyta                |
|OPINSIGHTS_WORKSPACE_ID                | Arbetsyte-ID (guid) för arbetsytan att lägga till                    |
|OPINSIGHTS_WORKSPACE_KEY               | Arbetsytenyckel som används för att autentisera från början med arbetsytan |
|OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE  | Ange molnmiljön där arbetsytan finns <br> 0 = kommersiella azure-molnet (standard) <br> 1 = azure Government |
|OPINSIGHTS_PROXY_URL               | URI för proxyn så att den använder |
|OPINSIGHTS_PROXY_USERNAME               | Användarnamnet för att få åtkomst till en autentiserad proxyserver |
|OPINSIGHTS_PROXY_PASSWORD               | Lösenord för åtkomst till en autentiserad proxyserver |

1. Extrahera installationsfilerna för agent från en upphöjd kommandotolk kör `MMASetup-<platform>.exe /c` och du blir ombedd du sökvägen till filerna ska extraheras.  Du kan också ange sökvägen genom att skicka argument `MMASetup-<platform>.exe /c /t:<Full Path>`.  
2. Om du vill utföra tyst installation av agenten och konfigurera den för att rapportera till en arbetsyta i kommersiella Azure-molnet, från mappen extraherade du filer för installationsprogrammet för att skriva: 
   
     ```dos
    setup.exe /qn NOAPM=1 ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE=0 OPINSIGHTS_WORKSPACE_ID="<your workspace ID>" OPINSIGHTS_WORKSPACE_KEY="<your workspace key>" AcceptEndUserLicenseAgreement=1
    ```

   eller om du vill konfigurera agenten för att rapportera till Azure US Government-molnet, skriver du: 

     ```dos
    setup.exe /qn NOAPM=1 ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE=1 OPINSIGHTS_WORKSPACE_ID="<your workspace ID>" OPINSIGHTS_WORKSPACE_KEY="<your workspace key>" AcceptEndUserLicenseAgreement=1
    ```
    >[!NOTE]
    >Strängvärden för parametrarna *OPINSIGHTS_WORKSPACE_ID* och *OPINSIGHTS_WORKSPACE_KEY* måste vara inkapslade i dubbla citattecken att instruera Windows Installer till interprit som giltiga alternativ för paketet. 

## <a name="install-the-agent-using-dsc-in-azure-automation"></a>Installera agenten med DSC i Azure Automation

Du kan använda följande skript för att installera agenten med hjälp av Azure Automation DSC.   Om du inte har ett Automation-konto, se [Kom igång med Azure Automation](/azure/automation/) att förstå kraven och steg för att skapa ett Automation-konto som krävs innan du kan använda Automation DSC.  Om du inte är bekant med Automation DSC kan du läsa [komma igång med Automation DSC](../../automation/automation-dsc-getting-started.md).

I följande exempel installeras 64-bitars agent, identifieras av den `URI` värde. Du kan också använda 32-bitars version genom att ersätta URI-värdet. URI: er för båda versionerna är:

- Windows 64-bitars agent- https://go.microsoft.com/fwlink/?LinkId=828603
- Windows 32-bitars agent- https://go.microsoft.com/fwlink/?LinkId=828604


>[!NOTE]
>Den här proceduren och skript exempel stöder inte uppgradera agenten redan har distribuerats till en Windows-dator.

32-bitars och 64-bitars versioner av agenten paketet har olika produktkoder och nya versioner är också ha ett unikt värde.  Den här koden är ett GUID som är en huvudnamn identifiering av ett program eller en produkt och representeras av Windows Installer **ProductCode** egenskapen.  Den `ProductId` värde i den **MMAgent.ps1** skriptet måste matcha den här koden från 32-bitars eller 64-bitars agent installer-paketet.

För att hämta den här koden från installationspaket för agenten direkt, du kan använda Orca.exe från den [Windows SDK-komponenterna för Windows Installer-utvecklare](https://msdn.microsoft.com/library/windows/desktop/aa370834%28v=vs.85%29.aspx) som är en komponent i Windows Software Development Kit eller med hjälp av Följande PowerShell en [exempelskript](https://www.scconfigmgr.com/2014/08/22/how-to-get-msi-file-information-with-powershell/) skrivs av en Microsoft Valuable Professional (MVP).  För båda metoder, måste du först extrahera den **MOMagent.msi** filen i MMASetup installationspaket.  Detta visas tidigare i det första steget i avsnittet [installera agenten från kommandoraden](#install-the-agent-using-the-command-line).  

1. Importera xPSDesiredStateConfiguration DSC-modul från [ https://www.powershellgallery.com/packages/xPSDesiredStateConfiguration ](https://www.powershellgallery.com/packages/xPSDesiredStateConfiguration) till Azure Automation.  
2.  Skapa Azure Automation variabler för tillgångar för *OPSINSIGHTS_WS_ID* och *OPSINSIGHTS_WS_KEY*. Ange *OPSINSIGHTS_WS_ID* till Log Analytics arbetsyte-ID och set *OPSINSIGHTS_WS_KEY* till den primära nyckeln i din arbetsyta.
3.  Kopiera skriptet och spara den som MMAgent.ps1.

    ```powershell
    Configuration MMAgent
    {
        $OIPackageLocalPath = "C:\Deploy\MMASetup-AMD64.exe"
        $OPSINSIGHTS_WS_ID = Get-AutomationVariable -Name "OPSINSIGHTS_WS_ID"
        $OPSINSIGHTS_WS_KEY = Get-AutomationVariable -Name "OPSINSIGHTS_WS_KEY"

        Import-DscResource -ModuleName xPSDesiredStateConfiguration
        Import-DscResource -ModuleName PSDesiredStateConfiguration

        Node OMSnode {
            Service OIService
            {
                Name = "HealthService"
                State = "Running"
                DependsOn = "[Package]OI"
            }

            xRemoteFile OIPackage {
                Uri = "https://go.microsoft.com/fwlink/?LinkId=828603"
                DestinationPath = $OIPackageLocalPath
            }

            Package OI {
                Ensure = "Present"
                Path  = $OIPackageLocalPath
                Name = "Microsoft Monitoring Agent"
                ProductId = "8A7F2C51-4C7D-4BFD-9014-91D11F24AAE2"
                Arguments = '/C:"setup.exe /qn NOAPM=1 ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_ID=' + $OPSINSIGHTS_WS_ID + ' OPINSIGHTS_WORKSPACE_KEY=' + $OPSINSIGHTS_WS_KEY + ' AcceptEndUserLicenseAgreement=1"'
                DependsOn = "[xRemoteFile]OIPackage"
            }
        }
    }

    ```

4. Uppdatera den `ProductId` värdet i skriptet med den här koden som extraheras från den senaste versionen av agenten installera paketet med hjälp av de metoder som rekommenderas tidigare. 
5. [Importera konfigurationsskript MMAgent.ps1](../../automation/automation-dsc-getting-started.md#importing-a-configuration-into-azure-automation) i ditt Automation-konto. 
5. [Tilldela en Windows-dator eller en nod](../../automation/automation-dsc-getting-started.md#onboarding-an-azure-vm-for-management-with-azure-automation-state-configuration) i konfigurationen. Noden kontrollerar dess konfiguration inom 15 minuter och agenten skickas till noden.

## <a name="verify-agent-connectivity-to-log-analytics"></a>Verifiera agentanslutning till Log Analytics

När installationen av agenten är klar, verifierar den anslutits och rapportering kan utföras på två sätt.  

På datorn går du till **Kontrollpanelen** och letar upp objektet **Microsoft Monitoring Agent**.  Välj den. På fliken **Azure Log Analytics** bör agenten visa ett meddelande där det står: **Microsoft Monitoring Agent har anslutits till tjänsten Microsoft Operations Management Suite.**<br><br> ![MMA-anslutningsstatus till Log Analytics](media/agent-windows/log-analytics-mma-laworkspace-status.png)

Du kan också utföra en enkel logg-fråga i Azure-portalen.  

1. Klicka på **Alla tjänster** på Azure Portal. I listan över resurser skriver **Azure Monitor**. När du börjar skriva filtreras listan baserat på det du skriver. Välj **Azure Monitor**.  
2. Välj **loggar** på menyn. 
2. I fönstret loggar i skriver:  

    ```
    Heartbeat 
    | where Category == "Direct Agent" 
    | where TimeGenerated > ago(30m)  
    ```

Du bör se pulsslagsposter för den dator som du anger den är ansluten och rapporterar till tjänsten i sökresultaten returneras.   

## <a name="next-steps"></a>Nästa steg

- Granska [hantera och underhålla Log Analytics-agenten för Windows och Linux](agent-manage.md) vill veta mer om hur du hanterar agenten under livscykeln distribution på dina datorer.  

- Granska [felsöka Windows-agent](agent-windows-troubleshoot.md) om det uppstår problem när du installerar eller hantering av agenten.
