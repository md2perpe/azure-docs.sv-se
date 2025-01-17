---
title: Konfigurera haveriberedskap för virtuella Hyper-V-datorer på lokala platser med Azure Site Recovery | Microsoft Docs
description: Lär dig att konfigurera haveriberedskap för virtuella Hyper-V-datorer mellan dina lokala platser med Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 05/30/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 067040349a5d435860492497dddbf39aad2635eb
ms.sourcegitcommit: d89032fee8571a683d6584ea87997519f6b5abeb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66398409"
---
# <a name="set-up-disaster-recovery-for-hyper-v-vms-to-a-secondary-on-premises-site"></a>Konfigurera haveriberedskap för virtuella Hyper-V-datorer till en sekundär lokal plats

[Azure Site Recovery](site-recovery-overview.md)-tjänsten bidrar till din strategi för haveriberedskap genom att hantera och samordna replikering, redundans och återställning av fysiska servrar och virtuella Azure-datorer.

Den här artikeln visar hur du konfigurerar haveriberedskap till en sekundär plats för lokala virtuella Hyper-V-datorer som hanteras i System Center Virtual Machine Manager-moln (VMM). I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Förbereda lokala VMM-servrar och Hyper-V-värdar
> * Skapa ett Recovery Services-valv för Site Recovery 
> * Konfigurera käll- och målreplikeringsmiljöer. 
> * Konfigurera nätverksmappning 
> * Skapa replikeringsprincip
> * Aktivera replikering för en virtuell dator

## <a name="prerequisites"></a>Nödvändiga komponenter

För att slutföra den här kursen gör du följande:

- Granska [arkitekturen och komponenterna för scenariot](hyper-v-vmm-architecture.md).
- Se till att VMM-servrarna och Hyper-V-värdarna uppfyller [kraven för stöd](hyper-v-vmm-secondary-support-matrix.md).
- Kontrollera att de virtuella datorer som du vill replikera uppfyller [stöd för replikerad dator](hyper-v-vmm-secondary-support-matrix.md#replicated-vm-support).
- Förbereda VMM-servrar för nätverksmappning.

### <a name="prepare-for-network-mapping"></a>Förbereda för nätverksmappning

[Nätverksmappning](hyper-v-vmm-network-mapping.md) mappar mellan lokala VMM-VM-nätverk i käll- och målmoln. Mappning utför följande:

- Ansluter virtuella datorer till lämpliga VM-målnätverk efter redundansväxling. 
- Placerar virtuella replikdatorer optimalt på Hyper-V-målvärdservrar. 
- Om du inte konfigurerar nätverksmappning kommer inte virtuella replikdatorer att vara anslutna till något virtuellt datornätverk efter redundansväxling.

Förbereda VMM:er på följande sätt:

1. Kontrollera att du har [logiska VVM-nätverk](https://docs.microsoft.com/system-center/vmm/network-logical) på VMM-käll och -målservrarna.
    - Det logiska nätverket på källservern ska vara associerat med det källmoln där Hyper-V-värdarna finns.
    - Det logiska nätverket på målservern ska vara associerat med målmolnet.
1. Kontrollera att du har [VM-nätverk](https://docs.microsoft.com/system-center/vmm/network-virtual) på VMM-käll och -målservrarna. VM-nätverk ska vara länkade till det logiska nätverket på varje plats.
2. Ansluta virtuella datorer på Hyper-V-källvärdar till VM-källnätverket. 


## <a name="create-a-recovery-services-vault"></a>skapar ett Recovery Services-valv

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]


## <a name="choose-a-protection-goal"></a>Välja ett skyddsmål

Välj vad och vart du vill replikera.

1. Klicka på **Webbplatsåterställning** > **steg 1: Förbered infrastrukturen** > **Skyddsmål**.
2. Välj **Till återställningsplats** och **Ja, med Hyper-V**.
3. Välj **Ja** för att indikera att du använder VMM för att hantera Hyper-V-värdar.
4. Välj **Ja** om du har en sekundär VMM-server. Om du distribuerar replikering mellan moln på en enda VMM-server klickar du på **Nej**. Klicka sedan på **OK**.


## <a name="set-up-the-source-environment"></a>Konfigurera källmiljön

Installera Azure Site Recovery-providern på VMM-servrarna och identifiera och registrera servrarna i valvet.

1. Klicka på **Förbereda infrastrukturen** > **Källa**.
2. I **Förbered källa** klickar du på **+ VMM** för att lägga till en VMM-server.
3. I **Lägg till server** kontrollerar du att **System Center VMM-server** visas i **Servertyp**.
4. Ladda ned installationsfilen för Azure Site Recovery-providern.
5. Ladda ned registreringsnyckeln. Du behöver detta när du installerar providern. Nyckeln är giltig i fem dagar efter att du har genererat den.

    ![Konfigurera källan](./media/hyper-v-vmm-disaster-recovery/source-settings.png)

6. Installera providern på varje VMM-server. Du behöver inte installera något uttryckligen på Hyper-V-värdar.

### <a name="install-the-azure-site-recovery-provider"></a>Installera Azure Site Recovery-providern

1. Kör installationsfilen för providern på varje VMM-server. Om VMM distribueras i ett kluster installerar du för första gången på följande sätt:
    -  Installera providern på en aktiv nod och slutför installationen för att registrera VMM-servern i valvet.
    - Installera sedan providern på de andra noderna. Alla klusternoder bör köra samma version av providern.
2. Installationsprogrammet kör några nödvändiga kontroller och begär tillstånd att stoppa VMM-tjänsten. VMM-tjänsten startas om automatiskt när installationen är klar. Om du installerar i ett VMM-kluster uppmanas du att stoppa klusterrollen.
3. I **Microsoft Update** kan du välja att provideruppdateringar ska installeras i enlighet med din Microsoft Update-princip.
4. I **Installation** accepterar du eller ändrar standardinstallationsplatsen och klickar på **Installera**.
5. När installationen är klar klickar du på **Registrera** för att registrera servern i valvet.

    ![Installationsplats](./media/hyper-v-vmm-disaster-recovery/provider-register.png)
6. I **Valvnamn** kontrollerar du namnet på valvet som servern ska registreras i. Klicka på **Nästa**.
7. I **proxyanslutning** anger du hur den provider som körs på VMM-servern ska ansluta till Azure.
   - Du kan ange att providern ska ansluta direkt till Internet eller via en proxy. Ange proxyinställningar efter behov.
   - Om du använder en proxy skapas ett RunAs-konto (DRAProxyAccount) i VMM automatiskt med de angivna proxyautentiseringsuppgifterna. Konfigurera proxyservern så att det här kontot kan autentiseras. Du kan ändra inställningarna för RunAs-kontot i VMM-konsolen > **Inställningar** > **Säkerhet** > **Kör-som-konton**.
   - Starta om VMM-tjänsten för att uppdatera ändringarna.
8. I **Registreringsnyckel** väljer du den nyckel som du laddade ned kopierade till VMM-servern.
9. Krypteringsinställningen är inte relevant för det här scenariot. 
10. I **Servernamn** anger du ett eget namn som identifierar VMM-servern i valvet. I ett kluster anger du namnet på VMM-klusterrollen.
11. I **Synkronisera molnmetadata** väljer du om du vill synkronisera metadata för alla moln på VMM-servern. Den här åtgärden behöver bara göras en gång på varje server. Om du inte vill synkronisera alla moln lämnar du den här inställningen avmarkerad. Du kan synkronisera varje moln individuellt i molnegenskaperna i VMM-konsolen.
12. Slutför processen genom att klicka på **Nästa**. Efter registreringen hämtar Site Recovery metadata från VMM-servern. Servern visas i **Servrar** > **VMM-servrar** i valvet.
13. När servern visas i valvet går du till **Källa** > **Förbered källa** och markerar VMM-servern. Välj det moln där Hyper-V-värden hanteras. Klicka sedan på **OK**.


## <a name="set-up-the-target-environment"></a>Konfigurera målmiljön

Välj VMM-målservern och molnet:

1. Klicka på **Förbered infrastruktur** > **Mål** och välj VMM-målservern.
2. VMM-moln som är synkroniserade med Site Recovery visas. Välj målmolnet.

   ![Mål](./media/hyper-v-vmm-disaster-recovery/target-vmm.png)


## <a name="set-up-a-replication-policy"></a>Konfigurerar en replikeringsprincip

Innan du börjar bör du se till att alla värdar som använder principen har samma operativsystem. Om värdarna kör olika versioner av Windows Server behöver du flera replikeringsprinciper.

1. Skapa en ny replikeringsprincip genom att klicka på **Förbered infrastruktur** > **Replikeringsinställningar** >  **+Skapa och koppla**.
2. I **Princip för att skapa och koppla** anger du ett principnamn. Käll- och måltypen ska vara **Hyper-V**.
3. I **Hyper-V-värdversion** väljer du vilket operativsystem som körs på värden.
4. I **Autentiseringstyp** och **Autentiseringsport** anger du hur trafik autentiseras mellan primär- och återställnings-Hyper-V-värdservrarna.
    - Välj **Certifikat** såvida du inte har en fungerande Kerberos-miljö. Azure Site Recovery kommer automatiskt att konfigurera certifikat för HTTPS-autentisering. Du behöver inte göra något manuellt.
    - Som standard öppnas port 8083 och 8084 (för certifikat) i Windows-brandväggen på Hyper-V-värdservrarna.
    - Om du väljer **Kerberos** används en Kerberos-biljett för ömsesidig autentisering av värdservrarna. Kerberos är endast relevant för Hyper-V-värdservrar som körs på Windows Server 2012 R2 eller senare.
1. I **Kopieringsfrekvens** anger du hur ofta du vill replikera förändringsdata (delta) efter den första replikeringen (med 30 sekunders mellanrum, var femte minut eller varje kvart).
2. I **Kvarhållning av återställningspunkt** anger du hur länge (i antal timmar) kvarhållningsperioden för varje återställningspunkt ska vara. Replikerade datorer kan återställas till valfri punkt inom en period.
3. I **Appkompatibel ögonblicksbildsfrekvens** anger du hur ofta (1–12 timmar) återställningspunkter som innehåller programkonsekventa ögonblicksbilder skapas. Hyper-V använder två typer av ögonblicksbilder:
    - **Standardögonblicksbild**: Tillhandahåller en inkrementell ögonblicksbild av hela den virtuella datorn.
    - **Appkonsekvent ögonblicksbild**: Tar en ögonblicksbild som baseras på tidpunkt av programdata i den virtuella datorn. Volume Shadow Copy-tjänsten (VSS) säkerställer att apparna är i ett konsekvent tillstånd när ögonblicksbilden tas. Om du aktiverar programkonsekventa ögonblicksbilder påverkas prestanda på virtuella källdatorer. Ange ett värde som är mindre än det antal ytterligare återställningspunkter som du konfigurerar.
4. I **Komprimering av dataöverföring** anger du huruvida överförda replikeringsdata ska komprimeras.
5. Välj **Delete replica VM** (Ta bort virtuell replikdator) för att ange att den replikerade virtuella datorn ska tas bort om du inaktiverar skydd för den virtuella källdatorn. Om du aktiverar den här inställningen gäller att när du inaktiverar skydd för den virtuella källdatorn så tas den bort från Site Recovery-konsolen, Site Recovery-inställningarna för VMM tas bort från VMM-konsolen och repliken tas bort.
6. I **Metod för inledande replikering** gäller att om du replikerar över nätverket så anger du huruvida du vill starta den inledande replikeringen eller schemalägga den. För att spara nätverksbandbredd kan det vara bra att schemalägga den utanför kontorstid. Klicka sedan på **OK**.

     ![Replikeringsprincip](./media/hyper-v-vmm-disaster-recovery/replication-policy.png)
     
7. Den nya principen associeras automatiskt med VVM-molnet. I **Replikeringsprincip** klickar du på **OK**. 


## <a name="enable-replication"></a>Aktivera replikering

1. Klicka på **Replikera program** > **Källa**. 
2. I **Källa** väljer du VMM-servern och det moln där de Hyper-V-värdar som du vill replikera finns. Klicka sedan på **OK**.
3. I **Mål** kontrollerar du den sekundära VMM-servern och molnet.
4. I **Virtuella datorer** väljer du de virtuella datorer du vill skydda i listan.


Du kan följa förloppet för åtgärden **Aktivera skydd** under **Jobb** > **Site Recovery-jobb**. När jobbet **Slutför skyddet** är klart är den inledande replikeringen slutförd och den virtuella datorn är redo för redundans.

## <a name="next-steps"></a>Nästa steg

[Köra ett återställningstest](hyper-v-vmm-test-failover.md)
